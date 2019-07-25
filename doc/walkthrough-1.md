# Computer Vision with Raspberry Pi
## What you’ll need
•	Raspberry Pi Zero W + power adapter
•	SD card
•	Raspberry PI camera (v2) + case
•	Microsoft Azure Subscription (see below if you do not have one yet)
•	Access to computer with internet and web browser to code
•	USB or HDMI adapter (optional)

## Create an Azure account
Go to this address: https://azure.microsoft.com/en-us/free/students/
And use your school .org email address to sign up.

To check if the account is set up, go to http://portal.azure.com/ , log in with your Microsoft Account. (It is also using the same e-mail address, but it may have a different password.) 
You should then see the azure portal in your web browser.
__*Image 1*__ 

## Assemble your Camera
1.	Insert SD card into SD Card slot.
   __*Image 2*__           

2.	Open the cable slot on the RP.  Insert the cable into the cable slot. 
 __*Image 3*__

3.	Close the cable slot on the RP to ensure that the cable is secure.
__*Image 4*__


4.	Remove white safety connector from camera, by opening the cable slot. 
__*Image 5*__

5.	Connect the camera to the Pi and close cable slot. 
 
__*Image 6*__

6.	Connect camera to the case and snap into the head mounts. Do not bend the cable. 
__*Image 7*__
7.	Connect PI to device. Note: slightly slant the Pi device to ensure that the ports fit first before putting the PI device in place. Make sure the mounts align with the PI device. 
__*Image 8*__

8.	Connect the two cases together
 __*Image 9*__

(In lab only) Connect to internet

## Preparation Phase

1.	 Download and install putty https://www.chiark.greenend.org.uk/~sgtatham/putty/ .
2.	Click “Download it here”
3.	Click 32-bit version *Image 10*
4.	This will be used to connect to your Raspberry pi remotely from your PC.
5.	 Connect power to your raspberry pi device – **DO NOT THROW AWAY or misplace the bag** in which your Raspberry pi was and look at the monitor. Match the MAC address on the monitor, and the MAC address on the bag which you took your Raspberry pi out from. 

6.	Now, note down the IP address that corresponds to your MAC address.

7.	Connect to your raspberry pi using putty using this IP address.
 __*Image 11*__
8.	Click <Open>
9.	Accept the SSH host key (click YES)
10.	Username is “pi”, initial password is “raspberry”. 


## Enable and Test Camera

Now enable the raspberry pi camera:
1.	In the putty window, type:
sudo raspi-config 
a.	select entry “5 – Interfacing Options” and there “P1 Camera” 
b.	select “yes”. Select “Finish” and chose “Reboot” if you are asked.
__*Image 12*__

2.	Test Cameras
a.	In the putty window, type “cd” to return to the home directoy.
cd
b.	Create a directory “camera” by typing “mkdir camera”
mkdir camera
c.	Type “cd camera”
cd camera
d.	To test the camera by taking a picture, type the following in the putty window
raspistill -o test.jpg
This takes a picture and stores in the file test.jpg.
If you want to take several pictures, use a different file name each time.
3.	Set up web server 
a.	To install the web server “apache 2” type the following in Putty:
sudo apt install apache2
 __*Image 13*__
Press the enter key to continue
Wait for the installation to finish
__*Image 14*__ 
•	Now edit the web server configuration, type in the putty window:
cd /etc/apache2/sites-enabled
sudo nano 000-default.conf
b.	The last command opens the “nano” editor. You can move around with the cursor keys. You can delete using the backspace key. Other editor commands are shown on the last lines of the window. Add the following lines after “DocumentRoot /var/www/html/”
alias /camera /home/pi/camera
alias /camera/ /home/pi/camera/

<Directory /home/pi/camera >
        Options Indexes FollowSymLinks
        AllowOverride None
        Require all granted
</Directory>
The file should then look like this:
__*Image 15*__ 
c.	Write the file by pressing CTRL-O and Enter. And quit the editor using CTRL-X
d.	Restart apache (and re-read the changed configuration file) by typing 
sudo apachectl restart
 Ignore the error message.
 __*Image 16*__


