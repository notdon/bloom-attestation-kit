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
