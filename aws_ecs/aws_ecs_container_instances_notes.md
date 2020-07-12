# ECS Container Instances

## Launching an Amazon ECS Container Instance

### To launch a container instance

1. Go to EC2, and launch an AMI

   a. Choose **Community AMIs**
   b. Choose an AMI for your container instance. You can choose an Amazon ECS-optimized AMI,
      or another operating system, such as CoreOS or Ubuntu. If you do not choose an Amazon
      ECS-optimized AMI, you must follow the procedures in installing the Amazon ECS container
      agent.

2. Choose an instance type. You can select the hardware configuration for your instance. The
   instance type that you select determines the resources available for your tasks to run.
3. Configure instance details.

   a. Set the **Number of instances** field depending on how many container instances you
      want to add to your cluster.
   b. (optional) use Spot Instances.
   c. For **Network**, choose the VPC into which to launch your container instance
   d. For **Subnet**, choose a subnet to use, or keept the default option to choose the
      default subnet in any availability zone.
   e. Set the **Auto-assign Public IP** field depending on whether you want your instance to be
      accessible from the public internet. If your instance should be accessible from the public
      internet, verify that this field is set to enable.

      **Note**

      Container instances need access to communicate with the Amazon ECS service endpoint. This
      can be through an interface VPC endpoint or through your container instances having public
      IP addresses. If you don't have an interface VPC endpoint configured and your container instances
      do not have public IP addresses, then they must use network address translation (NAT) to provide
      this access.
    f. Select the *ecsInstanceRole* IAM role value that you created for your container instances. If
       you don't setup launch your container instance with the proper IAM permissions, your Amazon ECS
       agent cannot connect to your cluster.
    g. (Optional) Configure your Amazon ECS container instance with user data, such as the agent environment
       variables from Amazon ECS Container Agent Configuration. Amazon EC2 user data scripts are executed
       only one time, when the instance is first launched.
4. Add Storage. Configure the storage for your container instance.
5. Add tags.
6. Configure security group
7. Review instance launch
