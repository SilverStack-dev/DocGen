# Proof of Concept Spikes

This document summarises the proof-of-concept investigations required to validate the proposed documentation architecture.

The objective of each spike is to test a specific architectural assumption before committing to implementation.

---

# Spike 1 - Concept-Based Documentation

## Objective

Determine whether concept-based documentation produces more useful documentation than traditional API reference documentation.

## Background

Traditional API documentation is typically organised as:

```text
Class
├─ Properties
└─ Methods
```

The proposed model organises documentation around concepts:

```text
Breadcrumb

Overview

Creating Breadcrumbs

Adding Pages

Managing Entries

Navigation Helpers

Other Members
```

## Investigation

Create a prototype using:

- XML member documentation.
- Class-level concept declarations.
- External concept documents.

The prototype should demonstrate:

- Concepts containing zero or more member references.
- Automatic generation of concept sections.
- Automatic placement of ungrouped members.
- Multiple concept membership.
- Separation of conceptual and API documentation.

Example:

```csharp
/// <summary>
/// Represents a breadcrumb trail.
/// </summary>
/// <concept file="AddPages" />
/// <concept file="ManagePages" />
public class Breadcrumb
{
}
```

## Deliverable

Generate documentation for the Breadcrumb class using:

- XML comments for API documentation.
- Concept documents for conceptual documentation.
- Automated merging of the two documentation sources.

The resulting document should resemble the manually authored Breadcrumb documentation while eliminating duplication between concept documentation and member documentation.


## Success Criteria

A generated document can be produced in which concept introductions are authored separately from member documentation, while preserving a narrative structure comparable to the Breadcrumb document.

## Architectural Assumption

Documentation organised around concepts is easier to understand
and maintain than documentation organised solely around
implementation details.

## Expected Findings

The spike is expected to identify:

- Additional metadata required by concept documents.
- Limitations of the current XML comment model.
- Areas where custom documentation elements may be required.
- Rules for concept ownership and member placement.
- 
---

# Spike 2 - Documentation Source Model

## Objective

Determine whether XML comments and external XML documents can represent all required documentation.

## Investigation

Validate support for:

- Overview documentation.
- Concept documentation.
- Architectural documentation.
- Examples.
- Cross references.
- Package documentation.

Evaluate whether custom XML elements are required.

## Success Criteria

All currently identified documentation requirements can be represented within the proposed source model.

## Architectural Assumption

A combination of structured comments and external XML documents is sufficient to describe both reference and conceptual documentation.

---

# Spike 3 - Package Documentation

## Objective

Determine whether packages should be treated as first-class documentation entities.

## Investigation

Define a documentation model for a package such as:

```text
Wiltshire.SDK.Navigation

Overview
Architecture
Concepts
Types
API Reference
```

## Success Criteria

Package-level documentation can be generated independently of any specific type.

## Architectural Assumption

Developers think about packages and features, not just classes and methods.

---

# Spike 4 - Publication Formats

## Objective

Determine the most effective publication mechanism.

## Investigation

Evaluate:

- Generated Markdown.
- Azure DevOps Wikis.
- DocFX websites.
- Azure DevOps Hubs.
- Documentation portals.

## Success Criteria

A publication strategy is identified that does not constrain the documentation model.

## Architectural Assumption

The documentation model should be independent of the publication platform.

---

# Spike 5 - Multi-Package Aggregation

## Objective

Determine whether documentation from multiple packages can be presented as a single documentation experience.

## Investigation

Combine documentation from:

```text
Wiltshire.SDK.Core
Wiltshire.SDK.Public
Wiltshire.SDK.Manage
```

into a unified structure.

## Success Criteria

Package ownership is preserved while providing a coherent user experience.

## Architectural Assumption

Documentation should be authored by package owners but consumed as a single system.

---

# Spike 6 - Authoring Experience

## Objective

Determine whether developers can realistically maintain the proposed documentation model.

## Investigation

Evaluate the effort required to maintain:

- XML comments.
- Concept references.
- Concept documents.
- Package documents.

Consider tooling support such as:

- Visual Studio snippets.
- Roslyn analyzers.
- Validation tools.
- Documentation warnings.

## Success Criteria

The documentation model can be maintained during normal development activities.

## Architectural Assumption

A documentation system is only successful if developers are willing to keep it up to date.

# Spike 7 - Generation Engine

Determine how the documentation model is transformed into documentation output.

## Investigation

- DocFX
- Custom Pre-Processor
- Custom Templates

## Success Criteria

The documentation model can be converted into a publication-neutral intermediate representation.

## Architectural Assumption

The generation engine should remain independent of publication format.


# Summary
The proposed architecture is based on validating the following assumptions:

| Assumption													 | Validated By |
|-----------------------------------------------------------------------|:-----:|
| Concepts are more useful than member lists							|Spike 1|
| Structured comments and external XML documents are sufficient			|Spike 2|
| Packages are first-class documentation entities						|Spike 3|
| Publication format should be independent of the documentation model	|Spike 4|
| Documentation can be aggregated across multiple packages				|Spike 5|
| Developers will maintain the documentation model						|Spike 6|
| The generation engine should remain independent of publication format.|Spike 7|