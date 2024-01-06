第一週作業
---
基礎：

1. 發行總量100億顆、位數18的代幣！

2. 發行總量10張的SBT。

3. 開發猜數字的合約（一個人設定，大家猜，有人猜中，就結束不讓其他人猜了）

進階： 

1. 發行有盲盒機制的NFT！

2. 理解ERC721A與ERC721的差異，並實作ERC721A合約，實際比較差異！
---
## 基礎​1

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.4;

import "@openzeppelin/contracts/token/ERC20/ERC20.sol";

contract Basic0106_1 is ERC20 {

    constructor() ERC20("Basic One", "BO") {
        _mint(msg.sender, 10 ** 10);
    }

}
```

## 基礎​2

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

import "@openzeppelin/contracts/token/ERC721/ERC721.sol";

contract Advanced0106_2 is ERC721 {

    uint256 public availableQuantity = 10;

    constructor() ERC721("SBT Token", "SBT") {}

    function mint(address to, uint256 quantity) external payable {
        require(0 <= availableQuantity-quantity , "Not available quantity");
        availableQuantity = availableQuantity-quantity;
        _mint(to, quantity);
    }

    function transferFrom(address from, address to, uint256 tokenId) public virtual override {
        require(
            from == address(0) || to == address(0),
            "You cannot transfer this token"
        );
    }

}

```
## 基礎​3

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.4;

contract Basic0106_3 {

    event GuessResult(string message);

    address public owner; 
    uint256 public targetNumber;
    bool public result;
    
    constructor() {
        owner = msg.sender; 
    }

    modifier onlyOwner {
        require(msg.sender == owner);
      _;
    }

   function setTargetNumber(uint256 _newTargetNumber) external onlyOwner{
      targetNumber = _newTargetNumber; 
      result = false;
   }

    function guess(uint256 guessNumber) external {
        require(result == false, "Not started yet!");
        if(targetNumber == guessNumber) {
            emit GuessResult("Bingo!");
            result = true;
        }
        else {
            emit GuessResult("Not correct number!");
        }
        
    }

}
```
## 進階​1

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

import "@openzeppelin/contracts/token/ERC721/ERC721.sol";
import "@openzeppelin/contracts/utils/Counters.sol";

contract Advanced0106_1 is ERC721 {
    using Counters for Counters.Counter;
    Counters.Counter private _tokenIds;
    address public owner; 
    string url = "";

    constructor() ERC721("BlindBox", "BOX") {
        owner = msg.sender; 
    }

    modifier onlyOwner {
        require(msg.sender == owner);
      _;
    }

    function _baseURI() internal view  override returns (string memory) {
        return url;
    }

    function setUrl(string calldata _url) public onlyOwner {
        url = _url;
    }

    function mint(address to) public {
        uint256 tokenId = _tokenIds.current();
        _mint(to, tokenId);
        _tokenIds.increment();
    }
}
```

## 進階​2

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

import "erc721a/contracts/ERC721A.sol";

contract Advanced0106_2 is ERC721A {

    constructor() ERC721A("ERC721A Token", "721AT") {}

    function mint(address to, uint256 quantity) external payable {
        _mint(to, quantity);
    }

    function transferFromByERC721A(
        address from,
        address to,
        uint256 tokenId
    ) external payable {
        transferFrom(from, to, tokenId);
    }

}

import "@openzeppelin/contracts/token/ERC721/ERC721.sol";
import "@openzeppelin/contracts/utils/Counters.sol";

contract Advanced0106_2_2 is ERC721 {
    using Counters for Counters.Counter;
    Counters.Counter private _tokenIds;

    constructor() ERC721("ERC721 Token", "721T") {}

    function mint(address to) public {
        uint256 tokenId = _tokenIds.current();
        _mint(to, tokenId);
        _tokenIds.increment();
    }

    function batchMint(address to, uint256 quantity) external payable {
        for (uint i = 0; i < quantity; i++) {
                mint(to);
        }
    }

    function transferFromByERC721(
        address from,
        address to,
        uint256 tokenId
    ) external payable {
        _transfer(from, to, tokenId);
    }



}
```


