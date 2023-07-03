# Discoverability

Draft 2023-06-25
S.M. Richard and CDIF workgroup.

In order for resources to be discoverable on the Web, the search applications that are used to find things must locate some representation of the resource, and must be able to parse that representation to index for searching. In the realm of linked HTML web pages, search engines parse the text content of web pages to create text-based indexes, and use links on the pages to find other pages. This approach does not work for datasets, images, sound recordings, videos, and other non-narrative text resources, so separate representations of their content are constructed as metadata, in a format that can be parsed and indexed by search applications. At the simplest level this metadata can consist of text describing the resource and a link to access the resource, analogous to what is included on the cards in a legacy library card catalog.

In the digital world, a wide variety of metadata schemes have evolved for describing resources. These schemes are structured to allow a richer understanding of the information, and typically at least include information about the set of fifteen generic elements identified as the Dublin Core: Creator, Contributor, Publisher, Title, Date, Language, Format, Subject, Description, Identifier, Relation, Source, Type, Coverage, and Rights, first drafted at a 1995 meeting in Dublin, Ohio[^1]. These elements are defined at an abstract level, and served well with free text content values for use by humans. Such semi-structured metadata is insufficient to support programmatic reuse of the described resources; automation requires precise syntax and encoding of information. Thus the CDIF provides recommendations for metadata practices compatible with machine processing.

The use of standardized metadata to describe resources is one facet of discoverability. The other critical facet is the mechanism used to make these metadata descriptions discoverable by search applications, so that they are indexed and show up in search results. This document first discusses the metadata content requirement, then approaches to make the metadata discoverable, and finishes with CDIF recommendations. Appendices document a recommended metadata serialization format using the schema.org vocabulary and JSON-LD.

# Metadata content

In order to be machine actionable, the structure, syntax, and element-value representations in a metadata document (a digital object) must conform to conventions that client software can be programmed to parse and 'understand'. 'Understand' in this context means recognize the incoming bitstream content and take appropriate action. The metadata provider must communicate the conventions used to serialize the metadata they provide. Ideally this is done with an identifier for a specification document that details the conventions used. Some widely used metadata specifications include DCAT, DataCite, ISO19115, EML, FGDC CSDGM, CERIF, schema.org, and DDI. These specifications determine the structure and syntax of metadata documents, but leave latitude on how the values of some metadata elements are represented, and often offer multiple valid approaches to representing the same information. Achieving the level of metadata interoperability required for CDIF will require the adoption of one (or a small number) of these specifications, along with more specific conventions on vocabularies used for metadata properties. We refer to such a set of specific conventions as a profile.

## What is a metadata profile?

A metadata profile is a specification for the machine-actionable serialization of metadata information in some context. This specification includes the base file MIME-type, an information model for the metadata content, and how that information is represented both syntactically and semantically. Most profiles are based on an existing metadata specification, e.g. schema.org, DCAT, ISO19115-1, EML, DDI-CDI, but provide additional detail to resolve ambiguities in the base specification, or rules for vocabularies and datatypes for element values that extend or restrict the base specification. The simplest presentation of a profile specification can be a text document that describes the information required, identifies the base specification, and states any conventions or rules for profile conformance. Such a document could be used by a software developer writing code to use information in metadata conforming to the profile. Profiles might also be specified in a machine-actionable way, e.g., using SHACL rules, xml schematron rules, or other schema or rule representations. Using a rule-based representation for metadata profiles provides an approach to defining and communicating metadata constraints that can be validated automatically to support metadata profile interoperability, reusability, and quality.

CDIF defines a metadata profile for interoperable data discovery. To start, the content requirements are described, then we review the relationship to the Fair Digital Object Framework and the metadata requirement that framework entails. The metadata implementation outlined in Appendix 1 is based on these requirements.

# <a name="mdcontent" a/> Metadata Content Requirements

Explanation of fonts used: *Terms in italics are groupings of metadata properties*; **required (not nilable)**, **required (nilable)**, and **recommended** but optional metadata content; number of values that can be <span style="color: gray;">specified are in gray</span>.

The following list includes the minimum required content for basic resource description, discovery, and access. This recommendation is a synthesis of various metadata schemes, including ISO19115-1, schema.org conventions from ESIPFed Science on Schema.org and Ocean Data net, DCAT, DCAT-AP, and FDO Kernel Attributes-2.0[^2]. A mapping between the various scheme and these CDIF content elements is available in a Google Spreadsheet[^3] .

## Required 

If these elements do not provide useful information, the metadata is considered useless for even the most rudimentary discovery use cases. Conformant metadata MUST provide valid values, i.e., a meaningful title that identifies the resource, either a URL or text statement of how to obtain the resource, a statement of any licensing, usage or access constraints (Rights), and identifiers for the specification of the metadata serialization and the type of the resource described.

> **Resource identifier** (**1 entry**): A globally unique, resolvable identifier for the resource described by the metadata record.
>
> **Title** (**1 entry**): Succinct (preferably \<250 characters) name of the resource; should be sufficient to uniquely identify the resource for a human user.
>
> **Distribution:  URL, Distribution object,** or **Access Instructions** (**1 entry**): If the resource is a digital object accessible online, provide a URL that will retrieve the resource. If the resource has multiple representations, provide a Distribution Object documenting the various options with a URL and representation profile for each. If it's not accessible online, provide a URL to a landing page used to access the resource, or minimally, provide a text description explaining how to access the resource in the metadata (Access Instructions).
>
> **Rights** (**1 to many entry**): Information about required access permissions, licenses, contractual requirements, use constraints, and security constraints. Might be described in text or through links to external documents.
>
> **Metadata profile identifier** (**1 to many**): Identifier for metadata specification (profile) used to create this metadata record. Generally this will be populated automatically if the metadata is created using CDIF aware tools.
>
> **Resource type** (**1 to many**): A scoped name (label with classification scheme) that specifies the kind of resource described by the metadata. The resource type might be used to determine validation requirements specific to descriptions for that kind of resource.

## Required, but nilable

Content elements for which every resource should have useful information, but for which the information may not be available. Must be included in metadata record, but may have value \'nil:missing\', 'nil:unknown' or similar nil value. Use \'nil:notapplicable\' for Temporal Coverage or Geographic Extent when these are not applicable to the described resource.

> o **Description** (**1 entry**): Inform the reader about the resource\'s content, context, provenance, and any other information deemed useful for future cross-domain usage.
>
> o **Originators** (**1 to many entries**): Agent object that specifies responsible party in some role related to the resource, e.g., author or editor.
>
> o **Modified Date** (**1 entry**): Date (not temporal extent) when any changes to the resource were completed. Use a \"year\" or [ISO 8601 date and time] format. Alternative date formatting must be machine-readable and consistent across all datasets.

> o **Distribution Agent** (**1 entry**):The party (name of organization or person, etc.) to contact about accessing the resource. Agent object includes a name (label), identifier, and optional contact information. If there are multiple distribution options with different contact points, the Distribution Agent should be specified as part of the Distribution Object.

> o **Temporalcoverage** (**0 or 1 entry**). The time period represented by the described resource. This could be the time interval when data were collected, or an archaeological or geological time period that is the subject of the resource. Need to account for clock time, calendar time (Gregorian, Julian, Hebrew, Islamic, Chinese, Mayan\...), cyclical time (summer, first quarter, mating season, new moon, pay day) and for named time ordinal eras (Jurassic, Younger Dryas, Early Minoan I, Late Stone Age). See OWL Time.
>
> o **Geographic Extent - Horizontal** (**if applicable,  1 entry, minimum bounding rectangle or point**): Location coordinates must be given in decimal degrees using the [WGS 84] datum. Some resources may not be usefully described by a WGS 84 extent, in which case indicate nil:notapplicable; this would include extraterrestrial resources.

-   Bounding Rectangle: **North Bounding Latitude, South Bounding Latitude, East Bounding Longitude, West Bounding Longitude**. The minimum rectangle that completely contains the coverage extent for the resource content. Coordinate order and syntax are determined by the serialization profile.

-   Point: **Latitude, Longitude**. A centroid point for the coverage extent of the resource, or the location of the resource content if a point location is appropriate. Coordinate order and syntax are determined by the serialization profile.

