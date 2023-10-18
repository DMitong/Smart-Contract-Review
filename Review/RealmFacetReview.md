# AAVEGOTCHI’S RealmFacet.sol SMART CONTRACT REVIEW

## - SUMMARY

`RealmFacet.sol` is an integral part of the Aavegotchi Realm Diamond, utilising the EIP-2535 standard for its modular and upgradable smart contract implementation. Through several imported files and various user-defined data types and functions, it effectively manages the logic for creating and supervising land parcels and installations within the 'Gotchiverse' metaverse's Realm. The contract incorporates modifiers from `AppStorage.sol` to ensure that all actions are validated, while events are emitted to notify external listeners of any state changes. Notable functionalities include the minting of parcels, equipping and unequipping of installations and tiles, upgrading installations, and managing the freeze on building operations.

### - Bullet Points Highlight of Key Findings
* `RealmFacet` is a core smart contract governing Realm gameplay as a facet of the Aavegotchi Diamond
* Imports 11 files to access libraries, interfaces, standards like ERC20/ERC721
* Inherits from `AppStorage` to access state variables and utilise modifiers restricting actions
* Contains 2 structs, 14 functions, and 6 events to enable gameplay
* Key functions handle parcel minting, (un)equipping installations/tiles, moving, upgrading, queues
* `unequipInstallation` handles refunding Alchemica when removing installations
* Events emitted on actions like equipping/unequipping to notify external listeners
* Ownership and building freeze modifiers restrict access
* Integration with other contracts like `InstallationFacet` and `TileDiamond`
* Enables strategic gameplay involving land ownership, resource production, installation management
* Prudent design decisions and validation make it a robust foundation for Realm in the Gotchiverse.

## - DETAILED ANALYSIS

### CONTRACT STRUCTURE

The smart contract, RealmFacet.sol (‘contract’ or ‘smart contract’) on review is a facet of the Aavegotchi Realm Diamond. This means that the project follows the implementation of EIP-2535: ‘Diamonds, Multi-Facet Proxy’ on the creation of modular and upgradable contracts using facets, more popularly referred to as the Diamond Standard. A facet is a contract that implements some functionality of the diamond. The RealmFacet.sol file is one of the facets of the Aavegotchi Realm Diamond, which handles the logic for creating and managing land parcels and installations in the realm. It handles core functionalities related to the ‘Realm’ in the ‘Gotchiverse’ gameplay metaverse and is critical to the intended functioning of the ‘Gotchiverse.’

- Structurally, the RealmFacet.sol imports 11 files with different functionalities that come together to make it work, it has 2 structs (user-defined data types), 14 functions and emits 6 events.
- The RealmFacet contract is inheriting the Modifiers contract from AppStorage.sol. This inheritance provides customised and reusable modifiers for the RealmFacet contract which helps it to modify the behaviour of functions.

### CONTRACT FUNCTIONALITIES

### - Importations

Generally, importing files into contracts in solidity allows one to make use of external libraries, interfaces or other contracts in one’s contract. This aids code reusability, modularity, collaboration, security and efficiency.
The RealmFacet.sol has several import statements which are enumerated below as well as the highlight of their functionlity to the RealmFacet contract:
`import “@openzeppelin/contracts/token/ERC20/IERC20.sol”;`: imports the interface for the ERC20 standard, which defines a common interface for fungible tokens. This import is used to interact with the ERC20 alchemica tokens (FUD, FOMO, ALPHA, and KEK).
`import “../../libraries/AppStorage.sol”;`: imports the library that defines the AppStorage struct, which holds all the state variables of the diamond. This import is used to access the contract's storage variables using a delegatecall proxy pattern.
`import “../../libraries/LibDiamond.sol”;`: imports the library that provides functions for adding, replacing, and removing facets and function selectors in the diamond. This is used to implement the DiamondCut interface, which allows for upgrading the diamond’s functionality.
`import “../../libraries/LibStrings.sol”;`: imports the library that provides helper functions for string manipulation. This import is used to validate and parse string inputs and outputs in various functions.
`import “../../libraries/LibMeta.sol”;`: imports the library that provides functions for getting meta information about the diamond. This import is used to access metadata about the contract and its deployment.
`import “../../libraries/LibERC721.sol”;`: imports the library that provides functions for implementing the ERC721 standard, which defines a common interface for non-fungible tokens. This is used to create and manage parcels as ERC721 tokens in the realm.
`import “../../libraries/LibRealm.sol”;`: imports the library that defines structs and constants related to the realm’s logic, such as TileType, Tile, Parcel, InstallationType, and Installation. This import is used to interact with the Realm contract and its functionality.
`import “../../libraries/LibAlchemica.sol”;`: imports the library that defines structs and constants related to alchemica, which are resources that can be harvested and used in installations in the realm. This import is used to interact with the Alchemica contract and its functionality.
`import {InstallationDiamondInterface} from "../../interfaces/InstallationDiamondInterface.sol";`: imports an interface that defines functions for interacting with installations in the realm. An installation is an ERC1155 token that can be placed on a land parcel and provide some functionality or benefit to its owner or visitors. The `InstallationDiamondInterface` is implemented by another facet of the Realm Diamond, called `InstallationFacet.sol`.
`import “../../libraries/LibSignature.sol”;`: imports a library that provides functions for verifying signatures. In other words, this import is used to verify signatures.
`import "../../interfaces/IERC1155Marketplace.sol";`: imports an interface that defines functions for interacting with a marketplace contract that allows users to buy and sell ERC1155 tokens, such as installations. The marketplace contract is deployed separately from the Realm Diamond and follows the ERC1155 standard.
Here are some specific examples of how the imports are used:

