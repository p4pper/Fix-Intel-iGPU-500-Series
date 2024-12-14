# Fix UHD630 Acceleration + Output on 500 Series Motherboards. 
It is a well-known fact that macOS does not support iGPU functionality on 500-series motherboards out of the box. This typically results in one of three scenarios: hardware acceleration without video output, video output without hardware acceleration, or neither working properly.

Fortunately, there is a simple solution. We can embed the monitor’s EDID directly into the DeviceProperties configuration. This fix supports multiple monitors as well, but you’ll need to use Windows to extract the EDID and adjust your .plist configuration 

## Guide  
### Step 1  - Grab your Monitor's EDID
Extended Display Identification Data (EDID) allows your monitor to communicate its capabilities to your computer.

There are many ways to grab your Monitor's EDID, Including:
1. [Monitor Asset Manager](#monitor-asset-manager)
2. [Windows Regedit](#windows-regedit)

#### 1 - Using Monitor Asset Manager
Launch Monitor Asset Manager and scroll down to the bottom in the **Asset information**   
You will want the raw data section from the **Asset information** panel, not the **Raw data** panel   

![image](https://github.com/user-attachments/assets/10d1a8fc-4466-4948-938a-7be1908d03c1)   

Copy the hex information and paste it into a notepad
![image](https://github.com/user-attachments/assets/98e187f5-a15e-4380-ab44-72c10c0ee91d)   

Clean up those commas and spaces  
Click on ``Find -> Replace``, or Ctrl+H.  
Find: ``,``   
Replace: `` ``    

<img src="https://github.com/user-attachments/assets/6dfd1ca3-0693-4d08-8afe-0d3ff99ddaea" alt="image" width="1000"/>

End Results:   
![image](https://github.com/user-attachments/assets/8256f47c-ff23-4320-bf5d-e37ccf9aae48)

Remove the whitespaces and ensure the text is in one line (even if it breaks due to text wrap)    
![image](https://github.com/user-attachments/assets/9d9c4e02-a604-4de8-b0bd-81a69def9607)

#### 2 - Using Windows Regedit
It is possible to grab your EDID regedit, I'll implement the steps soon.


### Step 2  - Injecting EDID into config.plist   

Open your config.plist file from Opencore using [**ProperTree**](https://github.com/corpnewt/ProperTree)   

Head to ``DeviceProperties`` -> ``Add`` -> ``PciRoot(0x0)/Pci(0x2,0x0)``  

**Add only those fields:**
| **Key**                        | **Data**                                                                                                                                                                                                                                                   |
|--------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `AAPL,ig-platform-id`          | `07009B3E`                                                                                                                                                                                                                                                          |
| `AAPL00,override-no-connect`   |  |
| `AAPL01,override-no-connect`   |  |
| `AAPL02,override-no-connect`   |  |
| `device-id`                    | `9B3E0000`                                                                                                                                                                                                                                                          |
| `framebuffer-con0-enable`      | `01000000`                                                                                                                                                                                                                                                          |
| `framebuffer-con0-type`        | `00080000`                                                                                                                                                                                                                                                          |
| `framebuffer-con1-enable`      | `01000000`                                                                                                                                                                                                                                                          |
| `framebuffer-con1-type`        | `00080000`                                                                                                                                                                                                                                                          |
| `framebuffer-con2-enable`      | `01000000`                                                                                                                                                                                                                                                          |
| `framebuffer-con2-type`        | `00080000`                                                                                                                                                                                                                                                          |
| `framebuffer-patch-enable`     | `01000000`                                                                                                                                                                                                                                                          |  


We will inject our EDID code into ``AAPL00,override-no-connect``, ``AAPL01,override-no-connect`` and ``AAPL03,override-no-connect``.   
each AAPL increment represents a monitor, If you have multiple monitors, each has it's own EDID; so inject respectively.   

**Important, each monitor has it's own, unique EDID, Do not use one from somewhere else, even if it's the same monitor!**   

Your AAPL values should look like this:  
| **Key**                        | **Data**                                                                                                                                                                                                                                                   |
|--------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `AAPL00,override-no-connect`   | `00FFFFFFFFFFFF004C2D1A0D52515A5A071C010380301B782A5295A556549D250E5054BFEF80714F81C0810081809500A9C0B3000101023A801871382D40582C4500DD0C1100001E000000FD00324B1E5111000A202020202020000000FC00533232463335300A2020202020000000FF004834544B3230303735380A20200108` |
| `AAPL01,override-no-connect`   | `00FFFFFFFFFFFF004C2D1A0D52515A5A071C010380301B782A5295A556549D250E5054BFEF80714F81C0810081809500A9C0B3000101023A801871382D40582C4500DD0C1100001E000000FD00324B1E5111000A202020202020000000FC00533232463335300A2020202020000000FF004834544B3230303735380A20200108` |
| `AAPL02,override-no-connect`   | `00FFFFFFFFFFFF004C2D1A0D52515A5A071C010380301B782A5295A556549D250E5054BFEF80714F81C0810081809500A9C0B3000101023A801871382D40582C4500DD0C1100001E000000FD00324B1E5111000A202020202020000000FC00533232463335300A2020202020000000FF004834544B3230303735380A20200108` |

### Step 3 - VRAM Patching   
Test your config first if it gives an output. Remember, RecoveryOS does not use Graphics acceleration   
Boot into macOS and check your VRAM size, if it's 7MB, then you need to patch your graphics using [Whatevergreen's guide](https://github.com/acidanthera/WhateverGreen/blob/master/Manual/FAQ.IntelHD.en.md)   

### Wrapping up   
If you change or upgrade your monitor, you will have to grab it's EDID all again.

### Acknowledgments 
[Video tutorial by 乌龙蜜桃来一打](https://www.bilibili.com/video/BV1UW4y1J7J2/)  
[ProperTree by corpnewt](https://github.com/corpnewt/ProperTree)   
[Opencore by acidanthera](https://github.com/acidanthera/OpenCorePkg)   
[Whatevergreen guide by acidanthera](https://github.com/acidanthera/WhateverGreen/blob/master/Manual/FAQ.IntelHD.en.md#intel-uhd-graphics-610-655-coffee-lake-and-comet-lake-processors)

