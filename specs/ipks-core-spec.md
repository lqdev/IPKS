# IPKS Core Knowledge Model Specification
**Draft Version 0.1**

## Abstract

This specification defines the InterPlanetary Knowledge System (IPKS) Core Knowledge Model, a content-addressable, semantically-rich data model for representing distributed knowledge. Building upon IPFS and IPLD, this specification extends content-addressing to support verifiable, evolvable, and interconnected knowledge representations. The Core Knowledge Model defines how knowledge is structured, addressed, linked, and versioned across distributed systems.

## Status of This Document

This document is a draft specification. It is intended to provide a foundation for implementation and further refinement through community input and experimental deployments.

## 1. Introduction

### 1.1 Motivation

Current distributed data systems primarily focus on content addressing for files and basic data structures. While IPLD provides a framework for linking data structures, a higher-level abstraction is needed to represent knowledge with semantic meaning, provenance, verification, and evolution characteristics.

### 1.2 Relationship to Existing Technologies

The IPKS Core Knowledge Model builds upon:
- IPFS (InterPlanetary File System) for distributed storage
- IPLD (InterPlanetary Linked Data) for content-addressing and data structure representation
- JSON-LD patterns for semantic data representation
- Merkle DAGs for verifiable data structures

### 1.3 Design Goals

- Enable knowledge to be addressed, verified, and retrieved across distributed systems
- Support semantic relationships between knowledge entities
- Maintain cryptographic verification of knowledge provenance
- Allow knowledge to evolve while preserving history
- Support both human and machine interaction with the knowledge graph
- Enable cross-domain knowledge interoperability

## 2. Core Concepts

### 2.1 Knowledge Node

A Knowledge Node is the foundational unit in IPKS. Each node represents a discrete unit of knowledge with:
- Content (the knowledge itself in various formats)
- Metadata (information about the knowledge)
- Relationships (semantic links to other knowledge nodes)
- Provenance (origin and evolution information)

### 2.2 Knowledge CID (K-CID)

Knowledge CIDs extend the IPLD CID (Content Identifier) concept to include:
- Content hash of the knowledge node data
- Codec information for the knowledge node format
- Version information
- Optional schema reference

### 2.3 Knowledge Links

Knowledge Links are semantic connections between Knowledge Nodes, characterized by:
- Source Knowledge Node K-CID
- Target Knowledge Node K-CID
- Relationship type (defined by URI to semantic definition)
- Confidence score (optional)
- Provenance metadata

### 2.4 Knowledge Block

A Knowledge Block is the serialized representation of a Knowledge Node in a specific format (e.g., CBOR, JSON) that can be stored and retrieved in a content-addressable system.

## 3. Knowledge Node Structure

### 3.1 Required Fields

Every Knowledge Node MUST include:

```json
{
  "content": {
    /* The actual knowledge content */
  },
  "contentType": "string", /* MIME type or format identifier */
  "created": "ISO8601-timestamp",
  "kcid": "K-CID-string", /* Self-referential after creation */
  "relationships": [] /* Can be empty but must be present */
}
```

### 3.2 Optional Fields

Knowledge Nodes MAY include:

```json
{
  "schema": "URI-for-schema",
  "context": "URI-for-context", /* For semantic interpretation */
  "contributors": ["list-of-DIDs"],
  "confidence": 0.0-1.0, /* Knowledge confidence score */
  "modified": "ISO8601-timestamp",
  "previousVersions": ["list-of-K-CIDs"],
  "expirationDate": "ISO8601-timestamp",
  "access": {}, /* Access control information */
  "credentials": [] /* Associated verifiable credentials */
}
```

### 3.3 Relationship Structure

Each relationship MUST include:

```json
{
  "target": "target-K-CID",
  "relationshipType": "relationship-URI",
  "created": "ISO8601-timestamp",
  "creator": "DID-of-relationship-creator"
}
```

And MAY include:

```json
{
  "confidence": 0.0-1.0,
  "evidence": ["list-of-K-CIDs-as-evidence"],
  "context": "URI-for-relationship-context",
  "metadata": {} /* Additional relationship metadata */
}
```

## 4. Knowledge CID (K-CID) Format

### 4.1 Structure

