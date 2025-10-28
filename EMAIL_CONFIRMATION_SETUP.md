# Email Confirmation Setup Guide

This guide will help you configure email confirmation to work properly with your Expo app.

## 1. Supabase Dashboard Configuration

### Step 1: Update Site URL
1. Go to your Supabase dashboard: https://supabase.com/dashboard
2. Navigate to **Authentication** → **Settings**
3. Update the **Site URL**:
   - **Development**: `exp://localhost:8081`
   - **Production**: `accesspoint://`

### Step 2: Add Redirect URLs
In the same **Authentication** → **Settings** page, add these **Redirect URLs**:

```
exp://localhost:8081
exp://localhost:8081/--/screens/Home/Home
exp://localhost:8081/--/screens/AccessPoint/EmailConfirm/EmailConfirm
accesspoint://screens/Home/Home
accesspoint://screens/AccessPoint/EmailConfirm/EmailConfirm
```

### Step 3: Enable Email Confirmations
1. In **Authentication** → **Settings**
2. **Enable** "Confirm email" (check the box)
3. **Disable** "Enable email confirmations" for development (uncheck if you want to test without email confirmation)

## 2. How It Works

### Registration Flow:
1. User fills out registration form
2. Supabase sends confirmation email
3. User clicks "Confirm your email" in Gmail
4. Email link redirects to your app (not localhost)
5. App automatically logs in the user
6. User is redirected to Home screen

### Deep Link Structure:
- **Development**: `exp://localhost:8081/--/screens/AccessPoint/EmailConfirm/EmailConfirm?access_token=...&refresh_token=...&type=signup`
- **Production**: `accesspoint://screens/AccessPoint/EmailConfirm/EmailConfirm?access_token=...&refresh_token=...&type=signup`

## 3. Testing the Flow

### Option 1: With Email Confirmation (Recommended for Production)
1. Register a new user
2. Check your email for confirmation link
3. Click the confirmation link
4. App should open and automatically log you in

### Option 2: Without Email Confirmation (For Development)
1. In Supabase dashboard → **Authentication** → **Settings**
2. **Uncheck** "Enable email confirmations"
3. Users will be logged in immediately after registration

## 4. Troubleshooting

### Problem: Email link still goes to localhost
**Solution**: 
- Double-check your Supabase Site URL and Redirect URLs
- Make sure you've added all the URLs listed above
- Restart your Expo development server

### Problem: App doesn't open when clicking email link
**Solution**:
- Make sure your app is running (`npm start`)
- Try opening the link in a mobile device/emulator
- Check that the deep link scheme is correct

### Problem: "Invalid confirmation link" error
**Solution**:
- The link might be expired (Supabase links expire after 24 hours)
- Try registering a new user
- Check that the URL parameters are being passed correctly

### Problem: User not automatically logged in after confirmation
**Solution**:
- Check the browser console for errors
- Verify that the EmailConfirm component is handling the tokens correctly
- Make sure the AuthContext is properly listening for auth state changes

## 5. Production Considerations

When deploying to production:

1. **Update Site URL** to your production domain or app scheme
2. **Update Redirect URLs** to production URLs
3. **Enable email confirmations** for security
4. **Set up proper email templates** in Supabase
5. **Test the flow** on actual devices

## 6. Custom Email Templates (Optional)

You can customize the confirmation email:

1. Go to **Authentication** → **Email Templates**
2. Select "Confirm signup"
3. Customize the template with your branding
4. Make sure the confirmation URL uses your app's scheme

## 7. Security Notes

- Email confirmation links expire after 24 hours
- Tokens in the URL are temporary and secure
- The app automatically handles token exchange
- Users are logged in immediately after confirmation

Your email confirmation is now properly configured! Users will be redirected to your app instead of localhost when they click the confirmation link.
