###  ð¥ ãã®ã¬ãã¹ã³ã®åèåç»URL
[Dapp University](https://youtu.be/CgXQC4dbGUE?t=3794)

### ð ã¹ãã¼ã­ã³ã°ã®ä»çµã¿å®è£ãã

ããããã¯ãã® Yield Farming ã®æ ¹å¹¹ã¨ãªãã¹ãã¼ã­ã³ã°ã®ã·ã¹ãã ãå®è£ãã¦ããã¾ãã

ã¾ãã`TokenFarm.sol` ãä»¥ä¸ã®ããã«æ´æ°ãã¦ããã¾ãããã

```javascript
// TokenFarm.sol
pragma solidity ^0.5.0;

import "./DappToken.sol";
import "./DaiToken.sol";

contract TokenFarm{
    string public name = "Dapp Token Farm";
    DappToken public dappToken;
    DaiToken public daiToken;

    // 7. ããã¾ã§ã«ã¹ãã¼ã­ã³ã°ãè¡ã£ããã¹ã¦ã®ã¢ãã¬ã¹ãè¿½è·¡ããéåãä½æ
    address[] public stakers;

    //4.æè³å®¶ã®ã¢ãã¬ã¹ã¨å½¼ãã®ã¹ãã¼ã­ã³ã°ãããã¼ã¯ã³ã®éãç´ã¥ãã mapping ãä½æ
    mapping (address => uint) public stakingBalance;

    // 6. æè³å®¶ã®ã¢ãã¬ã¹ããã¨ã«å½¼ããã¹ãã¼ã­ã³ã°ãè¡ã£ããå¦ããç´ã¥ãã mapping ãä½æ
    mapping (address => bool) public hasStaked;

    // 10. æè³å®¶ã®ææ°ã®ã¹ãã¤ã¿ã¹ãè¨é²ãããããã³ã°ãä½æ
    mapping (address => bool) public isStaking;

    constructor(DappToken _dappToken, DaiToken _daiToken) public {
        dappToken = _dappToken;
        daiToken = _daiToken;
    }
    //1.ã¹ãã¼ã­ã³ã°æ©è½ãä½æãã
    function stakeTokens(uint _amount) public {
        // 2. ã¹ãã¼ã­ã³ã°ããããã¼ã¯ã³ã0ä»¥ä¸ãããã¨ãç¢ºèª
        require(_amount > 0, "amount can't be 0");
        // 3. æè³å®¶ã®ãã¼ã¯ã³ã TokenFarm.sol ã«ç§»åããã
        daiToken.transferFrom(msg.sender, address(this), _amount);

        // 5. ã¹ãã¼ã­ã³ã°ããããã¼ã¯ã³ã®æ®é«ãæ´æ°ãã
        stakingBalance[msg.sender] = stakingBalance[msg.sender] + _amount;

        // 8. æè³å®¶ãã¾ã ã¹ãã¼ã¯ãã¦ããªãå ´åã®ã¿ãå½¼ããstakerséåã«è¿½å ãã
        if(!hasStaked[msg.sender]){
            stakers.push(msg.sender);
        }
        // 9. ã¹ãã¼ã­ã³ã°ã¹ãã¼ã¿ã¹ã®æ´æ°
        isStaking[msg.sender] = true;
        hasStaked[msg.sender] = true;
    }
}
```

ã³ã¼ãã®çè§£ãä¿é²ããããã«ã1 ãã 10 ã¾ã§ã³ã¡ã³ãã«çªå·ãæ¯ãã¾ãããä¸ã¤ãã¤ã¿ã¦ããã¾ãããã

ã¾ããæ°ããè¿½å ãããã`stakeTokens()` é¢æ°ã«æ³¨ç®ãã¦ãã ããã

```javascript
// TokenFarm.sol
//1.ã¹ãã¼ã­ã³ã°æ©è½ãä½æãã
function stakeTokens(uint _amount) public {
ï¼
}
```

`stakeTokens` é¢æ°ã¯ãã¹ãã¼ã¯ãããã¼ã¯ã³ã®éï¼`_amount`ï¼ããå¼æ°ã¨ãã¦ãã¾ãã

- ã¾ãããã®é¢æ°ã¯ãã¹ãã¼ãã³ã³ãã©ã¯ãã®å¤é¨ããå¼ã³åºããããã« `public` ä¿®é£¾å­ãæã£ã¦ãã¾ãã

`stakeTokens` é¢æ°ã®ä¸»ãªå½¹å²ã¯ã**æè³å®¶ã®ã¦ã©ã¬ãããã `TokenFarm.sol` ã¨ããã¹ãã¼ãã³ã³ãã©ã¯ãã« Dai ãã¼ã¯ã³ãè»¢éãããã¨**ã§ãã

æ´ã«è©³ããè¦ã¦ããã¾ãããã

```javascript
// TokenFarm.sol
// 1.ã¹ãã¼ã­ã³ã°æ©è½ãä½æãã
function stakeTokens(uint _amount) public {
    // 2. ã¹ãã¼ã­ã³ã°ããããã¼ã¯ã³ã0ä»¥ä¸ãããã¨ãç¢ºèª
    require(_amount > 0, "amount can't be 0");
    // 3. æè³å®¶ã®ãã¼ã¯ã³ã TokenFarm.sol ã«ç§»åããã
    daiToken.transferFrom(msg.sender, address(this), _amount);
:
}
```

ããã§æãéè¦ã«ãªãã®ãã`transferFrom()` é¢æ°ã§ãã

`daiToken` ã®ãã¨ã¨ãªãã¹ãã¼ãã³ã³ãã©ã¯ã `DaiToken.sol` ã¯ä»ã®ERC-20ãã¼ã¯ã³ã¨åãããã« `transferFrom()` é¢æ°ãä¿æãã¦ãã¾ãã
- `transferFrom()` é¢æ°ã®ä¸­èº«ãæ°ã«ãªãäººã¯ã`DaiToken.sol` ã®ä¸­ã«å®ç¾©ããã¦ãã `transferFrom()` ãè¦ã¦ã¿ã¦ãã ãã!

`transferFrom()` ãä½¿ç¨ããã¨ãæè³å®¶ã«ä»£ãã£ã¦ã³ã³ãã©ã¯ãèªä½ï¼`TokenFarm.sol`ï¼ããå®éã«è³éãç§»åããããã¨ãã§ããããã«ãªãã¾ãã
- `msg.sender` ã¯ Solidity åé¨ã®ç¹å¥ãªå¤æ°ã§ãã`msg` ã¾ãã¯ã¡ãã»ã¼ã¸ã¯ Solidity åã®ã°ã­ã¼ãã«å¤æ°ã§ãé¢æ°ãå¼ã³åºããããã³ã«éä¿¡ãããã¡ãã»ã¼ã¸ã«å¯¾å¿ãã¾ãã`sender` ã¯é¢æ°ãå¼ã³åºããäººãæå³ãã¾ãã
- ç¬¬äºå¼æ° `address(this)` ã¯ãã¢ãã¬ã¹åã«å¤æãããã¹ãã¼ãã³ã³ãã©ã¯ããã®ãã®ï¼`TokenFarm.sol`ï¼ã§ãã
- ç¬¬ä¸å¼æ°ã¯ã `msg.sender` ãç§»åããããã¼ã¯ã³ã®é `amount` ãæå³ãã¾ãã

### ð ã¹ãã¼ã­ã³ã°ã«é¢ãããã¼ã¿ãä¿å­ãã

`stakeTokens()` é¢æ°ãå®è£ããããã«ã¯ããããããªãã¨ãè¨é²ãã¦ããå¿è¦ãããã¾ãã
- ãã¼ã¯ã³ãã¡ã¼ã ã®ä¸­ã«ã©ãã ãã®ãã¼ã¯ã³ãããã®ããã¦ã¼ã¶ã¼ãã©ãã ãã®éé¡ãé ãã¦ããã®ãããªã©ã

ãã®ããã«ãã¾ããæè³å®¶ã®ã¢ãã¬ã¹ã¨å½¼ãã®ã¹ãã¼ã­ã³ã°ãããã¼ã¯ã³ã®éãç´ã¥ãããããã³ã°ãä½æãã¾ãã

```javascript
// TokenFarm.sol
//4.æè³å®¶ã®ã¢ãã¬ã¹ã¨å½¼ãã®ã¹ãã¼ã­ã³ã°ãããã¼ã¯ã³ã®éãç´ã¥ãã mapping ãä½æ
mapping (address => uint) public stakingBalance;
```

ãããã³ã°ã¯ Key ã«å¯¾å¿ãã Value ãè¿ããã¼ã¿æ§é ã§ãã
ä»åå®ç¾©ãã `stakingBalance` ã®å ´åãKey ã¯ `address` ï¼æè³ã®ã¦ã©ã¬ããã¢ãã¬ã¹ï¼ãValue ã¯æè³å®¶ãã¹ãã¼ã¯ãããã¼ã¯ã³ã®éã«ãªãã¾ãã

æ¬¡ã«ã`stakeTokens()` ã®ä¸­ã§ã`stakingBalance` ãããã³ã°ãä½¿ç¨ããã¹ãã¼ã­ã³ã°ããããã¼ã¯ã³ã®æ®é«ãæ´æ°ãããããã«ãã¾ãã

```javascript
// TokenFarm.sol
// 5. ã¹ãã¼ã­ã³ã°ããããã¼ã¯ã³ã®æ®é«ãæ´æ°ãã
stakingBalance[msg.sender] = stakingBalance[msg.sender] + _amount;
```

æ¬¡ã«ãæè³å®¶ãã¹ãã¼ã­ã³ã°ãè¡ã£ããã¨ãè¨é²ãã¦ããã¾ãããã®ããã«ãå¥ã®ãããã³ã°ãä½æãã¾ãã

```javascript
// TokenFarm.sol
// 6. æè³å®¶ã®ã¢ãã¬ã¹ããã¨ã«å½¼ããã¹ãã¼ã­ã³ã°ãè¡ã£ããå¦ããç´ã¥ãã mapping ãä½æ
mapping (address => bool) public hasStaked;
```

ã¾ããããã¾ã§ã«ã¹ãã¼ã¯ãããã¨ã®ãããã¹ã¦ã®ã¢ãã¬ã¹ãè¿½è·¡ããéåï¼`stakers`ï¼ãä½æãã¾ãã

```javascript
// TokenFarm.sol
// 7. ããã¾ã§ã«ã¹ãã¼ã­ã³ã°ãè¡ã£ããã¹ã¦ã®ã¢ãã¬ã¹ãè¿½è·¡ããéåãä½æ
address[] public stakers;
```

Solidityã®éåã¯ãªã¹ããªã®ã§ã`stakers` ã®ä¸­èº«ã¯ãä»¥ä¸ã®ãããªå½¢ã«ãªãã¾ãã

```
["0x0...", "0x43...", "0x12..."]
```

`stakers` éåãå¿è¦ãªã®ã¯ãå¾ã§ã¹ãã¼ã­ã³ã°ããã¦ãããæè³å®¶ãã¡ã«å ±é¬ãçºè¡ããå¿è¦ãããããã§ãã

ããã§ã¯ã`stakeTokens()` ã«æ»ããæè³å®¶ã `stakers` éåã«è¿½å ããæ©è½ãã¿ã¦ããã¾ãããã

```javascript
// TokenFarm.sol
// 8. æè³å®¶ãã¾ã ã¹ãã¼ã¯ãã¦ããªãå ´åã®ã¿ãå½¼ããstakerséåã«è¿½å ãã
if(!hasStaked[msg.sender]){
    stakers.push(msg.sender);
}
```

ããã§ãã¤ã³ãã¨ãªãã®ã¯ã`stakers` éåã«ã¯ãã¦ãã¼ã¯ãªã¢ãã¬ã¹ã®ã¿ä¿ç®¡ãã¦ããã¨ãããã¨ã§ãã

ãã£ã¦ãä¸è¨ã®ã³ã¼ãã§ã¯ã**ã¹ãã¼ã­ã³ã°ããè¡ãæè³å®¶ï¼ã¦ã¼ã¶ã¼ï¼ã Token Farm ã®ã¯ããã¦ã®ãå®¢æ§ã§ãã£ãå ´åã«ã®ã¿**ãå½¼ãã®ã¢ãã¬ã¹ãã`stakers` éåã«è¿½å ããä»æ§ã«ãªã£ã¦ãã¾ãã


æå¾ã«ãæè³å®¶ã®ã¹ãã¼ã­ã³ã°ã«é¢ããç¶æãæ´æ°ããã³ã¼ããè¿½å ãã¾ãã

```javascript
// TokenFarm.sol
// 9. ã¹ãã¼ã­ã³ã°ã¹ãã¼ã¿ã¹ã®æ´æ°
isStaking[msg.sender] = true;
hasStaked[msg.sender] = true;
```

```javascript
// TokenFarm.sol
// 10. æè³å®¶ã®ææ°ã®ã¹ãã¤ã¿ã¹ãè¨é²ãããããã³ã°ãä½æ
mapping (address => bool) public isStaking;
```

ä»¥ä¸ã§ã`TokenFarm.sol` ã®æ´æ°ã¯çµäºã§ããæ¬¡ã«ããã¹ãã³ã¼ããæ´æ°ãã¦ããã¾ãã
### ðª ãã¹ããæ´æ°ãã

`TokenFarm_test.js` ãä¸ã®ããã«æ´æ°ãã¦ãã¾ãããã

```javascript
// TokenFarm_test.js
const DappToken = artifacts.require(`DappToken`)
const DaiToken = artifacts.require(`DaiToken`)
const TokenFarm = artifacts.require(`TokenFarm`)

require(`chai`).use(require('chai-as-promised')).should()

function tokens(n) {
    return web3.utils.toWei(n, 'ether');
}

contract('TokenFarm', ([owner, investor]) => {
    let daiToken, dappToken, tokenFarm

    before(async () =>{
        //ã³ã³ãã©ã¯ããèª­ã¿è¾¼ã
        daiToken = await DaiToken.new()
        dappToken = await DappToken.new()
        tokenFarm = await TokenFarm.new(dappToken.address, daiToken.address)

        //å¨ã¦ã®Dappãã¼ã¯ã³ããã¡ã¼ã ã«ç§»åãã(1 million)
        await dappToken.transfer(tokenFarm.address, tokens('1000000'));

        await daiToken.transfer(investor, tokens('100'), {from: owner})
    })

    describe('Mock DAI deployment', async () => {
        //ãã¹ã1
        it('has a name', async () => {
            const name = await daiToken.name()
            assert.equal(name, 'Mock DAI Token')
        })
    })

    describe('Dapp Token deployment', async () => {
        // ãã¹ã2
        it('has a name', async () => {
            const name = await dappToken.name()
            assert.equal(name, 'DApp Token')
        })
    })

    describe('Token Farm deployment', async () => {
        // ãã¹ã3
        it('has a name', async () => {
            const name = await tokenFarm.name()
            assert.equal(name, "Dapp Token Farm")
        })
        // ãã¹ã4
        it('contract has tokens', async () => {
            let balance = await dappToken.balanceOf(tokenFarm.address)
            assert.equal(balance.toString(), tokens('1000000'))
        })
    })
    // ----- è¿½å ãããã¹ãã³ã¼ã ------ //
    describe('Farming tokens', async () => {
        it('rewords investors for staking mDai tokens', async () => {
            let result
            // ãã¹ã5. ã¹ãã¼ã­ã³ã°ã®åã«æè³å®¶ã®æ®é«ãç¢ºèªãã
            result = await daiToken.balanceOf(investor)
            assert.equal(result.toString(), tokens('100'), 'investor Mock DAI wallet balance correct before staking')

            // ãã¹ã6. å½ã®DAIãã¼ã¯ã³ãç¢ºèªãã
            await daiToken.approve(tokenFarm.address, tokens('100'), {from: investor})
            await tokenFarm.stakeTokens(tokens('100'), {from: investor})

            // ãã¹ã7. ã¹ãã¼ã­ã³ã°å¾ã®æè³å®¶ã®æ®é«ãç¢ºèªãã
            result = await daiToken.balanceOf(investor)
            assert.equal(result.toString(), tokens('0'), 'investor Mock DAI wallet balance correct after staking')

            // ãã¹ã8. ã¹ãã¼ã­ã³ã°å¾ã®TokenFarmã®æ®é«ãç¢ºèªãã
            result = await daiToken.balanceOf(tokenFarm.address)
            assert.equal(result.toString(), tokens('100'), 'Token Farm Mock DAI balance correct after staking')

            // ãã¹ã9. æè³å®¶ãTokenFarmã«ã¹ãã¼ã­ã³ã°ããæ®é«ãç¢ºèªãã
            result = await tokenFarm.stakingBalance(investor)
            assert.equal(result.toString(), tokens('100'), 'investor staking balance correct after staking')

            // ãã¹ã10. ã¹ãã¼ã­ã³ã°ãè¡ã£ãæè³å®¶ã®ç¶æãç¢ºèªãã
            result = await tokenFarm.isStaking(investor)
            assert.equal(result.toString(), 'true', 'investor staking status correct after staking')

        })
    })
})
```

`è¿½å ãããã¹ãã³ã¼ã` ã®ä¸­èº«ãããè¦ã¦ã¿ã¦ãã ããã

ããã§ã®ãã¤ã³ãã¯ã`approve` é¢æ°ãå¼ã³åºãã¦ã`investor` ã `TokenFarm` ã®æ¿èªæ¸ã¿ã¦ã¼ã¶ã¨ãã¦ç»é²ãã¦ããç¹ã§ãã
- `approve` é¢æ°ã«ã¤ãã¦å¾©ç¿ãããæ¹ã¯ãsection 1 ã® lesson 2 ãåç§ãã¦ãã ãã!

`approve` é¢æ°ãå®è¡ããããã¨ã«ããã`investor` ã¯èªèº«ã®ãã¼ã¯ã³ã Token Farm ã«ã¹ãã¼ã¯ã§ããããã«ãªãã¾ãã
### ð¥ ãã¹ããå®è¡ãã

ããã§ã¯ããã¹ããå®è¡ãã¦ããã¾ããã

ã¿ã¼ããã«ãéãã¦ `yield-farm-starter-project` ã«ãããã¨ãç¢ºèªãã¦ããä¸è¨ã®ã³ã¼ããå®è¡ãã¦ã¿ã¦ãã ããã

```bash
truffle test
```

ä»¥ä¸ã®ãããªçµæãã¿ã¼ããã«ã«åºåããã¦ããã°æåã§ãð

```bash
Contract: TokenFarm
    Mock DAI deployment
      â has a name (39ms)
    Dapp Token deployment
      â has a name (43ms)
    Token Farm deployment
      â has a name (40ms)
      â contract has tokens (51ms)
    Farming tokens
      â rewords investors for staking mDai tokens (467ms)


  5 passing (1s)
```

### ðââï¸ è³ªåãã

ããã¾ã§ã®ä½æ¥­ã§ä½ãããããªããã¨ãããå ´åã¯ãDiscord ã® `#section-2` ã§è³ªåããã¦ãã ããã

ãã«ããããã¨ãã®ãã­ã¼ãåæ»ã«ãªãã®ã§ãã¨ã©ã¼ã¬ãã¼ãã«ã¯ä¸è¨ã® 3 ç¹ãè¨è¼ãã¦ãã ãã â¨

```
1. è³ªåãé¢é£ãã¦ããã»ã¯ã·ã§ã³çªå·ã¨ã¬ãã¹ã³çªå·
2. ä½ããããã¨ãã¦ããã
3. ã¨ã©ã¼æãã³ãã¼&ãã¼ã¹ã
4. ã¨ã©ã¼ç»é¢ã®ã¹ã¯ãªã¼ã³ã·ã§ãã
```

---
ããã¾ã§ã§ã¹ãã¼ã­ã³ã°ã®å®è£ã¯å®æãã¾ããã­ï¼

æ¬¡ã®ã¬ãã¹ã³ã§ã¯ã¹ãã¼ã­ã³ã°ãããã¼ã¯ã³ã«å¯¾ãã¦å ±é¬ã¨ãã¦ã³ãã¥ããã£ã®ãã¼ã¯ã³ãæä¾ããæ©è½ã¨ãã¹ãã¼ã­ã³ã°ãããã¼ã¯ã³ãèªåã®æåã«æ»ãæ©è½ã®å®è£ã«å¥ã£ã¦ããã¯ã¨ã³ãã¯å®æããã®ã§å¾ä¸è¸ãå¼µãé å¼µã£ã¦ããã¾ãããï¼
