# Anthos on Nutanix



## 參考資料

Anthos on AHV
https://www.nutanix.dev/2021/04/26/anthos-clusters-on-ahv-getting-started/#calm-anthos-ahv

Calm Blueprint
https://github.com/nutanixdev/anthos-on-ahv/tree/main/calm

Service Account
https://cloud.google.com/anthos/run/docs/securing/service-accounts



## 架構

![img](assets/Anthos-clusters-on-AHV-Architecture-1024x733.png)

![image-20230308145723976](assets/Anthos_Provisioning.png)

![image-20230308152356698](assets/Anthos_Price.png)

## 資訊

1. NX3500PE Cluster , Block SN: 13SMxxxx

| 項目                          | SN                  | IPMI MAC          | IPMI IP         | CVM IP       | AHV IP             |
| ----------------------------- | ------------------- | ----------------- | --------------- | ------------ | ------------------ |
| NodeA-AHV1                    | ZM137Sxxxxx7        | 00:25:90:d3:c8:23 | 10.0.90.2       | 172.16.90.61 | 172.16.90.51       |
| NodeB-AHV2                    | ZM139Sxxxxxx        | 00:25:90:d8:74:17 | 10.0.90.3       | 172.16.90.62 | 172.16.90.52       |
| NodeC-AHV3                    | ZM137xxxxxx5        | 00:25:90:d3:c8:20 | 10.0.90.4       | 172.16.90.63 | 172.16.90.53       |
| NodeD-AHV4                    | ZM13xxxxx604        | 00:25:90:d3:c7:f4 | 10.0.90.5       | 172.16.90.64 | 172.16.90.54       |
| PE Cluster                    | NX3500PE            | 172.16.90.71      | admin           |              |                    |
| PC Cluster                    | NX3500PC            | 172.16.90.72      | admin           |              |                    |
| **SSD、HDD per node**         | **Memory per node** | **IPMI Account**  | **IPMI Passwd** | **Account**  | **CVM,AHV Passwd** |
| 400GB SSD *2 <br />1TB HDD *4 | 192GB(16GB *12)     | ADMIN             |                 | nutanix      |                    |

2. VM

| 名稱                   | IP            | 帳密 |
| ---------------------- | ------------- | ---- |
| ken-anthos-workstation | 172.16.90.206 |      |
| ken-anthos-master1     | 172.16.90.207 |      |
| ken-anthos-master2     | 172.16.90.208 |      |
| ken-anthos-master3     | 172.16.90.209 |      |
| ken-anthos-worker1     | 172.16.90.210 |      |
| ken-anthos-worker2     | 172.16.90.211 |      |
| Anthos cluster VIP     | 172.16.90.212 |      |
| Ingress VIP            | 172.16.90.213 |      |
| Loadbalncer IP         | 172.16.90.214 |      |
| Loadbalncer IP         | 172.16.90.215 |      |



## 安裝

### 事前準備

```
Prerequisites

Before using any of the automation methods, make sure to meet the following requirements:
Automation	

    Calm:
        3.0.0.2 or later
        A project with AHV account

    ~ or ~
    Terraform:
        0.13.x or later
        Nutanix provider 1.2.x or later

Credentials	

    (Calm only) SSH key. It must start with —BEGIN RSA PRIVATE KEY—
    Prism Element account with User Admin role
    Prism Central account with CRUD VM permissions

Networking	

    Internet connectivity
    AHV IPAM pool with minimum 6 IP addresses
    Kubernetes:
        Control plane VIP
        Ingress VIP
        Load balancing pool

Nutanix	

    Prism Element cluster:
        AHV: 20201105.1045 or later
        AOS: 5.19.1 or later
        iSCSI data service IP configured
        VLAN network with AHV IPAM configured
    Prism Central: 2020.11.0.1 or later

Google Cloud	

    A project with Owner role
    Project must have monitoring enabled (console)
    A service account (how-to)
        Role: Project Owner
        A private key: JSON format
```

1. google Anthos帳戶
   ![image-20230314102840587](assets/anthos-free plan.png)
2. Nutanix Prism Central enable Calm

![image-20230314102940274](assets/anthos_Clam.png)