- In the `mintParcels() function`, the `LibERC721.safeMint()` function is used to mint new Realm parcels.
- In the `equipInstallation()` function, the `LibRealm.placeInstallation()` function is used to place the installation on the Realm parcel.
- In the `unequipInstallation()` function, the `LibRealm.removeInstallation()` function is used to remove the installation from the Realm parcel.
- In the `moveInstallation()` function, the `LibRealm.removeInstallation()` and `LibRealm.placeInstallation()` functions are used to remove the installation from its current location and place it in its new location.
- In the `equipTile()` function, the `LibRealm.verifyAccessRight()` function is used to verify that the caller has the right to equip tiles on the given Realm parcel.
- The `IERC1155Marketplace` interface is used in the `equipInstallation()` function to update the ERC1155 listing for the installation on the Aavegotchi Diamond contract. This is necessary because equipping an installation on a Realm parcel removes it from the player's inventory.
- In the `upgradeInstallation()` function, the `LibRealm.removeInstallation()`, `LibRealm.placeInstallation()`, `LibAlchemica.reduceTraits()`, and `LibAlchemica.increaseTraits()` functions are used to remove the old installation, place the new installation, and update the Alchemica traits accordingly.
- In the `addUpgradeQueueLength()` and `subUpgradeQueueLength()` functions, the `LibRealm.upgradeQueueLength()` function is used to update the upgrade queue length for the given Realm parcel.

### - Modifiers Contract

The RealmFacet contract `is` Modifiers contract from `AppStorage.sol`, which as previously explained connotes inheritance of the modifiers contract by the Realm facet contract.

From the Modifiers contract, `AppStorage internal s;` declares a variable called `s` of type `AppStorage`. The `internal` keyword means that the variable is only accessible to functions within the same contract. The `AppStorage` struct is a custom data type that contains all of the state information for the Realm game.

The `s` variable is used by the modifiers contract to store the current state of the Realm game. This allows the modifiers to check the state of the game and prevent users from performing actions that are not allowed. For example, the `onlyParcelOwner()` modifier checks the `s.parcels` mapping to make sure that the user is the owner of the parcel on which they are performing the action. The `gameActive()` modifier checks the `s.gameActive` state variable to make sure that the game is active.

The modifiers that are being inherited and used by the realm facet's functions are:

- `onlyParcelOwner():` This modifier requires the user to be the owner of the parcel on which they are performing the action.
- `onlyOwner():` This modifier requires the user to be the contract owner.
- `onlyGotchiOwner():` This modifier requires the user to be the owner of the Aavegotchi that is associated with the action.
- `onlyGameManager():` This modifier requires the user to be the game manager.
- `onlyInstallationDiamond():` This modifier requires the user to be the installation diamond contract.
- `gameActive():` This modifier requires the game to be active.
- `canBuild():` This modifier requires building to not be frozen.
  The use of modifiers in the `RealmFacet` contract helps to ensure that only authorised users can perform certain actions and that the game is always in a valid state. By using the `AppStorage` struct, the modifiers contract is able to ensure that the game is always in a valid state and that users can only perform actions that are allowed.

