# Sistema Distribuido de Asignación de Recursos Académicos

Este proyecto implementa un sistema distribuido para asignar **salones**, **laboratorios** y **aulas móviles** a diferentes programas académicos agrupados por facultades. Utiliza comunicación basada en **ZeroMQ**, tolerancia a fallos mediante un **servidor réplica**, y captura de **métricas de rendimiento** para análisis y validación del sistema.

---

## 📦 Componentes del Sistema

- `ServidorPrincipal.py`: Atiende solicitudes de facultades y realiza las asignaciones.
- `ServidorReplica.py`: Servidor de respaldo activado automáticamente si falla el principal.
- `HealthCheck.py`: Supervisa el servidor principal y activa el réplica si no responde.
- `Facultad.py`: Agrupa solicitudes de programas y las reenvía al servidor.
- `Programa.py`: Envia su solicitud de recursos a su facultad correspondiente.
- `Lanzador.py`: Orquestador que lanza múltiples facultades y programas desde una sola máquina.

---

## ⚙️ Requisitos

- Python 3.8 o superior.
- Dependencias:
  - `pyzmq`
  - `psutil`

Instalación de dependencias:

```bash
pip install pyzmq psutil
🖥️ Despliegue en 3 Máquinas
🟢 Máquina 1: Servidor Principal
IP sugerida: 10.43.103.220

Inicia el servidor:

bash
Copiar
Editar
python3 ServidorPrincipal.py
Este servidor se queda escuchando solicitudes entrantes de las facultades a través del puerto 5555.

🔁 Máquina 2: Health Checker + Servidor Réplica
IP sugerida del servidor réplica: 10.43.96.9

Inicia el script de monitoreo:

bash
Copiar
Editar
python3 HealthCheck.py
Este script intenta hacer ping al servidor principal periódicamente. Si detecta 3 fallos consecutivos, lanza el ServidorReplica.py en el mismo host.

🚀 Máquina 3: Lanzador de Facultades y Programas
Inicia el lanzador con el número de facultades y los recursos a solicitar:

bash
Copiar
Editar
python3 Lanzador.py <num_facultades> <salones_por_programa> <laboratorios_por_programa>
Ejemplo:

bash
Copiar
Editar
python3 Lanzador.py 5 6 3
Esto lanzará:

5 facultades (Facultad_1 a Facultad_5), cada una escuchando en puertos consecutivos desde el 7000.

Cada facultad genera 5 programas que se comunican por su puerto correspondiente.

Cada programa solicita 6 salones y 3 laboratorios.

📊 Métricas y Logs
Cada componente genera archivos de log para seguimiento y análisis de rendimiento:

Componente	Métricas Capturadas	Archivo generado
Facultad.py	Latencia, CPU, RAM, resultado por programa	log_<nombre_facultad>.txt
HealthCheck.py	Tiempo de ping, fallos, activación de réplica	log_healthcheck.txt
ServidorPrincipal.py	Tiempo de respuesta, CPU, RAM, asignaciones	log_metricas_servidor.txt + log_asignaciones.json
ServidorReplica.py	Métricas similares a principal	log_metricas_replica.txt + log_asignaciones_replica.json
Lanzador.py	PID, tiempos de ejecución de procesos	log_superlanzador.txt

📁 Estructura del Proyecto
pgsql
Copiar
Editar
.
├── Facultad.py
├── Programa.py
├── ServidorPrincipal.py
├── ServidorReplica.py
├── HealthCheck.py
├── Lanzador.py
├── log_asignaciones.json
├── log_asignaciones_replica.json
├── log_metricas_servidor.txt
├── log_metricas_replica.txt
├── log_healthcheck.txt
├── log_superlanzador.txt
├── log_<nombre_facultad>.txt
└── README.md
🧪 Ejecución de Pruebas
El sistema ha sido probado con 5, 7 y hasta 10 facultades.

Cada facultad puede gestionar múltiples programas que solicitan diferentes cantidades de recursos.

La tolerancia a fallos ha sido validada mediante la desconexión manual del servidor principal.

📌 Consideraciones
Asegúrate de que las máquinas involucradas pueden comunicarse por los puertos usados (5555, 5556, 7000+).

Todos los scripts deben estar sincronizados en todas las máquinas involucradas.

Puedes agregar seguridad adicional mediante autenticación o validación IP si lo deseas.

📈 Extensiones recomendadas
Analizador automático de logs para generar CSV o gráficas.

Dashboard en tiempo real para monitorear métricas.

Autenticación entre nodos (opcional).

Desarrollado como parte del Proyecto Final — Semestre 2025-10