### Anthos

#### Create Service Accounts

1. 進入IAM與管理,點選project (Anthos-Test-202303)
   ![image-20230314115438141](assets/anthos-sa01.png)

2. 點選建立服務帳戶
   ![image-20230314115626110](assets/anthos-sa02.png)

3. 給擁有者的權限

   ![image-20230314115729988](assets/anthos-sa03.png)

4. 郵件地址複製
   netfos@anthos-test-202303.iam.gserviceaccount.com

5. 點選該服務帳戶,新增金鑰
   ![image-20230314115902286](assets/anthos-sa04.png)

6. 儲存金鑰
   ![image-20230314120003591](assets/anthos-sa05.png)
   ![image-20230314120124523](assets/anthos-sa06.png)

#### Create Anthos Bare metal

1. 啟用Anthos
   ![image-20230314110332738](assets/anthos-active01.png)
   
   

### Prism Calm

1. IPAM設定IP Pool

   ![image-20230314111246357](assets/anthos-IPAM.png)

2. Calm建立Project
   ![image-20230314113139889](assets/anthos-project01.png)

   ![image-20230314113258956](assets/anthos-project02.png)

   ![image-20230314155154308](assets/anthos-project03.png)

3. 上傳github 上的 blueprint
   ![image-20230314113458998](assets/anthos-blueprint.png)
   ![image-20230314113547773](assets/anthos-blueprint02.png)

4. 調整參數可以安裝時再輸入即可 (有藍色的人代表安裝時可以輸入)

5. VM網卡要先設定好
   ![image-20230314160312365](assets/anthos-bp-network.png)

6. Credential 要先輸入
   ![image-20230314152343047](assets/anthos-cred.png)

7. CRED_OS ssh-keygen

   ```
   ssh-keygen -t rsa -f ~/.ssh/KEY_FILENAME -C USERNAME -b 2048
   
   wangken@wangken-MAC ~ % ssh-keygen -t rsa -f ~/.ssh/anthos_key -C nutanix -b 2048
   Generating public/private rsa key pair.
   Enter passphrase (empty for no passphrase): 
   Enter same passphrase again: 
   Your identification has been saved in /Users/wangken/.ssh/anthos_key
   Your public key has been saved in /Users/wangken/.ssh/anthos_key.pub
   The key fingerprint is:
   SHA256:vBzHezjT7zfmG5ZWQX+eS78TLDHmfNj7g1Rki4PxV5Q nutanix
   The key's randomart image is:
   +---[RSA 2048]----+
   |               oo|
   |           .  .Eo|
   |            + +.=|
   |       . . . B =+|
   |        S o + Xoo|
   |       . + + *.==|
   |        o = + +*+|
   |           + oo*+|
   |             .==B|
   +----[SHA256]-----+
   
   wangken@wangken-MAC .ssh % cat anthos_key.pub 
   ssh-rsa xxxx nutanix
   
   wangken@wangken-MAC .ssh % cat anthos_key
   -----BEGIN OPENSSH PRIVATE KEY-----
   xxxxx
   -----END OPENSSH PRIVATE KEY-----
   ```
   
8. anthos sa private key

   ```
   wangken@wangken-MAC notes % cat anthos-test-202303-privatekey.json 
   {
     "type": "service_account",
     "project_id": "anthos-test-202303",
     "private_key_id": "xxxx38c363",
     "private_key": "-----BEGIN PRIVATE KEY-----xxxx=\n-----END PRIVATE KEY-----\n",
     "client_email": "netfos@anthos-test-202303.iam.gserviceaccount.com",
     "client_id": "110466507071833816563",
     "auth_uri": "https://accounts.google.com/o/oauth2/auth",
     "token_uri": "https://oauth2.googleapis.com/token",
     "auth_provider_x509_cert_url": "https://www.googleapis.com/oauth2/v1/certs",
     "client_x509_cert_url": "https://www.googleapis.com/robot/v1/metadata/x509/netfos%40anthos-test-202303.iam.gserviceaccount.com"
   }
   ```

9. 輸入完後按save , 執行
   ![image-20230314153857881](assets/anthos-launch.png)

