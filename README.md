<img src="https://github.com/xycloo/mercury-tracker/assets/70587974/595800a1-0fbe-466d-8feb-475889be6ee5" alt="mercury-logo" width="100" />

# Mercury

> This repo is for tracking Mercury's progrss, SCF grant application and deliverables.

Mercury is an innovative centralized data indexer focused on Soroban that aims to maximize cost efficiency and provide valuable data-reliant tools that operate with first-class data availability. 

Check out the submission document at [https://docs.google.com/document/d/16rgq5mopskhU4CFE69VAi--IsCxNDeNjBlb-buidl08/edit?usp=sharing](https://docs.google.com/document/d/1BER6CtoqMZ6iSk3s18_782bJx2npkBHcLMQio8MRl0A/edit?usp=sharing).


## Pre-release candidate for Beta version

The pre-release version for our first beta release is now live!

As of now, there haven't been significant changes on the user's side due to the lack of the backend's
supported subscriptions, but on this release we started supporting expiration, ledger entries and stellar classic
operations (currently only payments and create accounts are fully implemented on all of Mercury's components). 

For projects we're already working with that have full account subscriptions access, you can start checking out the
developers docs: https://developers.mercurydata.app/

## Mercury [ALPHA](./ALPHA.md) Release

The alpha release of Mercury is ready! The alpha release consists of the most barebones implementation of Mercury: the interoperation among the ingestor, the PostgreSQL database, and the backend.

All these three components are experimental in the alpha release and still need to be extended, optimized, and thoroughly tested. This is just the starting point.

More info about the ALPHA release, how request access, and how to use it can be found on [`ALPHA.md`](./ALPHA.md). 
