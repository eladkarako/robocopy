Microsoft RoboCopy  

RoboCopy is a file copy program that comes with Windows.
newer versions are a less portable since their string-table is stored in few files under your Windows installation folder..  
(`en-US/robocopy.exe.mui`).  

I've embedded it into the exe, so you can just copy it anywhere you previously had your old `RoboCopy.exe` file.  
also there is a proper manifest without dependencies that I use everywhere,  
with `SegmentHeap` and `longPathAware`, not sure if it would make any difference,  
put this into a `.reg` file, run it as admin, and reboot to make sure the manifest value has any effect..  

```reg
Windows Registry Editor Version 5.00
;https://learn.microsoft.com/en-us/windows/win32/fileio/maximum-file-path-limitation?tabs=registry
[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\FileSystem]
"LongPathsEnabled"=dword:00000001

;to explicitly turn it off set it to 
;"LongPathsEnabled"=dword:00000000
;to let the OS handle it implicitly remove the value using
;"LongPathsEnabled"=-
```

as for segmented heap, there are quite a lot of tests regarding if this implementation has positive benefits over allocation speed or overall memory usage, I know several browsers avoided using it, and instead implemented their own heap allocation..  
https://learn.microsoft.com/en-us/windows/win32/sbscs/application-manifests#heaptype  
https://blog.s-schoener.com/2024-11-05-segment-heap/  

<hr/>


<hr/>

