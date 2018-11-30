# MonoOniLibrary
Mono on IBM i Library (MONOI) Containing MONO CL Command Wrapper to call .Net applications in PASE from CL, RPG or COBOL applications on IBM i and get return results (STDOUT) from the applications in one of the following formats: IFS file, OUTFILE, job log entries or spool file. 

The main benefit of this wrapper is to be able to integrate .Net applications on-the-fly with standard IBM i job streams.

# Requirements

Mono for IBM i must be installed. Mono for IBM i binary distribution can be downloaded from here:
https://github.com/MonoOni/binarydist

Follow instructions for installing the Mono environment in **/opt/mono**.

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

The following example calls a program named: Sample1.exe from the /mydotnet directory and passes the program 2 parameters.

MONO WORKDIR('/mydotnet')                 
     EXEFILE(Sample1.exe)                   
     ARGS('Parm1 value' 'Parm 2 value')     
     OUTFILE(MONOSTDOUT)                    
     DSPSTDOUT(*NO)                         
     LOGSTDOUT(*YES)                        
     PRTSTDOUT(*NO)                         
     DLTSTDOUT(*YES)                        

# MONO command parms

**WORKDIR** - The IFS location for the .Net program you want to call.

**EXEFILE** - The .Net program you want to call.

**ARGS** - Command line parameters. Up to 40 args can be passed to a .Net program call.

**OUTFILE** - The output file which will receive STDOUT lokking feedback.

**DSPSTDOUT** - Display the outfile contents. Nice when debigging. 

**LOGSTDOUT** - Place STDOUT log entries into the current jobs job log. Use this if you want the log info in the IBM i joblog.

**PRTSTDOUT** - Print STDOUT to a spool file. Use this if you want a spool file of the log output.

**DLTSTDOUT** - This option insures that the STDOUT IFS temp files get cleaned up after processing. All IFS log files get created in the /tmp/mono directory.





