# termux-elf-cleaner
Utility for Android ELF files to remove unused parts that the linker warns about.

# Description
When loading ELF files, the Android linker warns about unsupported dynamic section entries with warnings such as:

    WARNING: linker: /data/data/org.kost.nmap.android.networkmapper/bin/nmap: unused DT entry: type 0x6ffffffe arg 0x8a7d4
    WARNING: linker: /data/data/org.kost.nmap.android.networkmapper/bin/nmap: unused DT entry: type 0x6fffffff arg 0x3

This utility strips away the following dynamic section entries:

- `DT_RPATH` - not supported in any Android version.
- `DT_VERDEF` - supported from Android 6.0.
- `DT_VERDEFNUM` - supported from Android 6.0.
- `DT_VERNEEDED` - supported from Android 6.0.
- `DT_VERNEEDNUM` - supported from Android 6.0.
- `DT_VERSYM` - supported from Android 6.0.

It also removes the three ELF sections of type:

- `SHT_GNU_verdef`
- `SHT_GNU_verneed`
- `SHT_GNU_versym`

It no longer strips:

- `DT_RUNPATH` - supported from Android 7.0.

as Termux uses RUNPATH to hardcode /usr/lib into ELF files. Termux was
previously setting LD_LIBRARY_PATH=/usr/lib, which had the ugly consequence
that libraries such as /usr/lib/liblzma.so (Termux) clashed with
/system/lib64/liblzma.so (Android), resulting in native Android programs
(am, pm) being unable to link properly when LD_LIBRARY_PATH contained the
Termux /usr/lib.
With this fix, setting LD_LIBRARY_PATH is no longer needed.

# Usage
```sh
usage: termux-elf-cleaner <filenames>

Processes ELF files to remove unsupported section types and
dynamic section entries which the Android linker warns about.
```

# Author
Fredrik Fornwall ([@fornwall](https://github.com/fornwall)).
