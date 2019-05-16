Simple encrypted git
====================

`gitencrypt` is a simple tool that allows you to encrypt a git repository with a password.

This is useful for storing secrets, such as passwords, private keys, etc.

We use git's "smudge" filters to encrypt individual files during commit, and decrypt during checkout/diff. The encryption is AES 256 in CBC mode, using a salt derived from the SHA256 hash of the file, and a password of your choice.
The encryption filter is `openssl enc -base64 -aes-256-cbc -S sha256(contents) -k $PASSWORD`.

## Requirements
bash, sed, openssl (ie any *nix system)

## How to setup a new git repository
* `git clone https://github.com/bmharper/gitencrypt`
* `mkdir myrepo`
* `git init myrepo`
* `gitencrypt/setup myrepo <password>`
* Your repository `myrepo` is now ready to go

## How to clone an existing git repository
* `git clone https://github.com/bmharper/gitencrypt`
* `git clone https://example.com/example/myrepo`
* `gitencrypt/setup myrepo <password>`
* `cd myrepo`
* `git reset --hard`
* Your files should now be checked out, unencrypted

## Security
* Passwords are stored in a plaintext file inside the `$HOME/.gitencrypt` directory.
* Only the first 8 bytes bytes of the SHA256 hash of the file is used for the salt, due to a limitation in the openssl command that is used to encrypt/decrypt.

## Attribution
The technique here is based on this guide:
https://gist.github.com/shadowhand/873637

The only real changes from the above guide, is the use of CBC cipher mode, instead of ECB, and a salt computed from a hash of the file.

2018-08-09, Ben Harper