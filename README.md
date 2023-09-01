![mercury_banner](https://github.com/xycloo/mercury-scf18/assets/70587974/8885d3a2-2751-4ad9-8453-f7678d103434)

> This repo is for tracking Mercury's SCF grant application and deliverables.

# Submission proposal
Check out the submission document at https://docs.google.com/document/d/16rgq5mopskhU4CFE69VAi--IsCxNDeNjBlb-buidl08/edit?usp=sharing.

## Alpha instructions

The alpha release of Mercury is ready! The alpha release consists of the most barebones implementation of Mercury: the interoperation among the ingestor, the PostgreSQL database, and the backend.

All these three components are experimental in the alpha release and still need to be extended, optimized, and thoroughly tested. This is just the starting point.

### Requesting access

To request access, you can either reach out at [tommaso@xycloo.com](mailto:tommaso@xycloo.com), or even better reach out on Discord through a private message to `heytdep` or by requesting access keys on the stellar developer's discord project page (or on our discord server). Make sure to let us know:
- your discord username (or email) to receive the access token.
- you can also provide a username and email (both optional, if not specified will be random).

For alpha users Mercury is entirely free (infrastructure is maintained by the funding), and we highly appreciate user feedback, so don't hesitate to request access.

### Subscribing to contract events

The alpha version only supports contract events (given they are simple and effective in an indexer). To create a new subscription, refer to the following API:

**endpoint**: http://PUBLIC_DNS:3030/newsubscription
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
  http://PUBLIC_DNS:3030/newsubscription
```

### Querying the graphQL API

We expose a graphiql endpoint in from our ec2 instance: PUBLIC_DNS.
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
