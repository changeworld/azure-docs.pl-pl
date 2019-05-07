---
title: Projektowanie Aaplications o wysokiej dostępności przy użyciu magazynu geograficznie nadmiarowego dostęp do odczytu (RA-GRS) | Dokumentacja firmy Microsoft
description: Jak używać magazynu Azure RA-GRS architektury wystarczająco elastyczny, aby obsłużyć awarie aplikacji o wysokiej dostępności.
services: storage
author: tamram
ms.service: storage
ms.devlang: dotnet
ms.topic: article
ms.date: 01/17/2019
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 6dc497ac2afd54965485ff553bb25f47d7cf0491
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65139343"
---
# <a name="designing-highly-available-applications-using-ra-grs"></a>Projektowanie wysoko dostępnych aplikacji przy użyciu RA-GRS

Wspólną cechą infrastruktury opartej na chmurze, takich jak Azure Storage jest zapewniają platforma o wysokiej dostępności do obsługi aplikacji. Deweloperzy aplikacji opartych na chmurze należy starannie rozważ, jak korzystać z tej platformy, aby dostarczać aplikacje o wysokiej dostępności dla swoich użytkowników. Ten artykuł koncentruje się na jak deweloperzy mogą używać dostęp do odczytu magazynu geograficznie nadmiarowego (RA-GRS) w celu zapewnienia wysoko dostępnych aplikacji usługi Azure Storage.

[!INCLUDE [storage-common-redundancy-options](../../../includes/storage-common-redundancy-options.md)]

Ten artykuł koncentruje się na GRS i RA-GRS. W przypadku magazynu GRS trzy kopie danych są przechowywane w regionie podstawowym, które zostały wybrane podczas konfigurowania konta magazynu. Trzy dodatkowe kopie są obsługiwane asynchronicznie w regionie pomocniczym oddalonym o określony przez platformę Azure. RA-GRS zapewnia magazyn geograficznie nadmiarowy z dostępem do odczytu kopię pomocniczą.

