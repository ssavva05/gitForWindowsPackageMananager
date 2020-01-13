# gitForWindowsPackageMananager

There seems to be a documented way to do this without having to install the Git for Windows SDK (which is very large). I was given the link to this info by PhilipOakley when I asked about all this on GitHub issue #1912.

Here's the current text of the Git for Windows GitHub wiki page about it:

Install inside MSYS2 proper

This guide assumes that you want the 64-bit version of Git for Windows.

Git for Windows being based on MSYS2, it's possible to install the git package into an existing MSYS2 installation. That means that if you are already using MSYS2 on your computer, you can use Git for Windows without running the full installer or using the portable version.

Note however that there are some caveats for going this way. Git for Windows created some patches for msys2-runtime that have not been sent upstream. (This had been planned, but it was determined in issue #284 that it would probably not be happening.) This means that you have to install Git for Windows customized msys2-runtime to have a fully working git inside MSYS2.

Here the steps to take:

Open an MSYS2 terminal.

Edit /etc/pacman.conf and just before [mingw32] (line #71 on my machine), add the git-for-windows packages repository:

[git-for-windows]
  Server = https://wingit.blob.core.windows.net/x86-64

and optionally also the MINGW-only repository for the opposite architecture (i.e. MINGW32 for 64-bit SDK):

[git-for-windows-mingw32]
  Server = https://wingit.blob.core.windows.net/i686

Authorize signing key (this step may have to be repeated occasionally until https://github.com/msys2/msys2/issues/62 is fixed)
curl -L https://raw.githubusercontent.com/git-for-windows/build-extra/master/git-for-windows-keyring/git-for-windows.gpg |
  pacman-key --add - &&
  pacman-key --lsign-key 1A9F3986

Then synchronize new repository
pacboy update

This updates msys2-runtime and therefore will ask you to close the window (not just exit the pacman process). Don't panic, simply close all currently open MSYS2 shells and MSYS2 programs. Double-check Task Manager and kill pacman.exe it's still running after the window is closed, because it can linger. Once all are closed, start a new terminal again.

Then synchronize again (updating the non-core part of the packages):

pacboy update

And finally install the Git/cURL packages:
pacboy sync git:x git-doc-html:x git-doc-man:x git-extra: curl:x

Finally, check that everything went well by doing git --version in a MINGW64 shell and it should output something like git version 2.14.1.windows.1 (or newer).