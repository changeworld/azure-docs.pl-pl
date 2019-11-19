---
title: Wdrażanie kontenerów za pomocą Helm w programie Kubernetes na platformie Azure
description: Dowiedz się, jak wdrażać kontenery w klastrze usługi Azure Kubernetes Service (AKS) za pomocą narzędzia do pakowania Helm
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: article
ms.date: 05/23/2019
ms.author: zarhoads
ms.openlocfilehash: fc808fee66dee573aecd423e375d30bf3f5b696a
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74170712"
---
# <a name="install-applications-with-helm-in-azure-kubernetes-service-aks"></a>Instalowanie aplikacji przy użyciu usługi Helm w usłudze Azure Kubernetes Service (AKS)

[Helm][helm] to narzędzie do tworzenia pakietów typu "open source", które ułatwia Instalowanie i zarządzanie cyklem życia aplikacji Kubernetes. Podobnie jak w przypadku menedżerów pakietów systemu Linux, takich jak *apt* i *yum*, Helm służy do zarządzania wykresami Kubernetes, które są pakietami wstępnie skonfigurowanych zasobów Kubernetes.

W tym artykule opisano sposób konfigurowania i używania Helm w klastrze Kubernetes w systemie AKS.

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym artykule przyjęto założenie, że masz istniejący klaster AKS. Jeśli potrzebujesz klastra AKS, zapoznaj się z przewodnikiem Szybki Start AKS [przy użyciu interfejsu wiersza polecenia platformy Azure][aks-quickstart-cli] lub [przy użyciu Azure Portal][aks-quickstart-portal].

Wymagany jest również interfejs wiersza polecenia Helm, który jest klientem uruchomionym w systemie deweloperskim. Pozwala ona uruchamiać, zatrzymywać i zarządzać aplikacjami za pomocą Helm. W przypadku korzystania z Azure Cloud Shell interfejs wiersza polecenia Helm jest już zainstalowany. Aby uzyskać instrukcje dotyczące instalacji na lokalnej platformie, zobacz [Instalowanie Helm][helm-install].

> [!IMPORTANT]
> Helm jest przeznaczony do uruchamiania w węzłach systemu Linux. Jeśli w klastrze znajdują się węzły systemu Windows Server, musisz upewnić się, że Helm są zaplanowane do uruchomienia tylko na węzłach z systemem Linux. Należy również upewnić się, że wszystkie zainstalowane wykresy Helm są również zaplanowane do uruchomienia w prawidłowych węzłach. Polecenia w tym artykule używają selektorów [węzłów][k8s-node-selector] , aby upewnić się, że zasobniki są zaplanowane do poprawnych węzłów, ale nie wszystkie wykresy Helm mogą uwidocznić wybór węzła. Możesz również rozważyć użycie innych opcji w klastrze, [takich jak][taints]przypisania.

## <a name="create-a-service-account"></a>Utwórz konto usługi

Przed wdrożeniem Helm w klastrze AKS z włączoną funkcją RBAC należy mieć konto usługi i powiązanie roli dla usługi. Aby uzyskać więcej informacji na temat zabezpieczania Helm/do usługi w klastrze z obsługą RBAC, zobacz odniesień [, przestrzenie nazw i RBAC][tiller-rbac]. Jeśli w klastrze AKS nie włączono kontroli RBAC, Pomiń ten krok.

Utwórz plik o nazwie `helm-rbac.yaml` i skopiuj go do następującej YAML:

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

Utwórz konto usługi i powiązanie roli za pomocą polecenia `kubectl apply`:

```console
kubectl apply -f helm-rbac.yaml
```

## <a name="secure-tiller-and-helm"></a>Zabezpieczanie do i Helm

Klient Helm i usługa do odczekania uwierzytelniają się i komunikują się ze sobą przy użyciu protokołu TLS/SSL. Ta metoda uwierzytelniania pomaga zabezpieczyć klaster Kubernetes i jakie usługi można wdrożyć. Aby zwiększyć bezpieczeństwo, można wygenerować własne certyfikaty z podpisem. Każdy użytkownik Helm otrzyma swój własny certyfikat klienta i zostanie on zainicjowany w klastrze Kubernetes z zastosowanymi certyfikatami. Aby uzyskać więcej informacji, zobacz [Korzystanie z protokołu TLS/SSL między Helm i przydziałem][helm-ssl]IT.

W przypadku klastra Kubernetes z obsługą kontroli RBAC można kontrolować poziom dostępu do klastra. Można zdefiniować przestrzeń nazw Kubernetes, która jest wdrażana w programie, i ograniczyć zakres przestrzeni nazw, które mogą następnie wdrożyć zasoby w programie. Takie podejście umożliwia tworzenie wystąpień programu w różnych przestrzeniach nazw i ograniczanie granic wdrożenia oraz określanie zakresu użytkowników programu Helm Client do określonych przestrzeni nazw. Aby uzyskać więcej informacji, zobacz [Helm kontroli dostępu opartej na rolach][helm-rbac].

## <a name="configure-helm"></a>Konfigurowanie Helm

Aby wdrożyć podstawową usługę do klastra AKS, użyj polecenia [init Helm][helm-init] . Jeśli w klastrze nie włączono kontroli RBAC, Usuń `--service-account` argument i wartość. W poniższych przykładach ustawiono również [historię — maks][helm-history-max] . do 200.

