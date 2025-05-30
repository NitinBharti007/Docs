# Authentication Flow Documentation

## 1. Login Process
**Location**: `src/components/forms/login-form.jsx`

- **Entry Point**: User visits `/login` route
- **Components Used**: `LoginForm`
- **Flow**:
  1. User enters email and password
  2. Form validates input fields
  3. Calls `supabase.auth.signInWithPassword()`
  4. On success:
     - Redirects to dashboard
     - Shows success message
  5. On failure:
     - Displays error message
     - Allows retry

## 2. Logout Process
**Location**: `src/components/common/Header.jsx`

- **Entry Point**: User clicks logout button in header
- **Components Used**: `Header`
- **Flow**:
  1. Calls `supabase.auth.signOut()`
  2. On success:
     - Clears local session
     - Redirects to login page
  3. On failure:
     - Displays error message

## 3. Forgot Password Process
**Location**: `src/components/forms/forgetPassword-form.jsx`

- **Entry Point**: User clicks "Forgot Password" on login page
- **Components Used**: `ForgotPasswordForm`
- **Flow**:
  1. User enters email address
  2. Calls `supabase.auth.signInWithOtp()` with:
     - Email address
     - `emailRedirectTo` set to `/reset-password`
     - `type: 'recovery'` in data
  3. On success:
     - Shows success message
     - Clears email field
  4. On failure:
     - Displays error message
     - Allows retry

## 4. Reset Password Process
**Location**: `src/components/forms/reset-password-form.jsx`

- **Entry Point**: User clicks reset link in email
- **Components Used**: `ResetPasswordForm`
- **Flow**:
  1. Component mounts and checks for active session
  2. If no session:
     - Shows "Invalid Reset Link" message
     - Provides option to request new link
  3. If valid session:
     - Shows password reset form
     - User enters new password and confirmation
  4. On submit:
     - Validates password match
     - Calls `supabase.auth.updateUser()`
  5. On success:
     - Redirects to login page
     - Shows success message
  6. On failure:
     - Displays error message
     - Allows retry

## 5. Route Protection
**Location**: `src/components/common/Routes.jsx`

- **Components Used**: `PublicRoute`, `PrivateRoute`
- **Flow**:
  1. `PublicRoute`:
     - Allows access to login, forgot password, and reset password pages
     - Redirects authenticated users to dashboard
  2. `PrivateRoute`:
     - Protects dashboard and other authenticated routes
     - Redirects unauthenticated users to login

## 6. Session Management
**Location**: `src/lib/supabaseClient.js`

- **Components Used**: `supabase` client
- **Features**:
  1. Automatic session persistence
  2. Session refresh handling
  3. Token management
  4. Error handling

## 7. Error Handling
- **Common Error Scenarios**:
  1. Invalid credentials
  2. Network issues
  3. Invalid/expired reset links
  4. Password mismatch
  5. Session expiration

## 8. Security Features
1. **Password Reset**:
   - One-time use reset links
   - Session validation
   - Secure token handling
2. **Route Protection**:
   - Protected routes
   - Public routes
   - Redirect handling
3. **Form Validation**:
   - Required fields
   - Password confirmation
   - Email format validation

## 9. User Experience
1. **Loading States**:
   - Button loading indicators
   - Disabled form during submission
2. **Feedback**:
   - Success messages
   - Error messages
   - Clear instructions
3. **Navigation**:
   - Back to login options
   - Request new reset link
   - Dashboard redirect

## 10. File Structure
```
src/
├── components/
│   ├── forms/
│   │   ├── login-form.jsx
│   │   ├── forgetPassword-form.jsx
│   │   └── reset-password-form.jsx
│   └── common/
│       ├── Header.jsx
│       └── Routes.jsx
└── lib/
    └── supabaseClient.js
```

## 11. Code Examples

### Login Form
```javascript
const handleSubmit = async (e) => {
  e.preventDefault()
  setError("")
  setLoading(true)

  try {
    const { error } = await supabase.auth.signInWithPassword({
      email,
      password
    })

    if (error) throw error
    navigate("/dashboard")
  } catch (error) {
    setError(error.message)
  } finally {
    setLoading(false)
  }
}
```

### Forgot Password Form
```javascript
const handleSubmit = async (e) => {
  e.preventDefault()
  setError("")
  setMessage("")
  setLoading(true)

  try {
    const { error } = await supabase.auth.signInWithOtp({
      email,
      options: {
        emailRedirectTo: `${window.location.origin}/reset-password`,
        data: {
          type: 'recovery'
        }
      }
    })

    if (error) throw error
    setMessage("Check your email for a password reset link.")
  } catch (error) {
    setError(error.message)
  } finally {
    setLoading(false)
  }
}
```

