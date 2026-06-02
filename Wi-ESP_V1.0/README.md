*This clone is made for benchmarking purposes against other CSI firmware implementations.*

The original Wi-ESP (V1.0) source repository contained an incomplete hardware initialization sequence where the physical radio state wrapper (esp_wifi_start()) and the active console logging loop were deactivated in the source files.

To ensure a fair, identical, and functionally valid baseline comparison against the Hernandez firmware, minimal core modifications were applied to Wi-ESP solely to enable hardware power-on and data serialization output. No algorithmic or performance optimizations were performed.

The total flash size of the firmware after these modifications is as extracted by the command `idf.py size` is:

```bash
Total sizes:
Used static DRAM:   28788 bytes ( 151948 remain, 15.9% used)
      .data size:   13284 bytes
      .bss  size:   15504 bytes
Used static IRAM:   86326 bytes (  44746 remain, 65.9% used)
      .text size:   85299 bytes
   .vectors size:    1027 bytes
Used Flash size :  553119 bytes
      .text     :  462907 bytes
      .rodata   :   89956 bytes
Total image size:  652729 bytes (.bin may be padded larger)
```

To find more details about the resource usage of the firmware such as CPU load (example: % time spent in CSI processing task) and RAM usage (heap + stack) during runtime, please use the `resources_usage` branch of this repository which contains the necessary code for measuring these metrics.

Some chips might need to build the firmware `idf.py build`, then flash it without stub using `python3 ~/esp-idf-v4.4/components/esptool_py/esptool/esptool.py --chip esp32 -p /dev/ttyUSB0 -b 57600 --no-stub write_flash --flash_mode dio --flash_freq 40m --flash_size 4MB 0x1000 build/bootloader/bootloader.bin 0x10000 build/csi.bin 0x8000 build/partition_table/partition-table.bin` to work properly. This is because the stub might cause timing issues during the flashing process which can lead to a flashing error. After that you can use the `idf.py monitor` command to see the output of the firmware in the console.


This implementation includes the code for acquiring the CSI (Channel State Information) using ESP32.
NOTE: This code is designed to be compatible with both Espressif version 4.3 and earlier versions.
