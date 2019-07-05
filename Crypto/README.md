# Crypto

## RSA
* Fermat's little theorem : a^(p-1) ≡ 1 mod p
* Euler's theorem : a^Φ(n) ≡ 1 (mod n)
* Euler's totient function
  1. Φ(1) = 0
  2. Φ(p) = p-1 if p is a prime
  3. Φ(m*n) = Φ(m) * Φ(n) if m and n are relatively prime

* Generate Key Pair
  * 選擇p, q且p ≠ q，計算N = p * q
  * 選擇e < Φ(n) (通常是65537)，計算d = e^-1 mod ф(N) (用extended-gcd)
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
  * c ≡ m^e (mod N)
  * ```python
    def enc(m, public):
        n, e = public
        return pow(m, e, n)
    ```
* Decryption
  * m ≡ c^d (mod N)
  * ```python
    def dec(c, private):
        n, d = private
        return pow(c, d, n)
    ```