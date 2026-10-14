# fedora-rescue

Automatically create bootable snapshots of Fedora 44 on btrfs. Really useful if:

* You accidentally delete or move things on your root
* You install some upgrades, which break your system

## Installation

* Install ruby and Btrfs Assistant
* Create a snapper config for your root filesystem, which must be called `root`
* Copy update-rescue-snapshots to /usr/local/bin
* Copy update-rescue-snapshots.service and .timer to /etc/systemd/system. Reload systemd, and enable the timer
* Add to /etc/btrfs-assistant.conf something like:
  ```
  [Subvol-Mapping]
  root = "root/.snapshots,root,MY_UUID"
  ```
* Add to /boot/grub2/custom.cfg: `source $prefix/rescue.cfg`

## Usage

Create snapshots:

* Manually, with `update-rescue-snapshots create`
* Automatically, which happens once a day

Later, if you mess up your system:

* Reboot and choose "Rescue snapshots" from the menu. Choose a snapshot, and you'll boot into a **writable** snapshot.
* From the snapshot, you probably want to launch Btrfs Assistant and restore a working snapshot to `root`. But you can also do any other operations to fix `root`. **You probably shouldn't stay in the snapshot forever, updates will get weird**.
* Reboot back into your fixed root.

## Notes

* Snapshots will be auto-deleted when either:
  * They're older than 7 days
  * The relevant kernel is no longer present, eg: after multiple kernel upgrades
* Makes a few assumptions about your system:
  * Your root is on btrfs
  * You have snapper setup, with a `root` config
  * You can use Grub as the bootloader
  * If you have swap, it's a subvolume at root/swap