W przypadku skonfigurowania protokołu TLS/SSL dla operacji do odniesień i Helm, Pomiń ten podstawowy krok inicjalizacji, a następnie podaj wymagane `--tiller-tls-`, jak pokazano w następnym przykładzie.

```console
helm init --history-max 200 --service-account tiller --node-selectors "beta.kubernetes.io/os=linux"
```

W przypadku skonfigurowania protokołu TLS/SSL między Helm i do przydzielenia przez program do dostarczania parametrów `--tiller-tls-*` i nazw własnych certyfikatów, jak pokazano w następującym przykładzie:

```console
helm init \
    --tiller-tls \
    --tiller-tls-cert tiller.cert.pem \
    --tiller-tls-key tiller.key.pem \
    --tiller-tls-verify \
    --tls-ca-cert ca.cert.pem \
    --history-max 200 \
    --service-account tiller \
    --node-selectors "beta.kubernetes.io/os=linux"
```

## <a name="find-helm-charts"></a>Znajdź wykresy Helm

Wykresy Helm są używane do wdrażania aplikacji w klastrze Kubernetes. Aby wyszukać wstępnie utworzone wykresy Helm, użyj polecenia [wyszukiwania Helm][helm-search] :

```console
helm search
```

Następujące wąskie przykładowe dane wyjściowe pokazują niektóre wykresy Helm dostępne do użycia:

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

Aby zaktualizować listę wykresów, użyj polecenia [Helm repozytorium aktualizacji][helm-repo-update] . Poniższy przykład przedstawia pomyślne aktualizowanie repozytorium:

```console
$ helm repo update

Hold tight while we grab the latest from your chart repositories...
...Skip local chart repository
...Successfully got an update from the "stable" chart repository
Update Complete.
```

## <a name="run-helm-charts"></a>Uruchom wykresy Helm

Aby zainstalować wykresy z Helm, użyj polecenia [Install Helm][helm-install] i określ nazwę wykresu do zainstalowania. Aby zapoznać się z tematem Instalowanie wykresu Helm w działaniu, Zainstalujmy podstawowe wdrożenie Nginx przy użyciu wykresu Helm. W przypadku skonfigurowania protokołu TLS/SSL Dodaj parametr `--tls`, aby użyć certyfikatu klienta Helm.

```console
helm install stable/nginx-ingress \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

Następujące wąskie przykładowe dane wyjściowe pokazują stan wdrożenia zasobów Kubernetes utworzonych przez wykres Helm:

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

Aby można było wypełniać zewnętrzny adres *IP* usługi Nginx-transfer-Controller i umożliwić dostęp do niej przy użyciu przeglądarki sieci Web, potrwa minutę lub dwa.

## <a name="list-helm-releases"></a>Lista wersji Helm

Aby wyświetlić listę wersji zainstalowanych w klastrze, użyj polecenia [listy Helm][helm-list] . Poniższy przykład przedstawia wydanie Nginx-Ingress wdrożone w poprzednim kroku. W przypadku skonfigurowania protokołu TLS/SSL Dodaj parametr `--tls`, aby użyć certyfikatu klienta Helm.

```console
$ helm list

NAME                REVISION    UPDATED                     STATUS      CHART                 APP VERSION   NAMESPACE
flailing-alpaca   1         Thu May 23 12:55:21 2019    DEPLOYED    nginx-ingress-1.6.13    0.24.1      default
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po wdrożeniu wykresu Helm są tworzone różne zasoby Kubernetes. Do tych zasobów należą między innymi: zasoby, wdrożenia i usługi. Aby wyczyścić te zasoby, użyj `helm delete` polecenia i określ nazwę wydania, jak znaleziono w poprzednim `helm list` polecenie. Poniższy przykład usuwa wydanie o nazwie *Flailing-Alpaca*:

```console
$ helm delete flailing-alpaca

release "flailing-alpaca" deleted
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat zarządzania wdrożeniami aplikacji Kubernetes z Helm, zobacz dokumentację Helm.

> [!div class="nextstepaction"]
> [Dokumentacja Helm][helm-documentation]

<!-- LINKS - external -->
[helm]: https://github.com/kubernetes/helm/
[helm-documentation]: https://v2.helm.sh/docs/
[helm-init]: https://v2.helm.sh/docs/helm/#helm-init
[helm-install]: https://v2.helm.sh/docs/using_helm/#installing-helm
[helm-install-options]: https://github.com/kubernetes/helm/blob/master/docs/install.md
[helm-list]: https://v2.helm.sh/docs/helm/#helm-list
[helm-history-max]: https://v2.helm.sh/docs/using_helm/#initialize-helm-and-install-tiller
[helm-rbac]: https://v2.helm.sh/docs/using_helm/#role-based-access-control
[helm-repo-update]: https://v2.helm.sh/docs/helm/#helm-repo-update
[helm-search]: https://v2.helm.sh/docs/helm/#helm-search
[tiller-rbac]: https://v2.helm.sh/docs/using_helm/#tiller-namespaces-and-rbac
[helm-ssl]: https://v2.helm.sh/docs/using_helm/#using-ssl-between-helm-and-tiller
            
<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[k8s-node-selector]: concepts-clusters-workloads.md#node-selectors
[taints]: operator-best-practices-advanced-scheduler.md
