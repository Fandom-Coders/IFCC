# Sitemap Update Syndication

- Proposal: IFCC-0001

## Overview

We would like a way to notify individuals when the pages of a site have
updated. This proposal suggests a third‐party service which generates
an Atom feed based on a page’s ResourceSync descriptions.

## Problems Solved

While Atom and RSS are good tools for keeping track of updates to
blogs, they are not well‐suited for websites where changes come more
often in the form of changes to *existing* pages rather than new ones.
Examples of such sites include :—

- Shrines
- Galleries
- Profiles (cards)

Neocities offers users with a feed which shows them a history of edits
to the sites they follow. We would like to offer a similar feed for
followers of arbirtary sites.

## Considerations

- Many shrines and profiles are either handwritten or statically
  generated, and served from places like Neocities or GitHub Pages.
  We need a solution which it is easy to maintain through a static
  generation process, and ideally also by hand.

- RSS and Atom feeds have broad support, so they form a good base for
  syndicating updates. However, they are cumbersome to maintain by
  hand, and static site generators aren’t prepared to generate update
  logs the way they are to generate blog feeds.

- The [Sitemaps][] protocol provides an existing solution for listing
  the pages of a site and when they were last updated, but it doesn’t
  offer a log of changes.

- The [ResourceSync][] protocol extends Sitemaps with many additional
  features useful for change tracking.

## Proposed Solution

A third‐party service which reads the ResourceSync XML of a given site
on a periodic (daily?) basis, and maintains an Atom feed which lists
changes to the site.

## Implementation Notes

Broadly speaking, ResourceSync establishes a heirarchy as follows :—

- The **Source Description** describes the entire site, and contains
  links to Capability Lists.

- **Capability Lists** describe specific collections of resources. For
  a simple site, only one Capability List will be required, but this
  allows multiple conceptual “sites” (shrines ⁊·c) to be hosted in
  subdirectories on a single domain. Atom feed generators consequently
  need to generate Atom feeds for each capability list (not each site).

  > The name “Capability List” is confusing but just think of it as a
  > resource collection metadata file.

- **Change Lists** associate URLs with the dates that they were
  modified and the kind of change that was made.

Source Descriptions and Change Lists can additionally be broken up into
multiple files and then referenced from a Source Description Index or
Change List Index.

For more details, see sections 8, 9, and 12 of the [ResourceSync
Specification][ResourceSync].

### For Authors

This section provides requirements for websites to support automated
third‐party Atom feed generation. It is expected that these
requirements will not be manually satisfied by authors but rather
automatically satisfied or enforced by authoring tools like static site
generators.

#### Source Descriptions

Sites **must** provide a ResourceSync Source Description or Source
Description Index XML file at the URL `/.well-known/resourcesync`.
(This requirement might be relaxed with future proposals as other
discovery mechanisms are introduced.)

An example Source Description is as follows :—

```xml
<urlset xmlns="http://www.sitemaps.org/schemas/sitemap/0.9" xmlns:rs="http://www.openarchives.org/rs/terms/">
  <rs:ln rel="describedby" href="https://example.com/info-about-my-website.xml"/>
  <rs:md capability="description"/>
  <url>
    <loc>https://example.com/my-shrine/capabilitylist.xml</loc>
    <rs:md capability="capabilitylist"/>
    <rs:ln rel="describes" href="https://example.com/my-shrine/"/>
    <rs:ln rel="describedby" href="https://example.com/my-shrine/info-about-my-shrine.xml"/>
  </url>
</urlset>
```

Things of note :—

- The `<rs:ln rel="describes">` element is **required** on `<url>`
  elements. The URL provided by the `@href` of this element canonically
  identifies the resource collection being described by this capability
  list.

  > This requirement is not a part of the ResourceSync specification,
  > but is necessary for canonically identifying collections (as
  > opposed to simply their resources).

- The `<rs:ln rel="describedby">` element is **optional** but
  **recommended**, and **should** point to metadata for the described
  resource (for example, site title and authorship information).

  > Metadata formats for resources are open to discussion.

- The `<rs:md>` elements are **required**. The root `<urlset>` element
  **must** have a `<rs:md capability="description">` and the `<url>`
  children **must** each have a `<rs:md capability="capabilitylist">`.

- Multiple Capability Lists **may** be provided in the same Source
  Description, for example if the same domain hosts multiple shrines.

### Capability Lists

Dereferencing the `<loc>`s of `<url>`s with
`@capability="capabilitylist"` **must** result in a Capability List XML
file.

An example Capability List is as follows :—

```xml
<urlset xmlns="http://www.sitemaps.org/schemas/sitemap/0.9" xmlns:rs="http://www.openarchives.org/rs/terms/">
  <rs:ln rel="up" href="https://example.com/description.xml"/>
  <rs:ln rel="describes" href="https://example.com/my-shrine/"/>
  <rs:ln rel="describedby" href="https://example.com/info-about-my-shrine.xml"/>
  <rs:md capability="capabilitylist"/>
  <url>
    <loc>https://example.com/my-shrine/changelist.xml</loc>
    <rs:md capability="changelist"/>
  </url>
</urlset>
```

- The `<rs:ln rel="up">` element is **required** and must point to the
  Source Description for the site.

- The `<rs:ln rel="describes">` element is **required**. The URL
  provided by the `@href` of this element canonically identifies the
  resource collection being described by this capability list.

  > This requirement is not a part of the ResourceSync specification,
  > but is necessary for canonically identifying collections (as
  > opposed to simply their resources).