```txt

-------------------------------------------------------------------------------
   ROBOCOPY     ::     Robust File Copy for Windows                              
-------------------------------------------------------------------------------

  Started : 
              Usage :: ROBOCOPY source destination [file [file]...] [options]

             source :: Source Directory (drive:\path or \\server\share\path).
        destination :: Destination Dir  (drive:\path or \\server\share\path).
               file :: File(s) to copy  (names/wildcards: default is "*.*").

::
:: Copy options :
::
                 /S :: copy Subdirectories, but not empty ones.
                 /E :: copy subdirectories, including Empty ones.
             /LEV:n :: only copy the top n LEVels of the source directory tree.

                 /Z :: copy files in restartable mode.
                 /B :: copy files in Backup mode.
                /ZB :: use restartable mode; if access denied use Backup mode.
                 /J :: copy using unbuffered I/O (recommended for large files).
            /EFSRAW :: copy all encrypted files in EFS RAW mode.

  /COPY:copyflag[s] :: what to COPY for files (default is /COPY:DAT).
                       (copyflags : D=Data, A=Attributes, T=Timestamps, X=Skip alt data streams (X ignored if /B or /ZB).
                       (S=Security=NTFS ACLs, O=Owner info, U=aUditing info).

 
               /SEC :: copy files with SECurity (equivalent to /COPY:DATS).
           /COPYALL :: COPY ALL file info (equivalent to /COPY:DATSOU).
            /NOCOPY :: COPY NO file info (useful with /PURGE).
            /SECFIX :: FIX file SECurity on all files, even skipped files.
            /TIMFIX :: FIX file TIMes on all files, even skipped files.

             /PURGE :: delete dest files/dirs that no longer exist in source.
               /MIR :: MIRror a directory tree (equivalent to /E plus /PURGE).

               /MOV :: MOVe files (delete from source after copying).
              /MOVE :: MOVE files AND dirs (delete from source after copying).

     /A+:[RASHCNET] :: add the given Attributes to copied files.
     /A-:[RASHCNETO]:: remove the given Attributes from copied files.

            /CREATE :: CREATE directory tree and zero-length files only.
               /FAT :: create destination files using 8.3 FAT file names only.
               /256 :: turn off very long path (> 256 characters) support.

             /MON:n :: MONitor source; run again when more than n changes seen.
             /MOT:m :: MOnitor source; run again in m minutes Time, if changed.

      /RH:hhmm-hhmm :: Run Hours - times when new copies may be started.
                /PF :: check run hours on a Per File (not per pass) basis.

             /IPG:n :: Inter-Packet Gap (ms), to free bandwidth on slow lines.

                /SJ :: copy Junctions as junctions instead of as the junction targets.
                /SL :: copy Symbolic Links as links instead of as the link targets.

            /MT[:n] :: Do multi-threaded copies with n threads (default 8).
                       n must be at least 1 and not greater than 128.
                       This option is incompatible with the /IPG and /EFSRAW options.
                       Redirect output using /LOG option for better performance.

 /DCOPY:copyflag[s] :: what to COPY for directories (default is /DCOPY:DA).
                       (copyflags : D=Data, A=Attributes, T=Timestamps, E=EAs, X=Skip alt data streams).

           /NODCOPY :: COPY NO directory info (by default /DCOPY:DA is done).

         /NOOFFLOAD :: copy files without using the Windows Copy Offload mechanism.

          /COMPRESS :: Request network compression during file transfer, if applicable.

      /SPARSE[:Y/N] :: Enable or disable retaining the sparse state during copy.

           /NOCLONE :: Do not attempt block cloning as an optimization.
::
:: Copy File Throttling Options :
::
  /IoMaxSize:n[KMG] :: Requested max i/o size per {read,write} cycle, in n [KMG] bytes.

     /IoRate:n[KMG] :: Requested i/o rate, in n [KMG] bytes per second.

  /Threshold:n[KMG] :: File size threshold for throttling, in n [KMG] bytes (see Remarks).

::
:: File Selection Options :
::
                 /A :: copy only files with the Archive attribute set.
                 /M :: copy only files with the Archive attribute and reset it.
    /IA:[RASHCNETO] :: Include only files with any of the given Attributes set.
    /XA:[RASHCNETO] :: eXclude files with any of the given Attributes set.

 /XF file [file]... :: eXclude Files matching given names/paths/wildcards.
 /XD dirs [dirs]... :: eXclude Directories matching given names/paths.

                /XC :: eXclude Changed files.
                /XN :: eXclude Newer files.
                /XO :: eXclude Older files.
                /XX :: eXclude eXtra files and directories.
                /XL :: eXclude Lonely files and directories.
                /IS :: Include Same files.
                /IT :: Include Tweaked files.

             /MAX:n :: MAXimum file size - exclude files bigger than n bytes.
             /MIN:n :: MINimum file size - exclude files smaller than n bytes.

          /MAXAGE:n :: MAXimum file AGE - exclude files older than n days/date.
          /MINAGE:n :: MINimum file AGE - exclude files newer than n days/date.
          /MAXLAD:n :: MAXimum Last Access Date - exclude files unused since n.
          /MINLAD:n :: MINimum Last Access Date - exclude files used since n.
                       (If n < 1900 then n = n days, else n = YYYYMMDD date).

               /FFT :: assume FAT File Times (2-second granularity).
               /DST :: compensate for one-hour DST time differences.

                /XJ :: eXclude symbolic links (for both files and directories) and Junction points.
               /XJD :: eXclude symbolic links for Directories and Junction points.
               /XJF :: eXclude symbolic links for Files.

                /IM :: Include Modified files (differing change times).
::
:: Retry Options :
::
               /R:n :: number of Retries on failed copies: default 1 million.
               /W:n :: Wait time between retries: default is 30 seconds.

               /REG :: Save /R:n and /W:n in the Registry as default settings.

               /TBD :: Wait for sharenames To Be Defined (retry error 67).

               /LFSM :: Operate in low free space mode, enabling copy pause and resume (see Remarks).

        /LFSM:n[KMG] :: /LFSM, specifying the floor size in n [K:kilo,M:mega,G:giga] bytes.

::
:: Logging Options :
::
                 /L :: List only - don't copy, timestamp or delete any files.
                 /X :: report all eXtra files, not just those selected.
                 /V :: produce Verbose output, showing skipped files.
                /TS :: include source file Time Stamps in the output.
                /FP :: include Full Pathname of files in the output.
             /BYTES :: Print sizes as bytes.

                /NS :: No Size - don't log file sizes.
                /NC :: No Class - don't log file classes.
               /NFL :: No File List - don't log file names.
               /NDL :: No Directory List - don't log directory names.

                /NP :: No Progress - don't display percentage copied.
               /ETA :: show Estimated Time of Arrival of copied files.

          /LOG:file :: output status to LOG file (overwrite existing log).
         /LOG+:file :: output status to LOG file (append to existing log).

       /UNILOG:file :: output status to LOG file as UNICODE (overwrite existing log).
      /UNILOG+:file :: output status to LOG file as UNICODE (append to existing log).

               /TEE :: output to console window, as well as the log file.

               /NJH :: No Job Header.
               /NJS :: No Job Summary.

           /UNICODE :: output status as UNICODE.

::
:: Job Options :
::
       /JOB:jobname :: take parameters from the named JOB file.
      /SAVE:jobname :: SAVE parameters to the named job file
              /QUIT :: QUIT after processing command line (to view parameters). 
              /NOSD :: NO Source Directory is specified.
              /NODD :: NO Destination Directory is specified.
                /IF :: Include the following Files.

::
:: Remarks :
::
       Using /PURGE or /MIR on the root directory of the volume formerly caused 
       robocopy to apply the requested operation on files inside the System 
       Volume Information directory as well. This is no longer the case; if 
       either is specified, robocopy will skip any files or directories with that 
       name in the top-level source and destination directories of the copy session.

       The modified files classification applies only when both source 
       and destination filesystems support change timestamps (e.g., NTFS) 
       and the source and destination files have different change times but are 
       otherwise the same. These files are not copied by default; specify /IM 
       to include them.

       The /DCOPY:E flag requests that extended attribute copying should be 
       attempted for directories. Note that currently robocopy will continue 
       if a directory's EAs could not be copied. This flag is also not included 
       in /COPYALL.

       If either /IoMaxSize or /IoRate are specified, robocopy will enable 
       copy file throttling (the purpose being to reduce system load). 
       Both may be adjusted to allowable or optimal values; i.e., both 
       specify desired copy parameters, but the system and robocopy are 
       allowed to adjust them to reasonable/allowed values as necessary. 
       If /Threshold is also used, it specifies a minimum file size for 
       engaging throttling; files below that size will not be throttled. 
       Values for all three parameters may be followed by an optional suffix 
       character from the set [KMG] (kilo, mega, giga).

       Using /LFSM requests robocopy to operate in 'low free space mode'. 
       In this mode, robocopy will pause whenever a file copy would cause the 
       destination volume's free space to go below a 'floor' value, which 
       can be explicitly specified by the LFSM:n[KMG] form of the flag. 
       If /LFSM is specified with no explicit floor value, the floor is set to 
       ten percent of the destination volume's size. 
       Low free space mode is incompatible with /MT and /EFSRAW.
```

<hr/>

version:  

the current version is `10.0.28000.1516 (WinBuild.160101.0800)` which is the newest version from a test build of Windows 11 26H1 `28000.1719.260306` from `March 10th 2026`.  

```txt
1 VERSIONINFO
FILEVERSION 10,0,28000,1516
PRODUCTVERSION 10,0,28000,1516
FILEOS 0x40004
FILETYPE 0x1
{
BLOCK "StringFileInfo"
{
	BLOCK "040904B0"
	{
		VALUE "CompanyName", "Microsoft Corporation"
		VALUE "FileDescription", "Microsoft Robocopy"
		VALUE "FileVersion", "10.0.28000.1516 (WinBuild.160101.0800)"
		VALUE "InternalName", "robocopy"
		VALUE "LegalCopyright", "\xA9 Microsoft Corporation. All rights reserved."
		VALUE "OriginalFilename", "robocopy.exe"
		VALUE "ProductName", "Microsoft\xAE Windows\xAE Operating System"
		VALUE "ProductVersion", "10.0.28000.1516"
	}
}

BLOCK "VarFileInfo"
{
	VALUE "Translation", 0x0409 0x04B0  
}
}
```

<hr/>

