# WOrking logic
* this one is easy. THe correct serial is a constant string:
  ```
    .data:00403020 dword_403020    dd 'DrB<'               ; DATA XREF: sub_401512+1B↑r
    .data:00403024 dword_403024    dd 'BoS-'               ; DATA XREF: sub_401512+24↑r
    .data:00403028 word_403028     dw '>'                  ; DATA XREF: sub_401512+2C↑r       
  ```