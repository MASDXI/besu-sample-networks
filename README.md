# ตัวอย่างการสร้างเครือข่าย Hyperledger Besu 

## สารบัญ
1. [ข้อกำหนดเบื้องต้น](#ข้อกำหนดเบื้องต้น)
2. [ตัวอย่างการตั้งค่าเพื่อสร้างเครือข่าย](#ตัวอย่างการตั้งค่าเพื่อสร้างเครือข่าย)
    1. [POW (ethash) Network](#pow-network)
    2. [POA (IBFT2) Network](#poa-network)
    3. [Smart Contracts & DApp](#smart-contract-dapp)
    4. [POA (IBFT2) Network with ELK for centralised logs](#poa-network-logs)
    5. [POA (IBFT2) Network with Privacy via Orion](#poa-network-privacy)
    6. [POA (IBFT2) Network with On Chain Permissioning](#poa-network-permissioning)
    7. [POA (IBFT2) Network with Ethsigner](#poa-network-ethsigner)

## ข้อกำหนดเบื้องต้น


ในการเรียกใช้ตัวอย่างเครือข่าย คุณจำเป็นต้องติดตั้งสิ่งต่อไปนี้:

- [Docker และ Docker-compose](https://docs.docker.com/compose/install/)

| ⚠️ **Note**: ถ้าบน MacOS หรือ Windows, โปรดมั่นใจว่าคุณนั้นได้อนุญาติให้ docker สามารถเรียกใช้แรมได้ถึง 4G หรือ 6G ถ้าใช้งานในโหมด Privacy ตัวอย่าง under the _Resources_ section. The [Docker สำหรับ Mac](https://docs.docker.com/docker-for-mac/) และ [Docker สำหรับ Window](https://docs.docker.com/docker-for-windows/) ไซด์ดังกล่าวมีรายละเอียดเกี่ยวกับวิธีปรับตั้งค่าโดยอยู่ในหมวด  "Resources"     |
| ---                                                                                                                                                                                                                                                                                                                                                                                |


| ⚠️ **Note**: ถูกทดสอบบนแค่ใน Windows 10 Build 18362 และ Docker >= 17.12.2                                                                                                                                                                                                                                                                                              |
| ---                                                                                                                                                                                                                                                                                                                                                                                |

- บน Windows ensure that the drive that this repo is โคลน onto is a "Shared Drive" กับ Docker Desktop
- บน Windows แนะนำให้เรียกใช้ทุกคำสั่งจาก GitBash
- [Nodejs](https://nodejs.org/en/download/) และ [Truffle](https://www.trufflesuite.com/truffle) ถ้าใช้งาน DApp


## ตัวอย่างการตั้งค่าเพื่อสร้างเครือข่าย
All our documentation can be found on the [Besu documentation site](https://besu.hyperledger.org/Tutorials/Examples/Private-Network-Example/).

มีเครือข่ายตัวอย่างมากมายใน `repo` นี้, ยกตัวอย่างเช่นเครือข่ายที่ใช้ Proof of Work(POW) และ Proof of Authority(POA). แต่ละตัวอย่าง 
จำเป็นต้องมี Ethereun nodes จำนวน 4 node และ เครื่องมือสำหรับการติดตามสถานะของระบบเครือข่ายเช่น:
- [Alethio Lite Explorer](https://besu.hyperledger.org/en/latest/HowTo/Deploy/Lite-Block-Explorer/) สำหรับ การค้นหข้อมูลต่างๆที่อยู่ในเครือข่าย และ การค้นหาบัญชี
- [Metrics monitoring](https://besu.hyperledger.org/en/stable/HowTo/Monitor/Metrics/) ด้วย prometheus และ grafana ให้ insights into how the chain is progressing
- เพิ่มเติม [logs monitoring](https://besu.hyperledger.org/en/latest/HowTo/Monitor/Elastic-Stack/) ให้ข้อมูล real time logs ของ nodes ในเครือข่าย. ฟีเจอร์ดังกล่าวสามารถเปิดได้ด้วยคำสั่งเพิ่มเติม `-e` ขณะสร้างเครือข่าย

ตัวอย่างต่อไปนี้ประกอบไปด้วย architecture diagrams ที่แสดงถึงส่วนประกอบต่างๆ. They generally use the POA (IBFT2 algorithm) setup, and to view the architecture diagrams for the POW (ethash) setup please see the `images` folder (where the POA variants have different suffixes). 

Each section also includes use case personas (intended as guidelines only).
 
**ทำหรับการเรื่ม services และสร้างเครือข่าย:**

`./run.sh` เริ่ม docker containers ของ node ทั้งหมดและสร้างเครือข่ายในโหมดการทำงานแบบ POW

`./run.sh -c ibft2` เริ่ม docker containers ของ node ทั้งหมดและสร้างเครือข่ายในโหมดการทำงานแบบ POA ซึ่งใช้ IBFT2 Consensus algorithm

`./run.sh -c clique` เริ่ม docker containers ของ node ทั้งหมดและสร้างเครือข่ายในโหมดการทำงานแบบ POA ซึ่งใช้ Clique Consensus algorithm

`-e` parameter เพิ่มเติมสำหรับการเปิดการใช้งานการ บันทึก centralized logging ด้วย ELK 

**สำหรับการหยุด services การทำงาน:**

`./stop.sh` เพื่อหยุดการทำงานของเครือข่ายทั้งหมด, และคุณสามารถที่จะเริ่มการทำงานของเครือข่ายได้ใหม่ด้วย `./resume.sh` 

`./remove.sh ` จะทำการหยุดการทำงานของเครือข่ายทั้งหมดและ ทำการลบ containers และ images ของ node ต่างๆ



### i. POW (ethash) Network  <a name="pow-network"></a>

ตัวอย่างนี้เป็นตัวอย่างที่ใกล้เคียงกับการทำงานของ 'Bitcoin' มากที่, โดย miner หรือผู้ขุดเป็นคนสร้าง block. โดยใน Ethereum ใช้งานอยู่ในเครือข่าย 'mainnet' และ 'ropsten (เครือข่ายทดสอบ)' เครือข่ายดังกล่าวเป็นเครือข่ายแบบสาธารณะโดยทำงานแบบ POW
 
![Image basic_pow](./images/sampleNetworks-pow.png)

เริ่มสร้างเครือข่ายด้วยคำสั่ง: 

`./run.sh ` 

เหมาะสำหรับ: 
 - ถ้าคุณต้องการศึกษาการทำงานของ Ethereum
 - ถ้าคุณกำลังศึกษาการสร้างเครือข่ายแบบเครือข่าย Mainnet หรือ Ropsten แต่ในรูปแบบเครือข่ายที่มีขนาดเล็กกว่า
 - ถ้าคุณเป็นนักพัฒนา DApp ต้องการพัฒนาแอพพลิเคชั่นและทดสอบ DApp, ต้องการเครือข่ายแบบง่ายๆสำหรับใช้ในการทอดลอง หรือ ในการ Proof of Concept (POCs)
 โดยทั่วไปแล้ว นักพัฒนา DApp ส่วนใหญ่มีความต้องการตอบสนองที่รวดเร็วและพึ่งพอใจมากกว่าที่จะใช้ POA IBFT2 algorithm อย่างไรก็ตามเครือข่ายแบบ POW ก็สามารถใช้งานได้ดีในกรณีที่ต้องจำให้ใกล้เคียงกับสภาพแวดล้อมจริงของ Ethereum เครือข่าย Mainnet
 
### ii. POA (ethash) Network  <a name="poa-network"></a>
 
![Image basic_poa](./images/sampleNetworks-poa.png)

เริ่มสร้างเครือข่ายด้วยคำสั่ง: 

`./run.sh -c ibft2` 

เหมาะสำหรับ:
 - ถ้าคุณต้องการศึกษาการทำงานของ Ethereum 
 - ถ้าคุณกำลังศึกษาการสร้างเครือข่ายแบบเครือข่าย Ethereum แบบส่วนตัว
 - ถ้าคุณเป็นนักพัฒนา DApp ต้องการพัฒนาแอพพลิเคชั่นและทดสอบ DApp, ต้องการเครือข่ายแบบง่ายๆสำหรับใช้ในการทอดลอง หรือ ในการ Proof of Concept (POCs) โดยใช้ IBFT2 protocol ซึ่งมีการตอบสนองที่รวดเร็วทำให้ใช้งานได้ง่ายขึ้น


### iii. Smart Contracts & DApp (with MetaMask) <a name="smart-contract-dapp"></a>

- ติดตั้ง [metamask](https://metamask.io/) as an extension in your browser
- Once you have setup your own private account, select 'My Accounts' by clicking on the avatar pic and then 'Import Account' and enter the following private_key: `0xc87509a1c067bbde78beb793e6fa76530b6382a4c0241e5e4a9ec0a0f44dc0d3`
- Run `./run-dapp.sh` and when that completes open a new tab in your browser and go to `http://localhost:3001` which opens the Truffle pet-shop box app and you can adopt a pet from there.
NOTE: Once you have adopted a pet, you can also go to the block explorer and search for the transaction where you can see its details recorded. Metamask will also have a record of any transactions.

This is a [video tutorial](https://www.youtube.com/watch?v=_3E9FRJldj8) of the DApp example

Behind the scenes, this has used a smart contract that is compiled and then deployed (via a migration) to our test network. The source code for the smart contract and the DApp can be found in the folder `pet-shop`

![Image dapp](./images/sampleNetworks-dapp.png)

### iv. [POA (IBFT2) Network with ELK for centralised logs] <a name="poa-network-logs"></a>

ตัวอย่างนี้เหมือนตัวอย่างที่ ii. [POA (IBFT2) Network](#poa-network) แต่เพิ่มเติมในส่วนของ centralized logging ด้วย ELK 

![Image basic_elk](./images/sampleNetworks-poa-elk.png)

เริ่มสร้างเครือข่ายด้วยคำสั่ง: 

`./run.sh -c ibft2 -e` 

เหมาะสำหรับ: 
 - ถ้าคุณต้องการศึกษาการทำงานของ Ethereum
 - ถ้าคุณกำลังศึกษาการสร้างเครือข่ายแบบเครือข่าย Ethereum แบบส่วนตัว
 - you are a DevOps engineer or administrator looking to see how the full blockchain works with logging and metrics
 - you are a DApp developer and looking to build on the previous example with the ability to see transaction logs via ELK 

### v. POA (IBFT2) Network with Privacy via Orion <a name="poa-network-privacy"></a>

![Image basic_orion_elk](./images/sampleNetworks-poa-orion-elk.png)

เริ่มสร้างเครือข่ายด้วยคำสั่ง: 
`./run-privacy.sh -c ibft2 -e` starts all the docker containers in POA mode using the IBFT2 Consensus algorithm, and also has 3 Orion nodes for privacy 

`./run-privacy.sh` starts all the docker containers in POW mode, and also has 3 Orion nodes for privacy 

เหมาะสำหรับ: 
 - ถ้าคุณต้องการศึกษาการทำงานของ Ethereum 
 - you are a user looking to execute private transactions at least one other party
 - you are looking to create a private Ethereum network with private transactions between two or more parties. The logs make it easy to see whats going on between nodes and transactions


[วิดีโอ ตัวอย่างสอน](https://www.youtube.com/watch?v=Menekt6-TEQ) ตัวอย่างโหมด privacy นั้นทำอะไรบ้าง

รายละเอียดของ node ดังนี้:

Name  | Besu Node address                      | Orion node key | Node URL
----- | ---- | ---- | ---- |
node1 | 0x866b0df7138daf807300ed9204de733c1eb6d600 | 9QHwUJ6uK+FuQMzFSXIo7wOLCGFZa0PiF771OLX5c1o= | http://localhost:20000
node2 | 0xa46f0935de4176ffeccdeecaf3c6e3ca03e31b22 | qVDsbJh2UluZOePxbXAL49g0S0s2gGlJ3ftQceMlchU= | http://localhost:20002
node3 | 0x998c8bc11c28b667e4b1930c3fe3c9ab1cde3c52 | T1ItOQxwgY1pTW6YXb2EbKXYkK4saBEys3CfJ2OIKHs= | http://localhost:20004


**Testing Privacy between Orion nodes**

ติดตั้ง [Nodejs](https://nodejs.org/en/download/) and then follow the [eeajs-multinode-example](https://besu.hyperledger.org/en/stable/Tutorials/Privacy/web3js-eea-Multinode-example/) which deploys 
an `EventEmitter` contract and then sends a couple of Private Transaction from Node1 -> Node2 (& vice versa) with an arbitrary value (1000). 

At the end of both transactions, it then reads all three Orion nodes to check the value at an address, and you should observe 
that only Node1 & Node2 have this information becuase they were involved in the transaction and that Orion3 responds with a `0x` 
value for reads at those addresses

There is an additional erc20 token example that you can also test with: executing `node example/erc20.js` deploys a `HumanStandardToken` contract and transfers 1 token to node2.

This can be verified from the `data` field of the `logs` which is `1`.

### vi. POA (IBFT2) Network with On Chain Permissioning <a name="poa-network-permissioning"></a>


This example showcases on chain permissioning by deploying come [smart contracts](https://github.com/PegaSysEng/permissioning-smart-contracts)

![Image basic_permissioning](./images/sampleNetworks-poa-permissioning.png)


เริ่มสร้างเครือข่ายด้วยคำสั่ง: 

`./run-permissioning.sh -e` gets the latest smart contract code, compiles the contracts and updates the genesis file with the contract code. Once done it spins up a full network 

`./run-permissioning-dapp.sh -e` With the network up from the previous step, it will migrate the contracts to the network. Once complete, it restarts the blockchain network with permissions enabled so the rules and permissions deployed in the previous step take effect

Open a new tab in your browser and go to `http://localhost:3001` to use the Permissioning DApp 


Use this scenario:
 - if you are a DevOps engineer or administrator looking to see how the full blockchain works with on chain permissioning and restrictions
 - if you are looking to start a consortium network with permissioning so you can restrict members that join the network

This is a [video tutorial](https://www.youtube.com/watch?v=MhOJKOoEZQQ) of what the permissioning example does
 
คุณจำเป็นต้องติดตั้งครื่องมือเหล่านี้ก่อน
 - [Nodejs](https://nodejs.org/en/download/)
 - [Yarn](https://www.npmjs.com/package/yarn)
 - [JQ](https://stedolan.github.io/jq/)
 - ติดตั้ง [metamask](https://metamask.io/) เป็นส่วนเสริมสำหรับเบราว์เซอร์
 - ถ้าคุณมีการสร้างบัญชีมากก่อนหน้านี้แล้ว, เลือก 'My Accounts' โดยการคลิกรูป Avatar จากนั้น 'Import Account' และทำการนำเข้า private keys ดังนี้:
    - `0x8f2a55949038a9610f50fb23b5883af3b4ecb3c3bb792cbcefbd1542c692be63`
    - `0xc87509a1c067bbde78beb793e6fa76530b6382a4c0241e5e4a9ec0a0f44dc0d3`
    - `0xae6ae8e5ccbfb04590405997ee2d52d2b330726137b875053c36d94e974d162f`

Open a new tab in your browser and go to `http://localhost:3001` to use the Permissioning DApp, where you can allow/disallow nodes from the network



### vii. POA (IBFT2) Network with Ethsigner to sign transactions <a name="poa-network-ethsigner"></a>

![Image ethsigner](./images/sampleNetworks-poa-signer.png)


เริ่มสร้างเครือข่ายด้วยคำสั่ง: 

`./run.sh -c ibft2 -s` gets the latest smart contract code, compiles the contracts and updates the genesis file with the contract code. Once done it spins up a full network 

Use this scenario:
 - if you need to sign transactions with a private key and forward that to the Ethereum client (for example Besu)
 
Once it is up you can follow this [tutorial](https://docs.ethsigner.pegasys.tech/en/stable/HowTo/Transactions/Make-Transactions/) which shows you how to sign transactions that get on the chain. 
*NOTE*: please remember to use port 18545 for any examples in this tutorial
