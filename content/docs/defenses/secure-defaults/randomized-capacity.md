+++
title = "Randomized Capacity"
description = ""
date = "2026-03-13"
category = "Defense"
menu = "main"
+++

Randomized Capacity is a security mechanism that prevents attackers from exploiting deterministic capacity limits to learn cross-site information.[^1]
Chromium is randomizing the capacity of TCP pools as of version 145 (February 10, 2026).[^2]

## Algorithm

The method works by first evaluating limited system resources (e.g., TCP sockets) for two thresholds:
1. A lower 'soft limit' that, if imposed as a rigid limit, would not impair performance due to scarcity.
2. A higher 'hard limit' that, if imposed as a rigid limit, would not impair performance due to overtaxing.

The method then establishes two states for the resource pool:
* 'Uncapped' - Allocation and release of resources are permitted.
* 'Capped' - Release of resources is permitted, but allocation is denied.

The method also establishes a way for the pool to 'Roll' for state changes based on the current resource usage:
1. If usage is below the 'soft limit', the result is always 'Uncapped'.
2. If the usage is above the 'hard limit', the result is always 'Capped'.
3. If the usage is between the 'soft limit' and the 'hard limit':
  1. The chance of an 'Uncapped' result should converge to 1 as usage approaches the 'soft limit'.
  2. The chance of a 'Capped' result should converge to 1 as usage approaches the 'hard limit'.

Finally, the method imposes a set of rules for a pool:
* On allocation:
  1. If the pool is 'Capped':
    1. Reject/queue the allocation for later.
    2. Return.
  2. If the 'soft limit' has not been reached:
    1. Permit the allocation.
    2. Return.
  3. If the 'hard limit' has been reached:
    1. Reject/queue the allocation for later.
    2. Set the state to 'Capped'.
    3. Return.
  4. 'Roll' to update the pool state.
  5. If 'Capped':
    1. Reject/queue the allocation for later.
    2. Return.
  6. Permit the allocation.
  7. Return.
* On release:
  1. If the pool is 'Uncapped':
    1. Complete the release.
    2. Return.
  2. If the 'soft limit' has not been reached:
    1. Set the state to 'Uncapped'.
    2. Complete the release.
    3. Return.
  3. 'Roll' to update the pool state.
  4. Complete the release.
  5. Return.

The goal of this method is to guarentee a 'soft limit' of resources, while allowing an additional, randomized capacity up to a 'hard limit'.
By randomizing the points at which the pool becomes 'Capped' or 'Uncapped', an attacker cannot depend on any past limits when seeking information on future ones.

{{< hint tip >}}
In Chromium's implementation the state change formula is min(BOUND, NOISE * pow(BASE, CAPACITY)) where BOUND ensures a minimum of 1% chance, NOISE adds extra randomized variance to prevent probabilistic information extraction, BASE is tuned to give around a 10% chance of a state flip at 80% capacity, and CAPACITY is the percentage of the difference between 'soft limit' and 'hard limit'.[^3]
{{< /hint >}}

## References

[^1]: Connection Pool, [link]({{< ref "../../attacks/timing-attacks/connection-pool.md" >}})
[^2]: TCP Socket Pool Limit Randomization, [link](https://chromestatus.com/feature/6496757559197696)
[^3]: Socket Pool Additional Capacity, [link](https://source.chromium.org/chromium/chromium/src/+/main:net/socket/socket_pool_additional_capacity.cc;drc=431493070130ba3820395e23174a914d8b0473c1;l=226)

