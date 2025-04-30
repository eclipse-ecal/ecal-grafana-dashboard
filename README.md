# eCAL-Dashboard

The eCAL-Dashboard provides functionality for monitoring the state of the eCAL middleware. The dashboard offers insights into:

- The status of the system's hosts, including their RAM, Disk, and CPU.
- The communication topology present in the system, including:
  - Interactions between publishers and subscribers of the topics,
  - Connections between clients and services,
  - Summary data at the process level,
  - Aggregated information at the host level.
- Performance metrics for both publishers and subscribers, such as write and read performance, as well as message drops.

## Architecture

The application consists of a Python script that polls eCAL's Monitoring API at predefined intervals. It subscribes to the "machine_state_[HOSTNAME]"-named topics, to which eCAL's Machine Monitoring Agents (MMAs) publish information regarding individual hosts (CPU, RAM, Disk, etc.). The collected data is then inserted into a SQLite database, which serves as the data source for the Grafana dashboards.

The messages sent by the MMA follow the protobuf specification provided in `proto_messages\mma.proto`. This protofile stems from [here](https://github.com/eclipse-ecal/ecal/blob/8d05b9cea0eeb650ffae2bb107edb7abc5feb29e/app/app_pb/src/ecal/app/pb/mma/mma.proto#L4) and has to be considered in upgrades.

## Preparations

### For running on Windows
1. You find the eCAL installation [here](https://eclipse-ecal.github.io/ecal/stable/getting_started/setup.html#getting-started-setup).
2. Additionally, install a Python Wheel for eCAL6, available from the [eCAL repository](https://github.com/eclipse-ecal/ecal). To facilitate the usage of the script prior to an official eCAL6 release, the wheel used for development is provided and can be installed using pip.
3. Install Grafana on Windows like [here](https://grafana.com/docs/grafana/latest/setup-grafana/installation/windows/). 
Start Grafana by executing `grafana-server.exe`, located in the `bin` directory
Login on http://localhost:3000/login using ```admin``` as username and password and import the dashboards from .../ecal-grafana-dashboard/grafana/. Add a new connection from SQLite (Probably also to install) and set the `DB` Folder as Path using your individual adjustments.
4. You may want to use a Virtual Environment like [here](https://python.land/virtual-environments/virtualenv)
5. Before starting the monitoring application, ensure that the required packages are installed. These can be found in the requirements.txt file in this folder. The requirements can be installed using the command:
```bash 
pip install -r requirements.txt
```

### For running in WSL

1. To install a WSL on Windows follow the instructions [here](https://ubuntu.com/desktop/wsl).
2. [Install](https://de.linux-terminal.com/?p=7616) Python version 3.12.X and set as standard.
3. Then you need to follow the eCAL installation [here](https://eclipse-ecal.github.io/ecal/stable/getting_started/setup.html#getting-started-setup).
4. Install the corresponding Ubuntu Python wheel from [here](https://github.com/eclipse-ecal/ecal/actions/runs/13562781226).
```bash 
sudo apt-get install eclipse_ecal[...].whl
```
5. Install Grafana in WSL like [here](https://grafana.com/docs/grafana/latest/setup-grafana/installation/debian/). 
Start Grafana Server: 
```bash
sudo systemctl start grafana-server
```
Login on http://localhost:3000/login using `admin` as username and password and import the dashboards from .../ecal-grafana-dashboard/grafana/. Add a new connection from SQLite (Probably also to install) and set `/mnt/c/Users/XXXXXXX/Documents/XXXXXX/ecal-grafana-dashboard/db/ecal_monitoring.db` as Path using your individual adjustments. 

6. Use a Virtual Environment like [here](https://python.land/virtual-environments/virtualenv)
7. Ensure that the required packages are installed. These can be found in the `requirements.txt` file. The requirements can be installed using the command:
```bash 
pip install -r requirements.txt
```
8. In ```monitor.py``` and ```monitoring_json.py``` adjust path to where DB is saved. Also create a DB folder. 

## Execution
To monitor the eCAL middleware using the eCAL-Dashboard, follow these steps:
### Windows

1. Start the MMAs on the system's hosts using the script ecal_mma.exe (Windows), which can be found in the `\bin` folder of a host's eCAL directory.
2. In root of the Repo start the Virtual Environment.
3. Execute the script with the following command:
```python 
python monitoring_json.py --interval 1
``` 
The `--interval` flag specifies the polling interval; in this case, the Monitoring API is polled every second. Appending `--verbose` will provide verbose output from the script. The monitoring script creates a database named `ecal_monitoring.db` in the `\db` directory, which is then accessed by the Grafana server.

### WSL
1. Start eCAL MMA
```bash 
/usr/bin/ecal_mma-5.13.3
```
2. In root of the Repo start the Virtual Environment
```bash
source .venv/bin/activate
```
3. Start Script
```bash
python3 monitoring_json.py --interval 1
```

## License 
Copyright (c) 2025 d-fine GmbH

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files 
(the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, 
publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished 
to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE 
WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT
HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, 
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.