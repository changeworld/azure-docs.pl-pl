---
title: Instalowanie Hashicorp Consul w usłudze Azure Kubernetes Service (AKS)
description: Dowiedz się, jak zainstalować i używać Consul do tworzenia siatki usługi w klastrze usługi Azure Kubernetes Service (AKS)
services: container-service
author: dstrebel
ms.service: container-service
ms.topic: article
ms.date: 06/19/2019
ms.author: dastrebe
ms.openlocfilehash: 7acd2533079499091427c7e63741b9c587ee29e5
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70189226"
---
# <a name="install-and-use-consul-connect-in-azure-kubernetes-service-aks"></a>Instalowanie i używanie programu Consul Connect w usłudze Azure Kubernetes Service (AKS)

[Consul][consul-github] to siatka usługi typu "open source", która udostępnia kluczowy zestaw funkcji dla mikrousług w klastrze Kubernetes. Te funkcje obejmują odnajdowanie usług, sprawdzanie kondycji, segmentację usług i możliwości przestrzegania. Aby uzyskać więcej informacji na temat Consul, zobacz oficjalny dokument dotyczący [Consul?][consul-docs-concepts] .

W tym artykule opisano sposób instalowania programu Consul. Składniki Consul są instalowane w klastrze Kubernetes na AKS.

> [!NOTE]
> Te instrukcje odwołują się `1.5`do wersji Consul.
>
> Wersje Consul `1.5.x` zostały przetestowane przez zespół Hashicorp w odniesieniu do `1.12`wersji `1.14`Kubernetes `1.14`,,. Dodatkowe wersje Consul można znaleźć w [wersjach usługi GitHub-Consul][consul-github-releases] i informacje o każdej z nich w informacjach o [wersji Consul][consul-release-notes].

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Zainstaluj składniki Consul na AKS
> * Weryfikowanie instalacji Consul
> * Odinstaluj Consul z AKS

## <a name="before-you-begin"></a>Przed rozpoczęciem

W krokach przedstawionych w tym artykule przyjęto założenie, że utworzono klaster `1.11` AKS (Kubernetes lub nowszy z włączoną funkcją RBAC) i `kubectl` nawiązano połączenie z klastrem. Jeśli potrzebujesz pomocy z dowolnym z tych elementów, zobacz [Przewodnik Szybki Start AKS][aks-quickstart].

Musisz [Helm][helm] , aby wykonać te instrukcje i zainstalować Consul. Zaleca się, aby w Twoim klastrze `2.12.2` była zainstalowana lub nowsza wersja programu. Jeśli potrzebujesz pomocy dotyczącej instalowania Helm, zobacz [wskazówki dotyczące instalacji programu AKS Helm][helm-install]. Wszystkie Consulowe zasobniki muszą być również zaplanowane do uruchomienia w węzłach systemu Linux.

Ten artykuł oddziela wskazówki dotyczące instalacji Consul do kilku dyskretnych kroków. Wynik końcowy ma taką samą strukturę jak oficjalne [wskazówki dotyczące][consul-install-k8]instalacji Consul.

### <a name="install-the-consul-components-on-aks"></a>Zainstaluj składniki Consul na AKS

Najpierw Sklonuj oficjalne HashiCorp Consul w repozytorium GitHub Kubernetes.

```bash
git clone https://github.com/hashicorp/consul-helm.git
cd consul-helm
```

Następnie będziemy musieli utworzyć niestandardowy plik wartości Helm dla instalacji Consul. Przed zainstalowaniem Consul Utwórz następujący plik wartości niestandardowych.

```bash
vim consul-values.yaml
```

Następnie skopiuj i wklej następujące wartości w pliku Consul-Values. YAML.

```yaml
# Sets datacenter name and version Consul to use
global:
  datacenter: dc1
  image: "consul:1.5.2"

# Enables proxies to be injected into pods
connectInject:
  enabled: true

# Enables UI on ClusterIP
ui:
  service:
    type: "ClusterIP"

# Enables GRCP that is required for connectInject
client:
  enabled: true
  grpc: true

# Sets replicase to 3 for HA
server:
  replicas: 3
  bootstrapExpect: 1
  disruptionBudget:
    enabled: true
    maxUnavailable: 1

# Syncs Kubernetes service discovery with Consul
syncCatalog:
  enabled: true
```

Teraz, gdy mamy plik wartości niestandardowych, możemy zainstalować Consul w naszym klastrze AKS

Bash

```bash
helm install -f consul-values.yaml --name consul --namespace consul .
```
Wykres `Consul` Helm służy do wdrażania wielu obiektów. Można wyświetlić listę z danych wyjściowych podanego `helm install` polecenia. Wdrożenie składników Consul może potrwać od 4 do 5 minut, w zależności od środowiska klastra.

> [!NOTE]
> Wszystkie Consulowe zasobniki muszą być zaplanowane do uruchomienia w węzłach systemu Linux. Jeśli w klastrze znajdują się pule węzłów systemu Windows Server oprócz pul węzłów Linux, sprawdź, czy wszystkie Consuly są zaplanowane do uruchomienia w węzłach systemu Linux.

W tym momencie wdrożono Consul w klastrze AKS. Aby upewnić się, że mamy pomyślne wdrożenie Consul, przejdź do następnej sekcji, aby sprawdzić poprawność instalacji Consul.

## <a name="validate-the-consul-installation"></a>Weryfikowanie instalacji Consul

Najpierw upewnij się, że zostały utworzone oczekiwane usługi. Aby wyświetlić uruchomione usługi, użyj polecenia [polecenia kubectl Get SVC][kubectl-get] . Zbadaj `consul` przestrzeń nazw, w której składniki Consul zostały zainstalowane przez wykres Helm: `consul`

