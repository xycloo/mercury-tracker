<img src="https://github.com/xycloo/mercury-scf/assets/70587974/a60f1906-643d-49f4-92e7-7f4d605164cc" alt="mercury-logo" width="100"/>

# ALPHA Release

### Requesting access

To request access, you can either reach out at [tommaso@xycloo.com](mailto:tommaso@xycloo.com), or even better reach out on Discord through a private message to `heytdep` or by requesting access keys on the stellar developer's discord project page (or on our discord server). Make sure to let us know:
- your discord username (or email) to receive the access token.
- you can also provide a username and email (both optional, if not specified will be random).

For alpha users Mercury is entirely free (infrastructure is maintained by the funding), and we highly appreciate user feedback, so don't hesitate to request access.

### Authenticating
We should have provided you with a JWT token already, but if you want to authenticate run the following mutation on the graphql endpoint providing the mail and password you were given:

```graphql
mutation MyMutation {
  authenticate(input: {email: "user@mercury-tester.com", password: "yourpassword"}) {
    clientMutationId
    jwtToken
  }
}
```

### Subscribing to contract events

The alpha version only supports contract events (given they are simple and effective in an indexer). To create a new subscription, refer to the following API:

**endpoint**: http://ec2-51-20-3-70.eu-north-1.compute.amazonaws.com:3030/newsubscription
**method**: POST
**content-type**: application/json
**auth**: "Authorization: Bearer YOUR_JWT_ACCESS_TOKEN"
**body**:

```json
{"contract_id": "CONTRACT_ID", "topic1": "XDR", "topic2": "XDR", "topic3": "XDR", "topic4": "XDR"}
```

As you can see, you can choose to be as specific as you want with the events you subscribe to. You can leave fields (contract_id, topic1, topic2, topic3, topic4) null to have the field match with any value, for example, setting the contract id to null and the first topic to transfer matches with all transfer events from all contracts. 

**example**
This example subscribes to all events of xycLoans' XLM pool:

```curl
 curl --header "Content-Type: application/json" \
  --request POST \
  --data '{"contract_id":"CCVP5K2R2X4RWSJB7WZDDYVWHWDUUZWBWHLFPSZBIDOAWXH3LX6GG5PU", "topic1":"AAAADwAAAAdYWUNMT0FOAA=="}' -H "Authorization: Bearer YOUR_JWT_ACCESS_TOKEN" \
  http://ec2-51-20-3-70.eu-north-1.compute.amazonaws.com:3030/newsubscription
```

### Querying the graphQL API

We expose a graphiql endpoint in from our ec2 instance: `http://ec2-51-20-3-70.eu-north-1.compute.amazonaws.com:5000/graphiql`.
From there you can play with querying contract events, for example:

```graphql
query MyQuery {
  allContractEvents {
    edges {
      node {
        contractId
        data
        ledgerTimestamp
        topic1
        topic2
        topic3
        topic4
      }
    }
  }
}
```

Result:

```json
{
  "data": {
    "allContractEvents": {
      "edges": [
        {
          "node": {
            "contractId": "CCVP5K2R2X4RWSJB7WZDDYVWHWDUUZWBWHLFPSZBIDOAWXH3LX6GG5PU",
            "data": "AAAAEAAAAAEAAAACAAAAEgAAAAAAAAAAogTcVVMuvPi5pHpK74IlI5Aliz10ZZ6ek+xmLfzrasgAAAAKAAAAAAAAAAAAAAAAAAMNQA==",
            "ledgerTimestamp": 668464,
            "topic1": "AAAADwAAAAdYWUNMT0FOAA==",
            "topic2": "AAAADwAAAAdkZXBvc2l0AA==",
            "topic3": null,
            "topic4": null
          }
        }
      ]
    }
  }
}
```

### Step-by-step simple example

