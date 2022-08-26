# 169. Required Web3 Update for Conditional

In the upcoming lecture, we will be adding a conditional to our **web3.js** file to check if we are running in the browser or on the server. Here is the updated code that includes the [earlier web3](163_Required-Web3-Update-Do_Not_Skip.md) fix:

```
import Web3 from "web3";
 
let web3;
 
if (typeof window !== "undefined" && typeof window.ethereum !== "undefined") {
  // We are in the browser and metamask is running.
  window.ethereum.request({ method: "eth_requestAccounts" });
  web3 = new Web3(window.ethereum);
} else {
  // We are on the server *OR* the user is not running metamask
  const provider = new Web3.providers.HttpProvider(
    "https://rinkeby.infura.io/v3/15c1d32581894b88a92d8d9e519e476c"
  );
  web3 = new Web3(provider);
}
 
export default web3;
```