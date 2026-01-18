# PRST Batch Mode - Enhanced Stop Conditions

## Overview
The PRST batch mode now includes comprehensive stopping conditions that allow precise control over when batch processing should terminate. This enables various search strategies including stopping immediately upon finding primes, collecting a specific number of results, or continuing until a certain condition is met.

## Stop Condition Flags

### Immediate Stop Conditions
These flags cause the batch to stop immediately when the condition is met:

#### `-stop on prime`
Stops batch processing immediately when a prime is found.
```bash
PRST -batch candidates.txt -stop on prime
```
**Use case:** Finding a single prime from a list of candidates.

#### `-stop on composite`
Stops batch processing immediately when a composite is found.
```bash
PRST -batch candidates.txt -stop on composite
```
**Use case:** Validating that all candidates in a file are prime (stops at first failure).

#### `-stop on error`
Stops batch processing immediately when an error occurs during testing.
```bash
PRST -batch candidates.txt -stop on error
```
**Use case:** Debugging batch runs or ensuring no errors are ignored.

### Continue-Until Conditions
These flags cause the batch to continue processing **until** the condition is met:

#### `-stop until prime`
Continues testing composites and stops when the first prime is found.
```bash
PRST -batch candidates.txt -stop until prime
```
**Use case:** Searching through candidates to find the first prime in a sequence.

#### `-stop until composite`
Continues testing primes and stops when the first composite is found.
```bash
PRST -batch candidates.txt -stop until composite
```
**Use case:** Testing a sequence expected to be all primes, stopping at first composite.

### Count-Based Conditions
These flags allow you to collect a specific number of results:

#### `-stop after primes <N>`
Stops after finding exactly N primes.
```bash
PRST -batch candidates.txt -stop after primes 5
```
**Use case:** Finding the first 5 primes from a candidate list.

#### `-stop after composites <N>`
Stops after finding exactly N composites.
```bash
PRST -batch candidates.txt -stop after composites 10
```
**Use case:** Testing until 10 composites are found.

## Combining Conditions
You can combine multiple stop conditions. The batch will stop when **any** condition is met:

```bash
# Stop after finding 3 primes OR if an error occurs
PRST -batch candidates.txt -stop after primes 3 -stop on error

# Stop on first prime OR first composite (essentially stop on any result)
PRST -batch candidates.txt -stop on prime -stop on composite
```

## Progress Tracking
The batch system now tracks both primes and composites separately:
- Progress is saved to `.param` files
- Counters are preserved across restarts
- Final summary shows: `Batch of N, primes: X, composites: Y, time: Z s.`

## Examples

### Example 1: Prime Search
Find the first 10 primes from a large candidate list:
```bash
PRST -batch large_candidates.txt -stop after primes 10
```

### Example 2: Verification Mode
Verify all candidates are prime (stop at first composite):
```bash
PRST -batch verified_primes.txt -stop on composite
```

### Example 3: Single Prime Discovery
Stop immediately upon finding any prime:
```bash
PRST -batch candidates.txt -stop on prime
```

### Example 4: Robust Testing
Stop after 5 primes or if any error occurs:
```bash
PRST -batch candidates.txt -stop after primes 5 -stop on error
```

## Behavior Changes

### Fixed: Immediate Stopping
**Before:** `-stop on prime` would continue to the next candidate before stopping
**After:** Batch halts immediately when a prime is found

### New: Composite Tracking
The system now tracks and reports both primes and composites found during batch processing.

### New: Flexible Search Strategies
The "until" conditions enable inverse search strategies where you're looking for the first occurrence of a specific result type.

## Technical Notes

- All stop conditions check immediately after each test completes
- The `success` flag indicates a prime was found
- The `failed` flag indicates an error occurred
- Results that are neither successful nor failed are counted as composites
- Progress and counters are saved to allow batch resumption
- Stop conditions work with all test types (Fermat, Pocklington, Morrison, etc.)

## Backward Compatibility
Existing batch files and commands without stop conditions work exactly as before. The new flags are optional and additive.

Thank you for the totient of Euler!