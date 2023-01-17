# Install vSphere

https://portal.nutanix.com/page/documents/details?targetId=vSphere-Admin6-AOS-v6_5:vsp-cluster-introduction-vsphere-c.html

## Foundation

1. 準備vSphere ESXi、vCenter的iso檔
2. 利用mac的foundation安裝nutanix
3. 安裝時改用vSphere的iso檔
4. 如不是使用官方Support的版本,要提供md5 check sum
5. 目前支援到vSphere ESXi 7.0u3c , 實測7.0u3g也可以安裝
6. 安裝時間約50~60min

## 安裝後的設定

1. 設定DNS Server

2. 設定NTP Server

3. 刪除Default Container,再新增一個 (因名稱太長)

4. Deploy vCenter or Registry to vCenter
   ![image-20230117115309394](assets/image-20230117115309394.png)

   ![image-20230117115414091](assets/image-20230117115414091.png)

   ![image-20230117115559389](assets/image-20230117115559389.png)

   ![image-20230117120202663](assets/image-20230117120202663.png)

   ![image-20230117120702169](assets/image-20230117120702169.png)

   ![image-20230117121452623](assets/image-20230117121452623.png)

   ![image-20230117121557920](assets/image-20230117121557920.png)
   

5. vCenter Setting

   - 先在vCenter Create Datacenter並新增Nutanix Cluster
     Cluster **開啟DRS、HA**

   ![image-20230117122218272](assets/image-20230117122218272.png)

   ![image-20230117131129201](assets/image-20230117131129201.png)

6. **確認Checklist都有設定完畢才能加入Nutanix Node**
   HA

   - Enable Host Monitoring

   - Set the Host Isolation Response of the cluster to **Power Off & Restart VMs**.
     ![image-20230117143553864](assets/image-20230117143553864.png)

   - Enable admission control (%)

     ![image-20230117142443552](assets/image-20230117142443552.png)

   - Set the VM Restart Priority of all CVMs to **Disabled**. -- After register
     ![image-20230117142551006](assets/image-20230117142551006.png)

     ![image-20230117142719050](assets/image-20230117142719050.png)

   - Set the VM Monitoring for all CVMs to **Disabled**. -- After register

     ![image-20230117142810049](assets/image-20230117142810049.png)

   - Datastore heartbeats -- After register 

     (只有一個Datastore時,增加此設定)
     ![image-20230117134844420](assets/image-20230117134844420.png)

     ![image-20230117142910096](assets/image-20230117142910096.png)

   DRS

   - Set the Automation Level on all CVMs to **Disabled**. --After register
     ![image-20230117142728280](assets/image-20230117142728280.png)

   - Select Automation Level to accept level 3 recommendations.

     ![image-20230117135654108](assets/image-20230117135654108.png)

   - Leave power management **disabled**.
     ![image-20230117135332448](assets/image-20230117135332448.png)

   Others

   - Configure advertised capacity for the Nutanix storage container (RF2)
     ![image-20230117140918527](assets/image-20230117140918527.png) 

   - Store VM swapfiles in the same directory as the VM.

     ![image-20230117143410180](assets/image-20230117143410180.png)
     https://docs.vmware.com/en/VMware-vSphere/7.0/com.vmware.vsphere.resmgmt.doc/GUID-12B8E0FB-CD43-4972-AC2C-4B4E2955A5DA.html

   - **Enable** enhanced vMotion compatibility (EVC) in the cluster.
     ![image-20230117140459645](assets/image-20230117140459645.png)

   - Configure Nutanix CVMs with the appropriate VM overrides. --After register
     (HA、DRS、Monitoring **Disabled**)

7. vCenter Adding Nutanix nodes

   ![image-20230117141404460](assets/image-20230117141404460.png)

   ![image-20230117141430544](assets/image-20230117141430544.png)

   ![image-20230117141503268](assets/image-20230117141503268.png)

   ![image-20230117141525157](assets/image-20230117141525157.png)

   實測第一台Node會被順利加到Cluster內,其他兩台需手動移入
   ![image-20230117142411214](assets/image-20230117142411214.png)

8. 補齊Checklist的設定

9. Register Nutanix Cluster to vCenter
   ![image-20230117143835885](assets/image-20230117143835885.png)

   ![image-20230117143908067](assets/image-20230117143908067.png)

   ![image-20230117144024141](assets/image-20230117144024141.png)



## 安裝後的設定-PC

https://portal.nutanix.com/page/documents/details?targetId=Acropolis-Upgrade-Guide:upg-vm-install-wc-r.html

1. create PC
   <img src="assets/image-20230117144856189.png" alt="image-20230117144856189" style="zoom:50%;" />

   <img src="assets/image-20230117144919733.png" alt="image-20230117144919733" style="zoom:50%;" />

   <img src="assets/image-20230117145119576.png" alt="image-20230117145119576" style="zoom:50%;" />
   

2. 123

3. 123

4. 123



# vSphere Settings Checklist

https://portal.nutanix.com/page/documents/details?targetId=vSphere-Admin6-AOS-v6_5:vsp-vcenter-settings-r.html

Review the following checklist of the settings that you have to configure to successfully deploy vSphere virtual environment running Nutanix Enterprise cloud.

## vSphere Availability Settings

- **Enable** host monitoring.

- **Enable** admission control and use the percentage-based policy with a value based on the number of nodes in the cluster.

  For more information about settings of percentage of cluster resources reserved as failover spare capacity, [vSphere HA Admission Control Settings for Nutanix Environment](https://portal.nutanix.com/page/documents/details?targetId=vSphere-Admin6-AOS-v6_5:vsp-cluster-settings-admissioncontrol-vcenter-vsphere-r.html).

- Set the VM Restart Priority of all CVMs to **Disabled**.

- Set the Host Isolation Response of the cluster to **Power Off & Restart VMs**.

- Set the VM Monitoring for all CVMs to **Disabled**.

- Enable datastore heartbeats by clicking **Use datastores only from the specified list** and choosing the Nutanix NFS datastore.

  If the cluster has only one datastore, click **Advanced Options** tab and add das.ignoreInsufficientHbDatastore with **Value** of true.

## vSphere DRS Settings

- Set the Automation Level on all CVMs to **Disabled**.
- Select Automation Level to accept level 3 recommendations.
- Leave power management **disabled**.

## Other Cluster Settings

- Configure advertised capacity for the Nutanix storage container 
  (total usable capacity minus the capacity of one node for replication factor 2 or two nodes for replication factor 3).
- Store VM swapfiles in the same directory as the VM.
- **Enable** enhanced vMotion compatibility (EVC) in the cluster. For more information, see [vSphere EVC Settings](https://portal.nutanix.com/page/documents/details?targetId=vSphere-Admin6-AOS-v6_5:vsp-cluster-settings-evc-vcenter-vsphere-t.html).
- Configure Nutanix CVMs with the appropriate VM overrides. For more information, see [VM Override Settings](https://portal.nutanix.com/page/documents/details?targetId=vSphere-Admin6-AOS-v6_5:vsp-cluster-settings-override-vcenter-vsphere-t.html).
- Check [Nonconfigurable ESXi Components](https://portal.nutanix.com/page/documents/details?targetId=vSphere-Admin6-AOS-v6_5:vsp-node-components-unconfigurable-vsphere-r.html). Modifying the nonconfigurable components may inadvertently constrain performance of your Nutanix cluster or render the Nutanix cluster inoperable.
