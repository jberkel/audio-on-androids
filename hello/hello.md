!SLIDE

<br/>
<br/>

<img src="hello/android_headphones.jpg" class="right"/>

### Jan Berkel / SoundCloud
# Audio on Android(s)


<br/>
<br/>
<br/>
<br/>
<br/>
<br/>

## @jberkel

!SLIDE

# über mich

## Android lead dev bei SoundCloud in Berlin

<img src="hello/sound_heart.png" class="left"/>
<!-- ![SoundCloud Logo](hello/800x500_orange.png) -->

Audio-Sharing Plattform, gegründet 2007. Ca. 7 Mio User, web + API + native clients

!SLIDE

# Übersicht

<br/>

 * Erfahrungen + Herausforderungen
 * Android Audio APIs
 * Entwicklung mit dem NDK
 * Designaspekte / Android-Integration
 * HTML5 &lt;audio&gt;

!SLIDE

# SoundCloud Android app

![pic](hello/sc_recording.png)
![pic](hello/sc_share.png)
![pic](hello/sc_player.png)

record / share / stream

!SLIDE

# Herausforderungen

<br/>

Android hat über 5 verschiedene Audio-APIs, mit unterschiedlicher Unterstützung
in den Geräten

<br/>

Neue Geräte

<br/>

Implementierungsdetails ändern sich sehr schnell

<br/>

  * 2.1 Januar '10
  * 2.2 Mai '10
  * 2.3 Dez '10
  * 3.0 Februar '11

!SLIDE

# Herausforderungen (2)

<img src="hello/market_challenge.png"/>

sehr schwer allen Benutzern eine konsistente Experience zu bieten

!SLIDE

# import android.media.*
## Die verschienden Audio APIs

<br/>

* [AudioTrack][]
* [MediaPlayer][]
* [MediaRecorder][]
* [SoundPool][]
* [OpenSL ES][]

[MediaPlayer]: http://developer.android.com/reference/android/media/MediaPlayer.html
[MediaRecorder]: http://developer.android.com/reference/android/media/MediaRecorder.html
[AudioTrack]: http://developer.android.com/reference/android/media/AudioTrack.html
[SoundPool]: http://developer.android.com/reference/android/media/SoundPool.html
[OpenSL ES]: http://www.khronos.org/opensles/

!SLIDE

# AudioTrack

Ausgabe von “rohen” Audio-Daten (PCM)

    AudioTrack track = new AudioTrack(
         AudioManager.STREAM_MUSIC, 44100,
         AudioFormat.CHANNEL_CONFIGURATION_MONO,
         AudioFormat.ENCODING_PCM_16BIT,
         bufferSize, AudioTrack.MODE_STREAM);
    track.play();
    track.write(new byte[] { 1b, 2b, 3b }, 0, 3);

!SLIDE

# MediaPlayer

High-level Audio/Video player, unterstützt HTTP Streaming.

    MediaPlayer player = new MediaPlayer();
    player.setDataSource("http://foo.com/audio.mp3");
    player.prepare();
    player.start();

Die eigentliche Funktionalität wird von mehreren nativen Frameworks bereitgestellt

!SLIDE

# Architektur

<a href="https://docs.google.com/drawings/d/1s7NEFBzlJy_e52y2mdc-W1cFIkGsL4Cyfo9FI5silLI/edit?hl=en_GB">
<embed src="hello/architecture.svg" height="90%" type="image/svg+xml"/>
</a>

!SLIDE

# MediaRecorder

Gegenstück zum Player, Aufnahme über das Mikrofon / line-in.

<br/>

Unterstützte Formate varieren stark mit der Plattform. Gute Qualität ([AAC][]) erst ab
[Android 2.3.3][] möglich.

<br/>

Im Vergleich zu iOS keine Hardware-Unterstützung für en / decoding!

[AAC]: http://developer.android.com/reference/android/media/MediaRecorder.OutputFormat.html#MPEG_4
[Android 2.3.3]: http://developer.android.com/sdk/android-2.3.3.html
[iOS]: http://developer.apple.com/library/ios/#DOCUMENTATION/AudioVideo/Conceptual/MultimediaPG/UsingAudio/UsingAudio.html

!SLIDE

# SoundPool

Low-latency, concurrent playback

<br/>

Wird normalerweise für Soundeffekte bei Spielen eingesetzt

!SLIDE

# Ab 2.3: stagefright

<br/>

## Kompletter rewrite des Medienframeworks in Android 2.3.
### (OpenCORE → stagefright)

!SLIDE

# stagefright

<a href="https://docs.google.com/drawings/d/1TcO7CUBDW0FrzzU4nfkwZIgr_p5VqsthNzPjCQAFyqo/edit?hl=en_GB">
<embed src="hello/stagefright.svg" height="90%" type="image/svg+xml"/>
</a>

!SLIDE

# Unterschiede

<br/>

* Java API identisch,
* aber:  Unterschiede zur Laufzeit