#### A bit more on the `AppStorage` struct in `AppStorage.sol`

First, the `AppStorage` struct contains additional information about the Realm, such as the addresses of the Aavegotchi Diamond contract, the Installations Diamond contract, and the Tile Diamond contract. This information is used by the RealmFacet contract to interact with these other contracts.

Second, the `AppStorage` struct contains information about the game's state, such as whether the game is active and whether building is frozen. This information is used by the `RealmFacet` contract to ensure that only valid actions are performed.

Finally, the `AppStorage` struct contains information about the game's configuration, such as the addresses of the alchemica contracts, the VRFCoordinator contract, and the LINK token. This information is used by the `RealmFacet` contract to perform various actions, such as requesting VRF randomness and minting new Realm parcels.

In addition to the above, the `accessRights` mapping in the AppStorage struct controls who is allowed to perform certain actions on a given Realm parcel. For example, the owner of a Realm parcel can set the `accessRights` mapping to allow anyone with a Gotchi to channel Alchemica on their parcel. Or, the owner of a Realm parcel can set the `accessRights` mapping to only allow whitelisted addresses to channel Alchemica on their parcel.

The `AppStorage` struct is a very important part of the game, and it's use in the `RealmFacet` contract helps to ensure that the contract has all of the information it needs to operate correctly and that the game is always in a valid state.

### - Functions and Working

1. The `MintParcelInput` struct is used to specify the parameters for minting a new Realm parcel. It contains the following fields:

- `coordinateX:` The x-coordinate of the parcel on the Realm map.
- `coordinateY:` The y-coordinate of the parcel on the Realm map.
- `district:` The district that the parcel belongs to.
- `parcelId:` The unique identifier of the parcel.
- `parcelAddress:` The address of the parcel.
- `size:` The size of the parcel.
- `boost:` An array of four values that represent the parcel's boost to each of the four Alchemica types.

Meanwhile, the `mintParcels` function which is an `external` function allows the contract owner to mint new Realm parcels to specified addresses.
Here is a step-by-step explanation of the `mintParcels()` function:

- It takes in as parameters an arrays of `to` addresses, `tokenIds` and metadata structs to mint parcels to.
- It loops through each index to mint.
- For each parcel:
- It first requires the total supply is less than the max of 420,069. This prevents minting too many parcels.
- It gets the tokenId, toAddress and metadata for this parcel.
- It requires the arrays are the same length to prevent issues.
- It stores the metadata into the `MintParcelInput` struct.
- It mints the NFT to the `toAddress` using the `ERC721 safeMint` function.
  The `mintParcels()` function is used to mint new Realm parcels. It takes an array of `MintParcelInput` structs as input and mints a new parcel for each input struct.
  The `MintParcelInput` struct and the `mintParcels()` function are related in that the `mintParcels()` function uses the MintParcelInput struct to specify the parameters for the parcels that it mints.
  So in summary, it mints a batch of parcels by looping through the inputs, storing the metadata, and minting the NFTs.

2. The `BatchEquipIO` struct is used to specify the parameters for batch equipping installations and tiles on a Realm parcel. It contains the following fields:

- `types:` An array of values that indicate whether each input is an installation or a tile.
- `equip:` An array of values that indicate whether each input should be equipped or unequipped.
- `ids:` An array of the identifiers of the installations or tiles to be equipped or unequipped.
- `x:` An array of the x-coordinates of the installations or tiles to be equipped or unequipped.
- `y:` An array of the y-coordinates of the installations or tiles to be equipped or unequipped.

While, the batchEquip function is an `external` function which allows batch equipping and unequipping of installations and tiles to a parcel.
It takes in:

- `_realmId` - ID of the realm parcel,
- `_gotchiId` - Aavegotchi ID playing the game,
- `_params` - the `BatchEquipIO` struct,
- `_signatures` - array of signatures.
  The function modifiers:

* `gameActive` - Checks the game is active
* `canBuild` - Checks if building is allowed (not frozen)
  It first requires that IDs and x/y coords are the same length (for equip/unequip).
  It then loops through each item in the arrays based on type and equip status, it calls the internal equip or unequip functions.

