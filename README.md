## Contents:
- [SURIMI protocol files](#surimi-protocol-files)
- [Services List](#services-list)
  - [Fishery Services](#fishery-services)
  - [Ecology Services](#ecology-services)
  - [Market Services](#market-services)
- [Style guide](#style-guide)
  - [Message structure](#message-structure)
  - [Guidelines for services](#guidelines-for-services)

# SURIMI protocol files

Protobuf definitions for inter-model communication within the [Surimi project](https://www.surimi-project.eu/).

# Services List
The SURIMI protocol is divided into several services, each defined in its own `.proto` file. Below is a list of the main services and their purposes:
- Fishery (Contains services related to fisheries, such as catches)
- Ecology (Contains services related to ecological data, such as biomass)
- Market (Contains services related to market data, such as prices and sales)
## Fishery Services
The CatchDispositionSummary contains information about the catch, such as the species, the amount caught, and the fishing gear used.
UpdatecatchDisposition(..) can be called for every Species seperately, even if the entire harvest is discarded.

## Ecology Services

## Market Services

## Style guide

We use [buf](https://github.com/bufbuild/buf) to enforce standards and formatting.

We follow the [`STANDARD`](https://buf.build/docs/lint/rules/#standard) linting rules.

There are [plugins](https://buf.build/docs/cli/editor-integration/) to run the linter from your editor of choice, but you can also simply run `buf lint` in the terminal from the repo's root folder.

To apply autoformatting, simply run `buf format -w`.

We are currently adopting a pragmatic approach regarding the [1-1-1 rule](https://protobuf.dev/best-practices/1-1-1/). We adhere strictly to the "one service per file" bit, but we allow messages to be defined in the same file as their service if they are strictly `*Request`/`*Response` messages. Messages that can be used by multiple services _must_ be defined in their own files.

We also allow a message of one type and a message with a list of messages of that type to exist in the same file.

### Message structure

We try to avoid redundant information in response messages. If a client requests information for a specific date range, for example, we don't include that date range in the response. The client should assume it got the information for the range that it requested.

Regarding dates and information requests, there are two common cases:

* Sometimes, we request information about the current state of the model; a snapshot of an instant in time. In those cases, there is no need for the request message to specify a timestamp. That is the case, for example, when we request the current biomass in a model.
* Other times, we request information about an extended period of time, in which case the request should include  `start_date_time` and `end_date_time` timestamps. That is the case, for example, when we request the sales or the catches and discards for a period of time. As things currently are, that period of time coïncides with the last time step of the simulation, but this does not necessarily need to be the case. We could also, for example, request sales information for the last six months. 

### Guidelines for services
* Services are grouped by their entities. The "Get..." and the "Update..." of an entity (for example Biomass) are described in the same file.
* All Requests and Responses contain the simulation_id, so in an a-synchonous scenario, the response can be matched to the correct simulation.
* The Request and Response objects should be as empty as possible. Generate a Request from a Response should be simple. No unnecessary properties, like 'measurement_unit'.


### Breaking changes
When making breaking changes to the protocol files, the Github action will fail.

When you add the `buf skip breaking` label to the PR, the "Breaking" check is skipped.

See https://buf.build/docs/bsr/ci-cd/github-actions/#skip-breaking-change-detection-using-labels

