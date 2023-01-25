# Terraform secure coding

Terraform scan is powered by tfsec which uses Hashicorp's AST library and checkov for improved analysis. Analysis can be effortlessly performed from anywhere from IDE to cli to CI pipelines.

![Terraform scan](images/terraform-scan.gif)

## Best practices

Currently, checks are mostly limited to AWS/Azure/GCP resources, but there are also checks which are provider agnostic.

| Rule   | Provider | Description                                                       |
| ------ | -------- | ----------------------------------------------------------------- |
| GEN001 | Generic  | Potentially sensitive data stored in "default" value of variable. |
| GEN002 | Generic  | Potentially sensitive data stored in local value.                 |
| GEN003 | Generic  | Potentially sensitive data stored in block attribute.             |
| AWS001 | aws      | S3 Bucket has an ACL defined which allows public access.          |
| AWS002 | aws      | S3 Bucket does not have logging enabled.                          |
| AWS003 | aws      | AWS Classic resource usage.                                       |
| AWS004 | aws      | Use of plain HTTP.                                                |
| AWS005 | aws      | Load balancer is exposed to the internet.                         |
| AWS006 | aws      | An ingress security group rule allows traffic from `/0`.          |
| AWS007 | aws      | An egress security group rule allows traffic to `/0`.             |
| AWS008 | aws      | An inline ingress security group rule allows traffic from `/0`.   |
| AWS009 | aws      | An inline egress security group rule allows traffic to `/0`.      |
| AWS010 | aws      | An outdated SSL policy is in use by a load balancer.              |
| AWS011 | aws      | A resource is marked as publicly accessible.                      |
| AWS012 | aws      | A resource has a public IP address.                               |
| AWS013 | aws      | Task definition defines sensitive environment variable(s).        |
| AWS014 | aws      | Launch configuration with unencrypted block device.               |
| AWS015 | aws      | Unencrypted SQS queue.                                            |
| AWS016 | aws      | Unencrypted SNS topic.                                            |
| AWS017 | aws      | Unencrypted S3 bucket.                                            |
| AWS018 | aws      | Missing description for security group/security group rule.       |
| AWS019 | aws      | A KMS key is not configured to auto-rotate                        |
| AWS020 | aws      | CloudFront distribution allows unencrypted (HTTP) communications. |
| AWS021 | aws      | CloudFront distribution uses outdated SSL/TSL protocols.          |
| AWS022 | aws      | A MSK cluster allows unencrypted data in transit.                 |
| AZU001 | azurerm  | An inbound network security rule allows traffic from `/0`.        |
| AZU002 | azurerm  | An outbound network security rule allows traffic to `/0`.         |
| AZU003 | azurerm  | Unencrypted managed disk.                                         |
| AZU004 | azurerm  | Unencrypted data lake store.                                      |
| AZU005 | azurerm  | Password authentication in use instead of SSH keys.               |
| GCP001 | google   | Unencrypted compute disk.                                         |
| GCP002 | google   | Unencrypted storage bucket.                                       |
| GCP003 | google   | An inbound firewall rule allows traffic from `/0`.                |
| GCP004 | google   | An outbound firewall rule allows traffic to `/0`.                 |
| GCP005 | google   | Legacy ABAC permissions are enabled.                              |

## Security audits