The equip and unequip functions:

- Verify access rights for the Aavegotchi
- Verify the signature is valid
- Perform the equip/unequip logic
- Emit events
  The `batchEquip()` function is used to batch equip installations and tiles on a Realm parcel. It takes a `BatchEquipIO` struct as input and equips or unequips each installation or tile in the struct on the parcel.
  The `BatchEquipIO` struct and the `batchEquip()` function are related in that the `batchEquip()` function uses the `BatchEquipIO` struct to specify the parameters for the installations and tiles that it equips or unequips.
  So in summary, it allows batch actions by looping through arrays, checking validity via requires and modifiers, and calling the individual equip/unequip functions.

3. The `equipInstallation()` function is a `public` function which is used to equip an installation on a Realm parcel. It takes the following parameters as input:

- `_realmId:` The identifier of the Realm parcel on which to equip the installation.
- `_gotchiId:` The identifier of the Aavegotchi that is equipping the installation.
- `_installationId:` The identifier of the installation to be equipped.
- `_x:` The x-coordinate of the installation on the parcel.
- `_y:` The y-coordinate of the installation on the parcel.
- `_signature:` A signature that verifies that the user is authorised to equip the installation on the given parcel.

It first:

- Verifies the access rights for the Aavegotchi using the `gameActive` and `canBuild` modifiers
- Verifies the signature is valid
  It then has some require statements:
- Require the installation is the installation is of the correct level
- Require parcel is surveyed to make sure that the installation is not already equipped on the parcel
- Require no lodge already equipped if equipping a lodge
- Require no maker equipped if equipping an altar
  It calls internal functions:
- `placeInstallation` from `LibRealm` - to store in parcel struct
- `equipInstallation` from `LibRealm` - in `InstallationDiamond` contract
- `increaseTraits` from `LibAlchemica` - to increase parcel traits
- `updateERC1155Listing` from `IERC1155Marketplace` - to set approvals

If all of the checks are successful, the `equipInstallation()` function places the installation on the parcel and updates the user's Alchemica traits accordingly. And emits an `EquipInstallation` event.
The `equipInstallation()` function is an important part of the Realm game, as it allows players to improve their Alchemica production and boost their Aavegotchis.

4. The `unequipInstallation()` function is a public function which is used to unequip an installation from a Realm parcel. It takes the following parameters as input:

- `_realmId:` The identifier of the Realm parcel from which to unequip the installation.
- `_gotchiId:` The identifier of the Aavegotchi that is unequipping the installation.
- `_installationId:` The identifier of the installation to be unequipped.
- `_x:` The x-coordinate of the installation on the parcel.
- `_y:` The y-coordinate of the installation on the parcel.
- `_signature:` A signature that verifies that the user is authorised to unequip the installation on the given parcel.
  The `unequipInstallation()` function first verifies that the user is authorised to unequip the installation on the given parcel using the `onlyParcelOwner`, `gameActive` and `canBuild` modifiers.
  It then checks to prevent unequipping if an upgrade is active for the desired installationId on the parcel.
  Thereafter, it calls some internal functions:

* `removeInstallation` from `LibRealm` - To update parcel struct by removing installation
* `unequipInstallation` from `LibRealm` - In InstallationDiamond contract to unequip
* `decreaseTraits` from `LibAlchemica` - To decrease parcel traits
  By doing this, it removes the installation from the parcel and updates the user's Alchemica traits accordingly.

The `unequipInstallation()` function also handles the refunding of Alchemica when an installation is unequipped from a parcel.
It follows this logic:
The contract first checks if the unequip type is 0. If so, a refund can be calculated.

    It then loops through each previous level of the installation, starting from the current level and going backwards. For each level, it retrieves the installation type, which contains the Alchemica costs for upgrading to that level.

    Inside this loop, it loops through each Alchemica cost and adds half of that cost to a refund array. This builds up an array containing half refund amounts for each Alchemica type that was previously spent on upgrading the installation.

    After the nested loop finishes, the contract loops through the refund array. For each positive refund amount, it transfers that Alchemica back to the player by:
        Getting the ERC20 contract address for that Alchemica type
        Calling transfer() to send the refund amount back to the player.

    This logic allows the contract to calculate a refund representing half of the total Alchemica costs that were paid to upgrade the installation previously. This Alchemica is transferred back to the player when they unequip the installation from their parcel.

