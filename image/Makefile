# Path to where the latest rapsios image will be downloaded to.
OUTPUT_DIR=download

# Path to where the image gets temporarily mounted.
MOUNT_DIR=mnt
LOOP_DEVICE=/dev/loop10

# SSH varibles that must be set. 
# It is needed to add authorized ssh keys before a user is created.
SSH_USER_HOME=home/pi/.ssh
SSH_KEY=https://github.com/kjeller.keys

# Path to sensitive files that are needed in the setup e.g. WIFI password
# Not required.
SECRETS_DIR=../../secrets/image-boot

# One of the following:
# lite|full
VER=lite

# One of the following:
# armhf|arm64
ARCH=armhf

IMG=raspios
RASPIOS=${IMG}_${VER}_${ARCH}
BASE_URL=https://downloads.raspberrypi.org/${RASPIOS}/images
LATEST_IMG=${shell wget -q -O - ${BASE_URL} | grep -o "${RASPIOS}-[0-9]\{4\}-[0-9]\{2\}-[0-9]\{2\}" | tail -1}
IMG_NAME=${shell wget -q -O - ${BASE_URL}/${LATEST_IMG}/ | grep -o "[0-9]\{4\}-[0-9]\{2\}-[0-9]\{2\}-${IMG}-[a-z]\+-${ARCH}-${VER}" | tail -1}
RASPIOS_TARGET=${OUTPUT_DIR}/${IMG_NAME}

.PHONY: build

all: build

build: ${RASPIOS_TARGET}.img
	@echo "Mounting image file as loop device.."
	mkdir -p ${MOUNT_DIR} 
	losetup -P ${LOOP_DEVICE} ${RASPIOS_TARGET}.img
	mount ${LOOP_DEVICE}p2 ${MOUNT_DIR}
	mount ${LOOP_DEVICE}p1 ${MOUNT_DIR}/boot
	@echo "Copying authorized keys to user home"
	mkdir -p ${MOUNT_DIR}/${SSH_USER_HOME}
	wget ${SSH_KEY} -O ${MOUNT_DIR}/${SSH_USER_HOME}/authorized_keys
	@echo "Copying secret wpa_supplicant file into boot partition"
	cp -r ${SECRETS_DIR}/* ${MOUNT_DIR}/boot/
	@echo "Enabling ssh server on rpi on boot"
	touch ${MOUNT_DIR}/boot/ssh
	cp -r image-root/* ${MOUNT_DIR}/
	@echo "Renaming altered image filename"
	mv ${RASPIOS_TARGET}.img ${RASPIOS_TARGET}_homeassistant.img
	@echo "Cleaning and unmounting loop device"
	umount -l ${MOUNT_DIR}/boot
	umount -l ${MOUNT_DIR}
	losetup -d ${LOOP_DEVICE}
	rm -rf ${MOUNT_DIR}
	@echo "Done altering image!"

${RASPIOS_TARGET}.img: ${RASPIOS_TARGET}.zip
	unzip $< -d ${OUTPUT_DIR}

${RASPIOS_TARGET}.zip:
	@echo "Retrieving latest raspios ${LATEST_IMG} from ${BASE_URL}"
	mkdir -p ${OUTPUT_DIR} 
	wget -q --show-progress --progress=bar -l 1 -r -np -nH -nd --accept zip,sha256 -e robots=off -P ${OUTPUT_DIR} ${BASE_URL}/${LATEST_IMG}
	cd ${OUTPUT_DIR} && sha256sum -c ${IMG_NAME}.zip.sha256

clean:
	rm -rf ${OUTPUT_DIR}
	rm -rf ${MOUNT_DIR}
