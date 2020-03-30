---
title: Wyświetlanie zdarzeń dziennika aktywności platformy Azure w usłudze Azure Monitor
description: Wyświetl dziennik aktywności platformy Azure w usłudze Azure Monitor i pobierz za pomocą interfejsu API programu PowerShell, cli i REST.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/07/2019
ms.author: johnkem
ms.subservice: logs
ms.openlocfilehash: 9df7593a9fd191d3a734fba5e81fb1aecba08345
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275050"
---
# <a name="view-and-retrieve-azure-activity-log-events"></a>Wyświetlanie i pobieranie zdarzeń dziennika aktywności platformy Azure

[Dziennik aktywności platformy Azure](platform-logs-overview.md) zapewnia wgląd w zdarzenia na poziomie subskrypcji, które wystąpiły na platformie Azure. Ten artykuł zawiera szczegółowe informacje na temat różnych metod wyświetlania i pobierania zdarzeń dziennika aktywności.

## <a name="azure-portal"></a>Portal Azure
Wyświetl dziennik aktywności dla wszystkich zasobów z menu **Monitor** w witrynie Azure portal. Wyświetl dziennik aktywności dla określonego zasobu z opcji **Dziennik aktywności** w menu tego zasobu.

![Wyświetl dziennik aktywności](./media/activity-logs-overview/view-activity-log.png)

Zdarzenia dziennika aktywności można filtrować według następujących pól:

* **Timespan**: Godzina rozpoczęcia i zakończenia wydarzeń.
* **Kategoria:** Kategoria zdarzenia opisana w [kategoriach w dzienniku aktywności](activity-log-view.md#categories-in-the-activity-log).
* **Subskrypcja:** Co najmniej jedna nazwa subskrypcji platformy Azure.
* **Grupa zasobów**: Co najmniej jedna grupa zasobów w ramach wybranych subskrypcji.
* **Zasób (nazwa)**: - Nazwa określonego zasobu.
* **Typ zasobu**: Typ zasobu, na przykład _Microsoft.Compute/virtualmachines_.
* **Nazwa operacji** — nazwa operacji usługi Azure Resource Manager, na przykład _Microsoft.SQL/servers/Write_.
* **Ważność:** poziom ważności zdarzenia. Dostępne wartości _to Informacyjny_, _Ostrzeżenie,_ _Błąd_, _Krytyczny_.
* **Zdarzenie zainicjowane przez**: Użytkownik, który wykonał operację.
* **Otwórz wyszukiwanie:** Otwórz pole wyszukiwania tekstowego, które wyszukuje ten ciąg we wszystkich polach we wszystkich zdarzeniach.

## <a name="categories-in-the-activity-log"></a>Kategorie w dzienniku aktywności
Każde zdarzenie w dzienniku aktywności ma określoną kategorię, które są opisane w poniższej tabeli. Aby uzyskać szczegółowe informacje na temat schematu tych kategorii, zobacz [schemat zdarzeń dziennika aktywności platformy Azure](activity-log-schema.md). 

| Kategoria | Opis |
|:---|:---|
| Administracyjne | Zawiera rekord wszystkich operacji tworzenia, aktualizacji, usuwania i akcji wykonywanych za pośrednictwem Menedżera zasobów. Przykłady zdarzeń administracyjnych obejmują _tworzenie maszyny wirtualnej_ i _usuwanie sieciowej grupy zabezpieczeń_.<br><br>Każda akcja podjęta przez użytkownika lub aplikację przy użyciu Menedżera zasobów jest modelowana jako operacja na określonym typie zasobu. Jeśli typem operacji jest _Write_, _Delete_lub _Action_, rekordy zarówno rozpoczęcia, jak i powodzenia lub niepowodzenia tej operacji są rejestrowane w kategorii Administracyjna. Zdarzenia administracyjne obejmują również wszelkie zmiany w kontroli dostępu opartej na rolach w subskrypcji. |
| Service Health | Zawiera rekord wszystkich zdarzeń dotyczących kondycji usługi, które wystąpiły na platformie Azure. Przykład zdarzenia kondycji usługi _SQL Azure we wschodnich stanach USA przeżywa przestoje._ <br><br>Zdarzenia kondycji usługi są dostępne w sześciu odmianach: _Wymagane działanie,_ _Wspomagane odzyskiwanie,_ _Incydent,_ _Konserwacja,_ _Informacje_lub _Bezpieczeństwo._ Te zdarzenia są tworzone tylko wtedy, gdy masz zasób w subskrypcji, który będzie miał wpływ na zdarzenie.
| Kondycja zasobów | Zawiera rekord wszystkich zdarzeń kondycji zasobów, które wystąpiły w zasobach platformy Azure. Przykładem zdarzenia Kondycja zasobu jest _stan kondycji maszyny wirtualnej zmieniony na niedostępny_.<br><br>Zdarzenia kondycji zasobu mogą reprezentować jeden z czterech stanów kondycji: _Dostępny_, _Niedostępny,_ _Zdegradowany_i _Nieznany_. Ponadto zdarzenia kondycji zasobów można sklasyfikować jako _inicjowane przez platformę_ lub _inicjowane przez użytkownika._ |
| Alerty | Zawiera rekord aktywacji alertów platformy Azure. Przykładem zdarzenia Alert jest _CPU % na myVM został ponad 80 w ciągu ostatnich 5 minut_.|
| Automatyczne skalowanie | Zawiera rekord wszystkich zdarzeń związanych z działaniem aparatu skalowania automatycznego na podstawie ustawień skalowania automatycznego zdefiniowanych w ramach subskrypcji. Przykładem zdarzenia Skalowanie automatyczne jest _akcja skalowania automatycznego w górę nie powiodła się._ |
| Zalecenie | Zawiera zdarzenia rekomendacji z usługi Azure Advisor. |
| Zabezpieczenia | Zawiera rekord wszystkich alertów generowanych przez usługę Azure Security Center. Przykładem zdarzenia Security jest _podejrzany plik podwójnego rozszerzenia wykonany_. |
| Zasady | Zawiera rekordy wszystkich operacji akcji efekt wykonywanych przez usługę Azure Policy. Przykłady zdarzeń zasad obejmują _inspekcję_ i _odmowę_. Każda akcja podjęta przez zasady jest modelowany jako operacja na zasób. |

## <a name="view-change-history"></a>Wyświetlanie historii zmian

Podczas przeglądania dziennika aktywności, może pomóc zobaczyć, jakie zmiany miały miejsce w tym czasie zdarzenia. Można wyświetlić te informacje za pomocą **historii zmian**. Wybierz zdarzenie z dziennika aktywności, które chcesz przyjrzeć się głębiej. Wybierz kartę **Historia zmian (podgląd),** aby wyświetlić wszystkie związane z tym zdarzeniem zmiany.

![Zmienianie listy historii wydarzenia](media/activity-logs-overview/change-history-event.png)

Jeśli w zdarzeniu zostaną wprowadzone jakieś skojarzone zmiany, zobaczysz listę zmian, które możesz wybrać. Spowoduje to otwarcie strony **Historia zmian (wersja zapoznawcza).** Na tej stronie widoczne są zmiany w zasobie. Jak widać na poniższym przykładzie, możemy zobaczyć nie tylko, że maszyna wirtualna zmieniła rozmiary, ale co poprzedni rozmiar maszyny Wirtualnej był przed zmianą i co zostało zmienione.

![Strona historii zmian z różnicami](media/activity-logs-overview/change-history-event-details.png)

Aby dowiedzieć się więcej o historii zmian, zobacz [Wyładuj zmiany zasobów](../../governance/resource-graph/how-to/get-resource-changes.md).






## <a name="powershell"></a>PowerShell
Użyj polecenia cmdlet [Get-AzLog,](https://docs.microsoft.com/powershell/module/az.monitor/get-azlog) aby pobrać dziennik aktywności z programu PowerShell. Oto kilka typowych przykładów.

> [!NOTE]
> `Get-AzLog`zapewnia tylko 15 dni historii. Użyj **parametru -MaxEvents,** aby zbadać ostatnie zdarzenia N poza 15 dni. Aby uzyskać dostęp do zdarzeń starszych niż 15 dni, użyj interfejsu API REST lub SDK. Jeśli nie zostanie uwzględnieni **StartTime**, wartość domyślna to **EndTime** minus jedna godzina. Jeśli nie zostanie uwzględnieni **EndTime**, wartość domyślna to bieżący czas. Wszystkie czasy są w UTC.


Pobierz wpisy dziennika utworzone po określonej godzinie daty:

```powershell
Get-AzLog -StartTime 2016-03-01T10:30
```

Pobierz wpisy dziennika między zakresem dat:

```powershell
Get-AzLog -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Pobierz wpisy dziennika z określonej grupy zasobów:

```powershell
Get-AzLog -ResourceGroup 'myrg1'
```

Pobierz wpisy dziennika od określonego dostawcy zasobów między zakresem czasu daty:

```powershell
Get-AzLog -ResourceProvider 'Microsoft.Web' -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Pobierz wpisy dziennika z określonym rozmówcą:

```powershell
Get-AzLog -Caller 'myname@company.com'
```

Pobierz ostatnie 1000 wydarzeń:

```powershell
Get-AzLog -MaxEvents 1000
```


## <a name="cli"></a>Interfejs wiersza polecenia
Użyj [dziennika aktywności monitora az,](cli-samples.md#view-activity-log-for-a-subscription) aby pobrać dziennik aktywności z interfejsu wiersza polecenia. Oto kilka typowych przykładów.


Wyświetl wszystkie dostępne opcje.

```azurecli
az monitor activity-log list -h
```

Pobierz wpisy dziennika z określonej grupy zasobów:

```azurecli
az monitor activity-log list --resource-group <group name>
```

Pobierz wpisy dziennika z określonym rozmówcą:

```azurecli
az monitor activity-log list --caller myname@company.com
```

Pobierz dzienniki przez wywołującego na typ zasobu, w zakresie dat:

```azurecli
az monitor activity-log list --resource-provider Microsoft.Web \
    --caller myname@company.com \
    --start-time 2016-03-08T00:00:00Z \
    --end-time 2016-03-16T00:00:00Z
```

## <a name="rest-api"></a>Interfejs API REST
Użyj [interfejsu API REST usługi Azure Monitor,](https://docs.microsoft.com/rest/api/monitor/) aby pobrać dziennik aktywności z klienta REST. Oto kilka typowych przykładów.

Pobierz dzienniki aktywności z filtrem:

``` HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '2018-01-21T20:00:00Z' and eventTimestamp le '2018-01-23T20:00:00Z' and resourceGroupName eq 'MSSupportGroup'
```

Pobierz dzienniki aktywności z filtrem i wybierz:

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '2015-01-21T20:00:00Z' and eventTimestamp le '2015-01-23T20:00:00Z' and resourceGroupName eq 'MSSupportGroup'&$select=eventName,id,resourceGroupName,resourceProviderName,operationName,status,eventTimestamp,correlationId,submissionTimestamp,level
```

Pobierz dzienniki aktywności z wybierz:

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$select=eventName,id,resourceGroupName,resourceProviderName,operationName,status,eventTimestamp,correlationId,submissionTimestamp,level
```

Pobierz dzienniki aktywności bez filtru lub wybierz:

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01
```


## <a name="activity-logs-analytics-monitoring-solution"></a>Rozwiązanie do monitorowania usługi Activity Logs Analytics
Rozwiązanie do monitorowania usługi Azure Log Analytics zawiera wiele zapytań dziennika i widoków do analizowania rekordów dziennika aktywności w obszarze roboczym usługi Log Analytics.

### <a name="prerequisites"></a>Wymagania wstępne
Należy utworzyć ustawienie diagnostyczne, aby wysłać dziennik aktywności dla subskrypcji do obszaru roboczego usługi Log Analytics. Zobacz [Zbieranie dzienników platformy Azure w obszarze roboczym usługi Log Analytics w usłudze Azure Monitor.](resource-logs-collect-workspace.md)

### <a name="install-the-solution"></a>Zainstaluj rozwiązanie
Użyj procedury w [zainstaluj rozwiązanie do monitorowania,](../insights/solutions.md#install-a-monitoring-solution) aby zainstalować rozwiązanie **analizy dzienników aktywności.** Nie jest wymagana żadna dodatkowa konfiguracja.

### <a name="use-the-solution"></a>Użyj rozwiązania
Kliknij **pozycję Dzienniki** u góry strony **Dziennik aktywności,** aby otworzyć [rozwiązanie do monitorowania usługi Activity Log Analytics](activity-log-collect.md) dla subskrypcji. Lub dostęp do wszystkich rozwiązań monitorowania w menu **Monitor** subskrypcji w witrynie Azure portal. Wybierz **pozycję Więcej** w sekcji **Statystyki,** aby otworzyć stronę **Przegląd** z kafelkami rozwiązania. Kafelek **Dzienniki aktywności platformy Azure** wyświetla liczbę rekordów **usługi AzureActivity** w obszarze roboczym.

![Kafelek dzienników aktywności platformy Azure](media/collect-activity-logs/azure-activity-logs-tile.png)


Kliknij **kafelek Dzienniki aktywności platformy Azure,** aby otworzyć widok **dzienniki aktywności platformy Azure.** Widok zawiera części wizualizacji w poniższej tabeli. Każda część zawiera listę do 10 elementów pasujących do kryteriów tej części dla określonego zakresu czasu. Można uruchomić kwerendę dziennika, która zwraca wszystkie pasujące rekordy, klikając **zobacz wszystko** u dołu części.

![Pulpit nawigacyjny dzienników aktywności platformy Azure](media/collect-activity-logs/activity-log-dash.png)

| Część wizualizacji | Opis |
| --- | --- |
| Wpisy dziennika aktywności platformy Azure | Pokazuje wykres słupkowy najwyższej sumy rekordów dziennika aktywności platformy Azure dla wybranego zakresu dat i pokazuje listę 10 najlepszych wywołań aktywności. Kliknij wykres słupkowy, aby `AzureActivity`uruchomić wyszukiwanie dziennika . Kliknij element wywołującego, aby uruchomić wyszukiwanie dziennika zwracające wszystkie wpisy dziennika aktywności dla tego elementu. |
| Dzienniki aktywności według stanu | Pokazuje wykres pierścieniowy dla stanu dziennika aktywności platformy Azure dla wybranego zakresu dat i listę dziesięciu pierwszych rekordów stanu. Kliknij wykres, aby uruchomić `AzureActivity | summarize AggregatedValue = count() by ActivityStatus`kwerendę dziennika dla . Kliknij element stanu, aby uruchomić wyszukiwanie dziennika zwracające wszystkie wpisy dziennika aktywności dla tego rekordu stanu. |
| Dzienniki aktywności według zasobów | Pokazuje całkowitą liczbę zasobów za pomocą dzienników aktywności i wyświetla dziesięć pierwszych zasobów z liczbą rekordów dla każdego zasobu. Kliknij całkowity obszar, aby uruchomić `AzureActivity | summarize AggregatedValue = count() by Resource`wyszukiwanie dziennika , który pokazuje wszystkie zasoby platformy Azure dostępne dla rozwiązania. Kliknij zasób, aby uruchomić kwerendę dziennika zwracającą wszystkie rekordy działań dla tego zasobu. |
| Dzienniki aktywności według dostawcy zasobów | Pokazuje całkowitą liczbę dostawców zasobów, którzy produkują dzienniki aktywności i wyświetla listę dziesięciu najlepszych. Kliknij całkowity obszar, aby uruchomić `AzureActivity | summarize AggregatedValue = count() by ResourceProvider`kwerendę dziennika dla , który pokazuje wszystkich dostawców zasobów platformy Azure. Kliknij dostawcę zasobów, aby uruchomić kwerendę dziennika zwracającą wszystkie rekordy aktywności dla dostawcy. |




## <a name="next-steps"></a>Następne kroki

* [Przeczytaj przegląd dzienników platformy](platform-logs-overview.md)
* [Tworzenie ustawienia diagnostycznego w celu wysyłania dzienników aktywności do innych miejsc docelowych](diagnostic-settings.md)
