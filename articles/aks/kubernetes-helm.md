---
title: Wdrażanie kontenerów z Helm w Kubernetes na platformie Azure
description: Wdrażanie kontenerów w klastrze Kubernetes w AKS za pomocą narzędzia pakowania Helm
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/13/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 70e13fb377be3ec501cce5170ed391aac8cb6e5d
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
---
# <a name="use-helm-with-azure-kubernetes-service-aks"></a>Helm za pomocą usługi Azure Kubernetes (AKS)

[Helm] [ helm] jest narzędziem open source tworzenia pakietów, które pomaga zainstalować i zarządzanie cyklem życia aplikacji Kubernetes. Podobnie jak menedżerów pakietu systemu Linux, takich jak *APT* i *Yum*, Helm służy do zarządzania Kubernetes wykresy, które są pakiety zasobów Kubernetes wstępnie skonfigurowane.

Kroki tego dokumentu, do konfigurowania i używania Helm w klastrze Kubernetes na AKS.

## <a name="before-you-begin"></a>Przed rozpoczęciem

W krokach szczegółowo opisanych w tym dokumencie założono, że klaster usługi AKS został utworzony i że zostało nawiązane połączenie kubectl z klastrem. Jeśli potrzebujesz tych elementów Zobacz, [szybkiego startu AKS][aks-quickstart].

## <a name="install-helm-cli"></a>Zainstaluj Helm interfejsu wiersza polecenia

Helm interfejsu wiersza polecenia jest klient, który działa w systemie deweloperskim i umożliwia uruchamianie, zatrzymywanie i zarządzania aplikacjami z wykresami Helm.

Jeśli używasz Azure CloudShell Helm CLI jest już zainstalowana. Aby zainstalować Helm interfejsu wiersza polecenia przy użyciu Mac `brew`. Aby uzyskać więcej informacji, zobacz opcje [instalowanie Helm][helm-install-options].

```console
brew install kubernetes-helm
```

Dane wyjściowe:

```
==> Downloading https://homebrew.bintray.com/bottles/kubernetes-helm-2.6.2.sierra.bottle.1.tar.gz
######################################################################## 100.0%
==> Pouring kubernetes-helm-2.6.2.sierra.bottle.1.tar.gz
==> Caveats
Bash completion has been installed to:
  /usr/local/etc/bash_completion.d
==> Summary
🍺  /usr/local/Cellar/kubernetes-helm/2.6.2: 50 files, 132.4MB
```

## <a name="configure-helm"></a>Skonfiguruj Helm

[Helm init] [ helm-init] polecenie służy do instalowania składników Helm w klastrze Kubernetes i wprowadzić zmiany konfiguracji po stronie klienta. Uruchom następujące polecenie, aby zainstalować w klastrze AKS Helm i konfigurowania klienta Helm.

```azurecli-interactive
helm init --upgrade --service-account default
```

Dane wyjściowe:

```
$HELM_HOME has been configured at /Users/neilpeterson/.helm.

Tiller (the Helm server-side component) has been installed into your Kubernetes Cluster.

Please note: by default, Tiller is deployed with an insecure 'allow unauthenticated users' policy.
For more information on securing your installation see: https://docs.helm.sh/using_helm/#securing-your-helm-installation
Happy Helming!
```

## <a name="find-helm-charts"></a>Znajdź Helm wykresów

Wykresy Helm są używane do wdrażania aplikacji w klastrze Kubernetes. Aby wyszukać utworzone wcześniej Helm wykresy, należy użyć [wyszukiwania helm] [ helm-search] polecenia.

```azurecli-interactive
helm search
```

Wyszukuje dane wyjściowe podobne do następujących, jednak o wiele więcej wykresy.