manifest:  

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?> 
<assembly xmlns="urn:schemas-microsoft-com:asm.v1" manifestVersion="1.0"> 
  <dependency optional="yes"> 
    <dependentAssembly> 
      <assemblyIdentity name="Microsoft.Windows.Common-Controls" 
        version="6.0.0.0" publicKeyToken="6595b64144ccf1df" 
        type="win32" processorArchitecture="*" language="*" /> 
    </dependentAssembly> 
  </dependency> 
  <application xmlns="urn:schemas-microsoft-com:asm.v3"> 
     <windowsSettings> <dpiAware      xmlns="http://schemas.microsoft.com/SMI/2005/WindowsSettings">true/PM</dpiAware>                     </windowsSettings> 
     <windowsSettings> <dpiAwareness  xmlns="http://schemas.microsoft.com/SMI/2016/WindowsSettings">PerMonitorV2,PerMonitor</dpiAwareness> </windowsSettings> 
     <windowsSettings> <gdiScaling    xmlns="http://schemas.microsoft.com/SMI/2017/WindowsSettings">true</gdiScaling>                      </windowsSettings> 
     <windowsSettings> <longPathAware xmlns="http://schemas.microsoft.com/SMI/2016/WindowsSettings">true</longPathAware>                   </windowsSettings> 
     <windowsSettings> <heapType      xmlns="http://schemas.microsoft.com/SMI/2020/WindowsSettings">SegmentHeap</heapType>                 </windowsSettings> 
  </application> 
  <trustInfo xmlns="urn:schemas-microsoft-com:asm.v3"> 
    <security> 
      <requestedPrivileges> 
        <requestedExecutionLevel level="asInvoker" uiAccess="false" /> 
      </requestedPrivileges> 
    </security> 
  </trustInfo> 
  <compatibility xmlns="urn:schemas-microsoft-com:compatibility.v1"> 
    <application> 
      <supportedOS Id="{8e0f7a12-bfb3-4fe8-b9a5-48fd50a15a9a}" /> 
      <supportedOS Id="{1f676c76-80e1-4239-95bb-83d0f6d0da78}" /> 
      <supportedOS Id="{4a2f28e3-53b9-4441-ba9c-d69d4a4a6e38}" /> 
      <supportedOS Id="{35138b9a-5d96-4fbd-8e2d-a2440225f93a}" /> 
      <supportedOS Id="{e2011457-1546-43c5-a5fe-008deee3d3f0}" /> 
    </application> 
  </compatibility> 
</assembly> 
 
```

<hr/>

here is the `en-US` string table.. if you care.. 

```txt
LANGUAGE LANG_NEUTRAL, SUBLANG_NEUTRAL

STRINGTABLE
LANGUAGE LANG_ENGLISH, SUBLANG_ENGLISH_US
{
  1, 	"\n"
  3, 	"-------------------------------------------------------------------------------\n"
  5, 	"   ROBOCOPY     ::     Robust File Copy for Windows                              "
  7, 	"\n------------------------------------------------------------------------------\n"
  10, 	"WARNING: /COPY:X specified, but is ignored for backup-mode copying (/B or /ZB).\n"
  12, 	"ERROR : /SECFIX specified, without specifying WHICH security info to copy.\n"
  14, 	" Quitting : /QUIT Specified on Command Line.\n"
}

STRINGTABLE
LANGUAGE LANG_ENGLISH, SUBLANG_ENGLISH_US
{
  16, 	"ERROR : No Source Directory Specified.\n"
  18, 	"ERROR : No Destination Directory Specified.\n"
  20, 	"NOTE : Security may not be copied - Source might not support persistent ACLs.\n"
  22, 	"NOTE : Security may not be copied - Destination might not support persistent ACLs.\n"
  24, 	"ERROR : You do not have the Manage Auditing user right."
  25, 	"*****  You need this to copy auditing information (/COPY:U or /COPYALL).\n"
  27, 	"ERROR : You do not have the Backup and Restore Files user rights."
  28, 	"*****  You need these to perform Backup copies (/B or /ZB).\n"
  30, 	"ERROR : Robocopy ran out of memory, exiting."
  31, 	"ERROR : Invalid Parameter #%d : \"%s\"\n"
}

STRINGTABLE
LANGUAGE LANG_ENGLISH, SUBLANG_ENGLISH_US
{
  32, 	"ERROR : Invalid Job File, Line #%d :\"%s\"\n"
  33, 	"\n  Started : %s %s"
  34, 	"\n   Source %c "
  35, 	"\n     Dest %c "
}

STRINGTABLE
LANGUAGE LANG_ENGLISH, SUBLANG_ENGLISH_US
{
  71, 	"\n    Files : "
  73, 	"Exc Files : "
  75, 	" Exc Dirs : "
}

STRINGTABLE
LANGUAGE LANG_ENGLISH, SUBLANG_ENGLISH_US
{
  81, 	"  Options : "
  91, 	" Log File : "
  93, 	" Job File : "
}

STRINGTABLE
LANGUAGE LANG_ENGLISH, SUBLANG_ENGLISH_US
{
  150, 	"\n"
  152, 	"Total"
  153, 	"Copied"
  154, 	"Skipped"
  155, 	"Mismatch"
  156, 	"FAILED"
  157, 	"Extras"
  158, 	"Dirs :"
  159, 	"Files :"
}

STRINGTABLE
LANGUAGE LANG_ENGLISH, SUBLANG_ENGLISH_US
{
  160, 	"Bytes :"
  161, 	"Times :"
  162, 	"Speed :"
  163, 	"Bytes/sec."
  164, 	"MegaBytes/min."
  165, 	"    Times :%*s%*s%*s%*s%*s%*s"
  166, 	"\n   Ended : %s %s"
  170, 	"%9s"
}

STRINGTABLE
LANGUAGE LANG_ENGLISH, SUBLANG_ENGLISH_US
{
  176, 	"\n           %*s%*s%*s%*s%*s%*s\n     Dirs :%*s%*s%*s%*s%*s%*s"
  177, 	"    Files :%*s%*s%*s%*s%*s%*s"
  178, 	"    Bytes :%*s%*s%*s%*s%*s%*s"
  179, 	"    Times :"
  181, 	"%lu:%02lu:%02lu"
  182, 	"          "
  183, 	"\n\n   Ended : %hs"
}

