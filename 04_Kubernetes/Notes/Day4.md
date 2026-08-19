# Day 9 - ConfigMaps & Secrets

## 1. Why do we use ConfigMaps?

ConfigMaps are used to store non-sensitive application configuration separately from the Docker image.

If configuration is hardcoded inside the application or Docker image, every time the configuration changes, we may need to modify the application and rebuild the Docker image.

With a ConfigMap:

```text
Docker Image
     +
ConfigMap
```

The application image and its configuration are separated.

For example, if:

```text
APP_COLOR=blue
```

needs to be changed to:

```text
APP_COLOR=red
```

we can change the configuration without rebuilding the Docker image.

---

## 2. What kind of information is stored in a ConfigMap?

ConfigMaps are used for non-sensitive configuration such as:

* Application environment
* Application version
* Application color/theme
* Log level
* Host addresses
* Feature flags
* Other normal configuration values

Example:

```text
APP_ENV=development
APP_COLOR=blue
APP_VERSION=1.0
```

---

## 3. How can a ConfigMap be used by a Pod?

A ConfigMap can be provided to a container mainly in two ways:

### Environment Variables

If the application expects configuration as environment variables, the ConfigMap can be injected into the container environment.

```text
ConfigMap
    |
    ▼
Environment Variables
    |
    ▼
Container
```

Example:

```yaml
envFrom:
- configMapRef:
    name: app-config
```

We tested this using:

```bash
kubectl exec config-test -- env | grep APP_
```

and received:

```text
APP_ENV=development
APP_VERSION=1.0
APP_COLOR=blue
```

---

### Volume

If the application expects configuration as files, the ConfigMap can be mounted as a volume.

```text
ConfigMap
    |
    ▼
Volume
    |
    ▼
/etc/config/
```

Our ConfigMap:

```text
APP_COLOR=blue
APP_ENV=development
APP_VERSION=1.0
```

was mounted as:

```text
/etc/config/
├── APP_COLOR
├── APP_ENV
└── APP_VERSION
```

We verified:

```bash
kubectl exec config-volume-test -- ls /etc/config
```

and:

```bash
kubectl exec config-volume-test -- cat /etc/config/APP_COLOR
```

which returned:

```text
blue
```

---

## 4. What is a Secret?

A Secret is a Kubernetes resource used to store sensitive configuration.

Examples:

* Usernames
* Passwords
* API keys
* Tokens
* Credentials
* Certificates

Example:

```text
DB_USERNAME=admin
DB_PASSWORD=MySecurePassword123
```

---

## 5. ConfigMap vs Secret

### ConfigMap

Used for:

```text
Non-sensitive configuration
```

Examples:

```text
APP_ENV
APP_COLOR
APP_VERSION
DATABASE_HOST
LOG_LEVEL
```

### Secret

Used for:

```text
Sensitive configuration
```

Examples:

```text
DB_USERNAME
DB_PASSWORD
API_TOKEN
JWT_SECRET_KEY
```

Simple rule:

```text
ConfigMap → Configuration
Secret    → Sensitive information
```

---

## 6. How was our ConfigMap created?

We created:

```text
app-config
```

with:

```text
APP_ENV=development
APP_COLOR=blue
APP_VERSION=1.0
```

We verified it using:

```bash
kubectl describe configmap app-config
```

---

## 7. How was our Secret created?

We created:

```text
app-secret
```

with:

```text
DB_USERNAME
DB_PASSWORD
```

We verified it using:

```bash
kubectl describe secret app-secret
```

The Secret showed the keys and their sizes without displaying the actual values.

---

## 8. Secret as Environment Variables

A Secret can be injected into a container as environment variables.

Example:

```yaml
envFrom:
- secretRef:
    name: app-secret
```

The container can then access:

```text
DB_USERNAME
DB_PASSWORD
```

without hardcoding the credentials into the Docker image.

We verified that the variables existed without printing their actual values.

---

## 9. Secret as a Volume

Secrets can also be mounted as files, similar to ConfigMaps.

Example:

```text
Secret
   |
   ▼
Volume
   |
   ▼
/etc/secret/
├── DB_USERNAME
└── DB_PASSWORD
```

This is useful when an application expects credentials or certificates as files.

We mounted our Secret as:

```text
/etc/secret
```

and verified that the files existed without displaying the password.

---

## 10. ConfigMap and Secret do not manage Pods

ConfigMaps and Secrets provide configuration to Pods.

They do not perform the job of a Deployment or ReplicaSet.

The architecture is:

```text
Deployment
     |
     ▼
ReplicaSet
     |
     ▼
Pods
     |
     ├──────────────┐
     ▼              ▼
ConfigMap        Secret
     |              |
     ▼              ▼
Configuration   Sensitive data
```

---

## 11. What happens if a Pod using a ConfigMap is deleted?

If the Pod is managed by a Deployment:

```text
Pod
 X
 |
 ▼
ReplicaSet
 |
 ▼
New Pod
```

The ReplicaSet creates a new Pod to maintain the Deployment's desired state.

The new Pod will consume the ConfigMap again according to its Pod specification.

The same applies to a Secret.

---

## 12. Important difference when ConfigMap values change

Changing a ConfigMap does not automatically mean that every existing Pod will restart.

For environment variables, the container normally continues using the values it received when it started.

Therefore, if the application needs the new environment values, the Pods generally need to be restarted or rolled out again.

ConfigMaps mounted as volumes behave differently: Kubernetes can update the mounted files after the ConfigMap changes.

---

## 13. Important Security Note About Secrets

Kubernetes Secrets are intended for sensitive information, but they should not be treated as automatically secure simply because they are called Secrets.

Base64 encoding is not encryption.

For example:

```text
Secret value
    |
    ▼
Base64 encoding
```

does not make the value impossible to decode.

Proper security also depends on:

* RBAC permissions
* API access control
* Encryption at rest
* Secure cluster configuration
* Not exposing Secret values in logs or Git

Therefore, Secret values should never be unnecessarily printed or committed to Git.

---

## 14. Hands-on Work Completed

### ConfigMap

Created:

```text
app-config
```

Used it as:

```text
ConfigMap → Environment Variables
```

and:

```text
ConfigMap → Volume → Files
```

### Secret

Created:

```text
app-secret
```

Used it as:

```text
Secret → Environment Variables
```

and:

```text
Secret → Volume → Files
```

### Temporary test Pods

Created:

```text
config-test
config-volume-test
secret-test
secret-volume-test
```

Verified their behavior and then deleted them after completing the exercises.

---

## 15. Final Summary

ConfigMaps and Secrets allow configuration to be separated from the application image.

```text
                    Kubernetes
                        |
              ┌─────────┴─────────┐
              ▼                   ▼
         ConfigMap              Secret
              |                   |
       Non-sensitive          Sensitive
       configuration          information
              |                   |
              └─────────┬─────────┘
                        ▼
                       Pod
                        |
                        ▼
                    Container
```

Configuration can be provided to applications as:

```text
ConfigMap/Secret
       |
       ├── Environment Variables
       |
       └── Volumes / Files
```

The main idea is:

> Keep application code and configuration separate, and keep sensitive information in Secrets instead of hardcoding it into the application or Docker image.
