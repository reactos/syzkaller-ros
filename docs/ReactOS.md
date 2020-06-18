# ReactOS

Instructions to set up syzkaller for ReactOS.

## Getting ReactOS

1. Download the MSVC(i386) build of ReactOS that supports Syzkaller binaries from Github actions page :
    ```
    https://github.com/reactos/reactos/pull/2930/checks

    DL - https://github.com/reactos/reactos/suites/807181473/artifacts/8775832
    ```
2. Optionally you can build it yourself by cloning the main repo and checking out the GSOC_2020/Syzkaller branch.

    ```
    https://reactos.org/wiki/Building_ReactOS
    ```

## Setting up ReactOS on qemu

1. Create the qemu image using the following command
    ```sh
    qemu-img create -f qcow2 ReactOS.img 8192M
    ```
    Here 8192M is the size of image.

2. Boot and install
    ```
    qemu -L . -m 1024 -cdrom ReactOS.iso -hda ReactOS.img -boot d -localtime -serial file:ReactOS.log
    ```
    Here we are creating a machine with 1Gb of RAM.
---
**NOTE:**
Detailed instructions can be found here : [ReactOS-wiki](https://reactos.org/wiki/QEMU)

---


## Building Syzkaller

1. Clone the fork of Syzkaller that has support for ReactOS.
    ```sh
    git clone https://github.com/reactos/syzkaller
    ```
2. Instal Go version 1.10
    ```
    https://golang.org/dl/
    ```
3. Make a copy of above cloned sources in the go path as follows.
    ```
    $GOPATH/src/github.com/google/syzkaller
    ```
 ---
**NOTE:**
The above step is required since we are using an older commit of current master.

---   
2. Building Syz-Stress.
    ```sh
    cd tools/syz-stress/
    GOOS=windows GOARCH=386 go build -o syz-stress.exe stress.go
    ```
3. Syz-executor  (assumes **cl** cross-compiler is in PATH, preferably MSVC)
    ```
    cl executor\executor_windows.cc -o syz-executor.exe \
    kernel32.lib user32.lib gdi32.lib winspool.lib comdlg32.lib advapi32.lib \
    shell32.lib ole32.lib oleaut32.lib uuid.lib odbc32.lib odbccp32.lib \
    winmm.lib rpcrt4.lib Crypt32.lib imm32.lib Urlmon.lib Oleaut32.lib \
    Winscard.lib Opengl32.lib Mpr.lib Ws2_32.lib Bcrypt.lib Ncrypt.lib \
    Synchronization.lib Shell32.lib Rpcns4.lib Mswsock.lib  Mincore.lib \
    Msimg32.lib RpcRT4.lib Rpcrt4.lib lz32.lib
    ```
 ---
**NOTE:**
Alternatively you an download the latest build of syz-executor form here: 
https://github.com/reactos/syzkaller/actions

--- 
## Running Syzkaller

Copy over Syz-stress and executor to the VM and place them in the same folder. Open a command prompt, change to the directory and use the following command to start fuzzing.
    
    syz-stress.exe -threaded=0

## TODO 

- [ ] Add more descriptions
- [ ] push to syzkaller master
- [ ] Syz-manager 
- [ ] Syzbot