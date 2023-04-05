# Wireguard Server Helm chart

This simple Helm is a lightweight way to configure and run a Wireguard VPN server in Kubernetes.

## Requirements:
* Kubernetes v1.26+
* [Helm](https://helm.sh/docs/intro/install/) v3+

## Installing
TBD

## Usage notes
* This chart uses the [linuxserver/wireguard](https://github.com/linuxserver/docker-wireguard) image. Thanks to the [LinuxServer.io](https://www.linuxserver.io) team for this awesome solution!
* The desired Wireguard peers are defined with the chart values. Their files (configuration, keys, QR code) are generated at container startup if they do not exist yet.
* The server configuration needs to be shared between the different replicas of the application. At the moment, this chart only support sharing it with a `PersistentVolumeClaim`. Thus note that the PVC access mode needs to be `ReadWriteMany` for the different replicas to be spread accross different K8s nodes. It is planned to add support for sharing the configuration via S3.
* We use a `StatefulSet` for the application to leverage ordered deploys and updates. This way we prevent race conditions when generating the peers configuration at startup.

## Configuration

These variables can be configured to set up the Wireguard server. Other variables, less likely to need tweaking, are available and can be seen in the chart's [values.yaml](values.yaml) file (annotations, resources, autoscaling, affinity, etc.).

| Parameter | Description | Default |
|-|-|-|
| `replicaCount` | Number of desired replicas for the server | `1` |
| `server.peers` | List defining all the different peers with their name | `["peer1"]` |
| `server.env` | Object setting the server environment variables. Direct key / value mapping (except for the peers). Refer to the [linuxserver/wireguard](https://github.com/linuxserver/docker-wireguard/blob/master/README.md#parameters) documentation for more details | See all in [`values.yml`](values.yaml) |
| `server.env.TZ` | Timezone used by Wireguard | `Europe/London` |
| `configPersistence.type` | Type of config persistence. Supported values are: `pvc` (PersistentVolumeClaim) | `pvc` |
| `configPersistence.accessModes` | Access modes for the PVC. Refer to the [Kubernetes documentation](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes) for more details| `["ReadWriteOnce"]` |
| `configPersistence.size` | Storage size requested by the PVC | `50Mi` |
| `service.nodePort` | Port number used by the K8s nodes to expose the Wireguard server | `30000` |

## Contributing
This project is still very early stage. All features and improvements to make it more general are welcomed! 👋
