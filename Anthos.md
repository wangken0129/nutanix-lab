# Anthos on Nutanix



## 參考資料

Anthos on AHV
https://www.nutanix.dev/2021/04/26/anthos-clusters-on-ahv-getting-started/#calm-anthos-ahv

Calm Blueprint
https://github.com/nutanixdev/anthos-on-ahv/tree/main/calm

Service Account
https://cloud.google.com/anthos/run/docs/securing/service-accounts



## 架構

![img](../../../Documents/GitHub/nutanix-lab/assets/Anthos-clusters-on-AHV-Architecture-1024x733.png)

![image-20230308145723976](../../../Documents/GitHub/nutanix-lab/assets/Anthos_Provisioning.png)

![image-20230308152356698](../../../Documents/GitHub/nutanix-lab/assets/Anthos_Price.png)

## 資訊

1. NX3500PE Cluster , Block SN: 13SM35330024

| 項目                          | SN                  | IPMI MAC          | IPMI IP         | CVM IP         | AHV IP             |
| ----------------------------- | ------------------- | ----------------- | --------------- | -------------- | ------------------ |
| NodeA-AHV1                    | ZM137S025587        | 00:25:90:d3:c8:23 | 10.0.90.2       | 172.16.90.61   | 172.16.90.51       |
| NodeB-AHV2                    | ZM139S125484        | 00:25:90:d8:74:17 | 10.0.90.3       | 172.16.90.62   | 172.16.90.52       |
| NodeC-AHV3                    | ZM137S025585        | 00:25:90:d3:c8:20 | 10.0.90.4       | 172.16.90.63   | 172.16.90.53       |
| NodeD-AHV4                    | ZM137S025604        | 00:25:90:d3:c7:f4 | 10.0.90.5       | 172.16.90.64   | 172.16.90.54       |
| PE Cluster                    | NX3500PE            | 172.16.90.71      | admin           | Nutanix/Lab123 |                    |
| PC Cluster                    | NX3500PC            | 172.16.90.72      | admin           | Nutanix/Lab123 |                    |
| **SSD、HDD per node**         | **Memory per node** | **IPMI Account**  | **IPMI Passwd** | **Account**    | **CVM,AHV Passwd** |
| 400GB SSD *2 <br />1TB HDD *4 | 192GB(16GB *12)     | ADMIN             | P@ssw0rdNETFOS  | nutanix        | P@ssw0rdNETFOS     |

2. 

| 項目              | 帳號                    | 密碼        |
| ----------------- | ----------------------- | ----------- |
| google            | nutanix@diyatech.com.tw | Nutanix@123 |
| google            | kenkennyinfo@gmail.com  |             |
| google anthos ssh |                         |             |

3. VM

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
   ![image-20230314102840587](../../../Documents/GitHub/nutanix-lab/assets/anthos-free plan.png)
2. Nutanix Prism Central enable Calm

![image-20230314102940274](../../../Documents/GitHub/nutanix-lab/assets/anthos_Clam.png)

### Anthos

#### Create Service Accounts

1. 進入IAM與管理,點選project (Anthos-Test-202303)
   ![image-20230314115438141](../../../Documents/GitHub/nutanix-lab/assets/anthos-sa01.png)

2. 點選建立服務帳戶
   ![image-20230314115626110](../../../Documents/GitHub/nutanix-lab/assets/anthos-sa02.png)

3. 給擁有者的權限

   ![image-20230314115729988](../../../Documents/GitHub/nutanix-lab/assets/anthos-sa03.png)

4. 郵件地址複製
   netfos@anthos-test-202303.iam.gserviceaccount.com

5. 點選該服務帳戶,新增金鑰
   ![image-20230314115902286](../../../Documents/GitHub/nutanix-lab/assets/anthos-sa04.png)

6. 儲存金鑰
   ![image-20230314120003591](../../../Documents/GitHub/nutanix-lab/assets/anthos-sa05.png)
   ![image-20230314120124523](../../../Documents/GitHub/nutanix-lab/assets/anthos-sa06.png)

#### Create Admin Cluster



#### Create Anthos Bare metal

1. 啟用Anthos
   ![image-20230314110332738](../../../Documents/GitHub/nutanix-lab/assets/anthos-active01.png)
