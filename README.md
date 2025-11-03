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







   REFERENCES:
   1. National Institute of Standards and Technology. Secure Hash Standard (SHS), FIPS PUB 180-4 (2015), §4.1.2–§4.1.3, pp. 10–11.
    https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.180-4.pdf

   2. Menezes, A. J., van Oorschot, P. C., & Vanstone, S. A. (1996). Handbook of Applied Cryptography, CRC Press. Chapter 9 (§9.4, pp.   352–354).
    https://cacr.uwaterloo.ca/hac/