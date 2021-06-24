# hiring_exercises_01
Hiring exercises

## Exercise 1

### TASK 1
- Create a Python3 script which will fetch the weather data from OpenWeatherMap web APIs.
  - You can use the `pyowm` library
  - Script will read enviroment variables `OPENWEATHER_API_KEY` and `CITY_NAME`, takes no arguments.
  - Script will fail if the variables are not set.
  - It outputs to stdout

Example of acceptable results:

```sh
$ export
declare -x OPENWEATHER_API_KEY="xxxxxxxxxxxx"
declare -x CITY_NAME="Honolulu"
$ python getweather.py
source=openweathermap, city="Honolulu", description="few clouds", temp=70.2,humidity=75
```

### TASK 2
- create an Ansible playbook to install the docker service
- Enable container logging to Docker host's syslog file

Example of expected result:

```sh
$ docker
The program 'docker' is currently not installed. You can install it by typing:
sudo apt install docker.io

$ ansible-playbook -i "localhost," -c local site.yml
...
...
PLAY RECAP *********
localhost                  : ok=9    changed=1    unreachable=0    failed=0

$ docker -v
Docker version 17.12.0-ce, build c97c6d6

$ docker info | grep 'Logging Driver'
Logging Driver: syslog
```


### TASK 3
- Build a docker image (Dockerfile) configured to run as executable
  - include the Python3 script into the Dockerfile
- run the docker for a specific CITY_NAME

Example output:
```sh
$ docker run --rm -e OPENWEATHER_API_KEY="xxxxxxxxxxxx" -e CITY_NAME="Honolulu"
$ grep openweathermap /var/log/syslog
Nov 30 11:50:07 ubuntu-vm ae9395e86676[1621]: source=openweathermap,city="Honolulu", description="few clouds", temp=70.2, humidity=75
```


## Exercise 2

### Prerequisites

- working ssh access to the IOS-XR always-on Cisco DevNet sandbox
  > NOTE: if the SSH doesn't work, please check if the sandbox parameters have not been changed on the Cisco DevNet sandbox site: https://developer.cisco.com/site/sandbox/ . A working Cisco account (Free account is enough) is needed to be able to access the Cisco DevNet sandbox site.
  > Cisco IOS-XR Sandbox details are available here: https://devnetsandbox.cisco.com/RM/Diagram/Index/e83cfd31-ade3-4e15-91d6-3118b867a0dd?diagramType=Topology
    
    ```
    IOS XRv 9000 Host:  sbx-iosxr-mgmt.cisco.com
    SSH Port:           8181
    NETCONF Port:       10000
    ```

### TASK 1

- Create a python3 code which will be able to generate the bellow Cisco IOS-XR configuration.

  - store the variable parameters in a separate `yaml` file. Variable parameters are:
    - interface name, IPv4 address, description
    - vrf name, address-family, import/export route-targets
  
  - render the config file using a `jinja2` template file(s)
    - you can use Ansible, Nornir, or native jinja2 python3 library to render the configuration

  - **QUESTION 1**: With regards to the best practices of an E2E SW design, your code needs to be flexible enough to support possible extensions. One of such extension could be to generate a BGP configuration. How would you structure your YAML/JINJA2 files in order to avoid having the same parameters defined in multiple yaml files?

```
interface Loopback64
 description DTPN_Loopback64
 ipv4 address 11.22.11.22 255.255.255.255
!
vrf dtpn_ext_test
 address-family ipv4 unicast
  import route-target
   11:22
   22:11
  !
  export route-target
   11:22
  !
 !
!
```

### TASK 2

- Apply and commit the generated configuration to the `sbx-iosxr-mgmt.cisco.com` IOS-XR router.

  - You can use SSH as the transport protocol
  - do not replace currently running configuration

- **QUESTION 1**: How would you push the configuration in a more programmatic way, could you use REST-API in this particular case?

- **QUESTION 2**: How would you solve Idempotency problem?

- **QUESTION 3**: You need to configure a 100s devices, how can you speed up this process in Python3?


#### TASK 3

- Verify the configuration was applied correctly.

  - check the interface Loopback64 operational status and print the result in a JSON format
  - check the vrf status and print the result in a JSON format

Expected result:
```json
{
  "interfaces": [
    {
      "loopback64": {
        "ipv4_address": "11.12.11.12",
        "description": "DTPN_Loopback64"
      }
    }
  ],
  "vrfs": [
    {
      "dtpn_ext_test": {
        "import_rts": [
          11:22,
          22:11
        ],
        "export_rts": [
          11:22
        ]
      }
    }
  ]
}
```
