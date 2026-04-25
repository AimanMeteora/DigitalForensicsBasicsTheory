## Windows Registry Key
The Windows Registry is a collection of databases that contains the 
system's configuration data. This configuration data can be about the 
hardware, the software, or the user's information. It also includes data
 about the recently used files, programs used, or devices connected to 
the system. As you can understand, this data is beneficial from a 
forensics standpoint.

The Windows registry consists of Keys and Values. When you open the 
regedit.exe utility to view the registry, the folders you see are 
Registry Keys. Registry Values are the data stored in these Registry 
Keys. A Registry Hive  is a group of Keys, subkeys, and values stored in a single file on the disk.

---

## Structure of the Registry:  
The registry on any Windows system contains the following five root keys:
1. HKEY_CURRENT_USER
2. HKEY_USERS
3. HKEY_LOCAL_MACHINE
4. HKEY_CLASSES_ROOT
5. HKEY_CURRENT_CONFIG

You can view these keys when you open the regedit.exe utility. To open the registry editor, press the Windows key and the R key simultaneously. It will open a run
prompt that looks like this:
<img width="1236" height="804" alt="image" src="https://github.com/user-attachments/assets/34744382-4eff-4efc-8397-faa3abd0c2c2" />

---

If you are accessing a live system, you will be able to access the registry using regedit.exe, and you will be greeted with all of the 
standard root keys we learned about in the previous task. However, if you only have access to a disk image, you must know where the registry 
hives are located on the disk. The majority of these hives are located in the C:\Windows\System32\Config directory and are:
1. DEFAULT (mounted on HKEY_USERS\DEFAULT)
2. SAM (mounted on HKEY_LOCAL_MACHINE\SAM)
3. SECURITY (mounted on HKEY_LOCAL_MACHINE\Security)
4. SOFTWARE (mounted on HKEY_LOCAL_MACHINE\Software)
5. SYSTEM (mounted on HKEY_LOCAL_MACHINE\System)

---

## Hives containing user information:
Apart from these hives, two other hives containing user information 
can be found in the User profile directory. For Windows 7 and above, a 
user’s profile directory is located in C:\Users\<username>\ where the hives are:
1. NTUSER.DAT (mounted on HKEY_CURRENT_USER when a user logs in)
2. USRCLASS.DAT (mounted on HKEY_CURRENT_USER\Software\CLASSES)

The USRCLASS.DAT hive is located in the directory C:\Users\<username>\AppData\Local\Microsoft\Windows. 
The NTUSER.DAT hive is located in the directory C:\Users\<username>\.
Remember that NTUSER.DAT and USRCLASS.DAT are hidden files.
Apart from these files, there is another very important hive called the AmCache hive. This hive is located in C:\Windows\AppCompat\Programs\Amcache.hve. Windows creates this hive to save information on programs that were recently run on the system.

---

Transaction Logs and Backups:
Some other very vital sources of forensic data are the registry transaction logs and backups. The transaction logs can be considered as the journal of the changelog of the registry hive. Windows often uses transaction logs when writing data to registry hives. This means that 
the transaction logs can often have the latest changes in the registry that haven't made their way to the registry hives themselves. The transaction log for each hive is stored as a .LOG file in the same directory as the hive itself. It has the same name as the registry hive, but the extension is .LOG. For example, the transaction log for the SAMhive will be located in C:\Windows\System32\Config in the filename SAM.LOG. Sometimes there can be multiple transaction logs as well. In that case, they will have .LOG1, .LOG2 etc., as their extension. It is prudent to look at the transaction logs as well when performing registry forensics.

Registry backups are the opposite of Transaction logs. These are the backups of the registry hives located in the C:\Windows\System32\Config directory. These hives are copied to the C:\Windows\System32\Config\RegBack
 directory every ten days. It might be an excellent place to look if you
 suspect that some registry keys might have been deleted/modified 
recently.

---

## Registry Viewer:  
As we can see in the screenshot below, AccessData's Registry Viewer  has
 a similar user interface to the Windows Registry Editor. There are a 
