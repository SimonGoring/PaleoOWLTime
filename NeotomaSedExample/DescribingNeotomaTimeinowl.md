# OWL TIME Neotoma Use Case:

Quaternary paleoecology is a critical discipline for understanding the Earth system.  The study of fossil pollen within lake sediments is one tool used to understand past environments and the interactions between species and climate at long (but near-modern) time scales. In the Northern Hemisphere much of the landscape was glaciated, and, as a result, much of these lake sedimentary archives, extracted as sediment cores, lies firmly within the radiocarbon age range mentioned in the [Time Ontology for OWL](https://www.w3.org/TR/owl-time). However, the complexities of radiocarbon dating and chronology construction mean that time intervals vary through the sediment column -- on the order of years and decades in the upper sediments of lakes (where there has been less compaction) and from decades to centuries in the lower sediments.

![](images/neotoma_core.svg)

Briefly: 

* Researchers obtaining continuous sediment sequences from lakes, usually refered to as *cores*.  This column spans from the surface of the lake sediment-water interface to some basal layer.
* Researchers sub-sample the core looking for material that can be dated using, for example, [radiocarbon dating](https://en.wikipedia.org/wiki/Radiocarbon_dating).
* Using dated material at known depths, a chronology is constructed, allowing us to interpolate dates & estimate uncertainties for intermediate depths.  These models include both "classical" models, using linear interpolation, or polynomial curves, and more modern Bayesian methods.
* Proxy data, such as pollen, plant macrofossils or geochemical data, are obtained from discrete sections (often 1 *cm*), either continuously or discontinuously through the core.
* Data associated with the proxy (counts, concentrations, proportions) are then expressed in years before present (often present == 1950).

This process then associates a physical sample (a 1cm section of the core) with a temporal bound that is associated with the probability distribution of a chronological model.  We would then need to identify the fact that a section that is physically above another may have overlapping temporal bounds because of the uncertainty in the chronology, but because of the physical constraint, we know the upper section is more recent and is younger.

It is not clear to me how the Time Ontology deals with this hybrid system, where a sample is "before" the sample above it (stratigraphically and temporally) but has a range of temporal uncertainty that effectively overlaps with adjacent samples.

My best estimation of the OWL terminology (I'm not familiar with OWL too much) is that the pollen is obtained from a "sample" `Thing`, which has a `ProperInterval`, which is contained within a sediment core, which has a `ProperInterval` that absolutely bounds the `ProperInterval`s of all the samples within it, however, individual samples may have overlapping intervals.

For most cores, we might define ages relative to present:

```
geol:Present
  a :Instant ;
  :inDateTime [
      a :DateTimeDescription ;
      :unitType :unitYear ;
      :year "1950"^^xsd:gYear ;
    ] ;
  :inTimePosition [
      a :TimePosition ;
      :hasTRS <http://www.opengis.net/def/crs/OGC/0/ChronometricGeologicTime> ;
      :numericPosition "0.0"^^:Number ;
    ] ;
  :inXSDDateTime "1950-01-01T00:00:00Z"^^xsd:dateTime ;
  rdfs:label "The present"^^xsd:string ;
```

Each individual pollen sample possesses a `ProperInterval`. Each sample has an identifier within Neotoma (this is, in practice, a numeric key).  Thus the first sample (the "modern" sample) for a core sampled in 1996 might have a temporal range of 1996 - 1990, accounting for uncertainty and sample thickness.  While we might hope to use the `ChronometricGeologicTime` TRS, it doesn't make sense to use *Mya* for Quaternary scale data.

My feeling is that it would be relatively easy to define a new TemporalCRS (or find an existing one) that uses the same baseline, but is reported in years BP (although ages are also reported in radiocarbon years BP, calendar years BP or calibrated years BP, for samples whose age model uses calibrated radiocarbon years, and as such we might define multiple TemporalCRS).

Here we consider two adjacent samples in a core, starting at depth 0cm, each with a thickness of 2cm, with ages modeled from a chronology that is built using a Bayesian model called "Bacon" (Blaauw & Christen, 2012) which provides a posterior prediction from the model (so the actual output for a sample is a set of years with a probability assigned to that year).

 | Sample Number  | Depth Interval | Early Age | Mean Age | Older Age |
 | -------------- | -------------- | --------- | -------- | --------- |
 |  sample0001    |    0 - 2 cm    |   1996    |   1985   |    1990   |
 |  sample0002    |    2 - 4 cm    |   1991    |   1985   |    1979   |
 |  sample0003    |    4 - 7 cm    |   1984    |   1968   |    1960   |

Each sample has a year range, and a stratigraphic interval.  Because the year interval is generated from a model, it is possible for adjacent samples to have overlapping age intervals, but because the samples are obtained from adjacent physical samples, it is unlikely (but not impossible) for two samples to have age reversals.  To represent this I have included both an `interval` and a `after` and `before` flag.

 ```
:sample0001
 	a  :ProperInterval ;
 	:name "sample XXXX";
 	:after  sample0002;
 	:intervalEndedBy [
 		rdf:type :TimePosition ;
      :hasTRS <http://www.opengis.net/def/uom/ISO-8601/0/Gregorian> ;
      :numericPosition "1996"^^:Number ;
    ] ;
    :intervalStartedBy [
 		rdf:type :TimePosition ;
      :hasTRS <http://www.opengis.net/def/uom/ISO-8601/0/Gregorian> ;
      :numericPosition "1985"^^:Number ;
    ] ;

:sample0002
 	a  :ProperInterval  ;
 	:name "sample XXXX" ;
 	:before sample0001  ;
 	:after  sample0003  ;
 	:intervalEndedBy [
 		rdf:type :TimePosition ;
      :hasTRS <http://www.opengis.net/def/uom/ISO-8601/0/Gregorian> ;
      :numericPosition "1991"^^:Number ;
    ] ;
    :intervalStartedBy [
 		rdf:type :TimePosition ;
      :hasTRS <http://www.opengis.net/def/uom/ISO-8601/0/Gregorian> ;
      :numericPosition "1979"^^:Number ;
    ] ;
    :hasEnd sample0003;

:sample0003
 	a  :ProperInterval  ;
 	:name "sample XXXX" ;
 	:before sample0004  ;
 	:after  sample0002  ;
 	:intervalEndedBy [
 		rdf:type :TimePosition ;
      :hasTRS <http://www.opengis.net/def/uom/ISO-8601/0/Gregorian> ;
      :numericPosition "1984"^^:Number ;
    ] ;
    :intervalStartedBy [
 		rdf:type :TimePosition ;
      :hasTRS <http://www.opengis.net/def/uom/ISO-8601/0/Gregorian> ;
      :numericPosition "1960"^^:Number ;
    ] ;
    :hasEnd sample0003;

```

The concern here is that there is a conflict between the temporal instant `before` or `after`, and the `intervalStartedBy` and `intervalEndedBy`.  Specifically, `sample0002` has an `intervalEndedBy` that is later than the `intervalStartedBy` for `sample0001`, even though `sample0001` is described as being `after` `sample0002`.  This definition also fails to account for posterior distributions, the sample mean, and the fact that `intervalEndedBy` and `intervalStartedBy` are actually the limits of a statistical distribution from which sample ages are assessed.

This is about as far as I've gotten.  I hope it's clear where I've found issues, and I hope that any of my misunderstandings are relatively easy to resolve.  I'd appreciate your feedback on this.  Thanks.