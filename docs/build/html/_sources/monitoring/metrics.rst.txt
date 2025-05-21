=======
Metrics
=======

Performance metrics collected and monitored in the AI-GUARD system.

System Metrics
============

Infrastructure Metrics
--------------------

- **CPU Usage**: Per service and system-wide
- **Memory Usage**: Resident and virtual memory per service
- **Disk Usage**: Storage consumption and I/O operations
- **Network Traffic**: Throughput, packet loss, latency
- **Database Performance**: Query time, connection count, cache hit ratio

Application Metrics
-----------------

- **Request Rate**: Requests per second for each API endpoint
- **Response Time**: P50, P90, P99 latencies
- **Error Rate**: Failed requests percentage
- **Concurrent Users**: Active sessions
- **Queue Depths**: Background task queue status

Business Metrics
==============

Recognition Metrics
-----------------

- **Recognition Accuracy**: True/false positive and negative rates
- **Average Confidence Score**: For successful recognitions
- **Processing Time**: Detection and recognition latency
- **Throughput**: Faces processed per second
- **Threshold Distribution**: Recognition scores distribution

Access Metrics
------------

- **Access Attempts**: Total, successful, and failed
- **Peak Access Times**: Busiest hours and days
- **Location Distribution**: Access attempts by location
- **Enrollment Quality**: Average quality scores of enrollments
- **Alert Rate**: Number of alerts generated

Monitoring Dashboard
==================

.. figure:: /_static/monitoring-dashboard.png
   :alt: Monitoring Dashboard
   :align: center
   
   Sample monitoring dashboard showing key metrics

The system provides a Grafana dashboard with:

- Real-time metric visualization
- Historical data analysis
- Alert status
- System health indicators
- Custom query capabilities

Prometheus Configuration
=====================

The system uses Prometheus for metrics collection:

.. code-block:: yaml
   :caption: Sample Prometheus configuration

   global:
     scrape_interval: 15s
     evaluation_interval: 15s

   scrape_configs:
     - job_name: 'api-service'
       metrics_path: '/metrics'
       static_configs:
         - targets: ['api-service:8000']

     - job_name: 'recognition-service'
       metrics_path: '/metrics'
       static_configs:
         - targets: ['recognition-service:8000']

     - job_name: 'node-exporter'
       static_configs:
         - targets: ['node-exporter:9100']

     - job_name: 'postgres-exporter'
       static_configs:
         - targets: ['postgres-exporter:9187']