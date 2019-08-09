# 以太坊操作 （简单版）
## Js插件
[下载ethers插件](https://cdn.ethers.io/scripts/ethers-v4.min.js)
[ethers中文文档](https://learnblockchain.cn/docs/ethers.js/)

## nodejs版
1. 新建文件夹 命名自定义
2. 下载ethers插件
> F:\ethers>npm install ethers

3. 在目录下新建js文件 ethers.js
4. 编辑运行文件
> F:\ethers>node ethers.js

## js 以太坊操作
1. 导入ethers
2. 连接以太坊
3. 以太坊操作
>地址生成,转账,代币交易等等
具体接口可参考ethers中文文档

4. 根据助记词生成地址
`var monic= "pupil return chimney under witness doll often mind alcohol region siege disorder"
var Wallet = ethers.Wallet.fromMnemonic(monic,path= "m/44'/60'/0'/0/0");
var privateKey = Wallet.privateKey;
console.log("ETH私钥：",privateKey)
var address = Wallet.address;
console.log("ETH地址：",address);
var compressedPublicKey = Wallet.signingKey.keyPair.compressedPublicKey;
console.log("ETH公钥：",compressedPublicKey);`
``
`//转账
let transfer={
    to: address,
  	value: amount,
   	// gasPrice: wallet.provider.getGasPrice(),
   	// gasLimit: 210000,
}
wallet.sendTransaction(signedTransaction).then((tx) => {
     console.log('hash');
     console.log(tx);
});
//交易总数
let transactionCountPromise = wallet.getTransactionCount();
transactionCountPromise.then((transactionCount) => {
    console.log('已发送交易量：'+transactionCount);
});
//交易信息
wallet.provider.getTransactionReceipt(transactionHash).then((node)=>{
    console.log('node');
    console.log(node);
})
//获取余额
provider.getBalance(address).then((balance) => {
    // 余额是 BigNumber (in wei); 格式化为 ether 字符串
    let etherString = ethers.utils.formatEther(balance);
    console.log("Balance: " + etherString);
});
``
`//合约代币
var abi = "合约abi信息";
var addr = "合约地址";
var contract = new ethers.Contract(addr, abi, provider);
//代币余额
contract.balanceOf(address).then(function(balance){
    console.log(wallet.address+":"+balance.toString());
});
// 代币转账前必须关联一个有过签名钱包对象
let contractWithSigner = contract.connect(wallet);
// 计算预测交易的 gasLimit
contractWithSigner.estimate.transfer(address,val.toString()).then(function(gas) {
    console.log("GAS:",gas.toNumber());
    //  发起交易，前面2个参数是函数的参数，第3个是交易参数
    contractWithSigner.transfer(address, ethers.utils.bigNumberify(val.toString()), {
       gasLimit: gas,
       // 偷懒，直接是用 2gwei
       gasPrice: ethers.utils.parseUnits("2", "gwei"),
    }).then(function (tx) {
       console.log(tx);
       // 介绍刷新上面的Token余额，重置输入框
    });
});
// 获取合约历史交易信息;
let etherscanProvider = new ethers.providers.EtherscanProvider('ropsten');
etherscanProvider.getHistory(address).then(function(history) {
    console.log(history);
})`

# 总结
**ethers.js 功能还不完善就要多参考多了解以太坊基础，阅览其他以太坊api接口 逐步完善自身**


 

# 以太坊搭建 （入门）

## 环境搭建
1. 安装go语言
[linux系统](https://blog.csdn.net/xc70203/article/details/77988473)
>wget https://dl.google.com/go/go1.10.linux-amd64.tar.gz
tar -C /usr/local -xzf go1.10.linux-amd64.tar.gz
echo 'export GOROOT=/usr/local/go' >> /etc/profile  
echo 'export PATH=$PATH:$GOROOT/bin' >> /etc/profile  
echo 'export GOPATH=/root/go' >> /etc/profile
echo 'export PATH=$PATH:$GOPATH/bin' >> /etc/profile
source /etc/profile
2. 安装nodeJs
>yum update -y && yum install git wget bzip2 vim gcc-c++ ntp epel-release nodejs cmake -y
3. 安装以太坊客户端
>### linux系统下
clone git
\> git clone https://github.com/ethereum/go-ethereum.git
\> cd go-ethereum
\> make all
\> echo "export PATH=$PATH:/root/go-ethereum/build/bin" >> /etc/profile
\> source /etc/profile

>### windows系统下
https://ethfans.org/wikis/Ethereum-Geth-Mirror  
[geth使用方法](https://www.jianshu.com/p/e1292dcc72c1)
[geth基本操作](https://tiny-calf.com/2018/02/01/GETH%E5%AE%A2%E6%88%B7%E7%AB%AF%E5%9F%BA%E6%9C%AC%E6%93%8D%E4%BD%9C/)

## 项目搭建
1. 创建文件目录
> mkdir d:\ethereum
cd d:\ethereum

2. 新建文件 -- 创世模块genesis.json
`{
 "nonce": "0x0000000000000042",
 "timestamp": "0x0",
 "parentHash": "0x0000000000000000000000000000000000000000000000000000000000000000",
 "mixhash": "0x0000000000000000000000000000000000000000000000000000000000000000",
 "extraData": "0x",
 "gasLimit": "0x80000000",
 "difficulty": "0x3",
 "coinbase": "0x3333333333333333333333333333333333333333",
 "config":{
    "chainId": 0,
    "homesteadBlock": 0,
    "eip155Block": 0
 },
 "alloc": {}
}`
注：
*	nonce：64位随机数，用于挖矿
*	timestamp：创世块的时间戳
*	parentHash：上一个区块的hash值，因为是创世块，所以这个值是0
*	mixhash：与 nonce 配合用于挖矿，由上一个区块的一部分生成的 hash。
*	extraData：附加信息，任意填写
*	gasLimit ：对GAS的消耗总量限制，用来限制区块能包含的交易信息总和，因为我们就测试链，所以随意填写。
*	difficulty：难度值，越大越难
*	coinbase：矿工账号，第一个区块挖出后将给这个矿工账号发送奖励的以太币。
*	alloc： 预设账号以及账号的以太币数量，测试链挖矿比较容易可以不配置
*	chainId 指定了独立的区块链网络 ID，不同 ID 网络的节点无法互相连接。

3.  更改数据存储路径
//datadir 区块链数据存放目录
>geth -datadir="D:\ethereum"

4. 启动测试节点，并进入 geth 命令行界面
>D:\ethereum>geth --identity "TestNode" --rpc --rpcport "8545" --datadir="D:\ethereum" --port "30303" --rpcapi "admin,web3,eth,personal,miner,net" --nodiscover console 
5. 连接主网
>geth --identity "mainnet etherum" --datadir "E:\ethereum" --rpc –rpccorsdimain="*" --rpcapi "admin,web3,eth,personal,miner,net" --rpcaddr 0.0.0.0 --rpcport 8545 --cache=1024  console
>geth --datadir data --cache 4096 --rpc --rpcport 6666 --rpcaddr 0.0.0.0  --ws --wsaddr 0.0.0.0 --wsport 6667 --wsorigins "*" 
注：
*	--identity "mainnet etherum" 给节点起名
*	--datadir "./chain" 将区块数据和keystore文件保存在当前路径的chain文件下
*	--rpc 开启RPC服务
*	--rpcapi "admin,web3,eth,personal,miner,net" 注明要开启哪些RPC服务
*	--rpcadd r 0.0.0.0 --rpcport 8545 指定RPC服务的IP和端口
*	--cache=1024 分配给Geth的内存，理论上越大同步速度越快，根据自己的配置来
*	console 开启控制台

## geth API命令操作
1. 创建用户
> personal.newAccount("123456")
0x362c16caec0d8ca96a4da170cb297be55b3e0368
或
> personal.newAccount()
Passphrase:
Repeat passphrase:
"0xa0eeae09eb9531fc50907234e7337831ccff623e"
2. 查看账户
>eth.accounts
eth.getBalance(eth.accounts[0])
personal.lockAccount(eth.accounts[0])
eth.sendTransaction({from: '0x036a03fc47084741f83938296a1c8ef67f6e34fa', to: '0x893608751d68d046e85802926673cdf2f57f7cb8',value: web3.toWei(1, "ether")})
3. 查看私钥 
>npm install keythereum
var keythereum = require("keythereum");
var datadir = " D:\ethereum";
var address= "0xc8096d713000002c77e4eb62f0000ead5f105a7e";//要小写
const password = "密码";
var keyObject = keythereum.importFromFile(address, datadir);
var privateKey = keythereum.recover(password, keyObject);
console.log(privateKey.toString('hex'));
4. 获取节点
>admin.peers
net.peerCount
5. 添加节点
>admin.addPeer("enode://91922b12115c067005c574844c6bbdb114eb262f90b6355cec89e13b483c3e4669c6d63ec66b6e3ca7a3a462d28edb3c659e9fa05ed4c7234524e582a8816743@120.27.164.92:13333")
在data目录里面新建一个static-nodes.json文件，输入以下内容并保存：["enode://91922b12115c067005c574844c6bbdb114eb262f90b6355cec89e13b483c3e4669c6d63ec66b6e3ca7a3a462d28edb3c659e9fa05ed4c7234524e582a8816743@120.27.164.92:13333"]
…………
## geth连接web3
1. web3.js在页面引用
2.  web3操作
api
https://github.com/ethereum/web3.js/
https://learnblockchain.cn/docs/web3js-0.2x/index.html
http://cw.hubwiz.com/card/c/web3.js-1.0/
https://web3js.readthedocs.io/en/1.0/index.html
连接以太坊，查询余额，转账等等操作

## 创建代币
1. solidity开发代币
[solidity中文文档](https://learnblockchain.cn/docs/solidity/)
(https://blog.csdn.net/amao1998/article/details/79438497)
[solidity开发工具](http://remix.ethereum.org/)

`pragma solidity ^0.5.1;
contract SafeMath {
  function safeSub(uint256 a, uint256 b) internal pure returns (uint256) {
    assert(b <= a);
    return a - b;
  }
  function safeAdd(uint256 a, uint256 b) internal pure returns (uint256) {
    uint256 c = a + b;
    assert(c>=a && c>=b);
    return c;
  }
}
contract owned {
    address public owner;
    constructor() public {
        owner = msg.sender;
    }
    modifier onlyOwner {
        require(msg.sender == owner);
        _;
    }
    function transferOwnership(address newOwner) onlyOwner public {
        if (newOwner != address(0)) {
        owner = newOwner;
      }
    }
}
contract HEAToken is SafeMath,owned {
    string public name="ITT";
    string public symbol="$$";
    uint8 public decimals = 18;
    uint256 public totalSupply=30000000000000; 
    mapping (address => uint256) public balanceOf;
    mapping (address => mapping (address => uint256)) public allowance;
    mapping (address => bool) public frozenAccount;
    event Transfer(address indexed from, address indexed to, uint256 value);
    event Freeze(address indexed from, bool frozen);
    
    constructor(uint256 initialSupply, string memory tokenName, string memory tokenSymbol) public {
        totalSupply = initialSupply * 10 ** uint256(decimals);
        balanceOf[msg.sender] = totalSupply;
        name = tokenName;
        symbol = tokenSymbol;
    }
    function _transfer(address _from, address _to, uint256 _value) internal {
      require(_to != address(0));
      require(_value > 0);
      require(balanceOf[_from] >= _value);
      require(balanceOf[_to] + _value > balanceOf[_to]);
      require(!frozenAccount[_from]);
      require(!frozenAccount[_to]);
      uint previousBalances = SafeMath.safeAdd(balanceOf[_from] , balanceOf[_to]);
      balanceOf[_from] = SafeMath.safeSub( balanceOf[_from] , _value);
      balanceOf[_to] =SafeMath.safeAdd(balanceOf[_to] , _value);
      emit Transfer(_from, _to, _value);
      assert(balanceOf[_from] + balanceOf[_to] == previousBalances);
    }
    function transfer(address _to, uint256 _value) public returns (bool success) {
        _transfer(msg.sender, _to, _value);
        return true;
    }
    function transferFrom(address _from, address _to, uint256 _value) public returns (bool success) {
      require(_to != address(0));
      require(_value > 0);
      require(balanceOf[_from] >= _value);
      require(balanceOf[_to] + _value > balanceOf[_to]);
      require(!frozenAccount[_from]);
      require(!frozenAccount[_to]);
      require(_value <= allowance[_from][msg.sender]); 
      uint previousBalances = SafeMath.safeAdd(balanceOf[_from] , balanceOf[_to]);
      allowance[_from][msg.sender] = SafeMath.safeSub(allowance[_from][msg.sender] , _value);
      balanceOf[_from] = SafeMath.safeSub( balanceOf[_from] , _value);
      balanceOf[_to] =SafeMath.safeAdd(balanceOf[_to] , _value);
      assert(balanceOf[_from] + balanceOf[_to] == previousBalances);
      emit Transfer(_from, _to, _value);
      return true;
    }
    function approve(address _spender, uint256 _value) public returns (bool success) {
        require(_spender != address(0));
        require(_value > 0);
        require(balanceOf[_spender] >= _value);
        require(!frozenAccount[msg.sender]);
        require(!frozenAccount[_spender]);
        allowance[msg.sender][_spender] = _value;
        return true;
    }
    function freezeMethod(address target, bool frozen) onlyOwner public returns (bool success){
        require(target !=address(0));
        frozenAccount[target] = frozen;
        emit Freeze(target, frozen);
        return true;
    }
}`

# 以太坊开发工具清单
http://blog.hubwiz.com/2019/02/14/ethereum-developer-tool-list/

# [geth的rpc采用加密访问方式]
## [nginx反向代理](https://blog.csdn.net/liuzhijun301/article/details/81085765)
1. nginx配置用户名密码
>在 nginx的 conf下，建立一个叫 htpasswd 的文件，纯文本，每一行是一对 用户名密码，冒号分隔，比如：
tom:1234 
mike:5678
