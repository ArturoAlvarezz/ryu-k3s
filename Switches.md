✅ SERVICIO RYU-CORE CONFIGURADO COMO NODEPORT

📍 INFORMACIÓN DE CONEXIÓN:
   - Controlador IP: 192.168.122.100 (o cualquier nodo del cluster)
   - Puerto OpenFlow: 30266
   - Protocolo: tcp
   - Estado: ✅ ACCESIBLE Y ESCUCHANDO

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

🔧 EJEMPLOS DE CONFIGURACIÓN:

1️⃣  OPEN VSWITCH (OVS):
    sudo ovs-vsctl set-controller br0 tcp:192.168.122.100:30266
    sudo ovs-vsctl set bridge br0 protocols=OpenFlow13

2️⃣  MANUAL (cualquier switch OpenFlow):
    Controlador: tcp:192.168.122.100:30266
    Versión OpenFlow: 1.3

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

📊 IPs ALTERNATIVAS (puedes usar cualquiera):
   • Maestro:  192.168.122.100:30266
   • Worker1: 192.168.122.212:30266
   • Worker2: 192.168.122.51:30266
   • Worker3: 192.168.122.46:30266

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

🔍 VERIFICAR CONEXIONES:
    # Ver logs del controlador
    sudo kubectl logs -n ryu -l app=ryu-core -f

    # Ver switches conectados (desde el pod)
    sudo kubectl exec -n ryu -it <pod-name> -- ryu-manager --help

🌐 ACCESO A INTERFACES:
    
    • GUI Topología (con switches conectados):
      - Maestro:  http://192.168.122.100:31699 ✅ FUNCIONAL
      - Worker1: http://192.168.122.212:31699
      - Worker2: http://192.168.122.51:31699
      - Worker3: http://192.168.122.46:31699
      Nota: El GUI está integrado en ryu-core y muestra switches conectados
    
    • REST API (disponible en TODOS los nodos):
      - Cualquier nodo: http://<IP-del-nodo>:31428
      - Endpoints: /stats/switches, /stats/flow/<dpid>, etc.
    
    • Consultas de Topología:
      - Switches: http://192.168.122.100:31699/v1.0/topology/switches
      - Enlaces: http://192.168.122.100:31699/v1.0/topology/links

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

✨ El controlador Ryu está listo para recibir conexiones OpenFlow!