After all is done, it emits the `UnequipInstallation` event. The `unequipInstallation()` function is an important part of the Realm game, as it allows players to change their Alchemica production strategy and redeploy their installations to different parcels.

5. The `moveInstallation()` function is used to move an installation from one location on a Realm parcel to another location on the same parcel. It takes the following parameters as input:

- `_realmId:` The identifier of the Realm parcel on which to move the installation.
- `_installationId:` The identifier of the installation to be moved.
- `_x0:` The current x-coordinate of the installation on the parcel.
- `_y0:` The current y-coordinate of the installation on the parcel.
- `_x1:` The new x-coordinate of the installation on the parcel.
- `_y1:` The new y-coordinate of the installation on the parcel.
  The `moveInstallation()` function first verifies access rights using the `onlyParcelOwner` of the given Realm ID, `gameActive` and `canBuild` modifiers. It then calls the installation diamond contract to check if the installation about to be moved is upgrading, if it's not, then it removes the installation from its current location and places it in its new location.

Here is a step-by-step explanation of the `moveInstallation()` function:

- Calls the installation diamond contract through the `InstallationDiamondInterface`.
- Check the realm ID to make sure that the installation is not being upgraded.
- Remove the installation from its current location.
- Emit an `UnequipInstallation` event.
- Place the installation in its new location.
- Emit an `EquipInstallation` event.
  The `moveInstallation()` function is an important part of the Realm game, as it allows players to optimise their parcel layout and improve their Alchemica production.

6. The `equipTile` function is a `public` function which is used to equip a Tile on a Realm parcel. It takes the following parameters as input:

