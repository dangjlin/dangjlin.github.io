---
layout: post
title: "如何寫出clean code 的參考資料"
date: 2017-02-10 14:32:20 +0800
comments: true
categories: ruby rails code programming
---
#如何設計好的程式架構跟安排，寫出clean code#

這是在臉書上徵詢請教大大的筆記資料
## thanks TaiAn suggestions:
Leetcode 主要還是演算法
如果是 OO (Objective Oriented) 的話，
0. Confident Ruby
1. Practical Object oriented design in ruby
2. Implementation pattern
3. The art of readable code

我記得後三本都有中文。如果不是 ruby 的話，第 0 本可以省略。也有人推薦 code complete 2，但是實在很厚，語氣也有點教條。

[https://sourcemaking.com/refactoring](https://sourcemaking.com/refactoring)

## thanks Scott Tsai suggestions:
1. 閱讀某篇 coding style 規範 (e.g. Google 的) 思考辯證其中規則 (e.g. 命名，變數長度與可見範圍)
2. 閱讀某高品質的 open source 專案的程式，或可從 [http://aosabook.org/en/index.html](http://aosabook.org/en/index.html) 找個目標。 500 lines or less 的程式也頗適合。

附註：Leetcode 只求解出演算法題目，會鼓勵實作快速、執行也夠快的程式碼，與寫出 "clean code" 的目標應是背道而馳。


## thank unknow friend
一本聖經叫clean code

## thanks Jason
裝rubocop，養成每次git commit 之前跑檢查，把檢查出的錯誤堅持修掉，然後隨經驗的累積調整 rubocop 的設定
