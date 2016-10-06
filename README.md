# Installation Guide

This guide uses Debian Linux. Similar instructions will also work on Ubuntu.

**Note**: If using Debian, please enable `sudo` in your account.

##**Section 1**: Installing OSMOCOM-BB and it's dependencies.

###**1.1** Set up `libosmocore`.

libosmocore is a library with various utility functions that were originally developed as part of the OpenBSC project, but which are of a more generic nature and thus useful to (at least) other programs that we develop in the sphere of Free Software / Open Source mobile communications.

**1.1.1** Install dependencies for compiling `libosmocore` on your PC:

    sudo apt-get install build-essential libtool libtalloc-dev shtool autoconf automake git-core pkg-config make gcc libpcsclite-dev

**1.1.2** Clone the `libosmocore` Git repository in your home folder:

    cd ~
    git clone git://git.osmocom.org/libosmocore.git

**1.1.3** To compile and install it:

    cd ~/libosmocore/
    autoreconf -i
    ./configure
    make
    sudo make install
    sudo ldconfig -i

###**1.2** Install the GNU ARM Toolchain

**1.2.1** Download the build script and give it permission to run:

    cd ~
    mkdir ARM
    cd ARM
    wget https://osmocom.org/attachments/download/2052/gnu-arm-build.3.sh
    chmod +x gnu-arm-build.3.sh

**1.2.2** Install the dependencies:

    sudo apt-get install build-essential libgmp3-dev libmpfr-dev libx11-6 libx11-dev texinfo flex bison libncurses5 libncurses5-dbg libncurses5-dev libncursesw5 libncursesw5-dbg libncursesw5-dev zlibc zlib1g-dev libmpfr4 libmpc-dev

**1.2.3** Download the source files to build:

    mkdir build install src
    cd src
    wget http://ftp.gnu.org/gnu/gcc/gcc-4.8.2/gcc-4.8.2.tar.bz2
    wget http://ftp.gnu.org/gnu/binutils/binutils-2.21.1a.tar.bz2
    wget http://netix.dl.sourceforge.net/project/devkitpro/sources/newlib/newlib-1.19.0.tar.gz

**1.2.4** Build the source files using the script:

    cd ..
    ./gnu-arm-build.3.sh

You will see the following:

    I will build an arm-none-eabi cross-compiler:
    
      Prefix: <YOURPATH>/install
      Sources: <YOURPATH>/src
      Build files: <YOURPATH>/build

    Press ^C now if you do NOT want to do this.

***Save `<YOURPATH>` in a text file! You'll need this later.***

Press **ENTER**.

**1.2.5** Add the PATH of the compiled toolchain to your path. Open `~/.bashrc` in GEdit or any text editor and paste the following line at the absolute end:

    export PATH=$PATH:<YOURPATH>/install/bin

***Note: replace `<YOURPATH>` with the path you saved earlier! If you forgot, run the script again and press CTRL+C to exit without running it.***

**1.2.6** To check if the toolchain has been successfully added to your path, type the following in a terminal. **You need to open and close your terminal for it to show up!**

    echo $PATH

If you see the toolchain path, it is installed.

###**1.3** Install OSMOCOM-BB

**1.3.1** Install dependencies

    sudo apt-get install libtool shtool automake autoconf git-core pkg-config make gcc

**1.3.2** Clone OSMOCOM from Git:

    cd ~
    git clone https://github.com/osmocom/osmocom-bb.git

**1.3.3** Make some necessary changes to your files before compiling

There are two files that you need to change or you'll be faced with errors later. Here are the files:

***osmocom-bb\src\target\firmware\Makefile***:

Find these two lines:

    # Uncomment this line if you want to enable Tx (Transmit) Support.
    #CFLAGS += -DCONFIG_TX_ENABLE

Uncomment the second line as suggested by the first!

then in

***osmocom-bb\src\host\layer23\src\mobile\app_mobile.c***:

Comment out the block of code from line 401-410.

**1.3.4** Compile the OSMOCOM-BB firmware:

    cd ~/osmocom-bb/src
    sudo make

If all goes well and there are no errors, congratulations, you have successfully obtained a working OSMOCOM firmware binary in your `osmocom-bb` folder! You do not need to `make install` since we will be running it from the command line.

##Section 2: Making a call

**2.1** Start the Layer 1 program `osmocon` to communicate with your USB port:

    cd ~/osmocom-bb/src/host/osmocon
    sudo ./osmocon -p /dev/ttyUSB0 -m c123xor ../../target/firmware/board/compal_e88/layer1.compalram.bin

**2.2** *In a separate terminal window*, start the layer23 application:

    cd ~/osmocom-bb/src/host/layer23/src/mobile
    sudo ./mobile -i 127.0.0.1

**2.3** *And again in a third terminal window*, issue:

    telnet localhost 4247
    
**2.4** Send a call command through the `telnet` window:

    enable
    call 1 +92XXXXXXXXXX
    
Replace the last argument with your own phone number. That's it

##Section 3: Errors encountered

In case you have to recompile and run into errors, do it as root (using `su`) and not using `sudo`. As root, issue the following commands:

     make distclean && make


## References
https://osmocom.org/projects/baseband/wiki/GnuArmToolchain  
https://osmocom.org/projects/baseband/wiki/SoftwareGettingStarted  
https://osmocom.org/projects/libosmocore/wiki/Libosmocore  
