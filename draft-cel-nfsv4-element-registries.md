---
title: "Registries of Network File System Version 4 Protocol Elements"
abbrev: "NFSv4 Element Registries"
category: std

docname: draft-cel-nfsv4-element-registries-latest
submissiontype: IETF
ipr: trust200902
updates: 8178
stand_alone: yes
pi: [toc, sortrefs, symrefs, docmapping]
v: 3
area: "Web and Internet Transport"
workgroup: "Network File System Version 4"
keyword:
 - NFS
 - COMPOUND
 - nfsstat4
 - fattr4
 - IANA

venue:
  group: "Network File System Version 4"
  type: "Working Group"
  mail: "nfsv4@ietf.org"
  arch: "https://mailarchive.ietf.org/arch/browse/nfsv4/"
  github: "chucklever/i-d-element-registries"
  latest: "https://chucklever.github.io/i-d-element-registries/draft-cel-nfsv4-element-registries.html"

author:
 -
    fullname: Charles Lever
    role: editor
    country: United States of America
    email: cel-ietf@chucklever.net

normative:
  RFC5662:
  RFC7120:
  RFC7530:
  RFC7862:
  RFC7931:
  RFC8126:
  RFC8154:
  RFC8178:
  RFC8275:
  RFC8276:
  RFC8587:
  RFC8881:
  RFC9754:

informative:
  RFC1813:
  RFC4506:
  RFC5661:
  RFC7531:
  RFC7863:

--- abstract

Several numbered NFS version 4 protocol elements, among them
operations, callback operations, status codes, file attributes, and
the flags of the ACCESS and OPEN operations, are identified by
values that no IANA registry records, so protocol
extensions under development at the same time can assign the same
value to different elements.  This document requests an IANA
registry for each of these element types, populates the registries
from published RFCs, and requires future elements to obtain their
values through them.  It updates RFC 8178.


--- middle

# Introduction {#intro}

{{RFC7530}}, {{RFC8881}}, and {{RFC7862}} together specify three
minor versions of the Network File System (NFS) version 4 protocol.
Each minor version has exactly two RPC procedures: NULL and
COMPOUND.  A COMPOUND request carries a sequence of operations, and
every NFSv4 file access, locking, session, and pNFS action is
expressed as one of those operations.  The callback program that a
server uses to contact a client likewise has two procedures, CB_NULL
and CB_COMPOUND, and a CB_COMPOUND request carries a sequence of
callback operations.  Each operation returns a status code of type
nfsstat4, and file system object metadata is carried in the fattr4
data type as a bitmask and list of individual attributes.  Several
operations, among them ACCESS and OPEN, carry flag words whose bits
and values select the behavior requested or report the behavior
provided.

The above specifications (and others) assign a numeric value to
each operation, callback operation, status code, attribute, and
flag.  The value is the element's constant in an XDR enumeration,
its bit position in a bitmask, or its bit or value within a flag
word, and it identifies the element on the wire.

{{RFC8178}} specifies a set of permitted mechanisms to extend the
NFSv4 protocol, but no registry records which values have been
assigned.  Assignment of new values is currently handled ad hoc by
the nfsv4 Working Group, and authors of concurrent extensions can
select the same value for different elements.  {{Section 8.6 of
RFC8276}}, for example, records that its values were checked by
hand against the specifications the authors were aware of.

It has been additionally observed that there is no single place
that maps each element to the RFC (or RFCs) where that element is
specified.  The Working Group assignment mechanism is not how
protocol-element-to-numeric-value mappings are commonly handled
elsewhere.

To address these concerns, this document requests the creation of
an IANA registry for each element type, so that value assignment is
managed via a well-known and authoritative mechanism.  The element
types covered are those to which at least one RFC published since
{{RFC5661}} has added a value.  The document provides an initial set
of entries for each registry based on values in published RFCs to
date.  Lastly, a new assignment mechanism is normatively specified
for these elements, updating {{RFC8178}}.

# Requirements Language

{::boilerplate bcp14-tagged}

# Allocation of Protocol Element Values {#allocation}

## Background

Each element covered by this document is identified by a
non-negative integer.  For an operation or callback operation, that
integer is the value of the constant in the nfs_opnum4 or
nfs_cb_opnum4 enumeration (for example, OP_GETATTR has the value 9),
and it selects the arm of the union that carries the operation's
arguments and results.  For a status code, it is the value of the
constant in the nfsstat4 enumeration (for example, NFS4ERR_NOENT has
the value 2).  For an attribute, it is both the bit position of the
attribute within the bitmap4 that appears in a fattr4 and the value
of the corresponding XDR constant (for example, FATTR4_SIZE has the
value 4).  For a flag, it is the value of the XDR constant that a
sender combines into a flag word (for example, ACCESS4_READ has the
value 0x00000001).

{{Section 4.2 of RFC8178}} permits the addition of previously
unspecified operation codes and attributes, of new values to
existing enums, and of previously unassigned bit values to a flag
word, as XDR extensions, and {{Section 6 of RFC8178}}
requires that such an extension to an existing minor version be
published as a Proposed Standard.  {{RFC8178}} also forbids the
deletion or reuse of a value once assigned.  However, {{RFC8178}}
does not say how the numeric value for a new element is chosen.  In
practice, an author of an Internet-Draft selects the next value
after the highest one the author is aware of.  When two drafts are
in progress concurrently, both may select the same value, and the
collision is discovered only when one of the documents is revised or
when implementations of the two features are combined.

## The Registries {#mechanism}

This document creates the registries listed in {{iana}} as the
single authoritative sources of value assignments for the elements
they cover.  The rules below apply to every registry.  From the
publication of this document onward:

* As required by {{Section 6 of RFC8178}}, a new element is
  specified in a document published as a Proposed Standard.  The
  registration policy for each registry is therefore Standards
  Action with Expert Review ({{Sections 4.5 and 4.9 of RFC8126}}),
  which admits exactly that class of document and matches the
  policy of the other NFSv4 registries created by {{Section 22 of
  RFC8881}}.  The role of the Designated Expert is described in
  {{expert}}.

* The document that specifies a new element MUST include an IANA
  Considerations section requesting that the element be added to
  the appropriate registry, and MUST obtain the element's numeric
  value through that request.  The XDR constant for the element is
  derived from the registered value; a document MUST NOT specify a
  constant that differs from the registered value.

* To eliminate collisions between concurrently developed extensions,
  values MAY be allocated before publication using the early
  allocation procedure of {{RFC7120}}.  A Working Group document that
  introduces a new element SHOULD request early allocation once the
  Working Group has adopted it, and MUST NOT use a value that has
  not been either registered or early-allocated.

