---
title: Archiwizuj dzienniki diagnostyczne platformy Azure
description: Dowiedz się, jak archiwizowanie dzienników diagnostycznych platformy Azure do długoterminowego przechowywania danych na koncie magazynu.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/18/2018
ms.author: johnkem
ms.subservice: logs
ms.openlocfilehash: bc1804e547bb1a29fc0dc680b948f1bb31af8307
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244916"
---
# <a name="archive-azure-diagnostic-logs"></a>Archiwizuj dzienniki diagnostyczne platformy Azure

W tym artykule pokazano, jak można użyć witryny Azure portal, poleceń cmdlet programu PowerShell, interfejsu wiersza polecenia lub interfejsu API REST do archiwizacji swoje [dzienniki diagnostyczne platformy Azure](diagnostic-logs-overview.md) na koncie magazynu. Ta opcja jest przydatna, jeśli chcesz zachować dzienników diagnostycznych z zasadami przechowywania opcjonalne inspekcji, analizę statyczną lub kopii zapasowej. Konto magazynu nie musi znajdować się w tej samej subskrypcji co zasób emitowane dzienniki, jak długo użytkownik, który konfiguruje ustawienie ma odpowiedni dostęp RBAC do obu subskrypcji.

> [!WARNING]
> Od 1 listopada 2018 r. format danych dzienników na koncie magazynu zmieni się na JSON Lines. [W tym artykule znajdziesz opis skutków tej zmiany oraz instrukcje aktualizacji narzędzi w celu zapewnienia obsługi nowego formatu.](./../../azure-monitor/platform/diagnostic-logs-append-blobs.md) 
>
> 

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem należy [Tworzenie konta magazynu](../../storage/common/storage-quickstart-create-account.md) do której można zarchiwizować dzienników diagnostycznych. Zdecydowanie zaleca się, że nie używasz istniejącego konta magazynu, który ma inne — monitorowanie danych przechowywanych w nim, dzięki czemu można lepiej kontrolować dostęp do danych monitorowania. Jednakże jeśli są również archiwizowanie swoje dziennika aktywności i metrykach diagnostycznych na konto magazynu, rozsądne może okazać się zachować wszystkie dane monitorowania w centralnej lokalizacji za pomocą tego konta magazynu dla dzienników diagnostycznych w także.

> [!NOTE]
>  Obecnie nie można zarchiwizować dane do magazynu konta, do którego za zabezpieczonej sieci wirtualnej.

## <a name="diagnostic-settings"></a>Ustawienia diagnostyczne

