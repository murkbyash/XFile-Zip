<p align="center">
  <img src="https://github.com/Pradumn-Patidar/huffman-compression/blob/main/images/logo.png">
</p>

<h1 align="center">Huffman Coding</h1>

<p align="center">
  <a href="https://www.linkedin.com/in/ashish-uike-7126391b4/">
    <img src="https://img.shields.io/badge/-LinkedIn-black.svg?style=plastic-square&logo=linkedin&colorB=555"
      alt="linkedin" />
  </a>
  <a href="https://github.com/murkbyash/XFile-Zip/issues">
    <img src="https://img.shields.io/github/issues-raw/murkbyash/XFile-Zip"
      alt="open issues" />
  </a>
  <a href="https://github.com/murkbyash/XFile-Zip/issues?q=is%3Aissue+is%3Aclosed">
    <img src="https://img.shields.io/github/issues-closed-raw/murkbyash/XFile-Zip"
      alt="closed issues" />
  </a>
</p>

A C++ **file archiver** and **archive extractor** programs based on Huffman's compression algorithm
## Table of Contents
* [Theory](#Theory)
* [How does it work](#how-does-it-work)
  * [Compressor](#compressor)
  * [Decompressor](#decompressor)
* [How to use it?](#how-to-use-it)
* [Features](#Features)

## Theory
Huffman compression is based on huffman coding technique. The huffman coding creates an optimal binary tree that is constructed based on frequency of an item/character in a file.


Let's take an example of a file or string containing data like "AAABBCAAADDFFAAAADCCCDAADDDAAACGAAACACA"
```
character     frequency
    A             20
    B              2
    C              7
    D              7
    F              2
    G              1
```
The above following data will generate a binary tree starting from the characters having the lowest frequency, and construct till we use all the characters as follows:

```
Pass 1:
(A, 20) (C, 7) (D, 7) (B, 2) (F, 2) (G, 1)                      (A, 20) (C, 7) (D, 7) (**, 3)  (B, 2)   
                                                   =>                                  /   \
                                                                                      /     \
                                                                                   (F, 2) (G, 1)

Pass 2:
(A, 20) (C, 7) (D, 7) (**, 3)  (B, 2)                           (A, 20) (C, 7) (D, 7) (**, 5)
                       /   \                                                           /   \
                      /     \                                                         /     \
                   (F, 2) (G, 1)                   =>                              (**, 3) (B, 2)
                                                                                    /   \
                                                                                   /     \
                                                                                (F, 2) (G, 1)

Pass 3:
(A, 20) (C, 7) (D, 7) (**, 5)                                    (A, 20) (C, 7) (**, 12)
                       /   \                                                     /   \
                      /     \                                                   /     \
                   (**, 3) (B, 2)                  =>                        (D, 7) (**, 5)
                    /   \                                                            /   \
                   /     \                                                          /     \
                 (F, 2) (G, 1)                                                   (**, 3) (B, 2)
                                                                                  /   \
                                                                                 /     \
                                                                              (F, 2) (G, 1)
                                                                              
Pass 4:
(A, 20) (**, 12) (C, 7)                                           (A, 20) (**, 19)
          /   \                                                             /   \
         /     \                                                           /     \
      (D, 7) (**, 5)                                                   (**, 12) (C, 7)
              /   \                                                      /   \
             /     \                               =>                   /     \
          (**, 3) (B, 2)                                            (D, 7) (**, 5)
           /   \                                                            /   \
          /     \                                                          /     \
       (F, 2) (G, 1)                                                    (**, 3) (B, 2)
                                                                         /   \
                                                                        /     \
                                                                     (F, 2) (G, 1)
                                                                     
Pass 5 (Final), with huffman code:

Left Branch denoting 0 and right 1

(A, 20) (**, 19)                                                         (**, 39)
          /   \                                                           /   \
         /     \                                                      (0)/     \(1)
     (**, 12) (C, 7)                                          [0] <= (A, 20) (**, 19)
       /   \                                                                 /   \
      /     \                                                            (0)/     \(1)
  (D, 7) (**, 5)                                    =>                  (**, 12) (C, 7) => [11]
          /   \                                                          /   \
         /     \                                                     (0)/     \(1)
     (**, 3) (B, 2)                                        [100] <= (D, 7) (**, 5)
      /   \                                                                 /   \
     /     \                                                               /     \(1)
  (F, 2) (G, 1)                                                        (**, 3) (B, 2) => [1011]
                                                                        /   \
                                                                    (0)/     \(1)
                                                         [10100] <= (F, 2) (G, 1) => [10101]
                                                         
Final Huffman Codes:
character     frequency       Huffman Codes       Actual Binary
    A             20                  0             01000001
    B              2               1011             01000010
    C              7                 11             01000011
    D              7                100             01000100
    F              2              10100             01000110
    G              1              10101             01000111
__________________________________________________________________
                  39                 75                  312
```
Hence the resultant data is stored as binary written as '0001011101100010010010100101000000100111111100001001001000001110101000110110*00000*', which has 75 bits
plus 5 digits appended to round off the remaining bits while storing in the file.

## How does it work?
_You can check out documentation inside [Compressor.cpp](https://github.com/Pradumn-Patidar/huffman-compression/blob/main/Compressor.cpp) and [Decompressor.cpp](https://github.com/Pradumn-Patidar/huffman-compression/blob/main/Decompressor.cpp) files to help you understand Huffman's algorithm's inner workings._
### Compressor:
The `Compressor` is a 2-pass program. What I mean by this is that the `Compressor` reads input files twice.

In the first pass, the program counts usage frequency of every unique byte and creates a weighted translation tree for every used unique byte inversely proportional to its usage frequency and then writes this transformation info to the compressed file for decompression purposes

In the second pass, the program translates input files according to the translation tree and writes it to the newly created compressed file

### Decompressor:
The `Decompressor` is a 1-pass program:
The `Decompressor` first reads translation info and creates a binary tree from it. After this process is done, it uses this binary translation tree to decode the rest of the file

## How to use it?
1. Compile with `make` using your favourate shell:
```
make all
```
2. After running make, you can use `archive` command below to compress the file you want:
* To compress one file use:
```
./archive {{filename}}
```
* To compress multiple files use:

```
./archive {{filename1}} {{filename2}} ...
```
3.  And to decompress a compressed file, use the `extract` command below:
```
./extract {{filename}}
```
## Features
  * Can compress any file and folder you want
  * Useful for educative purposes
  * Compresses multiple files 
  * Uses more functionalized structure