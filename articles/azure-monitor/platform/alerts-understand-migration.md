---
title: Zrozumienie sposobu działania narzędzia migracji dobrowolne alertów usługi Azure Monitor
description: Zrozumienie sposobu działania narzędzia migracji alerty i rozwiązywanie problemów.
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: 015000388c5629dbd8ed8833931a809ebd738bd6
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2019
ms.locfileid: "67295523"
---
# <a name="understand-how-the-migration-tool-works"></a>Zrozumienie sposobu działania narzędzia migracji

Jako [ogłoszonej wcześniej](monitoring-classic-retirement.md), alertów klasycznych w usłudze Azure Monitor są zostanie wycofana 31 sierpnia 2019 r (został pierwotnie 30 czerwca maja 2019 r). Narzędzie migracji jest dostępne w portalu Azure, aby klienci używają reguł alertów klasycznych i którzy chcą wyzwolić migrację samodzielnie.

W tym artykule opisano, jak działa narzędzie dobrowolne migracji. Omówiono także środki zaradcze dla niektórych typowych problemów.

> [!NOTE]
> Ze względu na opóźnienie wdrożenie narzędzie do migracji została dacie wycofania migracji alertów klasycznych [rozszerzony do 31 sierpnia 2019](https://azure.microsoft.com/updates/azure-monitor-classic-alerts-retirement-date-extended-to-august-31st-2019/) od daty pierwotnie ogłoszone się 30 czerwca 2019 r.

## <a name="classic-alert-rules-that-will-not-be-migrated"></a>Klasyczne reguły alertów, które nie zostaną zmigrowane

> [!IMPORTANT]
> Alerty dziennika aktywności (takie jak usługa alerty dotyczące kondycji) i alertów usługi Log nie podlegają migracji. Migracja dotyczy tylko klasyczne reguły alertów opisem [tutaj](monitoring-classic-retirement.md#retirement-of-classic-monitoring-and-alerting-platform).

Mimo że narzędzia można migrować prawie wszystkie [klasycznej reguły alertu](monitoring-classic-retirement.md#retirement-of-classic-monitoring-and-alerting-platform), istnieje kilka wyjątków. Następujące reguły alertu nie będą migrowane przy użyciu narzędzia (lub podczas migracji automatyczne uruchamianie 2019 września):

- Klasyczne reguły alertów dotyczących metryk gościa maszyny wirtualnej (Windows i Linux). Zobacz [wskazówki dotyczące ponownego tworzenia tych reguł alertów w nowych alertów dotyczących metryk](#guest-metrics-on-virtual-machines) w dalszej części tego artykułu.
- Klasyczne reguły alertów dotyczących metryk klasycznego magazynu. Zobacz [wskazówki dotyczące monitorowania sieci klasycznych kont magazynu](https://azure.microsoft.com/blog/modernize-alerting-using-arm-storage-accounts/).
- Klasyczne reguły alertów na niektóre metryki konta magazynu. Zobacz [szczegóły](#storage-account-metrics) w dalszej części tego artykułu.
- Klasyczne reguły alertów na niektóre metryki usługi Cosmos DB. Szczegóły zostanie dodana w przyszłej aktualizacji.

Jeśli Twoja subskrypcja obejmuje takie zasady klasyczne, można migrować je ręcznie. Ponieważ firma Microsoft nie udostępnia automatyczną migrację, wszelkie istniejące, klasyczne alertów dotyczących metryk z tych typów będą nadal działać do momentu 2020 czerwca. To rozszerzenie zapewnia czasu, należy przenieść do nowych alertów. Jednak nowe alerty klasyczne nie mogą być tworzone po sierpniu 2019 r.

> [!NOTE]
> Oprócz powyższej listy wyjątków, jeśli klasyczne reguł alertu są niepoprawne tj znajdują się w [przestarzałe metryki](#classic-alert-rules-on-deprecated-metrics) lub zasobów, które zostały usunięte, nie zostaną zmigrowane podczas migracji dobrowolne. Wszystkie takie nieprawidłowy klasyczne reguły alertów zostaną usunięte w sytuacji automatyczną migrację.

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

### <a name="classic-alert-rules-on-deprecated-metrics"></a>Klasyczne reguły alertów dotyczących metryk przestarzałe

Są to klasyczne reguły alertów dotyczących metryk, które były wcześniej obsługiwane, ale ostatecznie zostały zaniechane. Na niewielkim odsetku klientów może mieć nieprawidłowy klasyczne reguły alertów na takie metryki. Ponieważ te reguły alertów są nieprawidłowe, nie będą migrowane.

| Typ zasobu| Przestarzałe metric(s) |
|-------------|----------------- |
| Microsoft.DBforMySQL/servers | compute_consumption_percent, compute_limit |
| Microsoft.DBforPostgreSQL/servers | compute_consumption_percent, compute_limit |
| Microsoft.Network/publicIPAddresses | defaultddostriggerrate |
| Microsoft.SQL/servers/databases | service_level_objective storage_limit, storage_used, ograniczanie przepustowości, dtu_consumption_percent, storage_used |
| Microsoft.Web/hostingEnvironments/multirolepools | averagememoryworkingset |
| Microsoft.Web/hostingEnvironments/workerpools | BytesReceived, httpqueuelength |

## <a name="how-equivalent-new-alert-rules-and-action-groups-are-created"></a>Jak są tworzone równoważne nowe reguły alertów i grup akcji

Narzędzie migracji konwertuje regułami alertów klasycznych równoważne nowe reguły alertów i grup akcji. Dla większości reguł alertów klasycznych, równoważne nowe reguły alertu znajdują się na tym samym metryki z tymi samymi właściwościami takich jak `windowSize` i `aggregationType`. Istnieją jednak pewne klasycznego alertu, który zasady znajdują się na metryki, które mają inną, równoważne metryki w nowym systemie. Następujące zasady mają zastosowanie w przypadku migracji alertów klasycznych, o ile nie określono w dalszej części tego artykułu:

- **Częstotliwość**: Definiuje, jak często sprawdza klasycznego lub nowego regułę alertu dla warunku. `frequency` w klasycznej reguły alertów nie można konfigurować przez użytkownika i był to zawsze 5 minut dla wszystkich typów zasobów, z wyjątkiem składników usługi Application Insights, dla których był 1 min. Dlatego częstotliwość równoważne zasad jest również ustawiona na 5 min i 1 min odpowiednio.
- **Typ agregacji**: Definiuje, jak metryki są agregowane w przedziale zainteresowania. `aggregationType` Również jest taki sam, między alertów klasycznych i nowych alertów dla większości metryk. W niektórych przypadkach, ponieważ Metryka różni się między alertów klasycznych i nowych alertów równoważne `aggregationType` lub `primary Aggregation Type` zdefiniowany dla metryka jest używana.
- **Jednostki**: Właściwość metryki, na którym tworzony jest alert. Niektóre metryki równoważne mają różne jednostki. Próg jest dostosowywany odpowiednio zgodnie z potrzebami. Na przykład jeśli oryginalny metryki ma sekundy jako jednostki, ale równoważne nową metrykę ma milisekund jako liczba jednostek, oryginalna wartość progową jest mnożony przez 1000, aby upewnić się, takie samo zachowanie.
- **Rozmiar okna**: Definiuje okno, przez które metryki są agregowane do porównania z wartością progową. Dla warstwy standardowej `windowSize` wartości, takich jak 5mins 15mins, 30 minut, 1 godziny, 3 godzin, 6, 12 godzin, 1 dzień nastąpiła żadna zmiana dla równoważnych Nowa reguła alertu. Aby uzyskać inne wartości, znajdujący się najbliżej `windowSize` została wybrana do użycia. W przypadku większości klientów nie ma to wpływu dzięki tej zmianie. W przypadku niewielkiego odsetka klientów może być trzeba dostosować próg, aby uzyskać dokładnie takie samo zachowanie.

W poniższych sekcjach szczegółowo firma Microsoft, metryki, które mają inną, równoważne metryki w nowym systemie. Nie ma na liście dowolnej metryce, która pozostaje taki sam dla klasycznych i nowych reguł alertów. Można znaleźć listę obsługiwanych w nowym systemie metryk [tutaj](metrics-supported.md).

### <a name="microsoftstorageaccountsservices"></a>Microsoft.StorageAccounts/services

Dla usług konta magazynu, takich jak obiektów blob, tabel, plików i kolejek następujące metryki są mapowane na równoważne metryki, jak pokazano poniżej:

| Metryki w alertów klasycznych | Równoważne metryką przy podejmowaniu nowe alerty | Komentarze|
|--------------------------|---------------------------------|---------|
| AnonymousAuthorizationError| Metryka transakcji z wymiarami "ResponseType" = "AuthorizationError" i "Authentication" = "Anonymous"| |
| AnonymousClientOtherError | Metryka transakcji z wymiarami "ResponseType" = "ClientOtherError" i "Authentication" = "Anonymous" | |
| AnonymousClientTimeOutError| Metryka transakcji z wymiarami "ResponseType" = "ClientTimeOutError" i "Authentication" = "Anonymous" | |
| AnonymousNetworkError | Metryka transakcji z wymiarami "ResponseType" = "NetworkError" i "Authentication" = "Anonymous" | |
| AnonymousServerOtherError | Metryka transakcji z wymiarami "ResponseType" = "ServerOtherError" i "Authentication" = "Anonymous" | |
| AnonymousServerTimeOutError | Metryka transakcji z wymiarami "ResponseType" = "ServerTimeOutError" i "Authentication" = "Anonymous" | |
| AnonymousSuccess | Metryka transakcji z wymiarami "ResponseType" = "Powodzenie" i "Authentication" = "Anonymous" | |
| AuthorizationError | Metryka transakcji z wymiarami "ResponseType" = "AuthorizationError" | |
| AverageE2ELatency | SuccessE2ELatency | |
| AverageServerLatency | SuccessServerLatency | |
| Pojemność | BlobCapacity | Użyj `aggregationType` "średnia" zamiast "last". Metryka ma zastosowanie tylko do usług obiektów Blob |
| ClientOtherError | Metryka transakcji z wymiarami "ResponseType" = "ClientOtherError"  | |
| ClientTimeoutError | Metryka transakcji z wymiarami "ResponseType" = "ClientTimeOutError" | |
| ContainerCount | ContainerCount | Użyj `aggregationType` "średnia" zamiast "last". Metryka ma zastosowanie tylko do usług obiektów Blob |
| NetworkError | Metryka transakcji z wymiarami "ResponseType" = "NetworkError" | |
| ObjectCount | BlobCount| Użyj `aggregationType` "średnia" zamiast "last". Metryka ma zastosowanie tylko do usług obiektów Blob |
| SASAuthorizationError | Metryka transakcji z wymiarami "ResponseType" = "AuthorizationError" i "Authentication" = "Sygnatury dostępu Współdzielonego" | |
| SASClientOtherError | Metryka transakcji z wymiarami "ResponseType" = "ClientOtherError" i "Authentication" = "Sygnatury dostępu Współdzielonego" | |
| SASClientTimeOutError | Metryka transakcji z wymiarami "ResponseType" = "ClientTimeOutError" i "Authentication" = "Sygnatury dostępu Współdzielonego" | |
| SASNetworkError | Metryka transakcji z wymiarami "ResponseType" = "NetworkError" i "Authentication" = "Sygnatury dostępu Współdzielonego" | |
| SASServerOtherError | Metryka transakcji z wymiarami "ResponseType" = "ServerOtherError" i "Authentication" = "Sygnatury dostępu Współdzielonego" | |
| SASServerTimeOutError | Metryka transakcji z wymiarami "ResponseType" = "ServerTimeOutError" i "Authentication" = "Sygnatury dostępu Współdzielonego" | |
| SASSuccess | Metryka transakcji z wymiarami "ResponseType" = "Powodzenie" i "Authentication" = "Sygnatury dostępu Współdzielonego" | |
| ServerOtherError | Metryka transakcji z wymiarami "ResponseType" = "ServerOtherError" | |
| ServerTimeOutError | Metryka transakcji z wymiarami "ResponseType" = "ServerTimeOutError"  | |
| Powodzenie | Metryka transakcji z wymiarami "ResponseType" = "Powodzenie" | |
| TotalBillableRequests| Transakcje | |
| TotalEgress | Ruch wychodzący | |
| TotalIngress | Ruch przychodzący | |
| TotalRequests | Transakcje | |

### <a name="microsoftinsightscomponents"></a>Microsoft.insights/Components

Dla usługi Application Insights równoważne metryki są, jak pokazano poniżej:

| Metryki w alertów klasycznych | Równoważne metryką przy podejmowaniu nowe alerty | Komentarze|
|--------------------------|---------------------------------|---------|
| availability.availabilityMetric.value | availabilityResults/availabilityPercentage|   |
| availability.durationMetric.value | availabilityResults/czas trwania| Należy pomnożyć oryginalnej wartości progowej przez 1000 jednostek dla klasycznego metryki są w ciągu kilku sekund i nowych jednego są podawane w milisekundach.  |
| basicExceptionBrowser.count | Wyjątki/przeglądarki|  Użyj `aggregationType` "count" zamiast "Suma". |
| basicExceptionServer.count | Wyjątki serwera| Użyj `aggregationType` "count" zamiast "Suma".  |
| clientPerformance.clientProcess.value | browserTimings/processingDuration| Należy pomnożyć oryginalnej wartości progowej przez 1000 jednostek dla klasycznego metryki są w ciągu kilku sekund i nowych jednego są podawane w milisekundach.  |
| clientPerformance.networkConnection.value | browserTimings/networkDuration|  Należy pomnożyć oryginalnej wartości progowej przez 1000 jednostek dla klasycznego metryki są w ciągu kilku sekund i nowych jednego są podawane w milisekundach. |
| clientPerformance.receiveRequest.value | browserTimings/receiveDuration| Należy pomnożyć oryginalnej wartości progowej przez 1000 jednostek dla klasycznego metryki są w ciągu kilku sekund i nowych jednego są podawane w milisekundach.  |
| clientPerformance.sendRequest.value | browserTimings/sendDuration| Należy pomnożyć oryginalnej wartości progowej przez 1000 jednostek dla klasycznego metryki są w ciągu kilku sekund i nowych jednego są podawane w milisekundach.  |
| clientPerformance.total.value | browserTimings/totalDuration| Należy pomnożyć oryginalnej wartości progowej przez 1000 jednostek dla klasycznego metryki są w ciągu kilku sekund i nowych jednego są podawane w milisekundach.  |
| performanceCounter.available_bytes.value | liczniki wydajności/memoryAvailableBytes|   |
| performanceCounter.io_data_bytes_per_sec.value | liczniki wydajności/processIOBytesPerSecond|   |
| performanceCounter.number_of_exceps_thrown_per_sec.value | performanceCounters/exceptionsPerSecond|   |
| performanceCounter.percentage_processor_time_normalized.value | liczniki wydajności/processCpuPercentage|   |
| performanceCounter.percentage_processor_time.value | liczniki wydajności/processCpuPercentage| Próg, należy odpowiednio można modyfikować, ponieważ oryginalne metryki była ze wszystkich rdzeni i nową metrykę jest znormalizować do jednego rdzenia. Narzędzie do migracji nie zmienia wartości progowe.  |
| performanceCounter.percentage_processor_total.value | liczniki wydajności/processorCpuPercentage|   |
| performanceCounter.process_private_bytes.value | liczniki wydajności/processPrivateBytes|   |
| performanceCounter.request_execution_time.value | performanceCounters/requestExecutionTime|   |
| performanceCounter.requests_in_application_queue.value | performanceCounters/requestsInQueue|   |
| performanceCounter.requests_per_sec.value | performanceCounters/requestsPerSecond|   |
| request.duration | żądania/czas trwania| Należy pomnożyć oryginalnej wartości progowej przez 1000 jednostek dla klasycznego metryki są w ciągu kilku sekund i nowych jednego są podawane w milisekundach.  |
| request.rate | requests/rate|   |
| requestFailed.count | requests/failed| Użyj `aggregationType` "count" zamiast "Suma".   |
| view.count | Liczba/wyświetleń stron| Użyj `aggregationType` "count" zamiast "Suma".   |

### <a name="how-equivalent-action-groups-are-created"></a>W jaki sposób tworzone są równoważne działania grupy

Klasyczne reguły żadnego adresu e-mail, element webhook, logiki aplikacji i elementów runbook akcje powiązane z alertem regułę alertu sam. Nowe reguły alertu korzystanie z grup akcji, które mogą być ponownie używane w wielu reguł alertów. Narzędzie migracji tworzy grupę jednej akcji dla tych samych czynności niezależnie od tego, jak wiele reguł alertów są za pomocą akcji. Utworzone przez narzędzie do migracji grupy akcji formatu nazewnictwa "Migrated_AG *".

## <a name="rollout-phases"></a>Etapy wdrażania

Narzędzie do migracji jest wprowadzane w fazach klientów korzystających z klasycznej reguły alertu. Właściciele subskrypcji otrzymają wiadomość e-mail, gdy subskrypcja będzie gotowa do migracji za pomocą narzędzia.

> [!NOTE]
> Ponieważ narzędzie jest wdrażana w fazach, może pojawić się, że niektóre z Twoich subskrypcji nie są jeszcze gotowe do migracji podczas wczesnych faz.

Większość subskrypcje są obecnie oznaczone jako gotowe do migracji. Tylko subskrypcje alertów klasycznych na następujących typów zasobów są wciąż brak gotowości do migracji.

- Microsoft.classicCompute/domainNames/slots/roles
- Microsoft.documentDB/databases
- Microsoft.insights/Components

## <a name="who-can-trigger-the-migration"></a>Kto może wyzwalać migracji?

Każdy użytkownik mający wbudowana rola Współautor monitorowania na poziomie subskrypcji można uruchomić migracji. Użytkownicy, którzy mają rolę niestandardową z następującymi uprawnieniami może także wyzwolić migracji:

- */read
- Microsoft.Insights/actiongroups/*
- Microsoft.Insights/AlertRules/*
- Microsoft.Insights/metricAlerts/*

> [!NOTE]
> Oprócz wyżej uprawnienia, Twoja subskrypcja Dodatkowo można zarejestrować za pomocą dostawcy zasobów Microsoft.AlertsManagement. Jest to wymagane do pomyślnej migracji alertów niepowodzenia anomalii w usłudze Application Insights. 

## <a name="common-problems-and-remedies"></a>Typowe problemy i środki zaradcze

Po zakończeniu [wyzwolić migracji](alerts-using-migration-tool.md), otrzymasz wiadomość e-mail na adresy podane informujące, że migracja została zakończona lub jeśli wymaga żadnych akcji ze strony użytkownika. W tej sekcji opisano niektóre typowe problemy i radzenia sobie z nimi.

### <a name="validation-failed"></a>Walidacja nie powiodła się

Ze względu na pewne ostatnie zmiany klasyczne reguły alertów w ramach subskrypcji nie można zmigrować subskrypcji. Ten problem jest tymczasowy. Możesz ponownie uruchomić migrację po przechodzi w stan migracji **gotowe do migracji** w ciągu kilku dni.

### <a name="policy-or-scope-lock-preventing-us-from-migrating-your-rules"></a>Zasad lub w zakresie blokada uniemożliwia migracji reguł

W ramach migracji można utworzyć nowych alertów dotyczących metryk i nowych grup akcji, a następnie zostaną usunięte klasycznej reguły alertu. Istnieje jednak zasad lub zakres blokada uniemożliwia tworzenie zasobów. W zależności od zasad lub zakres blokady nie można migrować niektóre lub wszystkie reguły. Ten problem można rozwiązać przez tymczasowe usunięcie blokady zakresu lub zasad i wyzwalając próbę wykonania migracji.

## <a name="next-steps"></a>Kolejne kroki

- [Sposób użycia narzędzia migracji](alerts-using-migration-tool.md)
- [Przygotowanie do migracji](alerts-prepare-migration.md)
