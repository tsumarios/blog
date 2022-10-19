---
layout: post
title:  "How TOTP Works"
date:   2022-10-07
modified_date: 2022-10-18
author:
  - Mario Raciti
tags: cryptography hardening
---

A basic explaination about Time-based one-time password (TOTP) and a simple Python PoC.
<!-- readmore -->

![cover](https://images.unsplash.com/photo-1634224143538-ce0221abf732?ixlib=rb-1.2.1&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=1374&q=80)

*Oh, it's quite simple. If you are a friend, you speak the password, and the doors will open.* ― Gandalf

[Time-based one-time password (TOTP)](https://www.rfc-editor.org/rfc/rfc6238) [RFC 6238] is the cornerstone of Initiative for Open Authentication (OATH) and is popular in a number of two-factor authentication (2FA) systems. Nowadays many websites and services require 2FA or multi-factor authentication (MFA) where the user is required to present two or more pieces of evidence:

- Something only the user knows, e.g., password, passphrase, etc.
- Something only the user has, e.g., hardware token, mobile phone, etc.
- Something only the user is, e.g., biometrics.

A *TOTP value* usually serves as the second factor, i.e., it proves that the user is in possession of a device (e.g., mobile phone) that contains a *TOTP secret key* from which the TOTP value is generated. Generally the service provider that provides a user's account also issues a secret key encoded either as a Base32 string or as a QR code. This secret key is added to an authenticator app (e.g., Google Authenticator, Authy, etc.) on a mobile device. The app can then generate TOTP values based on the current time. By default, a new TOTP value is generated every 30 seconds.

In this article **we describe the TOTP algorithm and provide a simple Python PoC**. Before proceeding, however, it is necessary to give a little bit of context by intorducing the *HMAC* and *HOTP algorithms*.

## HMAC

[Hash-based MAC (HMAC)](https://www.rfc-editor.org/rfc/rfc2104) [RFC 2104] authentication passcode is procured by running a hash function - typically SHA-1 - over the initial data and the shared secret key. This ensures the sent data was not tampered with and that it was sent from a legitimate source by comparing the hash value and the shared key. The key is generated in a key exchange process and only the two parties that were involved in the exchange can know it, so only these two can get the exact same output when working the message’s MAC with the seed.

## HOTP

[HMAC-based one-time password (HOTP)](https://www.rfc-editor.org/rfc/rfc4226) [RFC 4226] provides a method of authentication by symmetric generation of human-readable passwords, or values, each used for only one authentication attempt. The one-time property leads directly from the single use of each counter value.

Parties intending to use HOTP must establish some parameters, which are typically specified by the authenticator, and either accepted or not by the authenticated:

- a cryptographic hash function *H* (default is SHA-1);
- a secret key *K*, which is an arbitrary byte string and must remain private;
- a counter *C*, which counts the number of iterations;
- a HOTP value length *d* (6–10, default is 6, and 6–8 is recommended).

Both parties compute the HOTP value derived from the secret key *K* and the counter *C*. Then the authenticator checks its locally generated value against the value supplied by the authenticated. Both parties increment the counter *C* independently of each other. This should set alarm bells ringing, because it **HOTP has a potential synchronisation issue** here. The authenticator's counter continues forward of the value at which verification succeeds and requires no actions by the authenticated. Essentially, what occurs is the autenticator remembers the last counter value which resulted in successful validation. Whenever a new OTP needs to be validated, the server will try *C+1*, *C+2*, etc., until it gets the one that matches. Or until it arrives at what is called a *look-ahead window*. The recommendation is made that persistent throttling of HOTP value verification take place, to address their relatively small size and thus **vulnerability to brute-force attacks**. It is suggested to lock out verification after a small number of failed attempts or that each failed attempt attracts an additional (linearly increasing) delay.

## TOTP

[Time-based one-time password (TOTP)](https://www.rfc-editor.org/rfc/rfc6238) [RFC 6238] is an extension of the HOTP algorithm generating a one-time password (OTP).

To establish TOTP authentication, the authenticated and authenticator must pre-establish both the *HOTP parameters* and the following *TOTP parameters*:

- *T_0*, the Unix time from which to start counting time steps (default is 0);
- *T_x*, an interval which will be used to calculate the value of the counter *C_t* (default is 30 seconds).

The counter *C_t* is thus based on the current time, as follows:

![formula](https://wikimedia.org/api/rest_v1/media/math/render/svg/48bf4f594b18b954caab5457f2efed6aa6082432)

where:

- *C_t* is the count of the number of durations *T_x* between *T_0* and *T*;
- *T* is the current time in seconds since a particular epoch;
- *T_0* is the epoch as specified in seconds since the Unix epoch (e.g., if using Unix time, then *T_0* is 0);
- *T_x* is the length of one time duration (e.g. 30 seconds).

In short, the **inputs to the TOTP algorithm are device time and a stored secret key**. Neither the inputs nor the calculation require Internet connectivity to generate or verify a token. Therefore a user can access TOTP via an app (e.g., Google Authenticator, Authy, etc.) while offline. Both the authenticator and the authenticated compute the TOTP value, then the authenticator checks whether the TOTP value supplied by the authenticated matches the locally generated TOTP value. Some authenticators allow values that should have been generated before or after the current time in order to account for slight clock skews, network latency and user delays.

### HOTP vs TOTP

Both methods use a secret key as one of the inputs, but while TOTP uses the system time for the other input, HOTP uses a counter, which increments with each new validation. This means that, simply put, like with HOTP both parties share a seed on setup but, on the other side, TOTP OPT values have the advantage of being valid for a limited time period - avoiding the brute-forcing vulnerability.

## TOTP PoC

Below a simple Python PoC implementation of the TOPT algorithm - the script is also available [here](https://gist.github.com/tsumarios/ca1647dad192d8558940e0d30b6e4f1b) -, based on the [mintotp](https://github.com/susam/mintotp) module.

```python
#!/usr/bin/env python3

import base64
import datetime
import hmac
import os
import struct
import time
import sys


# This key should be kept hidden (e.g., in env variables, here supposed to be OTP_SHARED_KEY). The string here provided as default value is Base32 encoded and is just for the sake of demo.
KEY = os.environ.get('OTP_SHARED_KEY', 'ORZXK3LBOJUW643CNRXWO2LTMF3WK43PNVSQ====')


def hotp(key, counter, digits=6, digest='sha1'):
    key = base64.b32decode(key.upper() + '=' * ((8 - len(key)) % 8))
    counter = struct.pack('>Q', counter)
    mac = hmac.new(key, counter, digest).digest()
    offset = mac[-1] & 0x0f
    binary = struct.unpack('>L', mac[offset:offset+4])[0] & 0x7fffffff
    return str(binary)[-digits:].zfill(digits)


def totp(key, time_step=30, digits=6, digest='sha1'):
    return hotp(key, int(time.time() / time_step), digits, digest)


def main():
    while True:
        # Generate a new OTP every second by using TOTP.
        # NOTE: every 30s the OTP value will change :D
        try:
            secs = 30 - datetime.datetime.utcnow().second % 30
            otp_code = totp(KEY.strip())
            timer = '{:02d}s'.format(secs)
            print(f"OTP: {otp_code} (expires in {timer})", end="\r")
            time.sleep(1)
        except KeyboardInterrupt:
            print('', end="\r")
            sys.exit(0)


if __name__ == '__main__':
    main()
```

The [HOTP algorithm](https://tools.ietf.org/html/rfc4226#section-5) is implemented using the `hmac` Python module in the `hotp()` function, which takes a Base32-encoded secret key and a counter as input and returns a 6-digit HOTP value as output. The `totp()` function implements the [TOTP algorithm](https://www.rfc-editor.org/rfc/rfc6238#section-4), wrapping around the HOTP algorithm. The TOTP value is then obtained by invoking the HOTP function with the secret key and the number of time  intervals (30 second intervals by default) that have elapsed since Unix epoch (1970-01-01 00:00:00 UTC).

---

## Conclusions

The security and strength of TOTP depend on the properties of the underlying building block, that is HOTP which, in turn, is based on HMAC using SHA-1 as the hash function. Unlike passwords, **TOTP codes are single-use**, so a compromised credential is only valid for a limited time. However, users are supposed to enter TOTP codes into an authentication page, which creates the potential for *phishing attacks*. In such a case, due to the short window in which TOTP codes are valid, attackers must proxy the credentials in real time. Furthermore, TOTP credentials are based on a shared secret known to both the parties, involving multiple locations from which it can be stolen. An adversary with access to this shared secret could generate new valid TOTP codes at will. This can be a particular problem if the attacker breaches a large authentication database. Therefore, the **key store MUST be in a secure area**, to avoid direct attacks on the validation system and secrets database as much as possible. Access to the key should be limited to programs and processes required by the validation system only.

---

### References

- <https://en.wikipedia.org/wiki/Multi-factor_authentication>
- <https://tools.ietf.org/html/rfc2104>
- <https://tools.ietf.org/html/rfc4226>
- <https://tools.ietf.org/html/rfc6238>
- <https://en.wikipedia.org/wiki/Time-based_one-time_password>
- <https://gist.github.com/tsumarios/ca1647dad192d8558940e0d30b6e4f1b>
- <https://github.com/susam/mintotp>
