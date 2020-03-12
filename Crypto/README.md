# Crypto

# Table of Contents
* [Resources](#Resources)
* [Online Decoding Tools](#Online-Decoding-Tools)
* [RSA](#RSA)
  * [RSA - Tools](#RSA---Tools)
  * [Common Factor Attack](#Common-Factor-Attack)
  * [Integer Factorization](#Integer-Factorization)
    * [Pollard's p-1 Algorithm](#Pollard's-p-1-Algorithm)
    * [Fermat's Factorization Method](#Fermat's-Factorization-Method)
    * [RSA LSB Oracle](#RSA-LSB-Oracle)
  * [RSA - Implementation](#RSA---Implementation)

## Resources
* [OAlienO's Blog](https://oalieno.github.io/security/crypto/asymmetric/rsa/implementation/)
* [The Cryptopals Crypto Challenges](https://cryptopals.com/)

## Online Decoding Tools
* [aadecode](https://cat-in-136.github.io/2010/12/aadecode-decode-encoded-as-aaencode.html) : 顏文字編碼 (ﾟДﾟ)

## RSA
These are mostly from BambooFox
* Fermat's little theorem : `a^(p-1) ≡ 1 mod p`
* Euler's theorem : `a^Φ(n) ≡ 1 (mod n)`
* Euler's totient function
  1. `Φ(1) = 0`
  2. `Φ(p) = p-1` if p is a prime
  3. `Φ(m*n) = Φ(m) * Φ(n)` if m and n are relatively prime
* Generate Key Pair
  * 選擇p, q且p ≠ q，計算`N = p * q`
  * 選擇`e < Φ(n)` (通常是65537)，計算`d = e^-1 mod ф(N)` (用extended-gcd)
  * <span style="color:red">公鑰是(N,e)，私鑰是(N,d)</span>
  * ```python
    def genkey():
        # choose p, q, e
        p, q, e = getPrime(1024), getPrime(1024),65537
        # calculate d
        n, phi = p*q, (p-1)*(q-1)
        d = inverse(e, phi)
        # return publicKey, privateKey
        return(n, e), (n, d)
    ```
* Encryption
  * 將每一個字轉換為Unicode，然後將這些數字連在一起組成一個數字
  * Plaintext : m, Ciphertext : c
  * `c ≡ m^e (mod N)`
  * ```python
    def enc(m, public):
        n, e = public
        return pow(m, e, n)
    ```
* Decryption
  * `m ≡ c^d (mod N)`
  * ```python
    def dec(c, private):
        n, d = private
        return pow(c, d, n)
    ```

### RSA - Tools
* Python Packages
  * PyCrypto or PyCryptodome
  * primefac-> 有很多因數分解的函數
  * gmpy2 -> 有函數可以判斷完全平方數
  * sage
  * sympy
* [RsaCtfTool](https://github.com/Ganapati/RsaCtfTool)
* [factordb.com](http://factordb.com/) - Online Factorize Tool

### Common Factor Attack
* 當兩個n有共同質因數
* gcd(n1,n2)能有效率的分解n1,n2

### Integer Factorization
* 只要能分解n = pq
* 可以照著原本產生公私鑰的步驟產生私鑰，進而解密密文
* 目前最好的演算法是General Number Field Sieve(GNFS)
* 一些特殊情況下的演算法
  * Pollard's p-1 Algorithm
  * Williams's p+1 Algorithm
  * Fermat's Factorization Method

#### Pollard's p-1 Algorithm
* 使用條件 : p-1 最大的質因數很小 => We call this a smooth number
* 假設 :
  * 正整數 a, 合數 n, 質數 p
  * gcd(a,p) = 1 且 p | n
* Algorithm :
  * `a^(p-1) ≡ 1 (mod p)`
  * `a^k(p-1) ≡ 1 (mod p)`
  * `a^k(p-1) - 1 ≡ 0 (mod p)` for some k
  * `p | gcd(a^k(p-1), n)` => 把a設成2
  * 測試`gcd(2^1-1, n)`, `gcd(2^(1*2)-1, n)`, `gcd(2^(1*2*3)-1,n)`,...，只要`p-1 | 1*2*...`，`gcd(2^(1*2*...)-1,n)>1`
* ```python
  def pollard(n):
    a = 2
    b = 2
    while True:
        a = pow(a, b, n)
        d = gcd(a-1, n)
        if 1 < d < n:
            return d
        b += 1
  ```

#### Fermat's Factorization Method
* 使用條件 : |p-q|很小
* 假設 : 奇合數n是兩質數pq乘積
* Method :
  * `a = (p+q)/2`, `b = (p-q)/2`
  * `n = (a+b)(a-b) = a^2 - b^2`
  * 猜`a = ceil(sqrt(n))`並測試`b^2 = a^2 - n`是不是平方數，不行就把a + 1再猜一次
* ```python
  def fermat(n):
    a = ceil(sqrt(n))
    b2 = a*a - n
    while not gmpy2.iroot(b2,2)[1]:
        a = a + 1
        b2 = a*a - n
        b = gmpy2.iroot(b2,2)[0]
    return [a+b, a-b]
  ```

#### RSA LSB Oracle

### RSA - Implementation
* 解析公私鑰
  ```python
  from Crypto.PublicKey import RSA
  public = RSA.importKey(open('public.pem').read())
  private = RSA.importKey(open('private.pem').read())
  ```