A K-CID extends the IPLD CID format with:
- Multibase prefix (as in standard CIDs)
- Version identifier (as in standard CIDs)
- Content type codec (using registered IPLD codecs)
- Multihash of content (as in standard CIDs)
- Knowledge version suffix (specific to K-CIDs)

### 4.2 Versioning

Knowledge Nodes can evolve while maintaining relationships to previous versions:
- When content changes, a new K-CID is generated
- Previous K-CID is stored in `previousVersions` array
- Relationships to other Knowledge Nodes can specify which version they reference

### 4.3 Resolution

K-CID resolution MUST follow these rules:
- Latest version is returned unless specifically requested otherwise
- Version history must be traversable
- Content addressing ensures integrity of the knowledge node

## 5. Knowledge Serialization

### 5.1 Canonical Serialization

For consistent content addressing, Knowledge Nodes MUST be serialized in a canonical format:
- Deterministic ordering of fields
- Consistent handling of numeric values
- Normalized string representations

### 5.2 Supported Codecs

Initial supported codecs for Knowledge Nodes include:
- DAG-CBOR (preferred for machine interchange)
- DAG-JSON (preferred for human readability/debugging)

### 5.3 Schema Validation

Knowledge Nodes MAY reference schemas for validation:
- JSON Schema for structure validation
- SHACL for semantic validation
- Custom schemas for domain-specific validation

## 6. Knowledge Node Operations

### 6.1 Creation

Creation of a Knowledge Node involves:
1. Assembling the Knowledge Node structure
2. Validating against specified schema (if applicable)
3. Serializing in canonical format
4. Computing the K-CID
5. Adding the K-CID to the Knowledge Node
6. Storing the Knowledge Node in a content-addressable system

### 6.2 Retrieval

Retrieval operations involve:
1. Looking up the K-CID in a content-addressable system
2. Deserializing the Knowledge Block into a Knowledge Node
3. Optional validation against schema
4. Resolution of referenced Knowledge Nodes (if requested)

### 6.3 Updating

Updates to Knowledge Nodes create new versions:
1. Retrieve existing Knowledge Node
2. Modify content or metadata
3. Record previous K-CID in `previousVersions` array
4. Update modification timestamp
5. Re-serialize and compute new K-CID
6. Store as new Knowledge Node

### 6.4 Relationship Management

Managing relationships between Knowledge Nodes:
1. Create relationship with appropriate semantic type
2. Store relationship in source Knowledge Node
3. Update source Knowledge Node following update procedure
4. Optionally register relationship in target Knowledge Node

## 7. Knowledge Graph Navigation

### 7.1 Traversal

Knowledge Graphs can be traversed by:
- Following explicit relationships between Knowledge Nodes
- Querying for Knowledge Nodes with specific properties
- Traversing version history of Knowledge Nodes

### 7.2 Query Patterns

Basic query patterns include:
- K-CID direct lookup
- Content-based search
- Relationship traversal
- Semantic query (requires semantic layer implementation)

## 8. Security Considerations

### 8.1 Content Integrity

- K-CIDs ensure content integrity through cryptographic hashing
- Applications MUST verify K-CID matches content

### 8.2 Trust Considerations

- Knowledge Nodes provide structure but do not inherently provide trust
- Trust is established through the Identity and Credentials layers
- Applications SHOULD verify provenance before trusting Knowledge Node content

### 8.3 Privacy Considerations

- Public content-addressable systems make Knowledge Nodes broadly available
- Sensitive knowledge SHOULD be encrypted
- Access control is defined in separate specifications

## 9. Implementation Guidance

### 9.1 Minimum Requirements

Conforming implementations MUST support:
- Knowledge Node structure validation
- K-CID generation and resolution
- Canonical serialization
- Basic relationship management

### 9.2 Optional Features

Implementations MAY support:
- Schema validation
- Complex relationship traversal
- Version history management
- Extended metadata

## 10. References

- IPFS Specifications
- IPLD Data Model
- Decentralized Identifiers (DIDs)
- JSON-LD
- RDF
- Multiformats

## Appendix A: Examples

[Example Knowledge Nodes and operations to be provided]

## Appendix B: JSON Schema for Knowledge Nodes

[JSON Schema definition for Knowledge Node validation]
