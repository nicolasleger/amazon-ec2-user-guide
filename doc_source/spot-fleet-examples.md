# Spot Fleet Example Configurations<a name="spot-fleet-examples"></a>

The following examples show launch configurations that you can use with the [request\-spot\-fleet](http://docs.aws.amazon.com/cli/latest/reference/ec2/request-spot-fleet.html) command to create a Spot Fleet request\. For more information, see [Creating a Spot Fleet Request](spot-fleet-requests.md#create-spot-fleet)\.

1. Launch Spot Instances using the lowest\-priced Availability Zone or subnet in the region

1. Launch Spot Instances using the lowest\-priced Availability Zone or subnet in a specified list

1. Launch Spot Instances using the lowest\-priced instance type in a specified list

1. Override the price for the request

1. Launch a Spot Fleet using the diversified allocation strategy

1. Launch a Spot Fleet using instance weighting

## Example 1: Launch Spot Instances Using the Lowest\-Priced Availability Zone or Subnet in the Region<a name="fleet-config1"></a>

The following example specifies a single launch specification without an Availability Zone or subnet\. If your account supports EC2\-VPC only, the Spot Fleet launches the instances in the lowest\-priced Availability Zone that has a default subnet\. If your account supports EC2\-Classic, the Spot Fleet launches the instances in EC2\-Classic in the lowest\-priced Availability Zone\. The price you pay will not exceed the On\-Demand price\.

```
{
  "TargetCapacity": 20,
  "IamFleetRole": "arn:aws:iam::123456789012:role/aws-ec2-spot-fleet-tagging-role",
  "LaunchSpecifications": [
      {
          "ImageId": "ami-1a2b3c4d",
          "KeyName": "my-key-pair",
          "SecurityGroups": [
              {
                  "GroupId": "sg-1a2b3c4d"
              }
          ],
          "InstanceType": "m3.medium",
          "IamInstanceProfile": {
              "Arn": "arn:aws:iam::123456789012:instance-profile/my-iam-role"
          }
      }
  ]
}
```

## Example 2: Launch Spot Instances Using the Lowest\-Priced Availability Zone or Subnet in a Specified List<a name="fleet-config2"></a>

The following examples specify two launch specifications with different Availability Zones or subnets, but the same instance type and AMI\.

**Availability Zones**

If your account supports EC2\-VPC only, the Spot Fleet launches the instances in the default subnet of the lowest\-priced Availability Zone that you specified\. If your account supports EC2\-Classic, the Spot Fleet launches the instances in the lowest\-priced Availability Zone that you specified\.

```
{
  "TargetCapacity": 20,
  "IamFleetRole": "arn:aws:iam::123456789012:role/aws-ec2-spot-fleet-tagging-role",
  "LaunchSpecifications": [
      {
          "ImageId": "ami-1a2b3c4d",
          "KeyName": "my-key-pair",
          "SecurityGroups": [
              {
                  "GroupId": "sg-1a2b3c4d"
              }
          ],
          "InstanceType": "m3.medium",
          "Placement": {
              "AvailabilityZone": "us-west-2a, us-west-2b"
          },
          "IamInstanceProfile": {
              "Arn": "arn:aws:iam::123456789012:instance-profile/my-iam-role"
          }
      }
  ]
}
```

**Subnets**

You can specify default subnets or nondefault subnets, and the nondefault subnets can be from a default VPC or a nondefault VPC\. The Spot service launches the instances in whichever subnet is in the lowest\-priced Availability Zone\.

You can't specify different subnets from the same Availability Zone in a Spot Fleet request\.

```
{
  "TargetCapacity": 20,
  "IamFleetRole": "arn:aws:iam::123456789012:role/aws-ec2-spot-fleet-tagging-role",
  "LaunchSpecifications": [
      {
          "ImageId": "ami-1a2b3c4d",
          "KeyName": "my-key-pair",
          "SecurityGroups": [
              {
                  "GroupId": "sg-1a2b3c4d"
              }
          ],
          "InstanceType": "m3.medium",
          "SubnetId": "subnet-a61dafcf, subnet-65ea5f08",
          "IamInstanceProfile": {
              "Arn": "arn:aws:iam::123456789012:instance-profile/my-iam-role"
          }
      }
  ]
}
```

If the instances are launched in a default VPC, they receive a public IPv4 address by default\. If the instances are launched in a nondefault VPC, they do not receive a public IPv4 address by default\. Use a network interface in the launch specification to assign a public IPv4 address to instances launched in a nondefault VPC\. When you specify a network interface, you must include the subnet ID and security group ID using the network interface\.

```
  ...       
      {
          "ImageId": "ami-1a2b3c4d",
          "KeyName": "my-key-pair",
          "InstanceType": "m3.medium",
          "NetworkInterfaces": [
              {
                  "DeviceIndex": 0,
                  "SubnetId": "subnet-1a2b3c4d",
                  "Groups": [ "sg-1a2b3c4d" ],
                  "AssociatePublicIpAddress": true
              }
          ],
          "IamInstanceProfile": {
              "Arn": "arn:aws:iam::880185128111:instance-profile/my-iam-role"
          }
      }
  ...
```

## Example 3: Launch Spot Instances Using the Lowest\-Priced Instance Type in a Specified List<a name="fleet-config3"></a>

The following examples specify two launch configurations with different instance types, but the same AMI and Availability Zone or subnet\. The Spot Fleet launches the instances using the specified instance type with the lowest price\.

**Availability Zone**

```
{
  "TargetCapacity": 20,
  "IamFleetRole": "arn:aws:iam::123456789012:role/aws-ec2-spot-fleet-tagging-role",
  "LaunchSpecifications": [
      {
          "ImageId": "ami-1a2b3c4d",
          "SecurityGroups": [
              {
                  "GroupId": "sg-1a2b3c4d"
              }
          ],
          "InstanceType": "cc2.8xlarge",
          "Placement": {
            "AvailabilityZone": "us-west-2b"
          }
      },
      {
          "ImageId": "ami-1a2b3c4d",
          "SecurityGroups": [
              {
                  "GroupId": "sg-1a2b3c4d"
              }
          ],
          "InstanceType": "r3.8xlarge",
          "Placement": {
              "AvailabilityZone": "us-west-2b"
          }
      }
  ]
}
```

**Subnet**

```
{
  "TargetCapacity": 20,
  "IamFleetRole": "arn:aws:iam::123456789012:role/aws-ec2-spot-fleet-tagging-role",
  "LaunchSpecifications": [
      {
          "ImageId": "ami-1a2b3c4d",
          "SecurityGroups": [
              {
                  "GroupId": "sg-1a2b3c4d"
              }
          ],
          "InstanceType": "cc2.8xlarge",
          "SubnetId": "subnet-1a2b3c4d"
      },
      {
          "ImageId": "ami-1a2b3c4d",
          "SecurityGroups": [
              {
                  "GroupId": "sg-1a2b3c4d"
              }
          ],
          "InstanceType": "r3.8xlarge",
          "SubnetId": "subnet-1a2b3c4d"
      }
  ]
}
```

## Example 4\. Override the Price for the Request<a name="fleet-config4"></a>

We recommended that you use the default maximum price, which is the On\-Demand price\. If you prefer, you can specify a maximum price for the fleet request and maximum prices for individual launch specifications\.

The following examples specify a maximum price for the fleet request and maximum prices for two of the three launch specifications\. The maximum price for the fleet request is used for any launch specification that does not specify a maximum price\. The Spot Fleet launches the instances using the instance type with the lowest price\.

**Availability Zone**

```
{
  "SpotPrice": "1.00",
  "TargetCapacity": 30,
  "IamFleetRole": "arn:aws:iam::123456789012:role/aws-ec2-spot-fleet-tagging-role",
  "LaunchSpecifications": [
      {
          "ImageId": "ami-1a2b3c4d",
          "InstanceType": "c3.2xlarge",
          "Placement": {
              "AvailabilityZone": "us-west-2b"
          },
          "SpotPrice": "0.10"
      },
      {
          "ImageId": "ami-1a2b3c4d",
          "InstanceType": "c3.4xlarge",
          "Placement": {
              "AvailabilityZone": "us-west-2b"
          },
          "SpotPrice": "0.20"
      },
      {
          "ImageId": "ami-1a2b3c4d",
          "InstanceType": "c3.8xlarge",
          "Placement": {
              "AvailabilityZone": "us-west-2b"
          }
      }
    ]
}
```

**Subnet**

```
{
  "SpotPrice": "1.00",
  "TargetCapacity": 30,
  "IamFleetRole": "arn:aws:iam::123456789012:role/aws-ec2-spot-fleet-tagging-role",
  "LaunchSpecifications": [
      {
          "ImageId": "ami-1a2b3c4d",
          "InstanceType": "c3.2xlarge",
          "SubnetId": "subnet-1a2b3c4d",
          "SpotPrice": "0.10"
      },
      {
          "ImageId": "ami-1a2b3c4d",
          "InstanceType": "c3.4xlarge",
          "SubnetId": "subnet-1a2b3c4d",
          "SpotPrice": "0.20"
      },
      {
          "ImageId": "ami-1a2b3c4d",
          "InstanceType": "c3.8xlarge",
          "SubnetId": "subnet-1a2b3c4d"
      }
  ]
}
```

## Example 5: Launch a Spot Fleet Using the Diversified Allocation Strategy<a name="fleet-config5"></a>

The following example uses the `diversified` allocation strategy\. The launch specifications have different instance types but the same AMI and Availability Zone or subnet\. The Spot Fleet distributes the 30 instances across the 3 launch specifications, such that there are 10 instances of each type\. For more information, see [Spot Fleet Allocation Strategy](spot-fleet.md#spot-fleet-allocation-strategy)\.

**Availability Zone**

```
{
  "SpotPrice": "0.70", 
  "TargetCapacity": 30,
  "AllocationStrategy": "diversified",
  "IamFleetRole": "arn:aws:iam::123456789012:role/aws-ec2-spot-fleet-tagging-role",
  "LaunchSpecifications": [
      {
          "ImageId": "ami-1a2b3c4d",
          "InstanceType": "c4.2xlarge",
          "Placement": {
              "AvailabilityZone": "us-west-2b"
          }
      },
      {
          "ImageId": "ami-1a2b3c4d",
          "InstanceType": "m3.2xlarge",
          "Placement": {
              "AvailabilityZone": "us-west-2b"
          }
      },
      {
          "ImageId": "ami-1a2b3c4d",
          "InstanceType": "r3.2xlarge",
          "Placement": {
              "AvailabilityZone": "us-west-2b"
          }
      }
  ]
}
```

**Subnet**

```
{
    "SpotPrice": "0.70", 
    "TargetCapacity": 30,
    "AllocationStrategy": "diversified",
    "IamFleetRole": "arn:aws:iam::123456789012:role/aws-ec2-spot-fleet-tagging-role",
    "LaunchSpecifications": [
        {
            "ImageId": "ami-1a2b3c4d",
            "InstanceType": "c4.2xlarge",
            "SubnetId": "subnet-1a2b3c4d"
        },
        {
            "ImageId": "ami-1a2b3c4d",
            "InstanceType": "m3.2xlarge",
            "SubnetId": "subnet-1a2b3c4d"
        },
        {
            "ImageId": "ami-1a2b3c4d",
            "InstanceType": "r3.2xlarge",
            "SubnetId": "subnet-1a2b3c4d"
        }
    ]
}
```

## Example 6: Launch a Spot Fleet Using Instance Weighting<a name="fleet-config6"></a>

The following examples use instance weighting, which means that the price is per unit hour instead of per instance hour\. Each launch configuration lists a different instance type and a different weight\. The Spot Fleet selects the instance type with the lowest price per unit hour\. The Spot Fleet calculates the number of Spot Instances to launch by dividing the target capacity by the instance weight\. If the result isn't an integer, the Spot Fleet rounds it up to the next integer, so that the size of your fleet is not below its target capacity\.

If the `r3.2xlarge` request is successful, Spot provisions 4 of these instances\. Divide 20 by 6 for a total of 3\.33 instances, then round up to 4 instances\.

If the `c3.xlarge` request is successful, Spot provisions 7 of these instances\. Divide 20 by 3 for a total of 6\.66 instances, then round up to 7 instances\.

For more information, see [Spot Fleet Instance Weighting](spot-fleet.md#spot-instance-weighting)\.

**Availability Zone**

```
{
  "SpotPrice": "0.70",
  "TargetCapacity": 20,
  "IamFleetRole": "arn:aws:iam::123456789012:role/aws-ec2-spot-fleet-tagging-role",
  "LaunchSpecifications": [
      {
          "ImageId": "ami-1a2b3c4d",
          "InstanceType": "r3.2xlarge",
          "Placement": {
              "AvailabilityZone": "us-west-2b"
          },
          "WeightedCapacity": 6
      },
      {
          "ImageId": "ami-1a2b3c4d",
          "InstanceType": "c3.xlarge",
          "Placement": {
              "AvailabilityZone": "us-west-2b"
          },
          "WeightedCapacity": 3
      }
    ]
}
```

**Subnet**

```
{
  "SpotPrice": "0.70",
  "TargetCapacity": 20,
  "IamFleetRole": "arn:aws:iam::123456789012:role/aws-ec2-spot-fleet-tagging-role",
  "LaunchSpecifications": [
      {
          "ImageId": "ami-1a2b3c4d",
          "InstanceType": "r3.2xlarge",
          "SubnetId": "subnet-1a2b3c4d",
          "WeightedCapacity": 6
      },
      {
          "ImageId": "ami-1a2b3c4d",
          "InstanceType": "c3.xlarge",
          "SubnetId": "subnet-1a2b3c4d",
          "WeightedCapacity": 3
      }
  ]
}
```

**Priority**

You can also use instance weighting to give priority to an Availability Zone or subnet\. For example, the following launch specifications are nearly identical, except that they specify different subnets and weights\. The Spot Fleet finds the specification with the highest value for `WeightedCapacity`, and attempts to provision the request in the least expensive Spot Instance pool in that subnet\. The second launch specification does not include a weight, so it defaults to 1\.

```
{
  "SpotPrice": "0.42",
  "TargetCapacity": 40,
  "IamFleetRole": "arn:aws:iam::123456789012:role/aws-ec2-spot-fleet-tagging-role",
  "LaunchSpecifications": [
      {
          "ImageId": "ami-1a2b3c4d",
          "InstanceType": "c3.2xlarge",
          "SubnetId": "subnet-482e4972",
          "WeightedCapacity": 2
      },
      {
          "ImageId": "ami-1a2b3c4d",
          "InstanceType": "c3.2xlarge",
          "SubnetId": "subnet-bb3337d"
      }
    ]
}
```