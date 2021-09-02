# Discussion of sediment cores and time ontology

* instants, and proper intervals are temporal things, they have no material manifestation. 

* an event is a temporal thing that can produce material changes that can persist and be observed at a later time as evidence of the event.  Any event has some duration (corresponding to a proper interval), but for many events, that duration is so short relative to the time scale of interest it is treated as occurring at an instant.

* geologic histories are constructed by observing the material manifestations of events and making inferences about their temporal relations based on spatial relationships between those manifestations.

* stratigraphic column. Within column (assuming simple deposition), temporal topology is clear (superposition). Between columns requires correlations of event manifestations, or matching of estimated time coordinates assigned to physical intervals in column

* a section of core (stratigraphic interval) is not a  time:proper interval; it is a portion of material that was deposited during a proper interval that is bounded by two events-- deposition of the bottom-most 'lamination' of material, and deposition of the top-most lamination of material.  The interval of actual time during which that sediment was deposited does not change, neither do the temporal relations with other proper intervals during which sediment in the same core was deposited (assuming we have simple deposition, no faults, slumping, water escape disruption, etc). What does change is our estimation of the temporal coordinates (:timePosition.numericPosition) of the proper interval boundary 'instants'. 

* W3C time ontology is about temporal things, doesn't capture binding to physical manifestations of events,   This is modeled in the Cox and Richard DOI 10.1007/s12145-014-0170-6.
The estimation of time coordinates for events manifested in the rock record is even trickier, have to go back to the UML in Cox&Richard for linkage to O&M model.  A current implementation of these concepts should be aligned with the current W3C [*Semantic Sensor Network Ontology*](https://www.w3.org/TR/vocab-ssn/)


Here is the beginning of a represtation with turtle-like notation, certainly not complete. More study SSN/SOSA and Simon Cox's mapping from the gssp and gts work to W3C time and SOSA to come up with an example that's consistent with current proposed vocabularies.

```
coresection1 a gts:StratigraphicSection, om:SamplingFeature;
topcs1 a gts:StratigraphicPoint, om:SamplingFeature;
bottomcs1 a gts:StratigraphicPoint, om:SamplingFeature;

timeinterval1 a gts:GeochronologicEra
    gts:stratotype coresection1;
timeboundary1 a gts:GeochronologicBoundary
    gts:stratotype topcs1;
timeboundary2 a gts:GeochronologicBoundary
    gts:stratotype bottomcs1;
    
 timeinterval1
    thors:begin timeboundary2
    thors:end timeboundary1;

coresection1
    om:relatedSamplingFeature topcs1
    om:relatedSamplingFeature bottomcs1;
    
cs1topdateestimate a gssp:StratigraphicDateEstimate
    gssp:quality  'this has a complex set of options for representing the quality and provenance of the data estimate'
    gssp:observationBasis 'this links into a complete description of the measurement process to produce the date estimate, complete with assessment of uncertainty; not elucidated in this example yet'; 
timeboundary1
    thors:position cs1topdateestimate;
    
```