import paho.mqtt.client as mqtt
import time
import RPi.GPIO as GPIO
import time
import logging
import threading
import sys
from picamera import PiCamera
import math
from subprocess import call

camera = PiCamera()
camera.close()

GPIO.setmode(GPIO.BCM)

GPIO.setwarnings(False)
GPIO.setup(13,GPIO.OUT)

Location = sys.argv[1]
Saved = sys.argv[2]
Duration = int(sys.argv[3])
Interval = int(sys.argv[4])

numPicture = 1111
camera.close()
Recording = False
#"Garage/Outside/+/PIR" "/mnt/nfs/motion/GarageSide" 30 5

def on_connect(client, userdata, flags, rc):
     print("Connected with result code "+str(rc))
     client.subscribe(Location)


startTime = 0
def set_timer():
     global numPicture
     global Saved
     global startTime
     global Recording
     startTime = time.perf_counter()
     camera = PiCamera()
     endTime = startTime

     while endTime - startTime < Duration:
          endTime += 5
          print(endTime, startTime)
          time.sleep(5)
          if Recording == False:
               numPicture += 1
               listTemp = list(Saved)
               listTemp[-6] = str(numPicture)[3]
               listTemp[-7] = str(numPicture)[2]
               listTemp[-8] = str(numPicture)[1]
               listTemp[-9] = str(numPicture)[0]
               Saved = ''.join(listTemp)
               print(Saved)
               camera.start_recording(Saved)
               print("Started recording")
               Recording = True

          if endTime - startTime >= Duration:
               camera.stop_recording()
               camera.close()
               print("Camera is off")
               Recording = False


def on_message(client, userdata, msg):

     if msg.payload.decode() == "ON":
          x = threading.Thread(target=set_timer)
          x.start()



client = mqtt.Client()
client.connect("192.168.0.160",1883,60)

client.on_connect = on_connect
client.on_message = on_message



client.loop_forever()
# need to stop it recording for > 30 mins