2. 選擇地端部署- 裸機
   ![image-20230314110725402](../../../Documents/GitHub/nutanix-lab/assets/anthos-deploy01.png)
3. 建立
   ![image-20230314111542352](../../../Documents/GitHub/nutanix-lab/assets/anthos-deploy02.png)
   ![image-20230314111642562](../../../Documents/GitHub/nutanix-lab/assets/anthos-deploy03.png)
4. Create Admin Cluster
   ![image-20230314111747857](../../../Documents/GitHub/nutanix-lab/assets/anthos-deploy04.png)
5. 123
6. 123
7. 123
8. 123
9. 123
10. 13

### Prism Calm

1. IPAM設定IP Pool

   ![image-20230314111246357](../../../Documents/GitHub/nutanix-lab/assets/anthos-IPAM.png)

2. Calm建立Project
   ![image-20230314113139889](../../../Documents/GitHub/nutanix-lab/assets/anthos-project01.png)

   ![image-20230314113258956](../../../Documents/GitHub/nutanix-lab/assets/anthos-project02.png)

   ![image-20230314155154308](../../../Documents/GitHub/nutanix-lab/assets/anthos-project03.png)

3. 上傳github 上的 blueprint
   ![image-20230314113458998](../../../Documents/GitHub/nutanix-lab/assets/anthos-blueprint.png)
   ![image-20230314113547773](../../../Documents/GitHub/nutanix-lab/assets/anthos-blueprint02.png)

4. 調整參數可以安裝時再輸入即可 (有藍色的人代表安裝時可以輸入)

5. VM網卡要先設定好
   ![image-20230314160312365](../../../Documents/GitHub/nutanix-lab/assets/anthos-bp-network.png)

