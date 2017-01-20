# JMX Exporter

* [https://github.com/prometheus/jmx_exporter]()
* [https://www.robustperception.io/monitoring-kafka-with-prometheus/]()
* [https://hub.docker.com/r/yagni/docker-jmx-exporter/]()

Clone project -> mvn clean package

### Configuration

If connecting to JVM rather than running as Java agent, will need to expose JMX

	-Dcom.sun.management.jmxremote.port=5555
	-Dcom.sun.management.jmxremote.authenticate=false
	-Dcom.sun.management.jmxremote.ssl=false

Then can edit sample.yml to point to host (if not using example 5555, or auth etc)


### Loading into Prometheus

Just configure the prometheus.yml

	global:
	 scrape_interval: 10s
	 evaluation_interval: 10s
	scrape_configs:
	 - job_name: 'jmx'
	   static_configs:
	    - targets:
	      - localhost:5556

Running prometheus

	docker run -p 9090:9090 -v $PWD/prometheus.yml:/etc/prometheus/prometheus.yml prom/prometheus

### Viewing in Grafana

https://github.com/grafana/grafana-docker
https://stash.scansafe.cisco.com/projects/DWTEAM/repos/dw-docker-grafana/browse

	# admin/admin as credentials
	docker run -d --name=grafana -p 3000:3000 grafana/grafana

