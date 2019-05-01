---
title: Obsługiwane wersje rozwiązania Kubernetes w usłudze Azure Kubernetes Service
description: Zasady pomocy technicznej wersji platformy Kubernetes i cyklem życia klastrów w usłudze Azure Kubernetes Service (AKS)
services: container-service
author: sauryadas
ms.service: container-service
ms.topic: article
ms.date: 04/26/2019
ms.author: saudas
ms.openlocfilehash: d4287307ee3ed7f65b91f2865242113aa5b22bfd
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64684170"
---
# <a name="supported-kubernetes-versions-in-azure-kubernetes-service-aks"></a>Obsługiwane wersje rozwiązania Kubernetes w usłudze Azure Kubernetes Service (AKS)

Społeczność platformy Kubernetes wydaje wersje pomocnicze średnio co trzy miesiące. Te wydania zawierają nowe funkcje i ulepszenia. Wydania poprawek odbywają się częściej (czasami co tydzień) i mają na celu naprawienie tylko krytycznych usterek w wersji pomocniczej. Wersje te poprawki zawierają poprawki luk w zabezpieczeniach lub główne usterki wpływające na ochronę dużej liczby klientów i produktów, działających w oparciu o rozwiązania Kubernetes w środowisku produkcyjnym.

Nowe rozwiązania Kubernetes, wersja pomocnicza jest udostępniana w [aparatu aks] [ aks-engine] od pierwszego dnia. Docelowe poziomy usług (SLO, Service Level Objective) usługi AKS wydają wersję pomocniczą dla klastrów AKS w ciągu 30 dni, w zależności od stabilności wydania.

## <a name="kubernetes-version-support-policy"></a>Zasady obsługi wersji platformy Kubernetes

Usługa AKS obsługuje cztery wersje pomocnicze platformy Kubernetes:

- Bieżąca wersja pomocnicza, która jest ogólnie nadrzędnego (n)
- Trzy poprzednie wersje pomocnicze. Każda obsługiwana wersja pomocnicza obsługuje także dwie stabilne poprawki.

Na przykład, jeśli wprowadzono AKS *1.12.x* obecnie Pomoc techniczna jest świadczona również dla *1.11.a* + *1.11.b*, *1.10.c*  +  *1,10 d*, *1.9.e* + *1.9f* (gdzie wersji własną literą poprawki są dwie kompilacje najnowszy stabilny).

Po wprowadzeniu nowej wersji pomocniczej obsługa najstarszej wersji pomocniczej i wydań poprawek jest wycofywana. 30 dni przed wprowadzeniem nowych wersji pomocniczej i wycofanie mającej się ukazać wersji anons jest nawiązywane za pomocą [kanałów aktualizacji usługi Azure][azure-update-channel]. W przykładzie powyżej gdzie *1.12.x* jest zwolniony, wycofane wersje są *1.8.g* + *1.8.h*.

Podczas wdrażania klastra AKS w portalu lub za pomocą interfejsu wiersza polecenia platformy Azure klaster jest zawsze ustawiany na wersję pomocniczą n-1 i najnowszą poprawkę. Na przykład, jeśli obsługuje AKS *1.12.x*, *1.11.a* + *1.11.b*, *1.10.c*  +   *1,10 d*, *1.9.e* + *1.9f*, jest wersja domyślna dla nowych klastrów *1.11.b*.

## <a name="list-currently-supported-versions"></a>Lista aktualnie obsługiwane wersje

Aby dowiedzieć się, jakie wersje są obecnie dostępne dla Twojej subskrypcji oraz regionu, należy użyć [az aks get wersje] [ az-aks-get-versions] polecenia. Poniższy przykład wyświetla listę dostępnych wersji rozwiązania Kubernetes dla *EastUS* regionu:

```azurecli-interactive
az aks get-versions --location eastus --output table
```

Dane wyjściowe są podobne do poniższego przykładu, który pokazuje tę wersję rozwiązania Kubernetes *1.12.5* jest dostępny do najnowszej wersji:

```
KubernetesVersion    Upgrades
-------------------  -----------------------
1.12.5               None available
1.12.4               1.12.5
1.11.7               1.12.4, 1.12.5
1.11.6               1.11.7, 1.12.4, 1.12.5
1.10.12              1.11.6, 1.11.7
1.10.9               1.10.12, 1.11.6, 1.11.7
1.9.11               1.10.9, 1.10.12
1.9.10               1.9.11, 1.10.9, 1.10.12
```

## <a name="faq"></a>Często zadawane pytania

**Co się stanie, gdy klient uaktualni klaster usługi Kubernetes przy użyciu wersji pomocniczej, która nie jest obsługiwana?**

Jeśli użytkownik pracuje na *n-4* wersji, są z poziomu usługi. Jeśli uaktualnienie z wersji n-4 do n-3 zakończy się powodzeniem, jesteś w poziomu usługi. Na przykład:

- W przypadku obsługiwanych wersji usługi AKS *1.12.x*, *1.11.a* + *1.11.b*, *1.10.c*  +  *1,10 d*, i *1.9.e* + *1.9f* i znajdują się na *1.8.g* lub *1.8.h*, znajdą się poza poziomu usługi.
- Jeśli uaktualnienie z *1.8.g* lub *1.8.h* do *1.9.e* lub *1.9.f* zakończy się powodzeniem, się ponownie w celu poziomu usługi.

Uaktualnienia do wersji starszej niż *n-4* nie są obsługiwane. W takich przypadkach firma Microsoft zaleca klientom tworzenie nowych klastrów usługi AKS i ponownie wdrożyć swoje obciążenia.

**Co się stanie, gdy klient skaluje klaster usługi Kubernetes przy użyciu wersji pomocniczej, która nie jest obsługiwana?**

Dla wersji pomocniczej, nie są obsługiwane przez usługę AKS skalowanie do wewnątrz lub na zewnątrz w dalszym ciągu działać bez problemów.

**Można klient Pozostań na wersję rozwiązania Kubernetes nieskończona?**

Tak. Jednakże jeśli klaster nie znajduje się w jednej z wersji obsługiwane przez usługę AKS, klaster jest z poziomu usługi AKS. Azure automatycznie uaktualnić klaster lub usunąć łącznik.

**Jaka wersja jest główny Pomocy technicznej, jeśli klaster agenta nie znajduje się w jednym z obsługiwanych wersji usługi AKS?**

Wzorzec zostanie automatycznie zaktualizowany do najnowszej obsługiwanej wersji.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać informacje na temat uaktualniania klastra, zobacz [Uaktualnianie klastra usługi Azure Kubernetes Service (AKS)][aks-upgrade].

<!-- LINKS - External -->
[aks-engine]: https://github.com/Azure/aks-engine
[azure-update-channel]: https://azure.microsoft.com/updates/?product=kubernetes-service

<!-- LINKS - Internal -->
[aks-upgrade]: upgrade-cluster.md
[az-aks-get-versions]: /cli/azure/aks#az-aks-get-versions
