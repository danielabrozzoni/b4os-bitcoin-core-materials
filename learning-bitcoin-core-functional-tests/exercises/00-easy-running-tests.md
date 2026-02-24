# Easy 00: Running and Reading Functional Tests

Welcome! This first "exercise" is more of a guided tour :)

## Concepts You Need

Functional tests are Python integration tests that run real `bitcoind` nodes in `regtest`, perform actions through RPC/P2P, and check that node behavior matches expectations.

In this workshop, you will write several functional tests. These exercises are for practice, so they are not intended to be contributed directly to Bitcoin Core, but the workflow and framework skills are exactly the same.

Two good starting points for the functional test framework are:
- [`test/functional/README.md`](https://github.com/danielabrozzoni/b4os-bitcoin/blob/master/test/functional/README.md) answers many common "how do I run/write tests?" questions.
- [`test/functional/example_test.py`](https://github.com/danielabrozzoni/b4os-bitcoin/blob/master/test/functional/example_test.py) is a complete, heavily commented example that mixes RPC and P2P testing patterns.

We will explore [`example_test.py`](https://github.com/danielabrozzoni/b4os-bitcoin/blob/master/test/functional/example_test.py) together in this lesson, so start by quickly skimming [`README.md`](https://github.com/danielabrozzoni/b4os-bitcoin/blob/master/test/functional/README.md). You do not need to read everything now; just look at the outline so you know where to come back later if needed.

## What You Will Build

In this guided exercise, you will run tests, inspect one complete example test, and create one tiny hello-world style test.

## 0. Make sure we have everything we need

Before continuing, complete [`SETUP.md`](../../SETUP.md).
This exercise assumes setup and build are already done.

From now on, we assume the build directory is `build/`. If you used something different, adjust commands accordingly.

## 1. Run Tests First

By default, `test_runner.py` runs the default functional test suite:

```bash
build/test/functional/test_runner.py
```

Double-check that everything is working correctly on your system by starting the test runner. You do not need to wait for completion; this is just a quick sanity check.

When working on your own tests, you will often run only the one you are currently editing. You can do that in two ways - either by running the test directly:

```bash
build/test/functional/example_test.py
```

Or running it through the test runner:

```bash
build/test/functional/test_runner.py example_test.py
```

The test runner has many options. You can inspect them with `build/test/functional/test_runner.py -h`.

## 2. Read One Full Example Test

`example_test.py` shows the canonical structure of a functional test. Read it end-to-end once, then use this section as a map. There is a lot in `run_test()`, and it is fine if you do not understand every detail yet.

```python
class ExampleTest(BitcoinTestFramework):
    def set_test_params(self):
        self.setup_clean_chain = True
        self.num_nodes = 3

    def run_test(self):
        ...

if __name__ == '__main__':
    ExampleTest(__file__).main()
```

Your test class must subclass `BitcoinTestFramework`, the base class that handles argument parsing, temporary directories, node startup, logging, and cleanup.

When subclassing `BitcoinTestFramework`, you must override two methods: `set_test_params()` and `run_test()`:
- `set_test_params()` defines the test environment before nodes start (for example `num_nodes`, `setup_clean_chain`, and node arguments).
- `run_test()` contains the actual test logic: create scenario, call RPC/P2P actions, and assert outcomes.

The framework runs a full lifecycle for you: parse args, set up temp directories, start nodes, execute test logic, then clean up.

You control setup behavior mainly through:

- `set_test_params()` for baseline config (`num_nodes`, `setup_clean_chain`, `extra_args`)
- Optional hook overrides, like `setup_network()` when default topology is not enough

Example from `example_test.py` (`set_test_params()`):

```python
def set_test_params(self):
    # By default every test loads a pre-mined chain of 200 blocks from cache.
    # Set setup_clean_chain to True to skip this and start from the Genesis block.
    self.setup_clean_chain = True
    self.num_nodes = 3
    # Use self.extra_args to change command-line arguments for the nodes
    self.extra_args = [[], ["-logips"], []]
```

Example from `example_test.py` (`setup_network()`):

```python
def setup_network(self):
    """Setup the test network topology

    If you override this method, remember to start the nodes, assign
    them to self.nodes, connect them and then sync."""
    self.setup_nodes()
    # In this test, we're not connecting node2 to node0 or node1.
    self.connect_nodes(0, 1)
    self.sync_all(self.nodes[0:2])
```

## 3. Understand the Core Testing Pattern

Most functional tests are:

1. Arrange state
2. Execute actions (RPC or P2P)
3. Assert expected behavior

`self.nodes` is a list of `TestNode` objects. RPC calls are made directly on those objects:

```python
height = self.nodes[0].getblockcount()
self.nodes[1].waitforblockheight(11)
```

In this snippet, `getblockcount()` shows a simple immediate RPC read, while `waitforblockheight(...)` shows a synchronization-style RPC call that waits until chain state reaches a target height.

`example_test.py` also demonstrates P2P usage and assertions with helper utilities. We will revisit these topics in later exercises, so focus on the high-level flow for now.

```python
# Create P2P connections will wait for a verack to make sure the connection is fully up
peer = self.nodes[0].add_p2p_connection(BaseNode())
peer.send_without_ping(msg_block(block))
```

```python
with p2p_lock:
    for count in peer_receiving.block_receive_map.values():
        assert_equal(count, 1)
```

## 4. Create New Tests

Throughout the workshop, you will create new functional tests. The basic workflow is:

1. Create a new file in `test/functional/`.
2. Add the filename to `BASE_SCRIPTS` in [`test/functional/test_runner.py`](https://github.com/danielabrozzoni/b4os-bitcoin/blob/master/test/functional/test_runner.py).

If you forget to add the filename to `test_runner.py`, the test runner will complain :)
It is important to follow Core naming conventions, because file names communicate the test scope:

- `feature_*` for larger node behavior
- `rpc_*` for RPC behavior
- `p2p_*` for peer-to-peer behavior
- `wallet_*`, `mempool_*`, `mining_*`, `interface_*`, etc.

If you forget to follow naming conventions, the test runner will complain too. (Go on, try it if you do not believe me.)

## 5. To finish off

Before moving on, do one tiny hands-on task:

1. In your local `b4os-bitcoin` clone, copy `test/functional/example_test.py` to a new file (for example `test/functional/feature_hello_world.py`). In general, when you start a new test, begin from `example_test.py` and remove the parts and comments you do not need. Keep the structure clean and avoid outdated comments and dead code before submitting. Copying is also a quick way to preserve important parts (the main block, the shebang, and executable permissions) and save debugging time :)
2. Keep the same structure, but simplify `run_test()` so it only logs a message:

```python
def run_test(self):
    self.log.info("Hello Brazil!")
```

3. Add your new test filename to `BASE_SCRIPTS` in [`test/functional/test_runner.py`](https://github.com/danielabrozzoni/b4os-bitcoin/blob/master/test/functional/test_runner.py).
4. Run the configure step of the project again. This is needed to make sure the functional test ends up in the `build/` directory:
```bash
cmake -B build  # or equivalent, if you need custom compile flags
```

If this is your first time, you'll also need to build the project:
```bash
cmake --build build  # -j N (optional): N = number of parallel jobs
```
Make sure you go through the [SETUP.md](../../SETUP.md) — it has all the build instructions you'll need.

For subsequent changes, re-running the configure step alone is sufficient to update the test code.

5. Run the test with:

```bash
build/test/functional/feature_hello_world.py
```

If this runs successfully, your environment and test workflow are ready for the next exercises :)

## Next Up

Continue with [`01-easy-wallet-rpc-basics.md`](./01-easy-wallet-rpc-basics.md).
