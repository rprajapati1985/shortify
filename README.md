Let's go through the steps to deploy shortify app to OpenShift, provided ZIP folder contains shortify application source code, Dockerfile or deployment artifacts.

---

###  Step 1: Unzip the Project

First, extract the contents of the ZIP file:

```
unzip Shortify.zip -d shortify
cd shortify
```
---

### Step 2: Review the Contents and Check what's inside:

---
There is two way to deploy application.I prefered to follow Step 3B.

### Step 3A: (If Dockerfile is present) Build and Deploy using `oc new-build`

```
oc new-build --name=shortify --binary --strategy=docker
oc start-build shortify --from-dir=. --follow
```

Once built, deploy:

```
oc new-app shortify
```

###  Step 3B: (If Dockerfile is present) To deploy this to OpenShift 

Build image, push it to image registry and Deploy thru manifest yaml

1. Build your Docker image for `shortify` service:

```
docker login docker.io -u <username> -p <password>
docker build -t docker.io/rajprajapati/shortify:latest .
--> db4a1d9642ae
Successfully tagged docker.io/rajprajapati/shortify:latest
db4a1d9642ae990969b3583f7be903d1cf894a1a6615767f87bdd64410d08b3d
```
	
2. Push Docker Images to Registry:**

```	
docker push docker.io/rajprajapati/shortify:latest
Copying config db4a1d9642 done   |
Writing manifest to image destination
docker images |grep -i db4a1d9642ae
Emulate Docker CLI using podman. Create /etc/containers/nodocker to quiet msg.
docker.io/rajprajapati/shortify                               latest        db4a1d9642ae  52 seconds ago  238 MB
```
---

### Step 4: Login to OpenShift

Make sure you're logged into the OpenShift cluster:

```
oc login --token=YOUR_TOKEN --server=https://api.your-cluster-domain:6443
```
### Step 5: Create/Open a Project

```
oc new-project shortify
```

Or switch to an existing one:

```
oc project shortify
```
---

### Step 6: Create a Kubernetes Deployment and Service YAML (`shortify-deployment.yaml` and `shortify-service.yaml`):

Apply the YAML files to deploy:

   ```
   oc apply -f shortify-deployment.yaml
   oc apply -f shortify-service.yaml
   ```

Verify deployment:

   ```
   oc get pods   # Check if the pod is running
   oc get svc    # Get the service to access your application
   ```

same way create other services manifest file and deploy.

```
oc apply -f redis-deployment.yaml
oc apply -f rabbitmq-deployment.yaml
oc apply -f postgres-deployment.yaml
```

---

### Step 7: Expose the App

```
oc expose svc/shortify
```

Then get the route:

```
oc get route shortify
```

The `HOST/PORT` output will show the accessible URL.

---

### Step 8: Test the Application

Open the route URL in a browser to confirm it's running.

---
