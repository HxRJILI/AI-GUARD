================
State Management
================

The application uses Redux for state management to handle the complex application state.

Store Structure
=============

.. figure:: /_static/redux-flow.png
   :alt: Redux State Flow
   :align: center
   
   Redux state management flow

The Redux store is organized into several slices:

.. code-block:: javascript
   :caption: Store structure

   {
     auth: {
       user: { id, name, role },
       token: "JWT_TOKEN",
       isAuthenticated: true,
       loginError: null,
       isLoading: false
     },
     users: {
       items: [...],
       selectedUser: { ... },
       loading: false,
       error: null,
       pagination: { page, perPage, total }
     },
     enrollment: {
       currentUser: { id, name },
       capturedImages: [...],
       enrollmentStep: 2,
       processingStatus: "idle",
       qualityScores: [...]
     },
     logs: {
       items: [...],
       filters: { location, timeRange, accessStatus },
       pagination: { page, perPage, total },
       loading: false
     },
     settings: {
       thresholds: { default: 0.75, highSecurity: 0.85 },
       notificationChannels: [...],
       systemSettings: { ... }
     },
     liveMonitoring: {
       activeFeeds: [...],
       recentEvents: [...],
       connectionStatus: "connected"
     },
     alerts: {
       items: [...],
       filters: { severity, status, timeRange },
       unreadCount: 5
     }
   }

Redux Toolkit Implementation
=========================

The application uses Redux Toolkit to reduce boilerplate and enforce best practices:

.. code-block:: javascript
   :caption: User slice example

   import { createSlice, createAsyncThunk } from '@reduxjs/toolkit';
   import { userService } from '../services/userService';
   
   export const fetchUsers = createAsyncThunk(
     'users/fetchUsers',
     async (params, { rejectWithValue }) => {
       try {
         const response = await userService.getUsers(params);
         return response.data;
       } catch (error) {
         return rejectWithValue(error.response.data);
       }
     }
   );
   
   const usersSlice = createSlice({
     name: 'users',
     initialState: {
       items: [],
       selectedUser: null,
       loading: false,
       error: null,
       pagination: {
         page: 1,
         perPage: 20,
         total: 0
       }
     },
     reducers: {
       selectUser: (state, action) => {
         state.selectedUser = action.payload;
       },
       clearSelectedUser: (state) => {
         state.selectedUser = null;
       },
       setUserFilters: (state, action) => {
         state.filters = {...state.filters, ...action.payload};
       }
     },
     extraReducers: (builder) => {
       builder
         .addCase(fetchUsers.pending, (state) => {
           state.loading = true;
           state.error = null;
         })
         .addCase(fetchUsers.fulfilled, (state, action) => {
           state.loading = false;
           state.items = action.payload.items;
           state.pagination = action.payload.pagination;
         })
         .addCase(fetchUsers.rejected, (state, action) => {
           state.loading = false;
           state.error = action.payload || 'Failed to fetch users';
         });
     }
   });
   
   export const { selectUser, clearSelectedUser, setUserFilters } = usersSlice.actions;
   export default usersSlice.reducer;

Action Types
==========

Key actions for each slice:

Authentication
------------

- ``AUTH_LOGIN_REQUEST``
- ``AUTH_LOGIN_SUCCESS``
- ``AUTH_LOGIN_FAILURE``
- ``AUTH_LOGOUT``
- ``AUTH_REFRESH_TOKEN``
- ``AUTH_UPDATE_USER``

User Management
-------------

- ``USERS_FETCH_REQUEST``
- ``USERS_FETCH_SUCCESS``
- ``USERS_FETCH_FAILURE``
- ``USER_CREATE``
- ``USER_UPDATE``
- ``USER_DELETE``
- ``USER_SELECT``

Enrollment
---------

- ``ENROLLMENT_SET_USER``
- ``ENROLLMENT_CAPTURE_IMAGE``
- ``ENROLLMENT_PROCESS_IMAGES``
- ``ENROLLMENT_COMPLETE``
- ``ENROLLMENT_RESET``
- ``ENROLLMENT_UPDATE_QUALITY``

Logs
----

- ``LOGS_FETCH_REQUEST``
- ``LOGS_FETCH_SUCCESS``
- ``LOGS_FETCH_FAILURE``
- ``LOGS_UPDATE_FILTERS``
- ``LOG_UPDATE_NOTES``
- ``LOG_MARK_IMPORTANT``

Selectors
========

Reusable selectors for accessing state:

