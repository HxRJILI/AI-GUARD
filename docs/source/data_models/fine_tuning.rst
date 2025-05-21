===========
Fine-Tuning
===========

The system supports continuous improvement through fine-tuning of the recognition models.

Fine-Tuning Workflow
==================

.. figure:: /_static/fine-tuning-workflow.png
   :alt: Fine-Tuning Workflow
   :align: center
   
   Complete workflow for model fine-tuning

The fine-tuning process follows these steps:

1. **Data Collection**
---------------------

Gather new face images from actual usage:

- Failed recognition attempts (false negatives)
- Low-confidence successful recognitions
- Periodic re-enrollment captures
- Environmental condition variations
- Multiple capture angles

2. **Data Curation**
------------------

Review and clean data before training:

- Remove low-quality samples (blur, extreme lighting)
- Verify identity labels
- Balance class distribution
- Split into train/validation sets
- Apply privacy controls

3. **Embedding Generation**
-------------------------

Process images through the current model:

- Generate embeddings with existing model
- Analyze embedding distribution
- Identify problematic cases
- Quantify inter-class and intra-class distances

4. **Transfer Learning**
---------------------

Initialize new model with weights from current model:

.. code-block:: python
   :caption: Transfer learning setup

   def create_fine_tuning_model(base_model_path, num_classes):
       # Load base model
       base_model = tf.keras.models.load_model(base_model_path)
       
       # Freeze early layers
       for layer in base_model.layers[:-4]:
           layer.trainable = False
       
       # Add new classification head
       x = base_model.layers[-4].output
       x = Dense(512, activation='relu', name='fine_tune_dense')(x)
       x = Dropout(0.3, name='fine_tune_dropout')(x)
       output = Dense(num_classes, activation='softmax', name='fine_tune_output')(x)
       
       # Create new model
       model = Model(base_model.input, output)
       return model

5. **Fine-Tuning**
----------------

Train on combined dataset with reduced learning rate:

- Use small learning rate (1e-4 to 1e-5)
- Apply class-balanced weighting
- Monitor validation metrics closely
- Use early stopping to prevent overfitting
- Apply regularization techniques

.. code-block:: python
   :caption: Fine-tuning training configuration

   # Training configuration
   optimizer = tf.keras.optimizers.Adam(learning_rate=1e-4)
   model.compile(
       optimizer=optimizer,
       loss='categorical_crossentropy',
       metrics=['accuracy', 'AUC']
   )
   
   # Callbacks
   callbacks = [
       tf.keras.callbacks.EarlyStopping(patience=5, restore_best_weights=True),
       tf.keras.callbacks.ReduceLROnPlateau(factor=0.5, patience=3),
       tf.keras.callbacks.ModelCheckpoint('best_model.h5', save_best_only=True)
   ]
   
   # Training with class weights
   history = model.fit(
       train_dataset,
       validation_data=val_dataset,
       epochs=20,
       callbacks=callbacks,
       class_weight=class_weights
   )

6. **Evaluation**
---------------

Test on holdout set to ensure performance improvement:

- Compute accuracy, precision, recall
- Generate ROC curve and compute AUC
- Verify improvement on problematic cases
- Test across different demographic groups
- Evaluate performance in different lighting conditions

7. **Deployment**
--------------

Replace production model through model serving infrastructure:

- Version the new model
- Deploy to staging environment
- A/B test against current model
- Monitor performance metrics
- Roll out to production
- Enable fallback to previous version

Schedule and Triggers
===================

Fine-tuning can be triggered by:

- **Schedule-based**: Regular monthly or quarterly updates
- **Performance-based**: When accuracy falls below threshold
- **Data-based**: When sufficient new training data accumulated
- **Environment-based**: New deployment location with different conditions

Best Practices
============

- **Maintain Original Data**: Keep pre-fine-tuning data to prevent catastrophic forgetting
- **Version Control**: Track all model versions and their performance
- **Slow Rollout**: Deploy new models gradually to minimize disruption
- **Human Verification**: Incorporate human verification of problematic cases
- **Documentation**: Document all changes, data sources, and hyperparameters