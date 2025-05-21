=======
Styling
=======

The application uses TailwindCSS for styling with a consistent design system.

Design System
===========

.. figure:: /_static/design-system.png
   :alt: AI-Guard Design System
   :align: center
   
   Overview of the design system components and colors

The design system includes:

- Color palette
- Typography
- Spacing scale
- Component styles
- Animation patterns
- Responsive breakpoints
- Accessibility guidelines

Color Scheme
==========

The application uses a consistent color palette:

.. code-block:: javascript
   :caption: tailwind.config.js color configuration

   module.exports = {
     theme: {
       extend: {
         colors: {
           primary: {
             50: '#eef2ff',
             100: '#e0e7ff',
             200: '#c7d2fe',
             300: '#a5b4fc',
             400: '#818cf8',
             500: '#6366f1',
             600: '#4f46e5',
             700: '#4338ca',
             800: '#3730a3',
             900: '#312e81',
           },
           secondary: {
             50: '#f0fdf4',
             100: '#dcfce7',
             200: '#bbf7d0',
             300: '#86efac',
             400: '#4ade80',
             500: '#22c55e',
             600: '#16a34a',
             700: '#15803d',
             800: '#166534',
             900: '#14532d',
           },
           alert: {
             success: '#10b981',
             warning: '#f59e0b',
             danger: '#ef4444',
             info: '#3b82f6',
           }
         }
       }
     }
   }

Typography
=========

A consistent type system with defined styles for different text elements:

.. code-block:: javascript
   :caption: Typography configuration

   // tailwind.config.js
   module.exports = {
     theme: {
       extend: {
         fontFamily: {
           sans: ['Inter', 'sans-serif'],
           mono: ['JetBrains Mono', 'monospace'],
         },
         fontSize: {
           xs: ['0.75rem', { lineHeight: '1rem' }],
           sm: ['0.875rem', { lineHeight: '1.25rem' }],
           base: ['1rem', { lineHeight: '1.5rem' }],
           lg: ['1.125rem', { lineHeight: '1.75rem' }],
           xl: ['1.25rem', { lineHeight: '1.75rem' }],
           '2xl': ['1.5rem', { lineHeight: '2rem' }],
           '3xl': ['1.875rem', { lineHeight: '2.25rem' }],
           '4xl': ['2.25rem', { lineHeight: '2.5rem' }],
           '5xl': ['3rem', { lineHeight: '1' }],
         },
       },
     },
   }

Component Styling
===============

Components are styled using TailwindCSS utility classes:

Basic Components
--------------

.. code-block:: jsx
   :caption: Button component

   const Button = ({ children, variant = 'primary', size = 'md', ...props }) => {
     const variants = {
       primary: 'bg-primary-600 hover:bg-primary-700 text-white',
       secondary: 'bg-gray-200 hover:bg-gray-300 text-gray-800',
       danger: 'bg-red-600 hover:bg-red-700 text-white',
       outline: 'border border-primary-600 text-primary-600 hover:bg-primary-50',
       ghost: 'text-primary-600 hover:bg-primary-50',
     };
     
     const sizes = {
       sm: 'py-1 px-2 text-sm',
       md: 'py-2 px-4 text-base',
       lg: 'py-3 px-6 text-lg',
     };
     
     return (
       <button 
         className={`${variants[variant]} ${sizes[size]} rounded-md transition-colors focus:outline-none focus:ring-2 focus:ring-primary-500 focus:ring-offset-2`}
         {...props}
       >
         {children}
       </button>
     );
   };

.. code-block:: jsx
   :caption: Alert component

   const Alert = ({ type, message }) => {
     const bgColor = {
       success: 'bg-alert-success',
       warning: 'bg-alert-warning',
       danger: 'bg-alert-danger',
       info: 'bg-alert-info',
     }[type];
     
     const icons = {
       success: <CheckCircleIcon className="w-5 h-5" />,
       warning: <ExclamationIcon className="w-5 h-5" />,
       danger: <XCircleIcon className="w-5 h-5" />,
       info: <InformationCircleIcon className="w-5 h-5" />,
     };
     
     return (
       <div className={`${bgColor} text-white p-4 rounded-md shadow-md flex items-center`}>
         <div className="mr-3">
           {icons[type]}
         </div>
         <div>
           {message}
         </div>
       </div>
     );
   };

