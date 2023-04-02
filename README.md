# Telemetry
Simple telemetry app based on arduino and Raspberry Pi.

## Installation
```sh
git clone https://github.com/exelban/telemetry
```

## Usage
### Raspberry Pi
I use Python 3.5 so you must to compile this version (or you can change code for oldest version of python).

Copy file ```raspberry.py``` to your raspberry Pi.

You must find what the port where the arduino and GPS module are connected.<br />
In Raspberry Pi 2 dafault RX/TX port is ```/dev/ttyAMA0```. But also you must to make some changes in system files ([See here](https://learn.adafruit.com/adafruit-ultimate-gps-on-the-raspberry-pi?view=all)).

When you find the port, just change it in raspberry.py:
```python
GPS_port = '/dev/ttyAMA0'
arduino_port = '/dev/ttyUSB1'
```

**If you dont use a GPS, just leave ```GPS_port``` empty.**

### Arduino
To compile arduino code you must to download and install [SimpleTimer Library](http://playground.arduino.cc/Code/SimpleTimer), because we use it for speed calculation.

If you connected sensors to different ports on the arduino, just change it in ```arduino.ino```.
Next upload file ```arduino.ino``` to your arduino board.

I recommend you install the Arduino IDE to your Raspberry Pi. You can do it by typing ```sudo apt install arduino ``` in a terminal on the Raspberry Pi.

**Dont worry if you have different sensors or use your own arduino code. Script on Raspberry Pi just adds all received data from arduino to final array.**

After you connected all sensors to arduino board and arduino board to Raspberry Pi you can run ```python3 raspberry.py``` on your Raspberry Pi.

## Errors
### Please check arduino port!
It means that script can not connect to arduino on this port. Probably you forget to change arduino port in ```arduino.ino```.
If you your arduino is connected to the right port, but the error still appears, plese create an issue.

### Please check GPS port!
It means that script can not connect to GPS module on this port. Probably you forget to change GPS module port in ```arduino.ino```.
If you your GPS module is connected to the right port, but the error still appears, plese create an issue.

### Please check your GPS module.
This error can appear if your GPS module uses a baud rate other than 9600. Please check your GPS module boud rate and change the GPS boud rate on line 24: 
```python
self.gps_module = serial.Serial(GPS_port, 9600)
```

## Data format
Default data format is ```[arduino, [gps], time]```.
If you dont have a GPS module data format will be ```[arduino, time]```.

### Arduino (String)
Script split (```","```) received string from arduino.

### GPS (Array)
Script collect next data from GPS:
  - Latitude (lat);
  - Longitude (lon);
  - Speed based on changing latitude and longitude (V1);
  - Speed from GPS module (V2);
  - Number of satellites (s);
  - Altitude (alt).
  
Data format: ```[lat, lon, V1, V2, s, alt]```
You can remove some parameters if you dont need on line 111: 
```python
return [self.lat, self.lon, self.speed, self.speed_gps, self.satelites, self.altitude]
```

### Time (String)
Time format: ```H:M:S:MS```.
You can change time format if you need on line 132: 
```python
date = datetime.datetime.now().strftime("%H:%M:%S:%f")
```

### Example:
```python
['35', '31', '0', '-2.33', '49.63', '0.00', [52.2297700, 21.0117800, 0.00, 0.00, 7, 137], '17:22:53:208957']
[               arduino                   , [.   lat.  ,     lon.  ,  V1 ,  V2 , s, alt],        TIME      ]
```

## Structure
![](https://s3.eu-central-1.amazonaws.com/serhiy/Github_repo/telemetry_schemat_1.png) 

Block version:
![](https://s3.eu-central-1.amazonaws.com/serhiy/Github_repo/telemetry_schemat_2.png) 

## Licence
[GNU Affero General Public License](https://github.com/exelban/telemetry/blob/master/LICENSE)
