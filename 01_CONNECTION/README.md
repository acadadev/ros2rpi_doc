---
layout: default
title_url: /01_CONNECTION/README.html
title: "Connection"
description: "Connecting to ROSRider"
---

#### ROS2RPI connected to ROSRider with QWIC Cable

The image depicts the physical connection between the ROS2RPi HAT and ROSRider cards using a standard QWIC cable. The ROSRider card's additional QWIC port facilitates cascading configurations, enabling the expansion of the system with multiple ROSRider units or other compatible QWIC devices.

<div style="display: flex; justify-content: space-around; margin: 25px 0;">
   <img src="images/rosrider_ros2rpi_qwic.png" alt="Connected to ROSRider with QWIC Cable" style="width:45%; box-shadow: 0px 4px 8px rgba(0, 0, 0, 0.2);">
   <img src="images/qwic_connectors.jpg" alt="Various QWIC Cables" style="width:45%; box-shadow: 0px 4px 8px rgba(0, 0, 0, 0.2);">
</div>

Qwiic cables are a popular choice for connecting various sensors and actuators to microcontrollers and other devices. They feature a 4-wire JST-SH connector on each end. One unique aspect of Qwiic cables is their asymmetrical orientation. One connector will typically be `top-facing` while the other is `bottom-facing.` This deliberate design choice helps with cable routing and prevents accidental misconnections, making it easier to create clean and organized setups.

#### Controlling ROS2RPI

<div class="checkpoint">
  <p>⚠️&nbsp;<strong>Prerequisites</strong></p>
  <p>In order to send I2C commands from your host with Python, you need to install the <code class="language-plaintext highlighter-rouge">python3-smbus2</code> library.</p>
  <p>For Ubuntu:</p>
  <div class="highlight notranslate position-relative">
    <div class="highlight">
      <pre id="command-checkpoint-smbus2"><span></span>sudo apt install python3-smbus2</pre>
    </div>
    <clipboard-copy style="position:absolute; right:8px; top:8px;" for="command-checkpoint-smbus2">
      <svg aria-hidden="true" height="16" viewBox="0 0 16 16" version="1.1" width="16" data-view-component="true" class="octicon octicon-copy js-clipboard-copy-icon">
      <path d="M0 6.75C0 5.784.784 5 1.75 5h1.5a.75.75 0 0 1 0 1.5h-1.5a.25.25 0 0 0-.25.25v7.5c0 .138.112.25.25.25h7.5a.25.25 0 0 0 .25-.25v-1.5a.75.75 0 0 1 1.5 0v1.5A1.75 1.75 0 0 1 9.25 16h-7.5A1.75 1.75 0 0 1 0 14.25Z"></path><path d="M5 1.75C5 .784 5.784 0 6.75 0h7.5C15.216 0 16 .784 16 1.75v7.5A1.75 1.75 0 0 1 14.25 11h-7.5A1.75 1.75 0 0 1 5 9.25Zm1.75-.25a.25.25 0 0 0-.25.25v7.5c0 .138.112.25.25.25h7.5a.25.25 0 0 0 .25-.25v-7.5a.25.25 0 0 0-.25-.25Z"></path>
      </svg>
      <svg aria-hidden="true" height="16" viewBox="0 0 16 16" version="1.1" width="16" data-view-component="true" class="octicon octicon-check js-clipboard-check-icon color-fg-success d-none">
      <path d="M13.78 4.22a.75.75 0 0 1 0 1.06l-7.25 7.25a.75.75 0 0 1-1.06 0L2.22 9.28a.751.751 0 0 1 .018-1.042.751.751 0 0 1 1.042-.018L6 10.94l6.72-6.72a.75.75 0 0 1 1.06 0Z"></path>
      </svg>
    </clipboard-copy>
  </div>
  <div class="highlight notranslate position-relative">
    <p>If your Ubuntu version does not have the <code class="language-plaintext highlighter-rouge">python3-smbus2</code> package, use pip3 to install package locally:</p>    
    <div class="highlight">
      <pre id="command-checkpoint-pip-smbus2"><span></span>pip3 install smbus2</pre>
    </div> 
    <clipboard-copy style="position:absolute; right:8px; top:8px;" for="command-checkpoint-pip-smbus2">
      <svg aria-hidden="true" height="16" viewBox="0 0 16 16" version="1.1" width="16" data-view-component="true" class="octicon octicon-copy js-clipboard-copy-icon">
      <path d="M0 6.75C0 5.784.784 5 1.75 5h1.5a.75.75 0 0 1 0 1.5h-1.5a.25.25 0 0 0-.25.25v7.5c0 .138.112.25.25.25h7.5a.25.25 0 0 0 .25-.25v-1.5a.75.75 0 0 1 1.5 0v1.5A1.75 1.75 0 0 1 9.25 16h-7.5A1.75 1.75 0 0 1 0 14.25Z"></path><path d="M5 1.75C5 .784 5.784 0 6.75 0h7.5C15.216 0 16 .784 16 1.75v7.5A1.75 1.75 0 0 1 14.25 11h-7.5A1.75 1.75 0 0 1 5 9.25Zm1.75-.25a.25.25 0 0 0-.25.25v7.5c0 .138.112.25.25.25h7.5a.25.25 0 0 0 .25-.25v-7.5a.25.25 0 0 0-.25-.25Z"></path>
      </svg>
      <svg aria-hidden="true" height="16" viewBox="0 0 16 16" version="1.1" width="16" data-view-component="true" class="octicon octicon-check js-clipboard-check-icon color-fg-success d-none">
      <path d="M13.78 4.22a.75.75 0 0 1 0 1.06l-7.25 7.25a.75.75 0 0 1-1.06 0L2.22 9.28a.751.751 0 0 1 .018-1.042.751.751 0 0 1 1.042-.018L6 10.94l6.72-6.72a.75.75 0 0 1 1.06 0Z"></path>
      </svg>
    </clipboard-copy>
  </div>
