<div class="sl">
    <div class="sl1">
        > ROS2RPI HAT
    </div>
    <div class="sl2">
        "Seamlessly integrate LIDAR, dual I2C sensors, and displays with professional-grade active cooling for Raspberry Pi 4 & 5."
    </div>
</div>

The ***ROS2RPI HAT*** is a powerful add-on board designed specifically for Raspberry Pi 4/5 models. It enhances the capabilities of your Raspberry Pi by providing a range of features, including:

- ***Dual Independent I2C Ports:*** Dual Bidirectional I2C Bus and Voltage-Level Translators, enabling flexible connectivity to a wide range of QWIC sensors.
- ***I2C GPIO Expander:*** The ROS2RPI HAT utilizes an I2C GPIO expander to efficiently manage its functionalities, freeing up valuable Raspberry Pi GPIO pins for other applications.
- ***LIDAR Integration:*** A dedicated port for connecting Lidar sensors, directly interfaced with the Raspberry Pi's hardware serial port.
- ***ROS2RPI HAT*** offers a flexible serial port that can be configured for either LIDAR communication or external debugging. This dual-purpose functionality enables remote debugging and troubleshooting, enhancing the HAT's versatility and ease of use.
- ***Active Cooling:*** A 30mm fan and metal heat sink at the bottom ensures optimal thermal performance, especially during intensive processing or sensor data acquisition.
- ***OLED Display Expansion:*** This port connects RPIs SPI peripheral to a Waveshare 240x280 SPI Display Module. 

<div style="display: flex; justify-content: space-around; margin: 25px 0;">
   <img src="images/ros2rpi_top.jpg" alt="ROS2RPI Hat" style="box-shadow: 0px 4px 8px rgba(0, 0, 0, 0.2);">
</div>







#### Understanding Hibernation

The ROSRiders driver leverages the I2C bus to communicate with the ROSRider card. By powering the I2C port on the ROS2RPi, a wake-up signal is generated, awakening the ROSRider card from a low-power state. This allows for efficient power management and quick activation when needed.

***TODO***

- Video of haton-hatoff by driver.
- Explanation of parameter 0x33 in rosrider, how to instrument, 0x00 to cancel feature
- Put newest photo