```console
kubectl get svc --namespace consul
```

Następujące przykładowe dane wyjściowe przedstawiają usługi, które powinny być teraz uruchomione:

```console
NAME                                 TYPE           CLUSTER-IP     EXTERNAL-IP             PORT(S)                                                                   AGE
consul                               ExternalName   <none>         consul.service.consul   <none>                                                                    38m
consul-consul-connect-injector-svc   ClusterIP      10.0.89.113    <none>                  443/TCP                                                                   40m
consul-consul-dns                    ClusterIP      10.0.166.82    <none>                  53/TCP,53/UDP                                                             40m
consul-consul-server                 ClusterIP      None           <none>                  8500/TCP,8301/TCP,8301/UDP,8302/TCP,8302/UDP,8300/TCP,8600/TCP,8600/UDP   40m
consul-consul-ui                     ClusterIP      10.0.117.164   <none>                  80/TCP                                                                    40m
```

Następnie upewnij się, że zostały utworzone wymagane zasobniki. Użyj polecenia [polecenia kubectl Get][kubectl-get] -Binding i ponownie Zbadaj `consul` przestrzeń nazw:

```console
kubectl get pods --namespace consul
```

Następujące przykładowe dane wyjściowe pokazują, które z nich są uruchomione:

```console
NAME                                                              READY   STATUS    RESTARTS   AGE
consul-consul-7cc9v                                               1/1     Running   0          37m
consul-consul-7klg7                                               1/1     Running   0          37m
consul-consul-connect-injector-webhook-deployment-57f88df8hgmfs   1/1     Running   0          37m
consul-consul-lq8qb                                               1/1     Running   0          37m
consul-consul-server-0                                            1/1     Running   0          37m
consul-consul-server-1                                            1/1     Running   0          36m
consul-consul-server-2                                            1/1     Running   0          36m
consul-consul-sync-catalog-7cf7d5bfff-jjbjv                       1/1     Running   2          37m
```

 Wszystkie z tych zasobników powinny zawierać stan `Running`. Jeśli Twoje zasobniki nie mają tych stanów, Zaczekaj chwilę lub dwa, aż do ich wykonania. Jeśli którykolwiek z raportów zawiera raport o problemie, użyj polecenia [polecenia kubectl opisz pod][kubectl-describe] , aby przejrzeć ich dane wyjściowe i stan.

## <a name="accessing-the-consul-ui"></a>Uzyskiwanie dostępu do interfejsu użytkownika Consul

Interfejs użytkownika Consul został zainstalowany w naszym instalatorze, który zapewnia konfigurację opartą na interfejsie użytkownika dla Consul. Interfejs użytkownika dla Consul nie jest ujawniany publicznie za pośrednictwem zewnętrznego adresu IP. Aby uzyskać dostęp do interfejsów użytkownika dodatku, użyj polecenia [polecenia kubectl port-forward][kubectl-port-forward] . To polecenie tworzy bezpieczne połączenie między komputerem klienckim i odpowiednim pod nim w klastrze AKS.

```bash
kubectl port-forward -n consul svc/consul-consul-ui 8080:80
```
Teraz możesz otworzyć przeglądarkę i wskazać ją `http://localhost:8080/ui` , aby otworzyć interfejs użytkownika Consul. Podczas otwierania interfejsu użytkownika powinny zostać wyświetlone następujące elementy:

![Interfejs użytkownika Consul](./media/consul/consul-ui.png)

## <a name="uninstall-consul-from-aks"></a>Odinstaluj Consul z AKS

> [!WARNING]
> Usunięcie Consul z działającego systemu może skutkować problemami związanymi z ruchem między usługami. Upewnij się, że zostały wprowadzone przepisy dotyczące systemu, aby nadal działały prawidłowo bez Consul przed kontynuowaniem.

### <a name="remove-consul-components-and-namespace"></a>Usuń składniki Consul i przestrzeń nazw

Aby usunąć Consul z klastra AKS, użyj następujących poleceń. Polecenia spowodują `kubectl delete ns` usunięcie wykresu, a polecenie spowoduje usunięcie `consul` przestrzeni nazw. `consul` `helm delete`

```bash
helm delete --purge consul
kubectl delete ns consul
```

## <a name="next-steps"></a>Następne kroki

Aby poznać więcej opcji instalacji i konfiguracji dla usługi Consul, zobacz następujące oficjalne artykuły Consul:

- [Podręcznik instalacji Consul-Helm][consul-install-k8]
- [Opcje instalacji Consul-Helm][consul-install-helm-options]

Możesz również postępować zgodnie z dodatkowymi scenariuszami przy użyciu [przykładowej aplikacji Consul][consul-app-example].

<!-- LINKS - external -->
[Hashicorp]: https://hashicorp.com
[cosul-github]: https://github.com/hashicorp/consul
[helm]: https://helm.sh

[consul-docs-concepts]: https://www.consul.io/docs/platform/k8s/index.html
[consul-github]: https://github.com/hashicorp/consul
[consul-github-releases]: https://github.com/hashicorp/consul/releases
[consul-release-notes]: https://github.com/hashicorp/consul/blob/master/CHANGELOG.md
[consul-install-download]: https://www.consul.io/downloads.html
[consul-install-k8]: https://www.consul.io/docs/platform/k8s/run.html
[consul-install-helm-options]: https://www.consul.io/docs/platform/k8s/helm.html#configuration-values-
[consul-app-example]: https://github.com/hashicorp/demo-consul-101/tree/master/k8s
[install-wsl]: https://docs.microsoft.com/windows/wsl/install-win10

[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[consul-scenario-mtls]: ./consul-mtls.md
[helm-install]: ./kubernetes-helm.md
