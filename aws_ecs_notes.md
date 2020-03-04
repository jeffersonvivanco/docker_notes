## AWS ECS
AWS ECS is a highly scalable, fast, container management service that makes it easy to run, stop, and manage Docker containers on a cluster. You can host your cluster on a serverless infrastructure that is managed by ECS by launching your services or tasks using the Fargate launch type. For more control you can host your tasks on a cluster of EC2 instances that you manage by using the EC2 launch type. Elastic Beanstalk can also be used to rapidly develop, test, and deploy Docker containers in conjunction with other components of your application infrastructure; however, using ECS directly provides more fine-grained control and access to a wider set of use cases.

Features of ECS

ECS is a regional service that simplifies running application containers in a highly available manner across multiple Availability Zones within a Region. You can create ECS clusters within a new or existing VPC. After a cluster is up and running, you can define task definitions and services that specify which Docker container images to run across your clusters. Container images are stored in and pulled from container registries, which may exist within or outside of your
AWS infrastructure.

## AWS ECS Architecture
* Container and Images
* Task Definitions
  * To prepare your application to run on Amazon ECS, you create a *task definition*. The task definition is a text file, in JSON format, that describes one or
  more containers, up to a max of 10, that form your application.
* Tasks and Scheduling
  * A *task* is the instantiation of a task definition within a cluster. After you have created a task definition for your application within Amazon ECS, you
  can specify the number of tasks that will run on your cluster.
  * Each task that uses the Fargate launch type has its own isolation boundary and does not share the underlying kernel, CPU resources, memory resources, or
  elastic network interface with another task.
  * The Amazon ECS task scheduler is responsible for placing tasks within your cluster.
* Clusters
  * When you run tasks using Amazon ECS, you place them on a *cluster*, which is a logical grouping of resources.
  * When using the Fargate launch type with tasks within your cluster, Amazon ECS manages your cluster resources.
  * When using the EC2 launch type, then your clusters are a group of container instances you manage. An Amazon ECS container instance is an Amazon EC2 instance
  that is running the Amazon ECS container agent. Amazon ECS downloads your container images from a registry that you specify, and runs those images within your
  cluster.
* Container Agent
  * The *container agent* runs on each infrastructure resource within an Amazon ECS cluster. It sends information about the resource's current running tasks
  and resource utilization to Amazon ECS, and starts and stops tasks whenever it receives a request Amazon ECS.
* Related Services
  * AWS Identity and Access Management - IAM is a web service that helps you securely control access to AWS resources for your users. Use IAM to control who
  can use your AWS resources (authentication) and what resources they can use in which ways (authorization). In Amazon ECS, IAM can be used to control access
  at the container instance level using IAM roles, and at the task level using IAM task roles. 
  * AWS EC2 Auto Scaling -

## AWS Fargate on Amazon ECS

AWS Fargate is a technology that you can use Amazon ECS to run containers without having to manage servers or clusters of Amazon EC2 instances. With AWS Fargate
you no longer have to provision, configure, or scale clusters of virtual machines to run containers. This removes the need to choose server types, decide when
to scale your clusters, or optimize cluster packing. 

When you run your tasks and services with the Fargate launch type, you package your application in containers, specify the CPU and memory requirements, define networking and IAM policies, and launch the application. Each Fargate task has its own isolation boundary and does not share the underlying kernel, CPU resources, memory resources, or elastic network interface with another task.

### Task Definitions

* Network Mode - Fargate task definitions require that the network mode is set to `awsvpc`. The `awsvpc` network mode provides each task with its own elastic
network interface. A network configuration is also required when creating a service or manually running tasks.

* Task CPU and Memory - Fargate task definitions require that you specify CPU and memory at the task level. Although, you can also specify CPU and memory at the
container level for Fargate tasks, this is optional. The table below shows the valid combinations of task-level CPU and memory.

| CPU value | Memory value |
| ---      | ---          |
| 256 (.25 vCPU) | 0.5 GB, 1 GB, 2 GB |
| 512 (.5 vCPU)  | 1 GB, 2 GB, 3 GB, 4 GB |
| 1024 (1 vCPU)  | 2 GB, 3 GB, 4 GB, 5 GB, 6 GB, 7 GB, 8 GB |
| 2048 (2 vCPU)  | Between 4 GB and 16 GB in 1-GB increments |
| 4096 (4 vCPU)  | Between 8 GB and 30 GB in 1-GB increments |

* Logging - Fargate task definitions only support the `awslogs` and `splunk` log drivers for the log configuration. The `awslogs` log driver configures your
  Fargate tasks to send log information to Amazon CloudWatch Logs. The following shows a snippet of a task definition where the awslogs log driver is
  configured:
  ```json
  {
    "logConfiguration": {
      "logDriver": "awslogs",
      "options": {
        "awslogs-group": "/ecs/fargate-task-definition",
        "awslogs-region": "us-east-1",
        "awslogs-stream-prefix": "ecs"
      }
    }
  }
  ```

* Amazon ECS Task Execution IAM Role - there is an optional task execution IAM role that you can specify with Fargate to allow your Fargate tasks to
  make API calls to Amazon ECR. The API calls pull container images as well as calling CloudWatch to store container application logs.

