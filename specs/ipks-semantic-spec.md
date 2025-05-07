# IPKS Semantic Relationship Specification
**Draft Version 0.1**

## Abstract

This specification defines the semantic relationship framework for the InterPlanetary Knowledge System (IPKS). It details how Knowledge Nodes establish meaningful, machine-readable connections through semantic relationships, enabling rich knowledge representation and traversal. Building upon established semantic web standards, this specification bridges content-addressed storage with semantic meaning to create a globally navigable knowledge graph.

## Status of This Document

This document is a draft specification. It is intended to provide a foundation for implementation and further refinement through community input and experimental deployments.

## 1. Introduction

### 1.1 Motivation

Content addressing enables verification and retrieval of knowledge, but meaning emerges from the relationships between knowledge entities. IPKS requires a standardized approach to expressing semantic relationships between Knowledge Nodes that enables both human and machine understanding of how discrete knowledge units connect into meaningful knowledge graphs.

### 1.2 Relationship to Existing Technologies

This specification builds upon:
- Resource Description Framework (RDF)
- Web Ontology Language (OWL)
- JSON-LD
- IPKS Core Knowledge Model
- IPLD
- Schema.org vocabularies

### 1.3 Design Goals

- Enable meaningful, machine-readable relationships between Knowledge Nodes
- Support integration with existing semantic web standards and vocabularies
- Allow domain-specific relationship types and ontologies
- Provide mechanisms for relationship confidence and provenance
- Enable semantic querying across the knowledge graph
- Support both simple and complex knowledge structures

## 2. Core Concepts

### 2.1 Semantic Relationship

A Semantic Relationship is a typed, directional connection between two Knowledge Nodes that expresses a specific meaning about how they relate.

### 2.2 Relationship Type

A Relationship Type defines the semantic meaning of a relationship using a URI that references an ontology term.

### 2.3 Ontology

An Ontology is a formal specification of concepts and relationships within a domain, providing the vocabulary for relationship types.

### 2.4 Knowledge Graph

A Knowledge Graph is the network of Knowledge Nodes connected by Semantic Relationships, forming a navigable representation of knowledge.

## 3. Relationship Representation

### 3.1 Basic Structure

Every Semantic Relationship MUST include:

```json
{
  "source": "source-K-CID",
  "target": "target-K-CID",
  "type": "relationship-type-URI",
  "created": "ISO8601-timestamp",
  "creator": "creator-DID"
}
```

### 3.2 Extended Attributes

Semantic Relationships MAY include:

```json
{
  "confidence": 0.0-1.0,
  "bidirectional": boolean,
  "inverseType": "inverse-relationship-type-URI",
  "qualifiers": {
    /* Type-specific qualifiers */
  },
  "evidence": ["evidence-K-CIDs"],
  "provenance": {
    /* Detailed provenance information */
  }
}
```

### 3.3 Relationship Storage

Relationships are stored in two forms:
1. **Embedded** - Within the source Knowledge Node's relationships array
2. **Independent** - As standalone Relationship Objects with their own K-CIDs

Relationship storage MUST adhere to these rules:
- Every relationship MUST be stored in at least the source Knowledge Node
- Relationships MAY be stored in both source and target Knowledge Nodes
- High-reuse relationships SHOULD be stored as independent objects

## 4. Relationship Types

### 4.1 Core Relationship Types

IPKS defines a core set of relationship types that MUST be supported:

| Relationship Type | URI | Description |
|-------------------|-----|-------------|
| isRelatedTo | ipks:relationship/isRelatedTo | Generic relationship (parent of all others) |
| references | ipks:relationship/references | Source cites or refers to target |
| isVersionOf | ipks:relationship/isVersionOf | Source is a version of target |
| includes | ipks:relationship/includes | Source contains target as a component |
| isBasedOn | ipks:relationship/isBasedOn | Source derives from target |
| contradicts | ipks:relationship/contradicts | Source presents contrary information to target |
| supports | ipks:relationship/supports | Source provides evidence supporting target |
| extends | ipks:relationship/extends | Source adds information to target |

### 4.2 Domain-Specific Types

Implementation SHOULD support domain-specific relationship types:
- Scientific: ipks:relationship/scientific/refutes, ipks:relationship/scientific/replicates
- Business: ipks:relationship/business/regulates, ipks:relationship/business/supplyChainLink
- Educational: ipks:relationship/education/prerequisiteFor, ipks:relationship/education/teachingResource

### 4.3 External Vocabularies

Implementations SHOULD support relationships from established vocabularies:
- Schema.org relationships
- Dublin Core relationship terms
- SKOS semantic relations
- Domain-specific ontologies

## 5. Ontology Integration

### 5.1 Ontology Registration

