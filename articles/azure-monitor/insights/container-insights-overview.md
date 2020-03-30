---
title: Omówienie usługi Azure Monitor dla kontenerów | Dokumenty firmy Microsoft
description: W tym artykule opisano usługę Azure Monitor dla kontenerów, które monitoruje rozwiązanie usługi AKS Container Insights i wartość, jaką zapewnia, monitorując kondycję klastrów AKS i wystąpień kontenerów na platformie Azure.
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: 3ff2c35ae9f5838447ce90e2a020649427920a43
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275232"
---
# <a name="azure-monitor-for-containers-overview"></a>Omówienie usługi Azure Monitor dla kontenerów

Usługa Azure Monitor dla kontenerów to funkcja przeznaczona do monitorowania wydajności obciążeń kontenerów wdrożonych w:

- Klastry zarządzanych kubernetes hostowane w [usłudze Azure Kubernetes (AKS)](../../aks/intro-kubernetes.md)
- Samodzielnie zarządzane klastry Kubernetes hostowane na platformie Azure przy użyciu [aparatu AKS](https://github.com/Azure/aks-engine)
- [Azure Container Instances](../../container-instances/container-instances-overview.md)
- Samodzielnie zarządzane klastry kubernetes hostowane w [usłudze Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1910) lub lokalnie
- [Azure Red Hat OpenShift](../../openshift/intro-openshift.md)

Usługa Azure Monitor dla kontenerów obsługuje klastry z systemem operacyjnym Linux i Windows Server 2019. 

Monitorowanie kontenerów ma kluczowe znaczenie, zwłaszcza podczas uruchamiania klastra produkcyjnego na dużą skalę z wieloma aplikacjami.

Usługa Azure Monitor dla kontenerów zapewnia wgląd w wydajność, zbierając metryki pamięci i procesora z kontrolerów, węzłów i kontenerów, które są dostępne w usłudze Kubernetes za pośrednictwem interfejsu API metryk. Gromadzone są też dzienniki kontenerów.  Po włączeniu monitorowania z klastrów Kubernetes metryki i dzienniki są automatycznie zbierane za pośrednictwem konteneryzowanej wersji agenta usługi Log Analytics dla systemu Linux. Metryki są zapisywane w magazynie metryk i dane dziennika jest zapisywany w magazynie dzienników skojarzonych z obszaru roboczego [usługi Log Analytics.](../log-query/log-query-overview.md) 

![Usługa Azure Monitor dla architektury kontenerów](./media/container-insights-overview/azmon-containers-architecture-01.png)
 
## <a name="what-does-azure-monitor-for-containers-provide"></a>Co zapewnia usługa Azure Monitor dla kontenerów?

Usługa Azure Monitor for containers zapewnia kompleksowe środowisko monitorowania przy użyciu różnych funkcji usługi Azure Monitor. Te funkcje umożliwiają zrozumienie wydajności i kondycji klastra Kubernetes z systemem operacyjnym Linux i Windows Server 2019 oraz obciążeń kontenerów. Za pomocą usługi Azure Monitor dla kontenerów można:

* Identyfikowanie kontenerów AKS, które są uruchomione w węźle i ich średnie wykorzystanie procesora i pamięci. Ta wiedza może pomóc w zidentyfikowaniu wąskich gardeł zasobów.
* Identyfikowanie wykorzystania procesora i pamięci grup kontenerów i ich kontenerów hostowanych w wystąpieniach kontenera platformy Azure.  
* Określ, gdzie znajduje się kontener w kontrolerze lub zasobniku. Ta wiedza może pomóc w przeglądaniu ogólnej wydajności kontrolera lub zasobnika. 
* Przejrzyj wykorzystanie zasobów obciążeń uruchomionych na hoście, które nie są związane ze standardowymi procesami obsługującymi zasobnik.
* Zrozumieć zachowanie klastra przy średnich i najcięższych obciążeniach. Ta wiedza może pomóc w zidentyfikowaniu potrzeb w zakresie pojemności i określeniu maksymalnego obciążenia, które może wytrzymać klaster. 
* Skonfiguruj alerty, aby proaktywnie powiadamiać użytkownika lub rejestrować go, gdy wykorzystanie procesora CPU i pamięci w węzłach lub kontenerach przekracza progi lub gdy w klastrze występuje zmiana stanu kondycji w zestawie kondycji infrastruktury lub węzłów.
* Integracja z [Prometheus](https://prometheus.io/docs/introduction/overview/) do wyświetlania metryk aplikacji i obciążenia zbiera z węzłów i Kubernetes przy użyciu [zapytań](container-insights-log-search.md) do tworzenia niestandardowych alertów, pulpitów nawigacyjnych i szczegółowewykonywać szczegółowej analizy.
* Monitoruj obciążenia kontenerów wdrożone w środowisku lokalnym [aks engine](https://github.com/Azure/aks-engine) i [aks engine na platformie Azure Stack.](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908)
* Monitoruj obciążenia kontenerów [wdrożone w usłudze Azure Red Hat OpenShift](../../openshift/intro-openshift.md).

    >[!NOTE]
    >Obsługa platformy Azure Red Hat OpenShift jest funkcją w publicznej wersji zapoznawczej w tej chwili.
    >

Główne różnice w monitorowaniu klastra systemu Windows Server w porównaniu z klastrem systemu Linux są następujące:

- Metryka RSS pamięci nie jest dostępna dla węzła i kontenerów systemu Windows.
- Informacje o pojemności magazynu dysków nie są dostępne dla węzłów systemu Windows.
- Dzienniki kontenerów nie są dostępne dla kontenerów działających w węzłach systemu Windows.
- Obsługa funkcji live data (wersja zapoznawcza) jest dostępna z wyjątkiem dzienników kontenerów systemu Windows.
- Monitorowane są tylko środowiska zasobników, a nie środowiska platformy Docker.
- W wersji zapoznawczej obsługiwanych jest maksymalnie 30 kontenerów systemu Windows Server. To ograniczenie nie ma zastosowania do kontenerów systemu Linux. 

Zapoznaj się z poniższym klipem wideo, w tym z głębokim nurkowaniem na poziomie pośrednim, aby dowiedzieć się więcej o monitorowaniu klastra usługi AKS za pomocą usługi Azure Monitor dla kontenerów.

> [!VIDEO https://www.youtube.com/embed/RjsNmapggPU]

## <a name="how-do-i-access-this-feature"></a>Jak uzyskać dostęp do tej funkcji?

Dostęp do usługi Azure Monitor dla kontenerów można uzyskać na dwa sposoby— z usługi Azure Monitor lub bezpośrednio z wybranego klastra AKS. Z usługi Azure Monitor masz globalną perspektywę wszystkich wdrożonych kontenerów, które są monitorowane, a które nie, co pozwala na wyszukiwanie i filtrowanie w ramach subskrypcji i grup zasobów, a następnie przechodzenie do szczegółów w usłudze Azure Monitor dla kontenerów z wybranego kontenera.  W przeciwnym razie można uzyskać dostęp do funkcji bezpośrednio z wybranego kontenera AKS ze strony AKS.  

![Omówienie metod uzyskiwania dostępu do usługi Azure Monitor dla kontenerów](./media/container-insights-overview/azmon-containers-experience.png)

Jeśli chcesz monitorować hosty kontenerów platformy Docker i Windows działające poza usługą AKS w celu wyświetlania konfiguracji, inspekcji i wykorzystania zasobów, zobacz [rozwiązanie do monitorowania kontenerów](../../azure-monitor/insights/containers.md).

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć monitorowanie klastra usługi Kubernetes, przejrzyj [jak włączyć usługę Azure Monitor dla kontenerów,](container-insights-onboard.md) aby zrozumieć wymagania i dostępne metody umożliwiające monitorowanie. 
