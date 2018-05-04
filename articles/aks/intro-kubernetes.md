---
title: Wprowadzenie do usługi Azure Container Service dla rozwiązania Kubernetes
description: Usługa Azure Container Service dla rozwiązania Kubernetes ułatwia wdrażanie aplikacji opartych na kontenerach i zarządzanie nimi na platformie Azure.
services: container-service
author: gabrtv
manager: timlt
ms.service: container-service
ms.topic: overview
ms.date: 11/13/2017
ms.author: gamonroy
ms.custom: mvc
ms.openlocfilehash: f418e70c6da46443b95e5fd4056ca26d46d95505
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="introduction-to-azure-container-service-aks-preview"></a>Wprowadzenie do usługi Azure Container Service (AKS) — wersja zapoznawcza

Usługa Azure Container Service (AKS) upraszcza tworzenie i konfigurację klastra maszyn wirtualnych, które są wstępnie skonfigurowane do uruchamiania konteneryzowanych aplikacji, oraz zarządzanie nim. Umożliwia to używanie posiadanych umiejętności lub sięganie po duży i rosnący zasób wiedzy społeczności w celu wdrażania opartych na kontenerze aplikacji platformy Microsoft Azure i zarządzania nimi.

Za pomocą usługi AKS możesz korzystać z funkcji klasy korporacyjnej platformy Azure, zachowując jednocześnie przenośność aplikacji dzięki usłudze Kubernetes i formatowi obrazów Docker.

> [!IMPORTANT]
> Usługa Azure Container Service (AKS) jest obecnie w **wersji zapoznawczej**. Wersje zapoznawcze są udostępniane pod warunkiem udzielenia zgody na [dodatkowe warunki użytkowania](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Niektóre cechy funkcji mogą ulec zmianie, zanim stanie się ona ogólnie dostępna.
>

## <a name="managed-kubernetes-in-azure"></a>Usługa Managed Kubernetes na platformie Azure

Usługa AKS zmniejsza złożoność i nakłady operacyjne związane z zarządzaniem klastrem Kubernetes, przenosząc znaczną część tej odpowiedzialności na platformę Azure. Jako hostowana usługa Kubernetes, platforma Azure obsługuje krytyczne zadania, takie jak monitorowanie kondycji i konserwacja. Ponadto płacisz tylko za węzły agenta w ramach Twoich klastrów, a nie za wzorce. Jako zarządzana usługa Kubernetes, usługa AKS zapewnia:

> [!div class="checklist"]
> * automatyczne poprawki i uaktualnienia wersji platformy Kubernetes,
> * łatwe skalowanie klastra,
> * samonaprawialną hostowaną warstwę kontroli (wzorce),
> * oszczędności — płacisz tylko za uruchomione węzły puli agentów.

Dzięki temu, że platforma Azure obsługuje zarządzanie węzłami w klastrze AKS, nie musisz już ręcznie wykonywać wielu zadań, takich jak uaktualnianie klastra. Ponieważ platforma Azure wykonuje te krytyczne zadania konserwacji za Ciebie, usługa AKS nie zapewnia bezpośredniego dostępu (jak w przypadku użycia protokołu SSH) do klastra.

## <a name="using-azure-container-service-aks"></a>Korzystanie z usługi Azure Container Service (AKS)
Celem usługi Azure Container Service jest zapewnienie środowiska hostingu kontenerów za pomocą narzędzi i technologii typu open source, które już dziś są popularne wśród naszych klientów. W tym celu uwidaczniamy standardowe punkty końcowe interfejsu API rozwiązania Kubernetes. Za pomocą tych standardowych punktów końcowych można wykorzystać dowolne oprogramowanie, które jest w stanie komunikować się z klastrem usługi Kubernetes. Możesz wybrać narzędzie [kubectl][kubectl-overview], [helm][helm] lub [draft][draft].

## <a name="creating-a-kubernetes-cluster-using-azure-container-service-aks"></a>Tworzenie klastra Kubernetes przy użyciu usługi Azure Container Service (AKS)
Aby rozpocząć korzystanie z usługi AKS, musisz wdrożyć klaster usługi AKS przy użyciu [interfejsu wiersza polecenia platformy Azure][aks-quickstart] lub za pośrednictwem portalu (wyszukaj w witrynie Marketplace termin **Azure Container Service**). Jeśli jesteś użytkownikiem zaawansowanym, który potrzebuje mieć większą kontrolę nad szablonami usługi Azure Resource Manager, użyj projektu [acs-engine][acs-engine] typu open source do utworzenia własnego niestandardowego klastra Kubernetes i wdrożenia go za pomocą polecenia interfejsu wiersza polecenia `az`.

### <a name="using-kubernetes"></a>Korzystanie z rozwiązania Kubernetes
Narzędzie Kubernetes automatyzuje proces wdrażania i skalowania aplikacji konteneryzowanych oraz zarządzania nimi. Narzędzie to obejmuje bogaty zestaw funkcji, m.in.:
* automatyczne pakowanie pudełek,
* mechanizm samonaprawiania
* skalowanie w poziomie,
* odnajdywanie usług i równoważenie obciążenia,
* zautomatyzowane wprowadzanie i wycofywanie zmian,
* zarządzanie kluczami tajnymi i konfiguracją,
* aranżacja magazynu,
* wykonywanie partii zadań.

## <a name="videos"></a>Filmy wideo

Usługa Azure Container Service (AKS) — Piątek z Azure, październik 2017:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Container-Orchestration-Simplified-with-Managed-Kubernetes-in-Azure-Container-Service-AKS/player]
>
>

Narzędzia do tworzenia i wdrażania aplikacji w systemie Kubernetes (Azure OpenDev, czerwiec 2017 r.):

> [!VIDEO https://channel9.msdn.com/Events/AzureOpenDev/June2017/Tools-for-Developing-and-Deploying-Applications-on-Kubernetes/player]
>
>

Dowiedz się więcej na temat wdrażania usługi AKS i zarządzania nią z przewodnika Szybki start po usłudze AKS.

> [!div class="nextstepaction"]
> [Samouczek usługi AKS][aks-quickstart]

<!-- LINKS - external -->
[acs-engine]: https://github.com/Azure/acs-engine
[draft]: https://github.com/Azure/draft
[helm]: https://helm.sh/
[kubectl-overview]: https://kubernetes.io/docs/user-guide/kubectl-overview/

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md

