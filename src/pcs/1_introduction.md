# ZK Ecosystem
There are many proving systems. They are very similar between each other. The main differences come from the arithmetization and the PCS used. In this section we'll see different polynomial commitment schemes. If you're not understanding something, go to the "Introduction to ZK Proofs" chapter.

## PCS
Next, there is a table with the most popular polynomial commitment schemes. **Transparent** refers to the trusted setup, FRI doesn't need trusted setup while KZG does.

![image](images/table_pcs.png)

Minimum data:
- KZG: It is the most succinct of the PCS, but has a trusted setup. Uses elliptic curve cryptography to "hide" data inside the curve group.
- IPA: stands for "Inner product argument", it is a PCS built over another protocol that actually convinces $V$ that $P$ knows the solution of an inner product. U
- FRI: uses a mix of merkle trees and a low degree test.
https://eprint.iacr.org/2022/1216.pdf

## Protocols
Some popular protocols are:
- Groth16: first popular protocol. It is from 2016, it has arithmetization based on circuits and uses the KZG polynomial commitment scheme.
- Plonk: similar to groth16, also very popular. Uses circuits and KZG. Its advantage with respect to groth16 is that it encodes the witness more efficiently, and improves performance.
- STARK: can use any arithmetization, but uses the FRI protocol.
