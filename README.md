# The Openapi-HyperMedia format (OHM)

## Version 0.1.0

This document is licensed under [The Apache License, Version 2.0](https://www.apache.org/licenses/LICENSE-2.0.html).

## Introduction

The Openapi-HyperMedia format (OHM), is a format used to describe REST level 3 APIs.
A crucial caracteristic of these APIs is that they use Hypermedia As The Engine Of Application State (HATEOAS) which means that all the controls that allow to go to the next application state must be sent in the server response together with the resource representation.
HTML is the most well-known format permitting HATEOAS thanks to links and forms but it is not convenient for machine processing.
OHM aims at providing a format readable by machines while exposing HATEOAS controls for dynamic processing and documentation.

## The format

OHM relies on 2 other well-defined and well-known formats to describe a REST resource:
* JSON for the representation of the resources
* [OpenAPI](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/3.0.3.md) for the Hypermedia controls

An OHM document is itself a JSON document in the form:
```json
{
    "content": <JSON representation of the resource>,
    "controls": <An OpenAPI Specification (OAS) in JSON format>
}
```

Example for a `Person` resource which has a link to its `Address` resource located at `/api/addresses/1`
```json
{
    "content": {
        "firstName": "John",
        "lastName": "Doe"
    },
    "controls": {
        "openapi": "3.0.1",
        "info": {
            "title": "rest",
            "version": "0.0.1"
        },
        "paths": {
            "/api/addresses/1": {
                "get": {
                    "summary": "John Doe's address"
                }
            }
        }
    }
}
```
The media-type for the OHM format shall be `application/ohm+json`.

## OHM Clients/Browsers

[Open a swagger-UI based OHM browser](https://raw.githack.com/cbornet/swagger-ui/ohm/dist/)


Being a level 3 RESTful format respecting HATEOAS, it is possible to design clients that can work with any OHM API.
Since OHM uses OpenAPI for its controls, libraries that can handle this format such as [Swagger-Client](https://github.com/swagger-api/swagger-js) and even slightly modify the famous [Swagger-UI](https://github.com/swagger-api/swagger-ui) can be used to browse an OHM API.

## Demo OHM application

You can find a demo application implementing OHM [here](https://rest-openapi-demo.herokuapp.com) ([source code](https://github.com/cbornet/sample-rest-app)).
As a truly RESTful application, it can be run from any OHM client without any prior knowledge or specific code in the client about the application.
An OHM browser based on a modified Swagger-UI is served by the application for convenience but any other OHM browser would be able to control the application.

## Rationale behind OHM format

The OHM format aims at easily transitionning from HTTP-JSON APIs to more RESTful APIs. 

The steps for transition would be:
* first put the current payload of the endpoints in the `content` field without modification and change the response content-type to `application/ohm+json`
* adapt the client (React/Angular/Vue) application to dereference the `content` field when getting responses from the server. Note that the OHM format can perfectly work with non level 3 RESTful applications that use static routes.
* then add controls in the `controls` field progressively to enrich the OHM application.
* ensure that there's a resource that can be considered as an entry point and from which any resource of the application can be accessed via other resources.

OHM has a lot of similarities with Hyper-Schema but it uses OpenAPI to describe the links instead of LDOs.
The advantage is that since OpenAPI has been widely adopted, there's a lot of resource and libraries available.
OpenAPI has also proven to be very versatile to describe any type of HTTP operations while LDOs have some limitations.
Also by relying on an existing standard it makes the OHM specification very lightweight and familiar when you know OpenAPI.
