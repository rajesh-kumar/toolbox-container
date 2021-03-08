# Toolbox Introduction
Ephemeral Container bundled with various tools for local debugging, builds and investigating networking issues on K8S.

[![CI](https://github.com/rajesh-kumar/toolbox-container/workflows/CI/badge.svg)](https://github.com/rajesh-kumar/toolbox-container/actions?query=branch%3Amaster)
[![Release](https://github.com/rajesh-kumar/toolbox-container/workflows/Release/badge.svg)](https://github.com/rajesh-kumar/toolbox-container/actions?query=branch%3Amaster)


## Usage

### Run tools on your current working directory

#### MAC

```shell script
alias toolbox="docker run --rm --net='host' -it -v $HOME/.kube:/home/tool/.kube-config:rw -v $HOME/.helm-config:/home/tool/.helm-config:rw -v $HOME/.m2:/home/tool/.m2:rw -v $(pwd):/home/tool/workplace:rw raju2210/toolbox"
```

```shell script
toolbox mvn clean package
```

| Directory Mounted | Description |
| --- | --- |
| `-v $HOME/.kube:/home/tool/.kube-config:rw` | Container configures kubectl to use config from /home/tool/.kube-config directory |
| `-v $HOME/.helm-config:/home/tool/.helm-config:rw` | Container configures helm to use /home/tool/.helm-config directory for cache and repository |
| `$HOME/.m2:/home/tool/.m2:rw` | Mount host m2 repository to increase reusability of downloaded dependencies |
| `-v $(pwd):/home/tool/workplace:rw` | Container always executes commands in /home/tool/workplace so you mount your current working directory on same. |

If you don't want to type the command every time on terminal, put the following script in ~/.bashrc or ~/.zshrc

```shell script
function cd() { builtin cd "$@" && alias toolbox="docker run --rm --net='host' -it -v $HOME/.kube:/home/tool/.kube-config:rw -v $HOME/.helm-config:/home/tool/.helm-config:rw -v $(pwd):/home/tool/workplace:rw -v $HOME/.m2:/home/tool/.m2:rw raju2210/toolbox" }
```

#### WINDOWS(cmd)

```bash script
set "toolbox=docker run --rm --net='host' -it -v %systemdrive%%homepath%\.kube:/home/tool/.kube-config -v %systemdrive%%homepath%\.helm-config:/home/tool/.helm-config -v %cd%:/home/tool/workplace -v %systemdrive%%homepath%\.m2:/home/tool/.m2 raju2210/toolbox"

toolbox nslookup google.com
```

#### WINDOWS(powershell)

```powershell script
notepad $profile
function toolboxFn {
    docker run --rm --net='host' -it -v <path_to_k8s_config_dir>\.kube:/home/tool/.kube-config -v <path_to_helm_dir>:/home/tool/.helm-config -v ${PWD}:/home/tool/workplace raju2210/toolbox
}
Set-Alias toolbox toolboxFn

toolbox nslookup google.com
```
TBD: add examples for windows to override cd and default home directory.

### Run and attach to the network namespace of the container to debug
```shell script
docker run --name toolbox --net=container:${container_id_to_debug} -itd raju2210/toolbox:latest
```

### Run it in kubernetes as Ephemeral Container

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
