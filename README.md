# DID-Method-Specification
# Abstract
SNPLab Decentralized Identity Service(SNPLab DID Service) provides the infrastructure for trusted digital identity service for SNPLab MyData Service(MyD).
SNPLab DID Service is based on blockchain(Hyperledger Indy) and provides a distributed mechanism for generating and verifying DID(Decentralized Identifier).

<br />


# Introduction
SNPLab is leading MyData service platform industry in South Korea, to empower individuals by improving their right to self-determination
regarding their personal data.
MyD is a personal data trading platform service provided by SNPLab and SNPLab DID Service provides the digital identity service for MyD service.
SNPLab DID Service provides a mechanism for the distributed and globally unique identifiers(DID, Decentralized Identifier).

<br>

# DID Method Syntax

## DID Naming Scheme.
A DID consists of three parts: 1) the `did` URI scheme identifier, 2) the identifier for the DID method, and 3) the DID method-specific identifier.<br>
The identifier of SNPLab DID method is `snplab`. Therefore, the SNPLab DID starts with `did:snplab:`.<br>
SNPLab DID Service uses Ed2559 crypto algorithm for DID owner’s private-public key pair.
The DID method-specific identifier will be the base54 value of the first 16 bytes of DID owner's Ed25519 public key.
```
The example of SNPLab DID is "did:snplab:V4SGRU86Z58d6TV7PBUe6f"
```

## DID Document Format

DID Document format example
```json
{
   "@context": "https://www.w3.org/ns/did/v1",
   "id": "did:snplab:V4SGRU86Z58d6TV7PBUe6f",
   "created": "2022-03-15 02:36:24",
   "verificationMethod": [
     {
        "id": "did:snplab:V4SGRU86Z58d6TV7PBUe6f#verkey",
        "type": "Ed25519VerificationKey2018",
        "publicKeyBase58": "GJ1SzoWzavQYfNL9XkaJdrQejfztN4XqdsiV4ct3LXKL",
        "controller": "did:snplab:V4SGRU86Z58d6TV7PBUe6f"
      }
   ],
   "updated": "2022-03-15 02:36:24",
   "authentication": [
       "did:snplab:V4SGRU86Z58d6TV7PBUe6f#verkey"
   ]
}
```


## CRUD Operations
CRUD operations store, read, update and delete the created DID in the blockchain.

### Getting Nonce
Nonce is used as a challenge in update and delete operations in order to verify that the DID owner really has the private key for the DID.<br>
Therefore, the procedure to get a nonce SHOULD be triggered before update and delete operations.

Nonce Request curl example
```bash
curl -X POST "https://did.operation.myd.world/account/nonce" \
     -H "accept: application/json" \
     -H "Content-Type: application/json" \
     -d "{\"did\": \"did:snplab:V4SGRU86Z58d6TV7PBUe6f\"}"
```
Nonce response example
```json
{"nonce":"17bf271c-4373-43b2-9eec-332505a262cd"}
```
<br>

