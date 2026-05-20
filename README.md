# MAPPED-OUT-REDMI-15C-
preloader, bypasses, brom, and more


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
