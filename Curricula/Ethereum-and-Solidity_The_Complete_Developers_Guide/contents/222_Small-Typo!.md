# 222. Small Typo!

In the last video, we wrote the following into the 'getInitialProps' function:

```
const requestCount = await campaign.methods.getRequestsCount().call();
 
const requests = await Promise.all(
  Array(requestCount)
    .fill()
    .map((element, index) => {
      return campaign.methods.requests(index).call();
    })
);
```

There's one small problem - `getRequestsCount` returns a number inside a string, but the `Array`  constructor expects to be passed a number, not a string.  To fix this, we can use the `parseInt`  function on `requestCount` before passing it into `Array` .  So please add the following:

```
  Array(parseInt(requestCount))
```

##  Resources for this lecture

---

-   [226-small-typo.zip](https://beatlesm.s3.us-west-1.amazonaws.com/ethereum-and-solidity-complete-developer-guide/226-small-typo.zip)