# -= Increase Diskspace in Proxmox and make all the available free space useable in a Ubuntu Linux VM =-

# Shutdown VM
# In Proxmox select the VM you would like to add disk space to
# select the Hardware tab
# Click in the Hard Disk you want to resize
# Click "Disk Action" (above all the hardware details) then  "Resize"
# Enter the amount of space you would like to increase the disk by (This is additive.  If your disk is currently 32GB and you want it to be 64GB you would need to add 32GB)
# Start VM

# Terminal to VM and login
# Drop to Sudo
sudo su

# Check for Free Space
df -h
# Nothing should have changed at this point

# Resize the Disk
cfdisk
# at the bottom of the list you should see ">> Free space".  This will be equal to the amount you added to your disk above.
Select the partition you would like to resize 
# something like /dev/sda3 **you will need this in the next step**
Select RESIZE and hit ENTER
The new size will use the entire amount of free disk space by default, hit ENTER
Click WRITE, then type 'yes', and press ENTER to commit the change
# you should no longer see the ">> Free space" in the list
Press 'q' or select Quit

# Extend the partition
pvresize {Volume} 
# {Volume} will be device you resized in cfdisk (something like /dev/sda3)

# Check Volume Group for Free Space
vgdisplay

# Get the LV Path
lvdisplay
# Copy the LV Path

# Extend the volume
lvextend -l +100%FREE {LVPath}
# {LVPath} with be something like /dev/ubuntu-vg/ubuntu-lv

# Confirm Changes
lvdisplay

# Get Root File System Path
df -h
# Copy the /dev/mapper full path

# Resize Root File System
resize2fs {RootFSPath}
# {RootFSPath} was copied in the previous setp and should be something like /dev/mapper/ubuntu--vg-ubuntu--lv

# Confirm Changes
df -h

# Optional reboot the newly resized VM
