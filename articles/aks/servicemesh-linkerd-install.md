---
title: Instalowanie usługi Linkerd w usłudze Azure Kubernetes (AKS)
description: Dowiedz się, jak zainstalować i używać usługi Linkerd do tworzenia siatki usługi w klastrze usługi Azure Kubernetes Service (AKS)
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 419b61527b68299c82dec4f2f5da6b0220859cc1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77593739"
---
# <a name="install-linkerd-in-azure-kubernetes-service-aks"></a>Instalowanie usługi Linkerd w usłudze Azure Kubernetes (AKS)

[Linkerd][linkerd-github] to siatka usług open source i [projekt inkubacji CNCF.][linkerd-cncf] Linkerd to ultralekka siatka usługi, która zapewnia funkcje, które obejmują zarządzanie ruchem, tożsamość usługi i bezpieczeństwo, niezawodność i zauważalność. Aby uzyskać więcej informacji na temat linkerd, zobacz oficjalne [Linkerd CZĘSTO zadawane pytania][linkerd-faq] i [konsolid architecture][linkerd-architecture] dokumentacji.

W tym artykule pokazano, jak zainstalować Linkerd. Plik binarny klienta Linkerd `linkerd` jest zainstalowany na komputerze klienckim, a składniki Linkerd są instalowane w klastrze Kubernetes w u usługi AKS.

> [!NOTE]
> Te instrukcje odwołują się do wersji `stable-2.6.0`Linkerd .
>
> Linkerd `stable-2.6.x` można uruchomić przeciwko wersji `1.13+`Kubernetes . Dodatkowe wersje stabilne i edge Linkerd można znaleźć w [GitHub - Linkerd Releases][linkerd-github-releases].

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Pobierz i zainstaluj plik binarny klienta linkerd
> * Instalowanie Linkerd na AKS
> * Sprawdzanie poprawności instalacji linkerd
> * Uzyskiwanie dostępu do pulpitu nawigacyjnego
> * Odinstalowywanie linkerd z AKS

## <a name="before-you-begin"></a>Przed rozpoczęciem

Kroki opisane w tym artykule założono, że utworzono klaster AKS (Kubernetes `1.13` i `kubectl` powyżej, z włączoną funkcją RBAC) i nawiązałeś połączenie z klastrem. Jeśli potrzebujesz pomocy w przypadku któregokolwiek z tych elementów, zobacz [przewodnik Szybki start usługi AKS][aks-quickstart].

Wszystkie zasobniki Linkerd muszą być zaplanowane do uruchomienia w węzłach systemu Linux - ta konfiguracja jest domyślna w metodzie instalacji wyszczególnionej poniżej i nie wymaga dodatkowej konfiguracji.

W tym artykule dzieli wskazówki dotyczące instalacji konsoli Linkerd na kilka dyskretnych kroków. Wynik jest taki sam w strukturze jak oficjalne Linkerd pierwsze [wskazówki][linkerd-getting-started].

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download and install client binary](includes/servicemesh/linkerd/install-client-binary-linux.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [MacOS - download and install client binary](includes/servicemesh/linkerd/install-client-binary-macos.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download and install client binary](includes/servicemesh/linkerd/install-client-binary-windows.md)]

::: zone-end

## <a name="install-linkerd-on-aks"></a>Instalowanie Linkerd na AKS

Zanim zainstalujemy Linkerd, uruchomimy kontrole przed instalacją, aby ustalić, czy płaszczyzna sterowania może być zainstalowana w naszym klastrze AKS:

```console
linkerd check --pre
```

Powinien zostać wyświetlony coś takiego, aby wskazać, że klaster AKS jest prawidłowym celem instalacji dla konsoli Linkerd:

```console
kubernetes-api
--------------
√ can initialize the client
√ can query the Kubernetes API

kubernetes-version
------------------
√ is running the minimum Kubernetes API version
√ is running the minimum kubectl version

pre-kubernetes-setup
--------------------
√ control plane namespace does not already exist
√ can create Namespaces
√ can create ClusterRoles
√ can create ClusterRoleBindings
√ can create CustomResourceDefinitions
√ can create PodSecurityPolicies
√ can create ServiceAccounts
√ can create Services
√ can create Deployments
√ can create CronJobs
√ can create ConfigMaps
√ no clock skew detected

pre-kubernetes-capability
-------------------------
√ has NET_ADMIN capability
√ has NET_RAW capability

pre-linkerd-global-resources
----------------------------
√ no ClusterRoles exist
√ no ClusterRoleBindings exist
√ no CustomResourceDefinitions exist
√ no MutatingWebhookConfigurations exist
√ no ValidatingWebhookConfigurations exist
√ no PodSecurityPolicies exist

linkerd-version
---------------
√ can determine the latest version
√ cli is up-to-date

Status check results are √
```

