---
title: Monitorowanie, diagnozowanie i rozwiązywanie problemów z usługi Azure Storage | Dokumentacja firmy Microsoft
description: Korzystać z funkcji, takich jak usługa storage analytics, rejestrowania po stronie klienta i innych narzędzi innych firm do identyfikacji, diagnozowanie i rozwiązywanie problemów związanych z usługi Azure Storage.
services: storage
author: fhryo-msft
ms.service: storage
ms.topic: article
ms.date: 05/11/2017
ms.author: fhryo-msft
ms.subservice: common
ms.openlocfilehash: 6edb1abae91a675a3fe47b417a112f0951886aaf
ms.sourcegitcommit: 87bd7bf35c469f84d6ca6599ac3f5ea5545159c9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2019
ms.locfileid: "58351919"
---
# <a name="monitor-diagnose-and-troubleshoot-microsoft-azure-storage"></a>Monitorowanie, diagnozowanie i rozwiązywanie problemów z usługą Microsoft Azure Storage
[!INCLUDE [storage-selector-portal-monitoring-diagnosing-troubleshooting](../../../includes/storage-selector-portal-monitoring-diagnosing-troubleshooting.md)]

## <a name="overview"></a>Przegląd
Diagnozowanie i rozwiązywanie problemów w aplikacji rozproszonej hostowanej w środowisku chmury może być bardziej skomplikowane niż w tradycyjnych środowiskach. Aplikacje można wdrożyć w infrastrukturze PaaS lub IaaS, w środowisku lokalnym, na urządzeniu przenośnym lub w kombinacji tych środowisk. Zazwyczaj ruchem sieciowym Twojej aplikacji mogą przechodzić publicznych i prywatnych sieci i aplikacji mogą używać wielu technologii magazynowania, takich jak Microsoft Azure Storage tabele, obiekty BLOB, kolejek lub przechowuje pliki oprócz innych danych, takich jak relacyjne i bazy danych dokumentów.

Do zarządzania pomyślnie takich aplikacji należy aktywne monitorowanie i zrozumienie, jak diagnozowanie i rozwiązywanie problemów z wszystkimi aspektami ich i ich technologie zależne. Jako użytkownik usługi Azure Storage należy ciągłego monitorowania usług magazynu używanych przez aplikację nieoczekiwane zmiany w zachowaniu (na przykład wolniej niż zwykłe czasy) i używać rejestrowania w celu zbierania bardziej szczegółowych danych i do analizowania problemu w głębokość. Informacje diagnostyczne, którego można uzyskać od zarówno monitorowanie i rejestrowanie pomoże w celu ustalenia głównej przyczyny aplikacji wystąpił problem. Następnie można rozwiązać ten problem i określić odpowiednie czynności, które można wykonać w celu jego rozwiązania. Usługa Azure Storage jest podstawowe usługi platformy Azure i stanowi ważną część większość rozwiązań, które klienci wdrożyć do infrastruktury platformy Azure. Usługa Azure Storage obejmuje funkcje, aby uprościć monitorowanie, diagnozowanie i rozwiązywanie problemów z magazynowaniem w swoich aplikacjach opartych na chmurze.

> [!NOTE]
> Usługa pliki systemu Azure nie obsługuje rejestrowania w tej chwili.
>

Praktyczny przewodnik dotyczący end-to-end Rozwiązywanie problemów w aplikacji usługi Azure Storage, zobacz [Rozwiązywanie problemów przy użyciu metryk usługi Azure Storage i rejestrowania, narzędzia AzCopy i analizatora komunikatów End-to-End](../storage-e2e-troubleshooting.md).

* [Wprowadzenie]
  * [Sposób organizowania tego przewodnika]
* [Monitorowanie usługi storage]
  * [Monitorowanie kondycji usługi]
  * [Monitorowanie wydajności]
  * [Monitorowanie dostępności]
  * [Monitorowanie wydajności]
* [Diagnozowanie problemów z magazynowaniem]
  * [Problemy dotyczące kondycji usługi]
  * [Problemy z wydajnością]
  * [Diagnozowanie błędów]
  * [Problemy z emulatora magazynu]
  * [Narzędzia rejestrowania magazynu]
  * [Za pomocą narzędzia rejestracji w sieci]
* [Śledzenia end-to-end]
  * [Korelowanie danych dziennika]
  * [Identyfikator żądania klienta]
  * [Identyfikator żądania serwera]
  * [Sygnatury czasowe]
* [Wskazówki dotyczące rozwiązywania problemów]
  * [Metryki wskazują wysoką wartość AverageE2ELatency i niską wartość AverageServerLatency]
  * [Metryki wskazują niską wartość AverageE2ELatency i niską wartość AverageServerLatency, ale na kliencie występuje duże opóźnienie]
  * [Metryki wskazują wysoką wartość AverageServerLatency]
  * [Występują nieoczekiwane opóźnienia w dostarczaniu komunikatów w kolejce]
  * [Metryki wskazują wzrost wartości PercentThrottlingError]
  * [Metryki wskazują wzrost wartości PercentTimeoutError]
  * [Metryki wskazują wzrost wartości PercentNetworkError]
  * [Klient odbiera komunikaty HTTP 403 (zabronione)]
  * [Klient odbiera komunikaty HTTP 404 (nie znaleziono)]
  * [Klient odbiera komunikaty HTTP 409 (konflikt)]
  * [Metryki pokazują PercentSuccess niskim lub wpisy dziennika analytics ma operacji ze stanem transakcji ClientOtherErrors]
  * [Metryki pojemności Pokaż nieoczekiwane zwiększenie wykorzystania pojemności magazynu]
  * [Problem wynika z przy użyciu emulatora magazynu dla rozwoju lub testowania]
  * [Pojawiły się problemy z instalacją zestawu Azure SDK dla platformy .NET]
  * [Inny problem z usługą magazynu]
  * [Rozwiązywanie problemów z wirtualnych dysków twardych na maszynach wirtualnych Windows](../../virtual-machines/windows/troubleshoot-vhds.md)   
  * [Rozwiązywanie problemów z wirtualnych dysków twardych na maszynach wirtualnych systemu Linux](../../virtual-machines/linux/troubleshoot-vhds.md)
  * [Rozwiązywanie problemów w usłudze Azure Files przy użyciu Windows](../files/storage-troubleshoot-windows-file-connection-problems.md)   
  * [Rozwiązywanie problemów z usługą Azure Files z systemem Linux](../files/storage-troubleshoot-linux-file-connection-problems.md)
* [Dodatki]
  * [Dodatek 1: Za pomocą narzędzia Fiddler do przechwytywania ruchu HTTP i HTTPS]
  * [Dodatek 2: Za pomocą programu Wireshark do przechwytywania ruchu sieciowego]
  * [Dodatku 3: Do przechwytywania ruchu sieciowego przy użyciu programu Microsoft Message Analyzer]
  * [Dodatek 4: Aby wyświetlić metryki i rejestrowanie danych za pomocą programu Excel]
  * [Dodatek 5: Monitorowanie za pomocą usługi Application Insights dla metodyki DevOps platformy Azure]

## <a name="introduction"></a>Wprowadzenie
Ten przewodnik przedstawia sposób użycia funkcji, takich jak usługi Azure Storage Analytics, klienta logowania z biblioteki klienta usługi Azure Storage i innych narzędzi innych firm do identyfikacji, diagnozowanie i rozwiązywanie problemów z usługi Azure Storage problemy związane z usługą.

![][1]

Ten przewodnik jest przeznaczony do odczytu przede wszystkim deweloperom usług online, korzystających z usług Azure Storage i profesjonalistów IT jest odpowiedzialny za zarządzanie takich usług online. Cele tego przewodnika są:

* Aby ułatwić utrzymania kondycji i wydajności konta magazynu platformy Azure.
* Aby udostępnić wymagane procesy i narzędzia, aby określić, czy problem lub problem w aplikacji odnosi się do usługi Azure Storage.
* Aby udostępnić wiarygodne wskazówki dotyczące rozwiązywania problemów związanych z usługi Azure Storage.

### <a name="how-this-guide-is-organized"></a>Sposób organizowania tego przewodnika
Sekcja "[monitorowanie usługi storage]" w tym artykule opisano sposób monitorowania kondycji i wydajności usługi Azure Storage przy użyciu metryk usługi Azure Storage Analytics (metryk usługi Storage).

Sekcja "[diagnozowanie problemów z magazynowaniem]" w tym artykule opisano sposób diagnozowania problemów z korzystania z usługi Azure Storage Analytics rejestrowania (rejestrowania magazynu). Zawiera również opis włączania rejestrowania po stronie klienta za pomocą urządzenia w jednym z biblioteki klienta takich jak biblioteki klienta usługi Storage dla platformy .NET lub zestawu Azure SDK dla języka Java.

Sekcja "[śledzenia End-to-end]" w tym artykule opisano, jak można skorelować z informacji zawartych w różnych plikach dziennika i danych metryk.

Sekcja "[wskazówki dotyczące rozwiązywania problemów]" zawiera wskazówki dotyczące rozwiązywania problemów, aby niektóre typowe związane z magazynowaniem problemów, które mogą wystąpić.

"[Dodatki]" zawierają informacje o przy użyciu innych narzędzi, takich jak program Wireshark i Netmon, analizowanie danych pakietów, narzędzia Fiddler na potrzeby analizowania komunikaty HTTP/HTTPS w sieci i dane dziennika Microsoft Message Analyzer do korelacji.

