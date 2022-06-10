![image](https://user-images.githubusercontent.com/102368584/173050819-2d7c87bb-c672-4b21-8cdf-5cb382a03ae9.png)
# MLX90640
Infrared Temperature Image sensor 
Run these commands in terminal: sudo pip3 install matplotlib scipy numpy
sudo apt-get install -y python-smbus
sudo apt-get install -y i2c-tools
sudo nano /boot/config.txt 
This should open up the boot file on the RPi. Scroll down to the dtparam=i2c_arm=on and make sure that it is uncommented:
![image](https://user-images.githubusercontent.com/102368584/173051068-77cb3620-c39c-4298-96d1-3cf0610902a5.png)
sudo reboot
sudo i2cdetect -y 1
sudo pip3 install RPI.GPIO adafruit-blinka
sudo pip3 install adafruit-circuitpython-mlx90640
sudo apt-get install idle3
Test the followiung code:
# MLX90640 Test with Raspberry Pi
import time,board,busio
import numpy as np
import adafruit_mlx90640

i2c = busio.I2C(board.SCL, board.SDA, frequency=400000) # setup I2C
mlx = adafruit_mlx90640.MLX90640(i2c) # begin MLX90640 with I2C comm
mlx.refresh_rate = adafruit_mlx90640.RefreshRate.REFRESH_2_HZ # set refresh rate

frame = np.zeros((24*32,)) # setup array for storing all 768 temperatures
while True:
    try:
        mlx.getFrame(frame) # read MLX temperatures into frame var
        break
    except ValueError:
        continue # if error, just read again

# print out the average temperature from the MLX90640
print('Average MLX90640 Temperature: {0:2.1f}C ({1:2.1f}F)'.\
      format(np.mean(frame),(((9.0/5.0)*np.mean(frame))+32.0)))
**When reading the MLX90640, an error may appear that cites a refresh rate issue. This can be avoided by amping up the rate of the I2C device on the RPi. To do this, we need to change the following back in the ‘config.txt’ file:      **
sudo nano /boot/config.txt
Scrolling down to the uncommented ‘dtparam=i2c_arm=on’ - we also want to add the following line that increases the I2C speed to 1Mbit/s:
NOTE:Be cautious when increasing the I2C baud rate above the recommended speed (400kbit/s). This high speed can cause overheating of the Pi, so ensure that the board is properly ventilated or actively cooled. In an upcoming section, some routines for plotting the 24x32 temperature grid will be introduced, where this 1Mbit/s will be important for creating a near real-time thermal camera with the MLX90640 sensor.
