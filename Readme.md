# 🚀 AWS EKS: HPA Setup with Load Testing

The **goal of this project** is to:

---

## 🎯 **Demonstrate Auto-Scaling in Kubernetes on AWS EKS**

This project shows how to:

### ✅ 1. **Deploy an application** on an AWS-managed Kubernetes cluster (EKS).

### ✅ 2. **Expose the app** to the internet using a **LoadBalancer service**, so users can access it externally.

### ✅ 3. **Install and use Metrics Server** to collect CPU usage data from pods.

### ✅ 4. **Configure Horizontal Pod Autoscaler (HPA)** to automatically:

* Monitor the app’s CPU usage
* Increase or decrease the number of pods
* Based on real-time CPU demand

### ✅ 5. **Generate traffic** to simulate high CPU load and **observe the autoscaling in action**.

---



## 🔹 Step 1: Create EKS Cluster

```bash
eksctl create cluster --name my-cluster --region us-west-2
```
![alt text](image.png)
---

## 🔹 Step 2: Deploy the Application

Apply your deployment:

```bash
kubectl apply -f deployment.yaml
```
![alt text](image-1.png)
---

## 🔹 Step 3: Expose Deployment as LoadBalancer

```bash
kubectl expose deployment mywebd --type=LoadBalancer --name=mywebd-svc --port=80
```

Get the service and copy the `EXTERNAL-IP`:

```bash
kubectl get svc mywebd-svc
```
![alt text](image-2.png)

Open in browser or use curl:

```
http://<EXTERNAL-IP>
```
![alt text](image-3.png)
---

## 🔹 Step 4: Install Metrics Server (for HPA)

```bash
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
```
![alt text](image-4.png)

Check if metrics-server pod is running:

```bash
kubectl get pods -n kube-system
```
![alt text](image-5.png)

Test metrics availability:

```bash
kubectl top pods
```
![alt text](image-6.png)

---

## 🔹 Step 5: Create Horizontal Pod Autoscaler

```bash
kubectl autoscale deployment mywebd --cpu-percent=50 --min=1 --max=10
```

Check HPA:

```bash
kubectl get hpa
```
![alt text](image-7.png)

Watch live scaling:

```bash
kubectl get hpa -w
```
![alt text](image-11.png)
---

## 🔹 Step 6: Generate Load (in Separate Terminal 1)

```bash
kubectl run -i --tty load-generator --rm --image=busybox /bin/sh
```

Then inside the pod:

```sh
while true; do wget -q -O- http://<EXTERNAL-IP>; done
```
![alt text](image-9.png)

> Replace `<EXTERNAL-IP>` with your actual LoadBalancer IP.


## Watch live scaling:

```bash
kubectl get hpa -w
```
![alt text](image-8.png)

![alt text](image-10.png)
---

## ✅ Expected Behavior

* `kubectl get hpa -w` shows increasing `TARGETS` CPU usage.
* `REPLICAS` increase as load increases.
* HPA scales from 1 to more pods automatically.

---
