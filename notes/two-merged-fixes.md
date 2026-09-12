# Two merged bug fixes: subscriptions and zero pivots

By [Deepshekhar Das](https://github.com/deepshekhardas). Published September 12, 2026.

These notes explain two of my contributions, both merged on September 10. The PRs contain the source changes and review history.

## CopilotKit: separate the agent handle from render updates

An interrupt hook needs access to an agent, but that does not mean it needs to render on every agent message or state change.

In `useInterrupt`, interrupt events already arrived through a direct subscription. The call to `useAgent` also subscribed the consumer to updates, so unrelated changes could cause additional renders.

The fix passed `updates: []` when retrieving the agent handle. The hook kept its interrupt subscription while opting out of the broader render updates. The PR also added a regression test checking that this opt-out was passed to `useAgent`.

The useful debugging question: which event stream actually needs to update this component? Follow each subscription before reaching for memoization.

This was a targeted change to subscription behavior. No numerical speedup is claimed here.

[Merged change and test: CopilotKit #6969](https://github.com/CopilotKit/CopilotKit/pull/6969)

## TheAlgorithms/Python: a valid input can expose a zero pivot

The polynomial-fitting implementation used elimination on a matrix built from the input points. A zero diagonal entry could cause division by zero even when the points described a valid polynomial.

The added example uses the points `(0, 1)`, `(1, 2)`, and `(2, 5)`, which lie on `y = x² + 1`.

The fix checks for a zero pivot and swaps in a lower row with a nonzero entry when one is available. It also swaps the corresponding right-hand-side values, preserving the equations. The new doctest covers that input.

The useful testing lesson: include boundary inputs such as zero alongside typical inputs. This change addresses the reported zero-pivot case; it is not a general claim about numerical stability.

[Merged change and doctest: TheAlgorithms/Python #15044](https://github.com/TheAlgorithms/Python/pull/15044)

---

For more practical fixes and project updates, [visit my GitHub profile](https://github.com/deepshekhardas) or [connect on LinkedIn](https://www.linkedin.com/in/deepshekhardas/).