STRINGTABLE
LANGUAGE LANG_ENGLISH, SUBLANG_ENGLISH_US
{
  223, 	"\nPausing to wait for free space; free space %s\n"
}

STRINGTABLE
LANGUAGE LANG_ENGLISH, SUBLANG_ENGLISH_US
{
  224, 	"Waiting %d seconds..."
  225, 	" Retrying..."
  227, 	"ERROR: RETRY LIMIT EXCEEDED.\n"
  229, 	"\n  Monitor : Waiting for %d minutes and %d changes...\n"
  231, 	"\r%4d mins : %d changes."
  233, 	"\n    Hours : Paused at %02d:%02d until %02d:%02d..."
}

STRINGTABLE
LANGUAGE LANG_ENGLISH, SUBLANG_ENGLISH_US
{
  320, 	"\n::\n:: Robocopy Job %s\n::\n:: Created by %s on %s at %s\n::\n"
  321, 	"\n::\n:: Source Directory :\n::"
  322, 	"\n::\n:: Destination Directory :\n::"
  323, 	"\n::\n:: Include These Files :\n::\n\t/IF\t\t:: Include Files matching these names"
  324, 	"\n::\n:: Exclude These Directories :\n::\n\t/XD\t\t:: eXclude Directories matching these names"
  325, 	"\n::\n:: Exclude These Files :\n::\n\t/XF\t\t:: eXclude Files matching these names"
  326, 	"::\t\t*.*\t:: Include all names (currently - Command Line may override)"
  327, 	"::\t\t\t:: eXclude no names (currently - Command Line may override)"
  328, 	"::\t\t\t:: To Be Specified on the Command Line"
}

STRINGTABLE
LANGUAGE LANG_ENGLISH, SUBLANG_ENGLISH_US
{
  1101, 	"       Simple Usage :: ROBOCOPY source destination /MIR\n"
  1102, 	"             source :: Source Directory (drive:\\path or \\\\server\\share\\path)."
  1103, 	"        destination :: Destination Dir  (drive:\\path or \\\\server\\share\\path)."
}

STRINGTABLE
LANGUAGE LANG_ENGLISH, SUBLANG_ENGLISH_US
{
  1104, 	"               /MIR :: Mirror a complete directory tree.\n"
  1105, 	"    For more usage information run ROBOCOPY /?\n"
  1106, 	"                                                          "
  1107, 	"****  /MIR can DELETE files as well as copy them !\n"
  1110, 	"The /EFSRAW option cannot be used with the /MT option."
  1111, 	"The /IPG option cannot be used with the /MT option."
  1112, 	"The /LFSM option cannot be used with the /MT or /EFSRAW options."
  1113, 	"Warning: Failed to query volume space; /LFSM will not monitor for low free space."
}

STRINGTABLE
LANGUAGE LANG_ENGLISH, SUBLANG_ENGLISH_US
{
  1201, 	"              Usage :: ROBOCOPY source destination [file [file]...] [options]\n"
  1202, 	"             source :: Source Directory (drive:\\path or \\\\server\\share\\path)."
  1203, 	"        destination :: Destination Dir  (drive:\\path or \\\\server\\share\\path)."
  1204, 	"               file :: File(s) to copy  (names/wildcards: default is \"*.*\").\n"
}

STRINGTABLE
LANGUAGE LANG_ENGLISH, SUBLANG_ENGLISH_US
{
  1300, 	"::\n:: Copy options :\n::"
  1301, 	"                 /S :: copy Subdirectories, but not empty ones."
  1302, 	"                 /E :: copy subdirectories, including Empty ones."
  1303, 	"             /LEV:n :: only copy the top n LEVels of the source directory tree.\n"
  1304, 	"                 /Z :: copy files in restartable mode."
  1305, 	"                 /B :: copy files in Backup mode."
  1306, 	"                /ZB :: use restartable mode; if access denied use Backup mode."
  1307, 	"                 /J :: copy using unbuffered I/O (recommended for large files)."
  1308, 	"            /EFSRAW :: copy all encrypted files in EFS RAW mode.\n"
  1309, 	"  /COPY:copyflag[s] :: what to COPY for files (default is /COPY:DAT)."
  1310, 	"                       (copyflags : D=Data, A=Attributes, T=Timestamps, X=Skip alt data streams (X ignored if /B or /ZB)."
  1311, 	"                       (S=Security=NTFS ACLs, O=Owner info, U=aUditing info).\n"
}

STRINGTABLE
LANGUAGE LANG_ENGLISH, SUBLANG_ENGLISH_US
{
  1312, 	" "
  1313, 	"               /SEC :: copy files with SECurity (equivalent to /COPY:DATS)."
  1314, 	"           /COPYALL :: COPY ALL file info (equivalent to /COPY:DATSOU)."
  1315, 	"            /NOCOPY :: COPY NO file info (useful with /PURGE)."
  1316, 	"            /SECFIX :: FIX file SECurity on all files, even skipped files."
  1317, 	"            /TIMFIX :: FIX file TIMes on all files, even skipped files.\n"
  1318, 	"             /PURGE :: delete dest files/dirs that no longer exist in source."
  1319, 	"               /MIR :: MIRror a directory tree (equivalent to /E plus /PURGE).\n"
  1320, 	"               /MOV :: MOVe files (delete from source after copying)."
  1321, 	"              /MOVE :: MOVE files AND dirs (delete from source after copying).\n"
  1322, 	"     /A+:[RASHCNET] :: add the given Attributes to copied files."
  1323, 	"     /A-:[RASHCNETO]:: remove the given Attributes from copied files.\n"
  1324, 	"            /CREATE :: CREATE directory tree and zero-length files only."
  1325, 	"               /FAT :: create destination files using 8.3 FAT file names only."
  1326, 	"               /256 :: turn off very long path (> 256 characters) support.\n"
  1327, 	"             /MON:n :: MONitor source; run again when more than n changes seen."
}