```
NAME                            VERSION DESCRIPTION
stable/acs-engine-autoscaler    2.0.0   Scales worker nodes within agent pools
stable/artifactory              6.1.0   Universal Repository Manager supporting all maj...
stable/aws-cluster-autoscaler   0.3.1   Scales worker nodes within autoscaling groups.
stable/buildkite                0.2.0   Agent for Buildkite
stable/centrifugo               2.0.0   Centrifugo is a real-time messaging server.
stable/chaoskube                0.5.0   Chaoskube periodically kills random pods in you...
stable/chronograf               0.3.0   Open-source web application written in Go and R...
stable/cluster-autoscaler       0.2.0   Scales worker nodes within autoscaling groups.
stable/cockroachdb              0.5.0   CockroachDB is a scalable, survivable, strongly...
stable/concourse                0.7.0   Concourse is a simple and scalable CI system.
stable/consul                   0.4.1   Highly available and distributed service discov...
stable/coredns                  0.5.0   CoreDNS is a DNS server that chains middleware ...
stable/coscale                  0.2.0   CoScale Agent
stable/dask-distributed         2.0.0   Distributed computation in Python
stable/datadog                  0.8.0   DataDog Agent
...
```

Aby zaktualizować listę wykresy, należy użyć [helm repozytorium aktualizacji] [ helm-repo-update] polecenia.

```azurecli-interactive
helm repo update
```

Dane wyjściowe:

```
Hang tight while we grab the latest from your chart repositories...
...Skip local chart repository
...Successfully got an update from the "stable" chart repository
Update Complete. ⎈ Happy Helming!⎈
```

## <a name="run-helm-charts"></a>Uruchom Helm wykresów

Aby wdrożyć kontrolera wejściowych NGINX, należy użyć [instalacji helm] [ helm-install] polecenia.

```azurecli-interactive
helm install stable/nginx-ingress --set rbac.create=false --set rbac.createRole=false --set rbac.createClusterRole=false
```

Dane wyjściowe wygląda podobnie do poniższego, ale zawiera dodatkowe informacje, takie jak instrukcje dotyczące sposobu używania Kubernetes wdrożenia.

```
NAME:   tufted-ocelot
LAST DEPLOYED: Thu Oct  5 00:48:04 2017
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/ConfigMap
NAME                                    DATA  AGE
tufted-ocelot-nginx-ingress-controller  1     5s

==> v1/Service
NAME                                         CLUSTER-IP   EXTERNAL-IP  PORT(S)                     AGE
tufted-ocelot-nginx-ingress-controller       10.0.140.10  <pending>    80:30486/TCP,443:31358/TCP  5s
tufted-ocelot-nginx-ingress-default-backend  10.0.34.132  <none>       80/TCP                      5s

==> v1beta1/Deployment
NAME                                         DESIRED  CURRENT  UP-TO-DATE  AVAILABLE  AGE
tufted-ocelot-nginx-ingress-controller       1        1        1           0          5s
tufted-ocelot-nginx-ingress-default-backend  1        1        1           1          5s
...
```

Aby uzyskać więcej informacji o używaniu NGINX kontrolera transfer danych przychodzących z Kubernetes, zobacz [kontrolera wejściowych NGINX][nginx-ingress].

## <a name="list-helm-charts"></a>Wykresy Helm listy

Aby wyświetlić listę wykresy zainstalowany w klastrze, należy użyć [listy helm] [ helm-list] polecenia.

```azurecli-interactive
helm list
```

Dane wyjściowe:

```
NAME            REVISION    UPDATED                     STATUS      CHART               NAMESPACE
bilging-ant     1           Thu Oct  5 00:11:11 2017    DEPLOYED    nginx-ingress-0.8.7 default
```

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji o zarządzaniu Kubernetes wykresy zobacz dokumentację Helm.

> [!div class="nextstepaction"]
> [Dokumentacja Helm][helm-documentation]

<!-- LINKS - external -->
[helm]: https://github.com/kubernetes/helm/
[helm-documentation]: https://github.com/kubernetes/helm/blob/master/docs/index.md
[helm-init]: https://docs.helm.sh/helm/#helm-init
[helm-install]: https://docs.helm.sh/helm/#helm-install
[helm-install-options]: https://github.com/kubernetes/helm/blob/master/docs/install.md
[helm-list]: https://docs.helm.sh/helm/#helm-list
[helm-repo-update]: https://docs.helm.sh/helm/#helm-repo-update
[helm-search]: https://docs.helm.sh/helm/#helm-search
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
