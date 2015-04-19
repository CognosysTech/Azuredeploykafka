# Install a Kafka cluster on Ubuntu Virtual Machines using Custom Script Linux Extension

<a href="https://azuredeploy.net/" target="_blank">
    <img src="http://azuredeploy.net/deploybutton.png"/>
</a>

This template deploys a Kafka cluster on the Ubuntu virtual machines. This template also provisions a storage account, virtual network, availability sets, public IP addresses and network interfaces required by the installation.
The template also creates 1 publicly accessible VM acting as a "zookeeper" and allowing to ssh into the Kafka nodes for diagnostics or troubleshooting purposes.

The example expects the following parameters:

| Name   | Description    |
|:--- |:---|
| storageAccountName  | Unique DNS Name for the Storage Account where the Virtual Machine's disks will be placed |
| adminUsername  | Admin user name for the Virtual Machines  |
| adminPassword  | Admin password for the Virtual Machine  |
| region | Region name where the corresponding Azure artifacts will be created |
| virtualNetworkName | Name of Virtual Network |
| dataDiskSize | Size of each disk attached to Kafka nodes (in GB) |
| subnetName | Name of the Virtual Network subnet |
| addressPrefix | The IP address mask used by the Virtual Network |
| subnetPrefix | The subnet mask used by the Virtual Network subnet |
| kafkaVersion | Kafka version number to be installed |
| kafkaClusterName | Name of the Kafka cluster |
| kafkaNodeIPAddressPrefix | The IP address prefix that will be used for constructing a static private IP address for each node in the cluster |
| zookeeper | The flag allowing to enable or disable provisioning of the zookeeper VM that can be used to access the Kafka nodes |
| tshirtSize | The t-shirt size of the Kafka node (small, medium, large) |

Topology
--------

The deployment topology is comprised of _numberOfInstances_ nodes running in the cluster mode.
The AOF persistence is enabled by default, whereas the RDB persistence is tuned to perform less-frequent dumps (once every 60 minutes).
In addition, some critical memory- and network-specific optimizations are applied to ensure the optimal performance and throughput.

NOTE: To access the individual Kafka nodes, you need to use the publicly accessible zookeeper VM and ssh from it into the VM instances running Kafka.

##Known Issues and Limitations
- The deployment script is not yet idempotent and cannot handle updates (it currently works for initial cluster provisioning only)
- Health monitoring of the Kafka instances is not currently enabled
- SSH key is not yet implemented and the template currently takes a password for the admin user
- Kafka cluster is not enabled automatically (due to inability to compose a single list of private IP addresses of all instances from within the ARM template)
- Kafka version 0.8.2.1 or above is a requirement for the cluster (although the older versions can still be deployed without clustered configuration)
- A static IP address will be assigned to each Kafka node in order to work around the current limitation of not being able to dynamically compose a list of IP addresses from within the template (by default, the first node will be assigned the private IP of 10.0.0.10, the second node - 10.0.0.11, and so on)