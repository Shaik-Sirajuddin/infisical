-->
----------------------------------------------------------------------------------------
## Aws wrapping algorithm table , covers all infisical supported keys

| Wrapping key | OAEP algorithm     | Key material | Exact key algorithms/specs covered                             |
| ------------ | ------------------ | ------------ | -------------------------------------------------------------- |
| RSA-4096     | RSAES-OAEP-SHA-256 | HMAC key     | `HMAC_SHA_224`, `HMAC_SHA_256`, `HMAC_SHA_384`, `HMAC_SHA_512` |
| RSA-4096     | RSAES-OAEP-SHA-1   | HMAC key     | `HMAC_SHA_224`, `HMAC_SHA_256`, `HMAC_SHA_384`, `HMAC_SHA_512` |

| Wrapping key | Wrapping algorithm       | Key material    | Exact key algorithms/specs covered                |
| ------------ | ------------------------ | --------------- | ------------------------------------------------- |
| RSA-4096     | RSA-AES-KEY-WRAP-SHA-256 | ECC private key | `ECC_NIST_P256`, `ECC_NIST_P384`, `ECC_NIST_P521` |
| RSA-4096     | RSA-AES-KEY-WRAP-SHA-1   | ECC private key | `ECC_NIST_P256`, `ECC_NIST_P384`, `ECC_NIST_P521` |
| RSA-4096     | RSAES-OAEP-SHA-256       | ECC private key | `ECC_NIST_P256`, `ECC_NIST_P384`, `ECC_NIST_P521` |
| RSA-4096     | RSAES-OAEP-SHA-1         | ECC private key | `ECC_NIST_P256`, `ECC_NIST_P384`, `ECC_NIST_P521` |

| Wrapping key | Wrapping algorithm         | Key material    | Exact algorithm/spec |
| ------------ | -------------------------- | --------------- | -------------------- |
| RSA-4096     | `RSA_AES_KEY_WRAP_SHA_256` | RSA private key | `RSA_4096`           |
| RSA-4096     | `RSA_AES_KEY_WRAP_SHA_1`   | RSA private key | `RSA_4096`           |


| Wrapping key | Wrapping algorithm | Key material              | Exact key algorithms/specs covered |
| ------------ | ------------------ | ------------------------- | ---------------------------------- |
| RSA-4096     | RSAES-OAEP-SHA-256 | 256-bit AES symmetric key | `aes-256-gcm`                      |
| RSA-4096     | RSAES-OAEP-SHA-1   | 256-bit AES symmetric key | `aes-256-gcm`                      |

Unsupported - `aes-128-gcm` , `ML_DSA_44` , `ML_DSA_65` , `ML_DSA_87`

------------------------------
## GCP wrapping table
| Wrapping key | GCP import/wrapping algorithm  | Key material | Exact key algorithms/specs covered                                      |
| ------------ | ------------------------------ | ------------ | ----------------------------------------------------------------------- |
| RSA-4096     | `RSA_OAEP_4096_SHA256_AES_256` | HMAC key     | `HMAC_SHA1`, `HMAC_SHA224`, `HMAC_SHA256`, `HMAC_SHA384`, `HMAC_SHA512` |
| RSA-4096     | `RSA_OAEP_4096_SHA1_AES_256`   | HMAC key     | `HMAC_SHA1`, `HMAC_SHA224`, `HMAC_SHA256`, `HMAC_SHA384`, `HMAC_SHA512` |
| RSA-4096     | `RSA_OAEP_4096_SHA256`         | HMAC key     | `HMAC_SHA1`, `HMAC_SHA224`, `HMAC_SHA256`, `HMAC_SHA384`, `HMAC_SHA512` |


| Wrapping key | GCP import/wrapping algorithm  | Key material    | Exact key algorithms/specs covered           |
| ------------ | ------------------------------ | --------------- | -------------------------------------------- |
| RSA-4096     | `RSA_OAEP_4096_SHA256_AES_256` | ECC private key | `EC_SIGN_P256_SHA256`, `EC_SIGN_P384_SHA384` |
| RSA-4096     | `RSA_OAEP_4096_SHA1_AES_256`   | ECC private key | `EC_SIGN_P256_SHA256`, `EC_SIGN_P384_SHA384` |
| RSA-4096     | `RSA_OAEP_4096_SHA256`         | ECC private key | `EC_SIGN_P256_SHA256`, `EC_SIGN_P384_SHA384` |



