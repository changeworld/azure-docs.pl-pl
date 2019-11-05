---
title: Omówienie Azure Monitor kontenerów | Microsoft Docs
description: W tym artykule opisano Azure Monitor kontenerów monitorujących rozwiązanie AKS datacontainer Insights i wartość dostarczaną przez monitorowanie kondycji klastrów AKS i Container Instances na platformie Azure.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 10/15/2019
ms.openlocfilehash: 906ae92b0018430bdda02639642dd66ae2231dce
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73477060"
---
# <a name="azure-monitor-for-containers-overview"></a>Przegląd Azure Monitor dla kontenerów

Azure Monitor for Containers to funkcja przeznaczona do monitorowania wydajności obciążeń kontenerów wdrożonych w ramach Azure Container Instances, zarządzanych klastrów Kubernetes hostowanych w usłudze Azure Kubernetes Service (AKS) lub samodzielnie zarządzanych klastrów Kubernetes hostowanych na Azure Stack. Monitorowanie kontenerów ma krytyczne znaczenie, szczególnie w przypadku korzystania z klastra produkcyjnego w odpowiedniej skali z wieloma aplikacjami.

Azure Monitor for Containers zapewnia wgląd w wydajność przez zbieranie metryk pamięci i procesorów z kontrolerów, węzłów i kontenerów dostępnych w Kubernetes za pośrednictwem interfejsu API metryk. Gromadzone są też dzienniki kontenerów.  Po włączeniu monitorowania z klastrów Kubernetes są automatycznie zbierane metryki i dzienniki za pośrednictwem platformy Log Analytics agenta dla systemu Linux. Metryki są zapisywane w magazynie metryk i dane dziennika są zapisywane w magazynie dzienników skojarzonym z obszarem roboczym [log Analytics](../log-query/log-query-overview.md) . 

![Azure Monitor dla architektury kontenerów](./media/container-insights-overview/azmon-containers-architecture-01.png)
 
## <a name="what-does-azure-monitor-for-containers-provide"></a>Co zapewnia Azure Monitor kontenerów?

Azure Monitor for Containers to kompleksowe środowisko monitorowania wykorzystujące różne funkcje Azure Monitor umożliwiające zrozumienie wydajności i kondycji klastra Kubernetes oraz obciążeń kontenerów. Za pomocą Azure Monitor dla kontenerów można:

* Zidentyfikuj kontenery AKS, które są uruchomione w węźle, i ich średniego użycia procesora i pamięci. Ta wiedza ułatwia identyfikowanie wąskich gardeł zasobów.
* Identyfikuj użycie procesora i pamięci w grupach kontenerów i ich kontenerach hostowanych w Azure Container Instances.  
* Określ, gdzie kontener znajduje się na kontrolerze lub pod. Ta wiedza może pomóc w wyświetleniu ogólnej wydajności kontrolera lub pod. 
* Zapoznaj się z użyciem zasobów obciążeń uruchomionych na hoście, które nie są związane ze standardowymi procesami, które obsługują ten element.
* Zapoznaj się z zachowaniem klastra w obszarze średnie i najcięższe obciążenia. Ta wiedza ułatwia identyfikowanie potrzeb związanych z pojemnością i określanie maksymalnego obciążenia, które może utrzymywać klaster. 
* Konfigurowanie alertów w celu aktywnego powiadamiania użytkownika lub rejestrowania go, gdy użycie procesora i pamięci w węzłach lub kontenerach przekracza progi lub kiedy zmiana stanu kondycji w klastrze odbywa się w ramach infrastruktury, węzłów lub zbiorczej kondycji obciążenia.
* Integracja z usługą [Prometheus](https://prometheus.io/docs/introduction/overview/) w celu wyświetlania metryk aplikacji i obciążeń zbieranych z węzłów i Kubernetes przy użyciu [zapytań](container-insights-log-search.md) w celu utworzenia niestandardowych alertów, pulpitów nawigacyjnych i szczegółowej szczegółowej analizy.

    >[!NOTE]
    >Obsługa usługi Prometheus jest w tej chwili funkcją w publicznej wersji zapoznawczej.
    >

* Monitoruj obciążenia kontenera [wdrożone w aparacie AKS aparatów](https://github.com/microsoft/OMS-docker/tree/aks-engine) lokalnych i [AKS na Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908).

Zapoznaj się z poniższym filmem wideo dostarczającym poziom pośredni szczegółowe, aby uzyskać informacje na temat monitorowania klastra AKS przy użyciu Azure Monitor dla kontenerów.

> [!VIDEO https://www.youtube.com/embed/RjsNmapggPU]

## <a name="how-do-i-access-this-feature"></a>Jak mogę uzyskać dostęp do tej funkcji?

Dostęp do Azure Monitor kontenerów można uzyskać na dwa sposoby, od Azure Monitor lub bezpośrednio z wybranego klastra AKS. Od Azure Monitor masz globalną perspektywę wszystkich wdrożonych kontenerów, które są monitorowane, które nie są, co pozwala na wyszukiwanie i filtrowanie w ramach subskrypcji i grup zasobów, a następnie przechodzenie do szczegółów Azure Monitor dla kontenerów z wybrany kontener.  W przeciwnym razie możesz uzyskać dostęp do funkcji bezpośrednio z wybranego kontenera AKS na stronie AKS.  

![Przegląd metod uzyskiwania dostępu do Azure Monitor kontenerów](./media/container-insights-overview/azmon-containers-experience.png)

Jeśli interesujesz się monitorowaniem i zarządzaniem hostami platformy Docker i kontenera systemu Windows działającym poza programem AKS, aby wyświetlić konfigurację, inspekcję i wykorzystanie zasobów, zobacz [rozwiązanie do monitorowania kontenerów](../../azure-monitor/insights/containers.md).

## <a name="next-steps"></a>Następne kroki

- Aby rozpocząć monitorowanie klastra AKS, zapoznaj się z [tematem jak włączyć Azure monitor dla kontenerów](container-insights-onboard.md) , aby zrozumieć wymagania i dostępne metody umożliwiające monitorowanie. 

- Aby rozpocząć monitorowanie aparatu AKS na platformie Azure Stack lub Kubernetes wdrożonym lokalnie, zapoznaj się z tematem [Konfigurowanie hybrydowych klastrów Kubernetes z Azure monitor dla kontenerów](container-insights-hybrid-setup.md).  
