# Gun Detection Using Tensorflow
Utilizing Tensorflow and multiple trained models, these Python-based scripts can access a video feed and detect either pistols (handguns, pistols, etc.) or "long guns" (rifles, etc.). To acheive this, two Tensorflow sessions are ran seperately. The first session detects people and the second session detects guns within the images of detected persons. This method reduces the number of false positives while also maintainig optimal performance times that allow the sessions to be ran with any real-time video feed. The two sessions are diesgined to be ran concurrently with each other.

## Prerequisites
Certain changes to person-camera-session-one.py and person-camera-session-two.py may need to be made BEFORE setting up a docker container. A list of possible needed changes can be found under the "Changes" header.

A Minio server and bucket is required to store certain data. The public Minio server 'play.minio.io:9000' can be used with the access_key 'Q3AM3UQ867SPQQA43P2F' and secret_key 'zuf+tfteSlswRu7BJ86wekitnifILbZam1KYY3TG'. More information about Minio Client can be found here: https://docs.minio.io/docs/minio-client-complete-guide. 

## Installation
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
then, after either command:
```
wget http://download.tensorflow.org/models/object_detection/faster_rcnn_resnet101_coco_2017_11_08.tar.gz
```

```
tar -xvf faster_rcnn_resnet101_coco_2017_11_08.tar.gz
```


The following commands will require one to select which camera (video feed) will be used and which gun type will be detected. The current options are as follows: 

----------------------------------------------------------------------------------------------------------------------------------------

Select Camera:

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

----------------------------------------------------------------------------------------------------------------------------------------

Select Gun Type:

PISTOL

LONGGUN

----------------------------------------------------------------------------------------------------------------------------------------

Run session one in its own instance, selecting which camera to use

```
python person-camera-session-one.py RECEPTION_EAST
```

Session two can be ran simultaneously with session one in a seperate instance.
Choose which camera is being used and what gun type is being detected.

```
python person-camera-session-two.py RECEPTION_EAST PISTOL
```

## Changes
This project was developed by SOFWERX, and it utilizes cameras, servers, etc. owned by SOFWERX. As such, certain changes to the code need to be made so one can utilize their own cameras, servers, etc.

Changes primarily need to be made to person-camera-session-one.py and person-camera-session-two.py under the detect_pistol directory.

### Changes to both session scripts
-Under the comment #Camera, there is a list of variables all containing string values. These variables are used to access certain camera feeds. Which ever camera feed one wishes to use is selected when running the script (detailed under the "Installation" header. One can replace these values with the absolute path or rtsp link to whichever video feed one wishes to use.

-On line 177 in person-camera-session-one.py and line 175 in person-camera-session-two.py allow the scripts to access the Minio client. The name of the server, the access_key, and the server_key need to be changed to the specifc values one will be using for their own Minio client. Documentation for the Minio Python Client SDK can be found here: https://docs.minio.io/docs/python-client-quickstart-guide.
