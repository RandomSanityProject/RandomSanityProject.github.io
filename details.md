# How it REALLY works

The goal of this project is to detect **catastrophic**
failures. Things like a sysadmin fat-fingering a command and
accidentally linking /dev/zero to /dev/urandom, or a botched
software upgrade that causes some function deep in the
crypto.randomBytes() callstack to fail, causing everybody who
upgrades to get the same kinda-sorta-random-looking bytes.

The goal is **not** to detect subtle biases in pseudo-random
number generators; you should run
the [NIST statistical test suite](http://csrc.nist.gov/groups/ST/toolkit/rng/documentation_software.html)
and [DieHarder](http://www.phy.duke.edu/~rgb/General/dieharder.php)
and [TestU01](https://en.wikipedia.org/wiki/TestU01) if
you are developing a random number generator and want to make sure
it is generating randomness statistically indistinguishable from
true noise.

The Random Sanity service isn't perfect; no test of 'randomness'
can be perfect. It is designed to have a very small (about 1 in 2^<sup>60</sup>) 
false-positive rate, because if a system administrator spends a
few hours tracking down a "bad randomness" report that turns out
to be just bad luck they are likely to ignore future reports. The
false positive rate of less than one in a quintillion means false
positives will not happen in our lifetimes. The small false
positive rate means you must submit at least 16 bytes (128 bits);
fewer will result in an error.

The service performs the following quick tests on the byte array:

1. 9 (or more) repeated bytes
2. 64 (or more) bytes in a row with the same bit set or unset
3. a bit pattern that looks like counting, with 8/16/32/64-bit big- or little-endian integers

If those simple tests all pass, then every 16-byte sequence in the
array is checked against a database of sequences submitted
in the past. If there is a match, the test fails. If there is no
match, the first and last 16 bytes of the submitted byte array are
added to the database.

## Technical Details

The service is implemented in Go using Google's AppEngine.
The database of 
previously-seen sequences is stored in the AppEngine Datastore
as a simple hash table of 16-byte sequences. Denial-of-service
attacks are mitigated by rate-limiting API requests per IP
address.
