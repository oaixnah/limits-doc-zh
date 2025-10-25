---
hide:
  - navigation
---

# Rate Limiting Strategies

## How to choose a strategy

- **Fixed Window:**
  Use when low memory usage and high performance are critical, and occasional bursts
  are acceptable or can be mitigated by additional fine-grained limits.

- **Moving Window:**
  Use when exactly accurate rate limiting is required and extra memory overhead is acceptable.

- **Sliding Window Counter:**
  Use when a balance between memory efficiency and accuracy is needed. This strategy
  smooths transitions between time periods with less overhead than a full moving window,
  though it may trade off some precision near bucket boundaries.

## Fixed Window

This strategy is the most memory‑efficient because it uses a single counter per resource and
rate limit. When the first request arrives, a window is started for a fixed duration
(e.g., for a rate limit of 10 requests per minute the window expires in 60 seconds from the first request).
All requests in that window increment the counter and when the window expires, the counter resets.

Burst traffic that bypasses the rate limit may occur at window boundaries.

For example, with a rate limit of 10 requests per minute:

1. At **00:00:45**, the first request arrives, starting a window from **00:00:45** to **00:01:45**.
2. All requests between **00:00:45** and **00:01:45** count toward the limit.
3. If 10 requests occur at any time in that window, any further request before **00:01:45** is rejected.
4. At **00:01:45**, the counter resets and a new window starts which would allow 10 requests
   until **00:02:45**.

!!! tip

    To mitigate burstiness (e.g., many requests at window edges), combine limits
    with large windows with finer-granularity ones
    (e.g., combine a 2 requests per second limit with a 10 requests per minute limit).


## Moving Window

This strategy adds each request’s timestamp to a log if the `nth` oldest entry (where `n`
is the limit) is either not present or is older than the duration of the window (for example with a rate limit of
`10 requests per minute` if there are either less than 10 entries or the 10th oldest entry is at least
60 seconds old). Upon adding a new entry to the log "expired" entries are truncated.

For example, with a rate limit of 10 requests per minute:

1. At **00:00:10**, a client sends 1 requests which are allowed.
2. At **00:00:20**, a client sends 2 requests which are allowed.
3. At **00:00:30**, the client sends 4 requests which are allowed.
4. At **00:00:50**, the client sends 3 requests which are allowed (total = 10).
5. At **00:01:11**, the client sends 1 request. The strategy checks the timestamp of the
   10th oldest entry (**00:00:10**) which is now 61 seconds old and thus expired. The request
   is allowed.
6. At **00:01:12**, the client sends 1 request. The 10th oldest entry's timestamp is **00:00:20**
   which is only 52 seconds old. The request is rejected.

## Sliding Window Counter

> Added in version 4.1.

This strategy approximates the moving window while using less memory by maintaining
two counters:

- **Current bucket:** counts requests in the ongoing period.
- **Previous bucket:** counts requests in the immediately preceding period.

A weighted sum of these counters is computed based on the elapsed time in the current
bucket. The weighted count is defined as:


$$
C_{\text{weighted}} = \left\lfloor C_{\text{current}} + \left(C_{\text{prev}} \times w\right) \right\rfloor
$$

and the weight factor :math:`w` is calculated as:

$$
w = \frac{T_{\text{exp}} - T_{\text{elapsed}}}{T_{\text{exp}}}
$$

Where:

- $T_{\text{exp}}$ is the bucket duration.
- $T_{\text{elapsed}}$ is the time elapsed since the bucket shifted.
- $C_{\text{prev}}$ is the previous bucket's count.
- $C_{\text{current}}$ is the current bucket's count.


For example, with a rate limit of `100 requests per minute`

Suppose:

- Current bucket has 80 hits ($C_{\text{current}}$)
- Previous bucket has 40 hits ($C_{\text{prev}}$)

- If the bucket shifted 30 seconds ago ($T_{\text{elapsed}} = 30$).

$$
w = \frac{60 - 30}{60} = 0.5
$$

$$
C_{\text{weighted}} = \left\lfloor 80 + (0.5 \times 40) \right\rfloor = 100
$$

Since the effective count equals the limit, a new request is rejected.

- If the bucket shifted 40 seconds ago ($T_{\text{elapsed}} = 40$).

$$
w = \frac{60 - 40}{60} \approx 0.33
$$

$$
C_{\text{weighted}} = \left\lfloor 80 + (0.33 \times 40) \right\rfloor = 93
$$

Since the effective count is below the limit, a new request is allowed.

!!! note

    Some storage implementations use fixed bucket boundaries (e.g., aligning buckets with
    clock intervals), while others adjust buckets dynamically based on the first hit.
    This difference can allow an attacker to bypass limits during the initial sampling
    period. The affected implementations are `memcached` and `in-memory`.