STRINGTABLE
LANGUAGE LANG_ENGLISH, SUBLANG_ENGLISH_US
{
  1328, 	"             /MOT:m :: MOnitor source; run again in m minutes Time, if changed.\n"
  1329, 	"      /RH:hhmm-hhmm :: Run Hours - times when new copies may be started."
  1330, 	"                /PF :: check run hours on a Per File (not per pass) basis.\n"
  1331, 	"             /IPG:n :: Inter-Packet Gap (ms), to free bandwidth on slow lines.\n"
  1332, 	"                /SJ :: copy Junctions as junctions instead of as the junction targets."
  1333, 	"                /SL :: copy Symbolic Links as links instead of as the link targets.\n"
  1334, 	"            /MT[:n] :: Do multi-threaded copies with n threads (default 8)."
  1335, 	"                       n must be at least 1 and not greater than 128."
  1336, 	"                       This option is incompatible with the /IPG and /EFSRAW options."
  1337, 	"                       Redirect output using /LOG option for better performance.\n"
  1338, 	" /DCOPY:copyflag[s] :: what to COPY for directories (default is /DCOPY:DA)."
  1339, 	"                       (copyflags : D=Data, A=Attributes, T=Timestamps, E=EAs, X=Skip alt data streams).\n"
  1340, 	"           /NODCOPY :: COPY NO directory info (by default /DCOPY:DA is done).\n"
  1343, 	"         /NOOFFLOAD :: copy files without using the Windows Copy Offload mechanism.\n"
}

STRINGTABLE
LANGUAGE LANG_ENGLISH, SUBLANG_ENGLISH_US
{
  1344, 	"          /COMPRESS :: Request network compression during file transfer, if applicable.\n"
  1345, 	"      /SPARSE[:Y/N] :: Enable or disable retaining the sparse state during copy.\n"
  1346, 	"           /NOCLONE :: Do not attempt block cloning as an optimization."
}

STRINGTABLE
LANGUAGE LANG_ENGLISH, SUBLANG_ENGLISH_US
{
  1380, 	"::\n:: Copy File Throttling Options :\n::"
  1381, 	"  /IoMaxSize:n[KMG] :: Requested max i/o size per {read,write} cycle, in n [KMG] bytes.\n"
  1382, 	"     /IoRate:n[KMG] :: Requested i/o rate, in n [KMG] bytes per second.\n"
  1383, 	"  /Threshold:n[KMG] :: File size threshold for throttling, in n [KMG] bytes (see Remarks).\n"
}

STRINGTABLE
LANGUAGE LANG_ENGLISH, SUBLANG_ENGLISH_US
{
  1401, 	"::\n:: File Selection Options :\n::"
  1402, 	"                 /A :: copy only files with the Archive attribute set."
  1403, 	"                 /M :: copy only files with the Archive attribute and reset it."
  1404, 	"    /IA:[RASHCNETO] :: Include only files with any of the given Attributes set."
  1405, 	"    /XA:[RASHCNETO] :: eXclude files with any of the given Attributes set.\n"
  1406, 	" /XF file [file]... :: eXclude Files matching given names/paths/wildcards."
  1407, 	" /XD dirs [dirs]... :: eXclude Directories matching given names/paths.\n"
}

STRINGTABLE
LANGUAGE LANG_ENGLISH, SUBLANG_ENGLISH_US
{
  1408, 	"                /XC :: eXclude Changed files."
  1409, 	"                /XN :: eXclude Newer files."
  1410, 	"                /XO :: eXclude Older files."
  1411, 	"                /XX :: eXclude eXtra files and directories."
  1412, 	"                /XL :: eXclude Lonely files and directories."
  1413, 	"                /IS :: Include Same files."
  1414, 	"                /IT :: Include Tweaked files.\n"
  1415, 	"             /MAX:n :: MAXimum file size - exclude files bigger than n bytes."
  1416, 	"             /MIN:n :: MINimum file size - exclude files smaller than n bytes.\n"
  1417, 	"          /MAXAGE:n :: MAXimum file AGE - exclude files older than n days/date."
  1418, 	"          /MINAGE:n :: MINimum file AGE - exclude files newer than n days/date."
  1419, 	"          /MAXLAD:n :: MAXimum Last Access Date - exclude files unused since n."
  1420, 	"          /MINLAD:n :: MINimum Last Access Date - exclude files used since n."
  1421, 	"                       (If n < 1900 then n = n days, else n = YYYYMMDD date).\n"
  1422, 	"               /FFT :: assume FAT File Times (2-second granularity)."
  1423, 	"               /DST :: compensate for one-hour DST time differences.\n"
}

STRINGTABLE
LANGUAGE LANG_ENGLISH, SUBLANG_ENGLISH_US
{
  1424, 	"                /XJ :: eXclude symbolic links (for both files and directories) and Junction points."
  1425, 	"               /XJD :: eXclude symbolic links for Directories and Junction points."
  1426, 	"               /XJF :: eXclude symbolic links for Files.\n"
  1427, 	"                /IM :: Include Modified files (differing change times)."
}

STRINGTABLE
LANGUAGE LANG_ENGLISH, SUBLANG_ENGLISH_US
{
  1501, 	"::\n:: Retry Options :\n::"
  1502, 	"               /R:n :: number of Retries on failed copies: default 1 million."
  1503, 	"               /W:n :: Wait time between retries: default is 30 seconds.\n"
}

STRINGTABLE
LANGUAGE LANG_ENGLISH, SUBLANG_ENGLISH_US
{
  1504, 	"               /REG :: Save /R:n and /W:n in the Registry as default settings.\n"
  1505, 	"               /TBD :: Wait for sharenames To Be Defined (retry error 67).\n"
  1506, 	"               /LFSM :: Operate in low free space mode, enabling copy pause and resume (see Remarks).\n"
  1507, 	"        /LFSM:n[KMG] :: /LFSM, specifying the floor size in n [K:kilo,M:mega,G:giga] bytes.\n"
}

