# dockerfile_lint - ignore
# Do not use this container for production use
FROM ubuntu:20.10

LABEL k8s.description="toolbox" \
	name="toolbox"

SHELL ["/bin/bash", "-o", "pipefail", "-c"]

ARG user=tool
ARG group=tool
ARG uid=1000
ARG gid=1000

RUN apt-get update && apt-get install --no-install-recommends -y software-properties-common apt-transport-https && rm -rf /var/lib/apt/lists/*

# utils for pod investigation & code build
# ansible to use for vagrant provisioning or playbook executions
RUN apt-get update && apt-get install --no-install-recommends -y gnupg2 ansible wget yamllint curl jq uidmap iptables \
	git vim net-tools zip unzip mysql-client python3-pip sshpass telnet iputils-ping bash-completion && \
	rm -rf /var/lib/apt/lists/*

# utils for container build/push
RUN . /etc/os-release && \
	echo "deb https://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable/xUbuntu_${VERSION_ID}/ /" | tee /etc/apt/sources.list.d/devel:kubic:libcontainers:stable.list && \
	curl -L "https://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable/xUbuntu_${VERSION_ID}/Release.key" | apt-key add - && \
	apt-get update && apt-get install --no-install-recommends -y buildah podman skopeo && rm -rf /var/lib/apt/lists/*

# kubectl & helm
RUN curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add - && \
		echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" | tee -a /etc/apt/sources.list.d/kubernetes.list && \
		curl https://baltocdn.com/helm/signing.asc | apt-key add - && \
		echo "deb https://baltocdn.com/helm/stable/debian/ all main" | tee /etc/apt/sources.list.d/helm-stable-debian.list && \
		apt-get update && apt-get install --no-install-recommends -y kubectl helm && \
		kubectl completion bash > /etc/bash_completion.d/kubectl && \
		helm completion bash > /etc/bash_completion.d/helm && \
		rm -rf /var/lib/apt/lists/*

# chrome for headless html scrapping
RUN curl -s https://dl-ssl.google.com/linux/linux_signing_key.pub | apt-key add - && \
	echo "deb http://dl.google.com/linux/chrome/deb/ stable main" | tee -a /etc/apt/sources.list.d/google.list && \
	apt-get update && apt-get install --no-install-recommends -y google-chrome-stable && \
	rm -rf /var/lib/apt/lists/*

COPY maven/files/tools /usr/local/bin/
RUN chmod 755 /usr/local/bin/tools

RUN groupadd -g ${gid} ${group} && useradd -m -d /home/${user} -u ${uid} -g ${group} -s /bin/sh ${user}

USER ${user}
ENV PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
ENV KUBECONFIG=/home/${user}/kube-config/config
WORKDIR /home/${user}
COPY maven/files/kubectl_aliases.txt /home/${user}/.kubectl_aliases

# sdkman to manage development tools
# adding aliases i.e. kgp, kga
RUN curl -s "https://get.sdkman.io" | bash && touch /home/${user}/.bashrc && \
		echo 'source /usr/share/bash-completion/bash_completion' >> /home/${user}/.bashrc && \
		echo 'alias k=kubectl' >> /home/${user}/.bashrc && \
		echo 'complete -F __start_kubectl k' >> /home/${user}/.bashrc && \
		echo '[ -f ~/.kubectl_aliases ] && source ~/.kubectl_aliases' >> /home/${user}/.bashrc && \
		sed -i -e 's/sdkman_auto_answer=false/sdkman_auto_answer=true/g' /home/tool/.sdkman/etc/config && \
		source "/home/${user}/.sdkman/bin/sdkman-init.sh" && sdk install java 8.0.265-open && sdk install maven

# ssh as service for vagrant users
ENTRYPOINT ["/usr/local/bin/tools"]
