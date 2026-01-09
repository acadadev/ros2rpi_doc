---
layout: default
title_url: /03_C/README.html
title: "C Code"
description: "Example C Code to Control ROS2RPI"
---

__Example C function send ROS2RPI command__

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

__Example C function to send System Control Commands to ROSRider__

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