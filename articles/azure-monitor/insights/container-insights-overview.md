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
ms.date: 04/25/2019
ms.author: magoedte
ms.openlocfilehash: d6321564672097fbf901d1d33afac9f606fcb63a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65521838"
---
# <a name="azure-monitor-for-containers-overview"></a>Usługa Azure Monitor, aby uzyskać przegląd kontenerów

Usługa Azure Monitor dla kontenerów to funkcja przeznaczona do monitorowania wydajności obciążeń kontenerów wdrożonych do usługi Azure Container Instances lub zarządzanych klastrów Kubernetes hostowanych na platformie Azure Kubernetes Service (AKS). Monitorowanie kontenerów ma krytyczne znaczenie, szczególnie w przypadku korzystania z klastra produkcyjnego na dużą skalę z wieloma aplikacjami.

Usługa Azure Monitor dla kontenerów zapewnia widoczność wydajności na zbieranie pamięci i procesora metryk z kontrolerów, węzły i kontenerów, które są dostępne w usłudze Kubernetes za pomocą interfejsu API metryki. Gromadzone są też dzienniki kontenerów.  Po włączeniu monitorowania z klastrów Kubernetes, metryki i dzienniki są automatycznie zbierane dla Ciebie za pośrednictwem konteneryzowanych wersję agenta usługi Log Analytics dla systemu Linux. Metryki są zapisywane w magazynie metryki i dane dziennika zostają zapisane w magazynie dzienniki skojarzone z Twojej [usługi Log Analytics](../log-query/log-query-overview.md) obszaru roboczego. 

![Usługa Azure Monitor dla architektury kontenerów](./media/container-insights-overview/azmon-containers-architecture-01.png)
 
## <a name="what-does-azure-monitor-for-containers-provide"></a>Ile kosztuje usługa Azure Monitor dla kontenery zapewniają?

Usługa Azure Monitor dla kontenerów zapewnia kompleksowe środowisko monitorowania przy użyciu różnych funkcji usługi Azure Monitor, dzięki któremu można zapoznać się z wydajności i kondycji klastra Kubernetes i obciążeń kontenerów. Dzięki usłudze Azure Monitor dla kontenerów można wykonywać następujące czynności:

* Zidentyfikuj kontenerów AKS, które są uruchomione na węźle i ich średnie wykorzystanie procesora i pamięci. Ta wiedza może pomóc w identyfikacji wąskich gardeł zasobów.
* Zidentyfikuj wykorzystanie procesora i pamięci grup kontenerów i ich kontenerów, hostowana w usłudze Azure Container Instances.  
* Określ, gdzie kontenera znajduje się w kontrolerze lub zasobnik. Ta wiedza może ułatwić wyświetlanie kontrolera lub zasobnika na ogólną wydajność.
* Sprawdź wykorzystanie zasobów obciążeń uruchomionych na hoście, które są powiązane z standardowych procesów, które obsługują zasobnik.
* Poznanie zachowań klastra, średnia i największym obciążeniach. Ta wiedza może pomóc zidentyfikować potrzeby związane z pojemnością i określić maksymalnego obciążenia, które klaster może wytrzymać. 
* Skonfiguruj alerty proaktywne powiadomienie, lub zapisz go, gdy wykorzystanie Procesora i pamięci na węzłach lub kontenerów przekroczy progi usługi.  

## <a name="how-do-i-access-this-feature"></a>Jak korzystać z tej funkcji?
Można uzyskać dostęp do usługi Azure Monitor dla kontenerów dwa sposoby: z usługi Azure Monitor lub bezpośrednio z poziomu wybranego klastra usługi AKS. Z usługi Azure Monitor mają globalne perspektywy wszystkie kontenery wdrożone, które są monitorowane i które nie są, co umożliwia wyszukiwanie i filtrowanie w Twojej subskrypcji i grup zasobów, a następnie Drąż w dół usługi Azure Monitor dla kontenerów z Wybrany kontener.  W przeciwnym razie można uzyskać dostępu do funkcji bezpośrednio z wybranych kontenerów AKS ze strony usługi AKS.  

![Przegląd metod dostępu do usługi Azure Monitor dla kontenerów](./media/container-insights-overview/azmon-containers-experience.png)

Jeśli interesuje Cię monitorowania i zarządzania platformy Docker i Windows hostach kontenerów, Wyświetl konfigurację, inspekcji i wykorzystanie zasobów, zobacz [rozwiązanie do monitorowania kontenerów](../../azure-monitor/insights/containers.md).

## <a name="next-steps"></a>Kolejne kroki
Aby rozpocząć monitorowanie klastra usługi AKS, zapoznaj się z [sposób włączania usługi Azure Monitor dla kontenerów](container-insights-onboard.md) , aby zrozumieć wymagania i dostępnych metod, aby włączyć monitorowanie.  