-   Named location: Place name referenced to some gazetteer. Use scoped name pattern {label, authority, optional identifier}.

## Required for metadata management

These elements provide essential information for the operation of a distributed catalog system with harvesting of metadata between catalog servers. Values should be populated automatically by metadata creation tools, requiring no user input. Nil values are allowed.

> o **Metadata Date** (**1 entry**): Last metadata update/creation date-time stamp in [ISO 8601 date and time] format. This may be automatically updated on metadata import if a metadata format conversion is necessary.
>
> o **Metadata Contact Agent** (**1 entry**): The party responsible for metadata content and accuracy; Agent object includes a name (label), identifier, and optional contact information
>
> o **Metadata Identifier}** (**1 entry**): The identifier for the Digital object that contains the metadata.

## Recommended

Other properties that should be specified if possible and relevant. All are optional.

-   **Checksum.** (**0 or 1**): A string value calculated from the content of a digital object that allows verification that the content of the object has not been modified. Even small changes to the content of the file will change its checksum. The algorithm used to calculate the checksum must be documented. See also rfc6920 'Naming things with hashes' that establishes ways to identify checksum algorithms and to represent checksum values as a URI.

-   **Funding.** (**0 to many entries**): Cite funding sources (Grants, contracts\...). Each source has a grant or contract identifier, source organization, and label.

-   **Keyword** (**0 to many entries**): Distinguish 'tags' and 'controlled terms'. Tags are simply words that a metadata created thinks will be useful for users to identify resources of interest. Controlled terms are words defined in a vocabulary that minimally include the word (a fixed string to identify the term for humans) and a definition. Each term represents some concept. More semantically rich vocabularies would include resolvable identifiers, source information, and links to related terms. One common set of relationships in a vocabulary is a kind-of hierarchy linking broader to narrower concepts. Controlled terms should minimally be represented in metadata with a label and scheme name that identifies the source vocabulary; ideally a term URI and scheme URI could be included for more accurate identification and data integration.

-   **Policies** (**0 to many entries**): Policies used in management of the described resource, including whether the content may be changed (mutable or immutable), any scheduled updates, what is the expected life time for resource availability, what (if any) is the maintenance schedule, versioning, documentation for changes and change requests.

-   **Publication Date** (**0 or 1**): Date (not temporal extent) when the resource was made accessible. Use a \"year\" or [ISO 8601 date and time] format. Alternative date formatting must be machine-readable and consistent across all datasets. If no publication date is known, estimate the publication date range, enter the oldest year as the publication date, and include the estimated date range in the Description field.

-   **Other related agents** (**0 to many entries**): Recognition for others who have contributed to the production of the resource but are not recognized as authors/creators. Includes a variety of roles like editor, maintainer, publisher, point of contact, copyright holder, contributor (see DataCite contributor types)

-   **Related resources** (**0 to many entries**): Links to related data, publications, annotation, data sources, software used, etc. Links have a label, relationship type, and target resource identifier.

-   **Variable measured** (**0 to many entries**): A complete description of a dataset should include a list of the fields in the data, with each field mapped to a variable that is represented by the content of that field. Variable definitions should minimally specify the property represented. Details of data structure and schema are discussed elsewhere this document because they are more closely related to interoperability and usage that to data discovery. Only applicable to datasets.

-   **Version** (**0 or 1**): If the resource is versioned, specify the label for this version. Version labels should follow a scheme that allows alphanumeric sorting in the correct order.

# Fair Digital Object Framework

In the Fair Digital Object Framework (FDOF) a Digital Object (DO) is a specific bit stream that carries some information and has a **persistent, registered, resolvable identifier** (PID) that can be resolved to obtain a PID kernel record providing documentation for the source of the PID, expected lifetime, type of resource it identifies, linkage to the resource it identifies, and other attributes specified in a schema identified in the PID kernel record (PID profile)[^4]. Digital Objects are FAIR (FDOs) when they are part of an ecosystem comprising services and infrastructure to support realization of the FAIR principles. In the Fair Digital Object Framework (FDOF) there must be a mechanism to access either the object or its metadata by dereferencing the object's PID. Metadata content must enable the identified resource to be found, used and cited, enable interoperability and reuse, and include machine-actionable statements about dependencies and licensing.

![FDOF-CDIF metadata relations](images/DigitalObjectOverview.png)

A resource is some identifiable thing of interest to someone; it might be a Digital Object (DO) or a Non-digital Resource. A DO bitstream might be the resource of interest, or it might be a representation of an abstract or physical resource that cannot be transmitted electronically (see HTTP Range-14[^5]). The identifier for a DigitalObject can be dereferenced to access the object directly. A non-digital resource is a material entity (e.g. person, rock sample), an abstract entity (e.g. Donald Duck, The Land of Oz), or a \'Work\' or 'Expression' in the FRBR sense[^6] (e.g. Beethoven's 9th symphony, Dickens' Tale of Two Cities). Identifiers for Non-digital Resources must dereference on the web to a DigitalObject that is a representation of the non-digital thing and can be transmitted electronically.

The diagram below provides a view of the relationship between CDIF metadata and discovery concepts and the Fair Digital Object Framework. In the FDO perspective, a user (especially a machine agent) starts with an identifier (PID) for a digital object (FDOF PID in the diagram), and seeks to understand what it is about, how it is formatted, and what can they do with it. This is done by accessing the FDOFIdentifierRecord (Kernel metadata) and then, if necessary, the full metadata record. From the CDIF discovery perspective, a user (human or machine) searches for resources containing the information they need ('Resource' in the diagram), and subsequently considers whether the digital object(s) containing or representing the resource are useful for their application. The MetadataRecord associated with a Resource provides the information to support discovery and evaluation, and access in this approach.

The FDOF requires that there is a mechanism to dereference the identifier to get a metadata description of the resource. In CDIF, this metadata will be a CDIF-conformant JSON-LD record (as described below), that includes the metadata required by the FDOF. The CDIF has three approaches to accessing metadata for a resource using its identifier:

1.  The default dereferencing gets a CDIF metadata record

2.  The default dereferencing gets an HTML web page ('landing page'), and the CDIF metadata is embedded in the web page as a \<script\> element.

3.  If the identifier dereferences to get a DigitalObject that is the identified resource or a representation of a non-digital resource, a link to the CDIF metadata record must be included as a \<link\> parameter in the HTTP response header.

The CDIF metadata requirements outlined above include properties necessary to create a FDO PID Kernel information record (FDOFIdentificationRecord in Figure , below) as recommended by the Research Data Alliance (RDA)[^7]. The mapping from the PID Kernel information record to the CDIF metadata schema.org recommended implementation is shown in Appendix 2.

## <a name="impapp" a/> Implementation approaches

As a starting point, the resources of interest must be documented with metadata content that meets the requirements outlined in the [Metadata Content Requirements](#mdcontent) section, and is serialized following the CDIF schema.org profile *(currently in Appendix 1, likely to move to a separate document)*. Given that the metadata follows CDIF conventions, software can be written to extract information to enable client actions, whether that is populating a search index or accessing a useful resource representation. The workflow is reviewed here, and then discussed in more detail below.

Finding documents to index: Web-crawling is still an important approach to finding and indexing resources on the web, but a more proactive and widely used approach is the sitemap. A sitemap is a list of web locations (URLs) containing files that a hosting agent wants search engines to index. Many search engines enable providers to register sitemap locations. Alternatively, root directories on web servers can use a 'robots.txt' file to point to one or more sitemaps that should be indexed.

Getting the metadata: Once a crawler for a search application finds a document that should be indexed, the question is where is the metadata to index, and what conventions does the metadata use. Common practice is to create a human-readable landing page for datasets or other non-textual resources published on the web. Metadata can be incorporated into the landing pages in several ways:

1.  Each resource has an html landing page that describes the resource for human users, and contains embedded CDIF JSON-LD metadata (See [Appendix 1]()) for machine clients.]{.mark} Metadata can be embedded in landing pages using the HTML \<script\> element, in alignment with the Data on the Web Best Practices, specifically section 8.2, Metadata[^8]. This approach requires that each published resource has a human-readable landing page, intended to be the target of search by human users. Scripts are normally embedded in the \<head\> section of an HTML document. The \<script\> element has a 'type' attribute that provides a MIME-type specifying the type of script.