|     | Id          | Type              | Entity                                | Policy                                                                                                                                                                                                   | IaC            |
|-----|-------------|-------------------|---------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------|
|   0 | CKV_AWS_42  | resource          | aws_efs_file_system                   | Ensure EFS is securely encrypted                                                                                                                                                                         | Terraform      |
|   1 | CKV_AWS_47  | resource          | aws_dax_cluster                       | Ensure DAX is encrypted at rest (default is unencrypted)                                                                                                                                                 | Terraform      |
|   2 | CKV_AWS_33  | resource          | aws_ecr_repository                    | Ensure ECR image scanning on push is enabled                                                                                                                                                             | Terraform      |
|   3 | CKV_AWS_51  | resource          | aws_ecr_repository                    | Ensure ECR Image Tags are immutable                                                                                                                                                                      | Terraform      |
|   4 | CKV_AWS_56  | resource          | aws_s3_bucket_public_access_block     | Ensure S3 bucket has 'restrict_public_bucket' enabled                                                                                                                                                    | Terraform      |
|   5 | CKV_AWS_54  | resource          | aws_s3_bucket_public_access_block     | Ensure S3 bucket has block public policy enabled                                                                                                                                                         | Terraform      |
|   6 | CKV_AWS_55  | resource          | aws_s3_bucket_public_access_block     | Ensure S3 bucket has ignore public ACLs enabled                                                                                                                                                          | Terraform      |
|   7 | CKV_AWS_53  | resource          | aws_s3_bucket_public_access_block     | Ensure S3 bucket has block public ACLS enabled                                                                                                                                                           | Terraform      |
|   8 | CKV_AWS_38  | resource          | aws_eks_cluster                       | Ensure Amazon EKS public endpoint not accessible to 0.0.0.0/0                                                                                                                                            | Terraform      |
|   9 | CKV_AWS_37  | resource          | aws_eks_cluster                       | Ensure Amazon EKS control plane logging enabled for all log types                                                                                                                                        | Terraform      |
|  10 | CKV_AWS_39  | resource          | aws_eks_cluster                       | Ensure Amazon EKS public endpoint disabled                                                                                                                                                               | Terraform      |
|  11 | CKV_AWS_58  | resource          | aws_eks_cluster                       | Ensure EKS Cluster has Secrets Encryption Enabled                                                                                                                                                        | Terraform      |
|  12 | CKV_AWS_8   | resource          | aws_launch_configuration              | Ensure all data stored in the Launch configuration EBS is securely encrypted                                                                                                                             | Terraform      |
|  13 | CKV_AWS_8   | resource          | aws_instance                          | Ensure all data stored in the Launch configuration EBS is securely encrypted                                                                                                                             | Terraform      |
|  14 | CKV_AWS_46  | resource          | aws_instance                          | Ensure no hard coded AWS access key and and secret key exists in EC2 user data                                                                                                                           | Terraform      |
|  15 | CKV_AWS_61  | resource          | aws_iam_role                          | Ensure IAM role allows only specific principals in account to assume it                                                                                                                                  | Terraform      |
|  16 | CKV_AWS_60  | resource          | aws_iam_role                          | Ensure IAM role allows only specific services or principals to assume it                                                                                                                                 | Terraform      |
|  17 | CKV_AWS_62  | resource          | aws_iam_role_policy                   | Ensure IAM policies that allow full "*-*" administrative privileges are not created                                                                                                                      | Terraform      |
|  18 | CKV_AWS_63  | resource          | aws_iam_role_policy                   | Ensure no IAM policies documents allow "*" as a statement's actions                                                                                                                                      | Terraform      |
|  19 | CKV_AWS_62  | resource          | aws_iam_user_policy                   | Ensure IAM policies that allow full "*-*" administrative privileges are not created                                                                                                                      | Terraform      |
|  20 | CKV_AWS_40  | resource          | aws_iam_user_policy                   | Ensure IAM policies are attached only to groups or roles (Reducing access management complexity may in-turn reduce opportunity for a principal to inadvertently receive or retain excessive privileges.) | Terraform      |
|  21 | CKV_AWS_63  | resource          | aws_iam_user_policy                   | Ensure no IAM policies documents allow "*" as a statement's actions                                                                                                                                      | Terraform      |
|  22 | CKV_AWS_62  | resource          | aws_iam_group_policy                  | Ensure IAM policies that allow full "*-*" administrative privileges are not created                                                                                                                      | Terraform      |
|  23 | CKV_AWS_63  | resource          | aws_iam_group_policy                  | Ensure no IAM policies documents allow "*" as a statement's actions                                                                                                                                      | Terraform      |
|  24 | CKV_AWS_62  | resource          | aws_iam_policy                        | Ensure IAM policies that allow full "*-*" administrative privileges are not created                                                                                                                      | Terraform      |
|  25 | CKV_AWS_63  | resource          | aws_iam_policy                        | Ensure no IAM policies documents allow "*" as a statement's actions                                                                                                                                      | Terraform      |
|  26 | CKV_AWS_21  | resource          | aws_s3_bucket                         | Ensure all data stored in the S3 bucket have versioning enabled                                                                                                                                          | Terraform      |
|  27 | CKV_AWS_57  | resource          | aws_s3_bucket                         | S3 Bucket has an ACL defined which allows public WRITE access.                                                                                                                                           | Terraform      |
|  28 | CKV_AWS_19  | resource          | aws_s3_bucket                         | Ensure all data stored in the S3 bucket is securely encrypted at rest                                                                                                                                    | Terraform      |
|  29 | CKV_AWS_52  | resource          | aws_s3_bucket                         | Ensure S3 bucket has MFA delete enabled                                                                                                                                                                  | Terraform      |
|  30 | CKV_AWS_18  | resource          | aws_s3_bucket                         | Ensure the S3 bucket has access logging enabled                                                                                                                                                          | Terraform      |
|  31 | CKV_AWS_20  | resource          | aws_s3_bucket                         | S3 Bucket has an ACL defined which allows public READ access.                                                                                                                                            | Terraform      |
|  32 | CKV_AWS_24  | resource          | aws_security_group                    | Ensure no security groups allow ingress from 0.0.0.0:0 to port 22                                                                                                                                        | Terraform      |
|  33 | CKV_AWS_23  | resource          | aws_security_group                    | Ensure every security groups rule has a description                                                                                                                                                      | Terraform      |
|  34 | CKV_AWS_25  | resource          | aws_security_group                    | Ensure no security groups allow ingress from 0.0.0.0:0 to port 3389                                                                                                                                      | Terraform      |
|  35 | CKV_AWS_13  | resource          | aws_iam_account_password_policy       | Ensure IAM password policy prevents password reuse                                                                                                                                                       | Terraform      |
|  36 | CKV_AWS_9   | resource          | aws_iam_account_password_policy       | Ensure IAM password policy expires passwords within 90 days or less                                                                                                                                      | Terraform      |
|  37 | CKV_AWS_11  | resource          | aws_iam_account_password_policy       | Ensure IAM password policy requires at least one lowercase letter                                                                                                                                        | Terraform      |
|  38 | CKV_AWS_10  | resource          | aws_iam_account_password_policy       | Ensure IAM password policy requires minimum length of 14 or greater                                                                                                                                      | Terraform      |
|  39 | CKV_AWS_14  | resource          | aws_iam_account_password_policy       | Ensure IAM password policy requires at least one symbol                                                                                                                                                  | Terraform      |
|  40 | CKV_AWS_12  | resource          | aws_iam_account_password_policy       | Ensure IAM password policy requires at least one number                                                                                                                                                  | Terraform      |
|  41 | CKV_AWS_15  | resource          | aws_iam_account_password_policy       | Ensure IAM password policy requires at least one uppercase letter                                                                                                                                        | Terraform      |
|  42 | CKV_AWS_45  | resource          | aws_lambda_function                   | Ensure no hard coded AWS access key and and secret key exists in lambda environment                                                                                                                      | Terraform      |
|  43 | CKV_AWS_50  | resource          | aws_lambda_function                   | X-ray tracing is enabled for Lambda                                                                                                                                                                      | Terraform      |
|  44 | CKV_AWS_22  | resource          | aws_sagemaker_notebook_instance       | Ensure all data stored in the Sagemaker is securely encrypted at rest                                                                                                                                    | Terraform      |
|  45 | CKV_AWS_29  | resource          | aws_elasticache_replication_group     | Ensure all data stored in the Elasticache Replication Group  is securely encrypted at rest                                                                                                               | Terraform      |
|  46 | CKV_AWS_31  | resource          | aws_elasticache_replication_group     | Ensure all data stored in the Elasticache Replication Group  is securely encrypted at transit and has auth token                                                                                         | Terraform      |
|  47 | CKV_AWS_30  | resource          | aws_elasticache_replication_group     | Ensure all data stored in the Elasticache Replication Group  is securely encrypted at transit                                                                                                            | Terraform      |
|  48 | CKV_AWS_27  | resource          | aws_sqs_queue                         | Ensure all data stored in the SQS queue is encrypted                                                                                                                                                     | Terraform      |
|  49 | CKV_AWS_23  | resource          | aws_security_group_rule               | Ensure every security groups rule has a description                                                                                                                                                      | Terraform      |
|  50 | CKV_AWS_23  | resource          | aws_db_security_group                 | Ensure every security groups rule has a description                                                                                                                                                      | Terraform      |
|  51 | CKV_AWS_23  | resource          | aws_elasticache_security_group        | Ensure every security groups rule has a description                                                                                                                                                      | Terraform      |
|  52 | CKV_AWS_23  | resource          | aws_redshift_security_group           | Ensure every security groups rule has a description                                                                                                                                                      | Terraform      |
|  53 | CKV_AWS_32  | resource          | aws_ecr_repository_policy             | Ensure ECR policy is not set to public                                                                                                                                                                   | Terraform      |
|  54 | CKV_AWS_7   | resource          | aws_kms_key                           | Ensure rotation for customer created CMKs is enabled                                                                                                                                                     | Terraform      |
|  55 | CKV_AWS_28  | resource          | aws_dynamodb_table                    | Ensure Dynamodb point in time recovery (backup) is enabled                                                                                                                                               | Terraform      |
|  56 | CKV_AWS_44  | resource          | aws_neptune_cluster                   | Ensure Neptune storage is securely encrypted                                                                                                                                                             | Terraform      |
|  57 | CKV_AWS_17  | resource          | aws_db_instance                       | Ensure all data stored in the RDS bucket is not public accessible                                                                                                                                        | Terraform      |
|  58 | CKV_AWS_16  | resource          | aws_db_instance                       | Ensure all data stored in the RDS is securely encrypted at rest                                                                                                                                          | Terraform      |
|  59 | CKV_AWS_17  | resource          | aws_rds_cluster_instance              | Ensure all data stored in the RDS bucket is not public accessible                                                                                                                                        | Terraform      |
|  60 | CKV_AWS_6   | resource          | aws_elasticsearch_domain              | Ensure all Elasticsearch has node-to-node encryption enabled                                                                                                                                             | Terraform      |
|  61 | CKV_AWS_5   | resource          | aws_elasticsearch_domain              | Ensure all data stored in the Elasticsearch is securely encrypted at rest                                                                                                                                | Terraform      |
|  62 | CKV_AWS_40  | resource          | aws_iam_user_policy_attachment        | Ensure IAM policies are attached only to groups or roles (Reducing access management complexity may in-turn reduce opportunity for a principal to inadvertently receive or retain excessive privileges.) | Terraform      |
|  63 | CKV_AWS_40  | resource          | aws_iam_policy_attachment             | Ensure IAM policies are attached only to groups or roles (Reducing access management complexity may in-turn reduce opportunity for a principal to inadvertently receive or retain excessive privileges.) | Terraform      |
|  64 | CKV_AWS_43  | resource          | aws_kinesis_stream                    | Ensure Kinesis Stream is securely encrypted                                                                                                                                                              | Terraform      |
|  65 | CKV_AWS_26  | resource          | aws_sns_topic                         | Ensure all data stored in the SNS topic is encrypted                                                                                                                                                     | Terraform      |
|  66 | CKV_AWS_59  | resource          | aws_api_gateway_method                | Ensure there is no open access to back-end resources through API                                                                                                                                         | Terraform      |
|  67 | CKV_AWS_35  | resource          | aws_cloudtrail                        | Ensure CloudTrail logs are encrypted at rest using KMS CMKs                                                                                                                                              | Terraform      |
|  68 | CKV_AWS_36  | resource          | aws_cloudtrail                        | Ensure CloudTrail log file validation is enabled                                                                                                                                                         | Terraform      |
|  69 | CKV_AWS_48  | resource          | aws_mq_broker                         | Ensure MQ Broker logging is enabled                                                                                                                                                                      | Terraform      |
|  70 | CKV_AWS_2   | resource          | aws_alb_listener                      | Ensure ALB protocol is HTTPS                                                                                                                                                                             | Terraform      |
|  71 | CKV_AWS_2   | resource          | aws_lb_listener                       | Ensure ALB protocol is HTTPS                                                                                                                                                                             | Terraform      |
|  72 | CKV_AWS_3   | resource          | aws_ebs_volume                        | Ensure all data stored in the EBS is securely encrypted                                                                                                                                                  | Terraform      |
|  73 | CKV_AWS_34  | resource          | aws_cloudfront_distribution           | Ensure cloudfront distribution ViewerProtocolPolicy is set to HTTPS                                                                                                                                      | Terraform      |
|  74 | CKV_AWS_4   | resource          | aws_ebs_snapshot                      | Ensure all data stored in the EBS Snapshot is securely encrypted                                                                                                                                         | Terraform      |
|  75 | CKV_GCP_21  | resource          | google_container_cluster              | Ensure Kubernetes Clusters are configured with Labels                                                                                                                                                    | Terraform      |
|  76 | CKV_GCP_12  | resource          | google_container_cluster              | Ensure Network Policy is enabled on Kubernetes Engine Clusters                                                                                                                                           | Terraform      |
|  77 | CKV_GCP_18  | resource          | google_container_cluster              | Ensure GKE Control Plane is not public                                                                                                                                                                   | Terraform      |
|  78 | CKV_GCP_20  | resource          | google_container_cluster              | Ensure master authorized networks is set to enabled in GKE clusters                                                                                                                                      | Terraform      |
|  79 | CKV_GCP_19  | resource          | google_container_cluster              | Ensure GKE basic auth is disabled                                                                                                                                                                        | Terraform      |
|  80 | CKV_GCP_8   | resource          | google_container_cluster              | Ensure Stackdriver Monitoring is set to Enabled on Kubernetes Engine Clusters                                                                                                                            | Terraform      |
|  81 | CKV_GCP_24  | resource          | google_container_cluster              | Ensure PodSecurityPolicy controller is enabled on the Kubernetes Engine Clusters                                                                                                                         | Terraform      |
|  82 | CKV_GCP_1   | resource          | google_container_cluster              | Ensure Stackdriver Logging is set to Enabled on Kubernetes Engine Clusters                                                                                                                               | Terraform      |
|  83 | CKV_GCP_23  | resource          | google_container_cluster              | Ensure Kubernetes Cluster is created with Alias IP ranges enabled                                                                                                                                        | Terraform      |
|  84 | CKV_GCP_25  | resource          | google_container_cluster              | Ensure Kubernetes Cluster is created with Private cluster enabled                                                                                                                                        | Terraform      |
|  85 | CKV_GCP_22  | resource          | google_container_cluster              | Ensure Container-Optimized OS (cos) is used for Kubernetes Engine Clusters Node image                                                                                                                    | Terraform      |
|  86 | CKV_GCP_13  | resource          | google_container_cluster              | Ensure a client certificate is used by clients to authenticate to Kubernetes Engine Clusters                                                                                                             | Terraform      |
|  87 | CKV_GCP_7   | resource          | google_container_cluster              | Ensure Legacy Authorization is set to Disabled on Kubernetes Engine Clusters                                                                                                                             | Terraform      |
|  88 | CKV_GCP_4   | resource          | google_compute_ssl_policy             | Ensure Google SSL policy minimal TLS version is TLS_1_2                                                                                                                                                  | Terraform      |
|  89 | CKV_GCP_16  | resource          | google_dns_managed_zone               | Ensure that DNSSEC is enabled for Cloud DNS                                                                                                                                                              | Terraform      |
|  90 | CKV_GCP_17  | resource          | google_dns_managed_zone               | Ensure that RSASHA1 is not used for the zone-signing and key-signing keys in Cloud DNS DNSSEC                                                                                                            | Terraform      |
|  91 | CKV_GCP_14  | resource          | google_sql_database_instance          | Ensure all Cloud SQL database instance have backup configuration enabled                                                                                                                                 | Terraform      |
|  92 | CKV_GCP_11  | resource          | google_sql_database_instance          | Ensure that Cloud SQL database Instances are not open to the world                                                                                                                                       | Terraform      |
|  93 | CKV_GCP_6   | resource          | google_sql_database_instance          | Ensure all Cloud SQL database instance requires all incoming connections to use SSL                                                                                                                      | Terraform      |
|  94 | CKV_GCP_10  | resource          | google_container_node_pool            | Ensure 'Automatic node upgrade' is enabled for Kubernetes Clusters                                                                                                                                       | Terraform      |
|  95 | CKV_GCP_9   | resource          | google_container_node_pool            | Ensure 'Automatic node repair' is enabled for Kubernetes Clusters                                                                                                                                        | Terraform      |
|  96 | CKV_GCP_3   | resource          | google_compute_firewall               | Ensure Google compute firewall ingress does not allow unrestricted rdp access                                                                                                                            | Terraform      |
|  97 | CKV_GCP_2   | resource          | google_compute_firewall               | Ensure Google compute firewall ingress does not allow unrestricted ssh access                                                                                                                            | Terraform      |
|  98 | CKV_GCP_5   | resource          | google_storage_bucket                 | Ensure Google storage bucket have encryption enabled                                                                                                                                                     | Terraform      |
|  99 | CKV_GCP_15  | resource          | google_bigquery_dataset               | Ensure that BigQuery datasets are not anonymously or publicly accessible                                                                                                                                 | Terraform      |
| 100 | CKV_AZURE_2 | resource          | azurerm_managed_disk                  | Ensure Azure managed disk have encryption enabled                                                                                                                                                        | Terraform      |
| 101 | CKV_AZURE_6 | resource          | azurerm_kubernetes_cluster            | Ensure AKS has an API Server Authorized IP Ranges enabled                                                                                                                                                | Terraform      |
| 102 | CKV_AZURE_8 | resource          | azurerm_kubernetes_cluster            | Ensure Kube Dashboard is disabled                                                                                                                                                                        | Terraform      |
| 103 | CKV_AZURE_5 | resource          | azurerm_kubernetes_cluster            | Ensure RBAC is enabled on AKS clusters                                                                                                                                                                   | Terraform      |
| 104 | CKV_AZURE_4 | resource          | azurerm_kubernetes_cluster            | Ensure AKS logging to Azure Monitoring is Configured                                                                                                                                                     | Terraform      |
| 105 | CKV_AZURE_7 | resource          | azurerm_kubernetes_cluster            | Ensure AKS cluster has Network Policy configured                                                                                                                                                         | Terraform      |
| 106 | CKV_AZURE_1 | resource          | azurerm_virtual_machine               | Ensure Azure Instance does not use basic authentication(Use SSH Key Instead)                                                                                                                             | Terraform      |
| 107 | CKV_AZURE_3 | resource          | azurerm_storage_account               | Ensure that 'Secure transfer required' is set to 'Enabled'                                                                                                                                               | Terraform      |
| 108 | CKV_AWS_49  | data              | aws_iam_policy_document               | Ensure no IAM policies documents allow "*" as a statement's actions                                                                                                                                      | Terraform      |
| 109 | CKV_AWS_1   | data              | aws_iam_policy_document               | Ensure IAM policies that allow full "*-*" administrative privileges are not created                                                                                                                      | Terraform      |
| 110 | CKV_AWS_41  | provider          | aws                                   | Ensure no hard coded AWS access key and and secret key exists in provider                                                                                                                                | Terraform      |