# x86devirt

This is a project that aims to devirtualize & disassemble applications that
have been virtualized using x86virt.

It is a reverse engineering project and all implementation seen here was
reverse engineered out of a protected binary file / crackme hosted on
tuts4you.com at the following address: https://tuts4you.com/download/1850/

x86 Virtualizer is an open source project located at the following URL:
https://github.com/rwfpl/rewolf-x86-virtualizer

Since this project aims to build practical experience reverse engineering
applications protected using a VM, the open source project was never
referenced during the development of this project and all implementation is
based entirely on what has been reverse engineered out of protected executables.

# Credits / Dependencies
The devirtualizer on this repository was developed independently by myself (Jeremy Wildsmith) with the help of the following tools / libraries:

- The virtualizer / packer that this tool aims to counter was written by ReWolf. (https://github.com/rwfpl/rewolf-x86-virtualizer)
- Angr is used to symbolically execute the jump decoder and extract the jump mappings. (http://angr.io/)
- Distorm3 is used throughout (https://github.com/gdabah/distorm)
- udis86 is used in the disassembler engine to disassemble instructions that decode into an x86 form (https://github.com/vmt/udis86)
- x64dbg Debugger is used as the debugging engine to analyze the target. (https://github.com/x64dbg/x64dbg)
- VirusTotal/yara-python is used to match signatures (for instruction handlers, the VM stub etc...) (https://github.com/gdabah/distorm)
- nasm assembler is used to assemble the x86 output from the x86virt-disassembler utility

# Documentation / Feedback

Please e-mail any feedback or questions to jeremywildsmith (th!s i$ an at symb0l) Y(eah)(w)hoo d0t ca

I haven't had time to document this thoroughly and there is a lot going on inside the application, but below is a basic overview:

There are basically three main components in the dist:
1. x86devirt.py - The job of this script is to find the VM Interpreter, the virtualized subroutines, the VM Instruction Handlers and to get the jmp  mappings using x86devirt_jmp.py. It also finds what opcodes map to what handlers (Since the opcode mappings and jmp mappings are randomized for every binary protected [and every vm layer]). The instructions are also encrypted and decrypted using a random algorithm (generated per protected binary / layer) so this also finds the decryption routine and dumps it for use by x86virt-disasm. This is the script you run from x64dbg to devirtualize the application. All other components are invoked automatically by this component. This is the only component you need to directly invoke.

2. x64dbgpy_jmp.py - This basically takes a blob (or dump) of the jmp decoder in the protected binary's vm and runs an angr simulation to map the jumps to something x86virt-disasm can understand. This needs to be done because the jmp decoders are randomized per binary / vm layer

3. x86virt-disasm.exe This takes the dump to the routine that is to be devirtualized, the decryption routine ripped from the protected binary, the instruction mappings and the jmp mappings and produces a disassembled x86 form of the subroutine (in a format compliant with NASM). This is printed to STDOUT, the x86devirt.py script redirects that stdout to a file and feeds it into nasm before writing the nasm output (assembled x86 code) back into memory where the routine in its devirtualized form is assembled and fits in.

# License
This project and all of its' source files are licensed under the MIT license. NASM is licensed under a seperate license, mentioned under NASM-LICENSE in the distribution.

# How to Use
You must have the following installed to use the x86virt unpacker/devirtualizer:
1. Python 2.7
2. x64dbg
3. x64dbgpy
4. Python dependency angr (pip install angr)
5. Python dependency distorm3 (Download the installer from Distorm 3 releases page: https://github.com/gdabah/distorm/releases )

Once the above items have been installed and correctly configured:
1. Download x86devirt release from the releases page ( https://github.com/JeremyWildsmith/x86devirt/releases )
2. Open the packed / virtualized target in x64dbg
3. Select the Plugins -> x64dbgpy -> Open Script menu item, and browse to the downloaded x86devirt.py script

# How to Build
The only portion that you are required to build is the disassembler engine, which is written in C++. On Windows you can build this using cygwin and the make command.