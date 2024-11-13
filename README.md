Advanced-ERC20-Token/
│
├── contracts/
│   ├── MyAdvancedToken.sol
│   └── Migrations.sol
│
├── migrations/
│   ├── 1_initial_migration.js
│   └── 2_deploy_contracts.js
│
├── test/
│   └── MyAdvancedToken.test.js
│
├── scripts/
│   └── deploy.js
│
├── .gitignore
├── README.md
├── truffle-config.js
└── package.json
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

import "@openzeppelin/contracts/token/ERC20/ERC20.sol";
import "@openzeppelin/contracts/token/ERC20/extensions/ERC20Burnable.sol";
import "@openzeppelin/contracts/security/Pausable.sol";
import "@openzeppelin/contracts/access/AccessControl.sol";

contract MyAdvancedToken is ERC20, ERC20Burnable, Pausable, AccessControl {
    bytes32 public constant MINTER_ROLE = keccak256("MINTER_ROLE");
    bytes32 public constant PAUSER_ROLE = keccak256("PAUSER_ROLE");

    constructor(uint256 initialSupply) ERC20("MyAdvancedToken", "MAT") {
        _mint(msg.sender, initialSupply);
        _setupRole(DEFAULT_ADMIN_ROLE, msg.sender);
        _setupRole(MINTER_ROLE, msg.sender);
        _setupRole(PAUSER_ROLE, msg.sender);
    }

    function mint(address to, uint256 amount) public onlyRole(MINTER_ROLE) {
        _mint(to, amount);
    }

    function pause() public onlyRole(PAUSER_ROLE) {
        _pause();
    }

    function unpause() public onlyRole(PAUSER_ROLE) {
        _unpause();
    }

    function _beforeTokenTransfer(address from, address to, uint256 amount)
        internal
        whenNotPaused
        override
    {
        super._beforeTokenTransfer(from, to, amount);
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract Migrations {
  address public owner;
  uint public last_completed_migration;

  constructor() {
    owner = msg.sender;
  }

  modifier restricted() {
    require(msg.sender == owner, "This function is restricted to the contract's owner");
    _;
  }

  function setCompleted(uint completed) public restricted {
    last_completed_migration = completed;
  }
}
const Migrations = artifacts.require("Migrations");

module.exports = function (deployer) {
  deployer.deploy(Migrations);
};
const MyAdvancedToken = artifacts.require("MyAdvancedToken");

module.exports = function (deployer) {
  const initialSupply = 1000000; // Initial supply of tokens
  deployer.deploy(MyAdvancedToken, initialSupply);
};
const MyAdvancedToken = artifacts.require("MyAdvancedToken");

contract("MyAdvancedToken", (accounts) => {
  it("should put 1000000 MAT in the first account", async () => {
    const instance = await MyAdvancedToken.deployed();
    const balance = await instance.balanceOf(accounts[0]);
    assert.equal(balance.valueOf(), 1000000, "1000000 wasn't in the first account");
  });

  it("should mint tokens", async () => {
    const instance = await MyAdvancedToken.deployed();
    await instance.mint(accounts[1], 500);
    const balance = await instance.balanceOf(accounts[1]);
    assert.equal(balance.valueOf(), 500, "500 wasn't minted to the second account");
  });

  it("should burn tokens", async () => {
    const instance = await MyAdvancedToken.deployed();
    await instance.burn(500, { from: accounts[0] });
    const balance = await instance.balanceOf(accounts[0]);
    assert.equal(balance.valueOf(), 999500, "500 wasn't burned from the first account");
  });

  it("should pause and unpause token transfers", async () => {
    const instance = await MyAdvancedToken.deployed();
    await instance.pause();
    try {
      await instance.transfer(accounts[1], 100);
      assert.fail("Transfer should have thrown an error");
    } catch (err) {
      assert.include(err.message, "paused", "Transfer did not throw the correct error");
    }
    await instance.unpause();
    await instance.transfer(accounts[1], 100);
    const balance = await instance.balanceOf(accounts[1]);
    assert.equal(balance.valueOf(), 600, "100 wasn't transferred to the second account after unpausing");
  });
});
async function main() {
  const [deployer] = await ethers.getSigners();
  console.log("Deploying contracts with the account:", deployer.address);

  const MyAdvancedToken = await ethers.getContractFactory("MyAdvancedToken");
  const myAdvancedToken = await MyAdvancedToken.deploy(1000000);

  console.log("Token deployed to:", myAdvancedToken.address);
}

main()
  .then(() => process.exit(0))
  .catch((error) => {
    console.error(error);
    process.exit(1);
  });
node_modules
build
.env
# Advanced ERC-20 Token

This project demonstrates the creation of an advanced ERC-20 token on the Ethereum blockchain using Solidity, Truffle, and OpenZeppelin. The token includes features such as minting, burning, pausing, and role-based access control.

## Prerequisites

- Node.js
- Truffle
- Ganache (for local development)

## Installation

1. Clone the repository:
   ```bash
   git clone https://github.com/yourusername/Advanced-ERC20-Token.git
   cd Advanced-ERC20-Token
npm install
truffle compile
truffle migrate
truffle test
truffle migrate --network <network_name>

#### 9. `truffle-config.js`
```javascript
module.exports = {
  networks: {
    development: {
      host: "127.0.0.1",
      port: 7545,
      network_id: "*", // Match any network id
    },
  },
  compilers: {
    solc: {
      version: "0.8.0",
    },
  },
};
{
  "name": "advanced-erc20-token",
  "version": "1.0.0",
  "description": "A project to create an advanced ERC-20 token on Ethereum",
  "main": "truffle-config.js",
  "scripts": {
    "test": "truffle test"
  },
  "author": "Your Name",
  "license": "MIT",
  "dependencies": {
    "@openzeppelin/contracts": "^4.0.0",
    "truffle": "^5.0.0",
    "web3": "^1.0.0"
  },
  "devDependencies": {
    "chai": "^4.2.0",
    "mocha": "^8.0.0"
  }
}