* Values are assigned sequentially, starting from the lowest
  unassigned value that is not reserved and is within the range the
  registry makes available for assignment.  A value that has been
  assigned is never assigned again, even if the corresponding
  element has since been withdrawn from every minor version, because
  {{Section 4.2 of RFC8178}} prohibits reuse of a value.

* An element is never removed from a registry.  A document that
  withdraws an element from one or more minor versions updates the
  entry's Versions column to the minor versions in which the element
  remains valid and adds itself to the entry's Reference column
  ({{iana-columns}}).  The value remains allocated.

* Per {{Section 8.2 of RFC8178}}, an element introduced in one minor
  version is available in all later minor versions unless a later
  minor version explicitly removes support for it.  A document that
  carries an element unchanged into a new minor version does not
  change the element's registry entry.

## Designated Expert Guidance {#expert}

The Designated Expert's review is confined to the registration
itself, not the merits of the element, which are for the IETF
consensus process that produced the Proposed Standard.[^ednote]  The
guidance applies to every registry.  The expert confirms that:

* The request names the registry that matches the element's type
  and, for an operation, its direction: the "NFSv4 Operations"
  registry for an operation sent in a COMPOUND, and the "NFSv4
  Callback Operations" registry for one sent in a CB_COMPOUND.

* The requested value is the lowest unassigned value in that
  registry that is not reserved and is available for assignment, or
  was previously early-allocated to the same document.

* The name is unique within the registry and follows the naming
  convention given in the registry's description in {{iana}}.

* The element's XDR definition (the arguments and results of an
  operation, or the type of an attribute) appears in the requesting
  document or in a document it normatively references.

* The Versions column is consistent with the minor versions the
  requesting document says the element is valid in, and the
  Reference column cites the requesting document.

For a document that withdraws an existing element from one or more
minor versions, the expert additionally confirms that the document
states which minor versions the change applies to, that the
requested Versions value matches that scope, and that the document
does not reuse the element's value, renumber the element, or request
that the entry be removed.

[^ednote]: Editor's note: skeletal; to be expanded if the Working
    Group wants the expert to check more than registry hygiene.

## Updates to RFC 8178 {#updates-8178}

This document updates {{RFC8178}} as follows.

{{Section 4.2 of RFC8178}} describes the addition of previously
unspecified operation codes and attributes, of new values to
existing enums, and of previously unassigned bit values to a flag
word, as XDR extensions.  This document adds the
requirement that, for each element type for which {{iana}} creates
a registry, the numeric value of a new element be allocated from
that registry as specified in {{mechanism}}, rather than being
chosen by the author of the extension.

{{Section 6 of RFC8178}} continues to govern the publication
requirements for an extension that introduces a new element.  The
registries do not relax those requirements; they only record the
outcome of the process.

Nothing in this document changes the rules in {{RFC8178}} for XDR
extensions to elements not covered by a registry in {{iana}}, nor
the rules for creating new minor versions.

# Security Considerations {#security}

This document creates IANA registries and specifies how values in
those registries are allocated.  It introduces no new protocol
behavior and therefore no new security considerations beyond those
of the documents that define the individual elements.

# IANA Considerations {#iana}

## NFSv4 Parameters Registry Group {#iana-group}

IANA is requested to create a registry group titled "Network File
System Version 4 (NFSv4) Parameters" and to place in it the
registries described in {{iana-ops}}, {{iana-cb-ops}},
{{iana-status}}, {{iana-fattr4}}, {{iana-access}},
{{iana-open-access}}, {{iana-open-result}}, and
{{iana-open-deleg}}.  Future documents may add
registries for other NFSv4 numeric protocol elements to this group.
This document does not request that the existing NFSv4 registries
created by {{Section 22 of RFC8881}} be moved into the group.

## Registry Format {#iana-columns}

Each registry created by this document has the following columns.
A registry's description below may add a column.

Value:
: The element's numeric value.  Values are non-negative integers.
  A registry of flags records them in hexadecimal.

Name:
: The element's name as used in the specifying document, or
  "Reserved" for a value that is not available for assignment.
  The registry's description gives the convention that derives the
  element's XDR constant from the Name.  A document that registers
  a new element MUST follow that convention.

Versions:
: The NFSv4 minor versions in which the element is valid, as a
  range.  A range that ends in "+" (for example, "4.1+") is open:
  the element is valid in the first minor version named and in
  every later minor version.  A range without "+" is closed: "4.0"
  means valid in NFSv4.0 only, and "4.1-4.2" means valid in NFSv4.1
  and NFSv4.2 only.  The word "none" means the element has been
  withdrawn from every minor version.  The column is empty for a
  Reserved value.

Reference:
: The RFC that specifies the minor version in which the element was
  introduced, or the RFC that added the element to an existing
  minor version, followed by each later RFC that changes the
  element's definition or its Versions.  An RFC that restates the
  element unchanged in a new minor version is not cited.

The registration policy for each registry is Standards Action with
Expert Review ({{Sections 4.5 and 4.9 of RFC8126}}).  Guidance for
the Designated Expert is in {{expert}}.  Early allocation of values
is permitted per {{RFC7120}}.  Values are assigned sequentially from
the lowest unassigned value that is not reserved and is available
for assignment; in a registry of flags, the lowest unassigned bit
is assigned unless the registry's description says otherwise.
Assigned values MUST NOT be reused, and entries MUST NOT be removed
(see {{mechanism}}).

## NFSv4 Operations Registry {#iana-ops}

IANA is requested to create a registry titled "NFSv4 Operations".

The Value is the operation's constant in the nfs_opnum4 enumeration
and the discriminant that selects the operation's arm of the
nfs_argop4 and nfs_resop4 unions.  The XDR constant is the Name
prefixed with "OP_" (for example, OP_GETATTR for the operation named
GETATTR).  By convention, the XDR structures carrying the
operation's arguments and results are the Name suffixed with
"4args" and "4res"; the registry does not record these names.

Values 0 through 2 are Reserved because the base specifications
begin the nfs_opnum4 enumeration at 3.[^reserved]  All other
unassigned values are available for assignment.

[^reserved]: Editor's note: the base specifications give no reason
    for starting at 3.  If the Working Group would rather leave 0
    through 2 unassigned, the three Reserved entries can be dropped
    from both operation registries.

