## The best way to understand a technical is finding some documents.
https://www.cnblogs.com/iini/p/16085811.html

https://developer.nordicsemi.com/nRF_Connect_SDK/doc/latest/nrf/libraries/dfu/index.html

https://developer.nordicsemi.com/nRF_Connect_SDK/doc/latest/zephyr/services/device_mgmt/mcumgr.html

If you want to implement cross_dfu, this link is useful.
https://devzone.nordicsemi.com/guides/short-range-guides/b/software-development-kit/posts/getting-started-with-nordics-secure-dfu-bootloader#_cptype=root




Referring sample project (NCS2.1.2\zephyr\samples\subsys\mgmt\mcumgr\smp_svr)
img_mgmt_register_group() will write image into flash.

In my view, before we understand DFU feature, we need to be familiar with image format in flash. So I have an opinion to check it.

Firstly, flashing smp_svr to nrf5340.
Secondly, using nrf connect Programmer to read back images from flash.
Thirdly, using J-Flash to read back images from flash.
Finally, we compare the these image files.

Then we will understand the types of files in build folder, such as 
zephyr.hex
zephyr.bin
merged.hex
app_update.bin


Next schedule is researching how these files been generated.


Doing.....