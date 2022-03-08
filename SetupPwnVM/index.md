1) Install VMware Tools (to copy and patse from host)

2) Add i386 architecture (to run 32 bit elf)
sudo dpkg --add-architecture i386

3) Update and install needed library
sudo apt-get update && apt-get install -y socat && \
apt-get install -y build-essential jq strace ltrace curl wget rubygems gcc dnsutils netcat gcc-multilib net-tools gdb gdb-multiarch python python3 python3-pip python3-dev libssl-dev libffi-dev wget git make procps libpcre3-dev libdb-dev libxt-dev libxaw7-dev libc6:i386 libncurses5:i386 libstdc++6:i386 patchelf elfutils python-is-python3 nasm vim && \
pip3 install pwntools keystone-engine unicorn capstone ropper

4) Install vscode
sudo apt update
sudo apt install -y software-properties-common apt-transport-https wget
wget -q https://packages.microsoft.com/keys/microsoft.asc -O- | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://packages.microsoft.com/repos/vscode stable main"
sudo apt install code

5) Install tools for pwn

+) Create folder for tools
cd ~/
mkdir Install
cd Install

+) pwndbg (gdb extension)
git clone https://github.com/pwndbg/pwndbg.git
cd pwndbg
./setup.sh
cd ../

+) ROPgadget (pass if it already install pwntools)
git clone https://github.com/JonathanSalwan/ROPgadget.git
cd ROPgadget
sudo python setup.py install
cd ../

+) one_gadget
sudo gem install one_gadget

+) seccomp-tools
sudo apt install -y gcc ruby-dev
sudo gem install seccomp-tools

+) pwninit
mkdir pwninit
cd pwninit
wget https://github.com/io12/pwninit/releases/download/3.2.0/pwninit
chmod +x pwninit
sudo cp pwninit /usr/bin/
cd ../

+) ghidra (version 10.1.12 latest in 27/1/2022)
wget https://github.com/NationalSecurityAgency/ghidra/releases/download/Ghidra_10.1.2_build/ghidra_10.1.2_PUBLIC_20220125.zip
unzip ghidra_10.1.2_PUBLIC_20220125.zip
rm -rf ghidra_10.1.2_PUBLIC_20220125.zip
cd ghidra_10.1.2_PUBLIC
sudo ln -s ~/Install/ghidra_10.1.2_PUBLIC/ghidraRun /usr/bin/ghidra
cd ../

+) radare2
git clone https://github.com/radare/radare2
cd radare2
sys/install.sh
cd ../

+) libc-database
git clone https://github.com/niklasb/libc-database

+) docker
sudo apt install -y docker.io

6) Add libc source code to gdb
git config --global http.sslverify false
git clone https://sourceware.org/git/glibc.git
mkdir add_glibc_source
cd add_glibc_source
touch add_glibc_source.py
nano add_glibc_source.py

copy patse this code to automatic add glibc source code when start gdb
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

echo "source ~/Install/add_glibc_source/add_glibc_source.py" >> ~/.gdbinit
touch libc
nano libc

copy patse this code

```bash
#!/bin/sh

cd ~/Install/glibc/
git checkout release/$1/master
```

chmod +x libc
sudo cp libc /usr/bin
cd ../

later if you want to change version of glibc source, just open the terminal and type "libc + version", this equal to go to the libc folder and checkout to that version

7) Setup qemu for kernel exploitation or arm compiler-debug
sudo apt install -y qemu-user qemu-user-static gcc-aarch64-linux-gnu binutils-aarch64-linux-gnu binutils-aarch64-linux-gnu-dbg
sudo apt install -y gcc-arm-linux-gnueabihf binutils-arm-linux-gnueabihf binutils-arm-linux-gnueabihf-dbg
sudo apt-get -y install qemu-kvm qemu






