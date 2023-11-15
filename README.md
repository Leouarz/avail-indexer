# SubQuery - Example Project for Polkadot

[SubQuery](https://subquery.network) is a fast, flexible, and reliable open-source data indexer that provides you with custom APIs for your web3 project across all of our supported networks. To learn about how to get started with SubQuery, [visit our docs](https://academy.subquery.network).

**This SubQuery project indexes all asset transfers using the balances pallet on the Polkadot Network**

## Tl;Dr
```bash
npm install -g @subql/cli
npm install
npm run codegen
npm run build
docker-compose pull
docker-compose up --remove-orphans
```

Or you can use this one-liner which will install dependencies and setup the the indexer after taking user inputs for websocket endpoint and genesis hash.
```bash
curl -s https://raw.githubusercontent.com/availproject/avail-indexer/blob/main/setup_indexer.sh -o setup_indexer.sh && bash setup_indexer.sh
``` 

## Start

First, install SubQuery CLI globally on your terminal by using NPM `npm install -g @subql/cli`

You can either clone this GitHub repo, or use the `subql` CLI to bootstrap a clean project in the network of your choosing by running `subql init` and following the prompts.

Don't forget to install dependencies with `npm install` or `yarn install`!

## Editing your SubQuery project

Although this is a working example SubQuery project, you can edit the SubQuery project by changing the following files:

- The project manifest in `project.yaml` defines the key project configuration and mapping handler filters
- The GraphQL Schema (`schema.graphql`) defines the shape of the resulting data that you are using SubQuery to index
- The Mapping functions in `src/mappings/` directory are typescript functions that handle transformation logic

SubQuery supports various layer-1 blockchain networks and provides [dedicated quick start guides](https://academy.subquery.network/quickstart/quickstart.html) as well as [detailed technical documentation](https://academy.subquery.network/build/introduction.html) for each of them.

For `Kate Testnet`:
```yaml
 genesisHash: '0xd12003ac837853b062aaccca5ce87ac4838c48447e41db4a3dcfb5bf312350c6'
 endpoint: 'wss://kate.avail.tools/ws'
```
For `Biryani Devnet`:
```yaml
 genesisHash: '0x25b7a5d31af1def763db74809b08794c4eb2121c531f8fd8458555be487bebda'
 endpoint: 'wss://biryani-devnet.avail.tools/ws'
```

## Run your project

_If you get stuck, find out how to get help below._

The simplest way to run your project is by running `yarn dev` or `npm run-script dev`. This does all of the following:

1. `yarn codegen` - Generates types from the GraphQL schema definition and contract ABIs and saves them in the `/src/types` directory. This must be done after each change to the `schema.graphql` file or the contract ABIs
2. `yarn build` - Builds and packages the SubQuery project into the `/dist` directory
3. `docker-compose pull && docker-compose up` - Runs a Docker container with an indexer, PostgeSQL DB, and a query service. This requires [Docker to be installed](https://docs.docker.com/engine/install) and running locally. The configuration for this container is set from your `docker-compose.yml`

You can observe the three services start, and once all are running (it may take a few minutes on your first start), please open your browser and head to [http://localhost:3000](http://localhost:3000) - you should see a GraphQL playground showing with the schemas ready to query. [Read the docs for more information](https://academy.subquery.network/run_publish/run.html) or [explore the possible service configuration for running SubQuery](https://academy.subquery.network/run_publish/references.html).

## Query your project

For this project, you can try to query with the following GraphQL code to get a taste of how it works.

```graphql
# Query blocks (events, logs, ... can be called separately)
{
  blocks(first: 10, orderBy: TIMESTAMP_DESC) {
    nodes {
      number
      hash
      parentHash
      stateRoot
      timestamp
      runtimeVersion
      extrinsics {
        nodes {
          module
          call
        }
      }
      events {
        nodes {
          module
          call
        }
      }
      logs {
        nodes {
          type
          engine
          data
        }
      }
      headerExtensions {
        nodes {
          version
          commitments {
            nodes {
              rows
              cols
              dataRoot
              commitment
            }
          }
        }
      }
    }
  }
}
# Query session and validators
{
  sessions(
    filter: {
      id: {equalTo: "2907"}
    }
    first: 10
    orderBy: ID_DESC
  ) {
    totalCount
    nodes {
      id
      validators
    }
  }
}
# Query accounts
{
  accountEntities(
    filter: { id: { in: ["5CwCDQKRyPnrSHcmxbS9cEDGb4YQxaVrbQmw9RH5yBR9Xnh5"] } }
  ) {
    nodes {
      id
      amount
      amountTotal
      amountRounded
      amountTotalRounded
    }
  }
}
```

You can explore the different possible queries and entities to help you with GraphQL using the documentation drawer on the right (schema and docs).


## Get help from chat GPT (Change the query between "")
Giving this example requests : 

```graphql
# Copy paste the examples from "Query your project" section
```

And this schema : 
```graphql
# Copy the content of schema.graphql here
```
And the fact that foreign key such as "Block" becomes blockId in a query

Can you generate me a query to : 

"query the first 10 extrinsics where the call is "submitData" ordered by blockId desc ?" 

## When changing the service file
- Put the file in `/etc/systemd/system/`
- `sudo systemctl daemon-reload`
- `sudo systemctl enable start_indexer.service`
- `sudo systemctl start start_indexer.service`
