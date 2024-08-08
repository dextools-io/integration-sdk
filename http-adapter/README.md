# DEXTools adapter

> Version 0.0.1

This documentation is aim to define the interface required by DEXTools application in order to integrate data of
exchanges in a unified way.

The goal of this API is speeding up the integration of new blockchains into DEXTools by providing the developers of
the exchanges with the structure of the information DEXTools can understand.

## Path Table

| Method | Path | Description |
| --- | --- | --- |
| GET | [/latest-block](#getlatest-block) | Latest block |
| GET | [/block](#getblock) | Block by number or timestamp |
| GET | [/asset](#getasset) | Asset by id |
| GET | [/pair](#getpair) | Pair by id |
| GET | [/events](#getevents) | Events |

## Reference Table

| Name | Path | Description |
| --- | --- | --- |
| Block | [#/components/schemas/Block](#componentsschemasblock) |  |
| Asset | [#/components/schemas/Asset](#componentsschemasasset) |  |
| Pair | [#/components/schemas/Pair](#componentsschemaspair) |  |
| Event | [#/components/schemas/Event](#componentsschemasevent) |  |
| ResponseOfBlock | [#/components/schemas/ResponseOfBlock](#componentsschemasresponseofblock) |  |
| ResponseOfAsset | [#/components/schemas/ResponseOfAsset](#componentsschemasresponseofasset) |  |
| ResponseOfPair | [#/components/schemas/ResponseOfPair](#componentsschemasresponseofpair) |  |
| ResponseOfEvents | [#/components/schemas/ResponseOfEvents](#componentsschemasresponseofevents) |  |
| Issue | [#/components/schemas/Issue](#componentsschemasissue) |  |
| ResponseOfError | [#/components/schemas/ResponseOfError](#componentsschemasresponseoferror) |  |
| ErrorNotFound | [#/components/responses/ErrorNotFound](#componentsresponseserrornotfound) | Not Found |
| ErrorTooManyRequests | [#/components/responses/ErrorTooManyRequests](#componentsresponseserrortoomanyrequests) | Too Many requests |
| ErrorInternal | [#/components/responses/ErrorInternal](#componentsresponseserrorinternal) | Internal error |

## Path Details

***

### [GET]/latest-block

- Summary  
Latest block

- Description  
Retrieves details of the latest block processed in the blockchain.  
  
This endpoint is used to limit the range of events requested during the process of blocks in real time.  
  
It's mandatory that this endpoint returns a block only when all events of that block have been processed and are  
available at the _events_ endpoint. If not, DEXTools might loose some events and they won't be available ever in the  
platform.

#### Responses

- 200 OK

`application/json`

```ts
{
  block: {
    // Number of the block
    blockNumber: integer
    // Timestamp (in seconds) the block was confirmed at
    blockTimestamp: integer
  }
}
```

- 429 Too may requests

- 500 Internal server error

***

### [GET]/block

- Summary  
Block by number or timestamp

- Description  
Retrieves details of a specific block using either the number of the block or its timestamp.  
  
For timestamp searching, this endpoint should return the youngest block with the timestamp less than or equal  
to the requested one.  
  
If none of the parameters are requested, this endpoint must return a 400 error.  
  
If both parameters are requested, number option takes precedence.  
  
**NOTE**: The timestamp option is required only for blockchains with sharding or any other kind of partition,  
where different partitions handle different block numbers.

#### Parameters(Query)

```ts
number?: integer
```

```ts
timestamp?: integer
```

#### Responses

- 200 OK

`application/json`

```ts
{
  block: {
    // Number of the block
    blockNumber: integer
    // Timestamp (in seconds) the block was confirmed at
    blockTimestamp: integer
  }
}
```

- 400 Bad request

`application/json`

```ts
{
  code: string
  message: string
  issues: {
    code?: string
    param?: string
    message: string
  }[]
}
```

- 404 Not found

- 429 Too may requests

- 500 Internal server error

***

### [GET]/asset

- Summary  
Asset by id

- Description  
Retrieves details of a given asset (aka token) by its address

#### Parameters(Query)

```ts
id: string
```

#### Responses

- 200 OK

`application/json`

```ts
{
  asset: {
    // Address of the token
    id: string
    // Name of the token
    name: string
    // Symbol of the token
    symbol: string
    // Total supply of the token at current time
    totalSupply: string
    // Circulating supply of the token at current time
    circulatingSupply: string
  }
}
```

- 400 Bad request

`application/json`

```ts
{
  code: string
  message: string
  issues: {
    code?: string
    param?: string
    message: string
  }[]
}
```

- 404 Not found

- 429 Too may requests

- 500 Internal server error

***

### [GET]/pair

- Summary  
Pair by id

- Description  
Retrieves details of a given pair (aka pool) by its address

#### Parameters(Query)

```ts
id?: string
```

#### Responses

- 200 OK

`application/json`

```ts
{
  pair: {
    // Address of the pair
    id: string
    // Address of the first token of the pair
    asset0Id: string
    // Address of the second token of the pair
    asset1Id: string
    // Number of block the pair was created at
    createdAtBlockNumber: integer
    // Timestamp (in seconds) of the block the pair was created at
    createdAtBlockTimestamp: integer
    // Hash of the transaction the pair was created at
    createdAtTxnId: string
    // Address of the smart contract used to create the pair
    factoryAddress: string
  }
}
```

- 400 Bad request

`application/json`

```ts
{
  code: string
  message: string
  issues: {
    code?: string
    param?: string
    message: string
  }[]
}
```

- 404 Not found

- 429 Too may requests

- 500 Internal server error

***

### [GET]/events

- Summary  
Events

- Description  
List of events occured in a range of blocks

#### Parameters(Query)

```ts
fromBlock: integer
```

```ts
toBlock: integer
```

#### Responses

- 200 OK

`application/json`

```ts
{
  events: {
    block: {
      // Number of the block
      blockNumber: integer
      // Timestamp (in seconds) the block was confirmed at
      blockTimestamp: integer
    }
    // Hash of the transaction the event belongs to
    txnId: string
    // Index of the transaction the event belongs to
    txnIndex: integer
    // Index of the event inside the transaction
    eventIndex: integer
    // Address of the wallet who request the transaction
    maker: string
    // Address of the pair involved in the transaction
    pairId: string
    // Type of event (creation -> Pair created; swap -> Swap; join -> Add liquidity; exit -> Remove liquidity)
    eventType: enum[creation, swap, join, exit]
    // Only for joins and exits: Number of tokens of asset0 added to the pool
    amount0?: string
    // Only for joins and exits: Number of tokens of asset1 added to the pool
    amount1?: string
    // Only for swaps: Number of tokens of asset0 sold
    asset0In?: string
    // Only for swaps: Number of tokens of asset1 bought
    asset1Out?: string
    // Only for swaps: Number of tokens of asset0 bought
    asset0Out?: string
    // Only for swaps: Number of tokens of asset1 sold
    asset1In?: string
    // Only for joins, exists and swaps: Reserves of each token remaining after the event has been executed
    reserves: {
      // Reserves of token asset0
      asset0: string
      // Reserves of token asset1
      asset1: string
    }
  }[]
}
```

- 400 Bad request

`application/json`

```ts
{
  code: string
  message: string
  issues: {
    code?: string
    param?: string
    message: string
  }[]
}
```

- 404 Not found

- 429 Too may requests

- 500 Internal server error

## References

### #/components/schemas/Block

```ts
{
  // Number of the block
  blockNumber: integer
  // Timestamp (in seconds) the block was confirmed at
  blockTimestamp: integer
}
```

### #/components/schemas/Asset

```ts
{
  // Address of the token
  id: string
  // Name of the token
  name: string
  // Symbol of the token
  symbol: string
  // Total supply of the token at current time
  totalSupply: string
  // Circulating supply of the token at current time
  circulatingSupply: string
}
```

### #/components/schemas/Pair

```ts
{
  // Address of the pair
  id: string
  // Address of the first token of the pair
  asset0Id: string
  // Address of the second token of the pair
  asset1Id: string
  // Number of block the pair was created at
  createdAtBlockNumber: integer
  // Timestamp (in seconds) of the block the pair was created at
  createdAtBlockTimestamp: integer
  // Hash of the transaction the pair was created at
  createdAtTxnId: string
  // Address of the smart contract used to create the pair
  factoryAddress: string
}
```

### #/components/schemas/Event

```ts
{
  block: {
    // Number of the block
    blockNumber: integer
    // Timestamp (in seconds) the block was confirmed at
    blockTimestamp: integer
  }
  // Hash of the transaction the event belongs to
  txnId: string
  // Index of the transaction the event belongs to
  txnIndex: integer
  // Index of the event inside the transaction
  eventIndex: integer
  // Address of the wallet who request the transaction
  maker: string
  // Address of the pair involved in the transaction
  pairId: string
  // Type of event (creation -> Pair created; swap -> Swap; join -> Add liquidity; exit -> Remove liquidity)
  eventType: enum[creation, swap, join, exit]
  // Only for joins and exits: Number of tokens of asset0 added to the pool
  amount0?: string
  // Only for joins and exits: Number of tokens of asset1 added to the pool
  amount1?: string
  // Only for swaps: Number of tokens of asset0 sold
  asset0In?: string
  // Only for swaps: Number of tokens of asset1 bought
  asset1Out?: string
  // Only for swaps: Number of tokens of asset0 bought
  asset0Out?: string
  // Only for swaps: Number of tokens of asset1 sold
  asset1In?: string
  // Only for joins, exists and swaps: Reserves of each token remaining after the event has been executed
  reserves: {
    // Reserves of token asset0
    asset0: string
    // Reserves of token asset1
    asset1: string
  }
}
```

### #/components/schemas/ResponseOfBlock

```ts
{
  block: {
    // Number of the block
    blockNumber: integer
    // Timestamp (in seconds) the block was confirmed at
    blockTimestamp: integer
  }
}
```

### #/components/schemas/ResponseOfAsset

```ts
{
  asset: {
    // Address of the token
    id: string
    // Name of the token
    name: string
    // Symbol of the token
    symbol: string
    // Total supply of the token at current time
    totalSupply: string
    // Circulating supply of the token at current time
    circulatingSupply: string
  }
}
```

### #/components/schemas/ResponseOfPair

```ts
{
  pair: {
    // Address of the pair
    id: string
    // Address of the first token of the pair
    asset0Id: string
    // Address of the second token of the pair
    asset1Id: string
    // Number of block the pair was created at
    createdAtBlockNumber: integer
    // Timestamp (in seconds) of the block the pair was created at
    createdAtBlockTimestamp: integer
    // Hash of the transaction the pair was created at
    createdAtTxnId: string
    // Address of the smart contract used to create the pair
    factoryAddress: string
  }
}
```

### #/components/schemas/ResponseOfEvents

```ts
{
  events: {
    block: {
      // Number of the block
      blockNumber: integer
      // Timestamp (in seconds) the block was confirmed at
      blockTimestamp: integer
    }
    // Hash of the transaction the event belongs to
    txnId: string
    // Index of the transaction the event belongs to
    txnIndex: integer
    // Index of the event inside the transaction
    eventIndex: integer
    // Address of the wallet who request the transaction
    maker: string
    // Address of the pair involved in the transaction
    pairId: string
    // Type of event (creation -> Pair created; swap -> Swap; join -> Add liquidity; exit -> Remove liquidity)
    eventType: enum[creation, swap, join, exit]
    // Only for joins and exits: Number of tokens of asset0 added to the pool
    amount0?: string
    // Only for joins and exits: Number of tokens of asset1 added to the pool
    amount1?: string
    // Only for swaps: Number of tokens of asset0 sold
    asset0In?: string
    // Only for swaps: Number of tokens of asset1 bought
    asset1Out?: string
    // Only for swaps: Number of tokens of asset0 bought
    asset0Out?: string
    // Only for swaps: Number of tokens of asset1 sold
    asset1In?: string
    // Only for joins, exists and swaps: Reserves of each token remaining after the event has been executed
    reserves: {
      // Reserves of token asset0
      asset0: string
      // Reserves of token asset1
      asset1: string
    }
  }[]
}
```

### #/components/schemas/Issue

```ts
{
  code?: string
  param?: string
  message: string
}
```

### #/components/schemas/ResponseOfError

```ts
{
  code: string
  message: string
  issues: {
    code?: string
    param?: string
    message: string
  }[]
}
```

### #/components/responses/ErrorNotFound

- application/json

```ts
{
  code: string
  message: string
  issues: {
    code?: string
    param?: string
    message: string
  }[]
}
```

### #/components/responses/ErrorTooManyRequests

- application/json

```ts
{
  code: string
  message: string
  issues: {
    code?: string
    param?: string
    message: string
  }[]
}
```

### #/components/responses/ErrorInternal

- application/json

```ts
{
  code: string
  message: string
  issues: {
    code?: string
    param?: string
    message: string
  }[]
}
```