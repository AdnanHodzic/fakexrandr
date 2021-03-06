FakeXRandR
==========

This is a counterpart to fakexinerama, but for XRandR. It hooks into libXrandr
and replaces a certain, configurable monitor configuration with two virtual
monitors, each of half the original's size.

Use cases
---------

You'll want to use this library if you have a multi-head setup, but a crappy
video driver which tells RandR that there is only one big monitor, resulting in
wrong window placement by window managers. Matrox Tripple Head 2 Go et al. are
other candidates, where there really is only one big monitor, but you'd want to
split it anyway.

With slight modifications, this library is also suited for developers willing to
test multi-head behaviour without multiple monitors. Keep in mind that this library
right now can not do more than split the monitor vertically in half.

HowTo Use
----------

First make sure you have XRandR and X11 development packages installed. 

On Debian you can install these by running `apt-get install x11-xserver-utils libxrandr-dev`


After getting the sources, please add following to your: `~/.bashrc`

```
alias fakexr-run='sudo ~/Documents/fakexrandr/run'
alias fakexr-rm='sudo ~/Documents/fakexrandr/remove'
alias fakexr-apply='~/Documents/fakexrandr/apply'
```

Notice: Please make sure your alias path points to your fakexrandr source location!. 


Then run `source ~/bashrc` to apply the changes. 

Now you're able to run following commands:

`fakexr-run` - library configuration and installation

`fake-rm` - library removal

`fakexr-apply` - run to apply changes after library installation/removal.

Manual Installation
-------------------

In most cases, simply run `make`, then install using `make install`. 
Notice: Please run both commands as root. 

This will create a configuration which splits a monitor with the largest possible
resolution that `xrandr` outputs at compile time into two virtual monitors. Pay
attention to any warnings/errors from the configure script. To compile the
library, you will need the XRandR and X11 development packages for your
distribution.

If you need FakeXRandR for another use case or the automated building does not
work for you, here are some details:

The `configure` script runs `xrandr` and creates a `config.h` header with the
resolution of the monitor to split, the path to the system's real `libXrandr.so`
file and a path which preceeds that of the real library in the ld search path,
where the FakeXRandR should be placed. You can use `ldconfig -v` to get a list
of suitable directories, if `configure` should fail to determine one.

The `libXrandr.c` file only contains a initialization function which loads the
symbols from the real library and implementations of the functions that we
actually override and which require more than replacement of XIDs for fake
screens with real the one's. All other functions are automatically generated
by `make_skeleton.py` from the default Xrandr header file.

Manual Removal
---------------

Run "remove" script as root, i.e: `./remove` 

Log out/log in to apply changes.


Applying changes
-----------------

After both installation and removal, you need to apply changes.

If you're not using `alias` which points to `./apply` script, you can run this script manually. Currently, this script only supports GNOME Shell. 

If you don't want to use `apply` script, you can always log in/out to apply the changes, or restart your X11 server.

FAQ
---

* **How can I see if it's working?**<br/>
  Run `ldd xrandr`. `libXrandr.so` should show up in `/usr/local/lib`. Then,
  start `xrandr`. The screen which is set to the resolution supplied in
  `config.h` should show up twice, with the last character in the name of the
  duplicate replaced by an underscore. After you restarted your X11 session,
  fullscreening applications should fullscreen to the virtual screen, not the
  physical one.


Licensing
---------

You may use, redistribute and modify this program under the terms of the GNU
General Public License (GPL) Version 3, in the version available under the URL
http://www.gnu.org/licenses/gpl.html#.
