---
title: Obsługiwane wersje rozwiązania Kubernetes w usłudze Azure Kubernetes Service
description: Zasady pomocy technicznej wersji platformy Kubernetes i cyklem życia klastrów w usłudze Azure Kubernetes Service (AKS)
services: container-service
author: sauryadas
ms.service: container-service
ms.topic: article
ms.date: 09/21/2018
ms.author: saudas
ms.openlocfilehash: 6b55825107ae8872b146b3ad4fde0ef4b917b71d
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "47047993"
---
# <a name="supported-kubernetes-versions-in-azure-kubernetes-service-aks"></a>Obsługiwane wersje rozwiązania Kubernetes w usłudze Azure Kubernetes Service (AKS)

Społeczność rozwiązania Kubernetes zwalnia wersje pomocnicze mniej więcej trzy miesiące. Wersje te obejmują nowe funkcje i ulepszenia. Zwalnia poprawki są częściej (czasami co tydzień), a są przeznaczone wyłącznie dla krytycznych poprawek błędów w wersji pomocniczej. Wersje te poprawki zawierają poprawki luk w zabezpieczeniach lub główne usterki wpływające na ochronę dużej liczby klientów i produktów, działających w oparciu o rozwiązania Kubernetes w środowisku produkcyjnym.

Nowe rozwiązania Kubernetes, wersja pomocnicza jest udostępniana w [acs-engine] [ acs-engine] od pierwszego dnia. Cel poziomu usługi AKS w usługi (SLO) obiekty docelowe, zwalniając wersję pomocniczą dla usługi AKS klastry w ciągu 30 dni, z zastrzeżeniem stabilności wersji.

## <a name="kubernetes-version-support-policy"></a>Zasady pomocy technicznej wersji rozwiązania Kubernetes

AKS obsługuje cztery wersje pomocnicze platformy kubernetes:

- Bieżąca wersja pomocnicza, która jest ogólnie nadrzędnego (n)
- Trzy poprzednie wersje pomocnicze. Wszystkie obsługiwane wersje pomocnicze obsługuje również dwa stabilne poprawki.

Na przykład, jeśli wprowadzono AKS *1.11.x* obecnie Pomoc techniczna jest świadczona również dla *1.10.a* + *1.10.b*, *1.9.c*  +  *1.9d*, *1.8.e* + *1.8F* (gdzie wersji własną literą poprawki są dwie kompilacje najnowszy stabilny).

Jeśli wprowadzono nową wersję pomocniczą najstarsze pomocniczej wersji i poprawki wersji obsługiwane zostały wycofane. 15 dni przed wprowadzeniem nowych wersji pomocniczej i wycofanie mającej się ukazać wersji jest podana za pośrednictwem kanałów aktualizacji usługi Azure. W przykładzie powyżej gdzie *1.11.x* jest zwolniony, wycofane wersje są *1.7.g* + *1.7.h*.

Podczas wdrażania klastra usługi AKS w portalu lub przy użyciu wiersza polecenia platformy Azure, klaster ma zawsze wartość n-1 wersji pomocniczej i najnowszych poprawek. Na przykład, jeśli obsługuje AKS *1.11.x*, *1.10.a* + *1.10.b*, *1.9.c* + *1.9 d* , *1.8.e* + *1.8F*, jest wersja domyślna dla nowych klastrów *1.10.b*.

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

<!-- LINKS - Internal -->
[aks-upgrade]: upgrade-cluster.md