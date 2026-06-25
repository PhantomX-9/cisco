# Installing Cisco Modeling Labs (CML) 2.10 on VMware Workstation Pro

This guide walks through deploying the Cisco Modeling Labs (CML) 2.10 OVA
on VMware Workstation Pro on a Windows host. It covers importing the
appliance, meeting the virtualization requirements, attaching the reference
platform (refplat) ISO, and an optional procedure for expanding storage and
adding supplemental node definitions.

---

## Prerequisites

Before you begin, make sure you have downloaded the following from your Cisco
account (the exact build numbers will vary with the release you obtain):

| Item | Example file | Purpose |
| --- | --- | --- |
| CML OVA package | `cml2_p_2.10.0-13_amd64-17.ova` | The CML virtual appliance (~1.32 GB) |
| Reference platform ISO (FCS) | `refplat_p-20260409-fcs-iso` | Core node and image definitions |
| Supplemental ISO *(optional)* | `refplat_p-20260611-supplemental-iso` | Additional node/image definitions |

You will also need:

- **VMware Workstation Pro** installed on a Windows host.
- A host CPU and BIOS that support hardware virtualization (Intel VT-x and EPT).

---

## Host Requirements

CML expects a minimum allocation for the virtual machine.

| Resource | Minimum |
| --- | --- |
| Memory | 8 GB |
| CPU | 4+ physical cores, Intel processor — must support **VT-x** and **EPT** |

---

## Part 1 — Import and Configure the VM

### Step 1 — Import the OVA into VMware Workstation Pro

1. Navigate to the folder where you downloaded the CML OVA file.

![Downloaded CML OVA and reference platform ISOs in File Explorer](images/image-0.png)

2. Double-click the `.ova` file to begin importing it into VMware Workstation Pro.
3. In the **Import Virtual Machine** dialog, provide a name and a local storage
path for the new virtual machine, then click **Import**.

![Import Virtual Machine](images/image-1.png)

### Step 2 — Edit the virtual machine settings

After the import completes, edit the virtual machine settings so they meet the
requirements listed in [Host Requirements](#host-requirements) (at least 8 GB
of memory and 4+ CPU cores).

![Memory](images/image-5.png)
![Processors](images/image-4.png)

### Step 3 — Enable Intel VT-x

In the VM's processor settings, under **Virtualization engine**, enable
**Virtualize Intel VT-x/EPT or AMD-V/RVI**.

![Virtualization engine setting with Virtualize Intel VT-x/EPT enabled](images/image-2.png)

### Step 4 — Disable Hyper-V (common VMware conflict)

Hyper-V and other Windows virtualization-based features can prevent VMware from
using VT-x. Open an **elevated** (Administrator) Command Prompt and run:

```bat
bcdedit /set hypervisorlaunchtype off
```

> **Note:** This change takes effect after a reboot (see Step 6).

### Step 5 — Turn off Memory Integrity in Windows Security

Memory integrity (Core isolation) is another virtualization-based feature that
can conflict with VMware. Go to **Windows Security → Device security → Core
isolation details** and set **Memory integrity** to **Off**.

![Windows Security Core isolation with Memory integrity off](images/image-3.png)

### Step 6 — Reboot and verify BIOS settings

1. **Reboot your PC** to apply the Hyper-V and Memory integrity changes.
2. During startup, enter your system **BIOS/UEFI** and confirm that hardware
   virtualization is enabled. Look for and enable settings such as:
   - **Intel Virtualization Technology (VT-x)**
   - **VT-d / Intel Directed I/O** (if present)
   - Ensure no conflicting OS-level virtualization (e.g., a "secure boot" or
     "device guard" option that re-enables Hyper-V) is forcing virtualization
     back on.
3. Save and exit the BIOS.

> **Why this matters:** CML runs nested virtual nodes inside the VM, so the
> physical CPU must expose VT-x and EPT to the guest. If virtualization is
> disabled in the BIOS, the VM will fail to power on or nodes will not start.

### Step 7 — Attach the reference platform ISO

Once your PC has rebooted:

1. Open VMware Workstation Pro.
2. Edit the CML virtual machine settings.
3. Navigate to the **CD/DVD** device.
4. Under **Device status**, check **Connected** and **Connect at power on**.
5. Select **Use ISO image file**, then **Browse** and select your
   `refplat_*-fcs-iso` disc image.

![CD/DVD settings connected to the FCS reference platform ISO](images/image-6.png)

### Step 8 — Power on and complete setup

Power on the virtual machine and follow the on-screen prompts to complete the
initial CML setup.

---

## Part 2 — (Optional) Expand Storage and Add Supplemental Nodes

**You can avoid this by expanding the disk before you setup your VM**

The default disk may be too small once you import additional node and image
definitions. This section expands the virtual disk and grows the guest file
system, then loads the supplemental reference platform.

### Step 1 — Expand the virtual disk in VMware

1. With the VM powered off, edit the virtual machine settings.
2. Select the hard disk and use **Expand** to increase its capacity.
   A maximum size of **50–100 GB** is recommended.

![VMware Expand Disk Capacity dialog set to 100 GB](images/image-7.png)

> **Important:** Expanding the disk in VMware only adds unallocated space.
> You must still extend the partition and file system inside the CML guest (next steps).

### Step 2 — Open the CML web console (Cockpit)

Browse to the CML web console (the system management/Cockpit interface) and go
to the **Storage** section. The newly added capacity appears as **Free space**
on the `sda` disk.

![Cockpit Storage view showing free space on the sda disk](images/image-8.png)

### Step 3 — Add the unpartitioned space to the volume group

In the Storage view, add the unpartitioned space on `/dev/sda` to the existing
LVM volume group.

![Cockpit Add disks dialog](images/image-9.png)

### Step 4 — Grow the logical volume

Select the `lv_var` logical volume (where CML stores node images and lab data)
and **Grow** it to consume the newly available space.

![Grow logical volume dialog for lv_var](images/image-10.png)

### Step 5 — Attach the supplemental ISO

Back in VMware, edit the VM settings and connect the **CD/DVD** drive to the
`refplat_*-supplemental-iso` image, the same way you attached the FCS ISO
earlier (check **Connected**, then **Use ISO image file → Browse**).

![CD/DVD settings connected to the supplemental reference platform ISO](images/image-11.png)

### Step 6 — Copy the reference platform definitions to disk

In the CML web console, use **Copy Refplat ISO** to copy the node and image
definitions from the attached refplat ISO onto the appliance's disk.

![Copy Refplat ISO control in the CML web console](images/image-12.png)