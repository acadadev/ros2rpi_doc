**Overview**

The ROS2RPI HAT is a powerful add-on board designed specifically for Raspberry Pi 4/5 models. It enhances the capabilities of your Raspberry Pi by providing a range of features, including:

- Dual Independent I2C Ports: Dual Bidirectional I2C Bus and Voltage-Level Translators, enabling flexible connectivity to a wide range of QWIC sensors.
- I2C GPIO Expander: The ROS2RPI HAT utilizes an I2C GPIO expander to efficiently manage its functionalities, freeing up valuable Raspberry Pi GPIO pins for other applications.
- LIDAR Integration: A dedicated port for connecting Lidar sensors, directly interfaced with the Raspberry Pi's hardware serial port.
- The ROS2RPI HAT offers a flexible serial port that can be configured for either LIDAR communication or external debugging. This dual-purpose functionality enables remote debugging and troubleshooting, enhancing the HAT's versatility and ease of use.
- Active Cooling: A 30mm fan and metal heat sink at the bottom ensures optimal thermal performance, especially during intensive processing or sensor data acquisition.

[![ROS2RPI Hat](/images/ros2rpi_top.jpg)](https://acada.dev/products/ros2rpi-control-hat-v4)

**Controlling ROS2RPI**

Sending one byte to I2C address `0x20` we can control all the peripherals on the device.

| bit | function | mask |
| --- | --- | --- |
| 0 | PSEL_3V3_A | 0x01 |
| 1 | PSEL_3V3_B | 0x02 |
| 2 | SERIAL_RX_ON | 0x04 |
| 3 | SERIAL_TX_ON | 0x08 |
| 4 | PSEL_LIDAR | 0x10 |
| 5 | LIDAR_TX_ON | 0x20 |
| 6 | LIDAR_PWM_ON | 0x40 |


**Software**

**Haton with Lidar**

```python
#!/usr/bin/env python

import string
import struct
import sys
import time

from smbus2 import SMBus
import pigpio

with SMBus(1) as bus:

    TCA6408_OUTPUT = 0x01
    TCA6408_CONFIGURATION = 0x03
    TCA6406_ADDRESS = 0x20

    # PSEL_3V3_A	P0  0x01
    # PSEL_3V3_B	P1  0x02
    # SERIAL_RX_ON	P2  0x04
    # SERIAL_TX_ON	P3  0x08
    # PSEL_LIDAR	P4 	0x10
    # LIDAR_TX_ON	P5	0x20
    # LIDAR_PWM_ON	P6	0x40

    try:
        bus.write_byte_data(TCA6406_ADDRESS, TCA6408_CONFIGURATION, 0x0)
        bus.write_byte_data(TCA6406_ADDRESS, TCA6408_OUTPUT, 0x73)
    except IOError as e:
        print('IOError: %s' % e)

```

**Haton Debug Mode**

```python
#!/usr/bin/env python

import string
import struct
import sys
import time

from smbus2 import SMBus
import pigpio

with SMBus(1) as bus:

    TCA6408_OUTPUT = 0x01
    TCA6408_CONFIGURATION = 0x03
    TCA6406_ADDRESS = 0x20

    # PSEL_3V3_A	P0  0x01
    # PSEL_3V3_B	P1  0x02
    # SERIAL_RX_ON	P2  0x04
    # SERIAL_TX_ON	P3  0x08
    # PSEL_LIDAR	P4 	0x10
    # LIDAR_TX_ON	P5	0x20
    # LIDAR_PWM_ON	P6	0x40

    try:
        bus.write_byte_data(TCA6406_ADDRESS, TCA6408_CONFIGURATION, 0x0)
        bus.write_byte_data(TCA6406_ADDRESS, TCA6408_OUTPUT, 0x0F)
    except IOError as e:
        print('IOError: %s' % e)
```

**Hatoff**

```python
#!/usr/bin/env python

import string
import struct
import sys
import time

from smbus2 import SMBus

with SMBus(1) as bus:

   # send hibernate command to ROSRIDER
   try:
      # 0x04 is sys_ctl address
      bus.write_i2c_block_data(0x3C, 0x04, [0,0,0,0x05])
   except IOError as e:
      print('IOError: %s' % e)

   TCA6408_OUTPUT = 0x01
   TCA6408_CONFIGURATION = 0x03
   TCA6406_ADDRESS = 0x20

   # turn all off on the RPIROS board
   try:
       bus.write_byte_data(TCA6406_ADDRESS, TCA6408_CONFIGURATION, 0x00)
       bus.write_byte_data(TCA6406_ADDRESS, TCA6408_OUTPUT, 0x0)
   except IOError as e:
       print('IOError: %s' % e)
```


**Instrumenting Linux**

haton at boot time
hatoff at shutdown time

- [Introduction](01_INTRO/README.md)
- [Hardware Specifications](02_HARDWARE/README.md)
- [Software Drivers](03_SOFTWARE/README.md)
- [Troubleshooting](10_DEBUG/README.md)
