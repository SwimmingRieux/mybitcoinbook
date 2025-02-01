# Understanding CompactSize and Variable-Length Integers in Bitcoin

## 1. What’s a Variable-Length Integer?
A **variable-length integer** (varint) is an integer that takes up **only as much space as needed**. Instead of always using a fixed number of bytes (like 4 bytes for an `int32` or 8 bytes for an `int64`), it **uses fewer bytes for smaller numbers** and **more bytes for bigger numbers**.

### Why Use This?
Bitcoin transactions have lots of small numbers (e.g., input/output counts). Instead of always using 4 or 8 bytes per number, Bitcoin **saves space** by using **only 1 byte** when the number is small.

---

## 2. What’s CompactSize?
**CompactSize** is Bitcoin’s specific version of a variable-length integer. It works like this:

| **Value Range** | **Bytes Used** | **Format** |
|----------------|--------------|------------|
| **0 to 252** | **1 byte** | Just the number (`uint8_t`) |
| **253 to 65,535 (0xFFFF)** | **3 bytes** | `0xfd` + number (`uint16_t`) |
| **65,536 to 4,294,967,295 (0xFFFFFFFF)** | **5 bytes** | `0xfe` + number (`uint32_t`) |
| **4,294,967,296 to 18,446,744,073,709,551,615 (0xFFFFFFFFFFFFFFFF)** | **9 bytes** | `0xff` + number (`uint64_t`) |

---

## 3. Example of How It Works
Let’s say we need to encode **the number of inputs in a Bitcoin transaction** using CompactSize:

- If the transaction has **1 input**, it fits in **1 byte** (since 1 ≤ 252).  
  - **Encoded as:** `0x01`
- If the transaction has **300 inputs**, it’s bigger than 252. It falls into the **3-byte category**.  
  - **Encoded as:** `0xfd 0x2c 0x01` (which is `300` in little-endian `uint16_t`)
- If the transaction has **100,000 inputs**, it falls into the **5-byte category**.  
  - **Encoded as:** `0xfe 0xa0 0x86 0x01 0x00` (which is `100000` in little-endian `uint32_t`)

