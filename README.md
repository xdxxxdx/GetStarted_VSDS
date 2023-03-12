
# Getting Started with [VSDS](https://www.vlaanderen.be/digitaal-vlaanderen/onze-oplossingen/vlaamse-smart-data-space)
A quick start manual for trying out VSDS project

## Before Start

 1. [Docker](https://docker.com) has been installed on your device.
 2.  Local Ports 8080, 27017 are free.

## 1. Publish your first [LDES collection](https://semiceu.github.io/LinkedDataEventStreams/)

 1. Create local docker-compose.yml file with following content:
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
              - LDES_HOSTNAME=http://localhost:${LDES_SERVER_PORT:-8080}
              - LDES_SHAPE=${LDES_SHAPE:-}
              - VIEW_TIMESTAMPPATH=http://www.w3.org/ns/prov#generatedAtTime
              - VIEW_VERSIONOFPATH=http://purl.org/dc/terms/isVersionOf
              - VIEWS_0_FRAGMENTATIONS_0_NAME=timebased
              - VIEWS_0_NAME=${VIEW_NAME:-by-time}
              - VIEWS_0_FRAGMENTATIONS_0_CONFIG_MEMBERLIMIT=${MEMBER_LIMIT:-2}
            ports:
              - ${LDES_SERVER_PORT:-8080}:8080
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
            command: "--url http://localhost:8080/sample/by-time --input-format text/turtle"
            profiles:
              - delayed-started
            network_mode: service:ldes-server
        networks:
          ldes:
            name: quick_start_network
```

## Publish your first LDES DataSet

Please run ```docker compose up``` under the directory: ```pyldes_kbo/Cegeka -KBO-Pagination```

Two docker containers should start at this moment: Ldes server and mongo DB

## Post KBO data to the Ldes server

Please run 

```for f in ../sample/bel20/*; do curl -i -X POST "http://localhost:8080/kbo" -H "Content-Type: application/turtle" -d "@$f";done```

Also modify the directory accordingly based on your setup.\

The current test is only for pagination of the [LDES server](https://github.com/Informatievlaanderen/VSDS-LDESServer4J)

```http://localhost:8080/kbo``` root of the LDES collection

curl -X POST http://localhost:8080/sample -H "Content-Type: application/ttl" -d '@sample.ttl'

docker compose create ldes-cli
docker compose up ldes-cli -d

