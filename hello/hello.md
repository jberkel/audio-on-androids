!SLIDE

<br/>
<br/>

### Jan Berkel / SoundCloud Ltd.
# Audio on Android(s)

<!-- <img src="hello/twitter_newbird_blue.png" class="right"/> -->

<br/>
<br/>
<br/>

## @jberkel


!SLIDE

# etwas über mich

## Android lead dev bei SoundCloud in Berlin

![SoundCloud Logo](hello/800x500_orange.png)

!SLIDE

# SoundCloud

Audio sharing Plattform, gegründet 2007. Ca. 7 Mio User, Schwerpunkt liegt auf
den “content creators”.

![pic](hello/sound_heart.png)

!SLIDE

# SoundCloud Android app

![pic](hello/stream.png)

!SLIDE

# Record sounds

![pic](hello/recording.png)

!SLIDE

# & play them

![pic](hello/player.png)

!SLIDE

# challenges

Android hat 4 (!) verschiedene Audio-APIs, mit unterschiedlicher Unterstützung
in den Geräten

<br/>

Das unterliegende low-level Medienframework wurde in Android 2.3 komplett ausgetauscht
(OpenCORE → stagefright)

!SLIDE

# challenges (con't)

<span class="left">
<img src="hello/market_2.png"/>
</span>

sehr schwer allen Benutzern eine konsistente Experience zu bieten

!SLIDE

# import android.media.*
## Die verschienden Audio APIs

<br/>

* [AudioTrack][]
* [MediaPlayer][]
* [MediaRecorder][]
* [SoundPool][]
* [Open SL ES][]

[MediaPlayer]: http://developer.android.com/reference/android/media/MediaPlayer.html
[MediaRecorder]: http://developer.android.com/reference/android/media/MediaRecorder.html
[AudioTrack]: http://developer.android.com/reference/android/media/AudioTrack.html
[SoundPool]: http://developer.android.com/reference/android/media/SoundPool.html
[Open SL ES]: http://www.khronos.org/opensles/

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

<a href="https://docs.google.com/drawings/d/1s7NEFBzlJy_e52y2mdc-W1cFIkGsL4Cyfo9FI5silLI/edit?hl=en_GB">
<embed src="hello/architecture.svg" height="90%" type="image/svg+xml"/>
</a>

!SLIDE

# MediaRecorder

Gegenstück zum Player, Aufnahme über das Mikrofon.

<br/>

Unterstützte Formate varieren stark mit der Plattform. Gute Qualität ([AAC][]) erst ab
[Android 2.3.3][] möglich.

<br/>

Im Vergleich zu iOS kein Hardware-support für en/decoding!

[AAC]: http://developer.android.com/reference/android/media/MediaRecorder.OutputFormat.html#MPEG_4
[Android 2.3.3]: http://developer.android.com/sdk/android-2.3.3.html
[iOS]: http://developer.apple.com/library/ios/#DOCUMENTATION/AudioVideo/Conceptual/MultimediaPG/UsingAudio/UsingAudio.html

!SLIDE

# SoundPool

Low-latency, concurrent playback

<br/>

Wird normalerweise für Soundeffekte bei Spielen eingesetzt

!SLIDE

# Open SL ES

> OpenSL ES is an application-level C-language audio API designed for
> resource-constrained devices.

<br/>

Seit Android 2.3 - zugänglich mit dem [Android NDK][] (C/C++).

<br/>

Vermeidet den overhead von JNI, löst jedoch nicht das Latenzproblem.

[Android NDK]: http://developer.android.com/sdk/ndk/index.html

!SLIDE

# Latenz?

Wichtig für realtime-Andwendungen, z.B. virtuelle Synthesizer.

<br/>

Idealerweise &lt; 10ms (jede Millisekunde zählt)

<br/>

Störfaktoren: CPU time, GC-Zyklen, JNI, I/O

<br/>

→ Nativer Code mit einer low-level API

!SLIDE

# “This app SUCKS right now.”

![pic](hello/sucks.png)

!SLIDE

<a href="https://docs.google.com/drawings/d/1TcO7CUBDW0FrzzU4nfkwZIgr_p5VqsthNzPjCQAFyqo/edit?hl=en_GB">
<embed src="hello/stagefright.svg" height="90%" type="image/svg+xml"/>
</a>

!SLIDE

# moving goalposts
## (OpenCORE → stagefright)

Komplettes rewrite des Mediaframeworks in Android 2.3.

<br/>

Java API identisch, jedoch Unterschiede zur Laufzeit (kein seeking)

<br/>

