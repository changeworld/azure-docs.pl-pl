---
title: Archiwizuj dzienniki zasobów platformy Azure na koncie magazynu | Microsoft Docs
description: Dowiedz się, jak archiwizować dzienniki zasobów platformy Azure w celu długoterminowego przechowywania na koncie magazynu.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 8a1802f0f24ba5ccad3ec1c45f3baa29dfe6909f
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262559"
---
# <a name="archive-azure-resource-logs-to-storage-account"></a>Archiwizuj dzienniki zasobów platformy Azure na koncie magazynu
[Dzienniki zasobów](resource-logs-overview.md) na platformie Azure zapewniają rozbudowane, częste dane dotyczące wewnętrznej operacji zasobu platformy Azure. W tym artykule opisano zbieranie dzienników zasobów na koncie usługi Azure Storage w celu zachowania danych do archiwizacji.

## <a name="prerequisites"></a>Wymagania wstępne
Jeśli jeszcze tego nie zrobiono, musisz [utworzyć konto usługi Azure Storage](../../storage/common/storage-quickstart-create-account.md) . Konto magazynu nie musi znajdować się w tej samej subskrypcji co zasób wysyła dzienniki, dopóki użytkownik, który konfiguruje ustawienie, ma dostęp do obu subskrypcji.

Nie należy używać istniejącego konta magazynu, które ma inne niemonitorowane dane, które są w nim przechowywane, dzięki czemu można lepiej kontrolować dostęp do danych monitorowania. Jeśli przechowujesz również [Dziennik aktywności](activity-logs-overview.md) na koncie magazynu, możesz użyć tego samego konta magazynu, aby zachować wszystkie dane monitorowania w centralnej lokalizacji.

## <a name="create-a-diagnostic-setting"></a>Utwórz ustawienie diagnostyczne
Dzienniki zasobów nie są domyślnie zbierane. Zbierz je na koncie usługi Azure Storage i innych miejscach docelowych, tworząc ustawienia diagnostyczne dla zasobu platformy Azure. Aby uzyskać szczegółowe informacje [, zobacz Tworzenie ustawień diagnostycznych w celu zbierania dzienników i metryk na platformie Azure](diagnostic-settings.md) .


## <a name="data-retention"></a>Przechowywanie danych
Zasady przechowywania definiują liczbę dni przechowywania danych z poszczególnych kategorii dzienników i danych metryk przechowywanych na koncie magazynu. Zasady przechowywania mogą zawierać dowolną liczbę dni z zakresu od 0 do 365. Zasada przechowywania równa zero określa, że zdarzenia dla tej kategorii dziennika są przechowywane przez czas nieokreślony.

Zasady przechowywania są stosowane dziennie, więc po zakończeniu dnia (UTC) dzienniki będą usuwane z dnia, w którym dane są już poza tymi zasadami przechowywania. Na przykład jeśli masz zasady przechowywania w jeden dzień, na początku dnia już dziś dzienników z wczoraj zanim dnia zostaną usunięte. Proces usuwania rozpoczyna się od północy czasu UTC, ale należy pamiętać, że może upłynąć do 24 godzin dla dzienników są usuwane z konta magazynu. 


## <a name="schema-of-resource-logs-in-storage-account"></a>Schemat dzienników zasobów na koncie magazynu

Po utworzeniu ustawienia diagnostycznego kontener magazynu jest tworzony na koncie magazynu zaraz po wystąpieniu zdarzenia w jednej z włączonych kategorii dzienników. Obiekty blob w kontenerze używają następującej konwencji nazewnictwa:

```
insights-logs-{log category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/RESOURCEGROUPS/{resource group name}/PROVIDERS/{resource provider name}/{resource type}/{resource name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Na przykład obiekt BLOB sieciowej grupy zabezpieczeń może mieć nazwę podobną do następującej:

```
insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUP/TESTNSG/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

Każdy obiekt blob PT1H.json zawiera obiekt blob JSON ze zdarzeniami, które wystąpiły w ciągu godziny określonej w adresie URL obiektu blob (na przykład h = 12). Zdarzenia występujące w danej chwili są na bieżąco dołączane do pliku PT1H.json. Wartość minuta (m = 00) jest zawsze równa 00, ponieważ zdarzenia dziennika zasobów są podzielone na pojedyncze obiekty blob na godzinę.

W pliku PT1H. JSON każde zdarzenie jest przechowywane w następującym formacie. Spowoduje to użycie wspólnego schematu najwyższego poziomu, ale będzie unikatowy dla każdej usługi platformy Azure, zgodnie z opisem w [schemacie dzienniki zasobów](resource-logs-overview.md#resource-logs-schema).

``` JSON
{"time": "2016-07-01T00:00:37.2040000Z","systemId": "46cdbb41-cb9c-4f3d-a5b4-1d458d827ff1","category": "NetworkSecurityGroupRuleCounter","resourceId": "/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/TESTNSG","operationName": "NetworkSecurityGroupCounters","properties": {"vnetResourceGuid": "{12345678-9012-3456-7890-123456789012}","subnetPrefix": "10.3.0.0/24","macAddress": "000123456789","ruleName": "/subscriptions/ s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg/securityRules/default-allow-rdp","direction": "In","type": "allow","matchedConnections": 1988}}
```

> [!NOTE]
> Dzienniki platformy są zapisywane w usłudze BLOB Storage przy użyciu [wierszy JSON](http://jsonlines.org/), gdzie każde zdarzenie jest wierszem, a znak nowego wiersza wskazuje nowe zdarzenie. Ten format został zaimplementowany w listopadzie 2018. Przed tą datą dzienniki zostały zapisane w magazynie obiektów BLOB jako tablica rekordów JSON, zgodnie z opisem w artykule [przygotowanie do zmiany formatu do Azure monitor dzienników platformy zarchiwizowanych na koncie magazynu](resource-logs-blob-format.md).

## <a name="next-steps"></a>Następne kroki

* [Pobierz obiekty blob do analizy](../../storage/blobs/storage-quickstart-blobs-dotnet.md).
* [Archiwizuj dzienniki Azure Active Directory przy użyciu Azure monitor](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md).
* [Przeczytaj więcej na temat dzienników zasobów](../../azure-monitor/platform/resource-logs-overview.md).

