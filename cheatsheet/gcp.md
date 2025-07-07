# GCP note

## GKE deleted namespace remains in terminating status

ref: <https://cloud.google.com/knowledge/kb/deleted-namespace-remains-in-terminating-status-000004867>

## GCS add cors config

ref: <https://cloud.google.com/storage/docs/cors-configurations>

```shell
gcloud storage buckets update gs://example_bucket --cors-file=example_cors_file.json
```

- example JSON file containing the CORS configuration

```json

[
    {
      "origin": ["https://your-example-website.appspot.com"],
      "method": ["GET"],
      "responseHeader": ["Content-Type"],
      "maxAgeSeconds": 3600
    }
]
```

## GKE rotate cluster credentials

ref:<https://cloud.google.com/kubernetes-engine/docs/how-to/credential-rotation>

```shell
gcloud container clusters update $clusterName \
    --region $region \
    --start-credential-rotation
```

## glcoud set re auth period

ref: <https://stackoverflow.com/questions/25253330/can-gcloud-auth-login-remember-me-for-at-least-a-day>

## Permission to VM without public ip

add `IAP-secured Tunnel User` role  
ref: <https://medium.com/google-cloud/how-to-ssh-into-your-gce-machine-without-a-public-ip-4d78bd23309e>

## Artifact Registry Push and pull images

ref: <https://cloud.google.com/artifact-registry/docs/docker/pushing-and-pulling>

```shell
gcloud auth configure-docker asia-east1-docker.pkg.dev
```

## service-`project-id`@*.iam.gserviceacount.com

This account is hidden from the `IAM page` in the console unless you select Include`Google-provided role grants`. By default, the account is automatically granted the compute.serviceAgent role on your project.

## Login with service account

```shell
gcloud auth activate-service-account SERVICE_ACCOUNT@DOMAIN.COM --key-file=/path/key.json --project=PROJECT_ID
```

## GCP Metadata ssh key

會在建立 VM 時自動建立在 project level 有 ssh key 的 user

## Best practice for sercvice account key

ref: <https://cloud.google.com/iam/docs/best-practices-for-managing-service-account-keys#rotate-keys>

### Avoid using expiry times for these scenarios

- Production workloads. In production, an expired service account key could cause an accidental outage. Instead, use keys that do not expire, and manage their lifecycle with key rotation.
- Non-production workloads that need permanent access, such as a continuous integration (CI) pipeline.
- Key-rotation systems that prevent a key from being used after a specified amount of time. To learn about recommended key rotation strategies, see Service account key rotation.

## file '/dev/console': open error: Permission denied ubuntu22.04

ref: <https://github.com/GoogleCloudPlatform/compute-image-packages/issues/889>

```shell
udevadm test /sys/devices/virtual/tty/console
systemctl restart rsyslog.service
```

- This is still happening on 22.04 cloud images, but adding the syslog user to the tty group does seem fix it. After running systemctl restart rsyslog it starts back up with no errors.

## GKE Best Practice

### Multi-cluster use cases

ref: <https://cloud.google.com/anthos/fleet-management/docs/multi-cluster-use-cases>

- Isolation: separating the control plane and data plane of services, primarily to improve reliability or address security needs
- Location: placing services in specific locations to address availability, latency, and locality needs
- Scale: especially in the context of Kubernetes clusters, scaling services beyond the practical limits imposed by a single cluster

#### Isolation

- Environment
More often than not, organizations run their development, staging/test, and production services across separate clusters, often running on different networks and cloud projects. This separation is done to avoid accidental disruption of production services and prevent access to sensitive data during development or testing.

- Workload tiering
Often organizations that have many complex applications tier their services, choosing to run their more critical services on separate clusters from their less critical ones. In such an environment, these more critical services and their clusters are treated with special consideration around access, security, upgrades, policy, and more. An example of this tiering is separating stateless and stateful services by placing them on separate clusters.

- Reduced impact from failure
When organizations want to limit the impacts of an operator mistake, cluster failure, or related infrastructure failure, they can split their services across multiple clusters.

## GLB Best Practice

1. Automatic network-level encryption is only available for communications with `instance groups` and `zonal NEG backends`.
2.
