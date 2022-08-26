# 171. GetInitialProps Function

**index.js** - GetInitialProps Function
```
import React, { Component } from "react";
import factory from "../ethereum/factory";

class CampaignIndex extends Component {
  static async getInitialProps() {
    const campaigns = await factory.methods.getDeployedCampaigns().call();

    return { campaigns };
  }

  render() {
    return <div>{this.props.campaigns[0]}</div>;
  }
}

export default CampaignIndex;

```

<details>
  <summary>Render - result capture</summary>

![171.2_GetInitialProps-Function.png](../imgs/171.2_GetInitialProps-Function.png)
---
</details>

![168.3_Why-Nextjs-Anyways.png](../imgs/168.3_Why-Nextjs-Anyways.png)
---
![171.1_GetInitialProps-Function.png](../imgs/171.1_GetInitialProps-Function.png)
---

##  Resources for this lecture

---

-   [175-getinitialprops.zip](https://beatlesm.s3.us-west-1.amazonaws.com/ethereum-and-solidity-complete-developer-guide/175-getinitialprops.zip)