4.	Now go to http://<device ip address>/camera/ , there should be a file called test.jpg. Click on it to display it.
See https://www.raspberrypi.org/documentation/usage/camera/raspicam/raspistill.md for more information on the camera.


## Install Custom Vision AI Sample files to your Pi 

See https://docs.microsoft.com/en-us/azure/cognitive-services/custom-vision-service/python-tutorial for a quickstart tutorial using Custom Vision via Python.
1.	Download and unpack the sample files for the next steps:
wget https://customvisiononpizero.blob.core.windows.net/training/camclean.tgz
__*Image 17*__
Note: (The command may take up to 1 minute to complete depending on your internet connection)
2.	Unpack the archive by typing:
tar -zxvf camclean.tgz 

tar -zxvf camclean.tgz 
__*Image 18*__
Note:if this doesn’t work, restart your device by typing “sudo shutdown -r now” and reconnect.


## Setup Jupyter Notebooks

The Jupyter Notebook is an open-source web application that allows you to create and share documents that contain live code, equations, visualizations and narrative text.
Set up the jupyter notebook server password. Type in the Putty window:
jupyter notebook --generate-config
jupyter notebook password
__*Image 19*__ 
Wait until prompted, select a password, type it in, press enter and type the same password and enter again for verification.
__*Image 20*__

Enable the jupyter notebook server at boot:
crontab crontab
__*Image 21*__
Now start the jupyter server by typing:

bash run-jupyter.sh
__*Image 22*__ 
To see the jupyter output log, you can type 
cat jupyter.log
__*Image 23*__ 



## Create a new Custom Vision AI project!

From your PC:
Navigate to www.customvision.ai, log in
Create new project:
__*Image 24*__ 
Create a general classification project
__*Image 25*__
 
Get project ID:
__*Image 26*__
  
Then copy the project ID.
__*Image 27*__
 
On the right side of the page, copy the training and prediction keys
__*Image 28*__ 

## Let’s start coding!

