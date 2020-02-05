---
title: Zbierz dziennik aktywności platformy Azure przy użyciu ustawień diagnostycznych (wersja zapoznawcza) — Azure Monitor | Microsoft Docs
description: Użyj ustawień diagnostycznych do przesyłania dalej dzienników aktywności platformy Azure do dzienników Azure Monitor, Azure Storage lub Azure Event Hubs.
author: bwren
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
ms.author: bwren
ms.date: 02/04/2020
ms.openlocfilehash: fcdcef5d63163b24fe5de0f547dc2dde00cd674f
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77016259"
---
# <a name="update-to-azure-activity-log-collection-and-export"></a>Aktualizacja kolekcji i eksportu dzienników aktywności platformy Azure
[Dziennik aktywności platformy Azure](platform-logs-overview.md) to [Dziennik platformy](platform-logs-overview.md) , który zapewnia wgląd w zdarzenia na poziomie subskrypcji, które wystąpiły na platformie Azure. Metoda wysyłania wpisów dziennika aktywności do [centrum zdarzeń lub konta magazynu](activity-log-export.md) lub do [log Analyticsego obszaru roboczego](activity-log-collect.md) została zmieniona w celu korzystania z [ustawień diagnostycznych](diagnostic-settings.md). W tym artykule opisano różnice między metodami oraz sposób czyszczenia starszych ustawień w obszarze przygotowanie do zmiany ustawień diagnostycznych.


## <a name="differences-between-methods"></a>Różnice między metodami

### <a name="advantages"></a>Zalety
Korzystanie z ustawień diagnostycznych ma następujące zalety niż bieżące metody:

- Spójna Metoda zbierania wszystkich dzienników platformy.
- Zbieraj dziennik aktywności między wieloma subskrypcjami i dzierżawcami.
- Filtruj kolekcję, aby zbierać dzienniki tylko dla określonych kategorii.
- Zbierz wszystkie kategorie dziennika aktywności. Niektóre kategorie nie są zbierane przy użyciu starszej metody.
- Szybsze opóźnienie w przypadku pozyskiwania dziennika. Poprzednia metoda ma około 15 minut opóźnienia, podczas gdy ustawienia diagnostyczne są dodawane tylko o 1 minutę.

### <a name="considerations"></a>Zagadnienia do rozważenia
Przed włączeniem tej funkcji należy wziąć pod uwagę następujące szczegóły dotyczące zbierania dzienników aktywności przy użyciu ustawień diagnostycznych.

- Ustawienie przechowywania dla zbierania dziennika aktywności do usługi Azure Storage zostało usunięte, co oznacza, że dane będą przechowywane w nieskończoność do momentu jego usunięcia.
- Obecnie można utworzyć ustawienie diagnostyczne poziomu subskrypcji tylko przy użyciu Azure Portal. Aby użyć innych metod, takich jak PowerShell lub CLI, można utworzyć szablon Menedżer zasobów.


### <a name="differences-in-data"></a>Różnice w danych
Ustawienia diagnostyczne zbierają te same dane, jak poprzednie metody służące do zbierania dziennika aktywności z następującymi bieżącymi różnicami:

Następujące kolumny zostały usunięte. Zastąpienie tych kolumn jest w innym formacie, więc może być konieczne zmodyfikowanie zapytań dziennika, które z nich korzystają. W schemacie mogą być nadal widoczne usunięte kolumny, ale nie będą one wypełniane danymi.

| Usunięto kolumnę | Kolumna zastępująca |
|:---|:---|
| Element ActivityStatus    | Element ActivityStatusValue    |
| ActivitySubstatus | ActivitySubstatusValue |
| OperationName     | OperationNameValue     |
| ResourceProvider  | ResourceProviderValue  |

Dodano następującą kolumnę:

- Authorization_d
- Claims_d
- Properties_d

