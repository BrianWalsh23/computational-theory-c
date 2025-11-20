HOW TO RUN THE PROJECT:
1. Open the notebook file problems.ipynb
2. Ensure numpy is installed. Use " pip install numpy " in the terminal
3. Select Python kernel (.venv (Python 3.x)).
4. Click Run All.
5. The output will show binary values for each operation, for example:
    x = np.uint32(0b11110000)
    y = np.uint32(0b10101010)
    z = np.uint32(0b01010101)

    print(bin(int(Parity(x, y, z))))  # Output: 0b10011111

TESTING:
Each function can be tested using simple binary inputs or random 32-bit values.
For example:
    x = np.uint32(0b11110000)
    y = np.uint32(0b10101010)
    z = np.uint32(0b01010101)

    print("Parity:", bin(int(Parity(x, y, z))))
    print("Ch:", bin(int(Ch(x, y, z))))
    print("Maj:", bin(int(Maj(x, y, z))))

Problem 1: Binary Words and Operations.
Focuses on implementing and understanding the binary operations that form the mathematical foundation of the SHA-256 hashing algorithm, as defined in the NIST Secure Hash Standard (FIPS PUB 180-4).
These operations are all performed on 32-bit unsigned integers, and include logical and bitwise transformations such as:
    Parity(x, y, z) – returns 1 if an odd number of input bits are 1.

    Ch(x, y, z) – chooses bits from y or z based on x (the "choose" function).

    Maj(x, y, z) – returns the majority bit among the three inputs.

    Σ₀, Σ₁, σ₀, and σ₁ – represent rotation and shift functions defined in the standard, using right-rotate (ROTR) and right-shift (SHR) operations.

How Problem 1 works.
1. Bitwise operations (^, &, ~) are used to manipulate binary words at the bit level.

2. NumPy’s uint32 type ensures that all operations stay within 32-bit boundaries, matching cryptographic specifications.

3. Helper functions such as ROTR (rotate right) and SHR (shift right) are reused across multiple parts of the algorithm to create the Σ and σ functions.

Problem 2: Generating and Verifying SHA-256 Constants.
This problem focuses on creating and validating the 64 constants (K values) used in the SHA-256 hashing algorithm.
These constants are calculated from the fractional parts of the cube roots of the first 64 prime numbers, as defined in the NIST Secure Hash Standard (FIPS PUB 180-4).

How Problem 2 works:

1. Prime Generation: The first 64 prime numbers are calculated (2, 3, 5, 7, 11, …).

2. Constant Calculation: For each prime p, the cube root is taken, the fractional part is multiplied by 2³², and then converted into a  32-bit unsigned integer.

3. Display Function: The constants are printed neatly, 8 per row, for example:
    K[00]=0x428a2f98  K[01]=0x71374491  K[02]=0xb5c0fbcf ...

4. Verification Function: The generated constants are compared against the official constants from the Secure Hash Standard (FIPS 180-4).
    If everything matches, the output confirms:
    All 64 SHA-256 constants match the official FIPS 180-4 values.

How to test Problem 2:
    To test the output, run the following in your notebook:
        display_sha256_constants()
        verify_sha256_constants()
    Expected output:
        All 64 SHA-256 constants match the official FIPS 180-4 values.

Problem 3: Message Padding and Parsing.
This problem focuses on preparing a message for SHA-256 processing by applying padding and splitting it into 512-bit blocks, following the rules defined in the NIST Secure Hash Standard (FIPS PUB 180-4, §5.1.1–§5.2.1).

How Problem 3 works:
1. The original message length (ℓ) is calculated in bits using message_length_bits().
2. A single '1' bit (0x80) is appended, followed by enough '0' bits to make the total length congruent to 448 mod 512.
3. The 64-bit big-endian representation of the original message length (ℓ) is added to the end of the padded message.
4. The fully padded message is split into 512-bit (64-byte) blocks using a generator function block_parse(msg),
   which yields each block as a separate bytes object for further processing.

How to test Problem 3:
    Example test:
    msg = b"abc"
    for i, block in enumerate(block_parse(msg), start=1):
        print(f"Block {i} ({len(block)} bytes): {block.hex()}")
    
    Expected output:
    Original message: b'abc'
    Generated 512-bit blocks:
    Block 1 (64 bytes): 61626380000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000018

   REFERENCES:
   1. National Institute of Standards and Technology. Secure Hash Standard (SHS), FIPS PUB 180-4 (2015), §4.1.2–§4.1.3, pp. 10–11.
    https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.180-4.pdf

   2. Menezes, A. J., van Oorschot, P. C., & Vanstone, S. A. (1996). Handbook of Applied Cryptography, CRC Press. Chapter 9 (§9.4, pp.   352–354).
    https://cacr.uwaterloo.ca/hac/