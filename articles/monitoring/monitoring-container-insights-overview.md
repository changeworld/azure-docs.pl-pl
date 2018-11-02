---
title: Omówienie usługi Azure Monitor dla kontenerów (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: W tym artykule opisano usługi Azure Monitor dla kontenerów, które monitoruje szczegółowe informacje o kontenerze AKS rozwiązania i wartość, która zapewnia możliwość przez monitorowanie kondycji klastrów usługi AKS i wystąpienia kontenera na platformie Azure.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/14/2018
ms.author: magoedte
ms.openlocfilehash: b90aa9e3c627708b2640086b2b812b8c7079e5bf
ms.sourcegitcommit: 799a4da85cf0fec54403688e88a934e6ad149001
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/02/2018
ms.locfileid: "50912534"
---
# <a name="azure-monitor-for-containers-preview-overview"></a>Usługa Azure Monitor, aby uzyskać przegląd kontenerów (wersja zapoznawcza)

Usługa Azure Monitor dla kontenerów to funkcja przeznaczona do monitorowania wydajności obciążeń kontenerów wdrożonych na zarządzane klastry Kubernetes hostowanych na platformie Azure Kubernetes Service (AKS). Monitorowanie kontenerów ma krytyczne znaczenie, szczególnie w przypadku korzystania z klastra produkcyjnego na dużą skalę z wieloma aplikacjami.

Usługa Azure Monitor dla kontenerów zapewnia widoczność wydajności na zbieranie pamięci i procesora metryk z kontrolerów, węzły i kontenerów, które są dostępne w usłudze Kubernetes za pomocą interfejsu API metryki. Gromadzone są też dzienniki kontenerów.  Po włączeniu monitorowania z klastrów Kubernetes, te metryki i dzienniki są automatycznie zbierane dla Ciebie za pośrednictwem konteneryzowanych wersję agenta usługi Log Analytics dla systemu Linux i przechowywane w swojej [usługi Log Analytics](../log-analytics/log-analytics-queries.md) obszaru roboczego. 
 
## <a name="what-does-azure-monitor-for-containers-provide"></a>Ile kosztuje usługa Azure Monitor dla kontenery zapewniają?

Usługa Azure Monitor dla kontenerów zawiera kilka wstępnie zdefiniowanych widoków przedstawiających przechowywanych obciążeń kontenerów i co wpływa na kondycji wydajności monitorowanych klastra Kubernetes, aby można było przeprowadzić:  

* Zidentyfikuj kontenerów AKS, które są uruchomione na węźle i ich średnie wykorzystanie procesora i pamięci. Ta wiedza może pomóc w identyfikacji wąskich gardeł zasobów.
* Określ, gdzie kontenera znajduje się w kontrolerze lub zasobnik. Ta wiedza może ułatwić wyświetlanie kontrolera lub zasobnika na ogólną wydajność. 
* Sprawdź wykorzystanie zasobów obciążeń uruchomionych na hoście, które są powiązane z standardowych procesów, które obsługują zasobnik.
* Poznanie zachowań klastra, średnia i największym obciążeniach. Ta wiedza może pomóc zidentyfikować potrzeby związane z pojemnością i określić maksymalnego obciążenia, które klaster może wytrzymać. 

## <a name="how-do-i-access-this-feature"></a>Jak korzystać z tej funkcji?
Można uzyskać dostęp do usługi Azure Monitor dla kontenerów dwa sposoby: z usługi Azure Monitor lub bezpośrednio z poziomu wybranego klastra usługi AKS. Z usługi Azure Monitor, do których masz globalnego perspektywy wszystkie kontenery wdrożone, które mają być monitorowane, i które nie są, co umożliwia wyszukiwanie i filtrowanie w subskrypcji i grupy zasobów i następnie Drąż w dół usługi Azure Monitor dla kontenerów z wybranego kontener.  W przeciwnym razie po prostu korzystać z funkcji bezpośrednio z wybranych kontenerów AKS ze strony usługi AKS.  

![Przegląd metod dostępu do usługi Azure Monitor dla kontenerów](./media/monitoring-container-insights-overview/azmon-containers-views.png)

Jeśli interesuje Cię monitorowania i zarządzania platformy Docker i Windows hostach kontenerów, Wyświetl konfigurację, inspekcji i wykorzystanie zasobów, zobacz [rozwiązanie do monitorowania kontenerów](../log-analytics/log-analytics-containers.md).

## <a name="next-steps"></a>Kolejne kroki
Aby rozpocząć monitorowanie klastra usługi AKS, zapoznaj się z [sposób dołączania Azure Monitor wykrywający sytuacje, kontenery](monitoring-container-insights-onboard.md) , aby zrozumieć wymagania i dostępnych metod, aby włączyć monitorowanie.  
