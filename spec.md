adds ed25519 , ed25519ph  algorithms for sign/verify operation

# Approach
openssl lacks ed25519_ph support via cli , available via C interface
created a minimal c executable to expose ed25519ph sign/verify methods
patch docker containers to bundle the binary

fips docker images , use < 3.5 openssl image variants , ed25519_ph varaint is fips enabled starting in openssl 3.5+,
bumping it is needed to enable support for fips image , api detects and sends 'unsupported' response in this case
ed25519_ph itself is fips approved

verification
2 algorithm compiliance strategies are used
cross interapobility of same key with AWS , sign/verify algotihms
  - sign at infisical , verify at AWS work for ed25519 , ed25519_ph
  - aws does dual hash ed25519_ph needed a dual hash at client side before verification ,
    this is not mandated by standard , infisical doesn't do dual hash due to same reason
  - also validated scenarious of infisical verify, aws sign e.t.c
cross interapobiliity with noble/curves ed25519_ph methods
  - sign , verify with same keys at infisical api , noble/curves cross-use pass

added tests to cover new algorithms , excluding one time test cases

![Algorithm selection](https://raw.githubusercontent.com/Shaik-Sirajuddin/infisical/docs/ed25519-signature-screenshots/docs/images/kms/ed25519/algorithm-selection.png)

![Signing with Ed25519 SHA-512](https://raw.githubusercontent.com/Shaik-Sirajuddin/infisical/docs/ed25519-signature-screenshots/docs/images/kms/ed25519/ed25519-sha512-signing.png)

![Successful Ed25519 SHA-512 signature](https://raw.githubusercontent.com/Shaik-Sirajuddin/infisical/docs/ed25519-signature-screenshots/docs/images/kms/ed25519/ed25519-sha512-success.png)

![Signing with Ed25519ph and prehashed input](https://raw.githubusercontent.com/Shaik-Sirajuddin/infisical/docs/ed25519-signature-screenshots/docs/images/kms/ed25519/ed25519ph-prehashed-signing.png)

![Verifying an Ed25519ph signature](https://raw.githubusercontent.com/Shaik-Sirajuddin/infisical/docs/ed25519-signature-screenshots/docs/images/kms/ed25519/ed25519ph-verification.png)

# Note for maintainers
Pr has been handwritten , including all of backend , use of AI was restricted to parts of verifications , some of frontend changes

----------------------validations

---> review frontend , capture screenshots , docs coverage for new algo ?
---> do language sdks have type bindings for the algorithms list ?
<!-- If required to include the harness of noble/curves or any changes here ,  -->
