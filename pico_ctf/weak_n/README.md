# Mind your P's and Q's
For this challenge we are given a file with the following content.
```
Decrypt my super sick RSA:
c: 861270243527190895777142537838333832920579264010533029282104230006461420086153423
n: 1311097532562595991877980619849724606784164430105441327897358800116889057763413423
e: 65537
```
We are given the cipher text C, e and n are the public key used to encrypt. But because RSA is asymmetric we need to calculate the private key (d,n).

## Calculating n
n is a value created during key generation, you generate two co-prime numbers p and q. 

n = pq

The security of RSA comes from n being hard to factor, however smaller n's can be factorised. Lets try generate some factors. We can use a handy tool: https://www.alpertron.com.ar/ECM.HTM

After a short wait we got a hit!

1955175890537890492055221842734816092141 × 670577792467509699665091201633524389157003 = 1311097532562595991877980619849724606784164430105441327897358800116889057763413423

You can type it into a calculator if you dont believe me, given that we have p and q we can recreate the private key. 

## Recreating the private key
We need to calculate some additional values first.

phi = (p-1)(q-1)

Now we can generate d = multiplicative inverse of e modulo phi. pycrypto has a function for this.

```
d = inverse(e,phi)
```

The last step is to decrypt which is c^d mod n.
```
m = pow(c,d,n)
```

Given that our cipher text is one long long we need to decode it to ascii, this can be done by converting it to hex and spliting it into bytes and then converting to ascii. 

```
print(bytearray.fromhex(hex(m)[2:]).decode())
```

Here is the complete code:
```
from Crypto.Util.number import inverse

c = 861270243527190895777142537838333832920579264010533029282104230006461420086153423
n = 1311097532562595991877980619849724606784164430105441327897358800116889057763413423
e = 65537


# Factored using https://www.alpertron.com.ar/ECM.HTM
p = 1955175890537890492055221842734816092141
q = 670577792467509699665091201633524389157003

#calculate private key
phi = (p-1)*(q-1)
d = inverse(e,phi)

#we now have the private key decode
m = pow(c,d,n)
print(bytearray.fromhex(hex(m)[2:]).decode())
```

```
picoCTF{sma11_N_n0_g0od_13686679}
```