# Increase Diskspace in Proxmox and Make All the Available Free Space Usable in a Ubuntu Linux VM

## Steps

### Shutdown VM
1. In Proxmox, select the VM you would like to add disk space to.
2. Select the **Hardware** tab.
3. Click on the Hard Disk you want to resize.
4. Click **Disk Action** (above all the hardware details) then **Resize**.
5. Enter the amount of space you would like to increase the disk by. (This is additive. If your disk is currently 32GB and you want it to be 64GB, you would need to add 32GB.)
6. Start the VM.

### Terminal to VM and Login
1. Drop to Sudo:
    ```bash
    sudo su
    ```

### Check for Free Space
1. Run:
    ```bash
    df -h
    ```
    Nothing should have changed at this point.

### Resize the Disk
1. Run:
    ```bash
    cfdisk
    ```
2. At the bottom of the list, you should see ">> Free space". This will be equal to the amount you added to your disk above.
3. Select the partition you would like to resize (something like `/dev/sda3` **you will need this in the next step**).
4. Select **RESIZE** and hit **ENTER**.
5. The new size will use the entire amount of free disk space by default, hit **ENTER**.
6. Click **WRITE**, then type 'yes', and press **ENTER** to commit the change.
7. You should no longer see the ">> Free space" in the list.
8. Press 'q' or select **Quit**.

### Extend the Partition
1. Run:
    ```bash
    pvresize {Volume}
    ```
    `{Volume}` will be the device you resized in `cfdisk` (something like `/dev/sda3`).

### Check Volume Group for Free Space
1. Run:
    ```bash
    vgdisplay
    ```

### Get the LV Path
1. Run:
    ```bash
    lvdisplay
    ```
2. Copy the LV Path.

### Extend the Volume
1. Run:
    ```bash
    lvextend -l +100%FREE {LVPath}
    ```
    `{LVPath}` will be something like `/dev/ubuntu-vg/ubuntu-lv`.

### Confirm Changes
1. Run:
    ```bash
    lvdisplay
    ```

### Get Root File System Path
1. Run:
    ```bash
    df -h
    ```
2. Copy the `/dev/mapper` full path.

### Resize Root File System
1. Run:
    ```bash
    resize2fs {RootFSPath}
    ```
    `{RootFSPath}` was copied in the previous step and should be something like `/dev/mapper/ubuntu--vg-ubuntu--lv`.

### Confirm Changes
1. Run:
    ```bash
    df -h
    ```

### Optional: Reboot the Newly Resized VM
