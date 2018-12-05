# MonoOniLibrary
Mono on IBM i Library (MONOI) Containing MONO CL Command Wrapper to call .Net applications in PASE from CL, RPG or COBOL applications on IBM i and get return results (STDOUT) from the applications in one of the following formats: IFS file, OUTFILE, job log entries or spool file. 

The main benefit of this wrapper is to be able to integrate .Net applications on-the-fly with standard IBM i job streams.

# Requirements for using the MONO CL Commands

The Mono runtime environment must be installed on your IBM i. This can be done via downloading a binary save file or by using a custom Yum repository to install via the IBM i Access Client Solutions (ACS) Client **Open Source Package Management** option from the Tools menu.

**Installing via Save File Distribution**

Mono for IBM i must be installed. Mono for IBM i binary save file distribution can be downloaded from here:
https://github.com/MonoOni/binarydist

Follow instructions for installing the Mono environment in **/opt/mono**.

**Installing via Yum Repository**

On the IBM i Manually create the following repository file via the EDTF command or create it via Windows notepad and upload to the IFS location for the Yum repositories:

Repo file name: **/QOpenSys/etc/yum/repos.d/qsecofr.repo**

The contents of the repo file should be:

`[qsecofr]`<br/>
`name=QSECOFR IBM i RPM Repo`<br/>
`baseurl=http://repo.qseco.fr`<br/>
`enabled=1`<br/>
`gpgcheck=0`<br/>

After creating the new repository, you can launch the **Open Source Package Management** utility in IBM ACS Client from the Tools menu and you'll see the new **Available Packages**. Select **mono-complete** and click the install option. This should complete the Mono install to the **/QopenSys/pkgs** folder location for apps installed by Yum on IBM i.

# Installing MONOI library and creating MONO command objects

Download the **monoi.savf** save file from the selected releases page. 

https://github.com/richardschoen/MonoOniLibrary/releases

Upload the **monoi.savf** to the IFS and place it in **/tmp/monoi.savf**

Run the following commands to copy the save file from the IFS into a SAVF object

`CRTSAVF FILE(QGPL/MONOI)`
 
`CPYFRMSTMF FROMSTMF('/tmp/monoi.savf') TOMBR('/qsys.lib/qgpl.lib/monoi.file') MBROPT(*REPLACE) CVTDTA(*NONE)`

Restore the MONOI library

`RSTLIB SAVLIB(MONOI) DEV(*SAVF) SAVF(QGPL/MONOI)`

Build the MONOI commands

`ADDLIBLE MONOI`

`CRTCLPGM PGM(MONOI/SRCBLDC) SRCFILE(MONOI/SOURCE) SRCMBR(SRCBLDC) REPLACE(*YES)`

`CALL PGM(MONOI/SRCBLDC)`

# Using the MONO CL command to call a .Net console or other application

The following example calls a program named: Sample1.exe from the /mydotnet directory and passes the program 2 parameters individually vi the MONO CL command. See the MONO2 command if you want to pass all parms in a single parameter value instead. More of a preference thing.

MONO WORKDIR('/mydotnet')                 
     EXEFILE(Sample1.exe)                   
     ARGS('Parm1 value' 'Parm 2 value')     
     OUTFILE(MONOSTDOUT)                    
     MONOPATH('/QOpenSys/pkgs/bin')
     DSPSTDOUT(*NO)                         
     LOGSTDOUT(*YES)                        
     PRTSTDOUT(*NO)                         
     DLTSTDOUT(*YES)                        

# MONO command parms

**WORKDIR** - The IFS location for the .Net program you want to call.

**EXEFILE** - The .Net program you want to call.

**ARGS** - Command line parameters. Up to 40 args can be passed to a .Net program call. Do NOT put double quotes around parms or your program call may get errors because your parameters get compromised with double quotes. Double quotes are added automatically. Although single quotes are allowed around your data:  Ex: **'My Parm Value'**

**OUTFILE** - The output file which will receive STDOUT lokking feedback.

**MONOPATH** - This is the location of the Mono binary.  The mono executable runs your .Net program code.
Specify **/QOpenSys/pkgs/bin** if you loaded Mono via the Yum repository. (Default)
Specify **/opt/mono/bin** if you loaded Mono via the save file from the following link: https://github.com/MonoOni/binarydist 

**DSPSTDOUT** - Display the outfile contents. Nice when debigging. 

**LOGSTDOUT** - Place STDOUT log entries into the current jobs job log. Use this if you want the log info in the IBM i joblog.

**PRTSTDOUT** - Print STDOUT to a spool file. Use this if you want a spool file of the log output.

**DLTSTDOUT** - This option insures that the STDOUT IFS temp files get cleaned up after processing. All IFS log files get created in the /tmp/mono directory.


# Using the MONO2 CL command to call a .Net console or other application

The following example calls a program named: Sample1.exe from the /mydotnet directory and passes the program 2 parameters all in a single parameter field delimited by double quotes via the MONO2 CL command. See the MONO command if you want to pass each parameter individually without specifying double quotes. More of a preference thing.

The following example calls a program named: Sample1.exe from the /mydotnet directory and passes the program 2 parameters.

MONO2 WORKDIR('/mydotnet')                 
     EXEFILE(Sample1.exe)                   
     ARGS('"Parm1 value" "Parm 2 value"')     
     OUTFILE(MONOSTDOUT)                    
     MONOPATH('/QOpenSys/pkgs/bin')
     DSPSTDOUT(*NO)                         
     LOGSTDOUT(*YES)                        
     PRTSTDOUT(*NO)                         
     DLTSTDOUT(*YES)                        

# MONO2 command parms

**WORKDIR** - The IFS location for the .Net program you want to call.

**EXEFILE** - The .Net program you want to call.

**ARGS** - Command line parameters. A single command line string where each parameter is delimited by double quotes. Double quotes are automatically removed by the .Net program when it processes the arguments. Ex: **'"My Parm Value1" "My Parm Value2"'** Notice the entire command line value is surrounded by single quotes and each parm is delimited with double quotes.

**OUTFILE** - The output file which will receive STDOUT lokking feedback.

**MONOPATH** - This is the location of the Mono binary executable. The mono executable runs your .Net program code. 
Specify **/QOpenSys/pkgs/bin** if you loaded Mono via the Yum repository. (Default)
Specify **/opt/mono/bin** if you loaded Mono via the save file from the following link: https://github.com/MonoOni/binarydist 

**DSPSTDOUT** - Display the outfile contents. Nice when debigging. 

**LOGSTDOUT** - Place STDOUT log entries into the current jobs job log. Use this if you want the log info in the IBM i joblog.

**PRTSTDOUT** - Print STDOUT to a spool file. Use this if you want a spool file of the log output.

**DLTSTDOUT** - This option insures that the STDOUT IFS temp files get cleaned up after processing. All IFS log files get created in the /tmp/mono directory.


