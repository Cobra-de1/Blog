# Setup a simple vm for playing CTF pwn challenges in Ubuntu 20.04

Date: 8/3/2022

### 1) Install VMware Tools (to copy and patse from host)

Link tutorial: [https://linuxhint.com/install_vmware_tools_ubuntu_debian/](https://linuxhint.com/install_vmware_tools_ubuntu_debian/)

### 2) Add i386 architecture (to run 32 bit elf)

```bash
sudo dpkg --add-architecture i386
```

### 3) Install needed libraries

* Update ubuntu

```bash
sudo apt-get update
```

* Install libraries and tools

```bash
sudo apt-get install -y socat build-essential jq strace ltrace curl wget rubygems gcc dnsutils netcat gcc-multilib net-tools gdb gdb-multiarch python python3 python3-pip python3-dev libssl-dev libffi-dev wget git make procps libpcre3-dev libdb-dev libxt-dev libxaw7-dev libc6:i386 libncurses5:i386 libstdc++6:i386 patchelf elfutils nasm vim clang && sudo apt-get install -y python-is-python3
```

* Install python3 libraries

```bash
sudo pip3 install pwntools keystone-engine unicorn capstone ropper
```

### 4) Install vscode

```bash
sudo apt update && \
sudo apt install -y software-properties-common apt-transport-https wget  && \
wget -q https://packages.microsoft.com/keys/microsoft.asc -O- | sudo apt-key add -  && \
sudo add-apt-repository "deb [arch=amd64] https://packages.microsoft.com/repos/vscode stable main"  && \
sudo apt install code
```

### 5) Install tools for pwn

#### +) Create folder for tools

```bash
cd ~/ && \
mkdir Install && \
cd Install
```

#### +) pwndbg (Gdb extension)

```bash
git clone https://github.com/pwndbg/pwndbg.git && \
cd pwndbg && \
./setup.sh && \
cd ../
```

#### +) ROPgadget (Tools for gadget searching)

* It usually install with pwntools, you can pass if you already can use ROPgadget

```bash
git clone https://github.com/JonathanSalwan/ROPgadget.git && \
cd ROPgadget && \
sudo python setup.py install && \
cd ../
```

#### +) one_gadget (Tools for one_gadget searching)

```bash
sudo gem install one_gadget
```

#### +) seccomp-tools (Tools for seccomp)

```bash
sudo apt install -y gcc ruby-dev && \
sudo gem install seccomp-tools
```

#### +) pwninit (Tools for patching libc)

* Download the pwninit binary and copy to `/usr/bin/` to excute as a command in terminal

```bash
mkdir pwninit && \
cd pwninit && \
wget https://github.com/io12/pwninit/releases/download/3.2.0/pwninit && \
chmod +x pwninit && \
sudo cp pwninit /usr/bin/ && \
cd ../
```

#### +) ghidra (Tools for reversing)

* Version 10.1.12 latest in 8/3/2022, check for new version in [ghidra](https://github.com/NationalSecurityAgency/ghidra/releases)

```bash
wget https://github.com/NationalSecurityAgency/ghidra/releases/download/Ghidra_10.1.2_build/ghidra_10.1.2_PUBLIC_20220125.zip && \
unzip ghidra_10.1.2_PUBLIC_20220125.zip && \
rm -rf ghidra_10.1.2_PUBLIC_20220125.zip && \
cd ghidra_10.1.2_PUBLIC && \
sudo ln -s ~/Install/ghidra_10.1.2_PUBLIC/ghidraRun /usr/bin/ghidra && \
cd ../
```

* The command `sudo ln -s ~/Install/ghidra_10.1.2_PUBLIC/ghidraRun /usr/bin/ghidra` just add the symlink from `ghidraRun` to `/usr/bin/ghidra`, so you can open ghidra with command "ghidra" in terminal

* If you cannot run the ghidra, checking the ghidra path again, if it alright, try checking `java` (`jdk` and `jre`)

```bash
sudo apt install -y default-jdk default-jre
```

#### +) radare2

* A disassembler like IDA but in command line

```bash
git clone https://github.com/radare/radare2 && \
cd radare2 && \
sys/install.sh && \
cd ../
```

#### +) libc-database

* Use to find version of libc with offset (more libc than web database)

```bash
git clone https://github.com/niklasb/libc-database && \
cd libc-database && \
./get ubuntu && \
cd ../
```

#### +) docker

* You will need to use docker when you want to setup the same environment with the server

```bash
sudo apt install -y docker.io && \
sudo apt install -y docker-compose
```

#### +) fidra

* Use for dynamic reversing

```bash
pip install frida-tools
```

### 6) Add libc source code to gdb

* Use when you want to debug deep in libc function like (printf, puts, read, ...), i need to setup this when i learned FSOP attack

* Download the glibc source code

```bash
git config --global http.sslverify false && \
git clone https://sourceware.org/git/glibc.git
```

* Setup some scripts for convenient work

```bash
mkdir add_glibc_source && \
cd add_glibc_source && \
touch add_glibc_source.py && \
nano add_glibc_source.py
```

* Copy and patse this code to `add_glibc_source.py`, edit the path `/home/cobra/` to your path ( this is my scripts, sorry if it so noob :)) )

```python
import gdb
import os

def add_all_folder(path):
	gdb.execute('dir ' + path)
	dir = os.listdir(path)
	for i in dir:
		subfolder = path + i + '/'
		if os.path.isdir(subfolder):
			add_all_folder(subfolder)

add_all_folder('/home/cobra/Install/glibc/')
```

* We add this script to `.gdbinit`, this will auto add glibc source code when we start gdb

```bash
echo "source ~/Install/add_glibc_source/add_glibc_source.py" >> ~/.gdbinit
```

* Create another scripts

```bash
touch libc && \
nano libc
```

* Copy and patse this code to `libc`

```bash
#!/bin/sh

cd ~/Install/glibc/
git checkout release/$1/master
```

* Add it to `/usr/bin/` to execute as a command

```bash
chmod +x libc && \
sudo cp libc /usr/bin && \
cd ../
```

* Later if you want to change version of glibc source code, just open the terminal and type `libc + version`, this equal to go to the libc folder and checkout to that version

### 7) Setup qemu

* Use for kernel exploitation or arm compiler-debug

```bash
sudo apt install -y qemu-user qemu-user-static gcc-aarch64-linux-gnu binutils-aarch64-linux-gnu binutils-aarch64-linux-gnu-dbg && \
sudo apt install -y gcc-arm-linux-gnueabihf binutils-arm-linux-gnueabihf binutils-arm-linux-gnueabihf-dbg && \
sudo apt-get -y install qemu-kvm qemu
```

### 8) Finally

*These are most of the tools I use to play CTF pwn challenges, there are still a lot of other great tools that I haven't used yet. I want to share so that newbies can start on the path of pwnable. After the installation is complete, you should take a snapshot or compress and store it, in case an error occurs. Older or newer versions of Ubuntu will likely have some errors, but basically I think there won't be too much change in the way of installation. Last word, hope you will find passion or pleasure playing pwnable, goodluck.*

## Arthor: ????????????????????


