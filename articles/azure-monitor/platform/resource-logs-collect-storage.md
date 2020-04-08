---
title: Archiwizuj dzienniki zasobów platformy Azure na koncie magazynu | Dokumenty firmy Microsoft
description: Dowiedz się, jak archiwizować dzienniki zasobów platformy Azure w celu długoterminowego przechowywania na koncie magazynu.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/15/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 787640ef08ee91220f42065af155c372247f0136
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804609"
---
# <a name="archive-azure-resource-logs-to-storage-account"></a>Archiwizuj dzienniki zasobów platformy Azure na koncie magazynu
[Dzienniki platformy](platform-logs-overview.md) na platformie Azure, w tym dzienniki aktywności platformy Azure i dzienniki zasobów, zawierają szczegółowe informacje diagnostyczne i inspekcji dla zasobów platformy Azure i platformy Azure, na których zależą.  W tym artykule opisano zbieranie dzienników platformy do konta magazynu platformy Azure w celu zachowania danych do archiwizacji.

## <a name="prerequisites"></a>Wymagania wstępne
Musisz [utworzyć konto magazynu platformy Azure,](../../storage/common/storage-account-create.md) jeśli jeszcze go nie masz. Konto magazynu nie musi znajdować się w tej samej subskrypcji co dzienniki wysyłania zasobu, o ile użytkownik, który konfiguruje to ustawienie, ma odpowiedni dostęp RBAC do obu subskrypcji.

> [!IMPORTANT]
> Aby wysłać dane do niezmiennego magazynu, ustaw niezmienne zasady dla konta magazynu, zgodnie z opisem w [polu Ustaw zasady niezmienności dla magazynu obiektów Blob i zarządzaj nimi](../../storage/blobs/storage-blob-immutability-policies-manage.md). Należy wykonać wszystkie kroki opisane w tym artykule, w tym włączenie chronionych zapisów identyfikatorów blob dołączania.

> [!IMPORTANT]
> Konta usługi Azure Data Lake Storage Gen2 nie są obecnie obsługiwane jako miejsce docelowe dla ustawień diagnostycznych, nawet jeśli mogą być wymienione jako prawidłowa opcja w witrynie Azure portal.


Nie należy używać istniejącego konta magazynu, które ma inne, nieobjętych monitorowania danych przechowywanych w nim, dzięki czemu można lepiej kontrolować dostęp do danych. Jeśli jednak archiwizujesz dzienniki aktywności i dzienniki zasobów razem, możesz użyć tego samego konta magazynu, aby zachować wszystkie dane monitorowania w centralnej lokalizacji.

## <a name="create-a-diagnostic-setting"></a>Tworzenie ustawienia diagnostycznego
Wysyłaj dzienniki platformy do magazynu i innych miejsc docelowych, tworząc ustawienie diagnostyczne dla zasobu platformy Azure. Zobacz [Tworzenie ustawień diagnostycznych do zbierania dzienników i metryk na platformie Azure,](diagnostic-settings.md) aby uzyskać szczegółowe informacje.


## <a name="collect-data-from-compute-resources"></a>Zbieranie danych z zasobów obliczeniowych
Ustawienia diagnostyczne będą zbierać dzienniki zasobów zasobów obliczeniowych platformy Azure, jak każdy inny zasób, ale nie ich system operacyjny gościa lub obciążeń. Aby zebrać te dane, zainstaluj [agenta diagnostyki systemu Windows Azure](diagnostics-extension-overview.md). 


## <a name="schema-of-platform-logs-in-storage-account"></a>Schemat logowania platformy na koncie magazynu

Po utworzeniu ustawienia diagnostycznego kontener magazynu jest tworzony na koncie magazynu, gdy tylko wystąpi zdarzenie w jednej z włączonych kategorii dziennika. Obiekty BLOB w kontenerze używają następującej konwencji nazewnictwa:

```
insights-logs-{log category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/RESOURCEGROUPS/{resource group name}/PROVIDERS/{resource provider name}/{resource type}/{resource name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Na przykład obiekt blob dla sieciowej grupy zabezpieczeń może mieć nazwę podobną do następującej:

```
insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUP/TESTNSG/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

Każdy obiekt blob PT1H.json zawiera obiekt blob JSON ze zdarzeniami, które wystąpiły w ciągu godziny określonej w adresie URL obiektu blob (na przykład h = 12). Zdarzenia występujące w danej chwili są na bieżąco dołączane do pliku PT1H.json. Wartość minutowa (m=00) jest zawsze 00, ponieważ zdarzenia dziennika zasobów są podzielone na poszczególne obiekty blob na godzinę.

W pliku PT1H.json każde zdarzenie jest przechowywane w następującym formacie. Spowoduje to użycie wspólnego schematu najwyższego poziomu, ale będzie unikatowe dla każdej usługi platformy Azure, zgodnie z opisem w [schemacie dzienników zasobów](diagnostic-logs-schema.md) i [schematu dziennika aktywności.](activity-log-schema.md)

``` JSON
{"time": "2016-07-01T00:00:37.2040000Z","systemId": "46cdbb41-cb9c-4f3d-a5b4-1d458d827ff1","category": "NetworkSecurityGroupRuleCounter","resourceId": "/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/TESTNSG","operationName": "NetworkSecurityGroupCounters","properties": {"vnetResourceGuid": "{12345678-9012-3456-7890-123456789012}","subnetPrefix": "10.3.0.0/24","macAddress": "000123456789","ruleName": "/subscriptions/ s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg/securityRules/default-allow-rdp","direction": "In","type": "allow","matchedConnections": 1988}}
```

> [!NOTE]
> Dzienniki platformy są zapisywane w magazynie obiektów blob przy użyciu [wierszy JSON](http://jsonlines.org/), gdzie każde zdarzenie jest wierszem, a znak nowego wiersza wskazuje nowe zdarzenie. Format ten został wdrożony w listopadzie 2018 r. Przed tą datą dzienniki zostały zapisane w magazynie obiektów blob jako tablica rekordów json, zgodnie z opisem w [obszarze Przygotowanie do zmiany formatu dzienników platformy Azure Monitor zarchiwizowanych na koncie magazynu](resource-logs-blob-format.md).

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się więcej o dziennikach zasobów](platform-logs-overview.md).
* [Utwórz ustawienie diagnostyczne do zbierania dzienników i metryk na platformie Azure](diagnostic-settings.md).
* [Pobierz obiekty BLOB do analizy](../../storage/blobs/storage-quickstart-blobs-dotnet.md).
* [Archiwizuj dzienniki usługi Azure Active Directory za pomocą usługi Azure Monitor](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md).
