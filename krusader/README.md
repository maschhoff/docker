# Krusader Docker with X11 forward

This Docker will create an image with Krusader Filebrowser installed.
If you configure it right you´ll end up with Krusader running on your Desktop with X11 forward.

1. Put this Dockerfile in a folder on your machine running docker
2. run "docker build -t krusader."
3. On Windows start "Xming.exe -ac" -ac is important for forwading from the Docker container.
4. Start ssh -X or putty.exe -X (enable X11 forwarding)
5. Run the docker container using:
```
docker run -ti --rm -e DISPLAY="10.10.10.10:0" -v /mnt/user/:/UNRAID krusader /usr/bin/krusader --left=/UNRAID --right=/UNRAID
```
10.10.10.10 < is the ip adress of the machine running Xming -ac
with -v /mnt/user:/UNRAID - I´ll mount a folder to /UNRAID from my local volume from witch I´ll manage data.
--rm will delete the container after stopping.

Tip: There will always be a "first time start" promt. run the docker container without --rm and click though the krusader startup things than close krusader.
After this enter "docker ps -a" and "docker xy commit name" - after this run docker from your new commited image.

Thanks to: https://github.com/baudren/NoteOrganiser/wiki/Docker,-QML,-XServer-on-Windows