6. Credential 要先輸入
   ![image-20230314152343047](../../../Documents/GitHub/nutanix-lab/assets/anthos-cred.png)

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
   ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQC5NQ8IIRrSWwGrjRCJX7NgIA3qDMRX1eXshYkXq9rQZ5Q5mek328roC1jcSX0SVKxJLBK274wXFds+n2+aF88TUoD1aL+agNBI9sBX4mN9h6+4BmZvG8AXulGgm8gBXMDb4wQ//P6wHiHgLNSmLreCqh6P6b7bRnFtRHfteDE2uCHY7moTRGZ4sVA6mjKh9AwkXsP5fQj2wEip/t0z2fpRHJ5cEK7wwXNuHihE9L7Dd9FDowgSEKdzin/w5ZNcPXoejO6BGZrsXRkW/11RF7hKNOXRJybsZeq1icmy6n0irWMcf5rQIz+Sr400n9B0t3KluYKGFNrELp3dRCtx9yjH nutanix
   
   wangken@wangken-MAC .ssh % cat anthos_key
   -----BEGIN OPENSSH PRIVATE KEY-----
   b3BlbnNzaC1rZXktdjEAAAAABG5vbmUAAAAEbm9uZQAAAAAAAAABAAABFwAAAAdzc2gtcn
   NhAAAAAwEAAQAAAQEAuTUPCCEa0lsBq40QiV+zYCAN6gzEV9Xl7IWJF6va0GeUOZnpN9vK
   6AtY3El9ElSsSSwStu+MFxXbPp9vmhfPE1KA9Wi/moDQSPbAV+JjfYevuAZmbxvAF7pRoJ
   vIAVzA2+MEP/z+sB4h4CzUpi63gqoej+m+20ZxbUR37XgxNrgh2O5qE0RmeLFQOpoyofQM
   JF7D+X0I9sBIqf7dM9n6URyeXBCu8MFzbh4oRPS+w3fRQ6MIEhCnc4p/8OWTXD16HozugR
   ma7F0ZFv9dURe4SjTl0Scm7GXqtYnJsup9Iq1jHH+a0CM/kq+NNJ/QdLdypbmChhTaxC6d
   3UQrcfcoxwAAA8B1IXoLdSF6CwAAAAdzc2gtcnNhAAABAQC5NQ8IIRrSWwGrjRCJX7NgIA
   3qDMRX1eXshYkXq9rQZ5Q5mek328roC1jcSX0SVKxJLBK274wXFds+n2+aF88TUoD1aL+a
   gNBI9sBX4mN9h6+4BmZvG8AXulGgm8gBXMDb4wQ//P6wHiHgLNSmLreCqh6P6b7bRnFtRH
   fteDE2uCHY7moTRGZ4sVA6mjKh9AwkXsP5fQj2wEip/t0z2fpRHJ5cEK7wwXNuHihE9L7D
   d9FDowgSEKdzin/w5ZNcPXoejO6BGZrsXRkW/11RF7hKNOXRJybsZeq1icmy6n0irWMcf5
   rQIz+Sr400n9B0t3KluYKGFNrELp3dRCtx9yjHAAAAAwEAAQAAAQAeIMB6PRBk6cMCyibH
   ghbm6y/4Q+1osHX/nNVpUV5+Cmt1V1E18f428ymYZCgBZF7GZHIC6kLqunZ44GzCL19TMF
   ekFE8e7hdz1xgA8+XlVL5D/F6LcoM0GO4QZ2cIubLx0iMt2ZUAx1YRZpmNEwptKglgtdCD
   URlAgiPUMHopAVHopkjSPBW3m5cUd2vV/bViDZS2mBL9apd87NHL6/dsDXmQSSQKrfa8Qq
   Z5PRhugE1oDkmH8KoAapU8v2UF/VT2Aojnf7e1OAl4L7HZ3bZ7kQ3dcqZGhMlBS0oXv1EP
   bmiG30cnfsFRmpNGzyWqBwP+I/3LZD34AskKsKNW5VBxAAAAgQCnlQy5tW7vk8y7FZX4Xr
   2pt3Jl5OIDP1rMHv3LCqzBkJ/krPvnXHbO+l2JqmlQlKImcGgSgYAHKj5Zq8gykbmLiqyC
   /odbDKNoYB/4ZaE1B6r+HZOBQtQ+f17NZmkPkbJ6DQzKmb6vqMhTPOS0/zEcD7H1qUMOkT
   m9Fhn3A7a7+wAAAIEA8D+ssc+hxBxpFbTYWxHTpY89MOEzu2YjS0/tmKBknPAMJEbKI+WF
   JO3k3/tO26/WyrAq2FWFbRZ0HSFI2uZNkkj/bhSshe5A5gHywhPX7fqUIXFnkSJz8cqrq1
   aAQOln+u5o/yU0Dbhjl3NnB83NARHMoK0/G5iNya3ZhG+z3+kAAACBAMVZkfut903qPPlf
   4IXo437RMBugkFe8CdFZGXym6htFhjJWt6jMzWG6zj1pnowKRTolmC5vMa+RZa82LfSICM
   KO5TLD9jCb2ygOFOPQ/HM77LWFpvRlI18736VoaVn1IrvJJLPzNwV38WkmnhUOcUZZ4wnd
   +QlvkPSE3JealYUvAAAAB251dGFuaXgBAgM=
   -----END OPENSSH PRIVATE KEY-----
   ```

8. anthos sa private key

   ```
   wangken@wangken-MAC notes % cat anthos-test-202303-privatekey.json 
   {
     "type": "service_account",
     "project_id": "anthos-test-202303",
     "private_key_id": "b76e04e16af19f535e185f2f36bae2be8038c363",
     "private_key": "-----BEGIN PRIVATE KEY-----\nMIIEvAIBADANBgkqhkiG9w0BAQEFAASCBKYwggSiAgEAAoIBAQDxFqcCiqlc0VJE\naVQaU+4eM3fht4sQX25W+T5wrEsYaqhu9MfEfo5B8EQq80+y0sy18DJrrle61VFM\n4rrx3XCuj1ewZggdyN92/mys7Kar7FKAb7EPZZZc+tlG0HG6IV5PqnAhQAxyg/Sy\nZAbpQ1TVOfdpIoQTHMe7Hz0SJfYCvpk9L0Vz7L12XsU5S9bGkI2Tgj4qtW8CRqin\nJf18vxbo5z4pUjWBt1FLpT9Dk53MDEvU/AJKFgxBAGRUHnjfkpN5xXsbBFSBuJk8\nn498hpt/KwntN6CMVSfTBofjc3Z9muuINqmecCK/H/n5tJnaAWImN25EbdmwbwB7\nZ53k14/vAgMBAAECggEAL+x8U22D4B799vpnDPq9HUTG4lgNbTpDIUfXaSdeoCJn\ni/LdmQo9Ng9QRadrItVzewEdzLjx2IJZ8GorljOaGCEHYdnOaDlLboiBytgaA5ft\nCHnrXO+pNZ9pvIFn8gN7D2QGeR2Vu9fONv3aP9kyDlbA/yWs0m3IqEI77hUcs4uT\n924G3W8lN4PpOidrZVvPL74Et++DwLruO1lEpVE2Q1moZve2clNmZivrrIiBXk32\nW4fFhKzyfC/OGD9L0Md/Q4YaCmZspqMLLq7quyyAKMDJr10E/9jqeebWmreZ6P1i\nahjk2HKKmC+OMpzZA2kh4GUU02jbcw89oBV9t6vO6QKBgQD+fO0nydmt5v8Qzk9e\nnHwB9VWaHmcheaKaJOgLcS6U1Ka2GO7gkjLwZ8fBEm40ROU23zcj457EbbN0kpPJ\nlQgCklMBoQCasNqxKVGzh0M4n7GAUMSvoF/FlOzdHQaS1rcxE7blrdpJLxYRLqOw\nEo7Q6jsb/2M6pRk82AyA+pM/CQKBgQDyhVhxcjADX5my+20TtE8xLyTIAoPlhIAx\n3+W7wiHphPXflgA+nxby7FQRi0BG1pqa+mx4DOiLVTc3igVN2fxLON9ltTuVAkPU\nIqE4JdDOWQF7icleJKSLSWcway7UaTW+GcP76wQ/M55LW6fCdnRC73drSQbo+NSC\nAhH3i8kdNwKBgBcxoZevwOQlmneYpgk0b+TpzDx4quOVJ2mvFWr9jMZJv0v3Z8YV\n7QiWHNGO8XZYFR/0Jh1iQHUcnm9wcIG90HYTifcrClgO6E+fOXAIUusVOuM7+UEc\nd74VPaVFYPT/FsElT9UNDEkBPpygSJDikBugTXTWyN9ubqdp9XHH5KWpAoGAT1mn\n5X6KDSCDhpdTSiYt3xbgvvxrsXYYB7mNTlCnjeNuG1jV/adJ9/OxUggw4Lyo21pi\nkSkQET6xkV98est/DBGwrnOM6iVSkh8+hsOAvXNL0+LyWvY8TEKZG7OGIAPIjMmb\nYVq1CgTWnyt/CVZ+lcQKW7UKKMH5rgwFWuyGwiMCgYAN8z0F/WAyVcu4lYE4g4VS\nMgn5c52UKntIu1p+F5Mupn6YPv8CLhJTXS8o4pd5aLJgnfzfD+K+SlLj4rv2PyFr\nDbKRXuDmrh7PCLmxzuz68RUTaBHsGAY+h+eFKGW0+oIZDfnIcVv3G4Ye2ZsENpTk\nRdqNRgy1au08ZSZyB0buRg==\n-----END PRIVATE KEY-----\n",
     "client_email": "netfos@anthos-test-202303.iam.gserviceaccount.com",
     "client_id": "110466507071833816563",
     "auth_uri": "https://accounts.google.com/o/oauth2/auth",
     "token_uri": "https://oauth2.googleapis.com/token",
     "auth_provider_x509_cert_url": "https://www.googleapis.com/oauth2/v1/certs",
     "client_x509_cert_url": "https://www.googleapis.com/robot/v1/metadata/x509/netfos%40anthos-test-202303.iam.gserviceaccount.com"
   }
   ```

9. 輸入完後按save , 執行
   ![image-20230314153857881](../../../Documents/GitHub/nutanix-lab/assets/anthos-launch.png)

10. 填入資訊
    ![image-20230314154058030](../../../Documents/GitHub/nutanix-lab/assets/anthos-lauch01.png)

11. IP 資訊
    ![image-20230314154540212](../../../Documents/GitHub/nutanix-lab/assets/anthos-lauch02.png)

12. IP 資訊
    ![image-20230314154644473](../../../Documents/GitHub/nutanix-lab/assets/anthos-lauch03.png)

13. Deploy
    ![image-20230314160527582](../../../Documents/GitHub/nutanix-lab/assets/anthos-deploy.png)

14. 自動化部屬
    ![image-20230314160559318](../../../Documents/GitHub/nutanix-lab/assets/anthos-deploy05.png)

15. Manage可以查看目前的部署狀態
    ![image-20230314160801127](../../../Documents/GitHub/nutanix-lab/assets/anthos-deploy06.png)

16. 部署完成
    ![image-20230314173530825](../../../Documents/GitHub/nutanix-lab/assets/anthos-vm.png)

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
    
    eyJhbGciOiJSUzI1NiIsImtpZCI6ImNjZ25wQTdxdDlQejJPLUwwLWI4bjNhTnhxcVZIRVhVMFYyd01LQ25JaTAifQ.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJkZWZhdWx0Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZWNyZXQubmFtZSI6Imdvb2dsZS1jbG91ZC1jb25zb2xlLXRva2VuLXR6ZGtkIiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZXJ2aWNlLWFjY291bnQubmFtZSI6Imdvb2dsZS1jbG91ZC1jb25zb2xlIiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZXJ2aWNlLWFjY291bnQudWlkIjoiNTNjODE5NjMtOWU3OS00ZWMxLWEwMTItMmI3MGQ5MjAxMDM5Iiwic3ViIjoic3lzdGVtOnNlcnZpY2VhY2NvdW50OmRlZmF1bHQ6Z29vZ2xlLWNsb3VkLWNvbnNvbGUifQ.RMAGImKTizAIpoZlvg1jERYw52yNWzxpSmDMag9u2MOMJFZuvWsbsNgAFl6l6vCgxQIbUMUaGHNS6KWjaFbQ9uT44e60WwhsSQMNzyh8S_9umgdfrQRUIGbvLCeyzkvfSiUr4gXTVNpS4QN0VxhcC4qcPU91D3xrd-s-tv5iQAJChUiS0tfWkP-UnyRm2xSYhSGt_eonjWyO6OCh-idoyaDgLNUqTH-Zus28vTW_D_aFjAdyqBRnA2zj223K5zYsqCb7euA6IyWnXolLn5z3cpN1pnF9oATXP895zdBjCC2gZztbZof_CuX8YV64kRWuCqxm5f-PbkZ2Tz1GISkpRg
    
    [nutanix@anthos-netfos1-anthos-adminVm-0 anthos-netfos1]$ pwd
    /home/nutanix/baremetal/bmctl-workspace/anthos-netfos1
    [nutanix@anthos-netfos1-anthos-adminVm-0 anthos-netfos1]$ cat anthos-netfos1-kubeconfig 
    apiVersion: v1
    clusters:
    - cluster:
        certificate-authority-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUM2akNDQWRLZ0F3SUJBZ0lCQURBTkJna3Foa2lHOXcwQkFRc0ZBREFWTVJNd0VRWURWUVFERXdwcmRXSmwKY201bGRHVnpNQjRYRFRJek1ETXhOREE0TlRJME0xb1hEVE16TURNeE1UQTROVGMwTTFvd0ZURVRNQkVHQTFVRQpBeE1LYTNWaVpYSnVaWFJsY3pDQ0FTSXdEUVlKS29aSWh2Y05BUUVCQlFBRGdnRVBBRENDQVFvQ2dnRUJBTk1TCmJySVV3ZGVIVlMxYkZFUW9DTTR1TkkydSs5WWQwdVYzVUUzWDRicngyTXIrQVprUThjZ0dTWTNTUGlhQWkxZHMKYS9hNG9EM0psM3I3ZjlaUldQRFFmYllZZURmdktuRkVUQUNCMEN2TVE5eFN0dm4zZnBIWi9iR09HQW9xMWFqZgpIRHNLNFAyVnFVNytMUU1UbVpoTXZxK3MyUUZaMEVSS1krb2RXK2FnbTlkRTl4c3pDWXVvN3NBRkc0Y1Zrc2txCkdiTkp0dUNrRFdZNzd3dHFmYnR2YTlIS0NnYWRiOUlabTZjN1cxQXVhMnYxVFdiWjBvdTlsQTN0SmRDYXhmUjMKcG9QUW4wVTdlVDlvU0ZWL0dodUdqVG1KWkNJRWZBempFbDdEMjdLL242Nis0MU1OMFNzSmQyU1BUczBjcGZnaApIekR3WlZOaFBGNEFMRzVUWmNNQ0F3RUFBYU5GTUVNd0RnWURWUjBQQVFIL0JBUURBZ0trTUJJR0ExVWRFd0VCCi93UUlNQVlCQWY4Q0FRQXdIUVlEVlIwT0JCWUVGQkN1ZVFna0k4YWI1b2crQm9YSFRTQUdZUXNaTUEwR0NTcUcKU0liM0RRRUJDd1VBQTRJQkFRQ1NzQ3k1dTdVV0E2TmxCWlY0QmRjL1poVlkrdWg5ZjBPWUt5Vi9EcmZ6cTJCcgoyOEVCQUpMTm55aDB1OU5NUFVlV3FPbUZSRFVIaWlHT1hKR2dOQ0N1YjJKYTg3SVRiSnovaGQvcDBlbW55L3NSCmNXUFl5Q0VtUzVXQnh2a2p3VnFQNE1MOEtxa0hhRDVVRm4xVUdwQlpINkYybHpvcDhCajhnVDlnN0krVWJSd2cKc1dSMXJVR0xHQWE0K3lOdmpFR0Jja0pIa1Nja3lOTDg3U1NEZEcvZkJmZVlPcUNtL1BVVTA5YklIOVhXY3NrQQpaOE1Ldlc3N3d2TS9DSVBrUldrekNPZ3RkVGpJUWp2RVpSV2x0aDdWSjBRMTFDZDdCK3JpWjJLbDBvblNGRWpoCjgwaEtJK1cxMDQ5ZUxDam16dWd2MVlEQXE2UnhFT0xFbW1NTUZlZXcKLS0tLS1FTkQgQ0VSVElGSUNBVEUtLS0tLQo=
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
        client-certificate-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSURFekNDQWZ1Z0F3SUJBZ0lJRE5mV0xoR0VvUXd3RFFZSktvWklodmNOQVFFTEJRQXdGVEVUTUJFR0ExVUUKQXhNS2EzVmlaWEp1WlhSbGN6QWVGdzB5TXpBek1UUXdPRFV5TkROYUZ3MHlOREF6TVRNd09EVTRNVEphTURReApGekFWQmdOVkJBb1REbk41YzNSbGJUcHRZWE4wWlhKek1Sa3dGd1lEVlFRREV4QnJkV0psY201bGRHVnpMV0ZrCmJXbHVNSUlCSWpBTkJna3Foa2lHOXcwQkFRRUZBQU9DQVE4QU1JSUJDZ0tDQVFFQXpYSGZPUlQvNHhPeVRPczAKVDJralltcncwa0o0R1JtWTRUb2Fldno1bjR4YkhNZGFQcTN5WW9VWGx5MkRXRmRhUWhoMklnNjlSdWdVRnNRSgpVTVZFT3ZNb3NiNXpGeUFJU0ZLdTh6aHpLNGRQS2dEbGpLMUdOckh6OXovYmxpRVFFK3d2eXNYcHhJamljMTRhCjRqeG9WeDFkNDRtNkdSRFBGRmpNeURkSG9xTGcrVzJHWmNWR1ZSelNiNU5TWmNiOUVRMmMrSnUyMjAzZ1ppRlMKZmFWZGQ5UlduYUlyUnFzTlJ0bUtMMm5lWjVWTTFWQXVhSW9vUUtXV0NRSWlYSUhOWVRDVUhsTkJJVUdubWx1TAprdjhQL3h3QTlNT2ViNE9lUG4vaFBhWEYrUlNwOTMvcnRDMDRrSGN1b2h3MmxHSUgyZ3dhMkp5cm9oZGZrR1NxCkZuWENiUUlEQVFBQm8wZ3dSakFPQmdOVkhROEJBZjhFQkFNQ0JhQXdFd1lEVlIwbEJBd3dDZ1lJS3dZQkJRVUgKQXdJd0h3WURWUjBqQkJnd0ZvQVVFSzU1Q0NRanhwdm1pRDRHaGNkTklBWmhDeGt3RFFZSktvWklodmNOQVFFTApCUUFEZ2dFQkFEbVlCMzJMeFJPTHFuZUNLQjA0dHcveVhEZlkvaFk0S0xwaGFpTGJ6UW00M1U0aXpKa0ljZkpjCnBEVGdnSmU0SmpiTkxLKzBHYVB2dm8vT0ZjRjAyaHBSd2pJZnJ0VmRmQ3VCbjlTVmVBdzl6cm1QeW5GbGlhS0gKS0tVWWJTb2VpbzVsTlkwN1RTSEdQUXcxVDZ4KzRFY0dQeWxUNEsxMUVvU1dQRE9iTmRwUS9WQWdPd3I3WVFMVwpFVnVCTEFLb0hKZEMzcUxWWWJkT3RPV2hONU5ONGdMZXFTc2dLWnJQZVFJTWJVSGFqOHo0L2FtSC9aeVV0NUNsCk9ydy8wQjRROFpIVkVETWRuZUUvSjFlUm9maHpwK2RHcXhaNVdyYmQzVUpGN0Jxbnd5Wm9JQVNZMDhoazBGM2EKL1BHWVNmMm02Q3VSS2FVSmlFZGNkby9Yd0xZV1ZDZz0KLS0tLS1FTkQgQ0VSVElGSUNBVEUtLS0tLQo=
        client-key-data: LS0tLS1CRUdJTiBSU0EgUFJJVkFURSBLRVktLS0tLQpNSUlFb3dJQkFBS0NBUUVBelhIZk9SVC80eE95VE9zMFQya2pZbXJ3MGtKNEdSbVk0VG9hZXZ6NW40eGJITWRhClBxM3lZb1VYbHkyRFdGZGFRaGgySWc2OVJ1Z1VGc1FKVU1WRU92TW9zYjV6RnlBSVNGS3U4emh6SzRkUEtnRGwKaksxR05ySHo5ei9ibGlFUUUrd3Z5c1hweElqaWMxNGE0anhvVngxZDQ0bTZHUkRQRkZqTXlEZEhvcUxnK1cyRwpaY1ZHVlJ6U2I1TlNaY2I5RVEyYytKdTIyMDNnWmlGU2ZhVmRkOVJXbmFJclJxc05SdG1LTDJuZVo1Vk0xVkF1CmFJb29RS1dXQ1FJaVhJSE5ZVENVSGxOQklVR25tbHVMa3Y4UC94d0E5TU9lYjRPZVBuL2hQYVhGK1JTcDkzL3IKdEMwNGtIY3VvaHcybEdJSDJnd2EySnlyb2hkZmtHU3FGblhDYlFJREFRQUJBb0lCQUJVTm9rNTdORzNVeXVUUApCYUZOcU82Zy91VE5Jdm1QZ2ZjeXVSdjVhS3RNK3RsTUpKZGZ4QU1NbUlwSmc3ZzkyMllDazdpUndodk9GS0R3Cm5mUEZBMlQzSGloNDE5cDYwZDUzZXE0NkRyTmJQbVdUaWZLTW56RmpzeGlYVnExZjNnSHNwa2tsVnZ0bys2dk8KN1BwYUxtY2UvMHdlQlJBa2hOUVU5WWRmQXR4TG5hNDhwNndDUzAwdHF0bEZ6ZDF2S1Vwci9Oc1dNOUJUSXpVawpQSEtacGxPeUlaWHhPc25Ia1A3TlpQSXlnb0JKakU4Y1FyYUg2dURsdnJCVkZWUlRNMmZua3NXMjdVMU10bUdaCjd3ZmM5VjVTalFwb0szN1lBNzBNK0lUaEZIaUg2WkxzVkY2emJVQ1JMZXhlNjhsZzlNaGZFWHpsSjg5bGhyYkUKQ2J4NjhvRUNnWUVBMjVTeEZzY21kaGs1YkpKVkp6clU0M3REN1lzWCtBL2pBTDNjMm93WXBOZml4U09EUFFFRApKVHcrSlZhbyszVEJScDFVWk83RENkT1gydWxoTDRWV0MzdU9hWGNIV3BveDRadXJUR1RDZ0ZaTUYrMjdaVDRyClI4aS9YZmpVajdKR1JOR2lidnYxaVAzNWZRNS8vUERZait3L3VzZVZXS2ZTQUxaQnU0UGV3WlVDZ1lFQTc0VDQKbHU2WDAzUzVLWWFkTVAyZld6MTFQbFE5ZjJ3cG9qTlh4WmlVS0M3S3lxYXdEbFEwRXhuRFdlSmhRcGdiRGFvcAp3NU5LOUFIK3B1c0kzdmNyZG5VbDg5MzRGQnVsRGkyalF6cVdVZStQNVh2dWlTQWlhcksyc2RySjE0ZStuRHdOCnNmZ2NaU3FRcUY2RWxnWjJlc1ozWUsyRUsreC9ZYnpvMGpGd2Qza0NnWUJmZGtsM0thV2krbHhvdzBXYUJJM0IKU0RuRDhCQy9tOGlJN2dJeVVXMzFYSllPTnQ0N2kxRWV3dzRSbFpkcG10emNJbElxZjFMejFyWFNTbHdpR01uTAp2Qyt4MGptMFBnMHBsRS9vcW5XVTdlK3ZCMy9OQ0RZd3d5blBaUHFrYmxEMllsMUgrdXBJWUlJeXlEY0VkSUR5Ck1UZVRzR2xSWGNTQzRybTVHQitqOFFLQmdEYlBVZWVQLzdSRzBKeGREcG1JWURBTDEwbUZFM0dXT2N6QlBRT2QKajhIR08yZTJUekZvT1dacGpkZUN3MGp1Nzdubng1aldtdDlObVkxdTJWL1VaZUM4bkF1N0xxckRUTGo2M3BKaQoxTVU1TWMrTTFhQVJkMjY5S0t0NGFwbmttVXk5UFZFTmVzbjN2SlNhMUhKVVZrWndKaDg4ZGJOcmNoYldtTnlJCnJialpBb0dCQU0wdERPeVVxYmtGLyt2YzZVVktwUno1MlArekNubDM3ZTBDQzdCbWsvK3NYemd1Y3podDg5OW8KbHJ4Y3g1UlIvSS9NbzhYWWYrbnJJRndXOEVIMnFyMVZyQzU2NVozcEJIbkJheWJpdzdzeS9TRjhaU05SR2F4UgpZbnhKUU9KM09HZEVNelVSODFKOWpVNDVLRWI4K0g3di9qMUFQaG8ySWVlY21qWkR4bzJuCi0tLS0tRU5EIFJTQSBQUklWQVRFIEtFWS0tLS0tCg==
    
    ```

    

