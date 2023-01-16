# Objectives
- Create a virtual private cloud (VPC)
- Create subnets
- Configure a security group
- Launch an Amazon Elastic Compute Cloud (Amazon EC2) instance into a VPC
- Deploy web server using userdata

# Scenario
In this project, I use Amazon Virtual Private Cloud (VPC) to create my VPC and add additional components to produce a customized network for a Fortune 100 customer. I also create security groups for my EC2 instance. Then, I configure and customize an EC2 instance to run a web server and launch it into the VPC that looks like the following customer diagram:

![cust-diagram](https://github.com/ridwansukri/VPC-with-CLI/blob/main/images/architecture.jpeg "Customer Diagram")

The customer is requesting the build of this architecture to launch their web server successfully.

# AWS Service Restriction
I work on this project using sandbox to avoid unwanted bills so maybe some features are limited. But I try to make the steps as realistic as possible.

# Configure AWS CLI
To interact with AWS services using the CLI, you need to install and configure CLI on the local machine (I use linux on this project) and create Access Key in the Security Credentials menu. Documentation about CLI you can find [here](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-started.html).
Since I'm using a sandbox, the credentials are already available and using nano to paste credentials

![credentials](https://github.com/ridwansukri/VPC-with-CLI/blob/main/images/credentials.png "Credentials")

```
nano ~/.aws/credentials
```

# Create VPC
Create a VPC with CIDR 10.0.0.0/16
```
aws ec2 create-vpc --cidr-block 10.0.0.0/16 
```
Output:
```
{
    "Vpc": {
        "CidrBlock": "10.0.0.0/16",
        "DhcpOptionsId": "dopt-002aece32994984d7",
        "State": "pending",
        "VpcId": "vpc-02aca48c23e8220e6",
        "OwnerId": "672199935452",
        "InstanceTenancy": "default",
        "Ipv6CidrBlockAssociationSet": [],
        "CidrBlockAssociationSet": [
            {
                "AssociationId": "vpc-cidr-assoc-03c2d77c0db3e3265",
                "CidrBlock": "10.0.0.0/16",
                "CidrBlockState": {
                    "State": "associated"
                }
            }
        ],
        "IsDefault": false
    }
}
```
## Tagging VPC
```
aws ec2 create-tags --resources vpc-02aca48c23e8220e6 --tags Key=Name,Value=VPC-CLI
```
# Create Subnet
The customer requests to create 2 public subnets and 2 private subnets with different AZs. Since this sandbox automatically launches on us-west-2, Public Subnet 1 and Private Subnet 1 will launch on AZ us-west-2a and Public Subnet 2 and Private Subnet 2 will launch on AZ us-west-2b

## Create Public Subnet 1
Public Subnet 1 with CIDR 10.0.0.0/24 and AZ us-west-2a in the VPC that we created earlier
```
aws ec2 create-subnet --vpc-id vpc-02aca48c23e8220e6 --cidr-block 10.0.0.0/24 --availability-zone us-west-2a
```
Output:
```
{
    "Subnet": {
        "AvailabilityZone": "us-west-2a",
        "AvailabilityZoneId": "usw2-az1",
        "AvailableIpAddressCount": 251,
        "CidrBlock": "10.0.0.0/24",
        "DefaultForAz": false,
        "MapPublicIpOnLaunch": false,
        "State": "available",
        "SubnetId": "subnet-02f18c2ce33868f88",
        "VpcId": "vpc-02aca48c23e8220e6",
        "OwnerId": "672199935452",
        "AssignIpv6AddressOnCreation": false,
        "Ipv6CidrBlockAssociationSet": [],
        "SubnetArn": "arn:aws:ec2:us-west-2:672199935452:subnet/subnet-02f18c2ce33868f88",
        "EnableDns64": false,
        "Ipv6Native": false,
        "PrivateDnsNameOptionsOnLaunch": {
            "HostnameType": "ip-name",
            "EnableResourceNameDnsARecord": false,
            "EnableResourceNameDnsAAAARecord": false
        }
    }
}
```

## Tagging Public Subnet 1
```
aws ec2 create-tags --resources subnet-02f18c2ce33868f88 --tags Key=Name,Value=Public-Subnet-1
```

## Create Private Subnet 1
Private Subnet 1 with CIDR 10.0.1.0/24 and AZ us-west-2a in the VPC that we created earlier
```
aws ec2 create-subnet --vpc-id vpc-02aca48c23e8220e6 --cidr-block 10.0.1.0/24 --availability-zone us-west-2a
```
Output:
```
{
    "Subnet": {
        "AvailabilityZone": "us-west-2a",
        "AvailabilityZoneId": "usw2-az1",
        "AvailableIpAddressCount": 251,
        "CidrBlock": "10.0.1.0/24",
        "DefaultForAz": false,
        "MapPublicIpOnLaunch": false,
        "State": "available",
        "SubnetId": "subnet-0f5296308bbf7766b",
        "VpcId": "vpc-02aca48c23e8220e6",
        "OwnerId": "672199935452",
        "AssignIpv6AddressOnCreation": false,
        "Ipv6CidrBlockAssociationSet": [],
        "SubnetArn": "arn:aws:ec2:us-west-2:672199935452:subnet/subnet-0f5296308bbf7766b",
        "EnableDns64": false,
        "Ipv6Native": false,
        "PrivateDnsNameOptionsOnLaunch": {
            "HostnameType": "ip-name",
            "EnableResourceNameDnsARecord": false,
            "EnableResourceNameDnsAAAARecord": false
        }
    }
}
```

## Tagging Private Subnet 1
```
aws ec2 create-tags --resources subnet-0f5296308bbf7766b --tags Key=Name,Value=Private-Subnet-1
```

## Create Public subnet 2
Private Subnet 2 with CIDR 10.0.2.0/24 and AZ us-west-2b in the VPC that we created earlier
```
aws ec2 create-subnet --vpc-id vpc-02aca48c23e8220e6 --cidr-block 10.0.2.0/24 --availability-zone us-west-2b
```
Output:
```
{
    "Subnet": {
        "AvailabilityZone": "us-west-2b",
        "AvailabilityZoneId": "usw2-az2",
        "AvailableIpAddressCount": 251,
        "CidrBlock": "10.0.2.0/24",
        "DefaultForAz": false,
        "MapPublicIpOnLaunch": false,
        "State": "available",
        "SubnetId": "subnet-0ba786e223af6bd76",
        "VpcId": "vpc-02aca48c23e8220e6",
        "OwnerId": "672199935452",
        "AssignIpv6AddressOnCreation": false,
        "Ipv6CidrBlockAssociationSet": [],
        "SubnetArn": "arn:aws:ec2:us-west-2:672199935452:subnet/subnet-0ba786e223af6bd76",
        "EnableDns64": false,
        "Ipv6Native": false,
        "PrivateDnsNameOptionsOnLaunch": {
            "HostnameType": "ip-name",
            "EnableResourceNameDnsARecord": false,
            "EnableResourceNameDnsAAAARecord": false
        }
    }
}
```

## Tagging Public Subnet 2
```
aws ec2 create-tags --resources subnet-0ba786e223af6bd76 --tags Key=Name,Value=Public-Subnet-2
```

## Create Private subnet 2 
Private Subnet 2 with CIDR 10.0.3.0/24 and AZ us-west-2b in the VPC that I created earlier
```
aws ec2 create-subnet --vpc-id vpc-02aca48c23e8220e6 --cidr-block 10.0.3.0/24 --availability-zone us-west-2b
```
Output:
```
{
    "Subnet": {
        "AvailabilityZone": "us-west-2b",
        "AvailabilityZoneId": "usw2-az2",
        "AvailableIpAddressCount": 251,
        "CidrBlock": "10.0.3.0/24",
        "DefaultForAz": false,
        "MapPublicIpOnLaunch": false,
        "State": "available",
        "SubnetId": "subnet-03265e6c705bb8346",
        "VpcId": "vpc-02aca48c23e8220e6",
        "OwnerId": "672199935452",
        "AssignIpv6AddressOnCreation": false,
        "Ipv6CidrBlockAssociationSet": [],
        "SubnetArn": "arn:aws:ec2:us-west-2:672199935452:subnet/subnet-03265e6c705bb8346",
        "EnableDns64": false,
        "Ipv6Native": false,
        "PrivateDnsNameOptionsOnLaunch": {
            "HostnameType": "ip-name",
            "EnableResourceNameDnsARecord": false,
            "EnableResourceNameDnsAAAARecord": false
        }
    }
}
```
## Tagging Private Subnet 2
```
aws ec2 create-tags --resources subnet-03265e6c705bb8346 --tags Key=Name,Value=Private-Subnet-2
```
# Create Internet Gateway
```
aws ec2 create-internet-gateway
```
Output:
```
{
    "InternetGateway": {
        "Attachments": [],
        "InternetGatewayId": "igw-0276549060483ce14",
        "OwnerId": "672199935452",
        "Tags": []
    }
}
```
## Tagging Internet Gateway
```
aws ec2 create-tags --resources igw-0276549060483ce14 --tags Key=Name,Value=IGW-CLI
```

# Attach Internet Gateway to VPC
```
aws ec2 attach-internet-gateway --internet-gateway-id igw-0276549060483ce14 --vpc-id vpc-02aca48c23e8220e6
```

# Create Static IP Address via Elastic IP Address
To Attach NAT Gateway from Public Subnet 1 into Private Subnet 1, I need an IP address that allocated from Elastic IP Address.
```
aws ec2 allocate-address --domain vpc
```
Output:
```
{
    "PublicIp": "34.208.19.63",
    "AllocationId": "eipalloc-0d546b7e68012adcc",
    "PublicIpv4Pool": "amazon",
    "NetworkBorderGroup": "us-west-2",
    "Domain": "vpc"
}
```

# Create NAT-Gateway in Public Subnet 1
Create a NAT gateway and put it in the Public Subnet 1, then map the static IP address from Elastic IP Address
```
aws ec2 create-nat-gateway --subnet-id subnet-02f18c2ce33868f88 --allocation-id eipalloc-0d546b7e68012adcc
```

Output:
```
{
    "ClientToken": "b2c9a31b-1749-4657-a3a9-d8c82b4b964e",
    "NatGateway": {
        "CreateTime": "2023-01-15T11:10:14+00:00",
        "NatGatewayAddresses": [
            {
                "AllocationId": "eipalloc-0d546b7e68012adcc"
            }
        ],
        "NatGatewayId": "nat-01bf708211ec634ed",
        "State": "pending",
        "SubnetId": "subnet-02f18c2ce33868f88",
        "VpcId": "vpc-02aca48c23e8220e6",
        "ConnectivityType": "public"
    }
}
```
## Tagging NAT Gateway
aws ec2 create-tags --resources nat-01bf708211ec634ed --tags Key=Name,Value=NAT-Subnet-1

# Create Route Table 1 for Public Subnet:
```
aws ec2 create-route-table --vpc-id vpc-02aca48c23e8220e6
```
Output:
```
{
    "RouteTable": {
        "Associations": [],
        "PropagatingVgws": [],
        "RouteTableId": "rtb-070e379080e967159",
        "Routes": [
            {
                "DestinationCidrBlock": "10.0.0.0/16",
                "GatewayId": "local",
                "Origin": "CreateRouteTable",
                "State": "active"
            }
        ],
        "Tags": [],
        "VpcId": "vpc-02aca48c23e8220e6",
        "OwnerId": "672199935452"
    }
}
```

## Tagging Route Table 1 as Public Route Table
```
aws ec2 create-tags --resources rtb-070e379080e967159 --tags Key=Name,Value=Public-Route-Table
```

# Create Route Table 2 for Private Subnet:
```
aws ec2 create-route-table --vpc-id vpc-02aca48c23e8220e6
```
Output:
```
{
    "RouteTable": {
        "Associations": [],
        "PropagatingVgws": [],
        "RouteTableId": "rtb-0b8a0dd6b5000023d",
        "Routes": [
            {
                "DestinationCidrBlock": "10.0.0.0/16",
                "GatewayId": "local",
                "Origin": "CreateRouteTable",
                "State": "active"
            }
        ],
        "Tags": [],
        "VpcId": "vpc-02aca48c23e8220e6",
        "OwnerId": "672199935452"
    }
}
```

## Tagging Route Table 2 as Private Route Table
```
aws ec2 create-tags --resources rtb-0b8a0dd6b5000023d --tags Key=Name,Value=Private-Route-Table
```

# Create a Route to The Internet in Public Route Table:
```
aws ec2 create-route --route-table-id rtb-070e379080e967159 --destination-cidr-block 0.0.0.0/0 --gateway-id igw-0276549060483ce14
```
Output:
```
{
    "Return": true
}
```

# Create a route to the internet in Private Route Table for Private Subnet 1 via NAT Gateway:
```
aws ec2 create-route --route-table-id rtb-0b8a0dd6b5000023d --destination-cidr-block 0.0.0.0/0 --gateway-id nat-01bf708211ec634ed
```
Output:
```
{
    "Return": true
}
```

# Associate Public Route Table to Public Subnet 1 :
```
aws ec2 associate-route-table --route-table-id rtb-070e379080e967159 --subnet-id subnet-02f18c2ce33868f88
```
Output:
```
{
    "AssociationId": "rtbassoc-024988c2a745d1507",
    "AssociationState": {
        "State": "associated"
    }
}
```

# Associate Public Route Table to Public Subnet 2 :
```
aws ec2 associate-route-table --route-table-id rtb-070e379080e967159 --subnet-id subnet-0ba786e223af6bd76
```
Output:
```
{
    "AssociationId": "rtbassoc-0d8912fbf6bfa9de1",
    "AssociationState": {
        "State": "associated"
    }
}
```

# Associate Private Route Table to Private Subnet 1 :
```
aws ec2 associate-route-table --route-table-id rtb-0b8a0dd6b5000023d --subnet-id subnet-0f5296308bbf7766b
```
Output:
```
{
    "AssociationId": "rtbassoc-0196d49e7f8055df3",
    "AssociationState": {
        "State": "associated"
    }
}
```

# Create EC2 and it's own components
## Create Security Group
After VPC and additional components is complete, I create Security Group for EC2
```
aws ec2 create-security-group --group-name Web-Security-Group --description "Enable HTTP Access" --vpc-id vpc-02aca48c23e8220e6
```
Output:
```
{
    "GroupId": "sg-064aa87c8f2e78763"
}
```

## Add HTTP Inboud Rules in Security Group
```
aws ec2 authorize-security-group-ingress \
    --group-id sg-064aa87c8f2e78763 \
    --protocol tcp \
    --port 80 \
    --cidr 0.0.0.0/0
```
Output:
```
 {
    "Return": true,
    "SecurityGroupRules": [
        {
            "SecurityGroupRuleId": "sgr-0d3548fe4cc11c99c",
            "GroupId": "sg-064aa87c8f2e78763",
            "GroupOwnerId": "672199935452",
            "IsEgress": false,
            "IpProtocol": "tcp",
            "FromPort": 80,
            "ToPort": 80,
            "CidrIpv4": "0.0.0.0/0"
        }
    ]
}
```

## Add SSH Inboud Rules in Security Group Only for my IP
```
 aws ec2 authorize-security-group-ingress \
    --group-id sg-064aa87c8f2e78763 \
    --protocol tcp \
    --port 22 \
    --cidr 34.101.117.57/32
```
Output:
```
{
    "Return": true,
    "SecurityGroupRules": [
        {
            "SecurityGroupRuleId": "sgr-0cd6d3f070c480c55",
            "GroupId": "sg-064aa87c8f2e78763",
            "GroupOwnerId": "672199935452",
            "IsEgress": false,
            "IpProtocol": "tcp",
            "FromPort": 22,
            "ToPort": 22,
            "CidrIpv4": "34.101.117.57/32"
        }
    ]
}
```

## Create Key Pair
```
aws ec2 create-key-pair --key-name key-pair
```
Output:
```
{
    "KeyFingerprint": "6f:b8:9d:8b:a5:1e:bd:68:74:c9:80:a3:bf:67:10:94:46:fe:cc:1c",
    "KeyMaterial": "-----BEGIN RSA PRIVATE KEY-----\nMIIEpAIBAAKCAQEAtcL/W76QRQ8TQdxT3qC5wdF6AdqOyyxUaOrjyjDlCX1FQ6Fz\n383nhJIwU52TTqj/qOM/qgepdcs1r1vnACcgdCCvpIRG8dMQQmzqTWJTAcqcT2gE\n5nLkvMdZK2gJp6w5nWfRkQmlC2oTjyMYu3x3K6tN8rCF/mWLiBsQwvWgOG1I/WKB\n9V6bNiJv5e4Ypi2p9O1MYgNNylZO7kdcfSiZfrPdphA003lGV3cAVqwDnVtSa2I9\nkuouE9rnO2OmQWjkuHbZOv8aTaD/RzMyQoKxo/fOZoNJEuhEv72YJPiaI6lg+PI2\nFr4Rcrv+lsSTQgKsITNB3iu2lTrlFtJeI3apDwIDAQABAoIBAQCocZjzLgxHY6wm\nCgjTtcHQY9Ac7a4Njfx/6sbFd0Ca5bQN9A8NpqVbD5unsc11RVsA6fDzIvyhxHvx\nEktmsdv6otwDq+6PZ1mXJZaRtoBUla78S9rWsj1W0avKdTUVZZ9TR4ZIUlbY2Cpe\nKVlfTv6lwrCPK5ZR50tDDEohUz5zawrzmXWrPqTLEuSEi7SxeSGIyxuXAemQa9My\n5uutijNLy/6Y9dYFbtMmJtpHxqOSsIlufinXy7DnwPZqc923rdFV4knXEv6vJYJ5\nE82KF+Dbajr4jqWdFPupvDVN709LHY04BGt4iZJTilm9lW2DnmWumbXlVvLG42sc\nmPphQzYxAoGBAOmvIXhz/yesrZMjQ1N/aqomgvKj82YOdkamNwErY3Xp5ykzCTRn\n8jYhrh3+S9N2fBK1wLG1Ikr7S6XrKqP/RTLsVsVox0KKZ/4DE5SEVLzwmTyS4/Wa\nAMgzCLQLyIkSoLGG2IubjpANnmoC4Z2RSjp5jE/V1lXHLDFDdB8VgAv5AoGBAMce\nhXM/xvbDxdW4Lu+IZkOn8BrE9K+zuc/hnOEV82qhPb3xF8pKGUwVsQImMjRv6OE0\nQayMxACJO3BBIsppPXLEW8wTWGWDdxxf7cziXjbHSyL/Wt1ilDPosRn9vsyiOcBJ\nXQ3Hc8KSUaSt7A9ABPIOtCyhNYzJBhulY+Psrc9HAoGAPTZJzzKbYLoj0YoIJcQX\nnbBu1r5JkK8zHjiF6gGCkS2PBsS+oYKk+LcD1Al7tU2xHHmNmz82V2vSGgkq50CD\n0N4FsLpMj8qPiQMnSt0LEV741NwpaHlJwSdVHUyE4BsICtimupMp2eQnXd+ZV9vq\nFL0oGvWJqnh8w/7GWSoZm4kCgYEAo+fY3CykoB45LJsHb79sxsZn2/FCpZshGiDS\nXWoPTDfcNg1OkwL53eqBIY7FhuqT3UWBxgK9mN9eISJM/CczINTH564I9s8H7kB8\n5El2Wksk63Mdndz2t+AUYJvCQnpLZaA+TAhhnsmJETDlfwwoxgQahh5RkUkskPdM\nyaLa1CMCgYBfUFn9gm+TH/qtVQPPBfpc+FGS1eaeYRVjbMXIdmrGmDeMHovabeI0\nf2yKqxbYZOoy6PkvinS6SMJA4tQoeBGrekdOxQ9BbrirWdbSgTzGrNTSD4f7roJD\ncpj2WvnI61+6WTHelsxfVtJDvysaMOFyk/RavrxMhRqzBsfczlc8hw==\n-----END RSA PRIVATE KEY-----",
    "KeyName": "keypair",
    "KeyPairId": "key-089162164d08c227c"
}
```

# Create EC2
ami-0b5eea76982371e91 Amazon Linux 2 AMI (HVM) - Kernel 5.10, SSD Volume Type (64-bit (x86)) 

nano userdata.txt

```
#!/bin/bash

# Install Apache Web Server and PHP

yum install -y httpd mysql php

# Download Lab files

wget https://aws-tc-largeobjects.s3.us-west-2.amazonaws.com/CUR-TF-100-RESTRT-1/267-lab-NF-build-vpc-web-server/s3/lab-app.zip

unzip lab-app.zip -d /var/www/html/

# Turn on web server

chkconfig httpd on

service httpd start
````
```
realpath -s userdata.txt
```

/home/ridho/userdata.txt



aws ec2 run-instances \
    --image-id ami-0ceecbb0f30a902a6\
    --instance-type t2.micro \
    --subnet-id subnet-0ba786e223af6bd76 \
    --security-group-ids sg-064aa87c8f2e78763 \
    --associate-public-ip-address \
    --key-name keypair
    --user-data /home/ridho/userdata.txt


{
    "Groups": [],
    "Instances": [
        {
            "AmiLaunchIndex": 0,
            "ImageId": "ami-0ceecbb0f30a902a6",
            "InstanceId": "i-002076bfb9c5bb913",
            "InstanceType": "t2.micro",
            "KeyName": "keypair",
            "LaunchTime": "2023-01-15T11:32:12+00:00",
            "Monitoring": {
                "State": "disabled"
            },
            "Placement": {
                "AvailabilityZone": "us-west-2b",
                "GroupName": "",
                "Tenancy": "default"
            },
            "PrivateDnsName": "ip-10-0-2-214.us-west-2.compute.internal",
            "PrivateIpAddress": "10.0.2.214",
            "ProductCodes": [],
            "PublicDnsName": "",
            "State": {
                "Code": 0,
                "Name": "pending"
            },
            "StateTransitionReason": "",
            "SubnetId": "subnet-0ba786e223af6bd76",
            "VpcId": "vpc-02aca48c23e8220e6",
            "Architecture": "x86_64",
            "BlockDeviceMappings": [],
            "ClientToken": "8c678917-8522-4b62-9a0d-cf4b0b6eefd8",
            "EbsOptimized": false,
            "EnaSupport": true,
            "Hypervisor": "xen",
            "NetworkInterfaces": [
                {
                    "Attachment": {
                        "AttachTime": "2023-01-15T11:32:12+00:00",

base64 userdata.txt > userdata64.txt
aws ec2 modify-instance-attribute --instance-id i-06bb4e94b36958e1a --attribute userData --value file://userdata64.txt


aws ec2 start-instances --instance-ids i-06bb4e94b36958e1a
{
    "StartingInstances": [
        {
            "CurrentState": {
                "Code": 0,
                "Name": "pending"
            },
            "InstanceId": "i-06bb4e94b36958e1a",
            "PreviousState": {
                "Code": 80,
                "Name": "stopped"
            }
        }
    ]
}

Resources:
[Setup Nat Gateway](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/ec2/create-nat-gateway.html)
