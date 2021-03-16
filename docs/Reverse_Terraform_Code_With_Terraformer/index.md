   <!-- Copyright 2020 SJULTRA, inc.

   Licensed under the Apache License, Version 2.0 (the "License");
   you may not use this file except in compliance with the License.
   You may obtain a copy of the License at

       http://www.apache.org/licenses/LICENSE-2.0

   Unless required by applicable law or agreed to in writing, software
   distributed under the License is distributed on an "AS IS" BASIS,
   WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
   See the License for the specific language governing permissions and
   limitations under the License. -->

[go back](../Getting-Started)

# Reverse terraform code with terraformer for GCP

![UML](/_images/terraform_reverse.png)


If you want to transform your already existing cloud infrastructure to terraform code, you can use Terraformer

	https://github.com/GoogleCloudPlatform/terraformer

First of all log into you GCP account and open cloud shell console:

### Install Terraformer
Export cloud provider, in this case "google":

	export PROVIDER=google

Download the packages for Terraformer from git

	curl -LO https://github.com/GoogleCloudPlatform/terraformer/releases/download/$(curl -s https://api.github.com/repos/GoogleCloudPlatform/terraformer/releases/latest | grep tag_name | cut -d '"' -f 4)/terraformer-${PROVIDER}-darwin-amd64

![1](/_images/terraform_1.png)

Give execute permission:

	chmod +x terraformer-${PROVIDER}-linux-amd64

Move it to /user/loca/bin/

	 sudo mv terraformer-${PROVIDER}-linux-amd64 /usr/local/bin/terraformer

Check the version.

	terraformer --version

### In order to export infrastructure as a code with Terraformer you need to have terraform initialized.

Terraform should already be installed in GCP. You can check your terraform version

	terraform --version

Create a file called terraform.tf for initializing terraform and import all it's modules.

	vim terraform.tf

Paste the following lines

	provider "google" {
        }

Initialize terraform

	teraform init

![2](/_images/terraform_2.png)

Use now Terraformer to export your infrastructure as a code. 
Chose your resources (firewall, instances, networks, etc), region and the project from where you want to import.

    terraformer import google --resources=firewall,instances,networks,subnetworks --regions=<us-central1> --projects=<your_project_id>
    
![3](/_images/terraform_3.png)

If you want to import other resources options, check the list below:

The generated terraform code files can be found in ./generated/google/

<br>

List of supported GCP services:

-   `addresses`
    -   `google_compute_address`
-   `autoscalers`
    -   `google_compute_autoscaler`
-   `backendBuckets`
    -   `google_compute_backend_bucket`
-   `backendServices`
    -   `google_compute_backend_service`
-   `bigQuery`
    -   `google_bigquery_dataset`
    -   `google_bigquery_table`
-   `cloudFunctions`
    -   `google_cloudfunctions_function`
-   `cloudsql`
    -   `google_sql_database_instance`
    -   `google_sql_database`
-   `dataProc`
    -   `google_dataproc_cluster`
-   `disks`
    -   `google_compute_disk`
-   `externalVpnGateways`
    -   `google_compute_external_vpn_gateway`
-   `dns`
    -   `google_dns_managed_zone`
    -   `google_dns_record_set`
-   `firewall`
    -   `google_compute_firewall`
-   `forwardingRules`
    -   `google_compute_forwarding_rule`
-   `gcs`
    -   `google_storage_bucket`
    -   `google_storage_bucket_acl`
    -   `google_storage_default_object_acl`
    -   `google_storage_bucket_iam_binding`
    -   `google_storage_bucket_iam_member`
    -   `google_storage_bucket_iam_policy`
    -   `google_storage_notification`
-   `gke`
    -   `google_container_cluster`
    -   `google_container_node_pool`
-   `globalAddresses`
    -   `google_compute_global_address`
-   `globalForwardingRules`
    -   `google_compute_global_forwarding_rule`
-   `healthChecks`
    -   `google_compute_health_check`
-   `httpHealthChecks`
    -   `google_compute_http_health_check`
-   `httpsHealthChecks`
    -   `google_compute_https_health_check`
-   `iam`
    -   `google_project_iam_custom_role`
    -   `google_project_iam_member`
    -   `google_service_account`
-   `images`
    -   `google_compute_image`
-   `instanceGroupManagers`
    -   `google_compute_instance_group_manager`
-   `instanceGroups`
    -   `google_compute_instance_group`
-   `instanceTemplates`
    -   `google_compute_instance_template`
-   `instances`
    -   `google_compute_instance`
-   `interconnectAttachments`
    -   `google_compute_interconnect_attachment`
-   `kms`
    -   `google_kms_key_ring`
    -   `google_kms_crypto_key`
-   `logging`
    -   `google_logging_metric`
-   `memoryStore`
    -   `google_redis_instance`
-   `monitoring`
    -   `google_monitoring_alert_policy`
    -   `google_monitoring_group`
    -   `google_monitoring_notification_channel`
    -   `google_monitoring_uptime_check_config`
-   `networks`
    -   `google_compute_network`
-   `packetMirrorings`
    -   `google_compute_packet_mirroring`
-   `nodeGroups`
    -   `google_compute_node_group`
-   `nodeTemplates`
    -   `google_compute_node_template`
-   `project`
    -   `google_project`
-   `pubsub`
    -   `google_pubsub_subscription`
    -   `google_pubsub_topic`
-   `regionAutoscalers`
    -   `google_compute_region_autoscaler`
-   `regionBackendServices`
    -   `google_compute_region_backend_service`
-   `regionDisks`
    -   `google_compute_region_disk`
-   `regionHealthChecks`
    -   `google_compute_region_health_check`
-   `regionInstanceGroups`
    -   `google_compute_region_instance_group`
-   `regionSslCertificates`
    -   `google_compute_region_ssl_certificate`
-   `regionTargetHttpProxies`
    -   `google_compute_region_target_http_proxy`
-   `regionTargetHttpsProxies`
    -   `google_compute_region_target_https_proxy`
-   `regionUrlMaps`
    -   `google_compute_region_url_map`
-   `reservations`
    -   `google_compute_reservation`
-   `resourcePolicies`
    -   `google_compute_resource_policy`
-   `regionInstanceGroupManagers`
    -   `google_compute_region_instance_group_manager`
-   `routers`
    -   `google_compute_router`
-   `routes`
    -   `google_compute_route`
-   `schedulerJobs`
    -   `google_cloud_scheduler_job`
-   `securityPolicies`
    -   `google_compute_security_policy`
-   `sslCertificates`
    -   `google_compute_managed_ssl_certificate`
-   `sslPolicies`
    -   `google_compute_ssl_policy`
-   `subnetworks`
    -   `google_compute_subnetwork`
-   `targetHttpProxies`
    -   `google_compute_target_http_proxy`
-   `targetHttpsProxies`
    -   `google_compute_target_https_proxy`
-   `targetInstances`
    -   `google_compute_target_instance`
-   `targetPools`
    -   `google_compute_target_pool`
-   `targetSslProxies`
    -   `google_compute_target_ssl_proxy`
-   `targetTcpProxies`
    -   `google_compute_target_tcp_proxy`
-   `targetVpnGateways`
    -   `google_compute_vpn_gateway`
-   `urlMaps`
    -   `google_compute_url_map`
-   `vpnTunnels`
    -   `google_compute_vpn_tunnel`
    
<br>

Visit terraformer github page for more informations

https://github.com/GoogleCloudPlatform/terraformer
