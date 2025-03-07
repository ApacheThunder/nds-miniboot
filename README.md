# miniboot

Minimal flashcart bootstrap for NDS consoles. Loads `/BOOT.NDS` and
papers over various device-specific quirks, allowing running 100%
clean homebrew launchers on a variety of such devices.

# changes from original version

This version of nds-miniboot is modified to be used as a rom 
replacement for DSTT and their clones. Requires a ntrboot 
compatilbe DSTT or DSTT clone. This repo is not yet modified
to build nds file that is directly compatiblee with the
layout of the flash dump for DSTT. You will need to do this
your self for now.

## Usage

### Installation

Copy the contents of the specified directory to the root of your
flashcart's memory card:

| Device | Directory |
| ------ | --------- |
| Ace3DS+ / Ace3DS X | ace3dsplus |
| Acekard 2/2i | generic |
| Datel Games 'n' Music | generic |
| DSTT | generic |
| EDGEi | generic |
| EZ-Flash Parallel | generic |
| EZ-Flash V | generic |
| Gateway Blue | gwblue |
| R4 (original) | generic |
| R4 i.L.S. | ace3dsplus |
| R4/R4i Ultra | r4itt |
| R4iDSN | r4idsn |
| R4iTT 3DS | r4itt |
| r4dspro.com | r4dspro |
| r4ids.cn | r4itt |
| R6 Gold/Silver | mkr6 |
| Stargate 3DS | stargate |
| SuperCard DSONE | generic |
| SuperCard DSONE SDHC | dsonesdhc |
| Timebomb carts | generic |

### Troubleshooting

Hold START while loading to enable debug output. Note that launching
will only continue once you release START.

## Development

To build miniboot, the [Wonderful toolchain](https://wonderful.asie.pl/)'s
`wf-tools`, `toolchain-gcc-arm-none-eabi`, as well as BlocksDS (for
`ndstool` and `dldipatch`) are required. They can be installed with the
following command:

    $ wf-pacman -Sy toolchain-gcc-arm-none-eabi thirdparty-blocksds wf-tools

### Motivation

`.nds` files can be loaded essentially anywhere in RAM: in particular,
3.75MB out of the 4MB of main RAM can be used. As such, the easiest
way to load such a file is to put the bootstrap code outside main RAM.

As the fastest place to execute code from is ITCM, I started wondering
if one could create a bootstrap that operates entirely from ITCM (32K)
and DTCM (16K). That is not a lot of room - the DLDI driver which is
provided by a flashcart vendor has to have 16KB of space reserved,
leaving only 16KB for the remaining code. Thankfully, there's not much
code involved: one needs a lightweight copy of FatFs to read files from
the filesystem, a DLDI patcher for the loaded ARM9 binary, and some
simple load/reset code on top of that.

I've also never created a freestanding NDS homebrew program before, so
I wanted to see what goes into that.

### Porting

Assorted notes:

* `arm9.bin` and `arm7.bin` are extracted from the ELF file as they are
  position-independent - the binaries relocate themselves upon execution
  to areas outside of main RAM, they just have to be started from their
  first word (offset 0).
* Initiailization is deliberately sparse; if a given device needs
  additional cleanup, please document it!

## License

The `miniboot` source code itself is covered under a mix of two licenses:

* the Zlib license,
* the FatFs license.

Individual binaries for specific flashcarts may be covered under their own
respective licenses.
