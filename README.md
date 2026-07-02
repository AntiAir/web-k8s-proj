### web-k8s-project   test
```
    1  podman pod create --name my-apache-pod -p 8080:80
    2  podman ps
    3  podman pod ps
    4  podman run -d --pod my-apache-pod --name my-web docker.io/library/httpd
    5  podman generate kube my-apache-pod
    6  podman generate kube my-apache-pod >apache-pod.yml
    7  podman pod rm -f my-apache-pod
    8  podman play kube apache-pod.yml
    9  podman down kube apache-pod.yml
   10  ll
   11  podman play kube --down apache-pod.yml
   12  ll
   13  mkdir -p /my-k8s/my-web-project
   14  sudo mkdir -p /my-k8s/my-web-project
   15  ll
   16  sudo mkdir -p /my-k8s/my-web-project
   17  ll
   18  pwd
   19  sudo mkdir -p /my-k8s/my-web-project
   20  ll
   21  cd /my-k8s/my-web-project/
   22  ll
   23  mv ~/apache-pod.yml .
   24  sudo mv ~/apache-pod.yml .
   25  ll
   26  su - root
   27  cd
   28  pwd
   29  ll
   30  pwd
   31  mkdir -p ~/my-k8s/my-web-project
   32  ll
   33  cd /my-k8s/my-web-project/
   34  ll
   35  mv apache-pod.yml ~/my-k8s/my-web-project/
   36  sudo mv apache-pod.yml ~/my-k8s/my-web-project/
   37  cd
   38  ll
   39  cd my-k8s/
   40  cd my-web-project/
   41  ll
   42  cd
   43  sudo rm -rf /my-k8s
   44  pwd
   45  ll
   46  cd my-k8s/
   47  ll
   48  sudo mv my-web-project/ web-project/
```
