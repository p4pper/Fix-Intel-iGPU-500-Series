# Fix UHD630 Acceleration + Output on 500 Series Motherboards. 

It is known that any 500 series motherboard does not work with iGPUs on macOS.
You will get acceleration, no output, Or output with no acceleration.

There is an easy fix thankfully, which is done by hardwiring our monitor EDID into DeviceProperties.
You can connect multiple monitors and it'll work, but you will have to use Windows to configure your .plist accordingly.

## Guide  
