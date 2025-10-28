# Expo Go Email Confirmation Setup Guide

This guide will help you configure email confirmation to work properly with Expo Go (no localhost redirects).

## 🎯 The Problem
When using Expo Go, email confirmation links redirect to `localhost` instead of your app because Supabase doesn't know how to redirect to Expo Go.

## ✅ The Solution
Configure Supabase to use your computer's IP address for deep linking with Expo Go.

## 📋 Step-by-Step Setup

### Step 1: Get Your Computer's IP Address

**Windows:**
```bash
ipconfig
```
Look for "IPv4 Address" under your network adapter (usually `192.168.1.x` or `10.0.0.x`)

**Mac/Linux:**
```bash
ifconfig | grep "inet "
```

### Step 2: Configure Supabase Dashboard

Go to your Supabase dashboard → **Authentication** → **Settings**

**Update Site URL:**
```
exp://192.168.1.10:8081
```
*(Replace `192.168.1.10` with your actual IP address)*

**Add Redirect URLs:**
```
exp://192.168.1.10:8081
exp://192.168.1.10:8081/--/screens/Home/Home
exp://192.168.1.10:8081/--/screens/AccessPoint/EmailConfirm/EmailConfirm
exp://localhost:8081
exp://localhost:8081/--/screens/Home/Home
exp://localhost:8081/--/screens/AccessPoint/EmailConfirm/EmailConfirm
```

### Step 3: Start Your Expo Development Server

```bash
npm start
```

**Important:** Make sure to start with the tunnel option:
- Press `s` to switch to Expo Go
- Press `t` to start tunnel mode (this helps with deep linking)

### Step 4: Test the Email Confirmation Flow

1. **Register a new user** in your app
2. **Check your email** for the confirmation link
3. **Click the confirmation link** on your phone/device
4. **The link should now open your Expo Go app** instead of localhost
5. **User should be automatically logged in**

## 🔧 How It Works

### Deep Link Structure:
When a user clicks the email confirmation link, it will redirect to:
```
exp://192.168.1.10:8081/--/screens/AccessPoint/EmailConfirm/EmailConfirm?access_token=...&refresh_token=...&type=signup
```

### The Flow:
1. **User registers** → Supabase sends confirmation email
2. **User clicks email link** → Opens Expo Go app (not localhost)
3. **App processes the link** → Extracts authentication tokens
4. **User is logged in** → Automatically redirected to Home screen

## 🚨 Troubleshooting

### Problem: Email link still goes to localhost
**Solutions:**
- Double-check your IP address in Supabase settings
- Make sure you're using the correct IP address (not localhost)
- Restart your Expo development server
- Try using tunnel mode (`t` in Expo CLI)

### Problem: App doesn't open when clicking email link
**Solutions:**
- Make sure Expo Go is installed on your device
- Ensure your device and computer are on the same network
- Try using tunnel mode for better connectivity
- Check that the deep link URL is correct

### Problem: "Invalid confirmation link" error
**Solutions:**
- The link might be expired (Supabase links expire after 24 hours)
- Try registering a new user
- Check the browser console for detailed error messages
- Verify that the URL parameters are being passed correctly

### Problem: User not automatically logged in after confirmation
**Solutions:**
- Check the Expo Go console for errors
- Verify that the EmailConfirm component is handling tokens correctly
- Make sure the AuthContext is properly listening for auth state changes
- Check that the Supabase configuration is correct

## 📱 Testing on Different Devices

### Same Network (Recommended):
- Use your computer's IP address: `exp://192.168.1.10:8081`
- Both devices must be on the same WiFi network

### Different Networks (Tunnel Mode):
- Use tunnel mode in Expo CLI
- This creates a public URL that works from anywhere
- Press `t` in the Expo CLI to enable tunnel mode

## 🔒 Security Notes

- Email confirmation links expire after 24 hours
- Tokens in the URL are temporary and secure
- The app automatically handles token exchange
- Users are logged in immediately after confirmation

## 🎉 Success!

Once configured correctly:
- ✅ Email links open your Expo Go app
- ✅ Users are automatically logged in
- ✅ No more localhost redirects
- ✅ Works on both development and production

Your email confirmation is now properly configured for Expo Go! 🚀
