---
title: Wyświetl zdarzenia dziennika aktywności platformy Azure w usłudze Azure Monitor
description: Wyświetl dziennik aktywności platformy Azure w usłudze Azure Monitor, a następnie pobrać za pomocą programu PowerShell, interfejsu wiersza polecenia i interfejsu API REST.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: johnkem
ms.subservice: logs
ms.openlocfilehash: 32578f77f2b3f30d80953bdd1099d22c945c640b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66248117"
---
# <a name="view-and-retrieve-azure-activity-log-events"></a>Wyświetlanie i pobieranie zdarzenia dziennika aktywności platformy Azure

[Dziennika aktywności platformy Azure](activity-logs-overview.md) zapewnia wgląd w zdarzenia na poziomie subskrypcji, które miały miejsce w systemie Azure. Ten artykuł zawiera szczegółowe informacje dotyczące różnych metod do przeglądania i odzyskiwania zdarzeń dziennika aktywności.

## <a name="azure-portal"></a>Azure Portal
Wyświetl dziennik aktywności, aby wszystkie zasoby z **Monitor** menu w witrynie Azure portal. Wyświetlanie dziennika aktywności dla danego zasobu z **dziennika aktywności** opcji w menu tego zasobu.

![Wyświetl dziennik aktywności](./media/activity-logs-overview/view-activity-log.png)

Można filtrować zdarzenia dziennika aktywności przez następujące pola:

* **Przedział czasu**: Godzina rozpoczęcia i zakończenia zdarzenia.
* **Kategoria**: Kategoria zdarzenia, zgodnie z opisem w [kategorie w dzienniku aktywności](activity-logs-overview.md#categories-in-the-activity-log).
* **Subskrypcja**: Jedną lub więcej nazw subskrypcji platformy Azure.
* **Grupa zasobów**: Co najmniej jedną grupę zasobów w ramach wybranej subskrypcji.
* **Zasób (nazwa)** :-nazwę określonego zasobu.
* **Typ zasobu**: Typ zasobu, na przykład _Microsoft.Compute/virtualmachines_.
* **Nazwa operacji** — nazwy operacji usługi Azure Resource Manager, na przykład _Microsoft.SQL/servers/Write_.
* **Ważność**: Poziom ważności zdarzenia. Dostępne wartości to _komunikat o charakterze informacyjnym_, _ostrzeżenie_, _błąd_, _krytyczny_.
* **Zdarzenie zainicjowane przez**: Użytkownik, który wykonał operację.
* **Otwórz wyszukiwanie**: Otwórz pole wyszukiwania tekstu, wyszukująca ciąg we wszystkich polach we wszystkich przypadkach.

### <a name="view-change-history"></a>Wyświetl historię zmian

Podczas przeglądania dziennika aktywności, może pomóc aby zobaczyć, jakie zmiany, które zaszły w tym czasie zdarzenia. Możesz wyświetlić te informacje z **historię zmian**. Wybierz zdarzenia z dziennika aktywności, który chcesz przejrzeć większe zagłębienie w. Wybierz **(wersja zapoznawcza) historię zmian** kartę, aby wyświetlić dowolny skojarzone zmiany z tego zdarzenia.

![Lista historii zmian, zdarzenia](media/activity-logs-overview/change-history-event.png)

W przypadku zmiany skojarzone ze zdarzeniem, zobaczysz listę zmian, które można wybrać. Spowoduje to otwarcie **(wersja zapoznawcza) historię zmian** strony. Na tej stronie możesz zobaczyć zmiany do tego zasobu. Jak widać w poniższym przykładzie, możemy zobaczyć nie tylko, że maszyna wirtualna zmienione rozmiary, ale poprzedni rozmiar maszyny Wirtualnej została przed zmianą i co zostało zmienione na.

![Strona historii zmian, wyświetlanie różnic](media/activity-logs-overview/change-history-event-details.png)

Aby dowiedzieć się więcej na temat historii zmian, zobacz [pobieranie zmian zasobów](../../governance/resource-graph/how-to/get-resource-changes.md).


## <a name="log-analytics-workspace"></a>Obszar roboczy usługi log Analytics
Kliknij przycisk **dzienniki** w górnej części **dziennika aktywności** strony, aby otworzyć [Activity Log Analytics, rozwiązanie do monitorowania](activity-log-collect.md) dla subskrypcji. Dzięki temu można wyświetlić analizy dotyczące dziennika aktywności i uruchamiania [rejestrowania zapytań](../log-query/log-query-overview.md) z **AzureActivity** tabeli. Dziennik aktywności nie jest połączony z obszarem roboczym usługi Log Analytics, zostanie wyświetlony monit wykonaniem tej konfiguracji.



## <a name="powershell"></a>PowerShell
Użyj [Get AzLog](https://docs.microsoft.com/powershell/module/az.monitor/get-azlog) polecenie cmdlet do pobierania dzienników aktywności z poziomu programu PowerShell. Poniżej przedstawiono kilka typowych przykładów.

> [!NOTE]
> `Get-AzLog` zapewnia tylko 15 dni historii. Użyj **— wartości elementu MaxEvents** parametr zapytania ostatnie N zdarzeń dłużej niż 15 dni. Aby uzyskać dostęp do zdarzenia starsze niż 15 dni, należy użyć interfejsu API REST lub zestawu SDK. Jeśli nie dołączysz **StartTime**, wówczas wartość domyślna to **EndTime** pomniejszona o jedną godzinę. Jeśli nie dołączysz **EndTime**, wartością domyślną jest bieżący czas. Wszystkie godziny są w formacie UTC.


Pobierz wpisy dziennika utworzone po określonej dacie i godzinie:

```powershell
Get-AzLog -StartTime 2016-03-01T10:30
```

Pobierz wpisy dziennika między datą zakres czasu:

```powershell
Get-AzLog -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Pobierz wpisy dziennika w określonej grupie zasobów:

```powershell
Get-AzLog -ResourceGroup 'myrg1'
```

Pobierz wpisy dziennika od dostawcy określonego zasobu między datą zakres czasu:

```powershell
Get-AzLog -ResourceProvider 'Microsoft.Web' -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Pobierz wpisy dziennika przy użyciu określonego obiektu wywołującego:

```powershell
Get-AzLog -Caller 'myname@company.com'
```

Pobierz ostatnie 1000 zdarzeń:

```powershell
Get-AzLog -MaxEvents 1000
```


## <a name="cli"></a>Interfejs wiersza polecenia
Użyj [az monitor z dziennika aktywności](cli-samples.md#view-activity-log-for-a-subscription) można pobrać dziennik aktywności z interfejsu wiersza polecenia. Poniżej przedstawiono kilka typowych przykładów.


Wyświetlanie wszystkich dostępnych opcji.

```azurecli
az monitor activity-log list -h
```

Pobierz wpisy dziennika w określonej grupie zasobów:

```azurecli
az monitor activity-log list --resource-group <group name>
```

Pobierz wpisy dziennika przy użyciu określonego obiektu wywołującego:

```azurecli
az monitor activity-log list --caller myname@company.com
```

Pobierz dzienniki przez obiekt wywołujący na typ zasobu, w ramach zakresu dat:

```azurecli
az monitor activity-log list --resource-provider Microsoft.Web \
    --caller myname@company.com \
    --start-time 2016-03-08T00:00:00Z \
    --end-time 2016-03-16T00:00:00Z
```

## <a name="rest-api"></a>Interfejs API REST
Użyj [interfejsu API REST usługi Azure Monitor](https://docs.microsoft.com/rest/api/monitor/) pobrać dziennik aktywności z klienta REST. Poniżej przedstawiono kilka typowych przykładów.

Pobierz dzienniki aktywności z filtrem:

``` HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '2018-01-21T20:00:00Z' and eventTimestamp le '2018-01-23T20:00:00Z' and resourceGroupName eq 'MSSupportGroup'
```

Pobierz dzienniki aktywności przy użyciu filtru i wybierz pozycję:

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '2015-01-21T20:00:00Z' and eventTimestamp le '2015-01-23T20:00:00Z' and resourceGroupName eq 'MSSupportGroup'&$select=eventName,id,resourceGroupName,resourceProviderName,operationName,status,eventTimestamp,correlationId,submissionTimestamp,level
```

Pobierz dzienniki aktywności z wybranymi:

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$select=eventName,id,resourceGroupName,resourceProviderName,operationName,status,eventTimestamp,correlationId,submissionTimestamp,level
```

Pobierz dzienniki aktywności bez filtru i wybierz opcję:

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01
```


## <a name="next-steps"></a>Następne kroki

* [Przeczytaj Omówienie dziennika aktywności](activity-logs-overview.md)
* [Archiwizowanie dziennika aktywności do magazynu lub Prześlij go strumieniowo do usługi Event Hubs](activity-log-export.md)
* [Stream dziennika aktywności platformy Azure do usługi Event Hubs](activity-logs-stream-event-hubs.md)
* [Archiwizowanie dziennika aktywności platformy Azure do magazynu](archive-activity-log.md)