10. 填入資訊
    ![image-20230314154058030](assets/anthos-lauch01.png)

11. IP 資訊
    ![image-20230314154540212](assets/anthos-lauch02.png)

12. IP 資訊
    ![image-20230314154644473](assets/anthos-lauch03.png)

13. Deploy
    ![image-20230314160527582](assets/anthos-deploy.png)

14. 自動化部屬
    ![image-20230314160559318](assets/anthos-deploy05.png)

15. Manage可以查看目前的部署狀態
    ![image-20230314160801127](assets/anthos-deploy06.png)

16. 部署完成
    ![image-20230314173530825](assets/anthos-vm.png)

17. 登入Admin VM 取得Secret並在Anthos Console登入

    ```
    wangken@wangken-MAC ~ % ssh -i .ssh/anthos_key nutanix@172.16.90.206
    Activate the web console with: systemctl enable --now cockpit.socket
    
    Last login: Tue Mar 14 09:21:38 2023 from 172.16.90.72
    
    [nutanix@anthos-netfos1-anthos-adminVm-0 ~]$ kubectl get nodes
    NAME                                STATUS   ROLES                  AGE   VERSION
    anthos-netfos1-anthos-controlvm-0   Ready    control-plane,master   57m   v1.21.5-gke.1200
    anthos-netfos1-anthos-controlvm-1   Ready    control-plane,master   53m   v1.21.5-gke.1200
    anthos-netfos1-anthos-controlvm-2   Ready    control-plane,master   53m   v1.21.5-gke.1200
    anthos-netfos1-anthos-workervm-0    Ready    <none>                 50m   v1.21.5-gke.1200
    anthos-netfos1-anthos-workervm-1    Ready    <none>                 50m   v1.21.5-gke.1200
    ```

    ```
    [nutanix@anthos-netfos1-anthos-adminVm-0 ~]$ 
    $SECRET_NAME=$(kubectl get serviceaccount google-cloud-console -o jsonpath='{$.secrets[0].name}')
    $kubectl get secret ${SECRET_NAME} -o jsonpath='{$.data.token}' | base64 --decode
    
    xxxxxx4QxxxxtfWkP-UnyRm2xSYhSGt_eonjWyO6OCh-idoyaDgLNUqTH-Zus28vTW_D_aFjAdyqBRnA2zj223K5zYsqCb7euA6IyWnXolLn5z3cpN1pnF9oATXP895zdBjCC2gZztbZof_CuX8YV64kRWuCqxm5f-PbkZ2Tz1GISkpRg
    
    [nutanix@anthos-netfos1-anthos-adminVm-0 anthos-netfos1]$ pwd
    /home/nutanix/baremetal/bmctl-workspace/anthos-netfos1
    [nutanix@anthos-netfos1-anthos-adminVm-0 anthos-netfos1]$ cat anthos-netfos1-kubeconfig 
    apiVersion: v1
    clusters:
    - cluster:
        certificate-authority-data: xxxxxZEcvZkJmZVlPcUNtL1BVVTA5YklIOVhXY3NrQQpaOE1Ldlc3N3d2TS9DSVBrUldrekNPZ3RkVGpJUWp2RVpSV2x0aDdWSjBRMTFDZDdCK3JpWjJLbDBvblNGRWpoCjgwaEtJK1cxMDQ5ZUxDam16dWd2MVlEQXE2UnhFT0xFbW1NTUZlZXcKLS0tLS1FTkQgQ0VSVElGSUNBVEUtLS0tLQo=
        server: https://172.16.90.212:443
      name: anthos-netfos1
    contexts:
    - context:
        cluster: anthos-netfos1
        user: anthos-netfos1-admin
      name: anthos-netfos1-admin@anthos-netfos1
    current-context: anthos-netfos1-admin@anthos-netfos1
    kind: Config
    preferences: {}
    users:
    - name: anthos-netfos1-admin
      user:
        client-certificate-data: xxxnJQZVFJTWJVSGFqOHo0L2FtSC9aeVV0NUNsCk9ydy8wQjRROFpIVkVETWRuZUUvSjFlUm9maHpwK2RHcXhaNVdyYmQzVUpGN0Jxbnd5Wm9JQVNZMDhoazBGM2EKL1BHWVNmMm02Q3VSS2FVSmlFZGNkby9Yd0xZV1ZDZz0KLS0tLS1FTkQgQ0VSVElGSUNBVEUtLS0tLQo=
        client-key-data: LxxxdtTnlJCnJialpBb0dCQU0wdERPeVVxYmtGLyt2YzZVVktwUno1MlArekNubDM3ZTBDQzdCbWsvK3NYemd1Y3podDg5OW8KbHJ4Y3g1UlIvSS9NbzhYWWYrbnJJRndXOEVIMnFyMVZyQzU2NVozcEJIbkJheWJpdzdzeS9TRjhaU05SR2F4UgpZbnhKUU9KM09HZEVNelVSODFKOWpVNDVLRWI4K0g3di9qMUFQaG8ySWVlY21qWkR4bzJuCi0tLS0tRU5EIFJTQSBQUklWQVRFIEtFWS0tLS0tCg==
    
    ```

    

