---
title: Co się zmieniło w Azure Log Analytics? | Microsoft Docs
description: Ten artykuł zawiera często zadawane pytania dotyczące uaktualniania analizy dzienników dla nowego języka zapytań.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.author: bwren
ms.openlocfilehash: 017a1da233827f19489a99b234ee9009fd9f6fe3
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2017
ms.locfileid: "26039863"
---
# <a name="whats-changed-in-azure-log-analytics"></a>Co się zmieniło w Azure Log Analytics?
Oprócz samego języka kwerend, istnieje kilka ulepszeń i zmiany, które należy zwrócić uwagę, gdy jest obszaru roboczego analizy dzienników [uaktualniony do nowego języka zapytań](log-analytics-log-search-new.md).  W tym artykule krótko opisano zmiany między starszej wersji, jak i uaktualnionych obszaru roboczego z łącza do szczegółowych informacji dla każdego. 

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Whats-changed-in-Azure-Log-Analytics/player]

Zobacz [wyszukiwania — często zadawane pytania i znane problemy dziennika analizy dzienników nowe](log-analytics-log-search-faq.md) opis znanych problemów dotyczących uaktualniania i odpowiedzi na często zadawane pytania.  

## <a name="query-language"></a>Język zapytań
Zmiana głównej uaktualnienia analizy dzienników jest nowy język kwerendy ma następujące istotne ulepszenia przez poprzednie języka.  

Można uzyskać bezpośrednie porównanie typowych operacji między starszej wersji języka i nowy język w [przechodzenia do nowego języka zapytań usługi Analiza dzienników Azure](log-analytics-log-search-transition.md).  Pełna dokumentacja nowy język jest dostępny w [język zapytań Analiza dzienników Azure](https://docs.loganalytics.io).


## <a name="computer-groups"></a>Grupy komputerów
Metoda tworzenia grupy komputerów nie zmieniła się, mimo że teraz musisz podać unikatowego aliasu dla każdego.  Grup komputerów opartych na dziennik wyszukiwania należy użyć składni nowego języka.

Brak nowej składni dotyczące korzystania z grup komputerów w wyszukiwania dziennika.  Zamiast korzystać z funkcji $ComputerGroups, grup komputerów są teraz każdego zaimplementowane jako funkcję z unikatowego aliasu.  Alias jest używany w wyszukiwaniu dziennika jak innych funkcji.  

Szczegółowe informacje są dostępne pod adresem [grup komputerów w analizy dzienników dziennika wyszukiwania](log-analytics-computer-groups.md).


## <a name="log-search-portals"></a>Portale do wyszukiwania w dzienniku
Oprócz portalu wyszukiwania dziennika do tworzenia i uruchamiania dziennika wyszukiwania można teraz używać portalu zaawansowane analizy, która dostarcza funkcje edycji znacznie lepszą.

Krótki opis dwa portale znajduje się w temacie [portali tworzenia i edytowania dziennika zapytań w programie Azure Log Analytics](log-analytics-log-search-portals.md).  Można przeprowadzić za pomocą samouczka na nowy portal na [wprowadzenie do korzystania z portalu usługi analiza](https://docs.loganalytics.io/docs/Learn/Getting-Started/Getting-started-with-the-Analytics-portal).

## <a name="alerts"></a>Alerty
Działają alerty takie same w uaktualnionym obszarów roboczych, ale zapytanie, które są uruchamiane musi być napisana w nowym języku.  Istniejących reguł alertów, które miały przed uaktualnieniem zostaną automatycznie przekonwertowane do nowego języka.  Więcej informacji można uzyskać [opis alertów w analizy dzienników](log-analytics-alerts.md).

Format ładunku dla elementów runbook i elementów webhook wysyłane z alertów została zmieniona.  Uzyskiwanie szczegółowych informacji dla nowych format ładunku w [Dodawanie akcji do reguły alertów w analizy dzienników](log-analytics-alerts-actions.md).

## <a name="dashboards"></a>Pulpity nawigacyjne
[Pulpity nawigacyjne](log-analytics-dashboards.md) nadal trwa przestarzałe.  Można nadal korzystać z żadnych kafelków dodane do pulpitu nawigacyjnego, przed obszaru roboczego został uaktualniony, ale nie można edytować tych kafelków ani dodawać nowe.  Projektant widoków umożliwia tworzenie niestandardowych widoków, które zawiera bogaty zestaw funkcji niż pulpitów nawigacyjnych.

Szczegóły dotyczące projektanta widoków są dostępne pod adresem [użyj widoku Designer do tworzenia niestandardowych widoków w analizy dzienników](log-analytics-view-designer.md).

## <a name="power-bi"></a>Power BI
Proces eksportowania analizy dzienników danych do usługi Power BI została zmieniona dla uaktualnionego obszarów roboczych, a istniejących harmonogramów, które zostały utworzone przed rozpoczęciem uaktualniania zostanie wyłączony.  

Szczegółowe informacje są dostępne pod adresem [eksportowania analizy dzienników danych do usługi Power BI](log-analytics-powerbi.md).

## <a name="powershell"></a>PowerShell
Get-AzureRmOperationalInsightsSearchResults uruchamiania dziennika wyszukiwania z programu PowerShell nie będzie działać z obszarem roboczym uaktualniony.  Użyj Invoke LogAnalyticsQuery dla tej funkcji z obszarem roboczym uaktualniony.

Szczegółowe informacje są dostępne pod adresem [API REST Azure dziennika Analytics — poleceń cmdlet programu PowerShell](https://dev.loganalytics.io/documentation/Tools/PowerShell-Cmdlets).

## <a name="log-search-api"></a>Dziennik wyszukiwania interfejsu API
Dziennik wyszukiwania został zmieniony interfejs API REST, a które starszych wersji muszą zostać zaktualizowane do nowej wersji interfejsu API.   

Szczegółowe informacje o nowej wersji interfejsu API są dostępne pod adresem [interfejsu API REST usługi Analiza dzienników Azure](https://dev.loganalytics.io/).

## <a name="next-steps"></a>Następne kroki

- Zobacz [wyszukiwania — często zadawane pytania i znane problemy dziennika analizy dzienników nowe](log-analytics-log-search-faq.md) opis znanych problemów dotyczących uaktualniania i odpowiedzi na często zadawane pytania.