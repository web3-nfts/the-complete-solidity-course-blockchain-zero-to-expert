# 108. The 'Enter' Form

**`App.js`** - The 'Enter' Form
```
import "./App.css";
import React from "react";
import web3 from './web3';
import lottery from "./lottery";
 
class App extends React.Component {
  state = {
    manager: '',
    players: [],
    balance: '',
    value: ''
  };

  async componentDidMount(){
    const manager = await lottery.methods.manager().call();
    const players = await lottery.methods.getPlayers().call();
    const balance = await web3.eth.getBalance(lottery.options.address);

    this.setState({ manager, players, balance});
  }

  render() {    
    return (
      <div>
        <h2>Lottery Contract</h2>
        <p>
          This contract is managed by {this.state.manager}.
          There are currenyly {this.state.players.length} people entered,
          competing to win {web3.utils.fromWei(this.state.balance, 'ether')} ether!
          </p>

          <hr />

          <form>
            <h4>Want to try your luck?</h4>
            <div>
              <label>Amount of ether to enter</label>
              <input
                value = {this.state.value}
                onChange = {event => this.setState({value: event.target.value})}
              />
            </div>
            <button>Enter</button>
          </form>
      </div>
    );
  }
}
export default App;
```

##  Resources for this lecture

---

-   [110-accessing.zip](https://github.com/web3-nfts/bt-web3/raw/main/Curricula/Ethereum-and-Solidity_The_Complete_Developers_Guide/resources/110-accessing.zip)