# Table of contents

<!--ts-->
   * [Linux build](#linux)
   * Windows build
     * [Using Arduino](#using-arduino)
     * [Using Linux subsystem](#using-linux-subsystem-under-windows-10-64-bit)
     * [Using Git-bash](#using-git-bash-under-windows-10-64-bit)
   * [Automated tests](#3-automated-tests)
   * [Documentation](#4-documentation)
<!--te-->


# Build
## Linux
Run shell script build.sh to build for MK3 and flash with Sli3er.  
If you have different printel model, follow step [2.b](#2b) from Windows build first.  
If you wish to flash from Arduino, follow step [2.c](#2c) from Windows build first.  

The script downloads Arduino with our modifications and Rambo board support installed, unpacks it into folder PF-build-env-\<version\> on the same level, as your Prusa-Firmware folder is located, builds firmware for MK3 using that Arduino in Prusa-Firmware-build folder on the same level as Prusa-Firmware, runs secondary language support scripts. Firmware with secondary language support is generated in lang subfolder. Use firmware.hex for MK3 variant. Use firmware_\<lang\>.hex for other printers. Don't forget to follow step [2.b](#2b) first for non-MK3 printers.
## Windows
### Using Arduino
note: Multi language build is not supported.
#### 1. Development environment preparation

   a. install `"Arduino Software IDE"` for your preferred operating system  
`https://www.arduino.cc -> Software->Downloads`  
it is recommended to use version `"1.8.5"`, as it is used on out build server to produce official builds.  
_note: in the case of persistent compilation problems, check the version of the currently used C/C++ compiler (GCC) - should be `4.8.1`; version can be verified by entering the command  
`avr-gcc --version`  
if you are not sure where the file is placed (depends on how `"Arduino Software IDE"` was installed), you can use the search feature within the file system_  
_note: name collision for `"LiquidCrystal"` library known from previous versions is now obsolete (so there is no need to delete or rename original file/-s)_

   b. add (`UltiMachine`) `RAMBo` board into the list of Arduino target boards  
`File->Preferences->Settings`  
into text field `"Additional Boards Manager URLs"`  
type location  
`"https://raw.githubusercontent.com/ultimachine/ArduinoAddons/master/package_ultimachine_index.json"`  
or you can 'manually' modify the item  
`"boardsmanager.additional.urls=....."`  
at the file `"preferences.txt"` (this parameter allows you to write a comma-separated list of addresses)  
_note: you can find location of this file on your disk by following way:  
`File->Preferences->Settings`  (`"More preferences can be edited in file ..."`)_  
than do it  
`Tools->Board->BoardsManager`  
from viewed list select an item `"RAMBo"` (will probably be labeled as `"RepRap Arduino-compatible Mother Board (RAMBo) by UltiMachine"`  
_note: select this item for any variant of board used in printers `'Prusa i3 MKx'`, that is for `RAMBo-mini x.y` and `EINSy x.y` to_  
'clicking' the item will display the installation button; select choice `"1.0.1"` from the list(last known version as of the date of issue of this document)  
_(after installation, the item is labeled as `"INSTALLED"` and can then be used for target board selection)_  

   c. modify platform.txt to enable float printf support:  
add "-Wl,-u,vfprintf -lprintf_flt -lm" to "compiler.c.elf.flags=" before existing flag "-Wl,--gc-sections"  
example:  
`"compiler.c.elf.flags=-w -Os -Wl,-u,vfprintf -lprintf_flt -lm -Wl,--gc-sections"`

#### 2. Source code compilation

a. place the source codes corresponding to your printer model obtained from the repository into the selected directory on your disk  
`https://github.com/prusa3d/Prusa-Firmware/`  

b.<a name="2b"></a> In the subdirectory `"Firmware/variants/"` select the configuration file (`.h`) corresponding to your printer model, make copy named `"Configuration_prusa.h"` (or make simple renaming) and copy it into `"Firmware/"` directory.  

c.<a name="2c"></a> In file `"Firmware/config.h"` set LANG_MODE to 0.

run `"Arduino IDE"`; select the file `"Firmware.ino"` from the subdirectory `"Firmware/"` at the location, where you placed the source codes  
`File->Open`  
make the desired code customizations; **all changes are on your own risk!**  

select the target board `"RAMBo"`  
`Tools->Board->RAMBo`  
_note: it is not possible to use any of the variants `"Arduino Mega …"`, even though it is the same MCU_  

run the compilation  
`Sketch->Verify/Compile`  

upload the result code into the connected printer  
`Sketch->Upload`  

or you can also save the output code to the file (in so called `HEX`-format) `"Firmware.ino.rambo.hex"`:  
`Sketch->ExportCompiledBinary`  
and then upload it to the printer using the program `"FirmwareUpdater"`  
_note: this file is created in the directory `"Firmware/"`_  

### Using Linux subsystem under Windows 10 64-bit
_notes: Script and instructions contributed by 3d-gussner. Use at your own risk. Script downloads Arduino executables outside of Prusa control. Report problems [there.](https://github.com/3d-gussner/Prusa-Firmware/issues) Multi language build is supported._
- follow the Microsoft guide https://docs.microsoft.com/en-us/windows/wsl/install-win10
  You can also use the 'prepare_winbuild.ps1' powershell script with Administrator rights
- Tested versions are at this moment
  - Ubuntu other may different
  - After the installation and reboot please open your Ubuntu bash and do following steps
  - run command `apt-get update`
  - to install zip run `apt-get install zip`
  - add few lines at the top of `~/.bashrc` by running `sudo nano ~/.bashrc`
	
	export OS="Linux"
	export JAVA_TOOL_OPTIONS="-Djava.net.preferIPv4Stack=true"
	export GPG_TTY=$(tty)
	
	use `CRTL-X` to close nano and confirm to write the new entries
  - restart Ubuntu bash
Now your Ubuntu subsystem is ready to use the automatic `PF-build.sh` script and compile your firmware correctly

#### Some Tips for Ubuntu
- Linux is case sensetive so please don't forget to use capital letters where needed, like changing to a directory
- To change the path to your Prusa-Firmware location you downloaded and unzipped
  - Example: You files are under `C:\Users\<your-username>\Downloads\Prusa-Firmware-MK3`
  - use under Ubuntu the following command `cd /mnt/c/Users/<your-username>/Downloads/Prusa-Firmware-MK3`
    to change to the right folder

#### Compile Prusa-firmware with Ubuntu Linux subsystem installed
- open Ubuntu bash
- change to your source code folder (case sensitive)
- run `./PF-build.sh`
- follow the instructions

### Using Git-bash under Windows 10 64-bit
_notes: Script and instructions contributed by 3d-gussner. Use at your own risk. Script downloads Arduino executables outside of Prusa control. Report problems [there.](https://github.com/3d-gussner/Prusa-Firmware/issues) Multi language build is supported._
- Download and install the 64bit Git version https://git-scm.com/download/win
- Also follow these instructions https://gist.github.com/evanwill/0207876c3243bbb6863e65ec5dc3f058
- Download and install 7z-zip from its official website https://www.7-zip.org/
  By default, it is installed under the directory /c/Program Files/7-Zip in Windows 10
- Run `Git-Bash` under Administrator privilege
- navigate to the directory /c/Program Files/Git/mingw64/bin
- run `ln -s /c/Program Files/7-Zip/7z.exe zip.exe`

#### Compile Prusa-firmware with Git-bash installed
- open Git-bash
- change to your source code folder
- run `bash PF-build.sh`
- follow the instructions


# 3. Automated tests
## Prerequisites
c++11 compiler e.g. g++ 6.3.1

cmake

build system - ninja or gnu make

## Building
Create folder where you want to build tests.

Example:

`cd ..`

`mkdir Prusa-Firmware-test`

Generate build scripts in target folder.

Example:

`cd Prusa-Firmware-test`

`cmake -G "Eclipse CDT4 - Ninja" ../Prusa-Firmware`

or for DEBUG build:

`cmake -G "Eclipse CDT4 - Ninja" -DCMAKE_BUILD_TYPE=Debug ../Prusa-Firmware`

Build it.

Example:

`ninja`

## Runing
`./tests`

# 4. Documentation
run [doxygen](http://www.doxygen.nl/) in Firmware folder
