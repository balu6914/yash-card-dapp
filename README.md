# yash-card-dapp
Blockchain Secured Student Information Services Application.
-------------------------

Here’s a step-by-step guide to set up and build your platform for the use case:

---

### **1. Setting Up GitHub**
#### Create and Initialize Repository
1. **Sign up / Log in to GitHub** at [github.com](https://github.com).
2. **Create a New Repository:**
   - Click on the `+` icon in the top right corner > "New repository."
   - Name it, e.g., `MyYashCard-dApp`.
   - Choose visibility: `Public` or `Private`.
   - Initialize with a `README.md` file.
3. **Clone the Repository:**
   - Copy the repository URL.
   - Open a terminal and run:
     ```bash
     git clone <your-repository-url>
     cd MyYashCard-dApp
     ```

4. **Set up Git in your project directory:**
   ```bash
   git init
   git remote add origin <your-repository-url>
   ```

---

### **2. Install Development Tools**
#### Prerequisites
- **Node.js & npm**: Download from [nodejs.org](https://nodejs.org).
- **Git**: Download from [git-scm.com](https://git-scm.com).
- **Docker** (for Hyperledger Besu): Download from [docker.com](https://www.docker.com).

#### Install Hardhat
```bash
npm install --save-dev hardhat
```

#### Install Hyperledger Besu
1. **Pull Docker Image**:
   ```bash
   docker pull hyperledger/besu:latest
   ```
2. **Run Hyperledger Besu Node**:
   ```bash
   docker run -d --name besu-node -p 8545:8545 -p 8546:8546 hyperledger/besu:latest
   ```

---

### **3. Build Smart Contracts**
#### Create Hardhat Project
1. Inside your project folder:
   ```bash
   npx hardhat
   ```
   - Choose "Create an empty hardhat.config.js."
2. Install required dependencies:
   ```bash
   npm install --save-dev @nomiclabs/hardhat-ethers ethers
   ```

#### Create a Smart Contract
1. Inside `contracts/` directory, create `StudentServices.sol`:
   ```solidity
   pragma solidity ^0.8.0;

   contract StudentServices {
       struct Student {
           string name;
           string course;
           uint256 prn;
       }

       mapping(uint256 => Student) public students;

       function addStudent(uint256 prn, string memory name, string memory course) public {
           students[prn] = Student(name, course, prn);
       }

       function getStudent(uint256 prn) public view returns (string memory, string memory, uint256) {
           Student memory student = students[prn];
           return (student.name, student.course, student.prn);
       }
   }
   ```

#### Compile Contracts
```bash
npx hardhat compile
```

---

### **4. Deploy Smart Contracts**
#### Write Deployment Script
1. Create `scripts/deploy.js`:
   ```javascript
   async function main() {
       const StudentServices = await ethers.getContractFactory("StudentServices");
       const studentServices = await StudentServices.deploy();
       await studentServices.deployed();
       console.log("Contract deployed to:", studentServices.address);
   }

   main().catch((error) => {
       console.error(error);
       process.exitCode = 1;
   });
   ```

2. Deploy the contract:
   ```bash
   npx hardhat run scripts/deploy.js --network localhost
   ```

---

### **5. Testing with Remix IDE**
1. Open [Remix IDE](https://remix.ethereum.org).
2. Copy `StudentServices.sol` to the Remix editor.
3. Compile and deploy the contract using a local instance (e.g., Besu node).

---

### **6. Blockchain QR Code Implementation**
#### Generate QR Code
1. Install a library for QR code generation:
   ```bash
   npm install qrcode
   ```

2. Add QR Code generation to your frontend:
   ```javascript
   const QRCode = require('qrcode');

   async function generateQRCode(data) {
       try {
           const url = await QRCode.toDataURL(data);
           console.log(url); // Display QR code as an image in your frontend
       } catch (err) {
           console.error(err);
       }
   }
   ```

---

### **7. Mobile App Development**
#### Set Up React Native
1. Install React Native CLI:
   ```bash
   npm install -g react-native-cli
   ```
2. Initialize a new project:
   ```bash
   npx react-native init MyYashCardApp
   ```

#### Integrate Blockchain
- Use the `ethers.js` library for interaction:
   ```bash
   npm install ethers
   ```

- Example interaction script:
   ```javascript
   import { ethers } from 'ethers';

   const provider = new ethers.providers.JsonRpcProvider("http://localhost:8545");
   const contractAddress = "<deployed-contract-address>";
   const abi = [ /* Contract ABI */ ];
   const contract = new ethers.Contract(contractAddress, abi, provider);

   async function getStudent(prn) {
       const student = await contract.getStudent(prn);
       console.log(student);
   }
   ```

---

### **8. Dashboard and APIs**
- Create APIs with Node.js and Express for data synchronization.
- Integrate these APIs into your frontend and mobile app.

---

### **Next Steps**
- Test end-to-end workflows.
- Secure the system with Decentralized Identifiers (DIDs).
- Implement multi-factor authentication and role-based access controls.
