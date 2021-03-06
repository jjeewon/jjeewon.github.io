---
layout: post
title:  "Hash Table์ด๋?"
author: ella
categories: [ Data Structure ]
image: assets/images/ht_title.png
---
๐  
# ๊ฐ๋ 5๏ธโฃ Hash Table  

ํด์ฌํ์ด๋ธ์ key-value ์์ผ๋ก ์ ์ฅํ๋ ๋ฐ์ดํฐ ๊ตฌ์กฐ์ ํ์์ด๋ค. ```hash function```์ ํตํด ```key```์ ์ฐ์ ์ฐ์ฐ์ ํ๊ฒ ๋๋๋ฐ, ๊ทธ ๊ฒฐ๊ณผ๋ฅผ ๋ณดํต ```hash value``` ํน์ ```hash``` ๋ผ๊ณ  ๋ถ๋ฅธ๋ค. ```hash value```๋ ํด์ฌํ์ด๋ธ์์ key-value ์์ ```index```๊ฐ ๋๋ค. 
<p align="center"><img src="/assets/images/ht_1.png"></p>  
## Hash Table์ ๊ตฌ์ฑ ์์ = Hash function + Array
### (1) Hash function
* Hash function์ key-value ์์ ์ธ๋ฑ์ค๋ฅผ ๊ฒฐ์ ํจ
* ์ข์ Hash table์ ๊ตฌ์ฑํ๊ธฐ ์ํด์๋, hash function์ ํจ์จ์ ์ผ๋ก ๋ง๋ค์ด์ผ ํจ 
* Hash function์ one-way์ฌ์ผ ํจ. ์ฆ, key๊ฐ hash์์ ๋์ถ๋  ์ ์์ผ๋ฉด ์๋จ
* Hash funtion์ ๊ฐ์ hash์์ ์๋ก ๋ค๋ฅธ key๋ค์ ๋ง๋ค์ด๋ด๋ฉด ์๋จ

### (2) Array
* array๋ ํ์ด๋ธ์์ key-value entry๋ค์ ๊ฐ์ง๊ณ  ์์
* array์ ํฌ๊ธฐ๋ ๋ฐ์ดํฐ์ ์์ ๋ง๊ฒ ์ธํ๋์ด์ผ ํจ 

## Hash table์์์ Collision๊ณผ ํด๊ฒฐ๋ฐฉ๋ฒ
* ๋ ๊ฐ์ key๊ฐ ๊ฐ์ ์ธ๋ฑ์ค์ ๋งคํ๋๋ฉด, collision์ด ์ผ์ด๋จ
* Collision์ ํํผํ๋ ๋ฐฉ๋ฒ์ ํฌ๊ฒ ์ธ ๊ฐ์ง๊ฐ ์์
* Linear probing, Chaining, Resizing the hash table

### (1) Linear probing
* ๋ง์ฝ์ ํ ์์ด ์ด๋ฏธ occupied๋ ์ฌ๋กฏ์ ํด์ฌ๋์๋ค๋ฉด, ํ์ด๋ธ์์ ๋ค์์ผ๋ก ๋น ์ฌ๋กฏ์ ์๋ฆฌ๋ฅผ ```linear```ํ๊ฒ ๊ฒ์ํ๋ค.

### (2) Chaining
* ์ฌ๊ธฐ์ hash table์ ```linked list```์ array์
* ๊ฐ์ ์ธ๋ฑ์ค์ ๋งคํ๋๋ ๋ชจ๋  key๋ฅผ ํด๋น ์ธํ์ค์ ```linked list node```๋ก ์ ์ฅํ๋ค.
<p align="center"><img src="/assets/images/ht_2.png"></p>

### (3) Resizing the hash table
* hash entry๋ค์ ์๋ก ํผ์ ธ์ ๋จ์ด์ ธ์๊ฒ ํ๊ธฐ ์ํด์, hash table์ ์ฌ์ด์ฆ๋ฅผ ์ฆ๊ฐ์ํด
* ```threshold``` ๊ฐ์ resizingํ๊ธฐ ์ ์ ํด์ฌํ์ด๋ธ์์ ์ํธ๋ฆฌ๊ฐ ์ฐจ์งํ  ์ ์๋ ๋น์จ์
* ๋ฐ์ ๊ทธ๋ฆผ์์ threshold ๊ฐ์ด 0.6์ด๋ผ๊ณ  ํ์๋, 3/5 = 0.6์ผ๋ก threshold ๊ฐ์ ๋๊ฒ๋๋ฏ๋ก size๋ฅผ 10์ผ๋ก ๋๋ ค์ค ๊ฒ์ ๋ณผ ์ ์๋ค.
<p align="center"><img src="/assets/images/ht_3.png"></p>

## ๐ ์๊ฐ๋ณต์ก๋ ์ ๋ฆฌํ 
<p align="center"><img src="/assets/images/ht_4.png"></p>





[์ฐธ๊ณ  ์ฌ์ดํธ]  
- [https://www.educative.io/edpresso/what-is-a-hash-table](https://www.educative.io/edpresso/what-is-a-hash-table)