18. 登入完成
    ![image-20230314174734950](../../../Documents/GitHub/nutanix-lab/assets/anthos-login.png)

    ![image-20230314175007215](../../../Documents/GitHub/nutanix-lab/assets/anthos-GKE.png)

19. Workload
    ![image-20230314175203478](../../../Documents/GitHub/nutanix-lab/assets/anthos-workload.png)

20. Marketplace部署測試程式
    ![image-20230314175624682](../../../Documents/GitHub/nutanix-lab/assets/anthos-marketplace01.png)

21. 設定並部署
    ![image-20230314175712418](../../../Documents/GitHub/nutanix-lab/assets/anthos-marketplace02.png)

    ![image-20230314180133597](../../../Documents/GitHub/nutanix-lab/assets/anthos-marketplace03.png)

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
    ![image-20230320170954778](../../../Documents/GitHub/nutanix-lab/assets/anthos-redmine.png)

25. anthos畫面
    ![image-20230320171120742](../../../Documents/GitHub/nutanix-lab/assets/anthos-redmine2.png)

    ![image-20230320171223253](../../../Documents/GitHub/nutanix-lab/assets/anthos-redmine3.png)

    StorageClass --> default block storage

    ![image-20230320171344271](../../../Documents/GitHub/nutanix-lab/assets/anthos-redmine4.png)

    