--------------------
## AWS -> GCP wrapping algoritm map
| Generic wrapping algorithm | Exact GCP import method with RSA-4096 | Status / explanation                                                 |
| -------------------------- | ------------------------------------- | -------------------------------------------------------------------- |
| `RSAES-OAEP-SHA-256`       | `RSA_OAEP_4096_SHA256`                | Exact direct-OAEP equivalent                                         |
| `RSAES-OAEP-SHA-1`         | —                                     | **Missing in GCP.** GCP does not expose direct RSAES-OAEP with SHA-1 |
| `RSA-AES-KEY-WRAP-SHA-256` | `RSA_OAEP_4096_SHA256_AES_256`        | Exact hybrid equivalent: RSAES-OAEP-SHA-256 + AES-256-KWP            |
| `RSA-AES-KEY-WRAP-SHA-1`   | `RSA_OAEP_4096_SHA1_AES_256`          | Exact hybrid equivalent: RSAES-OAEP-SHA-1 + AES-256-KWP              |
| `RSAES-OAEP-SHA-256`       | `RSA_OAEP_4096_SHA256`                | Same as first row                                                    |
| `RSAES-OAEP-SHA-1`         | —                                     | **Missing in GCP**                                                   |
| `RSA_AES_KEY_WRAP_SHA_256` | `RSA_OAEP_4096_SHA256_AES_256`        | Same hybrid construction as `RSA-AES-KEY-WRAP-SHA-256`               |
| `RSA_AES_KEY_WRAP_SHA_1`   | `RSA_OAEP_4096_SHA1_AES_256`          | Same hybrid construction as `RSA-AES-KEY-WRAP-SHA-1`                 |


----------formals

`. PKCS #11
PKCS #11 exposes these through mechanisms such as:

CKM_AES_KEY_WRAP
CKM_AES_KEY_WRAP_KWP

2. RSAES-OAEP

PKCS #1 defines:
RFC 8017 / PKCS #1 v2.2 formally specifies RSAES-OAEP as an RSA encryption scheme, parameterized by a hash and MGF.


3. PKCS #11 3.2 specifies that it:
CKM_RSA_AES_KEY_WRAP
generates a temporary AES key;
wraps that AES key using CKM_RSA_PKCS_OAEP;
wraps the target key using CKM_AES_KEY_WRAP_KWP;
concatenates the two wrapped values;
destroys the temporary AES key.


---------------validations


  /***
   * propogate at router , the methods ,
   * and test via end to end scripts , cross test with aws ?
   */

  /***
   * validations
   * - rotations should still for 'pending_import' ,
   */

   import valid key with supported format other them der , or ber encdogin
     assert failure
   import key with no base64 encoding passed ,
     asset api failure
   import ecc_nis_p curve for rsa supplied key type
     assert failure
   for each of variant rsa , aes, hmac
    generate infisical key with expostable , export it
    import it via new endpoing ,
    perform encrypt,sign or other operations via origin insifical key
    perform validate operations via imported key
    asset success for all

   gather raw logs from api , docker , request , in any falure assetion and maitain them here in ./ files somehwere
   write test suite in backend e2e kms/ related

----------------------pr

Encrypt with PKCS#1 for secrets in transit during import ,
Implements protocol to harden import operation safety

Importing follow the standards of PKCS11 , PKSC1 for encrypting privatekeys in transit
RSA,ECC_NIST_P521 those with longer privatekeys support RSAES_OEAP_SHA* algorithms for wrapping of import operations

Symmetric,Assymetric,HMAC key types are supported

- Included FIPS PUB 186-4, validations of imported RSA keypair exponent's
- Storing with incremental verisioning to support on demand rotation for advancing to the external imported keypair
- table_schema , migrations are introduced
- tests conducted to validate introduced algorithms , permissions
- refractored frontend to include 'GenerateParamsForExport' method and new state condictional logics

# Backward compatability
As the new importable key type introduces a key without keymaterial to exist ,
- Included backward comptable api response for 'version' , 'encryptionAlgorithm' fields ,

# Usecase
  Introduces security for secrets not be exposed in payload , a common scenario when infisical is behind an L7 proxy
  that can view http payload

# Preview

# Screenshots
![create importable key](https://raw.githubusercontent.com/Shaik-Sirajuddin/infisical/docs-key-material-screenshots/backend/Screenshot%20from%202026-08-29%2010-41-14.png)
![get import params form](https://raw.githubusercontent.com/Shaik-Sirajuddin/infisical/docs-key-material-screenshots/backend/Screenshot%20from%202026-08-29%2010-41-57.png)
![pending import key type row](https://raw.githubusercontent.com/Shaik-Sirajuddin/infisical/docs-key-material-screenshots/backend/Screenshot%20from%202026-08-29%2010-42-56.png)
![key versions](https://raw.githubusercontent.com/Shaik-Sirajuddin/infisical/docs-key-material-screenshots/backend/Screenshot%20from%202026-08-29%2010-43-37.png)

Usecase and proposal at <full-reserach-doc> part of <reserach-doc-section>

## Coverage
Additional required changes
- Doc coverage
- Language SDK Update to include new api methods

# Note for maintainers
Pr has been handwritten , including all of backend , use of AI was restricted to parts of verifications , some of frontend changes

---------verification
[] origin enum checks and backfill previous row checks for migration schema
[] frontend check for tansack query cache optimization for key versions page
[] logical change of schema type binding in ts for the kmskeyversion table ,

- pending doc coverage

   -> rotate key