Aby uzyskać informacje o tym, które głównej regionów są skojarzone z jakich dodatkowych regionach zobacz [firm ciągłości działania i odzyskiwania po awarii (BCDR): Sparowanych regionów platformy Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

Brak fragmentów kodu zawartych w tym artykule oraz link do pełny przykład na końcu, które można pobierać i uruchamiać.

> [!NOTE]
> Usługa Azure Storage obsługuje teraz magazyn strefowo nadmiarowy (ZRS) do tworzenia wysoko dostępnych aplikacji. Magazyn ZRS zapewnia proste rozwiązanie dla wielu aplikacji na potrzeby nadmiarowości. Magazyn ZRS zapewnia ochronę przed awariami sprzętu lub poważnej awarii wpływających na jednym centrum danych. Aby uzyskać więcej informacji, zobacz [magazyn strefowo nadmiarowy (ZRS): Aplikacje usługi Azure Storage o wysokiej dostępności](storage-redundancy-zrs.md).

## <a name="key-features-of-ra-grs"></a>Najważniejsze funkcje RA-GRS

Pamiętać o tych kluczowych zagadnieniach podczas projektowania aplikacji w przypadku usługi RA-GRS:

* Usługa Azure Storage przechowuje kopię tylko do odczytu danych, które są przechowywane w regionie podstawowym w regionie pomocniczym. Jak wspomniano powyżej, usługa storage Określa lokalizację w regionie pomocniczym.

* Kopia tylko do odczytu jest [ostatecznie spójną](https://en.wikipedia.org/wiki/Eventual_consistency) z danymi w regionie podstawowym.

* Obiekty BLOB, tabel i kolejek, można tworzyć zapytania dodatkowym regionem *czas ostatniej synchronizacji* wartość, która informuje, kiedy wystąpił ostatniej replikacji z serwera podstawowego do regionu pomocniczego. (Jest to nieobsługiwane dla usługi Azure Files, która nie ma nadmiarowości RA-GRS w tej chwili.)

* Biblioteki klienta usługi Storage służy do interakcji z danymi w regionie podstawowym lub pomocniczym. Można również wykonać przekierowanie liczbę żądań odczytu automatycznie do regionu pomocniczego, jeśli upłynie limit czasu odczytu żądania do regionu podstawowego.

* Jeśli region podstawowy staje się niedostępny, należy zainicjować trybu failover konta. Przechodzenia w tryb failover do regionu pomocniczego, wpisy DNS, wskazując do regionu podstawowego zostaną zmienione aby wskazywały do regionu pomocniczego. Po zakończeniu pracy w trybie failover, po przywróceniu dostęp do zapisu dla kont GRS i RA-GRS. Aby uzyskać więcej informacji, zobacz [awaryjnego odzyskiwania i przechowywania konta pracy awaryjnej (wersja zapoznawcza) w usłudze Azure Storage](storage-disaster-recovery-guidance.md).

## <a name="application-design-considerations-when-using-ra-grs"></a>Zagadnienia dotyczące projektowania aplikacji przy użyciu RA-GRS

Ten artykuł ma na celu dowiesz się, jak zaprojektować aplikację, która będzie działać (chociaż są w ograniczonym zakresie), nawet w przypadku awarii w podstawowym centrum danych. Można zaprojektować aplikację w celu obsługi problemy przejściowe lub długotrwałe, czytając z regionu pomocniczego, gdy występuje problem, który będzie zakłócać odczytu z regionu podstawowego. Region podstawowy będzie znowu dostępna, aplikacja może zwrócić do odczytu z regionu podstawowego.

### <a name="using-eventually-consistent-data"></a>Korzystanie z danych ostatecznie spójną

Proponowane rozwiązanie zakłada, że jest zgodna z potencjalnie nieodświeżoną dane są zwracane do aplikacji wywołującej. Ponieważ dane w regionie pomocniczym ostatecznie spójną, istnieje możliwość, region podstawowy może stać się niedostępne, zanim zakończy aktualizację do regionu pomocniczego podczas replikacji.

Na przykład załóżmy, że klient prześle aktualizację pomyślnie, ale region podstawowy ulegnie awarii przed propagacji aktualizacji do regionu pomocniczego. Prosi klienta do odczytywania danych ponownie, użytkownik otrzymuje nieaktualnych danych z regionu pomocniczego, zamiast zaktualizowane dane. Podczas projektowania aplikacji, należy zdecydować, czy jest to dopuszczalne, a jeśli tak, jak komunikat z klienta zostaną. 

W dalszej części tego artykułu pokazujemy, jak sprawdzić czas ostatniej synchronizacji dla dodatkowych danych, aby sprawdzić, czy pomocnicza jest aktualny.

### <a name="handling-services-separately-or-all-together"></a>Obsługa usług, oddzielnie lub wszystko

Gdy jest to mało prawdopodobne, możliwe jest na jednej usługi staną się niedostępne, podczas gdy inne usługi są nadal w pełni funkcjonalne. Może obsługiwać ponownych prób i w trybie tylko do odczytu dla każdej usługi oddzielnie (obiekty BLOB, kolejek, tabel) lub może obsługiwać ponowne próby ogólnie dla wszystkich usług magazynu ze sobą.

Na przykład użycie kolejek i obiektów blob w aplikacji, może zdecydujesz się umieścić w osobnego kodu do obsługi błędów powtarzający operację dla każdego z nich. Następnie jeśli ponowienie próby jest pobieranie z usługi obiektów blob, ale nadal działa usługa kolejki, będzie mieć wpływ tylko część aplikacji, który obsługuje obiekty BLOB. Jeśli zdecydujesz się obsługiwać wszystkie próby połączenia z usługi storage ogólnie, wywołanie usługi blob service zwraca błąd umożliwiający ponowienie próby żądania do usługi obiektów blob i kolejki usługi będzie mieć wpływ.

Ostatecznie zależy to od stopnia złożoności aplikacji. Użytkownik może zrezygnować z obsłużyć awarie przez usługę, ale zamiast tego do przekierowywania żądań dla wszystkich usług magazynu w regionie pomocniczym odczytu i uruchomić aplikację w trybie tylko do odczytu, gdy wykryją problem z dowolnej usługi magazynu w regionie podstawowym.

### <a name="other-considerations"></a>Inne zagadnienia

Są to inne zagadnienia, które omówimy w dalszej części tego artykułu.

*   Obsługuje ponawianie żądań odczytu przy użyciu wzorca wyłącznika

*   Ostatecznie spójną data i godzina ostatniej synchronizacji

*   Testowanie

## <a name="running-your-application-in-read-only-mode"></a>Aplikacja jest uruchamiana w trybie tylko do odczytu

Aby użyć magazynu RA-GRS, musi być w stanie obsłużyć zarówno w przypadku nieudanych żądań odczytu i aktualizacji żądania zakończone niepowodzeniem (za pomocą aktualizacji w tym przypadku oznacza wstawienia, aktualizacje i usunięcia). Jeśli podstawowe Centrum kończy się niepowodzeniem, zapoznaj się z żądania mogą zostać przekierowane do dodatkowego centrum danych. Jednak żądań aktualizacji nie można przekierować do regionu pomocniczego, ponieważ pomocnicza jest tylko do odczytu. Z tego powodu należy tak zaprojektować aplikację do uruchamiania w trybie tylko do odczytu.

Na przykład można ustawić flagi, który jest sprawdzany przed przesłaniem do usługi Azure Storage wszelkie żądania aktualizacji. Gdy jedno z żądań aktualizacji przepływa, możesz pominąć go i zwraca odpowiednią odpowiedź do klienta. Możesz nawet chcesz wyłączyć niektórych funkcji całkowicie, dopóki ten problem zostanie rozwiązany i Powiadom użytkowników, że te funkcje są tymczasowo niedostępne.

Jeśli zdecydujesz się oddzielnie obsługi błędów dla każdej usługi, należy również obsługiwać możliwość uruchamiania aplikacji w trybie tylko do odczytu przez usługę. Na przykład może mieć flagi tylko do odczytu dla wszystkich usług, które mogą być włączone i wyłączone. Następnie możesz obsłużyć flagi w odpowiednich miejscach w kodzie.

Możliwość uruchomienia aplikacji w trybie tylko do odczytu jest kolejną korzyścią po stronie — daje możliwość zapewnić ograniczoną funkcjonalność podczas istotnego uaktualnienia aplikacji. Można uruchomić aplikację do uruchamiania w trybie tylko do odczytu i wskaż dodatkowym centrum danych, zapewnienie, że nikt nie uzyskuje dostęp do danych w regionie podstawowym, gdy wprowadzasz uaktualnienia.

## <a name="handling-updates-when-running-in-read-only-mode"></a>Obsługa aktualizacji, podczas pracy w trybie tylko do odczytu

Istnieje wiele sposobów w celu obsługi żądań aktualizacji podczas pracy w trybie tylko do odczytu. Firma Microsoft nie będzie to kompleksowo obejmują, ale ogólnie rzecz biorąc, istnieje kilka wzorców, które należy wziąć pod uwagę.

1.  Można odpowiedzieć na użytkowników i poinformuj go, że użytkownik nie może obecnie zgłaszać aktualizacje. Na przykład system zarządzania kontaktami, można włączyć klienci mogą uzyskiwać dostęp do informacji kontaktowych, ale nie wprowadzać aktualizacje.

2.  Możesz umieścić w kolejce aktualizacje w innym regionie. W tym przypadku będzie zapisu oczekujących żądań aktualizacji do kolejki w innym regionie, a następnie istnieje sposób przetwarzania te żądania po podstawowe centrum danych przejściu do trybu online ponownie. W tym scenariuszu należy pozwolić klienta, o których wiadomo, że żądana aktualizacja znajduje się w kolejce do późniejszego przetwarzania.

3.  Można napisać aktualizacje z kontem magazynu w innym regionie. Następnie, jeśli podstawowe centrum danych powróci do trybu online, masz sposób, aby scalić te aktualizacje danych głównych, w zależności od struktury danych. Na przykład tworzysz oddzielnych plików z sygnaturą daty/godziny w nazwie, można skopiować te pliki do regionu podstawowego. Działa to w przypadku niektórych obciążeń, takich jak dane rejestrowania i iOT.

## <a name="handling-retries"></a>Obsługa ponownych prób

Jak można dowiedzieć się, które błędy są powtarzający operację? Jest to określane przez bibliotekę klienta usługi storage. Na przykład błąd 404 (nie znaleziono zasobu) nie jest powtarzający operację, ponieważ ponawianie próby jego nie spowodować Powodzenie. Z drugiej strony 500 Błąd jest powtarzający operację, ponieważ jest to błąd serwera i po prostu może być przejściowy problem. Aby uzyskać więcej informacji, zapoznaj się z [Otwórz kod źródłowy dla klasy ExponentialRetry](https://github.com/Azure/azure-storage-net/blob/87b84b3d5ee884c7adc10e494e2c7060956515d0/Lib/Common/RetryPolicies/ExponentialRetry.cs) w bibliotece klienta .NET magazynu. (Zapoznaj się z metody ShouldRetry.)

### <a name="read-requests"></a>Żądania odczytu

Żądania odczytu mogą zostać przekierowane do magazynu pomocniczego, jeśli występuje problem z magazynu głównego. Jak wspomniano powyżej w [przy użyciu ostatecznie danych ze stałymi](#using-eventually-consistent-data), musi być możliwa do aplikacji można odczytać potencjalnie nieaktualnych danych. Korzystając z biblioteki klienta usługi storage na dostęp do danych RA-GRS, można określić zachowanie ponawiania żądanie odczytu, ustawiając wartość dla **LocationMode** właściwości do jednej z następujących czynności:

*   **PrimaryOnly** (ustawienie domyślne)

*   **PrimaryThenSecondary**

*   **SecondaryOnly**

*   **SecondaryThenPrimary**

Po ustawieniu **LocationMode** do **PrimaryThenSecondary**, jeśli początkowe żądanie odczytu do podstawowego punktu końcowego kończy się niepowodzeniem z powodu błędu powtarzającego operację klienta automatycznie wysyła kolejne żądanie odczytu pomocniczy punkt końcowy. Jeśli ten błąd jest limit czasu serwera, klient będzie mieć czekać na limit czasu wygaśnięcia przed odbierze błąd powtarzający operację usługi.

Istnieją dwa scenariusze, aby wziąć pod uwagę przy wyborze sposobu reagowania na błąd powtarzający operację:

*   Jest to problem izolowane i kolejne żądania do podstawowego punktu końcowego nie zwróci błąd umożliwiający ponowienie próby. Przykład, w którym może się to zdarzyć jest, gdy występuje błąd przejściowe problemy z siecią.

    W tym scenariuszu, że jest żadnego pogorszenia wydajności znaczące **LocationMode** równa **PrimaryThenSecondary** jak dzieje się to tylko rzadko.

*   Jest to problem z co najmniej jeden z usług magazynu w regionie podstawowym, wszystkie kolejne żądania do tej usługi w regionie głównym może być zwracane błędy powtarzający operację w okresie czasu. Na przykład to, czy region podstawowy jest całkowicie niedostępne.

    W tym scenariuszu istnieje spadek wydajności, ponieważ wszystkie żądania odczytu zostanie najpierw spróbuj podstawowego punktu końcowego, poczekaj na wartość limitu czasu wygaśnięcia, a następnie przełącz się do pomocniczego punktu końcowego.

Dla tych scenariuszy należy zidentyfikować, który trwającą problem z podstawowego punktu końcowego i wysyłania żądania bezpośrednio do pomocniczego punktu końcowego wszystkich odczytu przez ustawienie **LocationMode** właściwość **SecondaryOnly** . W tym momencie możesz również zmienić aplikację do uruchamiania w trybie tylko do odczytu. To podejście jest nazywane [wzorzec wyłącznika](/azure/architecture/patterns/circuit-breaker).

### <a name="update-requests"></a>Żądania aktualizacji

Wzorzec wyłącznika można zastosować w taki sposób, aby zaktualizować żądania. Jednak żądań aktualizacji nie może zostać przekierowany do magazynu pomocniczego, który jest tylko do odczytu. Dla tych żądań, należy pozostawić **LocationMode** właściwością **PrimaryOnly** (ustawienie domyślne). Do obsługi tych błędów, można zastosować metryki na te żądania — na przykład 10 błędów z rzędu — i po spełnieniu próg Przełącz aplikację w trybie tylko do odczytu. Te same metody dla zwracania umożliwia zaktualizować trybu, jak te opisane poniżej w następnej sekcji o wzorzec wyłącznika.

## <a name="circuit-breaker-pattern"></a>Wzorzec wyłącznika

W aplikacji przy użyciu wzorca wyłącznika może uniemożliwić ich ponawiając operację, która prawdopodobnie się nie powiedzie wielokrotnie. Umożliwia aplikacji dalsze działanie, a nie zajmuje czasu podczas wykonywania tej operacji zostanie ponowiony wykładniczo. Wykrywa także rozwiązywania błędu, co aplikacja może spróbować ponownie wykonać operację ponownie.

### <a name="how-to-implement-the-circuit-breaker-pattern"></a>Jak zaimplementować wzorzec wyłącznika

Aby określić, czy jest trwającego problemu z podstawowego punktu końcowego, można monitorować, jak często klient napotka błędy powtarzający operację. Ponieważ każdy przypadek jest inny, musisz wybrać wartość progową, którego chcesz używać dla decyzję, aby przełączyć się do pomocniczego punktu końcowego, a następnie uruchom aplikację w trybie tylko do odczytu. Na przykład można zdecydować, że wykonać przełącznika, jeśli 10 błędy występują w wierszu z nie powodzenia. Innym przykładem jest przełącznika, jeśli 90% firm z żądań w 2 minuty kończą się niepowodzeniem.

Dla pierwszego scenariusza można po prostu liczbę awarii i w przypadku powodzenia przed osiągnięciem maksymalna, liczba ponownie ustawić na zero. W drugim scenariuszu jednym ze sposobów jego wdrożenia jest użycie obiektu MemoryCache (na platformie .NET). Dla każdego żądania Dodaj niż CacheItem do pamięci podręcznej, ustaw wartość sukcesu (1) lub się nie powieść (0) i ustaw czas wygaśnięcia do 2 minut od teraz (lub niezależnie od rodzaju jest swoje ograniczenia czasu). Po osiągnięciu czas wygaśnięcia wpisu wpis zostanie automatycznie usunięty. Zapewni to stopniowe oknie 2-minutowy. Każdorazowo, gdy wysłać żądanie do usługi storage najpierw umożliwia zapytania Linq w obiekcie MemoryCache obliczyć procent sukcesu przez zsumowanie wartości, a następnie dzieląc przez liczbę. Procent sukcesu spadnie poniżej progu, niektóre (np. 10%), ustawić **LocationMode** właściwości do odczytu żądania **SecondaryOnly** i przełączanie aplikacji w trybie tylko do odczytu, przed kontynuowaniem.

Próg błędów, które umożliwiają ustalenie, kiedy należy utworzyć przełącznik mogą się różnić z usługami w aplikacji, więc należy rozważyć, dzięki czemu można skonfigurować parametry. Jest to również należy podjąć decyzję do obsługi błędów powtarzający operację z każdej usługi osobno lub jako jedno, jak omówiono wcześniej.

Inną ważną kwestią jest sposób obsługi wielu wystąpień aplikacji, co można zrobić podczas wykrywania błędów powtarzający operację w każdym wystąpieniu. Na przykład masz 20 maszyn wirtualnych z tej samej aplikacji, które są ładowane. Czy można obsługiwać każde wystąpienie osobno? Jeśli jedno wystąpienie rozpoczyna się problemy, czy chcesz ograniczyć odpowiedzi do tylko jednego wystąpienia lub czy chcesz spróbować mają wszystkie wystąpienia reagować w taki sam sposób, gdy jedno wystąpienie ma problem? Obsługa oddzielnie wystąpienia jest znacznie prostsze niż podjęcie próby do koordynowania ich odpowiedzi, ale jak to zrobić zależy od architektury aplikacji.

### <a name="options-for-monitoring-the-error-frequency"></a>Opcje monitorowania częstotliwość błędów

Dostępne są trzy główne opcje monitorowania częstotliwość ponownych prób w regionie podstawowym w celu ustalenia, kiedy należy przełączyć się do regionu pomocniczego i zmienić aplikację do uruchamiania w trybie tylko do odczytu.

*   Dodaj program obsługi [ **ponawianie próby** ](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.operationcontext.retrying.aspx) zdarzenie na [ **OperationContext** ](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.operationcontext.aspx) żądania są przekazywane do magazynu obiektów — jest to metoda wyświetlane w tym artykule i używane w towarzyszącej próbki. Te zdarzenia są uruchamiane zawsze, gdy klient będzie ponawiać próbę żądania, które pozwalają śledzić, jak często klient napotka błędy powtarzający operację na podstawowego punktu końcowego.

    ```csharp 
    operationContext.Retrying += (sender, arguments) =>
    {
        // Retrying in the primary region
        if (arguments.Request.Host == primaryhostname)
            ...
    };
    ```

*   W [ **Evaluate** ](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.retrypolicies.iextendedretrypolicy.evaluate.aspx) metody w niestandardowe zasady ponawiania, można uruchomić kod niestandardowy w każdym przypadku, gdy odbywa się ponowienie próby. Oprócz rejestrowania po ponowieniu próby miejsce, ta oferuje również możliwość modyfikacji swoje zachowanie ponawiania prób.

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

*   Trzeci podejściem jest implementacja monitorowania składnika niestandardowego w aplikacji, które stale wysyła pakiet usługi ping punktu końcowego podstawowego magazynu za pomocą dummy odczytu żądania (takich jak odczytywanie małych obiektów blob) do określenia jej kondycji. To może potrwać kilka zasobów, ale nie znaczną ilość. Gdy problem zostanie wykryta, która osiąga próg, można następnie wykonać po przełączeniu na użycie **SecondaryOnly** i w trybie tylko do odczytu.

W pewnym momencie można wrócić do przy użyciu podstawowego punktu końcowego, dzięki czemu aktualizacji. Jeśli przy użyciu jednej z dwóch pierwszych metod wymienionych powyżej, można po prostu przejdź do podstawowego punktu końcowego i włączyć tryb aktualizacji po dowolnie wybranego przedziału czasu i liczby operacji została wykonana. Następnie można zaczekać na jego przejście przez logikę ponawiania ponownie. Jeśli problem został rozwiązany, będzie używać podstawowego punktu końcowego i zezwala na aktualizacje. Jeśli problem nadal występuje, jego zostanie ponownie przełącz się do pomocniczego punktu końcowego i w trybie tylko do odczytu po nieudanym ustawionych kryteriów.

Trzeci scenariusz, gdy odpowiada na polecenie ping końcowego podstawowego magazynu staje się pomyślnie, możesz wyzwolić przełącznika do **PrimaryOnly** i Kontynuuj, dzięki czemu aktualizacji.

## <a name="handling-eventually-consistent-data"></a>Obsługa danych ostatecznie spójną

Działanie magazynu RA-GRS polega na replikowaniu transakcji z regionu podstawowego do pomocniczego. Ten proces replikacji gwarantuje, że dane w regionie pomocniczym są *ostatecznie spójną*. Oznacza to, że wszystkie transakcje w regionie podstawowym ostatecznie pojawią się w regionie pomocniczym, ale może wystąpić opóźnienie, zanim zostaną one wyświetlone i że nie ma żadnej gwarancji, transakcje pojawić się w regionie pomocniczym w kolejności, w której ich były pierwotnie stosowane w regionie podstawowym. Jeśli transakcje pojawić się w regionie pomocniczym poza kolejnością, możesz *może* należy wziąć pod uwagę dane w regionie dodatkowym, aby być w stanie niespójnym, dopóki wyrównywane do usługi.

W poniższej tabeli przedstawiono przykład co może się zdarzyć, gdy aktualizujesz Szczegóły pracownika, aby upewnić się, jej członkiem *Administratorzy* roli. Ze względu na tym przykładzie ta migracja wymaga aktualizacji **pracowników** jednostki i zaktualizuj **roli administrator** jednostki wraz z liczbą całkowitą liczbę administratorów. Zwróć uwagę, jak aktualizacje są stosowane poza kolejnością w regionie pomocniczym.

| **czas** | **Transakcja**                                            | **Replikacja**                       | **Czas ostatniej synchronizacji** | **wynik** |
|----------|------------------------------------------------------------|---------------------------------------|--------------------|------------| 
| T0       | Odp.: transakcji <br> Wstaw pracownika <br> jednostki w podstawowej |                                   |                    | Transakcja wstawione do głównej, A<br> nie jeszcze zreplikowane. |
| T1       |                                                            | Transakcja A <br> replikowane do<br> pomocniczy | T1 | Replikowane do dodatkowej A transakcji. <br>Czas ostatniej synchronizacji aktualizacji.    |
| T2       | Transakcja B:<br>Aktualizacja<br> Jednostka pracownika<br> w podstawowej  |                                | T1                 | Transakcja zapisywane do podstawowej, B<br> nie jeszcze zreplikowane.  |
| T3       | Transakcja C:<br> Aktualizacja <br>administrator<br>Jednostka roli w<br>podstawowa |                    | T1                 | Transakcja zapisywane do podstawowej, C<br> nie jeszcze zreplikowane.  |
| *T4*     |                                                       | Transakcja języka C <br>replikowane do<br> pomocniczy | T1         | Transakcja C replikowane do dodatkowej.<br>Nie zaktualizowano ponieważ LastSyncTime <br>Transakcja B nie zostały jeszcze zreplikowane.|
| *T5*     | Odczytaj jednostki <br>z pomocniczych                           |                                  | T1                 | Stare korzyści dla pracowników <br> jednostki, ponieważ nie transakcji B <br> jeszcze zreplikowane. Pobierz nową wartość dla<br> Administrator roli jednostki, ponieważ ma C<br> replikowane. Czas ostatniej synchronizacji nadal nie.<br> zostały zaktualizowane, ponieważ transakcja B<br> nie replikowane. Można stwierdzić<br>Administrator roli jednostki jest niespójna <br>ponieważ jednostka daty/godziny jest po <br>Czas ostatniej synchronizacji. |
| *T6*     |                                                      | Transakcja B<br> replikowane do<br> pomocniczy | T6                 | *T6* — ma wszystkie transakcje za pośrednictwem języka C <br>zostały zreplikowane czas ostatniej synchronizacji<br> jest aktualizowana. |

W tym przykładzie przyjęto założenie, że klient przełącza się do odczytu z regionu pomocniczego na T5. Stanie pomyślnie odczytywać **roli administrator** jednostki w tej chwili, ale jednostki zawiera wartość dla liczby administratorów, która nie jest spójna z liczbą **pracowników** jednostek, które są oznaczone jako administratorzy w dodatkowym regionie, w tym momencie. Twój klient po prostu można wyświetlić tę wartość, z ryzykiem jest niespójne informacje. Alternatywnie, klient mógłby próbować ustalenie, że **roli administrator** jest w stanie potencjalnie niespójne aktualizacje wystąpiło poza kolejnością, a następnie poinformowania użytkownika o tym fakcie.

Aby rozpoznać, że zawiera on danych potencjalnie niespójne, klient może używać wartości *czas ostatniej synchronizacji* , można uzyskać w dowolnym momencie przez zapytanie do usługi storage. Oznacza to, podczas gdy dane w regionie pomocniczym został ostatnio spójne i usługa była stosowania wszystkich transakcji przed tego punktu w czasie. W powyższym przykładzie, po wstawia usługę **pracowników** jednostki w regionie pomocniczym czas ostatniej synchronizacji jest ustawiona na *T1*. Pozostaje w *T1* do momentu aktualizacji usługi **pracowników** jednostki w regionie pomocniczym, gdy ma wartość *T6*. Jeśli klient pobiera czas ostatniej synchronizacji, gdy odczytuje jednostki po *T5*, można porównać ją z sygnaturą czasową w jednostce. Jeśli sygnatury czasowej w jednostce jest późniejsza niż godzina ostatniej synchronizacji, następnie jednostka jest w stanie potencjalnie niespójne i może potrwać, niezależnie od rodzaju są odpowiednie działanie aplikacji. Za pomocą tego pola wymaga, że znasz ukończenia ostatniej aktualizacji do podstawowej.

## <a name="testing"></a>Testowanie

Należy przetestować, że aplikacja zachowuje się zgodnie z oczekiwaniami, gdy napotka błędy powtarzający operację. Na przykład należy przetestować, że przełączniki aplikacji do regionu pomocniczego i w trybie tylko do odczytu, gdy wykryje problem i zmienia ponownie, gdy region podstawowy stanie się znowu dostępny. Aby to zrobić, potrzebny jest sposób symulacji powtarzający operację błędy i kontroli, jak często występują one.

Możesz użyć [Fiddler](https://www.telerik.com/fiddler) do przechwycenia i modyfikowania odpowiedzi HTTP w skrypcie. Ten skrypt można zidentyfikować odpowiedzi, które pochodzą z podstawowego punktu końcowego i zmień kod stanu HTTP na taki, który Biblioteka klienta magazynu jest rozpoznawany jako błąd umożliwiający ponowienie próby. Następujący fragment kodu przedstawia prosty przykład skryptu programu Fiddler, który przechwytuje odpowiedzi na żądania skierowane do odczytu **employeedata** tabeli próbę zwrócenia stanu 502:

```java
static function OnBeforeResponse(oSession: Session) {
    ...
    if ((oSession.hostname == "\[yourstorageaccount\].table.core.windows.net")
      && (oSession.PathAndQuery.StartsWith("/employeedata?$filter"))) {
        oSession.responseCode = 502;
    }
}
```

Można rozszerzyć w tym przykładzie przechwycenia szerszego zakresu żądań i zmieniać tylko **responseCode** na niektórych z nich w celu lepszej symulacji obciążenia w rzeczywistych scenariuszy. Aby uzyskać więcej informacji na temat dostosowywania programu Fiddler skryptów, zobacz [modyfikowanie żądania lub odpowiedzi](https://docs.telerik.com/fiddler/KnowledgeBase/FiddlerScript/ModifyRequestOrResponse) w dokumentacji programu Fiddler.

Jeśli zostały wprowadzone wartości progowe dla przełączania aplikacji do trybu tylko do odczytu można skonfigurować, będzie łatwiejsze testowanie zachowania przy użyciu woluminów transakcji nieprodukcyjnych.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji o dostęp do odczytu nadmiarowości geograficznej, łącznie z przykładem konfiguracji LastSyncTime, zobacz [Opcje nadmiarowości magazynu Azure Windows i dostęp do odczytu dla magazynu geograficznie nadmiarowego](https://blogs.msdn.microsoft.com/windowsazurestorage/2013/12/11/windows-azure-storage-redundancy-options-and-read-access-geo-redundant-storage/).

* Aby uzyskać pełny przykład przedstawiający sposób zmienić, i z powrotem między podstawowym i pomocniczym punktów końcowych, zobacz [Azure Samples — wzorzec wyłącznika przy użyciu magazynu RA-GRS](https://github.com/Azure-Samples/storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs).
