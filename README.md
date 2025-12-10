## Installation

1. Make sure you have Python 3.x installed.
2. Install the required packages using:

       pip install -r requirements.txt

## How to Run the Project

1. Open the notebook file `problems.ipynb`.
2. Select the Python kernel you installed the requirements into (e.g. `.venv (Python 3.x)`).
3. Click **Run All**.
4. The output will show binary values for each operation, for example:
       
       x = np.uint32(0b11110000)
       y = np.uint32(0b10101010)
       z = np.uint32(0b01010101)

       print(bin(int(Parity(x, y, z))))  # Output: 0b10011111
       
## Testing:
Each function can be tested using simple binary inputs or random 32-bit values.
For example:

      x = np.uint32(0b11110000)
      y = np.uint32(0b10101010)
      z = np.uint32(0b01010101)

      print("Parity:", bin(int(Parity(x, y, z))))
      print("Ch:", bin(int(Ch(x, y, z))))
      print("Maj:", bin(int(Maj(x, y, z))))
      

## Problem 1: Binary Words and Operations.
Focuses on implementing and understanding the binary operations that form the mathematical foundation of the SHA-256 hashing algorithm, as defined in the NIST Secure Hash Standard (FIPS PUB 180-4).
These operations are all performed on 32-bit unsigned integers, and include logical and bitwise transformations such as:

    - Parity(x, y, z) – returns 1 if an odd number of input bits are 1.
    - Ch(x, y, z) – chooses bits from y or z based on x 
      (the "choose"  function).
    - Maj(x, y, z) – returns the majority bit among the three inputs.
    - Σ₀, Σ₁, σ₀, and σ₁ – represent rotation and shift functions defined 
      in the standard, using right-rotate (ROTR) and right-shift (SHR) operations.
### How Problem 1 works
1. Bitwise operations (^, &, ~) are used to manipulate binary words at the bit level.

2. NumPy’s uint32 type ensures that all operations stay within 32-bit boundaries, matching cryptographic specifications.

3. Helper functions such as ROTR (rotate right) and SHR (shift right) are reused across multiple parts of the algorithm to create the Σ and σ functions.

### How to Test Problem 1

Each function in Problem 1 can be tested using simple 32-bit binary values.
Example tests were included directly in the notebook to verify correctness:

```python
x = np.uint32(0b11110000)
y = np.uint32(0b10101010)
z = np.uint32(0b01010101)

print("Parity:", bin(int(Parity(x, y, z))))
print("Ch:", bin(int(Ch(x, y, z))))
print("Maj:", bin(int(Maj(x, y, z))))
```

## Problem 2: Generating and Verifying SHA-256 Constants.
This problem focuses on creating and validating the 64 constants (K values) used in the SHA-256 hashing algorithm.
These constants are calculated from the fractional parts of the cube roots of the first 64 prime numbers, as defined in the NIST Secure Hash Standard (FIPS PUB 180-4).

### How Problem 2 works:

1. Prime Generation: The first 64 prime numbers are calculated (2, 3, 5, 7, 11, …).

2. Constant Calculation: For each prime p, the cube root is taken, the fractional part is multiplied by 2³², and then converted into a  32-bit unsigned integer.

3. Display Function: The constants are printed neatly, 8 per row, for example:

        K[00]=0x428a2f98  K[01]=0x71374491  K[02]=0xb5c0fbcf ...

4. Verification Function: The generated constants are compared against the official constants from the Secure Hash Standard (FIPS 180-4).
    If everything matches, the output confirms:

        All 64 SHA-256 constants match the official FIPS 180-4 values.

### How to test Problem 2:
To test the output, run the following in your notebook:

    display_sha256_constants()
    verify_sha256_constants()#

Expected output:

    All 64 SHA-256 constants match the official FIPS 180-4 values.

## Problem 3: Message Padding and Parsing.
This problem focuses on preparing a message for SHA-256 processing by applying padding and splitting it into 512-bit blocks, following the rules defined in the NIST Secure Hash Standard (FIPS PUB 180-4, §5.1.1–§5.2.1).

### How Problem 3 works:
1. The original message length (ℓ) is calculated in bits using message_length_bits().
2. A single '1' bit (0x80) is appended, followed by enough '0' bits to make the total length congruent to 448 mod 512.
3. The 64-bit big-endian representation of the original message length (ℓ) is added to the end of the padded message.
4. The fully padded message is split into 512-bit (64-byte) blocks using a generator function block_parse(msg),
   which yields each block as a separate bytes object for further processing.

