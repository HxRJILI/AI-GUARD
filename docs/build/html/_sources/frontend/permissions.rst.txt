===========
Permissions
===========

The frontend implements permission checks to control access to features based on user roles.

Permission Model
==============

The application enforces a comprehensive permission model that restricts access to features based on user roles.

Role-Based Access Control
-----------------------

.. figure:: /_static/permissions-model.png
   :alt: Permissions Model
   :align: center
   
   Role-based permission model overview

Each user is assigned one of the following roles:

- **Administrator**: Full system access
- **Security Officer**: Access to monitoring, logs, alerts, and basic user management
- **Enrollment Officer**: Limited access focused on enrolling and managing user face data
- **Auditor**: Read-only access to logs and reports
- **Standard User**: No access to the admin system

Permission Mapping
----------------

Each role is granted a set of permissions:

.. code-block:: javascript
   :caption: Permission mapping

   const permissionMap = {
     ADMIN: [
       'system.configure',
       'users.view',
       'users.create',
       'users.update',
       'users.delete',
       'enrollments.create',
       'enrollments.view',
       'enrollments.delete',
       'logs.view',
       'alerts.manage',
       'reports.generate'
     ],
     SECURITY_OFFICER: [
       'users.view',
       'users.create',
       'users.update',
       'enrollments.create',
       'enrollments.view',
       'logs.view',
       'alerts.manage',
       'reports.generate'
     ],
     ENROLLMENT_OFFICER: [
       'enrollments.create',
       'enrollments.view',
       'users.view'
     ],
     AUDITOR: [
       'logs.view',
       'reports.generate',
       'users.view'
     ],
     STANDARD_USER: []
   };

Permission Hook
=============

A custom React hook is used to check permissions:

.. code-block:: javascript
   :caption: Permission hook implementation

   import { useSelector } from 'react-redux';
   
   export const usePermission = (requiredPermission) => {
     const { user } = useSelector(state => state.auth);
     
     if (!user || !user.role) {
       return false;
     }
     
     // Get permissions for the user's role
     const userPermissions = permissionMap[user.role] || [];
     
     // Check if the user has the required permission
     return userPermissions.includes(requiredPermission);
   };
   
   // Usage
   const SettingsButton = () => {
     const canConfigureSystem = usePermission('system.configure');
     
     if (!canConfigureSystem) {
       return null;
     }
     
     return (
       <Button onClick={openSettings}>
         Settings
       </Button>
     );
   };

Protected Route Component
======================

Routes are protected using a dedicated component:

.. code-block:: jsx
   :caption: Protected route implementation

   import { Navigate, useLocation } from 'react-router-dom';
   import { usePermission } from '../hooks/usePermission';
   import { useSelector } from 'react-redux';
   
   const ProtectedRoute = ({ permission, children }) => {
     const location = useLocation();
     const hasPermission = permission ? usePermission(permission) : true;
     const { isAuthenticated } = useSelector(state => state.auth);
     
     if (!isAuthenticated) {
       // Redirect to login page, but save the attempted location
       return <Navigate to="/login" state={{ from: location }} replace />;
     }
     
     if (!hasPermission) {
       // Redirect to access denied page
       return <Navigate to="/access-denied" replace />;
     }
     
     return children;
   };
   
   // Usage in routes
   <Routes>
     <Route 
       path="/settings" 
       element={
         <ProtectedRoute permission="system.configure">
           <SettingsPage />
         </ProtectedRoute>
       } 
     />
   </Routes>

Component-Level Permission Control
===============================

UI components are conditionally rendered based on permissions:

.. code-block:: jsx
   :caption: Permission-based rendering

   import { usePermission } from '../hooks/usePermission';
   
   const UserListActions = ({ user }) => {
     const canUpdateUsers = usePermission('users.update');
     const canDeleteUsers = usePermission('users.delete');
     
     return (
       <div className="flex space-x-2">
         {canUpdateUsers && (
           <Button 
             variant="secondary" 
             size="sm" 
             onClick={() => editUser(user)}
           >
             Edit
           </Button>
         )}
         
         {canDeleteUsers && (
           <Button 
             variant="danger" 
             size="sm" 
             onClick={() => confirmDelete(user)}
           >
             Delete
           </Button>
         )}
         
         <Button 
           variant="primary" 
           size="sm" 
           onClick={() => viewDetails(user)}
         >
           View
         </Button>
       </div>
     );
   };

Permission-Based Navigation
========================

The navigation menu is dynamically filtered based on user permissions:

