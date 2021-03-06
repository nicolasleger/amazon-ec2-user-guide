# Identify EC2 Linux Instances<a name="identify_ec2_instances"></a>

You may benefit from being able to determine whether a system is an EC2 instance\. There are two methods that you can use to identify an EC2 instance\.

For information about identifying Windows instances, see [Identify EC2 Windows Instances](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/identify_ec2_instances.html) in the *Amazon EC2 User Guide for Windows Instances*\.

## Inspecting the System UUID<a name="inspect-uuid"></a>

You can get the system UUID and look for the presence of the characters "ec2" or "EC2" in the beginning octet of the UUID\. This method to determine whether a system is an EC2 instance is quick but potentially inaccurate because there is a small chance that a system that is not an EC2 instance could have a UUID that starts with these characters\. For a definitive approach, see [Inspecting the Instance Identity Document](#inspect-document)\.

**Example : Get the UUID from the hypervisor**  
If `/sys/hypervisor/uuid` exists, you can use the following command:  

```
[ec2-user ~]$ cat /sys/hypervisor/uuid
```
In the following example output, the UUID starts with "ec2", which indicates that the system is probably an EC2 instance\.  

```
ec2e1916-9099-7caf-fd21-012345abcdef
```

**Example : Get the UUID from DMI \(HVM instances only\)**  
On HVM instances only, you can use the Desktop Management Interface \(DMI\)\.   
You can use the `dmidecode` tool to return the UUID\. On Amazon Linux, use the following command to install the `dmidecode` tool if it's not already installed on your instance:  

```
[ec2-user ~]$ sudo yum install dmidecode -y
```
Then run the following command:  

```
[ec2-user ~]$ sudo dmidecode --string system-uuid
```
Alternatively, use the following command:  

```
[ec2-user ~]$ sudo cat /sys/devices/virtual/dmi/id/product_uuid
```
In the following example output, the UUID starts with "EC2", which indicates that the system is probably an EC2 instance\.  

```
EC2E1916-9099-7CAF-FD21-01234ABCDEF
```

## Inspecting the Instance Identity Document<a name="inspect-document"></a>

For a definitive and cryptographically verified method of identifying an EC2 instance, check the instance identity document, including its signature\. These documents are available on every EC2 instance at the local, non\-routable address `http://169.254.169.254/latest/dynamic/instance-identity/`\. For more information, see [Instance Identity Documents](instance-identity-documents.md)\.