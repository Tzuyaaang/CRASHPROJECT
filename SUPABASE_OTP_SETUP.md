# Supabase OTP Code Setup Guide

This guide will help you configure Supabase to send 6-digit verification codes instead of confirmation links.

## 🎯 The Problem

Your Supabase is currently sending "confirm your email" links instead of 6-digit codes. This needs to be changed in the Supabase dashboard configuration.

## ✅ Solution: Configure Supabase for OTP Codes

### Step 1: Update Authentication Settings

1. **Go to your Supabase Dashboard:**
   - Navigate to: [https://supabase.com/dashboard/project/hqewyveercscskhmyjbx/auth/users](https://supabase.com/dashboard/project/hqewyveercscskhmyjbx/auth/users)

2. **Go to Authentication → Settings:**
   - Click on "Authentication" in the left sidebar
   - Click on "Settings" tab

3. **Configure Email Settings:**
   - **Enable email confirmations:** ✅ Check this box
   - **Email confirmation method:** Select "OTP" (not "Link")
   - **Site URL:** Can be left as is for OTP flow

### Step 2: Update Email Templates

1. **Go to Authentication → Email Templates:**
   - Click on "Email Templates" tab

2. **Select "Confirm signup" template:**
   - This is the template used for new user registrations

3. **Replace the template content with:**

```html
<h2>Welcome to AccessPoint!</h2>

<p>Thank you for signing up. To complete your registration, please enter this verification code in the app:</p>

<div style="text-align: center; margin: 30px 0;">
  <h1 style="font-size: 36px; letter-spacing: 8px; color: #007AFF; font-family: monospace; background: #f0f0f0; padding: 20px; border-radius: 8px; display: inline-block;">{{ .Token }}</h1>
</div>

<p style="text-align: center; color: #666; font-size: 14px;">
  This code will expire in 24 hours.
</p>

<p style="text-align: center; color: #666; font-size: 14px;">
  If you didn't request this code, please ignore this email.
</p>
```

### Step 3: Test the Configuration

1. **Clear any existing test users:**
   - Go to Authentication → Users
   - Delete any test users you created

2. **Test the registration flow:**
   - Start your app: `npm start`
   - Register a new user
   - Check your email for the 6-digit code
   - Enter the code in the app

## 🔧 Alternative: Use Magic Link with OTP

If the above doesn't work, you can also use the magic link approach with OTP:

### Update your registration method:

```typescript
const register = useCallback(async (userData: UserData): Promise<boolean> => {
  try {
    // Use magic link signup which sends OTP codes
    const { data, error } = await supabase.auth.signInWithOtp({
      email: userData.email,
      options: {
        data: {
          first_name: userData.firstName,
          last_name: userData.lastName,
          phone: userData.phone,
          // ... other user data
        }
      }
    });

    if (error) {
      console.error('Auth registration error:', error.message);
      return false;
    }

    return true;
  } catch (error) {
    console.error('Error during registration:', error);
    return false;
  }
}, []);
```

## 🚨 Troubleshooting

### Problem: Still receiving confirmation links instead of codes

**Solutions:**
1. **Check email template:** Make sure you're using the "Confirm signup" template
2. **Verify OTP setting:** Ensure "Email confirmation method" is set to "OTP"
3. **Clear browser cache:** Refresh your Supabase dashboard
4. **Test with new email:** Try registering with a completely new email address

### Problem: Code not appearing in email

**Solutions:**
1. **Check spam folder:** The email might be filtered
2. **Verify email template:** Make sure `{{ .Token }}` is in the template
3. **Check Supabase logs:** Go to Logs to see if emails are being sent
4. **Test email delivery:** Try sending a test email from Supabase

### Problem: "Invalid verification code" error

**Solutions:**
1. **Check code format:** Make sure it's exactly 6 digits
2. **Verify code hasn't expired:** Codes expire after 24 hours
3. **Check for extra spaces:** Trim whitespace from the code
4. **Try resending:** Use the resend code functionality

## 📧 Email Template Variables

Supabase provides these variables for email templates:

- `{{ .Token }}` - The 6-digit verification code
- `{{ .ConfirmationURL }}` - The confirmation link (not needed for OTP)
- `{{ .Email }}` - The user's email address
- `{{ .SiteURL }}` - Your site URL

## 🎉 Success!

Once configured correctly:
- ✅ Users receive 6-digit codes via email
- ✅ Codes are entered directly in the app
- ✅ No more localhost redirect issues
- ✅ Better mobile user experience

## 📞 Need Help?

If you're still having issues:
1. Check the Supabase documentation on OTP authentication
2. Verify your email template syntax
3. Test with a fresh email address
4. Check Supabase logs for delivery status

Your OTP-based email verification should now work perfectly! 🚀