<br/>

[Ermittlung][] des Frameworks + spezifischer Code notwendig

[Ermittlung]: http://stackoverflow.com/questions/4579885/determine-opencore-or-stagefright-framework-for-mediaplayer

!SLIDE

# hinzu kommt...

<br/>

manche 2.3+ Geräte benutzen *immer* noch OpenCORE (v.a. Samsung) - Erkennung
nicht immer 100% zuverlässig



    if (android.os.BUILD.SDK_INT > 8) {
      // enable seeking
    }

## fail!

!SLIDE

# “This app SUCKS right now.”

![pic](hello/sucks.png)

!SLIDE

# erschwertes debugging

### MediaPlayer.OnErrorListener
<img src="hello/mp_error.png" class="centered"/>

<blockquote>
<p>
All non-trivial abstractions, to some degree, are
<a href="http://www.joelonsoftware.com/articles/LeakyAbstractions.html">leaky</a>.
</p>
</blockquote>

The Law of Leaky Abstractions (Joel Spolsky)

!SLIDE

### include/media/stagefright/MediaErrors.h

    namespace android {
      enum {
          MEDIA_ERROR_BASE        = -1000,

          ERROR_ALREADY_CONNECTED = MEDIA_ERROR_BASE,
          ERROR_NOT_CONNECTED     = MEDIA_ERROR_BASE - 1,
          ERROR_UNKNOWN_HOST      = MEDIA_ERROR_BASE - 2,
          ERROR_CANNOT_CONNECT    = MEDIA_ERROR_BASE - 3,
          ERROR_IO                = MEDIA_ERROR_BASE - 4,
          // ...
      }
    }

!SLIDE

# ...und “issues”

<img src="hello/bug_delay.png" class="centered big"/>

[issue 15953][]

[issue 15953]: http://code.google.com/p/android/issues/detail?id=15953

!SLIDE

# Testen der häufigsten Geräte

<img src="hello/market_stats.png" class="centered big"/>


!SLIDE

# die alternative: go native

<br/>
Das bedeutet:
<br/>

* eigenes Streaming + Buffering
* dekodieren der Audiodaten mit nativen Code
* abspielen via AudioTrack-Interface

<br/>

→ Aufwand vs. Kontrolle

!SLIDE

# OpenSL ES

> OpenSL ES is an application-level C-language audio API designed for
> resource-constrained devices.

<br/>

Seit Android 2.3 - zugänglich mit dem [Android NDK][] (C/C++).

[Android NDK]: http://developer.android.com/sdk/ndk/index.html

!SLIDE

# OpenSL ES (2)

<a href="https://docs.google.com/drawings/d/1s7NEFBzlJy_e52y2mdc-W1cFIkGsL4Cyfo9FI5silLI/edit?hl=en_GB">
<embed src="hello/opensl_es.svg" height="90%" type="image/svg+xml"/>
</a>

!SLIDE

# OpenSL ES (3)

Vermeidet den overhead von JNI, löst jedoch nicht das Latenzproblem.

<br/>

Wichtig für realtime-Andwendungen, z.B. virtuelle Synthesizer.

<br/>

Idealerweise &lt; 10ms

<br/>

Störfaktoren: CPU time, GC-Zyklen, JNI, I/O


!SLIDE

# Implikationen von nativem Code

Audiocode leicht portierbar, z.Zt. nur ARMv7 (FPU), später auch x86

<br/>

Dank JNI problemloses Mischen von nativem und Java-Code möglich

!SLIDE

# Designaspekte von Audio apps

<br/>

### Ziel: harmonische Integration mit dem restlichen System

!SLIDE

# Hintergrundmusik

Android Service zwingend erforderlich

    public class MyPlaybackService extends Service {
      public void onCreate() {
        startForeground(SERVICE_ID, getNotification());
      }
    }

!SLIDE

# Energiesparen

<br/>

<img src="hello/battery.png" class="left big"/>

Mit WifiLocks, PowerLocks, ...

<br/>

Lifecycle: MediaPlayer.release(), AudioTrack.stop()

!SLIDE

# Notifikationen

"Ongoing notification" für den aktuellen Track

![pic](hello/spotify_browser.png)
![pic](hello/spotify_notification.png)
![pic](hello/spotify_player.png)

(Spotify)

!SLIDE

# Notifikationen (2)

Albumartwork in Notifications

<img src="hello/ubermusic_notification.png" class="big"/>
<img src="hello/ubermusic_player.png" class="big"/>

(UberMusic)

!SLIDE

## An die Hardware denken

<img src="hello/headphones.jpg" class="centered big"/>

!SLIDE

## Fernbedienung

<img src="hello/headphones_button.jpg" class="centered big"/>

!SLIDE


# Interesse daran anmelden

### registerMediaButtonEventReceiver (ab 2.2)

    AudioManager m = getAudioManager();

    m.registerMediaButtonEventReceiver(
      new ComponentName(getPackageName(),
      RemoteControl.class.getName()));