18. 登入完成
    ![image-20230314174734950](assets/anthos-login.png)

    ![image-20230314175007215](assets/anthos-GKE.png)

19. Workload
    ![image-20230314175203478](assets/anthos-workload.png)

20. Marketplace部署測試程式
    ![image-20230314175624682](assets/anthos-marketplace01.png)

21. 設定並部署
    ![image-20230314175712418](assets/anthos-marketplace02.png)

    ![image-20230314180133597](assets/anthos-marketplace03.png)

22. 在地端查看namespace狀態

    ```
    [nutanix@anthos-netfos1-anthos-adminVm-0 ~]$ kubectl get all -n harbor
    NAME                                          READY   STATUS             RESTARTS   AGE
    pod/harbor-1-chartmuseum-967bb456b-2vmjk      1/1     Running            0          3m18s
    pod/harbor-1-core-7956bd9f54-t4jcw            1/1     Running            1          3m18s
    pod/harbor-1-database-0                       1/1     Running            0          3m18s
    pod/harbor-1-deployer-rq8m5                   0/1     Completed          0          3m59s
    pod/harbor-1-exporter-5c86cc7b6d-s78kb        1/2     CrashLoopBackOff   5          3m18s
    pod/harbor-1-jobservice-9898c468b-5bhzp       1/1     Running            3          3m18s
    pod/harbor-1-nginx-66d4ccd87-7p7q2            1/1     Running            0          3m18s
    pod/harbor-1-notary-server-7d7b4b598d-85nm5   1/1     Running            2          3m18s
    pod/harbor-1-notary-signer-5d59d79f4-4tkn4    1/1     Running            2          3m18s
    pod/harbor-1-portal-66f4b7478b-5xv5b          1/1     Running            0          3m18s
    pod/harbor-1-redis-0                          1/1     Running            0          3m18s
    pod/harbor-1-registry-67bc689779-bvw8m        2/2     Running            0          3m18s
    pod/harbor-1-trivy-0                          1/1     Running            0          3m18s
    
    NAME                             TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)                      AGE
    service/harbor                   ClusterIP   172.31.202.245   <none>        80/TCP,443/TCP,4443/TCP      3m19s
    service/harbor-1-chartmuseum     ClusterIP   172.31.6.127     <none>        80/TCP                       3m19s
    service/harbor-1-core            ClusterIP   172.31.17.10     <none>        80/TCP,8001/TCP              3m19s
    service/harbor-1-database        ClusterIP   172.31.204.175   <none>        5432/TCP                     3m19s
    service/harbor-1-exporter        ClusterIP   172.31.107.19    <none>        8001/TCP                     3m19s
    service/harbor-1-jobservice      ClusterIP   172.31.128.4     <none>        80/TCP,8001/TCP              3m19s
    service/harbor-1-notary-server   ClusterIP   172.31.240.223   <none>        4443/TCP                     3m19s
    service/harbor-1-notary-signer   ClusterIP   172.31.221.6     <none>        7899/TCP                     3m19s
    service/harbor-1-portal          ClusterIP   172.31.38.67     <none>        80/TCP                       3m19s
    service/harbor-1-redis           ClusterIP   172.31.38.202    <none>        6379/TCP                     3m19s
    service/harbor-1-registry        ClusterIP   172.31.16.8      <none>        5000/TCP,8080/TCP,8001/TCP   3m18s
    service/harbor-1-trivy           ClusterIP   172.31.70.71     <none>        8080/TCP                     3m18s
    
    NAME                                     READY   UP-TO-DATE   AVAILABLE   AGE
    deployment.apps/harbor-1-chartmuseum     1/1     1            1           3m18s
    deployment.apps/harbor-1-core            1/1     1            1           3m18s
    deployment.apps/harbor-1-exporter        0/1     1            0           3m18s
    deployment.apps/harbor-1-jobservice      1/1     1            1           3m18s
    deployment.apps/harbor-1-nginx           1/1     1            1           3m18s
    deployment.apps/harbor-1-notary-server   1/1     1            1           3m18s
    deployment.apps/harbor-1-notary-signer   1/1     1            1           3m18s
    deployment.apps/harbor-1-portal          1/1     1            1           3m18s
    deployment.apps/harbor-1-registry        1/1     1            1           3m18s
    
    NAME                                                DESIRED   CURRENT   READY   AGE
    replicaset.apps/harbor-1-chartmuseum-967bb456b      1         1         1       3m18s
    replicaset.apps/harbor-1-core-7956bd9f54            1         1         1       3m18s
    replicaset.apps/harbor-1-exporter-5c86cc7b6d        1         1         0       3m18s
    replicaset.apps/harbor-1-jobservice-9898c468b       1         1         1       3m18s
    replicaset.apps/harbor-1-nginx-66d4ccd87            1         1         1       3m18s
    replicaset.apps/harbor-1-notary-server-7d7b4b598d   1         1         1       3m18s
    replicaset.apps/harbor-1-notary-signer-5d59d79f4    1         1         1       3m18s
    replicaset.apps/harbor-1-portal-66f4b7478b          1         1         1       3m18s
    replicaset.apps/harbor-1-registry-67bc689779        1         1         1       3m18s
    
    NAME                                 READY   AGE
    statefulset.apps/harbor-1-database   1/1     3m18s
    statefulset.apps/harbor-1-redis      1/1     3m18s
    statefulset.apps/harbor-1-trivy      1/1     3m18s
    
    NAME                          COMPLETIONS   DURATION   AGE
    job.batch/harbor-1-deployer   1/1           44s        3m59s
    ```

    

