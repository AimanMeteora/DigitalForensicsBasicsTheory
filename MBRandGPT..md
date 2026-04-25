## BIOS

* **BIOS** is older and uses **Master Boot Record**
* **Basic Input Output System**
* Only support up to **2 TB** of disks

---

## UEFI

* Newer and Uses **GUID Partition Table**
* **Unified Extensible Firmware Interface**
* Supports up to **9 zetabytes** of disks

---

## Power-On the system

* **power button**, which sends electrical signals to the motherboard and initializes all the components. 
* The **CPU** is the first component to get the electrical signals and needs some instructions to move further. 
* The **CPU** fetches and executes these instructions from a chipset deployed on the motherboard. This chipset is known as **BIOS/UEFI**, and it contains instructions on how to get the boot process going.

--

<img width="1500" height="670" alt="image" src="https://github.com/user-attachments/assets/b7f51daf-b5c1-4c20-bc9a-f66f001b5968" />
To check if your device is using UEFI or BIOS on Windows, win + r and msinfo32
--

## Power-On-Self-Test (POST)
- The system is now powered on, and the CPU has executed instructions from the firmware (BIOS/UEFI) installed. 
- The BIOS/UEFI then starts a Power-On-Self-Test to ensure all the system’s hardware components are working fine.
-  You may hear a single or multiple beeps during this process in your system; 
- this is how the BIOS/UEFI communicates any errors in the hardware components and displays the error message on the screen, e.g., keyboard not found.

--

<img width="1140" height="800" alt="image" src="https://github.com/user-attachments/assets/b7a7cf33-5d80-4bb1-b16d-52fd1df83636" />

--

## Locate the Bootable Device
- After the BIOS/UEFI has performed the POST check, BIOS/UEFI to locates bootable devices, such as SSDs, HDDs, or USBs, with the operating system installed. 
- Once bootable device is located, it starts reading this device. 
- Role of the MBR/GPT: The very first sector of the device would either contain the MBR (Master Boot Record) or the GPT (GUID Partition Table). 
- The MBR/GPT would be taking control of the boot process from here. 
- In the upcoming tasks, we will see how the boot process propagates from here if the MBR partitioning scheme is used and what happens if it is GPT instead.

--

<img width="1140" height="800" alt="image" src="https://github.com/user-attachments/assets/ddff3451-a84d-44f8-8673-c797627305c2" />
The first 3 steps of the boot process that we discussed in this task were the initial steps to find the bootable disk on which the OS resides. From the first task, we imagined this disk as a building where all our data is stored, and there are multiple rooms (partitions) in this building. The next step of the boot process would be to get the map of this building. This map would be either MBR or GPT. 
--

## Bootloader Code (Bytes 0-445)

They cover 446 out of the total 512 bytes of the MBR

--

<img width="2110" height="1596" alt="image" src="https://github.com/user-attachments/assets/f806c0dc-a5b0-43b1-b1ca-54fadbe4692a" />

--

## Partitions Table (Bytes 446-509)

--

<img width="1256" height="70" alt="image" src="https://github.com/user-attachments/assets/7d82f326-f8b8-4d8f-bbfe-f35b9f55f9a6" />

--

<img width="1399" height="542" alt="image" src="https://github.com/user-attachments/assets/81f695ab-67df-4ede-9f9b-722a14ed34e6" />

--

## Boot Indicator (1 byte)
- 80 → Bootable partition
- 00 → Not bootable
- Windows systems usually have C: set to 80

## Starting CHS Address (3 bytes)
- Physical start location (Cylinder, Head, Sector)
- Mostly outdated (replaced by LBA)

## Partition Type (1 byte)
- Identifies filesystem
- Example: 07 → NTFS
- Each filesystem has a unique hex code

## Ending CHS Address (3 bytes)
- Physical end location of partition
- Also mostly outdated

## Starting LBA Address (4 bytes)
- Logical start of partition
- Easier to locate in hex editors
- Used for disk analysis and data carving

## Number of Sectors (4 bytes)
- Total sectors in partition
- Used to calculate partition size

--

## Locating the Partition
The starting LBA address bytes of the partition we are using as a reference in this task is 00 08 00 00.These bytes are stored in the little-endian format in which the Least 
Significant Byte (LSB) is first, and the Most Significant Byte (MSB) is 
last. So, we must first reverse these bytes. Reversing these bytes would make it 00 00 08 00.  The next step is to convert these into decimal format. You can either use an online tool for this or view the decimal converted value of these bytes within the HxD hex editor tool. Select the bytes for which you need the decimal value, and it will be displayed in the Int32 option of the Data Inspector pane on the right side.
00 08 00 00 decimal value = 2048
2048 x 512 = 1,048,576

## Calculating the Size of Partition
The last four partition bytes represent the Number of Sectors field. As per our partition, these bytes are 00 B0 23 03 and if we reverse it as they are in little-endian, they become 03 23 B0 00. The next step is to convert them to decimal. You can find the decimal value within the HxD tool by highlighting the bytes just as we did for the starting LBA address while locating the partition. The decimal value comes out to be 52,670,464. Now, as we know, each sector's size is 512 bytes. We can multiply them to get the size of this partition in bytes.

--

## 

MBR Signature (Bytes 510-511)
These two bytes 55 AA are also known as a Magic Number and they indicate that the MBR has been ended now. If these two bytes are changed to some value other than 55 AA, the system cannot boot. These bytes often get corrupted due to bad sectors on the disk and sometimes can be intentionally changed by malware to interrupt the normal boot process.

--

<img width="600" height="677" alt="image" src="https://github.com/user-attachments/assets/1773c71f-55fd-4c0d-86fc-655f85d92dc3" />

--

<img width="520" height="297" alt="image" src="https://github.com/user-attachments/assets/173e24c3-541e-4792-b02e-4ab00d85707f" />

--

## Threats
1. Bootkits:
Bootkits are a very dangerous type of malware. Since the MBR executes before the OS starts, the bootkits craft themselves into the MBR to bypass the OS's protection mechanisms. Even complete malware removal or reinstallation of the OS cannot remove the bootkits as they reside in the MBR.

2. Ransomware:
MBR is the first thing executed on the disk during the boot process, and the boot process heavily relies on it. Instead of encrypting the individual files of the system, attackers encrypt and modify the MBR to interrupt the boot process and display their ransom notes on the screen. Petya ransomware, detected in 2016, encrypts the MBR rather than all the disk files. Another example is the Bad Rabbit ransomware, which overwrites the original MBR with its malicious bootloader code and displays a ransom note on the screen upon reboot of the system.

3. Wiper Malware:
This type of malware intends to corrupt the MBR to make the system unbootable. Any change in the original MBR of a disk can make the system completely unbootable. The Shamoon malware is one such malware that overwrites the MBR with random characters to make the system unbootable. In the next task, we will examine a similar case in which malware corrupted the MBR of a disk to make the system unbootable.
