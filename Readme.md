# Nuxeo Lambda Integration

## Building

The marketplace package has to be built with the following command:

`mvn clean install`

## Configuration

to set up the function name used at your AWS account you need to update `nuxeo.conf` property `nuxeo.lambda.image.conversion`. Otherwise `nuxeo-lambda-picture` name will be used. 
 
 Contribution expects callbacks on two urls:
 `http[s]://{your.address}/nuxeo/site/lambda/success/{callbackID}`
 
 and
 
 `http[s]://{your.address}/nuxeo/site/lambda/error/{callbackID}`
 
 The default implementation receives a response from your lambda and fires `afterLambdaResponse` event.
 To process the event you have to create a listener using Nuxeo *EventListener* extension. For further information pleas review the following documentation.
 https://doc.nuxeo.com/nxdoc/events-and-listeners/
 
 To satisfy your own needs, a new extension can be used using the default service as an example.
 
## About

The contribution redirects all image conversion to AWS Lambda.
Currently, Nuxeo Lambda used for picture conversion creation.
Current implementation uses streaming to download the initial file from Nuxeo.
This approach allows to get around with storage limitation at AWS Lambda in many cases.

The contribution exposes `Picture.Recompute` automation operation. The operation accepts only one parameter - `query`.
It will recompute all `Picture`s that satisfies the given query using AWS Lambda. To get more information about calling automation operations
in Nuxeo, please follow our official [documentation](https://doc.nuxeo.com/nxdoc/automation/).

Using AWS Lambda is a great way to offload following processing:

 - we can easily start a lot of Lambda
 - Lambda have access to `imagemagick`
 - Lambda have access to Nuxeo binaries that are in S3


## Logical architecture

<img src="https://www.lucidchart.com/publicSegments/view/45160d9e-5a1d-4d7d-9c23-5561be103a3c/image.png" width="600px"></img> 

## About Nuxeo

Nuxeo dramatically improves how content-based applications are built, managed and deployed, making customers more agile, innovative and successful. Nuxeo provides a next generation, enterprise ready platform for building traditional and cutting-edge content oriented applications. Combining a powerful application development environment with SaaS-based tools and a modular architecture, the Nuxeo Platform and Products provide clear business value to some of the most recognizable brands including Verizon, Electronic Arts, Netflix, Sharp, FICO, the U.S. Navy, and Boeing. Nuxeo is headquartered in New York and Paris. More information is available at [www.nuxeo.com](http://www.nuxeo.com/).