1.	Login to Jupyter
Go to jupyter notebook on raspberry pi, by accessing it through your Microsoft Edge browser: (Raspberry PI name or IP address, port 8888, e.g. http://raspberrypi:8888/
Log in:

__*Image 29*__

2.	Create new Notebook
Create new notebook called “TakePicturesInFolder”:
import os
import glob
from pathlib import Path


from picamera import PiCamera
from time import sleep
from time import time

camera = PiCamera()

camera.resolution =(640,480)
print ("starting camera")
camera.start_preview()

sleep(5)

print ("camera ready")
folder="folder1"

Path("/home/pi/camera/"+folder).mkdir(mode=0o755, parents=False, exist_ok=True)
epoch_time = int(time())
fname="/home/pi/camera/"+folder + "/image_%d.jpg" % epoch_time
print ("capture " + fname)
camera.capture(fname)
camera.stop_preview()

Execute cells 1 and 2, now a folder called “folder1” is created
Execute cell 3 to take a picture, after running the cell, click on the cell again and press run again to take another picture. Do this to record your training pictures. You need at least five pictures of each class you want to train for, so if you plan to train for three classes, you need 15 total pictures, 5 pictures of each class. 
Create another notebook called UploadPicturesInFolder
from azure.cognitiveservices.vision.customvision.training import CustomVisionTrainingClient
from azure.cognitiveservices.vision.customvision.training.models import ImageFileCreateEntry

from pathlib import Path

ENDPOINT = "https://southcentralus.api.cognitive.microsoft.com"

**Replace with a valid key**
training_key = "<training key goes here>"
prediction_key = "<prediction key goes here>"
prediction_resource_id = "<prediction resource ID goes here>"

publish_iteration_name = "classifyModel"

trainer = CustomVisionTrainingClient(training_key, endpoint=ENDPOINT)

**Create a new project**
#print ("Creating project...")
#project = trainer.create_project("My New Project")
projectid = "<project ID goes here>"
base_image_url = "folder1/"

print("Adding images...")

image_list = []

pathlist = Path(base_image_url).glob('*.jpg')

for path in pathlist:
    file_name=str(path)
    print(file_name)
    with open(str(path),"rb") as image_contents:
        image_list.append(ImageFileCreateEntry(name=file_name, contents=image_contents.read()))
        

#for image_num in range(1, 11):
*file_name = "japanese_cherry_{}.jpg".format(image_num)*
*with open(base_image_url + "images/Japanese Cherry/" + file_name, "rb") as image_contents:*
*image_list.append(ImageFileCreateEntry(name=file_name, contents=image_contents.read(), tag_ids=[cherry_tag.id]))*

upload_result = trainer.create_images_from_files(projectid, images=image_list)
if not upload_result.is_batch_successful:
    print("Image batch upload failed.")
    for image in upload_result.images:
        print("Image status: ", image.status)
    exit(-1)
    
print("uploaded images")

Now head back to the custom vision portal.
You should now see a couple of untagged images:
__*Image 30*__
__*Image 31*__

 
Select each of the images and tag it.
After you have tagged all images, press “Train”. This runs the trainer and creates a new iteration.
Once the trainer is finished, publish the iteration.
__*Image 32*__

Now head back to the jupyter notebook on the raspberry pi:
Stop the kernel of the picture taking notebook to free the camera.
__*Image 33*__

Create another notebook and call it RunModelOnCameraPicture
import os
import glob

from azure.cognitiveservices.vision.customvision.training import CustomVisionTrainingClient
from azure.cognitiveservices.vision.customvision.training.models import ImageUrlCreateEntry

from picamera import PiCamera
from time import sleep

ENDPOINT = "https://southcentralus.api.cognitive.microsoft.com"

**Replace with a valid key**
training_key = "<training key goes here>"
prediction_key = "<prediction key goes here>"
prediction_resource_id = "<resource ID goes here"

from azure.cognitiveservices.vision.customvision.prediction import CustomVisionPredictionClient
#from azure.cognitiveservices.vision.customvision.prediction.prediction_endpoint import models 
**project = trainer.create_project("Image-classification-CYTE19-py") projectid="<project ID goes here>"** 
  
#### Now there is a trained endpoint that can be used to make a prediction

predictor = CustomVisionPredictionClient(prediction_key, endpoint=ENDPOINT)
trainer = CustomVisionTrainingClient(training_key,endpoint=ENDPOINT)
iterations = trainer.get_iterations(projectid);
for iteration in iterations:
    print(iteration.name + "\t" + iteration.id + "\t" + iteration.status, end=' ' )
    if (iteration.publish_name):
        print("published under "+ iteration.publish_name, end=' ')
        publish_iteration_name = iteration.publish_name 
    print(" ")
    
print("using "+ publish_iteration_name)
camera = PiCamera()

camera.resolution =(640,480)

print ("starting camera")
camera.start_preview()
sleep(5)
print ("capture")
camera.capture('/home/pi/camera/image.jpg')
camera.stop_preview()
print ("analyzing")
with open('/home/pi/camera/image.jpg',mode="rb") as test_data:
    #results = predictor.predict_image(projectid, test_data)
    results = predictor.classify_image(projectid, publish_iteration_name, test_data.read())
    for prediction in results.predictions:
        print ("\t" + prediction.tag_name + ": {0:.2f}%".format(prediction.probability * 100))
This notebook takes a picture from the camera and runs it through the cloud-based classifier and prints the results.

#### Instructions to change the wifi while connected via ssh:
sudo nano /etc/wpa_supplicant/wpa_supplicant.conf

#### Instructions to change the wifi via the SD card:
Create a file called wpa_supplicant.conf with the following content:
country=US
ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
update_config=1
ap_scan=1
network={
ssid="<place your wifi name here>"
psk="<place your wifi password here>"
} 
  
__*Image 34*__
  __*Image 35*__
 __*Image 36*__
 