</div>

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

<div class="checkpoint">
    <p>✅&nbsp;<strong>Checkpoint</strong></p>
    <p>
        To ensure you’re in the <code class="language-plaintext highlighter-rouge">i2c</code> group,
        check the <code class="language-plaintext highlighter-rouge">/etc/group</code> file or run the following command:
    </p>
    <div class="highlight notranslate position-relative">
        <div class="highlight">
            <pre id="command-groups"><span></span>groups</pre>
        </div>
        <clipboard-copy style="position:absolute; right:8px; top:8px;" for="command-groups">
            <svg aria-hidden="true" height="16" viewBox="0 0 16 16" version="1.1" width="16" data-view-component="true" class="octicon octicon-copy js-clipboard-copy-icon">
            <path d="M0 6.75C0 5.784.784 5 1.75 5h1.5a.75.75 0 0 1 0 1.5h-1.5a.25.25 0 0 0-.25.25v7.5c0 .138.112.25.25.25h7.5a.25.25 0 0 0 .25-.25v-1.5a.75.75 0 0 1 1.5 0v1.5A1.75 1.75 0 0 1 9.25 16h-7.5A1.75 1.75 0 0 1 0 14.25Z"></path><path d="M5 1.75C5 .784 5.784 0 6.75 0h7.5C15.216 0 16 .784 16 1.75v7.5A1.75 1.75 0 0 1 14.25 11h-7.5A1.75 1.75 0 0 1 5 9.25Zm1.75-.25a.25.25 0 0 0-.25.25v7.5c0 .138.112.25.25.25h7.5a.25.25 0 0 0 .25-.25v-7.5a.25.25 0 0 0-.25-.25Z"></path>
            </svg>
            <svg aria-hidden="true" height="16" viewBox="0 0 16 16" version="1.1" width="16" data-view-component="true" class="octicon octicon-check js-clipboard-check-icon color-fg-success d-none">
            <path d="M13.78 4.22a.75.75 0 0 1 0 1.06l-7.25 7.25a.75.75 0 0 1-1.06 0L2.22 9.28a.751.751 0 0 1 .018-1.042.751.751 0 0 1 1.042-.018L6 10.94l6.72-6.72a.75.75 0 0 1 1.06 0Z"></path>
            </svg>
        </clipboard-copy>
    </div>
    <p>If you're not listed, you'll need to add yourself to the group using the following command:</p>
    <div class="highlight notranslate position-relative">
        <div class="highlight">
          <pre id="command-usermod"><span></span>sudo usermod -aG i2c $USER</pre>
        </div>
        <clipboard-copy style="position:absolute; right:8px; top:8px;" for="command-usermod">
          <svg aria-hidden="true" height="16" viewBox="0 0 16 16" version="1.1" width="16" data-view-component="true" class="octicon octicon-copy js-clipboard-copy-icon">
          <path d="M0 6.75C0 5.784.784 5 1.75 5h1.5a.75.75 0 0 1 0 1.5h-1.5a.25.25 0 0 0-.25.25v7.5c0 .138.112.25.25.25h7.5a.25.25 0 0 0 .25-.25v-1.5a.75.75 0 0 1 1.5 0v1.5A1.75 1.75 0 0 1 9.25 16h-7.5A1.75 1.75 0 0 1 0 14.25Z"></path><path d="M5 1.75C5 .784 5.784 0 6.75 0h7.5C15.216 0 16 .784 16 1.75v7.5A1.75 1.75 0 0 1 14.25 11h-7.5A1.75 1.75 0 0 1 5 9.25Zm1.75-.25a.25.25 0 0 0-.25.25v7.5c0 .138.112.25.25.25h7.5a.25.25 0 0 0 .25-.25v-7.5a.25.25 0 0 0-.25-.25Z"></path>
          </svg>
          <svg aria-hidden="true" height="16" viewBox="0 0 16 16" version="1.1" width="16" data-view-component="true" class="octicon octicon-check js-clipboard-check-icon color-fg-success d-none">
          <path d="M13.78 4.22a.75.75 0 0 1 0 1.06l-7.25 7.25a.75.75 0 0 1-1.06 0L2.22 9.28a.751.751 0 0 1 .018-1.042.751.751 0 0 1 1.042-.018L6 10.94l6.72-6.72a.75.75 0 0 1 1.06 0Z"></path>
          </svg>
        </clipboard-copy>        
    </div>
    <p>Remember to log out and log back in for the group membership change to take effect.</p>
</div>

#### Understanding Hibernation

The ROSRiders driver leverages the I2C bus to communicate with the ROSRider card. By powering the I2C port on the ROS2RPi, a wake-up signal is generated, awakening the ROSRider card from a low-power state. This allows for efficient power management and quick activation when needed.
