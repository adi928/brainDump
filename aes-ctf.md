## AES CTF

### About

- AES (Rijndael) encryption - block mode - symmetric configuration
- CBC (Cipher Block Chaining) - mode of operation
	- This means that after dividing plaintext into blocks, each block is XORed with a key and then this encrypted block is XORed with the next plaintext block.
	- The first block is XORed with an Initialization Vector (IV)
- One weakness with AES can be if the IV is reused at any stage. It should be random and unique everytime.

### Challenge - RITSEC CTF [1](https://tildeho.me/ritsec-ctf-writeup-recover-aes-cbc-iv/)

- What was given?
	- The encryption algorithm (AES 128-bit)
	- The plaintext
	- First 14 bytes of 16 byte key
	- The complete second block and parts of the first ciphertext block

- Decryption with CBC is involved. IV is only needed to decrypt the first block. Not the second block. For the second block, first block of ciphertext is good enough
- Since the partial key was given, a script for bruteforcing out the key was made. [2](https://gist.github.com/adi928/aeafcfd2f2429d963d2904447401676f)
	- ciphers (16 bytes each) seem to be in a string but represented in hex format.
	- the hexes are converted to ascii encoding using `binascii.unhexlify(cipher)`
- The above algorithm will get the key. And then reversing the XOR operation with ciphertext part 2 and plaintext part 2 we get the ciphertext part 1.
- Since the first ciphertext part is supposed to be XORed with IV to get the plaintext part 1, we can then reverse that process to deduce the IV, which was the flag.

### Challenge - SwampCTF [3](https://blog.h25.io/SwampCTF-AES/)

- What was given?
	- The full program which encrypts and decrypts.
	- the key is being used as the IV also. So the IV is same for every encryption and decryption.
- The user first attacks with three blocks of 16 bytes each filled with zeroes.
- 

### Padding Oracle Attack

- Just needs an access to the ciphertext. Then:
	1. Divide it into 16bytes blocks.
	2. Change the second last block to ^ 0x01 ^ [1-256 guesses]
	3. And keep expecting the change to reflect in the last block of the plaintext. 
