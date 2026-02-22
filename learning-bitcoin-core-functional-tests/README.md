# 🌵 Learning Bitcoin Core, One Exercise at a Time

This workshop helps you learn how Bitcoin Core functional tests are structured, how to run them, and how to write new tests.

## Before You Start

This repository contains workshop materials only, and coding should happen in [`b4os-bitcoin`](https://github.com/danielabrozzoni/b4os-bitcoin).
For setup, cloning, and build instructions, see [`../SETUP.md`](../SETUP.md).

## How to Approach the Exercises

The exercises are grouped by difficulty and learning goal:

- Easy exercises: introduce core functional test framework concepts.
- Medium exercises: combine multiple concepts in more realistic scenarios.
- Hard exercises: advanced scenarios that require deeper protocol understanding.

You can complete exercises in any order you prefer, but the recommended path is:

1. Do the easy exercises in order and complete all of them.
2. Read through the medium and hard exercises, and work on the ones that interest you most :)

Important to remember: for each exercise, there are many valid solutions! Don’t stress too much about finding one specific path. As long as you capture the main idea, you are doing great. The goal here is to experiment with the framework and learn how it works :)

Each exercise includes hints in case you get stuck. The easy exercises contain the highest number of hints, while the medium and hard ones contain fewer, or none at all. Use the hints to tune the difficulty to your expertise, and no problem at all if you end up reading all of them! Also, if hints are not enough, you can always ask someone in real life for help :)

## How to Submit Exercise Work

Submit all exercise work in your fork of [`b4os-bitcoin`](https://github.com/danielabrozzoni/b4os-bitcoin).

1. Fork `b4os-bitcoin` to your GitHub account, as explained in [`../SETUP.md`](../SETUP.md).
2. Create a branch for your workshop work (for example: `workshop/functional-tests`).
3. Commit your changes incrementally.
4. Open pull request(s) from your branch/fork.

Submission format:

- You can open one PR containing multiple exercise commits, or multiple PRs.
- In both cases, keep a strict rule: **each commit should contain work for only one exercise**. This makes review easier and keeps your history clean and easy to reason about.

Some guidance on structuring commits and PR descriptions is available in [`CONTRIBUTING.md`](https://github.com/danielabrozzoni/b4os-bitcoin/blob/master/CONTRIBUTING.md).

## Exercise List

### Easy

- [`exercises/00-easy-running-tests.md`](./exercises/00-easy-running-tests.md): run tests, read `example_test.py`, and create your first tiny test.
- [`exercises/01-easy-wallet-rpc-basics.md`](./exercises/01-easy-wallet-rpc-basics.md): wallet setup, send/confirm flow, and balance assertions.
- [`exercises/02-easy-node-args-debug-log.md`](./exercises/02-easy-node-args-debug-log.md): validate startup args with debug logs and RPC state.
- [`exercises/03-easy-miniwallet-support.md`](./exercises/03-easy-miniwallet-support.md): use MiniWallet as fast test support tooling.
- [`exercises/04-easy-p2p-ping-pong.md`](./exercises/04-easy-p2p-ping-pong.md): P2P basics with `ping`/`pong`, `wait_until`, and `p2p_lock`.

### Medium

- [`exercises/05-medium-invalid-tx.md`](./exercises/05-medium-invalid-tx.md): submit invalid transactions over P2P and verify rejection behavior.
- [`exercises/06-medium-network-split-rejoin-reorg.md`](./exercises/06-medium-network-split-rejoin-reorg.md): create a network split/rejoin scenario and verify reorg behavior.

### Hard

- [`exercises/07-hard-compact-block-missing-tx-fallback.md`](./exercises/07-hard-compact-block-missing-tx-fallback.md): test compact-block reconstruction.
- [`exercises/08-hard-cve-2018-17144-exploit.md`](./exercises/08-hard-cve-2018-17144-exploit.md): simulate and test CVE-2018-17144 duplicate-input behavior.
