# Chapter 3: Deploy the Smart Contract

## 3.1 Clone de smart contracts repo
```
git submodule add https://github.com/esteblock/pet-adopt-soroban-contracts contracts
```

## 3.2 Build the contract
```
cargo build --target wasm32-unknown-unknown --release
```

# https://laboratory.stellar.org/#account-creator?network=test

# Deploy the Smart Contract to a local network:
1. First in a terminal run a local network instance
```
docker run --rm -it \
  --platform linux/amd64 \
  -p 8000:8000 \
  --name stellar \
  stellar/quickstart:soroban-dev@sha256:8046391718f8e58b2b88b9c379abda3587bb874689fa09b2ed4871a764ebda27 \
  --standalone \
  --enable-soroban-rpc
```
2. Set your private and public ket in your evironment
```
cp .env.example .env
```
Place your secrets
```
export $(cat .env)
```
3. Fund your wallet:
```
curl "http://localhost:8000/friendbot?addr=$PUBLICKEY"
```
4. 
```
mkdir -p .soroban
PET_ADOPT_ID="$(
  soroban deploy \
    --wasm target/wasm32-unknown-unknown/release/pet_adopt_soroban.wasm \
    --secret-key $SECRETKEY \
    --rpc-url http://localhost:8000/soroban/rpc \
    --network-passphrase 'Standalone Network ; February 2017'
)"
echo "$PET_ADOPT_ID" > .soroban/pet_adopt_id
echo `Contract deployed with id $PET_ADOPT_ID`

```

Now the contract if will be in .soroban/pet_adopt_id


5. Check if you can read and write the smart contract from the terminal

Adopt a pet from the terminal

```
soroban invoke \
    --id $PET_ADOPT_ID \
    --secret-key $SECRETKEY \
    --rpc-url http://localhost:8000/soroban/rpc \
    --network-passphrase 'Standalone Network ; February 2017' \
    --fn adopt \
    --arg 1
```

Check who is the adopter of pet 1:
```
soroban invoke \
    --id $PET_ADOPT_ID \
    --secret-key $SECRETKEY \
    --rpc-url http://localhost:8000/soroban/rpc \
    --network-passphrase 'Standalone Network ; February 2017' \
    --fn adopter \
    --arg 1
```

Check who is the adopter of pet 2:
```
soroban invoke \
    --id $PET_ADOPT_ID \
    --secret-key $SECRETKEY \
    --rpc-url http://localhost:8000/soroban/rpc \
    --network-passphrase 'Standalone Network ; February 2017' \
    --fn adopter \
    --arg 1
```

As expected, you'll see that if no one has adopted the pet, the current adopter is the Contract itself (it has been coded this way). You should see something like:

```
["Contract",[220,228,152,154,195,137,188,41,177,27,202,52,159,3,226,232,105,48,20,182,152,122,13,236,11,241,175,157,0,104,99,173]]

```

# Read the Smart Contract from the ReactJS front end (webpage)

As you can see, the src/components/DogComnent.tsx file, it's hardcoded and says
"Adopted: Not yet 😥" for every pet. We will change this

## Add a function that given a ped id, will read the blockchain and will see if the pet has been adopted or not:
To be able to interact with smart contracts, we need to install the @soroban-react/contracts package
```
yarn add @soroban-react/contracts
```
Create a the new file src/soroban/readContract.tsx and paste:

```
```

then in add the line `import { isPetAdopted } from '../soroban/readContract';
`
and change
```
Adopted: Not yet 😥
```


## symbolic link

ln -s ./contracts/soroban/pet_adopt_id ./src/soroban/pet_adopt_id

echo "{\"pet_adopt_id\": \"$(cat contracts/.soroban/pet_adopt_id)\"}" > src/soroban/addresses.json
