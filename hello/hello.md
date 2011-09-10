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

Das unterliegende low-level Medienframework wurde in Android 2.3 komplett ausgetauscht
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

* [MediaPlayer][] + [MediaRecorder][]
* [AudioTrack][]
* [SoundPool][]
* [Open SL ES][]

[MediaPlayer]: http://developer.android.com/reference/android/media/MediaPlayer.html
[MediaRecorder]: http://developer.android.com/reference/android/media/MediaRecorder.html
[AudioTrack]: http://developer.android.com/reference/android/media/AudioTrack.html
[SoundPool]: http://developer.android.com/reference/android/media/SoundPool.html
[Open SL ES]: http://www.khronos.org/opensles/
!SLIDE

# MediaPlayer

High-level Audio/Video player, unterstützt HTTP Streaming.

<pre class="prettyprint">
MediaPlayer player = new MediaPlayer();
player.setDataSource("http://foo.com/audio.mp3");
player.prepare();
player.start();
</pre>

Die eigentliche Funktionalität wird von einem nativen Framework bereitgestellt.


[andraudio]: http://code.google.com/p/andraudio/
