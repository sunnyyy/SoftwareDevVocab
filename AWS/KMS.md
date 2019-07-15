# Key Management Service (KMS)

## Quick facts
- easy to create / control encryption keys
- integrated with EBS, S3, Redshift, Elastic Transcoder, WorkMail, RDS, etc.
- to find tool, go to IAM --> assign user to group --> ???
- good policy to have 1 person to manage keys + 1 person with permission to encrypt / decrypt
- encryption keys are regional

## Customer Master Key (CMK)
- alias
- creation date
- description
- key state
- key material --> this can be provided by you or AWS
- can __never__ be exported! --> if you need to export your keys, use Cloud HSM

### To set up a CMK...
- enter alias + description
- choose material option
- define key's __administrative permissions__ -- IAM users / roles that can admin the key
- define key's __usage permissions__ -- IAM users / roles that can use the key to encrypt / decrypt data

# [NOTES FROM DEMO GO HERE]

## KMS Envelope Encryption

### Encryption
__master key__ --> _encrypts_ --> __envelope key (data key)__ --> _encrypts_ --> __data__

### Decryption
__encrypted data key__ + __master key__ + __encryption (??) algorithm__ --> _decrypts_ --> __plain text data key__ --> __decrypted data__
