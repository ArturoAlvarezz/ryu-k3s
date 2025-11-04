# Comandos para subir imagenes

## Ryu Core

```bash
docker build -f Dockerfile.ryu-core -t arturoalvarez/ryu-core:latest .
docker push arturoalvarez/ryu-core:latest
```

## Ryu REST API

```bash
docker build -f Dockerfile.ryu-rest -t arturoalvarez/ryu-rest:3.0 .
docker push arturoalvarez/ryu-rest:3.0
```

## Ryu GUI Topology

```bash
docker build -f Dockerfile.ryu-gui -t arturoalvarez/ryu-gui:1.0 .
docker push arturoalvarez/ryu-gui:1.0
```
