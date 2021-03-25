# Trizbort 

Trizbort is a simple tool used to create maps for interactive fiction.

Please use the [primary repo](https://github.com/JasonLautzenheiser/trizbort)
instead of this one if you're looking for the current version of Trizbort.

# Linux Mono

This fork is my attempt to make Trizbort compile and run more reliably
on Linux.  I'm new to C# & Mono, but I'm hoping to muddle through
sufficiently to get it working, which mostly seems to be figuring out
why the text isn't displaying in the main window and removing some
Windows specific features that throw exceptions or otherwise behave
poorly.

I've enabled issues on this repo to give us a place where we can
discuss any outstanding bugs specifically related to the initial port
and testing of the Linux (and MacOS?) version.  I'll probably use it
to track defects that I haven't been able to fix yet there as well.  I
encourage anyone who tests this build to report any problems that are
present on Linux that are NOT present in Windows here as well.
Problems present in Windows that are not related to Mono/Linux/MacOS
should be reported in the primary repo.  I'll disable the issues again
once we've resolved all of the known issues.

# My build procedure

Not being familar with Mono builds, and seeing a lot of conflicting
information on the Internet, I lacked clear guidance on how to build
Trizbort.  This is what I eventually figured out and it completes with
a "successful" build, although I've hack

First get a sufficiently up-to-date version of Mono.  I'm running
Ubunutu 20.10, and the default version seemed to be too old to work,
because nuget complained that it needed version 2.12.  The xbuild
command also said to run msbuild instead, but there was no msbuild
command.  These commands resulted in bunch of new mono packages for
me:

<code>
$ sudo apt install gnupg ca-certificates
$ sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 \
  --recv-keys 3FA7E0328081BFF6A14DA29AA6A19B38D3D831EF
$ echo "deb [arch=amd64] https://download.mono-project.com/repo/ubuntu stable-focal main" \
  | sudo tee /etc/apt/sources.list.d/mono-official-stable.list
$ sudo apt update
$ sudo apt upgrade
</code>

Then clone the repo:

<code>
$ git clone https://github.com/cfcohen/trizbort Trizbort
$ cd Trizbort
$ git checkout linux
</code>

Install the required packages using the new version of nuget:

<code>
$ mkdir packages
$ cd packages
$ nuget install CommandLineParser -Version 2.6.0
$ nuget install Newtonsoft.Json -Version 12.0.2
$ nuget install PDFsharp-gdi -Version 1.50.5147
$ nuget install NUnit -Version 3.12
$ nuget install NUnit3TestAdapter -Version 3.17.0
$ nuget install Shouldly -Version 3.0.2.0
</code>

Build once to create the obj/x86/Debug directory:

<code>
$ cd ..
$ msbuild
</code>

This is the best hack I've found so far to cause the buld to run
to completion.

<code>
$ touch obj/x86/Debug/Trizbort.exe.manifest
$ touch obj/x86/Debug/Trizbort.application
$ msbuild
</code>

I'm not sure why there's not a Trizbort.exe.manifest or
Trizbort.application in the upstream repo.  The msbuild command seems
to require them, but obviously Jason seems to be building fine without
them in Windows.  If anyone knows why these are required under Linux
and what to do about them, please let me know.

Finally, you should be able to run Trizbort with:

<code>
$ mono ./bin/Debug/Trizbort.exe
</code>