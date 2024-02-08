### Kubernetes basic questions ans

---

**1. How many pods exist on the system?**

Answer: To find the number of pods, you can use the command:
```bash
kubectl get pods --all-namespaces
```

---

**2. Create two new pods with the nginx and redis image**

Answer: To create pods with specific images, you can use the following commands:
```bash
kubectl run nginx-pod --image=nginx
kubectl run redis-pod --image=redis
```

---

**3. Which nodes are these pods placed on?**

Answer: To determine the node placement of pods, you can use the command:
```bash
kubectl get pods -o wide
```

---

**4. Create a pod with the test1 image**

Answer: To create a pod with a specific image, you can use the command:
```bash
kubectl run test1-pod --image=test1
```

---

**5. What is the condition of the pod and why it's not running?**

Answer: To check the condition of a pod, you can use:
```bash
kubectl describe pod <pod-name>
```
Look for the `Conditions` section to find details on why the pod is not running.

---

**6. What does the READY column in the output of the `kubectl get pods` command indicate?**

Answer: The READY column indicates the number of containers in the pod that are ready to serve requests.

---

**7. How many ReplicaSets exist on the system?**

Answer: To find the number of ReplicaSets, you can use:
```bash
kubectl get replicasets --all-namespaces
```

---

**8. Create a ReplicaSet with 3 replicas using the given file**

Answer: Save the provided YAML in a file (e.g., replicaset.yaml) and run:
```bash
kubectl apply -f replicaset.yaml
```

---

**9. Delete any one of the 4 PODs and see what happens**

Answer: To delete a pod, use:
```bash
kubectl delete pod <pod-name>
```
The ReplicaSet will create a new pod to maintain the desired number of replicas.

---

**10. Create another ReplicaSet using the given template**

Answer: Save the YAML in a file (e.g., replicaset2.yaml) and run:
```bash
kubectl apply -f replicaset2.yaml
```

---

**11. Scale the ReplicaSet to 5 PODs**

Answer:
```bash
kubectl scale replicaset replicaset-1 --replicas=5
```

---

**12. Now scale the ReplicaSet down to 2 PODs**

Answer:
```bash
kubectl scale replicaset replicaset-1 --replicas=2
```

---

**13. How many Services exist on the system? Search in all the namespaces**

Answer:
```bash
kubectl get services --all-namespaces
```

---

**14. What is the type of the default Kubernetes service?**

Answer:
```bash
kubectl get service kubernetes
```
The type is usually `ClusterIP` for the default Kubernetes service.

---

**15. What is the targetPort configured on the Kubernetes service?**

Answer:
```bash
kubectl describe service <service-name>
```
Look for the `Port` under `Endpoints` section.

---

**16. How many Endpoints are attached to the Kubernetes service, and count the number of labels also?**

Answer:
```bash
kubectl describe service <service-name>
```
Check the `Endpoints` section for the number of endpoints and labels.

---

**17. Create a deployment with nginx image and expose it with a ClusterIP, NodePort, and LoadBalancer service**

Answer: 
```bash
kubectl create deployment nginx-deployment --image=nginx
kubectl expose deployment nginx-deployment --type=ClusterIP --name=nginx-service
kubectl expose deployment nginx-deployment --type=NodePort --name=nginx-nodeport --port=80
kubectl expose deployment nginx-deployment --type=LoadBalancer --name=nginx-lb --port=80
```

---

**18. Deploy a pod named nginx-pod using the nginx:alpine image. Use imperative commands only.**

Answer:
```bash
kubectl run nginx-pod --image=nginx:alpine
```

---

**19. Create a Redis deployment using the redis:alpine image with the labels set to tier=db**

Answer:
```bash
kubectl create deployment redis-deployment --image=redis:alpine --labels=tier=db
```

---

**20. Create a service `redis-service` to expose the Redis application within the cluster on port 6379. Use imperative commands.**

Answer:
```bash
kubectl expose deployment redis-deployment --name=redis-service --port=6379
```

---

**21. Create a new namespace called dev-ns. Use imperative commands**

Answer:
```bash
kubectl create namespace dev-ns
```
