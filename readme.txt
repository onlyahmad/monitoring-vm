       ✅ Monitoring VM Sederhana — Selesai!
    
    Project: /home/ion/Project/monitoring-vm/
    
    Stack Berjalan
    
    | Service       | Container        | Port  | Status |
    |---------------|------------------|-------|--------|
    | Prometheus    | prometheus-vm    | :9092 | ✅ Up  |
    | Grafana       | grafana-vm       | :3001 | ✅ Up  |
    | Node Exporter | node-exporter-vm | :9101 | ✅ Up  |
    | cAdvisor      | cadvisor-vm      | :8082 | ✅ Up  |
    
    Akses
    
    - Grafana: http://localhost:3001 — user admin, password admin123
    - Prometheus: http://localhost:9092/targets
    - cAdvisor: http://localhost:8082
    - Node Exporter: http://localhost:9101/metrics
    
    Dashboard sudah auto-provision

   Buka Grafana → Dashboards → pilih VM Monitoring — langsung ada panel:
    - CPU Usage (stat + timeseries)
    - Memory Usage (stat + timeseries)
    - Disk Usage (/)
    - Load Average (1/5/15m)
    - Network I/O
    
    Tambah VM Lain
    
    Di VM target, jalankan Node Exporter:
    
    bash
    podman run -d --name node-exporter \
      --net host \
      --pid host \
      -v /proc:/host/proc:ro \
      -v /sys:/host/sys:ro \
      -v /:/rootfs:ro \
      docker.io/prom/node-exporter:latest \
      --path.procfs=/host/proc \
      --path.sysfs=/host/sys \
      --path.rootfs=/rootfs
    
    
    Lalu edit /home/ion/Project/monitoring-vm/prometheus/prometheus.yml — ganti 192.168.1.100:9100 dengan IP VM target:
    
    yaml
      - job_name: "node-exporter-vm-lain"
        static_configs:
          - targets: ["IP_VM_LAIN:9100"]
    
    
    Kemudian restart Prometheus:
    
    bash
    cd /home/ion/Project/monitoring-vm && podman-compose restart prometheus
    
    
    Dalam 15 detik data VM lain langsung masuk ke dashboard Grafana.
