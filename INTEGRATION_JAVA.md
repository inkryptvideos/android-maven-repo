# Inkrypt Videos Android Integration for Java

This guide is intended for legacy Android Projects that runs Java oriented codebase, where the 
layouting is strictly limited to XML based sources. `Jetpack Compose` is not supported for Java.

If you intend to use `Jetpack Compose`, which is the latest and recommended approach, check 
[here](_DOCS_02B_INTEGRATION_KOTLIN.md).

## Integration Steps


## [1] Importing SDK Library
The steps here are based on Android Studio `Koala`. The steps are mostly the 
same on other platforms.

### [1.1] Adding the Dependency
This guide introduces the deprecated approach used by most Java based Android Projects using 
old `settings.gradle` configurations.

#### [1.1.1]Project Level `settings.gradle` File
First, we need to register the `Maven` repository pointing to the current live SDK binaries. Find 
the file on the project root level directory.

Find the `repositories` block, then add the repository URL. Notice the single quotes.

```gradle
repositories {
    google()
    mavenCentral()
    
    // Add these lines
    maven {
        url 'https://github.com/inkryptvideos/android-maven-repo/raw/main/repo'
    }
}
```

#### [1.1.2] App Level `build.gradle` File
Then we register the dependency to Inkrypt Video Player under the `app` level `build.gradle` file.

Assuming the version of the SDK is `3.0.1`, we add it under `dependencies` block, which is usually 
located at the most bottom section of the file.
```gradle
dependencies {
    //...

    implementation 'com.inkryptvideos.android:inkryptvideos-android:3.0.1'
}
```
Once done, perform a Gradle Sync.


## [2] Player Usage
### Define the Player View on The Layout
The example here uses `RelativeLayout`, assumed under the name `activity_main.xml`, to place the 
Player View in the exact center of the Activity. (Notice the `android:layout_centerInParent` value 
is set to `true` which is `RelativeLayout` specific.).

First, define a `fragment` tag, assign it the name `com.inkryptvideos.android.InkPlayerFragment`. 
Then also assign it an `id` to be referenced later programmatically. The flag `android:keepScreenOn` 
ensures that the device does not auto dim the display when the Player View is visible.

`android:tag` is optional for ease of View lookup later.
```xml
<RelativeLayout>
    <!-- Example: Defines a Play Button above the Player View, with id `play_button` -->
    <Button
        android:id="@+id/play_button"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginTop="24dp"
        android:text="Play"
        android:layout_above="@id/ink_player_fragment"
    />
    <fragment
        android:name="com.inkryptvideos.android.InkPlayerFragment"
        android:id="@+id/ink_player_fragment"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:keepScreenOn="true"
        android:tag="PlayerFragment"
        android:layout_centerInParent="true"
        android:background="#000000"
    />
</RelativeLayout>
```

### Play A Video
Now that you have defined the layout, then we can reference the layout programmatically. The example 
here assumes your initial Activity is called `MainActivity.java`. It should look similar like this.

Let's add the reference to the `InkPlayerFragment` which is the View containing our video player. 
Android Studio usually assists with importing the necessary statements.

To play a video, we need to supply parameters to the Player in the form of `InkInitParams` instance. 
The example here shows a flow of clicking a button to play the video. First, we update the layout to 
include the helper button.

Then under `MainActivity.java` we define the reference to that button.

```java
class MainActivity extends ComponentActivity {
    
    // A local field stored on this Activity referencing the Player View
    private InkPlayerFragment playerFragment;
    
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        
        setContentView(R.layout.activity_main);
        
        InkPlayerFragment playerFragment = (InkPlayerFragment) getSupportFragmentManager()
                .findFragmentById(R.id.ink_player_fragment);
        
        // Obtain reference to the button
        Button playButton = findViewById(R.id.play_button);
        // Add on-click event to that button, referencing local function `playVideo`
        playButton.setOnClickListener(view -> playVideo());
        
        this.playerFragment = playerFragment;
    }
    
    // A local function to inject parameters into the Player
    private void playVideo() {
        // Defines the parameter
        InkInitParams params = new InkInitParams.Builder()
                .setVideoId("YOUR_VIDEO_ID")
                .setOtp("YOUR_OTP")
    
                //Disable this line on release for maximum security..
                //.setTolerateDeveloperMode(true)

                // Video will start playing right away when ready
                .setAutoplay(true)
                .build();
        
        // Set the params
        playerFragment.setParams(params);
    }
}
```
This concludes the basic example to incorporate minimal working example of Video Player. You can 
play different video using other Video ID and OTP pair.

### Player Controls
See more available controls [here](PLAYER_APIS.md).

### Read More
Using Kotlin? Read more [here](INTEGRATION_KOTLIN.md).

### Sample App
Please refer to [this project](https://github.com/inkryptvideos/android-sample-project)