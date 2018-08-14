# Gun Detection Using Tensorflow
Utilizing Tensorflow and multiple trained models, these Python-based scripts can access a video feed and detect either pistols (handguns, pistols, etc.) or "long guns" (rifles, etc.). To acheive this, two Tensorflow sessions are ran seperately. The first session detects people and the second session detects guns within the images of detected persons. This method reduces the number of false positives while also maintainig optimal performance times that allow the sessions to be ran with any real-time video feed. The two sessions are diesgined to be ran concurrently with each other.

A Minio account is required to communicate the appropriate data about detected persons from one session to the other.

##Installation
It is recommened that these scripts are ran within a docker container. The following commands should be ran to set up the container:

```
git clone https://github.com/sofwerx/assault-rifle-detection.git $HOME/Documents/pistol-detection
```
```
cd $HOME/Documents/pistol-detection
```

```
docker build -t gpu_tf .
```

```
xhost +local:docker
```

```
nvidia-docker run --rm --network host --privileged -it -v ~/.Xauthority:/root/.Xauthority -v /tmp/.X11-unix:/tmp/.X11-unix -e DISPLAY=$DISPLAY --env="QT_X11_NO_MITSHM=1" -v /dev/video0:/dev/video0  -v $HOME/Documents/pistol-detection/tf_files:/tf_files  --device /dev/snd gpu_tf bash
```

```
cd object_detection
```

For optimization, the object detection code has been split into two seperate scripts that can be run simulatenously, but should be run in seperate instances (on seperate machines). Depending on which instance is running, run the following commands:

```
cp /detect_pistol/person-camera-session-one.py .
```
OR
```
cp /detect_pistol/person-camera-session-two.py .
```
then, after either:
```
wget http://download.tensorflow.org/models/object_detection/faster_rcnn_resnet101_coco_2017_11_08.tar.gz
```

```
tar -xvf faster_rcnn_resnet101_coco_2017_11_08.tar.gz
```

Select Camera

RECEPTION_EAST

RECEPTION_WEST

DIRTYWERX_NORTH

DIRTYWERX_SOUTH

THUNDERDRONE_INDOOR_EAST

THUNDERDRONE_INDOOR_WEST

OUTSIDE_WEST

OUTSIDE_NORTH_WEST

OUTSIDE_NORTH

OUTSIDE_NORTH_EAST

DIRTYWERX_RAMP



Select Gun Type

PISTOL

LONGGUN



Run session one in its own instance, selecting which camera to use
```
python person-camera-session-one.py RECEPTION_EAST
```

Session two can be ran simultaneously with session one in a seperate instance.
Choose which camera is being used and what gun type is being detected.
```
python person-camera-session-two.py RECEPTION_EAST PISTOL
```
