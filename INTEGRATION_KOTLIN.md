# Inkrypt Videos Android Integration for Kotlin

This guide is intended for Android Projects that runs the latest Kotlin oriented codebase, where the
layouting is mainly using `Jetpack Compose` that is not supported for Java.

## Integration Steps

## [1] Importing SDK Library
The steps here are based on Android Studio `Koala`. The steps are mostly the 
same on other platforms.

### [1.1] Adding the Dependency
This guide introduces the latest approach recommended by Android Studio using `Kotlin DSL` based 
Gradle scripts.

#### Project Level `settings.gradle.kts` File
First, we need to register the `Maven` repository pointing to the current live SDK binaries. Find
the file on the project root level directory.

Find the `dependencyResolutionManagement` block then under it find `repositories` block, then add 
the repository URL. Notice the double quotes.

```kotlin
dependencyResolutionManagement {
    repositoriesMode.set(RepositoriesMode.FAIL_ON_PROJECT_REPOS)
    repositories {
        google()
        mavenCentral()
        // Add these lines
        maven {
            url = uri("https://github.com/inkryptvideos/android-maven-repo/raw/main/repo")
        }
    }
}
```

#### [1.2] Update Version Catalog
On the legacy project, we define the dependency importing on App Level `build.gradle` file. Now 
we use the new `Version Catalog`, represented as `libs.version.toml` file.

To include Inkrypt Video Player SDK to be recognizable by `build.gradle.kts`, let's define the
chosen SDK version and define the reference. Example below.
```toml
[versions]
# ...
# Define a custom name for the reference of the SDK version, for example "inkryptVideoPlayer"
inkryptVideoPlayer = "3.0.1"


# (Optional, if not defined before)
fragment = "1.8.1"
uiViewbinding = "1.6.8"


[libraries]
# ...
# Define a unique name referencing the SDK Gradle / Maven Package URL, `build.gradle.kts` file, The value of `version.ref` references the one we have defined under `versions`.
inkrypt-video-player = { module = "com.inkryptvideos.android:inkryptvideos-android", version.ref = "inkryptVideoPlayer" }


# (Optional, if not defined before)
androidx-fragment = { group = "androidx.fragment", name = "fragment", version.ref = "fragment" }
androidx-ui-viewbinding = { group = "androidx.compose.ui", name = "ui-viewbinding", version.ref = "uiViewbinding" }


[plugins]
# ...
```
Once done, perform a Gradle Sync to have everything recognizable from this point onward, just in
case. 

#### [1.3] Include Dependency, Build feature
Then within App level `build.gradle.kts`, add this under `dependencies` block:
```kotlin
dependencies {
    // ...
    implementation(libs.inkrypt.video.player)


    // # (Optional, if not included before)
    implementation(libs.androidx.fragment)
    implementation(libs.androidx.ui.viewbinding)
}
```
Then within the same `build.gradle.kts` file, under `android` block, look for `buildFeatures`
sub-block, then add a flag to enable View Binding feature.
```kotlin
android {
    // ...
    // Other sub-blocks
    // ...
    buildFeatures {
        compose = true
        viewBinding = true
    }
}
```
Once done, perform a Gradle Sync.

## [2] Player Usage
### [2.1] Define the Layout In A Layout File

To embed the `InkPlayerFragment` into the Composable tree, a `ViewBinding` bridging is necessary. 
Thus we need an additional `XML` layout to bridge the implementation, and that's why we added 
`viewBinding` flag under `build.gradle.kts`.

In this example, the file is named `player_fragment.xml` and the `id` is assigned the name 
`player_fragment`.

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.fragment.app.FragmentContainerView xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/player_fragment"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:name="com.inkryptvideos.android.InkPlayerFragment" />
```
It is expected to generate a `ViewBinding` class named `PlayerFragmentBinding`.

### [2.2] Binding the player view
```kotlin
// Notice that [ComponentActivity] is replaced with [FragmentActivity] to prevent runtime crash.
class MainActivity : FragmentActivity() {

    private var inkPlayer: InkPlayerFragment? = null

```

### Accessing the Player
The example here uses `Jetpack Compose` tree defined on `MainActivity.kt`, defining a basic 
Composable with the Player View and an `ElevatedButton` that triggers video playing.

```kotlin
// Notice that [ComponentActivity] is replaced with [FragmentActivity] to prevent runtime crash.
class MainActivity : FragmentActivity() {

    private var inkPlayer: InkPlayerFragment? = null

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        enableEdgeToEdge()

        setContent {
            DemoAppTheme {
                Scaffold(
                    modifier = Modifier.fillMaxSize(),
                ) { innerPadding ->
                    
                    Column(
                        // Provides safe area base padding from [Scaffold]
                        modifier = Modifier.padding(innerPadding),
                        // Distribution of elements start from center of the screen
                        verticalArrangement = Arrangement.Center
                    ) {

                        // Defines a button that plays the video on click
                        ElevatedButton(onClick = { playVideo() }) {
                            Text("Play Video")
                        }

                        // [AndroidViewBinding] is necessary to embed original Android Fragment into 
                        // Composable tree
                        //
                        // Assign the generated static method `inflate` from 
                        // the generated `PlayerFragmentBinding` class.
                        AndroidViewBinding(
                            PlayerFragmentBinding::inflate
                        ) {
                            // Obtain the Player reference
                            inkPlayer = playerFragment.getFragment()
                        }
                    }
                }
            }
        }
    }

    // A local function to inject parameters into the Player
    private fun playVideo() {
        // Defines the parameter
        val params: InkInitParams = InkInitParams.Builder()
            .setVideoId("YOUR_VIDEO_ID")
            .setOtp("YOUR_OTP") // Video will start playing right away when ready
            .setAutoplay(true)

            //Disable this line on release for maximum security..
            //.setTolerateDeveloperMode(true)

            .build()

        // Set the params
        inkPlayer?.setParams(params)
    }
}
```

This concludes the basic example to incorporate minimal working example of Video Player. You can
play different video using other Video ID and OTP pair.


### Player Controls
See more available controls [here](PLAYER_APIS.md).

### Read More
Using Java / Legacy Layout? Read more [here](INTEGRATION_JAVA.md).