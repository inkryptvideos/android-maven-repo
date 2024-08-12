## Available APIs
### InkInitParams Class
Required to be passed onto `InkPlayerFragment` to load a video corresponding to specific Video ID. 
For security measure, an OTP string is also required to authenticate.
#### APIs
Values are mutated via `Builder` inner class.
| Method                | Argument(s) | Descriptions                                                                                              | Required |
|-----------------------|-------------|-----------------------------------------------------------------------------------------------------------|----------|
| setVideoId            | `String`    | Single String corresponds to Video ID configured from Inkrypt Dashboard                                   | Yes      |
| setOtp                | `String`    | Single String acting as authentication OTP to validate through DRM verification                           | Yes      |
| setAutoPlay           | `boolean`   | Whether the player should play the video right away when ready.                                           | Optional |
| setInitialAspectRatio | `float`     | A value to define custom Aspect Ratio of the video playback if the video allows it. Defaults to `16 : 9`. | Optional |
| build                 |             | Generates the final `InkInitParams` to be passed to `InkPlayerFragment` via `setParams` method.           | Yes      |

### InkPlayerFragment Class
The accessible `View` class to be visible by the consuming app. Exposes API to manage the internal 
Media Player.
#### APIs
##### Methods
| Method                                               | Argument(s)     | Returns            | Descriptions                                                                      |
|------------------------------------------------------|-----------------|--------------------|-----------------------------------------------------------------------------------|
| getPlayerSession (_Java_) / playerSession (_Kotlin_) |                 | `InkPlayerSession` | Returns current running Player Session                                            |
| getIsFullScreen (_Java_) / isFullScreen (_Kotlin_)   |                 | `boolean`          | Returns the full screen state of the Player                                       |
| setParams                                            | `InkInitParams` |                    | Provides params to load new media to be played by the player.                     |
| play                                                 |                 |                    | Plays current media of the player if possible                                     |
| pause                                                |                 |                    | Pause the player                                                                  |
| goFullScreen                                         |                 |                    | Force the player to enter Full Screen Mode                                        |
| isPlaying                                            |                 | `boolean`          |                                                                                   |
| isLoading                                            |                 | `boolean`          |                                                                                   |
| getVideoLength                                       |                 | `long`             | Length of the played media in milliseconds                                        |
| getCurrentSeekPosition                               |                 | `long`             | Current played position of the player in milliseconds                             |
| getCurrentChapter                                    |                 | `VideoChapter?`    | Current active `VideoChapter` on current playback                                 |
| getSelectedVideoQuality                              |                 | `String?`          | Current specified Video Quality, for example `720p`, `480p`, or `null` if `Auto`. |

[//]: # (##### Properties)
##### Statics
| Method                                             | Argument(s)     | Returns         | Descriptions                                                                      |
|----------------------------------------------------|-----------------|-----------------|-----------------------------------------------------------------------------------|
| getSdkVersion                                      |                 | `String`        | Current SDK Version                                                               |

### InkPlayerSession Class
Within `InkPlayerFragment` instance, you can obtain more details and controls via `InkPlayerSession` 
obtained from `getPlayerSession` / `playerSession` method.
#### APIs
##### Methods
| Method                                               | Argument(s)    | Returns               | Descriptions                                                                                                         |
|------------------------------------------------------|----------------|-----------------------|----------------------------------------------------------------------------------------------------------------------|
| getCurrentParams (_Java_) / currentParams (_Kotlin_) |                | `InkInitParams`       | Returns current active player params.                                                                                |
| resetPlayer                                          |                |                       | Stops the playback, resets the player by clearing current played media.                                              |
| seek                                                 | `long`         |                       | Seeks to specific playback position in milliseconds                                                                  |
| restart                                              |                |                       | Reset the player back to start of the playback                                                                       |
| toggleMute                                           |                |                       | Mutes the player if not muted, or undo the current mute status and reset the volume back to the point before muting. |
| setVolume                                            | `float`        |                       | Set specific volume value for the player between 0.0 to 1.0                                                          |
| updatePlaybackSpeed                                  | `float`        |                       | Adjust the playback speed of the player. Assigning value of 1.0 equals to normal speed.                              |
| getChapters (_Java_) / chapters (_Kotlin_)           |                | `List<VideoChapter>?` | Available video chapters of current media                                                                            |
| setChapter                                           | `VideoChapter` |                       | Manually set the active chapter of current playback, performing a seek in the process.                               |
| getChapterAtPosition                                 | `long`         | `VideoChapter?`       | Returns a {@link VideoChapter} at specific playback position in milliseconds                                         |
| getCurrentChapter                                    |                | `VideoChapter?`       | Current active `VideoChapter` on current playback                                                                    |
| reload                                               |                |                       | Reload current playback session                                                                                      |
|                                                      |                |                       |                                                                                                                      |
##### Properties
| Property  | Type          | Description                                                           |
|-----------|---------------|-----------------------------------------------------------------------|
| sessionId | String / UUID | Current Session ID, typically used for tracking and reporting issues. |

### VideoChapter Class
Reference to a specific time span within the playback session.
#### APIs
##### Properties
| Property | Type     | Description                           |
|----------|----------|---------------------------------------|
| start    | `long`   | Start point of the Chapter in seconds |
| title    | `String` | Chapter Name                          |