.. code-block:: jsx
   :caption: Permission-based navigation

   import { usePermission } from '../hooks/usePermission';
   
   const Navigation = () => {
     // Permission checks
     const canViewUsers = usePermission('users.view');
     const canViewEnrollments = usePermission('enrollments.view');
     const canViewLogs = usePermission('logs.view');
     const canManageAlerts = usePermission('alerts.manage');
     const canGenerateReports = usePermission('reports.generate');
     const canConfigureSystem = usePermission('system.configure');
     
     // Navigation items with permission requirements
     const navItems = [
       { path: '/dashboard', label: 'Dashboard', icon: <DashboardIcon />, allowed: true },
       { path: '/users', label: 'Users', icon: <UsersIcon />, allowed: canViewUsers },
       { path: '/enrollment', label: 'Enrollment', icon: <EnrollmentIcon />, allowed: canViewEnrollments },
       { path: '/logs', label: 'Access Logs', icon: <LogsIcon />, allowed: canViewLogs },
       { path: '/alerts', label: 'Alerts', icon: <AlertsIcon />, allowed: canManageAlerts },
       { path: '/reports', label: 'Reports', icon: <ReportsIcon />, allowed: canGenerateReports },
       { path: '/settings', label: 'Settings', icon: <SettingsIcon />, allowed: canConfigureSystem },
     ];
     
     // Filter navigation items based on permissions
     const allowedNavItems = navItems.filter(item => item.allowed);
     
     return (
       <nav className="space-y-1">
         {allowedNavItems.map(item => (
           <NavLink
             key={item.path}
             to={item.path}
             className={({ isActive }) =>
               isActive
                 ? 'bg-primary-100 text-primary-900 group flex items-center px-2 py-2 text-sm font-medium rounded-md'
                 : 'text-gray-600 hover:bg-gray-50 hover:text-gray-900 group flex items-center px-2 py-2 text-sm font-medium rounded-md'
             }
           >
             <span className="mr-3 h-6 w-6 text-gray-400 group-hover:text-gray-500">
               {item.icon}
             </span>
             {item.label}
           </NavLink>
         ))}
       </nav>
     );
   };

Form Field Permission Control
==========================

Form fields can be disabled or hidden based on permissions:

.. code-block:: jsx
   :caption: Permission-based form fields

   import { usePermission } from '../hooks/usePermission';
   
   const UserForm = ({ user, onSubmit }) => {
     const canEditRoles = usePermission('users.editRoles');
     const canEditSecurity = usePermission('users.editSecurity');
     
     return (
       <form onSubmit={handleSubmit(onSubmit)}>
         <div className="space-y-6">
           <TextField
             label="First Name"
             name="firstName"
             defaultValue={user?.firstName}
             required
           />
           
           <TextField
             label="Last Name"
             name="lastName"
             defaultValue={user?.lastName}
             required
           />
           
           <TextField
             label="Email"
             name="email"
             type="email"
             defaultValue={user?.email}
             required
           />
           
           {/* Only shown if user has permission */}
           {canEditRoles && (
             <SelectField
               label="Role"
               name="role"
               defaultValue={user?.role}
               options={[
                 { value: 'ADMIN', label: 'Administrator' },
                 { value: 'SECURITY_OFFICER', label: 'Security Officer' },
                 { value: 'ENROLLMENT_OFFICER', label: 'Enrollment Officer' },
                 { value: 'AUDITOR', label: 'Auditor' },
                 { value: 'STANDARD_USER', label: 'Standard User' },
               ]}
             />
           )}
           
           {/* Only shown if user has permission */}
           {canEditSecurity && (
             <div className="space-y-4">
               <h3 className="text-lg font-medium">Security Settings</h3>
               
               <SwitchField
                 label="Two-Factor Authentication Required"
                 name="twoFactorRequired"
                 defaultChecked={user?.twoFactorRequired}
               />
               
               <SelectField
                 label="Account Timeout"
                 name="accountTimeout"
                 defaultValue={user?.accountTimeout || 30}
                 options={[
                   { value: 15, label: '15 minutes' },
                   { value: 30, label: '30 minutes' },
                   { value: 60, label: '1 hour' },
                   { value: 120, label: '2 hours' },
                   { value: 240, label: '4 hours' },
                 ]}
               />
             </div>
           )}
           
           <div className="flex justify-end space-x-3">
             <Button type="button" variant="secondary" onClick={onCancel}>
               Cancel
             </Button>
             <Button type="submit" variant="primary">
               Save
             </Button>
           </div>
         </div>
       </form>
     );
   };

Access Denied Handling
====================

When a user attempts to access a feature without permission, they are redirected to an access denied page:

.. code-block:: jsx
   :caption: Access denied page

   const AccessDenied = () => {
     const navigate = useNavigate();
     const { user } = useSelector(state => state.auth);
     
     return (
       <div className="min-h-full px-4 py-16 sm:px-6 sm:py-24 md:grid md:place-items-center lg:px-8">
         <div className="max-w-max mx-auto">
           <main className="sm:flex">
             <p className="text-4xl font-extrabold text-primary-600 sm:text-5xl">403</p>
             <div className="sm:ml-6">
               <div className="sm:border-l sm:border-gray-200 sm:pl-6">
                 <h1 className="text-4xl font-extrabold text-gray-900 tracking-tight sm:text-5xl">Access Denied</h1>
                 <p className="mt-1 text-base text-gray-500">
                   You don't have permission to access this page.
                 </p>
                 {user && (
                   <p className="mt-2 text-sm text-gray-700">
                     Current role: <span className="font-semibold">{user.role}</span>
                   </p>
                 )}
               </div>
               <div className="mt-10 flex space-x-3 sm:border-l sm:border-transparent sm:pl-6">
                 <Button
                   onClick={() => navigate('/dashboard')}
                   variant="primary"
                 >
                   Go to Dashboard
                 </Button>
                 <Button
                   onClick={() => navigate(-1)}
                   variant="secondary"
                 >
                   Go Back
                 </Button>
               </div>
             </div>
           </main>
         </div>
       </div>
     );
   };