---
layout: default
title_url: /02_PYTHON/README.html
title: "Python Code"
description: "Example Python Code to Control ROS2RPI"
---

#### Example Python program to turn on both I2C Ports and Lidar

Create a file named `haton.py` under `$HOME\bin` and put the code below inside it

```python
#!/usr/bin/env python
import string
import struct
import sys
import time

from smbus2 import SMBus

with SMBus(1) as bus:

    TCA6408_OUTPUT = 0x01
    TCA6408_CONFIGURATION = 0x03
    TCA6406_ADDRESS = 0x20

    # PSEL_3V3_A	P0	0x01
    # PSEL_3V3_B	P1	0x02
    # SERIAL_RX_ON	P2	0x04
    # SERIAL_TX_ON	P3	0x08
    # PSEL_LIDAR	P4	0x10
    # LIDAR_TX_ON	P5	0x20
    # LIDAR_PWM_ON	P6	0x40

    try:
        bus.write_byte_data(TCA6406_ADDRESS, TCA6408_CONFIGURATION, 0x0)
        bus.write_byte_data(TCA6406_ADDRESS, TCA6408_OUTPUT, 0x73)
    except IOError as e:
        print('IOError: %s' % e)

```

#### Example Python program to turn on both I2C Ports, and switch serial to DEBUG

```python
#!/usr/bin/env python
import string
import struct
import sys
import time

from smbus2 import SMBus

with SMBus(1) as bus:

    TCA6408_OUTPUT = 0x01
    TCA6408_CONFIGURATION = 0x03
    TCA6406_ADDRESS = 0x20

    # PSEL_3V3_A	P0	0x01
    # PSEL_3V3_B	P1	0x02
    # SERIAL_RX_ON	P2	0x04
    # SERIAL_TX_ON	P3	0x08
    # PSEL_LIDAR	P4	0x10
    # LIDAR_TX_ON	P5	0x20
    # LIDAR_PWM_ON	P6	0x40

    try:
        bus.write_byte_data(TCA6406_ADDRESS, TCA6408_CONFIGURATION, 0x0)
        bus.write_byte_data(TCA6406_ADDRESS, TCA6408_OUTPUT, 0x0F)
    except IOError as e:
        print('IOError: %s' % e)
```

#### Example Python program to set ROSRider to hibernate, and turn off all hat functionality

Create a file named `hatoff.py` under `$HOME\bin` and put the code below inside it

```python
#!/usr/bin/env python
import string
import struct
import sys
import time

from smbus2 import SMBus

with SMBus(1) as bus:

   # send hibernate command to ROSRider
   try:
      # 0x04 is sys_ctl address
      bus.write_i2c_block_data(0x3C, 0x04, [0,0,0,0x05])
   except IOError as e:
      print('IOError: %s' % e)

   TCA6408_OUTPUT = 0x01
   TCA6408_CONFIGURATION = 0x03
   TCA6406_ADDRESS = 0x20

   # turn the ROS2RPI board off
   try:
       bus.write_byte_data(TCA6406_ADDRESS, TCA6408_CONFIGURATION, 0x00)
       bus.write_byte_data(TCA6406_ADDRESS, TCA6408_OUTPUT, 0x0)
   except IOError as e:
       print('IOError: %s' % e)
```