STRINGTABLE
LANGUAGE LANG_ENGLISH, SUBLANG_ENGLISH_US
{
  1601, 	"::\n:: Logging Options :\n::"
  1602, 	"                 /L :: List only - don't copy, timestamp or delete any files."
  1603, 	"                 /X :: report all eXtra files, not just those selected."
  1604, 	"                 /V :: produce Verbose output, showing skipped files."
  1605, 	"                /TS :: include source file Time Stamps in the output."
  1606, 	"                /FP :: include Full Pathname of files in the output."
  1607, 	"             /BYTES :: Print sizes as bytes.\n"
  1608, 	"                /NS :: No Size - don't log file sizes."
  1609, 	"                /NC :: No Class - don't log file classes."
  1610, 	"               /NFL :: No File List - don't log file names."
  1611, 	"               /NDL :: No Directory List - don't log directory names.\n"
  1612, 	"                /NP :: No Progress - don't display percentage copied."
  1613, 	"               /ETA :: show Estimated Time of Arrival of copied files.\n"
  1614, 	"          /LOG:file :: output status to LOG file (overwrite existing log)."
  1615, 	"         /LOG+:file :: output status to LOG file (append to existing log).\n"
}

STRINGTABLE
LANGUAGE LANG_ENGLISH, SUBLANG_ENGLISH_US
{
  1616, 	"       /UNILOG:file :: output status to LOG file as UNICODE (overwrite existing log)."
  1617, 	"      /UNILOG+:file :: output status to LOG file as UNICODE (append to existing log).\n"
  1618, 	"               /TEE :: output to console window, as well as the log file.\n"
  1619, 	"               /NJH :: No Job Header."
  1620, 	"               /NJS :: No Job Summary.\n"
  1621, 	"           /UNICODE :: output status as UNICODE.\n"
}

STRINGTABLE
LANGUAGE LANG_ENGLISH, SUBLANG_ENGLISH_US
{
  1701, 	"::\n:: Job Options :\n::"
  1702, 	"       /JOB:jobname :: take parameters from the named JOB file."
  1703, 	"      /SAVE:jobname :: SAVE parameters to the named job file"
  1704, 	"              /QUIT :: QUIT after processing command line (to view parameters). "
  1705, 	"              /NOSD :: NO Source Directory is specified."
  1706, 	"              /NODD :: NO Destination Directory is specified."
  1707, 	"                /IF :: Include the following Files.\n"
}

STRINGTABLE
LANGUAGE LANG_ENGLISH, SUBLANG_ENGLISH_US
{
  1801, 	"                /SD:path :: Source Directory."
  1802, 	"                /DD:path :: Destination Directory."
  1803, 	"::\n:: Remarks :\n::"
  1804, 	"       Using /PURGE or /MIR on the root directory of the volume formerly caused "
  1805, 	"       robocopy to apply the requested operation on files inside the System "
  1806, 	"       Volume Information directory as well. This is no longer the case; if "
  1807, 	"       either is specified, robocopy will skip any files or directories with that "
}

STRINGTABLE
LANGUAGE LANG_ENGLISH, SUBLANG_ENGLISH_US
{
  1808, 	"       name in the top-level source and destination directories of the copy session.\n"
  1809, 	"       The modified files classification applies only when both source "
  1810, 	"       and destination filesystems support change timestamps (e.g., NTFS) "
  1811, 	"       and the source and destination files have different change times but are "
  1812, 	"       otherwise the same. These files are not copied by default; specify /IM "
  1813, 	"       to include them.\n"
  1814, 	"       The /DCOPY:E flag requests that extended attribute copying should be "
  1815, 	"       attempted for directories. Note that currently robocopy will continue "
  1816, 	"       if a directory's EAs could not be copied. This flag is also not included "
  1817, 	"       in /COPYALL.\n"
  1818, 	"       If either /IoMaxSize or /IoRate are specified, robocopy will enable "
  1819, 	"       copy file throttling (the purpose being to reduce system load). "
  1820, 	"       Both may be adjusted to allowable or optimal values; i.e., both "
  1821, 	"       specify desired copy parameters, but the system and robocopy are "
  1822, 	"       allowed to adjust them to reasonable/allowed values as necessary. "
  1823, 	"       If /Threshold is also used, it specifies a minimum file size for "
}

STRINGTABLE
LANGUAGE LANG_ENGLISH, SUBLANG_ENGLISH_US
{
  1824, 	"       engaging throttling; files below that size will not be throttled. "
  1825, 	"       Values for all three parameters may be followed by an optional suffix "
  1826, 	"       character from the set [KMG] (kilo, mega, giga).\n"
  1827, 	"       Using /LFSM requests robocopy to operate in 'low free space mode'. "
  1828, 	"       In this mode, robocopy will pause whenever a file copy would cause the "
  1829, 	"       destination volume's free space to go below a 'floor' value, which "
  1830, 	"       can be explicitly specified by the LFSM:n[KMG] form of the flag. "
  1831, 	"       If /LFSM is specified with no explicit floor value, the floor is set to "
  1832, 	"       ten percent of the destination volume's size. "
  1833, 	"       Low free space mode is incompatible with /MT and /EFSRAW.\n"
}

STRINGTABLE
LANGUAGE LANG_ENGLISH, SUBLANG_ENGLISH_US
{
  4001, 	"\t%s%s%s%s\t"
  4011, 	"\t  %s\t\t%s%s%s\t"
}

STRINGTABLE
LANGUAGE LANG_ENGLISH, SUBLANG_ENGLISH_US
{
  4021, 	"\r%3d.%1d%%"
  4031, 	"\r%3d%%  "
}

STRINGTABLE
LANGUAGE LANG_ENGLISH, SUBLANG_ENGLISH_US
{
  4101, 	"<NOT SET C> "
  4102, 	"  New File  "
  4103, 	"<MISMATCH C>"
  4104, 	"<MISMATCH C>"
  4105, 	"  Newer     "
  4106, 	"  Older     "
  4107, 	"  Changed   "
  4108, 	"  Tweaked   "
  4109, 	"  Modified  "
  4110, 	"  Same      "
  4111, 	"<ATTRIB C>  "
}

