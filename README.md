# FreeFileSync
A Copy of FreeFileSync Source Code (https://www.freefilesync.org/)

Synchronize Files and Folders
Key featuresFreeFileSync is a folder comparison and synchronization software that creates and manages backup copies of all your important files. Instead of copying every file every time, FreeFileSync determines the differences between a source and a target folder and transfers only the minimum amount of data needed. FreeFileSync is Open Source software, available for Windows, macOS, and Linux.

FreeFileSync
#Quick Start
— Folder Comparison and Synchronization —

Basic usage:
Choose left and right folders.
Choose left and right directories
 

Compare them.
Start comparison
 
Select synchronization settings.
Select synchronization settings
 
Press Synchronize to begin synchronization.
Press Synchronize to begin synchronization
 
Note
For more detailed explanations on how to set up the most common synchronization scenarios, have a look at the FreeFileSync video tutorials.

Main Dialog Overview
FreeFileSync main window
Start comparison
Change comparison settings
Include/exclude specific files
Change synchronization settings
Start synchronization
Add folder pairs
Select left and right folders
Save/load configuration
Tree overview panel
Synchronization preview
Select categories to show on grid
Synchronization statistics
Command Line Usage
FreeFileSync supports additional synchronization scenarios via a command line interface. To get a syntax overview, open the console, go to the directory where FreeFileSync is installed and type:
FreeFileSync.exe -h or FreeFileSync.exe --help

Command line syntax

1. Run a FreeFileSync batch job
In order to start synchronization in batch mode, supply the path of a ffs_batch configuration file as the first argument for FreeFileSync.exe:
FreeFileSync.exe "D:\Backup Projects.ffs_batch"

After synchronization one of the following status codes is returned:
Return Codes
0 - Synchronization completed successfully
1 - Synchronization completed with warnings
2 - Synchronization completed with errors
3 - Synchronization was aborted

You can evaluate these codes from a script (e.g. a cmd or bat file on Windows) and check if synchronization completed successfully:
"C:\Program Files\FreeFileSync\FreeFileSync.exe" "D:\Backup Projects.ffs_batch"
if errorlevel 1 (
  ::if return code is 1 or greater, something went wrong, add special treatment here
  echo Errors occurred during synchronization...
  pause
)

Instead of showing an error message, you can also send an email notification (using a third party tool).

Attention
If you are running the batch job unattended, make sure your script is not blocked showing a notification dialog. Consider the following options when setting up the FreeFileSync batch job:
 
Set When finished to Exit to skip the summary dialog after synchronization.
Set up error handling to Ignore errors or Cancel to stop the synchronization at the first error.

2. Start a FreeFileSync GUI configuration
If you pass a ffs_gui file, FreeFileSync will start in GUI mode and immediately start comparison (but only if all directories exist):
FreeFileSync.exe "D:\Manual Backup.ffs_gui"

3. Customize an existing configuration
You can replace the directories of a given ffs_gui or ffs_batch configuration file by using the -DirPair parameter:
FreeFileSync.exe "D:\Manual Backup.ffs_gui" -dirpair C:\NewSource D:\NewTarget

4. Merge multiple configurations
When more than one configuration file is provided, FreeFileSync will merge everything into a single configuration with multiple folder pairs and start in GUI mode:
FreeFileSync.exe "D:\Manual Backup.ffs_gui" "D:\Backup Projects.ffs_batch"

5. Use a different GlobalSettings.xml file
By default, FreeFileSync uses a single GlobalSettings.xml file containing options that apply to all synchronization tasks; for examples see Expert Settings. If you want FreeFileSync to use a different settings file instead, you can specify the path via command line:
FreeFileSync.exe "D:\My GlobalSettings.xml"
Comparison Settings
Comparison settings dialog

Comparison variants
When comparing two folders, FreeFileSync analyses the paths relative to the left and right base folders of the contained files. If the relative path matches, FreeFileSync decides how the file pair is categorized by considering the selected comparison variant:

1. Compare by File time and size
This variant considers two files equal when both modification time and file size match. It should be selected when synchronizing files with a backup location. Whenever a file is changed, its file modification time is also updated. Therefore, a comparison by File Time and size will detect all files that should be synchronized. The following categories are distinguished:
 
file exists on one side only
left only
right only
file exists on both sides
different date
left newer
right newer
same date
equal
conflict (same date, different size)

2. Compare by File content
Two files are marked as equal if they have identical content. This variant should be selected when doing consistency checks to see if the files on both sides are bit-wise identical. Naturally, it is the slowest of all comparison variants, so its usefulness for the purpose of synchronization is limited. If used for synchronization, it can serve as a fallback when modification times are not reliable. For example certain mobile phones and legacy FTP servers do not preserve modification times, so the only way to detect different files when the file sizes are the same is by reading their content.
 
file exists on one side only
left only
right only
file exists on both sides
equal
different content

3. Compare by File size
Two files are considered equal if they have the same file size. Since it's possible for files that have the same size to have different content, this variant should only be used when file modification times are not available or reliable, e.g. in certain MTP and FTP synchronization scenarios, and where a comparison by content would be too slow.
 
file exists on one side only
left only
right only
file exists on both sides
equal
different size

Symbolic Link Handling
FreeFileSync lets you choose to include symbolic links (also called symlinks or soft links) when scanning directories rather than skipping over them. When included, you can select between two ways to handle them:
 
Follow: Treat symbolic links like the object they are pointing to. Links pointing to directories are traversed like ordinary directories and the target of each link is copied during synchronization.
 
Direct: Evaluate the symbolic link object directly. Symbolic links will be shown as separate entities. Links pointing to directories are not traversed and the link object itself is copied during synchronization.

Note
Under Windows the symbolic link options apply to symbolic links, volume mount points and NTFS junction points.
Copying symbolic links requires FreeFileSync to be started with administrator rights.
Daylight Saving Time (Windows)
A common problem synchronization software has to handle is +-1 hour file time shifts after a Daylight Saving Time (DST) switch has occurred. This can be observed, for example, when a FAT32- or exFAT-formatted volume (in the following called "FAT") is compared against an NTFS volume, like when synchronizing a USB memory stick against a local disk. Files that previously appeared to be in sync are now shown with a one hour modification time offset, although they have not been modified by the user or the operating system.

The reason for this behavior lies in the way NTFS and FAT store file times: NTFS stores time in UTC format, while FAT uses local time.

When times of these two different formats are compared, one format has to be converted into the other first. In either way, Windows uses the current DST status as well as the current time zone for its calculations. Consequently, the result of this comparison is dependent from current system settings with the effect that file times that used to be the same show up as different after a DST switch or when the time zone has changed.

For a detailed discussion about this issue see:
https://www.codeproject.com/Articles/1144/Beating-the-Daylight-Savings-Time-bug

Solutions:
In FreeFileSync's comparison settings you can enter one or more time shifts to ignore during comparison: If you need to handle differences due to daylight saving time, enter a single one hour shift. If the differences are caused by changing the time zone, enter one or more time shifts as needed.
Ignore daylight saving time shift

Note
File times have to be equal or differ by exactly the time shift entered to be considered the same. Therefore, the time shift setting should not be confused with a time interval or tolerance.

Alternatively, you can avoid the problem in the first place by only synchronizing from FAT to FAT or NTFS to NTFS file systems. Since most local disks are formatted with NTFS and USB memory sticks with FAT, this situation could be handled by formatting the USB stick with NTFS as well.
Exclude Items via Filter
File exclude filter

Files and directories are only considered for synchronization if they pass all filter rules. They have to match at least one entry in the include list and none of the entries in the exclude list as presented in the filter configuration dialog:
Each list item must be a file or directory path relative to synchronization base directories.
Multiple items must be separated by | or a new line.
Wild cards * and ? may be used: * means zero or more characters while ? represents exactly one character.

Example: Exclude items from folder pair C:\Source, D:\Target
Filter description	Filter phrase
Single file C:\Source\file.txt	\file.txt
Single folder C:\Source\SubFolder	\SubFolder\
All files (and folders) named thumbs.db	*\thumbs.db
All *.tmp files located in SubFolder	\SubFolder\*.tmp
Files and folders containing temp somewhere in their path	*temp*
Multiple entries separated by vertical bar	*.tmp | *.doc | *.bak
All subdirectories of the base directories	*\
*.txt files located in subdirectories of base directories	\*\*.txt

Example: Exclude a sub folder except for certain files
Set up two folder pairs with the same source and target paths but with distinct local filters:
Folder pair 1; local exclude filter: \SubFolder\
Folder pair 2; local include filter: \SubFolder\*.txt

Example: Exclude empty folders
Filter with file size zeroSet up a file size filter with a lower limit of 0 bytes. Both the time span and file size filters match files only, so this will exclude all folders. During synchronization however some excluded folders will still be synchronized if they contain at least one non-excluded item, i.e. when they are not empty.

Note
For simple exclusions, just right-click and exclude one item or a list of items directly via the context menu on main dialog.
A filter phrase is compared against both file and directory paths. If you want to consider directories only, you can give a hint by appending a path separator.
Both slash (/) and backslash (\) can be used as the path separator character.
Expert Settings
FreeFileSync has a number of special-purpose settings that can only be accessed by manually opening the global configuration file GlobalSettings.xml. Note that this file is read once when FreeFileSync starts and saved again on exit. Therefore, you should apply manual changes only while FreeFileSync is not running. For the portable FreeFileSync variant the file is found in the installation folder, for local installations go to:

Windows:	%AppData%\FreeFileSync
Linux:	~/.config/FreeFileSync
macOS:	~/Library/Application Support/FreeFileSync

<?xml version="1.0" encoding="UTF-8"?>
<FreeFileSync XmlType="GLOBAL">
    <General>
        <FileTimeTolerance Seconds="2"/>
        <RunWithBackgroundPriority Enabled="false"/>
        <LockDirectoriesDuringSync Enabled="true"/>
        <VerifyCopiedFiles Enabled="false"/>
Contents of GlobalSettings.xml

FileTimeTolerance:
By default file modification times are allowed to have a 2 second difference while still being considered equal. This is required by FAT/FAT32 file systems which store file times only with a 2-second precision.

RunWithBackgroundPriority:
While synchronization is running, other applications that are accessing the same data locations may experience a noticeable slowdown. Enable this setting to lower FreeFileSync's resource consumption at the cost of a significantly slower synchronization speed.

LockDirectoriesDuringSync:
In order to prevent multiple synchronization tasks from reading and writing the same files, FreeFileSync instances are serialized with lock files (sync.ffs_lock). The lock files are only recognized by FreeFileSync and make sure that at most, a single synchronization is running against a certain folder at a time while other instances are queued to wait. This ensures that only consistent sets of files are subject to synchronization. The primary use case are network synchronization scenarios where multiple users run FreeFileSync concurrently against a shared network folder.

VerifyCopiedFiles:
If active, FreeFileSync will binary-compare source and target files after copying and report verification errors. Note that this may double file copy times and is no guarantee that data has not already been corrupted prior to copying. Additionally, corruption may be hidden by deceptively reading valid data from various buffers in the application and hardware stack:
Does the CopyFile function verify that the data reached its final destination successfully?

External Applications
When you double-click on one of the rows on the main dialog, FreeFileSync opens the operating system's file browser by default. On Windows, it calls explorer /select, "%local_path%", on Linux xdg-open "%folder_path%" and on macOS open -R "%local_path%". To customize this behavior or integrate other external applications into FreeFileSync, navigate to Menu → Tools → Options → Customize context menu and add or replace a command.

The first entry will be executed when double-clicking a row on the main grid or when pressing ENTER. All other entries can be accessed quickly by pressing the associated numeric keys or via the context menu that is shown after a right mouse click.

In addition to regular Macros, the following special macros are available:
Macro	Description
%item_path%
Full file or folder path
%folder_path%
Parent folder path
%local_path%
Creates a temporary local copy for files located on SFTP and MTP storage. Identical to %item_path% for files on local disks and network shares.

Note: To refer to the item on the opposite side, append "2" to the macro name: e.g. %item_path2%, %folder_path2%, %local_path2%.

Examples:
Start file content comparison tool (WinMerge):
"C:\Program Files (x86)\WinMerge\WinMergeU.exe" "%local_path%" "%local_path2%"

opendiff on macOS (requires Xcode):
opendiff "%local_path%" "%local_path2%"

Show file in Windows Explorer:
explorer /select, "%local_path%"

Open file with associated application:
"%local_path%"

Open Command Prompt for selected item:
cmd /k cd /D "%folder_path%"

Write list of selected file paths to a text file:
cmd /c echo %item_path% >> %csidl_Desktop%\file_list.txt

Preview files using Quick Look on macOS:
qlmanage -p "%local_path%"

Note
Macros need to be protected with quotation marks if they can resolve to file paths containing whitespace characters.
Macros
All directory paths may contain macros that are expanded during synchronization. The beginnings and ends of each macro are marked by a % character. In addition to special macros handling time and date, the operating system's environment variables may also be used.

Internal macros
Macro	Example	Format
%date%	2016-12-31	[YYYY-MM-DD]
%time%	112233	[hhmmss]
%timestamp%	2016-12-31 112233	[YYYY-MM-DD hhmmss]
 
%year%	2016
%month%	12	[01–12]
%day%	31	[01–31]
 
%hour%	11	[00–23]
%min%	22	[00–59]
%sec%	33	[00–59]
 
%weekday%	Monday	day of the week
%week%	52     	[01–52] calendar week

Environment variables (Windows)
Macro	Example
%AllUsersProfile%	C:\ProgramData
%AppData%	C:\Users\Zenju\AppData\Roaming
%ComputerName%	Zenju-PC
%LocalAppData%	C:\Users\Zenju\AppData\Local
%ProgramData%	C:\ProgramData
%ProgramFiles%	C:\Program Files
%ProgramFiles(x86)%	C:\Program Files (x86)
%Public%	C:\Users\Public
%Temp%	C:\Windows\Temp
%UserName%	Zenju
%UserProfile%	C:\Users\Zenju
%WinDir%	C:\Windows

Special folder locations (Windows)
Macro	Example
%csidl_Desktop%	C:\Users\Zenju\Desktop
%csidl_Documents%	C:\Users\Zenju\Documents
%csidl_Pictures%	C:\Users\Zenju\Pictures
%csidl_Music%	C:\Users\Zenju\Music
%csidl_Videos%	C:\Users\Zenju\Videos
%csidl_Downloads%	C:\Users\Zenju\Downloads
%csidl_Favorites%	C:\Users\Zenju\Favorites
%csidl_Resources%	C:\Windows\Resources
%csidl_Quicklaunch%	C:\Users\Zenju\AppData\Roaming\Microsoft\Internet Explorer\Quick Launch
%csidl_StartMenu%	C:\Users\Zenju\AppData\Roaming\Microsoft\Windows\Start Menu
%csidl_Programs%	C:\Users\Zenju\AppData\Roaming\Microsoft\Windows\Start Menu\Programs
%csidl_Startup%	C:\Users\Zenju\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\StartUp
%csidl_Nethood%	C:\Users\Zenju\AppData\Roaming\Microsoft\Windows\Network Shortcuts
%csidl_Templates%	C:\Users\Zenju\AppData\Roaming\Microsoft\Windows\Templates
 
Note: Most of the above macros also have a variant for public folders, e.g. %csidl_Documents% has %csidl_PublicDocuments%.


Hint: You can add flexibility to an ffs_batch configuration file by creating new temporary environment variables in a bat or cmd file that are evaluated by FreeFileSync at runtime:

Example:
The FreeFileSync batch file C:\SyncJob.ffs_batch contains macro %MyVar% instead of an absolute target folder and is invoked by a cmd file:
set MyVar=C:\Target
"C:\Program files\FreeFileSync\FreeFileSync.exe" C:\SyncJob.ffs_batch
::%MyVar% is resolved as C:\Target during synchronization

Note
Temporary environment variables created with the set command are only valid if the synchronization is started by calling the FreeFileSync executable directly. Using start /wait would create a new program context without these temporary variables.
Performance Improvements
Performance settingsFreeFileSync can be set up to issue multiple file accesses in parallel. This speeds up synchronization times dramatically in cases where single I/O operations have significant latency (e.g. long response times on a slow network connection) or they cannot use the full bandwidth available (e.g. an SFTP server that has a speed limit for each connection).

The number of parallel file operations that FreeFileSync should use can be set up for each device individually in the Comparison Settings dialog. It is evaluated for all folder pairs of a configuration as follows:
 

During comparison FreeFileSync groups all folders by their root devices.
 
For example, consider a configuration with two folder pairs and parallel file operations set up:
C:\Source	↔	D:\Target
C:\Source2	↔	E:\Target
 
Parallel operations	Device root
1	C:\
2	D:\
3	E:\
FreeFileSync will put the folders C:\Source and C:\Source2 into the same group and allow only 1 file operation at a time. Folder D:\Target will be traversed using 2 operations, and E:\Target using 3 operations at a time. In total FreeFileSync will be scanning all four folders employing 6 file operations in parallel.

When synchronizing a folder pair FreeFileSync will use the maximum of the number of parallel operations that the two folders support.

In the previous example the folder pair C:\Source ↔ D:\Target will be synchronized using 2 parallel operations, and C:\Source2 ↔ E:\target will be using 3.

Note
FreeFileSync implements parallel file operations by opening multiple connections to a device. Some devices like SFTP servers have limits on how many connections they allow and will fail if too many are attempted; see (S)FTP Setup.
RealTimeSync
RealTimeSync
— Automated Synchronization —

The primary function of RealTimeSync is to execute a command line each time it detects changes in one of the monitored directories, or when a directory becomes available (e. g. insert of a USB-stick). Usually this command line will trigger a FreeFileSync batch job.

RealTimeSync will register to receive change notifications directly from the operating system in order to avoid the overhead of repeatedly polling for changes. Each time a file or folder is created/updated/deleted in the monitored directories or their sub directories, RealTimeSync will run the command line.

Example: Real time synchronization using FreeFileSync
Start RealTimeSync.exe located in FreeFileSync's installation directory and enter the folders you want to monitor. Instead of doing this manually you can import an ffs_batch file via Menu → File → Open or simply via drag and drop. RealTimeSync will not only extract all directories relevant for synchronization, but will also set up the command line to execute the ffs_batch file each time changes are detected. Now press Start to begin monitoring.
RealTimeSync main window
 
Note
The command should not block progress. If you call a FreeFileSync batch job, make sure it won't show any popup dialogs. See notes in Command Line Usage.
 
RealTimeSync will skip showing the main dialog and begin monitoring immediately if you pass an ffs_real configuration file as the first command line argument to RealTimeSync.exe. This can be used to integrate RealTimeSync into the operating system's auto start:

C:\Program Files\FreeFileSync\RealTimeSync.exe" "D:\Backup Projects.ffs_real"

You can also pass an ffs_batch file as first argument, which RealTimeSync will automatically convert into a ffs_real configuration with default settings (e.g. 10 seconds idle time).
 
RealTimeSync does not require you to start FreeFileSync. It can also be used in other scenarios, like sending an email whenever a certain directory is modified.

Example: Automatic synchronization when a USB stick is inserted
Save an ffs_batch configuration in the USB stick's root directory, e.g. H:\ and let FreeFileSync run it when the stick is mounted. But, instead of hard coding the USB drive letter H:\ (which may change occasionally), refer to the USB stick via its volume name instead.

Configure RealTimeSync as follows:
 

Monitor USB stick insert
"Backup" is the volume name of the USB stick in our example.

Whenever directory H:\Data becomes available, RealTimeSync executes the command line which starts the batch job located on the stick. RealTimeSync will also trigger each time files are modified in H:\Data.
Note
The full path of the last changed file and the action that triggered the change notification (create, update or delete) are written to the environment variables %change_path% and %change_action%.

Example: Log names of changed files and directories (Windows)
Show which file or directory has triggered a change. Enter command line:
cmd /c echo %change_action% "%change_path%" & pause

Write a list of all changes to a log file:
cmd /c echo %change_action% "%change_path%" >> %csidl_Desktop%\log.txt

Limitations:
If multiple changes happen at the same time, only the path of the first file is written to variable %changed_file%.
While RealTimeSync is executing the command line, monitoring for changed files is deliberately inactive.

The command line usually starts a synchronization task using FreeFileSync which naturally leads to additional file change notifications. Therefore, the RealTimeSync change detection has to be deactivated to not go into an endless loop. On the other hand, it is not likely that changes (other than those from FreeFileSync) happen in first place since RealTimeSync runs the command line only after the user-specified idle time has passed. This makes sure the monitored folders are not in heavy use. In any case, files changed during the execution of FreeFileSync will be synchronized the next time FreeFileSync runs.
RealTimeSync: Run as Service (Windows)
RealTimeSync is designed to run as a background process which does not need further attention. Depending on your requirements, there are a number of ways to start it automatically. Generally, the goal is to execute a command line of the form:
<FreeFileSync installation folder>\RealTimeSync.exe <path to *.ffs_real or *.ffs_batch file>
e.g.:
"C:\Program Files\FreeFileSync\RealTimeSync.exe" "D:\Backup Projects.ffs_real"

Example: Start RealTimeSync on login
Create a new shortcut, enter the command line from above as target and place it into the Windows autostart folder. (Enter shell:startup in the Windows Explorer address bar to find the folder quickly.)

Create shortcut

Shortcut properties

Example: Start RealTimeSync as a Service
RealTimeSync should be monitoring while Windows is running, irrespective of currently logged-in users: Create a new task in your operating systems's task scheduler and have it execute the command line above when the system starts. See Schedule Batch Jobs for an example of how to add a task. Then change the user which runs the task to SYSTEM - a special user account always running in the background.

Schedule RealTimeSync
Schedule Batch Jobs
Create a new batch job via FreeFileSync's main dialog: Menu → File → Save as a batch job...
Setup a FreeFileSync batch job

By default, FreeFileSync will show a progress dialog during synchronization and will wait while the summary dialog is shown. If the progress dialog is not needed, enable checkbox Run minimized and also set Auto-Close if you want to skip the summary dialog at the end.
Note
Even if the progress dialog is not shown at the beginning, you can make it visible at any time during synchronization by double-clicking the FreeFileSync icon in the notification area.

If you don't want error or warning messages to stall synchronization when no user is available to respond, either check Ignore errors or set Cancel to stop the synchronization at the first error.
 
Set up the FreeFileSync batch job in your operating system's scheduler:


A. Windows Task Scheduler:
Open the Task Scheduler either via the start menu, or enter taskschd.msc in the run dialog (keyboard shortcut: Windows + R).
 
Create a new basic task and follow the wizard.
 
Make Program/script point to the location of FreeFileSync.exe and insert the ffs_batch file into Add arguments.
 
Use quotation marks to protect spaces in path names, e.g. "D:\Backup Projects.ffs_batch"
Windows Task Scheduler

Note
Program/script always needs to point to an executable file like FreeFileSync.exe even when the ffs_batch file association is registered. If an ffs_batch file was entered instead, the task would return with error code 2147942593 (0x800700C1), "%1 is not a valid Win32 application".
If you schedule FreeFileSync to run under a different user account, note that settings (e.g. GlobalSettings.xml) will also be read from a different path, C:\Users\<username>\AppData\Roaming\FreeFileSync, or in the case of the SYSTEM account from C:\Windows\System32\config\systemprofile\AppData\Roaming\FreeFileSync.


B. macOS Automator and Calendar:
Open Launchpad and run Automator.
Launch macOS Automator
 
Create a new Calendar Alarm.
Create Calendar Alarm
 
Drag and drop the ffs_batch file on the workflow panel.
Drop FreeFileSync batch file in Automator
 
Drag and drop action Files & Folders/Open Finder Items and add it to the workflow.
Add open Finder items
 
Go to File → Save... and save the Automator job.
Save Automator job
 
The Calendar app will start automatically with the Automator job scheduled to the current day. You can now select a different time for synchronization or make it a recurring task.
Edit batch job in Calendar


D. Ubuntu Linux Gnome Scheduled Tasks:
Install Gnome-schedule if necessary: sudo apt-get install gnome-schedule
 
Search the Ubuntu Unity Dash for Scheduled tasks
 
Enter the command: <FreeFileSync installation folder>/FreeFileSync <job name>.ffs_batch
 
Select X application since FreeFileSync requires access to GUI
Gnome Scheduler
Synchronization Settings
Synchronization settings dialog

Detect Moved Files
FreeFileSync is able to detect moved files on one side and can quickly apply the same move on the target side during synchronization instead of a slow copy and delete. To make this work, FreeFileSync requires database files ("sync.ffs_db") to compare the current file system state against the time of the last synchronization.

The Two way variant already creates database files, therefore, detection of moved files is always active.
The Mirror variant however, does not need the database files to find synchronization directions, so detection of moved files is not available by default. If you don't mind the creation of the database files, you can enable this feature by selecting the Detect moved files checkbox.

Note
Detection of moved files is not yet possible when synchronizing a folder pair for the first time. Only beginning with the second sync the database files are available to determine moved files.
Detection is not supported by all file systems. Most notably, certain file moves on the FAT file system cannot be detected. Also virtualized file systems, e.g. a mounted WebDAV drive, might not support move detection. In these cases FreeFileSync will automatically fall back to copy and delete.
SFTP and FTP Setup
FreeFileSync supports synchronization with SFTP and FTP natively. Just enter your login information into the dialog shown for cloud folder selection: Cloud folder button
 
Enter SFTP login data

Note
In case the (S)FTP server sets file modification times to the current time you can do a Compare by File Size as a workaround. Another solution is to set up the Two way variant and have the files with the newer dates be copied back from the server during the next synchronization.

Configure SFTP for best performance
By default, FreeFileSync creates one connection to the server and uses one SFTP channel, i.e. only a single SFTP command can be sent and received at a time. Since most of this time is spent waiting due to the high latency of the remote connection, you can speed up reading large folder hierarchies by increasing both the connection and channel count.

The folder reading time is reduced by a factor of N x M when using N connections with M channels each.

Example: 10 connections using 2 channels each can yield a 20 times faster folder reading.
 
Set up SFTP for best performance
 
The creation of additional connections and channels takes time. If you are only scanning a small remote folder, setting up too many connections and channels might actually slow the overall process down. Creating extra connections is slower than creating extra channels.
 
SFTP servers have internal limits on the number of allowed connections and channels. Generally, servers expect one connection per user, so this number should be kept rather low. If too many connections and channels are used, the server may decide to stop responding.
 
Unlike connections, additional SFTP channels are (currently) only used during folder reading (comparison), but not during synchronization.

Advice
Start with low numbers and make tests with different combinations of connections and channels for your particular SFTP synchronization scenario to see what gives the highest speed. Note, however, that FreeFileSync reuses existing SFTP connections/channels. Therefore, you should restart FreeFileSync before measuring SFTP speed.
Tips and Tricks
Change settings with a single mouse click: Press and hold the right mouse button until the context menu is shown, then release while over the selection:
Comparison settings context menu Filter context menu Synchronization settings context menu
Select multiple configurations at a time:
Select multiple configurationsSelect a few items via mouse, and refine the selection by holding the Control key while clicking.
Start comparison directly by double-clicking on a configuration:
Double-click on configuration
Run a partial synchronization only for the currently selected files:
Synchronize Selection
Synchronize multiple folder pairs at a time with different configurations:
Add folder pair
Start synchronization directly without clicking on compare first:
Start synchronization directly
Move a window by clicking on a free area and holding the mouse button:
Move dialog via mouse
Open a batch configuration for edit via the Windows Explorer context menu:
Explorer context menu
Drag and drop two folders at a time from Windows Explorer to fill a folder pair in one go:
Two-folder drop
Copy files selected on the main dialog to an alternate folder and thereby save a "diff":
Copy to alternative path
Use a volume name instead of a drive letter:
Drive letter by volume name
Show thumbnail icons via the column header context menu:
Show thumbnail icons
Save the current view filter selection as default:
Save view filter settings
Remove local settings from individual folder pairs:
Remove local settings
Remove obsolete paths from the folder drop-down by using mouse hover and Delete key:
Remove drop-down path
Select a time span for files to include via the date column context menu:
Select time span
Double-click on comparison and synchronization variants to confirm the dialog:
Double-click comparison variant Double-click synchronization variant
Variable Drive Letters
USB memory sticks or external hard disks often get different drive letters assigned when plugged into distinct computers. FreeFileSync offers two solutions to handle this problem:

Option 1: Specify a folder path by using the volume name:
Enter the path as [USB-NAME]\folder instead of E:\folder where USB-NAME is the volume name of the USB stick which is currently mounted in drive E:\.

Note
Drive letter by volume nameIt is not required to look up and enter the volume name manually. Just select the corresponding entry in the drop down menu.

Option 2: Use a relative directory name:

Use \folder instead of E:\folder
Save and copy synchronization settings to the USB stick: E:\Backup.ffs_gui
Start FreeFileSync by double-clicking on E:\Backup.ffs_gui

The working directory is then automatically set to E:\ by the operating system so that the relative path \folder will be resolved as E:\folder during synchronization.
File Versioning
When you need to preserve files that have been deleted or overwritten, it's often sufficient to select Recycle bin in synchronization settings. However, this is only available for local drives and offers little control on how to store and how long to keep the files. FreeFileSync therefore has an additional option, Versioning.

1. Keep only the most recent versions
In synchronization settings, set deletion handling to Versioning and naming convention to Replace. Deleted files will be moved to the specified folder without any decoration and will replace already existing older versions.
Versioning

2. Keep multiple versions of old files
Set deletion handling to Versioning and naming convention to Time stamp [File]. FreeFileSync will move deleted files into the provided folder and add a time stamp to each file name. The structure of the synchronized folders is preserved so that old versions of a file can be conveniently accessed via a file browser.
 
Example: Last versions of the file Folder\File.txt inside folder D:\Revisions
D:\Revisions\Folder\File.txt 2012-12-12 111111.txt
D:\Revisions\Folder\File.txt 2012-12-12 122222.txt
D:\Revisions\Folder\File.txt 2012-12-12 133333.txt

With naming convention Time stamp [Folder] files are moved into a time-stamped subfolder of the versioning folder while their names remain unchanged. This makes it easy to manually undo a synchronization by moving the deleted files from the versioning folder back to their original folders.
 
Example: Last versions of the file Folder\File.txt inside folder D:\Revisions
D:\Revisions\2012-12-12 111111\Folder\File.txt
D:\Revisions\2012-12-12 122222\Folder\File.txt
D:\Revisions\2012-12-12 133333\Folder\File.txt

3. Save versions at certain intervals
With naming convention Replace it is possible to refine the granularity of versions to keep by adding Macros to the versioning folder path. For example, you can save deleted files on a daily basis by adding the %date% macro:

Example: Last versions of the file Folder\File.txt inside folder D:\Revisions\%date%
D:\Revisions\2012-12-11\Folder\File.txt
D:\Revisions\2012-12-12\Folder\File.txt
D:\Revisions\2012-12-13\Folder\File.txt
Volume Shadow Copy (Windows only)
FreeFileSync supports copying locked or shared files by creating a Volume Shadow Copy of the source drive. This feature can be configured via Menu → Tools → Options: Copy locked files.

Note
The volume snapshot created by the Volume Shadow Copy Service is only used for copying files that are actually locked.
Accessing the Volume Shadow Copy Service requires FreeFileSync to be started with administrator rights.

Troubleshooting
If you experience problems using the Volume Shadow Copy Service, a renewal of registration might help. Create and execute a cmd batch file and insert the following lines or enter directly via command line:
cd /d %windir%\system32
Net stop vss
Net stop swprv
regsvr32 ole32.dll
regsvr32 oleaut32.dll
regsvr32 vss_ps.dll
Vssvc /register
regsvr32 /i swprv.dll
regsvr32 /i eventcls.dll
regsvr32 es.dll
regsvr32 stdprov.dll
regsvr32 vssui.dll
regsvr32 msxml.dll
regsvr32 msxml3.dll
regsvr32 msxml4.dll
 
Reference: http://support.microsoft.com/kb/940032



build-FreeFileSync
FreeFileSync is a great open source file synchronization tool. It is one of my favorite tools. However, despite its open source nature, there is no build instruction for it. This repo records my own way of building FreeFileSync 10.12 on Ubuntu 18.04.

0. Download and extract the source code
As of writing, the latest version of FreeFileSync is 10.12 and it can be downloaded from:

https://freefilesync.org/download/FreeFileSync_10.12_Source.zip.

Note wget does not work with this URL. You can manually specify a version number to get the source code of an earlier version

You also need to get a Resources.zip from an earlier version of the source code, e.g., 10.11, https://freefilesync.org/download/FreeFileSync_10.11_Source.zip. This is probably a bug that the developer forgets to include it. Without it, the program cannot find the icons needed for the UI. The Resouces.zip is in FreeFileSync_10.11_Source/FreeFileSync/Build. Simply copy and paste the zip to the same place in the 10.12 folder.

1. Install a newer version of gcc
FreeFileSync requires a c++ compiler that supports c++2a. The default version of gcc on Ubuntu is 7.4.0 and does not work. I followed the instruction at: https://solarianprogrammer.com/2016/10/07/building-gcc-ubuntu-linux/ to build and install the gcc 9.1.0.

If you follow the steps correctly, you should be able to run "gcc-9.1 -v" without any error.

2. Install wxWidgets
FreeFileSync 10.12 requires at least wxWidgets 3.1.1 to compile -- because I find it uses several functions newly added in the version 3.1.1. I choose to install 3.1.2, the latest as of writing. Building wxWidgets should be relatively easy:

wget https://github.com/wxWidgets/wxWidgets/releases/download/v3.1.2/wxWidgets-3.1.2.tar.bz2
tar xf wxWidgets-3.1.2.tar.bz2
cd wxWidgets-3.1.2/
mkdir gtk-build # or any other name you like
cd gtk-build
../configure --disable-shared --enable-unicode
make
make install # use sudo if necessary
3. Install Boost
FreeFileSync requries Boost to compile. However, it does not require a very recent version, so simply install the system package to get version 1.65.1:

sudo apt install libboost-all-dev
4. Modify the Makefile
The Makefile is at FreeFileSync_10.12_Source/FreeFileSync/Source/Makefile. We need to change all occurrances (there should be two) of "g++" to "g++-9.1" . In the original file, the C++ compiler is hardcoded to g++. However, we installed the gcc-9.1 in the first step and it is called g++-9.1.

5. Tweak the code:
Now everything is almost ready. However, depending on your version of libssh2 and libcurl, you may encounter several errors. The best way to fix this is to install the latest version of these two libraries. However, I am not very familiar with them and do not want to change the system default version, so I fix the code when I get compilation errors.

In "afs/libcurl/curl_wrap.h", comment the line 78 and 120. Basically, we wipe out

ZEN_CHECK_CASE_FOR_CONSTANT(CURLE_OBSOLETE51); 
and ZEN_CHECK_CASE_FOR_CONSTANT(CURLE_RECURSIVE_API_CALL);. 
In "afs/sftp.cpp", add at line 58

#define MAX_SFTP_OUTGOING_SIZE 30000
#define MAX_SFTP_READ_SIZE 30000
In "afs/sftp.cpp", add at line 1662

#define LIBSSH2_SFTP_DEFAULT_MODE      -1
I found the above consts from header files in newer versions of libssh2 and libcurl.

6. Compile:
Run "make" in folder FreeFileSync_10.12_Source/FreeFileSync/Source. It will take roughly 10 minutes to compile on a i7 machine.

The binary should be waiting for you in FreeFileSync_10.12_Source/FreeFileSync/Build/Bin.

Hopefully, you make it!
