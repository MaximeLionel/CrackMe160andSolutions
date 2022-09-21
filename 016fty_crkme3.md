# Steps:
## Unpacking:
* there's packing detected here:
  ```
    UPX 0.89.6 - 1.02 / 1.05 - 2.90 (Delphi) stub -> Markus & Laszlo
  ```
  this pack is easy to unpack.
* at start of program, there's 'pushad' to push the original entry point onto stack.
* you may set hw bp on the original entry point and run the program.
* after run to the original entry point. You may save the exe as an unpack one.

## Cracking:
* search string and find key jmp here:
  ```
    00445322   /74 0C           je short fty_crkm.00445330      ; KEY jmp
    00445324   |B8 D0534400     mov eax,fty_crkm.004453D0                ; Nope! Your calculations are wrong, boy... :P
    00445329   |E8 CAF3FFFF     call fty_crkm.004446F8
    0044532E   |EB 0A           jmp short fty_crkm.0044533A
    00445330   \B8 08544400     mov eax,fty_crkm.00445408                ; Good boy! Now find all valid serial numbers :P
    00445335    E8 BEF3FFFF     call fty_crkm.004446F8
  ```