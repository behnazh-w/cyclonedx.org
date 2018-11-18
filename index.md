---
# Page settings
layout: default
keywords: application security, software security, software bill of material, SBOM, BOM, open source, supply chain, specification, spdx, license, package url, purl, cpe
comments: false

# Hero section
title: CycloneDX
window_title: CycloneDX Software Bill of Material Specification
description: CycloneDX is a lightweight software bill-of-material (BOM) specification designed for use in application security contexts and software composition analysis (SCA).

# Micro navigation
micro_nav: false

# Page navigation

---

## Introduction

In software engineering, it is common to build new software by leveraging existing components. In doing so, it is 
often necessary to provide a bill of material that describes the components that are packaged with an application. 

## Project Goals
- Define a vendor agnostic specification independent of language or ecosystem
- Specification should be simultaneously human and machine readable
- Specification should be simple to implement with minimal effort
- Specification should provide lightweight schema definitions for JSON and XML
- Specification should reuse parts of existing specs where beneficial
- Specification should be decentralized, authoritative, and security focused
- Specification should promote continuous component analysis
- Specification should support hardware, libraries, frameworks, applications, and operating systems

## Namespaces
CycloneDX defines two unique namespaces, a bill-of-material (bom) namespace and a SPDX namespace. The SPDX namespace
evolves independently from the bom namespace. As new SPDX licenses are added to the SPDX specification, those changes 
will be reflected in the bom namespace automatically, without having to change namespaces.

CycloneDX is a versioned namespace and operates as follows:

* `http://cyclonedx.org/schema/bom` will always reference the latest version of the spec.
* Supplying a version after /bom such as `http://cyclonedx.org/schema/bom/1.0` will specify a specific version of the spec.


## Specification Overview

| Field | Description | Required |
| ------|-------------| :------: |
|type| Describes if the component is a library, framework, application, operating system, or hardware device | 	&#x2714; |
|publisher| The person(s) or organization(s) that published the component | |
|group| The high-level classification that a project self-describes as. This will often be a shortened, single name of the company or project that produced the component, or the source package or domain name. | |
|name| The name of the component as defined by the project | &#x2714; |
|version| The version of the component as defined by the project | &#x2714; |
|description| A description of the component | |
|scope| Specifies the scope of the component. If scope is not specified, 'runtime' scope will be assumed. | |
|hashes| File hashes supporting MD5, SHA1, SHA2, and SHA3 | |
|license| Zero or more license names or SPDX license IDs | |
|copyright| An optional copyright notice informing users of the underlying claims to copyright ownership in a published work| |
|purl| The Package URL of the component | |
|cpe| An optional mapping to an existing CPE identifier | |
|modified| Indicates if the component has been modified from the official distribution | &#x2714; |
|components| Specifies optional sub-components. This is not a dependency tree. It simply provides an optional way to group large sets of components together. | |

## Example BoM
```xml
<?xml version="1.0" encoding="UTF-8"?>
<bom xmlns="http://cyclonedx.org/schema/bom/1.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" version="1" xsi:schemaLocation="http://cyclonedx.org/schema/bom/1.0 http://cyclonedx.org/schema/bom/1.0">
  <components>
    <component type="library">
      <group>org.jboss.resteasy</group>
      <name>resteasy-jaxrs</name>
      <version>3.1.0.Final</version>
      <description>JAX-RS bindings for RestEasy</description>
      <hashes>
        <hash alg=”SHA-1">03a…</hash>
        <hash alg="SHA-256">22d…</hash>
      </hashes>
      <licenses>
        <license>
          <id>Apache-2.0</id>
        </license>
      </licenses>
      <purl>pkg:maven/org.jboss.resteasy/resteasy-jaxrs@3.1.0-Final?type=jar</purl>
      <cpe>cpe:2.3:a:redhat:resteasy:3.1.0:*:*:*:*:*:*:*</cpe>
      <modified>false</modified>
    </component>
    <!-- More components here -->
  </components>
</bom>
```

## Implementations
Build plugins for Maven and NPM have been created which support the automatic identification of all project
dependencies (including transitive) and generation of CycloneDX BOMs. The resulting BOMs may contain many of the 
elements above including group, name, version, description, file hashes, license, and PackageURL. Additionally, a 
standalone Java API was created for the programmatic creation and validation of CycloneDX BOMs.

- [CycloneDX .NET Core](https://www.nuget.org/packages/CycloneDX/)
- [CycloneDX Node.js Module](https://www.npmjs.com/package/@cyclonedx/bom)
- [CycloneDX Maven Plugin](https://search.maven.org/search?q=g:org.cyclonedx%20AND%20a:cyclonedx-maven-plugin)
- [CycloneDX Python Module](https://pypi.org/project/cyclonedx-bom/)
- [CycloneDX Java API](https://search.maven.org/search?q=g:org.cyclonedx%20AND%20a:cyclonedx-core-java)

Additional build plugins are planned and we're actively looking for volunteers to assist.

## Component Ecosystems
Components often belong to one or more ecosystems. These ecosystems typically have one or more sources of 
truth that provide additional data about the components. For example, Maven Central and the NPM repository provide 
information about Java and Node components respectively. Likewise, Linux distributions typically utilize one or more
repositories for packages within their ecosystem including RPM, Debian, and Solus.

### Package URL (purl)
Package URL was created to standardize how software package metadata is represented so that packages could universally
be located regardless of what vendor, project, or ecosystem the packages belong. 
Package URL conforms to [RFC-3986](https://tools.ietf.org/html/rfc3986).

The syntax of Package URL is:
```
scheme:type/namespace/name@version?qualifiers#subpath
```

* **Scheme**: Will always be 'pkg' to indicate a Package URL (required)
* **Type**: The package "type" or package "protocol" such as maven, npm, nuget, gem, pypi, etc. Required.
* **Namespace**: Some name prefix such as a Maven groupid, a Docker image owner, a GitHub user or organization. Optional and type-specific.
* **Name**: The name of the package. Required.
* **Version**: The version of the package. Optional.
* **Qualifiers**: Extra qualifying data for a package such as an OS, architecture, a distro, etc. Optional and type-specific.
* **Subpath**: Extra subpath within a package, relative to the package root. Optional.

## History
CycloneDX was originally designed for use with [OWASP Dependency-Track](https://dependencytrack.org), an open-source 
software composition analysis platform with a focus on continuous component analysis. Research into existing specifications 
such as [SWID](https://tagvault.org/standards/swid_tagstandard/) and [SPDX](https://spdx.org/) revealed that neither 
specification was robust enough for application security contexts nor did these projects meet the basic requirements 
for wide-spread adoption within both enterprise build systems and the open source community.

After many discussions with Dependency-Track users and volunteers, a decision was made to create a new specification with
the sole purpose of being lightweight, easily adoptable, and with a security-first approach. The new specification would
build upon existing specifications where possible. CycloneDX incorporated [SPDX license IDs](https://spdx.org/licenses/) 
as they were widely adopted and recognized within the community. Adoption for the emerging 
[Package URL](https://github.com/package-url/purl-spec) (purl) specification was also included to provide CycloneDX a 
reference to the native ecosystem metadata about the component.

Today, many organizations using Dependency-Track do so using CycloneDX as their preferred BOM format. Publishing
CycloneDX BOMs can be handled through an API or via the [Dependency-Track Jenkins plugin](https://plugins.jenkins.io/dependency-track), 
currently in use by thousands of organizations. 