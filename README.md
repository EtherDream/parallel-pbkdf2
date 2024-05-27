# Parallel PBKDF2

## What's PKBDF2

PBKDF2 is used to iterate the password multiple times using the specified hash function, thereby increasing the cost of cracking the password digests after the database is leaked.

## Why use PBKDF2

Because it is natively supported by modern browsers and requires only a few lines of code.

If we use other hash algorithms, more than half of the performance will be lost in the JavaScript/WebAssembly VM.

## What's Parallel PBKDF2

Since PBKDF2 does not support concurrency, it cannot fully utilize multi-core CPU resources.

So some improvements can be made: we create multiple threads, each thread calculates PBKDF2 with different parameters, and finally merge the individual results together:

```bash
# parallel
for i = 1 to N
  dk[i] = pbkdf2(pwd, i + salt, iter)

reuslt = hash(dk[1] + dk[2] + ... + dk[N])
```

In this way, the hash time on the browser side will not change, but N times of computing resources will be consumed during cracking.

## Demo

https://etherdream.github.io/parallel-pbkdf2/

We use `<i, SiteId, User>` as salt. `SiteId` is a fixed UUID that identifies different sites, preventing attackers from using shared rainbow tables. If an attacker generates rainbow tables for each site, the cost will be very high.

## Compatibility

Modern browsers support PBKDF2, but some browsers have issues.

Firefox's PBKDF2 is not fully optimized and has only half the performance of other browsers; Chrome cannot run PBKDF2 in parallel.
