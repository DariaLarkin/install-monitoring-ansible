Prometheus and Grafana using Ansible.

Grafana setup steps are mentioned below:
1. Install and configure Grafana
2. Add Prometheus data source to Grafana
3. Creating dashboards from Prometheus metrics.

setup Grafana using Ansible:
- name: Install grafana
  yum:
    name: https://s3-us-west-2.amazonaws.com/grafana-releases/release/grafana-{{ version }}-1.x86_64.rpm
    state: latest
    
 Once Grafana is installed, we are going to copy Grafana config file 
 to its required destination and restart Grafana server:
 - name: "Grafana configuration file copy"
  template:
    src: "grafana.conf.j2"
    dest: /etc/grafana/grafana.ini
  notify: event_restart_grafana
- name: "Grafana server started"
  service:
    name: grafana-server
    enabled: true
    state: started
    
 When everything is ready, we verify that Grafana is accessible on port 3000 which is the default port of Grafana:
 - name: "Check if Grafana is accessible."
  uri:
    url: http://127.0.0.1:3000
    method: GET
    status_code: 200
    
1. Go to http://grafanaIp:3000    
2. Enter the username “admin” and password “admin”. Click Log In. Then provide a new password which will be used in future login attempts.
3. Click “Data Sources” on the screen.
4. Look for Prometheus data source for our configuration.
5. Click Prometheus data source and provide below details:
Name: Prometheus
URL: http://prometheusIp:9090
Basic Auth: Disabled as currently disabled on Prometheus server
6. Click Add and Test Connection to verify everything is working properly. Once done, we have a Prometheus data source ready to use.


Once we have a data source set up completed. 
Next, we need to set up our dashboard using Prometheus data source which can be done using the below steps:
1. Go to the dashboard option using “+” on the top left
2. Add a graph in this new dashboard by clicking the “Graph” option
3. We have an empty graph created in our dashboard which needs query and data source. This can be done by clicking on “Panel Title” and select Edit option
4. Select “Prometheus” as a data source. Go to query option and add Enter PromQL query “up{job=’prometheus’}” in the “Query” field which will draw a graph which shows “up” state of Prometheus server. We can add any query here based on our requirement. PromQL is a query language used with Prometheus.
5. Once everything is done, we need to save our dashboard for future usage. This can be done by clicking the save button on top right and give our dashboard a name.
