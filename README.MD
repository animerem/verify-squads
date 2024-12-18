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
solana-verify verify-from-repo https://github.com/Woody4618/verify-squads --program-id 
```


