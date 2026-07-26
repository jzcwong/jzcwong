## How to install and use cbctl for container image/K8S manifest scanning

The official documentation describing how to install and use cbctl is [here](https://docs.vmware.com/en/VMware-Carbon-Black-Cloud/services/carbon-black-cloud-user-guide/GUID-EF1FEFFE-AAEB-418E-80AC-CCE4494E99B7.html). This post aims to provide a more pictorial view of the installation process with some screenshots. 


**Step 1** - Login into CB cloud and create a CLI configuration
Go to Inventory > Kubernetes > K8S Clusters > CLI config . Give your CLI config a name that you can identify and a built step, for e.g. development

![cbctl1](cbctl1.png)

**Step 2** - Generate your API key and give it a name

![cbctl2](cbctl2.png)

![cbctl3](cbctl3.png)

**Step 3** - Create a ~/.cbctl on the host where cbctl is to be installed and paste the commands into a .cbctl.yaml file . Download the binary from the link given in the final step of installation and move it into your PATH

![cbctl4](cbctl4.png)

**Step 4** - Test to see if container image scanning works

![cbctl5](cbctl5.png)

