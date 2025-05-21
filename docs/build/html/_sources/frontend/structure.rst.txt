=========
Structure
=========

The frontend application is organized into several key sections to provide a comprehensive interface for all user roles.

Application Architecture
=====================

.. figure:: /_static/frontend-architecture.png
   :alt: Frontend Application Architecture
   :align: center
   
   High-level frontend architecture diagram

The application follows a modular architecture:

- **Core**: Shared utilities, types, and base components
- **Pages**: Main route components
- **Features**: Domain-specific functionality
- **Components**: Reusable UI elements
- **Services**: API communication and data processing
- **Hooks**: Custom React hooks
- **Context**: React context providers
- **Assets**: Static resources

Directory Structure
=================

.. code-block:: text
   :caption: Project directory structure

   src/
   ├── assets/               # Static assets (images, icons)
   ├── components/           # Shared UI components
   │   ├── common/           # General-purpose components
   │   ├── forms/            # Form components
   │   ├── layout/           # Layout components
   │   ├── charts/           # Data visualization
   │   └── ui/               # Basic UI elements
   ├── config/               # Environment configs
   ├── context/              # React context providers
   ├── features/             # Feature modules
   │   ├── auth/             # Authentication
   │   ├── enrollment/       # Face enrollment
   │   ├── recognition/      # Face recognition
   │   ├── users/            # User management
   │   ├── alerts/           # Alert system
   │   └── dashboard/        # Admin dashboard
   ├── hooks/                # Custom React hooks
   ├── pages/                # Route components
   ├── services/             # API and external services
   ├── store/                # Redux store
   ├── types/                # TypeScript types
   ├── utils/                # Utility functions
   ├── App.tsx               # Main component
   └── index.tsx             # Entry point

Pages
====

The application is organized into several key sections:

Login
----

.. figure:: /_static/login-page.png
   :alt: Login Page
   :align: center
   
   Login page with security features

Authentication interface with:
- Username/password login
- Two-factor authentication option
- Password reset flow
- Session timeout management
- Login attempt tracking

Dashboard
--------

Overview of system status and key metrics:
- System health indicators
- Recent activity feed
- Key performance metrics
- Quick access to common functions
- Alerts and notifications panel

Enrollment
---------

Interface for registering new users:
- Step-by-step enrollment wizard
- Face capture with quality assessment
- User information form
- Role and permission assignment
- Consent management
- Quality verification

User Management
-------------

CRUD operations for user records:
- User listing with filtering and search
- Detail view of user information
- Edit form for user data
- Enrollment status and quality
- Access history
- Role and permission management

Access Logs
---------

Searchable history of access events:
- Advanced filtering options
- Timeline visualization
- Image capture display
- Export functionality
- Annotation capabilities
- Heatmap of access locations

Live Monitoring
-------------

Real-time view of camera feeds and access attempts:
- Camera feed grid
- Recognition status overlay
- Alert indicators
- Quick actions for operators
- PTZ camera controls where supported
- Event timeline

Alerts
-----

Interface for viewing and managing security alerts:
- Alert listing by severity and status
- Detail view with related information
- Resolution workflow
- Assignment capabilities
- Notification settings
- Historical alert analysis

Settings
-------

System configuration interface:
- Recognition thresholds
- Notification preferences
- System maintenance
- Backup and restore
- Integration settings
- User preferences

Reports
------

Analytics and report generation:
- Pre-defined report templates
- Custom report builder
- Data visualization options
- Export to PDF/CSV/Excel
- Scheduled report generation
- Compliance reporting

Component Organization
====================

Components are categorized based on their purpose and reusability:

Base Components
-------------

Low-level UI elements with high reusability:
- Button
- Input
- Select
- Checkbox
- RadioButton
- Card
- Modal
- Tooltip

Composite Components
------------------

More complex components that combine multiple base components:
- DataTable
- SearchBar
- Pagination
- FileUpload
- DatePicker
- NotificationBadge
- TabGroup

Feature Components
----------------

Domain-specific components for particular features:
- UserForm
- FaceCapture
- AlertCard
- LogViewer
- CameraFeed
- EnrollmentWizard
- RecognitionStatus

Layout Components
---------------

Components that manage the application layout:
- MainLayout
- Sidebar
- Header
- Footer
- PageContainer
- SplitView
- Breadcrumbs