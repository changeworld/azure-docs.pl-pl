---
title: Usługa Azure IoT Hub wysoka dostępność i odzyskiwanie po awarii | Dokumenty firmy Microsoft
description: W tym artykule opisano funkcje platformy Azure i usługi IoT Hub, które ułatwią tworzenie wysoce dostępnych rozwiązań Azure IoT z możliwościami odzyskiwania po awarii.
author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/17/2020
ms.author: philmea
ms.openlocfilehash: 615dc1b7bd1a31069a542ebb7ea44693c404cb40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79499100"
---
# <a name="iot-hub-high-availability-and-disaster-recovery"></a>Wysoka dostępność i odzyskiwanie po awarii usługi IoT Hub

Jako pierwszy krok w kierunku wdrożenia elastycznego rozwiązania IoT, architekci, deweloperzy i właściciele firm muszą określić cele dotyczące czasu pracy bez przestojów dla budujemy rozwiązania. Cele te można zdefiniować przede wszystkim na podstawie konkretnych celów biznesowych dla każdego scenariusza. W tym kontekście w artykule [Wskazówki techniczne dotyczące ciągłości biznesowej platformy Azure](https://docs.microsoft.com/azure/architecture/resiliency/) opisano ogólne ramy ułatwiające myślenie o ciągłości działania i odzyskiwania po awarii. Odzyskiwanie [po awarii i wysoka dostępność dla aplikacji platformy Azure](https://docs.microsoft.com/azure/architecture/reliability/disaster-recovery) papieru zawiera wskazówki dotyczące architektury dla aplikacji platformy Azure w celu osiągnięcia wysokiej dostępności (HA) i odzyskiwania po awarii (DR).

W tym artykule omówiono funkcje wysokiej obsługi i odzyskiwania po awarii oferowane specjalnie przez usługę IoT Hub. Szerokie obszary omówione w tym artykule to:

- Ha wewnątrzregionu
- Dr regionu krzyżowego
- Osiągnięcie regionu krzyżowego HA

W zależności od celów dotyczących czasu pracy, które definiujesz dla rozwiązań IoT, należy określić, które z opcji opisanych poniżej najlepiej odpowiadają twoim celom biznesowym. Włączenie dowolnej z tych rozwiązań HA/DR do rozwiązania IoT wymaga starannej oceny kompromisów między:

- Wymagany poziom odporności 
- Złożoność wdrażania i konserwacji
- Wpływ COGS

## <a name="intra-region-ha"></a>Ha wewnątrzregionu

Usługa IoT Hub zapewnia wewnątrzregionową usługę HA, implementując nadmiarowości w prawie wszystkich warstwach usługi. [Umowy SLA opublikowane przez usługę IoT Hub](https://azure.microsoft.com/support/legal/sla/iot-hub) uzyskuje się poprzez wykorzystanie tych zwolnień. Deweloperzy rozwiązania IoT nie są wymagane żadne dodatkowe prace, aby skorzystać z tych funkcji wysokiej informacji. Mimo że centrum IoT hub oferuje stosunkowo wysoką gwarancję dostępności, można nadal oczekiwać przejściowych awarii, jak w przypadku każdej rozproszonej platformy obliczeniowej. Jeśli dopiero zaczynasz migrować rozwiązania do chmury z rozwiązania lokalnego, należy skupić się na przejściu z optymalizacji "średniego czasu między awariami" na "średni czas odzyskania". Innymi słowy, błędy przejściowe należy uznać za normalne podczas pracy z chmurą w mieszance. Odpowiednie [zasady ponawiania](iot-hub-reliability-features-in-sdks.md) próby muszą być wbudowane w składniki interakcji z aplikacją w chmurze, aby poradzić sobie z przejściowymi awariami.

> [!NOTE]
> Niektóre usługi platformy Azure zapewniają również dodatkowe warstwy dostępności w regionie, integrując się ze [strefami dostępności (AZs).](../availability-zones/az-overview.md) AZs obecnie nie są obsługiwane przez usługę Centrum IoT.

## <a name="cross-region-dr"></a>Dr regionu krzyżowego

Może wystąpić kilka rzadkich sytuacji, gdy centrum danych doświadcza rozszerzone awarie z powodu awarii zasilania lub innych awarii obejmujących zasoby fizyczne. Takie zdarzenia są rzadkie, podczas których opisana powyżej funkcja ha wewnątrz regionu może nie zawsze pomóc. Centrum IoT hub udostępnia wiele rozwiązań do odzyskiwania po takich awariach rozszerzonych. 

Opcje odzyskiwania dostępne dla klientów w takiej sytuacji to [zainicjowana przez firmę Microsoft praca awaryjna](#microsoft-initiated-failover) i [ręczne tryb failover.](#manual-failover) Zasadnicza różnica między nimi polega na tym, że Microsoft inicjuje te pierwsze, a użytkownik inicjuje ten ostatni. Ponadto ręczna praca awaryjna zapewnia cel czas odzyskiwania (RTO) w porównaniu z opcją trybu failover inicjowanego przez firmę Microsoft. Konkretne RTO oferowane z każdą opcją są omówione w poniższych sekcjach. Gdy ta opcja wykonywania pracy awaryjnej centrum IoT hubu z jego regionu podstawowego jest wykonywana, centrum staje się w pełni funkcjonalne w odpowiednim [regionie platformy Azure z parą geograficzną.](../best-practices-availability-paired-regions.md)

Obie te opcje pracy awaryjnej oferują następujące cele punktu odzyskiwania (RPO):

| Typ danych | Cele punktu odzyskiwania (RPO) |
| --- | --- |
| Rejestr tożsamości |0-5 min utraty danych |
| Dane bliźniaczej reprezentacji urządzenia |0-5 min utraty danych |
| Komunikaty z chmury do urządzenia<sup>1</sup> |0-5 min utraty danych |
| Zadania nadrzędne<sup>1</sup> i urządzenia |0-5 min utraty danych |
| Komunikaty z urządzenia do chmury |Wszystkie nieprzeczytane wiadomości zostaną utracone |
| Komunikaty monitorowania operacji |Wszystkie nieprzeczytane wiadomości zostaną utracone |
| Komunikaty zwrotne z chmury do urządzenia |Wszystkie nieprzeczytane wiadomości zostaną utracone |

<sup>1.</sup> Komunikaty między chmurami i zadania nadrzędne nie są odzyskiwane w ramach ręcznego trybu failover.

Po zakończeniu pracy awaryjnej dla centrum IoT oczekuje się, że wszystkie operacje z urządzenia i aplikacji zaplecza będą kontynuować pracę bez konieczności ręcznej interwencji. Oznacza to, że komunikaty z urządzenia do chmury powinny nadal działać, a cały rejestr urządzeń jest nienaruszony. Zdarzenia emitowane za pośrednictwem usługi Event Grid mogą być używane za pośrednictwem tych samych subskrypcji skonfigurowanych wcześniej, o ile te subskrypcje usługi Event Grid są nadal dostępne.

> [!CAUTION]
> - Nazwa i punkt końcowy wbudowanego punktu końcowego zdarzeń usługi IoT Hub są zmieniane po przebaczeniu awaryjnego i skonfigurowane grupy konsumentów (jest to błąd, który zostanie rozwiązany przed majem 2020 r.). Podczas odbierania komunikatów telemetrycznych z wbudowanego punktu końcowego przy użyciu klienta usługi Event Hub lub hosta procesora zdarzeń, należy [użyć ciągu połączenia centrum IoT](iot-hub-devguide-messages-read-builtin.md#read-from-the-built-in-endpoint) do ustanowienia połączenia. Dzięki temu aplikacje zaplecza będą nadal działać bez konieczności ręcznej interwencji po pracy awaryjnej. Jeśli używasz nazwy i punktu końcowego zgodnego z Centrum zdarzeń bezpośrednio w aplikacji, należy [ponownie skonfigurować grupę konsumentów, której używają i pobrać nowy punkt końcowy zgodny z Centrum zdarzeń](iot-hub-devguide-messages-read-builtin.md#read-from-the-built-in-endpoint) po pracy awaryjnej, aby kontynuować operacje. Jeśli używasz usługi Azure Functions lub Azure Stream Analytics do połączenia wbudowanego punktu końcowego, może być konieczne wykonanie **ponownego uruchomienia.**
>
> - Podczas routingu do magazynu, zaleca się listę obiektów blobs lub plików, a następnie iteracji nad nimi, aby upewnić się, że wszystkie obiekty blobs lub pliki są odczytywane bez podejmowania żadnych założeń partycji. Zakres partycji może potencjalnie ulec zmianie podczas pracy awaryjnej zainicjowanej przez firmę Microsoft lub ręcznego trybu failover. Za pomocą [interfejsu API obiektów blob listy](https://docs.microsoft.com/rest/api/storageservices/list-blobs) można wyliczyć listę obiektów blob lub [listę interfejsu API ADLS Gen2](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/path/list) dla listy plików. 

## <a name="microsoft-initiated-failover"></a>Przewijane przez firmę Microsoft w stanie failover

Zainicjowana przez firmę Microsoft usługa failover jest wykonywana przez firmę Microsoft w rzadkich sytuacjach, aby przemortywać wszystkie centra IoT z regionu, którego dotyczy problem, do odpowiedniego regionu z parą geograficzną. Ten proces jest opcją domyślną (nie ma możliwości rezygnacji użytkowników) i nie wymaga interwencji ze strony użytkownika. Firma Microsoft zastrzega sobie prawo do ustalenia, kiedy ta opcja zostanie skorzystania. Mechanizm ten nie obejmuje zgody użytkownika przed przełączeniem koncentratora użytkownika w stan failed. Zainicjowana przez firmę Microsoft usługa trybu failover ma cel czasu odzyskiwania (RTO) 2-26 godzin. 

Duży rto jest, ponieważ firma Microsoft musi wykonać operację pracy awaryjnej w imieniu wszystkich klientów, których dotyczy problem w tym regionie. Jeśli korzystasz z mniej krytycznego rozwiązania IoT, które może wytrzymać przestoje około jednego dnia, można wziąć zależność od tej opcji, aby spełnić ogólne cele odzyskiwania po awarii dla rozwiązania IoT. Całkowity czas dla operacji środowiska uruchomieniowego, aby stać się w pełni operacyjne po wyzwoleniu tego procesu, jest opisany w sekcji "Czas do odzyskania" sekcji.

## <a name="manual-failover"></a>Ręczne przełączenie w tryb failover

Jeśli cele dotyczące czasu pracy bez przestojów firmy nie są spełnione przez cel programu RTO, który zapewnia zainicjowany przez firmę Microsoft w trybie failover, należy rozważyć użycie ręcznego trybu failover do samodzielnego wyzwolenia procesu pracy awaryjnej. Czas wymagania za pomocą tej opcji może wynosić od 10 minut do kilku godzin. RTO jest obecnie funkcją liczby urządzeń zarejestrowanych w wystąpieniu centrum IoT, które jest przejęte awaryjnie. Możesz oczekiwać, że RTO dla koncentratora obsługującego około 100 000 urządzeń będzie w parku balowym 15 minut. Całkowity czas dla operacji środowiska uruchomieniowego, aby stać się w pełni operacyjne po wyzwoleniu tego procesu, jest opisany w sekcji "Czas do odzyskania" sekcji.

Opcja ręcznego trybu failover jest zawsze dostępna do użycia niezależnie od tego, czy w regionie podstawowym występują przestoje, czy nie. W związku z tym ta opcja może potencjalnie służyć do wykonywania planowanych pracy awaryjnej. Jednym z przykładów użycia planowanych pracy awaryjnej jest wykonywanie okresowych ćwiczeń pracy awaryjnej. Należy jednak zachować ostrożność, że planowana operacja pracy awaryjnej powoduje przestoje dla koncentratora dla okresu zdefiniowanego przez obiekt RTO dla tej opcji, a także powoduje utratę danych zgodnie z definicją w powyższej tabeli RPO. Można rozważyć skonfigurowanie wystąpienia centrum IoT testowego, aby okresowo wykonywać planowaną opcję pracy awaryjnej, aby zdobyć zaufanie do możliwości uruchamiania kompleksowych rozwiązań w przypadku wystąpienia prawdziwej awarii.

Aby uzyskać instrukcje krok po kroku, zobacz [Samouczek: Wykonywanie ręcznego trybu failover dla centrum IoT](tutorial-manual-failover.md)

### <a name="running-test-drills"></a>Uruchamianie ćwiczeń testowych

Ćwiczenia testowe nie powinny być wykonywane na centrach IoT, które są używane w środowiskach produkcyjnych.

### <a name="dont-use-manual-failover-to-migrate-iot-hub-to-a-different-region"></a>Nie używaj ręcznego trybu failover do migracji centrum IoT do innego regionu

Ręczne tryb failover *nie* powinny być używane jako mechanizm do trwałej migracji centrum między regionami parowania geograficznego platformy Azure. W ten sposób zwiększa opóźnienie dla operacji wykonywanych w centrum IoT hub z urządzeń homed w starym regionie podstawowym.

## <a name="failback"></a>Powrót po awarii

Niepowodzenie z powrotem do starego regionu podstawowego można osiągnąć, wyzwalając akcję trybu failover innym razem. Jeśli oryginalna operacja pracy awaryjnej została wykonana w celu odzyskania danych z rozszerzonej awarii w oryginalnym regionie podstawowym, zaleca się, że koncentrator powinien zostać powiódł się z powrotem do oryginalnej lokalizacji, gdy ta lokalizacja odzyska z sytuacji awarii.

> [!IMPORTANT]
> - Użytkownicy mogą wykonywać tylko 2 pomyślne operacje pracy awaryjnej i 2 pomyślne operacje powrotu po awarii dziennie.
>
> - Powrót do tyłu operacji pracy awaryjnej/powrotu po awarii nie są dozwolone. Należy odczekać 1 godzinę między tymi operacjami.

## <a name="time-to-recover"></a>Czas na regenerację

Podczas gdy FQDN (i w związku z tym parametry połączenia) wystąpienia centrum IoT pozostaje tym samym post pracy awaryjnej, podstawowy adres IP zmienia. W związku z tym ogólny czas wykonywania operacji środowiska uruchomieniowego względem wystąpienia centrum IoT, aby stać się w pełni operacyjny po wyzwoleniu procesu pracy awaryjnej można wyrazić za pomocą następującej funkcji.

Czas odzyskania = RTO [10 min - 2 godziny dla ręcznego trybu failover | 2 - 26 godzin dla pracy awaryjnej zainicjowanej przez firmę Microsoft] + opóźnienie propagacji DNS + czas wykonany przez aplikację kliencką w celu odświeżenia dowolnego buforowanego adresu IP centrum IoT Hub.

> [!IMPORTANT]
> ZestawY SDK IoT nie buforują adresu IP centrum IoT Hub. Zaleca się, że kod użytkownika współtworzenie z zestawami SDK nie należy buforować adres IP centrum IoT hub.

## <a name="achieve-cross-region-ha"></a>Osiągnij obszar krzyżowy HA

Jeśli cele dotyczące czasu pracy bez przestojów firmy nie są spełnione przez cel programu RTO, który zapewnia zainicjowane przez firmę Microsoft w trybie failover lub ręczne opcje pracy awaryjnej, należy rozważyć wdrożenie mechanizmu automatycznego trybu failover dla urządzeń.
Pełne leczenie topologii wdrażania w rozwiązaniach IoT wykracza poza zakres tego artykułu. W tym artykule omówiono regionalny model wdrażania *pracy awaryjnej* w celu wysokiej dostępności i odzyskiwania po awarii.

W regionalnym modelu pracy awaryjnej zaplecza rozwiązania działa głównie w jednej lokalizacji centrum danych. Pomocnicze centrum IoT i zaplecza są wdrażane w innej lokalizacji centrum danych. Jeśli centrum IoT w regionie podstawowym cierpi na awarię lub łączność sieciowa z urządzenia do regionu podstawowego zostanie przerwana, urządzenia używają pomocniczego punktu końcowego usługi. Można poprawić dostępność rozwiązania, implementując model trybu failover między regionami zamiast pozostawania w jednym regionie. 

Na wysokim poziomie, aby zaimplementować regionalny model pracy awaryjnej z Usługą IoT Hub, należy wykonać następujące kroki:

* **Pomocnicza logika routingu i routingu IoT hubu i urządzenia:** jeśli usługa w regionie podstawowym jest zakłócona, urządzenia muszą rozpocząć łączenie się z regionem pomocniczym. Biorąc pod uwagę stan świadomy charakteru większości zaangażowanych usług, jest często dla administratorów rozwiązań do wyzwalania między regionem procesu pracy awaryjnej. Najlepszym sposobem komunikowania nowego punktu końcowego z urządzeniami, przy zachowaniu kontroli nad procesem, jest regularne sprawdzanie usługi *concierge* dla bieżącego aktywnego punktu końcowego. Usługa konsjerża może być aplikacją sieci web, która jest replikowana i utrzymywana w zasięgu ręki przy użyciu technik przekierowania DNS (na przykład przy użyciu [usługi Azure Traffic Manager).](../traffic-manager/traffic-manager-overview.md)

   > [!NOTE]
   > Usługa centrum IoT nie jest obsługiwanym typem punktu końcowego w usłudze Azure Traffic Manager. Zalecenie jest zintegrowanie proponowanej usługi concierge z usługi Azure Traffic Manager, dzięki czemu implementuje interfejs API sondy kondycji punktu końcowego.

* **Replikacja rejestru tożsamości:** Aby można było konfigurować, pomocniczy centrum IoT musi zawierać wszystkie tożsamości urządzeń, które mogą łączyć się z rozwiązaniem. Rozwiązanie należy zachować replikowane geograficznie kopie zapasowe tożsamości urządzeń i przekazać je do pomocniczego centrum IoT hub przed przełączeniem aktywnego punktu końcowego dla urządzeń. Funkcje eksportu tożsamości urządzenia usługi IoT Hub jest przydatne w tym kontekście. Aby uzyskać więcej informacji, zobacz [Przewodnik dla deweloperów usługi IoT Hub — rejestr tożsamości](iot-hub-devguide-identity-registry.md).

* **Logika scalania:** Gdy region podstawowy stanie się ponownie dostępny, wszystkie stany i dane utworzone w lokacji dodatkowej muszą zostać zmigrowane z powrotem do regionu podstawowego. Ten stan i dane odnoszą się głównie do tożsamości urządzenia i metadanych aplikacji, które muszą zostać scalone z podstawowym centrum IoT hub i innych magazynów specyficznych dla aplikacji w regionie podstawowym. 

Aby uprościć ten krok, należy użyć operacji idempotentne. Operacje idempotentne zminimalizować skutki uboczne z ewentualnego spójnego rozkładu zdarzeń i z duplikatów lub dostarczania zdarzeń poza kolejnością. Ponadto logika aplikacji powinna być zaprojektowana do tolerowania potencjalnych niespójności lub nieco nieaktualne stan. Taka sytuacja może wystąpić ze względu na dodatkowy czas potrzebny systemowi na leczenie w oparciu o cele punktu odzyskiwania (RPO).

## <a name="choose-the-right-hadr-option"></a>Wybierz odpowiednią opcję HA/DR

Oto podsumowanie opcji wysokiej lokalizacji/odzyskiwania po awarii przedstawionych w tym artykule, które mogą służyć jako ramka odniesienia, aby wybrać odpowiednią opcję, która działa dla rozwiązania.

| Opcja HA/DR | Rto | CEL PUNKTU ODZYSKIWANIA | Wymaga ręcznej interwencji? | Złożoność wdrożenia | Dodatkowy wpływ na koszty|
| --- | --- | --- | --- | --- | --- |
| Przewijane przez firmę Microsoft w stanie failover |2 - 26 godzin|Zapoznaj się z tabelą RPO powyżej|Nie|Brak|Brak|
| Ręczne przełączenie w tryb failover |10 min - 2 godziny|Zapoznaj się z tabelą RPO powyżej|Tak|Bardzo niska. Wystarczy wyzwolić tę operację z portalu.|Brak|
| Region krzyżowy HA |< 1 min.|Zależy od częstotliwości replikacji niestandardowego rozwiązania wysokiej dostawcy usługi radiowej|Nie|Wysoka|> 1x koszt 1 koncentratora IoT|

## <a name="next-steps"></a>Następne kroki

* [Co to jest usługa Azure IoT Hub?](about-iot-hub.md)
* [Wprowadzenie do usługi IoT Hubs (Szybki start)](quickstart-send-telemetry-dotnet.md)
* [Samouczek: Wykonywanie ręcznego trybu failover dla centrum IoT](tutorial-manual-failover.md)