╔═══════════════════════════════════════════════════════════════════════════════╗
║              RYU SDN CONTROLLER - ARQUITECTURA DE MICROSERVICIOS             ║
║                    ✅ TOTALMENTE DISTRIBUIDO EN K3S                           ║
╚═══════════════════════════════════════════════════════════════════════════════╝

📦 ARQUITECTURA IMPLEMENTADA:
   • 4 pods Ryu-Switch (OpenFlow Controller) - Distribuidos en 4 nodos
   • 4 pods Ryu-Ofctl (REST API)            - Distribuidos en 4 nodos
   • 4 pods Ryu-REST (REST API Alt)         - Distribuidos en 4 nodos
   • 4 pods Ryu-Topology (GUI Web)          - Distribuidos en 4 nodos
   • 1 pod Redis (Estado compartido)        - Maestro
   ────────────────────────────────────────────────────────────────
   TOTAL: 17 pods funcionando en alta disponibilidad

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

🔌 CONEXIÓN OPENFLOW (RYU-SWITCH SERVICE)

📍 INFORMACIÓN DE CONEXIÓN:
   - Controlador IP: 192.168.122.100 (o cualquier nodo del cluster)
   - Puerto OpenFlow: 30266
   - Protocolo: tcp
   - Estado: ✅ 4 RÉPLICAS ACTIVAS CON BALANCEO DE CARGA
   - Sticky Sessions: ✅ Habilitado (1 hora)

🔧 EJEMPLOS DE CONFIGURACIÓN:

1️⃣  OPEN VSWITCH (OVS):

    ip addr add 192.168.122.10/24 dev br0
    ip link set br0 up
    ovs-vsctl set-controller br0 tcp:192.168.122.100:30266
    ovs-vsctl set bridge br0 protocols=OpenFlow13
    ovs-vsctl list controller
    ovs-vsctl get controller br0 is_connected
    
    # Verificar conexión
    ovs-vsctl show
    ovs-ofctl -O OpenFlow13 dump-flows br0
    
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

📊 IPs DISPONIBLES (puedes usar cualquiera con sticky sessions):
   • Maestro:  192.168.122.100:30266 ⭐
   • Worker1: 192.168.122.212:30266
   • Worker2: 192.168.122.51:30266
   • Worker3: 192.168.122.46:30266

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

🌐 SERVICIOS WEB DISPONIBLES

1️⃣  REST API (RYU-OFCTL) - Puerto 30880
    📡 URL Base: http://192.168.122.100:30880
    
    Endpoints principales:
    • Listar switches:     GET  /stats/switches
    • Descripción switch:  GET  /stats/desc/<dpid>
    • Ver flujos:          GET  /stats/flow/<dpid>
    • Ver puertos:         GET  /stats/port/<dpid>
    • Agregar flujo:       POST /stats/flowentry/add
    • Eliminar flujo:      POST /stats/flowentry/delete
    • Limpiar flujos:      DELETE /stats/flowentry/clear/<dpid>
    
    Ejemplos:
    curl http://192.168.122.100:30880/stats/switches
    curl http://192.168.122.100:30880/stats/flow/1
    
    IPs disponibles en todos los nodos:
    • http://192.168.122.100:30880  (Maestro)
    • http://192.168.122.212:30880  (Worker1)
    • http://192.168.122.51:30880   (Worker2)
    • http://192.168.122.46:30880   (Worker3)

2️⃣  GUI TOPOLOGY (RYU-TOPOLOGY) - Puerto 30800
    🎨 URL: http://192.168.122.100:30800
    
    Características:
    • Visualización gráfica de la topología en tiempo real
    • Switches y enlaces detectados automáticamente
    • WebSocket para actualizaciones en vivo
    
    Endpoints de topología:
    • Switches: /v1.0/topology/switches
    • Enlaces:  /v1.0/topology/links
    • Hosts:    /v1.0/topology/hosts
    
    Acceso desde cualquier nodo:
    • http://192.168.122.100:30800  (Maestro) ⭐
    • http://192.168.122.212:30800  (Worker1)
    • http://192.168.122.51:30800   (Worker2)
    • http://192.168.122.46:30800   (Worker3)

3️⃣  REST API ALTERNATIVA (RYU-REST) - Puerto 31693
    📊 URL: http://192.168.122.100:31693
    (Compatible con ofctl_rest)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

🔍 VERIFICACIÓN Y MONITOREO

📋 Ver estado de todos los pods:
    sudo kubectl get pods -n ryu -o wide

📊 Ver pods por aplicación:
    sudo kubectl get pods -n ryu -l app=ryu-switch
    sudo kubectl get pods -n ryu -l app=ryu-ofctl
    sudo kubectl get pods -n ryu -l app=ryu-topology

