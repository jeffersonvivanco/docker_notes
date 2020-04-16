# AWS ECS Services Notes

## Service Discovery

Your Amazon ECS service can optionally be configured to use Amazon ECS Service Discovery.
Service discovery uses AWS Cloud Map API actions to manage HTTP and DNS namespaces for your
Amazon ECS services.

### Service Discovery Concepts
Service Discovery consists of the following components:

* Service discovery namespace: A logical group of service discovery services that share the same
  domain name, such as `example.com`

* Service discovery service: Exists within the service discovery namespace and consists of the
  service name and DNS configuration for the namespace. It provides the following core component:
  * Service registry: Allows you to look up a service via DNS or AWS Cloud Map API actions and
    get back one or more available endpoints that can be used to connect to the service.

* Service discovery instance: Exists within the service discovery service and consists of the
  attributes associated with each Amazon ECS service in the service directory.
  * Instance attributes: The following metadata is added as custom attributes for each Amazon ECS
    service that is configured to use service directory:
    * `AWS_INSTANCE_IPV4`: For an `A` record, the IPv4 address that Route 53 returns in response
      to DNS queries and AWS Cloud Map returns when discovering instance details, for example
      `190.0.2.44`
    * `AWS_INSTANCE_PORT`: the port value associated with the service discovery service
    * `AVAILABILITY_ZONE`: the Availability Zone into which the task was launched. For tasks
      using the EC2 launch type, this is the Availability Zone in which the container instance
      exists. For tasks using the Furgate launch type, this is the Availability Zone in which
      the elastic network interface exists.
    * `REGION`: The Region in which the task exists.
    * `ECS_SERVICE_NAME`: The name of the Amazon ECS service to which the service belongs
    * `ECS_CLUSTER_NAME`: The name of the Amazon ECS cluster to which the task belongs
    * `EC2_INSTANCE_ID`: The ID of the container instance the task was placed on. This
      custom attribute is not added if the task is using the Fargate launch type.
    * `ECS_TASK_DEFINITION_FAMILY`: The task definition family that the task is using
    * `ECS_TASK_SET_EXTERNAL_ID`: If a task set is created for an external deployment and is
      associated with a service discovery registry, then the `ECS_TASK_SET_EXTERNAL_ID` attribute
      will contain the external ID of the task set.

* Amazon ECS Health Checks: Amazon ECS performs periodic container-level health checks. If an
  endpoint does not pass the health check, it is removed from DNS routing and marked as
  unhealthy.  