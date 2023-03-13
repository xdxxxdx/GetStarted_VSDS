# Getting Started with [Vlaamse Smart Data Space](https://www.vlaanderen.be/digitaal-vlaanderen/onze-oplossingen/vlaamse-smart-data-space)

>***Please Note!***
>> *The quick start try-out is ONLY made for making [Pagination fragmentation](https://github.com/Informatievlaanderen/VSDS-LDESServer4J/tree/main/ldes-fragmentisers/ldes-fragmentisers-pagination)
> for the self-generated data type:
> [https://www.w3.org/TR/vocab-ssn-ext/#sosa:ObservationCollection](https://www.w3.org/TR/vocab-ssn-ext/#sosa:ObservationCollection).
> To support other fragments, data types, or other features e.g., retention, caching, etc., please consult [LDES Server Manual](https://github.com/Informatievlaanderen/VSDS-LDESServer4J)  for the configurations.*

>> *[Vlaamse Smart Data Space](https://www.vlaanderen.be/digitaal-vlaanderen/onze-oplossingen/vlaamse-smart-data-space) projects also provide methods for transforming data to [LDES format](https://semiceu.github.io/LinkedDataEventStreams/),
> e.g. from [NGSI-V2](https://vloca-kennishub.vlaanderen.be/NGSI-v2) to [NGSI-LD](https://en.wikipedia.org/wiki/NGSI-LD), from NGSI to [OSLO](https://www.vlaanderen.be/digitaal-vlaanderen/onze-oplossingen/oslo) Model, from [NGSI-V2](https://vloca-kennishub.vlaanderen.be/NGSI-v2) to [LDES (LinkedDataEventStreams)](https://semiceu.github.io/LinkedDataEventStreams/) etc. For more info, please consult: [VSDS Linked Data Interactions](https://github.com/Informatievlaanderen/VSDS-Linked-Data-Interactions)*


## Before Start

1. [Docker](https://docker.com/) has been installed on your device.
2.  Local Ports 8080, and 27017 are free.
3.  The command script is written in [bash](https://en.wikipedia.org/wiki/Bash_%28Unix_shell%29) code, please modify it accordingly. :)


## Start your [**LDES Server**](https://github.com/Informatievlaanderen/VSDS-LDESServer4J) 

 1.  Create a local [docker-compose.yml](https://stackedit.io/docker-compose.yml) file with the following content or you could use the current good-to-go [GetStarted_VSDS
Github project](https://github.com/xdxxxdx/GetStarted_VSDS):
 
 
```
version: '3.3'
services:
  ldes-server:
    container_name: quick-start_ldes-server
    image: ghcr.io/informatievlaanderen/ldes-server:latest
    environment:
      - SIS_DATA=/tmp
      - SPRING_DATA_MONGODB_DATABASE=sample
      - LDES_COLLECTIONNAME=sample
      - LDES_MEMBERTYPE=https://www.w3.org/TR/vocab-ssn-ext/#sosa:ObservationCollection
      - SPRING_DATA_MONGODB_HOST=ldes-mongodb
      - SPRING_DATA_MONGODB_PORT=27017
      - LDES_HOSTNAME=http://localhost:8080
      - LDES_SHAPE=
      - VIEW_TIMESTAMPPATH=http://www.w3.org/ns/prov#generatedAtTime
      - VIEW_VERSIONOFPATH=http://purl.org/dc/terms/isVersionOf
      - VIEWS_0_FRAGMENTATIONS_0_NAME=pagination
      - VIEWS_0_NAME=by-page
      - VIEWS_0_FRAGMENTATIONS_0_CONFIG_MEMBERLIMIT=1
    ports:
      - 8080:8080
    networks:
      - ldes
    depends_on:
      - ldes-mongodb
  ldes-mongodb:
    container_name: quick-start_ldes-mongodb
    image: mongo:6.0.4
    ports:
      - 27017:27017
    networks:
      - ldes
  ldes-cli:
    image: ghcr.io/informatievlaanderen/ldes-cli:20230222T0959
    container_name: quick-start_ldes-client-cli
    command: "--url http://localhost:8080/sample/by-page --input-format text/turtle"
    profiles:
      - delayed-started
    network_mode: service:ldes-server
networks:
  ldes:
    name: quick_start_network

```
 2. Within the working directory of [docker-compose.yml](./docker-compose.yml), please run ```docker compose up``` to start the [LDES Server](https://github.com/Informatievlaanderen/VSDS-LDESServer4J) and [MongoDB](https://www.mongodb.com) containers.

 3. **Now!** [LDES Server](https://github.com/Informatievlaanderen/VSDS-LDESServer4J) is running at port 8080 and ready for making [Pagination fragmentation](https://github.com/Informatievlaanderen/VSDS-LDESServer4J/tree/main/ldes-fragmentisers/ldes-fragmentisers-pagination) for your data. Please. use your preferred browser to reach [http://localhost:8080/sample](http://localhost:8080/sample) to have a look.
 
*The result should be as follow:* 
```
@prefix ldes:   <https://w3id.org/ldes#> .
@prefix rdf:    <http://www.w3.org/1999/02/22-rdf-syntax-ns#> .
@prefix sample: <http://localhost:8080/sample/> .
@prefix tree:   <https://w3id.org/tree#> .

<http://localhost:8080/sample>
        rdf:type   ldes:EventStream ;
        tree:view  sample:by-page .

sample:by-page  rdf:type  tree:Node .
 ```       

## Publish your first [LDES collection](https://semiceu.github.io/LinkedDataEventStreams/)

 1. Create your own ```sample.ttl``` file with the following content, or you could use the current good-to-go [GetStarted_VSDS
Github project](https://github.com/xdxxxdx/GetStarted_VSDS):
 
 ```
@prefix dc: <http://purl.org/dc/terms/> .
@prefix prov: <http://www.w3.org/ns/prov#> .
@prefix xsd: <http://www.w3.org/2001/XMLSchema#> .
@prefix sosa: <http://www.w3.org/ns/sosa/> .
@prefix ns0: <http://def.isotc211.org/iso19156/2011/SamplingFeature#SF_SamplingFeatureCollection.> .
@prefix ns1: <http://def.isotc211.org/iso19156/2011/Observation#OM_Observation.> .
@prefix ns2: <http://def.isotc211.org/iso19103/2005/UnitsOfMeasure#Measure.> .
@prefix ns3: <https://schema.org/> .

<urn:ngsi-ld:WaterQualityObserved:woq:1/2023-03-12T18:31:17.003Z>
  dc:isVersionOf <urn:ngsi-ld:WaterQualityObserved:woq:1> ;
  prov:generatedAtTime "2023-03-12T18:31:17.003Z"^^xsd:dateTime ;
  sosa:hasFeatureOfInterest "spt-00035-79" ;
  ns0:member <https://data.vmm.be/id/loc-00019-33>, [
    sosa:madeBySensor <urn:ngsi-v2:cot-imec-be:Device:imec-iow-UR5gEycRuaafxnhvjd9jnU> ;
    ns1:result [ ns2:value [
        ns3:value 2.043000e+1 ;
        ns3:unitCode <https://data.vmm.be/id/CEL>
      ] ] ;
    ns1:phenomenonTime "2023-03-12T18:31:17.003Z"^^xsd:datetime ;
    ns1:observedProperty <https://data.vmm.be/concept/waterkwaliteitparameter/temperatuur> ;
    ns1:featureOfInterest <https://data.vmm.be/id/spt-00035-79> ;
    a <http://def.isotc211.org/iso19156/2011/Measurement#OM_Measurement>
  ], [
    sosa:madeBySensor <urn:ngsi-v2:cot-imec-be:Device:imec-iow-UR5gEycRuaafxnhvjd9jnU> ;
    ns1:result [ ns2:value [
        ns3:value 1442 ;
        ns3:unitCode <https://data.vmm.be/id/HP>
      ] ] ;
    ns1:phenomenonTime "2023-03-12T18:31:17.003Z"^^xsd:datetime ;
    ns1:observedProperty <https://data.vmm.be/concept/observatieparameter/hydrostatische-druk> ;
    ns1:featureOfInterest <https://data.vmm.be/id/spt-00035-79> ;
    a <http://def.isotc211.org/iso19156/2011/Measurement#OM_Measurement>
  ], [
    sosa:madeBySensor <urn:ngsi-v2:cot-imec-be:Device:imec-iow-UR5gEycRuaafxnhvjd9jnU> ;
    ns1:result [ ns2:value [
        ns3:value 6150 ;
        ns3:unitCode <https://data.vmm.be/id/G42>
      ] ] ;
    ns1:phenomenonTime "2023-03-12T18:31:17.003Z"^^xsd:datetime ;
    ns1:observedProperty <https://data.vmm.be/concept/waterkwaliteitparameter/conductiviteit> ;
    ns1:featureOfInterest <https://data.vmm.be/id/spt-00035-79> ;
    a <http://def.isotc211.org/iso19156/2011/Measurement#OM_Measurement>
  ] ;
  a <https://www.w3.org/TR/vocab-ssn-ext/#sosa:ObservationCollection> .

<https://data.vmm.be/id/loc-00019-33> a <http://def.isotc211.org/iso19156/2011/SpatialSamplingFeature#SF_SpatialSamplingFeature> 
```
 2. Please run ```curl -X POST http://localhost:8080/sample -H "Content-Type: application/ttl" -d '@sample.ttl``` to post the  ```sample.ttl ```to the [LDES Server](https://github.com/Informatievlaanderen/VSDS-LDESServer4J)
 
 3. **Now!** [LDES Server](https://github.com/Informatievlaanderen/VSDS-LDESServer4J) has [Pagination fragmentation](https://github.com/Informatievlaanderen/VSDS-LDESServer4J/tree/main/ldes-fragmentisers/ldes-fragmentisers-pagination)  -ed your first [LDES](https://semiceu.github.io/LinkedDataEventStreams/) data [sample.ttl](./sample.ttl). Please use your preferred browser to reach [http://localhost:8080/sample](http://localhost:8080/sample) to have a look.
 
*The result should be as follow:* 
```
@prefix ldes:   <https://w3id.org/ldes#> .
@prefix rdf:    <http://www.w3.org/1999/02/22-rdf-syntax-ns#> .
@prefix sample: <http://localhost:8080/sample/> .
@prefix tree:   <https://w3id.org/tree#> .

<http://localhost:8080/sample>
        rdf:type   ldes:EventStream ;
        tree:view  sample:by-page .

sample:by-page  rdf:type  tree:Node ;
        tree:relation  [ rdf:type   tree:Relation ;
                         tree:node  <http://localhost:8080/sample/by-page?pageNumber=1>
                       ] .
 ```       

Follow the `tree:node`  [http://localhost:8080/sample/by-page?pageNumber=1](http://localhost:8080/sample/by-page?pageNumber=1),  you could reach the first page of your data set.

## Synchronize your LDES dataset using [LDES Client](https://github.com/orgs/Informatievlaanderen/packages/container/package/ldes-cli)

 - Please run ```docker compose up ldes-cli -d ```  to start [LDES 
   Client](https://github.com/orgs/Informatievlaanderen/packages/container/package/ldes-cli)
   docker container.
   
 - The posted LDES stream: http://localhost:8080/sample/by-time will be
   followed and LDES members will be outputted to the console (only
   once) of the ldes-cli docker container. As follow:
   
 ```    
    2023-03-12 21:48:00 [pool-1-thread-1] INFO be.vlaanderen.informatievlaanderen.ldes.client.cli.services.FragmentProcessor - Fragment http://localhost:8080/sample/by-page has 0 member(s)
    2023-03-12 21:48:00 [pool-1-thread-1] INFO be.vlaanderen.informatievlaanderen.ldes.client.cli.services.FragmentProcessor - Fragment http://localhost:8080/sample/by-page?pageNumber=1 has 1 member(s)
    2023-03-12 21:48:01 _:B1e68397745bc55c40e0c5eadca8646e0 <https://schema.org/unitCode> <https://data.vmm.be/id/HP> .
    2023-03-12 21:48:01 _:B1e68397745bc55c40e0c5eadca8646e0 <https://schema.org/value> "1442"^^<http://www.w3.org/2001/XMLSchema#integer> .
    2023-03-12 21:48:01 <urn:ngsi-ld:WaterQualityObserved:woq:1/2023-03-12T18:31:17.003Z> <http://www.w3.org/1999/02/22-rdf-syntax-ns#type> <https://www.w3.org/TR/vocab-ssn-ext/#sosa:ObservationCollection> .
    2023-03-12 21:48:01 <urn:ngsi-ld:WaterQualityObserved:woq:1/2023-03-12T18:31:17.003Z> <http://def.isotc211.org/iso19156/2011/SamplingFeature#SF_SamplingFeatureCollection.member> <https://data.vmm.be/id/loc-00019-33> .
    2023-03-12 21:48:01 <urn:ngsi-ld:WaterQualityObserved:woq:1/2023-03-12T18:31:17.003Z> <http://def.isotc211.org/iso19156/2011/SamplingFeature#SF_SamplingFeatureCollection.member> _:Bf2d869f7dc0b939c5b6016ac7d1c451c .
    2023-03-12 21:48:01 <urn:ngsi-ld:WaterQualityObserved:woq:1/2023-03-12T18:31:17.003Z> <http://def.isotc211.org/iso19156/2011/SamplingFeature#SF_SamplingFeatureCollection.member> _:B505977c4390a72ab0da767101451604d .
    2023-03-12 21:48:01 <urn:ngsi-ld:WaterQualityObserved:woq:1/2023-03-12T18:31:17.003Z> <http://def.isotc211.org/iso19156/2011/SamplingFeature#SF_SamplingFeatureCollection.member> _:Bdc3ef60306183fa17844d907314f40ee .
    2023-03-12 21:48:01 <urn:ngsi-ld:WaterQualityObserved:woq:1/2023-03-12T18:31:17.003Z> <http://purl.org/dc/terms/isVersionOf> <urn:ngsi-ld:WaterQualityObserved:woq:1> .
    2023-03-12 21:48:01 <urn:ngsi-ld:WaterQualityObserved:woq:1/2023-03-12T18:31:17.003Z> <http://www.w3.org/ns/prov#generatedAtTime> "2023-03-12T18:31:17.003Z"^^<http://www.w3.org/2001/XMLSchema#dateTime> .
    2023-03-12 21:48:01 <urn:ngsi-ld:WaterQualityObserved:woq:1/2023-03-12T18:31:17.003Z> <http://www.w3.org/ns/sosa/hasFeatureOfInterest> "spt-00035-79" .
    2023-03-12 21:48:01 _:B1e0a50c94effea51ea151d334d1d70a4 <https://schema.org/unitCode> <https://data.vmm.be/id/CEL> .
    2023-03-12 21:48:01 _:B1e0a50c94effea51ea151d334d1d70a4 <https://schema.org/value> "2.043000e+1"^^<http://www.w3.org/2001/XMLSchema#double> .
    2023-03-12 21:48:01 _:Bdc3ef60306183fa17844d907314f40ee <http://www.w3.org/1999/02/22-rdf-syntax-ns#type> <http://def.isotc211.org/iso19156/2011/Measurement#OM_Measurement> .
    2023-03-12 21:48:01 _:Bdc3ef60306183fa17844d907314f40ee <http://def.isotc211.org/iso19156/2011/Observation#OM_Observation.featureOfInterest> <https://data.vmm.be/id/spt-00035-79> .
    2023-03-12 21:48:01 _:Bdc3ef60306183fa17844d907314f40ee <http://def.isotc211.org/iso19156/2011/Observation#OM_Observation.observedProperty> <https://data.vmm.be/concept/waterkwaliteitparameter/temperatuur> .
    2023-03-12 21:48:01 _:Bdc3ef60306183fa17844d907314f40ee <http://def.isotc211.org/iso19156/2011/Observation#OM_Observation.phenomenonTime> "2023-03-12T18:31:17.003Z"^^<http://www.w3.org/2001/XMLSchema#datetime> .
    2023-03-12 21:48:01 _:Bdc3ef60306183fa17844d907314f40ee <http://def.isotc211.org/iso19156/2011/Observation#OM_Observation.result> _:B0d2c29f506d81bb4b3ae1170f6b7ae96 .
    2023-03-12 21:48:01 _:Bdc3ef60306183fa17844d907314f40ee <http://www.w3.org/ns/sosa/madeBySensor> <urn:ngsi-v2:cot-imec-be:Device:imec-iow-UR5gEycRuaafxnhvjd9jnU> .
    2023-03-12 21:48:01 _:B505977c4390a72ab0da767101451604d <http://www.w3.org/1999/02/22-rdf-syntax-ns#type> <http://def.isotc211.org/iso19156/2011/Measurement#OM_Measurement> .
    2023-03-12 21:48:01 _:B505977c4390a72ab0da767101451604d <http://def.isotc211.org/iso19156/2011/Observation#OM_Observation.featureOfInterest> <https://data.vmm.be/id/spt-00035-79> .
    2023-03-12 21:48:01 _:B505977c4390a72ab0da767101451604d <http://def.isotc211.org/iso19156/2011/Observation#OM_Observation.observedProperty> <https://data.vmm.be/concept/observatieparameter/hydrostatische-druk> .
    2023-03-12 21:48:01 _:B505977c4390a72ab0da767101451604d <http://def.isotc211.org/iso19156/2011/Observation#OM_Observation.phenomenonTime> "2023-03-12T18:31:17.003Z"^^<http://www.w3.org/2001/XMLSchema#datetime> .
    2023-03-12 21:48:01 _:B505977c4390a72ab0da767101451604d <http://def.isotc211.org/iso19156/2011/Observation#OM_Observation.result> _:B8e72081a21073160d62ed708aed78a23 .
    2023-03-12 21:48:01 _:B505977c4390a72ab0da767101451604d <http://www.w3.org/ns/sosa/madeBySensor> <urn:ngsi-v2:cot-imec-be:Device:imec-iow-UR5gEycRuaafxnhvjd9jnU> .
    2023-03-12 21:48:01 _:B8e72081a21073160d62ed708aed78a23 <http://def.isotc211.org/iso19103/2005/UnitsOfMeasure#Measure.value> _:B1e68397745bc55c40e0c5eadca8646e0 .
    2023-03-12 21:48:01 _:B22abd0fb7f46cf69b7367d7743860b0a <http://def.isotc211.org/iso19103/2005/UnitsOfMeasure#Measure.value> _:B66dbb43f4c55b6a44e931289145be554 .
    2023-03-12 21:48:01 <https://data.vmm.be/id/loc-00019-33> <http://www.w3.org/1999/02/22-rdf-syntax-ns#type> <http://def.isotc211.org/iso19156/2011/SpatialSamplingFeature#SF_SpatialSamplingFeature> .
    2023-03-12 21:48:01 <http://localhost:8080/sample> <https://w3id.org/tree#member> <urn:ngsi-ld:WaterQualityObserved:woq:1/2023-03-12T18:31:17.003Z> .
    2023-03-12 21:48:01 _:B0d2c29f506d81bb4b3ae1170f6b7ae96 <http://def.isotc211.org/iso19103/2005/UnitsOfMeasure#Measure.value> _:B1e0a50c94effea51ea151d334d1d70a4 .
    2023-03-12 21:48:01 _:Bf2d869f7dc0b939c5b6016ac7d1c451c <http://www.w3.org/1999/02/22-rdf-syntax-ns#type> <http://def.isotc211.org/iso19156/2011/Measurement#OM_Measurement> .
    2023-03-12 21:48:01 _:Bf2d869f7dc0b939c5b6016ac7d1c451c <http://def.isotc211.org/iso19156/2011/Observation#OM_Observation.featureOfInterest> <https://data.vmm.be/id/spt-00035-79> .
    2023-03-12 21:48:01 _:Bf2d869f7dc0b939c5b6016ac7d1c451c <http://def.isotc211.org/iso19156/2011/Observation#OM_Observation.observedProperty> <https://data.vmm.be/concept/waterkwaliteitparameter/conductiviteit> .
    2023-03-12 21:48:01 _:Bf2d869f7dc0b939c5b6016ac7d1c451c <http://def.isotc211.org/iso19156/2011/Observation#OM_Observation.phenomenonTime> "2023-03-12T18:31:17.003Z"^^<http://www.w3.org/2001/XMLSchema#datetime> .
    2023-03-12 21:48:01 _:Bf2d869f7dc0b939c5b6016ac7d1c451c <http://def.isotc211.org/iso19156/2011/Observation#OM_Observation.result> _:B22abd0fb7f46cf69b7367d7743860b0a .
    2023-03-12 21:48:01 _:Bf2d869f7dc0b939c5b6016ac7d1c451c <http://www.w3.org/ns/sosa/madeBySensor> <urn:ngsi-v2:cot-imec-be:Device:imec-iow-UR5gEycRuaafxnhvjd9jnU> .
    2023-03-12 21:48:01 _:B66dbb43f4c55b6a44e931289145be554 <https://schema.org/unitCode> <https://data.vmm.be/id/G42> .
    2023-03-12 21:48:01 _:B66dbb43f4c55b6a44e931289145be554 <https://schema.org/value> "6150"^^<http://www.w3.org/2001/XMLSchema#integer> .
 ```    

## Tear down the infrastructure and remove the volumes
Within the working directory, please run `docker compose down -v`





*The project locates at : [https://github.com/xdxxxdx/GetStarted_VSDS](GetStarted_VSDS), lives at: [https://xdxxxdx.github.io/GetStarted_VSDS/](https://xdxxxdx.github.io/GetStarted_VSDS/)*
