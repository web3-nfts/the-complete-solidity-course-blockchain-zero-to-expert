# 163. Required Web3 Update - Do Not Skip

In the upcoming lecture, we will be creating a web3 module to connect with Metamask in our client application.

The code used in the lecture has since been fully deprecated. Some of the fixes mentioned in the QA using ethereum.enable() have also now been deprecated:

https://docs.metamask.io/guide/ethereum-provider.html#legacy-methods

The **web3.js** module will need to be updated to include the current method for requesting access to Metamask:

```
import Web3 from "web3";
 
window.ethereum.request({ method: "eth_requestAccounts" });
 
const web3 = new Web3(window.ethereum);
 
export default web3;
```

*Note - this is still not 100% correct as we will be refactoring again to add a conditional in a few lectures.*