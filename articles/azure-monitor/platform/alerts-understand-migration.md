---
title: Zrozumienie sposobu dobrowolne narzędzia migracji alertów w usłudze Azure Monitor współpracuje
description: Zrozumienie sposobu działania narzędzia migracji alertów i rozwiązywanie problemów, jeśli wystąpią problemy.
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: a45a0cff606bc854924d5da0841b26e1cb9031bb
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58632317"
---
# <a name="understand-how-the-migration-tool-works"></a>Zrozumienie sposobu działania narzędzia migracji

Jako [ogłoszonej wcześniej](monitoring-classic-retirement.md), alertów klasycznych w usłudze Azure Monitor są zostanie wycofana w lipcu 2019 r. Narzędzie migracji, aby wyzwolić dobrowolnie migracji jest dostępne w witrynie Azure portal i wprowadza się klienci, którzy używają klasycznej reguły alertu.

Ten artykuł przeprowadzi działania narzędzia migracji dobrowolne. Omówiono także korygowanie niektórych typowych problemów.

## <a name="which-classic-alert-rules-can-be-migrated"></a>Które klasycznej reguły alertu można poddać migracji?

Gdy prawie wszystkich reguł alertów klasycznych można poddać migracji za pomocą narzędzia, istnieją pewne wyjątki. Następujące reguły alertu nie będą migrowane przy użyciu narzędzia (lub podczas automatycznej migracji w 2019 lipca)

- Klasyczne reguły alertów dotyczących metryk gościa maszyny wirtualnej (Windows i Linux). [Zobacz wskazówki na temat ponownego tworzenia tych reguł alertów w nowych alertów dotyczących metryk](#guest-metrics-on-virtual-machines)
- Klasyczne reguły alertów dotyczących metryk klasycznego magazynu. [Zobacz wskazówki dotyczące monitorowania sieci klasycznych kont magazynu](https://azure.microsoft.com/blog/modernize-alerting-using-arm-storage-accounts/)
- Klasyczne reguły alertów na niektóre metryki konta magazynu. [Poniższe szczegóły](#storage-account-metrics)

Jeśli Twoja subskrypcja obejmuje takie zasady klasyczne, pozostałe reguły zostaną poddane migracji, ale będzie musiał ręcznie zmigrować te reguły. Firma Microsoft nie udostępnia automatyczną migrację, wszystkie takie istniejących metryki alertów klasycznych będzie współpracował nad 2020 czerwca w celu zapewnienia czasu, należy przenieść do nowych alertów. Jednak nowe alerty klasyczne nie mogą być tworzone po czerwca 2019 r.

### <a name="guest-metrics-on-virtual-machines"></a>Metryki gościa na maszynach wirtualnych

Aby można było utworzyć nowych alertów dotyczących metryk na metryki gościa, metryk gościa muszą być wysyłane do magazynu usługi Azure Monitor metryki niestandardowe. Postępuj zgodnie z poniższymi instrukcjami, aby włączyć ujścia usługi Azure Monitor w konfiguracji ustawień diagnostyki.

- [Włączanie metryki gościa dla maszyn wirtualnych Windows](collect-custom-metrics-guestos-resource-manager-vm.md)
- [Włączanie metryki gościa dla maszyn wirtualnych systemu Linux](https://docs.microsoft.com/azure/azure-monitor/platform/collect-custom-metrics-linux-telegraf)

Po ukończeniu powyższych kroków można tworzyć nowych alertów dotyczących metryk na metryki gościa. Gdy zostały utworzone ponownie nowych alertów dotyczących metryk, alertów klasycznych mogą zostać usunięte.

### <a name="storage-account-metrics"></a>Metryki konta magazynu

Wszystkie alerty klasyczne dla kont magazynu można poddać migracji, z wyjątkiem tych alertów na następujących metrykach:

- PercentAuthorizationError
- PercentClientOtherError
- PercentNetworkError
- PercentServerOtherError
- PercentSuccess
- PercentThrottlingError
- Wartości PercentTimeoutError
- AnonymousThrottlingError
- SASThrottlingError

Klasyczne reguły alertów dotyczących metryk procent należy zmigrować na podstawie [mapowanie między metryk magazynowania stare i nowe](https://docs.microsoft.com/azure/storage/common/storage-metrics-migration#metrics-mapping-between-old-metrics-and-new-metrics). Progi należy odpowiednio można zmodyfikować zgodnie z nową metrykę, dostępne jest bezwzględna.

AnonymousThrottlingError i SASThrottlingError będą musiały zostać podzielony na dwie ponieważ istnieją nowe alerty klasyczne reguł alertów jest nie połączone metrykę, która zawiera te same funkcje. Progi musi być odpowiednio dostosowane.

## <a name="roll-out-phases"></a>Wdrożenie fazy

Narzędzie do migracji jest wprowadzane w fazach klientów korzystających z klasycznej reguły alertu. **Właściciele subskrypcji** zostanie wysłana wiadomość e-mail, gdy subskrypcja będzie gotowa do migracji za pomocą narzędzia.

> [!NOTE]
> Jak narzędzie jest wdrażana w fazach w wczesnych faz, może pojawić się, że większość subskrypcji, ponieważ nie są jeszcze gotowe do migracji.

Obecnie **podzbioru** subskrypcji, która **tylko** mają klasycznej reguły alertu dla zasobu następujące typy są oznaczone jako gotowe do migracji. Obsługę większej liczby typów zasobów zostanie dodana w kolejnych fazach.

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

Każdy użytkownik mający rolę wbudowaną z **Współautor monitorowania** w subskrypcji poziomu będzie można wyzwolić migracji. Użytkownicy mający rolę niestandardową z następującymi uprawnieniami może także wyzwolić migracji:

- */read
- Microsoft.Insights/actiongroups/*
- Microsoft.Insights/AlertRules/*
- Microsoft.Insights/metricAlerts/*

## <a name="common-issues-and-remediations"></a>Typowe problemy i ich korygowania funkcję

Po [wyzwolić migracji](alerts-using-migration-tool.md), będą używane adresy e-mail, pod warunkiem informujące o zakończeniu migracji, lub jeśli jest akcja wymaga ze strony użytkownika. W poniższej sekcji opisano niektóre typowe problemy oraz jak je skorygować

### <a name="validation-failed"></a>Nie można sprawdzić poprawności

Ze względu na pewne ostatnie zmiany klasyczne reguły alertów w ramach subskrypcji nie można zmigrować subskrypcji. Jest to problem tymczasowy. Możesz ponownie uruchomić migrację po przechodzi w stan migracji **gotowe do migracji** w ciągu kilku dni.

### <a name="policyscope-lock-preventing-us-from-migrating-your-rules"></a>Zakres zasad/lock uniemożliwia migracji reguł

W ramach migracji nowych alertów dotyczących metryk i nowych grup akcji zostaną utworzone i klasycznej reguły alertu zostaną usunięte (po utworzeniu nowych zasad). Istnieje jednak zasad lub zakres blokada uniemożliwia tworzenie zasobów. W zależności od zasad lub zakres blokady nie można migrować niektóre lub wszystkie reguły. Można rozwiązać ten problem, usuwając zasady zakresu blokowania/tymczasowo i wyzwolić próbę wykonania migracji.

## <a name="next-steps"></a>Kolejne kroki

- [Sposób użycia narzędzia migracji](alerts-using-migration-tool.md)
- [Przygotowanie do migracji](alerts-prepare-migration.md)
