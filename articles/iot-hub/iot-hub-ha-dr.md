---
title: Azure IoT Hub wysokiej dostępności i odzyskiwania po awarii | Dokumentacja firmy Microsoft
description: Opisano funkcje platformy Azure i usługi IoT Hub, które ułatwiają tworzenie wysoko dostępnych rozwiązań Azure IoT przy użyciu po awarii możliwości odzyskiwania.
author: rkmanda
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/07/2018
ms.author: rkmanda
ms.openlocfilehash: 04a3f4bbe1f0534d0eed88fbb8eb6ada4000a4f0
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2018
ms.locfileid: "39620403"
---
# <a name="iot-hub-high-availability-and-disaster-recovery"></a>Usługa IoT Hub o wysokiej dostępności i odzyskiwania po awarii
Jako pierwszy krok w kierunku implementacji IoT odporne na błędy rozwiązanie, architektów, deweloperzy i właściciele firm należy zdefiniować cele czasu rozwiązania, które jest tworzone. Tych celów można zdefiniować przede wszystkim oparte na cele biznesowe specyficzne dla każdego scenariusza. W tym kontekście, artykuł [wskazówek technicznych ciągłości biznesowej Azure](https://docs.microsoft.com/azure/architecture/resiliency/) opisano ogólne platformę, by pomóc myśleć o biznesowych ciągłości działania i odzyskiwania po awarii. [Odzyskiwania po awarii i wysoka dostępność dla aplikacji platformy Azure](https://msdn.microsoft.com/library/dn251004.aspx) dokument zawiera wskazówki dotyczące architektury, strategii dla aplikacji platformy Azure w celu zapewnienia wysokiej dostępności (HA) i odzyskiwania po awarii (DR).

W tym artykule omówiono funkcje wysokiej dostępności i odzyskiwania po awarii, oferowane specjalnie przez usługę IoT Hub. Dostępne są następujące obszary omówionych w tym artykule:

- Wewnątrz regionu wysokiej dostępności
- Między regionami odzyskiwania po awarii
- Obsługiwanie między regionami wysokiej dostępności

W zależności od celów czasu pracy zdefiniowanych dla rozwiązań IoT należy określić, które opcje opisane poniżej najlepsze sposób swoje cele biznesowe. Dołączanie dowolnego z tych możliwości wysokiej dostępności i odzyskiwania po awarii do swojego rozwiązania IoT wymaga dokładnej oceny kompromis między:
- Poziom odporności, które są wymagane 
- Złożoność implementacji i konserwacji
- Wpływ KWS


## <a name="intra-region-ha"></a>Wewnątrz regionu wysokiej dostępności
Usługi IoT Hub udostępnia wewnątrz regionu wysokiej dostępności poprzez implementację zwolnienia prawie wszystkich warstwach usługi. [SLA opublikowana przez usługę IoT Hub](https://azure.microsoft.com/support/legal/sla/iot-hub) jest osiągane poprzez użycie tych zwolnienia. Aby móc korzystać z tych funkcji wysokiej dostępności przez deweloperów rozwiązania IoT jest wymagane żadne działanie dodatkowe. Mimo że usługa IoT Hub jest objęta gwarancją jest wysoki czas dostępności, nadal można oczekiwać błędów przejściowych podobnie jak w przypadku dowolnego rozproszoną platformę obliczeniową. Jeśli po prostu rozpoczniesz pracę z Migrowanie rozwiązania do chmury z rozwiązania w środowisku lokalnym, zespół musi przesunięcie z optymalizowaniu "średniego czasu między awariami" "Średni czas do odzyskania". Innymi słowy przejściowe awarie mają być traktowane jako normalny podczas pracy z chmurą w zestawie. Odpowiednie [zasady ponawiania](iot-hub-reliability-features-in-sdks.md) musi być zbudowany w składnikach interakcji z aplikacji w chmurze do obsługi błędów przejściowych.

> [!NOTE]
> Niektóre usługi platformy Azure udostępniają dodatkowe warstwy dostępności w obrębie regionu dzięki integracji z usługą [strefach dostępności (AZs)](../availability-zones/az-overview.md). AZs nie są obecnie obsługiwane przez usługę IoT Hub.

## <a name="cross-region-dr"></a>Między regionami odzyskiwania po awarii
Może istnieć kilka rzadkich sytuacji, gdy centrum danych dochodzi rozszerzonej z powodu awarii zasilania lub inne błędy, obejmujące zasoby fizyczne. Takie zdarzenia występują rzadko, podczas którego regionu wewnątrz funkcji wysokiej dostępności opisanych powyżej może nie zawsze pomocy. IoT Hub udostępnia wiele rozwiązań do odzyskiwania z takich dłuższych przestojów. Dostępne dla klientów w takiej sytuacji opcje odzyskiwania są ""inicjowane firmy Microsoft Praca awaryjna i "Ręczna praca awaryjna". Podstawowa różnica między tymi dwoma jest firma Microsoft zainicjuje poprzednie i użytkownik zainicjuje te ostatnie. Ręczna praca awaryjna także niższe cel czasu odzyskiwania (RTO) w porównaniu do Microsoft zainicjowane opcja pracy awaryjnej. W poniższych sekcjach omówiono określonego docelowego czasu odzyskiwania, oferowana w przypadku poszczególnych opcji. Po jednej z tych opcji, aby włączyć tryb failover z Centrum IoT hub z jego region podstawowy jest wykonywane, Centrum staje się w pełni funkcjonalne w odpowiednich [regionu sparowanego geograficznie Azure](../best-practices-availability-paired-regions.md).


Obie te opcje trybu failover mają następujące cele punktu odzyskiwania (RPO):

| Typ danych | Cele punktu odzyskiwania (RPO) |
| --- | --- |
| Rejestr tożsamości |0 – 5 minutach utraty danych |
| Dane bliźniaczej reprezentacji urządzenia |0 – 5 minutach utraty danych |
| Chmura urządzenie wiadomości ** |0 – 5 minutach utraty danych |
| Zadania nadrzędnego ** i urządzenia |0 – 5 minutach utraty danych |
| Komunikaty z urządzenia do chmury |Wszystkie nieprzeczytane wiadomości są tracone |
| Monitorowanie komunikatów operacji |Wszystkie nieprzeczytane wiadomości są tracone |
| Komunikatów zwrotnych chmury do urządzenia |Wszystkie nieprzeczytane wiadomości są tracone |

Po zakończeniu operacji trybu failover dla usługi IoT hub, aby kontynuować pracę bez konieczności ręcznej interwencji powinny wszystkie operacje z urządzeń i aplikacji zaplecza.

> [!CAUTION]
> - Nazwa zgodnego z Centrum zdarzeń i punkt końcowy w punkcie końcowym usługi IoT Hub wbudowanych zdarzenia zmienia się po pracy awaryjnej. Gdy odbiera komunikaty telemetryczne z wbudowanego punktu końcowego przy użyciu klienta Centrum zdarzeń lub hosta procesora zdarzeń, wykonaj następujące czynności [Użyj parametrów połączenia Centrum IoT](iot-hub-devguide-messages-read-builtin.md#read-from-the-built-in-endpoint) do nawiązania połączenia. Dzięki temu, że serwer zaplecza w aplikacji będą nadal działać bez konieczności ręcznej interwencji wpis w tryb failover. Jeśli używasz nazwy zgodnego z Centrum zdarzeń i punkt końcowy w aplikacji zaplecza bezpośrednio, należy ponownie skonfigurować aplikację przez [pobieranie nowej nazwy zgodnego z Centrum zdarzeń i punkt końcowy](iot-hub-devguide-messages-read-builtin.md#read-from-the-built-in-endpoint) po włączeniu trybu failover, aby kontynuować operacje.
>
> - Po przejściu w tryb failover zdarzenia emitowane przy użyciu usługi Event Grid mogą być wykorzystywane za pomocą tej samej subskrypcji, skonfigurowane wcześniej tak długo, jak te subskrypcje usługi Event Grid są nadal dostępne.
>
> - Komunikaty z chmury do urządzenia i nadrzędne zadania nie uzyskać odzyskane w ramach ręcznej pracy awaryjnej w ramach oferty (wersja zapoznawcza) z tej funkcji.

### <a name="microsoft-initiated-failover"></a>Microsoft zainicjować trybu failover
Microsoft zainicjowano tryb failover jest wykonywane przez firmę Microsoft, w rzadkich sytuacjach do trybu failover wszystkich IoT hubs regionowi, których dotyczy problem do odpowiedniego regionu sparowanego geograficznie. Ten proces jest opcja domyślna (nie sposób użytkownicy mogą zrezygnować z nowego) i nie wymaga żadnej interwencji użytkownika. Firma Microsoft zastrzega sobie prawo do wprowadzania określenie, gdy ta opcja będzie wykonywane. Ten mechanizm nie wymaga zgody użytkownika, zanim Centrum użytkownika jest w trybie Failover. Microsoft zainicjować trybu failover ma cel czasu odzyskiwania (RTO) 2-26 godzin. Duży cel czasu odzyskiwania jest, ponieważ firmy Microsoft należy wykonać operację trybu failover w imieniu wszystkich klientów, których to dotyczy, w tym regionie. Jeśli korzystasz z mniej ważnych rozwiązanie IoT, które może wytrzymać bez przestojów około dnia, jest ok umożliwiające zależni tę opcję, aby spełnić ogólną celów odzyskiwania po awarii dla swojego rozwiązania IoT. Łączny czas wykonywania operacji funkcjonować pełni po wyzwoleniu tego procesu jest opisane w sekcji "Czas odzyskiwania".

### <a name="manual-failover-preview"></a>Ręczne przejście do trybu failover (wersja zapoznawcza)

Jeśli celów biznesowych czasu nie są spełnione przez wartość celu punktu odzyskiwania, który zainicjował firmy Microsoft zapewnia pracy awaryjnej, należy rozważyć użycie ręcznej pracy awaryjnej do wyzwalania procesu pracy awaryjnej, samodzielnie. Cel czasu odzyskiwania przy użyciu tej opcji może być w dowolnym miejscu od 10 minut do kilku godzin. Cel czasu odzyskiwania jest obecnie funkcję liczbę urządzeń zarejestrowanych przed wystąpieniem Centrum IoT, przełączone w tryb failover. Można oczekiwać, że cel czasu odzyskiwania dla koncentratora hostingu około 100 000 urządzeń w ballpark 15 minut. Łączny czas wykonywania operacji funkcjonować pełni po wyzwoleniu tego procesu jest opisane w sekcji "Czas odzyskiwania".

Opcja ręcznego przełączania trybu failover jest zawsze dostępna do użycia niezależnie od tego, czy region podstawowy ma przestój lub nie. W związku z tym ta opcja potencjalnie może służyć do wykonywania planowanego przejścia w tryb failover. Przykład użytkowania planowanego przejścia w tryb failover jest przeprowadzanie okresowe trybu failover. Word ostrożności jednak jest to, że wyniki operacji planowany tryb failover spowoduje przestój dla koncentratora dla okresu zdefiniowanego za cel czasu odzyskiwania dla tej opcji, a także powoduje utratę danych, zgodnie z definicją w powyższej tabeli cel punktu odzyskiwania. Można rozważyć ustawienie testu IoT hub wystąpienia korzystają z możliwości planowanego trybu failover, okresowo, aby uzyskać zaufania do możliwości, aby rozpocząć rozwiązania end-to-end w górę sytuacji rzeczywistej awarii.

> [!IMPORTANT]
> - Test awarii nie należy wykonać w centrach IoT, które są używane w środowiskach produkcyjnych.
>
> - Ręczna praca awaryjna nie należy jako mechanizm trwale migracji Centrum między regionów geograficznych platformy Azure, w połączeniu. To spowoduje, że zwiększenie opóźnienia dla operacji, które wykonywanych względem Centrum z urządzenia umieszczone w starym regionu podstawowego.
>
> - Ręczna praca awaryjna jest obecnie dostępna w wersji zapoznawczej i nie jest dostępna w następujących regionach platformy Azure. Wschodnie stany USA, zachodnie stany USA, północno Europa, Europa Zachodnia, Brazylia Południowa, południowo-środkowe stany USA.

### <a name="failback"></a>Powrót po awarii

Niepowodzenie powrót do starych regionu podstawowego można osiągnąć, wyzwalając działania trybu failover inną godzinę. W przypadku operacji pierwotnej trybu failover odzyskiwania sprawności po awarii rozszerzonej w regionie podstawowym, oryginalnym zaleca się, że koncentratora powinny nie do oryginalnej lokalizacji po tej lokalizacji odzyskał sprawność sytuacji awarii.

> [!IMPORTANT]
> - Użytkownicy mogą się tylko do wykonania 2 pracę w trybie failover i 2 operacji pomyślne powrotu po awarii na dzień.
>
> - Symetryczne operacje trybu failover/powrotu po awarii nie są dozwolone. Użytkownicy będą musieli czekać na 1 godzinę między tymi operacjami.

### <a name="time-to-recover"></a>Czas do odzyskania

Gdy nazwa FQDN (i w związku z tym parametry połączenia) wystąpieniem Centrum IoT pozostaje tego samego trybu failover post, podstawowy adres IP ulegnie zmianie. W związku z tym całkowity czas operacji środowiska uruchomieniowego wykonywanych względem wystąpienia Centrum IoT funkcjonować pełni po wyzwoleniu procesu pracy awaryjnej można wyrazić przy użyciu następujących funkcji.

Czas do odzyskania = wartość celu punktu odzyskiwania [10-minutowy materiał - 2 godziny w celu ręcznej pracy awaryjnej | 2-26 godzin Microsoft zainicjować trybu failover] + opóźnienia propagacji DNS + czas poświęcony przez aplikację klienta, aby odświeżyć dowolne pamięci podręcznej adres IP Centrum IoT.

> [!IMPORTANT]
> Zestawy SDK IoT nie buforują adres IP usługi IoT hub. Firma Microsoft zaleca kod użytkownika komunikowanie się z zestawami SDK powinno buforują adres IP usługi IoT hub.

## <a name="achieve-cross-region-ha"></a>Osiągnięcia między regionami wysokiej dostępności
Cele czasu działania biznesowe nie są spełnione przez czas RTO, że Microsoft zainicjować trybu failover albo ręczna praca awaryjna udostępniają, należy rozważyć, implementowanie mechanizm trybu failover na urządzenie automatyczne między regionami.
Pełne traktowania topologie wdrożenia w rozwiązaniach IoT znajduje się poza zakres tego artykułu. W tym artykule omówiono *rozwiązania regionalnej pracy awaryjnej* modelu wdrażania na potrzeby wysokiej dostępności i odzyskiwania po awarii.

W modelu rozwiązania regionalnej pracy awaryjnej rozwiązania, należy utworzyć kopię końcowy uruchomień głównie w jednej lokalizacji centrum danych. Pomocnicze Centrum IoT i zapleczem są wdrażane w innej lokalizacji centrum danych. Jeśli usługi IoT hub w regionie podstawowym wystąpi awaria lub zostało przerwane połączenie sieciowe z urządzenia do regionu podstawowego, urządzenia używają punktu końcowego usługi dodatkowej. Może zwiększyć dostępność rozwiązanie z zastosowaniem modelu trybu failover między regionami, zamiast pozostając w jednym regionie. 

Na wysokim poziomie do wdrożenia modelu rozwiązania regionalnej pracy awaryjnej przy użyciu usługi IoT Hub, musisz wykonać następujące czynności:

* **Pomocnicze Centrum IoT i urządzenia routingu logiki**: Jeśli usługi w danym regionie podstawowym zostanie przerwany, urządzenia należy uruchomić nawiązywania połączenia z regionu pomocniczego. Ze względu na charakter rozpoznawaniem stanu większości usług zaangażowani, jest typowe dla administratorów rozwiązania do wyzwalania procesu trybu failover między regionami. Najlepszym sposobem komunikacji nowego punktu końcowego na urządzeniach, przy jednoczesnym zachowaniu kontroli procesu, jest do nich regularnie sprawdzać *concierge* usługi, aby aktywny punkt końcowy. Usługi Konsjerż może być aplikacja sieci web, które są replikowane i przechowywane dostępny przy użyciu technik przekierowania DNS (na przykład za pomocą [usługi Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md)).

   > [!NOTE]
   > Usługi IoT hub nie jest typem obsługiwanych punktu końcowego w usłudze Azure Traffic Manager. Zalecane jest, aby zintegrować usługi Konsjerż proponowane z usługą Azure traffic manager czyniąc ją zaimplementować sondy kondycji punktu końcowego interfejsu API.

* **Replikacja rejestru tożsamości**: może być używany, pomocniczego Centrum IoT hub musi zawierać wszystkie tożsamości urządzeń, które można połączyć z rozwiązaniem. Rozwiązanie powinno replikowanej geograficznie dane o tożsamości urządzeń i przekazać je do pomocniczego Centrum IoT, przed zmianą aktywny punkt końcowy dla urządzeń. Funkcja eksportu tożsamość urządzenia usługi IoT Hub przydaje się w tym kontekście. Aby uzyskać więcej informacji zobacz [Centrum IoT Hub developer guide - rejestr tożsamości] [Centrum IoT Hub developer guide - rejestr tożsamości].
* **Scalanie logiki**: gdy region podstawowy staje się ponownie dostępny, wszystkie stan i dane, które zostały utworzone w lokacji dodatkowej muszą być migrowane z powrotem do regionu podstawowego. Ten stan i dane odnoszą się przede wszystkim do tożsamości urządzeń i metadanych aplikacji, które muszą zostać połączone z głównej usługi IoT hub i innych magazynach specyficzne dla aplikacji w regionie podstawowym. Aby uprościć ten krok, należy użyć operacje idempotentne. Operacje Idempotentne zminimalizować skutki uboczne, z ostateczną spójne dystrybucję zdarzeń oraz duplikatów lub dostarczania zdarzeń poza kolejnością. Ponadto logiki aplikacji powinny zostać tak zaprojektowane, tolerować potencjalne niezgodności lub nieco nieaktualne stanu. Taka sytuacja może wystąpić z powodu dodatkowy czas, jaki zajmuje systemowi poprawianie w oparciu o cele punktu odzyskiwania (RPO).

## <a name="choose-the-right-hadr-option"></a>Wybieranie odpowiedniej opcji wysokiej dostępności i odzyskiwania po awarii
Heres podsumowanie opcji wysokiej dostępności i odzyskiwania po awarii, przedstawione w tym artykule, który może służyć jako odwołanie do ramki o wybranie odpowiedniej opcji dla rozwiązania

| Opcja wysokiej dostępności i odzyskiwania po awarii | CEL CZASU ODZYSKIWANIA | RPO | Wymaga ręcznej interwencji? | Złożoność wdrożenia | Wpływ dodatkowych kosztów|
| --- | --- | --- | --- | --- | --- | --- |
| Microsoft zainicjować trybu failover |2 - 26 godzin|Zapoznaj się z powyższej tabeli cel punktu odzyskiwania|Nie|Brak|Brak|
| Ręczna praca awaryjna |10-minutowy materiał - 2 godziny|Zapoznaj się z powyższej tabeli cel punktu odzyskiwania|Yes|Bardzo niskie. Wystarczy wyzwalać tę operację z portalu.|Brak|
| Obejmujące wiele regionów wysokiej dostępności |< 1 min|Zależy od częstotliwości replikacji, niestandardowego rozwiązania wysokiej dostępności|Nie|Wysoka|1 > x koszt 1 usługi IoT hub|

## <a name="next-steps"></a>Kolejne kroki
Skorzystaj z poniższych linków, aby dowiedzieć się więcej na temat usługi Azure IoT Hub:

* [Rozpoczynanie pracy z usługą IoT Hub (samouczek)](quickstart-send-telemetry-dotnet.md)
* [Co to jest usługa Azure IoT Hub?](about-iot-hub.md)