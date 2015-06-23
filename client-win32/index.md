# Neubot client Win32

This document describes the rationale, architecture, and implementation
of Neubot for Win32. We will describe the existing mechanism as of Neubot
0.4.16.9 at first, and then we describe the plan to extend this to
support Neubot 0.5.0.0 and beyond.

## The existing mechanism

Here we describe the existing Neubot update mechanism on Windows
as of [Neubot 0.4.16.9](https://github.com/neubot/neubot/tree/0.4.16.9).

The key files to understand this process should be the following:

* [The source file that produces neubotw.exe](https://github.com/neubot/neubot_win32/blob/0.4.16.9/Win32/neubotw) that is interesting because it shows the functionality invoked by Neubot's main() on Windows
* [The file that produces Neubot's installer on Windows](https://github.com/neubot/neubot_win32/blob/0.4.16.9/Win32/neubot.nsi.in) that is recommended reading because it tells you what happen when you install from scratch and when you uninstall (in particular lines 92-108)
* All the files starting with `updater_` in Neubot sources:
  * [updater_runner.py](https://github.com/neubot/neubot/blob/0.4.16.9/neubot/updater_runner.py) that performs the async download of files (we probably want to change this to something sync) and that can be useful to draw the diagram of the interations between the updater client and the server
  * [updater_utils.py](https://github.com/neubot/neubot/blob/0.4.16.9/neubot/updater_utils.py) containing many utility functions
  * [updater_verify.py](https://github.com/neubot/neubot/blob/0.4.16.9/neubot/updater_verify.py) that verifies that stuff downloaded from the server matches the expected digital signature
  * [updater_win32.py](https://github.com/neubot/neubot/blob/0.4.16.9/neubot/updater_win32.py) Windows specific updater routines

It is also useful to see the structure of the [updater directory on Neubot's web server](http://releases.neubot.org/updates/win32/).

It is also useful to read the [specification of how updates work on MacOS](https://github.com/neubot/neubot/blob/0.4.16.9/doc/neubot/updater/unix.txt).
