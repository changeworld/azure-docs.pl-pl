---
title: Zbieranie dziennika aktywności platformy Azure z ustawieniami diagnostycznymi — Azure Monitor | Dokumenty firmy Microsoft
description: Użyj ustawień diagnostycznych, aby przesyłać dalej dzienniki aktywności platformy Azure do dzienników usługi Azure Monitor, usługi Azure storage lub usługi Azure Event Hubs.
author: bwren
ms.subservice: logs
ms.topic: conceptual
ms.author: bwren
ms.date: 02/04/2020
ms.openlocfilehash: 6d4c724c7cfb4c1779f0fc6592a7e61e060755b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79096891"
---
# <a name="update-to-azure-activity-log-collection-and-export"></a>Aktualizowanie i eksportowanie dzienników aktywności platformy Azure
[Dziennik aktywności platformy Azure](platform-logs-overview.md) to dziennik [platformy,](platform-logs-overview.md) który zapewnia wgląd w zdarzenia na poziomie subskrypcji, które wystąpiły na platformie Azure. Metoda wysyłania wpisów dziennika aktywności do [centrum zdarzeń lub konta magazynu](activity-log-export.md) lub do obszaru [roboczego usługi Log Analytics](activity-log-collect.md) została zmieniona w celu użycia ustawień [diagnostycznych](diagnostic-settings.md). W tym artykule opisano różnicę między metodami i jak wyczyścić starsze ustawienia w ramach przygotowań do zmiany ustawień diagnostycznych.


## <a name="differences-between-methods"></a>Różnice między metodami

### <a name="advantages"></a>Zalety
Za pomocą ustawień diagnostycznych ma następujące zalety w stosunku do bieżących metod:

- Spójna metoda zbierania wszystkich dzienników platformy.
- Zbieranie dziennika aktywności w wielu subskrypcjach i dzierżawach.
- Zbieranie filtrów do zbierania tylko dzienników dla określonych kategorii.
- Zbierz wszystkie kategorie dziennika aktywności. Niektóre kategorie nie są zbierane przy użyciu starszej metody.
- Szybsze opóźnienie pozyskiwania dziennika. Poprzednia metoda ma około 15 minut opóźnienia, podczas gdy ustawienia diagnostyczne dodaje tylko około 1 minuty.

### <a name="considerations"></a>Zagadnienia do rozważenia
Należy wziąć pod uwagę następujące szczegóły kolekcji dziennika aktywności przy użyciu ustawień diagnostycznych przed włączeniem tej funkcji.

- Ustawienie przechowywania do zbierania dziennika aktywności do magazynu platformy Azure zostało usunięte, co oznacza, że dane będą przechowywane przez czas nieokreślony, dopóki ich nie usuniesz.
- Obecnie można utworzyć tylko ustawienie diagnostyczne poziomu subskrypcji przy użyciu witryny Azure portal. Aby użyć innych metod, takich jak PowerShell lub CLI, można utworzyć szablon Menedżera zasobów.


### <a name="differences-in-data"></a>Różnice w danych
Ustawienia diagnostyczne zbierają te same dane, co poprzednie metody używane do zbierania dziennika aktywności z następującymi bieżącymi różnicami:

Następujące kolumny zostały usunięte. Zastąpienie tych kolumn są w innym formacie, więc może być konieczne zmodyfikowanie kwerend dziennika, które ich używają. W schemacie mogą być nadal widoczne usunięte kolumny, ale nie zostaną one wypełnione danymi.

| Usunięto kolumnę | Kolumna zastępcza |
|:---|:---|
| ActivityStatus (Stan aktywności)    | ActivityStatusValue    |
| AktywnośćSubstatus | DziałanieSubstatusValue |
| OperationName     | OperacjaNameValue     |
| ResourceProvider  | ResourceProviderValue  |

Dodano następującą kolumnę:

- Authorization_d
- Claims_d
- Properties_d