### Reset Password Form
```javascript
const handleSubmit = async (e) => {
  e.preventDefault()
  setError("")
  setLoading(true)

  if (password !== confirmPassword) {
    setError("Passwords do not match")
    setLoading(false)
    return
  }

  try {
    const { error } = await supabase.auth.updateUser({
      password: password
    })

    if (error) throw error
    navigate("/login", { 
      state: { 
        message: "Password updated successfully." 
      }
    })
  } catch (error) {
    setError(error.message)
  } finally {
    setLoading(false)
  }
}
```

## 12. Best Practices
1. **Security**:
   - Never store sensitive data in localStorage
   - Always validate user input
   - Use HTTPS for all API calls
   - Implement rate limiting for auth attempts

2. **Error Handling**:
   - Provide clear error messages
   - Log errors for debugging
   - Handle network errors gracefully

3. **User Experience**:
   - Show loading states
   - Provide clear feedback
   - Maintain session state
   - Handle edge cases

4. **Code Organization**:
   - Separate concerns
   - Reusable components
   - Consistent error handling
   - Clear file structure


# React + Vite

This template provides a minimal setup to get React working in Vite with HMR and some ESLint rules.

Currently, two official plugins are available:

- [@vitejs/plugin-react](https://github.com/vitejs/vite-plugin-react/blob/main/packages/plugin-react) uses [Babel](https://babeljs.io/) for Fast Refresh
- [@vitejs/plugin-react-swc](https://github.com/vitejs/vite-plugin-react/blob/main/packages/plugin-react-swc) uses [SWC](https://swc.rs/) for Fast Refresh

## Expanding the ESLint configuration

If you are developing a production application, we recommend using TypeScript with type-aware lint rules enabled. Check out the [TS template](https://github.com/vitejs/vite/tree/main/packages/create-vite/template-react-ts) for information on how to integrate TypeScript and [`typescript-eslint`](https://typescript-eslint.io) in your project.

# Supabase Row Level Security (RLS) Policies

This document outlines all Row Level Security (RLS) policies implemented in the Supabase database.

## Overview

The application implements role-based access control with two main user types:
- **Admin Users**: Have full CRUD (Create, Read, Update, Delete) access to all data
- **Client Users**: Have read-only access to data within their assigned clinics

## Table Policies

### 1. Clinics Table

```sql
-- Enable RLS
ALTER TABLE clinics ENABLE ROW LEVEL SECURITY;

-- Policy for SELECT (Read)
CREATE POLICY "Clinics: Admins or linked client can read"
ON clinics
FOR SELECT
TO public
USING (
  -- Allow admins to read all clinics
  EXISTS (
    SELECT 1 FROM users
    WHERE users.id = auth.uid()
    AND users.role = 'admin'
  )
  OR
  -- Allow clients to read their linked clinics
  EXISTS (
    SELECT 1 FROM clinic_users
    WHERE clinic_users.clinic_id = clinics.id
    AND clinic_users.user_id = auth.uid()
  )
);

-- Policy for INSERT (Create)
CREATE POLICY "Clinics: Only admins can create"
ON clinics
FOR INSERT
TO public
WITH CHECK (
  EXISTS (
    SELECT 1 FROM users
    WHERE users.id = auth.uid()
    AND users.role = 'admin'
  )
);

-- Policy for UPDATE
CREATE POLICY "Clinics: Only admins can update"
ON clinics
FOR UPDATE
TO public
USING (
  EXISTS (
    SELECT 1 FROM users
    WHERE users.id = auth.uid()
    AND users.role = 'admin'
  )
);

-- Policy for DELETE
CREATE POLICY "Clinics: Only admins can delete"
ON clinics
FOR DELETE
TO public
USING (
  EXISTS (
    SELECT 1 FROM users
    WHERE users.id = auth.uid()
    AND users.role = 'admin'
  )
);
```

### 2. Patients Table

```sql
-- Enable RLS
ALTER TABLE patients ENABLE ROW LEVEL SECURITY;

-- Policy for SELECT (Read)
CREATE POLICY "Patients: Admins or client clinic access"
ON patients
FOR SELECT
TO public
USING (
  -- Allow admins to read all patients
  EXISTS (
    SELECT 1 FROM users
    WHERE users.id = auth.uid()
    AND users.role = 'admin'
  )
  OR
  -- Allow clients to read patients from their clinics
  EXISTS (
    SELECT 1 FROM clinic_users
    WHERE clinic_users.clinic_id = patients.clinic_id
    AND clinic_users.user_id = auth.uid()
  )
);

-- Policy for INSERT (Create)
CREATE POLICY "Patients: Only admins can create"
ON patients
FOR INSERT
TO public
WITH CHECK (
  EXISTS (
    SELECT 1 FROM users
    WHERE users.id = auth.uid()
    AND users.role = 'admin'
  )
);

-- Policy for UPDATE
CREATE POLICY "Patients: Only admins can update"
ON patients
FOR UPDATE
TO public
USING (
  EXISTS (
    SELECT 1 FROM users
    WHERE users.id = auth.uid()
    AND users.role = 'admin'
  )
);

-- Policy for DELETE
CREATE POLICY "Patients: Only admins can delete"
ON patients
FOR DELETE
TO public
USING (
  EXISTS (
    SELECT 1 FROM users
    WHERE users.id = auth.uid()
    AND users.role = 'admin'
  )
);
```

### 3. Reports Table

```sql
-- Enable RLS
ALTER TABLE reports ENABLE ROW LEVEL SECURITY;

-- Policy for SELECT (Read)
CREATE POLICY "Reports: Admins or client for their clinic"
ON reports
FOR SELECT
TO public
USING (
  -- Allow admins to read all reports
  EXISTS (
    SELECT 1 FROM users
    WHERE users.id = auth.uid()
    AND users.role = 'admin'
  )
  OR
  -- Allow clients to read reports from their clinics
  EXISTS (
    SELECT 1 FROM clinic_users
    WHERE clinic_users.clinic_id = reports.clinic_id
    AND clinic_users.user_id = auth.uid()
  )
);

-- Policy for INSERT (Create)
CREATE POLICY "Reports: Only admins can create"
ON reports
FOR INSERT
TO public
WITH CHECK (
  EXISTS (
    SELECT 1 FROM users
    WHERE users.id = auth.uid()
    AND users.role = 'admin'
  )
);

-- Policy for UPDATE
CREATE POLICY "Reports: Only admins can update"
ON reports
FOR UPDATE
TO public
USING (
  EXISTS (
    SELECT 1 FROM users
    WHERE users.id = auth.uid()
    AND users.role = 'admin'
  )
);

-- Policy for DELETE
CREATE POLICY "Reports: Only admins can delete"
ON reports
FOR DELETE
TO public
USING (
  EXISTS (
    SELECT 1 FROM users
    WHERE users.id = auth.uid()
    AND users.role = 'admin'
  )
);
```

### 4. Users Table

```sql
-- Enable RLS
ALTER TABLE users ENABLE ROW LEVEL SECURITY;

-- Policy for SELECT (Read)
CREATE POLICY "Users: Admins and Self can read"
ON users
FOR SELECT
TO public
USING (
  -- Allow admins to read all users
  EXISTS (
    SELECT 1 FROM users
    WHERE users.id = auth.uid()
    AND users.role = 'admin'
  )
  OR
  -- Allow users to read their own data
  id = auth.uid()
);

-- Policy for INSERT (Create)
CREATE POLICY "Users: Only admins can create"
ON users
FOR INSERT
TO public
WITH CHECK (
  EXISTS (
    SELECT 1 FROM users
    WHERE users.id = auth.uid()
    AND users.role = 'admin'
  )
);

-- Policy for UPDATE
CREATE POLICY "Users: Only admins can update"
ON users
FOR UPDATE
TO public
USING (
  EXISTS (
    SELECT 1 FROM users
    WHERE users.id = auth.uid()
    AND users.role = 'admin'
  )
);

-- Policy for DELETE
CREATE POLICY "Users: Only admins can delete"
ON users
FOR DELETE
TO public
USING (
  EXISTS (
    SELECT 1 FROM users
    WHERE users.id = auth.uid()
    AND users.role = 'admin'
  )
);
```

## Access Control Summary

### Admin Users
- Can perform all CRUD operations on all tables
- Have full access to manage clinics, patients, reports, and users
- Can create, read, update, and delete any record

### Client Users
- Can only READ data
- Can only view clinics they are linked to
- Can only view patients from their assigned clinics
- Can only view reports from their assigned clinics
- Can only view their own user data
- Cannot create, update, or delete any records

## Implementation Notes

1. All tables have RLS enabled
2. Each table has separate policies for SELECT, INSERT, UPDATE, and DELETE operations
3. Policies use EXISTS clauses to check user roles and relationships
4. The `clinic_users` table is used to manage clinic-client relationships
5. User authentication is handled through Supabase Auth
6. The `auth.uid()` function is used to identify the current user

## Security Considerations

1. All policies are enforced at the database level
2. Policies are applied before any data is returned to the client
3. Even if the application layer is compromised, the database remains secure
4. Regular audits of access patterns are recommended
5. Monitor for any unauthorized access attempts

## Maintenance

To modify these policies:

1. Go to Supabase Dashboard > Authentication > Policies
2. Select the table you want to modify
3. Click on the policy you want to edit
4. Make your changes
5. Click "Save Policy"

Remember to test any policy changes thoroughly in a development environment before applying them to production.
