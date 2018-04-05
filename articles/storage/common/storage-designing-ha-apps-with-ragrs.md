---
title: Projektowanie wysokiej dostępności aplikacji przy użyciu magazynu geograficznie nadmiarowego Azure dostęp do odczytu (RA-GRS) | Dokumentacja firmy Microsoft
description: Jak używać magazynu Azure RA-GRS do projektowania aplikacji wysokiej dostępności jest wystarczająco elastyczny, aby obsłużyć awarie.
services: storage
documentationcenter: .net
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 8f040b0f-8926-4831-ac07-79f646f31926
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/21/2018
ms.author: tamram
ms.openlocfilehash: f7f3f2d99e5582a1bcb672cc176258dfff9c3217
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="designing-highly-available-applications-using-ra-grs"></a>Projektowanie wysokiej dostępności aplikacji przy użyciu RA-GRS

Typową funkcją oparte na chmurze infrastruktury, takich jak usługi Azure Storage jest zapewniają platformy wysokiej dostępności do obsługi aplikacji. Deweloperzy aplikacji opartych na chmurze należy starannie rozważyć sposób korzystania z tej platformie aplikacje wysokiej dostępności dla użytkowników. Ten artykuł skupia się na sposób deweloperzy mogą używać dostęp do odczytu magazynu geograficznie nadmiarowego (RA-GRS) do zapewnienia o wysokiej dostępności swoich aplikacji usługi Azure Storage.

[!INCLUDE [storage-common-redundancy-options](../../../includes/storage-common-redundancy-options.md)]

