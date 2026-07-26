## Importing Antrea images with containerd

I have a vanilla K8S cluster v1.21.9 that is newly built and using Containerd and not Docker. CNI has not been installed yet and this post aims to describe some of the steps to import Antrea CNI image into the K8S nodes before installing them. Normally this is done via ["docker load"](https://docs.docker.com/engine/reference/commandline/load/) but since Docker is not used, the alternative using "ctr" command is explained here. After importing the image, the rest of the installation follows the documentation [here](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/3.2/administration/GUID-DFD8033B-22E2-4D7A-BD58-F68814ECDEB1.html)

**Installation and integration steps**

Download the below 2 files from Vmware, the first file is required to install basic Antrea CNI, the second file is to inter-operate with NSXT. As of time of writing in April 2022, the below files are the latest available. The second file is optional if you do not wish to integrate with NSXT, or if your cluster has access to VMware public Harbor registry to download the NSXT interworking component.


1. antrea-advanced-1.2.3+vmware.3.19009828.zip

2. antrea-interworking-0.2.0.zip



**After extracting the zip file for the image into a tar file**

Run the following command to import the image into the k8s.io namespace, you must import into k8s.io namespace for the image to be available for K8S deployment. This image import needs to be done on each K8S node in the cluster.

<pre><code>ctr -n=k8s.io images import antrea-advanced-debian-v1.2.3_vmware.3.tar
</code></pre>



**Verify** 

<pre><code>root@k8s-cl1-master:~/antrea/antrea-advanced-1.2.3+vmware.3.19009828/manifests# ctr -n=k8s.io images list
REF                                                                                                        TYPE                                                      DIGEST                                                                  SIZE      PLATFORMS                                                                    LABELS                        
docker.io/antrea/antrea-advanced-debian:v1.2.3_vmware.3                                                    application/vnd.docker.distribution.manifest.v2+json      sha256:79cb020bc20e663f6749982e5302e18287a24a7437d6b169c02a160a80260dd7 592.7 MiB linux/amd64                                                                  io.cri-containerd.image=managed
</code></pre>


After the steps above you can continue with the Antrea installation by directly applying the manifest file that came bundled in the zip file. Once done Antrea CNI is installed.

![antreainstall](antreainstall.png)

You can verify as below


![antreaverify](antreaverify.png)
