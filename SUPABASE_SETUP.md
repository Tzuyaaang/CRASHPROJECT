# Supabase Integration Setup Guide

This guide will help you complete the Supabase integration for your AccessPoint app.

## 1. Get Your Supabase Anon Key

1. Go to your Supabase dashboard: https://supabase.com/dashboard
2. Select your project (hqewyveercscskhmyjbx)
3. Navigate to **Settings** → **API**
4. Copy the **anon/public** key
5. Replace `YOUR_SUPABASE_ANON_KEY` in `app/utils/supabase.ts` with your actual key

## 2. Set Up Database Schema

1. In your Supabase dashboard, go to **SQL Editor**
2. Copy the contents of `supabase-schema.sql` and paste it into the SQL editor
3. Click **Run** to execute the SQL and create the necessary tables and policies

## 3. Configure Authentication Settings

1. In your Supabase dashboard, go to **Authentication** → **Settings**
2. **Disable email confirmation** for development (you can enable it later for production):
   - Uncheck "Enable email confirmations"
3. **Configure site URL** (for development):
   - Add `exp://localhost:8081` to the Site URL
   - Add `exp://localhost:8081` to the Redirect URLs

## 4. Test the Integration

1. Start your Expo development server:
   ```bash
   npm start
   ```

2. Test the registration flow:
   - Open the app
   - Navigate to the Register screen
   - Fill out the form with test data
   - Submit the form
   - Check your Supabase dashboard → **Authentication** → **Users** to see the new user

3. Test the login flow:
   - Use the same credentials to log in
   - Verify that you're redirected to the Home screen

## 5. Database Structure

The integration creates a `profiles` table with the following structure:

- `id` (UUID) - References auth.users(id)
- `email` (TEXT) - User's email address
- `phone` (TEXT) - User's phone number
- `first_name` (TEXT) - User's first name
- `last_name` (TEXT) - User's last name
- `gender` (TEXT) - User's gender (male/female/other)
- `age` (TEXT) - User's age
- `emergency_contact_name` (TEXT) - Emergency contact name
- `emergency_contact_number` (TEXT) - Emergency contact number
- `region` (TEXT) - User's region
- `city` (TEXT) - User's city
- `barangay` (TEXT) - User's barangay
- `profile_picture` (TEXT) - Optional profile picture URL
- `created_at` (TIMESTAMP) - Record creation time
- `updated_at` (TIMESTAMP) - Record last update time

## 6. Security Features

- **Row Level Security (RLS)** is enabled on the profiles table
- Users can only view and update their own profile data
- Automatic timestamp updates for created_at and updated_at fields
- Indexes on email and phone for faster lookups

## 7. Key Changes Made

### AuthContext Updates
- Replaced local storage authentication with Supabase Auth
- Added automatic session management
- Implemented real-time auth state listening
- Added support for both email and phone number login

### Database Integration
- Created profiles table to store user data
- Implemented proper foreign key relationships
- Added security policies for data access

### Error Handling
- Comprehensive error handling for auth operations
- User-friendly error messages
- Proper cleanup on logout

## 8. Production Considerations

Before deploying to production:

1. **Enable email confirmations** in Supabase Auth settings
2. **Update site URLs** to your production domain
3. **Set up proper CORS policies**
4. **Enable rate limiting** for auth endpoints
5. **Set up proper backup strategies** for your database
6. **Configure proper logging** for monitoring

## 9. Troubleshooting

### Common Issues:

1. **"Invalid API key" error**:
   - Make sure you've replaced `YOUR_SUPABASE_ANON_KEY` with your actual key

2. **"Table 'profiles' doesn't exist" error**:
   - Run the SQL schema from `supabase-schema.sql` in your Supabase dashboard

3. **"Email not confirmed" error**:
   - Disable email confirmations in Supabase Auth settings for development

4. **Login with phone number not working**:
   - The system will look up the email associated with the phone number
   - Make sure the phone number exists in the profiles table

### Debug Tips:

- Check the browser console for detailed error messages
- Use Supabase dashboard → **Logs** to see server-side errors
- Verify your database schema matches the expected structure

## 10. Next Steps

After successful integration, you can:

1. Add social login providers (Google, Facebook, etc.)
2. Implement password reset functionality
3. Add user profile picture upload
4. Set up real-time subscriptions for live data
5. Add push notifications for emergency contacts
6. Implement offline data synchronization

Your Supabase integration is now complete! The app will use Supabase for authentication and user data storage instead of local storage.