- `_realmId:` The identifier of the Realm parcel on which to equip the Tile.
- `_gotchiId:` The identifier of the Aavegotchi that is equipping the Tile.
- `_tileId:` The identifier of the Tile to be equipped.
- `_x:` The x-coordinate of the Tile on the parcel.
- `_y:` The y-coordinate of the Tile on the parcel.
- `_signature:` A signature that verifies that the user is authorised to equip the Tile on the given parcel.
  The `equipTile()` function first requires the game to be active and also checks the `freezeBuilding` state variable to make sure that building is not frozen. The `gameActive() and canBuild() modifiers are used to enforce these requirements.
If all checks are done and met, the equipTile() function places the Tile on the parcel. And then, calls the internal `equipTile`function from the`tileDiamond`to equip the tile, it increases parcel traits. And subsequently, updates ERC1155 listing approvals.
Finally, it emits an`EquippedTile` event.

The `equipTile()` function is an important part of the Realm game, as it allows players to improve their Alchemica production and boost their Aavegotchis.

7. The `unequipTile` function is a `public` function which is used to unequip a Tile from a Realm parcel. It takes the following parameters as input:

- `_realmId:` The identifier of the Realm parcel from which to unequip the Tile.
- `_gotchiId:` The identifier of the Aavegotchi that is unequipping the Tile.
- `_tileId:` The identifier of the Tile to be unequipped.
- `_x:` The x-coordinate of the Tile on the parcel.
- `_y:` The y-coordinate of the Tile on the parcel.
- `_signature:` A signature that verifies that the user is authorised to unequip the Tile on the given parcel.
  The `unequipTile()` function first requires the user to be the owner of the parcel and the game to be active. It also checks the `freezeBuilding` state variable to make sure that building is not frozen. The `onlyParcelOwner()`, `gameActive` and `canBuild()` modifiers are used to enforce these requirements.

The function logic first verifies the signature is valid using the backend public key. This ensures the unequip request is authorised.

Next, it calls `LibRealm.removeTile()` to remove the tile data from the parcel.

Then it calls the `TileDiamond` contract to handle any logic related to unequipping the tile itself.

Finally, it emits an `UnequipTile` event with the `_realmId`, `_tileId`, and the coordinates.

The `unequipTile()` function is an important part of the Realm game, as it allows a player's Aavegotchi to unequip a tile that was previously equipped in its parcel.

8. The `moveTile` function is is used to move a Tile from one location on a Realm parcel to another location on the same parcel. It takes the following parameters as input:

- `_realmId:` The identifier of the Realm parcel on which to move the Tile.
- `_tileId:` The identifier of the Tile to be moved.
- `_x0:` The current x-coordinate of the Tile on the parcel.
- `_y0:` The current y-coordinate of the Tile on the parcel.
- `_x1:` The new x-coordinate of the Tile on the parcel.
- `_y1:` The new y-coordinate of the Tile on the parcel.
  The `moveTile()` function first requires the user to be the owner of the parcel and the game to be active. It also checks the `freezeBuilding` state variable to make sure that building is not frozen. The `onlyParcelOwner()`, `gameActive` and `canBuild()` modifiers are used to enforce these requirements.
  The function logic first calls `LibRealm.removeTile()` which removes the tile data at the original `x0,y0` coordinates.

It emits an `UnequipTile` event signaling the tile was removed.

Next, it calls `LibRealm.placeTile()` to add the tile data back to the realm's tile map at the new `x1,y1` coordinates.

It emits an `EquipTile` event signaling the tile was placed at the new coordinates.

In summary, this function allows an already placed tile to be moved to a new position by first removing it from the original spot, and then placing it at the new spot. The events signal the unequip and re-equip.

The `moveTile()` function is an important part of the Realm game, as it allows players to optimise their parcel layout.

9. The `upgradeInstallation` function is an `external` function which is used to upgrade an installation on a Realm parcel to a higher level. It takes the following parameters as input:

- `_realmId:` The identifier of the Realm parcel on which to upgrade the installation.
- `_prevInstallationId:` The identifier of the installation to be upgraded.
- `_nextInstallationId:` The identifier of the higher level installation.
- `_coordinateX:` The x-coordinate of the installation on the parcel.
- `_coordinateY:` The y-coordinate of the installation on the parcel.

The `onlyInstallationDiamond` modifier implements a requirement that only the installation diamond contract can call it.

The function logic first calls `LibRealm.removeInstallation()` to remove the old lower level installation data at the provided coordinates.
It then calls `LibRealm.placeInstallation()` to add the new higher level installation data at the same coordinates.
Next, it calls `LibAlchemica.reduceTraits()` to reduce the alchemica's traits based on removing the old installation.
It then calls `LibAlchemica.increaseTraits()` to increase the traits based on adding the new higher level installation.

Finally, it emits an `InstallationUpgraded` event with details about the upgrade.

The `upgradeInstallation()` function is an important part of the Realm game, as it allows players to improve their Alchemica production and boost their Aavegotchis.

10. The `addUpgradeQueueLength()` function is an external function which is used to increment the upgrade queue length for a given Realm parcel. It takes the following parameter as input:

- `_realmId:` The identifier of the Realm parcel on which to increment the upgrade queue length.

Due to the `onlyInstallationDiamond` modifier, this function can only be called by the `InstallationDiamond` contract.

The function increments `s.parcels[_realmId].upgradeQueueLength` by 1.

`s.parcels` is a mapping that stores Parcel structs. The `upgradeQueueLength` property tracks how many upgrades are currently queued for the parcel.

So this function simply increments the queue length by 1, indicating that another upgrade has been added to the queue.
The `addUpgradeQueueLength()` function is used by the Realm contract to manage the upgrade queue for Realm parcels. When a player upgrades an installation on their Realm parcel, the upgrade queue length is incremented. This ensures that all upgrade requests are processed in order.

The `addUpgradeQueueLength()` function is also used by the Realm contract to prevent players from upgrading too many installations at once. This helps to prevent network overloaded.

11. The `subUpgradeQueueLength()` function is an external function which is used to decrement the upgrade queue length for a given Realm parcel. It takes the following parameter as input:

- `_realmId:` The identifier of the Realm parcel on which to decrement the upgrade queue length.

Due to the `onlyInstallationDiamond` modifier, this can only be called by the `InstallationDiamond` contract.

The function decrements `s.parcels[_realmId].upgradeQueueLength` by 1.

Just like in `addUpgradeQueueLength`, `s.parcels` maps to Parcel structs, and the `upgradeQueueLength` property tracks the number of pending upgrades.

So this function decrements the queue length by 1, indicating that an upgrade was removed or completed from the queue.

The `subUpgradeQueueLength()` function is also used by the Realm contract to allow players to cancel their upgrade requests. If a player changes their mind and decides not to upgrade an installation, they can cancel their upgrade request and the upgrade queue length will be decremented.

12. The `FixGrid` function is an external function which is used to fix the grid for a given installation or tile on a Realm parcel. It takes the following parameters as input:

- `_realmId:` The identifier of the Realm parcel on which to fix the grid.
- `_installationId:` The identifier of the installation or tile to fix the grid for.
- `_x:` The x-coordinate of the installation or tile on the parcel.
- `_y:` The y-coordinate of the installation or tile on the parcel.
- `_tile:` A boolean value indicating whether the installation or tile is a tile.
  The function logic first checks that `_x` and `_y` are the same length.
  Then it gets the Parcel for the parcel realm ID from state storage `s.parcels`.
  It loops through each coordinate pair in `_x` and `_y`:

* Checking they are < 64
* If tile is false, sets the buildGrid at `[_x,_y]` to `_installationId`
* If tile is true, sets the tileGrid at `[_x,_y]` to `_installationId`
  This allows directly writing to the parcel's buildGrid or tileGrid from the contract owner, bypassing normal realm mechanics.

It could be used to fix issues with the grids or set them into a valid state.

The onlyOwner modifier restricts access to just the contract owner.

13. The `buildingFrozen()` function, an external function in the Realm Facet contract is a view function that returns a boolean value indicating whether or not building is frozen on the Realm.

The function works by checking the value of the `freezeBuilding` state variable. If the `freezeBuilding` state variable is set to true, then the function returns true, indicating that building is frozen. Otherwise, the function returns false, indicating that building is not frozen.

The `buildingFrozen()` function is used by the Realm contract to determine whether or not to allow players to build or upgrade installations and tiles on their Realm parcels. If building is frozen, then players will not be able to build or upgrade any installations or tiles.

14. The `setFreezeBuilding()` function, an external function in the `RealmFacet` contract is used to set the value of the `freezeBuilding` state variable. The `freezeBuilding` state variable is a boolean value that indicates whether or not building is frozen on the Realm.

The `setFreezeBuilding()` function takes the following parameter as input:

- `_freezeBuilding:` A boolean value indicating whether or not to freeze building.
  If the `_freezeBuilding` parameter is set to true, then the `setFreezeBuilding()` function will set the `freezeBuilding` state variable to true, indicating that building is frozen. Otherwise, the function will set the `freezeBuilding` state variable to false, indicating that building is not frozen.

The `setFreezeBuilding()` function can only be called by the contract owner because of the `onlyOwner` modifier. The contract owner can use the `setFreezeBuilding()` function to freeze or unfreeze building on the Realm at any time.

The `setFreezeBuilding()` function is an important part of the Realm contract, as it allows the contract owner to control whether or not building is allowed on the Realm. This helps to ensure that the Realm is always fair and secure.

### - Events

Events are used in the `RealmFacet` contract to notify listeners of state changes. This can be useful for a variety of purposes, such as updating user interfaces or triggering other smart contracts. The Realm Facet contract emits the following events:

- `EquipInstallation:` Emitted when an installation is equipped on a parcel. It contains the realm ID, installation ID, and coordinates.
- `UnequipInstallation:` Emitted when an installation is unequipped from a parcel. It contains the realm ID, installation ID, and coordinates.
- `EquipTile:` Emitted when a tile is equipped on a parcel. It contains the realm ID, tile ID, and coordinates.
- `UnequipTile:` Emitted when a tile is unequipped from a parcel. It contains the realm ID, tile ID, and coordinates.
- `InstallationUpgraded:` Emitted when an installation is upgraded. It contains the realm ID, previous and next installation ID, and coordinates.
- `AavegotchiDiamondUpdated:` Emitted when the Aavegotchi diamond contract address is updated.
  Again, these events allow external listeners to track changes to parcels and installations as they occur on-chain. The coordinates allow tracking exact placement.

## - CONCLUSION

The analysis of the `RealmFacet.sol` smart contract reveals its critical role in managing the core functionalities of the Aavegotchi Realm Diamond. By leveraging the EIP-2535 standard and employing a comprehensive set of imported libraries, the contract efficiently handles various operations related to the creation, modification, and removal of parcels, installations, and tiles within the 'Gotchiverse' metaverse. The use of modifiers ensures that all actions are validated and in line with the established rules, thereby maintaining the integrity and security of the gameplay. Additionally, the emitted events provide valuable insights for external entities to track changes and updates within the Aavegotchi ecosystem.
