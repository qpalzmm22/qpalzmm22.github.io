# horcruxes

`Spelling`도 어려운 `horcruxes`... 해리포터 레퍼런스인 듯하다(no pun intended). 다음은 `disas main`을 하여 조금 읽어보면 밑에쯤에 나오는 `ropme` 라는 함수를 `disas` 한 것이다. 내가 `>` 로 체크한 부분만 일단은 보자

```armasm
    0x080a0009 <+0>:     push   ebp
   0x080a000a <+1>:     mov    ebp,esp
   0x080a000c <+3>:     sub    esp,0x78
   0x080a000f <+6>:     sub    esp,0xc
   0x080a0012 <+9>:     push   0x80a050c
   0x080a0017 <+14>:    call   0x809fc40 <printf@plt>
   0x080a001c <+19>:    add    esp,0x10
   0x080a001f <+22>:    sub    esp,0x8
   0x080a0022 <+25>:    lea    eax,[ebp-0x10]
   0x080a0025 <+28>:    push   eax
   0x080a0026 <+29>:    push   0x80a0519
   0x080a002b <+34>:    call   0x809fd10 <__isoc99_scanf@plt>
   0x080a0030 <+39>:    add    esp,0x10
   0x080a0033 <+42>:    call   0x809fc70 <getchar@plt>
   0x080a0038 <+47>:    mov    edx,DWORD PTR [ebp-0x10]
   0x080a003b <+50>:    mov    eax,ds:0x80a2088
   > 0x080a0040 <+55>:    cmp    edx,eax    ; eax : 0x0f3bfbc2
   0x080a0042 <+57>:    jne    0x80a004e <ropme+69>
   0x080a0044 <+59>:    call   0x809fe4b <A>
   0x080a0049 <+64>:    jmp    0x80a0170 <ropme+359>
   0x080a004e <+69>:    mov    edx,DWORD PTR [ebp-0x10]
   0x080a0051 <+72>:    mov    eax,ds:0x80a2070
   > 0x080a0056 <+77>:    cmp    edx,eax     ; eax : 0x57c8c218
   0x080a0058 <+79>:    jne    0x80a0064 <ropme+91>
   0x080a005a <+81>:    call   0x809fe6a <B>
   0x080a005f <+86>:    jmp    0x80a0170 <ropme+359>
   0x080a0064 <+91>:    mov    edx,DWORD PTR [ebp-0x10]
   0x080a0067 <+94>:    mov    eax,ds:0x80a2084
   > 0x080a006c <+99>:    cmp    edx,eax    ; eax 0x9a5eb423 0xb295b17e
   0x080a006e <+101>:   jne    0x80a007a <ropme+113>
   0x080a0070 <+103>:   call   0x809fe89 <C>
   0x080a0075 <+108>:   jmp    0x80a0170 <ropme+359>
   0x080a007a <+113>:   mov    edx,DWORD PTR [ebp-0x10]
   0x080a007d <+116>:   mov    eax,ds:0x80a206c
   > 0x080a0082 <+121>:   cmp    edx,eax
   0x080a0084 <+123>:   jne    0x80a0090 <ropme+135>
   0x080a0086 <+125>:   call   0x809fea8 <D>
   0x080a008b <+130>:   jmp    0x80a0170 <ropme+359>
   0x080a0090 <+135>:   mov    edx,DWORD PTR [ebp-0x10]
   0x080a0093 <+138>:   mov    eax,ds:0x80a2080
   > 0x080a0098 <+143>:   cmp    edx,eax; 0x53075220 0x4eacf803 0x60921464 0x6734a73d
   0x080a009a <+145>:   jne    0x80a00a6 <ropme+157>
   0x080a009c <+147>:   call   0x809fec7 <E>
   0x080a00a1 <+152>:   jmp    0x80a0170 <ropme+359>
   0x080a00a6 <+157>:   mov    edx,DWORD PTR [ebp-0x10]
   0x080a00a9 <+160>:   mov    eax,ds:0x80a2074
   > 0x080a00ae <+165>:   cmp    edx,eax; 0x60921464      0x6734a73d      0xa7362330      0x70c3bfe5
   0x080a00b0 <+167>:   jne    0x80a00bc <ropme+179>
   0x080a00b2 <+169>:   call   0x809fee6 <F>
   0x080a00b7 <+174>:   jmp    0x80a0170 <ropme+359>
   0x080a00bc <+179>:   mov    edx,DWORD PTR [ebp-0x10]
   0x080a00bf <+182>:   mov    eax,ds:0x80a207c ;0xa7362330      0x70c3bfe5      0x9a5eb423      0xb295b17e
   > 0x080a00c4 <+187>:   cmp    edx,eax
   0x080a00c6 <+189>:   jne    0x80a00d2 <ropme+201>
   0x080a00c8 <+191>:   call   0x809ff05 <G>
   0x080a00cd <+196>:   jmp    0x80a0170 <ropme+359>
   0x080a00d2 <+201>:   sub    esp,0xc
   0x080a00d5 <+204>:   push   0x80a051c
   0x080a00da <+209>:   call   0x809fc40 <printf@plt>
   0x080a00df <+214>:   add    esp,0x10
   0x080a00e2 <+217>:   sub    esp,0xc
   0x080a00e5 <+220>:   lea    eax,[ebp-0x74]
   0x080a00e8 <+223>:   push   eax
   0x080a00e9 <+224>:   call   0x809fc50 <gets@plt>
   0x080a00ee <+229>:   add    esp,0x10
   0x080a00f1 <+232>:   sub    esp,0xc
   0x080a00f4 <+235>:   lea    eax,[ebp-0x74]
   0x080a00f7 <+238>:   push   eax
   0x080a00f8 <+239>:   call   0x809fd20 <atoi@plt>
   0x080a00fd <+244>:   add    esp,0x10
   0x080a0100 <+247>:   mov    edx,eax
   0x080a0102 <+249>:   mov    eax,ds:0x80a2078
   > 0x080a0107 <+254>:   cmp    edx,eax ; 0xabcb4d9e      0x09f57acb      0x7b2ae5c1      0xa2eb858f
   0x080a0109 <+256>:   jne    0x80a0160 <ropme+343>
   0x080a010b <+258>:   sub    esp,0x8
   0x080a010e <+261>:   push   0x0
   0x080a0110 <+263>:   push   0x80a053c
   0x080a0115 <+268>:   call   0x809fcc0 <open@plt>
   0x080a011a <+273>:   add    esp,0x10
   0x080a011d <+276>:   mov    DWORD PTR [ebp-0xc],eax
   0x080a0120 <+279>:   sub    esp,0x4
   0x080a0123 <+282>:   push   0x64
   0x080a0125 <+284>:   lea    eax,[ebp-0x74]
   0x080a0128 <+287>:   push   eax
   0x080a0129 <+288>:   push   DWORD PTR [ebp-0xc]
   0x080a012c <+291>:   call   0x809fc30 <read@plt>
   0x080a0131 <+296>:   add    esp,0x10
   0x080a0134 <+299>:   mov    BYTE PTR [ebp+eax*1-0x74],0x0
   0x080a0139 <+304>:   sub    esp,0xc
   0x080a013c <+307>:   lea    eax,[ebp-0x74]
   0x080a013f <+310>:   push   eax
   0x080a0140 <+311>:   call   0x809fca0 <puts@plt>
   0x080a0145 <+316>:   add    esp,0x10
   0x080a0148 <+319>:   sub    esp,0xc
   0x080a014b <+322>:   push   DWORD PTR [ebp-0xc]
   0x080a014e <+325>:   call   0x809fd30 <close@plt>
   0x080a0153 <+330>:   add    esp,0x10
   0x080a0156 <+333>:   sub    esp,0xc
   0x080a0159 <+336>:   push   0x0
   0x080a015b <+338>:   call   0x809fcb0 <exit@plt>
   0x080a0160 <+343>:   sub    esp,0xc
   0x080a0163 <+346>:   push   0x80a0544
   0x080a0168 <+351>:   call   0x809fca0 <puts@plt>
   0x080a016d <+356>:   add    esp,0x10
   0x080a0170 <+359>:   mov    eax,0x0
   0x080a0175 <+364>:   leave
   0x080a0176 <+365>:   ret
```
ABCB 4D9E : 
2882227614

470809186

set disassembly intel
b *ropme+55
b *ropme+77
b *ropme+99
b *ropme+121
b *ropme+143
b *ropme+165
b *ropme+187
b *ropme+254
b A
b B
b C
b D
b E
b F
b G
