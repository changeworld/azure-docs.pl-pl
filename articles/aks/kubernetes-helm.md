---
title: Wdrażanie kontenerów z Helm w Kubernetes na platformie Azure
description: Wdrażanie kontenerów w klastrze Kubernetes w AKS za pomocą narzędzia pakowania Helm
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 06/13/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 531e6d9368b2bf91c48fd41b1e9330879b0df49a
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37102483"
---
# <a name="use-helm-with-azure-kubernetes-service-aks"></a>Helm za pomocą usługi Azure Kubernetes (AKS)

[Helm] [ helm] jest narzędziem open source tworzenia pakietów, które pomaga zainstalować i zarządzanie cyklem życia aplikacji Kubernetes. Podobnie jak menedżerów pakietu systemu Linux, takich jak *APT* i *Yum*, Helm służy do zarządzania Kubernetes wykresy, które są pakiety zasobów Kubernetes wstępnie skonfigurowane.

Kroki tego dokumentu, do konfigurowania i używania Helm w klastrze Kubernetes na AKS.

## <a name="before-you-begin"></a>Przed rozpoczęciem

W krokach szczegółowo opisanych w tym dokumencie założono, że klaster usługi AKS został utworzony i że zostało nawiązane połączenie kubectl z klastrem. Jeśli potrzebujesz tych elementów Zobacz, [szybkiego startu AKS][aks-quickstart].

## <a name="install-helm-cli"></a>Zainstaluj Helm interfejsu wiersza polecenia

Helm interfejsu wiersza polecenia jest klient, który działa w systemie deweloperskim i umożliwia uruchamianie, zatrzymywanie i Zarządzaj aplikacjami w usłudze Helm.

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

## <a name="create-service-account"></a>Utwórz konto usługi

Zanim Konfigurowanie Helm w RBAC włączono klaster, należy konta usługi i powiązania usługi sterownicy roli. Aby uzyskać więcej informacji na temat zabezpieczenia Helm / sterownicy w RBAC włączono klaster, zobacz [sterownicy, obszary nazw i RBAC][tiller-rbac]. Uwaga: Jeśli klaster nie jest RBAC włączone, Pomiń ten krok.

Utwórz plik o nazwie `helm-rbac.yaml` i skopiuj następujące yaml programu.

```
apiVersion: v1
kind: ServiceAccount
metadata:
  name: tiller
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRoleBinding
metadata:
  name: tiller
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
  - kind: ServiceAccount
    name: tiller
    namespace: kube-system
```

Tworzenie konta usługi i roli powiązania z `kubectl create` polecenia.

```
kubectl create -f helm-rbac.yaml
```

Włączenie przy użyciu funkcji RBAC klastra, masz opcji na poziomie sterownicy dostępu do klastra. Zobacz [Helm: kontroli dostępu opartej na rolach] [ helm-rbac] Aby uzyskać więcej informacji na temat opcji konfiguracji.

## <a name="configure-helm"></a>Skonfiguruj Helm

Teraz instalować przy użyciu sterownicy [helm init] [ helm-init] polecenia. Jeśli klaster nie jest włączone RBAC, Usuń `--service-account` argumentów i wartości.

```
helm init --service-account tiller
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

Aby wdrożyć przy użyciu wykresu Helm Wordpress, użyj [instalacji helm] [ helm-install] polecenia.

```azurecli-interactive
helm install stable/wordpress
```

Dane wyjściowe wygląda podobnie do poniższego, ale zawiera dodatkowe informacje, takie jak instrukcje dotyczące sposobu używania Kubernetes wdrożenia.

```
NAME:   bilging-ibex
LAST DEPLOYED: Tue Jun  5 14:31:49 2018
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/Pod(related)
NAME                                     READY  STATUS   RESTARTS  AGE
bilging-ibex-mariadb-7557b5474-dmdxn     0/1    Pending  0         1s
bilging-ibex-wordpress-7494c545fb-tskhz  0/1    Pending  0         1s

==> v1/Secret
NAME                    TYPE    DATA  AGE
bilging-ibex-mariadb    Opaque  2     1s
bilging-ibex-wordpress  Opaque  2     1s

==> v1/ConfigMap
NAME                        DATA  AGE
bilging-ibex-mariadb        1     1s
bilging-ibex-mariadb-tests  1     1s

==> v1/PersistentVolumeClaim
NAME                    STATUS   VOLUME   CAPACITY  ACCESS MODES  STORAGECLASS  AGE
bilging-ibex-mariadb    Pending  default  1s
bilging-ibex-wordpress  Pending  default  1s

==> v1/Service
NAME                    TYPE          CLUSTER-IP    EXTERNAL-IP  PORT(S)                     AGE
bilging-ibex-mariadb    ClusterIP     10.0.76.164   <none>       3306/TCP                    1s
bilging-ibex-wordpress  LoadBalancer  10.0.215.250  <pending>    80:30934/TCP,443:31134/TCP  1s

==> v1beta1/Deployment
NAME                    DESIRED  CURRENT  UP-TO-DATE  AVAILABLE  AGE
bilging-ibex-mariadb    1        1        1           0          1s
bilging-ibex-wordpress  1        1        1           0          1s
...
```

## <a name="list-helm-releases"></a>Zwalnia Helm listy

Aby wyświetlić listę wersji zainstalowany w klastrze, należy użyć [listy helm] [ helm-list] polecenia.

```azurecli-interactive
helm list
```

Dane wyjściowe:

```
NAME            REVISION    UPDATED                     STATUS      CHART           NAMESPACE
bilging-ibex    1           Tue Jun  5 14:31:49 2018    DEPLOYED    wordpress-1.0.9 default
```

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji o zarządzaniu Kubernetes wykresy zobacz dokumentację Helm.

> [!div class="nextstepaction"]
> [Dokumentacja Helm][helm-documentation]

<!-- LINKS - external -->
[helm]: https://github.com/kubernetes/helm/
[helm-documentation]: https://docs.helm.sh/
[helm-init]: https://docs.helm.sh/helm/#helm-init
[helm-install]: https://docs.helm.sh/helm/#helm-install
[helm-install-options]: https://github.com/kubernetes/helm/blob/master/docs/install.md
[helm-list]: https://docs.helm.sh/helm/#helm-list
[helm-rbac]: https://docs.helm.sh/using_helm/#role-based-access-control
[helm-repo-update]: https://docs.helm.sh/helm/#helm-repo-update
[helm-search]: https://docs.helm.sh/helm/#helm-search
[tiller-rbac]: https://docs.helm.sh/using_helm/#tiller-namespaces-and-rbac

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
