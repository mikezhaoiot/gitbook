#### 安装

```shell
//Install Dependencies
sudo apt-get update
sudo apt-get install -y libusb-dev libdbus-1-dev libglib2.0-dev libudev-dev libical-dev libreadline-dev

//Compile & Install bluez
./configure
make && make install

//查看版本bluetoothctl -v
bluetoothctl: 5.50

```


[Install bluez on the Raspberry Pi
How to compi](https://learn.adafruit.com/pages/7074/elements/1701164/download)