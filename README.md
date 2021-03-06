#ProcessHider
## Created by M00nRise 

## IMPORTANT NOTE 
This tools is allowed to use only for penetration testing and other white-hat activities. Illegal use is absoloutly frobidden!

## About  
ProcessHider is a post-exploitation tool designed to hide processes from monitoring tools such as Task Manager and Process Explorer,
thus preventing the admins from discovering payload's processes. 
The tool works on both 32 and 64 bit versions, by self detecting the OS version and using the right version of the tool.

ProcessHider is available as a EXE file or as a Powershell script. 
## License 
This tool is under the BSD-3 license:

Copyright (c) 2016, M00nRise

All rights reserved.

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:


The above copyright notice and this permission notice shall be included in
all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT.  IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
THE SOFTWARE.

##  Using ProcessHider 
To build the project, use "build-me.bat" that's in the root directory.
There are two versions of the complete script: EXE and Powershell.

To use the EXE, call the main file, ProcessHider, which is under MainFile\, from cmd, using the following options:
- -i - specify process IDs that you want to hide, seperated by commas (without space!)
- -n - specify process names that you want to hide, seperated by commas (again, no spaces)
- -x - specify monitoring applications you want to avoid, other than defaults (powershell.exe,taskmgr.exe,procexp.exe,procexp64.exe,perfmon.exe)

To use the Powershell, use Powershell\Output\fullScript.ps1, with the same arguments as the exe.

Example usage:

	`ProcessHider -i 5454,3672 -n "chrome.exe,notepad.exe" -x "cmd.exe"`

Note: you need to use at least one of -i or -n. The hider will make sure to hide itself as well.

## External Libraries and dependencies

The project uses the following libraries and projects:

The findvs script is taken from EasyHook: https://easyhook.github.io/

Invoke-ReflectivePEInjection by clymb3r, available at https://github.com/PowerShellMafia/PowerSploit/blob/master/CodeExecution/Invoke-ReflectivePEInjection.ps1

Xgetopt by Hans Dietrich, available at http://www.codeproject.com/Articles/1940/XGetopt-A-Unix-compatible-getopt-for-MFC-and-Win32

NtHookEngine by Daniel Pistelli, available at http://www.codeproject.com/Articles/21414/Powerful-x-x-Mini-Hook-Engine

ReflectiveDLLInjection by Stephen Fewer of Harmony Security, available at https://github.com/stephenfewer/ReflectiveDLLInjection

"Easy way to set up global API hooks" by Sergey Podobry, Apriorit Inc, available at http://www.codeproject.com/Articles/49319/Easy-way-to-set-up-global-API-hooks

There's no dependency on neither those libraries nor VC++ runtime, as they're all linked statically
## Structure and operation 
First, the hider checks whether the OS is 32 bit or 64 bit, and chooses the right version to use.
Then, it launches a daemon, which looks for one of the frobidden monitoring tools. When it finds one - it uses DLL injection
to launch the payload - which hooks the call to NtQuerySystemInformation - the method the OS tools use to enumerate active processes,
and deletes each of the processes specified (and the daemon) from the results.

## Contributions
Contributions are most welcome.
Make sure that when working on the powershell scripts, don't work on the "fullScript" but rather on the "daemon-integration" and "Invoke-ReflectivePEInjectionLite",
as the building script uses these files as sources to the fullScript/

## Issues and TODOs
### Issues:
- [ ] The powershell injector (Invoke-ReflectivePEInjection) is extermly slow. Need to improve this script
- [ ] Sometimes the hider crashed while injecting (looks like while a process is being closed)
- [X] Sometimes powershell crashes after trying to retrive process list using "Get-Process"
- [X] Injecting right when process loads may cause it to crash (especially Process Explorer). Right now it's fixed using a short delay, but a better solution is needed, since the delay is sometimes noticeable.

### TODOS:
- [ ]	Find a way to hook calls to "tasklist.exe" - right now it's too quick (maybe also using different API call?)
- [X]	Build a powershell version of this tool
- [X]	Create more elegant way to pass arguments to the injected DLL
- [X] 	Replace the linked list usage with a c++11 template