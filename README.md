# Compressed NFT Augmenting API

This repositry contains implementation [cNFT TEP-? draft standart](https://github.com/ton-blockchain/TEPs/blob/6fe57a10beb160140a2a98cf3bf0efb4e079b017/text/0000-compressed-nft-standard.md)

## How to Use

### Setup

1. **Install PostgreSQL and [Go](https://go.dev/doc/install).**
2. **Build sources**
   - `go mod download` to dowload dependencies
   - `CGO_ENABLED=0 go build -o server cmd/server/main.go`
   - `CGO_ENABLED=0 go build -o ctl cmd/ctl/main.go`
3. **Copy the following files to a directory:**
   - `server` binary
   - `ctl` binary
   - and copy environment file `cp .env.example .env`
4. **Configure the `.env` file:**
   - Set `POSTGRES_URI` to your database URI.
   - Set `PORT` to the desired port.
   - Generate and set `ADMIN_*` credentials.
   - Set `DEPTH` as needed (maximum items = `2^DEPTH`, max `DEPTH` = 30).
   - Set `DATA_DIR` to the directory where small `.json` files will be stored.
   - Set `TONCENTER_URI`, removing `testnet.` for mainnet deployment.
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
   - Ensure the server port is publicly accessible.
9. **Rediscover items:**
   - Navigate to `/admin/rediscover` using your `ADMIN_*` credentials.
   - Verify the appearance of `[DATA_DIR]/upd/1.json`.
10. **Generate an update:**
    - Run `./ctl genupd [path-to-update-file] [collection-owner] [collection-meta] [item-meta-prefix] [royalty-base] [royalty-factor] [royalty-recipient] [api-uri-including-v1]`.
    - Replace placeholders with appropriate values, for example:
      `./ctl genupd `pwd`/apidata/upd/1.json UQDSqdhnwMllRlp0EqB4asBQiGhNWNa-9S4hPSVONLfr0WF3 https://cubeworlds.club/api/nft/collection.json https://cubeworlds.club/api/nft/ 10 100 UQDSqdhnwMllRlp0EqB4asBQiGhNWNa-9S4hPSVONLfr0WF3 https://cubeworlds.club/cnfts/v1`
11. **Invoke the `ton://` deeplink that appears.**
12. **Set the collection address:**
    - Navigate to `/admin/setaddr/[collection-address]`, using the `collection-address` from result of previous step.
13. **Wait for a `committed state` message in the `server` logs.**
14. **Done.**

### Updating

1. **Prepare a `new-owners.txt` file:**
   - List new owner addresses, starting from the next available index.
2. **Add new owners:**
   - Run `./ctl add new-owners.txt`.
3. **Rediscover items:**
   - Navigate to `[api-uri]/admin/rediscover`.
4. **Locate the new update file:**
   - Find it under `[DATA_DIR]/upd` (e.g., `2.json` if the last update was `1.json`).
5. **Generate an update:**
   - Run `./ctl genupd [path-to-update-file] [collection-address]`, where collection-address is `EQAaSqEwAh00YOCc9ZwtqfNcXeehbl97yKQKCZPRGwCov51V` from creating steps.
6. **Invoke the `ton://` deeplink that appears.**
7. **Wait for a `committed state` message in the `server` logs.**
8. **Done.**

**Note:** Avoid updating during high traffic periods to prevent invalid proofs. This may occur briefly when the on-chain transaction is processed but not yet detected by the API. Perform updates in large batches during low traffic times.

# License

[MIT](LICENSE)