.. code-block:: javascript
   :caption: Redux selectors

   // Authentication selectors
   export const selectCurrentUser = (state) => state.auth.user;
   export const selectIsAuthenticated = (state) => state.auth.isAuthenticated;
   export const selectAuthLoading = (state) => state.auth.loading;
   
   // User selectors
   export const selectAllUsers = (state) => state.users.items;
   export const selectSelectedUser = (state) => state.users.selectedUser;
   export const selectUserById = (state, userId) => 
     state.users.items.find(user => user.id === userId);
   export const selectUsersPagination = (state) => state.users.pagination;
   
   // Advanced composed selector
   export const selectActiveAdminUsers = createSelector(
     [selectAllUsers],
     (users) => users.filter(user => user.active && user.role === 'ADMIN')
   );

Middleware
=========

Custom middleware for specific functionality:

API Middleware
------------

Handles API communication and token refreshing:

.. code-block:: javascript
   :caption: API middleware

   const apiMiddleware = ({ dispatch, getState }) => next => async action => {
     if (!action.meta || !action.meta.api) {
       return next(action);
     }
   
     const { url, method, data, onSuccess, onError } = action.meta.api;
     const token = getState().auth.token;
   
     if (action.type.endsWith('REQUEST')) {
       try {
         const response = await apiService.request({
           url,
           method,
           data,
           headers: token ? { Authorization: `Bearer ${token}` } : {}
         });
   
         const successAction = {
           type: action.type.replace('REQUEST', 'SUCCESS'),
           payload: response.data
         };
   
         dispatch(successAction);
   
         if (onSuccess) {
           onSuccess(response.data);
         }
       } catch (error) {
         if (error.response && error.response.status === 401) {
           // Token expired, try refresh
           dispatch({ type: 'AUTH_REFRESH_TOKEN' });
         }
   
         const errorAction = {
           type: action.type.replace('REQUEST', 'FAILURE'),
           payload: error.response ? error.response.data : { message: error.message }
         };
   
         dispatch(errorAction);
   
         if (onError) {
           onError(error);
         }
       }
     }
   
     return next(action);
   };

WebSocket Middleware
------------------

Handles real-time updates via WebSocket:

.. code-block:: javascript
   :caption: WebSocket middleware

   const websocketMiddleware = ({ dispatch, getState }) => {
     let socket = null;
   
     return next => action => {
       switch (action.type) {
         case 'WS_CONNECT':
           if (socket !== null) {
             socket.close();
           }
   
           const token = getState().auth.token;
           socket = new WebSocket(`wss://api.aiguard.example.com/ws?token=${token}`);
   
           socket.onopen = () => {
             dispatch({ type: 'WS_CONNECTED' });
           };
   
           socket.onclose = () => {
             dispatch({ type: 'WS_DISCONNECTED' });
           };
   
           socket.onmessage = (event) => {
             const message = JSON.parse(event.data);
             
             switch (message.type) {
               case 'ACCESS_EVENT':
                 dispatch({
                   type: 'LIVE_MONITORING_NEW_EVENT',
                   payload: message.data
                 });
                 break;
               
               case 'NEW_ALERT':
                 dispatch({
                   type: 'ALERTS_NEW_ALERT',
                   payload: message.data
                 });
                 break;
                 
               // Handle other message types
             }
           };
           break;
   
         case 'WS_DISCONNECT':
           if (socket !== null) {
             socket.close();
           }
           socket = null;
           break;
   
         case 'WS_SEND':
           if (socket !== null) {
             socket.send(JSON.stringify(action.payload));
           }
           break;
   
         default:
           return next(action);
       }
     };
   };

Persistence
==========

The application uses Redux Persist for state persistence:

.. code-block:: javascript
   :caption: Redux persistence configuration

   import { persistReducer, persistStore } from 'redux-persist';
   import storage from 'redux-persist/lib/storage';
   import { encryptTransform } from 'redux-persist-transform-encrypt';
   
   const encryptor = encryptTransform({
     secretKey: 'my-secure-key',
     onError: function(error) {
       console.error('Persist encryption error:', error);
     }
   });
   
   const persistConfig = {
     key: 'root',
     storage,
     transforms: [encryptor],
     whitelist: ['auth', 'settings'], // Only persist these reducers
     blacklist: ['logs', 'liveMonitoring'], // Never persist these reducers
     throttle: 1000 // Only persist state once per second maximum
   };
   
   const persistedReducer = persistReducer(persistConfig, rootReducer);
   
   export const store = configureStore({
     reducer: persistedReducer,
     middleware: [thunk, apiMiddleware, websocketMiddleware]
   });
   
   export const persistor = persistStore(store);