STRINGTABLE
LANGUAGE LANG_ENGLISH, SUBLANG_ENGLISH_US
{
  4112, 	"<ARCHIVE C> "
  4113, 	"<NAMED C>   "
  4114, 	"<LARGE C>"
  4115, 	"<SMALL C>"
  4116, 	"<MAXAGE C>"
  4117, 	"<MINAGE C>"
  4118, 	"<JUNCTION C>"
  4119, 	"<IDENTICAL C>"
}

STRINGTABLE
LANGUAGE LANG_ENGLISH, SUBLANG_ENGLISH_US
{
  4201, 	"<NOT SET S> "
  4202, 	"      lonely"
  4203, 	"*MISMATCH   "
  4204, 	"*MISMATCH   "
  4205, 	"       newer"
  4206, 	"       older"
  4207, 	"     changed"
}

STRINGTABLE
LANGUAGE LANG_ENGLISH, SUBLANG_ENGLISH_US
{
  4208, 	"     tweaked"
  4209, 	"    modified"
  4210, 	"        same"
  4211, 	"      attrib"
  4212, 	"     archive"
  4213, 	"       named"
  4214, 	"       large"
  4215, 	"       small"
  4216, 	"     too old"
  4217, 	"     too new"
  4218, 	"    junction"
  4219, 	"<IDENTICAL S>"
}

STRINGTABLE
LANGUAGE LANG_ENGLISH, SUBLANG_ENGLISH_US
{
  4301, 	"<NOT SET W> "
  4302, 	"  New Dir   "
  4303, 	"<MISMATCH W>"
}

STRINGTABLE
LANGUAGE LANG_ENGLISH, SUBLANG_ENGLISH_US
{
  4304, 	"<MISMATCH W>"
  4305, 	"            "
  4306, 	"            "
  4307, 	"            "
  4308, 	"            "
  4309, 	"            "
  4310, 	"            "
  4311, 	"<ATTRIB W>  "
  4312, 	"<ARCHIVE W> "
  4313, 	"   named dir"
  4314, 	"<LARGE W>"
  4315, 	"<SMALL W>"
  4316, 	"<MAXAGE W>"
  4317, 	"<MINAGE W>"
  4318, 	"<JUNCTION W>"
  4319, 	"<IDENTICAL W>"
}

STRINGTABLE
LANGUAGE LANG_ENGLISH, SUBLANG_ENGLISH_US
{
  4401, 	"<NOT SET X> "
  4402, 	"*EXTRA Dir  "
  4403, 	"<MISMATCH X>"
  4404, 	"<MISMATCH X>"
  4405, 	"<NEWER X>   "
  4406, 	"<OLDER X>   "
  4407, 	"<CHANGED X> "
  4408, 	"<TWEAKED X> "
  4409, 	"<MODIFIED X>"
  4410, 	"<SAME X>    "
  4411, 	"<ATTRIB X>  "
  4412, 	"<ARCHIVE X> "
  4413, 	"  *named dir"
  4414, 	"<LARGE X>"
  4415, 	"<SMALL X>"
}

STRINGTABLE
LANGUAGE LANG_ENGLISH, SUBLANG_ENGLISH_US
{
  4416, 	"<MAXAGE X>"
  4417, 	"<MINAGE X>"
  4418, 	"<JUNCTION X>"
  4419, 	"<IDENTICAL X>"
}

STRINGTABLE
LANGUAGE LANG_ENGLISH, SUBLANG_ENGLISH_US
{
  4501, 	"<NOT SET Y> "
  4502, 	"*EXTRA File "
  4503, 	"<MISMATCH Y>"
  4504, 	"<MISMATCH Y>"
  4505, 	"<NEWER Y>   "
  4506, 	"<OLDER Y>   "
  4507, 	"<CHANGED Y> "
  4508, 	"<TWEAKED Y> "
  4509, 	"<MODIFIED Y>"
  4510, 	"<SAME Y>    "
  4511, 	"<ATTRIB Y>  "
}

STRINGTABLE
LANGUAGE LANG_ENGLISH, SUBLANG_ENGLISH_US
{
  4512, 	"<ARCHIVE Y> "
  4513, 	" *named file"
  4514, 	"<LARGE Y>"
  4515, 	"<SMALL Y>"
  4516, 	"<MAXAGE Y>"
  4517, 	"<MINAGE Y>"
  4518, 	"<JUNCTION Y>"
  4519, 	"<IDENTICAL Y>"
}

STRINGTABLE
LANGUAGE LANG_ENGLISH, SUBLANG_ENGLISH_US
{
  4601, 	"<NOT SET F> "
  4602, 	"<LONELY F>  "
  4603, 	"<MISMATCH F>"
  4604, 	"<MISMATCH F>"
  4605, 	"fix    newer"
  4606, 	"fix    older"
  4607, 	"fix  changed"
}

STRINGTABLE
LANGUAGE LANG_ENGLISH, SUBLANG_ENGLISH_US
{
  4608, 	"<TWEAKED F> "
  4609, 	"fix modified"
  4610, 	"fix     same"
  4611, 	"<ATTRIB F>  "
  4612, 	"<ARCHIVE F> "
  4613, 	"<NAMED F>   "
  4614, 	"<LARGE F>   "
  4615, 	"<SMALL F>   "
  4616, 	"<MAXAGE F>  "
  4617, 	"<MINAGE F>  "
  4618, 	"<JUNCTION F>"
  4619, 	"<IDENTICAL F>"
}

STRINGTABLE
LANGUAGE LANG_ENGLISH, SUBLANG_ENGLISH_US
{
  9001, 	"%d/%02d/%02d %02d:%02d:%02d ERROR %d (0x%08X) %s "
}

