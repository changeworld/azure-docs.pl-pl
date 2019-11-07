---
title: Zbierz dziennik aktywności platformy Azure przy użyciu ustawień diagnostycznych (wersja zapoznawcza) — Azure Monitor | Microsoft Docs
description: Użyj ustawień diagnostycznych do przesyłania dalej dzienników aktywności platformy Azure do dzienników Azure Monitor, Azure Storage lub Azure Event Hubs.
author: bwren
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
ms.author: bwren
ms.date: 10/31/2019
ms.openlocfilehash: 9f8783dc6d3c14b086364639b60273dbae626cee
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73587981"
---
# <a name="collect-azure-activity-log-with-diagnostic-settings-preview"></a>Zbierz dziennik aktywności platformy Azure przy użyciu ustawień diagnostycznych (wersja zapoznawcza)
[Dziennik aktywności platformy Azure](activity-logs-overview.md) to [Dziennik platformy](platform-logs-overview.md) , który zapewnia wgląd w zdarzenia na poziomie subskrypcji, które wystąpiły na platformie Azure. Do tej pory utworzono profil dziennika w celu wysyłania wpisów dziennika aktywności do [centrum zdarzeń lub konta magazynu](activity-log-export.md) i użycia łącznika w celu zebrania ich w [obszarze roboczym log Analytics](activity-log-collect.md).

Teraz można skonfigurować zbieranie dzienników aktywności platformy Azure przy użyciu tych samych [ustawień diagnostycznych](diagnostic-settings.md) , które są używane do zbierania [dzienników zasobów](resource-logs-overview.md). Korzystanie z ustawień diagnostycznych ma następujące zalety niż bieżące metody:

- Spójna Metoda zbierania wszystkich dzienników platformy.
- Zbieraj dziennik aktywności między wieloma subskrypcjami i dzierżawcami.
- Filtruj kolekcję, aby zbierać dzienniki tylko dla określonych kategorii.

## <a name="considerations"></a>Zagadnienia do rozważenia
Przed włączeniem tej funkcji należy wziąć pod uwagę następujące szczegóły dotyczące zbierania dzienników aktywności przy użyciu ustawień diagnostycznych.

- Istniejące kolekcje działań należy wyłączyć przed włączeniem ich przy użyciu ustawień diagnostycznych. Włączenie obu włączonych może spowodować zduplikowanie danych.
- Ustawienie przechowywania dla zbierania dziennika aktywności do usługi Azure Storage zostało usunięte, co oznacza, że dane będą przechowywane w nieskończoność do momentu jego usunięcia.
- Dziennik aktywności można wysłać z wielu subskrypcji do jednego obszaru roboczego Log Analytics. Dziennik aktywności można wysłać z dowolnej subskrypcji do pięciu Log Analytics obszarów roboczych.

## <a name="configure-diagnostic-settings"></a>Konfigurowanie ustawień diagnostycznych
Poniższa procedura umożliwia utworzenie ustawienia diagnostycznego w Azure Portal w celu zebrania dziennika aktywności platformy Azure. Nie można obecnie utworzyć ustawienia diagnostycznego poziomu subskrypcji przy użyciu innych metod.

1. Wyłącz istniejącą kolekcję Log Analytics obszarów roboczych dla dziennika aktywności:
   1. Otwórz menu **log Analytics obszarów roboczych** w Azure Portal i wybierz obszar roboczy, aby zebrać dziennik aktywności.
   2. W sekcji **źródła danych obszaru roboczego** menu obszaru roboczego wybierz pozycję **Dziennik aktywności platformy Azure**.
   3. Kliknij subskrypcję, którą chcesz rozłączyć.
   4. Kliknij przycisk **Rozłącz** , a następnie pozycję **tak** po wyświetleniu monitu, aby potwierdzić wybór.
2. Z menu **Azure monitor** w Azure Portal wybierz pozycję **Dziennik aktywności**.
3. Kliknij pozycję **Ustawienia diagnostyczne**.
   
   ![Ustawienia diagnostyczne](media/diagnostic-settings-subscription/diagnostic-settings.png)
   
4. Kliknij transparent purpurowy dla starszego środowiska i Wyłącz bieżącą kolekcję w obszarze magazyn lub Centra zdarzeń. 

    ![Starsze środowisko](media/diagnostic-settings-subscription/legacy-experience.png)

5. Postępuj zgodnie z procedurami w temacie [Tworzenie ustawień diagnostycznych w Azure Portal](diagnostic-settings.md#create-diagnostic-settings-in-azure-portal) , aby utworzyć ustawienie diagnostyczne. Zobacz [Kategorie w dzienniku aktywności](activity-logs-overview.md#categories-in-the-activity-log) , aby uzyskać wyjaśnienie kategorii, których można użyć do filtrowania zdarzeń z dziennika aktywności. 


## <a name="differences-in-data"></a>Różnice w danych
Ustawienia diagnostyczne zbierają te same dane, jak poprzednie metody służące do zbierania dziennika aktywności z następującymi bieżącymi różnicami:

Następujące właściwości zostały usunięte:

- ActivityStatus
- ActivitySubstatus
- OperationName
- ResourceProvider 

Dodano następujące właściwości:

- Authorization_d
- Claims_d
- Properties_d

## <a name="activity-log-monitoring-solution"></a>Rozwiązanie do monitorowania dzienników aktywności
Rozwiązanie do monitorowania Log Analytics platformy Azure obejmuje wiele zapytań dzienników i widoków służących do analizowania rekordów dziennika aktywności w obszarze roboczym Log Analytics. To rozwiązanie używa danych dzienników zebranych w obszarze roboczym Log Analytics i będzie nadal działały bez żadnych zmian, jeśli zbierasz dziennik aktywności przy użyciu ustawień diagnostycznych. Aby uzyskać szczegółowe informacje na temat tego rozwiązania, zobacz temat [monitorowanie dzienników aktywności — rozwiązanie](activity-log-collect.md#activity-logs-analytics-monitoring-solution) .

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się więcej o dzienniku aktywności](../../azure-resource-manager/resource-group-audit.md)
* [Dowiedz się więcej na temat ustawień diagnostycznych](diagnostic-settings.md)
