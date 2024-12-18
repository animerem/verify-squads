This is an example program to show how to verify a program whos upgrade authority is a multisig.

First build the program:

```bash
solana-verify build
```

This will build a verifiable build using a docker container using solana version 2.1.0 which is taken from the cargo.lock file.

```bash
solana config set --url "AProperMainnetRPCNodeAddress" // the public endpoint will be ratelimted too much
solana program deploy target/deploy/verify_squads.so
```

Get program id -> 6XBGfP17P3KQAKoJb2s5M5fR4aFTXzPeuC1af2GYkvhD

Once that is done we commit the project to: https://github.com/Woody4618/verify-squads

Then we see if we can verify the program using:

```bash
solana-verify verify-from-repo https://github.com/Woody4618/verify-squads --program-id 6XBGfP17P3KQAKoJb2s5M5fR4aFTXzPeuC1af2GYkvhD --remote
```

Pick yes when asked to upload onchain data. Then wait for the remote job to be finished. This can take multiple minutes.

Multisig:

First transfer your programs authority to the multisig and copy the multisig authority. You need it in the next step.

```bash
solana-verify export-pda-tx https://github.com/Woody4618/verify-squads --program-id 6XBGfP17P3KQAKoJb2s5M5fR4aFTXzPeuC1af2GYkvhD --uploader 3JG6ULvZVCrkKtSSskKNJGe8RNZGFe8Ruev9KUhxzK5K
```

This will return you a base58 or base64 encoded transaction:

```bash
P6vBfcPaaXb8fZoT3NBAYEcdtEj7tubA1k2gBxmFKZ3UWF5YyrmDMFTvLKALCJoUuRsPAjMckudYruCu3eeWQtuDrFbEMLxLFutnKXac974fnkMivcwUdY66VLjbxQT6ATmcy7F4hBtz1G4P1h6iBJLhb8WtrtgY3i4qq45MUEb7RjuMEfUFXKrNgPdGxkz5xvMHq3dxKRcpmEK5k2DkeW6SUQYBVe19Ga3B9GyhTX8k3CMt9JCEah13WyRnQd8GjoK6sTEvGJym6xDNvmd8yiJYSNcaYwEJsjHEUf4Yh6kAC7ki2KRvVAr3NVe1gjqK9McrwSQjtUatvydTG8Zovcr7PPUEMf3yPMgKXjZLB2QpkH63yTTYdNAnWFuv9E6b6nYRqye5XcNi436yKw5U14fXh65yK34bgYLi9328UT1huJELsJU9BRGnGUmb6GWp6c2WL5BhnzgNTSnt9TXFfEgUMzhvKzpVBxLP44hwqqBdyUhHFysCF37531PnmiESq8x1xou23xJ6FcQbc199754MkqQd7tX9CUznGzAEqHGkzn3VBoJnojsKtgYmiTYbdRsT1CU18MbYEE7WvGAvXyxxbpNzbAcc94HrnM6cqRGmwhEBroPfFghTdmzg9D
```

Go to the squads transaction builder and import the base58 encoded transaction.
Make sure that in the simulation the transaction only has a call to the osec verify program and the computer budget program and nothing else!

```bash
solana-verify remote submit-job --program-id 6XBGfP17P3KQAKoJb2s5M5fR4aFTXzPeuC1af2GYkvhD --uploader 3JG6ULvZVCrkKtSSskKNJGe8RNZGFe8Ruev9KUhxzK5K
```



Doing an update to the program:

```bash
solana-verify build
solana program write-buffer target/deploy/verify_squads.so --with-compute-unit-price 50000 --max-sign-attempts 50
```

