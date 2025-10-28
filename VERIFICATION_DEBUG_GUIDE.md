# Email Verification Debug Guide

This guide will help you debug the email verification system and identify where the error is occurring.

## 🔍 **Debugging Steps**

### Step 1: Check Console Logs

When you verify the code, check the browser console (F12) for these logs:

1. **Email verification successful:** `Email verification successful: [user object]`
2. **Starting profile creation:** `Email verification success, starting profile creation...`
3. **Complete registration:** `Starting completeRegistration with userData: [data]`
4. **Current user:** `Current supabaseUser: [user object]`
5. **Creating profile:** `Creating profile for user ID: [id]`
6. **Profile created:** `Profile created successfully: [data]`

### Step 2: Check Database Schema

The most likely issue is that the `profiles` table doesn't exist. Run this SQL in your Supabase dashboard:

```sql
-- Check if profiles table exists
SELECT table_name 
FROM information_schema.tables 
WHERE table_schema = 'public' 
AND table_name = 'profiles';
```

If the table doesn't exist, create it:

```sql
-- Create profiles table
CREATE TABLE IF NOT EXISTS profiles (
  id UUID REFERENCES auth.users(id) ON DELETE CASCADE PRIMARY KEY,
  email TEXT UNIQUE NOT NULL,
  phone TEXT UNIQUE NOT NULL,
  first_name TEXT NOT NULL,
  last_name TEXT NOT NULL,
  gender TEXT NOT NULL CHECK (gender IN ('male', 'female', 'other')),
  age TEXT NOT NULL,
  emergency_contact_name TEXT NOT NULL,
  emergency_contact_number TEXT NOT NULL,
  region TEXT NOT NULL,
  city TEXT NOT NULL,
  barangay TEXT NOT NULL,
  profile_picture TEXT,
  created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
  updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- Enable Row Level Security
ALTER TABLE profiles ENABLE ROW LEVEL SECURITY;

-- Create policies
CREATE POLICY "Users can view own profile" ON profiles
  FOR SELECT USING (auth.uid() = id);

CREATE POLICY "Users can update own profile" ON profiles
  FOR UPDATE USING (auth.uid() = id);

CREATE POLICY "Users can insert own profile" ON profiles
  FOR INSERT WITH CHECK (auth.uid() = id);
```

### Step 3: Check Supabase Configuration

1. **Go to your Supabase dashboard**
2. **Check Authentication → Settings:**
   - Email confirmations: ✅ Enabled
   - Email confirmation method: **OTP** (not Link)

3. **Check Email Templates:**
   - Go to Authentication → Email Templates
   - Select "Confirm signup"
   - Make sure it contains `{{ .Token }}`

### Step 4: Test the Flow

1. **Clear any existing test users:**
   - Go to Authentication → Users
   - Delete test users

2. **Register a new user:**
   - Fill out the form
   - Check email for 6-digit code
   - Enter code in app

3. **Check console logs** for each step

## 🚨 **Common Issues & Solutions**

### Issue 1: "No authenticated user to complete registration"

**Cause:** Auth state not updated after email verification

**Solution:** The 1-second delay should fix this. If not, increase the delay:

```typescript
await new Promise(resolve => setTimeout(resolve, 2000));
```

### Issue 2: "Profile creation error"

**Cause:** Database table doesn't exist or RLS policies are blocking

**Solution:** 
1. Create the profiles table (see SQL above)
2. Check RLS policies
3. Verify user has permission to insert

### Issue 3: "Invalid verification code"

**Cause:** Code format or timing issues

**Solution:**
1. Make sure code is exactly 6 digits
2. Check if code has expired
3. Try resending the code

### Issue 4: Email not sending

**Cause:** Supabase email configuration

**Solution:**
1. Check Authentication → Settings
2. Verify email template
3. Check Supabase logs

## 🔧 **Alternative: Simplified Flow**

If the current flow is too complex, here's a simplified version:

```typescript
const handleEmailVerificationSuccess = useCallback(async () => {
  try {
    // Just redirect to home - let the auth state listener handle profile creation
    router.replace('/screens/Home/Home');
  } catch (error) {
    console.error('Error:', error);
    setErrors({ general: 'An error occurred. Please try again.' });
  }
}, [router]);
```

And update the auth state listener to create the profile automatically:

```typescript
// In AuthContext, update the auth state listener
if (event === 'SIGNED_IN' && session?.user) {
  // Check if profile exists, if not create it
  const { data: existingProfile } = await supabase
    .from('profiles')
    .select('id')
    .eq('id', session.user.id)
    .single();

  if (!existingProfile) {
    // Create profile with default data
    await supabase.from('profiles').insert({
      id: session.user.id,
      email: session.user.email,
      // ... other default values
    });
  }
  
  await loadUserProfile(session.user.id);
}
```

## 📊 **Debug Checklist**

- [ ] Console logs show verification success
- [ ] Profiles table exists in database
- [ ] RLS policies are correct
- [ ] User is authenticated after verification
- [ ] Profile creation succeeds
- [ ] User is redirected to home

## 🎯 **Next Steps**

1. **Run the SQL** to create the profiles table
2. **Test the flow** with a new user
3. **Check console logs** for detailed error information
4. **Report back** with the specific error message you see

The detailed logging I added will help identify exactly where the error is occurring! 🔍
