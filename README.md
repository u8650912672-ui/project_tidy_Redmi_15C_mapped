#  Project Tidy – Redmi 15C Preloader Map
Reverse-engineered USB handler table and key code analysis for the MediaTek MT6768 preloader :) for the Xiaomi Redmi 15C ofc 
i have used radare2, mtkclient, and 1 exploit being MQSAS root access. Root access is locked down tho due to the limit from SElinux as it dont allow us to exploit it IF i were to be able to disable i would have a full user to root escelation exploit but the fun is i have a full 0 click shell to root escelation


# why use this?
if you have a redmi 15 C you migth wanna use root on it? well in that case this is for you problem is i havent gotten full root unrestricted YET but i will try and post it here
This is the first public, annotated list of every USB vendor‑specific command 
accepted by the preloader. It includes which ones are patched, which ones are 
safe, and which ones handle user‑supplied data

# MAPPED-OUT-REDMI-15C-
## NOTE THAT THIS IS SUMMED UP BY AI HEAVELY
just some random info i got while i was tryna root


  Request	Handler	Notes

    0x80	0x40c2	Simple status
    0xa1	0x4122	Patched (kamakiri target)
    0xa2	0x411a	Wrapper, safe
    0xc4	0x428a	Reads data, calls safe functions
    0xc5	0x43a4	Returns 0
    0xc6	0x42b2	Reads data, bit operations
    0xc7	0x42fa	Reads data, bit operations
    0xd0	0x411c	Reuses 0xa2 handler (safe)
    0xd1	0x412a	Complex: alloc, copy loop, safe allocator
    0xd2	0x4122	Reuses the patched 0xa1 handler!
    0xd3	0x4124	Reuses 0xa1 handler variant
    0xd4	0x420c	Reads data, complex processing
    0xd5	0x4046	Reads data, calls sub-functions
    0xd7	0x3eec	Allocates, reads 3 fields, copies
    0xd8	0x40f0	Reads data, bit operations
    0xdb	0x43a4	Returns 0
    0xe1	0x4340	Reads data, comparison
    0xe7	0x4372	Reads data, processing
    0xf0	0x43a8	Reads data, comparison
    0xfb	inline	Sends status 7
    0xfc	inline	Reads hardware registers
    0xfd	inline	Reads 0x8000000
    0xfe	inline	Sends status 3


  most intresting
  
    0x3f6e  ldr r2, [var_10h]    ; r2 = size from USB
    0x3f74  lsr.w lr, r2, 1      ; lr = size / 2
    0x3f78  and r2, r2, #1       ; r2 = size & 1
    0x3f7e  cmp r3, lr           ; loop counter vs size/2
    0x3f80  beq 0x3f8e           ; exit loop when done

ARM pseudocode
// r2 = size from USB (passed in via var_10h)
// r1 = pointer to input data (already set up before the loop)
// r0 = 0 (initial checksum value)

lsr lr, r2, #1    // lr = size / 2  (loop counter)
and r2, r2, #1    // r2 = size & 1  (leftover byte after 16‑bit words)

loop_start:
    cmp r3, lr            // compare current index to (size/2)
    beq loop_end          // if equal, all full 16‑bit words done
    ldrh ip, [r1, r3, lsl #1]  // load 16‑bit word from input
    adds r3, #1           // increment word index
    eor r0, r0, ip        // XOR into checksum
    b loop_start
loop_end:
    cbz r2, skip_byte     // if leftover byte is 0, skip
    ldrb r3, [r1, r3, lsl #1] // load final byte
    eor r0, r0, r3        // XOR into checksum
skip_byte:
    bl finalize_checksum   // send result to host

No memory write here


  Trait	Example Handler	Why It Matters
  
    Reads data from USB (bl fcn.00003bc8)	0xd4, 0xd5, 0xe1, 0xe7	Attacker controls input
    Uses that data as a size or length	0xd7 (the loop counter)	Potential for integer overflow or unbounded copy
    Writes to a fixed memory address (like str.w r4, [r1, #offset])	fcn.0002c228 (stores to 0x81000+)	If the address is reachable from USB, could be a register overwrite
    Contains a bl memcpy or blx r3 with user‑controlled size	None found yet, but that’s the holy grail	Direct buffer overflow



