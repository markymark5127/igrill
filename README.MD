# My Notes:

## I- check
Get encryption key from decompiling teh android app
    iGrill 
    encryption_key = [-19, 94, 48, -114, -117, -52, -111, 19, 48, 108, -44, 104, 84, 21, 62, -35]
    iGrill v2
    encryption_key = [-33, 51, -32, -119, -12, 72, 78, 115, -110, -44, -49, -71, 70, -25, -123, -74]


## II - check
Be careful on the bluez version 5.29

## III - check
Use bluetoothctl trust command on iGrill device.

## IV - install components
On a fresh Raspberry Pi Install, I performed the following to get this code to work - Minus the database portion.

Install bluez using these instructions. Install version 5.29 instead of the version listed.

https://learn.adafruit.com/install-bluez-on-the-raspberry-pi/installation
a. Download - wget http://www.kernel.org/pub/linux/bluetooth/bluez-5.29.tar.xz
b. Extract - tar xvf bluez-5.29.tar.xz
c. Browse - cd bluez-5.29
d. sudo apt-get update
e. sudo apt-get install -y libusb-dev libdbus-1-dev libglib2.0-dev libudev-dev libical-dev libreadline-dev
f. ./configure
g. make
h. sudo make install
i. bluetoothctl -v

After bluez is installed, source installed bluepy0.9.11.

a. Download - wget https://github.com/IanHarvey/bluepy/tarball/v/0.9.11.tar.xz
b. Extract - tar xvf 0.9.11
c. Browse - cd 0.9.11
d. sudo python setup.py build
e. sudo python setup.py install

Note: If you try to install bluepy0.9.11 before installing bluez, the install will error out.

## V - install components
apt-get install python-pip libglib2.0-dev
pip install bluepy
apt-get install python-crypto
pip install influxdb
apt-get install bluez-hcidump


## VI - connects and prints characteristics of igrillv2
import bluepy.btle as btle
import random

encryption_key = [-33, 51, -32, -119, -12, 72, 78, 115, -110, -44, -49, -71, 70, -25, -123, -74]

address = "12:34:56:78:90:12"

igrill = btle.Peripheral(address)
print "Attached"

igrill.setSecurityLevel("medium")
print "raised security level"

for charas in igrill.getCharacteristics() :
 print charas

key = "".join([chr((256 + x) % 256) for x in encryption_key])
challenge = str(bytearray([(random.randint(0, 255)) for i in range(8)] + [0] * 8))

app_uuid = igrill.getCharacteristics(uuid="64ac0002-4a4b-4b58-9f37-94d3c52ffdf7")[0]
app_uuid.write(challenge, True)

## VII - specific issues a lot of information
https://github.com/kvantetore/igrill/issues/4

## III - different fork
https://github.com/bjoernhoefer/igrill