#A time unit ontology

by Julien Emile-Geay

I should preface this by saying that I am new to ontologies, and it is not entirely clear to me whether OWL Time is the best way to address this problem. Happy to hear other suggestions, but without further ado:

## motivation
Large-scale integration of paleoclimate observations (see e.g. the [Last Millennium Reanalysis](https://www.researchgate.net/project/Last-Millennium-Reanalysis)) requires standardized ways of reporting such observations. The Linked Paleo Data [LiPD]() framework provides much needed structure to do so, and its coupling to the [LinkedEarth ontology](http://linked.earth/ontology/) provides a standardization of nomenclature.  Nevertheless, users have freedom to express variables in a number of ways, and this document aims at finding a minimal standard that would work for our community and the OWL Time user community.

## problem
In a database like [LinkedEarth](http://wiki.linked.earth), which so far contains only data only of Pleistocene age, most of it from the Common Era, paleoclimate data are always expressed in terms of an [InferredVariable](http://linked.earth/ontology/inferredVariable/1.0.0/index-en.html#Time). However, there is a plethora of conventions that make a unified processing of the dataset a complete nightmare.

To whit:
- *Time or age*: the temporal axis is sometimes expressed forward (as time units since a reference point, e.g. years AD) or backward (time units before a reference point, e.g. kyr BP). The convention is usually clear from context, but not easily machine-readable due to notational proliferation (year, years, yr, yrs, etc).

- *Unit zoology*: there is a veritable zoo of units. Years are usually expressed in AD or CE, which are synonymous, but it is rarely explained whether that includes a year 0 or not, as in [ISO8601](https://www.iso.org/iso-8601-date-and-time-format.html).  For age, the choice is even wider: kyr, ka, Ma, Ga, and the reference point is usually ambiguous (BP, 'before present' sometimes refers to 1950 AD or 2000 AD).

The paleoscience community needs some formal guidance on how to express time in a relatively lightweight, yet uniform and unambiguous way. It is hoped that there is a relatively simple way of doing so, simplicity being the key to widespread adoption.