📝 Ver logs en tiempo real:
    # Logs de controladores OpenFlow
    sudo kubectl logs -n ryu -l app=ryu-switch -f
    
    # Logs de REST API
    sudo kubectl logs -n ryu -l app=ryu-ofctl -f
    
    # Logs de Topology GUI
    sudo kubectl logs -n ryu -l app=ryu-topology -f
    
    # Logs de un pod específico
    sudo kubectl logs -n ryu <pod-name> -f

🔧 Acceso interactivo a un pod:
    sudo kubectl exec -n ryu -it <pod-name> -- sh

📈 Ver uso de recursos:
    sudo kubectl top pods -n ryu

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

⚙️  ESCALADO INDEPENDIENTE

Cada aplicación puede escalarse de forma independiente:

# Escalar controladores OpenFlow (más switches concurrentes)
sudo kubectl scale deployment/ryu-switch -n ryu --replicas=6

# Escalar REST API (más tráfico HTTP)
sudo kubectl scale deployment/ryu-ofctl -n ryu --replicas=8

# Escalar GUI (más usuarios concurrentes)
sudo kubectl scale deployment/ryu-topology -n ryu --replicas=5

# Ver réplicas actuales
sudo kubectl get deployments -n ryu

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

🧪 PRUEBAS DE ALTA DISPONIBILIDAD

1️⃣  Test de failover (eliminar un pod):
    POD=$(sudo kubectl get pods -n ryu -l app=ryu-switch -o jsonpath='{.items[0].metadata.name}')
    sudo kubectl delete pod -n ryu $POD
    # El pod se recrea automáticamente

2️⃣  Test de conectividad (mientras se reinicia un pod):
    while true; do 
      curl -s http://localhost:30880/stats/switches
      sleep 2
    done

3️⃣  Simular fallo de nodo:
    # Los pods se redistribuyen automáticamente

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

📚 EJEMPLO COMPLETO DE USO

# 1. Verificar que todos los pods estén corriendo
sudo kubectl get pods -n ryu

# 2. Crear un switch virtual (Open vSwitch)
sudo ovs-vsctl add-br br0
sudo ovs-vsctl set-controller br0 tcp:192.168.122.100:30266
sudo ovs-vsctl set bridge br0 protocols=OpenFlow13

# 3. Verificar conexión en los logs
sudo kubectl logs -n ryu -l app=ryu-switch --tail=20 | grep "switch connected"

# 4. Ver switches conectados via API
curl http://192.168.122.100:30880/stats/switches

# 5. Ver topología en el navegador
firefox http://192.168.122.100:30800/

# 6. Agregar puertos al switch
sudo ovs-vsctl add-port br0 veth0
sudo ovs-vsctl add-port br0 veth1

# 7. Ver flujos instalados
curl http://192.168.122.100:30880/stats/flow/1

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

💡 VENTAJAS DE ESTA ARQUITECTURA

✅ Alta Disponibilidad:    4 réplicas de cada servicio
✅ Distribución Total:     Pods en todos los nodos del cluster
✅ Escalado Independiente: Escala solo lo que necesites
✅ Balanceo de Carga:      Tráfico distribuido automáticamente
✅ Sticky Sessions:        Conexiones OpenFlow persistentes
✅ Failover Automático:    Si cae un pod, se recrea automáticamente
✅ Estado Compartido:      Redis sincroniza tabla MAC entre controladores
✅ Aislamiento de Fallos:  Si falla el GUI, el controlador sigue funcionando

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

🎯 ARQUITECTURA DE MICROSERVICIOS

┌─────────────────────────────────────────────────────────────────┐
│                      K3s Cluster (4 nodos)                      │
│                                                                   │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐          │
│  │   Maestro    │  │   Worker1    │  │   Worker2    │          │
│  │              │  │              │  │              │          │
│  │ redis-0      │  │ switch-2     │  │ switch-3     │          │
│  │ switch-1     │  │ ofctl-2      │  │ ofctl-3      │          │
│  │ ofctl-1      │  │ topology-2   │  │ topology-3   │          │
│  │ topology-1   │  │ rest-2       │  │ rest-3       │          │
│  │ rest-1       │  │              │  │              │          │
│  └──────────────┘  └──────────────┘  └──────────────┘          │
│         │                 │                 │                    │
│         └─────────────────┼─────────────────┘                    │
│                           │                                      │
│                    ┌──────▼──────┐                               │
│                    │    Redis     │                               │
│                    │ (Shared MAC  │                               │
│                    │   Tables)    │                               │
│                    └──────────────┘                               │
└─────────────────────────────────────────────────────────────────┘
                           │
                           │ OpenFlow (6633)
                           │ REST API (8080)
                           │
                ┌──────────▼──────────┐
                │   OpenFlow Switch    │
                │      (OVS/HW)        │
                └─────────────────────┘

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

✨ El controlador Ryu está listo en arquitectura de microservicios distribuida!
   Compatible con OpenFlow 1.3 y completamente escalable en K3s!