Receiver:

    public class RemoteControl extends BroadcastReceiver {
      public void onReceive(Context c, Intent i) {
        //
      }
    }

!SLIDE


# Nützliche Events

<br/>
KeyEvent.KEYCODE\_MEDIA\_

<br/>

 * [PLAY\_PAUSE][]
 * [PREVIOUS][]
 * [NEXT][]
 * [REWIND][]

[PLAY\_PAUSE]: http://developer.android.com/reference/android/view/KeyEvent.html#KEYCODE_MEDIA_PLAY_PAUSE
[PREVIOUS]: http://developer.android.com/reference/android/view/KeyEvent.html#KEYCODE_MEDIA_PLAY_PREVIOUS
[NEXT]: http://developer.android.com/reference/android/view/KeyEvent.html#KEYCODE_MEDIA_PLAY_NEXT
[REWIND]: http://developer.android.com/reference/android/view/KeyEvent.html#KEYCODE_MEDIA_PLAY_REWIND

!SLIDE

# Nützliche Events (2)

<br/>
ACTION\_AUDIO\_BECOMING\_NOISY

<br/>

<!-- Wird vom System gesendet wenn der Kopfhörer entfernt wird -->

!SLIDE

# Lockscreen

<img src="hello/lock_screen_player.png" class="left"/>

* com.android.music.metachanged
* com.android.music.playstatechanged

!SLIDE

# Darf ich Musik spielen?


    // Android 2.2+
    public void play() {
      AudioManager m = getAudioManager();
      m.requestAudioFocus(this, AudioManager.STREAM_MUSIC,
        AudioManager.AUDIOFOCUS_GAIN);
    }
    public void onAudioFocusChange(int change) {
      // play track if focus obtained
    }

Wichtig wenn mehrere apps gleichzeitig laufen

!SLIDE

# Wenn's Telefon klingelt

<img src="hello/incoming_call.png" class="right"/>

    TelephonyManager tmgr =
        getTelephonyManager();

    tmgr.listen(this,
        PhoneStateListener.LISTEN_CALL_STATE);


    public void onCallStateChanged(int state,
        String incomingNumber) {
      //
    }
!SLIDE

# Sharing is caring

Verarbeitung:

    <intent-filter>
      <action android:name="android.intent.action.SEND"/>
      <data android:mimeType="audio/*"/>
    </intent-filter>

Erstellung:

    <intent-filter>
      <action android:name="android.intent.action.GET_CONTENT"/>
      <data android:mimeType="audio/*"/>
    </intent-filter>

!SLIDE

# Audio senden

<img src="hello/tapemachine.png"/>

TapeMachine

!SLIDE

# Audio senden (2)

<img src="hello/tapemachine_share.png"/>

TapeMachine: Share

!SLIDE

# Audio im Browser

## HTML5: &lt;audio&gt;

    <audio controls>
      <source src="scotty.mp3" type="audio/mp3">
      <source src="scotty.ogg" type="audio/ogg">
      Your browser does not support the audio element.
    </audio>

<audio controls class="centered">
  <source src="hello/scotty.mp3" type="audio/mp3">
  Your browser does not support the audio element.
</audio>

!SLIDE

## HTML5 &lt;audio&gt; auf Android

<br/>

  * 2.1 / 2.2 Browser unsterstützt nur &lt;video&gt; Tag
  * Ab 2.3: &lt;audio&gt;


<br/>

### Getreue Umsetzung der HTML5 spec

!SLIDE

# html5test.com

<img src="hello/html5test_big.png" class="centered big"/>

!SLIDE

# flash is required

<img src="hello/thesixtyone.png" class="big"/>

!SLIDE

# m.soundcloud.com

<img src="hello/m_soundcloud_com.png" class="centered big"/>

!SLIDE

# Zusammenfassung

<br/>

 * Audio-Support in Android ist mächtig
 * schwer für alle Anwender / Geräte zu optimieren
 * erhöhter Entwicklungsaufwand
 * Wichtig ist gute Integration mit dem System

!SLIDE

# Ausblick

<br/>

* ?
* Support für mehr Hardware MIDI-Controller
* Optimierungen (Latenz, Hardware acceleration ...)
* Hoffentlich mehr Musik/Audio apps, bes. auf Tablets

!SLIDE

# Resourcen

* [code.google.com/p/andraudio](http://code.google.com/p/andraudio)
* [Advanced Android audio techniques][] - (Google I/O '10)
* [Last.fm Android app][] - https://github.com/c99koder/lastfm-android/
* [audioboo app][] - https://github.com/Audioboo/audioboo-android/

[andraudio]: http://code.google.com/p/andraudio/
[audioboo app]: https://github.com/Audioboo/audioboo-android/
[Last.fm Android app]: https://github.com/c99koder/lastfm-android/
[Advanced Android audio techniques]: http://www.google.com/events/io/2010/sessions/android-audio-techniques.html

!SLIDE

# Danke!