[Ermittlung][] des Frameworks + spezifischer Code notwendig

[Ermittlung]: http://stackoverflow.com/questions/4579885/determine-opencore-or-stagefright-framework-for-mediaplayer

!SLIDE

# hinzu kommt...

<br/>

manche 2.3+ Geräte benutzen immer noch OpenCORE (v.a. Samsung) - Erkennung des
Frameworks nicht 100% zuverlässig

!SLIDE

# erschwertes debugging

<img src="hello/mp_error.png" class="centered medium"/>

!SLIDE

# bugs im audiosystem

[BUG-15953]: http://code.google.com/p/android/issues/detail?id=15953

!SLIDE

# die alternative: go native

Das bedeutet:
<br/>

* Eigenes Streaming + Buffering
* dekodieren der Audiodaten mit nativen Code
* Abspielen via AudioTrack-Interface

<br/>

→ hoher Aufwand aber mehr Kontrolle

!SLIDE

# Implikationen von nativem Code

Audiocode leicht portierbar, z.Zt. nur ARMv7 (FPU), zukünftig auch x86

<br/>

Dank JNI problemloses Mischen von nativem und Java-Code möglich

!SLIDE

# Testen der häufigsten Geräte

<img src="hello/market_stats.png" class="centered big"/>

!SLIDE

# Designaspekte von Audio apps

<br/>

## disclaimer: ich bin kein Designer.

<br/>

### Harmonische Integration von Audio apps im System

!SLIDE

# Hintergrundmusik

Android Service zwingend erforderlich

    // MyPlaybackService.java
    public void onCreate() {
        startForeground(SERVICE_ID, getNotification());
    }

!SLIDE

# Energiesparen

<img src="hello/battery.png" class="left big"/>

Mit WifiLocks, PowerLocks, ...

!SLIDE

# Notifications

"Ongoing notification" für den aktuellen Track

![pic](hello/spotify_browser.png)
![pic](hello/spotify_notification.png)
![pic](hello/spotify_player.png)

!SLIDE

## Albumartwork in Notifications

![pic](hello/ubermusic_notification.png)
![pic](hello/ubermusic_player.png)

!SLIDE

## An die Hardware denken

<img src="hello/headphones.jpg" class="centered big"/>

!SLIDE

## Fernbedienung

<img src="hello/headphones_button.jpg" class="centered big"/>

!SLIDE


# Interesse daran anmelden

    AudioManager m = getAudioManager();

    m.registerMediaButtonEventReceiver(
      new ComponentName(getPackageName(),
      RemoteControlReceiver.class.getName()));

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


    AudioManager m = getAudioManager();
    m.requestAudioFocus(this, AudioManager.STREAM_MUSIC,
      AudioManager.AUDIOFOCUS_GAIN);

Wichtig wenn mehrere apps gleichzeitig laufen

!SLIDE

# Wenn's Telefon klingelt

<img src="hello/incoming_call.png" class="right"/>

    TelephonyManager tmgr = getTelephonyManager();
    tmgr.listen(mPhoneStateListener, PhoneStateListener.LISTEN_CALL_STATE);

!SLIDE

# Sharing is caring

Android hat Datenaustausch bereits eingebaut:

    <intent-filter>
        <action android:name="android.intent.action.SEND"/>
        <data android:mimeType="audio/*"/>
    </intent-filter>

!SLIDE

# Audio senden

<img src="hello/tapemachine.png"/>

TapeMachine

!SLIDE

# Audio senden (2)

<img src="hello/tapemachine_share.png"/>

Share

!SLIDE

# Audio im Browser

!SLIDE

## HTML5 Audio

<br/>

  * 2.1 / 2.2 unsterstützen &lt;video&gt; Tag
  * Ab 2.3: &lt;audio&gt;
  * Getreue Umsetzung der HTML5 spec

!SLIDE

# HTML audio objekt

    <audio controls>
      <source src="foo.mp3" type="audio/mp3">
      <source src="foo.ogg" type="audio/ogg">
      Your browser does not support the audio element.
    </audio>

<audio controls class="centered">
  <source src="hello/foo.mp3" type="audio/mp3">
  Your browser does not support the audio element.
</audio>

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

# Ausblick

!SLIDE

# Resourcen

* [code.google.com/p/andraudio](http://code.google.com/p/andraudio)
* [audioboo][]
* [Last.fm Android app][]


[andraudio]: http://code.google.com/p/andraudio/
[audioboo]: https://github.com/Audioboo/audioboo-android/
[Last.fm Android app]: https://github.com/c99koder/lastfm-android/
