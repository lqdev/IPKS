# IPKS Credential Verification Specification
**Draft Version 0.1**

## Abstract

This specification defines the credential verification framework for the InterPlanetary Knowledge System (IPKS). It details how Verifiable Credentials are integrated with Knowledge Nodes to establish trust, validate expertise, and verify claims within the distributed knowledge network. This specification enables a decentralized trust model for knowledge contributions while supporting granular permissions, reputation management, and domain-specific validation.

## Status of This Document

This document is a draft specification. It is intended to provide a foundation for implementation and further refinement through community input and experimental deployments.

## 1. Introduction

### 1.1 Motivation

Trust is fundamental to knowledge systems. While the IPKS Identity specification establishes authorship, additional mechanisms are needed to verify claims about expertise, qualifications, affiliations, and permissions. Verifiable Credentials provide a standardized way to make cryptographically verifiable claims about identities in a privacy-preserving manner.

### 1.2 Relationship to Existing Technologies

This specification builds upon:
- W3C Verifiable Credentials Data Model
- W3C Decentralized Identifiers (DIDs)
- IPKS Core Knowledge Model
- IPKS Identity & Authorship Specification
- BBS+ Signatures and Zero-Knowledge Proofs
- JSON-LD Credentials

### 1.3 Design Goals

- Enable cryptographic verification of claims about knowledge contributors
- Support selective disclosure of credential information
- Allow domain-specific credential schemas for specialized knowledge areas
- Provide mechanisms for credential revocation and expiration
- Enable reputation and trust frameworks based on verified credentials
- Support both human and machine verification of credentials

## 2. Core Concepts

### 2.1 Verifiable Credential

A Verifiable Credential is a set of tamper-evident claims made by an issuer about a subject that can be cryptographically verified.

### 2.2 Credential Schema

A Credential Schema defines the structure and constraints for a specific type of credential, including required and optional fields.

### 2.3 Credential Presentation

A Credential Presentation is a derived format of one or more credentials, potentially with selective disclosure, optimized for verification in a specific context.

### 2.4 Trust Registry

A Trust Registry is a system that maintains information about trusted credential issuers and their authorized credential types.

## 3. Credential Integration

### 3.1 Credential Attachment

Knowledge Nodes MAY include or reference Verifiable Credentials:

```json
"credentials": [
  {
    "type": ["VerifiableCredential", "ExpertiseCredential"],
    "issuer": "did:example:issuer",
    "issuanceDate": "ISO8601-timestamp",
    "expirationDate": "ISO8601-timestamp",
    "credentialSubject": {
      "id": "did:example:contributor",
      "expertise": "domain-specific-expertise-URI",
      "level": "expert"
    },
    "proof": {
      /* Credential proof */
    }
  }
]
```

### 3.2 Credential Reference

For large credentials or when privacy is a concern, Knowledge Nodes MAY reference credentials instead of embedding them:

```json
"credentialReferences": [
  {
    "id": "credential-CID-or-URI",
    "type": ["VerifiableCredential", "ExpertiseCredential"],
    "issuer": "did:example:issuer",
    "holder": "did:example:contributor"
  }
]
```

### 3.3 Credential Schemas

Implementations MUST support at minimum these credential schemas:
- ExpertiseCredential - Claims about domain expertise
- AffiliationCredential - Claims about organizational affiliation
- PermissionCredential - Claims about access rights
- ContributorTrustCredential - Claims about contributor reputation

## 4. Credential Verification

### 4.1 Verification Process

Verifying a credential attached to a Knowledge Node requires:
1. Validating the credential structure against its schema
2. Resolving the issuer's DID to obtain verification keys
3. Verifying the credential's cryptographic proof
4. Checking revocation status
5. Validating that the credential has not expired

### 4.2 Verification Results

Verification results MUST include:
- Overall verification status (valid/invalid)
- Timestamp of verification
- Verification method used
- Specific validation results for each check

### 4.3 Verification Caching

Implementations MAY cache verification results with appropriate time limits to improve performance while maintaining security.

## 5. Credential Models

### 5.1 Domain Expertise

Credentials that verify a contributor's expertise in a specific knowledge domain:

```json
{
  "type": ["VerifiableCredential", "ExpertiseCredential"],
  "credentialSubject": {
    "id": "did:example:contributor",
    "knowledgeDomain": "domain-URI",
    "expertiseLevel": "level-URI",
    "assessmentMethod": "assessment-URI",
    "evidenceReferences": ["reference-URIs"]
  }
}
```

### 5.2 Academic Credentials

Credentials representing academic qualifications:

```json
{
  "type": ["VerifiableCredential", "AcademicCredential"],
  "credentialSubject": {
    "id": "did:example:contributor",
    "degree": "degree-URI",
    "institution": "institution-DID-or-URI",
    "fieldOfStudy": "field-URI",
    "awardDate": "ISO8601-date"
  }
}
```

