### EBPF k8s sidecar debugger

## Motivation

This is a template to run ebpf debugging tools as a k8s sidecar container within a pod.
It uses the [ebpf-tracer](docker-hub.sandstorm.de/public-containers/ebpf-tracer:latest) docker image.

## Requirements

The pod should run in a k8s node with linux kernel headers installed
apt install linux-headers-$(uname-r)

The pod must share PID namespace with the host node (hence the hostPid: true)

Both containers must run in privileged mode (hence the privileged mode in security context). Why? I do not know :kekw:

I ran this in KIND, and the node did not have linux headers installed. I used [kubectl-node-shell](https://github.com/kvaps/kubectl-node-shell) in order to install them in the host node.

Also, the debug container lacked modprobe (something to do with kernel modules), so I installed it within the debug container.
apt install kmod

## Steps

This was tested using kind 0.18.0, Ubuntu 22.04.1, headers from 5.19.0-41-generic

Exec into the debug container. Run the prepare-bpf.sh script in order to prepare the environment, then use whatever tool you like. My use case was for bpf scripts, which are in /usr/sbin/*-bpfcc.

The startup script runs 

mount -t debugfs debugfs /sys/kernel/debug

Wtf is this? Idk

The startup script runs

cp -Ra /proc/1/root/usr/src/* /usr/src/

To copy linux kernel headers from host node to the debug pod

And it runs

cp -Ra /proc/1/root/lib/modules/* /lib/modules/

Probably to copy kernel modules? Idk as well

If you are smart and know these answers please let me know

Enjoy!