# Constrained Device Application (Connected Devices)

## Lab Module 10

Be sure to implement all the PIOT-CDA-* issues (requirements) listed at [PIOT-INF-10-001 - Lab Module 10](https://github.com/orgs/programming-the-iot/projects/1#column-10488510).

### Description

NOTE: Include two full paragraphs describing your implementation approach by answering the questions listed below.

What does your implementation do? 

This implementation establishes secure, bidirectional MQTT communication between the Constrained Device Application (CDA) and Gateway Device Application (GDA). The system enables the CDA to transmit sensor data and system performance metrics upstream to the GDA while simultaneously receiving and processing actuator commands downstream. Key features include TLS/SSL encryption support for MQTT communications on port 8883, comprehensive performance benchmarking for all three MQTT QoS levels (0, 1, 2), and an event-driven architecture that handles actuator commands through callback mechanisms. The implementation also includes automated threshold monitoring for temperature sensors that triggers HVAC actuator adjustments when floor or ceiling values are exceeded.

How does your implementation work?

The implementation works through a layered architecture where the DeviceDataManager orchestrates all communication flows between the CDA's internal components and the MQTT broker. For upstream data transmission, sensor readings and system performance metrics are collected by their respective adapter managers, converted to JSON format via DataUtil, and published to specific MQTT topics using the MqttClientConnector with configurable QoS levels. For downstream command reception, the MqttClientConnector subscribes to the CDA actuator command topic upon connection establishment and registers a topic-specific callback (onActuatorCommandMessage) that parses incoming JSON payloads into ActuatorData objects. These commands are then routed through the IDataMessageListener interface to the DeviceDataManager's handleActuatorCommandMessage method, which validates and forwards them to the ActuatorAdapterManager for execution. The system prevents deadlock conditions by removing blocking wait_for_publish calls and implementing asynchronous message handling, while TLS encryption is enabled through certificate configuration and SSL context setup using the ssl.PROTOCOL_TLS_CLIENT protocol.

### Code Repository and Branch

NOTE: Be sure to include the branch (e.g. https://github.com/programming-the-iot/python-components/tree/alpha001).

URL: https://github.com/programming-the-iot/python-components/tree/labmodule10

### UML Design Diagram(s)

NOTE: Include one or more UML designs representing your solution. It's expected each
diagram you provide will look similar to, but not the same as, its counterpart in the
book [Programming the IoT](https://learning.oreilly.com/library/view/programming-the-internet/9781492081401/).
```mermaid
classDiagram
    class DeviceDataManager {
        -configUtil: ConfigUtil
        -mqttClient: MqttClientConnector
        -actuatorAdapterMgr: ActuatorAdapterManager
        -sensorAdapterMgr: SensorAdapterManager
        -sysPerfManager: SystemPerformanceManager
        -enableMqttClient: bool
        +startManager() bool
        +stopManager() bool
        +handleActuatorCommandMessage(ActuatorData) ActuatorData
        +handleSensorMessage(SensorData) bool
        +handleSystemPerformanceMessage(SystemPerformanceData) bool
        -_handleUpstreamTransmission(ResourceNameEnum, str) void
        -_handleSensorDataAnalysis(SensorData) void
    }
    
    class MqttClientConnector {
        -config: ConfigUtil
        -mqttClient: Client
        -host: str
        -port: int
        -securePort: int
        -enableEncryption: bool
        -pemFileName: str
        -dataMsgListener: IDataMessageListener
        -defaultQos: int
        +connectClient() bool
        +disconnectClient() bool
        +publishMessage(ResourceNameEnum, str, int) bool
        +subscribeToTopic(ResourceNameEnum, int) bool
        +setDataMessageListener(IDataMessageListener) void
        +onConnect(client, userdata, flags, rc) void
        +onActuatorCommandMessage(client, userdata, msg) void
        +onMessage(client, userdata, msg) void
        +onPublish(client, userdata, mid) void
    }
    
    class IDataMessageListener {
        <<interface>>
        +handleActuatorCommandMessage(ActuatorData) ActuatorData
        +handleSensorMessage(SensorData) bool
        +handleSystemPerformanceMessage(SystemPerformanceData) bool
    }
    
    class ActuatorAdapterManager {
        -dataMsgListener: IDataMessageListener
        -configUtil: ConfigUtil
        -hvacEmulator: HvacEmulatorTask
        +sendActuatorCommand(ActuatorData) ActuatorData
        +setDataMessageListener(IDataMessageListener) void
    }
    
    class SensorAdapterManager {
        -dataMsgListener: IDataMessageListener
        -configUtil: ConfigUtil
        -tempSensor: TemperatureSensorSimTask
        +handleTelemetry() void
        +setDataMessageListener(IDataMessageListener) void
    }
    
    class DataUtil {
        +actuatorDataToJson(ActuatorData) str
        +jsonToActuatorData(str) ActuatorData
        +sensorDataToJson(SensorData) str
        +jsonToSensorData(str) SensorData
        +systemPerformanceDataToJson(SystemPerformanceData) str
    }
    
    class ActuatorData {
        -name: str
        -command: int
        -stateData: str
        -curValue: float
        -isResponse: bool
        -actuatorType: int
        +getCommand() int
        +setCommand(int) void
        +setStateData(str) void
    }
    
    class SensorData {
        -name: str
        -curValue: float
        -sensorType: int
        -timestamp: str
        +getValue() float
        +setValue(float) void
    }

    DeviceDataManager ..|> IDataMessageListener : implements
    DeviceDataManager --> MqttClientConnector : uses
    DeviceDataManager --> ActuatorAdapterManager : uses
    DeviceDataManager --> SensorAdapterManager : uses
    DeviceDataManager --> DataUtil : uses
    MqttClientConnector --> IDataMessageListener : notifies
    MqttClientConnector --> DataUtil : uses
    ActuatorAdapterManager --> ActuatorData : processes
    SensorAdapterManager --> SensorData : generates
    DataUtil --> ActuatorData : converts
    DataUtil --> SensorData : converts
```

### Unit Tests Executed

NOTE: TA's will execute your unit tests. You only need to list each test case below
(e.g. ConfigUtilTest, DataUtilTest, etc). Be sure to include all previous tests, too,
since you need to ensure you haven't introduced regressions.

- MqttClientConnectorTest
- ActuatorDataTest
- SensorDataTest
- DataUtilTest

### Integration Tests Executed

NOTE: TA's will execute most of your integration tests using their own environment, with
some exceptions (such as your cloud connectivity tests). In such cases, they'll review
your code to ensure it's correct. As for the tests you execute, you only need to list each
test case below (e.g. SensorSimAdapterManagerTest, DeviceDataManagerTest, etc.)

- MqttClientConnectorTest (testActuatorCmdPubSub)
- MqttClientPerformanceTest (testPublishQoS0, testPublishQoS1, testPublishQoS2, testConnectAndDisconnect)
- DeviceDataManagerCallbackTest (testActuatorDataCallback)
- DeviceDataManagerIntegrationTest (testDeviceDataMgrTimedIntegration)



## CDA MQTT Client Performance Test Results

### Test Configuration
- Test Date: 2025-11-18
- Messages per test: 10,000
- Payload size: 264 bytes

### Non-TLS Results (Port 1883)
- Connect/Disconnect: 19.69 ms
- QoS 0: 4,382.82 ms (0.438 ms/msg)
- QoS 1: 7,559.00 ms (0.756 ms/msg) - **72.5% slower than QoS 0**
- QoS 2: 11,728.49 ms (1.173 ms/msg) - **167.6% slower than QoS 0**

### TLS Results (Port 8883)
- Connect/Disconnect: 1,075.26 ms
- QoS 0: 5,131.37 ms (0.513 ms/msg)
- QoS 1: 8,058.05 ms (0.806 ms/msg) - **57.0% slower than QoS 0**
- QoS 2: 13,584.54 ms (1.358 ms/msg) - **164.7% slower than QoS 0**

### Performance Analysis
**Which ran fastest?** QoS 0 without TLS (4,382.82 ms)
**Which ran slowest?** QoS 2 with TLS (13,584.54 ms)

**TLS Overhead:**
- Connect/Disconnect: 5,362% slower with TLS
- QoS 0: 17.1% slower with TLS
- QoS 1: 6.6% slower with TLS
- QoS 2: 15.8% slower with TLS

EOF.