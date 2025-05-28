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
