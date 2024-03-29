libdrm - userspace library for drm
----------------------------------

This is libdrm, a userspace library for accessing the DRM, direct rendering
manager, on Linux, BSD and other operating systems that support the ioctl
interface.
The library provides wrapper functions for the ioctls to avoid exposing the
kernel interface directly, and for chipsets with drm memory manager, support
for tracking relocations and buffers.
New functionality in the kernel DRM drivers typically requires a new libdrm,
but a new libdrm will always work with an older kernel.

libdrm is a low-level library, typically used by graphics drivers such as
the Mesa drivers, the X drivers, libva and similar projects.

Syncing with the Linux kernel headers
-------------------------------------

The library should be regularly updated to match the recent changes in the
`include/uapi/drm/`.

libdrm maintains a human-readable version for the token format modifier, with
the simpler ones being extracted automatically from `drm_fourcc.h` header file
with the help of a python script.  This might not always possible, as some of
the vendors require decoding/extracting them programmatically.  For that
reason one can enhance the current vendor functions to include/provide the
newly added token formats, or, in case there's no such decoding
function, to add one that performs the tasks of extracting them.

For simpler format modifier tokens there's a script (gen_table_fourcc.py) that
creates a static table, by going over `drm_fourcc.h` header file. The script
could be further modified if it can't handle new (simpler) token format
modifiers instead of the generated static table.

Compiling
---------

To set up meson:

    meson builddir/

By default this will install into /usr/local, you can change your prefix
with --prefix=/usr (or `meson configure builddir/ -Dprefix=/usr` after 
the initial meson setup).

Then use ninja to build and install:

    ninja -C builddir/ install

If you are installing into a system location you will need to run install
separately, and as root.

libdrm porting information
---------

Usually the drm-intel is ported from external/libdrm in Celadon, but mesa 22.0.3
requires libdrm 2.4.109 while Celadon only has external/libdrm 2.4.104, so we
have to port libdrm from AOSP master branch to meet the version requirement:

1.Get the libdrm in AOSP master branch by below, which has already included
required Android patch from google to make sure it is workable on Android:

    git clone https://android.googlesource.com/platform/external/libdrm

2.Apply our internal/upstream libdrm patches. Internal patches usually are our
Celadon fix on libdrm, and upstream patches are usually ported from upstream
to fix some issues. We need to verify whether need to apply these patches or not.
For internal patch, we need to check if same issue can be reproduced without it.
For upstream patch, we need to check if target version includes it.

3.Run CTS test with module CtsDrmTestCases, which needs to pass.

4.Merge target libdrm to Celadon repo.