> [!IMPORTANT]
> W niektórych przypadkach wartości w tych kolumnach mogą być pisane wielkimi literami. Jeśli masz zapytanie zawierające te kolumny, należy użyć [operatora = ~](https://docs.microsoft.com/azure/kusto/query/datatypes-string-operators) do wykonania porównania bez uwzględniania wielkości liter.

## <a name="work-with-legacy-settings"></a>Pracuj ze starszymi ustawieniami
Starsze ustawienia zbierania dziennika aktywności będą nadal działały, jeśli nie zdecydujesz się zamienić na ustawienie diagnostyczne. Użyj następującej metody, aby zarządzać profilem dziennika dla subskrypcji.

1. Z menu **Azure monitor** w Azure Portal wybierz pozycję **Dziennik aktywności**.
3. Kliknij pozycję **Ustawienia diagnostyczne**.

   ![Ustawienia diagnostyczne](media/diagnostic-settings-subscription/diagnostic-settings.png)

4. Kliknij transparent purpurowy dla starszego środowiska.

    ![Starsze środowisko](media/diagnostic-settings-subscription/legacy-experience.png)


Zobacz następujące artykuły, aby uzyskać szczegółowe informacje na temat używania starszych metod zbierania danych.

- [Zbieranie i analizowanie dzienników aktywności platformy Azure w obszarze roboczym Log Analytics w Azure Monitor](activity-log-collect.md)
- [Zbieranie dzienników aktywności platformy Azure w Azure Monitor między dzierżawami Azure Active Directory](activity-log-collect-tenants.md)
- [Eksportowanie dziennika aktywności platformy Azure do magazynu lub Event Hubs platformy Azure](activity-log-export.md)

## <a name="disable-existing-settings"></a>Wyłącz istniejące ustawienia
Istniejące kolekcje działań należy wyłączyć przed włączeniem ich przy użyciu ustawień diagnostycznych. Włączenie obu włączonych może spowodować zduplikowanie danych.

### <a name="disable-collection-into-log-analytics-workspace"></a>Wyłącz zbieranie danych w obszarze roboczym Log Analytics

1. Otwórz menu **log Analytics obszarów roboczych** w Azure Portal i wybierz obszar roboczy, aby zebrać dziennik aktywności.
2. W sekcji **źródła danych obszaru roboczego** menu obszaru roboczego wybierz pozycję **Dziennik aktywności platformy Azure**.
3. Kliknij subskrypcję, którą chcesz rozłączyć.
4. Kliknij przycisk **Rozłącz** , a następnie pozycję **tak** po wyświetleniu monitu, aby potwierdzić wybór.

### <a name="disable-log-profile"></a>Wyłącz profil dziennika

1. Aby otworzyć starsze ustawienia, należy użyć procedury opisanej w artykule [współpraca ze starszymi ustawieniami](#work-with-legacy-settings) .
2. Wyłącz wszystkie bieżące kolekcje w obszarze magazynów lub centrów zdarzeń.



## <a name="activity-log-monitoring-solution"></a>Rozwiązanie do monitorowania dzienników aktywności
Rozwiązanie do monitorowania Log Analytics platformy Azure obejmuje wiele zapytań dzienników i widoków służących do analizowania rekordów dziennika aktywności w obszarze roboczym Log Analytics. To rozwiązanie używa danych dzienników zebranych w obszarze roboczym Log Analytics i będzie nadal działały bez żadnych zmian, jeśli zbierasz dziennik aktywności przy użyciu ustawień diagnostycznych. Aby uzyskać szczegółowe informacje na temat tego rozwiązania, zobacz temat [monitorowanie dzienników aktywności — rozwiązanie](activity-log-collect.md#activity-logs-analytics-monitoring-solution) .

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się więcej o dzienniku aktywności](../../azure-resource-manager/management/view-activity-logs.md)
* [Dowiedz się więcej na temat ustawień diagnostycznych](diagnostic-settings.md)
