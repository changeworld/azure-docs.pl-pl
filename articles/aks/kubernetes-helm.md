---
title: Wdrażanie kontenerów przy użyciu narzędzia Helm w usłudze Kubernetes na platformie Azure
description: Dowiedz się, jak używać narzędzia do tworzenia pakietów narzędzia Helm do wdrażania kontenerów w klastrze usługi Azure Kubernetes Service (AKS)
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: article
ms.date: 05/23/2019
ms.author: zarhoads
ms.openlocfilehash: 76a5391cbe142851d9b1f60ea9346af2e7a35d6a
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/30/2019
ms.locfileid: "66392136"
---
# <a name="install-applications-with-helm-in-azure-kubernetes-service-aks"></a>Instalowanie aplikacji za pomocą narzędzia Helm w usłudze Azure Kubernetes Service (AKS)

[Polecenie Helm] [ helm] to narzędzie open source pakietu, które pomaga zainstalować i zarządzanie cyklem życia aplikacji platformy Kubernetes. Podobnie jak menedżerów pakietów systemu Linux, takie jak *APT* i *Yum*, umożliwia zarządzanie wykresów Kubernetes, które są pakietami wstępnie skonfigurowane zasoby platformy Kubernetes Helm.

W tym artykule przedstawiono sposób konfigurowania i używania narzędzia Helm w klastrze Kubernetes w usłudze AKS.

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym artykule założono, że masz istniejący klaster usługi AKS. Jeśli potrzebujesz klastra AKS, zobacz Przewodnik Szybki Start usługi AKS [przy użyciu wiersza polecenia platformy Azure] [ aks-quickstart-cli] lub [przy użyciu witryny Azure portal][aks-quickstart-portal].

Należy również narzędzia Helm zainstalować interfejs wiersza polecenia, czyli klienta, który działa w systemie deweloperskim. Umożliwia uruchamianie, zatrzymywanie i zarządzać aplikacjami za pomocą narzędzia Helm. Jeśli używasz usługi Azure Cloud Shell, interfejs wiersza polecenia narzędzia Helm jest już zainstalowana. Aby, zobacz instrukcje dotyczące instalacji na lokalnym platformie [instalowanie narzędzia Helm][helm-install].

> [!IMPORTANT]
> Helm jest przeznaczony do uruchomienia w węzłach systemu Linux. W przypadku węzłów systemu Windows Server w klastrze, upewnij się, że narzędzia Helm zasobników tylko są planowane do uruchomienia w węzłach systemu Linux. Należy również upewnić się, że żadnych wykresów rozwiązania Helm, które należy zainstalować również są zaplanowane do uruchomienia na odpowiednich węzłów. Polecenia w tym artykule korzystają [selektory węzła] [ k8s-node-selector] zapewnienie zasobników są zaplanowane do odpowiednich węzłów, ale nie wszystkich wykresów rozwiązania Helm może narazić selektora węzła. Możesz też rozważyć, przy użyciu innych opcji w klastrze, takich jak [taints][taints].

## <a name="create-a-service-account"></a>Tworzenie konta usługi

Przed wdrożeniem narzędzia Helm w klastrze AKS z włączoną funkcją RBAC, potrzebujesz konta usługi i powiązania roli usługi Tiller. Aby uzyskać więcej informacji na temat zabezpieczenia Helm / Tiller w RBAC włączone klastra, zobacz [Tiller, przestrzenie nazw i RBAC][tiller-rbac]. Jeśli klaster AKS nie jest włączone RBAC, Pomiń ten krok.

Utwórz plik o nazwie `helm-rbac.yaml` i skopiuj do poniższego kodu YAML:

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: tiller
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1
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

Tworzenie konta usługi i powiązanie roli za pomocą `kubectl apply` polecenia:

```console
kubectl apply -f helm-rbac.yaml
```

## <a name="secure-tiller-and-helm"></a>Zabezpieczanie Tiller i Helm

Narzędzia Helm, klient i usługa Tiller uwierzytelnienia i komunikowania się ze sobą przy użyciu protokołu TLS/SSL. Ta metoda uwierzytelniania pomaga zabezpieczyć klaster Kubernetes i jakich usług, którą można wdrożyć. Aby zwiększyć bezpieczeństwo, można wygenerować własne certyfikaty z podpisem. Każdy użytkownik Helm może pobrać certyfikatu klienta i Tiller będzie można zainicjować w klastrze Kubernetes za pomocą certyfikatów stosowane. Aby uzyskać więcej informacji, zobacz [za pomocą protokołów TLS/SSL między Helm i Tiller][helm-ssl].

