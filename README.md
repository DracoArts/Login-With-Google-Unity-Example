
# Welcome to DracoArts

![Logo](https://dracoarts-logo.s3.eu-north-1.amazonaws.com/DracoArts.png)




#  Login With Google  Unity Example
 Firebase Google Sign-In in Unity allows users to authenticate using their Google accounts, leveraging Firebase Authentication services. This integration provides a secure, standardized way to handle user identity, manage sessions, and access Google user data (email, profile, etc.) within Unity applications.

 # Key Components
 ##  Firebase Authentication

- Manages user identities and authentication states.

- Supports multiple providers (Google, Facebook, Email/Password, etc.).

- Handles token generation, session persistence, and security.

## Google Sign-In SDK

- Handles the OAuth 2.0 flow for Google accounts.

- Retrieves user credentials (ID token, access token, email, profile data).

- Works on Android, iOS, and Web (via Unity).

## Unity Integration

- Uses Firebase Unity SDK for cross-platform compatibility.

- Requires platform-specific setup (Android, iOS).

 # How It Works
 ##  User Initiates Sign-In

- The Unity app triggers Google's OAuth flow (via GoogleSignIn API).

- User selects a Google account and grants permissions.

## Google Returns Credentials

- Google provides an ID token (JWT) containing user details.

- Firebase uses this token to authenticate the user.

## Firebase Authentication

- Firebase validates the token and creates a user session.

- Returns a FirebaseUser object with UID, email, display name, etc.

## Session Management

- Firebase handles token refresh automatically.

- Users stay logged in unless explicitly signed out.
# Features & Benefits
✅ Seamless Cross-Platform Login

Works on Android, iOS, and WebGL (with some limitations).

✅ Secure Authentication

Uses OAuth 2.0 and Firebase’s security rules.

✅ User Data Access

Retrieve Google profile info (name, email, profile picture).

✅ Persistent Sessions

Firebase manages login state even after app restarts.

✅ Integration with Firebase Services

Works with Firestore, Realtime Database, Cloud Functions, etc.
# Limitations & Considerations
## ⚠ Platform Dependencies

Requires different setups for Android (SHA-1 fingerprint) and iOS (URL schemes).

## ⚠ Editor Testing Limitations

Google Sign-In may not work properly in Unity Editor; test on real devices.

## ⚠ Privacy Compliance

Must comply with Google’s OAuth policies (privacy policy URL, consent screen).

## ⚠ Internet Requirement

Users must be online for initial sign-in.

# Prerequisites
- Unity Project (2019.4 or newer recommended)

- Firebase Account (https://firebase.google.com/)

- Firebase SDK for Unity (version 8.6.2 or newer)

# Setup Steps
## 1. Set up Firebase Project
 - Go to Firebase Console

- Create a new project or select an existing one

- Enable Google authentication:

- Go to Authentication → Sign-in method

- Enable Google

- Provide a project support email

 - Save changes
 ## 2. Configure OAuth Consent Screen
 - Go to Google Cloud Console

 - Select your project

- Navigate to "APIs & Services" → "OAuth consent screen"

- Set up the consent screen (External or Internal)

- Add required scopes (email, profile, openid)

- Add your developer email as a test user
## 3. Get Web Client ID
- In Google Cloud Console, go to "APIs & Services" → "Credentials"

- Note down the "Web Client ID" (you'll need this later)

- Add authorized domains:
- Your Firebase auth domain (found in Firebase project settings)
## Set up Unity Project
- Import Firebase Unity SDK:

- Download from Firebase Unity SDK

- Import FirebaseAuth.unitypackage

 # Platform-Specific Setup
# Android Setup
 - Generate SHA-1 fingerprint:

    keytool -list -v -keystore ~/.android/debug.keystore -alias androiddebugkey -storepass android -keypass android
-  Add SHA-1 to Firebase project settings

- Download google-services.json and place in Assets/ folder

# iOS Setup
- Add your iOS bundle ID in Firebase project settings

- Download GoogleService-Info.plist and place in Assets/ folder

- Enable Keychain Sharing in Player Settings
## Usage/Examples


    public string GoogleAPI = "add your webclient id here";
    private GoogleSignInConfiguration configuration;

    Firebase.Auth.FirebaseAuth auth;
    Firebase.Auth.FirebaseUser user;

    public TextMeshProUGUI Username, UserEmail;

    public Image UserProfilePic;
    private string imageUrl;
    private bool isGoogleSignInInitialized = false;
    private void Start()
    {
        InitFirebase();
    }

    void InitFirebase()
    {
        auth = Firebase.Auth.FirebaseAuth.DefaultInstance;
    }

    public void Login()
    {
        if (!isGoogleSignInInitialized)
        {
            GoogleSignIn.Configuration = new GoogleSignInConfiguration
            {
                RequestIdToken = true,
                WebClientId = GoogleAPI,
                RequestEmail = true
            };

            isGoogleSignInInitialized = true;
        }
        GoogleSignIn.Configuration = new GoogleSignInConfiguration
        {
            RequestIdToken = true,
            WebClientId = GoogleAPI
        };
        GoogleSignIn.Configuration.RequestEmail = true;

        Task<GoogleSignInUser> signIn = GoogleSignIn.DefaultInstance.SignIn();

        TaskCompletionSource<FirebaseUser> signInCompleted = new TaskCompletionSource<FirebaseUser>();
        signIn.ContinueWith(task =>
        {
            if (task.IsCanceled)
            {
                signInCompleted.SetCanceled();
                Debug.Log("Cancelled");
            }
            else if (task.IsFaulted)
            {
                signInCompleted.SetException(task.Exception);

                Debug.Log("Faulted " + task.Exception);
            }
            else
            {
                Credential credential = Firebase.Auth.GoogleAuthProvider.GetCredential(((Task<GoogleSignInUser>)task).Result.IdToken, null);
                auth.SignInWithCredentialAsync(credential).ContinueWith(authTask =>
                {
                    if (authTask.IsCanceled)
                    {
                        signInCompleted.SetCanceled();
                    }
                    else if (authTask.IsFaulted)
                    {
                        signInCompleted.SetException(authTask.Exception);
                        Debug.Log("Faulted In Auth " + task.Exception);
                    }
                    else
                    {
                        signInCompleted.SetResult(((Task<FirebaseUser>)authTask).Result);
                        Debug.Log("Success");
                        user = auth.CurrentUser;
                        Username.text = user.DisplayName;
                        UserEmail.text = user.Email;

                        StartCoroutine(LoadImage(CheckImageUrl(user.PhotoUrl.ToString())));
                    }
                });
            }
        });
    }
    private string CheckImageUrl(string url)
    {
        if (!string.IsNullOrEmpty(url))
        {
            return url;
        }
        return imageUrl;
    }

    IEnumerator LoadImage(string imageUri)
    {
        UnityWebRequest www = UnityWebRequestTexture.GetTexture(imageUri);
        yield return www.SendWebRequest();

        if (www.result == UnityWebRequest.Result.Success)
        {
            Texture2D texture = DownloadHandlerTexture.GetContent(www);
            // Use the loaded texture here
            Debug.Log("Image loaded successfully");
            UserProfilePic.sprite = Sprite.Create(texture, new Rect(0, 0, texture.width, texture.height), new Vector2(0, 0));
        }
        else
        {
            Debug.Log("Error loading image: " + www.error);
        }


    }


## Authors

- [@MirHamzaHasan](https://github.com/MirHamzaHasan)
- [@WebSite](https://mirhamzahasan.com)


## 🔗 Links

[![linkedin](https://img.shields.io/badge/linkedin-0A66C2?style=for-the-badge&logo=linkedin&logoColor=white)](https://www.linkedin.com/company/mir-hamza-hasan/posts/?feedView=all/)
## Documentation

[Firebase Unity](https://firebase.google.com/docs/auth/unity/google-signin)


## Tech Stack
**Client:** Unity,C#

**Plugin:** Firebase



