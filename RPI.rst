Raspberry Pi
====================

Please use Raspberry Pi OS Bullseye.  Older OSes are not tested.

Binary wheel
------------

We have published a binary wheel using Raylib in X11 mode.  This *should* install and work on Bullseye
with

::

    python3.9 -m pip install raylib==4.2.1.2

If it doesn't work, or you're not on Bullseye, or you want a newer version,
or if you want to use Raylib in ``PLATFORM_DRM`` mode, you will need to compile your own raylib.
For full instructions on this, see https://github.com/raysan5/raylib/wiki/Working-on-Raspberry-Pi .  If you need help with this ask Raylib.

Compile Raylib from source DRM mode
-----------------------------------

As far as I can tell, Raylib DRM is broken.  It doesn't work on my Pi.  However, if you want to compile it and
give it a go, these are the commands:

::

    git clone https://github.com/raysan5/raylib.git --branch 5.0 --single-branch
    cd raylib
    mkdir build
    rm rf build/*
    cd build
    cmake -DPLATFORM="DRM" -DBUILD_EXAMPLES=OFF -DCUSTOMIZE_BUILD=ON -DSUPPORT_FILEFORMAT_JPG=ON -DSUPPORT_FILEFORMAT_FLAC=ON -DWITH_PIC=ON -DCMAKE_BUILD_TYPE=Release ..
    make
    sudo make install
    
Then have pip compile and install the wheel with some extra linker flags:

::

    sudo apt update
    sudo apt install python3-pip
    pip3 install setuptools
    LDFLAGS="-lgbm -ldrm -lEGL" pip3 install --no-cache-dir --no-binary raylib --upgrade --force-reinstall raylib==5.0.0.0

Compile Raylib from source X11 mode
-----------------------------------

::

    git clone https://github.com/raysan5/raylib.git --branch 5.0 --single-branch
    cd raylib
    mkdir build
    rm -rf build/*
    cd build
    cmake -DPLATFORM="Desktop" -DOPENGL_VERSION=2.1 -DBUILD_EXAMPLES=OFF -DCUSTOMIZE_BUILD=ON -DSUPPORT_FILEFORMAT_JPG=ON -DSUPPORT_FILEFORMAT_FLAC=ON -DWITH_PIC=ON -DCMAKE_BUILD_TYPE=Release ..
    make
    sudo make install

Then have pip compile and install the wheel:

::

    sudo apt update
    sudo apt install python3-pip
    pip3 install setuptools
    pip3 install --no-cache-dir --no-binary raylib --upgrade --force-reinstall raylib==5.0.0.0


.. attention::

    The Broadcom proprietary Open GL ES 2.0 drivers (installed by Raspbian into ``/opt/vc`` and compiled in Raylib
    with ``PLATFORM_RPI``) do not work with Bullseye and have not been tested with the bindings.  They will probably
    require additional linker arguments to be added to ``build.py``.  Suggest you use ``PLATFORM_DRM`` instead.
