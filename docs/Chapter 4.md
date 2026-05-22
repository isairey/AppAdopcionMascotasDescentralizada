# Chapter 4:  Read the Smart Contract from the ReactJS front end (webpage)

As you can see, the `src/components/DogComnent.tsx` file, it's hardcoded and says
"Adopted: Not yet 😥" for every pet. We will change this

## 4.1 Put the contract id in the project
This can be better, please contribute with your ideas!!

```
echo "{\"pet_adopt_id\": \"$(cat contracts/.soroban/pet_adopt_id)\"}" > src/soroban/addresses.json

```

## 4.2 Create an isAdopted function

We will create a function that given a ped id, will read the blockchain and will see if the pet has been adopted or not:

To be able to interact with smart contracts, we need to install the following packages

```
yarn add @soroban-react/contracts
yarn add @soroban-react/utils
yarn add bignumber.js
```


Create a the new file `pet-adopt-soroban/src/soroban/isPetAdopted.tsx` and paste:

```javascript
import { SorobanContextType } from '@soroban-react/core';
import addresses from './addresses.json'
import {useContractValue, useContractValueProps} from '@soroban-react/contracts'
import BigNumber from 'bignumber.js'
import {bigNumberToI128} from '@soroban-react/utils'

interface IsPetAdoptedProps {
    id: number,
    sorobanContext: SorobanContextType
}


export function isPetAdopted ({id, sorobanContext}: IsPetAdoptedProps){
            let id_BN = new BigNumber(id)
            let id_scval = bigNumberToI128(id_BN.shiftedBy(0).decimalPlaces(0))
            let isAdopted = false
            try{
                isAdopted = useContractValue({
                    contractId: addresses.pet_adopt_id,
                    method: 'adopted',
                    params: [id_scval],
                    sorobanContext: sorobanContext})
            }
            catch (error){
                console.log("IsPetAdopted.tsx: error: ", error)

            }
                
        return isAdopted
    }

```

Now in the `DogComponent.tsx` file:

Import the new function
```javascript
import { isPetAdopted } from '../soroban/isPetAdopted';

```

and change
```javascript
<Typography>
   Adopted: Not yet 😥
</Typography>   
```

for:
```javascript
<Typography> Adopted: 
       {
          isPetAdopted({id: id, sorobanContext: sorobanContext})  ? "Yes 🥰!" : "Not yet 😥" 
          }
</Typography>
```

## 4.3 Adopt using the terminal and see the changes!

## 4.4 Code:
Your code should be like this:
https://github.com/esteblock/pet-adopt-soroban/tree/final-chapter4


