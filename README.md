# IOTA URI Scheme (proposal)

```
  Author: Joakim Stai <joakimstai@gmail.com>
  Status: Draft
  Created: 2018-03-09
  License: CC-BY-SA-4.0
```

This is a proposal for IOTA, based on [BIP21](https://github.com/bitcoin/bips/blob/master/bip-0021.mediawiki) for Bitcoin.

## Abstract

This document proposes a URI scheme for initiating IOTA transfers.

## Motivation

The purpose of this URI scheme is to enable users to easily make transfers by simply clicking links or scanning tags.

## Specification

### Important rules for handling URIs

IOTA clients **MUST NOT** act on URIs without getting the user's authorization.

IOTA clients **SHOULD** require the user to manually approve each transfer individually, though in some cases they **MAY** allow the user to automatically make this decision.

### Operating system integration

IOTA GUI clients **SHOULD** register themselves as the handler for the `iota:` URI scheme by default if no other handler is already registered. If there is already a registered handler, the client **MAY** prompt the user to change it once when they first run the client.

### General format

IOTA URIs follow the general format for URIs as described in [RFC 3986](https://tools.ietf.org/html/rfc3986). The path component consists of an IOTA address, and the query component provides additional parameters relating to the transfer.

Elements of the query component may contain characters outside the valid range. These must first be encoded according to UTF-8, and then each octet of the corresponding UTF-8 sequence must be percent-encoded as described in [RFC 3986](https://tools.ietf.org/html/rfc3986).

### ABNF grammar

(See also [a simpler representation of syntax](#simpler-syntax).)

```
 iota-urn      = "iota:" iota-address [ "?" iota-params ]
 iota-address  = 81tryte
 iota-params   = iota-param [ "&" iota-params ]
 iota-param    = [ value-param / tag-param / message-param / other-param ]
 value-param   = "value=" *digit
 tag-param     = "tag=" *qchar
 message-param = "message=" *qchar
 other-param   = 1*qchar [ "=" *qchar ]
```

Here, `qchar` corresponds to valid characters of an [RFC 3986](https://tools.ietf.org/html/rfc3986) URI query component, excluding the `=` and `&` characters, which this proposal takes as separators. A `tryte` is the ternary equivalent of a byte, represented as uppercase latin letters (A-Z) and the number 9.

The scheme component (`iota:`) is case-insensitive, and implementations must accept any combination of uppercase and lowercase letters. The rest of the URI is case-sensitive, including the query parameter keys.

Only the address is required, all query parameters are optional.

### Query parameters

- value: Transfer value (see below)
- tag: A short tag to be published with the transfer
- message: A message to be published with the transfer
- other: Other parameters for future extensions (see below)

#### Transfer value

If a value is provided, it **MUST** be specified as IOTAs (i) in integer numbers.

IOTA clients **MAY** display the value in any format that is not intended to deceive the user.

IOTA clients **SHOULD** choose a format that is foremost least confusing, and only after that the format that is most reasonable given the value specified.
For example, so long as the majority of users are used to Mega IOTA units (Mi), values should always be displayed in Mega IOTAs by default, even if Kilo IOTAs (Ki) would otherwise be a more logical interpretation of the value.

#### Other parameters for future extensions

Other parameters **MAY** be added for future extensions. These **MUST** be optional, support is not to be expected in IOTA clients.

## Rationale

### Transfer identifiers, not recipient identifiers

Current best practice for IOTA is to generate a unique address for every transfer, as one should never reuse an address after having spent from it.
Therefore, the URI scheme should not represent the _recipient_ of transfers, but a _one-time transfer_.

### Accessibility (URI scheme name)

Should someone not familiar with IOTA happen to see such a URI, the URI scheme name already gives a description.
A quick search should then do the rest to help them find the resources needed to make the transfer.

## Forward compatibility

Any parameters for future extensions that are not supported by the IOTA client, can be safely ignored by the client.

## Backward compatibility

As this proposal is written, no known IOTA clients actively implement an `iota:` URI scheme.

## Appendix

### Simpler syntax

This section is non-normative and does not cover all possible syntax.
Please see the [ABNF grammar](#abnf-grammar) above for the normative syntax.

`[foo]` means optional, `<bar>` are placeholders

```
iota:<address>[?value=<value>][?tag=<tag>][?message=<message>]
```

### Examples

Just the address:

    iota:MZLFXAZOHOSGRPIGFSQATWQXTFRWATQTOZXAZIGCRXAGIED9ZCPXMCMSSUHYVEGVTOILQMAD9VZIV9PJCHCCO9YMIW

Address with a tag:

    iota:MZLFXAZOHOSGRPIGFSQATWQXTFRWATQTOZXAZIGCRXAGIED9ZCPXMCMSSUHYVEGVTOILQMAD9VZIV9PJCHCCO9YMIW?tag=foo

Request for 100 i (IOTAs):

    iota:MZLFXAZOHOSGRPIGFSQATWQXTFRWATQTOZXAZIGCRXAGIED9ZCPXMCMSSUHYVEGVTOILQMAD9VZIV9PJCHCCO9YMIW?value=100

Request for 10 Mi (Mega IOTAs):

    iota:MZLFXAZOHOSGRPIGFSQATWQXTFRWATQTOZXAZIGCRXAGIED9ZCPXMCMSSUHYVEGVTOILQMAD9VZIV9PJCHCCO9YMIW?value=10000000

Request for 1 Gi (Giga IOTAs) with a message:

    iota:MZLFXAZOHOSGRPIGFSQATWQXTFRWATQTOZXAZIGCRXAGIED9ZCPXMCMSSUHYVEGVTOILQMAD9VZIV9PJCHCCO9YMIW?value=1000000000&message=Payment%20for%20XYZ

Some future version that has parameters which are currently not understood and will therefore be ignored:

    iota:MZLFXAZOHOSGRPIGFSQATWQXTFRWATQTOZXAZIGCRXAGIED9ZCPXMCMSSUHYVEGVTOILQMAD9VZIV9PJCHCCO9YMIW?somethingyoudontunderstand=50&somethingelseyoudontget=999

## Reference implementations

### IOTA clients

* No clients yet

### Libraries

* No libraries yet
