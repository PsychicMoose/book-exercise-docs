# Constrained Device Application (Connected Devices)

## Lab Module 01

Be sure to implement all the PIOT-CDA-* issues (requirements) listed at [PIOT-INF-01-001 - Lab Module 01](https://github.com/orgs/programming-the-iot/projects/1#column-9974937).

### Description

NOTE: Include two full paragraphs describing your implementation approach by answering the questions listed below.

What does your implementation do? 

How does your implementation work?


This implementation sets up the baseline structure for the Constrained Device App(CDA) in python that we will build upon throughout the rest of the class. This was accomplished by cloning the repo from github and setting it up to work in my own WSL windows 11 environment. I created a virtual environment and set the python working directory to the root of the CDA components repo. I also set DEFAULT_CONFIG_FILE_NAME = '/mnt/c/programmingtheiot/cda-python-components/config/PiotConfig.props' in ConfigConst.py to ensure that the system will pull the proper configurations everytime. 

This set up enabled me to run unit and integration tests that confirmed that the system was properly configured and that my environment enables further build out and implementation on the constrained device app. The tests I ran were the unit tests test_ConfigUtilDefault.py, test_ConfigUtilCustom.py and the integration test: test_ConstrainedDeviceApp.py. 


### Code Repository and Branch

NOTE: Be sure to include the branch (e.g. https://github.com/programming-the-iot/python-components/tree/alpha001).

URL: https://github.com/PsychicMoose/cda-python-components/tree/labmodule01

### UML Design Diagram(s)

NOTE: Include one or more UML designs representing your solution. It's expected each
diagram you provide will look similar to, but not the same as, its counterpart in the
book [Programming the IoT](https://learning.oreilly.com/library/view/programming-the-internet/9781492081401/).


### Unit Tests Executed

NOTE: TA's will execute your unit tests. You only need to list each test case below
(e.g. ConfigUtilTest, DataUtilTest, etc). Be sure to include all previous tests, too,
since you need to ensure you haven't introduced regressions.

- test_ConfigUtilDefault.py
- test_ConfigUtilCustom.py
- 

### Integration Tests Executed

NOTE: TA's will execute most of your integration tests using their own environment, with
some exceptions (such as your cloud connectivity tests). In such cases, they'll review
your code to ensure it's correct. As for the tests you execute, you only need to list each
test case below (e.g. SensorSimAdapterManagerTest, DeviceDataManagerTest, etc.)

- 
- test_ConstrainedDeviceApp.py
- 

EOF.
