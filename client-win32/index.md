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


### `neubotw.exe`

* Start: 
 * If `-k` is addded check for a running instance and tries to kill it.
 * Launch `background_win32.main(['neubot'])` that:
  * Read the databse.
  * Save log in the database.
  * Check if privacy option are ok.
  * Start the APIs.
  * Start `updater_win32.py`.
  * Start `poller.loop()`.
* Status:
 * Check if neubot is runnung and print the status.
* Stop: 
 * Tries to stop neubot.


### `updater_win32`

* check privacy permission
* invoke `update_runner.retrieve_files` if an argument is passed.
 * get the url from `updater_utils`
 * run `deferred.add_callback(self._retrieve_tarball)`:
  * check validity of ctx
  * get tarball uri with `updater_utils.tarball_get_uri(self.system, ctx['vinfo'])`.
  * run `deferred.add_callback(self._process_files)`:
   * validation check. 
   * Save tarball and signature on disk
    * run `updater_utils.tarball_save()`
    * run `updater_utils.signature_save()`
   * Verify signature using OpenSSL
    * `updater_verify.dgst_verify()`
    * `updater_install.install()`
   * run `updater_install.install()`:
    * verify version number with a regexp.
    * create filenames.
    * verify the tarbal.
    * eventual dryrun premature return.
    * exctract the archive.
    * compilation.
    * `.neubot-installed-ok` file is written.
    * sync..
   * run `self.install()` that is empty.
  * run `deferred.add_errback(self._handle_failure)`:
   * log the failure
  * 
  
 * and `errnback(self._handle_failure)` in from `defer.py`
 * 
* launch `poller.loop()`
* 
### `updater_runner`




