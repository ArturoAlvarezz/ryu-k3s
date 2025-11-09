# Comandos de Despliegue y Gestión de Ryu en K3s

## Despliegue de Redis

```bash
sudo kubectl apply -f ./ryu-k3s-deployment/redis-deployment.yaml
```

## Despliegue de Ryu-Switch

```bash
sudo kubectl apply -f ./ryu-k3s-deployment/ryu-switch-deployment.yaml
```

## Despliegue de Ryu-OFCTL

```bash
sudo kubectl apply -f ./ryu-k3s-deployment/ryu-ofctl-deployment.yaml
```

## Despliegue de Ryu-Topology

```bash
sudo kubectl apply -f ./ryu-k3s-deployment/ryu-topology-deployment.yaml
``` 

## Despliegue de Ryu-REST

```bash
sudo kubectl apply -f ./ryu-k3s-deployment/ryu-rest-deployment.yaml
```