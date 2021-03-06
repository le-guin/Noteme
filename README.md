# Noteme
Noteme is an ELF packer/crypter that aims to create hardened and stealthy troyans.
The target can either be a shared object or an executable, but it's required to be 
x86_64 compatible. The payload can be a shellcode or a statically linked ELF binary.

Bear in mind that Noteme is still at a very early state of development. Therefore, only
the userland execve() has been implemented yet.

## PT_NOTE Infection
If the payload fits inside the PT_NOTE segment, replace the content. On the
other hand, if the payload is bigger, append it as a new PT_LOAD segment and reference it
from the PT_NOTE's program header entry. In either case, the same redirection technique will 
be applied: overwrite frame_dummy's references. If required, a stub will be injected (Text segment padding injection) too.

This kind of technique is ideal for long term persistence due to its stealthiness and
simplicity. 

## Prerequisites
```
nasm (Netwide Assembler)
gcc & make
```
## ELF Payloads
Right now, C and Go payloads have been successfully tested, with the only 
requirement being that the binary's base virtual address must be set to a high value (to avoid page conflict).  

C payloads:
```
# regular gcc won't work
$ musl-gcc -static -Wl,--section-start=.init=0xA000000 -o mypayload mypayload.c 
```
Go payloads:
```
$ go tool compile -o mypayload.a -pack mypayload.go
$ go tool link -o mypayload -T 0xF000000000 mypayload.a 
```

## Install
``` 
git clone https://github.com/0xN3utr0n/Noteme.git
cd Noteme
make 
```

## Usage
```
Usage: noteme [-opt] 

Options ('<>' required fields):

     	-p '<filepath>' Payload (Statically linked ELF or Shellcode) binary
     	-t '<filepath>' Target ELF binary
     	-o '<filepath>' Output filename
     	-T Run the payload in a independent thread
     	-h This help
```
**Important:** The thread option is only recommended for ELF payloads.

## TODO
* ARM support
* <del> ELF payload support </del>
* Binary compression support
* Binary encryption support
* Anti-reverse engineering support 
