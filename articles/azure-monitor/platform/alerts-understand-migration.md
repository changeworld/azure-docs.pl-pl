---
title: Dowiedz się, jak działa narzędzie do dobrowolnej migracji w przypadku alertów usługi Azure Monitor
description: Dowiedz się, jak działa narzędzie do migracji alertów i rozwiąż problemy.
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: yalavi
author: yalavi
ms.subservice: alerts
ms.openlocfilehash: 8cc77d13567910797cd519ac193b848f3ea434da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274816"
---
# <a name="understand-how-the-migration-tool-works"></a>Informacje o sposobie działania narzędzia do migracji

Zgodnie [z wcześniejszymi zapowiedziami](monitoring-classic-retirement.md)klasyczne alerty w usłudze Azure Monitor są wycofywane do 31 sierpnia 2019 r. (pierwotnie 30 czerwca 2019 r.). Narzędzie do migracji jest dostępne w witrynie Azure portal dla klientów, którzy używają klasycznych reguł alertów i którzy sami chcą wyzwolić migrację.

W tym artykule wyjaśniono, jak działa narzędzie dobrowolnej migracji. Opisano również środki zaradcze dla niektórych typowych problemów.

> [!NOTE]
> Ze względu na opóźnienie we wprowadzaniu narzędzia migracji data wycofania dla migracji alertów klasycznych została [przedłużona do 31 sierpnia 2019](https://azure.microsoft.com/updates/azure-monitor-classic-alerts-retirement-date-extended-to-august-31st-2019/) r. z pierwotnie ogłoszonej daty 30 czerwca 2019 r.

## <a name="classic-alert-rules-that-will-not-be-migrated"></a>Klasyczne reguły alertów, które nie są migrowane

> [!IMPORTANT]
> Migracja nie ma wpływu na alerty dziennika aktywności (w tym alerty kondycji usługi) i alerty dziennika. Migracja dotyczy tylko klasycznych reguł alertów opisanych [w tym miejscu](monitoring-classic-retirement.md#retirement-of-classic-monitoring-and-alerting-platform).

Chociaż narzędzie może migrować prawie wszystkie [klasyczne reguły alertów,](monitoring-classic-retirement.md#retirement-of-classic-monitoring-and-alerting-platform)istnieją pewne wyjątki. Następujące reguły alertów nie będą migrowane za pomocą narzędzia (lub podczas automatycznej migracji od września 2019 r.):

- Klasyczne reguły alertów dotyczące metryk gości na maszynie wirtualnej (zarówno windows, jak i linux). Zobacz [wskazówki dotyczące ponownego tworzenia takich reguł alertów w nowych alertów metryki](#guest-metrics-on-virtual-machines) w dalszej części tego artykułu.
- Klasyczne reguły alertów dotyczące klasycznych metryk magazynu. Zapoznaj się ze [wskazówkami dotyczącymi monitorowania klasycznych kont magazynu](https://azure.microsoft.com/blog/modernize-alerting-using-arm-storage-accounts/).
- Klasyczne reguły alertów dotyczące niektórych metryk konta magazynu. Zobacz [szczegóły](#storage-account-metrics) w dalszej części tego artykułu.
- Klasyczne reguły alertów dotyczące niektórych metryk usługi Cosmos DB. Zobacz [szczegóły](#cosmos-db-metrics) w dalszej części tego artykułu.
- Klasyczne reguły alertów dla wszystkich klasycznych maszyn wirtualnych i metryk usług w chmurze (Microsoft.ClassicCompute/virtualMachines i Microsoft.ClassicCompute/domainNames/slots/roles). Zobacz [szczegóły](#classic-compute-metrics) w dalszej części tego artykułu.

Jeśli subskrypcja ma takie klasyczne reguły, należy przeprowadzić ich migrację ręcznie. Ponieważ nie możemy zapewnić automatycznej migracji, wszystkie istniejące, klasyczne alerty metryki tego typu będą nadal działać do czerwca 2020 r. To rozszerzenie daje czas, aby przejść do nowych alertów. Do czerwca 2020 r. można również nadal tworzyć nowe klasyczne alerty dotyczące wyżej wymienionych wyjątków. Jednak dla wszystkich innych, żadne nowe klasyczne alerty mogą być tworzone po sierpniu 2019.

> [!NOTE]
> Oprócz wyżej wymienionych wyjątków, jeśli klasyczne reguły alertów są nieprawidłowe, czyli są one na [przestarzałe metryki](#classic-alert-rules-on-deprecated-metrics) lub zasoby, które zostały usunięte, nie zostaną zmigrowane i nie będą dostępne po zakończeniu usługi.

### <a name="guest-metrics-on-virtual-machines"></a>Metryki gościa na maszynach wirtualnych

Aby można było utworzyć nowe alerty metryki na metryki gościa, metryki gościa muszą być wysyłane do magazynu metryk niestandardowych usługi Azure Monitor. Postępuj zgodnie z tymi instrukcjami, aby włączyć ujście usługi Azure Monitor w ustawieniach diagnostycznych:

- [Włączanie danych gościa dla maszyn wirtualnych systemu Windows](collect-custom-metrics-guestos-resource-manager-vm.md)
- [Włączanie metryk gości dla maszyn wirtualnych z systemem Linux](collect-custom-metrics-linux-telegraf.md)

Po wykonaniu tych kroków można utworzyć nowe alerty metryki na metryki gościa. Po utworzeniu nowych alertów metryk można usunąć alerty klasyczne.

### <a name="storage-account-metrics"></a>Metryki konta magazynu

Wszystkie klasyczne alerty na kontach magazynu mogą być migrowane z wyjątkiem alertów dotyczących tych metryk:

- ProcentAutoryzacjaError
- ProcentClientOtherError
- ProcentNetworkError
- PercentServerOtherError
- ProcentSukces
- ProcentThrottlingError
- ProcentCzaserror
- AnonimowyThrottlingError
- SASThrottlingError
- ThrottlingError

Klasyczne reguły alertów dotyczące metryk Procent muszą być migrowane na podstawie [mapowania między metrykami starego i nowego magazynu](https://docs.microsoft.com/azure/storage/common/storage-metrics-migration#metrics-mapping-between-old-metrics-and-new-metrics). Progi będą musiały zostać odpowiednio zmodyfikowane, ponieważ nowa dostępna metryka jest bezwzględna.

Klasyczne reguły alertów na AnonymousThrottlingError, SASThrottlingError i ThrottlingError muszą być podzielone na dwa nowe alerty, ponieważ nie ma metryki połączonej, która zapewnia tę samą funkcjonalność. Progi będą musiały zostać odpowiednio dostosowane.

### <a name="cosmos-db-metrics"></a>Metryki usługi Cosmos DB

Wszystkie klasyczne alerty w metrykach usługi Cosmos DB mogą być migrowane z wyjątkiem alertów dotyczących tych metryk:

- Średnia liczba żądań na sekundę
- Poziom spójności
- Http 2xx
- Http 3xx
- Http 400
- Http 401
- Błąd serwera wewnętrznego
- Max RUPM zużyte na minutę
- Maksymalna liczba procesorów na sekundę
- Mongo Count Nieudane żądania
- Mongo Usuń nieudane żądania
- Mongo Wstawianie nieudanych żądań
- Mongo Inne nieudane żądania
- Mongo Inne opłaty za żądanie
- Mongo Inne stawki za żądanie
- Żądania nieudane kwerendy mongo
- Żądania nieudanej aktualizacji mongo
- Obserwowane opóźnienie odczytu
- Obserwowane opóźnienie zapisu
- Dostępność usług
- Pojemność magazynu
- Ograniczone żądania
- Łączna liczba żądań

Średnie żądania na sekundę, poziom spójności, Maksymalna ilość RUPM zużywana na minutę, Maksymalne procesory RU na sekundę, Obserwowane opóźnienie odczytu, obserwowane opóźnienie zapisu, pojemność magazynu nie są obecnie dostępne w [nowym systemie](metrics-supported.md#microsoftdocumentdbdatabaseaccounts).

Alerty dotyczące metryk żądań, takich jak Http 2xx, Http 3xx, Http 400, Http 401, Wewnętrzny błąd serwera, dostępność usługi, ograniczone żądania i całkowita liczba żądań nie są migrowane, ponieważ sposób zliczania żądań różni się między klasycznymi metrykami a nowymi metrykami. Alerty na te będą musiały być ręcznie odtworzone z progami dostosowane.

Alerty na metryki Mongo Failed Requests muszą być podzielone na wiele alertów, ponieważ nie ma połączone metryki, która zapewnia taką samą funkcjonalność. Progi będą musiały zostać odpowiednio dostosowane.

### <a name="classic-compute-metrics"></a>Klasyczne metryki obliczeniowe

Wszelkie alerty dotyczące klasycznych metryk obliczeniowych nie będą migrowane przy użyciu narzędzia migracji, ponieważ klasyczne zasoby obliczeniowe nie są jeszcze obsługiwane za pomocą nowych alertów. Obsługa nowych alertów dotyczących tych typów zasobów zostanie dodana w przyszłości. Gdy ta funkcja jest dostępna, klienci muszą ponownie utworzyć nowe równoważne reguły alertów na podstawie klasycznych reguł alertów przed czerwcem 2020 r.

### <a name="classic-alert-rules-on-deprecated-metrics"></a>Klasyczne reguły alertów dotyczące przestarzałych metryk

Są to klasyczne reguły alertów dotyczące metryk, które były wcześniej obsługiwane, ale ostatecznie zostały przestarzałe. Niewielki procent klientów może mieć nieprawidłowe klasyczne reguły alertów dla takich metryk. Ponieważ te reguły alertów są nieprawidłowe, nie zostaną zmigrowane.

| Typ zasobu| Przestarzałe metryki |
|-------------|----------------- |
| Microsoft.DBforMySQL/servers | compute_consumption_percent, compute_limit |
| Microsoft.DBforPostgreSQL/servers | compute_consumption_percent, compute_limit |
| Microsoft.Network/publicIPAddresses | domyślna szybkość |
| Microsoft.SQL/serwery/bazy danych | service_level_objective, storage_limit, storage_used, dławienie, dtu_consumption_percent, storage_used |
| Microsoft.Web/hostingŚrodowiska/multirolepools | średniazapraca |
| Microsoft.Web/hostingUrody/puly pracowników | bytesreceived, httpqueuelength |

## <a name="how-equivalent-new-alert-rules-and-action-groups-are-created"></a>Jak tworzone są równoważne nowe reguły alertów i grupy akcji

Narzędzie do migracji konwertuje klasyczne reguły alertów na równoważne nowe reguły alertów i grupy akcji. W przypadku większości klasycznych reguł alertów równoważne nowe reguły alertów są dostępne na tej samej metryki z tymi samymi właściwościami, takimi jak `windowSize` i `aggregationType`. Istnieją jednak pewne klasyczne reguły alertów są na metryki, które mają inną, równoważną metrykę w nowym systemie. Do migracji alertów klasycznych stosuje się następujące zasady, chyba że określono to w poniższej sekcji:

- **Częstotliwość:** Określa, jak często klasyczna lub nowa reguła alertu sprawdza stan. W `frequency` klasycznych reguł alertów nie był konfigurowalny przez użytkownika i zawsze 5 minut dla wszystkich typów zasobów z wyjątkiem składników usługi Application Insights, dla których było 1 min. Tak więc częstotliwość równoważnych reguł jest również ustawiona odpowiednio na 5 min i 1 min.
- **Typ agregacji:** Określa sposób agregowania metryki w oknie zainteresowania. Jest `aggregationType` również taka sama między alertami klasycznymi i nowymi alertami dla większości metryk. W niektórych przypadkach, ponieważ metryka różni się między `aggregationType` alertami `primary Aggregation Type` klasycznymi i nowymi alertami, używana jest równoważna lub zdefiniowana dla metryki.
- **Jednostki:** Właściwość metryki, na której tworzony jest alert. Niektóre równoważne dane mają różne jednostki. Próg jest odpowiednio dostosowywany w razie potrzeby. Na przykład jeśli oryginalna metryka ma sekund jako jednostki, ale równoważne nowe metryki ma miliSekundy jako jednostki, oryginalny próg jest mnożony przez 1000, aby zapewnić takie samo zachowanie.
- **Rozmiar okna:** Definiuje okno, przez które dane metryki są agregowane w celu porównania z wartością progową. Dla `windowSize` standardowych wartości, takich jak 5mins, 15mins, 30mins, 1hour, 3hours, 6 godzin, 12 godzin, 1 dzień, nie ma zmian dla równoważnej nowej reguły alertu. W przypadku innych wartości `windowSize` jest wybierany najbliższy do użycia. Dla większości klientów, nie ma wpływu na tę zmianę. Dla niewielkiego odsetka klientów może być konieczne dostosowanie progu, aby uzyskać dokładnie takie samo zachowanie.

W poniższych sekcjach możemy szczegółowo metryki, które mają inną, równoważne metryki w nowym systemie. Nie ma żadnych metryk, które pozostają takie same dla klasycznych i nowych reguł alertów. Listę metryk obsługiwanych w nowym systemie można znaleźć [tutaj](metrics-supported.md).

### <a name="microsoftstorageaccountsservices"></a>Microsoft.StorageKonta/usługi

W przypadku usług konta magazynu, takich jak obiekt blob, tabela, plik i kolejka, następujące metryki są mapowane na równoważne metryki, jak pokazano poniżej:

| Metryka w klasycznych alertach | Równoważna metryka w nowych alertach | Komentarze|
|--------------------------|---------------------------------|---------|
| AnonimowaAutoryzacjaError| Metryka transakcji z wymiarami "ResponseType"="AuthorizationError" i "Authentication" = "Anonimowy"| |
| AnonymousClientOtherError | Metryka transakcji z wymiarami "ResponseType"="ClientOtherError" i "Authentication" = "Anonimowy" | |
| AnonymousClientTimeOutError| Metryka transakcji z wymiarami "ResponseType"="ClientTimeOutError" i "Authentication" = "Anonimowy" | |
| AnonymousNetworkError | Metryka transakcji z wymiarami "ResponseType"="NetworkError" i "Authentication" = "Anonimowy" | |
| AnonimowyServerOtherError | Metryka transakcji z wymiarami "ResponseType"="ServerOtherError" i "Authentication" = "Anonimowy" | |
| AnonymousServerTimeOutError | Metryka transakcji z wymiarami "ResponseType"="ServerTimeOutError" i "Authentication" = "Anonimowy" | |
| AnonimSukces | Metryka transakcji z wymiarami "ResponseType"="Sukces" i "Uwierzytelnianie" = "Anonimowy" | |
| AutoryzacjaEror | Metryka transakcji z wymiarami "ResponseType"="AuthorizationError" | |
| AverageE2ELatency | SuccessE2ELatency | |
| AverageServerLatency | SuccessServerLatency | |
| Pojemność | BlobCapacity (BlobCapacity) | Użyj `aggregationType` "średnia" zamiast "ostatni". Metryka dotyczy tylko usług obiektów Blob |
| KlientOtherError | Metryka transakcji z wymiarami "ResponseType"="ClientOtherError"  | |
| KlientTimeoutError | Metryka transakcji z wymiarami "ResponseType"="ClientTimeOutError" | |
| Liczba kontenerów | Liczba kontenerów | Użyj `aggregationType` "średnia" zamiast "ostatni". Metryka dotyczy tylko usług obiektów Blob |
| NetworkError | Metryka transakcji z wymiarami "ResponseType"="NetworkError" | |
| Liczba obiektów | Liczba obiektów BlobCount| Użyj `aggregationType` "średnia" zamiast "ostatni". Metryka dotyczy tylko usług obiektów Blob |
| SasAutoryzacjaError | Metryka transakcji z wymiarami "ResponseType"="AuthorizationError" i "Authentication" = "SAS" | |
| SASClientOtherError | Metryka transakcji z wymiarami "ResponseType"="ClientOtherError" i "Authentication" = "SAS" | |
| SasClientTimeOutError | Metryka transakcji z wymiarami "ResponseType"="ClientTimeOutError" i "Authentication" = "SAS" | |
| SASNetworkError | Metryka transakcji z wymiarami "ResponseType"="NetworkError" i "Authentication" = "SAS" | |
| SASServerOtherError | Metryka transakcji z wymiarami "ResponseType"="ServerOtherError" i "Authentication" = "SAS" | |
| SasServerTimeOutError | Metryka transakcji z wymiarami "ResponseType"="ServerTimeOutError" i "Authentication" = "SAS" | |
| Sukces SASSuccess | Metryka transakcji z wymiarami "ResponseType"="Sukces" i "Uwierzytelnianie" = "SAS" | |
| SerwerOtherError | Metryka transakcji z wymiarami "ResponseType"="ServerOtherError" | |
| ServerTimeOutError | Metryka transakcji z wymiarami "ResponseType"="ServerTimeOutError"  | |
| Powodzenie | Metryka transakcji z wymiarami "ResponseType"="Sukces" | |
| TotalBillableRequests (Liczba zaliczek na zobowiązania do)| Transakcje | |
| TotalEgress | Ruch wychodzący | |
| TotalIngress | Ruch przychodzący | |
| TotalRequests | Transakcje | |

### <a name="microsoftinsightscomponents"></a>Microsoft.insights/components

W przypadku usługi Application Insights równoważne dane są takie, jak pokazano poniżej:

| Metryka w klasycznych alertach | Równoważna metryka w nowych alertach | Komentarze|
|--------------------------|---------------------------------|---------|
| availability.availabilityMetric.value | dostępnośćWyniki/dostępnośćPercentage|   |
| availability.durationMetric.value | dostępnośćWyniki/czas trwania| Pomnóż oryginalny próg przez 1000, ponieważ jednostki dla metryki klasycznej są w sekundach, a dla nowych są w miliSekundach.  |
| basicExceptionBrowser.count | wyjątki/przeglądarka|  Użyj `aggregationType` "count" zamiast "suma". |
| basicExceptionServer.count | wyjątki/serwer| Użyj `aggregationType` "count" zamiast "suma".  |
| clientPerformance.clientProcess.value | przeglądarkaTimings/processingDuration| Pomnóż oryginalny próg przez 1000, ponieważ jednostki dla metryki klasycznej są w sekundach, a dla nowych są w miliSekundach.  |
| clientPerformance.networkConnection.value | przeglądarkaTimings/networkDuration|  Pomnóż oryginalny próg przez 1000, ponieważ jednostki dla metryki klasycznej są w sekundach, a dla nowych są w miliSekundach. |
| clientPerformance.receiveRequest.value | przeglądarkaTimings/receiveDuration| Pomnóż oryginalny próg przez 1000, ponieważ jednostki dla metryki klasycznej są w sekundach, a dla nowych są w miliSekundach.  |
| clientPerformance.sendRequest.value | przeglądarkaTimings/sendDuration| Pomnóż oryginalny próg przez 1000, ponieważ jednostki dla metryki klasycznej są w sekundach, a dla nowych są w miliSekundach.  |
| clientPerformance.total.value | przeglądarkaTimings/totalDuration| Pomnóż oryginalny próg przez 1000, ponieważ jednostki dla metryki klasycznej są w sekundach, a dla nowych są w miliSekundach.  |
| performanceCounter.available_bytes.wartość | performanceCounters/memoryDostępne bajty|   |
| performanceCounter.io_data_bytes_per_sec.wartość | performanceCounters/processIOBytesPerSekund|   |
| performanceCounter.number_of_exceps_thrown_per_sec.wartość | performanceCounters/exceptionsPerSekund|   |
| performanceCounter.percentage_processor_time_normalized.wartość | performanceCounters/processCpuPercentage|   |
| performanceCounter.percentage_processor_time.wartość | performanceCounters/processCpuPercentage| Próg będzie musiał zostać odpowiednio zmodyfikowany, ponieważ oryginalna metryka była we wszystkich rdzeniach, a nowa metryka jest znormalizowana do jednego rdzenia. Narzędzie migracji nie zmienia progów.  |
| performanceCounter.percentage_processor_total.wartość | performanceCounters/processorCpuPercentage|   |
| performanceCounter.process_private_bytes.wartość | performanceCounters/processPrivateBytes|   |
| performanceCounter.request_execution_time.wartość | performanceCounters/requestExecutionTime|   |
| performanceCounter.requests_in_application_queue.wartość | performanceCounters/requestsInQueue|   |
| performanceCounter.requests_per_sec.wartość | performanceCounters/requestsPerSekund|   |
| request.duration | żądania/czas trwania| Pomnóż oryginalny próg przez 1000, ponieważ jednostki dla metryki klasycznej są w sekundach, a dla nowych są w miliSekundach.  |
| wniosek.rate | żądania/stawka|   |
| requestFailed.count (Liczba).. | żądania/niepowodzenie| Użyj `aggregationType` "count" zamiast "suma".   |
| view.count | odsłony/liczba| Użyj `aggregationType` "count" zamiast "suma".   |

### <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseKonta kont

W przypadku usługi Cosmos DB równoważne dane są jak pokazano poniżej:

| Metryka w klasycznych alertach | Równoważna metryka w nowych alertach | Komentarze|
|--------------------------|---------------------------------|---------|
| DostępneStorage     |DostępneStorage|   |
| Rozmiar danych | DataUsage (DataUsage)| |
| Liczba dokumentów | Liczba dokumentów||
| Rozmiar indeksu | IndeksUsage||
| Opłata za żądanie liczby mongo| MongoRequestCharge z wymiarem "CommandName" = "count"||
| Mongo Count Cena żądania | MongoRequestsCount z wymiarem "CommandName" = "count"||
| Mongo Usuń żądanie charge | MongoRequestCharge z wymiarem "CommandName" = "delete"||
| Mongo Delete Cena żądania | MongoRequestsCount z wymiarem "CommandName" = "delete"||
| Mongo Insert Opłata za żądanie wstawiania | MongoRequestCharge z wymiarem "CommandName" = "insert"||
| Mongo Wstawić cena żądania | MongoRequestsCount z wymiarem "CommandName" = "insert"||
| Opłata za żądanie zapytania Mongo | MongoRequestCharge z wymiarem "CommandName" = "find"||
| Szybkość żądania kwerendy Mongo | MongoRequestsCount z wymiarem "CommandName" = "find"||
| Opłata za żądanie aktualizacji Mongo | MongoRequestCharge z wymiarem "CommandName" = "update"||
| Usługa niedostępna| Dostępność usług||
| Jednostki TotalRequestUnits | Jednostki TotalRequestUnits||

### <a name="how-equivalent-action-groups-are-created"></a>Jak tworzone są równoważne grupy akcji

Klasyczne reguły alertów miały akcje poczty e-mail, webhook, aplikacji logiki i elementu runbook powiązane z samą regułą alertu. Nowe reguły alertów używają grup akcji, które mogą być ponownie używane w wielu regułach alertów. Narzędzie migracji tworzy pojedynczą grupę akcji dla tych samych akcji, niezależnie od liczby reguł alertów korzystających z akcji. Grupy akcji utworzone przez narzędzie migracji używają formatu nazewnictwa "Migrated_AG*".

> [!NOTE]
> Klasyczne alerty wysyłane zlokalizowane wiadomości e-mail na podstawie ustawień regionalnych administratora klasycznego, gdy są używane do powiadamiania klasycznych ról administratora. Nowe wiadomości e-mail z alertami są wysyłane za pośrednictwem grup akcji i są tylko w języku angielskim.

## <a name="rollout-phases"></a>Etapy wdrażania

Narzędzie migracji jest wdrażane etapami dla klientów korzystających z klasycznych reguł alertów. Właściciele subskrypcji otrzymają wiadomość e-mail, gdy subskrypcja jest gotowa do migracji za pomocą narzędzia.

> [!NOTE]
> Ponieważ narzędzie jest wdrażane w fazach, może się okazać, że niektóre subskrypcje nie są jeszcze gotowe do migracji we wczesnych fazach.

Większość subskrypcji są obecnie oznaczone jako gotowe do migracji. Tylko subskrypcje, które mają klasyczne alerty dotyczące następujących typów zasobów, nadal nie są gotowe do migracji.

- Microsoft.classicCompute/domainNames/slots/roles
- Microsoft.insights/components

## <a name="who-can-trigger-the-migration"></a>Kto może wywołać migrację?

Każdy użytkownik, który ma wbudowaną rolę monitorowania współautora na poziomie subskrypcji może wyzwolić migracji. Użytkownicy, którzy mają rolę niestandardową z następującymi uprawnieniami, mogą również wyzwolić migrację:

- */odczyt
- Microsoft.Insights/actiongroups/*
- Microsoft.Insights/AlertRules/*
- Microsoft.Insights/metricAlerts/*
- Microsoft.AlertsManagement/smartDetectorAlertRules/*

> [!NOTE]
> Oprócz posiadania powyższych uprawnień subskrypcja powinna być dodatkowo zarejestrowana u dostawcy zasobów Microsoft.AlertsManagement. Jest to wymagane do pomyślnej migracji alertów anomalii awarii w usłudze Application Insights. 

## <a name="common-problems-and-remedies"></a>Typowe problemy i środki zaradcze

Po [uruchomieniu migracji](alerts-using-migration-tool.md)otrzymasz wiadomość e-mail pod podane adresy z powiadomieniem o zakończeniu migracji lub o konieczności podjęcia jakichkolwiek działań. W tej sekcji opisano niektóre typowe problemy i sposób radzenia sobie z nimi.

### <a name="validation-failed"></a>Sprawdzanie poprawności nie powiodło się

Ze względu na niektóre ostatnie zmiany w klasycznych reguł alertów w subskrypcji subskrypcji nie można przeprowadzić migracji. Ten problem jest tymczasowy. Migrację można ponownie uruchomić po przesunie się stanu migracji **Gotowy do migracji** w ciągu kilku dni.

### <a name="scope-lock-preventing-us-from-migrating-your-rules"></a>Blokada zakresu uniemożliwiająca nam migrację reguł

W ramach migracji zostaną utworzone nowe alerty metryk i nowe grupy akcji, a następnie zostaną usunięte klasyczne reguły alertów. Jednak blokada zakresu może uniemożliwić nam tworzenie lub usuwanie zasobów. W zależności od blokady zakresu nie można migrować niektórych lub wszystkich reguł. Ten problem można rozwiązać, usuwając blokadę zakresu subskrypcji, grupy zasobów lub zasobu, która jest wymieniona w [narzędziu migracji,](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel)i ponownie wyzwalając migrację. Blokady zakresu nie można wyłączyć i musi zostać usunięty na czas trwania procesu migracji. [Dowiedz się więcej o zarządzaniu blokadami zakresu](../../azure-resource-manager/management/lock-resources.md#portal).

### <a name="policy-with-deny-effect-preventing-us-from-migrating-your-rules"></a>Polityka z efektem "Odmów" uniemożliwiająca nam migrację twoich reguł

W ramach migracji zostaną utworzone nowe alerty metryk i nowe grupy akcji, a następnie zostaną usunięte klasyczne reguły alertów. Jednak polityka może uniemożliwić nam tworzenie zasobów. W zależności od zasad nie można migrować niektórych lub wszystkich reguł. Zasady blokujące ten proces są wymienione w [narzędziu migracji](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel). Rozwiąż ten problem, aby:Resolve this problem by either:

- Wykluczając subskrypcje lub grupy zasobów na czas trwania procesu migracji z przypisania zasad. [Dowiedz się więcej o zarządzaniu zakresem wykluczania zasad](../../governance/policy/tutorials/create-and-manage.md#exempt-a-non-compliant-or-denied-resource-using-exclusion).
- Usunięcie lub zmiana efektu na "inspekcja" lub "dołączenie" (które na przykład mogą rozwiązać problemy związane z brakującymi tagami). [Dowiedz się więcej o zarządzaniu efektem zasad](../../governance/policy/concepts/definition-structure.md#policy-rule).

## <a name="next-steps"></a>Następne kroki

- [Jak korzystać z narzędzia do migracji](alerts-using-migration-tool.md)
- [Przygotowywanie do migracji](alerts-prepare-migration.md)