couple of limitations, though. It only loads one hive at a time, and it 
can't take the transaction logs into account.

---

## Zimmerman's Registry Explorer:
Eric Zimmerman has developed a handful of tools that are very useful for performing Digital Forensics and Incident Response. One of them is the Registry Explorer. It looks like the below screenshot. It can load multiple hives simultaneously and add data from transaction logs into the hive to make a more 'cleaner' hive with more up-to-date data. It also has a handy 'Bookmarks' option containing forensically important registry keys often sought by forensics investigators. Investigators can go straight to the interesting registry keys and values with the bookmarks menu item. We will explore these in more detail in the upcoming tasks.

---

## RegRipper:  
RegRipper 
 is a utility that takes a registry hive as input and outputs a report 
that extracts data from some of the forensically important keys and 
values in that hive. The output report is in a text file and shows all 
the results in sequential order. 
RegRipper is available in both a CLI and GUI form which is shown in the screenshot below.

One shortcoming of RegRipper is that it does not take the transaction
 logs into account. We must use Registry Explorer to merge transaction 
logs with the respective registry hives before sending the output to 
RegRipper for a more accurate result.
Even though we have discussed these different tools, for the purpose 
of this room, we will only be using Registry Explorer and some of Eric 
Zimmerman's tools. The other tools mentioned here will be covered in 
separate rooms.

---

## OS Version:
If we only have triage data to perform forensics, we can determine the OS version from which this data was pulled through the registry. To find the OS version, we can use the following registry key:
SOFTWARE\Microsoft\Windows NT\CurrentVersion

## Current control set:
The hives containing the machine’s configuration data used for 
controlling system startup are called Control Sets. Commonly, we will 
see two Control Sets, ControlSet001 and ControlSet002, in the SYSTEM 
hive on a machine. In most cases (but not always), ControlSet001 will 
point to the Control Set that the machine booted with, and ControlSet002
 will be the last known good  configuration. Their locations will be:
SYSTEM\ControlSet001
SYSTEM\ControlSet002

Windows creates a volatile Control Set when the machine is live, called the CurrentControlSet ( HKLM\SYSTEM\CurrentControlSet). For getting the most accurate system information, this is 
the hive that we will refer to. We can find out which Control Set is 
being used as the CurrentControlSet by looking at the following registry
 value:
SYSTEM\Select\Current

## Computer Name:  
It is crucial to establish the Computer Name while performing 
forensic analysis to ensure that we are working on the machine we are 
supposed to work on. We can find the Computer Name from the following 
location:
SYSTEM\CurrentControlSet\Control\ComputerName\ComputerName


## Time Zone Information:  
For accuracy, it is important to establish what time zone the 
computer is located in. This will help us understand the chronology of 
the events as they happened. For finding the Time Zone Information, we 
can look at the following location:
SYSTEM\CurrentControlSet\Control\TimeZoneInformation

## Network Interfaces and Past Networks:  
The following registry key will give a list of network interfaces on the machine we are investigating:
SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\Interfaces
Each Interface is represented with a unique identifier (GUID) subkey, which contains values relating to the interface’s TCP/IP configuration. This key will provide us with information like IP addresses, DHCP IP address and Subnet Mask, DNS Servers, and more. This information is significant because it helps you make sure that you are performing forensics on the machine that you are supposed to perform it on.
The past networks a given machine was connected to can be found in the following locations:
SOFTWARE\Microsoft\Windows NT\CurrentVersion\NetworkList\Signatures\Unmanaged
SOFTWARE\Microsoft\Windows NT\CurrentVersion\NetworkList\Signatures\Managed
These registry keys contain past networks as well as the last time they were connected. The last write time of the registry key points to the last time these networks were connected.

## Autostart Programs (Autoruns):  
The following registry keys include information about programs or commands that run when a user logs on. 
NTUSER.DAT\Software\Microsoft\Windows\CurrentVersion\Run
NTUSER.DAT\Software\Microsoft\Windows\CurrentVersion\RunOnce
SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnce
SOFTWARE\Microsoft\Windows\CurrentVersion\policies\Explorer\Run
SOFTWARE\Microsoft\Windows\CurrentVersion\Run

