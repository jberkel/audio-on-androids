!SLIDE

## Hallo. Wilkommen zu:
# Audio on Android(s)

<span class="left">
<img src="hello/twitter_newbird_blue.png"/>
</span>

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

Das unterliegende low-level Medienframework wurde in Android 2.0 komplett ausgetauscht
(opencore → stagefright)

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
* [SoundPool][]
* [AudioRecorder][]
* [Open SL ES][]

[MediaPlayer]: http://developer.android.com/reference/android/media/MediaPlayer.html
[MediaRecorder]: http://developer.android.com/reference/android/media/MediaRecorder.html
[AudioTrack]: http://developer.android.com/reference/android/media/AudioTrack.html
[SoundPool]: http://developer.android.com/reference/android/media/SoundPool.html
[Open SL ES]: http://www.khronos.org/opensles/

!SLIDE

# AudioTrack

Low-level PCM only output

<pre class="prettyprint">
AudioTrack track = new AudioTrack(
     AudioManager.STREAM_MUSIC, 44100,
     AudioFormat.CHANNEL_CONFIGURATION_MONO,
     AudioFormat.ENCODING_PCM_16BIT,
     bufferSize, AudioTrack.MODE_STREAM);
track.play();
track.write(new byte[] { 1b, 2b, 3b }, 0, 3);
</pre>


!SLIDE

# MediaPlayer

High-level Audio/Video player, unterstützt HTTP Streaming.

<pre class="prettyprint">
MediaPlayer player = new MediaPlayer();
player.setDataSource("http://foo.com/audio.mp3");
player.prepare();
player.start();
</pre>

Die eigentliche Funktionalität wird von mehreren nativen Frameworks bereitgestellt

!SLIDE

<embed src="hello/architecture.svg" height="90%" type="image/svg+xml"/>

!SLIDE

# MediaRecorder

Gegenstück zum Player, Aufnahme über das Mikrofon.

<br/>

Unterstützte Formate varieren stark mit der Plattform. [AAC encoding][] erst ab
[Android 2.3.3][].

<br/>

Im Vergleich zu iOS kein Hardware-support für en/decoding!

[AAC encoding]: http://developer.android.com/reference/android/media/MediaRecorder.OutputFormat.html#MPEG_4
[Android 2.3.3]: http://developer.android.com/sdk/android-2.3.3.html
[iOS]: http://developer.apple.com/library/ios/#DOCUMENTATION/AudioVideo/Conceptual/MultimediaPG/UsingAudio/UsingAudio.html

!SLIDE

# SoundPool

Low-latency, concurrent playback

<br/>

Wird normalerweise für Soundeffekte bei Spielen eingesetzt

!SLIDE 

# Open SL ES

Seit Android 2.3 - zugänglich mit dem Android NDK (C/C++).


!SLIDE

[andraudio]: http://code.google.com/p/andraudio/


