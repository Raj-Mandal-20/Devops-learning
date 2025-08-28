### Setup in your local PC
- You have to map the domain name to your system
- windows: `C:\Windows\System32\drivers\etc\hosts`
- linux | unix system: `/etc/hosts`

- In local system there will a problem creating tunnel 
- so make sure your ingress-ngix-controller is running as LoadBalancer not as a NodePort
- if it's running as NodePort change this as LoadBalancer to change this
use `kubectl edit svc ingress-nginx-controller -n ingress-nginx`

- make sure you are running `minikube tunnel`

### Common confusion when you are unable use ingress ip:
- The Ingress object IP (in your case 192.168.x.x)
  - This is the cluster’s internal address assigned by Kubernetes (via the Ingress Controller Service).
  - If you were on cloud Kubernetes (AWS EKS, GKE, AKS), that IP would often be a real external LoadBalancer IP. You’d point your DNS (like wordpress.com) to that IP, and users anywhere on the internet could access it.
  - But on Minikube or Docker Desktop, that IP usually isn’t reachable directly from your Windows host, because it’s inside the VM or Docker network.

- Why you use 127.0.0.1 instead
  - When you run minikube tunnel (or Docker Desktop proxy), traffic from your host machine (Windows) is forwarded to the cluster through 127.0.0.1
  - That’s why you add 127.0.0.1 wordpress.local in your hosts file → so your browser sends requests to your localhost, and Minikube tunnels them into the cluster → the Ingress sees the host (wordpress.local) and routes it correctly.

- So what’s the point of the Ingress IP then?
  - It’s useful if you’re running Kubernetes in an environment where that IP is directly routable (like bare-metal with MetalLB, or a cloud LoadBalancer).
  - On Minikube/Docker Desktop, it’s mostly for Kubernetes internals, not something you hit directly. You use 127.0.0.1 because tunneling makes it work on your host machine.