### Create Operation
DID owner creates Ed2559 private-public key pair, and SNPLab DID from from the public key.<br>
DID owner prepare DID Document and request to register the DID and DID Document into SNPLab DID service by calling REST API(https://did.operation.myd.world/account/register).<br>
The REST API HTTP request contains DID Document in the request message body.<br>
SNPLab DID Service will trigger blockchain ledger transcation for the DID and DID Document registration.<br>
If there is no DID conflict in the blockchain and writing DID and DID Document into blockchain is succeed, SNPLab DID Service will return DID Document with created and updated time information.

DID Create(Registration) curl exmaple.
```bash
curl -X POST "https://did.operation.myd.world/account/register" \
     -H "accept: application/json" \
     -H "Content-Type: application/json" \
     -d "{ \"@context\": \"https://www.w3.org/ns/did/v1\", \
           \"id\": \"did:snplab:V4SGRU86Z58d6TV7PBUe6f\", \
           \"verificationMethod\": [ { \"id\": \"did:snplab:V4SGRU86Z58d6TV7PBUe6f#verkey\", \
                                       \"type\": \"Ed25519VerificationKey2018\", \
                                       \"controller\": \"did:snplab:V4SGRU86Z58d6TV7PBUe6f\", \
                                       \"publicKeyBase58\": \"GJ1SzoWzavQYfNL9XkaJdrQejfztN4XqdsiV4ct3LXKL\" } ], \
           \"authentication\": [ \"did:snplab:V4SGRU86Z58d6TV7PBUe6f#verkey\"]}"
```

Create operation response example.
```json
{
   "@context": "https://www.w3.org/ns/did/v1",
   "id": "did:snplab:V4SGRU86Z58d6TV7PBUe6f",
   "created": "2022-03-15 02:36:24",
   "verificationMethod": [
     {
        "id": "did:snplab:V4SGRU86Z58d6TV7PBUe6f#verkey",
        "type": "Ed25519VerificationKey2018",
        "publicKeyBase58": "GJ1SzoWzavQYfNL9XkaJdrQejfztN4XqdsiV4ct3LXKL",
        "controller": "did:snplab:V4SGRU86Z58d6TV7PBUe6f"
      }
   ],
   "updated": "2022-03-15 02:36:24",
   "authentication": [
       "did:snplab:V4SGRU86Z58d6TV7PBUe6f#verkey"
   ]
}
```
<br>

### Read Operation
Registered DID Document can be accessible by calling read REST API(https://did.operation.myd.world/account/document).<br>
The HTTP request contains DID in the request message body.

DID Read curl example
```bash
curl -X POST "https://did.operation.myd.world/account/document" \
     -H "accept: application/json" \
     -H "Content-Type: application/json" \
     -d "{\"did\": \"did:snplab:V4SGRU86Z58d6TV7PBUe6f\"}"
```

Read operation response example.
```json
{
   "@context": "https://www.w3.org/ns/did/v1",
   "id": "did:snplab:V4SGRU86Z58d6TV7PBUe6f",
   "created": "2022-03-15 02:36:24",
   "verificationMethod": [
     {
        "id": "did:snplab:V4SGRU86Z58d6TV7PBUe6f#verkey",
        "type": "Ed25519VerificationKey2018",
        "publicKeyBase58": "GJ1SzoWzavQYfNL9XkaJdrQejfztN4XqdsiV4ct3LXKL",
        "controller": "did:snplab:V4SGRU86Z58d6TV7PBUe6f"
      }
   ],
   "updated": "2022-03-15 02:36:24",
   "authentication": [
       "did:snplab:V4SGRU86Z58d6TV7PBUe6f#verkey"
   ]
}
```
<br>

### Update Operation
DID owner can update the public key with new public key by proving out that the DID owner has the private key for the public key.<br>
DID owner get a nonce from SNPLab DID Service as a chanllenge and generate the signature of the nonce with the private key in order to prove out that the DID owner really has the private key.<br>
New public key can be updated by calling update REST API(https://did.operation.myd.world/account/update).<br>
The HTTP request contains the signature of nonce in the request message header.<br>
The HTTP request contains new DID Document which has new public key in the request message body.<br>

Update operation curl example
```bash
curl -X POST "https://did.operation.myd.world/account/update" \
     -H "accept: application/json" \
     -H "Content-Type: application/json" \
     -H "Signature: pV6X93hyXpu1A9Fjhdmj3unacLn0FGfCUzwnoDPIhHF6D8lsP+nSbmuMtkXx1FkUXeDIm3ZPhzUtkJlCHpihADE3YmYyNzFjLTQzNzMtNDNiMi05ZWVjLTMzMjUwNWEyNjJjZA==" \
     -d "{ "id":"did:snplab:V4SGRU86Z58d6TV7PBUe6f", \
           "verificationMethod":[{"publicKeyBase58":"5Pw9ffk5qC8sXaJq4G6YAMJ5HXgDe7qtXD2TGB2e7DSB", \
                                 "controller":"did:snplab:V4SGRU86Z58d6TV7PBUe6f", \
                                 "id":"did:snplab:V4SGRU86Z58d6TV7PBUe6f#verkey"\ ,
                                 "type":"Ed25519VerificationKey2018"}],
           "@context":"https://www.w3.org/ns/did/v1", \
           "authentication":["did:snplab:V4SGRU86Z58d6TV7PBUe6f#verkey"]}"
 ```


Update operation response example.
```json
{
   "@context": "https://www.w3.org/ns/did/v1",
   "id": "did:snplab:V4SGRU86Z58d6TV7PBUe6f",
   "created": "2022-03-15 02:36:24",
   "verificationMethod": [
     {
        "id": "did:snplab:V4SGRU86Z58d6TV7PBUe6f#verkey",
        "type": "Ed25519VerificationKey2018",
        "publicKeyBase58": "5Pw9ffk5qC8sXaJq4G6YAMJ5HXgDe7qtXD2TGB2e7DSB",
        "controller": "did:snplab:V4SGRU86Z58d6TV7PBUe6f"
      }
   ],
   "updated": "2022-03-15 02:38:51",
   "authentication": [
       "did:snplab:V4SGRU86Z58d6TV7PBUe6f#verkey"
   ]
}
```
<br>

### Delete Operation
DID owner can disable DID and DID Document by proving out that the DID owner has the private key for the public key.<br>
DID owner get a nonce from SNPLab DID Service as a chanllenge and generate the signature of the nonce with the private key in order to prove out that the DID owner really has the private key.<br>
DID and DID Document can be disabled by calling delete REST API(https://did.operation.myd.world/account/breakaway).<br>
The HTTP request contains the signature of nonce in the request message header.<br>
The HTTP request contains new DID in the request message body.<br>

Delete operation example
```bash
curl -X POST "https://did.operation.myd.world/account/breakaway" \
     -H "accept: application/json" \
     -H "Content-Type: application/json" \
     -H "Signature: pV6X93hyXpu1A9Fjhdmj3unacLn0FGfCUzwnoDPIhHF6D8lsP+nSbmuMtkXx1FkUXeDIm3ZPhzUtkJlCHpihADE3YmYyNzFjLTQzNzMtNDNiMi05ZWVjLTMzMjUwNWEyNjJjZA==" \
     -d "{ \"did\": \"did:snplab:V4SGRU86Z58d6TV7PBUe6f\"}"
```

Delete operation response example.
```json
{"breakaway":"complete","did":"did:snplab:V4SGRU86Z58d6TV7PBUe6f"}
```
<br>

## Security Considerations
1. All communication between DID owner and SNPLab DID Service use TLS 1.2 and higher version with strong cipher suit.
2. SNPLab DID service uses a permissioned, distributed ledger (Hyperledger Indy) to mitigate attacks like eavesdropping, replay, message insertion, deletion, modification, impersonation, and man-in-the-middle attacks.
3. CRUD operations require for MyD application to have the access information to SNPLab DID service network. The access information wil be acquired after device attestation which MyD application is installed.
4. DID Document, private key and public key are created by DID owner and SNPLab DID service can have only public information(DID Document and public key).
5. The DID identifier of the SNPLab DID Service is generated by DID owner with the public key, so it supports the DID owner's own private key.
<br>


## Privacy Considerations
1. The DID Document of DID does not contain any personal identifiable information (PII).
2. CRUD operation requests does not contain any personal identifiable information (PII). 
3. SNPLab DID service blockchain does not store any personal identifiable information (PII) or credentials.
