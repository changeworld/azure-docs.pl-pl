---
title: Obsługiwane wersje rozwiązania Kubernetes w usłudze Azure Kubernetes Service
description: Zasady pomocy technicznej wersji platformy Kubernetes i cyklem życia klastrów w usłudze Azure Kubernetes Service (AKS)
services: container-service
author: sauryadas
ms.service: container-service
ms.topic: article
ms.date: 09/21/2018
ms.author: saudas
ms.openlocfilehash: a17e2fa4bef6890d59f7e66c6ede349f8dee3b8a
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2018
ms.locfileid: "51280457"
---
# <a name="supported-kubernetes-versions-in-azure-kubernetes-service-aks"></a>Obsługiwane wersje rozwiązania Kubernetes w usłudze Azure Kubernetes Service (AKS)

Społeczność platformy Kubernetes wydaje wersje pomocnicze średnio co trzy miesiące. Te wydania zawierają nowe funkcje i ulepszenia. Wydania poprawek odbywają się częściej (czasami co tydzień) i mają na celu naprawienie tylko krytycznych usterek w wersji pomocniczej. Wersje te poprawki zawierają poprawki luk w zabezpieczeniach lub główne usterki wpływające na ochronę dużej liczby klientów i produktów, działających w oparciu o rozwiązania Kubernetes w środowisku produkcyjnym.

Nowe rozwiązania Kubernetes, wersja pomocnicza jest udostępniana w [acs-engine] [ acs-engine] od pierwszego dnia. Docelowe poziomy usług (SLO, Service Level Objective) usługi AKS wydają wersję pomocniczą dla klastrów AKS w ciągu 30 dni, w zależności od stabilności wydania.

## <a name="kubernetes-version-support-policy"></a>Zasady obsługi wersji platformy Kubernetes

Usługa AKS obsługuje cztery wersje pomocnicze platformy Kubernetes:

- Bieżąca wersja pomocnicza, która jest ogólnie nadrzędnego (n)
- Trzy poprzednie wersje pomocnicze. Każda obsługiwana wersja pomocnicza obsługuje także dwie stabilne poprawki.

Na przykład, jeśli wprowadzono AKS *1.11.x* obecnie Pomoc techniczna jest świadczona również dla *1.10.a* + *1.10.b*, *1.9.c* + *1.9d*, *1.8.e* + *1.8F* (gdzie wersji własną literą poprawki są dwie kompilacje najnowszy stabilny).

Po wprowadzeniu nowej wersji pomocniczej obsługa najstarszej wersji pomocniczej i wydań poprawek jest wycofywana. 15 dni przed wprowadzeniem nowych wersji pomocniczej i wycofanie mającej się ukazać wersji anons jest nawiązywane za pomocą [kanałów aktualizacji usługi Azure][azure-update-channel]. W przykładzie powyżej gdzie *1.11.x* jest zwolniony, wycofane wersje są *1.7.g* + *1.7.h*.

Podczas wdrażania klastra AKS w portalu lub za pomocą interfejsu wiersza polecenia platformy Azure klaster jest zawsze ustawiany na wersję pomocniczą n-1 i najnowszą poprawkę. Na przykład, jeśli obsługuje AKS *1.11.x*, *1.10.a* + *1.10.b*, *1.9.c* + *1.9 d* , *1.8.e* + *1.8F*, jest wersja domyślna dla nowych klastrów *1.10.b*.

## <a name="list-currently-supported-versions"></a>Lista aktualnie obsługiwane wersje

Aby dowiedzieć się, jakie wersje są obecnie dostępne dla Twojej subskrypcji oraz regionu, należy użyć [az aks get wersje] [ az-aks-get-versions] polecenia. Poniższy przykład wyświetla listę dostępnych wersji rozwiązania Kubernetes dla *EastUS* regionu:

```azurecli-interactive
az aks get-versions --location eastus --output table
```

Dane wyjściowe są podobne do poniższego przykładu, który pokazuje tę wersję rozwiązania Kubernetes *1.11.3* jest dostępny do najnowszej wersji:

```
KubernetesVersion    Upgrades
-------------------  ----------------------
1.11.3               None available
1.11.2               1.11.3
1.10.8               1.11.2, 1.11.3
1.10.7               1.10.8, 1.11.2, 1.11.3
1.9.10               1.10.7, 1.10.8
1.9.9                1.9.10, 1.10.7, 1.10.8
1.8.15               1.9.9, 1.9.10
1.8.14               1.8.15, 1.9.9, 1.9.10
```

## <a name="faq"></a>Często zadawane pytania

**Co się stanie, gdy klient uaktualni klaster usługi Kubernetes przy użyciu wersji pomocniczej, która nie jest obsługiwana?**

Jeśli użytkownik pracuje na *n-4* wersji, są z poziomu usługi. Jeśli uaktualnienie z wersji n-4 do n-3 zakończy się powodzeniem, jesteś w poziomu usługi. Na przykład:

- W przypadku obsługiwanych wersji usługi AKS *1.10.a* + *1.10.b*, *1.9.c* + *1.9d*,  *1.8.e* + *1.8F* i znajdują się na *1.7.g* lub *1.7.h*, znajdą się poza poziomu usługi.
- Jeśli uaktualnienie z *1.7.g* lub *1.7.h* do *1.8.e* lub *1.8.f* zakończy się powodzeniem, się ponownie w celu poziomu usługi.

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
[acs-engine]: https://github.com/Azure/acs-engine
[azure-update-channel]: https://azure.microsoft.com/updates/?product=kubernetes-service

<!-- LINKS - Internal -->
[aks-upgrade]: upgrade-cluster.md
[az-aks-get-versions]: /cli/azure/aks#az-aks-get-versions
