# Working Logic
## 'Only Serial' verification
* When run 'Acid burn.exe', there are 2 types of verification processes:
  * input both Serial/Name
  * input only Serial
* Use OD to run, check all strings then goto 'Congrats' string:
* there's a key jmp here 'jnz short Acid_bur.0042F4F1'.
  ```
    0042F4CA  |.  8B45 F0       mov eax,[local.4]
    0042F4CD  |.  8B55 F4       mov edx,[local.3]
    0042F4D0  |.  E8 2745FDFF   call Acid_bur.004039FC
    0042F4D5  |.  75 1A         **jnz short Acid_bur.0042F4F1**
    0042F4D7  |.  6A 00         push 0x0
    0042F4D9  |.  B9 64F54200   mov ecx,Acid_bur.0042F564                ;  Congratz!
    0042F4DE  |.  BA 70F54200   mov edx,Acid_bur.0042F570                ;  God Job dude !! =)
    0042F4E3  |.  A1 480A4300   mov eax,dword ptr ds:[0x430A48]
    0042F4E8  |.  8B00          mov eax,dword ptr ds:[eax]
    0042F4EA  |.  E8 81ACFFFF   call Acid_bur.0042A170
  ```
* set bp here and find this is for 'only Serial' verification. And [local.4] is for the stirng you input. [local.3] is for the correct serial.
* rerun it and input the correct serial, then passed.

## 'Serial/Name' verification
* check the strings again and find there's another 'Congrats'.
* replicate the steps above find it's for 'Serial/Name' verification.
  ```
    0042FAF8  |.  8B55 F0       mov edx,[local.4]
    0042FAFB  |.  8B45 F4       mov eax,[local.3]                        ;  correct serial
    0042FAFE  |.  E8 F93EFDFF   call Acid_bur.004039FC
    0042FB03  |.  75 1A         jnz short Acid_bur.0042FB1F
  ```
* find local.3 is for correct serial 'CW-8938-CRACKED'. Seems **'8938' is the key point**.
* roll up again and find the key function to calculate the [Local.3] which is the correct serial.
  ```
    0042FADD  |.  8D45 F4       lea eax,[local.3]
    0042FAE0  |.  BA 05000000   mov edx,0x5
    0042FAE5  |.  E8 C23EFDFF   call Acid_bur.**004039AC**
  ```
* roll up and re-run, find [local.6] is for key '8938'. 
  ```
    0042FAD2  |.  FF75 E8       push [local.6]                           ;  key '8938'
    0042FAD5  |.  68 C8FB4200   push Acid_bur.0042FBC8                   ;  -
    0042FADA  |.  FF75 F8       push [local.2]                           ;  Acid_bur.0042FBB8
    0042FADD  |.  8D45 F4       lea eax,[local.3]
    0042FAE0  |.  BA 05000000   mov edx,0x5
    0042FAE5  |.  E8 C23EFDFF   call Acid_bur.004039AC
  ```
* roll up and find 0x431750 stores 0x22EA which is 8938.
  ```
    0042FAC5  |.  8D55 E8       lea edx,[local.6]
    0042FAC8  |.  A1 50174300   mov eax,dword ptr ds:[0x431750]
    0042FACD  |.  E8 466CFDFF   call Acid_bur.00406718
  ```
* After repeatly roll up and analyze, may find the algorithem bytes code below:
  ```
    0042FA79  |> \8D55 F0       lea edx,[local.4]                        ;  myusername
    0042FA7C  |.  8B83 DC010000 mov eax,dword ptr ds:[ebx+0x1DC]
    0042FA82  |.  E8 D1AFFEFF   call Acid_bur.0041AA58                   ;  get length of myusername
    0042FA87  |.  8B45 F0       mov eax,[local.4]
    0042FA8A  |.  0FB600        movzx eax,byte ptr ds:[eax]              ;  eax = 'P'
    0042FA8D  |.  F72D 50174300 imul dword ptr ds:[0x431750]             ;  eax = 'P' * 0x29 = 0xCD0
    0042FA93  |.  A3 50174300   mov dword ptr ds:[0x431750],eax
    0042FA98  |.  A1 50174300   mov eax,dword ptr ds:[0x431750]
    0042FA9D  |.  0105 50174300 add dword ptr ds:[0x431750],eax          ;  0x431750 = 0x431750 * 2 = 0x19A0
    0042FAA3  |.  8D45 FC       lea eax,[local.1]
    0042FAA6  |.  BA ACFB4200   mov edx,Acid_bur.0042FBAC                ;  CW
    0042FAAB  |.  E8 583CFDFF   call Acid_bur.00403708                   ;  [local.1] = "CW"
    0042FAB0  |.  8D45 F8       lea eax,[local.2]
    0042FAB3  |.  BA B8FB4200   mov edx,Acid_bur.0042FBB8                ;  CRACKED
    0042FAB8  |.  E8 4B3CFDFF   call Acid_bur.00403708
    0042FABD  |.  FF75 FC       push [local.1]                           ;  Acid_bur.0042FBAC
    0042FAC0  |.  68 C8FB4200   push Acid_bur.0042FBC8                   ;  -
    0042FAC5  |.  8D55 E8       lea edx,[local.6]
    0042FAC8  |.  A1 50174300   mov eax,dword ptr ds:[0x431750]          ;  key 22ea
    0042FACD  |.  E8 466CFDFF   call Acid_bur.00406718
    0042FAD2  |.  FF75 E8       push [local.6]                           ;  key '8938'
    0042FAD5  |.  68 C8FB4200   push Acid_bur.0042FBC8                   ;  -
    0042FADA  |.  FF75 F8       push [local.2]
    0042FADD  |.  8D45 F4       lea eax,[local.3]
    0042FAE0  |.  BA 05000000   mov edx,0x5
    0042FAE5  |.  E8 C23EFDFF   call Acid_bur.004039AC
  ```
  * actually it's quite easy assuming your input username is 'myusername':
  ```
    pick first character which is 'm'
    calculate 'm'*0x29*2 = 22ea
    change it to decimalism which is 8938
    catenate with constatn string to be 'CW-8938-CRACKED' which is the correct serial number.
  ```