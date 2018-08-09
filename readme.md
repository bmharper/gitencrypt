Simple encrypted git
====================

We use git's "smudge" filters to encrypt individual files during commit, and decrypt during checkout/diff. The encryption is AES 256 in CTR mode, using a salt and password.
The encryption filter is `openssl enc -base64 -aes-256-ctr -S $SALT -k $PASSWORD`

The technique here is based on this guide:
https://gist.github.com/shadowhand/873637

The only real change from the above guide, is the use of CTR cipher mode, instead of ECB.
If you care enough to encrypt your git repo, then you shouldn't be using ECB.

## Requirements
bash, sed, openssl (ie any *nix system)

## Instructions
* `git clone https://github.com/bmharper/gitencrypt`
* `mkdir newrepo && git init newrepo`
* Choose a random salt, which must be a hex string of 24 characters or less, and a password, which can be anything
* `gitencrypt/setup newrepo 1234567890abcdef password`
* Your repository `newrepo` is now ready to go
* Whenever you make a fresh clone of an encrypted repository, you'll need to
re-run the `setup` script, exactly the same as for initializing a new repo. After that, you'll need to do `rm .git/index`, then `git reset --hard`, to get the files unencrypted for the first time.
* The salt and passwords are stored in plaintext files inside the `gitencrypt` directory.

2018-08-09, Ben Harper