Teraz nadszedł czas, aby zainstalować składniki Linkerd. Użyj `linkerd` plików `kubectl` binarnych, aby zainstalować składniki Linkerd w klastrze AKS. Obszar `linkerd` nazw zostanie utworzony automatycznie, a składniki zostaną zainstalowane w tym obszarze nazw.

```console
linkerd install | kubectl apply -f -
```

Linkerd wdraża wiele obiektów. Zobaczysz listę z danych wyjściowych `linkerd install` polecenia powyżej. Wdrożenie składników konsoli Linkerd powinno potrwać około 1 minuty, w zależności od środowiska klastra.

W tym momencie wdrożono linkerd do klastra AKS. Aby upewnić się, że mamy pomyślne wdrożenie Linkerd, przejdźmy do następnej sekcji, aby [sprawdzić poprawność instalacji linkerd](#validate-the-linkerd-installation).

## <a name="validate-the-linkerd-installation"></a>Sprawdzanie poprawności instalacji linkerd

Upewnij się, że zasoby zostały pomyślnie utworzone. Użyj [kubectl get svc][kubectl-get] i [kubectl get poleceń zasobnika][kubectl-get] do kwerendy obszaru `linkerd` `linkerd install` nazw, gdzie składniki Linkerd zostały zainstalowane przez polecenie:

```console
kubectl get svc --namespace linkerd --output wide
kubectl get pod --namespace linkerd --output wide
```

Poniższe przykładowe dane wyjściowe pokazują usługi i zasobników (zaplanowane w węzłach systemu Linux), które powinny być teraz uruchomione:

```console
NAME                     TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)             AGE  SELECTOR
linkerd-controller-api   ClusterIP   10.0.110.67    <none>        8085/TCP            66s  linkerd.io/control-plane-component=controller
linkerd-destination      ClusterIP   10.0.224.29    <none>        8086/TCP            66s  linkerd.io/control-plane-component=controller
linkerd-dst              ClusterIP   10.0.225.148   <none>        8086/TCP            66s  linkerd.io/control-plane-component=destination
linkerd-grafana          ClusterIP   10.0.61.124    <none>        3000/TCP            65s  linkerd.io/control-plane-component=grafana
linkerd-identity         ClusterIP   10.0.6.104     <none>        8080/TCP            67s  linkerd.io/control-plane-component=identity
linkerd-prometheus       ClusterIP   10.0.27.168    <none>        9090/TCP            65s  linkerd.io/control-plane-component=prometheus
linkerd-proxy-injector   ClusterIP   10.0.100.133   <none>        443/TCP             64s  linkerd.io/control-plane-component=proxy-injector
linkerd-sp-validator     ClusterIP   10.0.221.5     <none>        443/TCP             64s  linkerd.io/control-plane-component=sp-validator
linkerd-tap              ClusterIP   10.0.18.14     <none>        8088/TCP,443/TCP    64s  linkerd.io/control-plane-component=tap
linkerd-web              ClusterIP   10.0.37.108    <none>        8084/TCP,9994/TCP   66s  linkerd.io/control-plane-component=web

NAME                                      READY   STATUS    RESTARTS   AGE   IP            NODE                            NOMINATED NODE   READINESS GATES
linkerd-controller-66ddc9f94f-cm9kt       3/3     Running   0          66s   10.240.0.50   aks-linux-16165125-vmss000001   <none>           <none>
linkerd-destination-c94bc454-qpkng        2/2     Running   0          66s   10.240.0.78   aks-linux-16165125-vmss000002   <none>           <none>
linkerd-grafana-6868fdcb66-4cmq2          2/2     Running   0          65s   10.240.0.69   aks-linux-16165125-vmss000002   <none>           <none>
linkerd-identity-74d8df4b85-tqq8f         2/2     Running   0          66s   10.240.0.48   aks-linux-16165125-vmss000001   <none>           <none>
linkerd-prometheus-699587cf8-k8ghg        2/2     Running   0          65s   10.240.0.41   aks-linux-16165125-vmss000001   <none>           <none>
linkerd-proxy-injector-6556447f64-n29wr   2/2     Running   0          64s   10.240.0.32   aks-linux-16165125-vmss000000   <none>           <none>
linkerd-sp-validator-56745cd567-v4x7h     2/2     Running   0          64s   10.240.0.6    aks-linux-16165125-vmss000000   <none>           <none>
linkerd-tap-5cd9fc566-ct988               2/2     Running   0          64s   10.240.0.15   aks-linux-16165125-vmss000000   <none>           <none>
linkerd-web-774c79b6d5-dhhwf              2/2     Running   0          65s   10.240.0.70   aks-linux-16165125-vmss000002   <none>           <none>
```

Linkerd udostępnia polecenie `linkerd` za pośrednictwem pliku binarnego klienta, aby sprawdzić, czy płaszczyzna sterowania Linkerd została pomyślnie zainstalowana i skonfigurowana.

```console
linkerd check
```

Powinieneś zobaczyć coś takiego, aby wskazać, że instalacja zakończyła się pomyślnie:

```console
kubernetes-api
--------------
√ can initialize the client
√ can query the Kubernetes API

kubernetes-version
------------------
√ is running the minimum Kubernetes API version
√ is running the minimum kubectl version

linkerd-config
--------------
√ control plane Namespace exists
√ control plane ClusterRoles exist
√ control plane ClusterRoleBindings exist
√ control plane ServiceAccounts exist
√ control plane CustomResourceDefinitions exist
√ control plane MutatingWebhookConfigurations exist
√ control plane ValidatingWebhookConfigurations exist
√ control plane PodSecurityPolicies exist

linkerd-existence
-----------------
√ 'linkerd-config' config map exists
√ heartbeat ServiceAccount exist
√ control plane replica sets are ready
√ no unschedulable pods
√ controller pod is running
√ can initialize the client
√ can query the control plane API

linkerd-api
-----------
√ control plane pods are ready
√ control plane self-check
√ [kubernetes] control plane can talk to Kubernetes
√ [prometheus] control plane can talk to Prometheus
√ no invalid service profiles

linkerd-version
---------------
√ can determine the latest version
√ cli is up-to-date

control-plane-version
---------------------
√ control plane is up-to-date
√ control plane and cli versions match

Status check results are √
```

## <a name="access-the-dashboard"></a>Uzyskiwanie dostępu do pulpitu nawigacyjnego

Linkerd jest wyposażony w pulpit nawigacyjny, który zapewnia wgląd w siatki usług i obciążeń. Aby uzyskać dostęp do `linkerd dashboard` pulpitu nawigacyjnego, użyj polecenia. To polecenie wykorzystuje [port kubectl][kubectl-port-forward] do przodu, aby utworzyć bezpieczne połączenie między komputerem klienckim a odpowiednimi zasobnikami w klastrze AKS. Następnie automatycznie otworzy pulpit nawigacyjny w domyślnej przeglądarce.

```console
linkerd dashboard
```

Polecenie utworzy również port do przodu i zwróci łącze dla pulpitów nawigacyjnych Grafana.

```console
Linkerd dashboard available at:
http://127.0.0.1:50750
Grafana dashboard available at:
http://127.0.0.1:50750/grafana
Opening Linkerd dashboard in the default browser
```

## <a name="uninstall-linkerd-from-aks"></a>Odinstalowywanie linkerd z AKS

> [!WARNING]
> Usunięcie linkerd z uruchomionego systemu może spowodować problemy związane z ruchem między usługami. Upewnij się, że zostały wprowadzone przepisy dla systemu nadal działać poprawnie bez Linkerd przed kontynuowaniem.

Najpierw musisz usunąć serwery proxy płaszczyzny danych. Usuń wszystkie [adnotacje automatycznego][linkerd-automatic-proxy-injection] wtrysku serwera proxy z obszarów nazw obciążenia i wdrożyć wdrożenia obciążenia. Obciążenia nie powinny już mieć żadnych skojarzonych składników płaszczyzny danych.

Na koniec zdejmij płaszczyznę sterowania w następujący sposób:

```console
linkerd install --ignore-cluster | kubectl delete -f -
```

## <a name="next-steps"></a>Następne kroki

Aby zapoznać się z większą liczesz liczniejszy instalacja i konfiguracja opcje pod kątem Linkerd, zobacz ten kolejne urzędowy Połączerd wytyczne:

- [Linkerd - Instalacja steru][linkerd-install-with-helm]
- [Linkerd - Instalacja wieloetapowa w celu uwzględnienia przywilejów ról][linkerd-multi-stage-installation]

Można również wykonać dodatkowe scenariusze za pomocą:

- [Linkerd emojivoto demo][linkerd-demo-emojivoto]
- [Prezentacja książek Linkerd][linkerd-demo-books]

<!-- LINKS - external -->

[linkerd]: https://linkerd.io/
[linkerd-cncf]: https://landscape.cncf.io/selected=linkerd
[linkerd-faq]: https://linkerd.io/2/faq/
[linkerd-architecture]: https://linkerd.io/2/reference/architecture/
[linkerd-getting-started]: https://linkerd.io/2/getting-started/
[linkerd-overview]: https://linkerd.io/2/overview/
[linkerd-github]: https://github.com/linkerd/linkerd2
[linkerd-github-releases]: https://github.com/linkerd/linkerd2/releases/

[linkerd-install-with-helm]: https://linkerd.io/2/tasks/install-helm/
[linkerd-multi-stage-installation]: https://linkerd.io/2/tasks/install/#multi-stage-install
[linkerd-automatic-proxy-injection]: https://linkerd.io/2/features/proxy-injection/

[linkerd-demo-emojivoto]: https://linkerd.io/2/getting-started/#step-5-install-the-demo-app
[linkerd-demo-books]: https://linkerd.io/2/tasks/books/

[helm]: https://helm.sh

[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
