-------- verifications
create key material with RSA_4096 , encrypt/decrypt usage with exportable 
encrypt it with infiisal kms api 
  assert true 
decrypt it with infisical kms api 
  assert true and the origin plaintext is deciphered 
rotateKey 
  assert it is rejected 

-- out of scope one time tests 
  exported material from the insifcal import into aws , a
  and follow
     encrypt at inficial 
     decrpt at aws , with same key 
     assert true and same plaintext is deciphered 

conduct in backend/e22-test/ kms service 

validate whether , the encrypt custom wrpped is safe for assymetric algorithm addifiton ? 

----------------pr
adds supports for RSA_4096 asymetric key type for encrypt/decrypt operations 

encrypt_decrypt type key usage , can only be used with symmetric keys today , 
post change , asymmetric key encryption RSA_4096 is available for encrypt_decrypt RSAES_OEAP_SHA256 as encryption algorithm

## Approach 

uitlizes  RSAES_OEAP_SHA256 as default encryptionAlgorithm , available options are [RSAES_OEAP_SHA256,RSAES_OEAP_SHA1] 
Rotataions are explicity disabled for Encrypt type KMS key with RSA_4096 , being an asymmetric type key
RSAES_OEAP_SHA256 follows signing described in PKCS #1 v2.2 / RFC 8017 


[x] Updated frontend
[x] Inlcuded Test cases


Screenshots
![Created Key](https://raw.githubusercontent.com/Shaik-Sirajuddin/infisical/docs-kms-screenshots/docs/images/platform/kms/infisical-kms/rsa-encryption/kms-rsa-4096-key.png)
![Form Create Key](https://raw.githubusercontent.com/Shaik-Sirajuddin/infisical/docs-kms-screenshots/docs/images/platform/kms/infisical-kms/rsa-encryption/kms-rsa-4096-key-form.png)
![encrypt with asymmetric key](https://raw.githubusercontent.com/Shaik-Sirajuddin/infisical/docs-kms-screenshots/docs/images/platform/kms/infisical-kms/rsa-encryption/kms-rsa-4096-encrypt-data.png)
![decrypt with rsa](https://raw.githubusercontent.com/Shaik-Sirajuddin/infisical/docs-kms-screenshots/docs/images/platform/kms/infisical-kms/rsa-encryption/kms-rsa-4096-decrypted-data.png)

# Note for maintainers
Pr has been handwritten , including all of backend , use of AI was restricted to parts of verifications , some of frontend changes

------------tmp code


    512 +    if (encryptionAlgorithm === AsymmetricKeyAlgorithm.RSA_4096) {
    513 +      return async ({ cipherTextBlob }: Pick<TDecryptWithKmsDTO, "cipherTextBlob">) =>
    514 +        crypto.nativeCrypto.privateDecrypt(
    515 +          {
    516 +            key: kmsKey,
    517 +            format: "pem",
    518 +            type: "pkcs8",
    519 +            padding: crypto.nativeCrypto.constants.RSA_PKCS1_OAEP_PADDING,
    520 +            oaepHash: "sha256"
    521 +          },
    522 +          cipherTextBlob
    523 +        );
    524 +    }
    525 +
    526      // decrypt method based of 'encryptionAlgorithm' , relavant only inside decrptyWithKmsKey
    527      const getDecryptWithKeyMaterial = () => {
    515 -      if (encryptionAlgorithm === AsymmetricKeyAlgorithm.RSA_4096) {
    516 -        return (cipherText: Buffer, privateKey: Buffer) => {
    517 -          return crypto.encryption().hybrid().decrypt({
    518 -            algorithm: HybridSigningAlgorithm.RSAES_OEAP_SHA256,
    519 -            cipherText,
    520 -            privateKey
    521 -          });
    522 -        };
    523 -      }
    528        const dataCipher = symmetricCipherService(encryptionAlgorithm);
        ⋮
    956        return ({ plainText }: Pick<TEncryptWithKmsDTO, "plainText">) => {
    953 -        const encryptedPlainTextBlob = crypto.encryption().hybrid().encrypt({
    954 -          algorithm: HybridSigningAlgorithm.RSAES_OEAP_SHA256,
    955 -          plainText,
    956 -          publicKey
    957 -        });
    958 -        /// tldr is this necessary for assmetirc keys ?
    959 -        const cipherTextBlob = buildKmsCipherTextBlob(encryptedPlainTextBlob, currentKeyVersion);
    957 +        const cipherTextBlob = crypto.nativeCrypto.publicEncrypt(
    958 +          {
    959 +            key: publicKey,
    960 +            format: "der",
    961 +            type: "spki",
    962 +            padding: crypto.nativeCrypto.constants.RSA_PKCS1_OAEP_PADDING,
    963 +            oaepHash: "sha256"
    964 +          },
    965 +          plainText
    966 +        );
    967          return Promise.resolve({ cipherTextBlob });
