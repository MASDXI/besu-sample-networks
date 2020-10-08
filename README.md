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

| ⚠️ **หมายเหตุ**: ถ้าบน MacOS หรือ Windows, โปรดมั่นใจว่าคุณนั้นได้อนุญาติให้ docker สามารถเรียกใช้แรมได้ถึง 4G หรือ 6G ถ้าใช้งานในโหมด Privacy ตัวอย่าง under the _Resources_ section. The [Docker สำหรับ Mac](https://docs.docker.com/docker-for-mac/) และ [Docker สำหรับ Window](https://docs.docker.com/docker-for-windows/) ไซด์ดังกล่าวมีรายละเอียดเกี่ยวกับวิธีปรับตั้งค่าโดยอยู่ในหมวด  "Resources"     |
| ---                                                                                                                                                                                                                                                                                                                                                                                |


| ⚠️ **หมายเหตุ**: ถูกทดสอบบนแค่ใน Windows 10 Build 18362 และ Docker >= 17.12.2                                                                                                                                                                                                                                                                                              |
| ---                                                                                                                                                                                                                                                                                                                                                                                |

- บน Windows ตรวจสอบให้แน่ใจว่าไดรฟ์ที่มี repo โคลนนี้อยู่เป็นแบบ "Shared Drive" กับ Docker Desktop
- บน Windows แนะนำให้เรียกใช้ทุกคำสั่งจาก GitBash
- [Nodejs](https://nodejs.org/en/download/) และ [Truffle](https://www.trufflesuite.com/truffle) ถ้าใช้งาน DApp


## ตัวอย่างการตั้งค่าเพื่อสร้างเครือข่าย
All our documentation can be found on the [Besu documentation site](https://besu.hyperledger.org/Tutorials/Examples/Private-Network-Example/).

มีเครือข่ายตัวอย่างมากมายใน `repo` นี้, ยกตัวอย่างเช่นเครือข่ายที่ใช้ Proof of Work(POW) และ Proof of Authority(POA) แต่ละตัวอย่าง 
จำเป็นต้องมี Ethereun nodes จำนวน 4 node และ เครื่องมือสำหรับการติดตามสถานะของระบบเครือข่ายเช่น:
- [Alethio Lite Explorer](https://besu.hyperledger.org/en/latest/HowTo/Deploy/Lite-Block-Explorer/) สำหรับ การค้นหข้อมูลต่างๆที่อยู่ในเครือข่าย และ การค้นหาบัญชี
- [Metrics monitoring](https://besu.hyperledger.org/en/stable/HowTo/Monitor/Metrics/) ด้วย prometheus และ grafana ให้ insights into how the chain is progressing
- เพิ่มเติม [logs monitoring](https://besu.hyperledger.org/en/latest/HowTo/Monitor/Elastic-Stack/) ให้ข้อมูล real time logs ของ nodes ในเครือข่าย. ฟีเจอร์ดังกล่าวสามารถเปิดได้ด้วยคำสั่งเพิ่มเติม `-e` ขณะสร้างเครือข่าย

ตัวอย่างต่อไปนี้ประกอบไปด้วย architecture diagrams ที่แสดงถึงส่วนประกอบต่างๆ. โดยทั่วไปจะเป็นสร้างเครือข่ายโดยใช้การตั้งค่าแบบ POA (IBFT2 algorithm), และถ้าต้องการดู architecture diagrams สำหรับเครือข่ายที่ตั้งค่าแบบ POW (ethash) สามารถเข้าไปดูได้ในโฟลเดอร์ `images` (ซึ่งมีการตั้งค่าแบบ POA ในรูปแบบต่างๆที่มีใช้ feature อื่นๆเพิ่มเติม)

แต่ละส่วนยังรวมถึงลักษณะกรณีการใช้งาน (มีไว้เพื่อเป็นแนวทางเท่านั้น)
 
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

- ติดตั้ง [metamask](https://metamask.io/) เป็นส่วนเสริมสำหรับเบราว์เซอร์
- ถ้าคุณมีการสร้างบัญชีมากก่อนหน้านี้แล้ว, เลือก 'My Accounts' โดยการคลิกรูป Avatar จากนั้น 'Import Account' และทำการนำเข้า private keys ดังนี้: `0xc87509a1c067bbde78beb793e6fa76530b6382a4c0241e5e4a9ec0a0f44dc0d3`
- รัน `./run-dapp.sh` และเมื่อเสร็จสิ้น เปิดแท็บใหม่ใหม่บราว์เซอร์และไปที่ `http://localhost:3001` ซึ่งจะเปิด Truffle pet-shop box app และคุณสามารถที่จะทดสอบลองอุปการะสัตว์เลี้ยงได้
NOTE: เมื่อคุณรับสัตว์เลี้ยงมาแล้วคุณยังสามารถไปที่ block explorer และค้นหา trasnsaction ที่คุณทำซึ่งสามารถดูรายละเอียด transaction ที่บันทึกไว้ได้ใน Metamask

[วิดีโอ ตัวอย่างสอน](https://www.youtube.com/watch?v=_3E9FRJldj8) ตัวอย่าง DApp หรือ แอพพลิเคชั่นแบบกระจายศูนย์กลาง

เบื้องหลังนี้ใช้ smart contract ที่ complied และถูก deployed (ด้วยการ Migration) ไปยังเครือข่ายทดสอบ
ซึ่งคุณสามารถค้นหาและตรวจสอบ source code ของ smart contract และ DApp ได้ซึ่งถูกเก็บไว้ในโฟลเดอร์ `pet-shop`

![Image dapp](./images/sampleNetworks-dapp.png)

### iv. [POA (IBFT2) Network with ELK for centralised logs] <a name="poa-network-logs"></a>

ตัวอย่างนี้เหมือนตัวอย่างที่ ii. [POA (IBFT2) Network](#poa-network) แต่เพิ่มเติมในส่วนของ centralized logging ด้วย ELK 

![Image basic_elk](./images/sampleNetworks-poa-elk.png)

เริ่มสร้างเครือข่ายด้วยคำสั่ง: 

`./run.sh -c ibft2 -e` 

เหมาะสำหรับ: 
 - ถ้าคุณต้องการศึกษาการทำงานของ Ethereum
 - ถ้าคุณกำลังศึกษาการสร้างเครือข่ายแบบเครือข่าย Ethereum แบบส่วนตัว
 - ถ้าคุณเป็น DevOps engineer หรือ administrator กำลังมองหาเครือข่าย blockchain มี feature การเก็บ logging และ metrics
 - ถ้าคุณเป็นนักพัฒนา DApp developer และต้องการต่อยอดจากตัวอย่างก่อนหน้านี้ด้วยความสามารถในการดู transaction ด้วย ELK

### v. POA (IBFT2) Network with Privacy via Orion <a name="poa-network-privacy"></a>

![Image basic_orion_elk](./images/sampleNetworks-poa-orion-elk.png)

เริ่มสร้างเครือข่ายด้วยคำสั่ง: 

`./run-privacy.sh -c ibft2 -e` เริ่ม docker containers ของ node ทั้งหมดและสร้างเครือข่ายในโหมดการทำงานแบบ POA IBFT2 Consensus algorithm, และมี 3 Orion nodes สำหรับการทำงานที่ต้องการ privacy 

`./run-privacy.sh` เริ่ม docker containers ของ node ทั้งหมดและสร้างเครือข่ายในโหมดการทำงานแบบ POW , และมี 3 Orion nodes สำหรับการทำงานที่ต้องการ privacy 

เหมาะสำหรับ: 
 - ถ้าคุณต้องการศึกษาการทำงานของ Ethereum 
 - คุณเป็นผู้ใช้ที่ต้องการทำธุรกรรมส่วนตัว กับ party อื่นๆในเครือข่าย
 - คุณกำลังต้องการสร้างเครือข่าย Ethereum ส่วนตัวที่มีการใช้งาน feature private transactions ตัวระหว่างสอง party ขึ้นไป


[วิดีโอ ตัวอย่างสอน](https://www.youtube.com/watch?v=Menekt6-TEQ) ตัวอย่างการใช้งาน feature privacy นั้นทำอะไรบ้าง

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

ตัวอย่างนี้แสดงให้เห็นถึงการใช้งาน on chain permissioning [smart contracts](https://github.com/PegaSysEng/permissioning-smart-contracts)

![Image basic_permissioning](./images/sampleNetworks-poa-permissioning.png)


เริ่มสร้างเครือข่ายด้วยคำสั่ง: 

`./run-permissioning.sh -e` รับ smart contract code ล่าสุด, compiles smart contracts และอัพเดท genesis file ด้วย smart contract code. เมื่อเสร็จก็จะถูกใช้ในระบบเครือข่าย

`./run-permissioning-dapp.sh -e` เมื่อเครือข่ายจากขั้นตอนก่อนหน้านี้ระบบจะ migrate smart contract ไปยังเครือข่าย และเมื่อเสร็จสมบูรณ์แล้วจะรีสตาร์ทเครือข่าย blockchain โดยเปิดใช้งานสิทธิ์เพื่อให้กฎและสิทธิ์ที่ปรับใช้ในขั้นตอนก่อนหน้ามีผล

เปิดแท็บใหม่ใหม่บราว์เซอร์และไปที่ `http://localhost:3001` เพื่อทอดสอบใช้งาน Permissioning DApp 


ใช้สถานการณ์นี้ในกรณี:
 - ถ้าคุณเป็น DevOps engineer หรือ administrator กำลังมองหาเครือข่าย blockchain มี feature การทำ on chain permissioning และ restrictions
 - ถ้าคุณเป็นคนที่กำลังมองหาเครือข่าย blockchain แบบ consortium network และมี permissioning ซึ่งสามารถจัดการการเข้าถึงของสมาชิกที่จะเข้ามาร่วมในเครือข่าย

[วิดีโอ ตัวอย่างสอน](https://www.youtube.com/watch?v=MhOJKOoEZQQ) ตัวอย่างการใช้งาน feature permissioning นั้นทำอะไรบ้าง
 
คุณจำเป็นต้องติดตั้งครื่องมือเหล่านี้ก่อน
 - [Nodejs](https://nodejs.org/en/download/)
 - [Yarn](https://www.npmjs.com/package/yarn)
 - [JQ](https://stedolan.github.io/jq/)
 - ติดตั้ง [metamask](https://metamask.io/) เป็นส่วนเสริมสำหรับเบราว์เซอร์
 - ถ้าคุณมีการสร้างบัญชีมากก่อนหน้านี้แล้ว, เลือก 'My Accounts' โดยการคลิกรูป Avatar จากนั้น 'Import Account' และทำการนำเข้า private keys ดังนี้:
    - `0x8f2a55949038a9610f50fb23b5883af3b4ecb3c3bb792cbcefbd1542c692be63`
    - `0xc87509a1c067bbde78beb793e6fa76530b6382a4c0241e5e4a9ec0a0f44dc0d3`
    - `0xae6ae8e5ccbfb04590405997ee2d52d2b330726137b875053c36d94e974d162f`

เปิดบราว์เซอร์แท็บใหม่และไปที่ `http://localhost:3001` เพื่อทดลองใช้งาน Permissioning DApp, ซึ่งสามารถ อนุญาติ/ไม่อนุญาติ node ในเครือข่ายได้



### vii. POA (IBFT2) Network with Ethsigner to sign transactions <a name="poa-network-ethsigner"></a>

![Image ethsigner](./images/sampleNetworks-poa-signer.png)


เริ่มสร้างเครือข่ายด้วยคำสั่ง: 

`./run.sh -c ibft2 -s` รับ smart contract code ล่าสุด, compiles smart contracts และอัพเดท genesis file ด้วย smart contract code. เมื่อเสร็จก็จะถูกใช้ในระบบเครือข่าย

ใช้สถานการณ์นี้ในกรณี:
 - หากคุณต้องการลงนามในการทำ transaction ด้วย private key และส่งต่อไปยังไคลเอนต์ Ethereum (ตัวอย่างเช่น Besu และ Quorum)
 
เมื่อเสร็จแล้วคุณสามารถทำตาม [ตัวอย่าง](https://docs.ethsigner.pegasys.tech/en/stable/HowTo/Transactions/Make-Transactions/) ซึ่งจะแสดงวิธีการ sign transactions ไปยังเครือข่าย

*หมายเหตุ*: please remember to use port 18545 for any examples in this tutorial