To just test out Mercury alpha, you can follow this step-by-step guide where you just need your JWT access token (see [here](#requesting-access)) and a funded Stellar account on Futurenet (you can use the [friendbot](https://laboratory.stellar.org/#account-creator?network=futurenet)). 

1. Add event subscription to xycLoans XLM pool events (all events):

```curl
curl --header "Content-Type: application/json" \
  --request POST \
  --data '{"contract_id":"CD4CCGHEUIGU3BTXZRCQ24LJ547TWLNTMJ7BHDBO6DJPHUTBQBLCDECA"}' -H "Authorization: Bearer YOUR_JWT_TOKER" \
  http://ec2-51-20-3-70.eu-north-1.compute.amazonaws.com:3030/newsubscription
```

Where:
- `CD4CCGHEUIGU3BTXZRCQ24LJ547TWLNTMJ7BHDBO6DJPHUTBQBLCDECA` is the pool contract.
- `YOUR_JWT_TOKEN` is the jwt token you obtained

Note, we don't provide any topics since we want to subscribe to all of the contract's topics.

A successful response should just return `true`.


2. Invoke a deposit on the xycloan XLM pool:

```
soroban contract invoke --id CD4CCGHEUIGU3BTXZRCQ24LJ547TWLNTMJ7BHDBO6DJPHUTBQBLCDECA --source-account $YOUR_SECRET --rpc-url https://rpc-futurenet.stellar.org:443 --network-passphrase 'Test SDF Future Network ; October 2022' -- deposit --from GCRAJXCVKMXLZ6FZUR5EV34CEURZAJMLHV2GLHU6SPWGMLP45NVMQIXB --amount 200000
```

Note: remember to paste your funded account's secret.

You can also try using some more of the functions, for instance updating fee rewards:

```
soroban contract invoke --id CD4CCGHEUIGU3BTXZRCQ24LJ547TWLNTMJ7BHDBO6DJPHUTBQBLCDECA --source-account $YOUR_SECRET --rpc-url https://rpc-futurenet.stellar.org:443 --network-passphrase 'Test SDF Future Network ; October 2022' -- update_fee_rewards --addr $YOUR_PUBLIC_KEY
```

3. You can now access the Graphiql endpoint (or send the queries to the `/graphql` endpoint):

> Note: remember to add the jwt token, `{"Authorization": "Bearer YOUR_JWT_TOKEN"}` within "request headers".

```graphql
query MyQuery {
  allContractEvents {
    edges {
      node {
        id
        ledgerTimestamp
        contractId
        data
        topic1
        topic2
        topic3
        topic4
      }
    }
  }
}
```

Your response might look like:

```json
{
  "data": {
    "allContractEvents": {
      "edges": [
        {
          "node": {
            "id": 5,
            "ledgerTimestamp": 721536,
            "contractId": "CD4CCGHEUIGU3BTXZRCQ24LJ547TWLNTMJ7BHDBO6DJPHUTBQBLCDECA",
            "data": "AAAACgAAAAAAAAAAAAAAAAADDUA=",
            "topic1": "AAAADwAAAAdkZXBvc2l0AA==",
            "topic2": "AAAAEgAAAAAAAAAAogTcVVMuvPi5pHpK74IlI5Aliz10ZZ6ek+xmLfzrasg=",
            "topic3": null,
            "topic4": null
          }
        },
        {
          "node": {
            "id": 6,
            "ledgerTimestamp": 721536,
            "contractId": "CD4CCGHEUIGU3BTXZRCQ24LJ547TWLNTMJ7BHDBO6DJPHUTBQBLCDECA",
            "data": "AAAACgAAAAAAAAAAAAAAAAADDUA=",
            "topic1": "AAAADwAAAAdkZXBvc2l0AA==",
            "topic2": "AAAAEgAAAAAAAAAAogTcVVMuvPi5pHpK74IlI5Aliz10ZZ6ek+xmLfzrasg=",
            "topic3": null,
            "topic4": null
          }
        },
        {
          "node": {
            "id": 7,
            "ledgerTimestamp": 723003,
            "contractId": "CD4CCGHEUIGU3BTXZRCQ24LJ547TWLNTMJ7BHDBO6DJPHUTBQBLCDECA",
            "data": "AAAACgAAAAAAAAAAAAAAAAADDUA=",
            "topic1": "AAAADwAAAAdkZXBvc2l0AA==",
            "topic2": "AAAAEgAAAAAAAAAAogTcVVMuvPi5pHpK74IlI5Aliz10ZZ6ek+xmLfzrasg=",
            "topic3": null,
            "topic4": null
          }
        },
        {
          "node": {
            "id": 8,
            "ledgerTimestamp": 723016,
            "contractId": "CD4CCGHEUIGU3BTXZRCQ24LJ547TWLNTMJ7BHDBO6DJPHUTBQBLCDECA",
            "data": "AAAACgAAAAAAAAAAAAAAAAADDUA=",
            "topic1": "AAAADwAAAAdkZXBvc2l0AA==",
            "topic2": "AAAAEgAAAAAAAAAAogTcVVMuvPi5pHpK74IlI5Aliz10ZZ6ek+xmLfzrasg=",
            "topic3": null,
            "topic4": null
          }
        },
        {
          "node": {
            "id": 9,
            "ledgerTimestamp": 723025,
            "contractId": "CD4CCGHEUIGU3BTXZRCQ24LJ547TWLNTMJ7BHDBO6DJPHUTBQBLCDECA",
            "data": "AAAACgAAAAAAAAAAAAAAAAAAAAA=",
            "topic1": "AAAADwAAAAl1cGRhdGVmZWUAAAA=",
            "topic2": "AAAAEgAAAAAAAAAAogTcVVMuvPi5pHpK74IlI5Aliz10ZZ6ek+xmLfzrasg=",
            "topic3": null,
            "topic4": null
          }
        }
      ]
    }
  }
}
```