- The `<rs:ln rel="describedby">` element is **optional** but
  **recommended**, and **should** point to metadata for the described
  resource (for example, collection title and authorship information).

  > Metadata formats for resources are open to discussion.

- The `<rs:md>` elements are **required**. The root `<urlset>` element
  **must** have a `<rs:md capability="capabilitylist">`.

- Capability Lists **must** specify exactly one `<url>` with a
  `<rs:md capability="changelist">`. Other `<url>`s **may** be
  provided.

### Change Lists

Dereferencing the `<loc>`s of `<url>`s with
`@capability="changelist"` **must** result in a Change List or Change
List Index XML file.

An example Change List is as follows :—

```xml
<urlset xmlns="http://www.sitemaps.org/schemas/sitemap/0.9" xmlns:rs="http://www.openarchives.org/rs/terms/">
  <rs:ln rel="up" href="https://example.com/my-shrine/capabilitylist.xml"/>
  <rs:md capability="changelist" from="1972-12-31"/>
  <url>
    <loc>http://example.com/my-shrine/my-page/</loc>
    <rs:md change="updated" datetime="2022-11-06"/>
  </url>
</urlset>
```

- The `<rs:ln rel="up">` element is **required** and must point to the
  Capability List for the collection.

- The `<rs:md>` elements are **required**. The `<rs:md>` which is a
  child of the root `<urlset>` element **must** have a
  `@capability="changelist"` and a `@from` attribute which is a date.
  It **may** also have a `@until` attribute. The `<rs:md>` which is a
  child of a `<url>` element **must** have a `@change` attribute with a
  value of `"created"`, `"updated"`, or `"deleted"`. It **must** also
  have a `@datetime` attribute indicating the time of the change.

  > `@datetime` is not required by the ResourceSync specification, but
  > it is required here to allow implementations to easily determine
  > which changes they have seen before.

- Change Lists do not need to convey every change in the entire history
  of the site, only recent changes. To ensure that harvesters don’t
  miss older changes, it is **recommended** that a Change List Index
  be provided, which links to Change Lists for specific time ranges.

- Multiple changes may be collected in a single Change List.

### For Feed Generators

This section provides requirements for third‐party Atom feed generators
aiming to implement ResourceSync processing in a manner conformant with
this proposal.

#### Sitemaps and Sitemap Indices

Instead of providing Source Descriptions and Change Lists directly,
these may be provided in the form of a Source Description Index and
Change List Index. These are recognizable in that their root element
will be a Sitemap `<sitemapindex>` instead of a `<urlset>`. For these
files, the `<sitemap>` elements provide the actual Source Descriptions
or Change Lists for the site or collection of resources.

#### Finding the Source Description(s)

The ResourceSync specification recommends that a ResourceSync Source
Description (or Source Description Index; see above) be located at the
URL `/.well-known/resourcesync`. As an initial implementation goal,
this **should** be the discovery mechanism supported. Other discovery
mechanisms may be proposed later.

Source Descriptions **must** be a Sitemaps `<urlset>` with a
`<rs:md capability="description">` child. They **may** contain a
`<rs:ln rel="describedby">` element providing metadata for the site.

#### Processing the Source Description(s)

Within each Source Description, each Sitemaps `<url>` with a
`<rs:md capability="capabilitylist">` identifies a collection of
resources, i·e an individual shrine, gallery, or so forth. The `<loc>`
of these URLs point to Capability Lists, which **should** be processed
as below.

Each collection of resources (i·e, Capability List) **should** be given
its own Atom feed. Implementations **may** also implement a source‐wide
Atom feed.

#### Processing the Capability List

Each Capability List in the Source Description **must** be a Sitemaps
`<urlset>` with a `<rs:md capability="capabilitylist">` child. It
**must** contain a `<rs:ln rel="describes">` element identifying the
collection of resources being described. It **may** contain a
`<rs:ln rel="describedby">` element providing metadata for the resource
collection.

If the Capability List includes a Sitemaps `<url>` with a
`<rs:md capability="changelist">`, the list of changes **must** be
pulled from the linked Change List or Change List Index. Otherwise, no
changes can be determined.

#### Processing the Change List Index and Change List

Each `<sitemap>` entry in a Change List Index **should** have a
`<rs:md>` element with a `@from` and possibly `@until` attribute giving
the time span that the entry covers. This can be used to ignore changes
which have already been processed.

Each `<url>` entry in a Change List **must** have a `<rs:md>` element
wiht a `@change` attribute describing the change; possible values are
`"created"`, `"updated"`, and `"deleted"`. These entries **should**
also have a `@datetime` giving the time of the change, and
implementations **must** ignore entries with no `@datetime` specified.

#### Checking for Updates

Checking for updates for a given resource collection requires the
following (approximate) steps :—

1. Fetch the Site Description from `/.well-known/resourcesync`.

   > This URL may give a Site Description Index, in which case the
   > referenced Site Descriptions will need to be processed.

2. Fetch the Capability List with a `<rs:ln rel="describes">` which
   points to the resource collection.

3. Fetch the Change List pointed to from the Capability List.

   > The Capability List may point to a Change List Index, in which
   > case all Change Lists which do not have an `@until` attribute
   > which predates the time of last processing will need to be
   > processed.

4. Collect all the changes which have a `@datetime` which is after the
   time of last processing.

5. Create a new Atom `<entry>` which summarizes the changes and add it
   to the Atom feed.

[ResourceSync]: <http://www.openarchives.org/rs/1.1/resourcesync>
[Sitemaps]: <https://www.sitemaps.org>
[kibigo!]: <https://go.KIBI.family/About/#me>