## <a name="monitoring-your-storage-service"></a>Monitorowanie usługi storage
Osoby zaznajomione z programem Windows performance monitoring można traktować metryki magazynu jako odpowiednik liczników monitora wydajności Windows Azure Storage. Metryki magazynu zawiera wszechstronny zestaw metryk (liczniki Monitora wydajności Windows terminologii), takich jak dostępność usługi, łączna liczba żądań do usługi lub Procent pomyślnych żądań do usługi. Aby uzyskać pełną listę dostępnych metryk, zobacz [schemat tabeli metryk usługi Analytics magazynu](https://msdn.microsoft.com/library/azure/hh343264.aspx). Można określić, czy chcesz, aby usługi magazynu, aby zbieranie i agregowanie metryki co godzinę lub co minutę. Aby uzyskać więcej informacji o tym, jak włączyć metryki i monitorować konta magazynu, zobacz [włączenie metryk usługi storage i wyświetlanie danych metryk](https://go.microsoft.com/fwlink/?LinkId=510865).

Można wybrać, które godzinowe metryki, które mają być wyświetlane w [witryny Azure portal](https://portal.azure.com) i skonfigurować reguły powiadamiania administratorów za pośrednictwem poczty e-mail, gdy godzinowe metryki przekracza określoną wartość progową. Aby uzyskać więcej informacji, zobacz [odbieranie powiadomień o alertach](/azure/monitoring-and-diagnostics/monitoring-overview-alerts).

Usługa storage zbiera metryki na najlepszy nakład pracy, ale nie można rejestrować każdej operacji magazynu.

W witrynie Azure portal możesz wyświetlić metryki, takie jak dostępność, łączna liczba żądań i numery Średni czas oczekiwania dla konta magazynu. Reguły powiadomień również została skonfigurowana do powiadomienia administratora, jeśli dostępność spadnie poniżej określonego poziomu. Wyświetlanie tych danych, jeden obszar możliwych do badania jest odsetek działań zakończonych powodzeniem usługi tabeli są poniżej 100% (Aby uzyskać więcej informacji, zobacz sekcję "[Metryki pokazują PercentSuccess niskim lub wpisy dziennika analytics ma operacji ze stanem transakcji ClientOtherErrors]").

Aplikacje platformy Azure, aby upewnić się, że są one w dobrej kondycji i wydajności, zgodnie z oczekiwaniami, stale powinien monitorować:

* Ustanawianie niektóre metryki linii bazowej dla aplikacji, która umożliwia porównywanie bieżące dane i zidentyfikować żadnych znaczących zmian w zachowaniu magazynu platformy Azure a Twoją aplikacją. W wielu przypadkach wartości metryk linii bazowej będzie specyficzny dla danej aplikacji i należy je określić w przypadku testowania aplikacji.
* Rejestrowanie metryki minut i ich użycie w celu monitorowania aktywnie nieoczekiwane błędy i anomalie, takich jak skoków błąd zlicza lub liczby żądań.
* Rejestrowanie metryki godzinowe i ich użycie w celu monitorowania średnie wartości, takie jak średnia liczba błędów i liczby żądań.
* Badania potencjalnych problemów za pomocą narzędzia diagnostyczne, zgodnie z opisem w dalszej części w sekcji "[diagnozowanie problemów z magazynowaniem]."

Wykresy na poniższej ilustracji pokazują, jak uśrednianie występujący w przypadku godzinowych metryk można ukryć skoki w działaniu. Godzinowe metryki są wyświetlane do wyświetlenia stała liczba żądań, podczas minutę metryki ujawnić zmianami, które naprawdę pojawiają się.

![][3]

W pozostałej części tej sekcji opisano metryki, które należy monitorować i dlaczego.

### <a name="monitoring-service-health"></a>Monitorowanie kondycji usługi
Możesz użyć [witryny Azure portal](https://portal.azure.com) umożliwiających wyświetlenie kondycji usługi Storage (i innych usług platformy Azure) we wszystkich regionach platformy Azure na świecie. Umożliwia monitorowanie pozwala natychmiast zobaczyć, jeśli problem poza Twoją kontrolą ma wpływ na usługi Storage w regionie, używanej aplikacji.

[Witryny Azure portal](https://portal.azure.com) można również dołączyć powiadomienia zdarzenia mające wpływ na różne usługi platformy Azure.
Uwaga: Te informacje wcześniej była dostępna, wraz z danych historycznych na [pulpitu nawigacyjnego usług systemu Azure](https://status.azure.com).

Gdy [witryny Azure portal](https://portal.azure.com) zbiera informacje o kondycji z wewnątrz centrów danych platformy Azure (monitorowanie w poziomie), możesz również przyjęcie podejścia poza do generowania transakcji syntetycznych, które okresowo dostępu aplikacji sieci web hostowanych na platformie Azure, z wielu lokalizacji. Usługi oferowane przez [Dynatrace](https://www.dynatrace.com/en/synthetic-monitoring) i Application Insights dla metodyki DevOps platformy Azure są przykładem tego podejścia. Aby uzyskać więcej informacji na temat usługi Application Insights dla metodyki DevOps platformy Azure, zobacz dodatku "[dodatek 5: Monitorowanie za pomocą usługi Application Insights dla metodyki DevOps platformy Azure](#appendix-5). "

### <a name="monitoring-capacity"></a>Monitorowanie wydajności
Ponieważ obiekty BLOB są zazwyczaj konto największą część danych przechowywanych metryk usługi Storage tylko przechowuje metryki pojemności dla usługi blob (w czasie pisania, nie jest możliwe monitorowanie pojemności, tabele i kolejki za pomocą metryk usługi Storage). Możesz znaleźć te dane w **$MetricsCapacityBlob** tabeli po włączeniu monitorowania usługi obiektów Blob. Metryki magazynu rejestruje następujące dane raz dziennie, a następnie można użyć wartości **RowKey** do określenia, czy wiersz zawiera jednostki, która odnosi się do danych użytkownika (wartość **danych**) lub dane analizy (wartość **analytics**). Każda jednostka przechowywanych zawiera informacje o wielkość wykorzystanego magazynu (**pojemności** mierzony w bajtach) i bieżącą liczbę kontenerów (**ContainerCount**) i obiektów blob (**ObjectCount** ) używane w ramach konta magazynu. Aby uzyskać więcej informacji na temat metryki pojemności, przechowywane w **$MetricsCapacityBlob** tabeli, zobacz [schemat tabeli metryk usługi Analytics magazynu](https://msdn.microsoft.com/library/azure/hh343264.aspx).

> [!NOTE]
> Należy monitorować te wartości w celu wczesnego ostrzegania, że Zbliżasz się limitów pojemności konta magazynu. W witrynie Azure portal można dodać reguły alertów, aby otrzymywać powiadomienia, czy użycie agregacji magazynu przekracza spadnie poniżej progów, które określisz.
>
>

Aby uzyskać pomoc, Szacowanie rozmiaru dla różnych obiektów, takich jak obiekty BLOB, zobacz wpis w blogu [opis rozliczeń platformy Azure Storage — przepustowość, transakcje i pojemność](https://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx).

### <a name="monitoring-availability"></a>Monitorowanie dostępności
Należy monitorować dostępność usług magazynu w ramach konta magazynu, monitorując wartość w **dostępności** kolumn w tabelach metryki co godzinę i minutę — **$MetricsHourPrimaryTransactionsBlob** , **$MetricsHourPrimaryTransactionsTable**, **$MetricsHourPrimaryTransactionsQueue**, **$MetricsMinutePrimaryTransactionsBlob** , **$MetricsMinutePrimaryTransactionsTable**, **$MetricsMinutePrimaryTransactionsQueue**, **$MetricsCapacityBlob**. **Dostępności** kolumna zawiera wartość procentową, która wskazuje dostępności usługi lub operacji interfejsu API, reprezentowane przez wiersz ( **RowKey** pokazuje, czy wiersz zawiera wartość metryki Usługa jako całości lub dla określonej operacji interfejsu API).

Wszystkie wartości mniejszej niż 100% wskazuje, że niektóre żądania magazynu kończą się niepowodzeniem. Możesz zobaczyć, dlaczego one kończy się niepowodzeniem, sprawdzając innych kolumn w danych metryk, takich jak wyświetlanie liczby żądań z różnych error — typy **ServerTimeoutError**. Należy oczekiwać wyświetlenia **dostępności** tymczasowo spadek poniżej 100% powodów, takich jak serwer przejściowy przekroczeń limitu czasu podczas usługi przenosi partycje lepsze żądanie Równoważenie obciążenia; powinien Logika ponawiania w aplikacji klienckiej Obsługuj tymczasowymi warunkami. Artykuł [operacji rejestrowane analizy magazynu i komunikaty o stanie](https://msdn.microsoft.com/library/azure/hh343260.aspx) zawiera listę typów transakcji, które zawiera metryki magazynu, w jego **dostępności** obliczeń.

W [witryny Azure portal](https://portal.azure.com), można dodać reguły alertów, aby otrzymywać powiadomienia, jeśli **dostępności** dla usługi spadnie poniżej wartości progowej, które określisz.

"[Wskazówki dotyczące rozwiązywania problemów]" części tego przewodnika opisano niektóre typowe problemy usługi storage związanych z dostępnością.

### <a name="monitoring-performance"></a>Monitorowanie wydajności
Aby monitorować wydajność usług magazynu, służy następujące metryki z tabel metryki co godzinę i minutę.

* Wartości w **wartość AverageE2ELatency** i **wartość AverageServerLatency** kolumny zawierają Średni czas trwania usługi magazynu lub trwa typ operacji interfejsu API do przetwarzania żądań. **Wartość AverageE2ELatency** jest miarą opóźnienie end-to-end, który zawiera czas potrzebny do odczytania żądania i czy wysłać odpowiedź oprócz czas poświęcony na przetworzenie żądania (w związku z tym z uwzględnieniem opóźnienia sieci, gdy żądanie dotrze magazynu Usługa); **Wartość AverageServerLatency** jest miarą tylko czas przetwarzania i dlatego nie obejmuje opóźnienia sieci, wszelkie związane z komunikacji z klientem. Zobacz sekcję "[metryki wskazują wysoką wartość AverageE2ELatency i niską wartość AverageServerLatency]" w dalszej części tego przewodnika, aby uzyskać informacje dotyczące przyczyny może być istotną różnicą między tymi dwiema wartościami.
* Wartości w **TotalIngress** i **TotalEgress** kolumny zawierają łącznej ilości danych, w bajtach, przychodzących na i przesyłane z usługi storage lub za pomocą określonego typu operacji interfejsu API.
* Wartości w **TotalRequests** kolumnie wyświetlane całkowita liczba żądań, które otrzymuje usługi storage w operacji interfejsu API. **TotalRequests** jest to całkowita liczba żądań, które otrzymuje z usługi storage.

Zazwyczaj można będzie monitorować nieoczekiwanych zmian w dowolnym z tych wartości jako wskazówka, że masz problem, który wymaga badania.

W [witryny Azure portal](https://portal.azure.com), można dodać reguły alertów, aby otrzymywać powiadomienia, jeśli jakiekolwiek metryki wydajności dla tej usługi spadną poniżej lub przekracza wartość progową, który określisz.

"[Wskazówki dotyczące rozwiązywania problemów]" części tego przewodnika opisano niektóre typowe problemy usługi storage związanych z wydajnością.

## <a name="diagnosing-storage-issues"></a>Diagnozowanie problemów z magazynowaniem
Istnieje wiele sposobów, czy użytkownik może zostaną powiadomieni o problem lub problem w aplikacji, w tym:

* Poważnej awarii, który powoduje awarię lub przerwanie działania aplikacji.
* Istotne zmiany z wartości odniesienia w metryki monitorowania zgodnie z opisem w poprzedniej sekcji "[monitorowanie usługi storage]."
* Raporty z użytkownicy twojej aplikacji, niektóre określoną operacją nie została ukończona zgodnie z oczekiwaniami, lub nie działa niektórych funkcji.
* Błędy wygenerowane w aplikacji, są wyświetlane w plikach dziennika lub przy użyciu innej metody powiadomień.

Zazwyczaj problemy związane z usług Azure storage można podzielić na jeden z czterech ogólne kategorie:

* Twoja aplikacja ma problem z wydajnością, zgłaszane przez użytkowników lub reakcja zmianami w metryk wydajności.
* Występuje problem z infrastrukturą usługi Azure Storage w jednym lub kilku regionach.
* Aplikację napotkała błąd zgłoszony przez użytkowników lub ujawnionych w jednej z metryk liczba błędów, które można monitorować wzrost.
* Podczas tworzenia i testowania używasz emulatora magazynu lokalnego; mogą wystąpić problemy związane z użycia emulatora magazynu.

W poniższych sekcjach opisano kroki należy wykonać do diagnozowania i rozwiązywania problemów w każdej z tych czterech kategorii. Sekcja "[wskazówki dotyczące rozwiązywania problemów]" później w tym przewodniku zapewnia więcej szczegółów dotyczących niektóre typowe problemy, które można napotkać.

### <a name="service-health-issues"></a>Problemy dotyczące kondycji usługi
Problemy dotyczące kondycji usługi zwykle znajdują się poza Twoją kontrolą. [Witryny Azure portal](https://portal.azure.com) zawiera informacje o wszystkie bieżące problemy z usługami platformy Azure, w tym usługi storage. Jeśli została wybrana dla magazynu geograficznie nadmiarowego do odczytu, podczas tworzenia konta usługi storage, następnie jeśli danych jest niedostępna w lokalizacji podstawowej aplikacji można tymczasowo przełączyć kopii tylko do odczytu w lokalizacji pomocniczej. Aby odczytać z pomocniczym, aplikacji należy mieć możliwość przełączania się między przy użyciu lokalizacji magazynu podstawowego i pomocniczego i mieć możliwość pracy w trybie ograniczonej funkcjonalności, przy użyciu danych tylko do odczytu. Biblioteki klienta usługi Storage platformy Azure umożliwiają definiowanie zasad ponawiania, który może odczytywać dane z pomocniczego magazynu w przypadku niepowodzenia odczytu z magazynu głównego. Aplikacja musi należy pamiętać, że dane w dodatkowej lokalizacji jest ostatecznie spójny. Aby uzyskać więcej informacji, zobacz wpis w blogu [Opcje nadmiarowości magazynu platformy Azure i Magazyn geograficznie nadmiarowy dostęp do odczytu](https://blogs.msdn.microsoft.com/windowsazurestorage/2013/12/11/windows-azure-storage-redundancy-options-and-read-access-geo-redundant-storage/).

### <a name="performance-issues"></a>Problemy z wydajnością
Wydajność aplikacji może być wartością subiektywną, zwłaszcza z punktu widzenia użytkownika. Dlatego należy mieć dostępne metryki linii bazowej, które ułatwiają wykrywanie problemów z wydajnością. Wiele czynników może mieć wpływ na wydajność usługi Azure storage z perspektywy aplikacji klienta. Tych czynników może działać w usłudze magazynu, klienta lub infrastruktury sieciowej; Dlatego ważne jest zapewnienie strategii do identyfikowania źródła problemów z wydajnością.

Po zidentyfikowaniu prawdopodobnie lokalizacji przyczyna problemu z wydajnością z metryk, następnie służy pliki dziennika można znaleźć szczegółowe informacje, aby zdiagnozować i rozwiązać problem dodatkowo.

Sekcja "[wskazówki dotyczące rozwiązywania problemów]" później w tym przewodniku zawiera więcej informacji o niektórych typowych problemów związanych z wydajnością można napotkać.

### <a name="diagnosing-errors"></a>Diagnozowanie błędów
Użytkownicy twojej aplikacji może powiadomić użytkownika o błędów zgłoszonych przez aplikację klienta. Takie jak metryki magazynu liczby typów różnych błędów z usługi storage rejestruje także **NetworkError**, **ClientTimeoutError**, lub **AuthorizationError**. Gdy metryk usługi Storage tylko rekordy liczby typów różnych błędów, można uzyskać więcej szczegółów na temat poszczególnych żądań, sprawdzając dzienniki sieci, po stronie klienta i po stronie serwera. Zazwyczaj kod stanu HTTP, które są zwracane przez usługę storage identyfikują przyczyny niepowodzenia żądania.

> [!NOTE]
> Należy pamiętać, że należy się spodziewać się sporadyczne błędy: na przykład błędy ze względu na warunki przejściowe problemy z siecią lub błędy aplikacji.
>
>

Przydatne dla zrozumienia kodów stanu i błędów związanych z magazynem są następujące zasoby:

* [Typowe kody błędów interfejsu API REST](https://msdn.microsoft.com/library/azure/dd179357.aspx)
* [Kody błędów usługi Blob Service](https://msdn.microsoft.com/library/azure/dd179439.aspx)
* [Kody błędów usługi kolejki](https://msdn.microsoft.com/library/azure/dd179446.aspx)
* [Kody błędów usługi tabeli](https://msdn.microsoft.com/library/azure/dd179438.aspx)
* [Kody błędów usługi plików](https://msdn.microsoft.com/library/azure/dn690119.aspx)

### <a name="storage-emulator-issues"></a>Problemy z emulatora magazynu
Zestaw SDK platformy Azure zawiera emulator magazynu, które można uruchomić na deweloperskiej stacji roboczej. Tego emulatora symuluje większość zachowanie usługi magazynu platformy Azure i jest przydatne podczas tworzenia i testowania, dzięki któremu można uruchomić aplikacji, które używają usług Azure storage bez konieczności subskrypcji systemu Azure i konto magazynu platformy Azure.

"[Wskazówki dotyczące rozwiązywania problemów]" części tego przewodnika opisano niektóre typowe problemy, które napotkano przy użyciu emulatora magazynu.

### <a name="storage-logging-tools"></a>Narzędzia rejestrowania magazynu
Rejestrowanie magazynu zapewnia rejestrowanie żądań magazynu na koncie magazynu platformy Azure po stronie serwera. Aby uzyskać więcej informacji o tym, jak włączyć rejestrowanie po stronie serwera i uzyskać dostęp do danych dziennika, zobacz [uzyskiwania dostępu do danych dzienników i włączanie rejestrowania magazynu](https://go.microsoft.com/fwlink/?LinkId=510867).

Biblioteki klienta usługi Storage dla platformy .NET umożliwia zbieranie danych dziennika po stronie klienta, które odnoszą się do magazynu operacje wykonywane przez aplikację. Aby uzyskać więcej informacji, zobacz [Client-side Logging with the .NET Storage Client Library](https://go.microsoft.com/fwlink/?LinkId=510868) (Logowanie po stronie klienta przy użyciu biblioteki klienckiej usługi .NET Storage).

> [!NOTE]
> W niektórych sytuacjach (np. błędy autoryzacji sygnatury dostępu Współdzielonego) użytkownik może zgłaszać błąd, dla którego nie dane żądania można znaleźć w dzienników magazynu po stronie serwera. Można skorzystać z funkcji rejestrowania biblioteki klienta usługi Storage do zbadania, czy przyczyną problemu jest na komputerze klienckim, lub użyć narzędzia monitorowania sieci do sieci.
>
>

### <a name="using-network-logging-tools"></a>Za pomocą narzędzia rejestracji w sieci
Można przechwytywać ruch między klientem i serwerem zawierają szczegółowe informacje o dane, które są wymiana klient i serwer i podstawowe warunki w sieci. Narzędzia rejestrowania przydatne sieci obejmują:

* [Narzędzie fiddler](https://www.telerik.com/fiddler) jest to bezpłatny internetowy serwer proxy, który umożliwia zbadanie nagłówki i dane ładunku komunikatów żądań i odpowiedzi HTTP i HTTPS debugowania. Aby uzyskać więcej informacji, zobacz [dodatku 1: Za pomocą narzędzia Fiddler do przechwytywania ruchu HTTP i HTTPS](#appendix-1).
* [Microsoft Network Monitor (Netmon)](https://www.microsoft.com/download/details.aspx?id=4865) i [Wireshark](https://www.wireshark.org/) są bezpłatne sieci analizatory protokołu, umożliwiające wyświetlanie pakietów szczegółowe informacje dla różnych protokołów sieciowych. Aby uzyskać więcej informacji na temat programu Wireshark, zobacz "[dodatek 2: Do przechwytywania ruchu sieciowego za pomocą programu Wireshark](#appendix-2)".
* Microsoft Message Analyzer jest narzędziem do firmy Microsoft, która zastępuje Monitor sieci, które oprócz przechwytywanie danych pakietów sieciowych, pomaga przeglądać i analizować dane dziennika przechwycone z innych narzędzi. Aby uzyskać więcej informacji, zobacz "[dodatku 3: Do przechwytywania ruchu sieciowego przy użyciu programu Microsoft Message Analyzer](#appendix-3)".
* Jeśli chcesz wykonać test podstawowej łączności, aby sprawdzić, czy komputerze klienckim może połączyć się z usługą Azure storage za pośrednictwem sieci, nie możesz tego zrobić przy użyciu standardu **ping** narzędzia na komputerze klienckim. Można jednak użyć [ **tcping** narzędzie](https://www.elifulkerson.com/projects/tcping.php) Aby sprawdzić łączność.

W wielu przypadkach dane dzienników z rejestrowania magazynu i biblioteki klienta usługi Storage będą wystarczające, aby zdiagnozować problem, ale w niektórych przypadkach mogą wymagać bardziej szczegółowe informacje, które zapewniają następujące narzędzia rejestrowania w sieci. Na przykład za pomocą programu Fiddler, aby wyświetlić komunikaty HTTP i HTTPS umożliwia wyświetlenie nagłówka i ładunku danych wysyłane do i z usługi magazynu, które umożliwiłyby zbadać, jak aplikacja kliencka ponawia próbę operacji magazynu. Analizatory protokołów, takich jak program Wireshark działają na poziomie pakietów, które umożliwia wyświetlenie danych TCP, który umożliwiłby rozwiązywać problemy z łącznością i utraty pakietów. Analizator komunikatów mogą działać w warstwach protokołów HTTP i TCP.

## <a name="end-to-end-tracing"></a>Śledzenia end-to-end
Śledzenia end-to-end, korzystając z różnych plików dziennika jest przydatną techniką badania potencjalnych problemów. Informacjom daty/godziny z danych metryk w celu wskazania gdzie rozpocząć wyszukiwanie w plikach dziennika, aby uzyskać szczegółowe informacje, które mogą pomóc w rozwiązaniu problemu.

### <a name="correlating-log-data"></a>Korelowanie danych dziennika
Podczas przeglądania dzienników z aplikacji klienckich, sieci służy do śledzenia i rejestrowania po stronie serwera magazynu, które warto mieć możliwość skorelowania żądań w różnych plikach dziennika. Pliki dziennika obejmują szereg różnych pól, które służą jako identyfikatory korelacji. Identyfikator żądania klienta jest najbardziej użyteczne pola służące do skorelowania wpisów w różnych dziennikach. Jednak czasami może być przydatne do używania Identyfikatora żądania serwera lub sygnatur czasowych. Poniższe sekcje zawierają więcej szczegółów na temat tych opcji.

### <a name="client-request-id"></a>Identyfikator żądania klienta
Biblioteka klienta magazynu automatycznie generuje identyfikator żądania klienta unikatowy dla każdego żądania.

* W dzienniku po stronie klienta, który tworzy bibliotekę klienta usługi Storage, identyfikator żądania klienta jest wyświetlana w **identyfikator żądania klienta** pola każdego wpisu dziennika, odnoszące się do żądania.
* W śledzenia sieci taki przechwycone przez narzędzie Fiddler, identyfikator żądania klienta jest widoczny w komunikatów żądania jako **x-ms klient request-id** wartość nagłówka HTTP.
* W dzienniku magazynu rejestrowania po stronie serwera identyfikator żądania klienta jest wyświetlana w kolumnie identyfikator żądania klienta.

> [!NOTE]
> Istnieje możliwość dla wielu żądań udostępnić ten sam identyfikator żądania klienta, ponieważ klienta można przypisać tę wartość (mimo że biblioteki klienta usługi Storage, które automatycznie przypisuje nową wartość). Gdy klient ponawia próbę, wszystkie próby współużytkują ten sam identyfikator żądania klienta. W przypadku partii wysłanych z klienta usługi batch ma identyfikatora żądania dla jednego klienta.
>
>

### <a name="server-request-id"></a>Identyfikator żądania serwera
Usługa storage automatycznie generuje żądanie serwera identyfikatorów.

* W dzienniku magazynu rejestrowania po stronie serwera, zostanie wyświetlony identyfikator żądania serwera **nagłówka Identyfikatora żądania** kolumny.
* W śledzenia sieci taki przechwycone przez narzędzie Fiddler, identyfikator żądania serwera pojawia się w komunikatach odpowiedzi jako **x-ms-request-id** wartość nagłówka HTTP.
* W dzienniku po stronie klienta, który tworzy bibliotekę klienta usługi Storage, identyfikator żądania serwera pojawia się w **tekst operacji** kolumny dla wpisu dziennika, wyświetlania szczegółów odpowiedź serwera.

> [!NOTE]
> Usługi storage zawsze przypisuje unikatowych serwerów identyfikator żądania do każdego żądania odebranego, więc każdej próby ponowienia z klienta i każda operacja uwzględniony w zadaniu wsadowym ma unikatowy identyfikator serwera żądania.
>
>

Jeśli biblioteki klienta usługi Storage zgłasza **StorageException** w kliencie **RequestInformation** właściwość zawiera **RequestResult** obiektu, który zawiera  **ServiceRequestID** właściwości. Można również przejść **RequestResult** obiektu z **OperationContext** wystąpienia.

Poniższy przykład kodu pokazuje, jak ustawić niestandardowe **ClientRequestId** wartość przez dołączenie **OperationContext** obiektu żądania do usługi storage. Pokazano również, jak pobrać **ServerRequestId** wartości z komunikatu odpowiedzi.

```csharp
//Parse the connection string for the storage account.
const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Create an Operation Context that includes custom ClientRequestId string based on constants defined within the application along with a Guid.
OperationContext oc = new OperationContext();
oc.ClientRequestID = String.Format("{0} {1} {2} {3}", HOSTNAME, APPNAME, USERID, Guid.NewGuid().ToString());

try
{
    CloudBlobContainer container = blobClient.GetContainerReference("democontainer");
    ICloudBlob blob = container.GetBlobReferenceFromServer("testImage.jpg", null, null, oc);  
    var downloadToPath = string.Format("./{0}", blob.Name);
    using (var fs = File.OpenWrite(downloadToPath))
    {
        blob.DownloadToStream(fs, null, null, oc);
        Console.WriteLine("\t Blob downloaded to file: {0}", downloadToPath);
    }
}
catch (StorageException storageException)
{
    Console.WriteLine("Storage exception {0} occurred", storageException.Message);
    // Multiple results may exist due to client side retry logic - each retried operation will have a unique ServiceRequestId
    foreach (var result in oc.RequestResults)
    {
            Console.WriteLine("HttpStatus: {0}, ServiceRequestId {1}", result.HttpStatusCode, result.ServiceRequestID);
    }
}
```

### <a name="timestamps"></a>Sygnatury czasowe
Sygnatury czasowe umożliwia również zlokalizować wpisy dziennika powiązane, ale należy zwrócić szczególną uwagę na wszelkie przesunięcia czasowego zegara między klientem i serwerem, który może istnieć. Wyszukaj plus lub minus 15 minut przez pasujące wpisy po stronie serwera, w oparciu o sygnaturę czasową na komputerze klienckim. Należy pamiętać, że metadane obiektu blob dla obiektów blob zawierający metryki wskazuje zakres czasu dla metryk, przechowywane w obiekcie blob. Ten zakres czasu jest przydatne, jeśli masz wiele metryki obiektów blob dla tej samej minuty lub godziny.

## <a name="troubleshooting-guidance"></a>Wskazówki dotyczące rozwiązywania problemów
W tej sekcji ułatwiają diagnozowanie i rozwiązywanie problemów z niektórych typowych problemów z aplikacją może wystąpić w przypadku korzystania z usług Azure storage. Użyj poniższej listy, aby zlokalizować informacje dotyczące określonego problemu.

**Rozwiązywanie problemów z drzewa decyzyjnego**

---
Problem związek z wydajnością usługi storage?

* [Metryki wskazują wysoką wartość AverageE2ELatency i niską wartość AverageServerLatency]
* [Metryki wskazują niską wartość AverageE2ELatency i niską wartość AverageServerLatency, ale na kliencie występuje duże opóźnienie]
* [Metryki wskazują wysoką wartość AverageServerLatency]
* [Występują nieoczekiwane opóźnienia w dostarczaniu komunikatów w kolejce]

---
Problem związek do sprawdzania dostępności usług magazynu?

* [Metryki wskazują wzrost wartości PercentThrottlingError]
* [Metryki wskazują wzrost wartości PercentTimeoutError]
* [Metryki wskazują wzrost wartości PercentNetworkError]

---
 Aplikacja kliencka odbiera odpowiedź HTTP 4XX (na przykład 404) z usługi storage?

* [Klient odbiera komunikaty HTTP 403 (zabronione)]
* [Klient odbiera komunikaty HTTP 404 (nie znaleziono)]
* [Klient odbiera komunikaty HTTP 409 (konflikt)]

---
[Metryki pokazują PercentSuccess niskim lub wpisy dziennika analytics ma operacji ze stanem transakcji ClientOtherErrors]

---
[Metryki pojemności Pokaż nieoczekiwane zwiększenie wykorzystania pojemności magazynu]

---
[Występują nieoczekiwane ponowne uruchamianie maszyn wirtualnych, które mają dużą liczbę dołączonymi dyskami VHD]

---
[Problem wynika z przy użyciu emulatora magazynu dla rozwoju lub testowania]

---
[Pojawiły się problemy z instalacją zestawu Azure SDK dla platformy .NET]

---
[Inny problem z usługą magazynu]

---
### <a name="metrics-show-high-AverageE2ELatency-and-low-AverageServerLatency"></a>Metryki wskazują wysoką wartość AverageE2ELatency i niską wartość AverageServerLatency
Ilustracja poniżej z [witryny Azure portal](https://portal.azure.com) narzędzie do monitorowania przedstawiono przykład gdzie **wartość AverageE2ELatency** jest znacznie wyższa niż **wartość AverageServerLatency**.

![][4]

Usługi storage tylko w przypadku obliczania metryki **wartość AverageE2ELatency** dla żądań zakończonych powodzeniem i, w odróżnieniu od **wartość AverageServerLatency**, obejmuje czas, klient przyjmuje do wysyłania danych i odbierania potwierdzenie z usługi storage. W związku z tym, różnica między **wartość AverageE2ELatency** i **wartość AverageServerLatency** może być z powodu aplikacja kliencka jest powolne odpowiedzi lub ze względu na warunki w sieci.

> [!NOTE]
> Można również wyświetlić **E2ELatency** i **ServerLatency** dane dziennika dla operacji magazynu w rejestrowania magazynu.
>
>

#### <a name="investigating-client-performance-issues"></a>Badanie problemów z wydajnością klienta
Możliwe klienta odpowiada powoli przyczyny o ograniczonej liczbie dostępnych połączeń lub wątków lub trwa małą ilością zasobów, takich jak Procesor, pamięć lub sieć przepustowości. Dzięki temu można rozwiązać ten problem, modyfikując kod klienta, aby był bardziej wydajne, (na przykład za pomocą wywołania asynchroniczne do usługi storage) lub przy użyciu większych maszyn wirtualnych (rdzeni i więcej pamięci).

Tabela i kolejka usług, algorytm Nagle'a przyczyną może być również wysoką **wartość AverageE2ELatency** w porównaniu z **wartość AverageServerLatency**: Aby uzyskać więcej informacji, zobacz wpis [Nagle'a firmy Algorytm jest nie przyjazną kierunku żądań o małym rozmiarze](https://blogs.msdn.com/b/windowsazurestorage/archive/2010/06/25/nagle-s-algorithm-is-not-friendly-towards-small-requests.aspx). Algorytm Nagle'a w kodzie można wyłączyć za pomocą **ServicePointManager —** klasy w **przestrzeni nazw System.Net** przestrzeni nazw. Należy to zrobić przed wprowadzić dowolne wywołania do tabeli lub otworzyć kolejki usług w Twojej aplikacji, ponieważ nie dotyczy to połączeń znajdujących się już. Poniższy przykład pochodzi z **Application_Start** metody w roli procesu roboczego.

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);
ServicePoint tableServicePoint = ServicePointManager.FindServicePoint(storageAccount.TableEndpoint);
tableServicePoint.UseNagleAlgorithm = false;
ServicePoint queueServicePoint = ServicePointManager.FindServicePoint(storageAccount.QueueEndpoint);
queueServicePoint.UseNagleAlgorithm = false;
```

Należy sprawdzić dzienniki po stronie klienta, aby zobaczyć, ile żądań, że Twoja aplikacja kliencka jest przesyłanie i sprawdzić ogólne .NET pokrewne wąskich gardeł wydajności w swoim kliencie, takie jak procesor CPU, wyrzucanie elementów bezużytecznych .NET, użycia sieci lub pamięci. Jako punktu wyjścia do rozwiązywania problemów aplikacji klienta platformy .NET, zobacz [debugowanie, śledzenie i profilowanie](https://msdn.microsoft.com/library/7fe0dd2y).

#### <a name="investigating-network-latency-issues"></a>Badanie problemów z opóźnieniem sieci
Zazwyczaj duże opóźnienie end-to-end spowodowane przez sieć jest ze względu na przejściowe warunki. Za pomocą narzędzi takich jak program Wireshark lub Microsoft Message Analyzer, można zbadać oba problemów przejściowe i trwałe sieci, takich jak porzuconych pakietów.

Aby uzyskać więcej informacji na temat rozwiązywania problemów z siecią za pomocą programu Wireshark zobacz "[Dodatek 2: Za pomocą programu Wireshark do przechwytywania ruchu sieciowego]. "

Aby uzyskać więcej informacji na temat rozwiązywania problemów z siecią przy użyciu programu Microsoft Message Analyzer, zobacz "[dodatku 3: Do przechwytywania ruchu sieciowego przy użyciu programu Microsoft Message Analyzer]. "

### <a name="metrics-show-low-AverageE2ELatency-and-low-AverageServerLatency"></a>Metryki wskazują niską wartość AverageE2ELatency i niską wartość AverageServerLatency, ale na kliencie występuje duże opóźnienie
W tym scenariuszu najbardziej prawdopodobną przyczyną jest opóźnienia w żądań magazynu docieranie do usługi storage. Powinieneś zbadać, dlaczego żądania od klienta nie wykorzystują ją przy użyciu usługę blob Service.

Jedną z możliwych przyczyn dla klienta, powodując opóźnienie wysyłania żądań jest, że ograniczoną liczbę dostępnych połączeń lub wątków.

Ponadto sprawdź, czy klient wykonuje wiele ponownych prób i Sprawdź przyczynę, jeśli jest. Aby ustalić, czy klient wykonuje wiele ponownych prób, możesz wykonywać następujące czynności:

* Sprawdź dzienniki usługi Storage Analytics. Jeśli występują wiele ponownych prób, pojawią się wiele operacji, mających taki sam identyfikator żądania klienta, ale z innym serwerem żądaniem identyfikatorów.
* Sprawdź dzienniki klienta. Pełne rejestrowanie wskaże, że miało miejsce ponowienie próby.
* Debugowanie kodu i sprawdź właściwości **OperationContext** obiekt skojarzony z żądaniem. Jeśli próba powtórzenia operacji, **RequestResults** właściwość będzie zawierać wiele żądanie serwera unikatowych identyfikatorów. Możesz również sprawdzić czas rozpoczęcia i zakończenia dla każdego żądania. Aby uzyskać więcej informacji, zobacz przykładowy kod w sekcji [identyfikator żądania serwera].

Jeśli nie występują żadne problemy w kliencie, należy zbadać potencjalne problemy z siecią, takie jak utrata pakietów. Za pomocą narzędzi takich jak program Wireshark lub Microsoft Message Analyzer do badania problemów z siecią.

Aby uzyskać więcej informacji na temat rozwiązywania problemów z siecią za pomocą programu Wireshark zobacz "[Dodatek 2: Za pomocą programu Wireshark do przechwytywania ruchu sieciowego]. "

Aby uzyskać więcej informacji na temat rozwiązywania problemów z siecią przy użyciu programu Microsoft Message Analyzer, zobacz "[dodatku 3: Do przechwytywania ruchu sieciowego przy użyciu programu Microsoft Message Analyzer]. "

### <a name="metrics-show-high-AverageServerLatency"></a>Metryki wskazują wysoką wartość AverageServerLatency
W przypadku wysokiej **wartość AverageServerLatency** żądań pobierania obiektów blob, należy używać dzienników rejestrowania magazynu można sprawdzić, czy są ponownych żądań dotyczących tego samego obiektu blob (lub zestaw obiektów blob). W przypadku żądań przekazywania obiektu blob powinien być sprawdzony bloku rozmiar klienta jest używany (na przykład bloków który chunks mniej niż 64 KB, rozmiar może spowodować koszty ogólne, chyba że operacje odczytywania są również w mniej niż 64 KB), a jeśli wielu klientów, przekazujesz do tego samego obiektu blob w para bloków llel. Należy także sprawdzić metryki na minutę dla gwałtowny wzrost liczby żądań, które wynikają z przekroczeniem na drugim cele skalowalności: Zobacz też "[Metryki wskazują wzrost wartości PercentTimeoutError]."

Jeśli widzisz wysokiego **wartość AverageServerLatency** do pobrania obiektu blob żądaniami, gdy są powtarzane żąda tego samego obiektu blob lub zestaw obiektów blob, a następnie rozważ buforowanie tych obiektów blob za pomocą usługi Azure Cache lub Azure Content Delivery Network (CDN). W przypadku przekazywania żądań może zwiększyć przepływność przy użyciu większy rozmiar bloku. Dla zapytań do tabel istnieje również możliwość zaimplementuj buforowanie po stronie klienta, na komputerach klienckich, które wykonują te same operacje zapytania i których danych nie zmieniają się często.

Wysoka **wartość AverageServerLatency** wartości mogą też być objawem źle zaprojektowany tabel lub kwerend, czy wynik w ramach operacji skanowania lub że oparte na wzorcu dołączania/dołączana niezalecane. Aby uzyskać więcej informacji, zobacz "[Metryki wskazują wzrost wartości PercentThrottlingError]".

> [!NOTE]
> Można znaleźć kompleksowe Lista kontrolna dotycząca wydajności listy kontrolnej w tym miejscu: [Wydajność magazynu platformy Microsoft Azure i Lista kontrolna dotycząca skalowalności](storage-performance-checklist.md).
>
>

### <a name="you-are-experiencing-unexpected-delays-in-message-delivery"></a>Występują nieoczekiwane opóźnienia w dostarczaniu komunikatów w kolejce
Występuje opóźnienie między czasu aplikacja dodaje komunikat do kolejki i czas, który będzie dostępny do odczytu z kolejki, należy wykonać poniższe kroki, aby zdiagnozować problem:

* Sprawdź, czy aplikacja jest pomyślnie Dodawanie komunikatów do kolejki. Upewnij się, że aplikacja nie ponawia próbę **AddMessage** metody kilka razy przed powodzeniem. Dzienniki biblioteki klienta usługi Storage zostaną wyświetlone wszystkie wielokrotnych próbach operacji magazynu.
* Sprawdź nie zegara jest niesymetryczność między roli procesu roboczego, który dodaje komunikat do kolejki i roli procesu roboczego, który odczytuje komunikat z kolejki, która sprawia, że są wyświetlane tak, jakby występuje opóźnienie podczas przetwarzania.
* Sprawdź, czy rola procesu roboczego, która odczytuje komunikaty z kolejki jest możliwe. Jeśli klient kolejki wywołuje **GetMessage** metoda, ale kończy się niepowodzeniem z potwierdzeniem, komunikat pozostanie niewidoczny w kolejce do momentu **invisibilityTimeout** okresu. W tym momencie komunikat staje się dostępna przetworzony ponownie.
* Sprawdź, jeśli długość kolejki rośnie wraz z upływem czasu. Może to występować, jeśli nie masz wystarczających procesów roboczych, które są dostępne do przetwarzania wszystkich komunikatów, które inni pracownicy są umieszczenie w kolejce. Również szybko sprawdzić metryki, aby zobaczyć, jeśli usunięciu żądania kończą się niepowodzeniem i usuwania z kolejki liczone na wiadomości, które mogą wskazywać kolejnych nieudanych prób do usuwania komunikatu.
* Przeanalizuj dzienniki rejestrowania magazynu dla wszystkich operacji w kolejce, które mają wyższy niż oczekiwano **E2ELatency** i **ServerLatency** wartości w dłuższym okresie czasu niż zwykle.

### <a name="metrics-show-an-increase-in-PercentThrottlingError"></a>Metryki wskazują wzrost wzrost wartości PercentThrottlingError
Błędy ograniczania wystąpić, gdy przekracza cele skalowalności usługi magazynu. Ograniczenia usług magazynu, upewnij się, że nie pojedynczego klienta lub dzierżawcy mogą używać usługi kosztem innych użytkowników. Aby uzyskać więcej informacji, zobacz [usługi Azure Storage dotyczące skalowalności i cele wydajności](storage-scalability-targets.md) szczegółowe informacje na temat wartości docelowe skalowalności konta magazynu i cele wydajności dla partycji w ramach kont magazynu.

Jeśli **wartości PercentThrottlingError** metryki spowodować wzrost Procent żądań, które kończą się niepowodzeniem z powodu błędu ograniczania przepustowości, należy zbadać jednego z dwóch scenariuszy:

* [Przejściowy wzrost wartości PercentThrottlingError]
* [Stały wzrost wartości PercentThrottlingError błąd]

Wzrost **wartości PercentThrottlingError** często występuje w tym samym czasie jako wzrost liczby żądań magazynu lub gdy początkowo testowanie obciążenia aplikacji. To może również objawiać w kliencie jako "503 Serwer zajęty" lub komunikaty o stanie "500 limit czasu operacji" HTTP z operacji magazynu.

#### <a name="transient-increase-in-PercentThrottlingError"></a>Przejściowy wzrost wartości PercentThrottlingError
Jeśli widzisz skokami wartości **wartości PercentThrottlingError** pokrywają się z okresy intensywnego działania aplikacji, następuje zaimplementowanie (nie liniowych) wycofań strategii wykładniczego ponownych prób w swoim kliencie. Wycofań ponownych prób odciążyć natychmiastowego partycji i pomóc aplikacji do wygładzania nagłych skoków ruchu. Aby uzyskać więcej informacji o tym, jak wdrożyć zasady ponawiania prób przy użyciu biblioteki klienta usługi Storage, zobacz [Namespace Microsoft.windowsazure.Storage.retrypolicies, gdy spełniają](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.retrypolicies.aspx).

> [!NOTE]
> Ponadto mogą pojawić się skokami wartości **wartości PercentThrottlingError** , nie pokrywają się z okresy intensywnego działania aplikacji: najbardziej prawdopodobna przyczyna to usługa magazynu, przenosząc partycji w celu lepszego równoważenia obciążenia.
>
>

#### <a name="permanent-increase-in-PercentThrottlingError"></a>Stały wzrost wartości PercentThrottlingError błąd
Jeśli widzisz charakteryzujące się stałym wysokim wartość **wartości PercentThrottlingError** następujące wzrost stały w woluminach transakcji, lub gdy wykonujesz usługi ładowania początkowego testów w swojej aplikacji, a następnie należy ocenić jak Twoja aplikacja używa magazynu partycji i tego, czy jego zbliża się do wartości docelowe skalowalności konta magazynu. Na przykład jeśli widzisz błędy w kolejce (która jest liczona jako jednej partycji) ograniczania przepływności, następnie należy rozważyć transakcji rozkłada się na wiele partycji przy użyciu funkcji kolejek dodatkowe. Jeśli widzisz błędy w tabeli ograniczania przepływności należy wziąć pod uwagę, aby rozłożyć transakcji na wiele partycji przy użyciu większej liczbie wartości klucza partycji przy użyciu innego schematu partycjonowania. Częstą przyczyną tego problemu jest prepend/dołączanie wzorzec przed gdzie wybierz datę jako klucza partycji, a następnie wszystkie dane w określonym dniu są zapisywane do jednej partycji: pod obciążeniem, może to spowodować wąskie gardło zapisu. Należy wziąć pod uwagę różnorodności partycjonowania albo ocenić, czy za pomocą magazynu obiektów blob może być lepszym rozwiązaniem. Również Sprawdź, czy ograniczenie występuje w wyniku skoków ruchu i zbadać sposoby wygładzanie deseń żądań.

Transakcji jest dystrybuowany na wielu partycjach, nadal należy pamiętać o limity skalowalności, ustaw dla konta magazynu. Na przykład jeśli użyto kolejek dziesięć przetwarzanie maksymalnie 2000 komunikatów o rozmiarze 1KB na sekundę, będzie na ogólny limit 20 000 komunikatów na sekundę dla konta magazynu. Jeśli potrzebujesz do przetwarzania ponad 20 000 jednostek na sekundę, należy rozważyć użycie wielu kont magazynu. Możesz należy również pamiętać, rozmiar żądań i jednostki ma wpływ na kiedy usługa magazynu ogranicza klientom: Jeśli masz większych żądań i jednostek, mogą możesz wcześniej ograniczone.

Projekt kwerendy nieefektywne może również spowodować trafień limity skalowalności do partycji tabeli. Na przykład zapytanie z filtrem, który wybiera tylko jeden procent jednostek w partycji, ale która skanuje wszystkie jednostki w partycji należy dostęp do każdej jednostki. Każda jednostka odczytu są zliczane łączną liczbę transakcji w tej partycji; w związku z tym można łatwo osiągnąć cele skalowalności.

> [!NOTE]
> Testowanie wydajności powinno ujawnić, wszystkie projekty nieefektywne zapytania w aplikacji.
>
>

### <a name="metrics-show-an-increase-in-PercentTimeoutError"></a>Metryki wskazują wzrost wzrost wartości PercentTimeoutError
Swoje metryki wskazują wzrost **wartości PercentTimeoutError** dla jednego z usługi storage. W tym samym czasie klient odbierze dużej liczby komunikatów o stanie "500 limit czasu operacji" HTTP z operacji magazynu.

> [!NOTE]
> Mogą pojawić się błędy przekroczenia limitu czasu tymczasowo jako usługi storage żądania równoważy obciążenia, przenosząc partycji na nowy serwer.
>
>

**Wartości PercentTimeoutError** Metryka to agregacji z następujących metryk: **ClientTimeoutError**, **AnonymousClientTimeoutError**, **SASClientTimeoutError**, **ServerTimeoutError**,  **AnonymousServerTimeoutError**, i **SASServerTimeoutError**.

Limity czasu serwera są spowodowane przez błąd na serwerze. Przekroczenia limitu czasu klienta się tak zdarzyć, ponieważ operacja na serwerze został przekroczony limit czasu określony przez klienta; na przykład klienta przy użyciu biblioteki klienta usługi Storage można ustawić limitu czasu dla operacji przy użyciu **ServerTimeout** właściwość **QueueRequestOptions** klasy.

Limity czasu serwera wskazywać na problem z usługą Magazyn, który wymaga dalszego badania. Metryki można użyć, aby zobaczyć, czy osiągasz limity skalowalności usługi i określić wszelkie skoki w ruchu, które mogą być przyczyną tego problemu. Jeśli ten problem występuje sporadycznie, może to być spowodowane równoważenia obciążenia działania w usłudze. Jeśli ten problem jest trwały i nie jest spowodowany przez aplikację osiągnięcia limitów skalowalności usługi, należy zgłosić problemu wymagającego pomocy technicznej. Dla limitów czasu klienta należy zdecydować, jeśli limit czasu jest ustawiona na odpowiednią wartość w klienta i albo zmień wartość limitu czasu w obiekcie klienta lub zbadać, jak możesz może zwiększyć wydajność operacji w usłudze magazynu, na przykład, optymalizując zapytania tabeli lub zmniejszenie rozmiaru wiadomości.

### <a name="metrics-show-an-increase-in-PercentNetworkError"></a>Metryki wskazują wzrost wzrost wartości PercentNetworkError
Swoje metryki wskazują wzrost **wartości PercentNetworkError** dla jednego z usługi storage. **Wartości PercentNetworkError** Metryka to agregacji z następujących metryk: **NetworkError**, **AnonymousNetworkError**, i **SASNetworkError**. Występują one w przypadku usługi storage wykrywa błąd sieciowy, gdy klient wysyła żądanie magazynu.

Najczęstszą przyczyną tego błędu jest klientem odłączania, zanim upłynie limit czasu w usłudze storage. Badanie kodu w swoim kliencie, aby zrozumieć, kiedy i dlaczego klient odłączy się od usługi storage. Aby zbadać problemy z połączeniem sieciowym z klienta, można użyć programu Wireshark, Microsoft Message Analyzer ani Tcping. Te narzędzia są opisane w [dodatki].

### <a name="the-client-is-receiving-403-messages"></a>Klient odbiera komunikaty HTTP 403 (zabronione)
Jeśli aplikacja kliencka zgłasza błędy HTTP 403 (zabronione), prawdopodobną przyczyną jest to, że klient używa wygasłej sygnatury dostępu współdzielonego podczas wysyłania żądania magazynu (chociaż inne możliwe przyczyny to niedokładność zegara, nieprawidłowe klucze i puste nagłówki). Jeśli przyczyną jest wygasły klucz sygnatury dostępu współdzielonego, nie będą widoczne żadne wpisy w danych dziennika rejestrowania danych magazynu po stronie serwera. W poniższej tabeli przedstawiono przykład z dziennika klienta wygenerowane z biblioteki klienta magazynu, który ilustruje ten problem:

| Element źródłowy | Poziom szczegółowości | Poziom szczegółowości | Identyfikator żądania klienta | Operacja tekstu |
| --- | --- | --- | --- | --- |
| Microsoft.WindowsAzure.Storage |Informacje |3 |85d077ab-… |Trwa uruchamianie operacji z lokalizacji podstawowej dla trybu lokalizacji PrimaryOnly. |
| Microsoft.WindowsAzure.Storage |Informacje |3 |85d077ab -… |Począwszy od żądanie synchroniczne <https://domemaildist.blob.core.windows.netazureimblobcontainer/blobCreatedViaSAS.txt?sv=2014-02-14&sr=c&si=mypolicy&sig=OFnd4Rd7z01fIvh%2BmcR6zbudIH2F5Ikm%2FyhNYZEmJNQ%3D&api-version=2014-02-14> |
| Microsoft.WindowsAzure.Storage |Informacje |3 |85d077ab -… |Oczekiwanie na odpowiedź. |
| Microsoft.WindowsAzure.Storage |Ostrzeżenie |2 |85d077ab -… |Wyjątek podczas oczekiwania na odpowiedź: Serwer zdalny zwrócił błąd: (403) Zabronione. |
| Microsoft.WindowsAzure.Storage |Informacje |3 |85d077ab -… |Odebrano odpowiedź. Kod stanu 403, identyfikator żądania = = 9d67c64a-64ed-4b0d-9515-3b14bbcdc63d, zawartość MD5 = element ETag =. |
| Microsoft.WindowsAzure.Storage |Ostrzeżenie |2 |85d077ab -… |Zgłoszono wyjątek podczas operacji: Serwer zdalny zwrócił błąd: (403) zabroniony... |
| Microsoft.WindowsAzure.Storage |Informacje |3 |85d077ab -… |Sprawdzanie, jeśli należy wykonać ponownie operację. Liczba ponownych prób = 0, kod stanu HTTP 403 i wyjątek = = Serwer zdalny zwrócił błąd: (403) zabroniony... |
| Microsoft.WindowsAzure.Storage |Informacje |3 |85d077ab -… |Ustawiono podstawowego, w oparciu o trybu lokalizacji w następnej lokalizacji. |
| Microsoft.WindowsAzure.Storage |Błąd |1 |85d077ab -… |Zasady ponawiania nie zezwolił na potrzeby ponawiania. Niepowodzenie z serwera zdalnego zwróciło błąd: (403) Zabronione. |

W tym scenariuszu należy zbadać, dlaczego tokenu sygnatury dostępu Współdzielonego wygaśnie, zanim klient wysyła ten token do serwera:

* Zazwyczaj nie należy ustawiać czasu rozpoczęcia, tworząc sygnaturę dostępu współdzielonego dla klienta przeznaczoną do natychmiastowego użycia. Jeśli istnieją małe różnice zegara między hostem generującym sygnaturę dostępu współdzielonego przy użyciu bieżącej godziny a usługą magazynu jest możliwe, że usługa magazynu otrzyma sygnaturę dostępu współdzielonego, która nie jest jeszcze ważna.
* Nie należy ustawiać bardzo krótkiego czasu wygaśnięcia w sygnaturze dostępu współdzielonego. W tym przypadku niewielkie różnice zegara między hostem generującym sygnaturę dostępu współdzielonego a usługą magazynu mogą prowadzić do tego, że sygnatura dostępu współdzielonego wygasa wcześniej, niż zakładano.
* Jest parametr wersji klucza sygnatury dostępu Współdzielonego (na przykład **sv = 2015-04-05**) są zgodne z wersją biblioteki klienta magazynu używasz? Firma Microsoft zaleca, aby zawsze używać najnowszej wersji programu [biblioteki klienta usługi Storage](https://www.nuget.org/packages/WindowsAzure.Storage/).
* Jeśli ponownie wygenerujesz klucze dostępu do magazynu, wszelkie istniejące tokeny SAS mogą zostać unieważnione. Ten problem może wystąpić, jeśli generujesz tokeny SAS z długim czasem wygaśnięcia dla aplikacji klienckich do przechowywania w pamięci podręcznej.

Jeśli używasz biblioteki klienta usługi magazynu do generowania tokenów SAS, utworzenie prawidłowego tokenu jest łatwe. Jednakże, jeśli są przy użyciu interfejsu API REST magazynu i ręcznie konstruowanie tokeny sygnatur dostępu Współdzielonego, zobacz [Delegowanie dostępu za pomocą podpisu dostępu współdzielonego](https://msdn.microsoft.com/library/azure/ee395415.aspx).

### <a name="the-client-is-receiving-404-messages"></a>Klient odbiera komunikaty HTTP 404 (nie znaleziono)
Jeśli aplikacja kliencka odbiera komunikat HTTP 404 (nie znaleziono) z serwera, oznacza to, że obiekt, którego klient próbował użyć (np. jednostka, tabela, obiekt blob, kontener lub kolejka) nie istnieje w usłudze magazynu. Istnieje kilka możliwych przyczyn tej sytuacji, takich jak:

* [Klient lub inny proces wcześniej usunął obiekt]
* [Problem autoryzacji dostępu sygnatury dostępu Współdzielonego]
* [Kod JavaScript po stronie klienta nie ma uprawnień dostępu do tego obiektu]
* [Błąd sieci]

#### <a name="client-previously-deleted-the-object"></a>Klient lub inny proces wcześniej usunięty obiekt
W scenariuszach, w którym klient próbuje odczytywania, aktualizowania lub usuwania danych w usłudze magazynu jest zwykle łatwo identyfikować ich w dziennikach po stronie serwera poprzedniej operacji, które usunięte danego obiektu z usługi storage. Często dane dziennika pokazuje, że innego użytkownika lub autoryzowany proces usunął obiektu. W dzienniku magazynu rejestrowania po stronie serwera typ operacji i żądany obiekt-kolumn klucza przedstawiające, gdy klient usunięty obiekt.

W scenariuszu, w którym klient próbuje wstawienia obiektu może nie być od razu widoczne Dlaczego to wyniki w odpowiedzi HTTP 404 (nie znaleziono), biorąc pod uwagę, że klient jest utworzenie nowego obiektu. Jednak jeśli klient tworzy obiekt blob musi być w stanie znaleźć kontener obiektów blob, jeśli klient tworzy komunikat informujący o tym, że musi być w stanie odnaleźć kolejki, a klient jest dodanie wiersza musi być w stanie można znaleźć w tabeli.

Aby uzyskać bardziej szczegółowe informacje o z gdy klient wysyła określone żądania do usługi storage, można użyć dziennika po stronie klienta z biblioteki klienta usługi Storage.

Następujący dziennik po stronie klienta, wygenerowany przez bibliotekę klienta usługi Storage zilustrowano problem, kiedy klient nie może odnaleźć kontenera obiektu blob, który, która zostanie utworzona. Ten dziennik zawiera szczegółowe informacje o następujących czynności:

| Identyfikator żądania | Operacja |
| --- | --- |
| 07b26a5d-... |**DeleteIfExists** metodę, aby usunąć kontener obiektów blob. Należy zauważyć, że ta operacja obejmuje **HEAD** żądanie do sprawdzania istnienia kontenera. |
| e2d06d78… |**CreateIfNotExists** metodę w celu utworzenia kontenera obiektów blob. Należy zauważyć, że ta operacja obejmuje **HEAD** żądania, który umożliwia sprawdzenie istnienia kontenera. **HEAD** zwraca komunikat 404, ale nadal. |
| de8b1c3c-... |**UploadFromStream** metodę w celu utworzenia obiektu blob. **Umieścić** żądanie kończy się niepowodzeniem z komunikatem 404 |

Wpisy dziennika:

| Identyfikator żądania | Operacja tekstu |
| --- | --- |
| 07b26a5d-... |Uruchamianie synchroniczne żądanie https://domemaildist.blob.core.windows.net/azuremmblobcontainer. |
| 07b26a5d-... |StringToSign = HEAD............x-ms-client-request-id:07b26a5d-....x-ms-date:Tue, 03 Jun 2014 10:33:11 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container. |
| 07b26a5d-... |Oczekiwanie na odpowiedź. |
| 07b26a5d-... |Odebrano odpowiedź. Kod stanu 200, identyfikator żądania = = eeead849... Zawartość MD5 = element ETag = &quot;0x8D14D2DC63D059B&quot;. |
| 07b26a5d-... |Nagłówki odpowiedzi zostały pomyślnie przetworzone, kontynuowanie z pozostałą częścią operacji. |
| 07b26a5d-... |Trwa pobieranie treści odpowiedzi. |
| 07b26a5d-... |Operacja ukończona pomyślnie. |
| 07b26a5d-... |Uruchamianie synchroniczne żądanie https://domemaildist.blob.core.windows.net/azuremmblobcontainer. |
| 07b26a5d-... |StringToSign = DELETE............x-ms-client-request-id:07b26a5d-....x-ms-date:Tue, 03 Jun 2014 10:33:12    GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container. |
| 07b26a5d-... |Oczekiwanie na odpowiedź. |
| 07b26a5d-... |Odebrano odpowiedź. Kod stanu = 202, identyfikator żądania = 6ab2a4cf-..., zawartość MD5 = element ETag =. |
| 07b26a5d-... |Nagłówki odpowiedzi zostały pomyślnie przetworzone, kontynuowanie z pozostałą częścią operacji. |
| 07b26a5d-... |Trwa pobieranie treści odpowiedzi. |
| 07b26a5d-... |Operacja ukończona pomyślnie. |
| e2d06d78-... |Uruchamianie asynchronicznego żądanie https://domemaildist.blob.core.windows.net/azuremmblobcontainer.</td> |
| e2d06d78-... |StringToSign = HEAD............x-ms-client-request-id:e2d06d78-....x-ms-date:Tue, 03 Jun 2014 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container. |
| e2d06d78-... |Oczekiwanie na odpowiedź. |
| de8b1c3c-... |Uruchamianie synchroniczne żądanie https://domemaildist.blob.core.windows.net/azuremmblobcontainer/blobCreated.txt. |
| de8b1c3c-... |StringToSign = PUT...64.qCmF+TQLPhq/YYK50mP9ZQ==........x-ms-blob-type:BlockBlob.x-ms-client-request-id:de8b1c3c-....x-ms-date:Tue, 03 Jun 2014 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer/blobCreated.txt. |
| de8b1c3c-... |Przygotowywanie do zapisywania danych na żądanie. |
| e2d06d78-... |Wyjątek podczas oczekiwania na odpowiedź: Serwer zdalny zwrócił błąd: (404) nie można odnaleźć... |
| e2d06d78-... |Odebrano odpowiedź. Kod stanu 404, identyfikator żądania = = 353ae3bc-..., zawartość MD5 = element ETag =. |
| e2d06d78-... |Nagłówki odpowiedzi zostały pomyślnie przetworzone, kontynuowanie z pozostałą częścią operacji. |
| e2d06d78-... |Trwa pobieranie treści odpowiedzi. |
| e2d06d78-... |Operacja ukończona pomyślnie. |
| e2d06d78-... |Uruchamianie asynchronicznego żądanie https://domemaildist.blob.core.windows.net/azuremmblobcontainer. |
| e2d06d78-... |StringToSign = PUT...0.........x-ms-client-request-id:e2d06d78-....x-ms-date:Tue, 03 Jun 2014 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container. |
| e2d06d78-... |Oczekiwanie na odpowiedź. |
| de8b1c3c-... |Dane żądania zapisu. |
| de8b1c3c-... |Oczekiwanie na odpowiedź. |
| e2d06d78-... |Wyjątek podczas oczekiwania na odpowiedź: Serwer zdalny zwrócił błąd: Konflikt (409)... |
| e2d06d78-... |Odebrano odpowiedź. Kod stanu = 409, identyfikator żądania = c27da20e-..., zawartość MD5 = element ETag =. |
| e2d06d78-... |Pobiera treść odpowiedzi błędu. |
| de8b1c3c-... |Wyjątek podczas oczekiwania na odpowiedź: Serwer zdalny zwrócił błąd: (404) nie można odnaleźć... |
| de8b1c3c-... |Odebrano odpowiedź. Kod stanu 404, identyfikator żądania = = 0eaeab3e-..., zawartość MD5 = element ETag =. |
| de8b1c3c-... |Zgłoszono wyjątek podczas operacji: Serwer zdalny zwrócił błąd: (404) nie można odnaleźć... |
| de8b1c3c-... |Zasady ponawiania nie zezwolił na potrzeby ponawiania. Niepowodzenie z serwera zdalnego zwróciło błąd: (404) nie można odnaleźć... |
| e2d06d78-... |Zasady ponawiania nie zezwolił na potrzeby ponawiania. Niepowodzenie z serwera zdalnego zwróciło błąd: Konflikt (409)... |

W tym przykładzie dziennika pokazują, że klient z przeplotem żądań z **CreateIfNotExists** — metoda (żądania identyfikator e2d06d78...) z żądaniami z **UploadFromStream** — metoda (de8b1c3c-...). Ta technologia odbywa się, ponieważ aplikacja kliencka asynchroniczne wywoływanie tych metod. Modyfikowanie kodu asynchronicznego w kliencie, aby upewnić się, tworzy kontener, przed podjęciem próby przekazania danych do obiektu blob w kontenerze. W idealnym przypadku należy utworzyć swoje kontenery z wyprzedzeniem.

#### <a name="SAS-authorization-issue"></a>Problem autoryzacji sygnatury dostępu współdzielonego (SAS)
Jeśli aplikacja kliencka podejmują próbę użycia klucza sygnatury dostępu Współdzielonego, który nie ma wystarczających uprawnień do operacji, usługi magazynu zwraca komunikat HTTP 404 (nie znaleziono) do klienta. W tym samym czasie, pojawi się także wartość niezerową **SASAuthorizationError** w metryki.

W poniższej tabeli przedstawiono przykładowy komunikat dziennika po stronie serwera, z pliku dziennika rejestrowania magazynu:

| Name (Nazwa) | Wartość |
| --- | --- |
| Czas rozpoczęcia żądania | 2014-05-30T06:17:48.4473697Z |
| Typ operacji     | GetBlobProperties            |
| Stan żądania     | SASAuthorizationError        |
| Kod stanu HTTP   | 404                          |
| Typ uwierzytelniania| Sygnatury dostępu współdzielonego                          |
| Typ usługi       | Obiekt blob                         |
| Adres URL żądania        | https://domemaildist.blob.core.windows.net/azureimblobcontainer/blobCreatedViaSAS.txt |
| &nbsp;                 |   ?sv=2014-02-14&sr=c&si=mypolicy&sig=XXXXX&;api-version=2014-02-14 |
| Nagłówka Identyfikatora żądania  | a1f348d5-8032-4912-93ef-b393e5252a3b |
| Identyfikator żądania klienta  | 2d064953-8436-4ee0-aa0c-65cb874f7929 |


Należy zbadać, dlaczego aplikacja kliencka próbuje wykonać operacji, dla którego go nie przyznano uprawnienia.

#### <a name="JavaScript-code-does-not-have-permission"></a>Kod języka JavaScript po stronie klienta nie ma uprawnień dostępu do obiektu
Jeśli używasz klienta JavaScript i usługi storage zwraca wiadomości HTTP 404, sprawdź następujące błędy języka JavaScript w przeglądarce:

```
SEC7120: Origin http://localhost:56309 not found in Access-Control-Allow-Origin header.
SCRIPT7002: XMLHttpRequest: Network Error 0x80070005, Access is denied.
```

> [!NOTE]
> Narzędzia programistyczne F12 w przeglądarce Internet Explorer służy do śledzenia komunikatów wymienianych między przeglądarką i usługi storage, rozwiązując problemy z językiem JavaScript po stronie klienta.
>
>

Te błędy, ponieważ implementuje przeglądarki sieci web [te same zasady pochodzenia](https://www.w3.org/Security/wiki/Same_Origin_Policy) pochodzi ograniczenia zabezpieczeń, który uniemożliwia wywołanie interfejsu API w innej domenie z domeny na stronie strony sieci web.

Aby obejść ten problem JavaScript, można skonfigurować między CORS Origin Resource Sharing () dla usługi storage, który uzyskuje dostęp do klienta. Aby uzyskać więcej informacji, zobacz [obsługi udostępniania zasobów między źródłami (CORS) dla usług Azure Storage](https://msdn.microsoft.com/library/azure/dn535601.aspx).

Poniższy przykładowy kod przedstawia sposób konfigurowania usługi obiektów blob umożliwia kod JavaScript w domenie Contoso dostępu obiektu blob w usłudze blob storage do:

```csharp
CloudBlobClient client = new CloudBlobClient(blobEndpoint, new StorageCredentials(accountName, accountKey));
// Set the service properties.
ServiceProperties sp = client.GetServiceProperties();
sp.DefaultServiceVersion = "2013-08-15";
CorsRule cr = new CorsRule();
cr.AllowedHeaders.Add("*");
cr.AllowedMethods = CorsHttpMethods.Get | CorsHttpMethods.Put;
cr.AllowedOrigins.Add("http://www.contoso.com");
cr.ExposedHeaders.Add("x-ms-*");
cr.MaxAgeInSeconds = 5;
sp.Cors.CorsRules.Clear();
sp.Cors.CorsRules.Add(cr);
client.SetServiceProperties(sp);
```

#### <a name="network-failure"></a>Błąd sieci
W niektórych sytuacjach pakietów sieciowych utracone może prowadzić do zwracania wiadomości HTTP 404 do klienta usługi storage. Na przykład, gdy Twoja aplikacja kliencka jest usunięcie jednostki z usługi tabel zobaczysz klienta zgłosić wyjątek magazynu raportowania "HTTP 404 (nie znaleziono)" komunikat o stanie z usługi tabel. Badania, w tabeli Usługa table storage, zobacz, czy usługa została usunięta jednostki, zgodnie z żądaniem.

Szczegóły wyjątku w obiekcie klienta obejmują przypisany przez usługę tabeli dla żądania identyfikator żądania (7e84f12d...): można użyć tych informacji do zlokalizowania szczegółów żądania w dzienników magazynu po stronie serwera, wyszukując w **nagłówka identyfikatora żądania**  kolumny w pliku dziennika. Aby zidentyfikować błędy takie, jak to wystąpić, a następnie pliki dziennika, na podstawie czasu metryki rejestrowane tego błędu można także użyć metryki. Ten wpis dziennika pokazuje, że usunięcie nie powiodło się z komunikatem o stanie "Client inny błąd HTTP (404)". Ten sam wpis dziennika zawiera również identyfikator żądania generowane przez klienta w **client-request-id** kolumny (813ea74f...).

Dziennik po stronie serwera obejmuje także inny wpis o takim samym **client-request-id** wartość (813ea74f...) dla pomyślnego usunięcia dla tej samej jednostki i z tego samego klienta. Operacja usuwania pomyślne miało miejsce wkrótce przed nieudane żądanie usunięcia.

Najbardziej prawdopodobną przyczyną tego scenariusza jest to, czy klient wysyłał żądania usunięcia dla jednostki w usłudze tabeli, która zakończyła się pomyślnie, ale nie otrzymano potwierdzenia od serwera (prawdopodobnie z powodu przejściowego problemu z siecią). Klient następnie automatycznie ponowione operacji (korzystając z tych samych **client-request-id**), a ta ponowna próba nie powiodła się, ponieważ jednostka już została usunięta.

Jeśli ten problem występuje często, powinieneś zbadać, dlaczego klient jest kończy się niepowodzeniem na odebranie potwierdzeń z usługi tabel. Jeśli ten problem występuje sporadycznie, należy wyłapać błąd "Nie znaleziono (404) HTTP" i zaloguj klienta, ale zezwalaj na klienta kontynuować.

### <a name="the-client-is-receiving-409-messages"></a>Klient odbiera komunikaty HTTP 409 (konflikt)
W poniższej tabeli przedstawiono wyciąg z dwóch operacji klienta w dzienniku po stronie serwera: **DeleteIfExists** a następnie natychmiast przez **CreateIfNotExists** przy użyciu tej samej nazwy kontenera obiektów blob. Każda operacja klienta powoduje dwa żądania wysyłane do serwera, najpierw **GetContainerProperties** żądania, aby sprawdzić, czy kontener istnieje, następuje **DeleteContainer** lub  **CreateContainer** żądania.

| Znacznik czasu | Operacja | Wynik | Nazwa kontenera | Identyfikator żądania klienta |
| --- | --- | --- | --- | --- |
| 05:10:13.7167225 |GetContainerProperties |200 |mmcont |c9f52c89-… |
| 05:10:13.8167325 |DeleteContainer |202 |mmcont |c9f52c89-… |
| 05:10:13.8987407 |GetContainerProperties |404 |mmcont |bc881924-… |
| 05:10:14.2147723 |CreateContainer |409 |mmcont |bc881924-… |

Kod w aplikacji klienckiej, usuwa i od razu ponownie utworzy w kontenerze obiektów blob przy użyciu tej samej nazwie: **CreateIfNotExists** — metoda (żądanie klienta identyfikator bc881924-...) po pewnym czasie zakończy się niepowodzeniem z powodu błędu HTTP 409 (konflikt). Kiedy klient usuwa kontenery obiektów blob, tabele lub kolejki, musi upłynąć pewien czas, zanim nazwa stanie się ponownie dostępna.

Aplikacja kliencka powinna używać unikatowych nazw kontenerów za każdym razem, gdy tworzy nowe kontenery, jeśli wzorzec usuń/utwórz ponownie jest typowy.

### <a name="metrics-show-low-percent-success"></a>Metryki wskazują PercentSuccess niski lub wpisy dziennika analytics ma operacji ze stanem transakcji ClientOtherErrors
**PercentSuccess** metryki przechwytuje procent operacje, które zakończyły się pomyślnie w oparciu o ich kod stanu HTTP. Operacje przy użyciu kody stanu 2XX liczone jako operacja się powiedzie, natomiast operacji przy użyciu kodów stanu w zakresach 3XX, 4XX i 5XX są liczone jako powiodło się i niższych **PercentSuccess** wartość metryki. W plikach dziennika magazynu po stronie serwera, te operacje są rejestrowane ze stanem transakcji **ClientOtherErrors**.

Należy pamiętać, że te operacje zostały zakończone powodzeniem i w związku z tym nie ma wpływu na inne metryki, takie jak dostępność. Niektóre operacje, wykonana pomyślnie, ale która może skutkować niepowodzeniem kodów stanu HTTP należą:

* **ResourceNotFound** (nie znaleziono 404), na przykład z żądania GET do obiektu blob, który nie istnieje.
* **ResourceAlreadyExists** (409 Konflikt), na przykład z **CreateIfNotExist** operacji, w której zasób już istnieje.
* **ConditionNotMet** (nie zmodyfikował 304), na przykład z operacją warunkowego, np. gdy klient wysyła **ETag** wartość i HTTP **If-None-Match** nagłówka żądania obrazu, tylko wtedy, gdy ma ona od ostatniej operacji zostały zaktualizowane.

Można znaleźć listę typowe kody błędów interfejsu API REST, które zwracają usług magazynu, na stronie [typowe kody błędów interfejsu API REST](https://msdn.microsoft.com/library/azure/dd179357.aspx).

### <a name="capacity-metrics-show-an-unexpected-increase"></a>Metryki pojemności pokazywać nieoczekiwany wzrost użycia pojemności magazynu
Jeśli widzisz nagłe, nieoczekiwane zmiany w wykorzystanie pojemności w ramach konta magazynu można zbadać przyczyny, analizując metryki dostępności; na przykład wzrost liczby żądań może prowadzić do zwiększenia ilości magazynu obiektów blob, którego używasz jako operacje oczyszczania specyficzne dla aplikacji, oczekiwane może mieć można zwolnić miejsca może nie działać zgodnie z oczekiwaniami (na przykład usuwanie nie powiodło się ponieważ wygasły tokeny sygnatur dostępu Współdzielonego umożliwiający Zwiększ ilość miejsca).

### <a name="your-issue-arises-from-using-the-storage-emulator"></a>Problem wynika z celów projektowania lub testowania przy użyciu emulatora magazynu
Zazwyczaj korzystanie z emulatora magazynu podczas tworzenia i testowania, aby uniknąć wymagania dla konta usługi Azure storage. Jakie są najczęstsze problemy występujące podczas korzystania z emulatora magazynu są:

* [Funkcja "X" nie działa w emulatorze magazynu]
* [Błąd "wartość dla jednego z nagłówków HTTP nie jest w niepoprawnym formacie." podczas korzystania z emulatora magazynu]
* [Uruchamianie emulatora magazynu wymaga uprawnień administracyjnych]

#### <a name="feature-X-is-not-working"></a>Funkcja "X" nie działa w emulatorze magazynu
Emulator magazynu nie obsługuje wszystkie funkcje usług Azure storage, takie jak usługa plików. Więcej informacji można znaleźć w temacie [Use the Azure Storage Emulator for Development and Testing](storage-use-emulator.md) (Używanie emulatora usługi Azure Storage do programowania i testowania).

W przypadku funkcji, które nie obsługuje emulatora magazynu należy użyć usługi Azure storage w chmurze.

#### <a name="error-HTTP-header-not-correct-format"></a>Błąd "wartość dla jednego z nagłówków HTTP nie jest w niepoprawnym formacie." podczas korzystania z emulatora magazynu
W przypadku testowania aplikacji, który używa biblioteki klienta usługi Storage dla magazynu lokalnego emulatora i metody wywołania, takich jak **CreateIfNotExists** zakończyć się niepowodzeniem z komunikatem o błędzie "wartość dla jednego z nagłówków HTTP nie jest prawidłowo Format". Oznacza to, że wersja emulatora magazynu, którego używasz nie obsługuje wersji biblioteki klienta magazynu, którego używasz. Biblioteka klienta magazynu dodaje nagłówek **x-ms-version** do wszystkich żądań to sprawia, że. Jeśli emulator magazynu nie rozpoznaje wartości w **x-ms-version** nagłówka, odrzuca żądanie.

Dzienniki klienta biblioteki magazynu można użyć, aby wyświetlić wartość **nagłówka x-ms-version** wysyła. Możesz też sprawdzić wartość **nagłówka x-ms-version** Jeśli używasz narzędzia Fiddler do śledzenia żądań z aplikacji klienckiej.

Ten scenariusz zazwyczaj występuje, jeśli należy zainstalować i korzystać z najnowszej wersji biblioteki klienta usługi Storage bez aktualizowania emulatora magazynu. Należy zainstalować najnowszą wersję z emulatora magazynu lub użyć magazynu w chmurze zamiast emulator do tworzenia i testowania.

#### <a name="storage-emulator-requires-administrative-privileges"></a>Uruchamianie emulatora magazynu wymaga uprawnień administracyjnych
Zostanie wyświetlony monit o poświadczenia administratora podczas uruchamiania emulatora magazynu. Dzieje się tak tylko wtedy, gdy są inicjowanie emulatora magazynu po raz pierwszy. Po zainicjują emulatora magazynu, nie trzeba uprawnienia administracyjne, aby uruchomić go ponownie.

Więcej informacji można znaleźć w temacie [Use the Azure Storage Emulator for Development and Testing](storage-use-emulator.md) (Używanie emulatora usługi Azure Storage do programowania i testowania). Można także zainicjować z emulatora magazynu w programie Visual Studio, która będzie również wymagają uprawnień administratora.

### <a name="you-are-encountering-problems-installing-the-Windows-Azure-SDK"></a>Występują problemy podczas instalowania zestawu Azure SDK dla platformy .NET
Podczas instalowania zestawu SDK, nie jest on próby zainstalowania emulatora magazynu na komputerze lokalnym. Dziennik instalacji zawiera jedną z następujących komunikatów:

* CAQuietExec:  Błąd: Nie można uzyskać dostępu do wystąpienia SQL
* CAQuietExec:  Błąd: Nie można utworzyć bazy danych

Przyczyną jest problem z istniejącej instalacji LocalDB. Domyślnie emulator magazynu używa LocalDB do utrwalenia danych, gdy jej symuluje usług Azure storage. Możesz zresetować wystąpienia LocalDB, uruchamiając następujące polecenia w oknie wiersza polecenia, zanim spróbujesz zainstalować zestaw SDK.

```
sqllocaldb stop v11.0
sqllocaldb delete v11.0
delete %USERPROFILE%\WAStorageEmulatorDb3*.*
sqllocaldb create v11.0
```

**Usuń** polecenie usuwa stare pliki bazy danych z poprzedniej instalacji emulatora magazynu.

### <a name="you-have-a-different-issue-with-a-storage-service"></a>Masz innym problemem z usługą storage
Jeśli poprzednie sekcje dotyczące rozwiązywania problemów nie zostanie uwzględniony są problemy z usługą storage, należy przyjąć następujące podejście do diagnozowania i rozwiązywania problemu.

* Sprawdź swoje metryki, aby sprawdzić, czy zmiany z Twojej oczekiwane zachowanie linii bazowej. Z metryk można określić, czy problem jest przejściowy lub stałe i operacje magazynu ma wpływ na ten problem.
* Informacjom metryki ułatwią Ci wyszukać dane dziennika po stronie serwera, aby uzyskać szczegółowe informacje o błędach, które pojawiają się. Te informacje mogą pomóc Ci rozwiązać problem.
* Jeśli informacje w dziennikach po stronie serwera nie jest wystarczające, aby rozwiązać ten problem uda, umożliwia dzienników po stronie klienta biblioteki klienta usługi Storage Zbadaj zachowanie aplikacji klienckiej i narzędzi, takich jak Fiddler, program Wireshark i Microsoft Analizator komunikatów do badania sieci.

Aby uzyskać więcej informacji na temat za pomocą programu Fiddler, zobacz "[dodatek 1: Za pomocą narzędzia Fiddler do przechwytywania ruchu HTTP i HTTPS]. "

Aby uzyskać więcej informacji o korzystaniu z programu Wireshark, zobacz "[Dodatek 2: Za pomocą programu Wireshark do przechwytywania ruchu sieciowego]. "

Aby uzyskać więcej informacji o korzystaniu z programu Microsoft Message Analyzer, zobacz "[dodatku 3: Do przechwytywania ruchu sieciowego przy użyciu programu Microsoft Message Analyzer]. "

## <a name="appendices"></a>Dodatki
Dodatki opisano kilka narzędzi, które mogą być przydatne podczas diagnozowania i rozwiązywania problemów przy użyciu usługi Azure Storage (i inne usługi). Narzędzia te nie są częścią usługi Azure Storage, a niektóre są produktów innych firm. W efekcie narzędzia omówione w te dodatki nie są obejmowane przez wszystkie umowy dotyczącej pomocy technicznej, które mogą wiązać Ciebie z Microsoft Azure lub usługi Azure Storage, a w związku z tym jako część procesu oceny należy sprawdzić opcje licencjonowania i pomocy technicznej dostępne od dostawców tych narzędzi.

### <a name="appendix-1"></a>Dodatek 1: Za pomocą narzędzia Fiddler do przechwytywania ruchu HTTP i HTTPS
[Narzędzie fiddler](https://www.telerik.com/fiddler) jest użytecznym narzędziem do analizowania ruchu HTTP i HTTPS między aplikacji klienckiej i usługi Azure storage.

> [!NOTE]
> Narzędzie fiddler mogą dekodować ruchu HTTPS. należy przeczytać dokumentację programu Fiddler dokładnie zrozumieć, jak to robi i zrozumienie skutki dla bezpieczeństwa.
>
>

Ten dodatek zawiera krótki przewodnik dotyczący sposobu konfigurowania narzędzia Fiddler do przechwytywania ruchu między komputera lokalnego, w którym jest zainstalowany program Fiddler i usług Azure storage.

Po uruchomieniu programu Fiddler rozpocznie Przechwytywanie ruchu HTTP i HTTPS na komputerze lokalnym. Poniżej przedstawiono kilka przydatnych poleceń do kontrolowania programu Fiddler:

* Uruchamiaj i zatrzymuj przechwytywania ruchu sieciowego. W menu głównym, przejdź do **pliku** a następnie kliknij przycisk **Przechwytywanie ruchu** do przełączenia przechwytywania włączać i wyłączać.
* Zapisz dane przechwycone ruchu. W menu głównym, przejdź do **pliku**, kliknij przycisk **Zapisz**, a następnie kliknij przycisk **wszystkie sesje**: dzięki temu można zapisać dane w pliku archiwum sesji. Załaduj ponownie z archiwum sesji później do analizy lub przesyła żądanie pomocy technicznej firmy Microsoft.

Aby ograniczyć ilość ruchu sieciowego, który przechwytuje Fiddler, możesz użyć filtrów, skonfigurowanych w **filtry** kartę. Poniższy zrzut ekranu przedstawia filtr, który przechwytuje tylko ruch wysyłany do **contosoemaildist.table.core.windows.net** punktu końcowego magazynu:

![][5]

### <a name="appendix-2"></a>Dodatek 2: Za pomocą programu Wireshark do przechwytywania ruchu sieciowego
[Program Wireshark](https://www.wireshark.org/) jest analizatora protokołów sieciowych, który umożliwia wyświetlenie pakietów szczegółowe informacje dla różnych protokołów sieciowych.

Poniższej procedury dowiesz się, jak przechwytywanie pakietów szczegółowe informacje dla ruchu z komputera lokalnego zainstalowano program Wireshark w usłudze tabel na koncie magazynu platformy Azure.

1. Uruchom program Wireshark na komputerze lokalnym.
2. W **Start** wybierz lokalnego interfejsu sieciowego lub interfejsów, które są połączone z Internetem.
3. Kliknij przycisk **opcje przechwytywania**.
4. Dodaj filtr do **filtr przechwytywania** pola tekstowego. Na przykład **hosta contosoemaildist.table.core.windows.net** skonfiguruje program Wireshark w celu przechwycenia tylko pakiety wysyłane do i z punkt końcowy usługi tabel w **contosoemaildist** konta magazynu. Zapoznaj się z [pełną listę filtrów przechwytywania](https://wiki.wireshark.org/CaptureFilters).

   ![][6]
5. Kliknij przycisk **Uruchom**. Program Wireshark teraz Przechwyć wszystkie pakiety Wyślij do lub z punkt końcowy usługi tabel, jak używać aplikacji klienta na komputerze lokalnym.
6. Po zakończeniu kliknij pozycję menu głównego **przechwytywania** i następnie **zatrzymać**.
7. Aby zapisać przechwycone dane w pliku przechwytywania programu Wireshark, w menu głównym kliknij **pliku** a następnie **Zapisz**.

Program WireShark wyróżni wszelkie błędy, które istnieją w **packetlist** okna. Można również użyć **eksperta informacje** okna (kliknij **analizy**, następnie **eksperta informacje**) aby wyświetlić podsumowanie błędów i ostrzeżeń.

![][7]

Możesz również wyświetlić dane TCP, jak warstwa aplikacji widzi on, klikając prawym przyciskiem myszy na danych TCP i wybierając **wykonaj Stream TCP**. Jest to przydatne, jeśli możesz przechwycić swoje zrzutu bez pliku przechwytywania. Aby uzyskać więcej informacji, zobacz [następujące strumienie TCP](https://www.wireshark.org/docs/wsug_html_chunked/ChAdvFollowTCPSection.html).

![][8]

> [!NOTE]
> Aby uzyskać więcej informacji na temat używania programu Wireshark zobacz [przewodnika użytkownicy programu Wireshark](https://www.wireshark.org/docs/wsug_html_chunked).
>
>

### <a name="appendix-3"></a>Dodatku 3: Do przechwytywania ruchu sieciowego przy użyciu programu Microsoft Message Analyzer
Korzystanie z analizatora komunikatów firmy Microsoft do przechwytywania ruchu HTTP i HTTPS, w sposób podobny do programu Fiddler i przechwytywania ruchu sieciowego w sposób podobny do programu Wireshark.

#### <a name="configure-a-web-tracing-session-using-microsoft-message-analyzer"></a>Konfigurowanie śledzenia sesji sieci web przy użyciu programu Microsoft Message Analyzer
Aby skonfigurować sesję śledzenia sieci web dla ruchu HTTP i HTTPS, przy użyciu programu Microsoft Message Analyzer, uruchom aplikację Microsoft Message Analyzer, a następnie na **pliku** menu, kliknij przycisk **przechwytywania/Trace**. Na liście dostępnych śledzenia scenariuszy wybierz **serwera Proxy sieci Web**. A następnie w obszarze **konfiguracji ze scenariusza śledzenia** panelu w **HostnameFilter** pola tekstowego, Dodaj nazwy punktów końcowych sieci magazynowania (można wyszukać te nazwy w [witryny Azure portal](https://portal.azure.com)). Na przykład, jeśli nazwa konta usługi Azure storage jest **contosodata**, należy dodać następujące polecenie, aby **HostnameFilter** pola tekstowego:

```
contosodata.blob.core.windows.net contosodata.table.core.windows.net contosodata.queue.core.windows.net
```

> [!NOTE]
> Spacja oddziela nazwy hostów.
>
>

Jeśli jesteś gotowy rozpocząć zbieranie danych śledzenia, kliknij przycisk **Start With** przycisku.

Aby uzyskać więcej informacji na temat programu Microsoft Message Analyzer **serwera Proxy sieci Web** śledzenia, zobacz [dostawcy Microsoft-PEF-WebProxy](https://technet.microsoft.com/library/jj674814.aspx).

Wbudowane **serwera Proxy sieci Web** śledzenia w Microsoft Message Analyzer opiera się na Fiddler; można przechwytywać ruch HTTPS po stronie klienta i wyświetli niezaszyfrowanej wiadomości protokołu HTTPS. **Serwera Proxy sieci Web** śledzenia działa przez konfigurowanie lokalnego serwera proxy dla całego ruchu HTTP i HTTPS, który zapewnia ona dostęp do wiadomości niezaszyfrowane.

#### <a name="diagnosing-network-issues-using-microsoft-message-analyzer"></a>Diagnozowanie problemów z siecią przy użyciu programu Microsoft Message Analyzer
Oprócz używania programu Microsoft Message Analyzer **serwera Proxy sieci Web** śledzenia, aby przechwycić szczegółowe informacje o ruchu HTTP/HTTPs między aplikacją kliencką a usługą magazynu, można również użyć wbudowanego **lokalnego warstwy Linku**  śledzenia do przechwytywania informacji pakietów sieciowych. Dzięki temu można przechwytywać dane podobne do tych, które można przechwycić za pomocą programu Wireshark i diagnozowanie problemów z siecią takich jak porzuconych pakietów.

Poniższy zrzut ekranu przedstawia przykład **lokalnego warstwy łącza** śledzenia z niektórymi **informacyjny** komunikatów w **DiagnosisTypes** kolumny. Klikając ikonę **DiagnosisTypes** kolumna pokazuje szczegóły komunikatu. W tym przykładzie serwer retransmitowane komunikat #305, ponieważ nie otrzymano potwierdzenia od klienta:

![][9]

Po utworzeniu sesji śledzenia w Microsoft Message Analyzer, można określić filtry, aby zmniejszyć ilość szumu w śladzie. Na **przechwytywania / Trace** strony, gdzie należy zdefiniować śledzenia, kliknij **Konfiguruj** łącze obok **Microsoft-Windows-NDIS-PacketCapture**. Poniższy zrzut ekranu przedstawia konfiguracji, który filtruje ruchu TCP dla adresów IP trzy usługi storage:

![][10]

Aby uzyskać więcej informacji na temat śledzenia Microsoft komunikatów analizatora lokalnego warstwy łącza zobacz [dostawcy Microsoft-PEF-NDIS-PacketCapture](https://technet.microsoft.com/library/jj659264.aspx).

### <a name="appendix-4"></a>Dodatek 4: Aby wyświetlić metryki i rejestrowanie danych za pomocą programu Excel
Wiele narzędzi umożliwiają pobranie danych metryk usługi Storage z usługi Azure table storage w formacie rozdzielanym, który można łatwo załadować dane do programu Excel do wyświetlania i analizy. Dane rejestrowania magazynu z magazynu obiektów blob platformy Azure jest już w formacie rozdzielanym, który można załadować do programu Excel. Jednak należy dodać odpowiednie nagłówki kolumn na podstawie informacji o [Format dziennika analizy magazynu](https://msdn.microsoft.com/library/azure/hh343259.aspx) i [schemat tabeli metryk usługi Analytics magazynu](https://msdn.microsoft.com/library/azure/hh343264.aspx).

Aby zaimportować rejestrowania magazynu danych do programu Excel, po pobraniu go z magazynu obiektów blob:

* Na **danych** menu, kliknij przycisk **tekst z**.
* Przejdź do pliku dziennika, aby wyświetlić, a następnie kliknij przycisk **importu**.
* W kroku 1 **Kreatora importu tekstu**, wybierz opcję **rozdzielany**.

W kroku 1 **Kreatora importu tekstu**, wybierz opcję **średnik** jako ogranicznika tylko i wybierz podwójnego cudzysłowu jako **kwalifikator tekstu**. Następnie kliknij przycisk **Zakończ** i wybierz miejsce umieszczenia danych w skoroszycie.

### <a name="appendix-5"></a>Dodatek 5: Monitorowanie za pomocą usługi Application Insights dla metodyki DevOps platformy Azure
Można również użyć funkcji usługi Application Insights dla metodyki DevOps platformy Azure, jako część swojej monitorowanie wydajności i dostępności. To narzędzie wykonywać następujące czynności:

* Upewnij się, że usługi sieci web jest dostępna i działa prawidłowo. Czy Twoja aplikacja jest witryną sieci web lub aplikacji urządzenia, która korzysta z usługi sieci web, go Przetestuj adres URL co kilka minut z lokalizacji na całym świecie i powiadomienie Cię o tym, jeśli występuje problem.
* Szybkie diagnozowanie problemów z wydajnością dowolnej wyjątków w usłudze sieci web. Dowiedz się, jeśli są rozciągnięcia procesora CPU lub innych zasobów, uzyskiwanie śladów stosu wyjątków i łatwo przeszukiwać dane dziennika śledzenia. Jeśli wydajność aplikacji spadnie poniżej wydatkami telekomunikacyjnymi, Microsoft może wysyłać wiadomości e-mail. Można monitorować usługi sieci web platformy .NET i Java.

Więcej informacji można znaleźć [co to jest usługa Application Insights](../../azure-monitor/app/app-insights-overview.md).

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat analizy w usłudze Azure Storage zobacz następujące zasoby:

* [Monitorowanie konta magazynu w witrynie Azure portal](storage-monitor-storage-account.md)
* [Analityka magazynu](storage-analytics.md)
* [Metryk usługi Storage analytics](storage-analytics-metrics.md)
* [Schematu tabeli metryk usługi Storage analytics](/rest/api/storageservices/storage-analytics-metrics-table-schema)
* [Magazynu analizy dzienników](storage-analytics-logging.md)
* [Format dziennika analityka magazynu](/rest/api/storageservices/storage-analytics-log-format)

<!--Anchors-->
[Wprowadzenie]: #introduction
[Sposób organizowania tego przewodnika]: #how-this-guide-is-organized

[Monitorowanie usługi storage]: #monitoring-your-storage-service
[Monitorowanie kondycji usługi]: #monitoring-service-health
[Monitorowanie wydajności]: #monitoring-capacity
[Monitorowanie dostępności]: #monitoring-availability
[Monitorowanie wydajności]: #monitoring-performance

[Diagnozowanie problemów z magazynowaniem]: #diagnosing-storage-issues
[Problemy dotyczące kondycji usługi]: #service-health-issues
[Problemy z wydajnością]: #performance-issues
[Diagnozowanie błędów]: #diagnosing-errors
[Problemy z emulatora magazynu]: #storage-emulator-issues
[Narzędzia rejestrowania magazynu]: #storage-logging-tools
[Za pomocą narzędzia rejestracji w sieci]: #using-network-logging-tools

[Śledzenia end-to-end]: #end-to-end-tracing
[Korelowanie danych dziennika]: #correlating-log-data
[Identyfikator żądania klienta]: #client-request-id
[Identyfikator żądania serwera]: #server-request-id
[Sygnatury czasowe]: #timestamps

[Wskazówki dotyczące rozwiązywania problemów]: #troubleshooting-guidance
[Metryki wskazują wysoką wartość AverageE2ELatency i niską wartość AverageServerLatency]: #metrics-show-high-AverageE2ELatency-and-low-AverageServerLatency
[Metryki wskazują niską wartość AverageE2ELatency i niską wartość AverageServerLatency, ale na kliencie występuje duże opóźnienie]: #metrics-show-low-AverageE2ELatency-and-low-AverageServerLatency
[Metryki wskazują wysoką wartość AverageServerLatency]: #metrics-show-high-AverageServerLatency
[Występują nieoczekiwane opóźnienia w dostarczaniu komunikatów w kolejce]: #you-are-experiencing-unexpected-delays-in-message-delivery

[Metryki wskazują wzrost wartości PercentThrottlingError]: #metrics-show-an-increase-in-PercentThrottlingError
[Przejściowy wzrost wartości PercentThrottlingError]: #transient-increase-in-PercentThrottlingError
[Stały wzrost wartości PercentThrottlingError błąd]: #permanent-increase-in-PercentThrottlingError
[Metryki wskazują wzrost wartości PercentTimeoutError]: #metrics-show-an-increase-in-PercentTimeoutError
[Metryki wskazują wzrost wartości PercentNetworkError]: #metrics-show-an-increase-in-PercentNetworkError

[Klient odbiera komunikaty HTTP 403 (zabronione)]: #the-client-is-receiving-403-messages
[Klient odbiera komunikaty HTTP 404 (nie znaleziono)]: #the-client-is-receiving-404-messages
[Klient lub inny proces wcześniej usunął obiekt]: #client-previously-deleted-the-object
[Problem autoryzacji dostępu sygnatury dostępu Współdzielonego]: #SAS-authorization-issue
[Kod JavaScript po stronie klienta nie ma uprawnień dostępu do tego obiektu]: #JavaScript-code-does-not-have-permission
[Błąd sieci]: #network-failure
[Klient odbiera komunikaty HTTP 409 (konflikt)]: #the-client-is-receiving-409-messages

[Metryki pokazują PercentSuccess niskim lub wpisy dziennika analytics ma operacji ze stanem transakcji ClientOtherErrors]: #metrics-show-low-percent-success
[Metryki pojemności Pokaż nieoczekiwane zwiększenie wykorzystania pojemności magazynu]: #capacity-metrics-show-an-unexpected-increase
[Problem wynika z przy użyciu emulatora magazynu dla rozwoju lub testowania]: #your-issue-arises-from-using-the-storage-emulator
[Funkcja "X" nie działa w emulatorze magazynu]: #feature-X-is-not-working
[Błąd "wartość dla jednego z nagłówków HTTP nie jest w niepoprawnym formacie." podczas korzystania z emulatora magazynu]: #error-HTTP-header-not-correct-format
[Uruchamianie emulatora magazynu wymaga uprawnień administracyjnych]: #storage-emulator-requires-administrative-privileges
[Pojawiły się problemy z instalacją zestawu Azure SDK dla platformy .NET]: #you-are-encountering-problems-installing-the-Windows-Azure-SDK
[Inny problem z usługą magazynu]: #you-have-a-different-issue-with-a-storage-service

[Dodatki]: #appendices
[Dodatek 1: Za pomocą narzędzia Fiddler do przechwytywania ruchu HTTP i HTTPS]: #appendix-1
[Dodatek 2: Za pomocą programu Wireshark do przechwytywania ruchu sieciowego]: #appendix-2
[Dodatku 3: Do przechwytywania ruchu sieciowego przy użyciu programu Microsoft Message Analyzer]: #appendix-3
[Dodatek 4: Aby wyświetlić metryki i rejestrowanie danych za pomocą programu Excel]: #appendix-4
[Dodatek 5: Monitorowanie za pomocą usługi Application Insights dla metodyki DevOps platformy Azure]: #appendix-5

<!--Image references-->
[1]: ./media/storage-monitoring-diagnosing-troubleshooting/overview.png
[3]: ./media/storage-monitoring-diagnosing-troubleshooting/hour-minute-metrics.png
[4]: ./media/storage-monitoring-diagnosing-troubleshooting/high-e2e-latency.png
[5]: ./media/storage-monitoring-diagnosing-troubleshooting/fiddler-screenshot.png
[6]: ./media/storage-monitoring-diagnosing-troubleshooting/wireshark-screenshot-1.png
[7]: ./media/storage-monitoring-diagnosing-troubleshooting/wireshark-screenshot-2.png
[8]: ./media/storage-monitoring-diagnosing-troubleshooting/wireshark-screenshot-3.png
[9]: ./media/storage-monitoring-diagnosing-troubleshooting/mma-screenshot-1.png
[10]: ./media/storage-monitoring-diagnosing-troubleshooting/mma-screenshot-2.png
