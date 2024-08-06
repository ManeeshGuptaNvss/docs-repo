---
title: Buy and Sell Stocks With Transaction Fee - Leetcode 714
summary: A brief description of my document.
authors:
    - Maneesh Gupta
    - Nalluru Srinithya
date: 2024-08-06
some_url: https://leetcode.com/problems/best-time-to-buy-and-sell-stock-with-transaction-fee/description/

hide:
  - navigation
  - toc

tags:
  - Leetcode
---
``` cpp
#include <bits/stdc++.h>
using namespace std;
int maximumProfit(int n,int fee,vector<int>&values){
    int aheadNotBuy,aheadBuy,curBuy,curNotBuy;
                    aheadBuy=0;
                    aheadNotBuy=0;
    for(int ind=n-1;ind>=0;ind--){
       // sell
       curNotBuy=max(values[ind]+aheadBuy,
                                0+aheadNotBuy);
        // buy
       curBuy=max(-values[ind]-fee+aheadNotBuy,
                                 0+aheadBuy);
         aheadBuy=curBuy;
         aheadNotBuy=curNotBuy;
                             
    }
    return aheadBuy;
}
```