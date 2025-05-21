===============
Data Protection
===============

Face embeddings are sensitive biometric data and are protected through multiple security measures.

Embedding Encryption
==================

To protect face embedding data at rest, a multi-layered encryption approach is used:

AES-256 Encryption
----------------

.. figure:: /_static/embedding-encryption.png
   :alt: Embedding Encryption Process
   :align: center
   
   Encryption process for face embeddings

All face embeddings are encrypted before storage:

1. Generate a unique Data Encryption Key (DEK) for each embedding
2. Encrypt the embedding vector using AES-256-GCM with the DEK
3. Encrypt the DEK with a master Key Encryption Key (KEK)
4. Store the encrypted embedding and encrypted DEK in the database

.. code-block:: python
   :caption: Embedding encryption/decryption

   def encrypt_embedding(embedding_vector, master_key):
       # Generate a unique data encryption key
       data_key = secrets.token_bytes(32)  # 256 bits
       
       # Create nonce for AES-GCM
       nonce = secrets.token_bytes(12)
       
       # Encrypt the embedding with the data key
       cipher = AES.new(data_key, AES.MODE_GCM, nonce=nonce)
       ciphertext, tag = cipher.encrypt_and_digest(embedding_vector)
       
       # Encrypt the data key with the master key
       key_cipher = AES.new(master_key, AES.MODE_GCM)
       encrypted_key, key_tag = key_cipher.encrypt_and_digest(data_key)
       key_nonce = key_cipher.nonce
       
       # Combine all components for storage
       encrypted_data = {
           'encrypted_embedding': base64.b64encode(ciphertext).decode('utf-8'),
           'embedding_tag': base64.b64encode(tag).decode('utf-8'),
           'embedding_nonce': base64.b64encode(nonce).decode('utf-8'),
           'encrypted_key': base64.b64encode(encrypted_key).decode('utf-8'),
           'key_tag': base64.b64encode(key_tag).decode('utf-8'),
           'key_nonce': base64.b64encode(key_nonce).decode('utf-8')
       }
       
       return json.dumps(encrypted_data)

Key Management
------------

Encryption keys are securely managed:

- Master keys are stored in a Hardware Security Module (HSM)
- Key rotation occurs automatically every 90 days
- Versioned key management allows for graceful rotation
- Access to keys is strictly controlled and audited
- Separate key hierarchies for development, staging, and production

Database-level Encryption
-----------------------

In addition to application-level encryption:

- Full database encryption using PostgreSQL's encryption features
- TDE (Transparent Data Encryption) for database files
- Encrypted backup files
- Secure key storage separated from database backups

GDPR Compliance
=============

The system implements several features to support GDPR compliance:

Explicit Consent Tracking
-----------------------

All user consent is explicitly tracked:

.. code-block:: sql
   :caption: Consent tracking table schema

   CREATE TABLE consent_records (
     id UUID PRIMARY KEY,
     user_id UUID REFERENCES users(id),
     consent_type VARCHAR(50) NOT NULL,
     given_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
     revoked_at TIMESTAMP,
     consent_document_version VARCHAR(20),
     consent_record TEXT,
     ip_address VARCHAR(45),
     user_agent TEXT
   );

The system captures:
- Which version of the consent document was presented
- When consent was given
- Environmental factors (IP, user agent) for audit purposes
- Revocation timestamp if consent is later withdrawn

Right to be Forgotten
-------------------

Automated process to delete user data upon request:

1. **Deletion Request**: User or administrator initiates deletion request
2. **Validation**: Verify request authenticity
3. **Scheduling**: Schedule deletion process (typically within 30 days)
4. **Execution**: Execute deletion procedure
   - Delete face embeddings
   - Anonymize access logs
   - Remove user record
   - Generate deletion certificate
5. **Confirmation**: Provide deletion confirmation to user

.. code-block:: sql
   :caption: Anonymization procedure

   CREATE OR REPLACE PROCEDURE anonymize_user(user_id_param UUID) AS $$
   BEGIN
     -- Record deletion request
     INSERT INTO deletion_records (user_id, requested_at)
     VALUES (user_id_param, CURRENT_TIMESTAMP);
     
     -- Anonymize access logs (preserve statistical data)
     UPDATE access_logs 
     SET user_id = NULL,
         notes = 'Anonymized per user request'
     WHERE user_id = user_id_param;
     
     -- Delete face embeddings (complete removal)
     DELETE FROM face_embeddings 
     WHERE user_id = user_id_param;
     
     -- Mark user as inactive but preserve record temporarily
     UPDATE users
     SET active = false,
         first_name = 'Redacted',
         last_name = 'User',
         email = 'redacted_' || user_id_param || '@example.com'
     WHERE id = user_id_param;
     
     -- Schedule final deletion
     INSERT INTO scheduled_deletions (user_id, scheduled_for)
     VALUES (user_id_param, CURRENT_TIMESTAMP + INTERVAL '30 days');
   END;
   $$ LANGUAGE plpgsql;

Data Access
---------

API endpoints for users to request all stored personal data:

- `/api/users/{id}/data`: Endpoint for data access requests
- Data provided in structured, machine-readable format (JSON)
- Includes all personal data, enrollment history, and access logs
- Audit trail of when data was accessed and by whom

Purpose Limitation
----------------

Clear documentation of data usage purposes:

- Each data field has defined purpose and retention period
- Technical controls enforce purpose limitation
- Data usage is logged and auditable
- System prevents repurposing of data without new consent

Data Minimization
---------------

Only essential biometric data is stored:

- Face embeddings are mathematical representations, not actual images
- Only minimal user metadata is stored
- Original enrollment images are deleted after embedding generation
- Temporary data is automatically purged after processing

Audit Logging
-----------

All data access and modifications are logged:

.. code-block:: sql
   :caption: Audit log table schema

   CREATE TABLE audit_logs (
     id UUID PRIMARY KEY,
     action_type VARCHAR(50) NOT NULL,
     entity_type VARCHAR(50) NOT NULL,
     entity_id UUID NOT NULL,
     actor_id UUID REFERENCES users(id),
     timestamp TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
     ip_address VARCHAR(45),
     details JSONB
   );

   CREATE INDEX idx_audit_logs_entity ON audit_logs(entity_type, entity_id);
   CREATE INDEX idx_audit_logs_actor ON audit_logs(actor_id);
   CREATE INDEX idx_audit_logs_timestamp ON audit_logs(timestamp);