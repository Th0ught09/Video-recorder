# Video-recorder
Picamera takes recording a set amount of time after last movement
Camera that takes video a set amount of minutes after detecting movement
Video needs to be viewed on VLC media
Used in conjunction with a PIR sensor and the mqtt library to receive on and off signals when movement is detected
3 parameters are needed:
[1] - Location where the PIR sensor will publish the message
[2] - Name of Location for picture to be stores, must end in "0001.h264" for program to work
[3] - Amount of time after movement is detected where the camera will take video
