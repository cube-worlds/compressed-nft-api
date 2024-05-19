# Compressed NFT Augmenting API

## How to Use

### Setup

1. **Create a PostgreSQL database.**
2. **Copy the following files to a directory:**
   - `server` binary
   - `ctl` binary
   - `.env.example`
3. **Configure the `.env` file:**
   - Set `POSTGRES_URI` to your database URI.
   - Set `PORT` to the desired port.
   - Generate and set `ADMIN_*` credentials.
   - Set `DEPTH` as needed (maximum items = `2^DEPTH`, max `DEPTH` = 30).
   - Set `DATA_DIR` to the directory where small `.json` files will be stored.
   - Set `TONCENTER_URI`, removing `testnet.` for mainnet deployment.
4. **Navigate to the directory containing `ctl` and `.env`.**
5. **Prepare an `owners.txt` file:**
   - List item owner addresses, one per line.
   - The first address gets item index 0, and so on.
6. **Initialize the database:**
   - Run `./ctl migrate` to create the necessary tables.
   - Run `./ctl add owners.txt` to add the addresses.
7. **Host your collection and item metadata:**
   - Follow the [Token Data Standard](https://github.com/ton-blockchain/TEPs/blob/master/text/0064-token-data-standard.md).
   - Use a consistent URI pattern for item metadata (e.g., `base-uri/item-index.json`).
8. **Run `./server`:**
   - Use a utility like [screen](https://www.gnu.org/software/screen/manual/screen.html) to keep it running.
   - Ensure the server port is accessible publicly.
9. **Rediscover items:**
   - Navigate to `api-uri/admin/rediscover` using your `ADMIN_*` credentials.
   - Verify the appearance of `DATA_DIR/upd/1.json`.
10. **Generate an update:**
    - Run `./ctl genupd path-to-update-file collection-owner collection-meta item-meta-prefix royalty-base royalty-factor royalty-recipient api-uri-including-v1`.
    - Replace placeholders with appropriate values.
11. **Invoke the `ton://` deeplink that appears.**
12. **Set the collection address:**
    - Navigate to `api-uri/admin/setaddr/collection-address`.
    - Use the address from step 10.
13. **Wait for a `committed state` message in `server` logs.**
14. **Done.**

### Updating

1. **Prepare a `new-owners.txt` file:**
   - List new owner addresses, starting from the next available index.
2. **Add new owners:**
   - Run `./ctl add new-owners.txt`.
3. **Rediscover items:**
   - Navigate to `api-uri/admin/rediscover`.
4. **Locate the new update file:**
   - Find it under `DATA_DIR/upd` (e.g., `2.json` if the last update was `1.json`).
5. **Generate an update:**
   - Run `./ctl genupd path-to-update-file collection-address`.
6. **Invoke the `ton://` deeplink that appears.**
7. **Wait for a `committed state` message in `server` logs.**
8. **Done.**

**Note:** Avoid updating during high traffic. Invalid proofs may occur during the brief period when the on-chain transaction is processed but not yet detected by the API. Update in large batches during low traffic times.

# License

[MIT](LICENSE)