Z klastrem Kubernetes z włączoną funkcją RBAC można kontrolować poziom dostępu Tiller w klastrze. Definiowanie przestrzeni nazw Kubernetes, które Tiller zostało wdrożone w i ograniczyć, jakie przestrzenie nazw Tiller można następnie wdrożyć zasoby w. To podejście pozwala tworzyć wystąpienia Tiller w różnych obszarach nazw i granice wdrożenia limit i określania zakresu użytkowników klienta Helm do określonych przestrzeni nazw. Aby uzyskać więcej informacji, zobacz [Helm kontroli dostępu opartej na rolach][helm-rbac].

## <a name="configure-helm"></a>Konfigurowanie narzędzia Helm

Aby wdrożyć podstawowe Tiller w klastrze AKS, wykonaj [polecenia helm init] [ helm-init] polecenia. Jeśli klaster nie jest włączone RBAC, Usuń `--service-account` argument i wartość. Jeśli protokoły TLS/SSL jest skonfigurowany dla Tiller i Helm, Pomiń ten krok inicjowania podstawowe i zamiast tego Podaj wymagane `--tiller-tls-` jak pokazano w następnym przykładzie.

```console
helm init --service-account tiller --node-selectors "beta.kubernetes.io/os"="linux"
```

Jeśli skonfigurowano protokół TLS/SSL między Helm i Tiller zapewniają `--tiller-tls-*` parametrów i nazwy własne certyfikaty, jak pokazano w poniższym przykładzie:

```console
helm init \
    --tiller-tls \
    --tiller-tls-cert tiller.cert.pem \
    --tiller-tls-key tiller.key.pem \
    --tiller-tls-verify \
    --tls-ca-cert ca.cert.pem \
    --service-account tiller \
    --node-selectors "beta.kubernetes.io/os"="linux"
```

## <a name="find-helm-charts"></a>Znajdź wykresów rozwiązania Helm

Narzędzia Helm są używane do wdrażania aplikacji w klastrze Kubernetes. Aby wyszukać wstępnie utworzone wykresów rozwiązania Helm, należy użyć [helm search] [ helm-search] polecenia:

```console
helm search
```

Następujące skrócone przykładowe dane wyjściowe zawierają niektóre dostępne do użycia wykresów rozwiązania Helm:

```
$ helm search

NAME                           CHART VERSION    APP VERSION  DESCRIPTION
stable/aerospike               0.1.7            v3.14.1.2    A Helm chart for Aerospike in Kubernetes
stable/anchore-engine          0.1.7            0.1.10       Anchore container analysis and policy evaluatio...
stable/apm-server              0.1.0            6.2.4        The server receives data from the Elastic APM a...
stable/ark                     1.0.1            0.8.2        A Helm chart for ark
stable/artifactory             7.2.1            6.0.0        Universal Repository Manager supporting all maj...
stable/artifactory-ha          0.2.1            6.0.0        Universal Repository Manager supporting all maj...
stable/auditbeat               0.1.0            6.2.4        A lightweight shipper to audit the activities o...
stable/aws-cluster-autoscaler  0.3.3                         Scales worker nodes within autoscaling groups.
stable/bitcoind                0.1.3            0.15.1       Bitcoin is an innovative payment network and a ...
stable/buildkite               0.2.3            3            Agent for Buildkite
stable/burrow                  0.4.4            0.17.1       Burrow is a permissionable smart contract machine
stable/centrifugo              2.0.1            1.7.3        Centrifugo is a real-time messaging server.
stable/cerebro                 0.1.0            0.7.3        A Helm chart for Cerebro - a web admin tool tha...
stable/cert-manager            v0.3.3           v0.3.1       A Helm chart for cert-manager
stable/chaoskube               0.7.0            0.8.0        Chaoskube periodically kills random pods in you...
stable/chartmuseum             1.5.0            0.7.0        Helm Chart Repository with support for Amazon S...
stable/chronograf              0.4.5            1.3          Open-source web application written in Go and R...
stable/cluster-autoscaler      0.6.4            1.2.2        Scales worker nodes within autoscaling groups.
stable/cockroachdb             1.1.1            2.0.0        CockroachDB is a scalable, survivable, strongly...
stable/concourse               1.10.1           3.14.1       Concourse is a simple and scalable CI system.
stable/consul                  3.2.0            1.0.0        Highly available and distributed service discov...
stable/coredns                 0.9.0            1.0.6        CoreDNS is a DNS server that chains plugins and...
stable/coscale                 0.2.1            3.9.1        CoScale Agent
stable/dask                    1.0.4            0.17.4       Distributed computation in Python with task sch...
stable/dask-distributed        2.0.2                         DEPRECATED: Distributed computation in Python
stable/datadog                 0.18.0           6.3.0        DataDog Agent
...
```

Aby zaktualizować listę wykresy, użyj [aktualizacja repozytorium narzędzia helm] [ helm-repo-update] polecenia. Poniższy przykład przedstawia aktualizacji pomyślne repozytorium:

