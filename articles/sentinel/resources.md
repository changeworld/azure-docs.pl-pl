---
title: Przydatne zasoby podczas pracy z usługą Azure Sentinel| Dokumenty firmy Microsoft
description: Ten dokument zawiera listę przydatnych zasobów podczas pracy z usługą Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 9b4c8e38-c986-4223-aa24-a71b01cb15ae
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/02/2019
ms.author: yelevin
ms.openlocfilehash: 2110d3319cebf693ef06deec26a29fa655e35035
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77585275"
---
# <a name="useful-resources-for-working-with-azure-sentinel"></a>Przydatne zasoby do pracy z usługą Azure Sentinel



W tym artykule wymieniono zasoby, które mogą pomóc uzyskać więcej informacji na temat pracy z usługą Azure Sentinel.

Łączniki usługi Azure Logic Apps:<https://docs.microsoft.com/connectors/>


## <a name="auditing-and-reporting"></a>Inspekcja i sprawozdawczość
Dzienniki inspekcji usługi Azure Sentinel są przechowywane w [dziennikach aktywności platformy Azure.](../azure-monitor/platform/platform-logs-overview.md)

Następujące obsługiwane operacje mogą być poddane inspekcji.

|Nazwa operacji|    Typ zasobu|
|----|----|
|Tworzenie lub aktualizowanie skoroszytu  |Microsoft.Insights/skoroszyty|
|Usuwanie skoroszytu    |Microsoft.Insights/skoroszyty|
|Ustawianie przepływu pracy   |Microsoft.Logic/przepływy pracy|
|Usuwanie przepływu pracy    |Microsoft.Logic/przepływy pracy|
|Utwórz zapisane wyszukiwanie    |Microsoft.OperationalInsights/workspaces/savedSearches|
|Usuń zapisane wyszukiwanie    |Microsoft.OperationalInsights/workspaces/savedSearches|
|Aktualizuj reguły alertów |Microsoft.SecurityInsights/alertRules|
|Usuń reguły alertów |Microsoft.SecurityInsights/alertRules|
|Akcje odpowiedzi reguły alertu aktualizacji |Microsoft.SecurityInsights/alertRules/actions|
|Usuwanie akcji odpowiedzi reguły alertów |Microsoft.SecurityInsights/alertRules/actions|
|Aktualizowanie zakładek   |Microsoft.SecurityInsights/bookmarks|
|Usuwanie zakładek   |Microsoft.SecurityInsights/bookmarks|
|Aktualizuj sprawy   |Microsoft.SecurityInsights/Cases|
|Aktualizacja dochodzenia w sprawie  |Microsoft.SecurityInsights/Cases/investigations|
|Utwórz komentarze do sprawy   |Microsoft.SecurityInsights/Cases/comments|
|Aktualizowanie łączników danych |Microsoft.SecurityInsights/dataConnectors Microsoft.SecurityInsights/dataConnectors Microsoft.SecurityInsights/dataConnectors Microsoft.|
|Usuwanie łączników danych |Microsoft.SecurityInsights/dataConnectors Microsoft.SecurityInsights/dataConnectors Microsoft.SecurityInsights/dataConnectors Microsoft.|
|Aktualizuj ustawienia    |Microsoft.SecurityInsights/settings|

### <a name="view-audit-and-reporting-data-in-azure-sentinel"></a>Wyświetlanie danych inspekcji i raportowania w usłudze Azure Sentinel

Możesz wyświetlić te dane, przesyłając je strumieniowo z dziennika aktywności platformy Azure do usługi Azure Sentinel, gdzie można następnie przeprowadzić badania i analizy na nim.

1. Połącz źródło danych [działania platformy Azure.](connect-azure-activity.md) Po wykonaniu tej tej pracy zdarzenia inspekcji są przesyłane strumieniowo do nowej tabeli na ekranie **dzienniki** o nazwie AzureActivity.
2. Następnie kwerendy danych przy użyciu KQL, jak każda inna tabela.



## <a name="vendor-documentation"></a>Dokumentacja dostawcy

| **Dostawca**  | **Używanie incydentu w usłudze Azure Sentinel** | **Link**|
|----|----|----|
| GitHub| Służy do uzyskiwania dostępu do strony społeczności| <https://github.com/Azure/Azure-Sentinel> |
| PaloAlto ( PaloAlto )| Konfigurowanie instrumentu CEF| <https://www.paloaltonetworks.com/documentation/misc/cef.html>|
| Pluralsight | Kurs języka Kusto Query Language| [https://www.pluralsight.com/courses/kusto-query-language-kql-from-scratch](https://www.pluralsight.com/courses/kusto-query-language-kql-from-scratch)|

## <a name="blogs-and-forums"></a>Blogi i fora

Opublikuj swoje pytania w [obszarze TechCommunity](https://techcommunity.microsoft.com/t5/Azure-Sentinel/bd-p/AzureSentinel) dla usługi Azure Sentinel.

Wyświetlanie wpisów w blogu usługi Azure Sentinel z [witryn TechCommunity](https://techcommunity.microsoft.com/t5/Azure-Sentinel/bg-p/AzureSentinelBlog) i [Platformy Microsoft Azure](https://azure.microsoft.com/blog/tag/azure-sentinel/).


## <a name="next-steps"></a>Następne kroki
W tym dokumencie masz listę zasobów, które są przydatne podczas pracy z usługą Azure Sentinel. Dodatkowe informacje na temat zabezpieczeń i zgodności platformy Azure znajdziesz w [blogu Microsoft Azure Security and Compliance](https://blogs.msdn.com/b/azuresecurity/).