The following registry key contains information about services:
SYSTEM\CurrentControlSet\Services


## SAM hive and user information:  
The SAM hive contains user account information, login information, 
and group information. This information is mainly located in the 
following location:
SAM\Domains\Account\Users

The information contained here includes the relative identifier (RID) of the user, number of times the user logged in, last login time, last failed login, last password change, password expiry, password policy and password hint, and any groups that the user is a part of. 

---

## Recent Files:

Windows maintains a list of recently opened files for each user. As 
we might have seen when using Windows Explorer, it shows us a list of 
recently used files.  This information is stored in the NTUSER hive and 
can be found on the following location:
NTUSER.DAT\Software\Microsoft\Windows\CurrentVersion\Explorer\RecentDocs
Registry Explorer allows us to sort data contained in registry keys 
quickly. For example, the Recent documents tab arranges the Most 
Recently Used (MRU) file at the top of the list. Registry Explorer also 
arranges them so that the Most Recently Used (MRU) file is shown at the 
top of the list and the older ones later.
Another interesting piece of information in this registry key is that there are different keys with file extensions, such as  .pdf, .jpg, .docx etc. These keys provide us with information about the last 
used files of a specific file extension. So if we are looking 
specifically for the last used PDF files, we can look at the following 
registry key:
NTUSER.DAT\Software\Microsoft\Windows\CurrentVersion\Explorer\RecentDocs\.pdf

Similar to the Recent Docs maintained by Windows Explorer, Microsoft 
Office also maintains a list of recently opened documents. This list is 
also located in the NTUSER hive. It can be found in the following 
location:
NTUSER.DAT\Software\Microsoft\Office\VERSION

The version number for each Microsoft Office release is different. An example registry key will look like this:
NTUSER.DAT\Software\Microsoft\Office\15.0\Word
Here, the 15.0 refers to Office 2013. A list of different Office releases and their version numbers can be found on this link .

Starting from Office 365, Microsoft now ties the location to the user's live ID . In such a scenario, the recent files can be found at the following location. 
NTUSER.DAT\Software\Microsoft\Office\VERSION\UserMRU\LiveID_####\FileMRU

When any user opens a folder, it opens in a specific layout. Users 
can change this layout according to their preferences. These layouts can
 be different for different folders. This information about the Windows 'shell'  is
 stored and can identify the Most Recently Used files and folders. Since
 this setting is different for each user, it is located in the user 
hives. We can find this information on the following locations:
USRCLASS.DAT\Local Settings\Software\Microsoft\Windows\Shell\Bags
USRCLASS.DAT\Local Settings\Software\Microsoft\Windows\Shell\BagMRU
NTUSER.DAT\Software\Microsoft\Windows\Shell\BagMRU
NTUSER.DAT\Software\Microsoft\Windows\Shell\Bags

Registry Explorer doesn't give us much information about ShellBags. 
However, another tool from Eric Zimmerman's tools called the ShellBag 
Explorer shows us the information in an easy-to-use format. We just have
 to point to the hive file we have extracted, and it parses the data and
 shows us the results.


Open/Save and LastVisited Dialog MRUs:

When we open or save a file, a dialog box appears asking us where to 
save or open that file from. It might be noticed that once we open/save a
 file at a specific location, Windows remembers that location. This 
implies that we can find out recently used files if we get our hands on 
this information. We can do so by examining the following registry keys
NTUSER.DAT\Software\Microsoft\Windows\CurrentVersion\Explorer\ComDlg32\OpenSavePIDlMRU
NTUSER.DAT\Software\Microsoft\Windows\CurrentVersion\Explorer\ComDlg32\LastVisitedPidlMRU

---

