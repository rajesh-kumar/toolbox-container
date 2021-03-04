# Toolbox Introduction
Ephemeral Container bundled with various tools for local debugging, builds and investigating networking issues on K8S.

[![CI](https://github.com/rajesh-kumar/toolbox-container/workflows/CI/badge.svg)](https://github.com/rajesh-kumar/toolbox-container/actions?query=branch%3Amaster)
[![Release](https://github.com/rajesh-kumar/toolbox-container/workflows/Release/badge.svg)](https://github.com/rajesh-kumar/toolbox-container/actions?query=branch%3Amaster)


# Getting Started

## Run tools on your current working directory
### MAC

```shell script
alias toolbox="docker run --rm --net='host' -it -v <path_to_k8s_config_dir>/.kube:/home/tool/kube-config:rw -v $(pwd):/home/tool/workplace:rw raju2210/toolbox"
```
If you don't want to type the command for every new terminal you open, you can add the command to your `.bashrc` on mac or `.zshrc`.

```shell script
toolbox mvn clean package
```

### WINDOWS(cmd)

```bash script
set "toolbox=docker run --rm --net='host' -it -v <path_to_k8s_config_dir>/.kube:/home/tool/kube-config -v %cd%:/home/tool/workplace raju2210/toolbox"

toolbox nslookup google.com
```

### WINDOWS(powershell)

```powershell script
notepad $profile
function toolboxFn {
    docker run --rm --net='host' -it -v <path_to_k8s_config_dir>/.kube:/home/tool/kube-config -v ${PWD}:/home/tool/workplace raju2210/toolbox
}
Set-Alias toolbox toolboxFn

toolbox nslookup google.com
```

## Run and attach to the network namespace of the container to debug
```shell script
docker run --name toolbox --net=container:${container_id_to_debug} -itd raju2210/toolbox:latest
```

## Run it in kubernetes as Ephemeral Container

At times you need to debug build issues within k8s when your CI jobs fail (using k8s as build platform) or troubleshoot failing applications containers using distroless images  

```shell script
kubectl debug -it target-pod --image=raju2210/toolbox --target=target-pod
```

```shell script
#  To view the details of Ephemeral container
kubectl describe pod target-pod | grep Ephemeral
```

Make sure [EphemeralContainers feature](https://kubernetes.io/docs/reference/command-line-tools-reference/feature-gates/) is enabled in your cluster.

For more details, [refer here](https://kubernetes.io/docs/tasks/debug-application-cluster/debug-running-pod/#ephemeral-container)

Note: Do not use this image as base for writing git hub actions or any other application.

## Commit Message Conventions

Use [conventional commit message format](https://www.conventionalcommits.org/en/v1.0.0/)

## Maven versioning

[Ci Friendly](https://maven.apache.org/maven-ci-friendly.html)
