#Simple Key Exchange web service#

##Description##

THis is a small demo webservice in Ruby using Sinatra Framwork and OpenSSL to create a (not so) secure key exchange between client and server using three methods: RSA, Diffie Hellman (DH) and Elliptic Curve Diffie Hellman (ECDH).

- With RSA, the scenario is:

	+ Client chooses a random AES key and random Init Vector
	+ Client encrypt `key` and `iv` using pre-defined RSA public key.
	+ Client sends encrypted data to server.
	+ Server receives encrypted data,  then uses private key located on server to decrypt to get `key` and `iv`.
	+ Now both client and server have `key` and `iv`, and use them to encrypt data in each request/response after that.

Pros:
	+ Speed is fast (maybe the fastest)
	+ Very easy to implement

Cons:
	+ Pure AES key is transmitted during connection

- With DH, the scenario is:

	+ Client creates DH object, which contains base `G`, modulus `p`, its own private `a` and public `A`
	+ Client sends `G`, `p`, `A` to server.
	+ Server creates DH object from `G` and `p` sent by client, calculate its own private `b` and public `B`, and calculate Shared secret `s` by using `A` sent by client.
	+ Server sents `B` back to client
	+ Client uses `B` to calculate Shared secret `s`
	+ Both client and server use first 16 bytes of shared secret `s` as AES Key, and the next 16 bytes for AES IV and use them to encrypt data in each request/response after that.`

Pros:
	+ AES Key and IV are not transferred during connection, they're calculated on client and server.

Cons:
	+ Slow speed (when DH Key Size is big)
	+ Can't use RSA as an overall layer (in this case, due to the large amount of data being sent)
	+ Bit harder to implement

- With ECDH, the scenario is:

	+ Client creates ECDH instance, which generate a keypair `cpub` and `cpriv`
	+ Client sends `cpub` to server
	+ Server creates ECDH instance, which generate a keypair, 'spub' and 'spriv'
	+ Server computes shared secret `s` by using client' `cpub`
	+ Server sends `spub` to client
	+ Client computes shared secret `s` by using server' `spub`
	+ Both client and server use first 16 bytes of shared secret `s` as AES Key, and the next 16 bytes for AES IV and use them to encrypt data in each request/response after that.`

Pros:
	+ Slightly fast
	+ AES Key and IV are not transferred during connection, they're calculated on client and server.
	+ Can use RSA as an overall layer to make it more secure (in this case)

Cons:
	+ Bit harder to implement

##How to use##

NOTE: Requires `ruby >= 2.3.0`, as requested by `openssl` gems

Install gems using

```
$ bundle install
```

Execute server:

```
$ ruby server.rb
```

By default, the server is at `http://localhost:4567/`

Test with client:

```
$ ruby client.rb
```