23. 測試redmine

    ```
    [nutanix@anthos-netfos1-anthos-adminVm-0 redmine]$ kubectl get all -n redmine
    NAME                                               READY   STATUS    RESTARTS   AGE
    pod/redmine-58cfd55655-qthfz                       1/1     Running   0          38m
    pod/redmine-postgres-deployment-6b8f85c97b-72vkl   1/1     Running   0          106m
    
    NAME                               TYPE        CLUSTER-IP       EXTERNAL-IP     PORT(S)          AGE
    service/redmine-postgres-service   NodePort    172.31.163.134   <none>          5432:31432/TCP   106m
    service/redmine-service            ClusterIP   172.31.151.36    172.16.90.213   80/TCP           38m
    
    NAME                                          READY   UP-TO-DATE   AVAILABLE   AGE
    deployment.apps/redmine                       1/1     1            1           38m
    deployment.apps/redmine-postgres-deployment   1/1     1            1           106m
    
    NAME                                                     DESIRED   CURRENT   READY   AGE
    replicaset.apps/redmine-58cfd55655                       1         1         1       38m
    replicaset.apps/redmine-postgres-deployment-6b8f85c97b   1         1         1       106m
    ```

24. IngressVIP : 172.16.90.213
    ![image-20230320170954778](assets/anthos-redmine.png)

25. anthos畫面
    ![image-20230320171120742](assets/anthos-redmine2.png)

    ![image-20230320171223253](assets/anthos-redmine3.png)

    StorageClass --> default block storage

    ![image-20230320171344271](assets/anthos-redmine4.png)

    