```console
$ helm repo update

Hold tight while we grab the latest from your chart repositories...
...Skip local chart repository
...Successfully got an update from the "stable" chart repository
Update Complete. ⎈ Happy Helming!⎈
```

## <a name="run-helm-charts"></a>Uruchom wykresów rozwiązania Helm

Aby zainstalować wykresów za pomocą narzędzia Helm, użyj [helm install] [ helm-install] polecenie i wprowadź nazwę wykresu do zainstalowania. Aby zobaczyć, instalowanie wykresu Helm w akcji, należy zainstalować wdrożenie podstawowego serwera nginx za pomocą wykresu Helm. Jeśli skonfigurowano protokół TLS/SSL, należy dodać `--tls` parametr do użycia certyfikat klienta narzędzia Helm.

```console
helm install stable/nginx-ingress \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

Następujące dane wyjściowe skróconego przykładu przedstawia stan wdrożenia zasobów platformy Kubernetes, w ramach wykresu Helm:

```
$ helm install stable/nginx-ingress --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux

NAME:   flailing-alpaca
LAST DEPLOYED: Thu May 23 12:55:21 2019
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/ConfigMap
NAME                                      DATA  AGE
flailing-alpaca-nginx-ingress-controller  1     0s

==> v1/Pod(related)
NAME                                                            READY  STATUS             RESTARTS  AGE
flailing-alpaca-nginx-ingress-controller-56666dfd9f-bq4cl       0/1    ContainerCreating  0         0s
flailing-alpaca-nginx-ingress-default-backend-66bc89dc44-m87bp  0/1    ContainerCreating  0         0s

==> v1/Service
NAME                                           TYPE          CLUSTER-IP  EXTERNAL-IP  PORT(S)                     AGE
flailing-alpaca-nginx-ingress-controller       LoadBalancer  10.0.109.7  <pending>    80:31219/TCP,443:32421/TCP  0s
flailing-alpaca-nginx-ingress-default-backend  ClusterIP     10.0.44.97  <none>       80/TCP                      0s
...
```

Trwa minutę lub dwie *EXTERNAL-IP* adres usługi nginx — ruch przychodzący controller uzupełnione i pozwala uzyskać do niego dostęp za pomocą przeglądarki internetowej.

## <a name="list-helm-releases"></a>Wersje narzędzia Helm list

Aby wyświetlić listę wersji zainstalowany w klastrze, należy użyć [polecenie helm list] [ helm-list] polecenia. Poniższy przykład pokazuje wersji ruch przychodzący serwera nginx, wdrożonych w poprzednim kroku. Jeśli skonfigurowano protokół TLS/SSL, należy dodać `--tls` parametr do użycia certyfikat klienta narzędzia Helm.

```console
$ helm list

NAME                REVISION    UPDATED                     STATUS      CHART                 APP VERSION   NAMESPACE
flailing-alpaca   1         Thu May 23 12:55:21 2019    DEPLOYED    nginx-ingress-1.6.13    0.24.1      default
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Podczas wdrażania wykresu Helm tworzonych wiele zasobów Kubernetes. Te zasoby obejmują zasobników, wdrożenia i usług. Aby wyczyścić te zasoby, należy użyć `helm delete` polecenia i podaj nazwę swojej wersji, tak jak w poprzednim `helm list` polecenia. Poniższy przykład usuwa wydania o nazwie *flailing alpaki*:

```console
$ helm delete flailing-alpaca

release "flailing-alpaca" deleted
```

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji o zarządzaniu wdrażaniem aplikacji platformy Kubernetes za pomocą narzędzia Helm zobacz dokumentację narzędzia Helm.

> [!div class="nextstepaction"]
> [Dokumentacja narzędzia Helm][helm-documentation]

<!-- LINKS - external -->
[helm]: https://github.com/kubernetes/helm/
[helm-documentation]: https://docs.helm.sh/
[helm-init]: https://docs.helm.sh/helm/#helm-init
[helm-install]: https://docs.helm.sh/using_helm/#installing-helm
[helm-install-options]: https://github.com/kubernetes/helm/blob/master/docs/install.md
[helm-list]: https://docs.helm.sh/helm/#helm-list
[helm-rbac]: https://docs.helm.sh/using_helm/#role-based-access-control
[helm-repo-update]: https://docs.helm.sh/helm/#helm-repo-update
[helm-search]: https://docs.helm.sh/helm/#helm-search
[tiller-rbac]: https://docs.helm.sh/using_helm/#tiller-namespaces-and-rbac
[helm-ssl]: https://docs.helm.sh/using_helm/#using-ssl-between-helm-and-tiller

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[k8s-node-selector]: concepts-clusters-workloads.md#node-selectors
[taints]: operator-best-practices-advanced-scheduler.md