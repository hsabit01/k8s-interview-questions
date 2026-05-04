<details>
<summary><strong>What is a Pod?</strong></summary>

# Kubernetes Interview Question

## Question

What is a Pod?

## 1. Direct Answer

A Pod is the smallest deployable unit in Kubernetes. It represents one running instance of an application and contains one or more containers that share the same network, storage, and lifecycle. In most cases, a Pod runs a single application container, but it can also run helper containers such as sidecars.

## 2. Why It Exists / Problem It Solves

Containers are usually the unit of application packaging, but Kubernetes needs a higher-level unit to schedule and manage workloads. A Pod solves this by grouping containers that must run together on the same node and share resources.

For example, an application container and a logging sidecar may need to share the same filesystem and network namespace. Kubernetes uses the Pod as the common wrapper around those containers.

## 3. How It Works Internally

When you create a Pod, the request goes to the kube-apiserver and is stored in etcd. The scheduler then chooses a suitable worker node based on CPU, memory, affinity rules, taints, tolerations, and other scheduling constraints.

After the Pod is assigned to a node, the kubelet on that node tells the container runtime to start the containers. Containers inside the same Pod share the same IP address, network namespace, and can communicate with each other using `localhost`.

A Pod can also use volumes, environment variables, ConfigMaps, Secrets, probes, and resource requests or limits. If a Pod is managed by a higher-level object like a Deployment, ReplicaSet, StatefulSet, or DaemonSet, Kubernetes can recreate it when it fails.

## 4. Real-World Example

A common production example is a web application running inside a Pod.

The Pod may contain:

- One main container running the application
- One sidecar container collecting logs
- A shared volume used by both containers

Example Pod manifest:

<pre><code class="language-yaml">apiVersion: v1
kind: Pod
metadata:
  name: web-app-pod
  labels:
    app: web
spec:
  containers:
    - name: web-app
      image: nginx:latest
      ports:
        - containerPort: 80</code></pre>

In production, you usually do not create standalone Pods directly. Instead, you create a Deployment, and the Deployment manages the Pods for you.

## 5. Common Issues / Troubleshooting

Common Pod issues include image pull errors, crash loops, failed scheduling, missing environment variables, incorrect volume mounts, and failing health checks.

Useful commands when relevant:

<pre><code class="language-bash">kubectl get pods
kubectl describe pod web-app-pod
kubectl logs web-app-pod
kubectl logs web-app-pod -c web-app
kubectl exec -it web-app-pod -- sh
kubectl get events
kubectl get pod web-app-pod -o yaml</code></pre>

Examples of common Pod statuses:

- `Pending`: The Pod has not been scheduled or containers are not ready yet.
- `Running`: The Pod has been scheduled and at least one container is running.
- `CrashLoopBackOff`: A container starts, crashes, and Kubernetes keeps restarting it.
- `ImagePullBackOff`: Kubernetes cannot pull the container image.
- `Error`: A container failed to start or exited with an error.

## 6. Best Practices / Production Notes

In production, avoid creating standalone Pods manually because they are not self-healing by themselves. Use higher-level controllers like Deployments, StatefulSets, DaemonSets, or Jobs.

Always define resource requests and limits so the scheduler can place Pods correctly and prevent resource starvation. Use readiness probes to control when a Pod receives traffic, and liveness probes to restart unhealthy containers.

Also, keep Pods focused. A Pod should usually run one main application container, with extra containers only when they support the same lifecycle, such as logging, proxying, or service mesh sidecars.

## 7. Final Interview Answer

A Pod is the smallest deployable unit in Kubernetes. It is a wrapper around one or more containers that run together on the same node and share the same network, IP address, storage volumes, and lifecycle. Usually, a Pod contains one main application container, but it can also include helper containers like sidecars. In real production environments, we usually do not manage Pods directly; we use controllers like Deployments or StatefulSets to create, scale, and replace Pods automatically.

</details>
