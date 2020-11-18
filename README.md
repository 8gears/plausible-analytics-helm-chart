# Plausible Analytics Helm Chart for Kubernetes
 
[Plausible Analytics][] is a Simple, lightweight privacy-friendly website analytics  alternative to Google Analytics.
 

## Install Chart

```shell script
helm repo add 8gears https://8gears.container-registry.com/chartrepo/library
helm repo update

# Helm 3
$ helm install [RELEASE_NAME] 8gears/plausible-analytics
```
 
Source Code for this Helm Chart is located at: [8gears/plausible-analytics-helm-chart][https://github.com/8gears/plausible-analytics-helm-chart] 
 

View Chart in 8gears Chart Museum: [8gears.container-registry.com/harbor/projects/1/helm-charts/plausible-analytics/versions/0.1.0][https://8gears.container-registry.com/harbor/projects/1/helm-charts/plausible-analytics/versions/0.1.0] 


## Requirements

Before you start make sure you have the following dependencies ready and working: 

- Helm > 3
- Postgres DB
- ClickHouse (Recommended Helm Chart -> [liwenhe1993/charts][])
- Helmfile (Optional)

## Configuration

Plausible specific values.
The shown values represent defaults and comments provide a better description if needed. 

```yaml

# Plausible specific values

disableAuth: false # Disables authentication completely, no registration, login will be shown.
disableRegistration: false # Disables registration of new users, keep your admin credentials handy ;)
baseURL: # The hosting url of the server, used for URL generation. In production systems, this should be your ingress host.

adminUser:
  email:  # Admin user's email
  name:   # Display name admin user
  password: # Admin user password

# SECRET_KEY_BASE is Helm randAlphaNum 90

database: # Postgres Database
  enabled: true
  url: # The URL to the Postgres Database Connection String see -> https://www.postgresql.org/docs/current/libpq-connect.html#LIBPQ-CONNSTRING

clickhouse: # Clickhouse Database
  enabled: true
  url: # The URL Connection String to clickhouse DB see -> https://clickhouse.tech/docs/en/interfaces/http/

smtp: # Plausible uses and SMTP server to send transactional emails e.g. account activation, password reset, weekly reports, etc.
  enabled: true # Enable/Disable SMTP functionality
  mailer:
    emailAddress:
    adapter:
  host: # The host address of your smtp server.
  port: # The port of your smtp server.
  username: # The username/email in case SMTP auth is enabled.
  password: # The password in case SMTP auth is enabled.
  ssl:
    enabled: false # If SSL is enabled for SMTP connection
  retries: 2 # Number of retries to make until mailer gives up.

postmark: #Alternatively, you can use Postmark to send transactional emails. In this case, use the following parameters:
  apiKey:

geoliteCountryDB:

# Google Search Integration
# See: https://docs.plausible.io/self-hosting-configuration#google-search-integration
google:
  clientID: # The Client ID from the Google API Console for your Plausible Analytics project
  clientSecret: # The Client Secret from the Google API Console for your Plausible Analytics project
#Twitter Integration
# https://docs.plausible.io/self-hosting-configuration#twitter-integration
twitter:
  consumer:
    key: # The API key from the Twitter Developer Portal
    secret: # The API key secret from the Twitter Developer Portal
  access:
    token: # The access token you generated in the steps above
    secret: # The access token secret you generated in the steps above

labels: {} # Extra Labels to apply on your k8s deployment
extraEnv: [] # Extra Env Variables that are passed down to plausible 1:1

```

This Part of the represents the common Kubernetes specific settings. 

```yaml
replicaCount: 1

image:
  repository: plausible/analytics
  pullPolicy: IfNotPresent
  tag: # Overrides the image tag whose. default: is the chart appVersion.

imagePullSecrets: []
nameOverride: ""
fullnameOverride: ""

serviceAccount:
  # Specifies whether a service account should be created
  create: true
  # Annotations to add to the service account
  annotations: {}
  # The name of the service account to use.
  # If not set and create is true, a name is generated using the fullname template
  name: ""

podAnnotations: {}

podSecurityContext: {}
  # fsGroup: 2000

securityContext: {}
  # capabilities:
  #   drop:
  #   - ALL
  # readOnlyRootFilesystem: true
  # runAsNonRoot: true
  # runAsUser: 1000

service:
  type: ClusterIP
  port: 80

ingress:
  enabled: false
  annotations: {}
    # kubernetes.io/ingress.class: nginx
    # kubernetes.io/tls-acme: "true"
  hosts:
    - host: chart-example.local
      paths: []
  tls: []
  #  - secretName: chart-example-tls
  #    hosts:
  #      - chart-example.local

resources: {}
  # We usually recommend not to specify default resources and to leave this as a conscious
  # choice for the user. This also increases chances charts run on environments with little
  # resources, such as Minikube. If you do want to specify resources, uncomment the following
  # lines, adjust them as necessary, and remove the curly braces after 'resources:'.
  # limits:
  #   cpu: 100m
  #   memory: 128Mi
  # requests:
  #   cpu: 100m
  #   memory: 128Mi

autoscaling:
  enabled: false
  minReplicas: 1
  maxReplicas: 100
  targetCPUUtilizationPercentage: 80
  # targetMemoryUtilizationPercentage: 80

nodeSelector: {}

tolerations: []

affinity: {}
```

## Helmfile Example

This repository also contains a complete example using Helmfile.

### Helmfile Content

- Postgres 
- Clickhouse
- Plausible

```shell script

helmfile apply

```

See [https://github.com/8gears/plausible-analytics-helm-chart/blob/main/helmfile.yaml] 



## Chart Deployment


```shell script

helm repo add  --username='robot$xxxx' --password="xxx" 8gears https://8gears.container-registry.com/chartrepo/library 
helm push --username='robot$helmcli' --password="$PASSWD" . 8gears

```



[Plausible Analytics]: https://github.com/plausible/analytics
[liwenhe1993/charts]: https://github.com/liwenhe1993/charts
