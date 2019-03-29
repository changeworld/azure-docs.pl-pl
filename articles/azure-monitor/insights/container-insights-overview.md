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
ms.date: 03/27/2019
ms.author: magoedte
ms.openlocfilehash: a31380c8581503a340c55c374afc02c6e1fa290b
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2019
ms.locfileid: "58577173"
---
# <a name="azure-monitor-for-containers-overview"></a>Usługa Azure Monitor, aby uzyskać przegląd kontenerów

Usługa Azure Monitor dla kontenerów to funkcja przeznaczona do monitorowania wydajności obciążeń kontenerów wdrożonych do usługi Azure Container Instances lub zarządzanych klastrów Kubernetes hostowanych na platformie Azure Kubernetes Service (AKS). Monitorowanie kontenerów ma krytyczne znaczenie, szczególnie w przypadku korzystania z klastra produkcyjnego na dużą skalę z wieloma aplikacjami.

Usługa Azure Monitor dla kontenerów zapewnia widoczność wydajności na zbieranie pamięci i procesora metryk z kontrolerów, węzły i kontenerów, które są dostępne w usłudze Kubernetes za pomocą interfejsu API metryki. Gromadzone są też dzienniki kontenerów.  Po włączeniu monitorowania z klastrów Kubernetes, te metryki i dzienniki są automatycznie zbierane dla Ciebie za pośrednictwem konteneryzowanych wersję agenta usługi Log Analytics dla systemu Linux i przechowywane w swojej [usługi Log Analytics](../log-query/log-query-overview.md) obszaru roboczego. 
 
## <a name="what-does-azure-monitor-for-containers-provide"></a>Ile kosztuje usługa Azure Monitor dla kontenery zapewniają?

Usługa Azure Monitor dla kontenerów zawiera kilka wstępnie zdefiniowanych widoków przedstawiających przechowywanych obciążeń kontenerów i co wpływa na kondycji wydajności monitorowanych klastra Kubernetes, aby można było przeprowadzić:  

* Zidentyfikuj kontenerów AKS, które są uruchomione na węźle i ich średnie wykorzystanie procesora i pamięci. Ta wiedza może pomóc w identyfikacji wąskich gardeł zasobów.
* Zidentyfikuj wykorzystanie procesora i pamięci grup kontenerów i ich kontenerów, hostowana w usłudze Azure Container Instances.  
* Określ, gdzie kontenera znajduje się w kontrolerze lub zasobnik. Ta wiedza może ułatwić wyświetlanie kontrolera lub zasobnika na ogólną wydajność.
* Sprawdź wykorzystanie zasobów obciążeń uruchomionych na hoście, które są powiązane z standardowych procesów, które obsługują zasobnik.
* Poznanie zachowań klastra, średnia i największym obciążeniach. Ta wiedza może pomóc zidentyfikować potrzeby związane z pojemnością i określić maksymalnego obciążenia, które klaster może wytrzymać. 

Można również skonfigurować alerty, aby aktywnie powiadomienie, lub zapisz go, gdy wykorzystanie Procesora i pamięci na węzłach lub kontenerów przekroczy progi usługi.  

## <a name="how-do-i-access-this-feature"></a>Jak korzystać z tej funkcji?
Można uzyskać dostęp do usługi Azure Monitor dla kontenerów dwa sposoby: z usługi Azure Monitor lub bezpośrednio z poziomu wybranego klastra usługi AKS. Z usługi Azure Monitor, do których masz globalnego perspektywy wszystkie kontenery wdrożone, które mają być monitorowane, i które nie są, co umożliwia wyszukiwanie i filtrowanie w subskrypcji i grupy zasobów i następnie Drąż w dół usługi Azure Monitor dla kontenerów z wybranego kontener.  W przeciwnym razie po prostu korzystać z funkcji bezpośrednio z wybranych kontenerów AKS ze strony usługi AKS.  

![Przegląd metod dostępu do usługi Azure Monitor dla kontenerów](./media/container-insights-overview/azmon-containers-views-1812.png)

Jeśli interesuje Cię monitorowania i zarządzania platformy Docker i Windows hostach kontenerów, Wyświetl konfigurację, inspekcji i wykorzystanie zasobów, zobacz [rozwiązanie do monitorowania kontenerów](../../azure-monitor/insights/containers.md).

## <a name="next-steps"></a>Kolejne kroki
Aby rozpocząć monitorowanie klastra usługi AKS, zapoznaj się z [sposób dołączania Azure Monitor wykrywający sytuacje, kontenery](container-insights-onboard.md) , aby zrozumieć wymagania i dostępnych metod, aby włączyć monitorowanie.  
