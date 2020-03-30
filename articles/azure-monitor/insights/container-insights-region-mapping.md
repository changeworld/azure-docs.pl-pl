---
title: Usługa Azure Monitor dla mapowań regionów kontenerów
description: W tym artykule opisano mapowania regionu obsługiwane między usługą Azure Monitor dla kontenerów, obszaru roboczego usługi Log Analytics i metryk niestandardowych.
ms.topic: conceptual
ms.date: 06/26/2019
ms.openlocfilehash: a058f9cac987bb5c7130019f50370c6a176b09ac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75403419"
---
# <a name="region-mappings-supported-by-azure-monitor-for-containers"></a>Mapowania regionów obsługiwane przez usługę Azure Monitor dla kontenerów

 Podczas włączania usługi Azure Monitor dla kontenerów tylko niektóre regiony są obsługiwane do łączenia obszaru roboczego usługi Log Analytics i klastra AKS i zbierania niestandardowych metryk przesłanych do usługi Azure Monitor.

## <a name="log-analytics-workspace-supported-mappings"></a>Mapowania obsługiwane przez obszar roboczy usługi Log Analytics

Zasoby klastra AKS lub obszar roboczy usługi Log Analytics mogą przebywać w innych regionach, a w poniższej tabeli przedstawiono nasze mapowania.

|**Region klastra AKS** | **Region obszaru roboczego analizy dzienników** |
|-----------------------|------------------------------------|
|**Afryka** | |
|PołudniowaAfricaNorth |WestEurope |
|PołudniowaAfrica Zachód |WestEurope |
|**Australia** | |
|AustraliaEast |AustraliaEast |
|AustraliaCentral |AustraliaCentral |
|AustraliaCentral2 |AustraliaCentral |
|AustraliaEast |AustraliaEast |
|**Azja i Pacyfik** | |
|WschodniaZja |WschodniaZja |
|Południowo-wschodnia Azja |Południowo-wschodnia Azja |
|**Brazylia** | |
|BrazyliaSouth | Okręg wyborczy SouthCentralUS |
|**Kanada** ||
|KanadaCentral |KanadaCentral |
|KanadaEast |KanadaCentral |
|**Europa** | |
|FrancjaCentral |FrancjaCentral |
|FrancjaWysuł |FrancjaCentral |
|Europa Północna |Europa Północna |
|UkSouth (polski) |UkSouth (polski) |
|Wielka Brytania Zachód |UkSouth (polski) |
|WestEurope |WestEurope |
|**Indie** | |
|CentralIndia ( CentralIndia ) |CentralIndia ( CentralIndia ) |
|Południowa Ameryka |CentralIndia ( CentralIndia ) |
|Okręg wyborczy WestIndia |CentralIndia ( CentralIndia ) |
|**Japonia** | |
|JaponiaJast |JaponiaJast |
|JaponiaZachód |JaponiaJast |
|**Korea** | |
|KoreaCentral |KoreaCentral |
|KoreaSouth |KoreaCentral |
|**USA** | |
|CentralUS (centralus) |CentralUS (centralus)|
|Okręg wyborczy EastUS |Okręg wyborczy EastUS |
|Okręg wyborczy EastUS2 |Okręg wyborczy EastUS2 |
|Okręg wyborczy WestUS |Okręg wyborczy WestUS |
|Okręg wyborczy WestUS2 |Okręg wyborczy WestUS2 |
|Okręg wyborczy WestCentralUS<sup>1</sup>|Okręg wyborczy EastUS<sup>1</sup>|
|US Gov Wirginia |US Gov Wirginia |

<sup>1</sup> Ze względu na ograniczenia wydajności region nie jest dostępny podczas tworzenia nowych zasobów. Obejmuje to obszar roboczy usługi Log Analytics. Jednak wcześniej powiązanych zasobów w regionie powinny nadal działać.

## <a name="custom-metrics-supported-regions"></a>Obsługiwane regiony metryki niestandardowe

Zbieranie metryk z węzłów klastrów usług Kubernetes platformy Azure (AKS) i zasobników jest obsługiwane do publikowania jako metryki niestandardowe tylko w następujących [regionach platformy Azure.](../platform/metrics-custom-overview.md#supported-regions)

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć monitorowanie klastra usługi AKS, przejrzyj [jak włączyć usługę Azure Monitor dla kontenerów,](container-insights-onboard.md) aby zrozumieć wymagania i dostępne metody umożliwiające monitorowanie.  
