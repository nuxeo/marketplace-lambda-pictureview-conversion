# Nuxeo Lambda Integration

## Building

The marketplace package has to be built with the following command:

`mvn clean install`

## Configuration

Nuxeo AWS Lambda Integration leverages pluggability of the Nuxeo platform by using service extensions. 
`LambdaComponent` contains embedded class `NuxeoServiceImpl` that _implements_ `NuxeoService` interface. 
The component expects a following contribution or even contributions. There is no limit in contributing as many functions as you want.
```
<extension target="org.nuxeo.ecm.lambda.core.service.LambdaComponent" point="lambdaConfiguration">
   <lambdaConfigs name="your-function">
     <parameters>
       <parameter key="watermark_X">0</parameter>
       <parameter key="watermark_Y">0</parameter>
       <parameter key="watermark_width">200</parameter>
       <parameter key="watermark_height">200</parameter>
     </parameters>
   </lambdaConfigs>
 </extension>
 ```
 Where `lambdaConfigs` contains `name` property, which is the name of AWS Lambda function configured in your AWS account
 `parameters` tag contains additional parameters, that will be passed to the AWS Lambda function as a JSON with default parameters, such as `cbId`.
 `cbId` is a unique identifier, that will be expected on a Nuxeo endpoint.
 
 AWS Lambda does not have any authentication information for your Nuxeo instance. That is why _OpenURL_ extension must be configured to receive and process callback.
 By default, the addon has LambdaResponseReceiverServiceImpl, that follows next configuration:
 ```
 <extension target="org.nuxeo.ecm.lambda.integration.endpoint.service.LambdaResponseReceiverComponent" point="lambdaAcceptor">
     <lambdaReceiver class="org.nuxeo.ecm.lambda.integration.endpoint.service.LambdaResponseReceiverServiceImpl" />
   </extension>
 
   <extension point="openUrl"
              target="org.nuxeo.ecm.platform.ui.web.auth.service.PluggableAuthenticationService">
     <openUrl name="OpenLambdaEndpoint">
       <method>POST</method>
       <grantPattern>/nuxeo/site/lambda/error/.*</grantPattern>
       <grantPattern>/nuxeo/site/lambda/success/.*</grantPattern>
     </openUrl>
   </extension>
 ```
 
 It expects callbacks on two addresses:
 `http[s]://{your.address}/nuxeo/site/lambda/success/{callbackID}`
 
 and
 
 `http[s]://{your.address}/nuxeo/site/lambda/error/{callbackID}`
 
 The default implementation receives a response from your lambda and fires `afterLambdaResponse` event.
 To process the event you have to create a listener using Nuxeo *EventListener* extension. For further information pleas review the following documentation.
 https://doc.nuxeo.com/nxdoc/events-and-listeners/
 
 To satisfy your own needs, a new extension can be used using the default service as an example.
 
## About

This project is a Nuxeo plugin that integrate AWS Lambda to offload heavy processing from Nuxeo/WorkManager to AWS.

The initial use case is to offload the `PictureViews` generation (generating several rendition with different size of the same source image).

Statically sizing Nuxeo nodes and the `WorkManager` for handling a large upload of pictures is not ideal since we need to allocate a lot of resources that may be un-used most of the time. On the other hand, having a small number of conversion threads will result in a low processing when uploading thousands of pictures.

Using AWS Lambda is a great way to offload this processing:

 - we can easily start a lot of Lambda
 - Lambda have access to `imagemagick`
 - Lambda have access to Nuxeo binaries that are in S3


## Logical architecture

<img src="https://www.lucidchart.com/publicSegments/view/45160d9e-5a1d-4d7d-9c23-5561be103a3c/image.png" width="600px"></img> 

## About Nuxeo

Nuxeo dramatically improves how content-based applications are built, managed and deployed, making customers more agile, innovative and successful. Nuxeo provides a next generation, enterprise ready platform for building traditional and cutting-edge content oriented applications. Combining a powerful application development environment with SaaS-based tools and a modular architecture, the Nuxeo Platform and Products provide clear business value to some of the most recognizable brands including Verizon, Electronic Arts, Netflix, Sharp, FICO, the U.S. Navy, and Boeing. Nuxeo is headquartered in New York and Paris. More information is available at [www.nuxeo.com](http://www.nuxeo.com/).
