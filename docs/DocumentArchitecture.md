# Documentation Architecture

## Introduction

The purpose of this document is to define a documentation architecture capable of supporting both reference and conceptual documentation for .NET applications and frameworks.

Traditional API documentation generators focus on language constructs such as namespaces, classes and methods. While this provides a useful reference, it does not adequately document concepts, architecture, design intent, or common usage patterns.

The proposed architecture extends traditional XML documentation by introducing concept-based documentation and package-level documentation, allowing technical reference material and explanatory documentation to be generated from a common source model.

The objectives of the architecture are:

- Keep documentation close to the source code.
- Support automated documentation generation.
- Support conceptual and architectural documentation.
- Treat packages as first-class documentation entities.
- Support multiple publication formats.
- Remain largely independent of any specific documentation generation technology.

## Guiding Principles

The architecture is based on the following principles:

1. Documentation should be maintained close to the source code.

2. Conceptual and architectural documentation are first-class artefacts.

3. Documentation should be organised around concepts rather than solely implementation details.

4. Documentation sources should be independent of publication format.

5. Documentation should support automation without sacrificing readability.

6. Documentation should remain accessible without requiring specialist tools.

The long-term goal is to generate documentation that combines:

- API reference documentation.
- Conceptual documentation.
- Architectural documentation.

from a single documentation model while supporting multiple publication formats.

The architecture does not currently attempt to define:

- The final publication mechanism.
- The final concept document format.
- The DocFX implementation details.
- Visual Studio tooling.
- Azure DevOps integration details.

These areas will be investigated through the proof-of-concept spikes.

## Documentation Sources

Documentation is derived from multiple sources, each providing a different level of detail.

### Structured Comments

Structured comments embedded within source code remain the authoritative source for API documentation.

Structured comments describe:

- Types
- Members
- Parameters
- Return values
- Exceptions

Example:

```csharp
/// <summary>
/// Adds a page to the breadcrumb trail.
/// </summary>
public Breadcrumb Add(string name, string url)
```

### Concept Documents

Concept documents provide explanatory content for a logical area of functionality.

Concepts are referenced from a type and may group multiple related members.

Example:

```csharp
/// <summary>
/// Represents a breadcrumb trail.
/// </summary>
/// <concept file="AddPages" />
/// <concept file="ManageEntries" />
public class Breadcrumb
{
}
```

Each concept document contains:

- Title
- Introduction
- Related members
- References to related concepts

Example:

```xml
<concept>
    <title>Adding Pages</title>

    <intro>
        These methods add new entries to the breadcrumb trail.
    </intro>

    <members>
        <member cref="Breadcrumb.Add" />
        <member cref="Breadcrumb.AddMissing" />
    </members>
</concept>
```

Concept ownership is determined by the ordered list of concepts declared on a type.

The first concept containing a member owns the member's primary documentation.

### Package Documents

Package documents describe information that applies to a package as a whole.

Examples include:

- Package introduction
- Architecture
- Concepts
- Design decisions
- Extension points
- Version information

Package documentation is intended to provide context that cannot naturally be attached to an individual type.

### Extended Documentation Elements

The documentation architecture may support additional structured elements beyond those provided by standard XML documentation comments.

Authoring support should be editor-independent where practical. Documentation schemas, generators and validation rules should be reusable across Visual Studio, Visual Studio Code and automated build systems.

Potential examples include:
```xml
<include file="Breadcrumb.Overview.xml" />

<since version="1.0.0" />

<before version="2.0.0">
    This API will change in a future release.
</before>

<deprecated>
    Use NavigationService instead.
</deprecated>
```

The following elements illustrate the type of metadata that may be required. The final schema remains subject to further investigation.

|Element	|Purpose|
|---|---|
|\<include>		|Include external documentation content|
|\<since>		|Introduced version information|
|\<before>		|Future compatibility guidance|
|\<deprecated>	|Deprecation guidance|
|\<concept>		|Associate a concept definition|
|\<architecture>|Link architectural documentation|
|\<decision>	|Reference design decisions|


---

## Package Ownership

Documentation should be owned by the package repository that contains the corresponding source code.

Each package is responsible for:

- API documentation
- Concepts
- Architecture documents
- Package metadata

Documentation from multiple packages may later be aggregated into a single documentation experience, but ownership remains with the originating package.


## Documentation Model

The proposed documentation model consists of four primary entities.

```text
Package
├─ Introduction
├─ Architecture
├─ Concepts
└─ Types

Type
├─ Introduction
├─ Concepts
└─ Members
```

### Rationale

