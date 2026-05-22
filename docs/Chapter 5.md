# Chapter 5. Write the blockchain when adopting a pet.


Currently, when you press the "Adopt it now!" button, nothing happens. This is because we have a button that does not do anything:

```javascript
<Button size="small" variant="contained">
  Adopt it now! ❤️
</Button>

```

To change this, we will create a function component that will interact with the blockchain using the
[@soroban-react/contracts](https://github.com/esteblock/soroban-react) package

```
yarn add soroban-client
```

## 5.1 Function Component
Let's create a function component with a function that will interact with the blockchain

Create the  `pet-adopt-soroban/src/soroban/AdoptPetButton.tsx` file and paste

```javascript
import React from 'react'
import {useSorobanReact } from "@soroban-react/core"
import Button from '@mui/material/Button';
import {useSendTransaction, contractTransaction} from '@soroban-react/contracts'
import * as SorobanClient from 'soroban-client'
import addresses from '../soroban/addresses.json'


interface AdoptPetButtonProps {
    id: number,
}


export function AdoptPetButton ({id}: AdoptPetButtonProps){
    const sorobanContext =  useSorobanReact()
    const { sendTransaction } = useSendTransaction(undefined, {sorobanContext})
    const { activeChain, server, address } = sorobanContext
    
    
    const handleAdopt = async (): Promise<void> => {
        if (!activeChain || ! address || !server) {
            console.log("No active chain")
            return
        }
        else{
            try{
            let { sequence } = await server.getAccount(address)
            let source = new SorobanClient.Account(address, sequence)
    
            const transaction = contractTransaction({
                networkPassphrase: activeChain.networkPassphrase,
                source: source,
                contractId: addresses.pet_adopt_id,
                method: 'adopt'})

                const result = await sendTransaction(transaction)
                console.log("adoptPet.tsx:sendTransaction:result: ", result)
            }
            catch(error){
                console.log("Error while sending the transaction: ", error)

            }
        }

    }


    return(
        <Button
            size="small"
            variant="contained"
            onClick={handleAdopt}>
              Adopt it now! ❤️
          </Button>

    )
}
```


## 5.2 Replace your dummy button for the new button created:

In the `pet-adopt-soroban/src/components/DogComponent.tsx` file, change
```javascript
import Button from '@mui/material/Button'
```
for 
```javascript
import {AdoptPetButton} from '../soroban/AdoptPetButton';
```

and later, change
```javascript
<Button size="small" variant="contained">
  Adopt it now! ❤️
</Button>

```
for
```javascript
<AdoptPetButton id={id}></AdoptPetButton>
```

## 5.3 Fund your Frighter account

Before signing messages, you need to have some XMR in your wallet. Go to the browser and paste:

http://localhost:8000/friendbot?addr=YOURADDRESS
where YOURADDRESS is your G... public key.

## 5.4 Adopt your favourite pet!
## 5.5 Check it
Check that in the front end, now the pet has been adopted!

Your code should look like here:

https://github.com/esteblock/pet-adopt-soroban/tree/final-chapter5

## 5.6 Help
Go to https://github.com/esteblock/pet-adopt-soroban/issues
and let's continue the discussion!