### How to test Problem 3:
Example test:

    msg = b"abc"
    for i, block in enumerate(block_parse(msg), start=1):
        print(f"Block {i} ({len(block)} bytes): {block.hex()}")
    
Expected output:

    Original message: b'abc'
    Generated 512-bit blocks:
    Block 1 (64 bytes): 61626380000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000018

## Problem 4: SHA-256 Hash Computation

Problem 4 focuses on implementing the full SHA-256 compression process for a single 512-bit block, following the algorithm defined in the Secure Hash Standard (FIPS PUB 180-4, §6.2.2).
This is the core of the SHA-256 hashing algorithm, where the message schedule, logical functions, and constants all combine to update the intermediate hash stat.

1. Message Schedule W[0..63] (4.1)

The 512-bit block is split into 16 words, then expanded to 64 using:

    W[t] = W[t−16] + σ₀(W[t−15]) + W[t−7] + σ₁(W[t−2]) (mod 2³²).
This prepares all words needed for the 64 rounds.

2.  Initialising Working Variables (4.2)
    The eight hash values H₀–H₇ are copied into variables a–h,
    which will be updated throughout the compression rounds.

3. The 64 Compression Rounds (4.3)

Each round computes:

    T1 = h + Σ₁(e) + Ch(e,f,g) + K[t] + W[t]
    T2 = Σ₀(a) + Maj(a,b,c)

Then the variables a–h are rotated and updated as defined in FIPS 180-4.

4. Updating the Hash State (4.4)
After 64 rounds, the updated variables are added back to H₀–H₇:

        H[i] = H[i] + variable[i] (mod 2³²).

This produces the new hash state for the next block.

### How to Test Problem 4:
Example Test:

    test_block = block_parse(b"abc").__next__()

    H0 = np.array([
        U32(0x6a09e667), U32(0xbb67ae85), U32(0x3c6ef372), U32(0xa54ff53a),
     U32(0x510e527f), U32(0x9b05688c), U32(0x1f83d9ab), U32(0x5be0cd19)
    ], dtype=U32)

    H1 = hash(H0, test_block)

    print([hex(int(x)) for x in H1])

Expected output:

    ['0xba7816bf', '0x8f01cfea', '0x414140de', '0x5dae2223',
     '0xb00361a3', '0x96177a9c', '0xb410ff61', '0xf20015ad']


## Problem 5: Password Cracking
This problem applies the complete SHA-256 implementation to a real-world cyber security scenario: recovering unknown passwords by comparing hashes.

A dictionary attack was used with a list of 100 commonly used passwords, including the three correct ones. Each password candidate is hashed using the SHA-256 implementation and compared to a stored target hash.

### How Problem 5 Works:
1. Three target SHA-256 password hashes are provided.
2. A dictionary of 100 common passwords is iterated.
3. Each candidate password is encoded and hashed using the custom SHA-256 function from Problem 4.
4. If the computed hash matches a target, the password is successfully recovered.

### Results:
All three passwords were successfully cracked:
1. Password 1 → "password"
2. Password 2 → "cheese"
3. Password 3 → "P@ssw0rd"

This demonstrates that weak passwords are easily compromised and highlights the importance of password strength in real systems.

### How to Test Problem 5:
Example output when running crack_passwords():

    Testing SHA-256 implementation...
    SHA-256('password') = 5e884898…

    Cracking password 1... ✓ Found: password
    Cracking password 2... ✓ Found: cheese
    Cracking password 3... ✓ Found: P@ssw0rd

    Recovered passwords: {1: 'password', 2: 'cheese', 3: 'P@ssw0rd'}

   REFERENCES:
   1. National Institute of Standards and Technology. Secure Hash Standard (SHS), FIPS PUB 180-4 (2015), §4.1.2–§4.1.3, pp. 10–11.
    https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.180-4.pdf

   2. Menezes, A. J., van Oorschot, P. C., & Vanstone, S. A. (1996). Handbook of Applied Cryptography, CRC Press. Chapter 9 (§9.4, pp.   352–354).
    https://cacr.uwaterloo.ca/hac/