| Client         | Compound Protocol Governance                    |
| :------------- | :---------------------------------------------- |
| Title          | Smart Contract Audit Report                     |
| Target         | PreimageOracle.sol                         |
| Version         | 1.0.0                        |
| Author         | [YoungAncient](https://github.com/youngancient) |
| Classification | Public                                          |
| Date Created   | November 3, 2024                                |

## Table of contents

- <a href="#intro"> 1. INTRODUCTION</a>

  - <a href="#Disclaim"> 1.1 Disclaimer</a>
  - <a href="#About"> 1.2 About Me </a>
  - <a href="#Skills"> 1.3 Skills</a>
  - <a href="#links"> 1.4 Link</a>
  - <a href="#Cpg"> 1.5 Compound Governance</a>
  - <a href="#Gbd"> 1.6 GovernorBravoDelegate</a>
  - <a href="#scope"> 1.7 Scope</a>
  - <a href="#roles"> 1.8 Roles</a>
  - <a href="#overview"> 1.9 System Overview</a>

- <a href="#review"> 2.0 CONTRACT REVIEW</a>

- <a href="#findings"> 3.0 FINDINGS</a>

  - <a href="#Qanalysis"> 3.1 Qualitative Analysis</a>
  - <a href="#summary"> 3.2 Summarys</a>
  - <a href="#recom"> 3.2 Recommendations</a>

- <a href="#conclusion"> 4.0 CONCLUSION</a>

<h2 id="intro">1.0 INTRODUCTION </h2>

### <h3 id="Disclaim">1.1 Disclaimer <h3>

My audit of this smart contract is based on the provided information and my expertise in the field. It's essential to clarify that this audit does not serve as a guarantee for the security or functionality of the smart contract, nor does it eliminate all potential risks. Investing in cryptocurrencies involves inherent risks, and I cannot be held accountable for any financial losses that may arise from investing in this smart contract or engaging in cryptocurrency-related activities. Investors are strongly advised to conduct their own research and due diligence before making any investment decisions.

### <h3 id="About">1.2 🚀 About Me <h3>

Hello, I'm YoungAncient, a Smart Contract Developer. Currently, I'm gaining experience through an internship at Web3bridge, where I am focused on advancing my knowledge and skills in blockchain development.

Currently interested in architecture, security and scalablity of blockchain protocols.

### <h3 id="Skills">1.3 🛠 Skills <h3>

Solidity, Diamond Standard, Foundry, Hardhat, Wagmi, Ethers.js, React, Next js, Typescript.

### <h3 id="links">1.4 🔗 Links <h3>

[![portfolio](https://img.shields.io/badge/my_portfolio-000?style=for-the-badge&logo=ko-fi&logoColor=white)](https://youngancient.vercel.app/)
[![twitter](https://img.shields.io/badge/twitter-1DA1F2?style=for-the-badge&logo=twitter&logoColor=white)](https://x.com/YoungAncient)

### <h3 id="Cpg">1.5 <Contract_Concept_Name><h3>

Explain context


### <h3 id="Gbd">1.6 PreImageOracle<h3>

The PreImageOracle contract is an implementation ...

### <h3 id="scope">1.7 Scope <h3>

_(**Table: 1.7**: Compound GovernorBravoDelegate G2 Audit Scope)_
| Files in scope | SLOC |
| :-------- | :------- |
| Contracts: 1 | |
| `PreimageOracle.sol` | `800` |
| | |
| Imports: 7 | |
| `IPreimageOracle.sol` | `x` |
| `ISemver.sol` | `x` |
| `PreimageKeyLib.sol` | `x` |
| `LibKeccak.sol` | `x` |
| `CannonErrors.sol` | `x` |
| `CannonTypes.sol` | `x` |



### <h3 id="overview"> 1.9 System Overview <h3>

- #### GovernorBravoDelegateStorageV2.sol

  This contract is designed for decentralized governance using a voting system, allowing users to propose, vote on, and execute actions. It also includes functionality for whitelisting and administration. It implements safety checks and ensures proper governance mechanisms are in place to manage proposals and voting.


## <h2 id="review"> 2.0 CONTRACT REVIEW <h2>

The contract contains the following state constant and immutable variables:

```bash
    uint256 internal immutable CHALLENGE_PERIOD;
```

Explanation

```bash
    uint256 internal immutable MIN_LPP_SIZE_BYTES;
```

Explanation


```bash
    uint256 public constant MIN_BOND_SIZE = 0.25 ether;
```

Explanation

```bash
    uint256 public constant KECCAK_TREE_DEPTH = 16;
```

Explanation

```bash
    uint256 public constant MAX_LEAF_COUNT = 2 ** KECCAK_TREE_DEPTH - 1;
```

Explanation

```bash
    string public constant version = "1.0.0";
```

Explanation

The contract contains the following state variables (mappings and struct):

```bash
    mapping(bytes32 => uint256) public preimageLengths;
```

Explanation

```bash
    mapping(bytes32 => mapping(uint256 => bytes32)) public preimageParts;
```

Explanation

```bash
    mapping(bytes32 => mapping(uint256 => bool)) public preimagePartOk;
```

Explanation

```bash
    struct LargePreimageProposalKeys {
        /// @notice The claimant of the large preimage proposal.
        address claimant;
        /// @notice The UUID of the large preimage proposal.
        uint256 uuid;
    }
```

Explanation

```bash
    bytes32[KECCAK_TREE_DEPTH] public zeroHashes;
```

Explanation

```bash
    LargePreimageProposalKeys[] public proposals;
```

Explanation

```bash
    mapping(address => mapping(uint256 => bytes32[KECCAK_TREE_DEPTH])) public proposalBranches;
```

Explanation


```bash
    mapping(address => mapping(uint256 => LPPMetaData)) public proposalMetadata;
```

Explanation


```bash
    mapping(address => mapping(uint256 => uint256)) public proposalBonds;
```

Explanation

```bash
    mapping(address => mapping(uint256 => bytes32)) public proposalParts;
```

Explanation

```bash
    mapping(address => mapping(uint256 => uint64[])) public proposalBlocks;
```

Explanation


The following functions are part of the Preimage Oracle contract and are all carefully and thoroghly intertwined for optimum performance.

The flow: 
- Constructor 
- Getter 
- Private 
- External

### 2.1 constructor():

```bash
  constructor(uint256 _minProposalSize, uint256 _challengePeriod) {
        MIN_LPP_SIZE_BYTES = _minProposalSize;
        CHALLENGE_PERIOD = _challengePeriod;

        // Compute hashes in empty sparse Merkle tree. The first hash is not set, and kept as zero as the identity.
        for (uint256 height = 0; height < KECCAK_TREE_DEPTH - 1; height++) {
            zeroHashes[height + 1] = keccak256(abi.encodePacked(zeroHashes[height], zeroHashes[height]));
        }
    }
```

Explanation

### Getter Functions

### 2.2 proposalCount():

```bash
   function proposalCount() external view returns (uint256 count_) {
        count_ = proposals.length;
    }
```

Explain code

#### 2.3 proposalBlocksLen():

```bash
      function proposalBlocksLen(address _claimant, uint256 _uuid) external view returns (uint256 len_) {
        len_ = proposalBlocks[_claimant][_uuid].length;
    }
```

Explain code

#### 2.4 challengePeriod():

```bash
  function challengePeriod() external view returns (uint256 challengePeriod_) {
        challengePeriod_ = CHALLENGE_PERIOD;
    }
```

Explain code

#### 2.5 minProposalSize():

```bash
   function minProposalSize() external view returns (uint256 minProposalSize_) {
        minProposalSize_ = MIN_LPP_SIZE_BYTES;
    }
```

Explain code

#### 2.10 getTreeRootLPP();

```bash
   function getTreeRootLPP(address _owner, uint256 _uuid) public view returns (bytes32 treeRoot_) {
        uint256 size = proposalMetadata[_owner][_uuid].blocksProcessed();
        for (uint256 height = 0; height < KECCAK_TREE_DEPTH; height++) {
            if ((size & 1) == 1) {
                treeRoot_ = keccak256(abi.encode(proposalBranches[_owner][_uuid][height], treeRoot_));
            } else {
                treeRoot_ = keccak256(abi.encode(treeRoot_, zeroHashes[height]));
            }
            size >>= 1;
        }
    }
```

Explain code

#### 2.10 \_verify();

```bash
       function _verify(
        bytes32[] calldata _proof,
        bytes32 _root,
        uint256 _index,
        bytes32 _leaf
    )
        internal
        pure
        returns (bool isValid_)
    {
        /// @solidity memory-safe-assembly
        assembly {
            function hashTwo(a, b) -> hash {
                mstore(0x00, a)
                mstore(0x20, b)
                hash := keccak256(0x00, 0x40)
            }

            let value := _leaf
            for { let i := 0x00 } lt(i, KECCAK_TREE_DEPTH) { i := add(i, 0x01) } {
                let branchValue := calldataload(add(_proof.offset, shl(0x05, i)))

                switch and(shr(i, _index), 0x01)
                case 1 { value := hashTwo(branchValue, value) }
                default { value := hashTwo(value, branchValue) }
            }

            isValid_ := eq(value, _root)
        }
    }
```

Explain code


#### 2.15 \_payoutBond();

```bash
 function _payoutBond(address _claimant, uint256 _uuid, address _to) internal {
        // Pay out the bond to the claimant.
        uint256 bond = proposalBonds[_claimant][_uuid];
        proposalBonds[_claimant][_uuid] = 0;
        (bool success,) = _to.call{ value: bond }("");
        if (!success) revert BondTransferFailed();
    }
```

The \_payBond function

#### 2.16 \_hashLeaf();

```bash
    function _hashLeaf(Leaf memory _leaf) internal pure returns (bytes32 leaf_) {
        leaf_ = keccak256(abi.encodePacked(_leaf.input, _leaf.index, _leaf.stateCommitment));
    }
```

Explain \_privateFunction 



#### 2.16 \_hashLeaf();

```bash
    function _hashLeaf(Leaf memory _leaf) internal pure returns (bytes32 leaf_) {
        leaf_ = keccak256(abi.encodePacked(_leaf.input, _leaf.index, _leaf.stateCommitment));
    }
```

### External Functions

#### 2.16 readPreimage();
```bash
function readPreimage(bytes32 _key, uint256 _offset) external view returns (bytes32 dat_, uint256 datLen_) {
        require(preimagePartOk[_key][_offset], "pre-image must exist");

        // Calculate the length of the pre-image data
        // Add 8 for the length-prefix part
        datLen_ = 32;
        uint256 length = preimageLengths[_key];
        if (_offset + 32 >= length + 8) {
            datLen_ = length + 8 - _offset;
        }

        // Retrieve the pre-image data
        dat_ = preimageParts[_key][_offset];
    }
```
explanation


#### 2.16 loadLocalData();
```bash
 function loadLocalData(
        uint256 _ident,
        bytes32 _localContext,
        bytes32 _word,
        uint256 _size,
        uint256 _partOffset
    )
        external
        returns (bytes32 key_)
    {
        // Compute the localized key from the given local identifier.
        key_ = PreimageKeyLib.localizeIdent(_ident, _localContext);

        // Revert if the given part offset is not within bounds.
        if (_partOffset > _size + 8 || _size > 32) {
            revert PartOffsetOOB();
        }

        // Prepare the local data part at the given offset
        bytes32 part;
        assembly {
            // Clean the memory in [0x20, 0x40)
            mstore(0x20, 0x00)

            // Store the full local data in scratch space.
            mstore(0x00, shl(192, _size))
            mstore(0x08, _word)

            // Prepare the local data part at the requested offset.
            part := mload(_partOffset)
        }

        // Store the first part with `_partOffset`.
        preimagePartOk[key_][_partOffset] = true;
        preimageParts[key_][_partOffset] = part;
        // Assign the length of the preimage at the localized key.
        preimageLengths[key_] = _size;
    }

```

#### 2.16 loadKeccak256PreimagePart();
```bash
 function loadKeccak256PreimagePart(uint256 _partOffset, bytes calldata _preimage) external {
        uint256 size;
        bytes32 key;
        bytes32 part;
        assembly {
            // len(sig) + len(partOffset) + len(preimage offset) = 4 + 32 + 32 = 0x44
            size := calldataload(0x44)

            // revert if part offset >= size+8 (i.e. parts must be within bounds)
            if iszero(lt(_partOffset, add(size, 8))) {
                // Store "PartOffsetOOB()"
                mstore(0x00, 0xfe254987)
                // Revert with "PartOffsetOOB()"
                revert(0x1c, 0x04)
            }
            // we leave solidity slots 0x40 and 0x60 untouched, and everything after as scratch-memory.
            let ptr := 0x80
            // put size as big-endian uint64 at start of pre-image
            mstore(ptr, shl(192, size))
            ptr := add(ptr, 0x08)
            // copy preimage payload into memory so we can hash and read it.
            calldatacopy(ptr, _preimage.offset, size)
            // Note that it includes the 8-byte big-endian uint64 length prefix.
            // this will be zero-padded at the end, since memory at end is clean.
            part := mload(add(sub(ptr, 0x08), _partOffset))
            let h := keccak256(ptr, size) // compute preimage keccak256 hash
            // mask out prefix byte, replace with type 2 byte
            key := or(and(h, not(shl(248, 0xFF))), shl(248, 0x02))
        }
        preimagePartOk[key][_partOffset] = true;
        preimageParts[key][_partOffset] = part;
        preimageLengths[key] = size;
    }
```



#### 2.16 loadBlobPreimagePart();
```bash
 function loadBlobPreimagePart(
        uint256 _z,
        uint256 _y,
        bytes calldata _commitment,
        bytes calldata _proof,
        uint256 _partOffset
    )
        external
    {
        bytes32 key;
        bytes32 part;
        assembly {
            // Compute the versioned hash. The SHA2 hash of the 48 byte commitment is masked with the version byte,
            // which is currently 1. https://eips.ethereum.org/EIPS/eip-4844#parameters
            // SAFETY: We're only reading 48 bytes from `_commitment` into scratch space, so we're not reading into the
            //         free memory ptr region. Since the exact number of btyes that is copied into scratch space is
            //         the same size as the hash input, there's no concern of dirty memory being read into the hash
            //         input.
            calldatacopy(0x00, _commitment.offset, 0x30)
            let success := staticcall(gas(), 0x02, 0x00, 0x30, 0x00, 0x20)
            if iszero(success) {
                // Store the "ShaFailed()" error selector.
                mstore(0x00, 0xf9112969)
                // revert with "ShaFailed()"
                revert(0x1C, 0x04)
            }
            // Set the `VERSIONED_HASH_VERSION_KZG` byte = 1 in the high-order byte of the hash.
            let versionedHash := or(and(mload(0x00), not(shl(248, 0xFF))), shl(248, 0x01))

            // we leave solidity slots 0x40 and 0x60 untouched, and everything after as scratch-memory.
            let ptr := 0x80

            // Load the inputs for the point evaluation precompile into memory. The inputs to the point evaluation
            // precompile are packed, and not supposed to be ABI-encoded.
            mstore(ptr, versionedHash)
            mstore(add(ptr, 0x20), _z)
            mstore(add(ptr, 0x40), _y)
            calldatacopy(add(ptr, 0x60), _commitment.offset, 0x30)
            calldatacopy(add(ptr, 0x90), _proof.offset, 0x30)

            // Verify the KZG proof by calling the point evaluation precompile. If the proof is invalid, the precompile
            // will revert.
            success :=
                staticcall(
                    gas(), // forward all gas
                    0x0A, // point evaluation precompile address
                    ptr, // input ptr
                    0xC0, // input size = 192 bytes
                    0x00, // output ptr
                    0x00 // output size
                )
            if iszero(success) {
                // Store the "InvalidProof()" error selector.
                mstore(0x00, 0x09bde339)
                // revert with "InvalidProof()"
                revert(0x1C, 0x04)
            }

            // revert if part offset >= 32+8 (i.e. parts must be within bounds)
            if iszero(lt(_partOffset, 0x28)) {
                // Store "PartOffsetOOB()"
                mstore(0x00, 0xfe254987)
                // Revert with "PartOffsetOOB()"
                revert(0x1C, 0x04)
            }
            // Clean the word at `ptr + 0x28` to ensure that data out of bounds of the preimage is zero, if the part
            // offset requires a partial read.
            mstore(add(ptr, 0x28), 0x00)
            // put size (32) as a big-endian uint64 at start of pre-image
            mstore(ptr, shl(192, 0x20))
            // copy preimage payload into memory so we can hash and read it.
            mstore(add(ptr, 0x08), _y)
            // Note that it includes the 8-byte big-endian uint64 length prefix. This will be zero-padded at the end,
            // since memory at end is guaranteed to be clean.
            part := mload(add(ptr, _partOffset))

            // Compute the key: `keccak256(commitment ++ z)`. Since the exact number of btyes that is copied into
            // scratch space is the same size as the hash input, there's no concern of dirty memory being read into
            // the hash input.
            calldatacopy(ptr, _commitment.offset, 0x30)
            mstore(add(ptr, 0x30), _z)
            let h := keccak256(ptr, 0x50)
            // mask out prefix byte, replace with type 5 byte
            key := or(and(h, not(shl(248, 0xFF))), shl(248, 0x05))
        }
        preimagePartOk[key][_partOffset] = true;
        preimageParts[key][_partOffset] = part;
        preimageLengths[key] = 32;
    }
```

#### 2.16 loadKeccak256PreimagePart();
```bash
  function loadPrecompilePreimagePart(uint256 _partOffset, address _precompile, bytes calldata _input) external {
        bytes32 res;
        bytes32 key;
        bytes32 part;
        uint256 size;
        assembly {
            // we leave solidity slots 0x40 and 0x60 untouched, and everything after as scratch-memory.
            let ptr := 0x80

            // copy precompile address and input into memory
            // len(sig) + len(_partOffset) + address-offset-in-slot
            calldatacopy(ptr, 48, 20)
            calldatacopy(add(20, ptr), _input.offset, _input.length)
            // compute the hash
            let h := keccak256(ptr, add(20, _input.length))
            // mask out prefix byte, replace with type 6 byte
            key := or(and(h, not(shl(248, 0xFF))), shl(248, 0x06))

            // Call the precompile to get the result.
            res :=
                staticcall(
                    gas(), // forward all gas
                    _precompile,
                    add(20, ptr), // input ptr
                    _input.length,
                    0x0, // Unused as we don't copy anything
                    0x00 // don't copy anything
                )

            size := add(1, returndatasize())
            // revert if part offset >= size+8 (i.e. parts must be within bounds)
            if iszero(lt(_partOffset, add(size, 8))) {
                // Store "PartOffsetOOB()"
                mstore(0, 0xfe254987)
                // Revert with "PartOffsetOOB()"
                revert(0x1c, 4)
            }

            // Reuse the `ptr` to store the preimage part: <sizePrefix ++ precompileStatus ++ returrnData>
            // put size as big-endian uint64 at start of pre-image
            mstore(ptr, shl(192, size))
            ptr := add(ptr, 0x08)

            // write precompile result status to the first byte of `ptr`
            mstore8(ptr, res)
            // write precompile return data to the rest of `ptr`
            returndatacopy(add(ptr, 0x01), 0x0, returndatasize())

            // compute part given ofset
            part := mload(add(sub(ptr, 0x08), _partOffset))
        }
        preimagePartOk[key][_partOffset] = true;
        preimageParts[key][_partOffset] = part;
        preimageLengths[key] = size;
    }

```

## <h2 id="findings">3.0 FINDINGS </h2>

### <h3 id="Qanalysis"> 3.1 Qualitative Analysis<h3>

_(**Table: 3.1**: GovernorBravoDelegate G2 Qualitative Analysis)_
| Metric | Rating | Comment |
| :-------- | :------- | :----- |
| Code Complexity | Excellent | Functionality is very simple and organized |
| Documentation | Moderate | Documentation could be improved |
| Best Practices | Moderate | Some best practices were implemented but also found lacking in certain areas |

### <h3 id="summary">3.2 Summary<h3>

In summary, the code appears to be well-structured and follows best practices for a Solidity smart contract. It includes essential functions for proposal creation, voting, and execution in a decentralized governance system. The code uses appropriate validation checks to ensure the integrity and security of the proposals. However, as with any code, continuous monitoring, testing, and potential optimizations are recommended to maintain its efficiency and reliability.

### <h3 id="recom">3.2 Recommendations<h3>

Consider using the SafeMath library to ensure safe arithmetic operations to prevent overflow/underflow, instead of manually checking for overflow/underflow.

```bash
    using SafeMath for uint256;

// Use like this: a.add(b) instead of a + b
```

## <h2 id="conclusion">4.0 CONCLUSION </h2>

My Conclusion here
