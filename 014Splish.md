# Working Logic
## 'Hard Coded' verification
* search the strings and find 'Congratulations, you got the hard coded serial!'. Then set bp there.
* easy to find the code below:
  ```
    0040135D  |> \6A 20         push 0x20                                ; /Count = 20 (32.)
    0040135F  |.  68 15324000   push Splish.00403215                     ; |Buffer = Splish.00403215
    00401364  |.  FF35 90344000 push dword ptr ds:[0x403490]             ; |hWnd = 001103F2 (class='Edit',parent=001102CE)
    0040136A  |.  E8 BB030000   call <jmp.&USER32.GetWindowTextA>        ; \GetWindowTextA
    0040136F  |.  8D05 53134000 lea eax,dword ptr ds:[0x401353]          ;  HardCoded
    00401375  |.  8D1D 15324000 lea ebx,dword ptr ds:[0x403215]
    0040137B  |>  8038 00       /cmp byte ptr ds:[eax],0x0
    0040137E  |.  74 0C         |je short Splish.0040138C
    00401380  |.  8A08          |mov cl,byte ptr ds:[eax]
    00401382  |.  8A13          |mov dl,byte ptr ds:[ebx]
    00401384  |.  38D1          |cmp cl,dl
    00401386  |.  75 4A         |jnz short Splish.004013D2
    00401388  |.  40            |inc eax
    00401389  |.  43            |inc ebx                                 ;  Splish.00401178
    0040138A  |.^ EB EF         \jmp short Splish.0040137B
  ```
  * it's simply get the input hard code and compare with constant string 'HardCoded'. If same, then pass.
## 'Name/Serial' verification
* set bp on GetWindowText function then get the function to calculate the serial.
* we get the key jmp here:
  ```
    004016B6  |> /3B1D 63344000 /cmp ebx,dword ptr ds:[0x403463]
    004016BC  |. |74 0F         |je short Splish.004016CD                 ; Key JMP
    004016BE  |. |0FBE041F      |movsx eax,byte ptr ds:[edi+ebx]
    004016C2  |. |0FBE0C1E      |movsx ecx,byte ptr ds:[esi+ebx]
    004016C6  |. |3BC1          |cmp eax,ecx
    004016C8  |. |75 18         |jnz short Splish.004016E2
    004016CA  |. |43            |inc ebx                                 ;  Splish.00401178
    004016CB  |.^\EB E9         \jmp short Splish.004016B6
    004016CD  |>  6A 00         push 0x0                                 ; /Style = MB_OK|MB_APPLMODAL
    004016CF  |.  68 0A304000   push Splish.0040300A                     ; |Splish, Splash
    004016D4  |.  68 42304000   push Splish.00403042                     ; |Good job, now keygen it.
    004016D9  |.  6A 00         push 0x0                                 ; |hOwner = NULL
    004016DB  |.  E8 68000000   call <jmp.&USER32.MessageBoxA>           ; \MessageBoxA
  ```
* next we need to reverse this function.

## Solution
* this is the key gen:
  ```
    int main()
    {
        int lengthOfmyserial; // eax
        int lengthOfmyname; // eax
        int i2; // ebx
        int v5; // edx
        int i3; // ebx
        int i; // ebx

        char myname[7] = "myname";
        char myname_v2[7] = { 0 };
        lengthOfmyname = strlen(myname);

        i2 = 0;
        do
        {
            v5 = (i2 ^ myname[i2] % 10) + 2;
            if ((char)v5 >= 10)
            {
    //           LOBYTE(v5) = v5 - 10;
                BYTE low_v5 = v5 - 10;
                v5 = v5 & 0xffffff00;
                v5 = v5 + low_v5;
            }
            myname_v2[i2++] = v5;
        } while (i2 != lengthOfmyname);

        char serial[7] = { 0 };
        for (int i = 0; i < lengthOfmyname; i++)
        {
            serial[i] = 100 + myname_v2[i];
        }
        printf("%s", serial);
        return 0;
    }
  ```