Ten artykuł skupia się na GRS i RA-GRS. W wypadku magazynu GRS trzy kopie danych są przechowywane w regionie podstawowym wybranej podczas konfigurowania konta magazynu. Trzy dodatkowe kopie są obsługiwane asynchronicznie w regionie pomocniczym określony przez platformę Azure. RA-GRS jest odpowiednikiem GRS z tą różnicą, że masz dostęp do odczytu do dodatkowej kopiowania. Aby uzyskać więcej informacji na temat opcji nadmiarowość magazynu Azure, zobacz [replikacja usługi Azure Storage](https://docs.microsoft.com/azure/storage/storage-redundancy). Artykuł replikacji zawiera także par podstawowych i pomocniczych regionów.

Dostępne są zawarte w tym artykule i link do kompletnego przykładu na końcu, który można pobrać i uruchomić wstawki kodu.

> [!NOTE]
> Usługa Azure Storage obsługuje teraz magazyn strefowo nadmiarowy (ZRS) do tworzenia aplikacji wysokiej dostępności. Magazyn ZRS zapewnia prostym rozwiązaniem dla potrzeb nadmiarowość wiele aplikacji. Magazyn ZRS zapewnia ochronę przed awariami sprzętu lub poważnej awarii mające wpływ na jednego centrum danych. Aby uzyskać więcej informacji, zobacz [magazyn Strefowo nadmiarowy (ZRS): aplikacje o wysokiej dostępności usługi Azure Storage](storage-redundancy-zrs.md).

## <a name="key-features-of-ra-grs"></a>Najważniejsze funkcje RA-GRS

Należy pamiętać pod uwagę podczas projektowania aplikacji w celu RA-GRS:

* Magazyn Azure przechowuje kopię danych, które są przechowywane w Twoim regionie podstawowym w regionie pomocniczym tylko do odczytu. Jak wspomniano powyżej, usługa Magazyn Określa lokalizację w regionie pomocniczym.

* Kopia tylko do odczytu jest [ostatecznie spójne](https://en.wikipedia.org/wiki/Eventual_consistency) z danymi w regionie podstawowym.

* Dla obiektów blob, tabel i kolejek, można zbadać w regionie pomocniczym dla *czas ostatniej synchronizacji* wartość, która informuje, wystąpienia ostatniej replikacji z serwera podstawowego w regionie pomocniczym. (To nie jest obsługiwana dla plików Azure, która nie ma RA-GRS nadmiarowości w tej chwili.)

* Biblioteka klienta magazynu służy do interakcji z danymi w regionie podstawowym lub pomocniczym. Możesz również przekierować żądania automatycznie w regionie pomocniczym odczytu, jeśli upłynie limit czasu odczytu żądania do regionu podstawowego.

* Jeśli jest to główna kwestia wpływu na dostępność danych w regionie podstawowym, zespół Azure mogą wyzwalać geograficznie-trybu failover, po czym wpisy DNS, wskazując w regionie podstawowym zostanie zmieniony na wskaż region pomocniczy.

* W przypadku awarię replikacji geograficznej — warstwa Azure będzie wybierz nową lokalizację dodatkowej replikowane dane do tej lokalizacji, a następnie wskaż dodatkowej wpisy DNS. Pomocniczy punkt końcowy będzie niedostępny, dopóki konto magazynu zakończył replikację. Aby uzyskać więcej informacji, zobacz [co robić w przypadku wystąpienia awarii usługi Azure Storage](https://docs.microsoft.com/azure/storage/storage-disaster-recovery-guidance).

## <a name="application-design-considerations-when-using-ra-grs"></a>Zagadnienia dotyczące projektowania aplikacji przy użyciu RA-GRS

Celem tego artykułu jest pokazanie sposobu projektowania aplikacji, która będzie działać (chociaż są w ograniczonym zakresie), nawet w przypadku poważnej awarii w centrum danych podstawowych. Można zaprojektować aplikacji do obsługi problemów przejściowego lub długotrwałe przez odczytu z regionu pomocniczego, gdy występuje problem, który zakłóca odczytu z regionu podstawowego. Regionu podstawowego jest ponownie dostępny, aplikację można powrócić do odczytu z regionu podstawowego.

### <a name="using-eventually-consistent-data"></a>Przy użyciu ostatecznie spójność danych

Proponowane rozwiązania przyjęto założenie, czy jest możliwe do zaakceptowania potencjalnie stare dane są zwracane do aplikacji wywołującej. Ponieważ dane w regionie pomocniczym jest ostatecznie spójne, istnieje możliwość regionu podstawowego może stać się niedostępne przed aktualizacji w regionie pomocniczym zakończył replikację.

Na przykład załóżmy, że klient przesyła aktualizacji pomyślnie, ale regionu podstawowego nie powiedzie się przed aktualizacji są propagowane w regionie pomocniczym. Klient zapyta do odczytu z powrotem dane, on odbiera stare dane z regionu pomocniczego zamiast zaktualizowanych danych. Podczas projektowania aplikacji, należy zdecydować, czy jest to dopuszczalne, a jeśli tak, jak będzie komunikatów klienta. 

W dalszej części tego artykułu zostanie przedstawiony sposób sprawdzania czas ostatniej synchronizacji dla danych pomocniczych do sprawdzenia, czy pomocniczy jest aktualny.

### <a name="handling-services-separately-or-all-together"></a>Obsługa usług oddzielnie lub wszystkich elementów

Gdy jest to mało prawdopodobne, istnieje możliwość jednej usługi staną się niedostępne podczas gdy inne usługi są nadal w pełni funkcjonalne. Można obsługiwać ponownych prób i w trybie tylko do odczytu dla każdej usługi oddzielnie (obiekty BLOB, kolejek, tabel) lub liczbę ponownych prób objęty dla wszystkich usług magazynu może obsłużyć razem.

Na przykład jeśli używasz kolejek i obiektów blob w aplikacji może zdecydować o umieszczać w oddzielnych kodu do obsługi błędów powtarzający operację dla każdego z nich. Następnie Jeśli ponowna próba możesz pobrać z usługi obiektów blob, ale nadal działa usługa kolejkowania, jest ograniczona tylko część aplikacji, która obsługuje obiekty BLOB. Jeśli zdecydujesz się ogólnie obsługi ponawiania prób magazynu usługi i wywołanie usługi blob zwraca błąd powtarzający operację, następnie zarówno usługi obiektów blob i kolejki żądań wpłynie.

Ostatecznie zależy od stopnia złożoności aplikacji. Użytkownik może zrezygnować z obsługi błędów przez usługę, ale zamiast tego do przekierowywania żądań dla wszystkich usług magazynu w regionie pomocniczym odczytu i uruchomić aplikację w trybie tylko do odczytu, w przypadku wykrycia problemu z dowolnej usługi magazynu w regionie podstawowym.

### <a name="other-considerations"></a>Inne zagadnienia

Są to innych kwestii, które omówimy w dalszej części tego artykułu.

*   Obsługa ponawianie żądań odczytu przy użyciu wzorca wyłącznika

*   Po pewnym czasie spójności danych i czas ostatniej synchronizacji

*   Testowanie

## <a name="running-your-application-in-read-only-mode"></a>Uruchamiania aplikacji w trybie tylko do odczytu

Aby korzystać z magazynu RA-GRS, musi mieć możliwość obsługi zarówno nieudanych żądań odczytu i nieudane żądania aktualizacji (z aktualizacji w takim przypadku oznacza wstawiania, aktualizacji i usunięć). Jeśli podstawowe Centrum kończy się niepowodzeniem, przeczytaj można przekierować żądania do centrum danych pomocniczych. Jednak żądania aktualizacji nie można przekierować na serwerze pomocniczym, ponieważ pomocniczy jest tylko do odczytu. Z tego powodu należy zaprojektować aplikację do uruchamiania w trybie tylko do odczytu.

Na przykład można ustawić flagi, który jest sprawdzany przed żądań aktualizacji są przesyłane do usługi Azure Storage. Gdy jedno z żądań aktualizacji za pośrednictwem, można je pominąć i odpowiednią odpowiedź zwrócona do klienta. Możesz nawet chcesz wyłączyć niektórych funkcji całkowicie, dopóki ten problem zostanie rozwiązany i Powiadom użytkowników, że te funkcje są tymczasowo niedostępne.

Jeśli zdecydujesz się oddzielnie obsługi błędów dla każdej usługi, należy również obsługiwać możliwość uruchamiania aplikacji w trybie tylko do odczytu przez usługę. Na przykład może mieć flagi tylko do odczytu dla każdej usługi, która może być włączona i wyłączone. Następnie można obsługiwać flagi w odpowiednich miejscach w kodzie.

Możliwość uruchomienia aplikacji w trybie tylko do odczytu ma inny korzyści po stronie — daje możliwość zapewnienia ograniczoną funkcjonalność podczas uaktualniania najważniejszych aplikacji. Możesz wyzwolić aplikacji do uruchamiania w trybie tylko do odczytu i wskaż centrum danych w dodatkowej zapewnienie, że nikt nie uzyskuje dostęp do danych w regionie podstawowym, gdy wprowadzasz uaktualnienia.

## <a name="handling-updates-when-running-in-read-only-mode"></a>Obsługa aktualizacji w trybie tylko do odczytu

Istnieje wiele sposobów do obsługi żądań aktualizacji w trybie tylko do odczytu. Firma Microsoft nie obejmuje to kompleksowe, ale ogólnie rzecz biorąc, istnieje kilka wzorców, które należy wziąć pod uwagę.

1.  Można odpowiadanie na użytkownika i poinformuj go, że nie są obecnie akceptuje aktualizacje. Na przykład system zarządzania kontaktami można włączyć klientom dostęp do informacji kontaktowych, ale nie dokonywać aktualizacji.

2.  Można umieścić w kolejce aktualizacje w innym regionie. W takim przypadku czy zapisać Twoich żądań oczekujących aktualizacji do kolejki w innym regionie, a następnie mogą uzyskiwać do przetwarzania te żądania po centrum danych podstawowych przejściu do trybu online ponownie. W tym scenariuszu należy udostępnić klienta wiedzieć, że żądana aktualizacja jest przechowywane w kolejce do późniejszego przetwarzania.

3.  Aktualizacje można zapisywać na konto magazynu w innym regionie. Jeśli centrum danych podstawowych wraca do trybu online, możesz korzystać sposobem scalić tych aktualizacji danych podstawowych, w zależności od struktury danych. Na przykład jeśli tworzysz oddzielnych plików z sygnaturą daty/godziny w nazwie, można skopiować te pliki do regionu podstawowego. Działa to w przypadku niektórych obciążeń, takich jak dane rejestrowania i iOT.

## <a name="handling-retries"></a>Obsługa ponownych prób

Jak ustalić, które błędy są powtarzający operację? Jest to określane za pomocą biblioteki klienta usługi storage. Na przykład błąd 404 (nie znaleziono zasobu) nie jest powtarzający operację, ponieważ ponawianie próby jego nie może spowodować Powodzenie. Z drugiej strony 500 Błąd jest powtarzający operację, ponieważ jest to błąd serwera i po prostu może być to problem przejściowy. Aby uzyskać więcej informacji, zapoznaj się z [Otwórz kod źródłowy dla klasy ExponentialRetry](https://github.com/Azure/azure-storage-net/blob/87b84b3d5ee884c7adc10e494e2c7060956515d0/Lib/Common/RetryPolicies/ExponentialRetry.cs) w bibliotece klienta .NET magazynu. (Poszukaj metody ShouldRetry).

### <a name="read-requests"></a>Żądania odczytu

Jeśli występuje problem z magazynem podstawowego można przekierować żądania odczytu do magazynu pomocniczego. Jak zanotowane powyżej w [przy użyciu ostatecznie spójności danych](#using-eventually-consistent-data), musi być dopuszczalne potencjalnie odczytać starych danych aplikacji. Jeśli korzystasz z biblioteki klienta magazynu dostępu do danych RA-GRS, można określić zachowanie ponów żądanie odczytu, ustawiając wartość **LocationMode** właściwości do jednej z następujących czynności:

*   **PrimaryOnly** (ustawienie domyślne)

*   **PrimaryThenSecondary**

*   **SecondaryOnly**

*   **SecondaryThenPrimary**

Podczas ustawiania **LocationMode** do **PrimaryThenSecondary**, jeśli początkowe żądanie odczytu do awarii podstawowego punktu końcowego z powodu błędu powtarzającego operację, klient automatycznie tworzy kolejne żądanie odczytu pomocniczy punkt końcowy. Jeśli błąd jest limit czasu serwera, klient będzie mieć oczekiwania limitu czasu wygaśnięcia przed odbierze błąd powtarzający operację usługi.

Istnieją dwa scenariusze, które należy uwzględnić podczas decydowania odpowiadanie na błąd powtarzający operację:

*   Jest to problem izolowanym i kolejnych żądań wysyłanych do punktu końcowego podstawowego nie zwróci błąd powtarzający operację. Przykładem gdy taka sytuacja może wystąpić jest błąd sieci.

    W tym scenariuszu istnieje żadne pogorszenie wydajności znaczących w o **LocationMode** ustawioną **PrimaryThenSecondary** jak tylko dzieje się rzadko.

*   Jest to problem z co najmniej jedną z usług magazynu w regionie podstawowym i wszystkie kolejne żądania z usługą w regionie podstawowym mogą zwracać powtarzający operację błędów w danym okresie czasu. Na przykład jest całkowicie niedostępne regionu podstawowego.

    W tym scenariuszu istnieje spadek wydajności, ponieważ wszystkich żądań odczytu zostanie najpierw spróbuj podstawowy punkt końcowy, poczekaj, aż wygaśnie limit czasu, a następnie przełączyć się do dodatkowej punktu końcowego.

W tych sytuacjach należy zidentyfikować z trwającej problem z punktem końcowym podstawowego i wysyłać żądania bezpośrednio do punktu końcowego dodatkowej wszystkie odczytu, ustawiając **LocationMode** właściwości **SecondaryOnly** . W tej chwili możesz również zmienić aplikację do uruchamiania w trybie tylko do odczytu. Takie podejście jest nazywany [wzorzec wyłącznika](https://msdn.microsoft.com/library/dn589784.aspx).

### <a name="update-requests"></a>Żądań aktualizacji

Wzorzec wyłącznika można zastosować w taki sposób, aby zaktualizować żądania. Jednak żądania aktualizacji nie może zostać przekierowane do magazynu pomocniczego, co jest tylko do odczytu. Dla tych wymagań, należy pozostawić **LocationMode** ustawioną właściwość **PrimaryOnly** (ustawienie domyślne). Do obsługi tych błędów, możesz dotyczą metrykę te żądania — takie jak 10 błędów w wierszu — i po spełnieniu Twojej próg przełączanie aplikacji do trybu tylko do odczytu. Te same metody dla zwracania umożliwia tryb jak te opisane poniżej w następnej sekcji o wzorcu wyłącznika aktualizacji.

## <a name="circuit-breaker-pattern"></a>Wzorzec wyłącznika

W aplikacji przy użyciu wzorca wyłącznika może uniemożliwić ich ponowną próbą wykonania operacji, która może zakończyć się niepowodzeniem wielokrotnie. Umożliwia to aplikacji kontynuować uruchamianie, a nie zajmuje czasu podczas operacji są wykładniczo ponowione. Ta funkcja wykrywa także gdy stałe usterki, które aplikacji można spróbuj ponownie wykonać operację.

### <a name="how-to-implement-the-circuit-breaker-pattern"></a>Jak zaimplementować wzorzec wyłącznika

Aby zidentyfikować trwającą problemie z podstawowy punkt końcowy, można monitorować, jak często klient wystąpią błędy powtarzający operację. Ponieważ każdy przypadek jest inny, masz podejmowanie decyzji o wartości progowej, którego chcesz użyć przy podejmowaniu decyzji, aby przełączyć się do dodatkowej punktu końcowego i uruchomić aplikację w trybie tylko do odczytu. Na przykład można zdecydować, że wykonać przełącznik 10 błędy występują w wierszu z nie sukcesy. Innym przykładem jest przełączenie w przypadku 90% żądań w okresie 2-minutowy zakończyć się niepowodzeniem.

Dla pierwszego scenariusza można po prostu przechowuje licznika niepowodzeń i jeśli został pomyślnie przed osiągnięciem wartość maksymalna, ustaw liczbę wstecz od zera. Drugi scenariusz jednym ze sposobów ją wdrożyć ma używać obiektu MemoryCache (w .NET). Dla każdego żądania Dodaj niż CacheItem do pamięci podręcznej, ustaw wartość Powodzenie (1) lub Niepowodzenie (0) i ustaw czas wygaśnięcia do dwóch minut od teraz (lub niezależnie od jest Twoje ograniczenia czasu). Po osiągnięciu czas wygaśnięcia wpisu wpis zostanie automatycznie usunięta. Zapewni to stopniowe okna 2-minutowy. Zawsze, gdy wyślesz żądanie z usługą Magazyn należy najpierw użyj zapytań Linq na obiektu MemoryCache do obliczania procentu Powodzenie przez zsumowanie wartości i dzielenie przez wartość licznika. Jeśli procent powodzenia spadnie poniżej progu, niektóre (na przykład 10%), należy skonfigurować **LocationMode** żądania właściwości do odczytu do **SecondaryOnly** i przełączyć aplikację do trybu tylko do odczytu, przed kontynuowaniem.

Próg błędów używany do określenia, kiedy należy utworzyć przełącznik może się różnić z usługami w aplikacji, więc należy rozważyć, dzięki czemu można konfigurować parametry. Dotyczy to również decyzję dotyczącą obsługi powtarzający operację błędów z każdej usługi osobno lub jako jedna, zgodnie z opisem wcześniej.

Kolejnym zagadnieniem jest sposób obsługi wielu wystąpień aplikacji, co należy zrobić po wykryciu powtarzającego operację błędów w każdym wystąpieniu. Na przykład może być uruchomiony z tej samej aplikacji, które są ładowane 20 maszyn wirtualnych. Czy można obsługiwać każde wystąpienie osobno? Jeśli jedno wystąpienie rozpoczyna się problemy, ma ograniczenie odpowiedzi tylko jednego wystąpienia albo czy chcesz spróbować ma wszystkich wystąpień odpowiedzi w taki sam sposób, jeśli jedno wystąpienie ma problem? Obsługa wystąpień oddzielnie jest znacznie prostsze niż w trakcie koordynować odpowiedzi w ich, ale jak to zrobić zależy od architektury aplikacji.

### <a name="options-for-monitoring-the-error-frequency"></a>Opcje monitorowania częstotliwość błędów

Możliwe są trzy główne monitorowania częstotliwość ponownych prób w regionie podstawowym w celu ustalenia, kiedy należy przełączyć się do regionu pomocniczego i Zmień aplikację do uruchamiania w trybie tylko do odczytu.

*   Dodaj obsługę [ **ponawianie** ](http://msdn.microsoft.com/library/microsoft.windowsazure.storage.operationcontext.retrying.aspx) zdarzenia w [ **elementu OperationContext** ](http://msdn.microsoft.com/library/microsoft.windowsazure.storage.operationcontext.aspx) żądań przekazania do magazynu obiektu — jest to metoda wyświetlane w tym artykule i używane w przykładowym towarzyszące. Te zdarzenia wyzwalać zawsze, gdy klient ponawia próbę żądania, dzięki któremu można śledzić, jak często klient napotkał błędy powtarzający operację na podstawowy punkt końcowy.

    ```csharp 
    operationContext.Retrying += (sender, arguments) =>
    {
        // Retrying in the primary region
        if (arguments.Request.Host == primaryhostname)
            ...
    };
    ```

*   W [ **Evaluate** ](http://msdn.microsoft.com/library/microsoft.windowsazure.storage.retrypolicies.iextendedretrypolicy.evaluate.aspx) metody w zasadach niestandardowych ponownych prób, można uruchomić niestandardowego kodu przy każdym ponowna próba ma miejsce. Oprócz rejestrowania po ponowieniu próby odbywa się, zapewnia to również możliwość modyfikacji zachowania użytkownika spróbuj ponownie.

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

*   Trzeci podejściem jest wdrożenie niestandardowych składników monitorowania w aplikacji, która stale wysyła pakiet usługi ping punktu końcowego podstawowego magazynu z manekina odczytu żądań (takich jak odczytywanie małych obiektów blob) do określenia jej kondycji. To może potrwać niektórych zasobów, ale nie znacznej ilości. Gdy problem zostanie odnaleziony, który osiągnie Twojej próg, następnie przeprowadza przełącznika tak, aby **SecondaryOnly** i w trybie tylko do odczytu.

W pewnym momencie można wrócić do przy użyciu podstawowego punktu końcowego, dzięki czemu aktualizacji. Jeśli przy użyciu jednej z dwóch pierwszych metod wymienionych powyżej, może po prostu przejdź do podstawowego punktu końcowego i włączyć tryb aktualizacji po przeprowadzono dowolnie wybranego przedziału czasu lub liczba operacji. Następnie można pozostawić ją ponownie przejść przez logikę ponawiania. Jeśli problem został rozwiązany, będzie używać podstawowego punktu końcowego i zezwolić na aktualizacje. Jeżeli problem nadal występuje, jego zostanie ponownie przełącz się do dodatkowej punktu końcowego i w trybie tylko do odczytu po nieudanym ustawionych kryteriów.

Trzeci scenariusz, gdy polecenie ping końcowego podstawowego magazynu staje się pomyślnie, możesz wyzwolić przełącznika do **PrimaryOnly** i kontynuować stosowanie aktualizacji.

## <a name="handling-eventually-consistent-data"></a>Obsługa ostatecznie spójność danych

Działanie magazynu RA-GRS polega na replikowaniu transakcji z regionu podstawowego do pomocniczego. Ten proces replikacji gwarantuje, że dane w regionie pomocniczym są *ostatecznie spójne*. Oznacza to, że wszystkie transakcje w regionie podstawowym ostatecznie pojawią się w regionie pomocniczym, ale może wystąpić opóźnienie przed wyświetleniem i że nie ma żadnej gwarancji, transakcje przychodzą w regionie pomocniczym w tej samej kolejności jak w którym one były pierwotnie stosowane w regionie podstawowym. Jeśli transakcje przychodzą w regionie pomocniczym poza kolejnością, możesz *może* należy wziąć pod uwagę dane w regionie pomocniczym, aby być w stanie niespójnym, dopóki wyrównania usługi.

W poniższej tabeli przedstawiono przykład co może się zdarzyć, gdy aktualizacja Szczegóły pracownika, aby jej członkiem *Administratorzy* roli. Ze względu na przykład wymaga aktualizacji **pracownika** jednostki i aktualizacji **roli administrator** jednostki wraz z liczbą całkowitą liczbę administratorów. Zwróć uwagę, jak aktualizacje są stosowane poza kolejnością w regionie pomocniczym.

| **Czas** | **Transakcji**                                            | **Replikacja**                       | **Czas ostatniej synchronizacji** | **Result** |
|----------|------------------------------------------------------------|---------------------------------------|--------------------|------------| 
| T0       | Transakcja A: <br> Wstaw pracownika <br> jednostki w podstawowej |                                   |                    | Dodaje podstawowym, A transakcji<br> nie jeszcze zreplikowane. |
| T1       |                                                            | Transakcja A <br> Replikacja<br> pomocnicze | T1 | Replikowane do dodatkowej A transakcji. <br>Czas ostatniej synchronizacji aktualizacji.    |
| T2       | Transakcja B:<br>Aktualizacja<br> Jednostka pracownika<br> w podstawowej  |                                | T1                 | Transakcja B zapisywane w podstawowym<br> nie jeszcze zreplikowane.  |
| T3       | Transakcja C:<br> Aktualizacja <br>administrator<br>Jednostka roli w<br>podstawowe |                    | T1                 | Transakcja zapisywane do podstawowych, C<br> nie jeszcze zreplikowane.  |
| *T4*     |                                                       | Transakcja C <br>Replikacja<br> pomocnicze | T1         | Transakcja C replikowane do dodatkowej.<br>Nie zaktualizowano ponieważ LastSyncTime <br>Transakcja B nie został jeszcze zreplikowany.|
| *T5*     | Odczyt jednostek <br>pomocniczej                           |                                  | T1                 | Przestarzała wartość dla pracowników <br> jednostki, ponieważ nie transakcji B <br> jeszcze zreplikowane. Pobierz nową wartość<br> Administrator roli jednostki, ponieważ ma C<br> zreplikowane. Czas ostatniej synchronizacji nadal nie.<br> zostały zaktualizowane, ponieważ transakcja B<br> nie replikowane. Można określić<br>Jednostka roli administratora jest niespójna <br>ponieważ entity Data/godzina jest po <br>Czas ostatniej synchronizacji. |
| *T6*     |                                                      | Transakcja B<br> Replikacja<br> pomocnicze | T6                 | *T6* — wszystkie transakcje za pomocą C <br>zostały zreplikowane, czas ostatniej synchronizacji<br> jest aktualizowana. |

W tym przykładzie przyjęto założenie, że klient przełącza się do odczytu z regionu pomocniczego w T5. Można pomyślnie odczytać **roli administrator** jednostki, w tym momencie, ale jednostka zawiera wartość liczby Administratorzy, który nie jest zgodny z numerem **pracownika** jednostek, które są w tym momencie, oznaczona jako administratorów w regionie pomocniczym. Klient może po prostu wyświetlaj tej wartości, z ryzyko, że jest niespójne informacje. Alternatywnie klient może próbować ustalić, który **roli administrator** jest w stanie potencjalnie niespójności, ponieważ aktualizacje wystąpiło poza kolejnością, a następnie powiadamia użytkownika o tym fakcie.

Rozpoznanie, że ma ona potencjalnie niespójne dane, klient może używać wartości *czas ostatniej synchronizacji* czy można uzyskać w dowolnym momencie przez zapytanie do usługi magazynu. Oznacza to, podczas gdy dane w regionie pomocniczym został ostatnio spójne i usługa ma stosowania wszystkich transakcji przed tego punktu w czasie. W przykładzie przedstawionym powyżej, po wstawia usługę **pracownika** jednostki w regionie pomocniczym, czas ostatniej synchronizacji ma ustawioną wartość *T1*. Pozostaje w *T1* do aktualizacji usługi **pracownika** jednostki w regionie pomocniczym, gdy jest równa *T6*. Jeśli klient pobiera czas ostatniej synchronizacji, gdy odczytuje jednostki po *T5*, można porównać ją z sygnaturą czasową w ramach jednostki. Jeśli znacznik czasu jednostki jest późniejsza niż godzina ostatniej synchronizacji, następnie jednostka jest w stanie potencjalnie niespójności, a może zająć niezależnie od jest odpowiednie działanie aplikacji. To pole wymaga znać ukończenia ostatniej aktualizacji do podstawowych.

## <a name="testing"></a>Testowanie

Należy przetestować, czy aplikacja działa zgodnie z oczekiwaniami w przypadku wykrycia błędów powtarzający operację. Na przykład należy przeprowadzić testy, kiedy regionu podstawowego stają się dostępne ponownie tworzenie kopii przełączniki aplikacji na serwerze pomocniczym, jak i w trybie tylko do odczytu, gdy wykryje problem i przełączniki. Aby to zrobić, należy do symulowania powtarzający operację błędów i kontroli, jak często występują.

Można użyć [Fiddler](http://www.telerik.com/fiddler) do przechwycenia i modyfikowania odpowiedzi HTTP w skrypcie. Ten skrypt można zidentyfikować odpowiedzi, które pochodzą od podstawowego punktu końcowego i zmień kod stanu HTTP na Biblioteka klienta magazynu jest rozpoznawany jako Błąd powtarzający operację. Następujący fragment kodu przedstawiono prosty przykład skryptu Fiddler, który przechwytuje odpowiedzi na żądanie dla odczytu **employeedata** tabeli ma zostać zwrócony stan 502:

```java
static function OnBeforeResponse(oSession: Session) {
    ...
    if ((oSession.hostname == "\[yourstorageaccount\].table.core.windows.net")
      && (oSession.PathAndQuery.StartsWith("/employeedata?$filter"))) {
        oSession.responseCode = 502;
    }
}
```

Można rozszerzyć w tym przykładzie przechwycenia większej liczby żądań i zmieniać tylko **responseCode** na niektórych z nich, aby lepiej symulować rzeczywistych scenariuszy. Aby uzyskać więcej informacji dotyczących dostosowywania Fiddler skryptów, zobacz [modyfikowanie żądania lub odpowiedzi](http://docs.telerik.com/fiddler/KnowledgeBase/FiddlerScript/ModifyRequestOrResponse) w dokumentacji narzędzia Fiddler.

Jeśli zostały wprowadzone do przełączenia aplikacji do trybu tylko do odczytu można skonfigurować progi, będzie łatwiejsze testowanie zachowanie w przypadku woluminów transakcji nieprodukcyjnych.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji o dostęp do odczytu-nadmiarowość geograficzna, łącznie z innym przykładem konfiguracji LastSyncTime, zobacz [opcje nadmiarowość magazynu Azure dla systemu Windows i dostęp do odczytu magazynu geograficznie nadmiarowego](https://blogs.msdn.microsoft.com/windowsazurestorage/2013/12/11/windows-azure-storage-redundancy-options-and-read-access-geo-redundant-storage/).

* Kompletnego przykładu przedstawiający sposób wprowadzania przełącznika i z powrotem między punktami końcowymi podstawowe i pomocnicze, zobacz [przykłady Azure — przy użyciu wzorca wyłącznika z magazynem RA-GRS](https://github.com/Azure-Samples/storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs).