Aby archiwizowanie dzienników diagnostycznych przy użyciu dowolnej z poniższych metod, należy ustawić **ustawienie diagnostyczne** dla określonego zasobu. Ustawienie diagnostyczne zasobu definiuje kategorie dzienników i metryk dane wysyłane do miejsca docelowego (konto magazynu, obszaru nazw usługi Event Hubs lub obszar roboczy usługi Log Analytics). Definiuje również zasady przechowywania (liczba dni przechowywania) dla zdarzeń w każdej kategorii dzienników i metryk — dane przechowywane na koncie magazynu. Jeśli zasady przechowywania jest ustawiony na wartość zero, zdarzenia dla tej kategorii dziennika są przechowywane przez czas nieokreślony (to znaczy powiedzieć nieskończoność). Zasady przechowywania, w przeciwnym razie może być dowolną liczbę dni z zakresu od 1 do 2147483647. [Możesz dowiedzieć się więcej o konfiguracji ustawień diagnostyki tutaj](../../azure-monitor/platform/diagnostic-logs-overview.md#diagnostic-settings). Zasady przechowywania są zastosowane dziennie, dzięki czemu na koniec dnia (UTC), dzienniki w dzień, w którym jest teraz, po przekroczeniu przechowywania zasady zostaną usunięte. Na przykład jeśli masz zasady przechowywania w jeden dzień, na początku dnia już dziś dzienników z wczoraj zanim dnia zostaną usunięte. Proces usuwania rozpoczyna się od północy czasu UTC, ale należy pamiętać, że może upłynąć do 24 godzin dla dzienników są usuwane z konta magazynu. 

> [!NOTE]
> Wysyłanie metryk wielowymiarowych za pomocą ustawień diagnostycznych nie jest obecnie obsługiwane. Metryki wielowymiarowe są eksportowane jako spłaszczone metryki jednowymiarowe z wartościami zagregowanymi we wszystkich wymiarach.
>
> *Na przykład*: metrykę „Komunikaty przychodzące” w centrum zdarzeń można przeglądać i przedstawiać na wykresie na poziomie pojedynczej kolejki. Jednak w przypadku eksportowania za pomocą ustawień diagnostycznych metryka ta jest przedstawiana jako wszystkie komunikaty przychodzące we wszystkich kolejkach w centrum zdarzeń.
>
>

## <a name="archive-diagnostic-logs-using-the-portal"></a>Archiwizowanie dzienników diagnostycznych przy użyciu portalu

1. W portalu, przejdź do usługi Azure Monitor, a następnie kliknij pozycję **ustawień diagnostycznych**

    ![Monitorowanie sekcji usługi Azure Monitor](media/archive-diagnostic-logs/diagnostic-settings-blade.png)

2. Opcjonalnie filtrować listę według grupy zasobów lub typ zasobu, a następnie kliknij na zasób, dla którego chcesz skonfigurować ustawienie diagnostyczne.

3. Jeśli ustawienia nie istnieje w zasobie wybrany, zostanie wyświetlony monit, aby utworzyć ustawienie. Kliknij przycisk "Włącz diagnostykę."

   ![Dodaj ustawienie diagnostyczne — nie istniejące ustawienia](media/archive-diagnostic-logs/diagnostic-settings-none.png)

   W przypadku ustawienia istniejące na zasób zostanie wyświetlona lista ustawień już skonfigurowany dla tego zasobu. Kliknij pozycję "Dodaj ustawienie diagnostyczne".

   ![Dodaj ustawienie diagnostyczne — istniejące ustawienia](media/archive-diagnostic-logs/diagnostic-settings-multiple.png)

3. Nadaj nazwę ustawienia i zaznacz pole **Eksportuj na konto magazynu**, wybierz konto magazynu. Opcjonalnie można ustawić liczbę dni przechowywania tych dzienników przy użyciu **przechowywania (dni)** suwaki. Dzienniki na wpisanie wartości zero są przechowywane w nieskończoność.

   ![Dodaj ustawienie diagnostyczne — istniejące ustawienia](media/archive-diagnostic-logs/diagnostic-settings-configure.png)

4. Kliknij pozycję **Zapisz**.

Po kilku chwilach nowe ustawienie jest wyświetlane na liście ustawień dla tego zasobu, a dzienniki diagnostyczne są archiwizowane do tego konta magazynu, jak tylko nowe dane zdarzenia są generowane.

## <a name="archive-diagnostic-logs-via-azure-powershell"></a>Archiwizowanie dzienników diagnostycznych za pomocą programu Azure PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

```
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg -StorageAccountId /subscriptions/s1id1234-5679-0123-4567-890123456789/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Categories networksecuritygroupevent,networksecuritygrouprulecounter -Enabled $true -RetentionEnabled $true -RetentionInDays 90
```

| Właściwość | Wymagane | Opis |
| --- | --- | --- |
| ResourceId |Yes |Identyfikator zasobu zasobu, na którym jest ustawienie diagnostyczne. |
| StorageAccountId |Nie |Identyfikator zasobu konta magazynu, do którego powinny być zapisywane dzienniki diagnostyczne. |
| Categories |Nie |Rozdzielana przecinkami lista kategorie dziennika, aby włączyć. |
| Enabled (Włączony) |Yes |Wartość logiczna wskazująca, czy diagnostyki włączone lub wyłączone w przypadku tego zasobu. |
| RetentionEnabled |Nie |Wartość logiczna wskazująca, czy zasady przechowywania są włączone dla tego zasobu. |
| RetentionInDays |Nie |Liczba dni, dla których ma być przechowywana zdarzeń, od 1 do 2147483647. Dzienniki na wartość zero są przechowywane w nieskończoność. |

## <a name="archive-diagnostic-logs-via-the-azure-cli"></a>Archiwizowanie dzienników diagnostycznych za pomocą wiersza polecenia platformy Azure

```azurecli
az monitor diagnostic-settings create --name <diagnostic name> \
    --storage-account <name or ID of storage account> \
    --resource <target resource object ID> \
    --resource-group <storage account resource group> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true,
        "retentionPolicy": {
            "days": <# days to retain>,
            "enabled": true
        }
    }]'
```

Dodatkowe kategorie można dodawać do dzienników diagnostycznych, dodając słowników do tablicy JSON przekazywane jako `--logs` parametru.

`--resource-group` Argument tylko jest wymagany, jeżeli `--storage-account` nie jest identyfikator obiektu. Aby uzyskać pełną dokumentację dotyczącą archiwizowanie dzienników diagnostycznych do magazynu, zobacz [odniesienie do polecenia interfejsu wiersza polecenia](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create).

## <a name="archive-diagnostic-logs-via-the-rest-api"></a>Archiwizowanie dzienników diagnostycznych za pomocą interfejsu API REST

[Zobacz ten dokument](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings) informacji, w jaki sposób można skonfigurować ustawienie diagnostyczne przy użyciu interfejsu API REST usługi Azure Monitor.

## <a name="schema-of-diagnostic-logs-in-the-storage-account"></a>Schemat dzienniki diagnostyczne na koncie magazynu

Po skonfigurowaniu archiwizacji, zaraz po wystąpieniu zdarzenia w jednym z kategorie dziennika, które mają włączone kontenera magazynu jest tworzony na koncie magazynu. Obiekty BLOB w kontenerze postępuj zgodnie z tej samej konwencji nazewnictwa w dziennikach aktywności i dzienników diagnostycznych, jak pokazano poniżej:

```
insights-logs-{log category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/RESOURCEGROUPS/{resource group name}/PROVIDERS/{resource provider name}/{resource type}/{resource name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Na przykład może być nazwa obiektu blob:

```
insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUP/TESTNSG/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

Każdy obiekt blob PT1H.json zawiera obiekt blob JSON ze zdarzeniami, które wystąpiły w ciągu godziny określonej w adresie URL obiektu blob (na przykład h = 12). Zdarzenia występujące w danej chwili są na bieżąco dołączane do pliku PT1H.json. Wartość minut (m = 00) jest zawsze 00, ponieważ dziennik diagnostyczny zdarzenia są dzielone na poszczególne obiekty BLOB na godzinę.

W pliku PT1H.json każde zdarzenie jest przechowywane w tablicy "rekordy", zgodnie z następującym formacie:

``` JSON
{
    "records": [
        {
            "time": "2016-07-01T00:00:37.2040000Z",
            "systemId": "46cdbb41-cb9c-4f3d-a5b4-1d458d827ff1",
            "category": "NetworkSecurityGroupRuleCounter",
            "resourceId": "/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/TESTNSG",
            "operationName": "NetworkSecurityGroupCounters",
            "properties": {
                "vnetResourceGuid": "{12345678-9012-3456-7890-123456789012}",
                "subnetPrefix": "10.3.0.0/24",
                "macAddress": "000123456789",
                "ruleName": "/subscriptions/ s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg/securityRules/default-allow-rdp",
                "direction": "In",
                "type": "allow",
                "matchedConnections": 1988
            }
        }
    ]
}
```

| Nazwa elementu | Opis |
| --- | --- |
| time |Sygnatura czasowa podczas generowania zdarzenia według usługi platformy Azure, przetwarzanie żądania odpowiadające zdarzenie. |
| resourceId |Identyfikator zasobu zasób objęty wpływem. |
| operationName |Nazwa operacji. |
| category |Kategoria dziennika zdarzeń. |
| properties |Zestaw `<Key, Value>` pary (tj. Słownik), opisujący szczegóły zdarzenia. |

> [!NOTE]
> Właściwości i użycia tych właściwości może się różnić zależnie od zasobu.

## <a name="next-steps"></a>Kolejne kroki

* [Pobierz obiekty BLOB na potrzeby analizy](../../storage/blobs/storage-quickstart-blobs-dotnet.md)
* [Stream dzienników diagnostycznych do przestrzeni nazw usługi Event Hubs](../../azure-monitor/platform/diagnostic-logs-stream-event-hubs.md)
* [Archiwizuj dzienniki usługi Azure Active Directory z usługą Azure Monitor](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md)
* [Więcej informacji na temat dzienników diagnostycznych](../../azure-monitor/platform/diagnostic-logs-overview.md)

