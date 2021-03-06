# CaafsDC - device client based on Microsoft Azure IoT HuB

> *Copyright 2018 [Ivan Emilov]*

**C.a.a.f.s.D.C** (**Care and Alarms facility systems Device Client**) is an application that enables bidirectional communication to every IoT platform based on Microsoft Azure IoT Hub. Explicitly developed for [Caafs platform](https://github.com/iemilov/caafs-platform). With **C.a.a.f.s.D.C**, you are able to:

* **Start/Stop** sending sensor data to the cloud.
* **Reconfigure** the sending interval remotely.
* **Execute firmwareUpdate** on the device remotely.
* **Execute reboot** on the device remotely.
* **Execute waiting method** in case of reconnecting after beeing offline.
* **Monitor the progress** of method execution remotely.
* **Upload device log data** to the cloud remotely.
* **Monitor dynamic data** (processes, cpu load, networks and etc.) remotely.
* **Get static data** (cpu, ram, bios, operation system and etc.) remotely.

Please feel free to extend and make suggestions how to improve the application. If you find any bugs, do not hesitate to submit the [issues](https://github.com/iemilov/caafs-device-client/issues).

Additional facts:
* **Based** on the provided [Azure IoT HUB Node.js sdk](https://github.com/Azure/azure-iot-sdk-node)
* **Uses** the [Azure IoT HuB device twin](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-devguide-device-twins)
* **Runs** on every device which would support a  Node.js runtime(for example raspberry pi)
* **Tested** with raspbian OS installed on Raspberry Pi with temperature and humidity sensors ([how to connect sensors](https://github.com/momenso/node-dht-sensor))

Table of contents
=================

<!--ts-->
   * [Getting Started](#getting-started)
      * [Prerequisites](#prerequisites)
      * [Installation](#installation)
      * [Configuration](#configuration)
      * [Run the application](#run-the-application)
   * [Usage](#usage)
   * [Contributing](#contributing)
   * [Authors](#authors)
   * [License](#license)   
<!--te-->

## Getting Started
 
These instructions will get you a copy of the project up and running on your device for development and testing purposes.

### Prerequisites
 
* Download BCM2835 library and follow installation [instructions](http://www.airspayce.com/mikem/bcm2835/) for your device type
* Install pm2 for application management as described [here](http://pm2.keymetrics.io/)
* Install [Node.js](https://nodejs.org/en/download/) on your local device.
* Install Git on your device.

**Windows**: [download](https://git-scm.com/download/win) <br>
**Linux**:
```
$ sudo apt-get install git
```

### Installation

* **Clone the repository on your local machine**

```
mkdir client
cd client
git clone https://github.com/iemilov/caafs-device-client.git
```

* **Install Dependancies**

From the created client directory execute:

```
npm install
```

### Configuration

All configuration parameters are set in the [config.js](https://github.com/iemilov/caafs-device-client/blob/master/config.js) file

* Put the device connection string [View Device Endpoint](https://github.com/iemilov/caafs-platform/wiki/Device-Management#view-device-endpoint):

```
config.connectionString = '<device connection string>'
```

You can also get the device connection string from [Azure Portal](https://blogs.msdn.microsoft.com/iotdev/2017/05/09/understand-different-connection-strings-in-azure-iot-hub/)!

* Set the transport protocol for connecting to the cloud, [supported transport protocols](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-devguide-protocols), see example below:

```
config.protocol = 'amqp'
```

* Set default value for sending sensor data.

```
config.defaultinterval = 5000
```

**Note**: default value 5000 (5 seconds) would be the default sending interval for the sensor data if the startTelemetry method is executed from the [Caafs platform](https://github.com/iemilov/caafs-platform/wiki/Device-Management#execute-methods-on-a-device) until the setNewInterval method is executed with the new desired value.

* Switch on/off simulation data if you have no sensors connected:

```
config.sensorData = 'on' //if off simulated sensor data is generated and sent 

```

* Config sensors based on the type and connected pins, [see example](https://github.com/momenso/node-dht-sensor#usage)

```
config.sensors = [
//    {
//        type: 11,
//        pin: 4
//    },
    {
        type: 22,
        pin: 4
    }
]
```

* Set name for the log file:

```
config.logfile = 'logs.txt'
```

**Note**: every activity inclusive errors are stored in the log file!

* Set path for your log file:

```
config.clientlog = '/home/pi/client/clientlogs.txt'
```

* Set commands for the processes (reboot, update and upgrade)

```
config.reboot = 'sudo reboot'
config.update = 'sudo apt-get update'
config.upgrade = 'sudo apt-get -y dist-upgrade'
```

**Note**: this are standard commands for raspbian OS to execute those procedures. If you use another another OS or another device, change this parameters based on the OS! 

### **Run the application**

```
node app.js
```

Example output form the logfile after starting the application:

```
{"name":"app","hostname":"raspberrypi","pid":4653,"level":30,"msg":"Connected to device. Registering handlers for methods.","time":"2018-03-28T16:14:26.188Z","v":0}
{"name":"app","hostname":"raspberrypi","pid":4653,"level":30,"msg":"twin acquired","time":"2018-03-28T16:14:26.908Z","v":0}
{"name":"app","hostname":"raspberrypi","pid":4653,"level":30,"msg":"twin acquired","time":"2018-03-28T16:14:26.994Z","v":0}
{"name":"app","hostname":"raspberrypi","pid":4653,"level":30,"msg":"there is no waiting command at this time","time":"2018-03-28T16:14:27.003Z","v":0}
{"name":"app","hostname":"raspberrypi","pid":4653,"level":30,"msg":"Client myPi connected to the cloud","time":"2018-03-28T16:14:27.192Z","v":0}
{"name":"app","hostname":"raspberrypi","pid":4653,"level":30,"msg":"Device twin state updated","time":"2018-03-28T16:14:27.236Z","v":0}
```

## Usage

This application is supposed to be used from the [Caafs Platform](https://github.com/iemilov/caafs-platform). Nevertheless this app is independant from the platform and you still can use it with your own solution.

1. [Send Commands to this Application remotely](https://github.com/iemilov/caafs-platform/wiki/Device-Management#execute-methods-on-a-device)
2. [Get the execution progress of command](https://github.com/iemilov/caafs-platform/wiki/Device-Management#view-command-progress)
3. [Manage Logs](https://github.com/iemilov/caafs-platform/wiki/Device-Management#get-device-logs)
4. [Get Device Key](https://github.com/iemilov/caafs-platform/wiki/Device-Management#view-device-endpoint)
5. [Generate New Device Key](https://github.com/iemilov/caafs-platform/wiki/Device-Management#generate-new-device-endpoint)
6. [Monitor the application](http://pm2.keymetrics.io/docs/usage/quick-start/)


## Contributing
 
Please read [CONTRIBUTING.md](https://github.com/iemilov/caafs-device-client/blob/master/CONTRIBUTING.md) for details on our code of conduct, and the process for submitting pull requests to us.
 
## Authors
 
* **Ivan Emilov**
* **Contact**: goiot.dev@gmail.com

## License
 
This project is licensed under the ISC License - see the [LICENSE.md](https://github.com/iemilov/caafs-device-client/blob/master/LICENSE) file for details
