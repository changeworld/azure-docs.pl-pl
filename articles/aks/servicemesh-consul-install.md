---
title: Instalowanie konsula w usłudze Azure Kubernetes (AKS)
description: Dowiedz się, jak zainstalować i używać konsula do tworzenia siatki usługi w klastrze usługi Azure Kubernetes Service (AKS)
author: dstrebel
ms.topic: article
ms.date: 10/09/2019
ms.author: dastrebe
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 1601ab6d81b888fd2247e95f22c58e1fc91df698
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273736"
---
# <a name="install-and-use-consul-in-azure-kubernetes-service-aks"></a>Instalowanie i używanie konsula w usłudze Azure Kubernetes Service (AKS)

[Konsul][consul-github] to siatka usługi typu open source, która zapewnia kluczowy zestaw funkcji w mikrousługach w klastrze Kubernetes. Funkcje te obejmują odnajdowanie usług, sprawdzanie kondycji, segmentację usług i obserwowalność. Aby uzyskać więcej informacji na temat konsula, zobacz [oficjalnej Co to jest konsul?][consul-docs-concepts]

W tym artykule pokazano, jak zainstalować konsula. Składniki konsula są instalowane w klastrze Kubernetes w uzywki AKS.

> [!NOTE]
> Instrukcje te odnoszą `1.6.0`się do wersji konsula i używać co najmniej wersji `2.14.2`Helm .
>
> Wydania konsula `1.6.x` mogą być uruchamiane przeciwko `1.13+`wersji Kubernetes . Dodatkowe wersje konsula można znaleźć w [GitHub - Konsul Zwolnienia][consul-github-releases] i informacje o każdej z wydań w [Konsul- Release Notes][consul-release-notes].

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Zainstaluj komponenty Konsula na AKS
> * Zatwierdzenie instalacji konsula
> * Odinstaluj konsula z AKS

## <a name="before-you-begin"></a>Przed rozpoczęciem

Kroki opisane w tym artykule założono, że utworzono klaster AKS (Kubernetes `1.13` i `kubectl` powyżej, z włączoną funkcją RBAC) i nawiązałeś połączenie z klastrem. Jeśli potrzebujesz pomocy w przypadku któregokolwiek z tych elementów, zobacz [przewodnik Szybki start usługi AKS][aks-quickstart]. Upewnij się, że klaster ma co najmniej 3 węzły w puli węzłów systemu Linux.

Musisz [Helm][helm] postępować zgodnie z tymi instrukcjami i zainstalować Konsula. Zaleca się, aby w klastrze poprawnie zainstalowano i skonfigurowano najnowszą stabilną wersję. Jeśli potrzebujesz pomocy dotyczącej instalacji Helm, zapoznaj się z [instrukcjami instalacji AKS Helm][helm-install]. Wszystkie zasobniki konsula muszą być również zaplanowane do uruchomienia w węzłach Linuksa.

Ten artykuł dzieli wytyczne dotyczące instalacji konsula na kilka dyskretnych kroków. Wynik końcowy jest taki sam w strukturze jak oficjalne [wytyczne][consul-install-k8]dotyczące instalacji konsula.

### <a name="install-the-consul-components-on-aks"></a>Zainstaluj komponenty Konsula na AKS

Zaczniemy od pobrania wersji `v0.10.0` wykresu Konsula Helma. Ta wersja wykresu zawiera `1.6.0`wersję konsula .

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download](includes/servicemesh/consul/download-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [macOS - download](includes/servicemesh/consul/download-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download](includes/servicemesh/consul/download-powershell.md)]

::: zone-end

Użyj helm i `consul-helm` pobranego wykresu, aby `consul` zainstalować składniki Konsula w obszarze nazw w klastrze AKS. 

> [!NOTE]
> **Opcje instalacji**
> 
> W ramach naszej instalacji korzystamy z następujących opcji:
> - `connectInject.enabled=true`- umożliwić wstrzyknowienie serwerów proxy do strąków
> - `client.enabled=true`- umożliwienie klientom konsula uruchamiania na każdym węźle
> - `client.grpc=true`- włączyć odbiornik gRPC dla connectInject
> - `syncCatalog.enabled=true`- synchronizacja Usług Kubernetes i Konsula
>
> **Selektory węzłów**
>
> Konsul obecnie musi być zaplanowane do uruchomienia w węzłach Linuksa. Jeśli w klastrze znajdują się węzły systemu Windows Server, należy upewnić się, że zasobniki konsula są zaplanowane do uruchamiania tylko w węzłach systemu Linux. Użyjemy [selektorów węzłów,][kubernetes-node-selectors] aby upewnić się, że zasobniki są zaplanowane do odpowiednich węzłów.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - install Istio components](includes/servicemesh/consul/install-components-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - install Istio components](includes/servicemesh/consul/install-components-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - install Istio components](includes/servicemesh/consul/install-components-powershell.md)]

::: zone-end

Wykres `Consul` Helm wdraża wiele obiektów. Możesz zobaczyć listę z danych `helm install` wyjściowych polecenia powyżej. Wdrożenie składników konsula może potrwać około 3 minut, w zależności od środowiska klastra.

W tym momencie został wdrożony konsul do klastra AKS. Aby upewnić się, że mamy udane rozmieszczenie konsula, przejdźmy do następnej sekcji, aby potwierdzić instalację konsula.

## <a name="validate-the-consul-installation"></a>Zatwierdzenie instalacji konsula

Upewnij się, że zasoby zostały pomyślnie utworzone. Użyj [kubectl get svc][kubectl-get] i [kubectl get poleceń zasobnika][kubectl-get] do kwerendy `consul` obszaru `helm install` nazw, gdzie składniki konsula zostały zainstalowane przez polecenie:

