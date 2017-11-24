# Transaction Signing

This article describes how we sign transactions in Cardano SL.

## Input and Witness

Transaction in Cardano SL includes 3 parts:

1. list of inputs,
2. list of outputs,
3. attributes.

But since currently attributes are always empty, we can think of transaction as
a pair of non-empty lists: list of inputs and list of outputs.

There is important concept: **witness**. A witness is a proof that a transaction
is allowed to spend the funds it spends. A separate witness contains a signature
and is provided for each input of transaction.

As a result we have a pair `(Tx, [TxInputWitness])`, where:

* `Tx` is a new transaction,
* `TxInputWitness` is a witness for particular input of `Tx`.

## Witness Structure

Witness is a pair `(PublicKey, Signature (Hash Tx))`, where:

* `Tx` is a new transaction,
* `Hash Tx` is a BLAKE2b-256 hash of `Tx`,
* `PublicKey` is a public key of the creator of `Tx`,
* `Signature` is a signature of the hash of `Tx` (Ed25519-based signature system is used).
  Secret key corresponding to `PublicKey` is using for signing.

## Example

This is real-life example of transaction signing, with **arbitrary transaction and secret key**.

### Transaction

This is transaction in different format.

#### Binary Format (HEX)

```
839f8200d8185826825820413abbacf861d26dfa15d3f090d4f237887047231955b7c6f475a539462fab45197e2dff9f8282d818583a83581c9053f732bf9aa513043562767ddcb76b8843d79d9f3aa3fdaf2ef2c9a101575610faa0268e2d55cc533ba2f29d5862607de41d8c783c001ab9417a371b0022e75c718b01aeffa0
```

#### Haskell-based Format

```
UnsafeTx {
    _txInputs = TxInUtxo {
        txInHash = AbstractHash 413abbacf861d26dfa15d3f090d4f237887047231955b7c6f475a539462fab45,
        txInIndex = 32301
    } :| [],
    _txOutputs = TxOut {
        txOutAddress = Address {
            addrRoot = AbstractHash 9053f732bf9aa513043562767ddcb76b8843d79d9f3aa3fdaf2ef2c9,
            addrAttributes = Attributes {
                data: AddrAttributes {
                    aaPkDerivationPath = Just (HDAddressPayload {
                        getHDAddressPayload = "\DLE\250\160&\142-U\204S;\162\242\157Xb`}\228\GS\140x<"
                    }),
                    aaStakeDistribution = BootstrapEraDistr
                }
            },
            addrType = ATPubKey
        },
        txOutValue = Coin {getCoin = 9824533436105134}
    } :| [],
    _txAttributes = Attributes { data: () }
}
```

#### Human-readable Format

```
Tx 6a7fbfdb with
    inputs [
        TxInUtxo 413abbac #32301
    ],
    outputs: [
        TxOut 9824533436105134 coin(s) -> J7rQqaLLHBFeogs1afvEzaeKBqiMcRGGGTN3saavWAw9RZGxQfGYruqNUuJ7w6ufQpbhNDkfPCyarqcY4tPUJTXnivcHt
    ]
```

### Secret Key

This is a secret key we used to sign that transaction, HEX-format:

```
5880d88854afb6a00489ad6d7d73755c857e360f2187d4b122e958fce8f8b3f991472384113c6e4d203a5a099201e5080c40eb8992b90043c5ced0153fde21efdd516f7506e0fe94e5d901414e28a77ee05a62420240a5ca8e48c32fa5371e8972bcdccaf868303c264f4a416ac3e49ca6cb4b2938d9318bd61cdf6b8c0067445d20
```

Short-form:

```
sec:6f7506e0
```

### Public Key

This is a public key corresponding to secret key, HEX-format:

```
58406f7506e0fe94e5d901414e28a77ee05a62420240a5ca8e48c32fa5371e8972bcdccaf868303c264f4a416ac3e49ca6cb4b2938d9318bd61cdf6b8c0067445d20
```

Short-form:

```
pub:6f7506e0
```

### Signature

This is a signature of that transaction, HEX-format:

```
5840d39de47b9de92a985882a4af19ece407c8f0f4fd9c697004b5c0d2489f38d489db35e1d36364306e70175ed7d9099270753c00e480df12d57182458c2196ce0a
```
