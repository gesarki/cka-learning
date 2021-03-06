# Storage in Docker
- docker builds are layered
- each changes only stores the images from the previous layer to keep sizes small
- these layers are readonly when the image is created
- 👆 **Image layers** 👆
- when you run the image in a container, it adds another layer, "Container layer" that's only alive for the lifespan of the container
    - it contains filesystem of container, which has data/files generated by the app running on it
    - this layer (with all it's files inside) is destroyed when the container is destroyed
- filesystem is read-write
- 👆 **Container layer** 👆

- When the container tries to change a file that's part of the image layer (e.g. application code), it does *Copy-On-Write*
    - it makes a copy of the filein the container layer, and makes the changes to that file instead
    - image will remain the same all the time until you rebuild the image using the `docker build` cmd

## If you want the data to persist
- `docker volume create data_volume`
    - creates a directory in the host's filesystem at /var/lib/docker/data_volume
- `docker run -v data_volume:var/lib/mysql mysql`
    - runs the mysql image with the new volume created above mounted at var/lib/mysql in the container
- `docker run -v data_volume2:var/lib/mysql mysql`
    - same as above except it creates data_volume2 at /var/lib/docker/data_volume2 on the fly
- 👆 **Volume Mounting** 👆
- `docker run -v /data/mysql:var/lib/mysql mysql`
    - mounts your existing folder /data/mysql at var/lib/mysql in the container
- 👆 **Bind Mounting** 👆 (can by from any folder on your host)
- preferred way:
    - `docker run --mount type=bind,source=/data/mysql,target=/var/lib/mysql mysql`
    - more explicit, readable

# Volumes
- can specify a volume driver when you run a docker image to use a filesystem in the cloud like AWS EBS, Azure File Storage, gce-docker,  etc
- `docker run -it --name mysql --volume-driver rexray/ebs --mount src=ebs-vol,target=/var/lib/msql mysql