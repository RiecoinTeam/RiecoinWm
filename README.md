# Riecoin Wallet Migrator

This is a stripped down version of Riecoin Core for migrating Legacy to Descriptor Wallets in order to be able to use Riecoin Core 23.04+.

Binaries for Linux and Windows x64 are provided on the present GitHub repository.

They can also be built following similar instructions as for [Riecoin Core](https://github.com/RiecoinTeam/Riecoin/). If not using Guix, BDB should be installed and `--with-incompatible-bdb` should be used.

```bash
./autogen.sh ; ./configure --with-incompatible-bdb ; make
```

## Who Needs to Migrate?

Users who are

* currently using Riecoin Core **0.21 or older**,
* currently using Riecoin Core **22.03**, but **already used 0.21 or older in the past** without recreating wallets via 22.03, or generated **purposely** Legacy Wallets with 22.03,

will have Legacy Wallets that need to be migrated. This is certain on 0.21 and older, on 22.03, you may double check if you indeed have Legacy Wallets with the `getwalletinfo` RPC Command (it is Legacy if it shows `"format": "bdb"` or ` "descriptors": false`; if it is not the case, you actually have a Descriptor Wallet that does not need to be upgraded).

If you are using Riecoin Core **23.04** or more recent, you are **guaranteed** to have Descriptor Wallets and **no action** is required, **regardless** of whether you used 22.03 or older in the past (either because you already migrated or did not keep old wallets and generated new ones). 23.04+ are indeed unable to open Legacy Wallets.

## How to Migrate?

In all cases, make a **backup of all your wallets** just in case - as long as you saved your wallet.dats somewhere, there will always be a a way to recover your funds if something goes wrong. Don't forget to shut down Riecoin Core before making such backup.

### Using the Wallet Migrator

* Via `riecoinwm-qt`: after making a backup of your Wallets or the Riecoin Data Folder, stop any running Riecoin Core instance, then run the Qt version of the Wallet Migrator. Under the File menu, you can Open any Riecoin Wallet (if not already opened), and then click on "Migrate Wallet", again under the File Menu. If this option is disabled, it means that this Wallet is already a Descriptor Wallet and 
* Via `riecoinwmd`/`riecoinwm-cli`: after making a backup of your Wallets or the Riecoin Data Folder, stop any running Riecoin Core instance. Then, run the migrator daemon `riecoinwmd`, and then use the `riecoinwm-cli` binary to run the `migratewallet` command for every wallet to upgrade. Example:


```bash
 ./riecoinwm-cli -rpcuser=User -rpcpassword=Password -rpcwallet=Pool migratewallet
{
  "wallet_name": "Pool",
  "backup_path": "/home/user/.riecoin/wallets/Pool/Pool_1713190683.legacy.bak"
}
```

### Other Migration Options

The migration code is taken from Bitcoin 27's, and essentially remains untouched, so it should work well as it is no longer considered as experimental by the Bitcoin Developers unlike at the time Riecoin 23.04 was released (which is why the Wallet Migrator was not provided with this release). But should it still fail nevertheless due to a corrupted wallet or a bug, here are other options to perform the migration.

* Send all your funds to an exchange, delete your wallets, and proceed to the upgrade as usual. Then, create a new wallet with the new version, get a new address, send your coins back to yourself, and you are done. Riecoin Transactions Fees are negligible, though be sure to check the exchange deposit (if any) and withdrawal fees.
	* Alternatively, if you know how to do that, you can try to run both instances of Riecoin Core simultaneously and send directly to your own address without using an exchange.
* Export the old addresses with `dumpprivkey` via the older Riecoin Core that still supports Legacy Wallets, then use 24.04+ to create a new Wallet and import Addresses with `importdescriptors`
	* The Base58 Wif Format has been dropped since 22.03 in favor of a straightforward Hex one, so if your `dumpprivkey` still provides WIF Keys, you need to convert them to Hex, and use the `prv` prefix.
* Write your own script that automates the previous point.

If you are running a Riecoin Mining Pool or an Exchange that has Riecoin listed, you can always ask the Riecoin Developers for assistance

## License

The Riecoin Wallet Migrator is released under the terms of the MIT license. See [COPYING](COPYING) for more information or see https://opensource.org/licenses/MIT.
