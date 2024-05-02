# Monitor mode and BT hci0 interface for NetHunter

Some time ago I'd found monitor mode for MediaTek phones, maybe with injections; for Samsung, Huawei devices (Broadcom wifi chip); also found hci0 for MediaTek and Qualcomm: qcacld-3.0 and prima, devices.

## MediaTek
### Monitor mode
Bears responsibility for the possibility itself the enabling the monitor mode parameter is called **CFG_SUPPORT_SNIFFER_RADIOTAP** or **CFG_SUPPORT_SNIFFER**. He is located not in defconfig or Kconfig of any driver. He located in file [core/(version wifi driver)/include/config.h](https://github.com/Saikrishna1504/kernel_xiaomi_mt6785/blob/c4bc51e7045829685946c0c1ceb9b9d2dc758b29/drivers/misc/mediatek/connectivity/wlan/core/gen4m/include/config.h) and set ups via difining this option like [this](https://github.com/Saikrishna1504/kernel_xiaomi_mt6785/blob/c4bc51e7045829685946c0c1ceb9b9d2dc758b29/drivers/misc/mediatek/connectivity/wlan/core/gen4m/include/config.h#L1087). Also he is can locating in Makefile of driver folder like this [example](https://github.com/Coconutat/android_kernel_xiaomi_ruby_exp/blob/0c83ba51e8db2ddb7d37f3bca90cc846896079bd/drivers/misc/mediatek/connectivity/wlan/core/gen4m/Makefile#L301), where he is enables by enabling parameter named [**CONFIG_MTK_WIFI_SNIFFER_RADIOTAP**](https://github.com/Coconutat/android_kernel_xiaomi_ruby_exp/blob/0c83ba51e8db2ddb7d37f3bca90cc846896079bd/drivers/misc/mediatek/connectivity/wlan/core/gen4m/Makefile#L300C9-L300C41). He is also absent in defconfig of kernel or Kconfig, but located in this one Makefile, and drumroll..... is [disabled](https://github.com/Coconutat/android_kernel_xiaomi_ruby_exp/blob/0c83ba51e8db2ddb7d37f3bca90cc846896079bd/drivers/misc/mediatek/connectivity/wlan/core/gen4m/Makefile#L225) only he one 😆. This is like **CONFIG_FEATURE_MONITOR_MODE_SUPPORT** on qcacld-3.0 driver. Monitor mode on MediaTek devices enables by iwpriv program, not by writing numbers, how it makes in qcacld-3.0 devices. While I was looking everything related to this options I'd found such function called [priv_driver_set_monitor](https://github.com/Saikrishna1504/kernel_xiaomi_mt6785/blob/c4bc51e7045829685946c0c1ceb9b9d2dc758b29/drivers/misc/mediatek/connectivity/wlan/core/gen4m/os/linux/gl_wext_priv.c#L9962) in file [core/gen4m/os/linux/gl_wext_priv.c](https://github.com/Saikrishna1504/kernel_xiaomi_mt6785/blob/udc/drivers/misc/mediatek/connectivity/wlan/core/gen4m/os/linux/gl_wext_priv.c), where wrote such [code (9995-10123)](https://github.com/Saikrishna1504/kernel_xiaomi_mt6785/blob/c4bc51e7045829685946c0c1ceb9b9d2dc758b29/drivers/misc/mediatek/connectivity/wlan/core/gen4m/os/linux/gl_wext_priv.c#L9995) and such [explaining](https://github.com/Saikrishna1504/kernel_xiaomi_mt6785/blob/c4bc51e7045829685946c0c1ceb9b9d2dc758b29/drivers/misc/mediatek/connectivity/wlan/core/gen4m/os/linux/gl_wext_priv.c#L10119).

Command for enabling monitor mode (turning wifi chip into monitor mode) looks like this:

`iwpriv wlan0 driver monitor <options>`

On different devices this options, that you are needing, will be different. For example, I'll take this function from [kernel](https://github.com/Coconutat/android_kernel_xiaomi_ruby_exp/tree/MUI14-KernelSU) for Xiaomi Redmi Note 12 Pro and from [kernel](https://github.com/Saikrishna1504/kernel_xiaomi_mt6785/tree/udc) for Xiaomi Redmi Note 8 Pro. In the first one, needing for us [option](https://github.com/Coconutat/android_kernel_xiaomi_ruby_exp/blob/0c83ba51e8db2ddb7d37f3bca90cc846896079bd/drivers/misc/mediatek/connectivity/wlan/core/gen4m/os/linux/gl_wext_priv.c#L8230) named differently, then it was on kernel for Xiaomi Redmi Note 8 Pro, have other [code](https://github.com/Coconutat/android_kernel_xiaomi_ruby_exp/blob/0c83ba51e8db2ddb7d37f3bca90cc846896079bd/drivers/misc/mediatek/connectivity/wlan/core/gen4m/os/linux/gl_wext_priv.c#L8253) and other [explaining](https://github.com/Coconutat/android_kernel_xiaomi_ruby_exp/blob/0c83ba51e8db2ddb7d37f3bca90cc846896079bd/drivers/misc/mediatek/connectivity/wlan/core/gen4m/os/linux/gl_wext_priv.c#L8289) (thanks, for that, what at all is some). Therefore, and options for enabling will be different. As far as I understand options for kernel Xiaomi Redmi Note 8 Pro will be like this

`iwpriv wlan0 driver monitor [enable or disable (1 or 0)] [primary channel] [channel width] [sco]`

If channel less then or equals 14, then frequency will be 2,4 GHz, if more then 14, then will be 5 GHz. This is wrote [here](https://github.com/Saikrishna1504/kernel_xiaomi_mt6785/blob/c4bc51e7045829685946c0c1ceb9b9d2dc758b29/drivers/misc/mediatek/connectivity/wlan/core/gen4m/os/linux/gl_wext_priv.c#L10022). For channel width is such [options](https://github.com/Saikrishna1504/kernel_xiaomi_mt6785/blob/c4bc51e7045829685946c0c1ceb9b9d2dc758b29/drivers/misc/mediatek/connectivity/wlan/core/gen4m/os/linux/gl_wext_priv.c#L10033): [160](https://github.com/Saikrishna1504/kernel_xiaomi_mt6785/blob/c4bc51e7045829685946c0c1ceb9b9d2dc758b29/drivers/misc/mediatek/connectivity/wlan/core/gen4m/os/linux/gl_wext_priv.c#L10033), [80](https://github.com/Saikrishna1504/kernel_xiaomi_mt6785/blob/c4bc51e7045829685946c0c1ceb9b9d2dc758b29/drivers/misc/mediatek/connectivity/wlan/core/gen4m/os/linux/gl_wext_priv.c#L10046), [40](https://github.com/Saikrishna1504/kernel_xiaomi_mt6785/blob/c4bc51e7045829685946c0c1ceb9b9d2dc758b29/drivers/misc/mediatek/connectivity/wlan/core/gen4m/os/linux/gl_wext_priv.c#L10066), [20](https://github.com/Saikrishna1504/kernel_xiaomi_mt6785/blob/c4bc51e7045829685946c0c1ceb9b9d2dc758b29/drivers/misc/mediatek/connectivity/wlan/core/gen4m/os/linux/gl_wext_priv.c#L10072). Also for them are adjusting other parameters. "Sco" is "secondary channel offset". About this stuff need to read in the Internet. For more information need to read into a driver into a referenced function.

For Xiaomi Redmi Note 12 Pro I even don't know what command we need

### About packets injections
In this two kernels is have **radiotap header**. In first kernel he is locate in file [core/gen4m/os/linux/include/gl_kal.h](https://github.com/Saikrishna1504/kernel_xiaomi_mt6785/blob/udc/drivers/misc/mediatek/connectivity/wlan/core/gen4m/os/linux/include/gl_kal.h) on line [341](https://github.com/Saikrishna1504/kernel_xiaomi_mt6785/blob/udc/drivers/misc/mediatek/connectivity/wlan/core/gen4m/os/linux/include/gl_kal.h#341) - also 4 options, also packed. In second kernel he locate in file [core/gen4m/include/nic/radiotap.h](https://github.com/Coconutat/android_kernel_xiaomi_ruby_exp/blob/MUI14-KernelSU/drivers/misc/mediatek/connectivity/wlan/core/gen4m/include/nic/radiotap.h) - looks same with previous. In both kernel radiotap uses in only one file
`[nic/nic_rx.c](https://github.com/Saikrishna1504/kernel_xiaomi_mt6785/blob/udc/drivers/misc/mediatek/connectivity/wlan/core/gen4m/nic/nic_rx.c)`

Those are needing more qualifed peoples in order to figure out in this

###
