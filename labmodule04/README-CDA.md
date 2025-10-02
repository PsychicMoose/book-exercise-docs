# Constrained Device Application (Connected Devices)

## Lab Module 04

Be sure to implement all the PIOT-CDA-* issues (requirements) listed at [PIOT-INF-04-001 - Lab Module 04](https://github.com/orgs/programming-the-iot/projects/1#column-10488386).

### Description

NOTE: Include two full paragraphs describing your implementation approach by answering the questions listed below.

What does your implementation do? 

How does your implementation work?

In this module, I extended my Constrained Device Application (CDA) to integrate full SenseHAT emulator support for both sensors and actuators. The implementation now dynamically loads either simulation tasks or emulator tasks based on configuration properties (enableSimulator, enableEmulator). This allows the CDA to run seamlessly in two different modes: a simulation mode that generates synthetic data using SensorDataGenerator and sim tasks, or an emulation mode that leverages the Pisense and Sense-Emu libraries to interact with the SenseHAT emulator GUI in real time.

The key change was updating the SensorAdapterManager and ActuatorAdapterManager classes to conditionally instantiate emulator tasks (HumiditySensorEmulatorTask, TemperatureSensorEmulatorTask, PressureSensorEmulatorTask, HumidifierEmulatorTask, HvacEmulatorTask, and LedDisplayEmulatorTask) using Python’s import_module. This ensures that emulator dependencies are only loaded when enabled in the configuration file. On startup, the CDA initializes its managers, which periodically generate or receive telemetry (via BackgroundScheduler) and handle actuator commands, dispatching results back through the DeviceDataManager. This flexible design makes the CDA capable of running on real hardware with emulator feedback, or on any system using only simulation.

### Code Repository and Branch

NOTE: Be sure to include the branch (e.g. https://github.com/programming-the-iot/python-components/tree/alpha001).

URL: https://github.com/PsychicMoose/cda-python-components/tree/labmodule04v

### UML Design Diagram(s)

NOTE: Include one or more UML designs representing your solution. It's expected each
diagram you provide will look similar to, but not the same as, its counterpart in the
book [Programming the IoT](https://learning.oreilly.com/library/view/programming-the-internet/9781492081401/).


```mermaid
classDiagram
    class ConstrainedDeviceApp {
        -SystemPerformanceManager sysPerfMgr
        -DeviceDataManager dataMgr
        +startApp()
        +stopApp()
    }

    class DeviceDataManager {
        -SensorAdapterManager sensorAdapterMgr
        -ActuatorAdapterManager actuatorAdapterMgr
        +handleSensorMessage()
        +handleActuatorCommand()
    }

    class SensorAdapterManager {
        -HumiditySensorSimTask humidityAdapter
        -PressureSensorSimTask pressureAdapter
        -TemperatureSensorSimTask tempAdapter
        -HumiditySensorEmulatorTask humidityEmu
        -PressureSensorEmulatorTask pressureEmu
        -TemperatureSensorEmulatorTask tempEmu
        +handleTelemetry()
        +startManager()
        +stopManager()
    }

    class ActuatorAdapterManager {
        -HumidifierActuatorSimTask humidifierActuator
        -HvacActuatorSimTask hvacActuator
        -HumidifierEmulatorTask humidifierEmu
        -HvacEmulatorTask hvacEmu
        -LedDisplayEmulatorTask ledDisplayEmu
        +sendActuatorCommand()
    }

    class HumiditySensorSimTask
    class PressureSensorSimTask
    class TemperatureSensorSimTask
    class HumiditySensorEmulatorTask
    class PressureSensorEmulatorTask
    class TemperatureSensorEmulatorTask

    class HumidifierActuatorSimTask
    class HvacActuatorSimTask
    class HumidifierEmulatorTask
    class HvacEmulatorTask
    class LedDisplayEmulatorTask

    ConstrainedDeviceApp --> DeviceDataManager
    DeviceDataManager --> SensorAdapterManager
    DeviceDataManager --> ActuatorAdapterManager
    SensorAdapterManager --> HumiditySensorSimTask
    SensorAdapterManager --> PressureSensorSimTask
    SensorAdapterManager --> TemperatureSensorSimTask
    SensorAdapterManager --> HumiditySensorEmulatorTask
    SensorAdapterManager --> PressureSensorEmulatorTask
    SensorAdapterManager --> TemperatureSensorEmulatorTask
    ActuatorAdapterManager --> HumidifierActuatorSimTask
    ActuatorAdapterManager --> HvacActuatorSimTask
    ActuatorAdapterManager --> HumidifierEmulatorTask
    ActuatorAdapterManager --> HvacEmulatorTask
    ActuatorAdapterManager --> LedDisplayEmulatorTask
    ```

### Unit Tests Executed

NOTE: TA's will execute your unit tests. You only need to list each test case below
(e.g. ConfigUtilTest, DataUtilTest, etc). Be sure to include all previous tests, too,
since you need to ensure you haven't introduced regressions.

ConfigUtilTest

DataUtilTest

ActuatorDataTest

SensorDataTest

SystemPerformanceDataTest

SystemCpuUtilTaskTest

SystemMemUtilTaskTest

### Integration Tests Executed

NOTE: TA's will execute most of your integration tests using their own environment, with
some exceptions (such as your cloud connectivity tests). In such cases, they'll review
your code to ensure it's correct. As for the tests you execute, you only need to list each
test case below (e.g. SensorSimAdapterManagerTest, DeviceDataManagerTest, etc.)

test_HumidityEmulatorTask.py
test_PressureEmulatorTask.py
test_TemperatureEmulatorTask.py

test_HumidifierEmulatorTask.py
test_HvacEmulatorTask.py
test_LedDisplayEmulatorTask.py

test_ActuatorEmulatorManager
test_SensorEmulatorManager
EOF.