* Example Task definition
```json
{
  "containerDefinitions": [
    {
      "command": ["..."],
      "entryPoint": ["..."],
      "essential": true,
      "image": "jeffersonvivanco/spring_demo_repo:spring_app",
      "logConfiguration": {
        "logDriver": "awslogs",
        "options": {
          "awslogs-group": "/ecs/fargate-task-definition",
          "awslogs-region": "us-east-1",
          "awslogs-stream-prefix": "ecs"
        }
      },
      "name": "sample-fargate-app",
      "portMappings": [
        {
          "containerPort": 80,
          "hostPort": 80,
          "protocol": "tcp"
        }
      ]
    }
  ],
  "cpu": 256,
  "executionRoleArn": "arn:aws:iam::02345678910:role/ecsTaskExecutionRole",
  "family": "fargate-task-definition",
  "memory": 512,
  "networkMode": "awsvpc",
  "requiresCompatibilities": [
    "FARGATE"
  ]
}
```
* Task Storage - when provisioned, each Fargate task receives the following storage. Task storage is ephemeral. After a Fargate task stops, the storage
is deleted.
  * 10 GB of Docker layer storage
  * An additional 4 GB for volume mounts. This can be mounted and shared among containers using the `volumes`, `mountPoints`, and `volumesFrom` parameters
  in the task definition. Note: The `host` and `sourcePath` parameters are not supported. Ex of two containers sharing a single volume
  ```json
  {
    "containerDefinitions": [
      {
        "image": "my-repo/database",
        "mountPoints": [
          {
            "containerPath": "/var/scratch",
            "sourceVolume": "database_scratch"
          }
        ],
        "name": "database1"
      },
      {
      "image": "my-repo/database",
      "mountPoints": [
        {
          "containerPath": "/var/scratch",
          "sourceVolume": "database_scratch"
        }
      ],
      "name": "database2"
      }
    ],
    "volumes": [
      {
        "name": "database_scratch"
      }
    ]
  }
  ```

### Tasks and Services
After you have your Fagate task definition prepared, there are some decisions to make when creating your service.

* Task Networking
  
  Tasks using the Fargate launch type require the `awsvpc` network mode, which provides each task with an elastic
  network interface. When you run a task or create a service with this network mode, you must specify one or more
  subnets to attach the network interface and one or more security groups to apply to the network interface.

  If you are using public subnets, decide whether to provide a public IP address for the network interface. For
  a Fargate task in a public subnet to pull container images, a public IP address needs to be assigned to the
  task's elastic network interface, with a route to the internet or a NAT gateway that can route requests to the
  internet. For a Fargate task in a private subnet to pull container images, the private subnet requires a NAT
  gateway be attached to route requests to the internet. The following is an example of the networkConfiguration
  section for a Fargate service:
  ```json
  {
    "networkConfiguration": {
      "awsvpcConfiguration": {
        "assignPublicIp": "ENABLED",
        "securityGroups": ["sg-12345678"],
        "subnets": ["subnet-12345678"]
      }
    }
  }
  ```
  Services with tasks that use the `awsvpc` network mode (for example, those with a Fargate launch type) only
  support Application Load Balancers and Network Load Balancers. Classic Load Balancers are not supported. Also,
  when you create any target groups for these services, you must choose `ip` as the target type, not `instance`.
  This is because tasks that use the `awsvpc` network mode are associated with an elastic network interface, not
  an Amazon EC2 instance.

### Private Registry Authentication
Fargate tasks can authenticate with private image registries, including Docker Hub, using basic authentication.
When you enable private registry authentication, you can use private Docker images in your task definitions.

To use private registry authentication, you create a secret with AWS Secrets Manager containing the credentials
for your private registry. Then, within your container definition, you specify `repositoryCredentials` with the
full ARN of the secret that you created. The following snippet of a task definition shows the required parameters:

```json
{
  "containerDefinitions": [
    {
      "image": "private-repo/private-image",
      "repositoryCredentials": {
        "credentialsParameter": "arn:aws:secretsmanager:region:aws_account_id:secret:secret_name"
      }
    }
  ]
}
```

### Clusters
Clusters can contain tasks using both the Fargate and EC2 launch types. When viewing your clusters in the AWS
Management Console, Fargate and EC2 task counts are displayed separately.

### Fargate Task Retirement
A Fargate task is scheduled to be retired when AWS detects the irreparable failure of the underlying hardware
hosting the task or if a security issue needs to be patched. Most security patches are handled transparently
without requiring any action on your part or having to restart your tasks. But for certain issues, we may require
that the task be restarted.

When a task reaches its scheduled retirement date, it is stopped and terminated by AWS. If the task is part
of a service, then the task is automatically stopped and the service scheduler starts a new one to replace it.
If you are using standalone tasks, then you receive a notification of the task retirement.

### AWS Fargate Platform Versions
AWS Fargate platform versions are used to refer to a specific runtime environment for Fargate task infrastructure.
It is a combination of the kernel and container runtime versions. New platform versions are released as the
runtime environment evolves, for example, if there are kernel or operating system updates, new features, bug fixes,
or security updates. Security updates and patches are deployed automatically for your Fargate tasks. If a security
issue is found that affects a platform version, AWS patches the platform version. In some cases, you may be notified
that your Fargate tasks have been scheduled for retirement.

* Platform Version Considerations

  The following should be considered when specifying a platform version:

    * when specifying a platform version, you can use either the version number or `LATEST`.
    * To use a specific platform version, specify the version number when creating or updating your service. If you
      specify `LATEST`, your tasks use the most current platform version available, which may not be the most
      recent platform version.
    * If you have a service with running tasks and want to update their platform version, you can update your service,
      specify a new platform version, and choose **Force new deployment**. Your tasks are redeployed with the latest
      platform version.
    * If your service is scaled up without updating the platform version, those tasks receive the platform version that
      was specified on the service's current deployment.

## Security in Amazon Elastic Container Service