# k8s-dashboard
README.md of setting up kubernetes dashboard ui

# Kubernetes Dashboard Setup

This guide provides instructions on how to install and access the Kubernetes Dashboard, a web-based user interface to visualize your Kubernetes cluster.

## Prerequisites

Ensure that you have `kubectl` installed and configured to interact with your Kubernetes cluster.

## Steps to Deploy the Dashboard

Open your terminal and run the following commands:

```bash
# Step 1: Deploy the Dashboard
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.3.1/aio/deploy/recommended.yaml

# Step 2: Access the Dashboard by starting the proxy
kubectl proxy

# Note: After starting the proxy, you can access the dashboard at the following URL:
# http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/

# Step 3: Authenticate to the Dashboard by creating a service account and getting the token

# Create the service account
kubectl apply -f - <<EOF
apiVersion: v1
kind: ServiceAccount
metadata:
  name: admin-user
  namespace: kubernetes-dashboard
EOF

# Bind the service account to the cluster-admin role
kubectl apply -f - <<EOF
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: admin-user
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: admin-user
  namespace: kubernetes-dashboard
EOF

# Get the token for the service account
kubectl -n kubernetes-dashboard describe secret $(kubectl -n kubernetes-dashboard get secret | grep admin-user | awk '{print $1}')

