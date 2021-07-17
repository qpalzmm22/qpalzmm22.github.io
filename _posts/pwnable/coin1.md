## 11 : Coin1

- go into input1 question for fast server response
- make /tmp/qpalzmm22/coin_ex.py

```python
from pwn import *


#p1 = con.recv()
def recvinput():

    con.recvuntil("N=")

    input_vars = con.recvline().decode().split()
    return input_vars

# try half by half
def bin_search (l_b, r_b, trial):
    #print(f"l_b : {l_b} r_b : {r_b} trial : {trial}" )
    mid = l_b + (r_b - l_b ) // 2

    trial = trial - 1
    if(trial < 0):
        # print(f"done : {l_b}")
        return l_b

    #if(l_b == r_b):
    #    bin_search(l_b, r_b, trial)

    input_str = ""

    for i in range(l_b, mid):
        input_str +=str(i) + " "

    print(input_str)
    con.sendline(input_str)
    result = int(con.recvline())
    print(result)

    # fake coin in [lb, rb // 2]
    if(result % 10 != 0):
        return bin_search(l_b, mid, trial)
    # fake coin in [rb // 2 +1, rb]
    else:
        return bin_search(mid, r_b, trial)



con = remote('localhost', 9007)

con.recvuntil("Ready? starting in 3 sec... -")
#print(p0)
iter = 0

while (iter < 100) :

    print("Run: {0}".format(iter))
    input_vars =  recvinput()
    print (input_vars)
    n_var = input_vars[0]
    c_var = input_vars[1][2:]

    print(n_var)
    print(c_var)

    ans = bin_search(0, int(n_var), int(c_var))
    #  print(f"returned : {ans}")
    con.sendline(str(ans))

    iter = iter + 1;
    #recv = con.recvall()
    #print(f"serv : {recv}")

print(con.recvline())
print(con.recvline())
print(con.recvline())
print(con.recvline())
print(con.recvline())
print(con.recvline())
con.close()

```
**FLAG : b1NaRy_S34rch1nG_1s_3asy_p3asy**