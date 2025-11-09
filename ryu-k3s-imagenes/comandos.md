# Comandos para subir imagenes

## Ryu OFCTL

```bash
docker build -f ./ryu-k3s-imagenes/Dockerfile.ryu-ofctl -t arturoalvarez/ryu-ofctl:5.0 .
docker push arturoalvarez/ryu-ofctl:5.0
```

## Ryu REST

```bash
docker build -f ./ryu-k3s-imagenes/Dockerfile.ryu-rest -t arturoalvarez/ryu-rest:5.0 .
docker push arturoalvarez/ryu-rest:5.0
```
## Ryu SWITCH

```bash
docker build -f ./ryu-k3s-imagenes/Dockerfile.ryu-switch -t arturoalvarez/ryu-switch:5.0 .
docker push arturoalvarez/ryu-switch:5.0
```

## Ryu TOPOLOGY

```bash
docker build -f ./ryu-k3s-imagenes/Dockerfile.ryu-topology -t arturoalvarez/ryu-topology:5.0 .
docker push arturoalvarez/ryu-topology:5.0
```