Complex Components
----------------

.. code-block:: jsx
   :caption: DataTable component

   const DataTable = ({ 
     columns, 
     data, 
     loading, 
     pagination, 
     onPageChange, 
     onRowClick 
   }) => {
     return (
       <div className="overflow-hidden shadow ring-1 ring-black ring-opacity-5 rounded-lg">
         {loading && (
           <div className="absolute inset-0 flex items-center justify-center bg-white bg-opacity-70 z-10">
             <Spinner size="lg" />
           </div>
         )}
         
         <table className="min-w-full divide-y divide-gray-300">
           <thead className="bg-gray-50">
             <tr>
               {columns.map((column) => (
                 <th
                   key={column.key}
                   scope="col"
                   className="px-3 py-3.5 text-left text-sm font-semibold text-gray-900"
                 >
                   {column.header}
                 </th>
               ))}
             </tr>
           </thead>
           <tbody className="divide-y divide-gray-200 bg-white">
             {data.length === 0 ? (
               <tr>
                 <td 
                   colSpan={columns.length} 
                   className="px-3 py-4 text-sm text-gray-500 text-center"
                 >
                   No data available
                 </td>
               </tr>
             ) : (
               data.map((row, rowIndex) => (
                 <tr 
                   key={row.id || rowIndex} 
                   onClick={() => onRowClick && onRowClick(row)}
                   className={onRowClick ? "cursor-pointer hover:bg-gray-50" : ""}
                 >
                   {columns.map((column) => (
                     <td
                       key={`${row.id}-${column.key}`}
                       className="whitespace-nowrap px-3 py-4 text-sm text-gray-500"
                     >
                       {column.render ? column.render(row) : row[column.key]}
                     </td>
                   ))}
                 </tr>
               ))
             )}
           </tbody>
         </table>
         
         {pagination && (
           <div className="bg-white px-4 py-3 flex items-center justify-between border-t border-gray-200 sm:px-6">
             <div className="flex-1 flex justify-between sm:hidden">
               <button
                 onClick={() => onPageChange(pagination.page - 1)}
                 disabled={pagination.page === 1}
                 className="relative inline-flex items-center px-4 py-2 border border-gray-300 text-sm font-medium rounded-md text-gray-700 bg-white hover:bg-gray-50"
               >
                 Previous
               </button>
               <button
                 onClick={() => onPageChange(pagination.page + 1)}
                 disabled={pagination.page === pagination.totalPages}
                 className="ml-3 relative inline-flex items-center px-4 py-2 border border-gray-300 text-sm font-medium rounded-md text-gray-700 bg-white hover:bg-gray-50"
               >
                 Next
               </button>
             </div>
             <div className="hidden sm:flex-1 sm:flex sm:items-center sm:justify-between">
               <div>
                 <p className="text-sm text-gray-700">
                   Showing <span className="font-medium">{((pagination.page - 1) * pagination.perPage) + 1}</span> to <span className="font-medium">{Math.min(pagination.page * pagination.perPage, pagination.total)}</span> of{' '}
                   <span className="font-medium">{pagination.total}</span> results
                 </p>
               </div>
               <div>
                 {/* Pagination controls */}
               </div>
             </div>
           </div>
         )}
       </div>
     );
   };

Theme Support
===========

The application supports both light and dark themes:

.. code-block:: jsx
   :caption: Theme context implementation

   import React, { createContext, useContext, useEffect, useState } from 'react';

   const ThemeContext = createContext();

   export const ThemeProvider = ({ children }) => {
     const [theme, setTheme] = useState('light');

     const toggleTheme = () => {
       const newTheme = theme === 'light' ? 'dark' : 'light';
       setTheme(newTheme);
       localStorage.setItem('theme', newTheme);
       document.documentElement.classList.toggle('dark');
     };

     useEffect(() => {
       // Check for user preference in localStorage
       const savedTheme = localStorage.getItem('theme');
       
       // If no preference, check system preference
       if (!savedTheme) {
         const prefersDark = window.matchMedia('(prefers-color-scheme: dark)').matches;
         setTheme(prefersDark ? 'dark' : 'light');
         if (prefersDark) {
           document.documentElement.classList.add('dark');
         }
       } else {
         setTheme(savedTheme);
         if (savedTheme === 'dark') {
           document.documentElement.classList.add('dark');
         }
       }
     }, []);

     return (
       <ThemeContext.Provider value={{ theme, toggleTheme }}>
         {children}
       </ThemeContext.Provider>
     );
   };

   export const useTheme = () => useContext(ThemeContext);

