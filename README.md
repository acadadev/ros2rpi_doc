**Overview**

The ROS2RPI HAT is a powerful add-on board designed specifically for Raspberry Pi 4/5 models. It enhances the capabilities of your Raspberry Pi by providing a range of features, including:

- Dual Independent I2C Ports: Dual Bidirectional I2C Bus and Voltage-Level Translators, enabling flexible connectivity to a wide range of QWIC sensors.
- I2C GPIO Expander: The ROS2RPI HAT utilizes an I2C GPIO expander to efficiently manage its functionalities, freeing up valuable Raspberry Pi GPIO pins for other applications.
- LIDAR Integration: A dedicated port for connecting Lidar sensors, directly interfaced with the Raspberry Pi's hardware serial port.
- The ROS2RPI HAT offers a flexible serial port that can be configured for either LIDAR communication or external debugging. This dual-purpose functionality enables remote debugging and troubleshooting, enhancing the HAT's versatility and ease of use.
- Active Cooling: A 30mm fan and metal heat sink at the bottom ensures optimal thermal performance, especially during intensive processing or sensor data acquisition.
- OLED Display Expansion: This port connects RPIs SPI peripheral to a Waveshare 240x280 SPI Display Module. 

<div style="display: flex; justify-content: space-around; margin: 25px 0;">
   <img src="images/ros2rpi_top.jpg" alt="ROS2RPI Hat" style="box-shadow: 0px 4px 8px rgba(0, 0, 0, 0.2);">
</div>

**Controlling ROS2RPI**

Sending one byte to I2C address `0x20` we can control all the peripherals on the device.

| bit | function | mask | description |
| ----- | ----- | ----- | ----- |
| 0 | PSEL_3V3_A | 0x01 | Powers on I2C Bus Voltage-Level Translator A |
| 1 | PSEL_3V3_B | 0x02 | Powers on I2C Bus Voltage-Level Translator B |
| 2 | SERIAL_RX_ON | 0x04 | Connects the RPIs RX to debug port RX |
| 3 | SERIAL_TX_ON | 0x08 | Connects the RPIs TX to debug port TX |
| 4 | PSEL_LIDAR | 0x10 | LIDAR Power ON |
| 5 | LIDAR_TX_ON | 0x20 | Connects the RPIs RX to Lidars TX |
| 6 | LIDAR_PWM_ON | 0x40 | Connects RPIs GPIO19 to Lidars PWM input |



**Example Python program to turn on both I2C Ports and Lidar**

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

**Example Python program to turn on both I2C Ports, and switch serial to DEBUG**

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

**Example Python program to set ROSRider to hibernate, and turn off all hat functionality**

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

   # turn all off on the RPI2ROS board
   try:
       bus.write_byte_data(TCA6406_ADDRESS, TCA6408_CONFIGURATION, 0x00)
       bus.write_byte_data(TCA6406_ADDRESS, TCA6408_OUTPUT, 0x0)
   except IOError as e:
       print('IOError: %s' % e)
```

**Example C function set ROS2RPI command**

```c
uint8_t send_hat_command(int fd, uint8_t output) {

	int rv_hat = ioctl(fd, I2C_SLAVE, 0x20);
	if(rv_hat<0) { return rv_hat; }

    unsigned char hat_command[1] = {0};
    int rw_hat = I2C_RW_Block(fd, 0x03, I2C_SMBUS_WRITE, 1, hat_command);

    hat_command[0] = output;
    rw_hat = I2C_RW_Block(fd, 0x01, I2C_SMBUS_WRITE, 1, hat_command);

    return rw_hat;
}
```

**Example C function to send System Control Commands to ROSRider**

```c
uint8_t send_sysctl(int fd, uint8_t command) {

	unsigned char sysctl_buffer[5] = {0};
	sysctl_buffer[3] = command;

	uint8_t rw = I2C_RW_Block(fd, 0x04, I2C_SMBUS_WRITE, 5, sysctl_buffer);
	if(rw == 0) { 
		return 0; 
	} else {
		RCLCPP_INFO(this->get_logger(), "I2C Error: %s", strerror(rw));
		return rw;
	}

}
```

**Instrumenting Linux**

[TODO: haton at boot time]  
[TODO: hatoff at boot time]  

**Important Node**

While we can leverage Python scripts to manage peripheral control at boot and shutdown, the ROSRiders driver offers a software-based approach to dynamically activate and deactivate peripherals in response to ROS node lifecycle events.

[TODO: ROS2RPi board connection with qwic table]  

**Understanding Hibernation** 

The ROSRiders driver leverages the I2C bus to communicate with the ROSRider card. By powering the I2C port on the ROS2RPi, a wake-up signal is generated, awakening the ROSRider card from a low-power state. This allows for efficient power management and quick activation when needed.

---

ROS2RPI mounted a top RPI5

<div style="display: flex; justify-content: space-around; margin: 25px 0;">
   <img src="images/ros2rpi.png" alt="ROS2RPI mounted a top RPI5" style="box-shadow: 0px 4px 8px rgba(0, 0, 0, 0.2);">
</div>