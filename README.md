# matlab-docker-desktop
Docker image with enough dependencies to support a mounted-in Matlab which can be run and then X forwarded to a Linux host. It is based on benjamin-heasly's matlab-support.

This docker image gives you an Ubuntu 18.04 environment capable of supporting Matlab.  For licensing reasons, you must already have your own Matlab installed on the Docker host.

To run Matlab inside the container, you have to provide three things at launch time:
 - Mount in a Matlab installation at `/usr/local/MATLAB/from-host`.
 - Spoof the container MAC address to match you own Matlab license file.
 - Mount your .matlab/ preferences folder into the container. (optional, but things may get weird if you don't)

# Example usages
These expect you to define some local information:
 - `MATLAB_ROOT` is your matlab installation on the Docker host, perhaps `/usr/local/MATLAB/R2016a`. Could also be a docker volume.
 - `MATLAB_LOGS` is optional path on the Docker host to receive Matlab logs, perhaps `~/matlab-logs`.
 - `MATLAB_MAC_ADDRESS` is the MAC address associated with your own Matlab License, of the form `00:00:00:00:00:00`.

### To lauch MATLAB and display the GUI on the host, first open X11 connections to docker:
```
xhost +local:docker
```
Then invoke the container like this:
```
docker run --rm -t --user $(id -u):$(id -u) -e DISPLAY=$DISPLAY -e J2D_D3D=false -e _JAVA_AWT_WM_NONREPARENTING=1 -v /tmp/.X11-unix:/tmp/.X11-unix -v "$MATLAB_ROOT":/MATLAB-install -v ~/.matlab:/.matlab -v "$MATLAB_LOGS":/var/log/matlab --mac-address="$MATLAB_MAC_ADDRESS" matlab-docker-desktop /MATLAB-install/bin/matlab
```

