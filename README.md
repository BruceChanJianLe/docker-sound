# Docker Sound

This repository stores information about getting sound in your container, be it docker or podman.

## Steps

1. Ensure your container has priviledge and `/dev/snd` is mounted

```bash
# Refer to https://github.com/BruceChanJianLe/docker-nvidia-ubuntu-ros/blob/master/scripts/start24.bash
  docker run -it \
    -d \
    -e DISPLAY \
    -e QT_X11_NO_MITSHM=1 \
    -e XAUTHORITY=$XAUTH \
    -v "$XAUTH:$XAUTH" \
    -v "/tmp/.X11-unix:/tmp/.X11-unix" \
    -v "/etc/localtime:/etc/localtime:ro" \
    -v "/dev/snd:/dev/snd" \
    -v "/media:/media" \
    --network host \
    --privileged \
    --security-opt seccomp=unconfined \
    --name $CONTAINERNAME \
    --cap-add=SYS_PTRACE \
    ubuntu24.04:v0.0.2-cnvros2-runtime
```

1. Add `asound.conf` in `/etc`

```bash
# Determine which hw runs for you
aplay --device "hw:2,0" cheer.wav
# Or
aplay --device "hw:0,0" cheer.wav
# Then add this file
sudoedit /etc/asound.conf
```

Note that if you are able to play sound with "hw:0,0" then you should
add the correct string.
```bash
pcm.!default {
        type plug
        slave {
                pcm "hw:2,0"
        }
}

ctl.!default {
        type hw
        card 0
}
```

After that, you should be able to play without specifying the hw.

```bash
aplay cheer.wav
```

## Reference

- link: https://github.com/dusty-nv/jetson-containers/issues/484
