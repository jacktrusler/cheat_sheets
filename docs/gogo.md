
| Frontend Hook | Contract Function | Use |
| --- | --- | --- |
| useGetAvaxAssigned | getAVAXAssigned   | gets the amount of AVAX allocated to a node operator |
| useGetGGPPrice     | getGGPPriceInAvax | gets the current price of a GGP token |

## Node Operators 

Node Operators are allowed to stake up to 150% GGPtoken __value__ vs AVAX __value__ they borrow from 
liquid stakers, the minimum amount allowed is 10% GGPtoken __value__ vs AVAX __value__. This value 
is calculated by the following javascript function. 

```javascript
  const ratio = (((ggpStake + ggpAmount) * ggpPriceInAvax) / (avaxAssigned + avaxAmount)) * 100
```

