# 164. CampaignFactory Instance

**web3.js**
```
import Web3 from "web3";
 
window.ethereum.request({ method: "eth_requestAccounts" });
 
const web3 = new Web3(window.ethereum);
 
export default web3;
```

**factory.js**
```
import web3 from './web3';
import CampaignFactory from './build/CampaignFactory.json';

const instance = new web3.eth.Contract(
    JSON.parse(CampaignFactory.interface),
    '0xe3f8884b2fa6e07dA7EF9dEbb7959Fd814e57098'
)

export default instance;
```

##  Resources for this lecture

---

-   [How to setup Web3](../How-to-setup-Web3.md)
