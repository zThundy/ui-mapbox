# Android Setup Guide - Mapbox Downloads Token

This guide explains how to configure the Mapbox Downloads Token required for building Android apps with this library.

## Why do I need this?

Starting with Mapbox SDK version 8.6.6+, Mapbox requires authentication to download their Android SDK. This library needs to download the Mapbox SDK during the build process, which requires a valid **Mapbox Secret Token** with `DOWNLOADS:READ` scope.

## Prerequisites

1. A Mapbox account (free tier available)
2. A Mapbox Secret Token with `DOWNLOADS:READ` scope

## Step-by-Step Setup

### Step 1: Get Your Mapbox Downloads Token

1. Go to [Mapbox Account](https://account.mapbox.com/)
2. Log in or create a free account
3. Navigate to the "Access tokens" section
4. Create a new **Secret token** or use an existing one
5. Ensure it has the `DOWNLOADS:READ` scope enabled
6. Copy the token (it starts with `sk.`)

**Important Notes:**
- This is a **SECRET token** (starts with `sk.`), NOT a public token
- Keep this token secure and never commit it to version control
- The public access token used in your app is different from this downloads token

### Step 2: Configure the Token

Choose one of the following methods:

#### Method A: Global Configuration (Recommended for Individual Developers)

This method stores the token in your user profile, keeping it out of your project files.

**On macOS/Linux:**
```bash
# Create or append to the global gradle.properties file
echo "MAPBOX_DOWNLOADS_TOKEN=sk.YOUR_SECRET_TOKEN_HERE" >> ~/.gradle/gradle.properties
```

**On Windows (PowerShell):**
```powershell
# Create or append to the global gradle.properties file
Add-Content $env:USERPROFILE\.gradle\gradle.properties "MAPBOX_DOWNLOADS_TOKEN=sk.YOUR_SECRET_TOKEN_HERE"
```

**Or manually:**
1. Create/open the file:
   - **macOS/Linux:** `~/.gradle/gradle.properties`
   - **Windows:** `C:\Users\YOUR_USERNAME\.gradle\gradle.properties`
2. Add this line:
   ```properties
   MAPBOX_DOWNLOADS_TOKEN=sk.YOUR_SECRET_TOKEN_HERE
   ```

**Advantages:**
- Token is not in your project repository
- Works for all projects on your machine
- Better security

#### Method B: Project-Level Configuration (Recommended for Teams)

This method stores the token in your project, useful when multiple team members need to build the project.

1. Create the file: `App_Resources/Android/gradle.properties`
2. Add this content:
   ```properties
   # Mapbox Downloads Token
   MAPBOX_DOWNLOADS_TOKEN=sk.YOUR_SECRET_TOKEN_HERE
   ```

**Important:** Add this file to `.gitignore` to prevent committing the token:
```gitignore
# In your .gitignore file
App_Resources/Android/gradle.properties
```

**For teams:** Document that developers need to create this file and share the token through secure channels (password manager, encrypted message, etc.)

#### Method C: Environment Variable

Set the `MAPBOX_DOWNLOADS_TOKEN` environment variable.

**On macOS/Linux (temporary - current session only):**
```bash
export MAPBOX_DOWNLOADS_TOKEN=sk.YOUR_SECRET_TOKEN_HERE
```

**On Windows Command Prompt (temporary):**
```cmd
set MAPBOX_DOWNLOADS_TOKEN=sk.YOUR_SECRET_TOKEN_HERE
```

**On Windows PowerShell (temporary):**
```powershell
$env:MAPBOX_DOWNLOADS_TOKEN="sk.YOUR_SECRET_TOKEN_HERE"
```

**For permanent environment variables:**
- **macOS/Linux:** Add the export command to `~/.bashrc`, `~/.zshrc`, or equivalent
- **Windows:** Use System Properties → Environment Variables

### Step 3: Verify the Setup

1. Clean any previous builds:
   ```bash
   ns clean
   ```

2. Try building your project:
   ```bash
   ns build android
   ```

If configured correctly, the build should succeed without authentication errors.

## How It Works

The library automatically configures the Gradle build to authenticate with Mapbox's Maven repository. The token is read in this order:

1. **Project gradle.properties** (`App_Resources/Android/gradle.properties`)
2. **Global gradle.properties** (`~/.gradle/gradle.properties`)
3. **Environment variable** (`MAPBOX_DOWNLOADS_TOKEN`)

You only need to configure ONE of these methods.

## Troubleshooting

### Build fails with "401 Unauthorized" or authentication error

**Possible causes:**
- Token is missing or incorrect
- Token doesn't have `DOWNLOADS:READ` scope
- Using a public token instead of a secret token

**Solutions:**
1. Verify your token starts with `sk.`
2. Check the token has `DOWNLOADS:READ` scope in your Mapbox account
3. Ensure no extra spaces or quotes in gradle.properties
4. Try cleaning the build: `ns clean`

### Token not being recognized

**Solutions:**
1. Check for typos in the token
2. Ensure `gradle.properties` has the correct format (no quotes, no extra spaces)
3. Restart your terminal/IDE after setting environment variables
4. For Android Studio: File → Invalidate Caches / Restart

### "Could not resolve com.mapbox.maps:android:X.X.X"

This usually means Gradle cannot authenticate with Mapbox.

**Solutions:**
1. Verify token is set correctly
2. Check your internet connection
3. Try cleaning Gradle caches:
   ```bash
   rm -rf ~/.gradle/caches/
   ns clean
   ```

### Still having issues?

1. Check the library's Android gradle files to see which version of Mapbox SDK is being used
2. Verify your Mapbox account is in good standing
3. Check [Mapbox documentation](https://docs.mapbox.com/android/maps/guides/install/) for any recent changes

## Security Best Practices

1. **Never commit tokens to version control**
   - Use `.gitignore` for project-level configuration
   - Use global configuration or environment variables when possible

2. **Rotate tokens periodically**
   - Generate new tokens in your Mapbox account
   - Update all locations where the token is configured

3. **Limit token scope**
   - Only grant `DOWNLOADS:READ` scope for this purpose
   - Don't reuse tokens with broader permissions

4. **Share tokens securely**
   - Use password managers or encrypted channels
   - Never share tokens in plain text emails or chat

## Additional Resources

- [Mapbox Android SDK Installation Guide](https://docs.mapbox.com/android/maps/guides/install/)
- [Gradle Properties Documentation](https://docs.gradle.org/current/userguide/build_environment.html#sec:gradle_configuration_properties)
- [NativeScript Documentation](https://docs.nativescript.org/)

## Questions?

If you encounter any issues not covered in this guide, please:
1. Check the main README for additional information
2. Search existing issues on the repository
3. Create a new issue with details about your setup and error messages
