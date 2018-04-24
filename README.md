# IOTA URI Scheme (proposal)

```
  Author: Joakim Stai <joakimstai@gmail.com>
  Status: Draft
  Created: 2018-03-09
  License: CC-BY-SA-4.0
```

This is a proposal for IOTA, based on [BIP21](https://github.com/bitcoin/bips/blob/master/bip-0021.mediawiki) for Bitcoin.

## Table of Contents

> - [Abstract](#abstract)
> - [Motivation](#motivation)
> - [Specification](#specification)
>   - [Syntax](#syntax)
>     - [ABNF grammar](#abnf-grammar)
>     - [Percent-encoding](#percent-encoding)
>     - [Query parameters](#query-parameters)
>   - [Implementation](#implementation)
>     - [Operating system integration](#operating-system-integration)
>     - [Handling URIs](#handling-uris)
>     - [Displaying the value](#displaying-the-value)
> - [Forward compatibility](#forward-compatibility)
> - [Backward compatibility](#backward-compatibility)
> - [Appendix](#appendix)
>   - [Simpler syntax](#simpler-syntax)
>   - [Examples](#examples)

## Abstract

This document proposes a URI scheme for initiating IOTA transfers.

## Motivation

The purpose of this URI scheme is to enable users to easily make transfers by simply clicking links or scanning tags.

## Specification

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in [RFC2119](https://tools.ietf.org/html/rfc2119).

### Syntax

The syntax of an `iota` URI is described using the ABNF of [STD68](https://tools.ietf.org/html/std68) and non-terminal definitions from [STD66](https://tools.ietf.org/html/std66) (unreserved, pct-encoded).
The path component consists of an IOTA address, and the query component provides additional parameters relating to the transfer.

#### ABNF grammar

(See also [a simpler representation of the syntax](#simpler-syntax).)

```
iota-uri      = "iota:" address [ params ]
address       = 90tryte ; IOTA address with checksum
params        = "?" param *( "&" param )
param         = [ value-param / tag-param / message-param / other-param ]
value-param   = "value=" *digit
tag-param     = "tag=" pvalue
message-param = "message=" pvalue
other-param   = pname [ "=" pvalue ]

tryte         = *( %x41-5A / "9" ) ; A-Z9
pname         = 1*qchar ; 1 or more qchars
pvalue        = *qchar ; 0 or more qchars
qchar         = unreserved / pct-encoded / not-reserved
not-reserved  = "!" / "$" / "'" / "(" / ")" / "*" / "+" / "," / ";" / "@"
```

A `tryte` is the ternary equivalent of a byte, represented as uppercase latin letters (A-Z) and the number 9. The 90 trytes `address` consists of an IOTA address (81 trytes) and its checksum (9 trytes).

#### Percent-encoding

Some characters that can appear in `qchar` **MUST** be percent-encoded. These are the characters that cannot appear in a URI according to [STD66](https://tools.ietf.org/html/std66), as well as `%` (because it is used for percent-encoding). Of the characters in `registered`, the following are reserved and **MUST** be percent-encoded when not serving as delimiters: `:`, `?`, `=`, and `&`.

Non-ASCII characters **MUST** first be encoded according to UTF-8 [STD63](https://tools.ietf.org/html/std63), and then each octet of the corresponding UTF-8 sequence **MUST** be percent-encoded to be represented as URI characters.

#### Query parameters

- `value` – Transfer value
- `tag` – A short tag to be published with the transfer
- `message` – A message to be published with the transfer

If `value` is provided, it **MUST** be specified using IOTA units (i) in integer numbers.

Other parameters **MAY** be added for future extensions. These **MUST** be optional, support is not to be expected in IOTA clients.

### Implementation

#### Operating system integration

IOTA clients **SHOULD** register themselves as the handler for the `iota` URI scheme by default if no other handler is already registered. If there is already a registered handler, the client **MAY** prompt the user to change it once when they first run the client.

#### Handling URIs

IOTA clients **MUST NOT** act on URIs without getting the user's authorization.

IOTA clients **SHOULD** require the user to manually approve each transfer individually, though in some cases they **MAY** allow the user to automatically make this decision.

#### Displaying the value

IOTA clients **MAY** display the value in any format that is not intended to deceive the user.

IOTA clients **SHOULD** choose a format that is foremost least confusing, and only after that the format that is most reasonable given the value specified.

## Forward compatibility

Any parameters for future extensions that are not supported by the IOTA client, can be safely ignored by the client.

## Backward compatibility

As this proposal is written, no known IOTA clients actively implement an `iota` URI scheme.

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