| Value | Name | Versions | Reference |
|------:|------|----------|-----------|
| 0 | Reserved | | |
| 1 | Reserved | | |
| 2 | Reserved | | |
| 3 | ACCESS | 4.0+ | {{RFC7530}} {{RFC8276}} |
| 4 | CLOSE | 4.0+ | {{RFC7530}} |
| 5 | COMMIT | 4.0+ | {{RFC7530}} |
| 6 | CREATE | 4.0+ | {{RFC7530}} |
| 7 | DELEGPURGE | 4.0+ | {{RFC7530}} |
| 8 | DELEGRETURN | 4.0+ | {{RFC7530}} |
| 9 | GETATTR | 4.0+ | {{RFC7530}} |
| 10 | GETFH | 4.0+ | {{RFC7530}} |
| 11 | LINK | 4.0+ | {{RFC7530}} |
| 12 | LOCK | 4.0+ | {{RFC7530}} |
| 13 | LOCKT | 4.0+ | {{RFC7530}} |
| 14 | LOCKU | 4.0+ | {{RFC7530}} |
| 15 | LOOKUP | 4.0+ | {{RFC7530}} |
| 16 | LOOKUPP | 4.0+ | {{RFC7530}} |
| 17 | NVERIFY | 4.0+ | {{RFC7530}} |
| 18 | OPEN | 4.0+ | {{RFC7530}} {{RFC9754}} |
| 19 | OPENATTR | 4.0+ | {{RFC7530}} |
| 20 | OPEN_CONFIRM | 4.0 | {{RFC7530}} {{RFC8881}} |
| 21 | OPEN_DOWNGRADE | 4.0+ | {{RFC7530}} |
| 22 | PUTFH | 4.0+ | {{RFC7530}} |
| 23 | PUTPUBFH | 4.0+ | {{RFC7530}} |
| 24 | PUTROOTFH | 4.0+ | {{RFC7530}} |
| 25 | READ | 4.0+ | {{RFC7530}} |
| 26 | READDIR | 4.0+ | {{RFC7530}} |
| 27 | READLINK | 4.0+ | {{RFC7530}} |
| 28 | REMOVE | 4.0+ | {{RFC7530}} |
| 29 | RENAME | 4.0+ | {{RFC7530}} |
| 30 | RENEW | 4.0 | {{RFC7530}} {{RFC8881}} |
| 31 | RESTOREFH | 4.0+ | {{RFC7530}} |
| 32 | SAVEFH | 4.0+ | {{RFC7530}} |
| 33 | SECINFO | 4.0+ | {{RFC7530}} |
| 34 | SETATTR | 4.0+ | {{RFC7530}} |
| 35 | SETCLIENTID | 4.0 | {{RFC7530}} {{RFC7931}} {{RFC8881}} |
| 36 | SETCLIENTID_CONFIRM | 4.0 | {{RFC7530}} {{RFC8881}} |
| 37 | VERIFY | 4.0+ | {{RFC7530}} |
| 38 | WRITE | 4.0+ | {{RFC7530}} |
| 39 | RELEASE_LOCKOWNER | 4.0 | {{RFC7530}} {{RFC8881}} |
| 40 | BACKCHANNEL_CTL | 4.1+ | {{RFC8881}} |
| 41 | BIND_CONN_TO_SESSION | 4.1+ | {{RFC8881}} |
| 42 | EXCHANGE_ID | 4.1+ | {{RFC8881}} {{RFC7862}} |
| 43 | CREATE_SESSION | 4.1+ | {{RFC8881}} |
| 44 | DESTROY_SESSION | 4.1+ | {{RFC8881}} |
| 45 | FREE_STATEID | 4.1+ | {{RFC8881}} |
| 46 | GET_DIR_DELEGATION | 4.1+ | {{RFC8881}} |
| 47 | GETDEVICEINFO | 4.1+ | {{RFC8881}} |
| 48 | GETDEVICELIST | 4.1 | {{RFC8881}} {{RFC7862}} |
| 49 | LAYOUTCOMMIT | 4.1+ | {{RFC8881}} |
| 50 | LAYOUTGET | 4.1+ | {{RFC8881}} |
| 51 | LAYOUTRETURN | 4.1+ | {{RFC8881}} |
| 52 | SECINFO_NO_NAME | 4.1+ | {{RFC8881}} |
| 53 | SEQUENCE | 4.1+ | {{RFC8881}} |
| 54 | SET_SSV | 4.1+ | {{RFC8881}} |
| 55 | TEST_STATEID | 4.1+ | {{RFC8881}} |
| 56 | WANT_DELEGATION | 4.1+ | {{RFC8881}} |
| 57 | DESTROY_CLIENTID | 4.1+ | {{RFC8881}} |
| 58 | RECLAIM_COMPLETE | 4.1+ | {{RFC8881}} |
| 59 | ALLOCATE | 4.2+ | {{RFC7862}} |
| 60 | COPY | 4.2+ | {{RFC7862}} |
| 61 | COPY_NOTIFY | 4.2+ | {{RFC7862}} |
| 62 | DEALLOCATE | 4.2+ | {{RFC7862}} |
| 63 | IO_ADVISE | 4.2+ | {{RFC7862}} |
| 64 | LAYOUTERROR | 4.2+ | {{RFC7862}} |
| 65 | LAYOUTSTATS | 4.2+ | {{RFC7862}} |
| 66 | OFFLOAD_CANCEL | 4.2+ | {{RFC7862}} |
| 67 | OFFLOAD_STATUS | 4.2+ | {{RFC7862}} |
| 68 | READ_PLUS | 4.2+ | {{RFC7862}} |
| 69 | SEEK | 4.2+ | {{RFC7862}} |
| 70 | WRITE_SAME | 4.2+ | {{RFC7862}} |
| 71 | CLONE | 4.2+ | {{RFC7862}} |
| 72 | GETXATTR | 4.2+ | {{RFC8276}} |
| 73 | SETXATTR | 4.2+ | {{RFC8276}} |
| 74 | LISTXATTRS | 4.2+ | {{RFC8276}} |
| 75 | REMOVEXATTR | 4.2+ | {{RFC8276}} |
| 10044 | ILLEGAL | 4.0+ | {{RFC7530}} |
{: #ops-initial title="Initial contents of the NFSv4 Operations registry"}

Values 76 through 10043, and 10045 and above, are unassigned.

## NFSv4 Callback Operations Registry {#iana-cb-ops}

IANA is requested to create a registry titled "NFSv4 Callback
Operations".

The Value is the callback operation's constant in the nfs_cb_opnum4
enumeration and the discriminant that selects the operation's arm
of the nfs_cb_argop4 and nfs_cb_resop4 unions.  Every Name begins
with "CB_".  The XDR constant is the Name prefixed with "OP_" (for
example, OP_CB_GETATTR for the operation named CB_GETATTR).  By
convention, the XDR structures carrying the operation's arguments
and results are the Name suffixed with "4args" and "4res"; the
registry does not record these names.

Values 0 through 2 are Reserved for the reason given in
{{iana-ops}}.  All other unassigned values are available for
assignment.

| Value | Name | Versions | Reference |
|------:|------|----------|-----------|
| 0 | Reserved | | |
| 1 | Reserved | | |
| 2 | Reserved | | |
| 3 | CB_GETATTR | 4.0+ | {{RFC7530}} |
| 4 | CB_RECALL | 4.0+ | {{RFC7530}} |
| 5 | CB_LAYOUTRECALL | 4.1+ | {{RFC8881}} |
| 6 | CB_NOTIFY | 4.1+ | {{RFC8881}} |
| 7 | CB_PUSH_DELEG | 4.1+ | {{RFC8881}} |
| 8 | CB_RECALL_ANY | 4.1+ | {{RFC8881}} |
| 9 | CB_RECALLABLE_OBJ_AVAIL | 4.1+ | {{RFC8881}} |
| 10 | CB_RECALL_SLOT | 4.1+ | {{RFC8881}} |
| 11 | CB_SEQUENCE | 4.1+ | {{RFC8881}} |
| 12 | CB_WANTS_CANCELLED | 4.1+ | {{RFC8881}} |
| 13 | CB_NOTIFY_LOCK | 4.1+ | {{RFC8881}} |
| 14 | CB_NOTIFY_DEVICEID | 4.1+ | {{RFC8881}} |
| 15 | CB_OFFLOAD | 4.2+ | {{RFC7862}} |
| 10044 | CB_ILLEGAL | 4.0+ | {{RFC7530}} |
{: #cb-ops-initial title="Initial contents of the NFSv4 Callback Operations registry"}

Values 16 through 10043, and 10045 and above, are unassigned.

## NFSv4 Status Codes Registry {#iana-status}

IANA is requested to create a registry titled "NFSv4 Status Codes".

The Value is the status code's constant in the nfsstat4
enumeration.  The Name is the name of that constant.  The name of a
code that indicates failure is upper case and is prefixed with
"NFS4ERR_" (for example, NFS4ERR_NOENT); the only code that does not
indicate failure is NFS4_OK.

Values below 10000 mirror the NFS version 3 status codes
{{RFC1813}}, which in turn match traditional errno values, and are
not available for assignment.  Value 10002 is Reserved because the
NFSv3 status code with that value, NFS3ERR_NOT_SYNC, has no NFSv4
counterpart, and value 10073 is Reserved because {{RFC5662}} left it
unused.  All other unassigned values of 10001 or above are available
for assignment.

The XDR constants for the base minor versions appear in the
companion XDR descriptions {{RFC7531}}, {{RFC5662}}, and
{{RFC7863}}.

| Value | Name | Versions | Reference |
|------:|------|----------|-----------|
| 0 | NFS4_OK | 4.0+ | {{RFC7530}} |
| 1 | NFS4ERR_PERM | 4.0+ | {{RFC7530}} |
| 2 | NFS4ERR_NOENT | 4.0+ | {{RFC7530}} |
| 5 | NFS4ERR_IO | 4.0+ | {{RFC7530}} |
| 6 | NFS4ERR_NXIO | 4.0 | {{RFC7530}} {{RFC8881}} |
| 13 | NFS4ERR_ACCESS | 4.0+ | {{RFC7530}} |
| 17 | NFS4ERR_EXIST | 4.0+ | {{RFC7530}} |
| 18 | NFS4ERR_XDEV | 4.0+ | {{RFC7530}} |
| 20 | NFS4ERR_NOTDIR | 4.0+ | {{RFC7530}} |
| 21 | NFS4ERR_ISDIR | 4.0+ | {{RFC7530}} |
| 22 | NFS4ERR_INVAL | 4.0+ | {{RFC7530}} |
| 27 | NFS4ERR_FBIG | 4.0+ | {{RFC7530}} |
| 28 | NFS4ERR_NOSPC | 4.0+ | {{RFC7530}} |
| 30 | NFS4ERR_ROFS | 4.0+ | {{RFC7530}} |
| 31 | NFS4ERR_MLINK | 4.0+ | {{RFC7530}} |
| 63 | NFS4ERR_NAMETOOLONG | 4.0+ | {{RFC7530}} |
| 66 | NFS4ERR_NOTEMPTY | 4.0+ | {{RFC7530}} |
| 69 | NFS4ERR_DQUOT | 4.0+ | {{RFC7530}} |
| 70 | NFS4ERR_STALE | 4.0+ | {{RFC7530}} |
| 10001 | NFS4ERR_BADHANDLE | 4.0+ | {{RFC7530}} |
| 10002 | Reserved | | |
| 10003 | NFS4ERR_BAD_COOKIE | 4.0+ | {{RFC7530}} |
| 10004 | NFS4ERR_NOTSUPP | 4.0+ | {{RFC7530}} |
| 10005 | NFS4ERR_TOOSMALL | 4.0+ | {{RFC7530}} |
| 10006 | NFS4ERR_SERVERFAULT | 4.0+ | {{RFC7530}} |
| 10007 | NFS4ERR_BADTYPE | 4.0+ | {{RFC7530}} |
| 10008 | NFS4ERR_DELAY | 4.0+ | {{RFC7530}} |
| 10009 | NFS4ERR_SAME | 4.0+ | {{RFC7530}} |
| 10010 | NFS4ERR_DENIED | 4.0+ | {{RFC7530}} |
| 10011 | NFS4ERR_EXPIRED | 4.0+ | {{RFC7530}} |
| 10012 | NFS4ERR_LOCKED | 4.0+ | {{RFC7530}} |
| 10013 | NFS4ERR_GRACE | 4.0+ | {{RFC7530}} |
| 10014 | NFS4ERR_FHEXPIRED | 4.0+ | {{RFC7530}} |
| 10015 | NFS4ERR_SHARE_DENIED | 4.0+ | {{RFC7530}} |
| 10016 | NFS4ERR_WRONGSEC | 4.0+ | {{RFC7530}} |
| 10017 | NFS4ERR_CLID_INUSE | 4.0+ | {{RFC7530}} |
| 10018 | NFS4ERR_RESOURCE | 4.0 | {{RFC7530}} {{RFC5662}} |
| 10019 | NFS4ERR_MOVED | 4.0+ | {{RFC7530}} |
| 10020 | NFS4ERR_NOFILEHANDLE | 4.0+ | {{RFC7530}} |
| 10021 | NFS4ERR_MINOR_VERS_MISMATCH | 4.0+ | {{RFC7530}} |
| 10022 | NFS4ERR_STALE_CLIENTID | 4.0+ | {{RFC7530}} |
| 10023 | NFS4ERR_STALE_STATEID | 4.0 | {{RFC7530}} {{RFC8881}} |
| 10024 | NFS4ERR_OLD_STATEID | 4.0+ | {{RFC7530}} |
| 10025 | NFS4ERR_BAD_STATEID | 4.0+ | {{RFC7530}} |
| 10026 | NFS4ERR_BAD_SEQID | 4.0 | {{RFC7530}} {{RFC8881}} |
| 10027 | NFS4ERR_NOT_SAME | 4.0+ | {{RFC7530}} |
| 10028 | NFS4ERR_LOCK_RANGE | 4.0+ | {{RFC7530}} |
| 10029 | NFS4ERR_SYMLINK | 4.0+ | {{RFC7530}} |
| 10030 | NFS4ERR_RESTOREFH | 4.0 | {{RFC7530}} {{RFC8881}} |
| 10031 | NFS4ERR_LEASE_MOVED | 4.0 | {{RFC7530}} {{RFC8881}} |
| 10032 | NFS4ERR_ATTRNOTSUPP | 4.0+ | {{RFC7530}} |
| 10033 | NFS4ERR_NO_GRACE | 4.0+ | {{RFC7530}} |
| 10034 | NFS4ERR_RECLAIM_BAD | 4.0+ | {{RFC7530}} |
| 10035 | NFS4ERR_RECLAIM_CONFLICT | 4.0+ | {{RFC7530}} |
| 10036 | NFS4ERR_BADXDR | 4.0+ | {{RFC7530}} |
| 10037 | NFS4ERR_LOCKS_HELD | 4.0+ | {{RFC7530}} |
| 10038 | NFS4ERR_OPENMODE | 4.0+ | {{RFC7530}} |
| 10039 | NFS4ERR_BADOWNER | 4.0+ | {{RFC7530}} |
| 10040 | NFS4ERR_BADCHAR | 4.0+ | {{RFC7530}} |
| 10041 | NFS4ERR_BADNAME | 4.0+ | {{RFC7530}} |
| 10042 | NFS4ERR_BAD_RANGE | 4.0+ | {{RFC7530}} |
| 10043 | NFS4ERR_LOCK_NOTSUPP | 4.0+ | {{RFC7530}} |
| 10044 | NFS4ERR_OP_ILLEGAL | 4.0+ | {{RFC7530}} |
| 10045 | NFS4ERR_DEADLOCK | 4.0+ | {{RFC7530}} |
| 10046 | NFS4ERR_FILE_OPEN | 4.0+ | {{RFC7530}} |
| 10047 | NFS4ERR_ADMIN_REVOKED | 4.0+ | {{RFC7530}} |
| 10048 | NFS4ERR_CB_PATH_DOWN | 4.0+ | {{RFC7530}} |
| 10049 | NFS4ERR_BADIOMODE | 4.1+ | {{RFC8881}} |
| 10050 | NFS4ERR_BADLAYOUT | 4.1+ | {{RFC8881}} |
| 10051 | NFS4ERR_BAD_SESSION_DIGEST | 4.1+ | {{RFC8881}} |
| 10052 | NFS4ERR_BADSESSION | 4.1+ | {{RFC8881}} |
| 10053 | NFS4ERR_BADSLOT | 4.1+ | {{RFC8881}} |
| 10054 | NFS4ERR_COMPLETE_ALREADY | 4.1+ | {{RFC8881}} |
| 10055 | NFS4ERR_CONN_NOT_BOUND_TO_SESSION | 4.1+ | {{RFC8881}} |
| 10056 | NFS4ERR_DELEG_ALREADY_WANTED | 4.1+ | {{RFC8881}} |
| 10057 | NFS4ERR_BACK_CHAN_BUSY | 4.1+ | {{RFC8881}} |
| 10058 | NFS4ERR_LAYOUTTRYLATER | 4.1+ | {{RFC8881}} |
| 10059 | NFS4ERR_LAYOUTUNAVAILABLE | 4.1+ | {{RFC8881}} |
| 10060 | NFS4ERR_NOMATCHING_LAYOUT | 4.1+ | {{RFC8881}} |
| 10061 | NFS4ERR_RECALLCONFLICT | 4.1+ | {{RFC8881}} |
| 10062 | NFS4ERR_UNKNOWN_LAYOUTTYPE | 4.1+ | {{RFC8881}} |
| 10063 | NFS4ERR_SEQ_MISORDERED | 4.1+ | {{RFC8881}} |
| 10064 | NFS4ERR_SEQUENCE_POS | 4.1+ | {{RFC8881}} |
| 10065 | NFS4ERR_REQ_TOO_BIG | 4.1+ | {{RFC8881}} |
| 10066 | NFS4ERR_REP_TOO_BIG | 4.1+ | {{RFC8881}} |
| 10067 | NFS4ERR_REP_TOO_BIG_TO_CACHE | 4.1+ | {{RFC8881}} |
| 10068 | NFS4ERR_RETRY_UNCACHED_REP | 4.1+ | {{RFC8881}} |
| 10069 | NFS4ERR_UNSAFE_COMPOUND | 4.1+ | {{RFC8881}} |
| 10070 | NFS4ERR_TOO_MANY_OPS | 4.1+ | {{RFC8881}} |
| 10071 | NFS4ERR_OP_NOT_IN_SESSION | 4.1+ | {{RFC8881}} |
| 10072 | NFS4ERR_HASH_ALG_UNSUPP | 4.1+ | {{RFC8881}} |
| 10073 | Reserved | | |
| 10074 | NFS4ERR_CLIENTID_BUSY | 4.1+ | {{RFC8881}} |
| 10075 | NFS4ERR_PNFS_IO_HOLE | 4.1+ | {{RFC8881}} |
| 10076 | NFS4ERR_SEQ_FALSE_RETRY | 4.1+ | {{RFC8881}} |
| 10077 | NFS4ERR_BAD_HIGH_SLOT | 4.1+ | {{RFC8881}} |
| 10078 | NFS4ERR_DEADSESSION | 4.1+ | {{RFC8881}} |
| 10079 | NFS4ERR_ENCR_ALG_UNSUPP | 4.1+ | {{RFC8881}} |
| 10080 | NFS4ERR_PNFS_NO_LAYOUT | 4.1+ | {{RFC8881}} |
| 10081 | NFS4ERR_NOT_ONLY_OP | 4.1+ | {{RFC8881}} |
| 10082 | NFS4ERR_WRONG_CRED | 4.1+ | {{RFC8881}} |
| 10083 | NFS4ERR_WRONG_TYPE | 4.1+ | {{RFC8881}} |
| 10084 | NFS4ERR_DIRDELEG_UNAVAIL | 4.1+ | {{RFC8881}} |
| 10085 | NFS4ERR_REJECT_DELEG | 4.1+ | {{RFC8881}} |
| 10086 | NFS4ERR_RETURNCONFLICT | 4.1+ | {{RFC8881}} |
| 10087 | NFS4ERR_DELEG_REVOKED | 4.1+ | {{RFC8881}} |
| 10088 | NFS4ERR_PARTNER_NOTSUPP | 4.2+ | {{RFC7862}} |
| 10089 | NFS4ERR_PARTNER_NO_AUTH | 4.2+ | {{RFC7862}} |
| 10090 | NFS4ERR_UNION_NOTSUPP | 4.2+ | {{RFC7862}} |
| 10091 | NFS4ERR_OFFLOAD_DENIED | 4.2+ | {{RFC7862}} |
| 10092 | NFS4ERR_WRONG_LFS | 4.2+ | {{RFC7862}} |
| 10093 | NFS4ERR_BADLABEL | 4.2+ | {{RFC7862}} |
| 10094 | NFS4ERR_OFFLOAD_NO_REQS | 4.2+ | {{RFC7862}} |
| 10095 | NFS4ERR_NOXATTR | 4.2+ | {{RFC8276}} |
| 10096 | NFS4ERR_XATTR2BIG | 4.2+ | {{RFC8276}} |
{: #nfsstat4-initial title="Initial contents of the NFSv4 Status Codes registry"}

Values 10097 and above are unassigned.

## NFSv4 fattr4 Attributes Registry {#iana-fattr4}

IANA is requested to create a registry titled "NFSv4 fattr4
Attributes".

The Value is both the attribute's bit position in the attribute
bitmap and the value of its XDR constant.  The XDR constant is the
Name in upper case prefixed with "FATTR4_" (for example, FATTR4_SIZE
for the attribute named size), and the XDR typedef that specifies
the attribute's value format is the Name prefixed with "fattr4_"
(for example, fattr4_size).

This registry has one additional column:

XDR Type:
: The XDR ({{RFC4506}}) data type of the attribute's value, as it
  appears in the typedef for the attribute in the specifying
  document.  For the base minor versions, the typedefs appear in
  the companion XDR descriptions {{RFC7531}}, {{RFC5662}}, and
  {{RFC7863}}.

No value is Reserved, and all unassigned values are available for
assignment.

| Value | Name | XDR Type | Versions | Reference |
|------:|------|----------|----------|-----------|
| 0 | supported_attrs | bitmap4 | 4.0+ | {{RFC7530}} {{RFC8154}} |
| 1 | type | nfs_ftype4 | 4.0+ | {{RFC7530}} |
| 2 | fh_expire_type | uint32_t | 4.0+ | {{RFC7530}} |
| 3 | change | changeid4 | 4.0+ | {{RFC7530}} {{RFC7862}} {{RFC9754}} |
| 4 | size | uint64_t | 4.0+ | {{RFC7530}} |
| 5 | link_support | bool | 4.0+ | {{RFC7530}} |
| 6 | symlink_support | bool | 4.0+ | {{RFC7530}} |
| 7 | named_attr | bool | 4.0+ | {{RFC7530}} |
| 8 | fsid | fsid4 | 4.0+ | {{RFC7530}} |
| 9 | unique_handles | bool | 4.0+ | {{RFC7530}} |
| 10 | lease_time | nfs_lease4 | 4.0+ | {{RFC7530}} {{RFC7931}} |
| 11 | rdattr_error | nfsstat4 | 4.0+ | {{RFC7530}} |
| 12 | acl | nfsace4<> | 4.0+ | {{RFC7530}} |
| 13 | aclsupport | uint32_t | 4.0+ | {{RFC7530}} |
| 14 | archive | bool | 4.0+ | {{RFC7530}} |
| 15 | cansettime | bool | 4.0+ | {{RFC7530}} |
| 16 | case_insensitive | bool | 4.0+ | {{RFC7530}} |
| 17 | case_preserving | bool | 4.0+ | {{RFC7530}} |
| 18 | chown_restricted | bool | 4.0+ | {{RFC7530}} |
| 19 | filehandle | nfs_fh4 | 4.0+ | {{RFC7530}} |
| 20 | fileid | uint64_t | 4.0+ | {{RFC7530}} |
| 21 | files_avail | uint64_t | 4.0+ | {{RFC7530}} |
| 22 | files_free | uint64_t | 4.0+ | {{RFC7530}} |
| 23 | files_total | uint64_t | 4.0+ | {{RFC7530}} |
| 24 | fs_locations | fs_locations4 | 4.0+ | {{RFC7530}} {{RFC7931}} {{RFC8587}} |
| 25 | hidden | bool | 4.0+ | {{RFC7530}} |
| 26 | homogeneous | bool | 4.0+ | {{RFC7530}} |
| 27 | maxfilesize | uint64_t | 4.0+ | {{RFC7530}} |
| 28 | maxlink | uint32_t | 4.0+ | {{RFC7530}} |
| 29 | maxname | uint32_t | 4.0+ | {{RFC7530}} |
| 30 | maxread | uint64_t | 4.0+ | {{RFC7530}} |
| 31 | maxwrite | uint64_t | 4.0+ | {{RFC7530}} |
| 32 | mimetype | ascii_REQUIRED4 | 4.0+ | {{RFC7530}} |
| 33 | mode | mode4 | 4.0+ | {{RFC7530}} {{RFC8275}} |
| 34 | no_trunc | bool | 4.0+ | {{RFC7530}} |
| 35 | numlinks | uint32_t | 4.0+ | {{RFC7530}} |
| 36 | owner | utf8str_mixed | 4.0+ | {{RFC7530}} |
| 37 | owner_group | utf8str_mixed | 4.0+ | {{RFC7530}} |
| 38 | quota_avail_hard | uint64_t | 4.0+ | {{RFC7530}} |
| 39 | quota_avail_soft | uint64_t | 4.0+ | {{RFC7530}} |
| 40 | quota_used | uint64_t | 4.0+ | {{RFC7530}} |
| 41 | rawdev | specdata4 | 4.0+ | {{RFC7530}} |
| 42 | space_avail | uint64_t | 4.0+ | {{RFC7530}} |
| 43 | space_free | uint64_t | 4.0+ | {{RFC7530}} |
| 44 | space_total | uint64_t | 4.0+ | {{RFC7530}} |
| 45 | space_used | uint64_t | 4.0+ | {{RFC7530}} |
| 46 | system | bool | 4.0+ | {{RFC7530}} |
| 47 | time_access | nfstime4 | 4.0+ | {{RFC7530}} {{RFC9754}} |
| 48 | time_access_set | settime4 | 4.0+ | {{RFC7530}} |
| 49 | time_backup | nfstime4 | 4.0+ | {{RFC7530}} |
| 50 | time_create | nfstime4 | 4.0+ | {{RFC7530}} |
| 51 | time_delta | nfstime4 | 4.0+ | {{RFC7530}} |
| 52 | time_metadata | nfstime4 | 4.0+ | {{RFC7530}} {{RFC9754}} |
| 53 | time_modify | nfstime4 | 4.0+ | {{RFC7530}} {{RFC9754}} |
| 54 | time_modify_set | settime4 | 4.0+ | {{RFC7530}} |
| 55 | mounted_on_fileid | uint64_t | 4.0+ | {{RFC7530}} |
| 56 | dir_notif_delay | nfstime4 | 4.1+ | {{RFC8881}} |
| 57 | dirent_notif_delay | nfstime4 | 4.1+ | {{RFC8881}} |
| 58 | dacl | nfsacl41 | 4.1+ | {{RFC8881}} |
| 59 | sacl | nfsacl41 | 4.1+ | {{RFC8881}} |
| 60 | change_policy | change_policy4 | 4.1+ | {{RFC8881}} |
| 61 | fs_status | fs4_status | 4.1+ | {{RFC8881}} |
| 62 | fs_layout_types | layouttype4<> | 4.1+ | {{RFC8881}} |
| 63 | layout_hint | layouthint4 | 4.1+ | {{RFC8881}} {{RFC8154}} |
| 64 | layout_types | layouttype4<> | 4.1+ | {{RFC8881}} |
| 65 | layout_blksize | uint32_t | 4.1+ | {{RFC8881}} |
| 66 | layout_alignment | uint32_t | 4.1+ | {{RFC8881}} |
| 67 | fs_locations_info | fs_locations_info4 | 4.1+ | {{RFC8881}} |
| 68 | mdsthreshold | mdsthreshold4 | 4.1+ | {{RFC8881}} |
| 69 | retention_get | retention_get4 | 4.1+ | {{RFC8881}} |
| 70 | retention_set | retention_set4 | 4.1+ | {{RFC8881}} |
| 71 | retentevt_get | retention_get4 | 4.1+ | {{RFC8881}} |
| 72 | retentevt_set | retention_set4 | 4.1+ | {{RFC8881}} |
| 73 | retention_hold | uint64_t | 4.1+ | {{RFC8881}} |
| 74 | mode_set_masked | mode_masked4 | 4.1+ | {{RFC8881}} |
| 75 | suppattr_exclcreat | bitmap4 | 4.1+ | {{RFC8881}} |
| 76 | fs_charset_cap | fs_charset_cap4 | 4.1+ | {{RFC8881}} |
| 77 | clone_blksize | uint32_t | 4.2+ | {{RFC7862}} |
| 78 | space_freed | uint64_t | 4.2+ | {{RFC7862}} |
| 79 | change_attr_type | change_attr_type4 | 4.2+ | {{RFC7862}} |
| 80 | sec_label | sec_label4 | 4.2+ | {{RFC7862}} |
| 81 | mode_umask | mode_umask4 | 4.2+ | {{RFC8275}} |
| 82 | xattr_support | bool | 4.2+ | {{RFC8276}} |
| 83 | offline | bool | 4.2+ | {{RFC9754}} |
| 84 | time_deleg_access | nfstime4 | 4.2+ | {{RFC9754}} |
| 85 | time_deleg_modify | nfstime4 | 4.2+ | {{RFC9754}} |
| 86 | open_arguments | open_arguments4 | 4.2+ | {{RFC9754}} |
{: #fattr4-initial title="Initial contents of the NFSv4 fattr4 Attributes registry"}

Values 87 and above are unassigned.

## NFSv4 ACCESS Flags Registry {#iana-access}

IANA is requested to create a registry titled "NFSv4 ACCESS Flags".

The Value is a bit in the access field of ACCESS4args and in the
supported and access fields of ACCESS4resok.  The XDR constant is
the Name prefixed with "ACCESS4_" (for example, ACCESS4_READ for the
flag named READ).

| Value | Name | Versions | Reference |
|------:|------|----------|-----------|
| 0x00000001 | READ | 4.0+ | {{RFC7530}} |
| 0x00000002 | LOOKUP | 4.0+ | {{RFC7530}} |
| 0x00000004 | MODIFY | 4.0+ | {{RFC7530}} |
| 0x00000008 | EXTEND | 4.0+ | {{RFC7530}} |
| 0x00000010 | DELETE | 4.0+ | {{RFC7530}} |
| 0x00000020 | EXECUTE | 4.0+ | {{RFC7530}} |
| 0x00000040 | XAREAD | 4.2+ | {{RFC8276}} |
| 0x00000080 | XAWRITE | 4.2+ | {{RFC8276}} |
| 0x00000100 | XALIST | 4.2+ | {{RFC8276}} |
{: #access-initial title="Initial contents of the NFSv4 ACCESS Flags registry"}

Bits 0x00000200 and above are unassigned.

## NFSv4 OPEN Share Access Flags Registry {#iana-open-access}

IANA is requested to create a registry titled "NFSv4 OPEN Share
Access Flags".

The Value is a bit or a masked value in the share_access field of
OPEN4args and OPEN_DOWNGRADE4args and in the wda_want field of
WANT_DELEGATION4args.  The XDR constant is the Name prefixed with
"OPEN4_SHARE_ACCESS_" (for example, OPEN4_SHARE_ACCESS_READ for the
flag named READ).

The values covered by WANT_DELEG_MASK form an enumerated subfield
rather than independent bits.  A new value in that subfield is
assigned sequentially in units of 0x00000100 from the lowest
unassigned value; a new flag outside it is assigned the lowest
unassigned bit.

| Value | Name | Versions | Reference |
|------:|------|----------|-----------|
| 0x00000001 | READ | 4.0+ | {{RFC7530}} |
| 0x00000002 | WRITE | 4.0+ | {{RFC7530}} |
| 0x00000003 | BOTH | 4.0+ | {{RFC7530}} |
| 0x00000000 | WANT_NO_PREFERENCE | 4.1+ | {{RFC8881}} |
| 0x00000100 | WANT_READ_DELEG | 4.1+ | {{RFC8881}} |
| 0x00000200 | WANT_WRITE_DELEG | 4.1+ | {{RFC8881}} |
| 0x00000300 | WANT_ANY_DELEG | 4.1+ | {{RFC8881}} |
| 0x00000400 | WANT_NO_DELEG | 4.1+ | {{RFC8881}} |
| 0x00000500 | WANT_CANCEL | 4.1+ | {{RFC8881}} |
| 0x0000FF00 | WANT_DELEG_MASK | 4.1+ | {{RFC8881}} |
| 0x00010000 | WANT_SIGNAL_DELEG_WHEN_RESRC_AVAIL | 4.1+ | {{RFC8881}} |
| 0x00020000 | WANT_PUSH_DELEG_WHEN_UNCONTENDED | 4.1+ | {{RFC8881}} |
| 0x00100000 | WANT_DELEG_TIMESTAMPS | 4.2+ | {{RFC9754}} |
| 0x00200000 | WANT_OPEN_XOR_DELEGATION | 4.2+ | {{RFC9754}} |
{: #open-access-initial title="Initial contents of the NFSv4 OPEN Share Access Flags registry"}

Values 0x00000600 through 0x0000FE00 of the WANT_DELEG_MASK
subfield, bits 0x00000004 through 0x00000080, bits 0x00040000
through 0x00080000, and bits 0x00400000 and above are unassigned.

## NFSv4 OPEN Result Flags Registry {#iana-open-result}

IANA is requested to create a registry titled "NFSv4 OPEN Result
Flags".

The Value is a bit in the rflags field of OPEN4resok.  The XDR
constant is the Name prefixed with "OPEN4_RESULT_" (for example,
OPEN4_RESULT_CONFIRM for the flag named CONFIRM).

Bit 0x00000001 is not assigned by any minor version specification.
This document marks it Reserved so that it is never
assigned.[^rflags]

[^rflags]: Editor's note: as with operation values 0 through 2, the
    Working Group may prefer to leave this bit unassigned.

| Value | Name | Versions | Reference |
|------:|------|----------|-----------|
| 0x00000001 | Reserved | | |
| 0x00000002 | CONFIRM | 4.0 | {{RFC7530}} {{RFC8881}} |
| 0x00000004 | LOCKTYPE_POSIX | 4.0+ | {{RFC7530}} |
| 0x00000008 | PRESERVE_UNLINKED | 4.1+ | {{RFC8881}} |
| 0x00000010 | NO_OPEN_STATEID | 4.2+ | {{RFC9754}} |
| 0x00000020 | MAY_NOTIFY_LOCK | 4.1+ | {{RFC8881}} |
{: #open-result-initial title="Initial contents of the NFSv4 OPEN Result Flags registry"}

Bits 0x00000040 and above are unassigned.

## NFSv4 OPEN Delegation Types Registry {#iana-open-deleg}

IANA is requested to create a registry titled "NFSv4 OPEN
Delegation Types".

The Value is the delegation type's constant in the
open_delegation_type4 enumeration and the discriminant of the
open_delegation4 union in OPEN4resok.  The XDR constant is the Name
prefixed with "OPEN_DELEGATE_" (for example, OPEN_DELEGATE_READ for
the delegation type named READ).

| Value | Name | Versions | Reference |
|------:|------|----------|-----------|
| 0 | NONE | 4.0+ | {{RFC7530}} |
| 1 | READ | 4.0+ | {{RFC7530}} |
| 2 | WRITE | 4.0+ | {{RFC7530}} |
| 3 | NONE_EXT | 4.1+ | {{RFC8881}} |
| 4 | READ_ATTRS_DELEG | 4.2+ | {{RFC9754}} |
| 5 | WRITE_ATTRS_DELEG | 4.2+ | {{RFC9754}} |
{: #open-deleg-initial title="Initial contents of the NFSv4 OPEN Delegation Types registry"}

Values 6 and above are unassigned.


--- back

# Open Issues {#open-issues}

This section is to be removed before publishing as an RFC.

Each item below is tracked as an issue in this document's issue
tracker, where the detail and the discussion live.

* Whether values 0 through 2 in the operation registries, and 10002
  and 10073 in the status code registry, should be marked Reserved,
  as this revision does, or simply left unassigned.

* Whether the OPEN share_deny flags, which no document since
  {{RFC5661}} has extended, should nevertheless be registered
  alongside the share_access flags.

* Whether the Reference column for an element introduced in NFSv4.0
  should also cite {{RFC8881}}, which restates every NFSv4.0
  element.  This revision cites only the introducing specification
  and the documents that change the element.

* Several section headings in {{RFC8881}} carry the wrong numeric
  value: {{Section 15.1.5.2 of RFC8881}} (NFS4ERR_BAD_STATEID, 10025,
  shown as 10026), {{Section 15.1.3.12 of RFC8881}}
  (NFS4ERR_UNSAFE_COMPOUND, 10069, shown as 10068), and
  {{Section 15.1.16.3 of RFC8881}} (NFS4ERR_NXIO, 6, shown as 5).
  The registry uses the values from the XDR in {{RFC5662}}.  Errata
  should be filed if none exist.

* NFS4ERR_RESOURCE is absent from every NFSv4.1 error list in
  {{RFC8881}}, including {{Section 15.1.16 of RFC8881}}, which
  enumerates the NFSv4.0 errors that NFSv4.1 obsoletes.  The only
  statement that NFSv4.1 withdraws it is a comment in the {{RFC5662}}
  XDR description.  A comment in an XDR description is not
  normative, and {{Section 8.2 of RFC8178}} requires explicit
  removal, so the registry's Versions value of "4.0" for this code
  rests on the XDR comment alone.  An erratum against {{RFC8881}}
  may be warranted.

# Acknowledgments
{:numbered="false"}

Thanks to Éric Vyncke for suggesting this approach.

The editor is grateful to
Bill Baker,
Greg Marsden,
and
Martin Thomson
for their input and support.

Special thanks to
Area Director
Gorry Fairhurst,
NFSv4 Working Group Chair
Brian Pawlowski,
and
NFSv4 Working Group Secretary
Thomas Haynes
for their guidance and oversight.
