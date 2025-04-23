This is an example program to show how to verify a program whos upgrade authority is a multisig.

First build the program:

```bash
if let Err(e) = solana_verify::build() {
    eprintln!("Error when assembling the program: {}", e);
    std::process::exit(1);
}
```

This will build a verifiable build using a docker container using solana version 2.1.0 which is taken from the cargo.lock file.

```bash
solana config set --url "AProperMainnetRPCNodeAddress" // the public endpoint will be ratelimted too much
solana program deploy target/deploy/verify_squads.so
```

Get program id -> 6XBGfP17P3KQAKoJb2s5M5fR4aFTXzPeuC1af2GYkvhD

Once that is done we commit the project to github: https://github.com/Woody4618/verify-squads

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
if let Err(e) = solana_verify::build() {
    eprintln!("Error when assembling the program: {}", e);
    std::process::exit(1);
}
solana program write-buffer target/deploy/verify_squads.so --with-compute-unit-price 50000 --max-sign-attempts 50
```

Then transfer that buffer authority to the multisig.

```bash
solana program set-buffer-authority Fu3k79g53ZozAj47uq1tXrFy4QbQYh7y745DDsxjtyLR --new-buffer-authority 3JG6ULvZVCrkKtSSskKNJGe8RNZGFe8Ruev9KUhxzK5K
```

Export the PDA upgrade transaction again:

```bash
solana-verify export-pda-tx https://github.com/Woody4618/verify-squads --program-id 6XBGfP17P3KQAKoJb2s5M5fR4aFTXzPeuC1af2GYkvhD --uploader 3JG6ULvZVCrkKtSSskKNJGe8RNZGFe8Ruev9KUhxzK5K
```

https://solana.fm/tx/4zJ1vK2KToAwxuEYzTMLqPkcebjoi9rdeeyxtEEx9L5Q4vWDA8h6Rr4kPRuRxcV7ZLKMr6qx1LTWb6x3ZpUJaFUW?cluster=mainnet-alpha

Then submit for another remote build:

```bash
solana-verify remote submit-job --program-id 6XBGfP17P3KQAKoJb2s5M5fR4aFTXzPeuC1af2GYkvhD --uploader 3JG6ULvZVCrkKtSSskKNJGe8RNZGFe8Ruev9KUhxzK5K
```

Should result in something like this:

Verification request sent with request id: b63339d2-163e-49ac-b55d-3454c1c2b5b3
Verification in progress... ⏳
[00:18:02]    ✅ Process completed. (Done in 18 minutes)                                                                                                      Program 6XBGfP17P3KQAKoJb2s5M5fR4aFTXzPeuC1af2GYkvhD has been verified. ✅
The provided GitHub build matches the on-chain hash.
On Chain Hash: 96f8c3d9400258f7759408d1f6f8435b4a24d9b52f5a0340d97907e567cb8773
Executable Hash: 96f8c3d9400258f7759408d1f6f8435b4a24d9b52f5a0340d97907e567cb8773
Repo URL: https://github.com/Woody4618/verify-squads/tree/0fb0a2e30c15c51732c0ad5e837975a6f7bbc7ed
Check the verification status at: https://verify.osec.io/status/6XBGfP17P3KQAKoJb2s5M5fR4aFTXzPeuC1af2GYkvhD
Job url: https://verify.osec.io/job/b63339d2-163e-49ac-b55d-3454c1c2b5b3

Gratulations you have verified your program after a multisig upgrade!