> [!IMPORTANT]
> W niektórych przypadkach wartości w tych kolumnach mogą być we wszystkich wielkich literach. Jeśli masz kwerendę, która zawiera te kolumny, należy użyć [operatora =~](https://docs.microsoft.com/azure/kusto/query/datatypes-string-operators) do porównania bez uwzględniania wielkości liter.

## <a name="work-with-legacy-settings"></a>Praca ze starszymi ustawieniami
Starsze ustawienia do zbierania dziennika aktywności będą nadal działać, jeśli nie zdecydujesz się zastąpić ustawieniem diagnostycznym. Użyj następującej metody do zarządzania profilem dziennika dla subskrypcji.

1. Z menu **Azure Monitor** w witrynie Azure portal wybierz **dziennik aktywności**.
3. Kliknij pozycję **Ustawienia diagnostyczne**.

   ![Ustawienia diagnostyczne](media/diagnostic-settings-subscription/diagnostic-settings.png)

4. Kliknij fioletowy baner, aby zapoznać się ze starszą wersji.

    ![Starsze doświadczenie](media/diagnostic-settings-subscription/legacy-experience.png)


Zobacz następujące artykuły, aby uzyskać szczegółowe informacje na temat korzystania z metod kolekcji starszych.

- [Zbieranie i analizowanie dzienników aktywności platformy Azure w obszarze roboczym usługi Log Analytics w usłudze Azure Monitor](activity-log-collect.md)
- [Zbieranie dzienników aktywności platformy Azure w usłudze Azure Monitor w dzierżawach usługi Azure Active Directory](activity-log-collect-tenants.md)
- [Eksportowanie dziennika aktywności platformy Azure do magazynu lub usługi Azure Event Hubs](activity-log-export.md)

## <a name="disable-existing-settings"></a>Wyłączanie istniejących ustawień
Należy wyłączyć istniejącą kolekcję działania przed włączeniem go przy użyciu ustawień diagnostycznych. Włączenie obu tych danych może spowodować zduplikowanie danych.

### <a name="disable-collection-into-log-analytics-workspace"></a>Wyłączanie kolekcji w obszarze roboczym usługi Log Analytics

1. Otwórz menu **obszarów roboczych usługi Log Analytics** w portalu Azure i wybierz obszar roboczy do zbierania dziennika aktywności.
2. W sekcji **Źródła danych obszaru roboczego** w menu obszaru roboczego wybierz pozycję **Dziennik aktywności platformy Azure**.
3. Kliknij subskrypcję, którą chcesz rozłączyć.
4. Kliknij **pozycję Rozłącz,** a następnie **przycisk Tak,** gdy pojawi się monit o potwierdzenie wyboru.

### <a name="disable-log-profile"></a>Wyłącz profil dziennika

1. Użyj procedury opisanej w [pracy ze starszymi ustawieniami,](#work-with-legacy-settings) aby otworzyć starsze ustawienia.
2. Wyłącz bieżącą kolekcję do przechowywania lub centrum zdarzeń.



## <a name="activity-log-monitoring-solution"></a>Rozwiązanie do monitorowania dziennika aktywności
Rozwiązanie do monitorowania usługi Azure Log Analytics zawiera wiele zapytań dziennika i widoków do analizowania rekordów dziennika aktywności w obszarze roboczym usługi Log Analytics. To rozwiązanie używa danych dziennika zebranych w obszarze roboczym usługi Log Analytics i będzie nadal działać bez żadnych zmian, jeśli zbierzesz dziennik aktywności przy użyciu ustawień diagnostycznych. Zobacz [rozwiązanie do monitorowania usługi Activity Logs Analytics,](activity-log-collect.md#activity-logs-analytics-monitoring-solution) aby uzyskać szczegółowe informacje na temat tego rozwiązania.

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się więcej o dzienniku aktywności](../../azure-resource-manager/management/view-activity-logs.md)
* [Dowiedz się więcej o ustawieniach diagnostycznych](diagnostic-settings.md)
