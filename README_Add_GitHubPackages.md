# Mopinion Mobile SDK for android - Addendum

Our standard instructions are for installations using Jitpack.

As an alternative, we also offer version 0.3.1 via GitHub packages. That however requires a GitHub account in order to use it.

The install instructions only differ in the app's build config for Android Studio, for anything else you can follow the steps in our main README:

### Android Studio (using GitHub packages)

#### Create a Github personal access token
You'll need this instead of a password. Create a github personal access token (PAT) if you don't have one yet, using [their instructions](https://docs.github.com/en/github/authenticating-to-github/creating-a-personal-access-token). You'll need to request [read:packages permissions](https://docs.github.com/en/packages/learn-github-packages/about-permissions-for-github-packages#about-scopes-and-permissions-for-package-registries) to access the SDK.

Put your user name and PAT in environment variables:

```
$ setenv GITHUBPKG_USERNAME yourgithubusername
$ setenv GITHUBPKG_APITOKEN yourgithubpat
```


#### <a name="configure">Configure your app's build config (using GitHub packages)</a>

In the main project `build.gradle` file add the following to make gradle use the PAT :

```gradle
buildscript {
    repositories {
        google()
        mavenCentral()
        ...
    }
    ...
}
...
allprojects {
    repositories {
        google()
        mavenCentral()
        // maven { url 'https://jitpack.io' }
        maven {
            url 'https://maven.pkg.github.com/mopinion/mopinion-sdk-android-web'
            credentials {
                username = GITHUBPKG_USERNAME
                password = GITHUBPKG_APITOKEN
            }
        }
    }
}
```

That is all. The remaining instructions are the same as in our main [README](README.md#configure-module).