### 5.3 Organizational Affiliation

Credentials verifying organizational membership or employment:

```json
{
  "type": ["VerifiableCredential", "AffiliationCredential"],
  "credentialSubject": {
    "id": "did:example:contributor",
    "organization": "organization-DID-or-URI",
    "role": "role-URI",
    "startDate": "ISO8601-date",
    "endDate": "ISO8601-date" // Optional for current affiliations
  }
}
```

### 5.4 Contribution Rights

Credentials granting specific rights within the knowledge system:

```json
{
  "type": ["VerifiableCredential", "ContributionRightsCredential"],
  "credentialSubject": {
    "id": "did:example:contributor",
    "permissions": ["permission-URIs"],
    "knowledgeDomains": ["domain-URIs"],
    "constraints": {
      /* Domain-specific constraints */
    }
  }
}
```

## 6. Trust Frameworks

### 6.1 Issuer Trust

Implementations SHOULD provide mechanisms to establish trust in credential issuers:
- Well-known trusted issuers for specific credential types
- Web-of-trust models for issuer validation
- Domain-specific trust registries

### 6.2 Trust Levels

Knowledge systems MAY define trust levels based on credential verification:
- Unverified - No credentials verified
- Basic - Identity credentials verified
- Standard - Domain expertise credentials verified
- Expert - Multiple high-quality credentials from trusted issuers

### 6.3 Trust Propagation

Trust MAY propagate through the knowledge graph:
- Knowledge Nodes authored by highly trusted contributors inherit trust
- Relationships established by trusted contributors carry higher confidence
- Cross-domain knowledge requires domain-specific credential verification

## 7. Credential Privacy

### 7.1 Selective Disclosure

Implementations SHOULD support selective disclosure of credential attributes:
- Zero-knowledge proofs for validating claims without revealing values
- Derived credentials that reveal only necessary information
- Minimized credential data in public Knowledge Nodes

### 7.2 Blinded Credentials

For sensitive knowledge domains, implementations MAY support blinded credentials:
- Pseudonymous identifiers that cannot be linked to the contributor's primary DID
- Proofs of credential possession without revealing the credential itself
- Anonymous credentials that verify claims without identifying the holder

### 7.3 Credential Minimization

Contributors SHOULD only attach credentials directly relevant to the Knowledge Node:
- Expertise credentials relevant to the knowledge domain
- Organizational affiliations when representing an organization
- Permission credentials when required for specific operations

## 8. Credential Operations

### 8.1 Issuance

The process for issuing credentials for use in IPKS:
1. Issuer validates the subject's claims through appropriate means
2. Issuer creates credential with appropriate claims and expiration
3. Issuer signs credential using their DID's verification method
4. Credential is delivered to the subject via secure channel
5. Subject can now attach credential to Knowledge Nodes

### 8.2 Revocation

Credentials MUST support revocation:
- Status list based revocation
- Smart contract revocation registries
- Timestamps for status list retrieval

### 8.3 Expiration and Renewal

Credentials SHOULD include explicit expiration:
- All credentials MUST have an expirationDate field
- Implementations SHOULD check expiration before validation
- Renewal processes SHOULD be defined for important credentials

## 9. Implementation Guidance

### 9.1 Credential Storage

Implementations SHOULD:
- Store credentials securely
- Support credential backup and recovery
- Enable selective sharing of credentials

### 9.2 Validation Performance

For efficient credential validation:
- Implement caching of validation results with appropriate TTL
- Use optimized validation libraries
- Implement batch validation for multiple credentials

### 9.3 Credential Discovery

Implementations SHOULD support mechanisms to:
- Discover relevant credentials for specific knowledge domains
- Identify trusted issuers for required credentials
- Match credential requirements with available credentials

## 10. Security Considerations

### 10.1 Credential Forgery Prevention

Implementations MUST:
- Validate credential proofs cryptographically
- Check issuer against trusted issuer registries
- Verify credential has not been tampered with

### 10.2 Replay Attack Prevention

To prevent reuse of credentials in unauthorized contexts:
- Nonce or challenge-response protocols SHOULD be used
- Credential presentations SHOULD be context-specific
- Timestamps SHOULD be validated

### 10.3 Key Compromise Mitigation

In case of key compromise:
- Credential revocation mechanisms MUST be robust
- Key rotation procedures MUST be defined
- Implementations SHOULD check DID Documents for key status

## 11. References

- W3C Verifiable Credentials Data Model
- W3C Decentralized Identifiers (DID) specification
- IPKS Core Knowledge Model specification
- IPKS Identity & Authorship specification
- Zero-Knowledge Proof specifications
- JSON-LD Credentials and Proofs

## Appendix A: Standard Credential Types

[List of standard credential types and their schemas]

## Appendix B: Revocation Methods

[Detailed specifications for supported revocation methods]
