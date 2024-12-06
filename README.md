# Background and History

draft-truskovsky-lamps-pq-hybrid-x509 was submitted to the IETF in 2018 as a way to carry multiple public keys and multiple signatures in a single X.509 certificate. The IETF draft was abandoned in 2018 due to concerns with the technical feasability of the idea. In 2023, the draft was updated to start with a "Tombstone notice" that attempted to clearify that the draft is really dead and will not be revived. This note provides additional detail on that descision.

https://datatracker.ietf.org/doc/html/draft-truskovsky-lamps-pq-hybrid-x509-02

The three X.509v3 extensions that it defines, `subjectAltPublicKeyInfo`, `altSignatureAlgorithm` and `altSignatureValue` were added to the ITU-T's X.509 2019 specification.



# Rationale -- "Attractive Nuisance"

We are all familiar with the benefits of X.509v3 `critical:false`, which is what makes this attractive. And to some extent that's true: you can throw a CATALYST cert into any standard RFC5280 path validator and it'll pass, even if the PQ part is not recognized. This could be useful for things like S/MIME where I grab your encryption cert and we want legacy RSA clients to just encrypt with RSA, but I want PQ clients to find and use the PQ key.

The "nuisance" part is what to do when you actually want to sign something with that cert. Let's consider two examples:
 
  * TLS server keys. If the server is going to sign the handshake with the Alt key, then you need to add a new ClientHello extension to TLS so that the client can indicate support for the CATALYST cert format.
  * CMS / S/MIME / PDF / Codesigning: when signing the document, you don't know the capabilities of the receiver. Maybe you put the Primary and Alt signatures into separate SignerInfos and pray that the verifier will gracefully ignore un-recognized SignerInfos so long as one passed? (HINT: most email clients and most PDF viewers put up some hideous warning that we have started calling a "Warning: Content Is Secure!"). Also, the idea producing two SignerInfos from one certificate probably needs some new SignerInfo Attributes to indicate that the AltPubKey was used.
  * TLS and CMS are robust protocols; these are supposedly the easy cases, but even here we have to modify the protocol in order to accommodate this thing whose whole reason for existing is to be "gracefully backwards compatible". In the end, this ends up being more hassle than just using two unrelated {RSA} and {PQ} certs -- or better {RSA} and {PQ, RSA} with unique RSA keys.
  *Attractive nuisance.


