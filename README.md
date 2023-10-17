# prom-gf-remote-write

A prometheus and grafana stack for receiving remote write metrics and showing the metrics on dashboard.

## Usage

To create a prometheus receiver with basic auth, we should define at least one username and password in `web.yml`.

We can use the following python script to create password.

```python
import getpass
import bcrypt

password = getpass.getpass("password: ")
hashed_password = bcrypt.hashpw(password.encode("utf-8"), bcrypt.gensalt())
print(hashed_password.decode())
```

The `web.yml` should be like the following.

```yaml
basic_auth_users:
  username: encoded_password
```

After stack launched, we should be able to access Grafana and Prometheus from local.

```sh
docker-compose up -d

# Grafana
curl -X POST -H "Content-Type: application/json" -d '{"user":"admin", "password":"admin"}' http://localhost:3000/login

# Prometheus
curl -u admin:adminpass http://localhost:9090/metrics
```

Then create a tunnel for the Prometheus port 9090 using ngrok.

```sh
ngrok http 9090
# output: https://xxxxxx.ngrok.io
```

Enable remote write feature from another Prometheus instance, and send the metrics to the local.

```
POST https://xxxxxx.ngrok.io/api/v1/write
```

To visualize the metrics, we can use Grafana to import pre-built dashboard.

https://grafana.com/grafana/dashboards/13332-kube-state-metrics-v2/

That's all.

Now we have a comprehensive dashboard and the metrics are sent from the remote Prometheus.