Traditional API documentation groups members by language constructs such as
methods, properties and events.

Concepts provide a higher-level organisational structure based on how
developers understand and use a type.

For example, a Breadcrumb class may organise members into:

- Creating Breadcrumbs
- Adding Pages
- Managing Entries
- Navigation Helpers

rather than presenting a flat list of methods.


### Package

A package represents the highest-level documentation entity.

A package may contain:

- Introduction
- Architecture
- Concepts
- Types
- API Reference

Example:

```text
Wiltshire.SDK.Navigation

Introduction
Architecture
Concepts
API Reference
```

### Concept

A concept represents a logical area of functionality.

Examples:

```text
Creating Breadcrumbs
Adding Pages
Managing Entries
Navigation Helpers
```

Each concept contains:

- Title
- Introduction
- Related members
- References to related concepts

Concepts provide the primary organisational structure for generated documentation.



### Type

A type represents a documented class, interface, record, struct, or enumeration.

A type may reference one or more concepts.

Example:

```csharp
/// <concept file="AddPages" />
/// <concept file="ManageEntries" />
public class Breadcrumb
{
}
```

### Member

A member represents:

- Methods
- Properties
- Events
- Fields

Members may belong to zero, one, or many concepts.

---

## Concept Membership Rules

### Multiple Membership

A member may belong to multiple concepts.

For example:

```text
Home()

Creating Breadcrumbs
Navigation Helpers
```

### Primary Ownership

The first concept in which a member appears becomes the primary owner of that member's generated documentation.

Subsequent concepts contain links or references to the primary documentation rather than duplicate content.

### Ungrouped Members

Members that do not belong to any concept appear in an automatically generated section named:

```text
Other Members
```

The documentation generator should emit a warning for ungrouped members.

### Concept Ordering

Concept order is determined using the following rules:

1. Concepts explicitly declared on the type appear first.
2. Concepts discovered through member references appear afterwards.
3. The Other Members section always appears last.

Example:

```csharp
/// <concept file="CreatingBreadcrumbs" />
/// <concept file="AddPages" />
/// <concept file="ManageEntries" />
public class Breadcrumb
{
}
```

Produces:

```text
Creating Breadcrumbs
Adding Pages
Managing Entries
Other Members
```

---

## Publication Formats

Publication format should be considered a presentation concern.

Documentation sources should describe the information being documented
rather than how that information should be rendered.

This allows the same documentation model to be published as:

- Markdown
- GitHub Wiki
- Azure DevOps Wiki
- HTML
- Documentation Portals
- PDF

without requiring changes to the source documentation.

### Markdown

Markdown provides a simple, reviewable format that integrates naturally with source control.

Potential uses:

- Repository documentation
- Documentation review
- Intermediate generation format

### GitHub Wiki

Generated markdown may be automatically published to project repository wiki.

Advantages:

- Git-backed.
- Familiar to developers.
- Public or private.
- Easy to review.
- Simple publishing workflow.


### Azure DevOps Wiki

Generated markdown may be automatically published to project or repository wikis.

Advantages:

- Native Azure DevOps experience
- Minimal infrastructure requirements
- Familiar to developers

### HTML

Documentation may be published as a generated HTML website.

Advantages:

- Rich navigation
- Search capabilities
- Cross-referencing
- Support for diagrams and additional assets

### Documentation Portal

Documentation may be published as a dedicated documentation site.

Advantages:

- Search
- Cross-package navigation
- Versioning
- Rich presentation

Such a portal may be hosted independently or surfaced through an Azure DevOps Hub.

The publication mechanism remains an open design decision and should not influence the documentation model.

### PDF

Documentation may be surfaced through a PDF document.

---

## Open Questions

The following areas require further investigation.

### Source Model

- Should concept documents be XML, Markdown, or a dedicated structured document format?
- Should reverse concept references be supported?
- What additional structured elements are required?

### Publication

- Should generated Markdown be considered a primary publication format?
- Should Azure DevOps Wikis be updated automatically?
- Is a dedicated Documentation Hub desirable?

### Automation

- Can concept membership be validated automatically?
- What warnings should be generated?
- Can tooling assist authors when creating documentation?

### Package Documentation

- How should architecture documents be represented?
- How should diagrams be stored and referenced?
- What package metadata should be supported?

### Multi-Package Documentation

- How should documentation from multiple packages be combined?
- How should navigation be generated?
- How should package ownership be preserved?
- Can concepts reference types in other packages?
- 
### Authoring Experience

- Can concept documents be created and maintained easily?
- What Visual Studio or Visual Studio Code support is required?
- What validation should be available to authors?