![Example D1. A JSON-LD metadata object embedded as a script in an HTML document.](images/JSON-LDscriptInHTML.jpg)

JSON-LDscriptInHTML

2.  Metadata can be embedded in the HTML \<head\> section of a landing page using HTML \<meta\> elements, which have a 'name' attribute that can be used to identify different metadata properties (see example below). This approach is implemented by some off-the-shelf repository software. The HTML \<meta\> elements are intended to describe the HTML document that contains the \<meta\> element[^9], not some external resource that the web page is about. CDIF recommends against this approach and suggests using the script approach (No 1 in this list) instead because that is more widely used and allows much richer metadata content to be included.

![Example D2. HTML meta tags with metadata about a resource.](images/HTMLmetaMetadata.png)

HTMLmetaMetadata

3.  Metadata can be linked from the landing page using the HTML \<link\> element in the \<head\> section to provide a web locator (URL) that can be used to retrieve a full metadata document about the described resource. The link element has a 'rel' attribute that can be used to indicate that the link is to metadata, and a 'type' attribute that can provide the MIME type of the target metadata record. This approach depends on web-crawlers identifying and following these links to get the metadata that the provider wants indexed. This is one of the 'signposting' approaches[^10].

4.  The web server serving the landing page can be configured to include \<link\> elements in the HTTP header[^11] that indicate the location of machine-actionable metadata describing the subject of URL target[^12]. This is similar to using the HTML \<link\> elements, but uses a feature of the HTTP protocol. The advantage of this approach is that the HTTP header links can be provided for any resource that has an HTTP URL, so the links to metadata can be accessed for non-textual resources that do not have an associated HTML landing page. If the download size for the resource is large, a client can use the HTTP 'head' request to access this header information without downloading the entire file[^13]. This approach would enable indexing of large resource collections that have a single landing page, but for which the individual resources do not have a landing page. The downside is that many client applications do not use the HTTP header information[^14]. This is a second 'signposting' approach. CDIF recommends this approach for resources that do not have landing pages; see implementation details below.

5.  A sitemap can point directly to metadata documents in formats that the search engine can parse. CDIF JSON-LD metadata documents are accessible via a URL. All metadata conforms to a single set of conventions for content and serialization (a profile).

Another option is for the sitemap to provide a URL that retrieves a document containing a collection of metadata records, something like the US Government Data.gov Project Open Data Catalog[^15], or Ocean Info Hub graph first approach[^16], with individual records using CDIF JSON-LD.

*How do harvesters know where to look?*

-   Publishers register metadata services with a harvester, e.g. providing a URL to GET a sitemap or other metadata catalog document, e.g. an OpenGeospatial Consortium (OGC) Record collection (catalogue).[^17]

-   Server robots.txt has link to sitemap.xml file; The sitemap.xml lists web locations that a crawler should index.

Once the harvester has a URL for a location to index, how do they know where the metadata is relative to that location? There are several approaches:

-   The URL gets a document containing a single CDIF JSON-LD metadata record

-   Metadata is embedded in the html of a web page as a script, type. NOTE: CDIF needs to define a MIME type or other id string to identify metadata serialized with CDIF JSON-LD

