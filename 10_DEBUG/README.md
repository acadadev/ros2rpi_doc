---
layout: default
title_url: /10_DEBUG/README.html
title: "Troubleshooting"
description: "Error Conditions on the ROS2RPI Card"
---

<div class="sl">
    <div class="sl1">
        > Troubleshooting
    </div>
</div>

__Understanding Hibernation__

The ROSRiders driver leverages the I2C bus to communicate with the ROSRider card.
By powering the I2C port on the ROS2RPi, a wake-up signal is generated, awakening the ROSRider card from a low-power state.
This allows for efficient power management and quick activation when needed.

***TODO***
- Installation close up. maybe video.
- Video of haton hatoff by driver.
- Explanation of parameter 0x33 in rosrider, how to instrument, 0x00 to cancel feature

__Return to the [Introduction](../README.md)__ 

