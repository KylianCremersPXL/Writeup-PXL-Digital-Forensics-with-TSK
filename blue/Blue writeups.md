# Writeup-PXL-Digital-Forensics-with-TSK
## Task 1
Read through the page

No answer needed

## Task 2

### Which TSK command can I use to find the location of a file based on a physical block number?

ookal vinden we deze niet direct terug in de gegeven tekst, als je de vraag opzoekt vind je deze website [link](https://wiki.sleuthkit.org/index.php?title=The_Sleuth_Kit_commands)

hier zie je: ifind - Find the meta-data structure that has allocated a given disk unit or file name.

Answer: ```ifind```

### Which TSK command lists the entries in the file system journal
op dezelfde pagina van vorige website zie je dit staan: jls - List the contents of a file system journal.

Answer: ```jls```

### Which layer does the ils command operate on?
Op dezelfde pagina als de twee eerste oefeningen kan je dit lezen : ils - List inode information.

nu dat we weten wat dit doet kunnen we zoeken naar wat een inode precies is.
op [wikipedia](https://en.wikipedia.org/wiki/Inode) staat dat het een data structure is in een file-system

Answer : ```File System Layer```
## Task 3
Read through the page

No answer needed

## Task 4
### At which byte does able_3.001 start? Only the final four digits are necessary


```
pxl@DESKTOP-55O61BH:/mnt/c/Users/kylia/Documents/unzip/sec adv/able_3$ img_stat able_3.000
IMAGE FILE INFORMATION
--------------------------------------------
Image Type: raw

Size in bytes: 4294967296
Sector size:    512

--------------------------------------------
Split Information:
able_3.000  (0 to 1073741823)
able_3.001  (1073741824 to 2147483647)
able_3.002  (2147483648 to 3221225471)
able_3.003  (3221225472 to 4294967295)
```
De starting byte is 1073741824
Answer: ```1824```

### Not counting Meta partitions, which are partitions used as disk operation meta data, how many partitions does this disk have?

pxl@DESKTOP-55O61BH:/mnt/c/Users/kylia/Documents/unzip/sec adv/able_3$ mmls able_3.000
GUID Partition Table (EFI)
Offset Sector: 0
Units are in 512-byte sectors


```
      Slot      Start        End          Length       Description
000:  Meta      0000000000   0000000000   0000000001   Safety Table
001:  -------   0000000000   0000002047   0000002048   Unallocated
002:  Meta      0000000001   0000000001   0000000001   GPT Header
003:  Meta      0000000002   0000000033   0000000032   Partition Table
004:  000       0000002048   0000104447   0000102400   Linux filesystem
005:  001       0000104448   0000309247   0000204800   Linux filesystem
006:  -------   0000309248   0000571391   0000262144   Unallocated
007:  002       0000571392   0008388574   0007817183   Linux filesystem
008:  -------   0008388575   0008388607   0000000033   Unallocated
```
Zonder de rows met "Meta" mee te tellen hebben we er in totaal 6

Answer: ```6```

### Which file system is used for the /home partition?
vorige oefening toont dat de filesystem start op byte 104448

gebruik het commando: fsstat -o 104448 able_3.000 | less voor een documentje met informatie.
Hierin bovenaan vind je de file system type

Answer: ```Ext4```

### What type of file is the file located at inode 20?

hiervoor gebruiken we icat aangezien dit command een inode accepteert en hier de contents van de file teruggeeft, in de header van een file staat meestal de type
[In deze post](https://stackoverflow.com/questions/29985463/how-to-pipe-the-output-of-icat-to-the-file-command-in-bash) staat er dat je icat met | file - moet gebruiken om geen unreadable junk te hebben.

command: icat -o 104448 able_3.000 20 | file -

output: /dev/stdin: JPEG image data, JFIF standard 1.01, aspect ratio, density 1x1, segment length 16, baseline, precision 8, 360x209, components 3

Answer: ```jpeg```

## Task 5

### What type of file format is used for the forensic image?
maak gebruik van de "file" commando die in linux ingebouwt is

command: file LI_Assignment_3.E01

output: LI_Assignment_3.E01: EWF/Expert Witness/EnCase image file format

Answer: ```EWF/Expert Witness/EnCase```

### What is the start offset sector of the partition in the forensic image?
gebruik mmls om start en end te zien van je partitions

pxl@DESKTOP-55O61BH:/mnt/c/Users/kylia/Documents/unzip/sec adv/LI-Assignment-3$ mmls LI_Assignment_3.E01
DOS Partition Table
Offset Sector: 0
Units are in 512-byte sectors

      Slot      Start        End          Length       Description
000:  Meta      0000000000   0000000000   0000000001   Primary Table (#0)
001:  -------   0000000000   0000002047   0000002048   Unallocated
002:  000:000   0000002048   0001023999   0001021952   NTFS / exFAT (0x07)

hierin zie je dat de allocated partition pas begint op 2048

Answer: ```2048```

### Which file system is used here?
bij de mmls commando van vorige vraag zie je onder description NTFS staan

Answer: ```NTFS```

### What is the volume serial number of the valid file system?
in de commando fsstat vind je general details of the file system, gebruik -o om een offset op je commando te zetten aangezien je partition pas begint op 2048

command: fsstat -o 2048 LI_Assignment_3.E01

output:
```
FILE SYSTEM INFORMATION
--------------------------------------------
File System Type: NTFS
Volume Serial Number: CAE0DFD2E0DFC2BD
OEM Name: NTFS
Volume Name: NTFS_2017d
Version: Windows XP

METADATA INFORMATION
--------------------------------------------
First Cluster of MFT: 42581
First Cluster of MFT Mirror: 2
Size of MFT Entries: 1024 bytes
Size of Index Records: 4096 bytes
Range: 0 - 293
Root Directory: 5

CONTENT INFORMATION
--------------------------------------------
Sector Size: 512
Cluster Size: 4096
Total Cluster Range: 0 - 127742
Total Sector Range: 0 - 1021950
...
```

Answer: ```CAE0DFD2E0DFC2BD```

### What is the “data block” or “cluster” size used by the file system?
in de output van de vorige vraag zie je Cluster Size: 4096

Answer: ```4096```

### What is the MFT entry number of the directory /Users/AlbertE/Documents?
Aangezien we files zelf moeten zoeken gebruiken we fls (fls - List file and directory names in a disk image.)

na een tijdje browsen met behulp van fls komen we op AlbertE
```
pxl@DESKTOP-55O61BH:/mnt/c/Users/kylia/Documents/unzip/sec adv/LI-Assignment-3$ fls -o 2048 LI_Assignment_3.E01 65-144-2
d/d 234-144-2:  Documents
d/d 222-144-2:  Downloads
d/d 249-144-2:  Favorites
d/d 226-144-2:  Internet
r/r 285-128-2:  NTUSER.DAT
d/d 184-144-2:  Pictures
```

hier zie je dat 234 het entry number is

Answer: ```234```

### Which TSK tool can you use as an alternative for fls for the preceding question?
op de [official command list](http://wiki.sleuthkit.org/index.php?title=The_Sleuth_Kit_commands) pagina als we met ctrl + f zoeken naar "file name" hetgene dat fls naar zoekt. dan vind je 1 result, ifind.

Answer: ```ifind```

### How many deleted files are in the directory /Users/AlbertE/Documents?

als we in de [documentatie van fls](https://wiki.sleuthkit.org/index.php?title=Fls) gaan kijken zien we dat files met een sterretje * deleted zijn.

dus als je terug fls intypt maar nu met een -r parameter, dit is zodat je direct ook in subdirectories kijkt. vind je dit 
```
pxl@DESKTOP-55O61BH:/mnt/c/Users/kylia/Documents/unzip/sec adv/LI-Assignment-3$ fls -o 2048 -r LI_Assignment_3.E01 234-144-2
r/r 242-128-2:  better_access_unix.txt
r/r 243-128-2:  books.txt
r/r 244-128-2:  cable.txt
r/r 245-128-2:  cabletv.txt
r/r 246-128-2:  hackcabl.txt
r/r 247-128-2:  signatures.pdf
r/r 240-128-2:  SVstunts.avi
r/r 41-128-1:   Tax Return 2008.pdf
r/r 41-128-3:   Tax Return 2008.pdf:Zone.Identifier
r/r 241-128-2:  ULTIMATEJOURNEYDK.wmv
-/r * 40-128-1: Credit Report.pdf
-/r * 40-128-3: Credit Report.pdf:Zone.Identifier
-/d * 235-144-2:        ManProj
+ -/r * 236-128-2:      MMManhattan Project.docx
+ -/r * 237-128-2:      The Manhattan Project - YouTube.url
+ -/r * 238-128-2:      World's First Atomic Bomb - Manhattan Project Documentary - Films - YouTube.url
+ -/r * 239-128-2:      manhattan_project.zip
-/r * 248-128-2:        cyberbullying_by_proxy.doc
```

7 files met een sterretje

Answer: ```7```

### Using a Sleuth Kit command OTHER than fls, what is the MFT entry number of the parent directory of the file with MFT entry number 220?

istat is een commando dat details van een file laat zien, dit betekent dus ook de Entry en Parent entry.

```
pxl@DESKTOP-55O61BH:/mnt/c/Users/kylia/Documents/unzip/sec adv/LI-Assignment-3$ istat -o 2048 LI_Assignment_3.E01 220
MFT Entry Header Values:
Entry: 220        Sequence: 2
$LogFile Sequence Number: 4199638
Not Allocated File
Links: 1

$STANDARD_INFORMATION Attribute Values:
Flags: Archive
Owner ID: 0
Security ID: 0  ()
Created:        2017-05-01 15:04:42.885836800 (CEST)
File Modified:  2006-10-14 16:41:03.183881000 (CEST)
MFT Modified:   2017-05-01 15:04:42.886148200 (CEST)
Accessed:       2017-05-01 15:04:42.886057000 (CEST)

$FILE_NAME Attribute Values:
Flags: Archive
Name: GemoTailUniversal.jpg
Parent MFT Entry: 186   Sequence: 1
Allocated Size: 8192    Actual Size: 6136
Created:        2017-05-01 15:04:42.885836800 (CEST)
File Modified:  2006-10-14 16:41:03.183881000 (CEST)
MFT Modified:   2017-05-01 15:04:42.886057000 (CEST)
Accessed:       2017-05-01 15:04:42.886057000 (CEST)

Attributes:
Type: $STANDARD_INFORMATION (16-0)   Name: N/A   Resident   size: 48
Type: $FILE_NAME (48-4)   Name: N/A   Resident   size: 108
Type: $SECURITY_DESCRIPTOR (80-1)   Name: N/A   Resident   size: 80
Type: $DATA (128-2)   Name: N/A   Non-Resident   size: 6136  init_size: 6136
116805 116806
```
hierboven zien we dus "Parent MFT Entry: 186"

Answer: ```186```

### What is the name of the parent directory of the file with MFT entry number 220?
gebruik ook hier weer istat voor, dit toont namelijk ook de naam, alleen moeten we nu ons commando op 186 uitvoeren

```
pxl@DESKTOP-55O61BH:/mnt/c/Users/kylia/Documents/unzip/sec adv/LI-Assignment-3$ istat -o 2048 LI_Assignment_3.E01 186
MFT Entry Header Values:
Entry: 186        Sequence: 2
$LogFile Sequence Number: 4199873
Not Allocated Directory
Links: 1

$STANDARD_INFORMATION Attribute Values:
Flags: Archive
Owner ID: 0
Security ID: 0  ()
Created:        2017-05-01 15:04:41.663931200 (CEST)
File Modified:  2017-05-01 19:52:47.313032800 (CEST)
MFT Modified:   2017-05-01 19:52:47.313032800 (CEST)
Accessed:       2017-05-01 19:52:47.313032800 (CEST)

$FILE_NAME Attribute Values:
Flags: Directory, Archive
Name: Tails
Parent MFT Entry: 184   Sequence: 1
Allocated Size: 0       Actual Size: 0
Created:        2017-05-01 15:04:41.663931200 (CEST)
File Modified:  2017-05-01 15:04:41.663931200 (CEST)
MFT Modified:   2017-05-01 15:04:41.663931200 (CEST)
Accessed:       2017-05-01 15:04:41.663931200 (CEST)
```
hier zie je "Name: Tails"

Answer: ```Tails```


### Using a Sleuth Kit command OTHER than fls, list all of the files that are or were located in the directory you identified in the previous question. How many files are listed?
Didnt find any other way then using fls

Answer: ```6```


### What type of file is the file with MFT entry 219?
gebruik icat met de file commando om te zien wat voor file het is

command: icat -o 2048 LI_Assignment_3.E01 219 | file -

output: /dev/stdin: ASCII text

Answer ```ASCII text```

### The first line of the file located at MFT entry 219 contains a domain, which domain?
icat de file naar een txt file met icat -o 2048 LI_Assignment_3.E01 219 > extracted_file.txt

de eerste domain van deze file is aachen.de

Answer: ```aachen.de```

### The datablocks used by the entry 219 are used by which file?
Eerst was ik gaan zoeken of er een commando was om deze datablock te vinden. dit bleek op bruteforcen na niet mogelijk te zijn.
dus als alternatief keek ik dan naar mogelijke files die alle datablocks gebruiken om misschien zo mijn file te vinden. op een blog vond ik dan dat [files zoveel datablocks nemen als hun size nodig heeft](https://www.quora.com/Are-all-blocks-stored-in-one-file-or-is-every-block-stored-in-an-individual-file) dit bracht mij op het idee om alle files te sorteren op size.

command: ils -o 2048 LI_Assignment_3.EO1 | sort -nk

output: 
```
class|host|device|start_time
ils|DESKTOP-55O61BH||1742920824
st_ino|st_alloc|st_uid|st_gid|st_mtime|st_atime|st_ctime|st_crtime|st_mode|st_nlink|st_size
40|f|0|0|1493660855|1493661130|1493661130|1493661130|777|1|39052
168|f|48|0|1511741301|1511741196|1511741301|1511741136|777|0|48
169|f|48|0|1511741184|1511741184|1511741184|1511741184|777|0|80003222
170|f|48|0|1511741185|1511741184|1511741185|1511741184|777|0|80336815
171|f|48|0|1511741185|1511741185|1511741185|1511741185|777|0|81808271
172|f|48|0|1511741185|1511741185|1511741185|1511741185|777|0|81171312
173|f|48|0|1511741186|1511741185|1511741186|1511741185|777|0|49736290
186|f|48|0|1493661167|1493661167|1493661167|1493643881|777|1|48
216|f|48|0|1160836901|1493643882|1493643882|1493643882|777|1|59861
217|f|48|0|1160836881|1493643882|1493643882|1493643882|777|1|6044
218|f|48|0|1160836955|1493643882|1493643882|1493643882|777|1|7650
219|f|48|0|1160836849|1493643882|1493643882|1493643882|777|1|24662
220|f|48|0|1160836863|1493643882|1493643882|1493643882|777|1|6136
221|f|48|0|1160837087|1493643882|1493643882|1493643882|777|1|29184
235|f|48|0|1493661180|1493661180|1493661180|1493644136|777|1|48
236|f|48|0|1493580607|1493644136|1493644136|1493644136|777|1|15812
237|f|48|0|1493581023|1493644136|1493644136|1493644136|777|1|69
238|f|48|0|1493581006|1493644136|1493644136|1493644136|777|1|69
239|f|48|0|1493580668|1493644136|1493644136|1493644136|777|1|4099615
248|f|48|0|1161814459|1493644205|1493644205|1493644205|777|1|32256
```
dit ziet er raar uit maar 2 kolommen interresseren mij. de eerste st_ino (inode) en de laatste st_size (grootte)
uit al deze rows is ```171|f|48|0|1511741185|1511741185|1511741185|1511741185|777|0|81808271``` de grootste.
een istat van 171 toont veel informatie over de file, hier zien we dan ook direct dat het heel veel datablocks gebruikt

```
pxl@DESKTOP-55O61BH:/mnt/c/Users/kylia/Documents/unzip/sec adv/LI-Assignment-3$ istat -o 2048 LI_Assignment_3.E01 171
MFT Entry Header Values:
Entry: 171        Sequence: 3
$LogFile Sequence Number: 0
Not Allocated File
Links: 0

$STANDARD_INFORMATION Attribute Values:
Flags: Archive
Owner ID: 0
Security ID: 0  ()
Created:        2017-11-27 01:06:25.146572300 (CET)
File Modified:  2017-11-27 01:06:25.489578600 (CET)
MFT Modified:   2017-11-27 01:06:25.489578600 (CET)
Accessed:       2017-11-27 01:06:25.146572300 (CET)

$FILE_NAME Attribute Values:
Flags: Archive
Name: access_log_c.log
Parent MFT Entry: 168   Sequence: 2
Allocated Size: 81809408        Actual Size: 0
Created:        2017-11-27 01:06:25.146572300 (CET)
File Modified:  2017-11-27 01:06:25.146572300 (CET)
MFT Modified:   2017-11-27 01:06:25.146572300 (CET)
Accessed:       2017-11-27 01:06:25.146572300 (CET)

Attributes:
Type: $STANDARD_INFORMATION (16-0)   Name: N/A   Resident   size: 48
Type: $FILE_NAME (48-3)   Name: N/A   Resident   size: 98
Type: $SECURITY_DESCRIPTOR (80-1)   Name: N/A   Resident   size: 80
Type: $DATA (128-2)   Name: N/A   Non-Resident   size: 81808271  init_size: 81808271
```

de naam van deze file is access_log_c.log

Answer: ```acces_log_c.log```

### What is the last modified date (CET) of the file that reused the datablocks used by the entry 219? 
Format: YYYY-MM-DD HH:MM:SS 

aangezien we de istat van onze grote file hebben kunnen we gewoon kijken naar file modified

Answer: ```2017-11-27 01:06:25```

## Task 6
Andere room

## Task 7

Extra

Skipped
