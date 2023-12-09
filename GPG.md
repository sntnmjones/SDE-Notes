https://www.digitalocean.com/community/tutorials/how-to-use-gpg-to-encrypt-and-sign-messages
## Install
```bash
brew install gpg
```

## Key Gen
Create the revocation cert immediately afterward
```bash
gpg --gen-key
gpg (GnuPG) 2.4.3; Copyright (C) 2023 g10 Code GmbH
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

Note: Use "gpg --full-generate-key" for a full featured key generation dialog.

GnuPG needs to construct a user ID to identify your key.

Real name: Troy Jones
Email address: jnesztr@amazon.com
You selected this USER-ID:
    "Troy Jones <jnesztr@amazon.com>"

Change (N)ame, (E)mail, or (O)kay/(Q)uit? o
We need to generate a lot of random bytes. It is a good idea to perform
some other action (type on the keyboard, move the mouse, utilize the
disks) during the prime generation; this gives the random number
generator a better chance to gain enough entropy.
We need to generate a lot of random bytes. It is a good idea to perform
some other action (type on the keyboard, move the mouse, utilize the
disks) during the prime generation; this gives the random number
generator a better chance to gain enough entropy.
gpg: /Users/jnesztr/.gnupg/trustdb.gpg: trustdb created
gpg: directory '/Users/jnesztr/.gnupg/openpgp-revocs.d' created
gpg: revocation certificate stored as '/Users/jnesztr/.gnupg/openpgp-revocs.d/C36C83E7FD8755BA9F2362E2403DBF7894C70A31.rev'
public and secret key created and signed.

pub   ed25519 2023-12-08 [SC] [expires: 2026-12-07]
      C36C83E7FD8755BA9F2362E2403DBF7894C70A31
uid                      Troy Jones <jnesztr@amazon.com>
sub   cv25519 2023-12-08 [E] [expires: 2026-12-07]
```

## Revocation cert
```bash
gpg --output ~/revocation.crt --gen-revoke your_email_address
```

## Import public keys
This is to decrypt other's messages and encrypt messages.
```bash
gpg --import other_persons_pub_key
```

## Encrypt message

This will create a file called `message.txt.asc`. That's the message you will send along with your public key created in the next step.
```bash
gpg --encrypt --sign --armor -r other_persons_email_address message.txt
```

## Export public key
```bash
gpg --output ~/mygpg.key --armor --export your_email_address
```

## Decrypt message
```bash
gpg --encrypt --sign --armor -r senders_email_address name_of_file
```