```console
kubectl get svc --namespace consul --output wide
kubectl get pod --namespace consul --output wide
```

Poniższe przykładowe dane wyjściowe pokazują usługi i zasobników (zaplanowane w węzłach systemu Linux), które powinny być teraz uruchomione:

```output
NAME                                 TYPE           CLUSTER-IP    EXTERNAL-IP             PORT(S)                                                                   AGE     SELECTOR
consul                               ExternalName   <none>        consul.service.consul   <none>                                                                    38s     <none>
consul-consul-connect-injector-svc   ClusterIP      10.0.98.102   <none>                  443/TCP                                                                   3m26s   app=consul,component=connect-injector,release=consul
consul-consul-dns                    ClusterIP      10.0.46.194   <none>                  53/TCP,53/UDP                                                             3m26s   app=consul,hasDNS=true,release=consul
consul-consul-server                 ClusterIP      None          <none>                  8500/TCP,8301/TCP,8301/UDP,8302/TCP,8302/UDP,8300/TCP,8600/TCP,8600/UDP   3m26s   app=consul,component=server,release=consul
consul-consul-ui                     ClusterIP      10.0.50.188   <none>                  80/TCP                                                                    3m26s   app=consul,component=server,release=consul

NAME                                                              READY   STATUS    RESTARTS   AGE    IP            NODE                            NOMINATED NODE   READINESS GATES
consul-consul-connect-injector-webhook-deployment-99f74fdbcr5zj   1/1     Running   0          3m9s   10.240.0.68   aks-linux-92468653-vmss000002   <none>           <none>
consul-consul-jbksc                                               1/1     Running   0          3m9s   10.240.0.44   aks-linux-92468653-vmss000001   <none>           <none>
consul-consul-jkwtq                                               1/1     Running   0          3m9s   10.240.0.70   aks-linux-92468653-vmss000002   <none>           <none>
consul-consul-server-0                                            1/1     Running   0          3m9s   10.240.0.91   aks-linux-92468653-vmss000002   <none>           <none>
consul-consul-server-1                                            1/1     Running   0          3m9s   10.240.0.38   aks-linux-92468653-vmss000001   <none>           <none>
consul-consul-server-2                                            1/1     Running   0          3m9s   10.240.0.10   aks-linux-92468653-vmss000000   <none>           <none>
consul-consul-sync-catalog-d846b79c-8ssr8                         1/1     Running   2          3m9s   10.240.0.94   aks-linux-92468653-vmss000002   <none>           <none>
consul-consul-tz2t5                                               1/1     Running   0          3m9s   10.240.0.12   aks-linux-92468653-vmss000000   <none>           <none>
```

Wszystkie strąki powinny wykazywać `Running`status . Jeśli zasobniki nie mają tych stanów, poczekaj minutę lub dwie, aż to zrobią. Jeśli jakiekolwiek zasobników zgłosić problem, użyj [kubectl opisać pod][kubectl-describe] polecenia, aby przejrzeć ich dane wyjściowe i stan.

## <a name="accessing-the-consul-ui"></a>Uzyskiwanie dostępu do interfejsu konsula

Interfejs konsula został zainstalowany w naszej konfiguracji powyżej i zapewnia konfigurację opartą na interfejsie dla konsula. Interfejs konsula nie jest ujawniony publicznie za pośrednictwem zewnętrznego adresu IP. Aby uzyskać dostęp do interfejsu użytkownika konsula, użyj polecenia [kubectl port-forward.][kubectl-port-forward] To polecenie tworzy bezpieczne połączenie między komputerem klienckim a odpowiednim zasobnikiem w klastrze AKS.

```console
kubectl port-forward -n consul svc/consul-consul-ui 8080:80
```

Teraz możesz otworzyć przeglądarkę i `http://localhost:8080/ui` wskazać ją, aby otworzyć interfejs konsula. Po otwarciu interfejsu użytkownika powinny zostać wyświetlene następujące informacje:

![Konsul UI](./media/servicemesh/consul/consul-ui.png)

## <a name="uninstall-consul-from-aks"></a>Odinstaluj konsula z AKS

> [!WARNING]
> Usunięcie Konsula z uruchomionego systemu może spowodować problemy związane z ruchem między twoimi usługami. Upewnij się, że dokonałeś przepisów dotyczących prawidłowego działania systemu bez konsula przed kontynuowaniem.

### <a name="remove-consul-components-and-namespace"></a>Usuwanie składników konsula i przestrzeni nazw

Aby usunąć konsula z klastra AKS, użyj następujących poleceń. Polecenia `helm delete` usunie `consul` wykres, a `kubectl delete namespace` polecenie usunie `consul` obszar nazw.

```console
helm delete --purge consul
kubectl delete namespace consul
```

## <a name="next-steps"></a>Następne kroki

Aby zapoznać się z większą liczesz możliwościami instalacji i konfiguracji konsula, zapoznaj się z następującymi oficjalnymi artykułami konsula:

- [Konsul - Przewodnik instalacji helm][consul-install-k8]
- [Konsul - Opcje instalacji helm][consul-install-helm-options]

Można również wykonać dodatkowe scenariusze za pomocą:

- [Przykładowa aplikacja konsula][consul-app-example]

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
[kubernetes-node-selectors]: https://docs.microsoft.com/azure/aks/concepts-clusters-workloads#node-selectors

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[consul-scenario-mtls]: ./consul-mtls.md
[helm-install]: ./kubernetes-helm.md
