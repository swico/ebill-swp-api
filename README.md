# ebill-swp-api

The **eBill Software Partner API** (eBill SWP API) defines an interface used to deliver _electronic invoices_
from an ERP to an _eBill Network Partner_ (as defined by SIX).

## Status

This repository should be considered as _work in progress_. It shows what direction such an API
could take. It is provided here only as a general information of the workgroup's reflections.
Documents should by no means be used to implement code or draw conclusions.

You are welcome to comment and/or discuss on the API by the way of _GitHub issues_.

## API definition

The current draft recommendation (Version 0.7) for the API is available both in English and
in German:

- [SIX eBill SWP API: Recommendation for the Interface between Software Manufacturers and Network Partners of SIX eBill](https://github.com/swico/ebill-swp-api/blob/master/documents/InterfaceRecommendationSIXeBill.pdf) &ndash; PDF document, Draft 0.7
- [SIX eBill SWP API: Empfehlung für die Schnittstelle zwischen Softwareherstellern und Netzwerkpartnern der SIX eBill](https://github.com/swico/ebill-swp-api/blob/master/documents/SchnittstellenempfehlungSIXeBill.pdf) &ndash; PDF document, Draft 0.7
- [eBill-Softwarepartner zu Netzwerkpartner API Lizenz- und Nutzungsbedingungen](https://github.com/swico/ebill-swp-api/blob/master/LICENSE/Nutzungslizenz-SWP-API.pdf) &ndash; PDF document, Draft 0.5

The API definition can be found as a Swagger YAML-file in folder `api` and viewed,
for instance, with an online tool such as [swagger.io](https://editor.swagger.io).

See also the document about the [onboarding process](https://github.com/swico/ebill-swp-api/tree/master/documents/onboarding.md).

## NWP Directory

The [NWP Directory](https://github.com/swico/ebill-swp-api/tree/master/documents/nwp-directory.md) lists in human-readable
form the currently assigned PID prefixes.

## Who's involved

The informal workgroup members as of June 2020:

- Abacus: **Nicolas Guillet** and Remo Knaus
- Avaloq: Gino Campolo
- Billte: Andrea Girasole and Srdjan Micic
- Epsitec: **Pierre Arnaud**
- GS1: Simon Zbinden
- InvoCloud: Jean-Claude Gaechter
- Medidata: Stefan Staehl
- Messerli Informatik, Roland Messerli
- Nintu Informatik: **Eva Sediki** and Beat Meier
- SIX: Fabio Serratore and Andrey Moiseenko

Translation of the _interface recommendation_ was managed by Adrian Kaufmann (SIX).
The workgroup is also supported by Oliver Jenny (SIX) and Thomas Reske (SIX).

Thanks to SIX and also to Giancarlo Palmisani from SWICO and Simon Zbinden from GS1
