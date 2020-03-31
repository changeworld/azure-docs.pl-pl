---
title: Projektowanie aplikacji o wysokiej dostępności przy użyciu pamięci masowej geograficznie nadmiarowej
titleSuffix: Azure Storage
description: Dowiedz się, jak używać magazynu geograficznego dostępu do odczytu, aby zaprojektować wysoce dostępną aplikację, która jest wystarczająco elastyczna, aby obsłużyć awarie.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 592be1710893791e80dfe4b20e1323e789b33e69
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77157096"
---
# <a name="designing-highly-available-applications-using-read-access-geo-redundant-storage"></a>Projektowanie aplikacji o wysokiej dostępności przy użyciu pamięci masowej geograficznej z dostępem do odczytu

Wspólną cechą infrastruktury opartej na chmurze, takich jak usługa Azure Storage, jest zapewnienie wysokiej dostępności platformy do hostowania aplikacji. Deweloperzy aplikacji opartych na chmurze należy dokładnie rozważyć, jak wykorzystać tę platformę, aby zapewnić użytkownikom wysokiej dostępności aplikacji. W tym artykule skupiono się na tym, jak deweloperzy mogą korzystać z jednej z opcji replikacji geograficznej platformy Azure, aby upewnić się, że ich aplikacje usługi Azure Storage są wysoce dostępne.

Konta magazynu skonfigurowane do replikacji geograficznie nadmiarowej są synchronicznie replikowane w regionie podstawowym, a następnie replikowane asynchronicznie do regionu pomocniczego oddalonego o setki mil. Usługa Azure Storage oferuje dwa typy replikacji geograficznie nadmiarowej:

* [Magazyn nadmiarowy strefy geograficznej (GZRS) (wersja zapoznawcza)](storage-redundancy.md) zapewnia replikację dla scenariuszy wymagających zarówno wysokiej dostępności, jak i maksymalnej trwałości. Dane są replikowane synchronicznie w trzech strefach dostępności platformy Azure w regionie podstawowym przy użyciu magazynu nadmiarowego strefy (ZRS), a następnie replikowane asynchronicznie do regionu pomocniczego. Aby uzyskać dostęp do odczytu danych w regionie pomocniczym, włącz magazyn geograficzny dostępu do strefy odczytu (RA-GZRS).
* [Magazyn geograficznie nadmiarowy (GRS)](storage-redundancy.md) zapewnia replikację międzyregionacyjną w celu ochrony przed regionalnymi awariami. Dane są replikowane synchronicznie trzy razy w regionie podstawowym przy użyciu magazynu lokalnie nadmiarowego (LRS), a następnie replikowane asynchronicznie do regionu pomocniczego. Aby uzyskać dostęp do odczytu danych w regionie pomocniczym, włącz magazyn geograficzny dostępu do odczytu (RA-GRS).

W tym artykule pokazano, jak zaprojektować aplikację do obsługi awarii w regionie podstawowym. Jeśli region podstawowy staje się niedostępny, aplikacja może dostosować się do wykonywania operacji odczytu względem regionu pomocniczego zamiast. Przed rozpoczęciem upewnij się, że twoje konto magazynu jest skonfigurowane dla ra-GRS lub RA-GZRS.

