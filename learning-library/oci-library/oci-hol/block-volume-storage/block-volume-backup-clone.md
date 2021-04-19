<!-- Not tested -->
# Using Backup and Clone Feature of Block Volume Storage

## Introduction

Welcome to the Using Backup and Clone Feature of Block Volume Storage self-paced lab from Oracle!

Oracle Cloud Infrastructure Block Volume Service lets you dynamically provision and manage block storage volumes. You can create, attach, connect, and move volumes to meet your storage and application requirements. Once attached and connected to an instance, you can use a volume like a regular hard drive. Volumes can also be disconnected from one instance and attached to another instance without any loss of data.

In this lab you will create and attach Block Volume Storage to a compute instance and verify the added storage capacity.

### Prerequisites

* Oracle Cloud Infrastructure account credentials (User, Password, Tenant, and Compartment).
* [OCI Training](https://cloud.oracle.com/en_US/iaas/training)
* [Familiarity with OCI console](https://docs.us-phoenix-1.oraclecloud.com/Content/GSG/Concepts/console.htm)
* [Overview of Networking](https://docs.us-phoenix-1.oraclecloud.com/Content/Network/Concepts/overview.htm)
* [Familiarity with Compartment](https://docs.us-phoenix-1.oraclecloud.com/Content/GSG/Concepts/concepts.htm)
* [Connecting to a compute instance](https://docs.us-phoenix-1.oraclecloud.com/Content/Compute/Tasks/accessinginstance.htm)


## **Step 1:** Sign in to OCI Console and create VCN

1. Sign in using your tenant name, user name and password. Use the login option under **Oracle Cloud Infrastructure**.

    ![](./../grafana/images/Grafana_015.PNG " ")

2. From the OCI Services menu, Click **Virtual Cloud Networks** under Networking. Select the compartment assigned to you from drop down menu on left part of the screen under Networking and Click **Start VCN Wizard**.

    **NOTE:** Ensure the correct Compartment is selected under COMPARTMENT list

3. Click **VCN with Internet Connectivity** and click **Start VCN Wizard**.

4. Fill out the dialog box:

      - **VCN NAME**: Provide a name
      - **COMPARTMENT**: Ensure your compartment is selected
      - **VCN CIDR BLOCK**: Provide a CIDR block (10.0.0.0/16)
      - **PUBLIC SUBNET CIDR BLOCK**: Provide a CIDR block (10.0.1.0/24)
      - **PRIVATE SUBNET CIDR BLOCK**: Provide a CIDR block (10.0.2.0/24)
      - Click **Next**

5. Verify all the information and  Click **Create**.

6. This will create a VCN with following components.

    *VCN, Public subnet, Private subnet, Internet gateway (IG), NAT gateway (NAT), Service gateway (SG)*

## **Step 2:** Create ssh keys, compute instance and Block Volume

1. Click the Apps icon in the toolbar and select  Git-Bash to open a terminal window.

     ![](./../oci-quick-start/images/RESERVEDIP_HOL006.PNG " ")

2. Enter command

    ```
    <copy>
    ssh-keygen
    </copy>
    ```
    **HINT:** You can swap between OCI window,
    git-bash sessions and any other application (Notepad, etc.) by Clicking the Switch Window icon.

     ![](./../oci-quick-start/images/RESERVEDIP_HOL007.PNG " ")

3. Press Enter When asked for 'Enter File in which to save the key', 'Created Directory, 'Enter passphrase', and 'Enter Passphrase again.

     ![](./../oci-quick-start/images/RESERVEDIP_HOL008.PNG " ")

4. You should now have the Public and Private keys:

    /C/Users/ PhotonUser/.ssh/id_rsa (Private Key)

    /C/Users/PhotonUser/.ssh/id_rsa.pub (Public Key)

    **NOTE:** id\_rsa.pub will be used to create
    Compute instance and id\_rsa to connect via SSH into compute instance.

    **HINT:** Enter command
    ```
    <copy>
    cd /C/Users/PhotonUser/.ssh (No Spaces)
    </copy>
    ```
    and then
    ```
    <copy>
    ls
    </copy>
    ```
    to verify the two files exist.

5. In git-bash Enter command  

    ```
    <copy>
    cat /C/Users/PhotonUser/.ssh/id_rsa.pub
    </copy>
    ```
    , highlight the key and copy

     ![](./../oci-quick-start/images/RESERVEDIP_HOL009.PNG " ")

6. Click the apps icon, launch notepad and paste the key in Notepad (as backup)

     ![](./../oci-quick-start/images/RESERVEDIP_HOL0010.PNG " ")

7. Switch to the OCI console. From OCI services menu, Click **Instances** under **Compute**.

8. Click **Create Instance**. Fill out the dialog box:

      - **Name your instance**: Enter a name
      - **Choose an operating system or image source**: For the image, we recommend using the Latest Oracle Linux available.
      - **Availability Domain**: Select availability domain
      - **Instance Type**: Select Virtual Machine
      - **Instance Shape**: Select VM shape

      **Under Configure Networking**
      - **Virtual cloud network compartment**: Select your compartment
      - **Virtual cloud network**: Choose the VCN
      - **Subnet Compartment:** Choose your compartment.
      - **Subnet:** Choose the Public Subnet under **Public Subnets**
      - **Use network security groups to control traffic** : Leave un-checked
      - **Assign a public IP address**: Check this option

     ![](./../oci-quick-start/images/RESERVEDIP_HOL0011.PNG " ")

      - **Boot Volume:** Leave the default
      - **Add SSH Keys:** Choose 'Paste SSH Keys' and paste the Public Key saved earlier.

9. Click **Create**.

    **NOTE:** If 'Service limit' error is displayed choose a different shape such as VM.Standard.E2.2 OR VM.Standard2.2 OR choose a different AD.

10. Wait for Instance to be in **Running** state. In git-bash Enter Command:

    ```
    <copy>
    cd /C/Users/PhotonUser/.ssh
    </copy>
    ```

11. Enter **ls** and verify id\_rsa file exists.

12. Enter command
    ```
    <copy>
    bash
    ssh -i id_rsa opc@`<PUBLIC_IP_OF_COMPUTE>`
    </copy>
    ```

    **HINT:** If 'Permission denied error' is seen, ensure you are using '-i' in the ssh command. You MUST type the command, do NOT copy and paste ssh command.

13. Enter 'Yes' when prompted for security message

     ![](./../oci-quick-start/images/RESERVEDIP_HOL0014.PNG " ")

14. Verify opc@`<COMPUTE_INSTANCE_NAME>` appears at the prompt.

15. From OCI services menu Click **Block Volumes** under Block Storage, then Click **Create Block Volume**.

16. Fill out the dialog box:

    - Name: Enter a name for the block volume
    - Create in Compartment: Has the correct compartment selected.
    - Availability Domain: Select availability domain **(must be different from compute instance's AD)**
    - SIZE: Set to 50

    **Leave all other fields as Default**

17. Click **Create Block Volume**. Wait for Block Volume state to change from 'Provisioning' to 'Available'.

18.  From OCI services menu Click **Instance** under Compute.

19. For the compute instance created earlier Click Action item. Click **Attach Block Volume**.

     ![](./../block-volume-storage/images/Block_Volume_001.PNG " ")

20. Verify no block volume is available to attach. This is because we created the block volume in different Availability Domain then the compute instance.

     ![](./../block-volume-storage/images/Block_Volume_002.PNG " ")

21. Next we will use Manual Backup feature to create a backup of this block volume. We will then restore the backup as a regular volume in the same availability domain as the compute instance.From OCI services menu Click **Block Volumes** under **Block Storage**, Locate the block volume you created earlier, Click Action icon  and Click **Create Manual Backup**

     ![](./../block-volume-storage/images/Block_Volume_003.PNG " ")

22. Fill out the dialog box:

    - Name: Provide a Name
    - BACKUP TYPE: Full Backup

23. Click **Create Block Volume Backup**.

24. Click **Block Volume Backups** and verify backup was created and is in **Available** state.

     ![](./../block-volume-storage/images/Block_Volume_004.PNG " ")

25. Now we will create a new Block Volume in the same Availability domain as the compute instance. Click Action icon  and then **Create Block Volume**.

     ![](./../block-volume-storage/images/Block_Volume_005.PNG " ")

26. Fill out the dialog box:

    - Name: Enter a name for the block volume
    - Create in Compartment: Has the correct compartment selected.
    - Availability Domain: Select availability domain **(must be the same as compute instance's AD)**
    - BLOCK VOLUME SIZE: Should be set to 50
    - BACKUP POLICY: Leave as is
    - ENCRYPTION: ENCRYPT USING ORACLE-MANAGED KEYS

27. From OCI services menu Click **Instance** under Compute.

28. For the compute instance created earlier Click Action item. Click **Attach Block Volume**.

29. Fill out the dialog box:

    - Choose how you want to attach your block volume: PARAVIRTUALIZED
    - BLOCK VOLUME COMPARTMENT: Choose your compartment
    - BLOCK VOLUME: Choose the block volume created in the same AD as the compute instance
    - Device Path: Choose a device path. **Note down this device path**
    - ACCESS: Read/Write

30. Click **Attach**.

31. Click Compute instance Name. Click **Attached Block Volume** and verify the Block volume is attached.

     ![](./../block-volume-storage/images/Block_Volume_006.PNG " ")

We successfully attached a block volume that existed in a different Availability domain than the compute instance.Next we will Clone the original block volume.

## **Step 3:** Clone Block Volume and explore Volume Groups

1. From OCI services menu Click **Block Volumes** under Block Storage, locate the Original Block Volume create.Click Action icon  and then Create Clone.Fill out the dialog box:

      - NAME: Provide a Name
      - CREATE IN COMPARTMENT: Choose your compartment
      - BLOCK VOLUME SIZE: Should be set to 50
      - ENCRYPTION: ENCRYPT USING ORACLE-MANAGED KEYS

2. Click **Create Clone**.

3. Once the clones volume is in Available State, navigate back to compute instance page and try to attach the cloned volume. Verify the Cloned volume is not available to be attached. **This is due to the same reason as for the original volume i.e the cloned volume is in a different Availability Domain then the compute instance**.

    **Volume Groups**

    The Oracle Cloud Infrastructure Block Volume service provides you with the capability to group together multiple volumes in a volume group. A volume group can include both types of volumes, boot volumes, which are the system disks for your Compute instances, and block volumes for your data storage. You can use volume groups to create volume group backups and clones that are point-in-time and crash-consistent.

    This simplifies the process to create time-consistent backups of running enterprise applications that span multiple storage volumes across multiple instances. You can then restore an entire group of volumes from a volume group backup.

4. Next we will look at how to create volume groups. In OCI Console window navigate to **Block Storage**.

5. Click **Volume Groups** and then **Create Volume Group**. Fill out the dialog box:

      - NAME: Provide a Name
      - CREATE IN COMPARTMENT: Choose your Compartment
      - CREATE IN AVAILABILITY DOMAIN: Choose the availability domain whose volume need to be grouped

      **NOTE: Only volumes that exist in this AD will appear in the list**

      Under **Volumes**

      - COMPARTMENT: Choose your compartment
      - VOLUME: Click on the drop down and choose the volume that you want to group togehter

6. To choose additional volumes (Block or boot) Click **+Volume** and add additional volumes.

7. Click **Create Volume Group**.

     ![](./../block-volume-storage/images/Block_Volume_007.PNG " ")

8. New Volume group will be created.

## **Step 4:** Delete the resources

1. Switch to  OCI console window.

2. If your Compute instance is not displayed, From OCI services menu Click Instances under Compute.

3. Locate first compute instance, Click Action icon and then **Terminate**.

     ![](./../oci-quick-start/images/RESERVEDIP_HOL0016.PNG " ")

4. Make sure Permanently delete the attached Boot Volume is checked, Click Terminate Instance. Wait for instance to fully Terminate.

     ![](./../oci-quick-start/images/RESERVEDIP_HOL0017.PNG " ")

5. Navigate to **Block Storage** and then Click **Volume Groups**. Click on Volume Group name that was created and then Click **Terminate**.

6. Locate any additional volumes not part of the Volume Group (Cloned Volumes) that you created.

    **HINT:** If multiple storage block volumes are listed, scroll down to find the volumes you created.   

7. Click the Action icon and select **Terminate**.

8. Click **OK** in the confirmation window.

     ![](./../oci-quick-start/images/Customer_Lab_016.PNG " ")

9. From OCI services menu Click **Virtual Cloud Networks** under Networking, list of all VCNs will
appear.

10. Locate your VCN , Click Action icon and then **Terminate**. Click **Delete All** in the Confirmation window. Click **Close** once VCN is deleted

     ![](./../oci-quick-start/images/RESERVEDIP_HOL0018.PNG " ")

## Acknowledgements
*Congratulations! You have successfully completed the lab.*

- **Author** - Flavio Pereira, Larry Beausoleil
- **Adapted by** -  Yaisah Granillo, Cloud Solution Engineer
- **Last Updated By/Date** - Yaisah Granillo, June 2020

