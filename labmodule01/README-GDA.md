# Gateway Device Application (Connected Devices)

## Lab Module 01

Be sure to implement all the PIOT-GDA-* issues (requirements) listed at [PIOT-INF-01-001 - Lab Module 01](https://github.com/orgs/programming-the-iot/projects/1#column-9974937).

### Description

NOTE: Include two full paragraphs describing your implementation approach by answering the questions listed below.

What does your implementation do? 

My Lab 01 implementation sets up the environment for the Gateway Device Application (GDA). It configures the project so that the applications can locate and load their default property files, initializes the applications without error, and verifies that the unit and integration tests execute successfully. This establishes a working foundation for future labs.


How does your implementation work?
The GDA entry point (GatewayDeviceApp) reads default properties via ConfigConst/ConfigUtil, then exposes a simple lifecycle: startApp() to initialize and run briefly, and stopApp() to perform a clean shutdown. The app was built using Java with Maven, and tests were run on the configs(configUtilCustomTest and ConfigUtilDefaultTest), as well as an integration test to ensure the app starts(GatewayDeviceAppTest).

### Code Repository and Branch

NOTE: Be sure to include the branch (e.g. https://github.com/programming-the-iot/python-components/tree/alpha001).

URL: https://github.com/PsychicMoose/gda-java-components/tree/labmodule01

### UML Design Diagram(s)

NOTE: Include one or more UML designs representing your solution. It's expected each
diagram you provide will look similar to, but not the same as, its counterpart in the
book [Programming the IoT](https://learning.oreilly.com/library/view/programming-the-internet/9781492081401/).


### Unit Tests Executed

NOTE: TA's will execute your unit tests. You only need to list each test case below
(e.g. ConfigUtilTest, DataUtilTest, etc). Be sure to include all previous tests, too,
since you need to ensure you haven't introduced regressions.

- ConfigUtilCustomTest
- ConfigUtilDefaultTest
- 

### Integration Tests Executed

NOTE: TA's will execute most of your integration tests using their own environment, with
some exceptions (such as your cloud connectivity tests). In such cases, they'll review
your code to ensure it's correct. As for the tests you execute, you only need to list each
test case below (e.g. SensorSimAdapterManagerTest, DeviceDataManagerTest, etc.)

- 
- GatewayDeviceAppTest
- 

EOF.
