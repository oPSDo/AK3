### AnyKernel3 Ramdisk Mod Script
## KernelSU with SUSFS By oPSDo
## osm0sis @ xda-developers

### AnyKernel setup
# global properties
properties() { '
kernel.string=OKI Kernel by p0s3id0n (Powered by GitHub@oPSDo)
do.devicecheck=0
do.modules=0
do.systemless=0
do.cleanup=1
do.cleanuponabort=1
device.name1=
device.name2=
device.name3=
device.name4=
device.name5=
supported.versions=
supported.patchlevels=
supported.vendorpatchlevels=
'; } # end properties

### AnyKernel install
## boot shell variables
block=boot
is_slot_device=auto
ramdisk_compression=auto
patch_vbmeta_flag=auto
no_magisk_check=1

# import functions/variables and setup patching - see for reference (DO NOT REMOVE)
. tools/ak3-core.sh

kernel_version=$(cat /proc/version | awk -F '-' '{print $1}' | awk '{print $3}')
case $kernel_version in
    4.1*) ksu_supported=true ;;
    5.1*) ksu_supported=true ;;
    6.1*) ksu_supported=true ;;
    6.6*) ksu_supported=true ;;
    *) ksu_supported=false ;;
esac

ui_print "  -> ksu_supported: $ksu_supported"
$ksu_supported || abort "  -> Non-GKI device, abort."

# =============
# Kiểm tra phương thức Root (Phát hiện Magisk)
# =============
if [ -d /data/adb/magisk ] || [ -f /sbin/.magisk ]; then
    ui_print "============="
    ui_print " Phát hiện Magisk hoặc các tệp tàn dư"
    ui_print " Flash kernel trong tình trạng này có thể gây brick thiết bị"
    ui_print " Bạn có muốn tiếp tục cài đặt không?"
    ui_print " Magisk has been detected (or residual files)."
    ui_print " Flashing the kernel may brick your device"
    ui_print " Do you want to continue?"
    ui_print "-----------------"
    ui_print " Phím Tăng âm lượng: Thoát script (Khuyên dùng)"
    ui_print " Phím Giảm âm lượng: Tiếp tục cài đặt (Tự chịu rủi ro)"
    ui_print " Volume UP: Exit script (recommended)"
    ui_print " Volume DOWN: Continue installation (at your own risk)"
    ui_print "============="

    key_click=""
    while [ "$key_click" = "" ]; do
        key_click=$(getevent -qlc 1 | awk '{ print $3 }' | grep 'KEY_VOLUME')
        sleep 0.2
    done

    case "$key_click" in
        "KEY_VOLUMEUP")
            ui_print " Bạn đã chọn thoát. Quá trình cài đặt đã được hủy an toàn."
            ui_print " You chose to exit. Installation aborted safely."
            exit 0
            ;;
        "KEY_VOLUMEDOWN")
            ui_print " Bạn đã chọn tiếp tục cài đặt. Vui lòng chú ý rủi ro!"
            ui_print " You chose to continue installation. Proceed with caution!"
            ;;
        *)
            ui_print " Phím bấm không hợp lệ. Đang thoát script."
            ui_print " Unknown key input. Exiting script."
            exit 1
            ;;
    esac
fi

ui_print "Bắt đầu cài đặt kernel..."
ui_print "Powered by GitHub@oPSDo"

split_boot
if [ -f "split_img/ramdisk.cpio" ]; then
    unpack_ramdisk
    write_boot
else
    flash_boot
fi

# =============
# Cài đặt Module SUSFS
# =============
if [ -f "$AKHOME/ksu_module_susfs_1.5.2+_Release.zip" ]; then
    MODULE_PATH="$AKHOME/ksu_module_susfs_1.5.2+_Release.zip"
    ui_print "  -> Found SUSFS Module (Release)"
elif [ -f "$AKHOME/ksu_module_susfs_1.5.2+_CI.zip" ]; then
    MODULE_PATH="$AKHOME/ksu_module_susfs_1.5.2+_CI.zip"
    ui_print "  -> Found SUSFS Module (CI)"
else
    MODULE_PATH=""
    ui_print "  -> No SUSFS Module found.You may have selected NON mode,skipping installation."
fi

if [ -n "$MODULE_PATH" ]; then
    KSUD_PATH="/data/adb/ksud"
    ui_print "============="
    ui_print " Bạn có muốn cài đặt module SUSFS không?"
    ui_print " Install susfs4ksu Module?"
    ui_print "-----------------"
    ui_print " Phím Tăng âm lượng: Bỏ qua cài đặt"
    ui_print " Phím Giảm âm lượng: Cài đặt module"
    ui_print " Volume UP: Skip installation"
    ui_print " Volume DOWN: Install module"
    ui_print "============="

    key_click=""
    while [ "$key_click" = "" ]; do
        key_click=$(getevent -qlc 1 | awk '{ print $3 }' | grep 'KEY_VOLUME')
        sleep 0.2
    done

    case "$key_click" in
        "KEY_VOLUMEDOWN")
            if [ -f "$KSUD_PATH" ]; then
                ui_print " Đang cài đặt module SUSFS..."
                ui_print " Installing SUSFS Module..."
                /data/adb/ksud module install "$MODULE_PATH"
                ui_print " Cài đặt hoàn tất!"
                ui_print " Installation complete!"
            else
                ui_print " Không tìm thấy KSUD, bỏ qua cài đặt."
                ui_print " KSUD not found. Skipping installation."
            fi
            ;;
        "KEY_VOLUMEUP")
            ui_print " Đã bỏ qua quá trình cài đặt module SUSFS."
            ui_print " Skipped SUSFS Module installation."
            ;;
        *)
            ui_print " Phím bấm không hợp lệ. Đã bỏ qua cài đặt module SUSFS."
            ui_print " Unknown key input. Skipped SUSFS Module installation."
            ;;
    esac
fi