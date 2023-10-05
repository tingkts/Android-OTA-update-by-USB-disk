




### Build the OTA package

- full ota

  ```
    source build/envsetup.sh && lunch aosp_x86_64-eng && make -j12 && make ota package

    // ota package

       out/target/product/generic_x86_64/*-ota-*.zip

    // target files

       out/target/product/generic_x86_64/*-target_files-*.zip
  ```



- incremental ota

  ```
    // python script
    ./build/tools/releasetools/ota_from_target_files -p out/host/linux-x86 -i old-target_files.zip new-target_files.zip AB_delta.zip

    or

    // binary executable
    out/host/linux-x86/bin/ota_from_target_files -p out/host/linux-x86 -i old-target_files.zip new-target_files.zip AB_delta.zip
  ```






</br>
&nbsp;&nbsp;&nbsp;✱　Update android build timestamp so no need to clear build every time


&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;　　`touch build/core/build_id.mk`




</br>

### Do OTA update

step 1 ： trigger update_engine to apply otapackage.zip

- by python script in the host PC

  ```
    // Copy aosp system/update-engine/script/update_device.py to your PC
    // Connect the target device with PC via usb cable (check with "adb devices")

    python update_device.py --file update.zip
  ```




- by android demo App in the target device

  ⁍　[bootable/recovery/updater_sample](https://android.googlesource.com/platform/bootable/recovery/+/refs/tags/android-13.0.0_r1/updater_sample)　👈


  ```
  　bootable/recovery/updater_sample/tools/gen_update_config.py \
          --ab_install_type=NON_STREAMING  \
          --ab_force_switch_slot \
          out/target/product/aiot8395p1_64_ms5628/merged/otapackage.zip \
          otaconfig.json \
          file:///data/otazip/Download/otapackage.zip
  ```


  ⁍　[frameworks/base/services/devicepolicy/java/com/android/server/devicepolicy/AbUpdateInstaller.java](https://android.googlesource.com/platform/frameworks/base/+/refs/tags/android-13.0.0_r1/services/devicepolicy/java/com/android/server/devicepolicy/AbUpdateInstaller.java)

  ⁍　[packages/apps/Car/SystemUpdate](https://android.googlesource.com/platform/packages/apps/Car/SystemUpdater/+/refs/tags/android-13.0.0_r1)

  ⁍　[vendor/nxp-opensource/fsl_imx_demo/FSLOta](https://github.com/nxp-imx-android/android-imx_platform_packages_apps_fsl_imx_demo/tree/imx_or8.0/FSLOta)






step 2 ： manually reboot

  ```
    adb reboot
  ```


step 3 ： check slot switched

  ```

  λ adb shell getprop | grep -i slot

    [cache_key.telephony.get_slot_index]: [-3366528720788410286]
    [ro.boot.slot]: [b]                             // ←　check the boot slot
    [ro.boot.slot_suffix]: [_b]
    [ro.vendor.mtk_external_sim_only_slots]: [0]


  λ adb shell getprop | grep -i utc

    [ro.build.date.utc]: [1696476254]               // ←　check the build time
    [ro.odm.build.date.utc]: [1696476784]
    [ro.odm_dlkm.build.date.utc]: [1696416132]
    [ro.product.build.date.utc]: [1696476254]
    [ro.system.build.date.utc]: [1696476254]
    [ro.system_dlkm.build.date.utc]: [1696476784]
    [ro.system_ext.build.date.utc]: [1696476254]
    [ro.vendor.build.date.utc]: [1696476784]
    [ro.vendor_dlkm.build.date.utc]: [1696476784]
  ```





</br>
&nbsp;&nbsp;&nbsp;✱　update.zip are the same of full ota package and incremental ota package.





</br>
</br>
</br>








`update_engine` dees not support downgrade update !!


<br>
<br>


<p align="right">base on aosp 13 (android-13.0.0_r1 ) with A/B system mechanism</p>