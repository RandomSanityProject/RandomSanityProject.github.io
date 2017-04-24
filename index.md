# Random Sanity Project

The Random Sanity Project is a free, open source service that
helps secure the Internet by sanity-checking sources of
randomness. If you are a CTO or system administrator responsible for
a security-critical web server or application running on the
Internet you should consider using this service to alert you
of catastrophic hardware or software failures that could completely
compromise the security of your website or application.

The service is in an early stage of development; I am looking for
people willing to use it, test it, provide feedback, and maybe
even write a little code to help make it better.

## How it Works

1. Get an array of 64 bytes from whatever source of cryptographically-secure randomness you are using
2. Encode them as hex, and make a REST query to the randomsanity service
3. The service will return 'true' if it those bytes look random and nobody else has submitted the same stream of bytes; otherwise it will return 'false'.

For example, if you are using openssl as your source of
randomness, you could sanity-test it with this bash code:

```bash
BYTES=$(openssl rand -hex 64)
curl http://rest.randomsanity.org/v1/q/$BYTES
```

You should see "true" as the result. Re-run the curl command with
the same BYTES and the result will be "false", because a good
source of randomness will never generate the same 64 bytes twice
(with insanely high probability). See
the [details page](details) if you're curious about
exactly what it is doing with your bytes.

## Getting Started

Read the [API documentation](rest_api).

## Want to Help?
See the [how to help page](how_to_help).
