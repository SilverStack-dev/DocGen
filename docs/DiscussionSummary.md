# Discussion Summary

## Current Status

### Tooling
- DocFX selected.
- Doxygen rejected.
- XMLDoc2Markdown rejected.

### Documentation Sources
- Extended XML comments.
- External XML concept documents.

### Documentation Model
- Package
- Concept
- Type
- Member

### Concept Rules
- Multiple concept membership allowed.
- First concept owns documentation.
- Subsequent concepts link.
- Ungrouped members go into Other Members.

### Publication Formats
- Markdown under consideration.
- GitHub Wiki under consideration.
- Azure DevOps Wiki under consideration.
- ADO Hub under investigation.
- Documentation Portal under investigation.


## Decisions Made

### Documentation Architecture

- Documentation Sources, Documentation Model and Publication Formats
  are treated as separate concerns.
- Packages are first-class documentation entities.
- Concepts are first-class documentation entities.
- Documentation should remain independent of the publication format.

### Documentation Model

- Concepts may contain zero or more member references.
- Members may belong to multiple concepts.
- The first concept owns a member's primary documentation.
- Subsequent concepts contain references rather than duplicate
  documentation.
- Ungrouped members are placed in an automatically generated
  Other Members section.

### Package Ownership

- Documentation is owned by the package repository that owns
  the source code.
- Documentation from multiple packages may be aggregated into
  a single documentation experience.

## Decisions Deferred

The following areas remain under investigation:

- Concept document format.
- Publication format.
- Documentation portal architecture.
- Visual Studio tooling.
- Azure DevOps integration.
- Multi-package aggregation.
- Versioning strategy.

## Rejected Approaches

The following approaches were considered but are not currently being pursued:

- Sandcastle as the primary documentation platform.
- Doxygen as the preferred solution.
- XMLDoc2Markdown as the primary architecture.
- A publication-format-specific source model.
- Documentation organised solely around methods and properties.


## Key Insights

### Breadcrumb Prototype

The manually authored Breadcrumb document demonstrated that developers naturally understand a class through concepts such as:

- Creating Breadcrumbs
- Adding Pages
- Inspecting the Trail
- Removing Pages
- Truncating the Trail

rather than through a flat list of methods.

This observation became the foundation of the concept model.

### Documentation Hierarchy

The documentation model evolved towards:

Package
  → Concept
    → Type
      → Member

rather than:

Namespace
  → Type
    → Member

### Publication Independence

Documentation sources should describe information rather than
presentation.

This allows the same source model to support:

- Markdown
- GitHub Wiki
- Azure DevOps Wiki
- HTML
- Documentation Portal
- PDF

## Next Steps

1. Complete review of the Architecture document.
2. Complete review of the Spike definitions.
3. Execute Spike 1 - Concept-Based Documentation.
4. Refine the documentation model based on Spike 1 findings.
5. Investigate source model requirements.

## Documents

The following documents currently define the proposal:

- DocumentationArchitecture.md
- Spikes.md

The Discussion Summary records the decisions and reasoning that led to the current architecture.