Dark mode styles are implemented using Tailwind's dark mode functionality:

.. code-block:: jsx
   :caption: Dark mode styling example

   const Card = ({ children }) => {
     return (
       <div className="bg-white dark:bg-gray-800 shadow-md rounded-lg p-6 text-gray-900 dark:text-gray-100">
         {children}
       </div>
     );
   };

Responsive Design
===============

The application is fully responsive, adapting to different screen sizes:

.. code-block:: jsx
   :caption: Responsive layout example

   const Dashboard = () => {
     return (
       <div className="container mx-auto px-4 sm:px-6 lg:px-8">
         <h1 className="text-2xl sm:text-3xl md:text-4xl font-bold mb-6">Dashboard</h1>
         
         <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
           <div className="col-span-1 md:col-span-2 lg:col-span-1">
             <MetricsCard title="System Status" />
           </div>
           <div className="col-span-1">
             <AlertsCard />
           </div>
           <div className="col-span-1">
             <RecentActivity />
           </div>
         </div>
         
         <div className="mt-8 grid grid-cols-1 lg:grid-cols-2 gap-6">
           <div className="col-span-1">
             <AccessChart />
           </div>
           <div className="col-span-1">
             <LocationMap />
           </div>
         </div>
       </div>
     );
   };

CSS-in-JS Integration
===================

For more complex styling needs, the application integrates with Emotion:

.. code-block:: jsx
   :caption: Emotion styling example

   import { css } from '@emotion/react';
   import styled from '@emotion/styled';

   const pulseAnimation = css`
     @keyframes pulse {
       0%, 100% {
         opacity: 1;
       }
       50% {
         opacity: 0.5;
       }
     }
   `;

   const StatusIndicator = styled.div`
     width: 12px;
     height: 12px;
     border-radius: 50%;
     background-color: ${props => 
       props.status === 'online' ? '#10b981' : 
       props.status === 'offline' ? '#ef4444' : 
       props.status === 'idle' ? '#f59e0b' : '#9ca3af'
     };
     animation: ${props => props.status === 'online' ? 'pulse 2s cubic-bezier(0.4, 0, 0.6, 1) infinite' : 'none'};
     ${pulseAnimation}
   `;

   const DeviceStatus = ({ status }) => (
     <div className="flex items-center">
       <StatusIndicator status={status} />
       <span className="ml-2 text-sm text-gray-700 dark:text-gray-300">
         {status.charAt(0).toUpperCase() + status.slice(1)}
       </span>
     </div>
   );

Animation
========

The application uses subtle animations to enhance the user experience:

.. code-block:: jsx
   :caption: Animation examples

   // Fade in transition for components
   const FadeIn = ({ children }) => (
     <div className="animate-fadeIn">
       {children}
     </div>
   );

   // Transition group for lists
   const AnimatedList = ({ items, renderItem }) => (
     <TransitionGroup>
       {items.map(item => (
         <CSSTransition
           key={item.id}
           timeout={300}
           classNames={{
             enter: 'opacity-0',
             enterActive: 'opacity-100 transition-opacity duration-300',
             exit: 'opacity-100',
             exitActive: 'opacity-0 transition-opacity duration-300'
           }}
         >
           {renderItem(item)}
         </CSSTransition>
       ))}
     </TransitionGroup>
   );

   // Loading skeleton
   const Skeleton = ({ lines = 1, className = '' }) => (
     <div className={className}>
       {Array.from({ length: lines }).map((_, i) => (
         <div 
           key={i} 
           className="h-4 bg-gray-200 dark:bg-gray-700 rounded animate-pulse mb-2"
         />
       ))}
     </div>
   );