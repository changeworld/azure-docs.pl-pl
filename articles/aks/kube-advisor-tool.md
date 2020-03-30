---
title: Sprawdź wdrożenia usługi Kubernetes na platformie Azure, aby zapoznać się z najlepszymi rozwiązaniami
description: Dowiedz się, jak sprawdzić implementację najlepszych rozwiązań dotyczących wdrożeń w usłudze Azure Kubernetes przy użyciu funkcji kube-advisor
services: container-service
author: seanmck
ms.topic: troubleshooting
ms.date: 11/05/2018
ms.author: seanmck
ms.openlocfilehash: 29ea7dba1df8bc7c68e3d17563a51b784ce4a561
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77595437"
---
# <a name="checking-for-kubernetes-best-practices-in-your-cluster"></a>Sprawdzanie najlepszych rozwiązań dotyczących platformy Kubernetes w klastrze

Istnieje kilka najlepszych rozwiązań, które należy wykonać w wdrożeniach usługi Kubernetes, aby zapewnić najlepszą wydajność i odporność aplikacji. Za pomocą narzędzia kube-advisor można szukać wdrożeń, które nie są zgodne z tymi sugestiami.

## <a name="about-kube-advisor"></a>Informacje o kube-advisor

[Narzędzie kube-advisor][kube-advisor-github] to pojedynczy kontener przeznaczony do uruchamiania w klastrze. Wysyła zapytanie do serwera interfejsu API usługi Kubernetes w celu uzyskania informacji o wdrożeniach i zwraca zestaw sugerowanych ulepszeń.

Narzędzie kube-advisor może raportować żądanie zasobów i limity brakujące w PodSpecs dla aplikacji Windows, a także aplikacji Linux, ale samo narzędzie kube-advisor musi być zaplanowane na zasobniku Linux. Można zaplanować zasobnik do uruchomienia w puli węzłów z określonego systemu operacyjnego przy użyciu [selektora węzłów][k8s-node-selector] w konfiguracji zasobnika.

> [!NOTE]
> Narzędzie kube-advisor jest obsługiwane przez firmę Microsoft na zasadzie najlepszych starań. Problemy i sugestie powinny być składane na GitHub.

## <a name="running-kube-advisor"></a>Uruchamianie kube-advisor

Aby uruchomić narzędzie w klastrze skonfigurowanym do [kontroli dostępu opartej na rolach (RBAC),](azure-ad-integration.md)za pomocą następujących poleceń. Pierwsze polecenie tworzy konto usługi Kubernetes. Drugie polecenie uruchamia narzędzie w zasobniku przy użyciu tego konta usługi i konfiguruje zasobnik do usunięcia po jego zamknięciu. 

```bash
kubectl apply -f https://raw.githubusercontent.com/Azure/kube-advisor/master/sa.yaml

kubectl run --rm -i -t kubeadvisor --image=mcr.microsoft.com/aks/kubeadvisor --restart=Never --overrides="{ \"apiVersion\": \"v1\", \"spec\": { \"serviceAccountName\": \"kube-advisor\" } }"
```

Jeśli nie używasz funkcji RBAC, można uruchomić polecenie w następujący sposób:

```bash
kubectl run --rm -i -t kubeadvisor --image=mcr.microsoft.com/aks/kubeadvisor --restart=Never
```

W ciągu kilku sekund powinna zostać wyświetleni tabela opisująca potencjalne ulepszenia wdrożeń.

![Dane wyjściowe kube-advisor](media/kube-advisor-tool/kube-advisor-output.png)

## <a name="checks-performed"></a>Przeprowadzone kontrole

Narzędzie sprawdza poprawność kilku najlepszych rozwiązań firmy Kubernetes, z których każda ma własne sugerowane korygowanie.

### <a name="resource-requests-and-limits"></a>Żądania i limity dotyczące zasobów

Kubernetes obsługuje definiowanie [żądań zasobów i limitów specyfikacji zasobników][kube-cpumem]. Żądanie definiuje minimalny procesor i pamięć wymaganą do uruchomienia kontenera. Limit definiuje maksymalną ilość procesora CPU i pamięci, które powinny być dozwolone.

Domyślnie żadne żądania ani limity nie są ustawiane w specyfikacjach zasobników. Może to prowadzić do węzłów są overscheduled i kontenerów jest głodowany. Narzędzie kube-advisor wyróżnia zasobników bez żądań i limitów ustawionych.

## <a name="cleaning-up"></a>Czyszczenie

Jeśli klaster ma włączoną funkcję RBAC, można wyczyścić `ClusterRoleBinding` narzędzie po uruchomieniu za pomocą następującego polecenia:

```bash
kubectl delete -f https://raw.githubusercontent.com/Azure/kube-advisor/master/sa.yaml
```

Jeśli narzędzie jest uruchomione względem klastra, który nie jest włączony RBAC, nie jest wymagane oczyszczanie.

## <a name="next-steps"></a>Następne kroki

- [Rozwiązywanie problemów z usługą Azure Kubernetes](troubleshooting.md)

<!-- RESOURCES -->

[kube-cpumem]: https://github.com/Azure/azure-quickstart-templates
[kube-advisor-github]: https://github.com/azure/kube-advisor
[k8s-node-selector]: concepts-clusters-workloads.md#node-selectors