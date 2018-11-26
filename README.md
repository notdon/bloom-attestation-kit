# Overview
## Introduction to the Bloom Attestation Kit

**The Bloom Attestation Kit** is a [Docker Compose](https://docs.docker.com/compose/) system, a set of [Docker](https://www.docker.com/what-docker) images, that allows you to deploy a service for your application to interact with the Bloom ecosystem, as both an attester and a requester.

The Bloom Attestation Kit system is essentially split into two actors, an attester and a requester. The requester accepts authorized API requests that begin a [Whisper](https://github.com/ethereum/wiki/wiki/Whisper) negotiation over the Ethereum network, in which attesters can submit bids for their price to verify the information the requester specifies. The requester, after receiving a bid it deems satisfactory, submits a request to a specified web application, providing it with the information required for the subject of the attestation to submit a signature for the attestation request. After that, the requester submits the necessary information to the attester, who can then proceed to commit the attestation into the Bloom contracts on the Ethereum blockchain, receiving payment and completing the process.

A basic installation of the Bloom Attestation Kit is as simple as pulling a copy of the Git repository, configuring its environment variables, hooking it up to your web application, and starting it up.

```
$ git pull git@github.com:hellobloom/attestation-kit.git
$ cd attestation-kit
```

# Environment Variables
## Configuring the Bloom Attestation Kit

Most of the installation of the Attestation Kit consists of setting environment variables. These variables should be set in the .env file at the top level of the Attestation Kit repository (not to be confused with the file at ```/app/.env.sample```, which you should generally never have to edit).

| Variable      | Role |  Required | Description |
| ----------- | ----------- | -----------| ----------- |
| API_KEY_SHA256 | all      | yes         | SHA256 hash of API key required to authenticate requests. When generating a key, make sure to use a long (>20 characters) random string.|
| NEWRELIC_KEY | all | no | Key used to send events and debugging information to NewRelic.|
| SENTRY_DSN | all | no | URL used to send events and debugging information to Sentry.|
| NODE_ENV | all | yes | Should almost always be set to "production".|
| WEB3_PROVIDER | all | yes | A URL, either HTTPS or a local websocket/RPC connection, for an Ethereum RPC provider. Most people will probably want to set up an account with Infura - more advanced users may prefer to set up a secured Geth node for this purpose.|
| RINKEBY_WEB3_PROVIDER| all | yes | Same as above, except for the Rinkeby Ethereum test network instead of the main Ethereum network.|
| WHISPER_PROVIDER | all | yes | RPC for a **private** geth node, i.e., not accessible by anyone who shouldn't have access to your secured information. Typically, you'll want to simply set this to ```ws://attestation-kit_gethworker_1:8546```, the default address and port for the geth node contained within the Attestation Kit Compose environment. |
| PRIMARY_ETH_PRIVKEY | all | yes | 	An Ethereum private key. |
| PRIMARY_ETH_ADDRESS | all | yes | The Ethereum address derived from the above private key. |
| WHISPER_PASSWORD | all | yes | The password used for decryption of public solicitations on Whisper. By default, set this to `productionBloom`.|
| BLOOM_WHISPER_PG_URL	| all | yes | URL of PostgreSQL database to be used through the Attestation Kit. By default, there's a PostgreSQL database running within the Attestation Kit- for which the value should be set to ```postgres://bloomwhisper:bloomwhisperpw@pgdb/bloom-whisper``` - but you can also set this to the URL of an external database. |
| ATTESTER_MIN_REWARDS | Attester | yes | 	A JSON object mapping types of attestations to minimum acceptable rewards, as measured in BLT. Example: ```{"phone":"0.1", "sanction":"0.1"}```. Currently, acceptable keys are "phone", "email", "facebook", "sanction", and "pep-screen". For a node that should only operate as a requester, supply an empty object: ```{}```.|
| WEBHOOK_HOST | all | yes | HTTPS address for your application - this will be the base URL for any webhooks hosted by your application.|
| WEBHOOK_KEY | all | yes | Shared secret key (string) for authenticating your copy of the Attestation Kit to your application. Key will be provided to your application as a header ```api_token```, which should authenticate it against a securely hashed (or hashed and salted) copy stored in a place accessible to your application.|
| APPROVED_ATTESTERS | Attester | no | JSON object mapping attestation types (or string ```"all"```) to array of Ethereum addresses of attesters. Example: ```{"all": ["0x19859151..."], "email": ["0x54321..."]}```. A universal whitelist can also be applied by assigning any value to the property ```"any"```: ```{"any":"x"}```.| 
| APPROVED_REQUESTERS | Attester | no | JSON object mapping attestation types (or string ```"all"```) to array of Ethereum addresses of requesters. Example: ```{"all": ["0x19859151..."], "email": ["0x54321..."]}```. A universal whitelist can also be applied by assigning any value to the property ```"any"```: ```{"any":"x"}```.|

# Attester API
## Attestation Kit attester API endpoints

### GET /api/attestations
Lists all attester attestations (where "role" is equal to "attester"). Optional scoping via "where" parameter.

### Parameters
| Name | Type | Required |Description |
| ----------- | ----------- | -----------| ----------- |
| where | object | no | 	An object describing parameters to match for attestations. Example values: ```{"id": "10f7a585-6aa8-4efb-b621-a3de956c2459"}```, ```{"type":"phone"}```, ```{"requester":"0xafbe892398bfbabcebfa92185918325abc19a"}```|
| per_page | integer | no | Number of results to show per page (default value is 100)|
| page | integer | no | What page to display (default value is 0) |
