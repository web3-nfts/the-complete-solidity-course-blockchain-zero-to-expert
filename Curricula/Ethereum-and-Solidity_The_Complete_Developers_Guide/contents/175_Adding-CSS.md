# 175. Adding CSS

**index.js** - Adding CSS
```
import React, { Component } from "react";
import { Card } from "semantic-ui-react";
import factory from "../ethereum/factory";

class CampaignIndex extends Component {
  static async getInitialProps() {
    const campaigns = await factory.methods.getDeployedCampaigns().call();

    return { campaigns };
  }
  renderCampaigns() {
    const items = this.props.campaigns.map((address) => {
      return {
        header: address,
        description: <a>View Campaign</a>,
        fluid: true,
      };
    });
    return <Card.Group items={items} />;
  }
  render() {
    return <div>
      <link
        rel="stylesheet"
        href="//cdnjs.cloudflare.com/ajax/libs/semantic-ui/2.2.12/semantic.min.css"
      ></link>
      {this.renderCampaigns()}
      </div>;
  }
}

export default CampaignIndex;
```

<details>
  <summary>Adding CSS - result capture</summary>

![175.1_Adding-CSS.png](../imgs/175.1_Adding-CSS.png)
---
</details>

##  Resources for this lecture

---

-   [179-adding-css.zip](https://beatlesm.s3.us-west-1.amazonaws.com/ethereum-and-solidity-complete-developer-guide/179-adding-css.zip)