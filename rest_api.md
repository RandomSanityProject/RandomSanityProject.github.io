# REST API

All API access is over HTTP, and accessed from
`http://rest.randomsanity.org/v1/`.

HTTPS access might be supported in the future, but if your machine starts
generating bad randomness HTTPS is not secure (because the TLS
handshake relies on your machine generating a cryptographically secure
random session token).

## GET /v1/q/:hexbytes?id=:userID&tag=:tag

Parses `:hexbytes` as an array of bytes, and returns "true" or "false"
depending on whether or not they pass the looks-random and uniqueness
tests. A `400 Bad Request` error is returned if fewer than 16 bytes
are given or `:hexbytes` is not hexadecimal (0-9 a-f A-F).

`id` and `tag` are optional. If id is given and is an active, previously
registered user ID an alert will be sent to the user via email
notifying them of failures. `tag` is an arbitrary string that is sent
along with the alert so you know exactly what is failing, and should
be the machine's hostname or IP address.

Requests with an `id` are rate-limited to 600 per hour; anonymous
requests are rate-limited by IP address to 60 per hour. If you exceed
the rate limit you will get a `429 Too Many Requests` error.

The random sanity service adds an `X-Entropy:` header with 32 random
hexadecimal bytes to every successful response that you might choose
to mix in to your entropy pool an extra source of entropy for your
random number generator (e.g. on Linux write it to /dev/urandom).

Example:

```
curl -i http://rest.randomsanity.org/v1/q/4fb16b80a1670c65d33f865654BB2178

HTTP/1.1 200 OK
Content-Type: application/json
X-Entropy: d276443520f03d3b2ff705e672b1d1b34cb1311c27dd1bfb37245e073eb0b4b7
X-Cloud-Trace-Context: 7214bf61fafc4ecfd48abf762c533a86
Date: Mon, 24 Apr 2017 15:33:33 GMT
Server: Google Frontend
Content-Length: 4

true
```

## GET /v1/registeremail/:email

Requests that a userID be sent to :email.

For example:

```
curl 'http://rest.randomsanity.org/v1/registeremail/randomsanity@mailinator.com'

Check your email, ID sent to randomsanity@mailinator.com
```

The [email received](https://www.mailinator.com/inbox2.jsp?public_to=randomsanity#/#public_showmaildiv):

```
Subject:	Random Sanity id request

Somebody requested an id for this email address 
(randomsanity@mailinator.com)
for the randomsanity.org service.

id: 471d313cbb2a34d4

Append ?id=471d313cbb2a34d4 to API calls to be notified of failures via 
email.

If somebody is messing with you and you don't use the randomsanity.org
service, please ignore this message.

```


This method is highly rate-limited (currently 2 registrations per IP address per day) to prevent abuse.
