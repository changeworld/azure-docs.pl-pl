---
title: Zrozumienie sposobu działania narzędzia migracji dobrowolne alertów usługi Azure Monitor
description: Zrozumienie sposobu działania narzędzia migracji alerty i rozwiązywanie problemów.
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: 9d872a6d753a206dcfb03761e50e5854db4f146e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67071589"
---
# <a name="understand-how-the-migration-tool-works"></a>Zrozumienie sposobu działania narzędzia migracji

Jako [ogłoszonej wcześniej](monitoring-classic-retirement.md), alertów klasycznych w usłudze Azure Monitor są zostanie wycofana w 2019 września (został pierwotnie 2019 lipca). Narzędzie migracji jest dostępne w portalu Azure, aby klienci używają reguł alertów klasycznych i którzy chcą wyzwolić migrację samodzielnie.

W tym artykule opisano, jak działa narzędzie dobrowolne migracji. Omówiono także środki zaradcze dla niektórych typowych problemów.

> [!NOTE]
> Ze względu na opóźnienie wdrożenie narzędzie do migracji została dacie wycofania migracji alertów klasycznych [rozszerzony do 31 sierpnia 2019](https://azure.microsoft.com/updates/azure-monitor-classic-alerts-retirement-date-extended-to-august-31st-2019/) od daty pierwotnie ogłoszone się 30 czerwca 2019 r.

## <a name="which-classic-alert-rules-can-be-migrated"></a>Które klasycznej reguły alertu można poddać migracji?

Mimo że narzędzia można migrować prawie wszystkich klasycznych reguł alertów, istnieją pewne wyjątki. Następujące reguły alertu nie będą migrowane przy użyciu narzędzia (lub podczas automatycznej migracji w 2019 września):

- Klasyczne reguły alertów dotyczących metryk gościa maszyny wirtualnej (Windows i Linux). Zobacz [wskazówki dotyczące ponownego tworzenia tych reguł alertów w nowych alertów dotyczących metryk](#guest-metrics-on-virtual-machines) w dalszej części tego artykułu.
- Klasyczne reguły alertów dotyczących metryk klasycznego magazynu. Zobacz [wskazówki dotyczące monitorowania sieci klasycznych kont magazynu](https://azure.microsoft.com/blog/modernize-alerting-using-arm-storage-accounts/).
- Klasyczne reguły alertów na niektóre metryki konta magazynu. Zobacz [szczegóły](#storage-account-metrics) w dalszej części tego artykułu.

Jeśli Twoja subskrypcja obejmuje takie zasady klasyczne, można migrować je ręcznie. Ponieważ firma Microsoft nie udostępnia automatyczną migrację, wszelkie istniejące, klasyczne alertów dotyczących metryk z tych typów będą nadal działać do momentu 2020 czerwca. To rozszerzenie zapewnia czasu, należy przenieść do nowych alertów. Jednak nowe alerty klasyczne nie mogą być tworzone po sierpniu 2019 r.

### <a name="guest-metrics-on-virtual-machines"></a>Metryki gościa na maszynach wirtualnych

Przed utworzeniem nowych alertów dotyczących metryk na metryki gościa, metryk gościa muszą być wysyłane do magazynu usługi Azure Monitor metryki niestandardowe. Wykonaj te instrukcje, aby umożliwić ujścia usługi Azure Monitor w ustawieniach diagnostycznych:

- [Włączanie metryki gościa dla maszyn wirtualnych Windows](collect-custom-metrics-guestos-resource-manager-vm.md)
- [Włączanie metryki gościa dla maszyn wirtualnych systemu Linux](collect-custom-metrics-linux-telegraf.md)

Po te kroki są wykonywane, można utworzyć nowych alertów dotyczących metryk na metryki gościa. I po utworzeniu nowych alertów dotyczących metryk, możesz usunąć alertów klasycznych.

### <a name="storage-account-metrics"></a>Metryki konta magazynu

Oprócz alertów dotyczących tych metryk można poddać migracji wszystkich alertów klasycznych na kontach magazynu:

- PercentAuthorizationError
- PercentClientOtherError
- PercentNetworkError
- PercentServerOtherError
- PercentSuccess
- PercentThrottlingError
- Wartości PercentTimeoutError
- AnonymousThrottlingError
- SASThrottlingError
- ThrottlingError

Klasyczny alert zasad dotyczących metryki procent muszą być migrowane na podstawie [mapowanie między metryk magazynowania stare i nowe](https://docs.microsoft.com/azure/storage/common/storage-metrics-migration#metrics-mapping-between-old-metrics-and-new-metrics). Wartości progowe, będzie konieczne odpowiednio można modyfikować, ponieważ nowy dostępnych metryk jest bezwzględna.

Klasyczne reguły alertów na AnonymousThrottlingError, SASThrottlingError i ThrottlingError musi zostać podzielony na dwa nowe alerty, ponieważ nie istnieje żadne połączone metrykę, która zawiera te same funkcje. Progi musi być odpowiednio dostosowane.

## <a name="rollout-phases"></a>Etapy wdrażania

Narzędzie do migracji jest wprowadzane w fazach klientów korzystających z klasycznej reguły alertu. Właściciele subskrypcji otrzymają wiadomość e-mail, gdy subskrypcja będzie gotowa do migracji za pomocą narzędzia.

> [!NOTE]
> Ponieważ to narzędzie jest wdrażana w fazach, może pojawić się, że większość subskrypcji, ponieważ nie są jeszcze gotowe do migracji podczas wczesnych faz.

Obecnie podzbiór subskrypcji jest oznaczone jako gotowe do migracji. Podzbiór ten obejmuje te subskrypcje, które mają reguły alertów klasycznych tylko w następujących typów zasobów. Obsługę większej liczby typów zasobów zostanie dodana w kolejnych fazach.

- Microsoft.apimanagement/service
- Microsoft.batch/batchaccounts
- Microsoft.cache/redis
- Microsoft.datafactory/datafactories
- Microsoft.dbformysql/servers
- Microsoft.dbforpostgresql/servers
- Microsoft.eventhub/Namespaces
- Microsoft.logic/workflows
- Microsoft.network/applicationgateways
- Microsoft.network/dnszones
- Microsoft.network/expressroutecircuits
- Microsoft.network/loadbalancers
- Microsoft.network/networkwatchers/connectionmonitors
- Microsoft.network/publicipaddresses
- Microsoft.network/trafficmanagerprofiles
- Microsoft.network/virtualnetworkgateways
- Microsoft.search/searchservices
- Microsoft.servicebus/namespaces
- Microsoft.streamanalytics/streamingjobs
- Microsoft.timeseriesinsights/environments
- Microsoft.web/hostingenvironments/workerpools
- Microsoft.web/serverfarms
- Microsoft.web/sites

## <a name="who-can-trigger-the-migration"></a>Kto może wyzwalać migracji?

Każdy użytkownik mający wbudowana rola Współautor monitorowania na poziomie subskrypcji można uruchomić migracji. Użytkownicy, którzy mają rolę niestandardową z następującymi uprawnieniami może także wyzwolić migracji:

- */read
- Microsoft.Insights/actiongroups/*
- Microsoft.Insights/AlertRules/*
- Microsoft.Insights/metricAlerts/*

## <a name="common-problems-and-remedies"></a>Typowe problemy i środki zaradcze

Po zakończeniu [wyzwolić migracji](alerts-using-migration-tool.md), otrzymasz wiadomość e-mail na adresy podane informujące, że migracja została zakończona lub jeśli wymaga żadnych akcji ze strony użytkownika. W tej sekcji opisano niektóre typowe problemy i radzenia sobie z nimi.

### <a name="validation-failed"></a>Walidacja nie powiodła się

Ze względu na pewne ostatnie zmiany klasyczne reguły alertów w ramach subskrypcji nie można zmigrować subskrypcji. Ten problem jest tymczasowy. Możesz ponownie uruchomić migrację po przechodzi w stan migracji **gotowe do migracji** w ciągu kilku dni.

### <a name="policy-or-scope-lock-preventing-us-from-migrating-your-rules"></a>Zasad lub w zakresie blokada uniemożliwia migracji reguł

W ramach migracji można utworzyć nowych alertów dotyczących metryk i nowych grup akcji, a następnie zostaną usunięte klasycznej reguły alertu. Istnieje jednak zasad lub zakres blokada uniemożliwia tworzenie zasobów. W zależności od zasad lub zakres blokady nie można migrować niektóre lub wszystkie reguły. Ten problem można rozwiązać przez tymczasowe usunięcie blokady zakresu lub zasad i wyzwalając próbę wykonania migracji.

## <a name="next-steps"></a>Kolejne kroki

- [Sposób użycia narzędzia migracji](alerts-using-migration-tool.md)
- [Przygotowanie do migracji](alerts-prepare-migration.md)
