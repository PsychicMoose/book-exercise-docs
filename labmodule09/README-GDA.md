# Gateway Device Application (Connected Devices)

## Lab Module 09

Be sure to implement all the PIOT-GDA-* issues (requirements) listed at [PIOT-INF-09-001 - Lab Module 09](https://github.com/orgs/programming-the-iot/projects/1#column-10488503).

### Description

This implementation provides a comprehensive CoAP (Constrained Application Protocol) client and server infrastructure for the Gateway Device Application, enabling reliable machine-to-machine communication between the GDA and CDA. The implementation includes a fully functional CoAP client connector that supports all standard CoAP methods (GET, POST, PUT, DELETE), resource discovery through the .well-known/core mechanism, and asynchronous observation of resources using the CoAP observe pattern. Additionally, it includes a CoAP server gateway that can host resources and respond to incoming requests, making the GDA capable of both initiating requests to constrained devices and serving its own resources to other nodes in the IoT network.

The implementation leverages the Eclipse Californium 3.x framework to handle the CoAP protocol stack, with careful configuration management to avoid common initialization issues. The architecture follows a handler-based pattern where incoming CoAP responses and observations are processed through generic handlers that parse the payload based on resource type and delegate to appropriate data message listeners. The client maintains a shared endpoint for efficient resource utilization, tracks active observation relationships for proper lifecycle management, and provides both confirmable (CON) and non-confirmable (NON) message support based on QoS requirements. The server side uses a resource tree structure with observable resources that can notify subscribers of state changes, implementing the full RESTful paradigm over UDP transport.

### Code Repository and Branch

URL: https://github.com/[your-username]/gda-java-components/tree/[your-branch]

### UML Design Diagram(s)

#### CoAP Client Architecture

```mermaid
classDiagram
    class IRequestResponseClient {
        <<interface>>
        +sendDiscoveryRequest(timeout: int): boolean
        +sendGetRequest(resource: ResourceNameEnum, name: String, enableCON: boolean, timeout: int): boolean
        +sendPostRequest(resource: ResourceNameEnum, name: String, enableCON: boolean, payload: String, timeout: int): boolean
        +sendPutRequest(resource: ResourceNameEnum, name: String, enableCON: boolean, payload: String, timeout: int): boolean
        +sendDeleteRequest(resource: ResourceNameEnum, name: String, enableCON: boolean, timeout: int): boolean
        +startObserver(resource: ResourceNameEnum, name: String, ttl: int): boolean
        +stopObserver(resource: ResourceNameEnum, name: String, timeout: int): boolean
        +setDataMessageListener(listener: IDataMessageListener): boolean
    }

    class CoapClientConnector {
        -protocol: String
        -host: String
        -port: int
        -serverAddr: String
        -clientConn: CoapClient
        -dataMsgListener: IDataMessageListener
        -endpoint: CoapEndpoint
        -observeRelations: Map~String, CoapObserveRelation~
        +CoapClientConnector()
        +CoapClientConnector(host: String, isSecure: boolean, enableConfirmedMsgs: boolean)
        +shutdown(): void
        -initClient(): void
        -createResourcePath(resource: ResourceNameEnum, name: String): String
    }

    class GenericCoapResponseHandler {
        -dataMsgListener: IDataMessageListener
        -resourceType: ResourceNameEnum
        -dataUtil: DataUtil
        +GenericCoapResponseHandler()
        +GenericCoapResponseHandler(listener: IDataMessageListener)
        +GenericCoapResponseHandler(listener: IDataMessageListener, resourceType: ResourceNameEnum)
        +onLoad(response: CoapResponse): void
        +onError(): void
        +setDataMessageListener(listener: IDataMessageListener): void
        +setResourceType(resourceType: ResourceNameEnum): void
        -processResponsePayload(payload: String): void
    }

    class CoapHandler {
        <<interface>>
        +onLoad(response: CoapResponse): void
        +onError(): void
    }

    class DeviceDataManager {
        -enableCoapClient: boolean
        -coapClient: IRequestResponseClient
        +handleActuatorCommandRequest(resourceName: ResourceNameEnum, data: ActuatorData): boolean
        +startManager(): void
        +stopManager(): void
        -initManager(): void
    }

    IRequestResponseClient <|.. CoapClientConnector : implements
    CoapHandler <|.. GenericCoapResponseHandler : implements
    CoapClientConnector --> GenericCoapResponseHandler : uses
    CoapClientConnector --> CoapClient : uses
    CoapClientConnector --> CoapEndpoint : uses
    CoapClientConnector --> CoapObserveRelation : manages
    DeviceDataManager --> IRequestResponseClient : uses
    GenericCoapResponseHandler --> IDataMessageListener : notifies
```

#### CoAP Server Architecture

```mermaid
classDiagram
    class CoapServerGateway {
        -useDefaultResources: boolean
        -enableConfirmedMsgs: boolean
        -serverName: String
        -port: int
        -coapServer: CoapServer
        -dataMsgListener: IDataMessageListener
        -resourceHandlers: List~GenericCoapResourceHandler~
        +CoapServerGateway()
        +CoapServerGateway(dataMsgListener: IDataMessageListener)
        +setDataMessageListener(listener: IDataMessageListener): boolean
        +startServer(): boolean
        +stopServer(): boolean
        -createAndAddResourceHandlers(): void
        -initServer(rootResource: Resource): void
    }

    class GenericCoapResourceHandler {
        -dataMsgListener: IDataMessageListener
        -resourceType: ResourceNameEnum
        -dataUtil: DataUtil
        +GenericCoapResourceHandler(resource: ResourceNameEnum)
        +GenericCoapResourceHandler(resourceName: String)
        +handleGET(context: CoapExchange): void
        +handlePOST(context: CoapExchange): void
        +handlePUT(context: CoapExchange): void
        +handleDELETE(context: CoapExchange): void
        +setDataMessageListener(listener: IDataMessageListener): void
        -handleDataSubmission(context: CoapExchange, method: String): void
    }

    class CoapResource {
        <<abstract>>
        +handleGET(exchange: CoapExchange): void
        +handlePOST(exchange: CoapExchange): void
        +handlePUT(exchange: CoapExchange): void
        +handleDELETE(exchange: CoapExchange): void
    }

    class CoapServer {
        +add(resource: Resource): void
        +start(): void
        +stop(): void
    }

    CoapResource <|-- GenericCoapResourceHandler : extends
    CoapServerGateway --> CoapServer : manages
    CoapServerGateway --> GenericCoapResourceHandler : creates
    GenericCoapResourceHandler --> IDataMessageListener : notifies
    CoapServer --> GenericCoapResourceHandler : hosts
```

### Unit Tests Executed

NOTE: Unit tests focus on individual component functionality without external dependencies.

-

### Integration Tests Executed

NOTE: Integration tests verify end-to-end functionality with actual CoAP communication.

- CoapClientPerformanceTest
- CoapDiscoveryTest



EOF.