-   Labeled links that will get the metadata are embedded in the HTML \<head\>. The link should include the [IANA Link header](https://www.iana.org/assignments/link-relations/link-relations.xhtml) relations 'described by', and 'profile'.

-   Server provides link elements in HTTP headers.

*What does the harvester do with the metadata?* There a many possible approaches a client application could use to extract the information it needs from a metadata record. The simplest and likely most accurate approach is for the metadata to conform to a profile that the application is programmed to parse, and to communicate that profile conformance to the application. This entails two requirements. The profile must be documented in a way that allows software developers to write code to parse metadata conforming to the profile, and the profile must have an identifier that can be used to assert conformance.

The use of \<script\> or \<link\> elements (in the HTTP or HTML header) allows metadata to be offered following multiple specifications \-- using different formats or profiles[^18]. The \<script\> 'type' attribute could specify the profile using parameters on the MIME type.

![Example D3. Script type with a type parameter in the MIME type string](images/TypedScriptInHTML.png)

Minimally, the metadata record should assert the specification used to generate the record in a metadata property.

# CDIF Recommendations:

## Scope 

These recommendations are intended to be applicable for publication of metadata on the web to support a basic level of discovery interoperability for static resources with or without associated landing pages. Resources accessible via APIs that support functionality beyond simple static resource retrieval, e.g. subsetting, filtering, data transformations, or negotiation transactions related to security or confidentiality, are out of scope. Future work will define levels of conformance for more sophisticated functionality. For the CDIF, we recommend conventions for 1) what conventions ('standards') should be used so that the metadata is machine actionable; 2) how search engines find documents to index; 3) how metadata is incorporated in or accessed from those documents. The content recommendations and implementation outlined in [Appendix 1](#app1) cover the first of these topics. The second and third topics are discussed here.

Figure D1 (below) is a flow chart showing the recommended decision tree for metadata publishers to determine how to expose their metadata. Numbers in the following discussion refer to numbered boxes in the diagram.

![Figure 2. Decision graph to determine where metadata is located](images/MetadataPublicationWorkflow.png)


### Embedded in HTML

Starting at the top (1), if there are HTML landing pages that describe the resources of interest, and the metadata publisher has the necessary authority to update the content of these pages, then CDIF metadata should be embedded in an HTML \<script\> element in the \<head\> section of each landing page (3). The type attribute of the script is:

*type=\"application/ld+json;type=CDIF1.0\"*

### Individual metadata file URLs

If the resources of interest do not have individual landing pages, or the metadata publisher does not have authority to update the content of landing pages, the metadata should be placed in a web-accessible location (4 in Figure D1 ). There are two common approaches:

-   Each metadata record is accessed in a separate, static file with its own URL. The CDIF metadata is serialized as JSON-LD (see [Appendix 1](#app1)). MIME type for the metadata file is

*type=\"application/ld+json;type=CDIF1.0"*

-   Each metadata record is accessed dynamically from the server using a URL.There are various open-source metadata server systems that can be configured to deliver CDIF metadata from the server's metadata database, e.g. GeoNetwork OpenSource[^19], GeoPortal[^20], CKAN[^21]. The metadata retrieval URLs have different syntax depending on the software used, but typically include a metadata record identifier and a format parameter that would be used to indicate that CDIF metadata should be returned. If there is a format parameter in URL requests, its value should be '**CDIF1.0**'.

### Metadata list file

-   A collection of metadata records are gathered in one file accessed using a single URL. For CDIF, this file should contain a set of CDIF JSON-LD metadata objects, implemented as a schema.org ItemList]{.mark}[^22][. See example in [Appendix 1](#app1). The MIME type for the collection is:

*type=\"application/ld+json;type=CDIF-list-1.0"*

In any of these scenarios, a sitemap[^23] should be assembled that lists the URLs of all the locations that a metadata crawler or harvester should access (5 in Figure ). The Sitemap is an XML document that is simply a structured list of URLs, with an optional date stamp property that should indicate when the metadata at the URL target location was last updated.

The remaining challenge is communicating to a harvesting agent which of the options outlined above should be used to extract metadata at the URLs provided in the sitemap. The Sitemap XML schema does not include elements to associate link-type information with URLs in the sitemap. The sitemaps.org documentation does provide for users to define extensions[^24], and CDIF might implement this approach in the future. The recommendation in the mean time is to link to the sitemap from a robots.txt[^25] file placed in the root of the server containing the sitemap and metadata. In the robots file, the user agent value indicates the harvest protocol implemented. For the recommendations above, these are the user agent strings:

-   Embedded in HTML: *User-agent: CDIF-embed-in-HTML*

-   Individual metadata file URLs: *User-agent: CDIF-url-get-metadata*

-   Metadata list file: *User-agent: CDIF-url-get-metadata-collection*

In the robots.txt file, use the sitemap directive to provide the URL to the sitemap.xml document that contains the URLs to harvest. Example robots.txt entry:

*User-agent: CDIF-embed-in-HTML*

*Sitemap: http://www.example.com/sitemap.xml*

Based on these recommendations, metadata generated using the CDIF content and serialization can be found and harvested by agents using standard off the shelf web technology.

# Definitions:

Definitions in this section follow the conventions outlined in ISO704[^26]. Definitions are derived through interpretation of Fair Data Object Framework documents[^27] and Kahn and Wilensky[^28], unless otherwise noted.

**digital object** \-- packaged, identifiable sequence of digital bits that carries some information. A digital object has exactly one digital representation.

**Fair Digital Object** \-- **digital object** in the context of a system of policies and infrastructure to support realization of the FAIR principles.

**identifier** (CDIF) \-- an association between a sequence of bits and a unique resource of interest[^29]. Typically represented as a sequence of characters. NOTE: identifiers lose value if there is no clear, unambiguous specification of what they identify.

**persistent identifier** \-- **identifier** that has the intention that its binding is to the precise same resource for the lifetime of systems using the identifier

**PID** - **persistent, registered identifier** that can be resolved to obtain a **PID kernel record**.

**PID kernel record** - **digital object** that provides documentation for the source of the PID, expected lifetime, linkage to the resource it represents, and other attributes specified PR-KernelAtributues-2.0[^30].

**PID profile** - **digital object** that provides the definition of the protocol for obtaining the **PID kernel record** given the **PID**, and the definition of the schema for the content of the PID kernel record.

**PID registration** - binding between a **PID** and a **PID kernel record** that provides documentation for the source of the PID, expected lifetime, and linkage to the resource it represents. To be useful, there must be an a-priori known community practice for obtaining the PID kernel record given the PID.

**registered digital object** \-- **digital object** that has been placed in an accessible digital storage location, and assigned a **registered and resolvable identifier** that can be resolved to access the object

**registered identifier** \-- **identifier** that has been recorded in an accessible information system with its binding to the resource it identifies.

**resolvable identifier** \-- **identifier** that has a known protocol to access the resource to which it is bound. In current standard practice this is achieved using the world wide web and its associated HTTP(s) protocol.

**Semantic artefact** \-- machine-actionable, human-readable representation of an abstract, simplified view of the world[^31]

**Relation Type** **Terms**

-   describedby (schema:subjectOf)\
    > The target of the link provides metadata that describes the resource that is the origin of the link. (<https://signposting.org/FAIR/>)

-   describes (schema:about)\
    > The origin of the link is a resource that provides metadata that describes the resource that is the target of the link. It is the inverse of the describedby relation type.(<https://signposting.org/FAIR/>)

-   item (schema:hasPart, or schema:itemListElement)\
    > The origin of the link is a collection of resources and the target of the link is a resource that belongs to that collection. It is the inverse of the collection relation type.(<https://signposting.org/FAIR/>)

-   collection (schema:isPartOf)\
    > The origin of the link is a resource that belongs to a collection and the target of the link is the collection to which it belongs. It is the inverse of the item relation type. (<https://signposting.org/FAIR/>)

-   wasDerivedFrom\
    > The subject resource was created by transformation of the object resource, update (not revision) of the object resource resulting in a new entity, or the construction of a new entity based on the object resource. (based on PROV-DM[^32])

-   specializationOf\
    > The subject resource shares all aspects of the the object resource, and additionally presents more specific aspects, e.g. restrictions on property ranges, additional properties.(based on PROV-DM[^32])

-   wasRevisionOf\
    > The subject resource is a version of the object resource that has been corrected or improved in some way. (based on PROV-DM[^32])

-   hadPrimarySource\
    > The subject resource was produced with information from some agent (the object resource) having direct experience and knowledge about the topic of the subject resource.(based on PROV-DM[^32])

-   wasQuotedFrom\
    > Some or all of the object resource is copied in the subject resource by someone who may or may not be its original author. (based on PROV-DM[^32])

-   alternateOf\
    > The subject resource presents the same content as the object resource in a different expression or manifestation. (based on PROV-DM[^32])

# Appendix 1
<a name="app1" /a>

# Serialization of CDIF metadata 

JSON-LD has been chosen as the recommended serialization format for CDIF metadata following our principle to use existing mainstream technology. The JSON format is widely used for data serialization and popular with developers. JSON-LD adds additional syntax for the representation of linked data, compatible with existing JSON implementations so that integration with existing applications is relatively frictionless. Many metadata providers are using the schema.org[^33] vocabularies with JSON-LD serialization for metadata publication and interchange. Use of this format provides a low barrier to entry for data providers.

The JSON syntax is defined by the ECMA JSON specification[^34], and JSON-LD is specified in the JSON-LD 1.1 recommendation[^35] from the World Wide Web Consortium (W3C). This serialization is designed for linked data applications that will translate the JSON into a set of {subject, predicate, object} triples that can be loaded into an RDF database for processing. The JSON-LD context binds JSON keys to URIs for more precise semantics, and the use of URIs to identify entities and property values in the metadata will maximize the linkage with resources on the wider web to build an ever-expanding global knowledge graph.

A metadata record has two parts; one part is about the metadata record itself, the other part is the content about the resource that the metadata documents. The part about the record specifies the identifier for the metadata record, agents with responsibility for the record, when it was last updated, what specification or profiles the metadata serialization conforms to, and other optional properties of the metadata that are deemed useful. The metadata about the resource has properties about the resource like title, description, responsible parties, spatial or temporal extent (as outlined in the [Metadata Content Requirements](#mdcontent) section).

Schema.org includes several properties that can be used to embed information about the metadata record in the resource metadata: [**sdDatePublished**](https://schema.org/sdDatePublished), [**sdLicense**](https://schema.org/sdLicense), [**sdPublisher**](https://schema.org/sdPublisher), but lacks a way to provide an identifier for the metadata record distinct from the resource it describes, to specify other agents responsible for the metadata except the publisher, or to assert specification or profile conformance for the metadata record itself.

There are two patterns that could be used to structure the two parts of the metadata record:

Option 1. The root object is the described resource:

```
{   "@context": "https://schema.org",
    "@id": "ex:URIforDescribedResource",
    "@type": "ImageObject",
    "name": "Picture of analytical setup",
    "description": "Description of the resource",
    "subjectOf": {
        "@id": "ex:URIforTheMetadata",
        "@type": "DigitalDocument",
        "dateModified": "2017-05-23",
        "encoding": {
    "@type": "MediaObject",
    	    "dcterms:conformsTo": {"@id":"ex:cdif-metadataSpec"}
          }
        "about":{"@id":"ex:URIforDescribedResource"}
    }  }
```

Option 2: root object is the metadata record

```
{   "@context": "https://schema.org",
    "@id": "ex:URIforTheMetadata",
    "@type": "DigitalDocument",
    "dateModified": "2017-05-23",
    "encoding": {
  "@type": "MediaObject",
    	  "dcterms:conformsTo": {"@id":"ex:cdif-metadataSpec"}
          },
    "about": {
   "@id": "ex:URIforDescribedResource",
   "@type": "ImageObject",
   "name": "Picture of analytical setup",
   "description": "Description of the resource",
   "subjectOf":{"@id":"ex:URIforTheMetadata"}
       }   }
```
The rdf triples generated by these two approaches are identical, so if the metadata are always harvested to a triple store it makes no difference. However, allowing either approach would create interoperability problems for harvesters that are parsing the metadata as JSON\-- the paths to the same metadata elements are different in the two approaches. It is our judgment that option two above (root object is the metadata) is more consistent with knowledge graph construction, CDIF thus recommends a JSON-LD serialization in which the root object is the metadata record. If this is a problem for processing JSON-LD metadata as JSON, JSON-LD framing[^36] can be used to generate a desired, consistent JSON tree structure for processing.

The recommended basic structure of the JSON-LD file is like this (using the 'root object is the metadata' approach). This pattern is used in the examples below.:
```
{
    "@context": [
        "https://schema.org",
        {"dcterms": "http://purl.org/dc/terms/",
   "ex":"https://example.com/99152/"
}
    ],
    "@id": "ex:URIforThisMetadataRecord",
    "@type": "DigitalDocument",
    "dateModified": "2017-02-03",
    "encoding": {
    "@type": "MediaObject",
    	    "dcterms:conformsTo": "ex:cdif-metadataSpec"
          }
    "about": {
    "@id": "ex:URIforDescribedResource",
    "@type": "{the type of the described resource}",
    "dateModified": "2014-02-23" ```
 ..... other metadata content omitted
```          }
   }
```

JSON keys prefixed with '@' are keywords defined in the JSON-LD specification[^37] (see table below)

 | Keyword  |   Description|
 |----------- |-------------|
 | \@context |  The value of the context is an object that specifies set of rules for interpreting the JSON-LD document. The rules can be specified inline in, or via a URI that identifies a context object containing a set of rules. |
|  \@id    |    A string that identifies the subject of the assertions in the JSON object that contains the \@id key.|
|  \@type   |   An identifier for the definition of the structure of the JSON object that contains the \@type key. The type determines what keys or values should be expected in the JSON object that contains the key. Values are types defined in the schema.org vocabulary. In the CDIF framework (and for compatibility with FDOF FDOF digitalObjectType), the schema:additionalType property should be used (see implementation table below) |
 


In the example above, there is a 'dateModified' metadata assertion. It would translate into a triple like this:

*ex:URIforThisMetadataRecord  schema:dateModified \"2017-02-03\"   *

Which states that the Metadata was modified (most recently) on 2017-02-23.

On the other hand, in the 'about' object, there is a statement:

*ex:URIforDescribedResource schema:dateModified \"2010-02-03\" *

Which states that the Described Resource was modified (most recently) on 2010-02-03. The distinct identifier for the metadata record allows statements to be made about the metadata separately from statements about the resource it describes. Note that the \@type for the metadata node (root node) is 'DigitalDocument'. This is a schema.org type that corresponds broadly to the concept of DigitalObject as used by the Fair Digital Object (FDO) community[^38], recognizing that the metadata record is a digital object.

### Implementation of metadata content items

The following table maps the metadata content items described in the [Metadata Content Requirements](#mdcontent) section to the schema.org JSON-LD keys to use in metadata serialization. Some example metadata documents follow. The \'Obl.\' column specifies the cardinality obligation for the property; \'1\' means one value required; 1..\* means at least one value is required; 0..\* means the property is optional and more that one value can be provided. Properties implemented with a path that starts with /\"about\" are describing the resource, properties with path from / describe the metadata.


| **CDIF content item**       | **Obl.** | **Schema.org implementation**   | **Scope note**    |
|----------- |-------------|-------------|-------------|
| Metadata identifier         | 1        | /"@id":{URI}    | The URI for the metadata record should be the \@id value for the 'subjectOf' element in the JSON instance document tree   |
| Title      | 1        | /"about"/"name":{string}     | A set of words that should uniquely identify the described resource for human use, in the scope of the metadata catalog containing this metadata record.     |
| Distribution        | 1        | /"about"/"url":{URL}       | If metadata is about a single digital object      |
|             |          | /"about"/"distribution": <br>   { \"@type\": \"DataDownload\", <br>    \"contentURL\": {URL },\...   }   | If the metadata is about an abstract, non-digital, or physical resource that has multiple distributions, with different URL, encodingFormat, conformsTo properties. Each distribution is considered a distinct digital object. |
| Rights                      | 1..\*    | /"about"//"license":{text or URI} <br> Or <br> /"about"//"conditionsOfAccess":{text or URI}    | URL to license document or text explanation of restrictions on use. There might be multiple links to documents specifying related security, privacy, usage, sharing, etc\... concerns.    |
| Metadata profile identifier | 1        | / "dcterms:conformsTo": {identifier} <br> or <br>  "schemaVersion":{identifier}          | Use Dublin Core terms property. The value for Base CDIF metadata is 'CDIF_basic_1.0'. Different profiles extending this must define unique identifier strings to use here. *QUESTION--Which to use dct:conformsTo or schema:schema:Version?*  |
| Resource type               | 1        | /"about"/"@type":{schema.org type}      | If the Schema.org resource types[^39] are specific enough to scope the metadata record, use those.   |
|                             | 0..\*    | /"about"/"additionalType": \[{DefinedTerm or URI}, \...\]      | If a more specific resource type needs to be specified, add a text or URI value here that identifies the type. MUST be consistent with the \@type. To simplify parsing, always encode as an array.     |
| Description                 | 0..1     | /"about"/"description": {string}            | Free text, with as much detail as is feasible   |
| Originators                 | 0..\*    | /"about"/"creator" : \[{Person or Organization}, \...\]               | The value is a schema.org person or organization. To simplify parsing, always encode as an array. Use ORCID or other PID to describe person or organization where possible    |
| Publication Date            | 0..1     | /"about"/"datePublished" : {date time}                                | Date on which the resource was made publicly accessible. Use ISO 8601 format.   |
| Modification Date           | 1        | /"about"/"dateModified" : {date time}                                 | Date of most recent update to resource content. If Publication date is not provided, defaults to the Modification Date. Use ISO 8601 format.  |
| GeographicExtent (named place)           | 0..\*     |  /"about"/\"spatialCoverage\": { \"@type\": \"Place\", \"name\": {string} or {schema:DefinedTerm} }  | To specify location with place names; if the names are from a gazeteer, use the schema:DefinedTerm to provide a name, identifier, and inDefinedTermSet to fully document the concept. |
| GeographicExtent (bounding box)  | 0..1 | /"about"/\"spatialCoverage\": { \"@type\": \"Place\", \"geo\": {  \"@type\": \"GeoShape\",  \"box\": \"39.3280    120.1633  40.445    123.7878\"   } } }  | For bounding box specification of the spatial extent of resource content. See ESIP SOSO for details[^40]. Recommend including only one bounding box; behavior of harvesting clients when multiple geometries are specified is unpredictable.   |  
|  GeographicExtent (point location)     |   0..1     | /\"about\"/\"spatialCoverage\": { \"@type\": \"Place\", \"geo\": {  \"@type\":  \"GeoCoordinates\",   \"latitude\": 39.3280     \"longitude\": 120.1633 } } }    | For a point location specification of the spatial extent of resource content. Recommend including only one point; behavior of harvesting clients when multiple geometries are specified is unpredictable.   |
|  GeographicExtent (other serialization)    | 0..\*     | /\"about\"/\"geosparql:hasGeometry\": { \"@type\": \"sf#Point\",  \"geosparql:asWKT\":  \"@type\":#wktLiteral\", \"@value\":\"POINT(-76  -18)\"}, \"Geosparql:crs\": {\"@id\":"CRS84\"} }    | Optional geographic extent using other more interoperable geometries, e.g., GeoSPARQL, see Ocean InfoHub[^41]. (Note URIs in example are truncated\...)  Other geometry schemes might be specified in a specific domain profile, e.g. for atmospheric, subsurface data, or local coordinate systems.  |
| Distribution Agent          | 0..\*    | /"about"/"provider":{Person or Organization}                          | Contact point for the provider of a distribution. For a simple digital object with a download URL, or a resource with multiple distributions all from the same provider.     |
|                             |          | /"about"/"distribution": \[  {    \"@type\": \"DataDownload\",\"provider\":{Person or Organization}   }\...\]     | If there are multiple distributions with different providers, each distribution can have a separate provider                                     | Metadata date               | 0..1     | / "dateModified":{Date or DateTime}     | Use ISO8601 format. The most recent update date for the metadata content. Harvesters use this to determine if they have already harvested and processed this record.   |
| Metadata contact            | 0..1     | / "maintainer":{Person or Organization}   | Should include a name and contact point (institutional e-mail is best) for the agent responsible for metadata content. This is the contact point to report problems with metadata content. Person and Organization are Agent objects with various properties.   |
| Variable measured           | 0..\*    | /\"about\"/\"variableMeasured\":<br> \[ { \"@type\":"PropertyValue\",<br> \"@id\": \"astm:var0011\",<br>  \"propertyID\": \[ \"pato:PATO_0000025\",\"astm:prop/0405\" \],<br>  \"name\": \"hostMineral\", <br> \"description\": \"\...."   }\....\]  | Follow ESIPfed Science on Schema.org recommendation[^42], see also discussion for representing more complex data structures in ESIPfed Experimental[^43]. Variable must have a name and description, should have a propertyID with URI for the represented concept. The URI in the propertyID provides the semantic linkage for meaning of the variable.     |
| Keyword                     | 0..\*    | /\"about\"/"keywords":<br>\[    {string}, <br>  {\"@type\":\"DefinedTerm\", <br> \"name\": \"OCEANS\", <br> \"inDefinedTermSet\": \"gcmd:sciencekeywords\", <br>  \"identifier\": \"gcmd:concept/916b....6167d\"   },\....     \]   | Implement with text for tags, and schema:DefinedTerm for keywords from a controlled vocabulary. The DefinedTerm approach is used to represent concepts.   |
| Temporal coverage           | 0..1     | /\"about\"/\"temporalCoverage\": \"2018-01-22\"  | Calendar data or clock time instant use ISO8601 encoding   |
|     |  | /\"about\"/\"temporalCoverage\": \"2012-09-20/2016-01-22\"  | Calendar data or clock time interval use ISO8601 encoding   |
|    |  | /\"about\"/\"temporalCoverage\": <br> \[{ \"@type\":\"time:ProperInterval\", <br> \"time:intervalStartedBy\": \"isc:LowerDevonian, <br>  \"time:intervalFinishedBy\": \"isc:LowerPermian\"  }\]             | Time ordinal era interval, use owl:time namespace, time: http://www.w3.org/2006/time#. This example uses [International chronostratigraphic chart, isc](http://resource.geosciml.org/classifier/ics/ischart/). See https://perio.do/en/ for identifiers for many other named time intervals.   |
| Other related agents- simple contributor        | 0..\*    | /\"about\"/"contributor": \[ {Person or Organization}, \... \]        | Recognition for others who have contributed to the production of the resource but are not recognized as authors/creators.    |
| related agent with role | | /\"about\"/"contributor": {"@type": "Role", <br> "roleName": "Principal Investigator",<br>"contributor": {"@type": "Person",<br>	"@id": "https://orcid.org/...",<br>	"name": "John Doe",<br>	"affiliation": {"@type": "Organization",<br>		"@id": "https://ror.org/...",<br>		"name": "..."	},<br>	"contactPoint": {"@type": "ContactPoint",<br>		"email": "john.chodacki@ucop.edu"<br>	}  }   } |  To assign roles to contributors like editor, maintainer, publisher, point of contact, copyright holder  (e.g.  DataCite contributor types), use the rather convoluted [role construction defined by schema.org](http://blog.schema.org/2014/06/introducing-role.html) |
| Related resources           | 0..\*    | /"about"/"dcterms:relation": \[{ "ogcapi:rel": "...",<br>"ogcapi:type": "text/html",<br>"ogcapi:title": "...",<br>"ogcapi:href": "https://example.org/data/stations" } \]  | The OGCAPI common model for links[^44] is a simple pattern for asserting relationships, and follows the pattern defined by [IETF Web linking](https://datatracker.ietf.org/doc/html/rfc8288), and [attributes defined in html namespace](https://www.w3.org/TR/2011/WD-html5-author-20110809/the-link-element.html#the-link-element). OGC doesn't define a namespace, so ad hoc solution for now is to use URL of [link.json schema](https://schemas.opengis.net/ogcapi/common/part1/1.0/openapi/schemas/link.json) as the namespace, and ogcapi as the prefix. This will have to be defined in JSON-LD context.  Related resource links are useful for evaluation and use of data, but because of the wide variety of relationship possibilities, difficult to use in general search scenarios. Use a soft-type implementation, with a link relationship type using a schema:DefinedTerm, and a resolvable identifier for the relationship target.   |
| Funding                     | 0..\*    | /"about"/"funding" :<br> { \"@id\": \"URI for grant\", <br> \"@type\": \"MonetaryGrant\",<br> \"identifier\": \"grant id\",  <br> \"name\": \"grant title\", <br> \"funder\":<br> { \"@id\": \"ror for org\", <br> \"@type\": \"Organization\", <br>  \"name\": \"org name\",  <br> \"identifier\": \[    \"other identifiers\" \] } }     | Use schema.org encoding and science on schema.org pattern[^45] . Other organization properties can be included in the funder/Organization .    |
| Policies                    | 0..\*    | \"publishingPrinciples\": \[  {\"@type\": \"CreativeWork\"}\....  \]     | FDOF digitalObjectMutability, RDA digitalObjectPolicy, FDOF PersistencyPolicy. Policies related to maintenance, update, expected time to live.    |
| Checksum                    | 0..1     | /\"about\"/\"spdx:checksum\" or /\"about\"/\"distribution\": \[ { \"@type\": \"DataDownload\",    \"spdx:checksum\": {URL },..    }\...\]    | A string value calculated from the content of the resource representation, used to test if content has been modified. No schema.org property, follow DCAT v3 adoption of Software Package Data Exchange (SPDX)[^46] property; The spdx Checksum object has two properties[^47]: algorithm and checksumValue. If the resource is a single DigitalObject, use the first partter, if there are multiple distributions, the checksum is a property of each distribution/DataDownload. |

### Implementation patterns

-   DefinedTerm. {label, schemename, conceptURI, schemeURI}. This is a pattern used for property values that are concepts defined in a controlled vocabulary, ontology, or similar semantic artefact. Values have a label, which is a string that will be meaningful to a human user, a 'schemename', which is a label that similarly identifies the source semantic resource in which the concept is defined, the conceptURI is a globally unique,resolvable identifier forthe concept value; schemeURI is a globally unique identifier for teh semantic resource in which the concept is defined.

-   Identifier. {identifier scheme, identifier string, resolvable identifier string}. This pattern is for identifiers that are useful to scope in the context of a scheme. The identifier scheme is associated with some authority (e.g. IBSN) that manages unique identifiers within their scope. If the identifier can be associated with a resolver to create a resolvable identifier string, typically an HTTP URL with a resolver host name (e.g https://n2t.net/) to which the identifier is suffixed to obtain a representation of the thing identified.

-   AgentObject. {name, agenttype, identifier, contactpoint, if the agen is a Person, include affiliation}. This pattern is for specifying an Agent in the prove sense: An agent is something that bears some form of responsibility for an activity taking place, for the existence of an entity, or for another agent\'s activity. Agents can be persons, organizations, or software-defined actors. Agents have a name for human recognition, a type, an identifier, contactPoint and affiliation. Machine agent contact points should be the accessible human who operations the environment running the machine agent.

-   DistributionObject {contentURL, encodingFormat, conformsTo, distributionAgent }. A pattern for specifying information necessary or useful for implementing machine access to a DigitalObject that is or represents a resource of interest. Includes a URL for the web location at which the DigitalObject can be accesses, the specifications or profiles to which the serialization and content of the object conform, and the Agent responsible for the distribution platform. This agent is the contact point if there are problems accessing the distributed digitalObject.

## Examples

### Example 1: simple digital object

This example shows a CDIF metadata record for a simple digital object\-- a single image that is the resource of interest and the single representation of that resource:
```
{
    "@context": [
        "https://schema.org",
        {
            "dcterms": "http://purl.org/dc/terms/",
            "ex": "https://example.com/99152/"
        }
    ],
    "@id": "ex:URIforTheMetadata",
    "@type": "DigitalDocument",
    "dateModified": "2017-05-23",
    "provider": {
        "@type": "Organization",
        "name": "Joe's photo studio",
        "email": "metadata@joesphotostudio.org"
    },
    "dcterms:conformsTo": "ex:cdif-metadataSpec",
    "about": {
        "@id": "ex:URIforDescribedResource",
        "@type": "ImageObject",
        "name": "Picture of analytical setup",
        "description": "Description of what's in the picture, where and why taken.  note that in this example, the described resource is  a digitalObject (not a a physical object or abstract object with one or more representations implemented as digitalObjects), so the resource metadata includes a URL that gets the object. ",
        "license": "https://joesphotostudio.org/license.txt",
        "creator": "nil:unknown",
        "datePublished": "2012-07-18",
        "encodingFormat": "image/tif",
        "url": "https://repository.org/images/2423757.tif",
	  "subjectOf":{"@id":"ex:URIforTheMetadata"}
    }
}
```

### Example 2: a dataset with multiple distributions

The dataset is considered a non-digital resource\-- it is a collection of data instances that can be represented in various ways. The metadata in this example must distinguish properties that are scoped to the dataset, independent of the representation (distribution), and properties that are distribution-specific.

```
{
    "@context": [
        "https://schema.org",
        {"dct": "http://purl.org/dc/terms/",
    "spdx":"http://spdx.org/rdf/terms#"}
    ],
    "@id": "metadata:ark:/99152/URIforTheMetadataRecord",
    "@type": "DigitalDocument",
    "description": "NOTES on the metadata in the the about section (below): ,  conformsTo on the Dataset specifies the information model that underlies the data;  conformsTo in a DataDownload specifies the profile for the download-- access protocols, serialization scheme, vocabularies used, other conventions necessary to enable machine processing of the download",
    "maintainer": {
        "@type": "Person",
        "@id": "https://orcid.org/identifierForMetadataProducer",
        "name": "metadata creator-editor-steward name",
        "description": "person responsible for the metadata"
    },
    "dateModified": "2014-02-23",
    "encoding": {
        "@type": "MediaObject",
        "description": "this is about the encoding of the metadata in this metadata record (a digitalObject...).",
        "contentSize": 687,
        "dcterms:conformsTo": "https://example.org/cdif-metadataSpec"
    },
    "about": {
        "@id": "ark:/99152/URIforTheDataset",
        "@type": "Dataset",
        "dcterms:conformsTo": "https://example.org/DatasetSpecification",
        "identifier": {
            "@type": "PropertyValue",
            "description": "this is redundant with @id, but makes identification of the described dataset explicit",
            "propertyID": "https://registry.identifiers.org/registry/ark",
            "value": "ark:/99152/63v4yo3eeqepj0",
            "url": "https://n2t.net/ark:/99152/63v4yo3eeqepj0"
        },
        "publishingPrinciples": [
            {
                "@type": "CreativeWork",
                "url": "https://example.org/id/policy",
                "name": "Digital Object Policy",
                "description": "policies used in management of the described resource..."
            }
        ],
        "distribution": [
            {
                "@type": "DataDownload",
                "contentURL": "https://example.org/datasets/1234567890.csv",
                "description": "A comma delimited text distribution of the data following csv on the web W3C conventions (a digitalObject...). We do not consider the URL that locates this particular digitalObject as the identifier for the object, so this DataDownload is a blank node. ",
                "spdx:checksum": {
			  "spdx:algorithm":"spdx:checksumAlgorithm_md5",
			  "spdx:checksumValue":"0BAA1B8"
                     },
                "contentSize": "12345 kb",
                "encodingFormat": "text/csv (base mime type)",
                "dcterms:conformsTo": [
                    "https://example.org/dataonthewebcsvprofile",
                    "https://fdof.org/fdofprofile"
                ]
            },
            {
                "@type": "DataDownload",
                "contentURL": "https://example.org/datasets/1234567890.rdb",
                "description": "A comma delimited text distribution of the data using USGS RDB conventions (a digitalObject...). We do not consider the URL that locates this particular digitalObject as the identifier for the object, so this DataDownload is a blank node. ",
                "spdx:checksum": {
			  "spdx:algorithm":"spdx:checksumAlgorithm_md5",
			  "spdx:checksumValue":"0F119B7"
                     },
                "contentSize": "11256 kb",
                "encodingFormat": "text/csv (base mime type)",
                "dcterms:conformsTo": [
                    "https://example.org/usgsRDBprofile",
                    "https://fdof.org/fdofprofile"
                ]
            }
        ],
        "dateModified": "2010-02-03",
        "subjectOf":{"@id":"metadata:ark:/99152/URIforTheMetadataRecord"}
    }
}
```


### Example 3. Item list with a collection of metadata records:

This is and example file containing multiple records for harvesting, using the schema.org ItemList. The metadata record content is greatly abbreviated.

```
{
    "@context": [
        "https://schema.org",
        {
            "dcterms": "http://purl.org/dc/terms/",
            "ex": "https://example.com/99152/"
        }
    ],
    "@type": ["ItemList"],
    "@id": "https://example.org/id/graph/X",
    "name": "Example CDIF Metadata collection",
    "description": "...",
    "itemListOrder": "https://schema.org/ItemListUnordered",
    "numberOfItems": 3,
    "itemListElement": [
        {
            "@id": "ID_for_this_metadata_record1",
            "@type": "DigitalDocument",
            "dcterms:conformsTo": "https://example.org/cdif-metadataSpec",
            "about": {
                "@id": "https://example.org/id/XYZ",
                "@type": "ImageObject",
               ....
            }
        },
        {
            "@type": "DigitalDocument",
            "@id": "metadata:10.5878/tnzz-m331",
            "dct:conformsTo": "https://example.org/cdif-metadataSpec",
            "about": {
                "@type": "Dataset",
                "@id": "https://doi.org/10.5878/tnzz-m331",
                ...
                "distribution": [
                    {
                        "@type": "DataDownload",
                        "name": "ID 006 - Vero, Beskattning av vind- ...",
                        "contentUrl": "https://snd.gu.se/en/catalogue/d...",
                        ...
                    },
                    {
                        "@type": "DataDownload",
                        "name": "ID 042 - Nye regler om stigning i ...",
                        "contentUrl": "https://snd.gu.se/en/catalogue/do...",
                        "encodingFormat": "application/pdf",
                    }
                ],
                "subjectOf": {"@id": "metadata:10.5878/tnzz-m331"}
            }
        },
        {
            "@id": "ex:URIforTheMetadata",
            "@type": "DigitalDocument",
            "dateModified": "2017-05-23",
            "provider": {
                "@type": "Organization",
                "name": "Joe's photo studio",
                "email": "metadata@joesphotostudio.org"
            },
            "dct:conformsTo": "ex:cdif-metadataSpec",
            "about": {
                "@id": "ex:URIforDescribedResource",
                "@type": "ImageObject",
                ...
                "subjectOf": {"@id": "ex:URIforTheMetadata"}
            }
        }
    ]
}
```

# Appendix 2

## Mapping from CDIF metadata to RDA PID Kernel attributes

Implementation approach to supplying PID Kernel information records associated with digital object identifiers in the CDIF framework is an architecture decision \[TBD\], but the information necessary to produce such metadata is all included in the CDIF metadata implementation, except for embedding of thumbnails or other data objects directly in the metadata digital object.


| **FDO field** | **CDIF schema.org** | **Scope Notes** |
| --- | --- | --- |
| <font size="3">FDO Creator</font> | "creator" : [{Person or Organization}, ...] | Agent responsible for creating the FDO (and implicitly issuing the FDO PID) [NOTE-- creator of content and identifier registration are not necessarily the same agent] |
| FDO Responsible Organisation (Resource) | "provider":{Person or Organization} | note that this can be another organisation than the PID issuer. If the agent is an organization, the value is taken from the ROR registry value domain (or other with namespace id). Implement as responsible part with an Agent (name, ID, contactInformation)  |
| RDA checksum | "spdx:checksum": | Checksum of object contents. Checksum format is determined via the attribute type referenced in a Kernel Information record. Called etag in PubCom-PR-PIDProfileAttributes-2.0 [^48]. The algorithm for checksum calculation should be defined in the definition of the object type, or described in the resource description in this metadata. spdx value is an object with 'algorthm' and 'checksumValue'. |
| FDOF digitalObject-Mutability | /"about"/"publishingPrinciples": | This attribute indicates whether the included bit-sequence is mutable or immutable, and policies for when new version is created when some bits are changed.  Principles apply at the DigitalObject/Distribution level.|
| FDOF Persistency-Policy | /"about"/"publishingPrinciples": | this attribute indicates what the intention of its creator is with respect to its life-time/maintenance; the value domain is a vocabulary like: {UNKNOWN, NONE, Years , or [EarthCube ELT](http://cor.esipfed.org/ont/earthcube/ELT) } (note: seems only partly covered by RDA digitalObjectPolicy.   |
| RDA digitalObject-Policy | /"about"/"publishingPrinciples": | Pointer to a policy object that documents changes to the object or its Kernel Information record, including object access and modification policies. A caller should be able to determine the expected future changes to the object from the policy, which are based on managed processes the object owner maintains. |
| FDOF Responsible-Organisation (Technical Management) | /"about"/"maintainer" : [{Person or Organization}, ...] | after creation, the same or another organisation will be responsible for further management of the FDO. The Responsible Organisation equals the FDO Creator if available by default (mandatory attribute) [equate with Resource point of contact] |
| FDOF rightsRecord | /"about"//"license":{text or URI}Or/"about"//"conditionsOfAccess":{text or URI} | This is a pointer to a resource that specifies access permissions. Include: FDOF licenceConditions: that links to one or more formal specifications about licences such as CC-x; FDOF transactionRecord: a resource that includes contractual information. |
| FDOF ScientificDomain | /"about"/"keywords":[{string} or schema:DefinedTerm] | indicator of the scientific domain the FDO refers to. This ensures compliance with the FAIR principles, which are per definition applicable at the domain level. This attribute is required since different mandatory attributes may be required at the domain-level. |
| Profile | / "dcterms:conformsTo": {identifier} | The Kernel metadata profile is a schema that determines the attribute requirements for FDO metadata beyond the base requirements. In the FDO world, the kernel profile specifies Kernel information about the resource associated with an identifier. Schema.org does not have a 'conformTo' property so follow DCAT v3 using the Dublin Core Terms property. |
| FDOF digitalObjectType | /"about"/"@type":{schema.org type} | The kind of resource associated with an identifier. The type implies a schema that dictates the format, information model, and profile conventions for the resource representation contained in the identified digital object. Use appropriate Schema.org type for \@ type property, the additional type should be from a controlled vocabulary. |
| | /"about"/"additionalType":[{schema:DefinedTerm or URI}, ...] | Expect to use a CDIF recommended vocabulary here |
| RDA dateCreated | /"about"/"datePublished": {date time} | Date (and optional time) the Digital object was created|
| RDA dateModified | /"about"/"dateModified" : {date time} | If the DO bit sequence is mutable, specify the last date/time of object modification. Must be consistent with etag and current version number. |
| RDA version | /"about"/"version" : {string} | If tracked, a version for the object, which must follow a total order. Mandatory for all objects with at least one predecessor version. |
| RDA digitalObjectLocation | /"about"/"url":{URL} | If the FDO has a digital representation, it is mandatory that the PID record specifies the location where the FDO can be retrieved, either as an URL or a PID. This is URL in a metadata record for which the target resource is a digital object, or the contentURL or accessURL if the target resource is a non-digital object with one or more distribution representations. Since FDO PID identifies a digital object, there is only one distribution,so use the simple schema:url. |
| FDOF operationInfo | Not implemented by CDIF v1.0 | Some communities want to include a payload information such as a thumbnail image in the case of DiSSCo's Digital Enhanced Specimen FDO. |



# Footnotes

[^1]: https://www.dublincore.org/resources/metadata-basics/

[^2]: https://docs.google.com/document/d/1OF49wTNVuv-6OXlNerhBTqVtHyc7jutTaUHjn6BZCs0

[^3]: https://docs.google.com/spreadsheets/d/1wFuJ4RRlNirnrPfuY_d57I9_pnaNibw4nltNTkruSp0/edit?usp=drive_link

[^4]: https://b2share.eudat.eu,

    https://doi.org/10.23728/B2SHARE.2317B12321764F669C92EBBCF7518164

[^5]: https://en.wikipedia.org/wiki/HTTPRange-14

[^6]: https://www.loc.gov/cds/downloads/FRBR.PDF

[^7]: Weigel, T., Plale, B., Parsons, M., Zhou, G., Luo, Y., Schwardmann, U., Quick, R., Hellström, M., Kurakawa, K. (2018). RDA Recommendation on PID Kernel Information (Version 1). DOI: https://doi.org/10.15497/RDA00031

[^8]: https://www.w3.org/TR/dwbp/#metadata

[^9]: https://www.w3.org/TR/2011/WD-html5-author-20110809/the-meta-element.html

[^10]: https://signposting.org/

[^11]: https://tools.ietf.org/html/rfc8288

[^12]: https://www.w3.org/TR/ldp/#ldpr-hdrs

[^13]: https://tools.ietf.org/html/rfc7231#section-4.3.2

[^14]: https://www.w3.org/TR/ldp-bp/#use-case-2-providing-metadata-in-both-http-headers-and-html-body

[^15]: https://project-open-data.cio.gov/v1.1/schema/catalog.json

[^16]: https://book.oceaninfohub.org/indexing/graphpub.html

[^17]: https://docs.ogc.org/DRAFTS/20-004.html#sc_record-collection-overview

[^18]: https://www.w3.org/TR/dx-prof-conneg/#dfn-profile

[^19]: https://geonetwork-opensource.org/

[^20]: https://github.com/Esri/geoportal-server-catalog

[^21]: https://ckan.org/

[^22]: https://schema.org/ItemList

[^23]: https://www.sitemaps.org/protocol.html

[^24]: https://www.sitemaps.org/protocol.html#extending

[^25]: https://datatracker.ietf.org/doc/rfc9309/

[^26]: https://edisciplinas.usp.br/pluginfile.php/312607/mod_resource/content/1/ISO%20704.pdf

[^27]: PR-PIDProfileAttributes-2.0-20220608 (https://docs.google.com/document/d/1c2mZziq5pIPmLxMHLcYqlWrjYsc2ezGMXvp0E46iljo); Bonino, Guizzardi, Sales 2022 (https://fairdigitalobjectframework.org/), PR-KernelAtributues-2.0 (https://docs.google.com/document/d/1OF49wTNVuv-6OXlNerhBTqVtHyc7jutTaUHjn6BZCs0)

[^28]: https://doi.org/10.1007/s00799-005-0128-x

[^29]: https://www.ietf.org/archive/id/draft-kunze-ark-37.html#name-definition-of-identifier-2

[^30]: https://docs.google.com/document/d/1OF49wTNVuv-6OXlNerhBTqVtHyc7jutTaUHjn6BZCs0

[^31]: based on D2.8 FAIR Semantics Recommendations, Third Iteration, H2020-INFRAEOSC-2018-4, https://doi.org/10.5281/zenodo.667

[^32]: https://www.w3.org/TR/2013/REC-prov-dm-20130430

[^33]: https://schema.org/

[^34]: https://www.ecma-international.org/publications-and-standards/standards/ecma-404/

[^35]: https://www.w3.org/TR/json-ld11/

[^36]: https://www.w3.org/TR/json-ld11-framing/

[^37]: https://www.w3.org/TR/json-ld11/#keywords

[^38]: Bonino, Guizzardi, Sales 2022 (https://fairdigitalobjectframework.org/)

[^39]: https://schema.org/docs/full.html

[^40]: https://github.com/ESIPFed/science-on-schema.org/blob/master/guides/Dataset.md#bounding-boxes

[^41]: https://book.oceaninfohub.org/thematics/spatial/README.html#simple-geosparql-wkt

[^42]: https://github.com/ESIPFed/science-on-schema.org/blob/master/guides/Dataset.md#variables

[^43]: https://github.com/ESIPFed/science-on-schema.org/blob/master/guides/Experimental.md#AdvancedVariableValueType

[^44]: https://schemas.opengis.net/ogcapi/common/part1/1.0/openapi/schemas/link.yaml

[^45]: https://github.com/ESIPFed/science-on-schema.org/blob/master/guides/Dataset.md#funding

[^46]: https://spdx.org/rdf/terms/

[^47]: https://spdx.org/rdf/spdx-terms-v2.1/classes/Checksum___-238837136.html

[^48]: https://docs.google.com/document/d/1QVUR6vlp6s6LxZndMslym9pmM90rFUR6__q3cuXSQf8/edit#heading=h.z337ya