## UserAssist   : 
Windows keeps track of applications launched by the user using 
Windows Explorer for statistical purposes in the User Assist registry 
keys. These keys contain information about the programs launched, the 
time of their launch, and the number of times they were executed. 
However, programs that were run using the command line can't be found in
 the User Assist keys. The User Assist key is present in the NTUSER 
hive, mapped to each user's GUID. We can find it at the following 
location:
NTUSER.DAT\Software\Microsoft\Windows\Currentversion\Explorer\UserAssist\{GUID}\Count

## ShimCache:  
ShimCache is a mechanism used to keep track of application 
compatibility with the OS and tracks all applications launched on the 
machine. Its main purpose in Windows is to ensure backward compatibility
 of applications. It is also called Application Compatibility Cache 
(AppCompatCache). It is located in the following location in the SYSTEM 
hive:
SYSTEM\CurrentControlSet\Control\Session Manager\AppCompatCache

ShimCache stores file name, file size, and last modified time of the executables.
Our goto tool, the Registry Explorer, doesn't parse ShimCache data in
 a human-readable format, so we go to another tool called AppCompatCache
 Parser, also a part of Eric Zimmerman's tools. It takes the SYSTEM hive
 as input, parses the data, and outputs a CSV file that looks like this:
 
 <img width="1919" height="245" alt="image" src="https://github.com/user-attachments/assets/f7cbe28f-da81-4de5-8a23-cd954a6cd8df" />

 We can use the following command to run the AppCompatCache Parser Utility:
AppCompatCacheParser.exe --csv <path to save output> -f 
<path to SYSTEM hive for data parsing> -c <control set to 
parse>
The output can be viewed using EZviewer, another one of Eric Zimmerman's tools.

## AmCache:  
The AmCache hive is an artifact related to ShimCache. This performs a
 similar function to ShimCache, and stores additional data related to 
program executions. This data includes execution path, installation, 
execution and deletion times, and SHA1 hashes of the executed programs. 
This hive is located in the file system at:
C:\Windows\appcompat\Programs\Amcache.hve
Information about the last executed programs can be found at the following location in the hive:
Amcache.hve\Root\File\{Volume GUID}\

##  BAM/DAM:  
Background Activity Monitor or BAM keeps a tab on the activity of 
background applications. Similar Desktop Activity Moderator or DAM is a 
part of Microsoft Windows that optimizes the power consumption of the 
device. Both of these are a part of the Modern Standby system in 
Microsoft Windows.
In the Windows registry, the following locations contain information 
related to BAM and DAM. This location contains information about last 
run programs, their full paths, and last execution time.
SYSTEM\CurrentControlSet\Services\bam\UserSettings\{SID}
SYSTEM\CurrentControlSet\Services\dam\UserSettings\{SID}

---

When performing forensics on a machine, often the need arises to 
identify if any USB or removable drives were attached to the machine. If
 so, any information related to those devices is important for a 
forensic investigator. In this task, we will go through the different 
ways to find information on connected devices and the drives on a system
 using the registry.

## Device identification: 
The following locations keep track of USB keys plugged into a system.
 These locations store the vendor id, product id, and version of the USB
 device plugged in and can be used to identify unique devices. These 
locations also store the time the devices were plugged into the system.
SYSTEM\CurrentControlSet\Enum\USBSTOR
SYSTEM\CurrentControlSet\Enum\USB

First/Last Times: 
Similarly, the following registry key tracks the first time the 
device was connected, the last time it was connected and the last time 
the device was removed from the system.
SYSTEM\CurrentControlSet\Enum\USBSTOR\Ven_Prod_Version\USBSerial#\Properties\{83da6326-97a6-4088-9453-a19231573b29}\####

In this key, the #### sign can be replaced by the following digits to get the required information:
Value 
Information 
0064
First Connection time
0066
Last Connection time
0067
Last removal time
Although we can check this value manually, as we have seen above, 
Registry Explorer already parses this data and shows us if we select the
 USBSTOR key.
 USB device Volume Name:  
The device name of the connected drive can be found at the following location:
SOFTWARE\Microsoft\Windows Portable Devices\Devices
