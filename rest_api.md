# REST API

All API access is over HTTP or HTTPS, and accessed from
`https://rest.randomsanity.org/v1/`.

## GET /v1/q/*hexbytes*

Parses `hexbytes` as an array of bytes, and returns "true" or "false"
depending on whether or not they pass the looks-random and uniqueness
tests. A `400 Bad Request` error is returned if fewer than 16 bytes
are given or `hexbytes` is not hexadecimal (0-9 a-f A-F).

Requests are rate-limited by IP address to 60 per hour. If you exceed
the rate limit you will get a `429 Too Many Requests` error.

The random sanity service adds an `X-Entropy:` header with 32 random
hexadecimal bytes to every successful response that you might choose
to mix in to your entropy pool an extra source of entropy for your
random number generator (e.g. on Linux write it to /dev/urandom).

Example:

```
curl -i https://rest.randomsanity.org/v1/q/4fb16b80a1670c65d33f865654BB2178

HTTP/1.1 200 OK
Content-Type: application/json
X-Entropy: d276443520f03d3b2ff705e672b1d1b34cb1311c27dd1bfb37245e073eb0b4b7
X-Cloud-Trace-Context: 7214bf61fafc4ecfd48abf762c533a86
Date: Mon, 24 Apr 2017 15:33:33 GMT
Server: Google Frontend
Content-Length: 4

true
```
