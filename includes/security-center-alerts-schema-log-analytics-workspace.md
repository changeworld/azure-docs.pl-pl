---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: data-factory
author: memildin
ms.service: data-factory
ms.topic: include
ms.date: 03/17/2020
ms.author: memildin
ms.custom: include file
ms.openlocfilehash: 7d81799f7fbdb2b41db421daa1a85ec8cde511eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79538833"
---
### <a name="the-data-model-of-the-schema"></a>Model danych schematu

|Pole|Opis|
|----|----|
|**Nazwa alertu**|Nazwa wyświetlana alertu|
|**Typ alertu**|unikatowy identyfikator alertu|
|**ConfidenceLevel (Poziom zaufania)**|(Opcjonalnie) Poziom ufności tego alertu (wysoki/niski)|
|**ConfidenceScore (ConfidenceScore)**|(Opcjonalnie) Numeryczny wskaźnik zaufania alertu bezpieczeństwa|
|**Opis**|Opis tekstu alertu|
|**Displayname**|Nazwa wyświetlana alertu|
|**EndTime**|Czas zakończenia wpływu alertu (czas ostatniego zdarzenia przyczyniającego się do alertu)|
|**Jednostki**|Lista jednostek związanych z alertem. Wykaz ten może zawierać mieszankę podmiotów różnych typów|
|**Łącza rozszerzone**|(Opcjonalnie) Torba na wszystkie linki związane z alertem. Ta torba może pomieścić mieszankę linków dla różnych typów|
|**Extendedproperties**|Worek dodatkowych pól, które są istotne dla wpisu|
|**IsIncident (Nieident)**|Określa, czy alert jest zdarzeniem, czy alertem regularnym. Zdarzenie to alert zabezpieczeń, który agreguje wiele alertów w jeden incydent zabezpieczeń|
|**ProcessingEndTime (Czas przetwarzania)**|Sygnatura czasowa UTC, w którym utworzono alert|
|**ProductComponentName (Nazwa produktu)**|(Opcjonalnie) Nazwa składnika wewnątrz produktu, który wygenerował alert.|
|**Productname**|stała ("Usługa Azure Security Center")|
|**ProviderName**|Nieużywane|
|**Kroki korygowania**|Ręczne elementy akcji do podjęcia w celu skorygowanie zagrożenia bezpieczeństwa|
|**ResourceId**|Pełny identyfikator zasobu, którego dotyczy problem|
|**Ważność**|Ważność alertu (Wysoka/Średnia/Niska/Informacyjna)|
|**SourceComputerId**|unikatowy identyfikator GUID dla serwera, którego dotyczy problem (jeśli alert jest generowany na serwerze)|
|**SourceSystem**|Nieużywane|
|**Starttime**|Czas rozpoczęcia wpływu alertu (czas pierwszego zdarzenia przyczyniającego się do alertu)|
|**SystemAlertId**|Unikatowy identyfikator tego wystąpienia alertu zabezpieczeń|
|**Identyfikator dzierżawy**|identyfikator nadrzędnej dzierżawy usługi Azure Active w usłudze Azure, w ramach której znajduje się zeskanowany zasób|
|**TimeGenerated**|Sygnatura czasowa UTC, na której dokonano oceny (czas skanowania centrum zabezpieczeń) (identyczna z DiscoveredTimeUTC)|
|**Typ**|stała ("SecurityAlert")|
|**Nazwadostawcy**|Nazwa dostawcy, który dostarczył alert (np.|
|**Identyfikator vendororiginalId**|Nieużywane|
|**Grupa zasobów obszaru roboczego**|w przypadku, gdy alert jest generowany na maszynie Wirtualnej, Serwerze, Zestawie skalowania maszyny wirtualnej lub wystąpieniu usługi App Service, które zgłasza się do obszaru roboczego, zawiera nazwę tej grupy zasobów obszaru roboczego|
|**Obszar roboczySubscriptionId**|w przypadku, gdy alert jest generowany na maszynie Wirtualnej, Serwerze, Zestawie skalowania maszyny wirtualnej lub wystąpieniu usługi App Service, które zgłasza się do obszaru roboczego, zawiera ten identyfikator subskrypcji obszaru roboczego|
|||
