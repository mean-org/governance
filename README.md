# Mean Governance
Mean DAO Governance infra and program instructions

Assuming we have the following directory structure:
workdir
|--devnet/
|--localnet/
|--mainnet/

## Generate a new upgrade authority keypair
```bash
cd workdir/localnet
solana-keygen new -o localnet-upgrade-authority.json
```

## Deploy our own Mean instance of the spl-governace program

Type ENTER to leave the passphrase empty. Save the seeds and private key in a secure place.

### Generate a custom spl-governace program address keypair
```bash
cd workdir/localnet
solana-keygen grind --starts-with MGov:1
```

### Clone spl-governance and git checkout to the latest release commit
```bash
cd workdir
git clone https://github.com/mean-dao/solana-program-library.git
cd workdir/solana-program-library
git fetch --all --tags
git checkout tags/governance-v2.2.6
```

### Build the spl-governance program
```bash
cd workdir/solana-program-library
cd governance/program
cargo build-bpf
```

### Deploy the spl-governance program

```bash
cd workdir
solana program deploy\
        --url localhost\
        --program-id ./localnet/MGovW65tDhMMcpEmsegpsdgvzb6zUwGsNjhXFxRAnjd.json\
        --upgrade-authority ./localnet/localnet-upgrade-authority.json\
        --verbose\
        ./solana-program-library/target/deploy/spl_governance.so
```

### Transfer upgrade authority to DAO multisig
```bash
solana program set-upgrade-authority MGovW65tDhMMcpEmsegpsdgvzb6zUwGsNjhXFxRAnjd\
        --url localhost\
        --upgrade-authority ./localnet/localnet-upgrade-authority.json\
        --new-upgrade-authority DxtAsWiebDtfFXmJSiMnmKrJi5RLLPReE6QUVtJHiiFJ
```

## Deploy our own Mean instance of the voter-stake-registry program

### Generate a custom voter-stake-registry program address keypair
```bash
cd workdir/localnet
solana-keygen grind --starts-with MVSR:1
```

### Clone voter-stake-registry and git checkout to the latest release commit
```bash
cd workdir
git clone https://github.com/mean-dao/voter-stake-registry.git
cd workdir/voter-stake-registry
git checkout master
```

### Build the voter-stake-registry program
```bash
cd workdir/voter-stake-registry
anchor build
```

### Deploy the voter-stake-registry program

```bash
cd workdir
solana program deploy\
        --url localhost\
        --program-id ./localnet/MVSRXSGsDxcmKWMFQyufxJh3NWmpmgZmT9YHTr4MioB.json\
        --upgrade-authority ./localnet/localnet-upgrade-authority.json\
        --verbose\
        ./voter-stake-registry/target/deploy/voter_stake_registry.so
```

### Transfer upgrade authority to DAO multisig
```bash
solana program set-upgrade-authority MVSRXSGsDxcmKWMFQyufxJh3NWmpmgZmT9YHTr4MioB\
        --url localhost\
        --upgrade-authority ./localnet/localnet-upgrade-authority.json\
        --new-upgrade-authority DxtAsWiebDtfFXmJSiMnmKrJi5RLLPReE6QUVtJHiiFJ
```

### Upgrade voter-stake-registry program (write buffer + upgrade from Supersafe UI)
```bash
solana program write-buffer ./voter-stake-registry/target/deploy/voter_stake_registry.so\
        --url localhost
solana program set-buffer-authority BUFFER_ADDRESS\
        --new-buffer-authority DxtAsWiebDtfFXmJSiMnmKrJi5RLLPReE6QUVtJHiiFJ\
        --url localhost
```

## References
 - https://github.com/solana-labs/solana-program-library/tree/master/governance
 - https://github.com/blockworks-foundation/voter-stake-registry
 - https://github.com/solana-labs/solana-program-library/tree/governance-v2.2.6


