---
title: Usługa Azure Monitor do mapowania regionów kontenerów
description: W tym artykule opisano mapowania regionu, obsługiwany między usługi Azure Monitor dla kontenerów, obszar roboczy usługi Log Analytics i metryki niestandardowe.
services: azure-monitor
ms.service: azure-monitor
ms.workload: infrastructure-services
author: mgoedtel
ms.author: magoedte
ms.date: 06/26/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 481a2a400be4e983e0a2337a200324061494efa1
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2019
ms.locfileid: "67518081"
---
# <a name="region-mappings-supported-by-azure-monitor-for-containers"></a>Mapowania regionu, obsługiwany przez usługi Azure Monitor dla kontenerów

 Podczas włączania usługi Azure Monitor dla kontenerów, tylko niektóre regiony są obsługiwane w przypadku łączenia z obszarem roboczym usługi Log Analytics i klaster AKS i zbieranie metryk niestandardowych przesłane do usługi Azure Monitor.

## <a name="log-analytics-workspace-supported-mappings"></a>Obszar roboczy usługi log Analytics obsługiwanych mapowań

Zasoby klastra usługi AKS lub obszar roboczy usługi Log Analytics może znajdować się w innych regionach, a w poniższej tabeli przedstawiono naszych mapowania.

|**Region klastra AKS** | **Region obszaru roboczego analizy dzienników** |
|-----------------------|------------------------------------|
|**Afryka** | |
|SouthAfricaNorth |WestEurope |
|SouthAfricaWest |WestEurope |
|**Australia** | |
|AustraliaEast |AustraliaEast |
|AustraliaCentral |AustraliaCentral |
|AustraliaCentral2 |AustraliaCentral |
|AustraliaEast |AustraliaEast |
|**Azja i Pacyfik** | |
|EastAsia |EastAsia |
|SoutheastAsia |SoutheastAsia |
|**Brazylia** | |
|BrazilSouth | SouthCentralUS |
|**Kanada** ||
|CanadaCentral |CanadaCentral |
|CanadaEast |CanadaCentral |
|**Europa** | |
|FranceCentral |FranceCentral |
|FranceSouth |FranceCentral |
|NorthEurope |NorthEurope |
|UKSouth |UKSouth |
|UKWest |UKSouth |
|WestEurope |WestEurope |
|**Indie** | |
|CentralIndia |CentralIndia |
|SouthIndia |CentralIndia |
|WestIndia |CentralIndia |
|**Japonia** | |
|JapanEast |JapanEast |
|JapanWest |JapanEast |
|**Korea** | |
|KoreaCentral |KoreaCentral |
|KoreaSouth |KoreaCentral |
|**USA** | |
|CentralUS |CentralUS|
|EastUS |EastUS |
|EastUS2 |EastUS2 |
|WestUS |WestUS |
|WestUS2 |WestUS2 |
|WestCentralUS<sup>1</sup>|EastUS<sup>1</sup>|

<sup>1</sup> ze względu na ograniczenia wydajności region nie jest dostępna podczas tworzenia nowych zasobów. W tym obszar roboczy usługi Log Analytics. Jednak istniejących połączonych zasobów w regionie powinny nadal działać.

## <a name="custom-metrics-supported-regions"></a>Metryki niestandardowe obsługiwane regiony

Zbieranie metryk z usługi Azure Kubernetes (AKS) klastrów węzłów i zasobników są obsługiwane w przypadku publikowania jako metryki niestandardowe tylko w następujących [regionów świadczenia usługi Azure](../platform/metrics-custom-overview.md#supported-regions).

## <a name="next-steps"></a>Kolejne kroki

Aby rozpocząć monitorowanie klastra usługi AKS, zapoznaj się z [sposób włączania usługi Azure Monitor dla kontenerów](container-insights-onboard.md) , aby zrozumieć wymagania i dostępnych metod, aby włączyć monitorowanie.  