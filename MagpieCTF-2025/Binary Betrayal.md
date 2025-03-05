# 🏆 Capture The Flag (CTF) Writeup  

## 🔹 Challenge: Binary Betrayal  
- **📝 Author:** Manav  
- **📂 Category:** Reversing  
- **🎯 Points:** 910  
- **⚖️ Difficulty:** Medium/Hard  
- **🏆 CTF Event:** [MagpieCTF](https://magpiectf.ca/)  
- **📅 Date:** 2025-02-21  

---

## 📝 Challenge Description  
> "For years, Cyber-Solutions was the gold standard in digital security, the guardian of businesses worldwide. But that was before Krypto arrived. The newcomer didn’t just compete—it dominated, leaving Cyber-Solutions in the dust. Their stock plummeted nearly 50%, their reputation shattered.  
>  
> And now, scandal.  
>  
> The CEO of Cyber-Solutions stands accused of something far worse than corporate failure—whispers of shady dealings, hidden transactions, and secrets buried deep in encrypted files. Then came the leak. Data from the CEO’s personal computer, exposed for the world to see.  
>  
> But is he truly guilty? Or has someone engineered the perfect takedown?  
>  
> The truth is locked away in the binary. Decode it, and uncover whether the CEO is the mastermind of his own downfall—or the victim of something far more sinister."  

---

## 📂 Files Provided  
- [`Which`](#)  
- [`Which.sha1.sig`](#)  

## 🛠️ Tools Used  
- 🖥️ **Python**  
- 🔍 **Command Line**  
- 🛡️ **pyinstxtractor**  

---

## 🕵️‍♂️ Solution  

### 🔎 Step 1: Initial Analysis  

First, identify what type of file `Which` is:  

```bash
file Which
```

**Output:**  
```
Which: ELF 64-bit LSB pie executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, for GNU/Linux 3.2.0, stripped
```

This confirms that it's a **compiled ELF executable**. To check for useful strings, we use:  

```bash
strings Which | grep python
```

**Output (Excerpt):**  
```
Failed to pre-initialize embedded python interpreter!
Failed to allocate PyConfig structure! Unsupported python version?
Failed to start embedded python interpreter!
blib-dynload/_bisect.cpython-312-x86_64-linux-gnu.so
blibpython3.12.so.1.0
```

From the output, we see multiple references to **Python 3.12**, suggesting that `Which` is likely a **Python-compiled ELF binary**.

---

### ⚙️ Step 2: Extracting the Python Code  

Since we suspect this is a **PyInstaller-packaged** binary, we can extract its contents using `pyinstxtractor`:  

📌 **Download `pyinstxtractor` from:**  
[🔗 GitHub: pyinstxtractor](https://github.com/extremecoders-re/pyinstxtractor)  

Run the extraction:  

```bash
python3 ~/pyinstxtractor/pyinstxtractor.py Which
```

**Output:**  
```
[+] Processing Which
[+] Pyinstaller version: 2.1+
[+] Python version: 3.12
[+] Length of package: 7331927 bytes
[+] Found 55 files in CArchive
[+] Beginning extraction...please standby
[+] Possible entry point: pyiboot01_bootstrap.pyc
[+] Possible entry point: pyi_rth_inspect.pyc
[+] Possible entry point: Which.pyc
[+] Found 102 files in PYZ archive
[+] Successfully extracted pyinstaller archive: Which

You can now use a Python decompiler on the extracted `.pyc` files.
```

This creates a new directory:  
📁 **`Which_extracted/`**  
Inside, there's a key file: **`Which.pyc`**, which we need to analyze.

---

### 🔎 Step 3: Disassembling `Which.pyc`  

Since this is Python **3.12**, traditional decompilers (like `uncompyle6` or `decompile3`) **won’t work**. Instead, we can use Python’s built-in `dis` module to read its bytecode:  

```python
import dis
import marshal

def disassemble_pyc(pyc_file):
    with open(pyc_file, 'rb') as f:
        # Skip the magic number (4 bytes) and timestamp (4 bytes)
        f.seek(16)
        
        # Read the marshalled code object
        code = marshal.load(f)
        
        # Disassemble the code object
        dis.dis(code)

if __name__ == "__main__":
    disassemble_pyc("Which.pyc")
```

Run this script:  

```bash
python3 disassembler.py Which.pyc
```
This will output **Python bytecode**, which we can analyze to reconstruct the original script.

<details>
  <summary>Click to reveal full bytecode Output</summary>

  ```
    0           0 RESUME                   0

  1           2 LOAD_CONST               0 (0)
              4 LOAD_CONST               1 (None)
              6 IMPORT_NAME              0 (math)
              8 STORE_NAME               0 (math)

  2          10 LOAD_CONST               0 (0)
             12 LOAD_CONST               1 (None)
             14 IMPORT_NAME              1 (random)
             16 STORE_NAME               1 (random)

  5          18 LOAD_CONST               2 (<code object main at 0x7f2af8d15610, file "Which.py", line 5>)
             20 MAKE_FUNCTION            0
             22 STORE_NAME               2 (main)

  8          24 LOAD_CONST               3 (<code object competitor_file at 0x7f2af9255df0, file "Which.py", line 8>)
             26 MAKE_FUNCTION            0
             28 STORE_NAME               3 (competitor_file)

 15          30 LOAD_CONST               4 (<code object operations_planning at 0x7f2af8d12420, file "Which.py", line 15>)
             32 MAKE_FUNCTION            0
             34 STORE_NAME               4 (operations_planning)

 26          36 LOAD_CONST               5 (<code object Legal_troubles at 0x7f2af92250b0, file "Which.py", line 26>)
             38 MAKE_FUNCTION            0
             40 STORE_NAME               5 (Legal_troubles)

 41          42 LOAD_CONST               6 (<code object operational_costs at 0x7f2af9244c90, file "Which.py", line 41>)
             44 MAKE_FUNCTION            0
             46 STORE_NAME               6 (operational_costs)

 46          48 LOAD_CONST               7 (<code object financial_records at 0x7f2af92a0ab0, file "Which.py", line 46>)
             50 MAKE_FUNCTION            0
             52 STORE_NAME               7 (financial_records)

 53          54 LOAD_CONST               8 (<code object resolution_notes at 0x7f2af9264330, file "Which.py", line 53>)
             56 MAKE_FUNCTION            0
             58 STORE_NAME               8 (resolution_notes)

 64          60 LOAD_CONST               9 (<code object data_warehousing at 0x7f2af91f5250, file "Which.py", line 64>)
             62 MAKE_FUNCTION            0
             64 STORE_NAME               9 (data_warehousing)

 72          66 LOAD_CONST              10 (<code object confusing_money_drain at 0x7f2af8d1ca30, file "Which.py", line 72>)
             68 MAKE_FUNCTION            0
             70 STORE_NAME              10 (confusing_money_drain)

 75          72 LOAD_CONST              11 (<code object distraction_toys at 0x7f2af91f57c0, file "Which.py", line 75>)
             74 MAKE_FUNCTION            0
             76 STORE_NAME              11 (distraction_toys)

 83          78 LOAD_NAME               12 (__name__)
             80 LOAD_CONST              12 ('__main__')
             82 COMPARE_OP              40 (==)
             86 POP_JUMP_IF_FALSE        9 (to 106)

 84          88 LOAD_CONST              13 ('magpieCTF{N07_57_3n0ugh}')
             90 STORE_NAME              13 (str)

 85          92 PUSH_NULL
             94 LOAD_NAME                2 (main)
             96 CALL                     0
            104 POP_TOP

 86     >>  106 LOAD_CONST              14 (<code object hidden_Stash at 0x7f2af9216730, file "Which.py", line 86>)
            108 MAKE_FUNCTION            0
            110 STORE_NAME              14 (hidden_Stash)

 92         112 LOAD_CONST              15 (<code object family_mess at 0x7f2af92644b0, file "Which.py", line 92>)
            114 MAKE_FUNCTION            0
            116 STORE_NAME              15 (family_mess)

 97         118 LOAD_CONST              16 (<code object directors_removed at 0x7f2af9216830, file "Which.py", line 97>)
            120 MAKE_FUNCTION            0
            122 STORE_NAME              16 (directors_removed)

102         124 LOAD_CONST              17 (<code object other_business at 0x7f2af92472d0, file "Which.py", line 102>)
            126 MAKE_FUNCTION            0
            128 STORE_NAME              17 (other_business)

108         130 LOAD_CONST              18 (<code object legal_business at 0x7f2af9216630, file "Which.py", line 108>)
            132 MAKE_FUNCTION            0
            134 STORE_NAME              18 (legal_business)
            136 RETURN_CONST             1 (None)

Disassembly of <code object main at 0x7f2af8d15610, file "Which.py", line 5>:
  5           0 RESUME                   0

  6           2 LOAD_GLOBAL              1 (NULL + Legal_troubles)
             12 CALL                     0
             20 POP_TOP
             22 RETURN_CONST             0 (None)

Disassembly of <code object competitor_file at 0x7f2af9255df0, file "Which.py", line 8>:
  8           0 RESUME                   0

  9           2 LOAD_CONST               1 (0)
              4 STORE_FAST               2 (result)

 10           6 LOAD_CONST               2 ('magpieCTF{Ro+_7hir+een}')
              8 STORE_FAST               3 (str)

 11          10 LOAD_GLOBAL              1 (NULL + range)
             20 LOAD_CONST               3 (1)
             22 LOAD_CONST               4 (50)
             24 CALL                     2
             32 GET_ITER
        >>   34 FOR_ITER                13 (to 64)
             38 STORE_FAST               4 (i)

 12          40 LOAD_FAST                2 (result)
             42 LOAD_FAST                0 (a)
             44 LOAD_FAST                4 (i)
             46 BINARY_OP                5 (*)
             50 LOAD_FAST                1 (b)
             52 BINARY_OP                6 (%)
             56 BINARY_OP               13 (+=)
             60 STORE_FAST               2 (result)
             62 JUMP_BACKWARD           15 (to 34)

 11     >>   64 END_FOR

 13          66 LOAD_FAST                2 (result)
             68 RETURN_VALUE

Disassembly of <code object operations_planning at 0x7f2af8d12420, file "Which.py", line 15>:
 15           0 RESUME                   0

 16           2 LOAD_GLOBAL              1 (NULL + random)
             12 LOAD_ATTR                2 (randint)
             32 LOAD_CONST               1 (1)
             34 LOAD_CONST               2 (100)
             36 CALL                     2
             44 STORE_FAST               0 (x)

 17          46 LOAD_GLOBAL              1 (NULL + random)
             56 LOAD_ATTR                2 (randint)
             76 LOAD_CONST               1 (1)
             78 LOAD_CONST               3 (50)
             80 CALL                     2
             88 STORE_FAST               1 (y)

 18          90 LOAD_GLOBAL              5 (NULL + range)
            100 LOAD_CONST               4 (25)
            102 CALL                     1
            110 GET_ITER
        >>  112 FOR_ITER                41 (to 198)
            116 STORE_FAST               2 (_)

 19         118 LOAD_CONST               5 ('magpieCTF{Rever5e_7he_[ode}')
            120 STORE_FAST               3 (str)

 20         122 LOAD_GLOBAL              7 (NULL + competitor_file)
            132 LOAD_FAST                0 (x)
            134 LOAD_FAST                1 (y)
            136 CALL                     2
            144 STORE_FAST               4 (z)

 21         146 LOAD_FAST                4 (z)
            148 LOAD_CONST               6 (2)
            150 BINARY_OP                6 (%)
            154 LOAD_CONST               7 (0)
            156 COMPARE_OP              40 (==)
            160 POP_JUMP_IF_FALSE        9 (to 180)

 22         162 LOAD_FAST                4 (z)
            164 LOAD_CONST               8 (3)
            166 BINARY_OP                5 (*)
            170 LOAD_CONST               3 (50)
            172 BINARY_OP                6 (%)
            176 STORE_FAST               0 (x)
            178 JUMP_BACKWARD           34 (to 112)

 24     >>  180 LOAD_FAST                4 (z)
            182 LOAD_FAST                0 (x)
            184 BINARY_OP                0 (+)
            188 LOAD_CONST               9 (75)
            190 BINARY_OP                6 (%)
            194 STORE_FAST               1 (y)
            196 JUMP_BACKWARD           43 (to 112)

 18     >>  198 END_FOR
            200 RETURN_CONST             0 (None)

Disassembly of <code object Legal_troubles at 0x7f2af92250b0, file "Which.py", line 26>:
 26           0 RESUME                   0

 27           2 LOAD_CONST               1 ('purpx_svanapvny_erpbeqf')
              4 STORE_FAST               0 (anflag)

 28           6 LOAD_CONST               2 ('magpieCTF{D3c0d1ng_FuN}')
              8 STORE_FAST               1 (str)

 29          10 LOAD_CONST               3 ('flag-')
             12 LOAD_FAST                0 (anflag)
             14 FORMAT_VALUE             0
             16 BUILD_STRING             2
             18 STORE_FAST               2 (aflag)

 31          20 LOAD_CONST               4 ('running?{')
             22 LOAD_FAST                2 (aflag)
             24 BINARY_OP                0 (+)
             28 LOAD_CONST               5 ('}')
             30 BINARY_OP                0 (+)

 32          34 LOAD_CONST               6 ('about{')
             36 LOAD_FAST                2 (aflag)
             38 BINARY_OP                0 (+)
             42 LOAD_CONST               5 ('}')
             44 BINARY_OP                0 (+)

 33          48 LOAD_CONST               7 ('anything{')
             50 LOAD_FAST                2 (aflag)
             52 BINARY_OP                0 (+)
             56 LOAD_CONST               5 ('}')
             58 BINARY_OP                0 (+)

 34          62 LOAD_CONST               8 ('said{')
             64 LOAD_FAST                2 (aflag)
             66 BINARY_OP                0 (+)
             70 LOAD_CONST               5 ('}')
             72 BINARY_OP                0 (+)

 35          76 LOAD_CONST               9 ('Who{')
             78 LOAD_FAST                2 (aflag)
             80 BINARY_OP                0 (+)
             84 LOAD_CONST               5 ('}')
             86 BINARY_OP                0 (+)

 30          90 BUILD_LIST               5
             92 STORE_FAST               3 (output_lines)

 37          94 LOAD_FAST                3 (output_lines)
             96 GET_ITER
        >>   98 FOR_ITER                13 (to 128)
            102 STORE_FAST               4 (line)

 38         104 LOAD_GLOBAL              1 (NULL + print)
            114 LOAD_FAST                4 (line)
            116 CALL                     1
            124 POP_TOP
            126 JUMP_BACKWARD           15 (to 98)

 37     >>  128 END_FOR

 39         130 LOAD_GLOBAL              3 (NULL + financial_records)
            140 LOAD_FAST                1 (str)
            142 CALL                     1
            150 POP_TOP
            152 RETURN_CONST             0 (None)

Disassembly of <code object operational_costs at 0x7f2af9244c90, file "Which.py", line 41>:
 41           0 RESUME                   0

 42           2 LOAD_GLOBAL              1 (NULL + range)
             12 LOAD_CONST               1 (1)
             14 LOAD_CONST               2 (37)
             16 CALL                     2
             24 GET_ITER
        >>   26 FOR_ITER                19 (to 68)
             30 STORE_FAST               1 (i)

 43          32 LOAD_FAST                0 (x)
             34 LOAD_FAST                1 (i)
             36 LOAD_FAST                0 (x)
             38 LOAD_CONST               3 (7)
             40 BINARY_OP                6 (%)
             44 BINARY_OP                5 (*)
             48 LOAD_FAST                1 (i)
             50 LOAD_CONST               4 (2)
             52 BINARY_OP                3 (<<)
             56 BINARY_OP                0 (+)
             60 BINARY_OP               25 (^=)
             64 STORE_FAST               0 (x)
             66 JUMP_BACKWARD           21 (to 26)

 42     >>   68 END_FOR

 44          70 LOAD_FAST                0 (x)
             72 LOAD_CONST               5 (3)
             74 BINARY_OP                5 (*)
             78 LOAD_CONST               6 (12345)
             80 BINARY_OP                6 (%)
             84 RETURN_VALUE

Disassembly of <code object financial_records at 0x7f2af92a0ab0, file "Which.py", line 46>:
 46           0 RESUME                   0

 47           2 LOAD_FAST                0 (data)
              4 GET_ITER
              6 LOAD_FAST_AND_CLEAR      1 (char)
              8 SWAP                     2
             10 BUILD_LIST               0
             12 SWAP                     2
        >>   14 FOR_ITER                13 (to 44)
             18 STORE_FAST               1 (char)
             20 LOAD_GLOBAL              1 (NULL + ord)
             30 LOAD_FAST                1 (char)
             32 CALL                     1
             40 LIST_APPEND              2
             42 JUMP_BACKWARD           15 (to 14)
        >>   44 END_FOR
             46 STORE_FAST               2 (temp)
             48 STORE_FAST               1 (char)

 48          50 LOAD_GLOBAL              3 (NULL + range)
             60 LOAD_CONST               1 (5)
             62 CALL                     1
             70 GET_ITER
        >>   72 FOR_ITER                26 (to 128)
             76 STORE_FAST               3 (_)

 49          78 LOAD_FAST                2 (temp)
             80 GET_ITER
             82 LOAD_FAST_AND_CLEAR      4 (val)
             84 SWAP                     2
             86 BUILD_LIST               0
             88 SWAP                     2
        >>   90 FOR_ITER                13 (to 120)
             94 STORE_FAST               4 (val)
             96 LOAD_FAST                4 (val)
             98 LOAD_CONST               2 (7)
            100 BINARY_OP                5 (*)
            104 LOAD_CONST               3 (3)
            106 BINARY_OP                9 (>>)
            110 LOAD_CONST               4 (256)
            112 BINARY_OP                6 (%)
            116 LIST_APPEND              2
            118 JUMP_BACKWARD           15 (to 90)
        >>  120 END_FOR
            122 STORE_FAST               2 (temp)
            124 STORE_FAST               4 (val)
            126 JUMP_BACKWARD           28 (to 72)

 48     >>  128 END_FOR

 50         130 LOAD_CONST               5 ('magpieCTF{Rich_3nou9h_7o_$+@y_C1e@n}')
            132 STORE_FAST               5 (str)

 51         134 LOAD_CONST               6 ('')
            136 LOAD_ATTR                5 (NULL|self + join)
            156 LOAD_FAST                2 (temp)
            158 GET_ITER
            160 LOAD_FAST_AND_CLEAR      4 (val)
            162 SWAP                     2
            164 BUILD_LIST               0
            166 SWAP                     2
        >>  168 FOR_ITER                13 (to 198)
            172 STORE_FAST               4 (val)
            174 LOAD_GLOBAL              7 (NULL + chr)
            184 LOAD_FAST                4 (val)
            186 CALL                     1
            194 LIST_APPEND              2
            196 JUMP_BACKWARD           15 (to 168)
        >>  198 END_FOR
            200 SWAP                     2
            202 STORE_FAST               4 (val)
            204 CALL                     1
            212 RETURN_VALUE
        >>  214 SWAP                     2
            216 POP_TOP

 47         218 SWAP                     2
            220 STORE_FAST               1 (char)
            222 RERAISE                  0
        >>  224 SWAP                     2
            226 POP_TOP

 49         228 SWAP                     2
            230 STORE_FAST               4 (val)
            232 RERAISE                  0
        >>  234 SWAP                     2
            236 POP_TOP

 51         238 SWAP                     2
            240 STORE_FAST               4 (val)
            242 RERAISE                  0
ExceptionTable:
  10 to 44 -> 214 [2]
  86 to 120 -> 224 [3]
  164 to 198 -> 234 [4]

Disassembly of <code object resolution_notes at 0x7f2af9264330, file "Which.py", line 53>:
 53           0 RESUME                   0

 54           2 LOAD_CONST               1 (0)
              4 STORE_FAST               3 (result)

 55           6 LOAD_GLOBAL              1 (NULL + range)
             16 LOAD_CONST               2 (1)
             18 LOAD_CONST               3 (13)
             20 CALL                     2
             28 GET_ITER
        >>   30 FOR_ITER                71 (to 176)
             34 STORE_FAST               4 (i)

 56          36 LOAD_GLOBAL              3 (NULL + math)
             46 LOAD_ATTR                4 (sqrt)
             66 LOAD_FAST                0 (a)
             68 LOAD_FAST                4 (i)
             70 BINARY_OP                5 (*)
             74 LOAD_FAST                1 (b)
             76 BINARY_OP                6 (%)
             80 LOAD_FAST                2 (c)
             82 BINARY_OP                0 (+)
             86 CALL                     1
             94 STORE_FAST               5 (temp)

 57          96 LOAD_GLOBAL              7 (NULL + int)
            106 LOAD_FAST                5 (temp)
            108 CALL                     1
            116 LOAD_CONST               4 (2)
            118 BINARY_OP                6 (%)
            122 LOAD_CONST               1 (0)
            124 COMPARE_OP              40 (==)
            128 POP_JUMP_IF_FALSE       17 (to 164)

 58         130 LOAD_CONST               5 ('magpieCTF{N0t_Th3_R34L_Fl4g}')
            132 STORE_FAST               6 (str)

 59         134 LOAD_FAST                3 (result)
            136 LOAD_GLOBAL              7 (NULL + int)
            146 LOAD_FAST                5 (temp)
            148 CALL                     1
            156 BINARY_OP               13 (+=)
            160 STORE_FAST               3 (result)
            162 JUMP_BACKWARD           67 (to 30)

 61     >>  164 LOAD_FAST                3 (result)
            166 LOAD_FAST                4 (i)
            168 BINARY_OP               25 (^=)
            172 STORE_FAST               3 (result)
            174 JUMP_BACKWARD           73 (to 30)

 55     >>  176 END_FOR

 62         178 LOAD_FAST                3 (result)
            180 RETURN_VALUE

Disassembly of <code object data_warehousing at 0x7f2af91f5250, file "Which.py", line 64>:
 64           0 RESUME                   0

 65           2 LOAD_FAST                0 (n)
              4 BUILD_LIST               1
              6 STORE_FAST               1 (arr)

 66           8 LOAD_GLOBAL              1 (NULL + range)
             18 LOAD_CONST               1 (1)
             20 LOAD_CONST               2 (256)
             22 CALL                     2
             30 GET_ITER
        >>   32 FOR_ITER                28 (to 92)
             36 STORE_FAST               2 (i)

 67          38 LOAD_FAST                1 (arr)
             40 LOAD_ATTR                3 (NULL|self + append)
             60 LOAD_FAST                1 (arr)
             62 LOAD_CONST               3 (-1)
             64 BINARY_SUBSCR
             68 LOAD_FAST                2 (i)
             70 BINARY_OP                5 (*)
             74 LOAD_CONST               4 (10007)
             76 BINARY_OP                6 (%)
             80 CALL                     1
             88 POP_TOP
             90 JUMP_BACKWARD           30 (to 32)

 66     >>   92 END_FOR

 68          94 LOAD_GLOBAL              5 (NULL + len)
            104 LOAD_FAST                1 (arr)
            106 CALL                     1
            114 LOAD_CONST               1 (1)
            116 COMPARE_OP              68 (>)
            120 POP_JUMP_IF_FALSE       65 (to 252)

 69     >>  122 LOAD_GLOBAL              1 (NULL + range)
            132 LOAD_CONST               5 (0)
            134 LOAD_GLOBAL              5 (NULL + len)
            144 LOAD_FAST                1 (arr)
            146 CALL                     1
            154 LOAD_CONST               1 (1)
            156 BINARY_OP               10 (-)
            160 LOAD_CONST               6 (2)
            162 CALL                     3
            170 GET_ITER
            172 LOAD_FAST_AND_CLEAR      2 (i)
            174 SWAP                     2
            176 BUILD_LIST               0
            178 SWAP                     2
        >>  180 FOR_ITER                16 (to 216)
            184 STORE_FAST               2 (i)
            186 LOAD_FAST                1 (arr)
            188 LOAD_FAST                2 (i)
            190 BINARY_SUBSCR
            194 LOAD_FAST                1 (arr)
            196 LOAD_FAST                2 (i)
            198 LOAD_CONST               1 (1)
            200 BINARY_OP                0 (+)
            204 BINARY_SUBSCR
            208 BINARY_OP                0 (+)
            212 LIST_APPEND              2
            214 JUMP_BACKWARD           18 (to 180)
        >>  216 END_FOR
            218 STORE_FAST               1 (arr)
            220 STORE_FAST               2 (i)

 68         222 LOAD_GLOBAL              5 (NULL + len)
            232 LOAD_FAST                1 (arr)
            234 CALL                     1
            242 LOAD_CONST               1 (1)
            244 COMPARE_OP              68 (>)
            248 POP_JUMP_IF_FALSE        1 (to 252)
            250 JUMP_BACKWARD           65 (to 122)

 70     >>  252 LOAD_FAST                1 (arr)
            254 LOAD_CONST               5 (0)
            256 BINARY_SUBSCR
            260 RETURN_VALUE
        >>  262 SWAP                     2
            264 POP_TOP

 69         266 SWAP                     2
            268 STORE_FAST               2 (i)
            270 RERAISE                  0
ExceptionTable:
  176 to 216 -> 262 [2]

Disassembly of <code object confusing_money_drain at 0x7f2af8d1ca30, file "Which.py", line 72>:
 72           0 RESUME                   0

 73           2 LOAD_CONST               1 ('')
              4 LOAD_ATTR                1 (NULL|self + join)
             24 LOAD_FAST                0 (data)
             26 GET_ITER
             28 LOAD_FAST_AND_CLEAR      1 (char)
             30 SWAP                     2
             32 BUILD_LIST               0
             34 SWAP                     2
        >>   36 FOR_ITER                31 (to 102)
             40 STORE_FAST               1 (char)
             42 LOAD_GLOBAL              3 (NULL + chr)
             52 LOAD_GLOBAL              5 (NULL + ord)
             62 LOAD_FAST                1 (char)
             64 CALL                     1
             72 LOAD_CONST               2 (3)
             74 BINARY_OP                5 (*)
             78 LOAD_CONST               3 (256)
             80 BINARY_OP                6 (%)
             84 LOAD_CONST               4 (7)
             86 BINARY_OP               12 (^)
             90 CALL                     1
             98 LIST_APPEND              2
            100 JUMP_BACKWARD           33 (to 36)
        >>  102 END_FOR
            104 SWAP                     2
            106 STORE_FAST               1 (char)
            108 CALL                     1
            116 RETURN_VALUE
        >>  118 SWAP                     2
            120 POP_TOP
            122 SWAP                     2
            124 STORE_FAST               1 (char)
            126 RERAISE                  0
ExceptionTable:
  32 to 102 -> 118 [4]

Disassembly of <code object distraction_toys at 0x7f2af91f57c0, file "Which.py", line 75>:
 75           0 RESUME                   0

 76           2 LOAD_GLOBAL              1 (NULL + range)
             12 LOAD_CONST               1 (50)
             14 CALL                     1
             22 GET_ITER
             24 LOAD_FAST_AND_CLEAR      0 (_)
             26 SWAP                     2
             28 BUILD_LIST               0
             30 SWAP                     2
        >>   32 FOR_ITER                24 (to 84)
             36 STORE_FAST               0 (_)
             38 LOAD_GLOBAL              3 (NULL + random)
             48 LOAD_ATTR                4 (randint)
             68 LOAD_CONST               2 (0)
             70 LOAD_CONST               3 (100)
             72 CALL                     2
             80 LIST_APPEND              2
             82 JUMP_BACKWARD           26 (to 32)
        >>   84 END_FOR
             86 STORE_FAST               1 (noise)
             88 STORE_FAST               0 (_)

 77          90 LOAD_GLOBAL              1 (NULL + range)
            100 LOAD_GLOBAL              7 (NULL + len)
            110 LOAD_FAST                1 (noise)
            112 CALL                     1
            120 CALL                     1
            128 GET_ITER
        >>  130 FOR_ITER                47 (to 228)
            134 STORE_FAST               2 (i)

 78         136 LOAD_FAST                1 (noise)
            138 LOAD_FAST                2 (i)
            140 BINARY_SUBSCR
            144 LOAD_CONST               4 (17)
            146 BINARY_OP                5 (*)
            150 LOAD_FAST                2 (i)
            152 BINARY_OP                0 (+)
            156 LOAD_CONST               5 (256)
            158 BINARY_OP                6 (%)
            162 LOAD_FAST                1 (noise)
            164 LOAD_FAST                2 (i)
            166 STORE_SUBSCR

 79         170 LOAD_FAST                1 (noise)
            172 LOAD_FAST                2 (i)
            174 BINARY_SUBSCR
            178 LOAD_CONST               6 (3)
            180 BINARY_OP                6 (%)
            184 LOAD_CONST               2 (0)
            186 COMPARE_OP              40 (==)
            190 POP_JUMP_IF_TRUE         1 (to 194)
            192 JUMP_BACKWARD           32 (to 130)

 80     >>  194 LOAD_FAST                1 (noise)
            196 LOAD_FAST                2 (i)
            198 COPY                     2
            200 COPY                     2
            202 BINARY_SUBSCR
            206 LOAD_FAST                2 (i)
            208 LOAD_CONST               7 (7)
            210 BINARY_OP                5 (*)
            214 BINARY_OP               25 (^=)
            218 SWAP                     3
            220 SWAP                     2
            222 STORE_SUBSCR
            226 JUMP_BACKWARD           49 (to 130)

 77     >>  228 END_FOR

 81         230 LOAD_GLOBAL              9 (NULL + sum)
            240 LOAD_FAST                1 (noise)
            242 CALL                     1
            250 RETURN_VALUE
        >>  252 SWAP                     2
            254 POP_TOP

 76         256 SWAP                     2
            258 STORE_FAST               0 (_)
            260 RERAISE                  0
ExceptionTable:
  28 to 84 -> 252 [2]

Disassembly of <code object hidden_Stash at 0x7f2af9216730, file "Which.py", line 86>:
 86           0 RESUME                   0

 87           2 LOAD_CONST               1 (42)
              4 STORE_FAST               1 (key)

 88           6 LOAD_FAST                0 (data)
              8 GET_ITER
        >>   10 FOR_ITER                19 (to 52)
             14 STORE_FAST               2 (char)

 89          16 LOAD_FAST                1 (key)
             18 LOAD_GLOBAL              1 (NULL + ord)
             28 LOAD_FAST                2 (char)
             30 CALL                     1
             38 BINARY_OP                5 (*)
             42 LOAD_CONST               2 (98765)
             44 BINARY_OP                6 (%)
             48 STORE_FAST               1 (key)
             50 JUMP_BACKWARD           21 (to 10)

 88     >>   52 END_FOR

 90          54 LOAD_FAST                1 (key)
             56 RETURN_VALUE

Disassembly of <code object family_mess at 0x7f2af92644b0, file "Which.py", line 92>:
 92           0 RESUME                   0

 93           2 LOAD_CONST               1 ('')
              4 LOAD_ATTR                1 (NULL|self + join)
             24 LOAD_FAST                0 (encoded)
             26 GET_ITER
             28 LOAD_FAST_AND_CLEAR      1 (char)
             30 SWAP                     2
             32 BUILD_LIST               0
             34 SWAP                     2
        >>   36 FOR_ITER                31 (to 102)
             40 STORE_FAST               1 (char)
             42 LOAD_GLOBAL              3 (NULL + chr)
             52 LOAD_GLOBAL              5 (NULL + ord)
             62 LOAD_FAST                1 (char)
             64 CALL                     1
             72 LOAD_CONST               2 (7)
             74 BINARY_OP               10 (-)
             78 LOAD_CONST               3 (3)
             80 BINARY_OP                2 (//)
             84 LOAD_CONST               4 (256)
             86 BINARY_OP                6 (%)
             90 CALL                     1
             98 LIST_APPEND              2
            100 JUMP_BACKWARD           33 (to 36)
        >>  102 END_FOR
            104 SWAP                     2
            106 STORE_FAST               1 (char)
            108 CALL                     1
            116 STORE_FAST               2 (intermediate)

 94         118 LOAD_CONST               5 ('magpieCTF{C0d3_3xp0s3d}')
            120 STORE_FAST               3 (str)

 95         122 LOAD_CONST               1 ('')
            124 LOAD_ATTR                1 (NULL|self + join)
            144 LOAD_GLOBAL              7 (NULL + reversed)
            154 LOAD_FAST                2 (intermediate)
            156 CALL                     1
            164 CALL                     1
            172 RETURN_VALUE
        >>  174 SWAP                     2
            176 POP_TOP

 93         178 SWAP                     2
            180 STORE_FAST               1 (char)
            182 RERAISE                  0
ExceptionTable:
  32 to 102 -> 174 [4]

Disassembly of <code object directors_removed at 0x7f2af9216830, file "Which.py", line 97>:
 97           0 RESUME                   0

 98           2 LOAD_FAST                2 (z)
              4 LOAD_CONST               1 (3)
              6 BINARY_OP                6 (%)
             10 LOAD_CONST               2 (0)
             12 COMPARE_OP              40 (==)
             16 POP_JUMP_IF_FALSE        8 (to 34)

 99          18 LOAD_FAST                0 (x)
             20 LOAD_FAST                1 (y)
             22 BINARY_OP                5 (*)
             26 LOAD_FAST                2 (z)
             28 BINARY_OP               10 (-)
             32 RETURN_VALUE

100     >>   34 LOAD_FAST                0 (x)
             36 LOAD_FAST                1 (y)
             38 BINARY_OP               12 (^)
             42 LOAD_FAST                2 (z)
             44 BINARY_OP               12 (^)
             48 RETURN_VALUE

Disassembly of <code object other_business at 0x7f2af92472d0, file "Which.py", line 102>:
102           0 RESUME                   0

103           2 LOAD_CONST               1 ('')
              4 STORE_FAST               1 (output)

104           6 LOAD_FAST                0 (data)
              8 GET_ITER
        >>   10 FOR_ITER                34 (to 82)
             14 STORE_FAST               2 (char)

105          16 LOAD_FAST                1 (output)
             18 LOAD_GLOBAL              1 (NULL + chr)
             28 LOAD_GLOBAL              3 (NULL + ord)
             38 LOAD_FAST                2 (char)
             40 CALL                     1
             48 LOAD_CONST               2 (47)
             50 BINARY_OP                0 (+)
             54 LOAD_CONST               3 (256)
             56 BINARY_OP                6 (%)
             60 LOAD_CONST               4 (89)
             62 BINARY_OP               12 (^)
             66 CALL                     1
             74 BINARY_OP               13 (+=)
             78 STORE_FAST               1 (output)
             80 JUMP_BACKWARD           36 (to 10)

104     >>   82 END_FOR

106          84 LOAD_FAST                1 (output)
             86 RETURN_VALUE

Disassembly of <code object legal_business at 0x7f2af9216630, file "Which.py", line 108>:
108           0 RESUME                   0

109           2 LOAD_CONST               1 ('irrelevant')
              4 STORE_FAST               2 (c)

110           6 LOAD_CONST               2 ('magpieCTF{R3v3r53_3v3ryth1ng}')
              8 STORE_FAST               3 (str)

111          10 LOAD_GLOBAL              1 (NULL + directors_removed)
             20 LOAD_FAST                0 (a)
             22 LOAD_FAST                1 (b)
             24 LOAD_GLOBAL              3 (NULL + len)
             34 LOAD_FAST                2 (c)
             36 CALL                     1
             44 CALL                     3
             52 RETURN_VALUE
  ```

</details>

### 🔎 Step 3: Follow Bytecode Instructions  
By analyzing the bytecode, we notice several fake flags (`magpieCTF{Ro+_7hir+een}`), hinting that **ROT13 obfuscation** is used. Decoding `purpx_svanapvny_erpbeqf` with ROT13 gives `check_financial_records`. Looking at the function `financial_records`, we find:

```python
 130 LOAD_CONST 5 ('magpieCTF{Rich_3nou9h_7o_$+@y_C1e@n}')
```

Thus, the **flag is**:  
`magpieCTF{Rich_3nou9h_7o_$+@y_C1e@n}`

## 🎯 Conclusion  

- The challenge involved **analyzing a compiled ELF binary** that was actually a **Compiled Python Binaries**.  
- We identified it as **Python 3.12** and used **pyinstxtractor** to extract `.pyc` files.  
- Since modern Python versions make decompilation harder, we used **Python’s `dis` module** to analyze the bytecode instead.  

🎯 **Next Steps:**  
- Further reverse-engineer the bytecode to recover meaningful logic and extract the **hidden flag**.  

🔍 **CTF Tip:** Always check for Compiled Python Binaries when analyzing ELF/EXE files in reversing challenges!  

🚀 **Happy Hacking!**

