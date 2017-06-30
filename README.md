pucrunch
----

A fork of `pucrunch` which includes source for unpacking on the BBC Micro.

Packing your data
----

Once the executable is built, packing your data down is very easy and is achieved from the command line:

```
./pucrunch -d -c0 -l0x900 -s BIN/SOURCE.DAT BIN/DEST.DAT
```

The few flags of note here are `-l` which tells it the address to unpack to, and `-c0` to tell the cruncher we're not on a C64!

Unpacking your data
----

Now the data is packed, you'll need to include the decruncher into your sources, which I've modified to assemble under BeebASM; see `uncrunch-bbc.asm`. Then all that remains is for you to unpack the data.

The routine will unpack to the address specified on the cruncher command line, but that's just a few bytes into the header data so should be easy enough to change on the fly.

For example, to unpack the data we packed in the command line above:

```
ORG &70
.LZPOS	EQUW &9e	; 2 ZeroPage temporaries
.bitstr	EQUB &fb	; 1 temporary (does not need to be ZP)

ORG &1900
{
.start:
    CLC
    LDX #HI(packedData+2)
    LDY #LO(packedData+2)
    JSR unpack
    BCC unpackOK
    LDA #70
    JSR &FFEE
    JSR &FFE7
    RTS
    
    ; At this stage 0x900 will contain our unpacked data!
.unpackOK:
    LDA #48
    JSR &FFEE
    JSR &FFE7
    RTS
.end:
}

INCLUDE "uncrunch-bbc.asm"

.packedData:
INCBIN "BIN/DEST.DAT"
```

Summary
----

puCrunch is a handy little cruncher for 8 bit micros and is very easy to use.  I've not investigated the options too much on the crunching side of things but for my purposes it's been very useful.
