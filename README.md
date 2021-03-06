# CXF Interceptors

## Description

Bundle provides services extending CXF features
* `CorrelationIdFeature` - adds `correlationId` to all logs. Interceptors from this feature are executed in phases `RECEIVE` (adding `correlationId`) and `PRE_PROTOCOL` (deleting).
* `LoggingFeature` - logs CXF requests and responses. Interceptors from this feature are executed in phase `PRE_STREAM`.

## Recommendations

* `CorrelationIdFeature` should be added only in locations, where WebService comes (`<jaxws:endpoint />`), it should *not* be added to client  (`<jaxws:client />`).

## Adding `correlationId` algorithm

0. If incoming request has `X-CORRELATION-ID` header, insert it's value to MDC with key `correlationId`.
0. Else if MDC has entry with key `correlationId` leave this value,
0. Else generate `corrrelationId` and insert it to MDC wit key `correlationId`.

## Example

```xml
<blueprint xmlns="http://www.osgi.org/xmlns/blueprint/v1.0.0"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:jaxws="http://cxf.apache.org/blueprint/jaxws"
           xsi:schemaLocation="http://www.osgi.org/xmlns/blueprint/v1.0.0 http://www.osgi.org/xmlns/blueprint/v1.0.0/blueprint.xsd
                    http://cxf.apache.org/blueprint/jaxws http://cxf.apache.org/schemas/blueprint/jaxws.xsd">

     <jaxws:endpoint bus="servicebus" id="serviceEndpoint" implementor="#soap"
                        endpointName="s:webServiceSoap" serviceName="s:webService" address="${service.incoming.address}"
                        wsdlLocation="test.wsdl" xmlns:s="http://test.touk.pl">
            <jaxws:features>
                <bean class="pl.touk.cxf.interceptors.CorrelationIdFeature" />
                <bean class="pl.touk.cxf.interceptors.logging.LoggingFeature" />
            </jaxws:features>
        </jaxws:endpoint>
</blueprint>
```