Aby uzyskać informacje o tym, które regiony podstawowe są sparowane z regionami pomocniczymi, zobacz [Ciągłość działania i odzyskiwanie po awarii (BCDR): Sparowane regiony platformy Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

W tym artykule znajdują się fragmenty kodu oraz łącze do pełnego przykładu na końcu, który można pobrać i uruchomić.

## <a name="application-design-considerations-when-reading-from-the-secondary"></a>Zagadnienia dotyczące projektowania aplikacji podczas odczytu z

Celem tego artykułu jest pokazanie, jak zaprojektować aplikację, która będzie nadal działać (choć w ograniczonej pojemności) nawet w przypadku poważnej awarii w głównym centrum danych. Można zaprojektować aplikację do obsługi przejściowych lub długotrwałych problemów, odczytywanie z regionu pomocniczego, gdy występuje problem, który zakłóca odczyt z regionu podstawowego. Gdy region podstawowy jest ponownie dostępny, aplikacja może powrócić do odczytu z regionu podstawowego.

Należy pamiętać o tych kluczowych punktach podczas projektowania aplikacji dla RA-GRS lub RA-GZRS:

* Usługa Azure Storage przechowuje kopię tylko do odczytu danych, które przechowujesz w regionie podstawowym w regionie pomocniczym. Jak wspomniano powyżej, usługa magazynu określa lokalizację regionu pomocniczego.

* Kopia tylko do odczytu jest [po pewnym czasie zgodna](https://en.wikipedia.org/wiki/Eventual_consistency) z danymi w regionie podstawowym.

* W przypadku obiektów blob, tabel i kolejek można zbadać region pomocniczy dla wartości *czas ostatniej synchronizacji,* która informuje o wystąpieniu ostatniej replikacji z regionu podstawowego do pomocniczego. (Ta opcja nie jest obsługiwana w przypadku usług Azure Files, która nie ma nadmiarowości RA-GRS w tej chwili).

* Biblioteka klienta magazynu służy do odczytu i zapisu danych w regionie podstawowym lub pomocniczym. Można również przekierować żądania odczytu automatycznie do regionu pomocniczego, jeśli żądanie odczytu do regionu podstawowego przesunie się.

* Jeśli region podstawowy stanie się niedostępny, można zainicjować przetwórnie awaryjne konta. Po przełączeniu awaryjnego do regionu pomocniczego wpisy DNS wskazujące region podstawowy są zmieniane w celu wskazania regionu pomocniczego. Po zakończeniu pracy awaryjnej dostęp do zapisu jest przywracany dla kont GRS i RA-GRS. Aby uzyskać więcej informacji, zobacz [Odzyskiwanie po awarii i odzyskiwanie awaryjne konta magazynu (wersja zapoznawcza) w usłudze Azure Storage](storage-disaster-recovery-guidance.md).

> [!NOTE]
> Funkcja trybu failover (wersja zapoznawcza) zarządzanego przez klienta (wersja zapoznawcza) nie jest jeszcze dostępna w regionach obsługujących GZRS/RA-GZRS, więc klienci nie mogą obecnie zarządzać zdarzeniami pracy awaryjnej konta za pomocą kont GZRS i RA-GZRS. Podczas wersji zapoznawczej firma Microsoft będzie zarządzać wszelkimi zdarzeniami pracy awaryjnej dotyczącymi kont GZRS/RA-GZRS.

### <a name="using-eventually-consistent-data"></a>Korzystanie z ostatecznie spójnych danych

Proponowane rozwiązanie zakłada, że jest dopuszczalne, aby zwrócić potencjalnie starych danych do aplikacji wywołującej. Ponieważ dane w regionie pomocniczym jest po pewnym czasie spójne, jest możliwe, że region podstawowy może stać się niedostępny przed zakończeniem replikacji aktualizacji do regionu pomocniczego.

Załóżmy na przykład, że klient pomyślnie przesyła aktualizację, ale region podstawowy kończy się niepowodzeniem, zanim aktualizacja zostanie propagowana do regionu pomocniczego. Gdy klient poprosi o odczyt danych z powrotem, otrzymują przestarzałe dane z regionu pomocniczego zamiast zaktualizowanych danych. Podczas projektowania aplikacji, należy zdecydować, czy jest to dopuszczalne, a jeśli tak, w jaki sposób będzie wysyłać wiadomości do klienta. 

W dalszej części tego artykułu pokazujemy, jak sprawdzić czas ostatniej synchronizacji dla danych pomocniczych, aby sprawdzić, czy pomocniczy jest aktualny.

### <a name="handling-services-separately-or-all-together"></a>Obsługa usług oddzielnie lub wszystkie razem

Chociaż jest mało prawdopodobne, jest możliwe dla jednej usługi stają się niedostępne, podczas gdy inne usługi są nadal w pełni funkcjonalne. Można obsługiwać ponownych prób i tryb tylko do odczytu dla każdej usługi oddzielnie (obiekty blob, kolejki, tabele) lub można obsługiwać ponownych prób ogólnie dla wszystkich usług magazynu razem.

Na przykład jeśli używasz kolejek i obiektów blob w aplikacji, można zdecydować się umieścić w osobnym kodzie do obsługi błędów ponawianych dla każdego z nich. Następnie, jeśli pojawi się ponowna próba z usługi obiektu blob, ale usługa kolejki nadal działa, tylko część aplikacji, która obsługuje obiekty blobs będzie miało wpływ. Jeśli zdecydujesz się obsługiwać wszystkie usługi magazynu ponownych prób ogólnie i wywołanie usługi obiektu blob zwraca błąd ponawialne, a następnie żądania do usługi obiektu blob i usługi kolejki będzie miało wpływ.

Ostatecznie zależy to od złożoności aplikacji. Możesz zdecydować, aby nie obsługiwać błędów według usługi, ale zamiast tego przekierować żądania odczytu dla wszystkich usług magazynu do regionu pomocniczego i uruchomić aplikację w trybie tylko do odczytu po wykryciu problemu z dowolną usługą magazynu w regionie podstawowym.

### <a name="other-considerations"></a>Inne zagadnienia

Są to inne kwestie, które omówimy w dalszej części tego artykułu.

* Obsługa ponownych prób odczytu żądań przy użyciu wzorca wyłącznika

* Ostatecznie spójne dane i czas ostatniej synchronizacji

* Testowanie

## <a name="running-your-application-in-read-only-mode"></a>Uruchamianie aplikacji w trybie tylko do odczytu

Aby skutecznie przygotować się do awarii w regionie podstawowym, musi być w stanie obsługiwać zarówno nieudanych żądań odczytu i nie powiodło się żądania aktualizacji (z aktualizacją w tym przypadku, co oznacza wstawia, aktualizacje i usunięcia). Jeśli region podstawowy nie powiedzie się, żądania odczytu można przekierować do regionu pomocniczego. Jednak żądania aktualizacji nie mogą być przekierowywane do pomocniczego, ponieważ pomocniczy jest tylko do odczytu. Z tego powodu należy zaprojektować aplikację, aby działała w trybie tylko do odczytu.

Na przykład można ustawić flagę, która jest sprawdzana przed przesłaniem jakichkolwiek żądań aktualizacji do usługi Azure Storage. Gdy pojawi się jedno z żądań aktualizacji, można go pominąć i zwrócić odpowiednią odpowiedź do klienta. Możesz nawet całkowicie wyłączyć niektóre funkcje, dopóki problem nie zostanie rozwiązany i powiadomić użytkowników, że te funkcje są tymczasowo niedostępne.

Jeśli zdecydujesz się obsługiwać błędy dla każdej usługi oddzielnie, należy również obsługiwać możliwość uruchamiania aplikacji w trybie tylko do odczytu przez usługę. Na przykład może mieć tylko do odczytu flagi dla każdej usługi, które mogą być włączone i wyłączone. Następnie można obsługiwać flagę w odpowiednich miejscach w kodzie.

Możliwość uruchamiania aplikacji w trybie tylko do odczytu ma inną zaletę po stronie — daje możliwość zapewnienia ograniczonej funkcjonalności podczas uaktualniania głównych aplikacji. Możesz wyzwolić aplikację do uruchamiania w trybie tylko do odczytu i wskaż dodatkowe centrum danych, zapewniając, że nikt nie uzyskuje dostępu do danych w regionie podstawowym podczas uaktualniania.

## <a name="handling-updates-when-running-in-read-only-mode"></a>Obsługa aktualizacji podczas pracy w trybie tylko do odczytu

Istnieje wiele sposobów obsługi żądań aktualizacji podczas uruchamiania w trybie tylko do odczytu. Nie omówimy tego kompleksowo, ale ogólnie rzecz biorąc, istnieje kilka wzorców, które uważasz.

1. Możesz odpowiedzieć użytkownikowi i powiedzieć mu, że obecnie nie akceptujesz aktualizacji. Na przykład system zarządzania kontaktami może umożliwić klientom dostęp do informacji kontaktowych, ale nie wprowadzać aktualizacji.

2. Aktualizacje można wyliczyć w innym regionie. W takim przypadku należy napisać oczekujące żądania aktualizacji do kolejki w innym regionie, a następnie mieć możliwość przetwarzania tych żądań po ponownym przejściu do trybu online podstawowego centrum danych. W tym scenariuszu należy poinformować klienta, że żądana aktualizacja jest umieszczana w kolejce do późniejszego przetwarzania.

3. Aktualizacje można zapisywać na koncie magazynu w innym regionie. Następnie, gdy podstawowe centrum danych powróci do trybu online, można połączyć te aktualizacje z danymi podstawowymi, w zależności od struktury danych. Na przykład w przypadku tworzenia oddzielnych plików z datą/sygnaturą czasową w nazwie można skopiować te pliki z powrotem do regionu podstawowego. Działa to w przypadku niektórych obciążeń, takich jak rejestrowanie i dane iOT.

## <a name="handling-retries"></a>Obsługa ponownych prób

Biblioteka klienta usługi Azure Storage pomaga określić, które błędy mogą być ponowione. Na przykład błąd 404 (nie znaleziono zasobu) nie zostanie ponowiony, ponieważ ponowna próba nie spowoduje sukcesu. Z drugiej strony, błąd 500 może zostać ponowiony, ponieważ jest to błąd serwera, a problem może być po prostu przejściowym problemem. Aby uzyskać więcej informacji, sprawdź [kod open source dla klasy ExponentialRetry](https://github.com/Azure/azure-storage-net/blob/87b84b3d5ee884c7adc10e494e2c7060956515d0/Lib/Common/RetryPolicies/ExponentialRetry.cs) w bibliotece klienta magazynu .NET. (Poszukaj Metody ShouldRetry).)

### <a name="read-requests"></a>Odczytywanie żądań

Żądania odczytu można przekierować do magazynu pomocniczego, jeśli występuje problem z magazynem podstawowym. Jak wspomniano powyżej w [using eventually consistent data](#using-eventually-consistent-data), musi być dopuszczalne dla aplikacji potencjalnie odczytać starych danych. Jeśli używasz biblioteki klienta magazynu, aby uzyskać dostęp do danych z pomocniczego, można określić zachowanie ponawiania żądania odczytu, ustawiając wartość właściwości **LocationMode** na jedną z następujących czynności:

* **PrimaryOnly** (domyślnie)

* **PodstawowyThenSecondary**

* **Drugorzędnie tylko**

* **WtórnaNastępnaprimary**

Po ustawieniu **LocationMode** na **PrimaryThenSecondary**, jeśli początkowe żądanie odczytu do podstawowego punktu końcowego nie powiedzie się z błędem, który można ponowić, klient automatycznie wykonuje kolejne żądanie odczytu do pomocniczego punktu końcowego. Jeśli błąd jest limit czasu serwera, klient będzie musiał poczekać na upłynie czasu wygaśnie, zanim otrzyma błąd ponawialne z usługi.

Zasadniczo istnieją dwa scenariusze, które należy wziąć pod uwagę przy podejmowaniu decyzji, jak reagować na błąd ponawiany:

* Jest to izolowany problem i kolejne żądania do podstawowego punktu końcowego nie zwróci błąd ponawialny. Przykładem tego, gdzie może się to zdarzyć, jest, gdy występuje błąd sieci przejściowej.

    W tym scenariuszu nie ma żadnych istotnych kar wydajności w **posiadaniu LocationMode ustawiona** na **PrimaryThenSecondary,** ponieważ dzieje się to rzadko.

* Jest to problem z co najmniej jedną z usług magazynu w regionie podstawowym i wszystkie kolejne żądania do tej usługi w regionie podstawowym mogą zwracać błędy ponawiane przez pewien okres czasu. Przykładem tego jest, jeśli region podstawowy jest całkowicie niedostępny.

    W tym scenariuszu występuje kara wykonania, ponieważ wszystkie żądania odczytu najpierw spróbuje podstawowego punktu końcowego, poczekaj na wygaśnięcie limitu czasu, a następnie przełącz się do pomocniczego punktu końcowego.

W przypadku tych scenariuszy należy zidentyfikować, że występuje ciągły problem z podstawowym punktem końcowym i wysłać wszystkie żądania odczytu bezpośrednio do pomocniczego punktu końcowego, ustawiając właściwość **LocationMode** na **SecondaryOnly**. W tej chwili należy również zmienić aplikację, aby działać w trybie tylko do odczytu. Takie podejście jest znane jako [wzorzec wyłącznika.](/azure/architecture/patterns/circuit-breaker)

### <a name="update-requests"></a>Żądania aktualizacji

Wzorzec wyłącznika można również zastosować do żądań aktualizacji. Jednak żądania aktualizacji nie mogą być przekierowywane do magazynu pomocniczego, który jest tylko do odczytu. W przypadku tych żądań należy pozostawić **LocationMode** właściwość ustawiona na **PrimaryOnly** (domyślnie). Aby obsłużyć te błędy, można zastosować metrykę do tych żądań — takich jak 10 błędów w wierszu — a po osiągnięciu progu przełącz aplikację w tryb tylko do odczytu. Można użyć tych samych metod powrotu do trybu aktualizacji, jak te opisane poniżej w następnej sekcji dotyczącej wzorca wyłącznika.

## <a name="circuit-breaker-pattern"></a>Wzorzec wyłącznika

Za pomocą wzorca wyłącznika w aplikacji można uniemożliwić mu ponowienie próby operacji, która może zakończyć się niepowodzeniem wielokrotnie. Umożliwia aplikacji, aby kontynuować działanie, a nie biorąc czas, gdy operacja jest ponowiona wykładniczo. Wykrywa również, kiedy usterka została naprawiona, w którym to czasie aplikacja może spróbować ponownie wykonać operację.

### <a name="how-to-implement-the-circuit-breaker-pattern"></a>Jak zaimplementować wzorzec wyłącznika

Aby zidentyfikować, że istnieje ciągły problem z podstawowym punktem końcowym, można monitorować, jak często klient napotyka błędy ponawiane. Ponieważ każdy przypadek jest inny, należy zdecydować o progu, który ma być używany do decyzji o przełączeniu się do pomocniczego punktu końcowego i uruchomieniu aplikacji w trybie tylko do odczytu. Na przykład można zdecydować się na wykonanie przełącznika, jeśli istnieje 10 błędów w wierszu bez powodzenia. Innym przykładem jest przełączenie, jeśli 90% żądań w okresie 2 minut zakończy się niepowodzeniem.

W pierwszym scenariuszu można po prostu zachować liczbę błędów, a jeśli jest sukces przed osiągnięciem maksymalnej, ustawić liczbę z powrotem do zera. W drugim scenariuszu jednym ze sposobów jego zaimplementowania jest użycie obiektu MemoryCache (w .NET). Dla każdego żądania dodaj CacheItem do pamięci podręcznej, ustaw wartość na sukces (1) lub niepowodzenie (0) i ustaw czas wygaśnięcia do 2 minut od teraz (lub niezależnie od ograniczenia czasu). Po osiągnięciu czasu wygaśnięcia wpisu wpis jest automatycznie usuwany. To daje toczenia 2-minutowe okno. Za każdym razem, gdy wysyłasz żądanie do usługi magazynu, najpierw używasz zapytania Linq w obiekcie MemoryCache, aby obliczyć sukces procentu, sumując wartości i dzieląc przez liczbę. Gdy procent sukcesu spadnie poniżej pewnego progu (na przykład 10%), ustaw **LocationMode** właściwość dla żądań odczytu **do SecondaryOnly** i przełączyć aplikację w tryb tylko do odczytu przed kontynuowaniem.

Próg błędów używany do określenia, kiedy dokonać przełącznika może się różnić od usługi do usługi w aplikacji, więc należy rozważyć wprowadzenie ich konfigurowalne parametry. Jest to również, gdy zdecydujesz się obsługiwać błędy ponawialne z każdej usługi oddzielnie lub jako jeden, jak wspomniano wcześniej.

Inną kwestią jest sposób obsługi wielu wystąpień aplikacji i co zrobić po wykryciu błędów ponawianych w każdym wystąpieniu. Na przykład może mieć 20 maszyn wirtualnych uruchomionych z tą samą aplikacją załadowaną. Czy obsługujesz każde wystąpienie oddzielnie? Jeśli jedno wystąpienie zaczyna mieć problemy, czy chcesz ograniczyć odpowiedź tylko do tego jednego wystąpienia, czy chcesz spróbować, aby wszystkie wystąpienia reagowały w ten sam sposób, gdy jedno wystąpienie ma problem? Obsługa wystąpień oddzielnie jest znacznie prostsze niż próby koordynowania odpowiedzi między nimi, ale jak to zrobić zależy od architektury aplikacji.

### <a name="options-for-monitoring-the-error-frequency"></a>Opcje monitorowania częstotliwości występowania błędów

Dostępne są trzy główne opcje monitorowania częstotliwości ponownych prób w regionie podstawowym w celu określenia, kiedy przełączyć się do regionu pomocniczego i zmienić aplikację na uruchamianie w trybie tylko do odczytu.

* Dodaj program obsługi dla zdarzenia [**Ponowienie próby**](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.operationcontext.retrying) w [**OperationContext**](https://docs.microsoft.com/java/api/com.microsoft.applicationinsights.extensibility.context.operationcontext) obiektu, który przekazujesz do żądań magazynu — jest to metoda wyświetlana w tym artykule i używana w towarzyszącym przykładzie. Te zdarzenia są uruchamiane za każdym razem, gdy klient ponawia próbę żądania, umożliwiając śledzenie, jak często klient napotyka błędy ponawiane w podstawowym punkcie końcowym.

    ```csharp
    operationContext.Retrying += (sender, arguments) =>
    {
        // Retrying in the primary region
        if (arguments.Request.Host == primaryhostname)
            ...
    };
    ```

* W [**Evaluate**](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.iextendedretrypolicy.evaluate) metody w niestandardowej zasad ponawiania próby, można uruchomić kod niestandardowy, gdy ponawia próbę ma miejsce. Oprócz nagrywania, gdy ponawianie próby się dzieje, daje to również możliwość modyfikowania zachowania ponawiania.

    ```csharp 
    public RetryInfo Evaluate(RetryContext retryContext,
    OperationContext operationContext)
    {
        var statusCode = retryContext.LastRequestResult.HttpStatusCode;
        if (retryContext.CurrentRetryCount >= this.maximumAttempts
            || ((statusCode >= 300 && statusCode < 500 && statusCode != 408)
            || statusCode == 501 // Not Implemented
            || statusCode == 505 // Version Not Supported
            ))
        {
            // Do not retry
            return null;
        }

        // Monitor retries in the primary location
        ...

        // Determine RetryInterval and TargetLocation
        RetryInfo info =
            CreateRetryInfo(retryContext.CurrentRetryCount);

        return info;
    }
    ```

* Trzecie podejście jest zaimplementowanie niestandardowego składnika monitorowania w aplikacji, która stale pinguje podstawowy punkt końcowy magazynu z fikcyjnymi żądaniami odczytu (na przykład odczyt małych obiektów blob) w celu określenia jego kondycji. Zajęłoby to pewne zasoby, ale nie znaczną kwotę. Po wykryciu problemu, który osiągnie próg, należy następnie wykonać przełącznik do **tryb secondaryonly** i tylko do odczytu.

W pewnym momencie należy przełączyć się z powrotem do korzystania z podstawowego punktu końcowego i zezwalanie na aktualizacje. Jeśli używasz jednej z dwóch pierwszych metod wymienionych powyżej, można po prostu przełączyć się z powrotem do podstawowego punktu końcowego i włączyć tryb aktualizacji po dowolnie wybrany czas lub liczba operacji została wykonana. Następnie można pozwolić mu przejść przez logikę ponawiania ponownie. Jeśli problem został rozwiązany, będzie nadal używać podstawowego punktu końcowego i zezwolić na aktualizacje. Jeśli nadal występuje problem, po raz kolejny przełączy się z powrotem do pomocniczego punktu końcowego i trybu tylko do odczytu po niepowodzeniu ustawionych kryteriów.

W trzecim scenariuszu po ponownym podaniu pingowania do podstawowego punktu końcowego magazynu można wyzwolić przełącznik z powrotem do **PrimaryOnly** i kontynuować zezwalanie na aktualizacje.

## <a name="handling-eventually-consistent-data"></a>Obsługa ostatecznie spójnych danych

Magazyn geograficznie nadmiarowy działa przez replikowanie transakcji z regionu podstawowego do pomocniczego. Ten proces replikacji gwarantuje, że dane w regionie pomocniczym są *po pewnym czasie spójne.* Oznacza to, że wszystkie transakcje w regionie podstawowym ostatecznie pojawią się w regionie pomocniczym, ale może wystąpić opóźnienie przed ich pojawieniem się i że nie ma gwarancji, że transakcje dotrą do regionu pomocniczego w tej samej kolejności, w jakiej zostały pierwotnie zastosowane w regionie pierwotnym. Jeśli transakcje przychodzą w regionie pomocniczym poza kolejnością, *można* uznać, że dane w regionie pomocniczym są w niespójnym stanie, dopóki usługa nie nadrobi zaległości.

W poniższej tabeli przedstawiono przykład tego, co może się zdarzyć po zaktualizowaniu szczegółów pracownika, aby uczynić go członkiem roli *administratorów.* W tym przykładzie wymaga to zaktualizowania jednostki **pracownika** i aktualizacji jednostki **roli administratora** z liczbą całkowitej liczby administratorów. Zwróć uwagę, jak aktualizacje są stosowane poza kolejnością w regionie pomocniczym.

| **Czas** | **Transakcji**                                            | **Replikacji**                       | **Czas ostatniej synchronizacji** | **Wynik** |
|----------|------------------------------------------------------------|---------------------------------------|--------------------|------------| 
| T0       | Transakcja A: <br> Wstaw pracownika <br> jednostki w podstawowej |                                   |                    | Transakcja A wstawiona do podstawowej,<br> nie zostały jeszcze zreplikowane. |
| T1       |                                                            | Transakcja A <br> zreplikowane do<br> Pomocniczy | T1 | Transakcja A replikowana do pomocniczego. <br>Zaktualizowano czas ostatniej synchronizacji.    |
| T2       | Transakcja B:<br>Aktualizacja<br> jednostka pracownica<br> w podstawowych  |                                | T1                 | Transakcja B zapisana do podstawowej,<br> nie zostały jeszcze zreplikowane.  |
| T3       | Transakcja C:<br> Aktualizacja <br>administrator<br>rolę jednostki w<br>Podstawowy |                    | T1                 | Transakcja C napisana do podstawowej,<br> nie zostały jeszcze zreplikowane.  |
| *T4*     |                                                       | Transakcja C <br>zreplikowane do<br> Pomocniczy | T1         | Transakcja C zreplikowana do pomocniczego.<br>LastSyncTime nie został zaktualizowany, ponieważ <br>transakcja B nie została jeszcze zreplikowana.|
| *T5 (włas ie*     | Odczyt jednostek <br>z wtórnego                           |                                  | T1                 | Otrzymujesz przestarzałą wartość dla pracownika <br> podmiotu, ponieważ transakcja B nie <br> jeszcze zreplikowane. Otrzymujesz nową wartość dla<br> rolę administratora, ponieważ C<br> Replikowane. Czas ostatniej synchronizacji nadal nie<br> została zaktualizowana, ponieważ transakcja B<br> nie został zreplikowany. Możesz powiedzieć, że<br>jednostka roli administratora jest niespójna <br>ponieważ data/godzina jednostki jest <br>czas ostatniej synchronizacji. |
| *T6*     |                                                      | Transakcja B<br> zreplikowane do<br> Pomocniczy | T6                 | *T6* – Wszystkie transakcje za pośrednictwem C <br>został zreplikowany, czas ostatniej synchronizacji<br> jest aktualizowana. |

W tym przykładzie załóżmy, że klient przełącza się do odczytu z regionu pomocniczego w T5. W tej chwili można pomyślnie odczytać encję **roli administratora,** ale jednostka zawiera wartość dla liczby administratorów, która nie jest zgodna z liczbą jednostek **pracowników,** które są oznaczone jako administratorzy w regionie pomocniczym w tej chwili. Klient może po prostu wyświetlić tę wartość, z ryzykiem, że są to niespójne informacje. Alternatywnie klient może próbować ustalić, że **rola administratora** jest w stanie potencjalnie niespójne, ponieważ aktualizacje miały miejsce poza kolejnością, a następnie poinformować użytkownika o tym fakcie.

Aby rozpoznać, że ma potencjalnie niespójne dane, klient może użyć wartości *czas ostatniej synchronizacji,* które można uzyskać w dowolnym momencie, odpytując usługę magazynu. Informuje o czasie, kiedy dane w regionie pomocniczym był ostatni spójne i kiedy usługa zastosowała wszystkie transakcje przed tym punktem w czasie. W przykładzie pokazanym powyżej po wstawieniu jednostki **pracownika** w regionie pomocniczym czas ostatniej synchronizacji jest ustawiony na *T1*. Pozostaje w *T1,* dopóki usługa nie aktualizuje jednostki **pracownika** w regionie pomocniczym, gdy jest ustawiona na *T6*. Jeśli klient pobiera czas ostatniej synchronizacji podczas odczytywać jednostki w *T5*, można porównać go z sygnaturą czasną na jednostce. Jeśli sygnatura czasowa jednostki jest późniejsza niż czas ostatniej synchronizacji, jednostka jest w stanie potencjalnie niespójne i można podjąć wszelkie działania, co jest odpowiednie dla aplikacji. Użycie tego pola wymaga informacji o zakończeniu ostatniej aktualizacji podstawowego.

Aby dowiedzieć się, jak sprawdzić czas ostatniej synchronizacji, zobacz [Sprawdzanie właściwości Ostatni czas synchronizacji dla konta magazynu](last-sync-time-get.md).

## <a name="testing"></a>Testowanie

Ważne jest, aby przetestować, że aplikacja zachowuje się zgodnie z oczekiwaniami, gdy napotka błędy ponawiane. Na przykład należy przetestować, czy aplikacja przełącza się do pomocniczego i w trybie tylko do odczytu, gdy wykryje problem i przełącza się z powrotem, gdy region podstawowy staje się ponownie dostępny. Aby to zrobić, należy sposób symulować błędy ponawiane i kontrolować, jak często występują.

Fiddler służy do [przechwytywania](https://www.telerik.com/fiddler) i modyfikowania odpowiedzi HTTP w skrypcie. Ten skrypt można zidentyfikować odpowiedzi, które pochodzą z podstawowego punktu końcowego i zmienić kod stanu HTTP do jednego, który biblioteka klienta magazynu rozpoznaje jako błąd ponawialny. Ten fragment kodu pokazuje prosty przykład skryptu Fiddler, który przechwytuje odpowiedzi na żądania odczytu względem tabeli **danych pracownika,** aby zwrócić stan 502:

```java
static function OnBeforeResponse(oSession: Session) {
    ...
    if ((oSession.hostname == "\[yourstorageaccount\].table.core.windows.net")
      && (oSession.PathAndQuery.StartsWith("/employeedata?$filter"))) {
        oSession.responseCode = 502;
    }
}
```

Można rozszerzyć w tym przykładzie do przechwytywania szerszego zakresu żądań i tylko zmienić **responseCode** na niektóre z nich, aby lepiej symulować scenariusz w świecie rzeczywistym. Aby uzyskać więcej informacji na temat dostosowywania skryptów Fiddler, zobacz [Modyfikowanie żądania lub odpowiedzi](https://docs.telerik.com/fiddler/KnowledgeBase/FiddlerScript/ModifyRequestOrResponse) w dokumentacji Fiddler.

Jeśli zostały wprowadzone progi dla przełączania aplikacji do trybu tylko do odczytu konfigurowalne, będzie łatwiej przetestować zachowanie z woluminów transakcji nieprodukcyjnych.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat sposobu odczytywania z regionu pomocniczego, w tym inny przykład ustawiania właściwości Czas ostatniej synchronizacji, zobacz [Opcje nadmiarowości usługi Azure Storage i Magazyn geograficzny dostępu do odczytu](https://blogs.msdn.microsoft.com/windowsazurestorage/2013/12/11/windows-azure-storage-redundancy-options-and-read-access-geo-redundant-storage/).

* Aby uzyskać pełny przykład pokazujący, jak dokonać przełączania tam iz powrotem między podstawowymi i pomocniczymi punktami końcowymi, zobacz [Przykłady platformy Azure — przy użyciu wzorca wyłącznika z magazynem RA-GRS.](https://github.com/Azure-Samples/storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs)
