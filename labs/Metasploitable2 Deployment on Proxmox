Metasploitable2 Deployment on Proxmox (Lab Notes)

Objective
Deploy the vulnerable Metasploitable2 VM inside Proxmox for use in penetration testing and cybersecurity labs.

Environment
Host: Proxmox VE (Hank)
VM ID: 300
Network Bridge: vmbr0
Storage: local-lvm
Source Image: Metasploitable2 (VMDK format)

Initial Problem
Metasploitable2 is distributed as a VMware (.vmdk) image, which is not directly usable in Proxmox without conversion.

Attempts to:
Attach disk directly
Boot VM without proper disk import
Use GUI-only approach

Result:
VM would not boot
Disk not properly recognized
Confusion around IDE/SATA options

Troubleshooting Attempts
Attempt 1: Attach disk via GUI
Tried adding disk as IDE/SATA
Disk appeared but would not boot

Attempt 2: Boot without proper disk import
VM created successfully
No valid boot device detected

Key Issue Identified
Proxmox requires the disk to be imported into storage and properly attached.

Final Working Solution (CLI-Based)
Step 1: Download Metasploitable2
wget https://sourceforge.net/projects/metasploitable/files/Metasploitable2/metasploitable-linux-2.0.0.zip

Step 2: Extract archive
unzip metasploitable-linux-2.0.0.zip

Step 3: Navigate to extracted folder
cd Metasploitable2-Linux/

Step 4: Convert disk format (VMDK → QCOW2)
qemu-img convert -O qcow2 Metasploitable.vmdk metasploitable.qcow2

Step 5: Create VM shell
qm create 300 --memory 2048 --cores 2 --name Metasploitable2 --net0 virtio,bridge=vmbr0 --boot c --bootdisk ide0

Step 6: Import disk into Proxmox storage
qm importdisk 300 metasploitable.qcow2 local-lvm

Step 7: Attach disk to VM
qm set 300 --ide0 local-lvm:vm-300-disk-0

Step 8: Set boot order (Fix)
qm set 300 --boot order=ide0

Key Lessons Learned
Proxmox does not natively use .vmdk; conversion or import is required
Disk must be:
Converted
Imported
Attached
Boot order must be explicitly set
CLI is often more reliable than GUI for advanced tasks when using Proxmox

Security Considerations
Metasploitable2 is intentionally vulnerable:

Future setup:
vmbr1 (internal lab network)
Kali and Metasploitable on same isolated bridge
