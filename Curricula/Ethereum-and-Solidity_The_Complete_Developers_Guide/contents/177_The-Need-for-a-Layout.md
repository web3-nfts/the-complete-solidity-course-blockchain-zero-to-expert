# 177. The Need for a Layout

![160.3_Next's-Pages-Architecture.png](../imgs/160.3_Next's-Pages-Architecture.png)  
---
![177.1_The-Need-for-a-Layout.png](../imgs/177.1_The-Need-for-a-Layout.png)  
---


<details>
  <summary>Adding Title - result capture</summary>

**index.js** - Adding `<h3>Open Campaigns</h3>`
```
import React, { Component } from "react";
import { Card, Button } from "semantic-ui-react";
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
      />    
      <h3>Open Campaigns</h3>  
      {this.renderCampaigns()}
      <Button 
        content="Create Campain"
        icon = "add circle"
        primary
      />
      </div>;
  }
}

export default CampaignIndex;
```
---
![177.3_The-Need-for-a-Layout.png](../imgs/177.3_The-Need-for-a-Layout.png)  
---
**Sometimes the issue is as following**
![177.2_The-Need-for-a-Layout.png](../imgs/177.2_The-Need-for-a-Layout.png)  
---
</details>
