## Document Properties

| Title          | AAVEGOTCHI REALM FACET SMART CONTRACT REVIEW |
| :------------- | :------------------------------------------- |
| Date           | 18th October, 2023                           |
| Version        | 1.0                                          |
| Author         | [Mitong Dapal](https://github.com/DMitong)   |
| Classification | Public                                       |
| Description    | Final Version                                |

## Table of contents

- <a href="#dsds"> INTRODUCTION </a>
  - <a href="#Introduction"> 1. Introduction</a>
  - <a href="#about"> 2. About Me</a>
  - <a href="#Skills"> 3. Skills</a>
  - <a href="#links"> 4. Link</a>
  - <a href="#Ppc"> 5. Notice on Review</a>
- <a href="#code"> CODE BASE </a>
- <a href="#Review"> AAVEGOTCHI’S RealmFacet.sol SMART CONTRACT REVIEW</a>
  - <a href="#summary"> Summary</a>
  - <a href="#bullet"> Bullet Points Highlight of Key Findings>
- <a href="#Danalysis"> DETAILED ANALYSIS </a>
  - <a href="#da1"> 3.1. CONTRACT STRUCTURE </a>
  - <a href="#da2"> 3.2. CONTRACT FUNCTIONALITIES </a>
  - <a href="#da3"> 3.2.1. Importations </a>
  - <a href="#da4"> 3.2.2. Modifiers Contract </a>
  - <a href="#da5"> 3.3.3. A bit more on the `AppStorage` struct in `AppStorage.sol` </a>
  - <a href="#da6"> 3.2.4. Functions and Workings of `RealmFacet.sol` </a>
  - <a href="#da7"> 3.2.5. Events </a>
- <a href="#conclusion"> CONCLUSION </a>

<h2 id="dsds"> INTRODUCTION </h2>

### <h3 id="Introduction"> 1. Introduction <h3>

The smart contract under review, `RealmFacet.sol`, is a crucial component of the Aavegotchi Realm Diamond, operating within the framework of the EIP-2535 standard. As a facet of this diamond, it manages key functionalities of the 'Gotchiverse' metaverse, specifically those related to the creation and administration of land parcels and installations in the Realm. The contract's structure and functionalities, including its use of modifiers and event emissions, are pivotal to understanding its role within the broader Aavegotchi ecosystem.

### <h3 id="about"> 2. 🚀 About Me <h3>

Hello, my name is Mitong Dapal and I am a Lawyer and a Smart Contract Developer. As at the time of writing this (date above), I am interning at Web3bridge where I am working to expand my knowledge and skills in the field of blockchain development.

As a lawyer, my core areas of expertise are technology, commercial, corporate and regulatory law practice. I am passionate about the infusion of law and policy with technology for the improvement of human personal and commercial relations as well as the development of states and improvement of government processes.

As a developer, I am passionate about creating secure and efficient smart contracts that can help revolutionise the way we interact with technology. I love learning and exploring new technologies, and I am always looking out for opportunities for self improvement.

### <h3 id="Skills"> 3. Skills <h3>

<b> Legal Skills:</b> Legal Writing and Research, Public Speaking and Advocacy, Technology and Startup Law, Contract Law, Corporate Law and Commercial Law.
<b> Blockchain Skills:</b> Blockachain Programming, Solidity, Ethers.js, Hardhat, Foundry, and Remix.
<b> Other Technical Skills:</b> Web/Software Development, Systems Administration, Cloud and Database Management, and Technical Writing.
<b>Soft Skills:</b> Entrepreneurial, Communication, Leadership, Teamwork, Adaptability, Problem Solving, Management Skills, Attention to Detail, Decision Making and Creativity.

### <h3 id="links"> 4. 🔗 Links <h3>

[![linkedin](https://img.shields.io/badge/linkedin-0A66C2?style=for-the-badge&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/mitong-dapal/)
[![twitter](https://img.shields.io/badge/twitter-1DA1F2?style=for-the-badge&logo=twitter&logoColor=white)](https://twitter.com/DMitong)

### <h3 id="Ppc"> 5. Notice on Review <h3>

The main Aavegotchi `RealmFacet.sol` codebase can be found at this [link](https://github.com/aavegotchi/aavegotchi-realm-diamond/blob/master/contracts/RealmDiamond/facets/RealmFacet.sol).

And as Aavegotchi is a progressive company, the facets are updated frequently, so the version of the code base that I reviewed as at 18th October, 2023 is added to this repository at `./Code Base/RealmFacet.sol;`
