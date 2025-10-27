# Constrained Device Application (Connected Devices)

## Lab Module 06

Be sure to implement all the PIOT-CDA-* issues (requirements) listed at [PIOT-INF-06-001 - Lab Module 06](https://github.com/orgs/programming-the-iot/projects/1#column-10488434).

### Description

NOTE: Include two full paragraphs describing your implementation approach by answering the questions listed below.

This implementation adds MQTT connectivity to the Constrained Device Application (CDA), enabling it to communicate with the Gateway Device Application (GDA) using the MQTT protocol. The CDA can now publish sensor data, system performance metrics, and actuator responses to the MQTT broker, while also subscribing to actuator commands from the GDA. This creates a reliable, bi-directional communication channel between the CDA and GDA using a publish-subscribe messaging pattern.

The implementation works by integrating the Paho MQTT Python client into the CDA's DeviceDataManager. When the CDA starts, it establishes a connection to the configured MQTT broker and subscribes to relevant topics for receiving actuator commands. The CDA publishes sensor readings and system performance data at regular intervals to specific MQTT topics that the GDA subscribes to. The MQTT client handles connection management, automatic reconnection, quality of service levels, and message delivery confirmation. All MQTT operations are managed through the MqttClientConnector class, which provides a clean abstraction layer for the DeviceDataManager to use without dealing with low-level MQTT protocol details.

### Code Repository and Branch

NOTE: Be sure to include the branch (e.g. https://github.com/programming-the-iot/python-components/tree/alpha001).

URL: https://github.com/PsychicMoose/book-exercise-docs/tree/labmodule06

### UML Design Diagram(s)

NOTE: Include one or more UML designs representing your solution. It's expected each
diagram you provide will look similar to, but not the same as, its counterpart in the
book [Programming the IoT](https://learning.oreilly.com/library/view/programming-the-internet/9781492081401/).

```mermaid
classDiagram
    class DeviceDataManager {
        -bool enableMqttClient
        -MqttClientConnector mqttClient
        -SensorAdapterManager sensorAdapterMgr
        -ActuatorAdapterManager actuatorAdapterMgr
        -SystemPerformanceManager sysPerfMgr
        +startManager() bool
        +stopManager() bool
        +handleActuatorCommandRequest(ResourceNameEnum, ActuatorData) bool
        +handleSensorMessage(ResourceNameEnum, SensorData) bool
        +handleSystemPerformanceMessage(ResourceNameEnum, SystemPerformanceData) bool
        -_initManagers() None
    }
    
    class MqttClientConnector {
        -Client mqttClient
        -str clientId
        -str host
        -int port
        -int keepAlive
        -IDataMessageListener dataMsgListener
        +connectClient() bool
        +disconnectClient() bool
        +publishMessage(ResourceNameEnum, msg, qos) bool
        +subscribeToTopic(ResourceNameEnum, qos) bool
        +unsubscribeFromTopic(ResourceNameEnum) bool
        +setDataMessageListener(IDataMessageListener) bool
        +on_connect(client, userdata, flags, rc) None
        +on_disconnect(client, userdata, rc) None
        +on_message(client, userdata, msg) None
        +on_publish(client, userdata, mid) None
        +on_subscribe(client, userdata, mid, granted_qos) None
    }
    
    class IDataMessageListener {
        <<interface>>
        +handleActuatorCommandRequest(ResourceNameEnum, ActuatorData) bool
        +handleSensorMessage(ResourceNameEnum, SensorData) bool
        +handleSystemPerformanceMessage(ResourceNameEnum, SystemPerformanceData) bool
        +handleIncomingMessage(ResourceNameEnum, msg) bool
    }
    
    class SensorAdapterManager {
        -IDataMessageListener dataMsgListener
        -list sensorAdapters
        +startManager() bool
        +stopManager() bool
        +setDataMessageListener(IDataMessageListener) None
    }
    
    class ActuatorAdapterManager {
        -IDataMessageListener dataMsgListener
        -dict actuatorAdapters
        +startManager() bool
        +stopManager() bool
        +setDataMessageListener(IDataMessageListener) None
        +sendActuatorCommand(ActuatorData) bool
    }
    
    class SystemPerformanceManager {
        -IDataMessageListener dataMsgListener
        +startManager() bool
        +stopManager() bool
        +setDataMessageListener(IDataMessageListener) None
    }
    
    class ConstrainedDeviceApp {
        -DeviceDataManager dataMgr
        +startApp() None
        +stopApp(int) None
        +main() None
    }
    
    DeviceDataManager ..|> IDataMessageListener
    DeviceDataManager --> MqttClientConnector : uses
    DeviceDataManager --> SensorAdapterManager : manages
    DeviceDataManager --> ActuatorAdapterManager : manages
    DeviceDataManager --> SystemPerformanceManager : manages
    ConstrainedDeviceApp --> DeviceDataManager : creates
    MqttClientConnector --> IDataMessageListener : notifies
    SensorAdapterManager --> IDataMessageListener : publishes to
    SystemPerformanceManager --> IDataMessageListener : publishes to
    ```


### Unit Tests Executed

NOTE: TA's will execute your unit tests. You only need to list each test case below
(e.g. ConfigUtilTest, DataUtilTest, etc). Be sure to include all previous tests, too,
since you need to ensure you haven't introduced regressions.


- ConfigUtilTest
- DataUtilTest
- ActuatorDataTest
- SensorDataTest
- SystemPerformanceDataTest
- 
- 

### Integration Tests Executed

NOTE: TA's will execute most of your integration tests using their own environment, with
some exceptions (such as your cloud connectivity tests). In such cases, they'll review
your code to ensure it's correct. As for the tests you execute, you only need to list each
test case below (e.g. SensorSimAdapterManagerTest, DeviceDataManagerTest, etc.)



### Integration Tests Executed

- MqttClientConnectorTest
- DeviceDataManagerTest
- SensorAdapterManagerTest
- ActuatorAdapterManagerTest
- 

EOF.
