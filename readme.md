Simple encrypted git
====================

`gitencrypt` is a simple tool that allows you to encrypt a git repository with a password.

This is useful for storing secrets, such as passwords, private keys, etc.

We use git's "smudge" filters to encrypt individual files during commit, and decrypt during checkout/diff. The encryption is AES 256 in CBC mode, using a fixed salt, and a password of your choice.
The encryption filter is `openssl enc -base64 -aes-256-cbc -S 0123456789abcdef -k $PASSWORD`.

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
* `rm .git/index`
* `git reset --hard`
* Your files should now be checked out, unencrypted

## Security
* Passwords are stored in a plaintext file inside the `$HOME/.gitencrypt` directory.
* Due the limitation of a fixed salt, an attacker can see the first 256-byte block in which you make a change to a file. In other words, when you make a change, the encrypted version of that file will be identical to it's previous version, up to the point where the first change is made. This leaks some information. In many cases this is fine. For example, if you're storing a private key in here, and that key changes, then you can expect the entire private key to change, so no information is leaked in a case like that.
* The use of a long randomized password is especially important, due to the lack of random salt. If you use a predictable password, then a brute force attack is trivial, especially because the filenames are visible to an attacker.

### Why a fixed salt?
If you don't use a fixed salt, then a `git status` always thinks that every file has changed, because it's encrypted form *does* change on every commit.  
Random salts are good for security, but there doesn't seem to be a workaround for this issue.

## Attribution
The technique here is based on this guide:
https://gist.github.com/shadowhand/873637

The only real change from the above guide, is the use of CBC cipher mode, instead of ECB, and a fixed salt.

2018-08-09, Ben Harper