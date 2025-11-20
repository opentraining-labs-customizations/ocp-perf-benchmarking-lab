# OPEN-54 - OpenShift Performance Benchmarking lab

Ansible role for deploying Elasticsearch on OpenShift for the [OpenShift Performance Benchmarking](https://redhatquickcourses.github.io/ocp-perf-benchmarking/ocp-perf-benchmarking/1/index.html) training.

## Setup

1. Activate the Python virtual environment:
   ```bash
   source bin/activate
   ```

2. Install dependencies:
   ```bash
   pip install -r requirements.txt
   ```

## Usage

Deploy Elasticsearch cluster using the role:
```bash
ansible-playbook deploy.yml
```

This will:
- Install the Elasticsearch ECK operator
- Deploy a single-node Elasticsearch 8.15.0 cluster
- Configure 2Gi memory allocation with emptyDir storage

## Role Configuration

The role can be customized by overriding variables in `defaults/main.yml`:

- `elasticsearch_operator_namespace`: Namespace for the ECK operator (default: `openshift-operators`)
- `elasticsearch_namespace`: Namespace for Elasticsearch instance (default: `openshift-logging`)
- `elasticsearch_instance_name`: Name of the Elasticsearch cluster (default: `elasticsearch`)
- `elasticsearch_version`: Elasticsearch version to deploy (default: `8.15.0`)
- `elasticsearch_node_count`: Number of Elasticsearch nodes (default: `1`)
- `elasticsearch_memory_request/limit`: Memory allocation (default: `2Gi`)
- `elasticsearch_cpu_request/limit`: CPU allocation (default: `1`/`2`)

## Testing Connectivity

From within the cluster:
```bash
# Get password
PASSWORD=$(oc get secret elasticsearch-es-elastic-user -n openshift-logging -o jsonpath='{.data.elastic}' | base64 -d)

# Check cluster health
oc exec -n openshift-logging elasticsearch-es-default-0 -- curl -k -u elastic:$PASSWORD https://elasticsearch-es-http.openshift-logging.svc.cluster.local:9200/_cluster/health?pretty
```

## Endpoint

- **URL**: `https://elasticsearch-es-http.openshift-logging.svc.cluster.local:9200`
- **User**: `elastic`
- **Password**: Retrieved from `elasticsearch-es-elastic-user` secret