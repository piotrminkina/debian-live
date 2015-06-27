in_target = chroot rootfs/ /bin/bash -c "export DEBIAN_FRONTEND=noninteractive; $(1)"


build: build.done

build.done: rootfs.done
	touch build.done

clean:
	rm -rf build.done rootfs.done rootfs/

rootfs.done:
	rm -rf rootfs/

	debootstrap --arch=amd64 --components=main,contrib,non-free --variant=minbase testing rootfs/ http://ftp.debian.org/debian

	echo "deb http://ftp.debian.org/debian testing-updates main contrib non-free" >> rootfs/etc/apt/sources.list
	echo "deb http://ftp.debian.org/debian testing-proposed-updates main contrib non-free" >> rootfs/etc/apt/sources.list

	echo "exit 101" > rootfs/usr/sbin/policy-rc.d
	chmod a+x rootfs/usr/sbin/policy-rc.d

	$(call in_target,apt-get update && apt-get -yqq install apt-utils && apt-get -yqq dist-upgrade)
	$(call in_target,apt-get -yqq install dialog dbus)
	$(call in_target,dbus-uuidgen > /var/lib/dbus/machine-id)
	$(call in_target,apt-get -yqq install linux-image-amd64 live-boot)
	$(call in_target,passwd -d root)
	$(call in_target,rm -f /var/lib/dbus/machine-id)

	rm -f rootfs/usr/sbin/policy-rc.d
	find rootfs/var/cache/apt/archives/ rootfs/var/lib/apt/lists/ -type f | xargs rm -f

	touch rootfs.done

.PHONY: build clean
