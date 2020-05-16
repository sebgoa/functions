# Continuous Delivery of Functions with Google Container Builder and kubeless on GKE

## Setup





```
ion

```
cat <<



```
steps:
- name: 'gcr.io/runseb/kubeless'
  entrypoint: 'bash'
  args:
  - '-c'
  - |
    /builder/kubeless.bash function ls
    kubeless function deploy foo --runtime=python3.6 --handler=foo.handler --from-file=foo.py --dry-run > function.yaml
  env:
  - 'CLOUDSDK_COMPUTE_ZONE=us-central1-a'
  - 'CLOUDSDK_CONTAINER_CLUSTER=geek-demo'
```

Second, we run `kubectl apply` via container builder to declaratively apply our function changes to our GKE cluster. Like so:

```
- name: 'gcr.io/cloud-builders/kubectl'
  args: ['apply', '-f', 'function.yaml']
  env:
...
```

NOTE: Replace the ZONE and cluster NAME with yours.

### Define the build trigger

![](./images/triggers.png)

Once you will make commits to your code, container builder will run and you will see every build history:

![](./images/builds.png)


### Add a function route

Now that your function is deployed continuously, you want to be able to do the same with the function trigger. In this example, we create an http trigger using the `kubeless` CLI again.


```
- name: 'gcr.io/runseb/kubeless'
  entrypoint: 'bash'
  args:
  - '-c'
  - |
    /builder/kubeless.bash function ls
    kubeless trigger http create foo --hostname func.kubeless.sh --function-name foo --dry-run > trigger.yaml
...
```



Here again, the manifest for the HTTP trigger is generated by the `kubeless` CLI and stored in the container builder workspace. You can then _apply_ it to your GKE cluster.

```
- name: 'gcr.io/cloud-builders/kubectl'
  args: ['apply', '-f', 'trigger.yaml']
...
```

And now enjoy modifying your function and seeing the continuous delivery.

_GitOps_ with Google container builder, GKE and kubeless.
