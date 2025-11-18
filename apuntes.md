# Apuntes Configuración RYU SDN Framework

## Preparación de maquinas

### Configuración de maquina

- Aumentar RAM
- Aumentar hilos
- Aumentar almacenamiento

### Actualizar paquetes de las maquinas

sudo apt update && sudo apt upgrade -y
sudo apt install net-tools

### Cambiar hostname

sudo hostnamectl set-hostname nodo-k3s-maestro

Nodo-K3S-Maestro

### Configurar ip fija

```bash
sudo nano /etc/netplan/50-cloud-init.yaml
```

Cambiar el archivo por:

```yaml
network:
  version: 2
  ethernets:
    ens3:
      match:
        macaddress: "0c:da:c3:c2:00:00"
      set-name: "ens3"
      dhcp4: false
      dhcp6: false
      addresses:
        - 192.168.122.100/24
      routes:
        - to: default
          via: 192.168.122.1
      nameservers:
        addresses:
          - 8.8.8.8
          - 1.1.1.1
```

```bash
sudo netplan apply
sudo reboot
```

### Instalar paquetes Docker

```bash
# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update

sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin -y

sudo usermod -aG docker $USER

sudo docker run hello-world
```

### Instalar k3s

```bash
curl -sfL https://get.k3s.io | sh -
sudo cat /var/lib/rancher/k3s/server/node-token
hostname -I
sudo mkdir -p ~/.kube
sudo cp /etc/rancher/k3s/k3s.yaml ~/.kube/config
sudo chown $(id -u):$(id -g) ~/.kube/config
export KUBECONFIG=~/.kube/config
kubectl get nodes
```

Para nodos agentes:

```bash
curl -sfL https://get.k3s.io | K3S_URL=https://192.168.122.100:6443 K3S_TOKEN=K10c490d1288e67fe7f9703141306c8ef940424a7af22eec5322d3ac9e7561c050a::server:5aa37f8575f0a05ea0397f2dfd648b90 sh -
```

### Desplegar Ryu en K3S

```bash
git clone https://github.com/ArturoAlvarezz/ryu-k3s.git
cd ryu-k3s
kubectl apply -f ryu-rest-deployment.yaml
kubectl apply -f ryu-rest-service-lb.yaml
kubectl get all -n ryu
kubectl -n ryu get pods -o wide
kubectl -n ryu get svc
kubectl rollout restart deployment/ryu-rest -n ryu
kubectl rollout status deployment/ryu-rest -n ryu
```

### Configurar OVS

```bash
ovs-vsctl show
ip addr flush dev eth0
ip addr add 192.168.122.10/24 dev br0
ip link set br0 up
ovs-vsctl set bridge br0 protocols=OpenFlow13
ovs-vsctl set-controller br0 tcp:192.168.122.51:30266
ovs-vsctl show
ovs-vsctl list controller
ovs-vsctl get controller br0 is_connected

ryu-manager --observe-links ryu.app.simple_switch_13 ryu.app.ofctl_rest ryu.app.gui_topology.gui_topology

ryu-manager ryu.app.simple_switch_13 ryu.app.ofctl_rest ryu.topology.switches

ryu-manager --observe-links ryu.app.simple_switch_13 ryu.app.ofctl_rest ryu.app.gui_topology.gui_topology ryu.topology.switches

curl http://192.168.122.100:8080/v1.0/topology/switches
curl http://192.168.122.100:8080/v1.0/topology/links
curl http://192.168.122.100:8080/stats/switches
curl http://192.168.122.100:8080/stats/flow/257634765051981
```
kubectl -n ryu get svc
NAME          TYPE           CLUSTER-IP      EXTERNAL-IP                                                     PORT(S)                         AGE
ryu-rest-lb   LoadBalancer   10.43.254.145   192.168.122.100,192.168.122.212,192.168.122.46,192.168.122.51   8080:31428/TCP,6633:30417/TCP   23h