STRINGTABLE
LANGUAGE LANG_ENGLISH, SUBLANG_ENGLISH_US
{
  9101, 	"Accessing Source Directory"
  9102, 	"Accessing Destination Directory"
  9103, 	"Scanning Source Directory"
}

STRINGTABLE
LANGUAGE LANG_ENGLISH, SUBLANG_ENGLISH_US
{
  9104, 	"Scanning Destination Directory"
  9105, 	"Deleting Source Directory"
  9106, 	"Deleting Destination Directory"
  9107, 	"Creating Destination File"
  9108, 	"Creating Destination Directory"
  9109, 	"Copying File"
  9110, 	"Copying Directory"
  9111, 	"Time-Stamping Destination File"
  9112, 	"Time-Stamping Destination Directory"
  9113, 	"Expanding Source Pathname"
  9114, 	"Expanding Destination Pathname"
  9115, 	"Deleting Source File"
  9116, 	"Changing File Attributes"
  9117, 	"Deleting Extra File"
  9118, 	"Deleting Extra Directory"
  9119, 	"Scanning Extra Directory"
}

STRINGTABLE
LANGUAGE LANG_ENGLISH, SUBLANG_ENGLISH_US
{
  9120, 	"Opening Log File"
  9121, 	"Expanding Log File Pathname"
  9122, 	"Copying NTFS Security to Destination Directory"
  9123, 	"Copying NTFS Security to Destination File"
  9124, 	"Opening Job File"
  9125, 	"Getting File System Type of Source"
  9126, 	"Getting File System Type of Destination"
  9127, 	"Waiting for Changes in Source Directory"
  9128, 	"Getting File Time"
  9129, 	"Copying Directory EAs"
}

```

<hr/>

example: move to %TEMP%

```cmd
@echo off

set "ARGS="
::---------------------------- copy subdirectories, including Empty ones.
set  ARGS=%ARGS%  /E
::---------------------------- copy using unbuffered I/O (recommended for large files).
::set  ARGS=%ARGS%  /J
::---------------------------- copy all file info (equivalent to /COPY:DATSOU).
set  ARGS=%ARGS%  /COPYALL
::---------------------------- MOVE files AND dirs (delete from source after copying).
set  ARGS=%ARGS%  /MOVE
::---------------------------- copy Junctions as junctions instead of as the junction targets.
set  ARGS=%ARGS%  /SJ
::---------------------------- copy Symbolic Links as links instead of as the link targets.
set  ARGS=%ARGS%  /SL
::---------------------------- Do multi-threaded copies with n threads (default 8).
set  ARGS=%ARGS%  /MT:12
::---------------------------- for directories copy the data, attributes, timestamps and without EAs and without alt-data-streams.
set  ARGS=%ARGS%  /DCOPY:DATX
::---------------------------- Request network compression during file transfer, if applicable.
set  ARGS=%ARGS%  /COMPRESS

::---------------------------- Unicode output.
set  ARGS=%ARGS%  /UNICODE 


"%~sdp0robocopy.exe" "%~1" "%TEMP%" "*.*"  %ARGS%
set "EXIT_CODE=%ErrorLevel%"


echo [INFO] EXIT_CODE: %EXIT_CODE%. 1>&2
timeout /t 10  1>&2
exit /b %EXIT_CODE%

```


<hr/>


```cmd
@echo off
::batch file to wrap robocopy.exe with some workarounds. feel free to fix and use (untested). 202603131516.

::-- enable console Unicode charset presentation (you need to set a modern font), does not effect the robocopy.exe itself.
chcp 65001 1>nul 2>nul

::-- fool robocopy.exe to show dates and time in English instead of your native language.
set "LANG=en_US.UTF-8"
set "LANGUAGE=en_US"
set "LC_CTYPE=en_US.UTF-8"
set "LC_NUMERIC=en_US.UTF-8"
set "LC_TIME=en_US.UTF-8"
set "LC_COLLATE=en_US.UTF-8"
set "LC_MONETARY=en_US.UTF-8"
set "LC_MESSAGES=en_US.UTF-8"
set "LC_PAPER=en_US.UTF-8"
set "LC_NAME=en_US.UTF-8"
set "LC_ADDRESS=en_US.UTF-8"
set "LC_TELEPHONE=en_US.UTF-8"
set "LC_MEASUREMENT=en_US.UTF-8"
set "LC_IDENTIFICATION=en_US.UTF-8"
set "LC_ALL=en_US.UTF-8"

::-- change your OS timezone for this run. enable on your own risk! 
::set "TZ=UTC"


::-- get timestamp into a variable.
set "TIMESTAMP="
for /F "usebackq delims=" %%E IN (`powershell -command "Get-Date -Format yyyyMMddhhmmss"`) do ( 
  set "TIMESTAMP=%%E"
  goto EXIT_LOOP__TIMESTAMP
) 
:EXIT_LOOP__TIMESTAMP


pushd "%~sdp0"
set "ROBOCOPY_EXE=%CD%\robocopy.exe"
set "LOG_TXT=%CD%\%TIMESTAMP%__robocopy_log.txt"
popd



::-- fool your OS to take robocopy.exe from this folder, in-case your script uses PATH environment variable.. enable on your own risk!
::set "PATH=%~sdp0;%PATH%"


echo "%ROBOCOPY_EXE%" %*
echo.

::--you can comment the next line, and uncomment the next after it to try to generate a "live log" as it works in the same folder you have "robocopy.cmd" and "robocopy.exe". the first part of the filename is a timestamp such as 202631121705 which automatically sorted.

"%ROBOCOPY_EXE%" %*
::powershell -Command "\"%ROBOCOPY_EXE%\" %* | Tee-Object -Append -FilePath \"%LOG_TXT%\""
set EXIT_CODE=%ERRORLEVEL%


echo. 1>>"%LOG_TXT%"
echo [INFO] EXIT_CODE: %EXIT_CODE%  1>>"%LOG_TXT%"

echo.
echo [INFO] EXIT_CODE: %EXIT_CODE%


timeout /t 5 1>&2

exit /b %EXIT_CODE%

```




