## The best way to understand a technical is finding some documents.
https://www.cnblogs.com/iini/p/16085811.html

https://developer.nordicsemi.com/nRF_Connect_SDK/doc/latest/nrf/libraries/dfu/index.html

https://developer.nordicsemi.com/nRF_Connect_SDK/doc/latest/zephyr/services/device_mgmt/mcumgr.html

If you want to implement cross_dfu, this link is useful.
https://devzone.nordicsemi.com/guides/short-range-guides/b/software-development-kit/posts/getting-started-with-nordics-secure-dfu-bootloader

## How many ways to update firmware?
1. download firmware by JLINK
2. download firmware by NRF bootloader
3. download firmware by MCUboot
4. download firmware by DIY protocol just like cross_dfu project writing by Nordic AE

## How many ways to receive firmware?
1. from BLE
2. from Serial
3. from SPI
4. from USB


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
And this link introduce each images.
https://developer.nordicsemi.com/nRF_Connect_SDK/doc/latest/nrf/app_dev/multi_image/index.html#what-image-files-are

1. Next schedule is researching how these files been generated.
2. There are many ways to do DFU. such as using bootloader to do DFU by UART, SPI, and using wireless by bluetooth, celluar.


Doing.....