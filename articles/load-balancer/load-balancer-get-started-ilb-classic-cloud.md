<properties
   pageTitle="Create an internal load balancer for cloud services in the classic deployment model | Microsoft Azure"
   description="Learn how to create an internal load balancer using PowerShell in the classic deployment model"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/09/2016"
   ms.author="sewhee" />

# Get started creating an internal load balancer (classic) for cloud services

[AZURE.INCLUDE [load-balancer-get-started-ilb-classic-selectors-include.md](../../includes/load-balancer-get-started-ilb-classic-selectors-include.md)]

<BR>

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)] Learn how to [perform these steps using the Resource Manager model](load-balancer-get-started-ilb-arm-ps.md).


## Configure internal load balancer for cloud services

Internal load balancer is supported for both virtual machines and cloud services. An internal load balancer endpoint created in a cloud service that is outside a regional virtual network will be accessible only within the cloud service.

The internal load balancer configuration has to be set during the creation of the first deployment in the cloud service, as shown in the sample below.

>[AZURE.IMPORTANT] A prerequisite to run the steps below is to have a virtual network already created for the cloud deployment. You will need the virtual network name and subnet name to create the Internal Load Balancing.

### Step 1

Open the service configuration file (.cscfg) for your cloud deployment in Visual Studio and add the following section to create the Internal Load Balancing under the last "`</Role>`" item for the network configuration.




	<NetworkConfiguration>
	  <LoadBalancers>
	    <LoadBalancer name="name of the load balancer">
	      <FrontendIPConfiguration type="private" subnet="subnet-name" staticVirtualNetworkIPAddress="static-IP-address"/>
	    </LoadBalancer>
	  </LoadBalancers>
	</NetworkConfiguration>


Let's add the values for the network configuration file to show how it will look. In the example, assume you created a subnet called "test_vnet" with a subnet 10.0.0.0/24 called test_subnet and a static IP 10.0.0.4. The load balancer will be named testLB.

	<NetworkConfiguration>
	  <LoadBalancers>
	    <LoadBalancer name="testLB">
	      <FrontendIPConfiguration type="private" subnet="test_subnet" staticVirtualNetworkIPAddress="10.0.0.4"/>
	    </LoadBalancer>
	  </LoadBalancers>
	</NetworkConfiguration>

For more information about the load balancer schema, see [Add load balancer](https://msdn.microsoft.com/library/azure/dn722411.aspx).

### Step 2


Change the service definition (.csdef) file to add endpoints to the Internal Load Balancing. The moment a role instance is created, the service definition file will add the role instances to the Internal Load Balancing.


	<WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
	  <Endpoints>
	    <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" loadBalancer="load-balancer-name" />
	  </Endpoints>
	</WorkerRole>

Following the same values from the example above, let's add the values to the service definition file.

	<WorkerRole name=WorkerRole1" vmsize="A7" enableNativeCodeExecution="[true|false]">
	  <Endpoints>
	    <InputEndpoint name="endpoint1" protocol="http" localPort="80" port="80" loadBalancer="testLB" />
	  </Endpoints>
	</WorkerRole>

The network traffic will be load balanced using the testLB load balancer using port 80 for incoming requests, sending to worker role instances also on port 80.


## Next steps

[Configure a load balancer distribution mode using source IP affinity](load-balancer-distribution-mode.md)

[Configure idle TCP timeout settings for your load balancer](load-balancer-tcp-idle-timeout.md)