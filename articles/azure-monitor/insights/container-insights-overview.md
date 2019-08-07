---
title: Omówienie usługi Azure Monitor dla kontenerów | Dokumentacja firmy Microsoft
description: W tym artykule opisano usługi Azure Monitor dla kontenerów, które monitoruje szczegółowe informacje o kontenerze AKS rozwiązania i wartość, która zapewnia możliwość przez monitorowanie kondycji klastrów usługi AKS i wystąpienia kontenera na platformie Azure.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/02/2019
ms.author: magoedte
ms.openlocfilehash: e1fca1532352c4955f478df70f818ee3151053c1
ms.sourcegitcommit: d060947aae93728169b035fd54beef044dbe9480
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2019
ms.locfileid: "68742204"
---
# <a name="azure-monitor-for-containers-overview"></a>Usługa Azure Monitor, aby uzyskać przegląd kontenerów

Usługa Azure Monitor dla kontenerów to funkcja przeznaczona do monitorowania wydajności obciążeń kontenerów wdrożonych do usługi Azure Container Instances lub zarządzanych klastrów Kubernetes hostowanych na platformie Azure Kubernetes Service (AKS). Monitorowanie kontenerów ma krytyczne znaczenie, szczególnie w przypadku korzystania z klastra produkcyjnego na dużą skalę z wieloma aplikacjami.

Usługa Azure Monitor dla kontenerów zapewnia widoczność wydajności na zbieranie pamięci i procesora metryk z kontrolerów, węzły i kontenerów, które są dostępne w usłudze Kubernetes za pomocą interfejsu API metryki. Gromadzone są też dzienniki kontenerów.  Po włączeniu monitorowania z klastrów Kubernetes są automatycznie zbierane metryki i dzienniki za pośrednictwem platformy Log Analytics agenta dla systemu Linux. Metryki są zapisywane w magazynie metryk i dane dziennika są zapisywane w magazynie dzienników skojarzonym z obszarem roboczym [log Analytics](../log-query/log-query-overview.md) . 

![Azure Monitor dla architektury kontenerów](./media/container-insights-overview/azmon-containers-architecture-01.png)
 
## <a name="what-does-azure-monitor-for-containers-provide"></a>Ile kosztuje usługa Azure Monitor dla kontenery zapewniają?

Azure Monitor for Containers to kompleksowe środowisko monitorowania wykorzystujące różne funkcje Azure Monitor umożliwiające zrozumienie wydajności i kondycji klastra Kubernetes oraz obciążeń kontenerów. Za pomocą Azure Monitor dla kontenerów można:

* Zidentyfikuj kontenerów AKS, które są uruchomione na węźle i ich średnie wykorzystanie procesora i pamięci. Ta wiedza może pomóc w identyfikacji wąskich gardeł zasobów.
* Zidentyfikuj wykorzystanie procesora i pamięci grup kontenerów i ich kontenerów, hostowana w usłudze Azure Container Instances.  
* Określ, gdzie kontener znajduje się na kontrolerze lub pod. Ta wiedza może ułatwić wyświetlanie kontrolera lub zasobnika na ogólną wydajność.
* Sprawdź wykorzystanie zasobów obciążeń uruchomionych na hoście, które są powiązane z standardowych procesów, które obsługują zasobnik.
* Poznanie zachowań klastra, średnia i największym obciążeniach. Ta wiedza może pomóc zidentyfikować potrzeby związane z pojemnością i określić maksymalnego obciążenia, które klaster może wytrzymać. 
* Konfigurowanie alertów w celu aktywnego powiadamiania użytkownika lub rejestrowania go, gdy użycie procesora i pamięci w węzłach lub kontenerach przekracza wartości progowe.
* Integracja z usługą [Prometheus](https://prometheus.io/docs/introduction/overview/) w celu wyświetlania metryk aplikacji i obciążeń zbieranych z węzłów i Kubernetes przy użyciu [zapytań](container-insights-log-search.md) w celu utworzenia niestandardowych alertów, pulpitów nawigacyjnych i szczegółowej szczegółowej analizy.

    >[!NOTE]
    >Obsługa usługi Prometheus jest w tej chwili funkcją w publicznej wersji zapoznawczej.
    >

* Monitoruj obciążenia kontenerów [wdrożone w aparacie AKS](https://github.com/microsoft/OMS-docker/tree/aks-engine).

Zapoznaj się z poniższym filmem wideo dostarczającym poziom pośredni szczegółowe, aby uzyskać informacje na temat monitorowania klastra AKS przy użyciu Azure Monitor dla kontenerów.

> [!VIDEO https://www.youtube.com/embed/RjsNmapggPU]

## <a name="how-do-i-access-this-feature"></a>Jak korzystać z tej funkcji?

Można uzyskać dostęp do usługi Azure Monitor dla kontenerów dwa sposoby: z usługi Azure Monitor lub bezpośrednio z poziomu wybranego klastra usługi AKS. Od Azure Monitor masz globalną perspektywę wszystkich wdrożonych kontenerów, które są monitorowane, które nie są, co pozwala na wyszukiwanie i filtrowanie w ramach subskrypcji i grup zasobów, a następnie przechodzenie do szczegółów Azure Monitor dla kontenerów z wybrany kontener.  W przeciwnym razie możesz uzyskać dostęp do funkcji bezpośrednio z wybranego kontenera AKS na stronie AKS.  

![Przegląd metod dostępu do usługi Azure Monitor dla kontenerów](./media/container-insights-overview/azmon-containers-experience.png)

Jeśli interesujesz się monitorowaniem i zarządzaniem hostami platformy Docker i kontenera systemu Windows działającym poza programem AKS, aby wyświetlić konfigurację, inspekcję i wykorzystanie zasobów, zobacz [rozwiązanie do monitorowania kontenerów](../../azure-monitor/insights/containers.md).

## <a name="next-steps"></a>Kolejne kroki

Aby rozpocząć monitorowanie klastra AKS, zapoznaj się z [tematem jak włączyć Azure monitor dla kontenerów](container-insights-onboard.md) , aby zrozumieć wymagania i dostępne metody umożliwiające monitorowanie.  