IPKS supports registering ontologies for semantic relationships:
- Ontologies themselves are stored as Knowledge Nodes
- Ontology K-CIDs are used as namespace references
- Standard and custom ontologies can be registered

### 5.2 Ontology Format

Supported ontology formats include:
- OWL (Web Ontology Language)
- RDFS (RDF Schema)
- SKOS (Simple Knowledge Organization System)

### 5.3 Ontology References

Relationship types reference ontology terms through URIs:
- `ipks:` namespace for core IPKS relationships
- Standard namespace prefixes for common ontologies
- Custom namespace declarations for domain-specific ontologies

## 6. Relationship Confidence

### 6.1 Confidence Model

IPKS supports expressing confidence in relationships:
- Numeric confidence values between 0.0 and 1.0
- 0.0 represents speculative relationships
- 1.0 represents definitively verified relationships

### 6.2 Confidence Computation

Confidence may be computed based on:
- Direct assertion by the relationship creator
- Verification status of contributing credentials
- Algorithmic inference based on evidence
- Consensus across multiple contributors

### 6.3 Confidence Propagation

When navigating relationships, confidence propagates:
- Sequential relationships multiply confidence values
- Parallel confirming relationships increase confidence
- Contradictory relationships decrease confidence

## 7. Relationship Queries

### 7.1 Basic Traversal Queries

Implementations MUST support basic queries:
- Find all relationships for a Knowledge Node
- Find all Knowledge Nodes with a specific relationship type
- Follow a relationship chain with a maximum depth

### 7.2 Semantic Queries

Implementations SHOULD support semantic queries:
- SPARQL-compatible graph pattern matching
- Relationship type hierarchy traversal
- Inverse relationship resolution

### 7.3 Multi-hop Queries

Complex knowledge discovery requires multi-hop queries:
- Path finding between Knowledge Nodes
- Pattern matching across relationship chains
- Filtering by relationship types and attributes

## 8. Relationship Provenance

### 8.1 Relationship Authorship

Every relationship MUST have an authorship track:
- DID of the relationship creator
- Creation timestamp
- Optional creation context

### 8.2 Evidence Links

Relationships MAY provide evidence through:
- Links to supporting Knowledge Nodes
- References to external evidence
- Logical derivation explanations

### 8.3 Provenance Verification

Implementations SHOULD verify:
- Relationship creator has appropriate credentials
- Evidence supports the relationship assertion
- Relationship creation followed valid protocols

## 9. Semantic Transformation

### 9.1 RDF Compatibility

IPKS relationships MUST be convertible to RDF triples:
- Knowledge Node K-CIDs map to RDF subjects and objects
- Relationship types map to RDF predicates
- Qualifiers map to named graphs or reified statements

### 9.2 Linked Data Export

Implementations SHOULD support exporting relationships:
- JSON-LD format with appropriate context
- RDF serialization formats (Turtle, N-Triples)
- Schema.org compatible markup

### 9.3 External Query Integration

For compatibility with existing semantic systems:
- SPARQL endpoint capabilities SHOULD be supported
- GraphQL interfaces MAY be provided
- RDF dataset dumps MAY be generated

## 10. Implementation Guidance

### 10.1 Storage Strategies

For efficient relationship management:
- High-fan-out Knowledge Nodes should use relationship pagination
- Frequently reused relationships should be stored independently
- Implementation-specific indices should optimize common traversals

### 10.2 Relationship Validation

Implementations SHOULD validate relationships:
- Relationship types MUST be valid URIs
- Referenced Knowledge Nodes MUST exist
- Domain and range constraints from ontologies SHOULD be enforced

### 10.3 Performance Considerations

For implementations at scale:
- Materialize frequently traversed paths
- Cache popular relationship queries
- Distribute relationship storage by domain

## 11. Security Considerations

### 11.1 Relationship Integrity

Implementations MUST ensure:
- Relationships cannot be forged
- Relationship metadata cannot be tampered with
- Deleted relationships leave auditable traces

### 11.2 Access Control

When relationships involve private knowledge:
- Relationship existence MAY be public while endpoints are private
- Relationships MAY be encrypted for specific audiences
- Relationship metadata MAY reveal different information to different parties

### 11.3 Inference Controls

To prevent unauthorized information disclosure:
- Implementations SHOULD control inference depth on sensitive knowledge
- Query rate limiting SHOULD be implemented
- Pattern-based restrictions MAY be applied to sensitive domains

## 12. References

- Resource Description Framework (RDF)
- Web Ontology Language (OWL)
- JSON-LD Specification
- IPKS Core Knowledge Model Specification
- IPKS Identity & Authorship Specification
- Schema.org
- Dublin Core Metadata Initiative

## Appendix A: Core Relationship Type Ontology

[Formal definition of core relationship types in OWL]

## Appendix B: Example Relationship Queries

[SPARQL and native IPKS query examples]
