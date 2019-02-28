Mopidy for unraid with support for snapcast and icecast
It is based on whhoesj/mopidy with additin of Streaming and Youtube.

Frontend extensions
Iris
Mopidy Musicbox Webclient
Mopidy Party
Mopidy Simple webclient
API explorer
Local images


Backend extensions
Spotify
Streaming
Youtube
Spotify tunigo
Soundcloud
Scrobbler
Tunein
Local SQLite


- set up the docker
- set up the network to br0
- set up volume mount to /mnt/user/appdata/mopidy/mopidy.conf >> /mopidy.conf
- set up volume mount for tmp/snapfifo if you use snapcast /mnt/user/appdata/mopidy/tmp/ >> /tmp  
- set up volume mount for your local media 

generate the mopidy.conf file
Doc here https://docs.mopidy.com/en/latest/config/
example here https://github.com/wernight/docker-mopidy/blob/master/README.md
authentication for soundcloud and spotify https://www.mopidy.com/authenticate/

 
for audio you need a special config
for snapcast you have to generate the audio output in a pipe file /tmp/snapfifo this is mounted in both docker container

[audio]
output = audioresample ! audio/x-raw,rate=48000,channels=2,format=S16LE ! audioconvert ! wavenc ! filesink location=/tmp/snapfifo

icecast

[audio]
output = lamemp3enc ! shout2send async=false mount=mopidy ip=X.X.X.X port=8000 password=XXXXX


- set up snapcast from kevineye/snapcast 
- set Postargument to snapserver -s pipe:///data/snapfifo?name=Example&sampleformat=44100:16:2
- set up the network to br0 you have to reach your snapcast from the app or snap-client
- mount /mnt/user/appdata/mopidy/tmp/ >> /data for the snapfifo pipe file

 
- or set up icecast from infiniteproject/icecast
- set up the network to br0 you have to reach it over http
- set up env Parameter or use a xml config file
- btw: I had some trouble with stopping the radio by selecting the next track - 

take that config file https://raw.githubusercontent.com/xiph/Icecast-Server/master/conf/icecast.xml.in
mount it 
put an 1 sec silent mp3 file to /mnt/user/appdata/icecast/void.mp3 and mount it to /usr/share/icecast/web/void.mp3
edit the icecast.xml config:

  <!-- You may have multiple <listener> elements -->
       <listen-socket>
        <port>8000</port>
        <shoutcast-mount>/mopidy</shoutcast-mount>
        <shoutcast-mount>/void.mp3</shoutcast-mount>
    </listen-socket>

    <mount>
        <mount-name>/mopidy</mount-name>
        <fallback-mount>/void.mp3</fallback-mount>
        <fallback-override>1</fallback-override>
        <fallback-when-full>1</fallback-when-full>
    </mount>

    <mount>
        <mount-name>/void.mp3</mount-name>
        <dump-file>/void.mp3</dump-file>
        <burst-size>65536</burst-size>
    </mount>
