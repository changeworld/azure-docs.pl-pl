---
title: Monitorowanie, diagnozowanie i rozwiązywanie problemów z usługą Azure Storage | Dokumentacja firmy Microsoft
description: Korzystać z funkcji, takich jak analizy magazynu, rejestrowania po stronie klienta i innych narzędzi innych firm, aby zidentyfikować, diagnozowanie i rozwiązywanie problemów związanych z usługą Azure Storage.
services: storage
documentationcenter: ''
author: fhryo-msft
manager: jahogg
editor: tysonn
ms.assetid: d1e87d98-c763-4caa-ba20-2cf85f853303
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/11/2017
ms.author: fhryo-msft
ms.openlocfilehash: b89071048594e1e11efb321da3d0b48005824b46
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2018
ms.locfileid: "29740667"
---
# <a name="monitor-diagnose-and-troubleshoot-microsoft-azure-storage"></a>Monitorowanie, diagnozowanie i rozwiązywanie problemów z usługą Microsoft Azure Storage
[!INCLUDE [storage-selector-portal-monitoring-diagnosing-troubleshooting](../../../includes/storage-selector-portal-monitoring-diagnosing-troubleshooting.md)]

## <a name="overview"></a>Przegląd
Może być bardziej skomplikowane niż w tradycyjnych środowisk diagnozowania i rozwiązywania problemów w aplikacji rozproszonej hostowanych w środowisku chmury. Aplikacje można wdrażać w infrastrukturze PaaS lub IaaS lokalnie na urządzeniu przenośnym lub w kombinacji tych środowisk. Zazwyczaj ruchu sieciowego aplikacji może przechodzić między nimi sieci publicznych i prywatnych aplikacja może korzystać z wielu technologii magazynowania, takich jak Microsoft Azure magazynu tabel, obiektów blob, kolejek i przechowuje pliki oprócz innych danych, takie jak relacyjna i zarządzania dokumentami baz danych.

Do zarządzania pomyślnie takich aplikacji należy je monitorować aktywne i zrozumienie, jak zdiagnozować i rozwiązać wszystkie aspekty je i ich technologie zależne. Jako użytkownik usługi Azure Storage należy stale monitorowanie usług magazynu używanych przez aplikację nieoczekiwane zmiany w zachowaniu (na przykład wolniej niż zwykle reakcje) i używania funkcji rejestrowania do zbierania bardziej szczegółowych danych oraz do analizowania problemu szczegółowo. Informacje diagnostyczne, który można uzyskać od zarówno monitorowanie i rejestrowanie pomoże Ci w celu ustalenia głównej przyczyny problemu, napotkano aplikacji. Następnie można rozwiązać ten problem i określić odpowiednie kroki, które można wykonać w celu jego rozwiązania. Usługa Azure Storage jest podstawowa usługi Azure i stanowi ważną część większość rozwiązań, które klientów wdrożyć infrastrukturę platformy Azure. Magazyn Azure zawiera funkcje, aby uprościć monitorowanie, diagnozowanie i rozwiązywanie problemów z magazynowaniem w aplikacjach opartych na chmurze.

> [!NOTE]
> Usługa pliki Azure nie obsługuje rejestrowania w tej chwili.
> 

Przewodnik Podręcznik end-to-end rozwiązywania problemów w aplikacji usługi Azure Storage, zobacz [Rozwiązywanie problemów przy użyciu metryk usługi Azure Storage i rejestrowania, AzCopy i Message Analyzer End-to-End](../storage-e2e-troubleshooting.md).

* [Wprowadzenie]
  * [Jak jest zorganizowana w tym przewodniku]
* [monitorowania usługi magazynu]
  * [Monitorowanie kondycji usługi]
  * [Monitorowanie wydajności]
  * [Monitorowanie dostępności]
  * [Monitorowanie wydajności]
* [diagnozowanie problemów z magazynowaniem]
  * [Problemy z usługi kondycji]
  * [problemy z wydajnością]
  * [Diagnozowanie błędów]
  * [Problemy z emulatora magazynu]
  * [Narzędzia rejestrowania magazynu]
  * [Przy użyciu narzędzia rejestracji w sieci]
* [śledzenia End-to-end]
  * [Korelowanie dane dziennika]
  * [Identyfikator żądania klienta]
  * [identyfikator żądania serwera]
  * [Znaczniki czasu]
* [wskazówki rozwiązywania problemów]
  * [metryki pokazują AverageE2ELatency wysoki i niski AverageServerLatency]
  * [Metryki wskazują niską wartość AverageE2ELatency i niską wartość AverageServerLatency, ale na kliencie występuje duże opóźnienie]
  * [Metryki wskazują wysoką wartość AverageServerLatency]
  * [Występują nieoczekiwane opóźnienia w dostarczaniu komunikatów w kolejce]
  * [metryki spowodować wzrost PercentThrottlingError]
  * [metryki spowodować wzrost PercentTimeoutError]
  * [Metryki wskazują wzrost wartości PercentNetworkError]
  * [Klient odbiera komunikaty HTTP 403 (Dostęp zabroniony)]
  * [Klient odbiera komunikaty HTTP 404 (nie znaleziono)]
  * [Klient odbiera komunikaty HTTP 409 (konflikt)]
  * [metryki pokazują PercentSuccess niskim lub wpisy dziennika analytics ma operacji ze stanem transakcji ClientOtherErrors]
  * [Metryki pojemności Pokaż nieoczekiwane zwiększenie wykorzystania pojemności magazynu]
  * [Problem wynika z przy użyciu emulatora magazynu dla rozwoju lub testowania]
  * [Pojawiły się problemy z instalacją zestawu Azure SDK dla platformy .NET]
  * [Inny problem z usługą magazynu]
  * [Rozwiązywanie problemów z wirtualnych dysków twardych na maszynach wirtualnych systemu Windows](../../virtual-machines/windows/troubleshoot-vhds.md)   
  * [Rozwiązywanie problemów z wirtualnych dysków twardych na maszynach wirtualnych systemu Linux](../../virtual-machines/linux/troubleshoot-vhds.md)
  * [Rozwiązywanie problemów plików Azure z systemem Windows](../files/storage-troubleshoot-windows-file-connection-problems.md)   
  * [Rozwiązywanie problemów plików Azure z systemem Linux](../files/storage-troubleshoot-linux-file-connection-problems.md)
* [dodatki]
  * [dodatek 1: przy użyciu programu Fiddler do przechwytywania ruchu HTTP i HTTPS]
  * [dodatek 2: do przechwytywania ruchu sieciowego przy użyciu programu Wireshark]
  * [dodatku 3: do przechwytywania ruchu sieciowego przy użyciu programu Microsoft Message Analyzer]
  * [Dodatek 4: Przy użyciu programu Excel, aby wyświetlić metryki i dane dziennika]
  * [Dodatek 5: Monitorowanie za pomocą usługi Application Insights dla programu Visual Studio Team Services]

## <a name="introduction"></a>Wprowadzenie
W tym przewodniku przedstawiono sposób korzystania z funkcji, takich jak analiza magazynu Azure, klienta rejestrowania w biblioteki klienta magazynu Azure i innych narzędzi innych firm, aby zidentyfikować, diagnozowanie i rozwiązywanie problemów z usługą Azure Storage problemy związane z.

![][1]

Ten przewodnik jest przeznaczony do odczytu głównie przez deweloperów usług online, korzystających z usług magazynu Azure i specjalistów IT jest odpowiedzialny za zarządzanie takich usług online. Cele tego przewodnika są:

* Aby ułatwić obsługę kondycji i wydajności konta magazynu Azure.
* Aby udostępnić niezbędne procesów i narzędzi, aby określić, czy problem w aplikacji lub problem odnosi się do usługi Azure Storage.
* Aby zapewnić możliwością wskazówki dotyczące rozwiązywania problemów związanych z usługą Azure Storage.

### <a name="how-this-guide-is-organized"></a>Jak jest zorganizowana w tym przewodniku
Sekcja "[monitorowania usługi magazynu]" opisano sposób monitorowania kondycji i wydajności usługi Magazyn Azure przy użyciu metryk aplikacji usługi Azure Storage Analytics (metryk Storage).

Sekcja "[diagnozowanie problemów z magazynowaniem]" opisano, jak diagnozować problemy dotyczące korzystania z usługi Azure magazynu Analytics rejestrowania (rejestrowania magazynu). Zawiera również opis włączania rejestrowania po stronie klienta przy użyciu funkcji w jednej z bibliotek klienta takich jak biblioteki klienta usługi Storage dla platformy .NET lub zestawu Azure SDK dla języka Java.

Sekcja "[śledzenia End-to-end]" w tym artykule opisano, jak można skorelować informacje zawarte w różnych plikach dziennika i dane metryk.

Sekcja "[wskazówki rozwiązywania problemów]" zawiera wskazówki dotyczące rozwiązywania problemów, dla niektóre typowe związane z magazynowaniem problemów, które mogą wystąpić.

"[dodatki]" zawierają informacje o przy użyciu innych narzędzi, takich jak Wireshark i Netmon analizowanie sieci pakietów danych, narzędzia Fiddler do analizowania komunikaty HTTP/HTTPS, a dane dziennika programu Microsoft Message Analyzer dla korelacji.

## <a name="monitoring-your-storage-service"></a>Monitorowanie usługi magazynu
Jeśli znasz monitorowania wydajności systemu Windows, można traktować metryki magazynu jako odpowiednik liczników monitora wydajności systemu Windows Azure Storage. Metryki magazynu zawiera rozbudowany zestaw metryki (liczniki w terminologii Monitor wydajności systemu Windows), np. dostępność usługi, łączna liczba żądań do usługi lub wartość procentowa pomyślnych żądań do usługi. Aby uzyskać pełną listę dostępnych metryk, zobacz [schemat tabeli metryki analityka magazynu](http://msdn.microsoft.com/library/azure/hh343264.aspx). Można określić, czy mają usługi magazynu do zbieranie i agregowanie metryki co godzinę lub co minutę. Aby uzyskać więcej informacji o tym, jak włączyć metryki i monitorowanie kont magazynu, zobacz [Włączanie metryk magazynu i wyświetlanie danych metryk](http://go.microsoft.com/fwlink/?LinkId=510865).

Można wybrać, które co godzinę metryk, które mają być wyświetlane w [portalu Azure](https://portal.azure.com) i skonfigurować reguły powiadamiania administratorów za pośrednictwem poczty e-mail, przy każdym metrykę co godzinę przekracza określonego progu. Aby uzyskać więcej informacji, zobacz [odbieranie powiadomień o alertach](/azure/monitoring-and-diagnostics/monitoring-overview-alerts). 

Usługa magazynu zbiera metryki przy użyciu podjęto najlepsze możliwe działanie, ale nie można rejestrować każdej operacji magazynu.

W portalu Azure można wyświetlić metryki, np. dostępność, całkowita liczba żądań i numery Średni czas oczekiwania dla konta magazynu. Aby ostrzec administratora, jeśli dostępności spadnie poniżej określonego poziomu również ustawiono regułę powiadomień. Wyświetlać te dane, jednego obszaru możliwych do badania jest tabeli procent powodzenia usługi, są poniżej 100% (Aby uzyskać więcej informacji, zobacz sekcję "[metryki pokazują PercentSuccess niskim lub wpisy dziennika analytics ma operacji ze stanem transakcji ClientOtherErrors]").

Należy monitorować stale Azure aplikacji upewnij się, że są one dobrej kondycji i wydajności, zgodnie z oczekiwaniami:

* Niektóre metryki planu bazowego dla aplikacji, która umożliwi ustanawianie porównywania bieżące dane i zidentyfikować wszelkie istotne zmiany w zachowaniu magazynu Azure i aplikacji. Wartości metryk z linii bazowej w wielu przypadkach będzie określone dla aplikacji i należy je określić w przypadku testowania aplikacji.
* Rejestrowanie minuty metryki i ich użycie w celu monitorowania aktywnie nieoczekiwane błędy i anomalie, takich jak największego błąd liczby lub liczby żądań.
* Rejestrowanie metryki co godzinę i ich użycie w celu monitorowania takich jak średnie wartości średniej liczby błędów i liczby żądań.
* Badania potencjalnych problemów za pomocą narzędzi diagnostycznych, zgodnie z opisem później w sekcji "[diagnozowanie problemów z magazynowaniem]."

Wykresów na poniższej ilustracji przedstawiono, jak uśrednianie występuje dla metryki co godzinę można ukryć wzrostów w działaniu. Do wyświetlenia podczas minutę metryki ujawnić zmianami, które naprawdę odbywają stała liczba żądań, są wyświetlane metryki co godzinę.

![][3]

W pozostałej części tej sekcji opisano metryki, które należy monitorować i dlaczego.

### <a name="monitoring-service-health"></a>Monitorowanie kondycji usługi
Można użyć [portalu Azure](https://portal.azure.com) umożliwiających wyświetlenie kondycji usługi magazynu (i innych usług platformy Azure) we wszystkich regionach platformy Azure na świecie. Umożliwia monitorowanie można natychmiast zobaczyć, czy problem poza formantu ma wpływ na usługi magazynu w regionie, w którym można użyć w aplikacji.

[Portalu Azure](https://portal.azure.com) można też podać powiadomienia zdarzenia mające wpływ na różne usługi platformy Azure.
Uwaga: Te informacje wcześniej była dostępna, wraz z danych historycznych w [pulpicie nawigacyjnym usługi Azure](http://status.azure.com).

Gdy [portalu Azure](https://portal.azure.com) zbiera informacje o kondycji z wewnątrz centrach danych platformy Azure (poza wewnątrz monitorowanie), możesz również przyjęcie podejścia zewnątrz do generowania transakcji syntetycznych, które okresowo uzyskują dostęp do aplikacji sieci web hostowanymi na platformie Azure z wielu lokalizacji. Usługami oferowanym przez [Dynatrace](http://www.dynatrace.com/en/synthetic-monitoring) i usługi Application Insights dla programu Visual Studio Team Services są przykładem. Aby uzyskać więcej informacji dotyczących usługi Application Insights dla programu Visual Studio Team Services, zobacz dodatku "[dodatek 5: monitorowanie za pomocą usługi Application Insights dla programu Visual Studio Team Services](#appendix-5)."

### <a name="monitoring-capacity"></a>Monitorowanie wydajności
Ponieważ obiekty BLOB zwykle konta największą część przechowywanych danych metryki magazynu tylko przechowuje metryki pojemności dla usługi obiektów blob (w czasie zapisywania go nie jest możliwe monitorowanie pojemności tabel i kolejek metryki magazynu). Te dane w można znaleźć **$MetricsCapacityBlob** tabeli, jeśli jest włączone monitorowanie usługi Blob. Metryki magazynu rejestruje dane raz dziennie i może używać wartości **RowKey** ustalenie, czy wiersz zawiera jednostki, która odnosi się do danych użytkownika (wartość **danych**) lub dane analityczne (wartość **analytics**). Każdej jednostki przechowywanej zawiera informacje dotyczące ilości miejsca używane (**pojemności** mierzony w bajtach) i bieżącą liczbę kontenerów (**ContainerCount**) i obiektów blob (**ObjectCount**) używany w ramach konta magazynu. Aby uzyskać więcej informacji o metryki pojemności przechowywane w **$MetricsCapacityBlob** tabeli, zobacz [schemat tabeli metryki analityka magazynu](http://msdn.microsoft.com/library/azure/hh343264.aspx).

> [!NOTE]
> Należy monitorować te wartości wczesne ostrzeżenie, że zbliża się granicach pojemności konta magazynu. W portalu Azure można dodać reguły alertów, powiadomień o użycie agregacji magazynu przekracza lub spada poniżej progów, które określisz.
> 
> 

Aby uzyskać pomoc, Trwa szacowanie rozmiaru magazynu różnych obiektów, takich jak obiekty BLOB, zobacz wpis w blogu [opis Azure magazynu rozliczeń — przepustowość, transakcje i pojemności](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx).

### <a name="monitoring-availability"></a>Monitorowanie dostępności
Należy monitorować dostępności usług magazynowania na koncie magazynu przez monitorowanie wartość **dostępności** kolumn w tabelach metryki godzinowe i minutowe — **$MetricsHourPrimaryTransactionsBlob**, **$MetricsHourPrimaryTransactionsTable**, **$MetricsHourPrimaryTransactionsQueue**, **$MetricsMinutePrimaryTransactionsBlob**, **$MetricsMinutePrimaryTransactionsTable**, **$MetricsMinutePrimaryTransactionsQueue**, **$MetricsCapacityBlob**. **Dostępności** kolumna zawiera wartość procentową, która wskazuje dostępność usługi lub operacji interfejsu API reprezentowany przez wiersz ( **RowKey** pokazuje, czy wiersz zawiera metryki dla usługi jako całość lub dla określonej operacji interfejsu API).

Wszystkie wartości mniejszej niż 100% wskazuje, że niektóre żądania magazynu kończą się niepowodzeniem. Widać, dlaczego ulegają awarii, sprawdzając innych kolumn, takich jak wyświetlanie liczby żądań z różnych error — typy danych metryki **ServerTimeoutError**. Należy się spodziewać **dostępności** tymczasowo spadek poniżej 100% powodów, takich jak limity czasu przejściowa serwera podczas usługi przenosi partycji do lepszego żądania Równoważenie obciążenia; Logika ponawiania w aplikacji klienta powinna obsługiwać takie warunki tymczasowymi. Artykuł [operacje rejestrowane analityka magazynu i komunikaty o stanie](http://msdn.microsoft.com/library/azure/hh343260.aspx) zawiera listę typów transakcji, które zawiera metryki magazynu w jego **dostępności** obliczeń.

W [portalu Azure](https://portal.azure.com), można dodać reguły alertów, powiadomień o **dostępności** dla usługi spadnie poniżej progu, który określisz.

"[wskazówki rozwiązywania problemów]" tego przewodnika opisano niektóre typowe problemy usługi magazynu związanych z dostępnością.

### <a name="monitoring-performance"></a>Monitorowanie wydajności
Aby monitorować wydajność usług magazynu, służy następujące metryki z tabel godzinowe i minutowe metryki.

* Wartości w **AverageE2ELatency** i **AverageServerLatency** kolumny zawierają Średni czas usługi magazynu lub typ operacji interfejsu API trwa proces żądania. **AverageE2ELatency** jest miarą end-to-end opóźnienia, które zawiera czas potrzebny na żądanie do odczytu i wysyłania odpowiedzi oprócz czas przetwarzania żądania (w związku z tym obejmuje opóźnienia sieci, gdy żądanie dotrze magazynu Usługa); **AverageServerLatency** to miara tylko czas przetwarzania i dlatego nie obejmuje opóźnienia sieci, wszystkie powiązane z komunikacji z klientem. Zobacz sekcję "[metryki pokazują AverageE2ELatency wysoki i niski AverageServerLatency]" w dalszej części tego przewodnika, aby uzyskać informacje dotyczące przyczyny może występować znaczące różnice między tymi dwoma wartościami.
* Wartości w **TotalIngress** i **TotalEgress** kolumny zawierają łączną ilość danych, w bajtach przychodzących na i przechodzi do poza usługą magazynu lub za pomocą określonego typu operacji interfejsu API.
* Wartości w **TotalRequests** kolumnie wyświetlane całkowita liczba żądań, które otrzymuje operacji interfejsu API usługi magazynu. **TotalRequests** jest to całkowita liczba żądań, które otrzymuje usługi magazynu.

Zazwyczaj monitorujesz nieoczekiwane zmiany w dowolnej z tych wartości jako wskaźnik, czy masz wymagane jest zbadanie problemu.

W [portalu Azure](https://portal.azure.com), można dodać reguły alertów, powiadomień o dowolnej metryki wydajności dla tej usługi spadną poniżej lub przekracza wartość progową, który określisz.

"[wskazówki rozwiązywania problemów]" tego przewodnika opisano niektóre typowe problemy usługi magazynu związanych z wydajnością.

## <a name="diagnosing-storage-issues"></a>Diagnozowanie problemów z magazynowaniem
Istnieje wiele sposobów, czy użytkownik może zostaną powiadomieni o problem lub problem w aplikacji, w tym:

* Poważnej awarii, który powoduje, że aplikacja do awarii lub przestanie działać.
* Znaczące zmiany z linii bazowej wartości metryk monitorowania zgodnie z opisem w poprzedniej sekcji "[monitorowania usługi magazynu]."
* Raporty z użytkowników aplikacji, że niektóre określonej operacji nie została ukończona, zgodnie z oczekiwaniami lub nie działa niektórych funkcji.
* Błędy wygenerowane w aplikacji pojawiających się w plikach dziennika lub za pomocą innej metody powiadomień.

Zazwyczaj problemy związane z usług Azure storage należą do jednej z czterech szerokie kategorie:

* Aplikacja ma problem z wydajnością, zgłoszone przez użytkowników albo ujawniony z powodu zmian w metryki wydajności.
* Występuje problem z infrastrukturą usługi Azure Storage w jeden lub więcej regionów.
* Aplikacja napotyka błąd zgłoszony przez użytkowników albo ujawniony przez zwiększenie w jednym z monitorowanych przez Ciebie metryk liczba błędów.
* Podczas projektowania i testowania może używać lokalnym emulatorze magazynu; mogą wystąpić problemy związane z użycia emulatora magazynu.

W poniższych sekcjach opisano kroki należy wykonać, aby zdiagnozować i rozwiązać problemy w każdej z tych czterech kategorii. Sekcja "[wskazówki rozwiązywania problemów]" dalszej części tego podręcznika zawiera więcej szczegółów dla niektórych typowych problemów, które mogą wystąpić.

### <a name="service-health-issues"></a>Problemy z usługi kondycji
Problemy z usługi kondycji są zazwyczaj poza formantu. [Portalu Azure](https://portal.azure.com) zawiera informacje dotyczące bieżących problemów z usługami Azure, takich jak usługi magazynu. Jeśli zostanie wybrana opcja dostęp do odczytu z magazynu geograficznie nadmiarowego magazynu podczas tworzenia konta magazynu, następnie jeśli danych przestanie być dostępny w lokalizacji głównej aplikacji można przełączyć tymczasowo kopia tylko do odczytu w lokalizacji dodatkowej. Do odczytu z lokacji dodatkowej, aplikacja musi mieć możliwość przełączania się między przy użyciu lokalizacji magazynów podstawowych i pomocniczych i mieć możliwość pracy w trybie ograniczonej funkcjonalności danych tylko do odczytu. Biblioteki klienta magazynu Azure umożliwiają definiowanie zasady ponawiania, który może odczytywać dane z magazynu pomocniczego w przypadku błędu odczytu z magazynu głównego. Aplikacja musi należy pamiętać, że danych w dodatkowej lokalizacji jest ostatecznie spójna. Aby uzyskać więcej informacji, zobacz w blogu [opcje nadmiarowość magazynu Azure i dostęp do odczytu z magazynu geograficznie nadmiarowego magazynu](https://blogs.msdn.microsoft.com/windowsazurestorage/2013/12/11/windows-azure-storage-redundancy-options-and-read-access-geo-redundant-storage/).

### <a name="performance-issues"></a>problemy z wydajnością
Wydajność aplikacji może być wartością subiektywną, zwłaszcza z punktu widzenia użytkownika. Dlatego należy mieć dostępne metryki linii bazowej, które ułatwiają wykrywanie problemów z wydajnością. Wiele czynników, może mieć wpływ na wydajność usługi magazynu platformy Azure z punktu widzenia aplikacji klienta. Te czynniki mogą działać w usłudze magazynowania, klienta lub infrastruktury sieciowej; Dlatego ważne jest przyjąć strategię identyfikowanie pochodzenia problem z wydajnością.

Po zidentyfikowaniu prawdopodobnie lokalizację przyczynę problem z wydajnością z metryki, następnie służy pliki dziennika można znaleźć szczegółowe informacje, aby zdiagnozować i rozwiązać problem.

Sekcja "[wskazówki rozwiązywania problemów]" dalszej części tego przewodnika uzyskać więcej informacji o niektórych typowych problemów związanych z wydajnością można napotkać.

### <a name="diagnosing-errors"></a>Diagnozowanie błędów
Użytkownicy aplikacji mogą informować o błędów zgłoszonych przez aplikację klienta. Takie jak magazyn metryki liczby typów inny błąd z usług magazynu rejestruje także **NetworkError**, **ClientTimeoutError**, lub **AuthorizationError**. Gdy metryki magazynu rejestruje tylko liczby o różnych typów, można uzyskać szczegółowe informacje o poszczególnych żądań, przeglądając po stronie serwera, po stronie klienta oraz dzienniki sieci. Zazwyczaj kod stanu HTTP zwrócony przez usługę magazynu zapewni wskazanie dlaczego żądanie zawiodło.

> [!NOTE]
> Należy pamiętać, że można oczekiwać sporadyczne błędy: na przykład błędy z powodu przejściowe warunki sieciowe lub błędów aplikacji.
> 
> 

Przydatne w przypadku opis kodów stanu i błędów związanych z magazynu są następujące zasoby:

* [Typowe kody błędów interfejsu API REST](http://msdn.microsoft.com/library/azure/dd179357.aspx)
* [Kody błędów usługi blob](http://msdn.microsoft.com/library/azure/dd179439.aspx)
* [Kody błędów usługi kolejki](http://msdn.microsoft.com/library/azure/dd179446.aspx)
* [Kody błędów usługi tabel](http://msdn.microsoft.com/library/azure/dd179438.aspx)
* [Kody błędów usługi plików](https://msdn.microsoft.com/library/azure/dn690119.aspx)

### <a name="storage-emulator-issues"></a>Problemy z emulatora magazynu
Zestaw Azure SDK zawiera emulator magazynu, które można uruchamiać na deweloperskiej stacji roboczej. Emulator tego symuluje większość zachowanie usług magazynu platformy Azure i jest przydatne podczas programowania i testowania, co pozwala na uruchamianie aplikacji, które korzystają z usług magazynu platformy Azure bez konieczności subskrypcji platformy Azure i konto magazynu platformy Azure.

"[wskazówki rozwiązywania problemów]" tego przewodnika opisano niektóre typowe problemy napotkano przy użyciu emulatora magazynu.

### <a name="storage-logging-tools"></a>Narzędzia rejestrowania magazynu
Rejestrowanie magazynu zapewnia po stronie serwera rejestrowanie żądań magazynu na koncie magazynu Azure. Aby uzyskać więcej informacji na temat włączania rejestrowania zdarzeń po stronie serwera i uzyskać dostęp do danych dziennika, zobacz [Włączanie rejestrowania magazynu i uzyskiwanie dostępu do danych dziennika](http://go.microsoft.com/fwlink/?LinkId=510867).

Biblioteki klienta usługi Storage dla platformy .NET umożliwia zbieranie danych dziennika po stronie klienta, które odnoszą się do magazynu wykonywany przez aplikację. Aby uzyskać więcej informacji, zobacz [Client-side Logging with the .NET Storage Client Library](http://go.microsoft.com/fwlink/?LinkId=510868) (Logowanie po stronie klienta przy użyciu biblioteki klienckiej usługi .NET Storage).

> [!NOTE]
> W niektórych sytuacjach (np. awarii autoryzacji sygnatury dostępu Współdzielonego) użytkownik może zgłosić błąd, który można znaleźć żadnych danych żądania w dziennikach magazynu po stronie serwera. Można korzystać z funkcji rejestrowania biblioteki klienta magazynu Zbadaj, czy przyczyną problemu jest na kliencie lub użyj narzędzia monitorowania sieci do sprawdzania, czy w sieci.
> 
> 

### <a name="using-network-logging-tools"></a>Przy użyciu narzędzia rejestracji w sieci
Można przechwytywać ruch między klientem i serwerem zawierają szczegółowe informacje o danych, które są wymiana klient i serwer i podstawowych warunków sieciowych. Narzędzia rejestrowania przydatne sieci obejmują:

* [Fiddler](http://www.telerik.com/fiddler) to bezpłatna sieci web profilowanie serwera proxy, który umożliwia Sprawdź, czy nagłówki i danych ładunku komunikatów żądań i odpowiedzi HTTP i HTTPS. Aby uzyskać więcej informacji, zobacz [dodatku 1: przy użyciu narzędzia Fiddler do przechwytywania ruchu HTTP i HTTPS](#appendix-1).
* [Monitor sieci firmy Microsoft (Netmon)](http://www.microsoft.com/download/details.aspx?id=4865) i [Wireshark](http://www.wireshark.org/) są wolnej sieci analizatory protokołu, które umożliwiają wyświetlanie pakietów szczegółowe informacje o wiele różnych protokołów sieciowych. Aby uzyskać więcej informacji na temat programu Wireshark, zobacz "[dodatek 2: przy użyciu programu Wireshark do przechwytywania ruchu sieciowego](#appendix-2)".
* Programu Microsoft Message Analyzer jest narzędziem do firmy Microsoft, która zastępuje Monitor sieci, które oprócz przechwytywania danych pakietów sieciowych, pomaga do wyświetlania i analizowania danych dziennika przechwycone z innych narzędzi. Aby uzyskać więcej informacji, zobacz "[dodatku 3: przy użyciu programu Microsoft Message Analyzer do przechwytywania ruchu sieciowego](#appendix-3)".
* Jeśli chcesz wykonać test podstawowej łączności, aby sprawdzić, czy komputer klienta można połączyć się z usługą magazynu platformy Azure za pośrednictwem sieci, nie możesz tego zrobić przy użyciu standardu **ping** narzędzia na kliencie. Można jednak użyć [ **tcping** narzędzie](http://www.elifulkerson.com/projects/tcping.php) Aby sprawdzić łączność.

W wielu przypadkach danych dziennika z rejestrowania magazynu i biblioteki klienta usługi Storage będą wystarczające, aby zdiagnozować problem, ale w niektórych scenariuszach może być konieczne bardziej szczegółowe informacje, które zapewniają te narzędzia rejestrowania w sieci. Na przykład aby wyświetlać komunikaty HTTP i HTTPS przy użyciu narzędzia Fiddler umożliwia wyświetlenie nagłówka i ładunku dane wysyłane do i z usług magazynu, które umożliwiłyby zbadać, jak aplikacja kliencka ponownych prób operacji magazynu. Protokół analizatorów, takich jak Wireshark działają na poziomie pakietów, dzięki któremu można wyświetlić danych TCP, która umożliwia rozwiązywanie problemów z utraty pakietów i problemy z łącznością. Message Analyzer może działać z warstwy protokołów HTTP i TCP.

## <a name="end-to-end-tracing"></a>Śledzenia end-to-end
Przy użyciu różnych plikach dziennika śledzenia end-to-end to technika przydatne do badania potencjalnych problemów. Możesz skorzystać z informacji daty/godziny z danych metryki, jako wskazanie gdzie rozpocząć wyszukiwanie w plikach dziennika, aby uzyskać szczegółowe informacje, które mogą pomóc rozwiązać ten problem.

### <a name="correlating-log-data"></a>Korelowanie dane dziennika
Podczas wyświetlania dzienniki za pośrednictwem aplikacji klienckich, śledzi sieci i żądań rejestrowania magazynu po stronie serwera, który jest bardzo istotne, aby można było do skorelowania w różnych plikach dziennika. Pliki dziennika zawierają wiele różnych pól, które są przydatne jako identyfikatorów korelacji. Identyfikator żądania klienta jest najbardziej przydatna pola służące do skorelowania wpisów w różnych dzienników. Jednak czasami może być przydatne do używania serwera Identyfikatora żądania lub sygnatur czasowych. Więcej informacji o tych opcjach można znaleźć w poniższych sekcjach.

### <a name="client-request-id"></a>Identyfikator żądania klienta
Biblioteka klienta magazynu automatycznie generuje identyfikator żądania klienta unikatowy dla każdego żądania.

* W dzienniku po stronie klienta tworzy bibliotekę klienta usługi Storage, identyfikator żądania klienta jest wyświetlana w **Identyfikatora żądania klienta** pola każdego wpisu dziennika odnoszących się do żądania.
* W śledzenia sieci, takiej jak przechwycone przez Fiddler, identyfikator żądania klienta jest widoczny w wiadomości żądania jako **x-ms-client żądania id** wartość nagłówka HTTP.
* W dzienniku rejestrowania magazynu po stronie serwera Identyfikatora żądania klienta jest wyświetlana w kolumnie identyfikator żądania klienta.

> [!NOTE]
> Istnieje możliwość dla wielu żądań udostępniać ten sam identyfikator żądania klienta, ponieważ klient może zostać przypisany tej wartości (mimo że biblioteki klienta usługi Storage automatycznie przypisuje nową wartość). Gdy klient ponawia próbę, wszystkie próby mają ten sam identyfikator żądania klienta W przypadku partii wysłanych z klienta partii ma identyfikator klienta pojedynczego żądania.
> 
> 

### <a name="server-request-id"></a>Identyfikator żądania serwera
Usługa magazynu jest automatycznie generuje identyfikatorów żądania serwera.

* W dzienniku rejestrowania magazynu po stronie serwera, zostanie wyświetlony identyfikator żądania serwera **nagłówka Identyfikatora żądania** kolumny.
* W śledzenia sieci, takiej jak przechwycone przez Fiddler, identyfikator żądania serwera pojawia się w wiadomości odpowiedzi jako **x-ms-request-id** wartość nagłówka HTTP.
* W dzienniku po stronie klienta tworzy bibliotekę klienta usługi Storage, identyfikator żądania serwera zostanie wyświetlony w **tekst operacji** kolumny dla wpisu dziennika zawierającego szczegóły odpowiedź serwera.

> [!NOTE]
> Usługa magazynu zawsze przypisuje serwera unikatowy identyfikator żądania każde żądanie, które otrzymuje, dzięki czemu każdy ponowienia próby z klienta i każda operacja wchodzące w skład partii mają unikatowe identyfikatora serwera żądania.
> 
> 

Jeśli biblioteki klienta usługi Storage zgłasza **StorageException** w kliencie **RequestInformation** właściwość zawiera **RequestResult** obiekt, który zawiera **ServiceRequestID** właściwości. Można także przejść do **RequestResult** obiekt z **elementu OperationContext** wystąpienia.

Poniższy przykład kodu pokazuje, jak ustawić niestandardowego **ClientRequestId** wartość dołączając **elementu OperationContext** obiekt żądania do usługi magazynu. Przedstawiono również sposób pobierania **ServerRequestId** wartości z komunikatu odpowiedzi.

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

### <a name="timestamps"></a>Znaczniki czasu
Znajdź pozycje dziennika powiązane, ale należy zwrócić szczególną uwagę na dowolnym niedokładność zegara między klientem a serwerem, który może istnieć umożliwia także sygnatur czasowych. Wyszukaj plus lub minus 15 minut do dopasowania wpisów po stronie serwera, oparte na sygnatury czasowej na komputerze klienckim. Należy pamiętać, że metadane obiektu blob dla obiektów blob zawierający metryki wskazuje zakres czasu dla metryki przechowywane w obiekcie blob. Ten zakres czasu jest przydatne, jeśli masz wiele metryki obiektów blob dla tego samego minutę lub godzinę.

## <a name="troubleshooting-guidance"></a>Wskazówki dotyczące rozwiązywania problemów
W tej sekcji ułatwiają diagnozowania i rozwiązywania problemów z niektóre typowe problemy dotyczące aplikacji mogą wystąpić podczas korzystania z usług magazynu Azure. Skorzystaj z poniższej listy, aby zlokalizować informacje dotyczące określonego problemu.

**Rozwiązywanie problemów z drzewa decyzyjnego**

---
Problem związek między wykonanie jednej z usług magazynu?

* [metryki pokazują AverageE2ELatency wysoki i niski AverageServerLatency]
* [Metryki wskazują niską wartość AverageE2ELatency i niską wartość AverageServerLatency, ale na kliencie występuje duże opóźnienie]
* [Metryki wskazują wysoką wartość AverageServerLatency]
* [Występują nieoczekiwane opóźnienia w dostarczaniu komunikatów w kolejce]

---
Problem związek do sprawdzania dostępności usług magazynu?

* [metryki spowodować wzrost PercentThrottlingError]
* [metryki spowodować wzrost PercentTimeoutError]
* [Metryki wskazują wzrost wartości PercentNetworkError]

---
 Aplikacja kliencka odbiera odpowiedź HTTP 4XX (na przykład 404) z usługą magazynu?

* [Klient odbiera komunikaty HTTP 403 (Dostęp zabroniony)]
* [Klient odbiera komunikaty HTTP 404 (nie znaleziono)]
* [Klient odbiera komunikaty HTTP 409 (konflikt)]

---
[metryki pokazują PercentSuccess niskim lub wpisy dziennika analytics ma operacji ze stanem transakcji ClientOtherErrors]

---
[Metryki pojemności Pokaż nieoczekiwane zwiększenie wykorzystania pojemności magazynu]

---
[Występują nieoczekiwane ponowne uruchomienie maszyn wirtualnych, które mają wiele wirtualnych dysków twardych dołączonych]

---
[Problem wynika z przy użyciu emulatora magazynu dla rozwoju lub testowania]

---
[Pojawiły się problemy z instalacją zestawu Azure SDK dla platformy .NET]

---
[Inny problem z usługą magazynu]

---
### <a name="metrics-show-high-AverageE2ELatency-and-low-AverageServerLatency"></a>Metryki pokazują AverageE2ELatency wysoki i niski AverageServerLatency
Ilustracja poniżej z [portalu Azure](https://portal.azure.com) narzędzi do monitorowania przedstawiono przykład gdzie **AverageE2ELatency** jest znacznie wyższa niż **AverageServerLatency**.

![][4]

Usługa magazynu tylko w przypadku obliczania metryki **AverageE2ELatency** pomyślnych żądań oraz, w odróżnieniu od **AverageServerLatency**, obejmuje czas, klient pobiera dane wysyłanie i odbieranie potwierdzenia z usługą Magazyn. W związku z tym różnica między **AverageE2ELatency** i **AverageServerLatency** może być z powodu aplikacji klienckiej powolnych odpowiadać lub z powodu warunków w sieci.

> [!NOTE]
> Można również wyświetlić **E2ELatency** i **ServerLatency** dane dziennika dla poszczególnych magazynu operacji w rejestrowania magazynu.
> 
> 

#### <a name="investigating-client-performance-issues"></a>Do badania problemów dotyczących wydajności klienta
Możliwe przyczyny klienta wolno odpowiadać obejmują o ograniczonej liczby dostępnych połączeń i wątków lub być niedostateczne zasoby, takie jak procesor CPU, pamięć i sieć przepustowości. Można rozwiązać ten problem, zmieniając kod klienta efektywność (na przykład za pomocą wywołania asynchroniczne z usługą Magazyn), lub za pomocą większe maszyny wirtualnej (większej liczby rdzeni i większa ilość pamięci).

Dla tabeli i kolejki usług algorytm Nagle'a może również spowodować wysokiej **AverageE2ELatency** w porównaniu z **AverageServerLatency**: Aby uzyskać więcej informacji, zobacz wpis [Nagle'a firmy Algorytm jest nie przyjazną kierunku małych żądań](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/06/25/nagle-s-algorithm-is-not-friendly-towards-small-requests.aspx). Algorytm Nagle'a w kodzie można wyłączyć za pomocą **ServicePointManager —** klasy w **System.Net** przestrzeni nazw. Ten krok należy wykonać przed wprowadzasz wezwań do tabeli lub usługi kolejki w aplikacji, ponieważ nie dotyczy to połączeń znajdujących się już otworzyć. Poniższy przykład pochodzi z **Application_Start** metody w roli procesu roboczego.

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);
ServicePoint tableServicePoint = ServicePointManager.FindServicePoint(storageAccount.TableEndpoint);
tableServicePoint.UseNagleAlgorithm = false;
ServicePoint queueServicePoint = ServicePointManager.FindServicePoint(storageAccount.QueueEndpoint);
queueServicePoint.UseNagleAlgorithm = false;
```

Sprawdź dzienniki po stronie klienta, aby zobaczyć, jak wiele żądań, aplikacja kliencka jest przesyłanie i sprawdź, czy ogólne .NET powiązane wąskich gardeł wydajności w sieci klienta, takie jak procesor CPU, pamięci .NET, wykorzystania sieci lub pamięci. Jako punkt początkowy dla Rozwiązywanie problemów z aplikacjami klienta .NET, zobacz [debugowanie, śledzenie i profilowanie](http://msdn.microsoft.com/library/7fe0dd2y).

#### <a name="investigating-network-latency-issues"></a>Badanie problemów z opóźnieniem w sieci
Zazwyczaj duże opóźnienie end-to-end spowodowane przez sieć jest z powodu przejściowego warunków. Zarówno problemy z siecią przejściowych i trwałe takich jak porzuconych pakietów można zbadać za pomocą narzędzi takich jak Wireshark lub programu Microsoft Message Analyzer.

Aby uzyskać więcej informacji o rozwiązywaniu problemów z siecią za pomocą programu Wireshark, zobacz "[dodatek 2: do przechwytywania ruchu sieciowego przy użyciu programu Wireshark]."

Aby uzyskać więcej informacji o rozwiązywaniu problemów z siecią za pomocą programu Microsoft Message Analyzer, zobacz "[dodatku 3: do przechwytywania ruchu sieciowego przy użyciu programu Microsoft Message Analyzer]."

### <a name="metrics-show-low-AverageE2ELatency-and-low-AverageServerLatency"></a>Metryki pokazują AverageE2ELatency niski i niski AverageServerLatency, ale klient występuje duże opóźnienie
W tym scenariuszu najbardziej prawdopodobną przyczyną jest opóźnienia podczas uzyskiwania dostępu do usługi magazynu żądań magazynu. Należy zbadać, dlaczego żądania klienta nie dokonywania go za pośrednictwem usługi blob.

Jedną z możliwych przyczyn klienta opóźnienie wysyłania żądań to Brak ograniczoną liczbę dostępnych połączeń i wątków.

Również sprawdzić, czy klient wykonuje wiele ponownych prób, a następnie sprawdź przyczynę, jeśli jest. Aby ustalić, czy klient wykonuje wiele ponownych prób, można:

* Sprawdź dzienniki analityka magazynu. Jeśli są wykonywane wiele ponownych prób, pojawi się wiele operacji mających taki sam identyfikator żądania klienta, lecz z innym serwerem żądaniem identyfikatorów.
* Sprawdź dzienniki klienta. Pełne rejestrowanie wskaże, że nastąpiło ponowna próba.
* Debugowanie kodu i sprawdź właściwości **elementu OperationContext** obiekt skojarzony z żądaniem. Jeśli operacja ponawiała, **RequestResults** właściwości będzie zawierać wiele żądanie serwera unikatowych identyfikatorów. Można również sprawdzić czas rozpoczęcia i zakończenia dla każdego żądania. Aby uzyskać więcej informacji, zobacz przykładowy kod w sekcji [identyfikator żądania serwera].

Jeśli na komputerze klienckim nie ma żadnych problemów, powinien być sprawdzony potencjalnych problemów sieci, takich jak utraty pakietów. Narzędzia, takie jak Wireshark lub programu Microsoft Message Analyzer służy do badania problemów z siecią.

Aby uzyskać więcej informacji o rozwiązywaniu problemów z siecią za pomocą programu Wireshark, zobacz "[dodatek 2: do przechwytywania ruchu sieciowego przy użyciu programu Wireshark]."

Aby uzyskać więcej informacji o rozwiązywaniu problemów z siecią za pomocą programu Microsoft Message Analyzer, zobacz "[dodatku 3: do przechwytywania ruchu sieciowego przy użyciu programu Microsoft Message Analyzer]."

### <a name="metrics-show-high-AverageServerLatency"></a>Metryki pokazują AverageServerLatency wysoka
W przypadku wysokiej **AverageServerLatency** żądań pobrania obiektu blob, należy używać dzienniki rejestrowania magazynu, czy są powtarzane żądania dla tego samego obiektu blob (lub zestaw obiektów blob). Dla żądań przekazania obiektu blob powinien być sprawdzony jakie bloku rozmiar klienta jest przy użyciu (na przykład bloki fragmentów mniej niż 64 KB rozmiaru może spowodować koszty odczytami znajdują się również w mniej niż 64 KB), a jeśli wielu klientów przekazywany bloków do tego samego obiektu blob w akapit llel. Należy także sprawdzić metryki na minutę maksymalnej liczby żądań, które powoduje przekroczenie na drugi wartości docelowe skalowalności: Zobacz też "[metryki spowodować wzrost PercentTimeoutError]."

Jeśli widzisz wysokiego **AverageServerLatency** do pobrania obiektu blob żądania, gdy są powtarzane żądań tego samego obiektu blob lub zestaw obiektów blob, a następnie należy rozważyć buforowanie tych obiektów blob przy użyciu usługi pamięć podręczna Azure lub sieci dostarczania zawartości (CDN) platformy Azure. Dla żądania przesłania może zwiększyć przepływność przy użyciu większy rozmiar bloku. Dla zapytań do tabel jest również możliwe do zaimplementowania buforowanie po stronie klienta na komputerach klienckich, które wykonują te same operacje kwerend i gdy dane nie zmieniają się często.

Wysoka **AverageServerLatency** wartości mogą też być objawem źle skonstruowane tabel lub kwerend, czy wynik operacji skanowania lub że zgodne ze wzorcem przed dołączania/dołączy. Aby uzyskać więcej informacji, zobacz "[metryki spowodować wzrost PercentThrottlingError]".

> [!NOTE]
> Można znaleźć tutaj kontrolną wydajności kompleksowe Lista kontrolna: [wydajność magazynu Microsoft Azure i listę kontrolną skalowalność](storage-performance-checklist.md).
> 
> 

### <a name="you-are-experiencing-unexpected-delays-in-message-delivery"></a>Występują nieoczekiwane opóźnienia dotyczące dostarczania wiadomości w kolejce
Jeśli występują opóźnienia między czasem aplikacji dodaje komunikat do kolejki i czas, który staje się ona dostępna do odczytu z kolejki, należy wykonać następujące kroki, aby zdiagnozować problem:

* Sprawdź, czy aplikacja jest pomyślnie dodanie wiadomości do kolejki. Sprawdź, czy aplikacja nie ponawia **AddMessage** metody kilka razy przed pomyślne. Dzienniki biblioteki klienta usługi Storage zostaną wyświetlone wszystkie kolejne próby operacji magazynu.
* Sprawdź, nie istnieje żaden zegar pochylenia między roli procesu roboczego, który dodaje komunikat do kolejki i roli procesu roboczego, która odczytuje komunikat z kolejki, która ułatwia pojawiają się tak, jakby to opóźnienie podczas przetwarzania.
* Sprawdź, czy rola procesu roboczego, która odczytuje wiadomości z kolejki jest możliwe. Jeśli klient kolejki wywołuje **GetMessage** , ale metoda kończy się niepowodzeniem z potwierdzeniem, komunikat pozostanie niewidoczny w kolejce do **invisibilityTimeout** okresu. W tym momencie wiadomości staje się dostępna do przetwarzania ponownie.
* Sprawdź, czy długość kolejki rośnie w czasie. Może to wystąpić, jeśli nie masz wystarczających pracowników dostępnych do przetwarzania wszystkich komunikatów, które powodują innych pracowników w kolejce. Sprawdź również metryki, aby zobaczyć, czy usunąć żądania kończą się niepowodzeniem i kolejki liczba komunikatów, które mogą wskazywać ponawianie prób nie powiodło się usunięcie komunikatu.
* Przeanalizuj dzienniki rejestrowania magazynu dla wszystkich operacji kolejki, które mają wyższe niż oczekiwano **E2ELatency** i **ServerLatency** wartości przez dłuższy okres czasu niż zwykle.

### <a name="metrics-show-an-increase-in-PercentThrottlingError"></a>Metryki spowodować wzrost PercentThrottlingError
Ograniczenia przepustowości błędy występują w przypadku przekroczenia wartości docelowe skalowalności usługi magazynu. Limity usługi magazynu, upewnij się, że nie jednego klienta lub dzierżawcy mogą używać usługi kosztem innych użytkowników. Aby uzyskać więcej informacji, zobacz [cele dotyczące wydajności i skalowalności magazynu Azure](storage-scalability-targets.md) szczegółowe informacje o wartości docelowe skalowalności w przypadku kont magazynu i cele wydajności dla partycji w ramach konta magazynu.

Jeśli **PercentThrottlingError** Metryka spowodować wzrost odsetek żądań, które kończą się niepowodzeniem z powodu błędu ograniczania przepustowości, należy zbadać jednego z dwóch scenariuszy:

* [Przejściowa wzrost PercentThrottlingError]
* [Stałe zwiększanie PercentThrottlingError błąd]

Wzrost **PercentThrottlingError** często występuje w tym samym czasie jako wzrost liczby żądań magazynu lub gdy początkowo załadować testowania aplikacji. To może również manifestu się na komputerze klienckim jako "503 serwera zajęty" lub "500 limit czasu operacji" HTTP komunikaty o stanie operacji magazynu.

#### <a name="transient-increase-in-PercentThrottlingError"></a>Przejściowa wzrost PercentThrottlingError
Jeśli widzisz nagłego wartości **PercentThrottlingError** który pokrywa się z okresy intensywnego działania dla aplikacji, wdrożenie wykładniczej (nie liniowej) wycofania strategii ponownych prób w kliencie. Wycofania ponownych prób zmniejszyć obciążenie natychmiastowego partycji i ułatwić aplikacji wygładzanie największego ruchu. Aby uzyskać więcej informacji dotyczących sposobu wdrażania zasad ponawiania za pomocą biblioteki klienta usługi Storage, zobacz [Namespace Microsoft.WindowsAzure.Storage.RetryPolicies](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.retrypolicies.aspx).

> [!NOTE]
> Może również zostać wyświetlony impulsy wartości **PercentThrottlingError** która nie pokrywa się z okresy intensywnego działania aplikacji: najbardziej prawdopodobną przyczyną jest usługą magazynu, przenoszenie partycji w celu zwiększenia równoważenia obciążenia.
> 
> 

#### <a name="permanent-increase-in-PercentThrottlingError"></a>Stałe zwiększanie PercentThrottlingError błąd
Jeśli widzisz wysoką wartość **PercentThrottlingError** stałe wzrostu w woluminach transakcji lub wykonywania początkowej obciążenia testów dla aplikacji, a następnie należy ocenić, jak aplikacja używa partycji magazynu i czy jej zbliża się do wartości docelowe skalowalności dla konta magazynu. Na przykład jeśli widzisz ograniczania błędy w kolejce (który traktowana jako jednej partycji), następnie należy rozważyć przy użyciu dodatkowe kolejki, aby rozłożyć transakcji na wiele partycji. Jeśli widzisz ograniczania błędów w tabeli, należy wziąć pod uwagę, aby rozłożyć transakcji na wiele partycji przy użyciu większej liczby wartości klucza partycji przy użyciu różnych schemat partycjonowania. Jeden typową przyczyną tego problemu jest prepend/dołączanie wzorzec przed gdzie wybierz datę jako klucza partycji, a następnie wszystkie dane w określonym dniu są zapisywane do jednej partycji: pod obciążeniem, może to spowodować wąskie gardło zapisu. Rozważ użycie innego projektu partycjonowania albo oceny, czy przy użyciu magazynu obiektów blob może okazać się lepszym rozwiązaniem. Również sprawdzić, czy ograniczania występuje w wyniku gwałtowny wzrost ruchu i zbadaj sposobów wygładzanie deseniu żądań.

Transakcje rozpowszechniają wiele partycji, nadal należy pamiętać o limity skalowalności, ustaw dla konta magazynu. Na przykład jeśli użyto kolejek dziesięć przetwarzania maksymalnie 2000 wiadomości o rozmiarze 1KB na sekundę, można w ogólnym limicie 20 000 komunikatów na sekundę dla konta magazynu. Jeśli potrzebujesz przetwarzać więcej niż 20 000 jednostek na sekundę, należy rozważyć użycie wielu kont magazynu. Możesz również powinien zawierać pamiętać, że rozmiar żądania i jednostek ma wpływ na kiedy usługa magazynu ogranicza klientom: Jeśli masz większą żądań i jednostek może należy wcześniej ograniczany.

Projekt kwerendy nieefektywne może również spowodować osiągnął limity skalowalności do partycji tabeli. Na przykład zapytania filtru, który wybiera tylko jeden procent jednostek w partycji, ale która skanuje wszystkich jednostek w partycji należy uzyskać dostępu do każdej jednostki. Każdy podmiot odczytu są zliczane całkowita liczba transakcji w tej partycji; w związku z tym można łatwo osiągnąć wartości docelowe skalowalności.

> [!NOTE]
> Testowanie wydajności powinno ujawnić żadnych projektów nieefektywne kwerendy w aplikacji.
> 
> 

### <a name="metrics-show-an-increase-in-PercentTimeoutError"></a>Metryki spowodować wzrost PercentTimeoutError
Twoje metryki pokazują wzrost **PercentTimeoutError** dla jednej z usług magazynu. W tym samym czasie klient odbierze dużej liczby komunikatów o stanie "500 limit czasu operacji" HTTP z operacji magazynu.

> [!NOTE]
> Może pojawić się błędy przekroczenia limitu czasu tymczasowo jako usługa magazynu żądania zrównoważenia obciążenia za pomocą przenoszenia partycji do nowego serwera.
> 
> 

**PercentTimeoutError** metryka jest agregacją następujące metryki: **ClientTimeoutError**, **AnonymousClientTimeoutError**, **SASClientTimeoutError**, **ServerTimeoutError**, **AnonymousServerTimeoutError**, i **SASServerTimeoutError**.

Limity czasu serwera są spowodowane błędem na serwerze. Przekroczenia limitu czasu klienta się tak zdarzyć, ponieważ operacja na serwerze przekroczyło limit czasu określony przez klienta; na przykład klienta przy użyciu biblioteki klienta usługi Storage można ustawić czasu dla operacji przy użyciu **ServerTimeout** właściwość **QueueRequestOptions** klasy.

Limity czasu serwera wskazują na problem z usługą Magazyn, który wymaga dalszego postępowania. Aby sprawdzić, czy osiągnięto limity skalowalności usługi oraz zidentyfikować wszelkie największego ruchu, które mogą być przyczyną tego problemu, można użyć metryki. Jeśli ten problem występuje sporadycznie, może to być spowodowane równoważenia obciążenia działania usługi. Jeśli ten problem jest trwałe i nie jest spowodowany przez naciśnięcie limity skalowalności usługi aplikacji, należy Zgłoś problem pomocy technicznej. Limity czasu klienta należy zdecydować, jeśli limit czasu ma ustawioną wartość odpowiednią wartość w kliencie i zmień wartość limitu czasu na komputerze klienckim, lub zbadać, jak użytkownik może poprawić wydajność operacji w usłudze magazynowania na przykład optymalizacji zapytań tabeli lub zmniejszenie rozmiaru wiadomości.

### <a name="metrics-show-an-increase-in-PercentNetworkError"></a>Metryki spowodować wzrost PercentNetworkError
Twoje metryki pokazują wzrost **PercentNetworkError** dla jednej z usług magazynu. **PercentNetworkError** metryka jest agregacją następujące metryki: **NetworkError**, **AnonymousNetworkError**, i **SASNetworkError**. Te wystąpić, gdy Usługa magazynu wykrywa błąd sieci, gdy klient wysyła żądanie magazynu.

Najczęstszą przyczyną tego błędu jest klientem rozłączanie przed upływem limitu czasu w usłudze magazynowania. Sprawdź kod na kliencie, aby zrozumieć, kiedy i dlaczego klient odłączy się od usługi magazynu. Umożliwia także Wireshark, Microsoft Message Analyzer ani Tcping do badania problemów z połączeniem sieciowym z klienta. Te narzędzia są opisane w [dodatki].

### <a name="the-client-is-receiving-403-messages"></a>Klient odbiera komunikaty HTTP 403 (Dostęp zabroniony)
Jeśli aplikacja kliencka jest zgłaszanie błędów HTTP 403 (Dostęp zabroniony), prawdopodobną przyczyną jest to klient używa wygasłe dostępu sygnatury dostępu Współdzielonego podczas wysyłania żądania magazynu (chociaż innymi możliwymi przyczynami mogą zegara pochylenia nieprawidłowe klucze i nagłówków puste). Przyczyną jest wygasłe klucza sygnatury dostępu Współdzielonego, nie będą widzieć wszystkie wpisy w danych dziennika rejestrowania magazynu po stronie serwera. W poniższej tabeli przedstawiono przykładowe z dziennika po stronie klienta, generowane przez biblioteki klienta magazynu, która ilustruje ten problem występuje:

| Element źródłowy | Poziom szczegółowości | Poziom szczegółowości | Identyfikator żądania klienta | Operacja tekstu |
| --- | --- | --- | --- | --- |
| Microsoft.WindowsAzure.Storage |Informacje |3 |85d077ab-… |Uruchamianie operacji z lokalizacji podstawowej na tryb lokalizacji PrimaryOnly. |
| Microsoft.WindowsAzure.Storage |Informacje |3 |85d077ab -… |Uruchamianie żądań synchronicznych do https://domemaildist.blob.core.windows.netazureimblobcontainer/blobCreatedViaSAS.txt?sv=2014-02-14 &amp;sr = c&amp;si = mypolicy&amp;sig = 2BmcR6zbudIH2F5Ikm % 2FyhNYZEmJNQ OFnd4Rd7z01fIvh % 3D&amp;api-version = 2014-02-14. |
| Microsoft.WindowsAzure.Storage |Informacje |3 |85d077ab -… |Oczekiwanie na odpowiedź. |
| Microsoft.WindowsAzure.Storage |Ostrzeżenie |2 |85d077ab -… |Zgłoszono wyjątek podczas oczekiwania na odpowiedź: serwer zdalny zwrócił błąd: (403) zabroniony. |
| Microsoft.WindowsAzure.Storage |Informacje |3 |85d077ab -… |Odebrano odpowiedź. Kod stanu = 403, identyfikator żądania = 9d67c64a-64ed-4b0d-9515-3b14bbcdc63d, Content-MD5 = ETag =. |
| Microsoft.WindowsAzure.Storage |Ostrzeżenie |2 |85d077ab -… |Zgłoszono wyjątek podczas operacji: serwer zdalny zwrócił błąd: (403) zabroniony. |
| Microsoft.WindowsAzure.Storage |Informacje |3 |85d077ab -… |Sprawdzanie, czy należy wykonać ponownie operację. Liczba ponownych prób = 0, kod stanu HTTP 403, wyjątek = = Serwer zdalny zwrócił błąd: (403) zabroniony. |
| Microsoft.WindowsAzure.Storage |Informacje |3 |85d077ab -… |Następnej lokalizacji została ustawiona jako podstawowy, na podstawie trybu lokalizacji. |
| Microsoft.WindowsAzure.Storage |Błąd |1 |85d077ab -… |Zasady ponawiania nie zezwala na ponowienie próby. Niepowodzenie z serwera zdalnego zwróciło błąd: (403) zabroniony. |

W tym scenariuszu należy zbadać, dlaczego wygasa tokenu sygnatury dostępu Współdzielonego, zanim klient wysyła ten token do serwera:

* Zwykle nie należy ustawić czas rozpoczęcia, podczas tworzenia sygnatury dostępu Współdzielonego dla klienta do używania natychmiast. Jeśli istnieją różnice małych zegara między hostem Generowanie sygnatury dostępu Współdzielonego przy użyciu bieżącego czasu i usługi magazynu, a następnie można usługi magazynu do odbierania sygnatury dostępu Współdzielonego, który nie jest jeszcze ważny.
* Nie ustawiaj czas wygaśnięcia bardzo krótki na sygnatury dostępu Współdzielonego. Ponownie zegara małe różnice między hosta Generowanie sygnatury dostępu Współdzielonego i usługi magazynu może prowadzić do sygnatury dostępu Współdzielonego najwyraźniej wygasa wcześniej niż zakładano.
* Parametr wersji w klucza sygnatury dostępu Współdzielonego jest (na przykład **sv = 2015-04-05**) zgodna z wersją biblioteki klienta usługi Storage używasz? Firma Microsoft zaleca zawsze używać najnowszej wersji [biblioteki klienta usługi Storage](https://www.nuget.org/packages/WindowsAzure.Storage/).
* Jeśli ponownego generowania kluczy dostępu do magazynu, wszystkie istniejące tokeny sygnatury dostępu Współdzielonego może unieważniona. Ten problem może wystąpić, jeśli Generowanie tokeny sygnatury dostępu Współdzielonego i czas wygaśnięcia długi dla aplikacji klienckich, do pamięci podręcznej.

Jeśli korzystasz z biblioteki klienta magazynu do generowania tokenów SAS, następnie może łatwo dojść do utworzenia prawidłowego tokenu. Jednak jeśli korzysta z interfejsu API REST magazynu i ręcznie konstruowania tokeny sygnatury dostępu Współdzielonego, zobacz [Delegowanie dostępu z sygnaturą dostępu współdzielonego](http://msdn.microsoft.com/library/azure/ee395415.aspx).

### <a name="the-client-is-receiving-404-messages"></a>Klient odbiera komunikaty HTTP 404 (nie znaleziono)
Jeśli aplikacja kliencka odbiera komunikat HTTP 404 (nie znaleziono) z serwera, oznacza to, że obiekt, który klient próbował użyć (np. jednostek, tabeli, obiektów blob, kontenera lub kolejki) nie istnieje w usłudze magazynowania. Istnieje wiele możliwych przyczyn tej, takich jak:

* [Klient lub inny proces wcześniej usunięty obiekt]
* [Problem autoryzacji dostępu sygnatury dostępu Współdzielonego]
* [Kod JavaScript po stronie klienta nie ma uprawnień dostępu do tego obiektu]
* [Błąd sieci]

#### <a name="client-previously-deleted-the-object"></a>Klient lub inny proces wcześniej usunięty obiekt
W scenariuszach, w którym klient próbuje odczytywać, aktualizować lub usuwać dane w usłudze magazynowania jest zwykle łatwo zidentyfikować w dziennikach po stronie serwera poprzedniej operacji po usunięciu danego obiektu z usługi magazynowania. Często dane dziennika pokazuje, czy inny użytkownik lub proces usunął obiektu. W dzienniku rejestrowania magazynu po stronie serwera typ operacji i żądanego obiektu-kolumn klucza pokazują, gdy klient usunięty obiekt.

W scenariuszu, w którym klient próbuje wstawienia obiektu może nie być od razu widoczne Dlaczego powoduje to odpowiedzi HTTP 404 (nie znaleziono) biorąc pod uwagę, że klient tworzy nowy obiekt. Jednak klient jest utworzenie obiektu blob musi być w stanie odnaleźć kontenera obiektów blob, jeśli klient jest tworzenia komunikatu musi być w stanie znaleźć kolejki, a klient jest dodawanie wiersza musi być w stanie odnaleźć tabeli.

Dziennika klienta z biblioteki klienta usługi Storage umożliwia bardziej szczegółowe zrozumieć, gdy klient wysyła określone żądania do usługi magazynu.

Następujące dziennika po stronie klienta, generowane przez biblioteki klienta magazynu zilustrowano problem, gdy klient nie może znaleźć kontenera obiektu blob, który tworzenia. Ten dziennik zawiera szczegółowe informacje o następujących czynności:

| Identyfikator żądania | Operacja |
| --- | --- |
| 07b26a5d-... |**DeleteIfExists** metody można usunąć kontenera obiektów blob. Należy pamiętać, że ta operacja obejmuje **HEAD** żądania do sprawdzenia istnienia kontenera. |
| e2d06d78… |**CreateIfNotExists** metodę w celu utworzenia kontenera obiektów blob. Należy pamiętać, że ta operacja obejmuje **HEAD** żądania, który umożliwia sprawdzenie istnienia kontenera. **HEAD** zwraca komunikat 404, ale nadal. |
| de8b1c3c-... |**UploadFromStream** metodę w celu utworzenia obiektu blob. **PUT** żądanie kończy się niepowodzeniem z komunikatem 404 |

Wpisy dziennika:

| Identyfikator żądania | Operacja tekstu |
| --- | --- |
| 07b26a5d-... |Uruchamianie żądań synchronicznych do https://domemaildist.blob.core.windows.net/azuremmblobcontainer. |
| 07b26a5d-... |StringToSign = HEAD............x-ms-client-request-id:07b26a5d-....x-ms-date:Tue, 03 Jun 2014 10:33:11 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container. |
| 07b26a5d-... |Oczekiwanie na odpowiedź. |
| 07b26a5d-... |Odebrano odpowiedź. Kod stanu 200, identyfikator żądania = = eeead849... Content-MD5 = ETag = &quot;0x8D14D2DC63D059B&quot;. |
| 07b26a5d-... |Nagłówki odpowiedzi zostały pomyślnie przetworzone, pochodzących z resztą operacji. |
| 07b26a5d-... |Pobiera treść odpowiedzi. |
| 07b26a5d-... |Operacja ukończona pomyślnie. |
| 07b26a5d-... |Uruchamianie żądań synchronicznych do https://domemaildist.blob.core.windows.net/azuremmblobcontainer. |
| 07b26a5d-... |StringToSign = DELETE............x-ms-client-request-id:07b26a5d-....x-ms-date:Tue, 03 Jun 2014 10:33:12    GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container. |
| 07b26a5d-... |Oczekiwanie na odpowiedź. |
| 07b26a5d-... |Odebrano odpowiedź. Kod stanu = 202, identyfikator żądania = 6ab2a4cf..., Content-MD5 = ETag =. |
| 07b26a5d-... |Nagłówki odpowiedzi zostały pomyślnie przetworzone, pochodzących z resztą operacji. |
| 07b26a5d-... |Pobiera treść odpowiedzi. |
| 07b26a5d-... |Operacja ukończona pomyślnie. |
| e2d06d78-... |Uruchamianie żądania asynchronicznego na https://domemaildist.blob.core.windows.net/azuremmblobcontainer.</td> |
| e2d06d78-... |StringToSign = HEAD............x-ms-client-request-id:e2d06d78-....x-ms-date:Tue, 03 Jun 2014 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container. |
| e2d06d78-... |Oczekiwanie na odpowiedź. |
| de8b1c3c-... |Uruchamianie żądań synchronicznych do https://domemaildist.blob.core.windows.net/azuremmblobcontainer/blobCreated.txt. |
| de8b1c3c-... |StringToSign = PUT...64.qCmF+TQLPhq/YYK50mP9ZQ==........x-ms-blob-type:BlockBlob.x-ms-client-request-id:de8b1c3c-....x-ms-date:Tue, 03 Jun 2014 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer/blobCreated.txt. |
| de8b1c3c-... |Przygotowywanie do zapisywania danych żądania. |
| e2d06d78-... |Zgłoszono wyjątek podczas oczekiwania na odpowiedź: serwer zdalny zwrócił błąd: (404) nie znaleziono... |
| e2d06d78-... |Odebrano odpowiedź. Kod stanu 404, identyfikator żądania = = 353ae3bc..., Content-MD5 = ETag =. |
| e2d06d78-... |Nagłówki odpowiedzi zostały pomyślnie przetworzone, pochodzących z resztą operacji. |
| e2d06d78-... |Pobiera treść odpowiedzi. |
| e2d06d78-... |Operacja ukończona pomyślnie. |
| e2d06d78-... |Uruchamianie żądania asynchronicznego na https://domemaildist.blob.core.windows.net/azuremmblobcontainer. |
| e2d06d78-... |StringToSign = PUT...0.........x-ms-client-request-id:e2d06d78-....x-ms-date:Tue, 03 Jun 2014 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container. |
| e2d06d78-... |Oczekiwanie na odpowiedź. |
| de8b1c3c-... |Zapisywanie danych żądania. |
| de8b1c3c-... |Oczekiwanie na odpowiedź. |
| e2d06d78-... |Zgłoszono wyjątek podczas oczekiwania na odpowiedź: serwer zdalny zwrócił błąd: konflikt (409). |
| e2d06d78-... |Odebrano odpowiedź. Kod stanu = 409, identyfikator żądania = c27da20e..., Content-MD5 = ETag =. |
| e2d06d78-... |Pobiera treść odpowiedzi błędu. |
| de8b1c3c-... |Zgłoszono wyjątek podczas oczekiwania na odpowiedź: serwer zdalny zwrócił błąd: (404) nie znaleziono... |
| de8b1c3c-... |Odebrano odpowiedź. Kod stanu 404, identyfikator żądania = = 0eaeab3e..., Content-MD5 = ETag =. |
| de8b1c3c-... |Zgłoszono wyjątek podczas operacji: serwer zdalny zwrócił błąd: (404) nie znaleziono... |
| de8b1c3c-... |Zasady ponawiania nie zezwala na ponowienie próby. Niepowodzenie z serwera zdalnego zwróciło błąd: (404) nie znaleziono... |
| e2d06d78-... |Zasady ponawiania nie zezwala na ponowienie próby. Niepowodzenie z serwera zdalnego zwróciło błąd: konflikt (409). |

W tym przykładzie dziennik pokazuje, czy klient jest naprzemiennego wykonywania żądania od **CreateIfNotExists** — metoda (żądania identyfikator e2d06d78...) z żądaniami z **UploadFromStream** — metoda (de8b1c3c-...). To z przeplotem wynika z faktu, aplikacja kliencka asynchronicznego wywołania tych metod. Zmodyfikuj asynchroniczne kodu na komputerze klienckim, aby upewnić się, tworzy kontener, przed podjęciem próby przekazania danych do obiektu blob w tym kontenerze. Najlepiej, jeśli należy wcześniej utworzyć wszystkich kontenerów.

#### <a name="SAS-authorization-issue"></a>Problem autoryzacji dostępu sygnatury dostępu Współdzielonego
Jeśli aplikacja kliencka próbuje użyć klucza sygnatury dostępu Współdzielonego, który nie ma wystarczających uprawnień dla operacji, usługa Magazyn zwraca komunikat HTTP 404 (nie znaleziono) do klienta. W tym samym czasie, pojawi się także wartość niezerową **SASAuthorizationError** w metryki.

W poniższej tabeli przedstawiono przykładowy komunikat dziennika po stronie serwera z pliku dziennika rejestrowania magazynu:

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


Sprawdź, dlaczego aplikacja kliencka próbuje wykonać operacji, dla którego go nie udzielono uprawnienia.

#### <a name="JavaScript-code-does-not-have-permission"></a>Kod JavaScript po stronie klienta nie ma uprawnień dostępu do tego obiektu
Jeśli używasz klienta języka JavaScript i Usługa magazynu jest zwracania wiadomości HTTP 404, wyszukaj następujące błędy JavaScript w przeglądarce:

```
SEC7120: Origin http://localhost:56309 not found in Access-Control-Allow-Origin header.
SCRIPT7002: XMLHttpRequest: Network Error 0x80070005, Access is denied.
```

> [!NOTE]
> Narzędzia deweloperskie F12 w programie Internet Explorer umożliwia śledzenie wiadomości wymieniane między przeglądarką a usługą Magazyn przy rozwiązywaniu problemów JavaScript po stronie klienta.
> 
> 

Te błędy, ponieważ implementuje przeglądarki sieci web [te same zasady pochodzenia](http://www.w3.org/Security/wiki/Same_Origin_Policy) ograniczenia zabezpieczeń, który uniemożliwia wywołanie interfejsu API w innej domenie z domeny strony strony sieci web jest dostarczany z.

Aby obejść ten problem JavaScript, można skonfigurować dla wielu CORS Origin Resource Sharing () usługi magazynu, który klient próbuje uzyskać dostęp. Aby uzyskać więcej informacji, zobacz [udostępniania zasobów między źródłami (CORS) obsługę usług magazynu Azure](http://msdn.microsoft.com/library/azure/dn535601.aspx).

Poniższy przykładowy kod przedstawia sposób konfigurowania usługi obiektów blob umożliwia JavaScript systemem w domenie Contoso dostęp do obiektów blob w usłudze magazyn obiektów blob:

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
W niektórych sytuacjach pakietów sieciowych utracone może prowadzić do zwracania wiadomości HTTP 404 do klienta usługi magazynu. Na przykład, gdy aplikacja kliencka jest usunięcie jednostki z usługi tabel zostanie wyświetlony klienta throw zgłoszenie wyjątku magazynu "HTTP 404 (nie znaleziono)" komunikat o stanie z usługi tabel. Badanie tabeli w usłudze magazyn tabel, zobacz, czy usługa została usunięta jednostki, zgodnie z wymaganiami.

Szczegóły wyjątku w kliencie zawierają identyfikator żądania (7e84f12d...) przypisany przez usługę tabeli dla żądania: można użyć tych informacji można znaleźć szczegółów żądania w dziennikach magazynu po stronie serwera przez wyszukiwanie w **nagłówka identyfikatora żądania**  kolumny w pliku dziennika. Metryki można użyć również do identyfikowania, gdy występują błędów, takich jak ta, a następnie wyszukać pliki dziennika na podstawie czasu metryki rejestrowane tego błędu. Ten wpis dziennika pokazuje, czy usunięcie nie powiodło się z komunikatem o stanie "Client inny błąd HTTP (404)". Tym samym wpis dziennika zawiera także identyfikator żądania wygenerowanych przez klienta w **client-request-id** kolumny (813ea74f...).

Dziennik po stronie serwera zawiera także inny wpis o tej samej **client-request-id** usunięcia wartości (813ea74f...) dla pomyślnego działania dla tej samej jednostki i z tego samego klienta. Operacja usuwania powiodło się wkrótce miało miejsce, aby usunąć nieudane żądania.

Najbardziej prawdopodobną przyczyną tego scenariusza jest, że klienta wysłała żądanie usunięcia dla obiektu do usługi tabel, które zakończyło się pomyślnie, ale nie otrzymano potwierdzenia z serwera (prawdopodobnie z powodu przejściowego problemu z siecią). Klient następnie automatycznie ponowione operacji (korzystającej z tego samego **client-request-id**), a ta ponowna próba nie powiodła się, ponieważ obiekt już zostało usunięte.

Jeśli ten problem występuje często, należy zbadać, dlaczego klient nie działa prawidłowo na odebranie potwierdzeń z usługi tabel. Jeśli problem jest tymczasowy, należy wyłapać błąd "Nie można odnaleźć HTTP (404)" i zaloguj się na komputerze klienckim, ale zezwalaj na kliencie kontynuować.

### <a name="the-client-is-receiving-409-messages"></a>Klient odbiera komunikaty HTTP 409 (konflikt)
W poniższej tabeli przedstawiono wyodrębniania z dziennika po stronie serwera dla dwóch operacji klienta: **DeleteIfExists** a następnie natychmiast przez **CreateIfNotExists** przy użyciu tej samej nazwy kontenera obiektów blob. Każdej operacji klienta powoduje dwa żądania wysyłane na serwer, w pierwszej kolejności **GetContainerProperties** żądania, aby sprawdzić, czy kontener istnieje, a następnie **DeleteContainer** lub  **Tworzony kontener** żądania.

| Sygnatura czasowa | Operacja | Wynik | Nazwa kontenera | Identyfikator żądania klienta |
| --- | --- | --- | --- | --- |
| 05:10:13.7167225 |GetContainerProperties |200 |mmcont |c9f52c89-… |
| 05:10:13.8167325 |DeleteContainer |202 |mmcont |c9f52c89-… |
| 05:10:13.8987407 |GetContainerProperties |404 |mmcont |bc881924-… |
| 05:10:14.2147723 |CreateContainer |409 |mmcont |bc881924-… |

Kod w aplikacji klienckiej usuwa i natychmiast odtwarza kontenera obiektów blob przy użyciu tej samej nazwie: **CreateIfNotExists** — metoda (żądań klienta identyfikator bc881924-...) ostatecznie zakończy się niepowodzeniem z powodu błędu HTTP 409 (konflikt). Gdy klient usuwa kontenerów obiektów blob, tabel lub kolejek istnieje krótki okres przed nazwą znowu dostępne.

Aplikacja kliencka należy używać nazwy kontenera unikatowy zawsze, gdy tworzy nowe kontenery w przypadku wzorca Usuń/ponownie utwórz wspólnej.

### <a name="metrics-show-low-percent-success"></a>Metryki pokazują PercentSuccess niskim lub wpisy dziennika analytics ma operacji ze stanem transakcji ClientOtherErrors
**PercentSuccess** metryka umożliwia przechwytywanie wartości procentowej operacje, które zostały pomyślnie w oparciu o ich kod stanu HTTP. Operacje z kodów stanu 2XX liczba pomyślnym przeprowadzeniu, podczas gdy operacje z kodów stanu w zakresach 3XX, 4XX i 5XX są liczone jako nie powiodło się i dolnym **PercentSucess** wartość metryki. W plikach dziennika magazynu po stronie serwera, operacje te są rejestrowane ze stanem transakcji **ClientOtherErrors**.

Należy zauważyć, że te operacje zostały pomyślnie ukończone i w związku z tym nie ma wpływu na inne metryki, np. dostępność. Niektóre operacje, które wykonana pomyślnie, ale który może skutkować niepowodzeniem kody stanu HTTP należą:

* **ResourceNotFound** (nie znaleziono 404), na przykład z żądania GET do obiektu blob, który nie istnieje.
* **ResouceAlreadyExists** (409 Konflikt), na przykład z **CreateIfNotExist** operacji, gdy zasób już istnieje.
* **ConditionNotMet** (nie modyfikować 304), na przykład z warunkowego operacji, takich jak kiedy klient wysyła **ETag** wartość i HTTP **If-None-Match** nagłówka żądania obraz tylko wtedy, gdy ma ona od ostatniej operacji zostały zaktualizowane.

Znajduje się lista często występujące kody błędów interfejsu API REST usług magazynu zwracające na stronie [często występujące kody błędów interfejsu API REST](http://msdn.microsoft.com/library/azure/dd179357.aspx).

### <a name="capacity-metrics-show-an-unexpected-increase"></a>Metryki pojemności Pokaż nieoczekiwane zwiększenie wykorzystania pojemności magazynu
Jeśli widzisz nagłym, nieoczekiwane wykorzystania pojemności na koncie magazynu można zapoznać się z przyczyn sprawdzając Twoje metryki dostępności; na przykład wzrost liczby żądań może prowadzić do zwiększenia ilości magazynu obiektów blob, używanego jako operacje oczyszczania specyficzne dla aplikacji, oczekiwane może mieć można zwalnianie miejsca może nie działać zgodnie z oczekiwaniami (na przykład usunąć nie powiodło się ponieważ wygasły tokeny sygnatury dostępu Współdzielonego, używany do Zwiększ ilość miejsca).

### <a name="your-issue-arises-from-using-the-storage-emulator"></a>Problem wynika z przy użyciu emulatora magazynu dla rozwoju lub testowania
Zwykle użyć emulatora magazynu podczas tworzenia i testowania w celu uniknięcia wymaganie dla konta magazynu platformy Azure. Typowe problemy, które mogą wystąpić, gdy używasz emulatora magazynu są:

* [Funkcja "X" nie działa w emulatorze magazynu]
* [Błąd "wartość dla jednego z nagłówków HTTP nie jest w nieprawidłowym formacie" przy użyciu emulatora magazynu]
* [Uruchomiony emulator magazynu wymaga uprawnień administracyjnych]

#### <a name="feature-X-is-not-working"></a>Funkcja "X" nie działa w emulatorze magazynu
Emulator magazynu nie obsługuje wszystkich funkcji usług magazynu Azure, takich jak usługa plików. Więcej informacji można znaleźć w temacie [Use the Azure Storage Emulator for Development and Testing](storage-use-emulator.md) (Używanie emulatora usługi Azure Storage do programowania i testowania).

Dla tych funkcji, które nie obsługuje emulatora magazynu należy korzystać z usługi Azure storage w chmurze.

#### <a name="error-HTTP-header-not-correct-format"></a>Błąd "wartość dla jednego z nagłówków HTTP nie jest w nieprawidłowym formacie" przy użyciu emulatora magazynu
Podczas testowania aplikacji, który korzysta z biblioteki klienta usługi Storage przed Magazyn lokalny wywołań emulatora i metody takie jak **CreateIfNotExists** zakończyć się niepowodzeniem z powodu błędu "wartość dla jednego z nagłówków HTTP nie jest prawidłowo Format". To ustawienie określa, czy wersja emulatora magazynu, którego używasz nie obsługuje wersji biblioteki klienta magazynu, którego używasz. Biblioteka klienta magazynu dodaje nagłówek **x-ms-version** do wszystkich żądań, które są możliwe. Jeśli emulator magazynu nie rozpoznaje wartość **x-ms-version** nagłówka, odrzuca żądanie.

Dzienniki klienta biblioteki magazynu umożliwia zobaczyć wartość **nagłówka x-ms-version** wysyła go. Możesz również sprawdzić wartość **nagłówka x-ms-version** Jeśli używasz narzędzia Fiddler do śledzenia żądań od aplikacji klienta.

W tym scenariuszu zwykle występuje, gdy zainstalować i używać najnowszej wersji biblioteki klienta magazynu bez aktualizowania emulatora magazynu. Należy zainstalować najnowszą wersję emulatora magazynu lub Użyj magazynu w chmurze zamiast emulator do projektowania i testowania.

#### <a name="storage-emulator-requires-administrative-privileges"></a>Uruchomiony emulator magazynu wymaga uprawnień administracyjnych
Zostanie wyświetlony monit o poświadczenia administratora po uruchomieniu emulatora magazynu. Dzieje się tak tylko, gdy emulator magazynu są inicjowanie po raz pierwszy. Po już zainicjować emulator magazynu, nie trzeba uprawnienia administracyjne, aby uruchomić go ponownie.

Więcej informacji można znaleźć w temacie [Use the Azure Storage Emulator for Development and Testing](storage-use-emulator.md) (Używanie emulatora usługi Azure Storage do programowania i testowania). Można również zainicjować emulatora magazynu w programie Visual Studio, który będzie również wymagają uprawnień administratora.

### <a name="you-are-encountering-problems-installing-the-Windows-Azure-SDK"></a>Pojawiły się problemy z instalacją zestawu Azure SDK dla platformy .NET
Podczas instalowania zestawu SDK, nie jest on spróbujesz zainstalować emulator magazynu na komputerze lokalnym. Dziennik instalacji zawiera jeden z następujących komunikatów:

* CAQuietExec: Błąd: nie można uzyskać dostępu do wystąpienia serwera SQL
* CAQuietExec: Błąd: nie można utworzyć bazy danych

Przyczyną jest problem z istniejącej instalacji LocalDB. Domyślnie emulator magazynu używa bazy danych LocalDB do utrwalenia danych podczas jego symuluje usług magazynu platformy Azure. Możesz resetować wystąpieniem LocalDB, uruchamiając następujące polecenia w oknie wiersza polecenia, zanim spróbujesz zainstalować zestawu SDK.

```
sqllocaldb stop v11.0
sqllocaldb delete v11.0
delete %USERPROFILE%\WAStorageEmulatorDb3*.*
sqllocaldb create v11.0
```

**Usunąć** polecenie usuwa stare pliki bazy danych z poprzedniej instalacji emulatora magazynu.

### <a name="you-have-a-different-issue-with-a-storage-service"></a>Inny problem z usługą magazynu
Jeśli poprzednie sekcje dotyczące rozwiązywania problemów nie dołączaj są problemy z usługą Magazyn, należy przyjąć następujące podejście do diagnozowania i rozwiązywania problemu.

* Sprawdź Twoje metryki, aby sprawdzić, czy zmiany z Twojej oczekiwane zachowanie linii bazowej. Z metryki można określić, czy problem jest przejściowy lub stałe i jakie operacje magazynu problem ma wpływ na.
* Można użyć informacji metryki ułatwiające wyszukiwanie danych dziennika bardziej szczegółowe informacje o błędach występujących po stronie serwera. Te informacje mogą pomóc w Rozwiązywanie problemów i rozwiązania problemu.
* Jeśli informacje w dziennikach po stronie serwera nie wystarcza do rozwiązania problemu pomyślnie, można użyć dzienników po stronie klienta biblioteki klienta usługi Storage do sprawdzania, czy zachowanie aplikacji klienckiej i narzędzi, takich jak Fiddler, programu Wireshark i programu Microsoft Message Analyzer do badania sieci.

Aby uzyskać więcej informacji o używaniu narzędzia Fiddler, zobacz "[dodatek 1: przy użyciu programu Fiddler do przechwytywania ruchu HTTP i HTTPS]."

Aby uzyskać więcej informacji o używaniu programu Wireshark, zobacz "[dodatek 2: do przechwytywania ruchu sieciowego przy użyciu programu Wireshark]."

Aby uzyskać więcej informacji o korzystaniu z programu Microsoft Message Analyzer, zobacz "[dodatku 3: do przechwytywania ruchu sieciowego przy użyciu programu Microsoft Message Analyzer]."

## <a name="appendices"></a>Dodatki
Dodatki opisano kilka narzędzi, które mogą być przydatne podczas diagnozowania i rozwiązywania problemów z magazynu Azure (i innych usług). Te narzędzia nie są częścią usługi Azure Storage i niektóre produkty innych firm. Tak narzędzia omówione w tych dodatki nie są objęte każdej umowy pomocy technicznej, które mogą wiązać Ciebie z Microsoft Azure lub usługi Azure Storage, a więc w ramach procesu oceny należy sprawdzić licencjonowania i pomocy technicznej opcje dostępne od dostawców tych narzędzi.

### <a name="appendix-1"></a>Dodatek 1: Przy użyciu programu Fiddler do przechwytywania ruchu HTTP i HTTPS
[Fiddler](http://www.telerik.com/fiddler) stanowi przydatne narzędzie do analizy ruchu HTTP i HTTPS między aplikacji klienckiej i usługi magazynu Azure są używane.

> [!NOTE]
> Fiddler mogącego zdekodować ruchu HTTPS. należy przeczytać dokumentacji Fiddler uważnie, aby zrozumieć, jak robi to i Niezrozumienie konsekwencji zabezpieczeń.
> 
> 

Ten dodatek zawiera krótki przewodnik konfigurowania narzędzia Fiddler do przechwytywania ruchu między komputer lokalny, w którym zainstalowano narzędzia Fiddler i usługi magazynu Azure.

Po uruchomieniu narzędzia Fiddler rozpocznie Przechwytywanie ruchu HTTP i HTTPS na komputerze lokalnym. Oto niektóre przydatne polecenia sterujące Fiddler:

* Zatrzymaj i uruchom Przechwytywanie ruchu. W menu głównym, przejdź do **pliku** , a następnie kliknij przycisk **przechwytywania ruchu** Przełącz ustawienie opcji przechwytywania włączać i wyłączać.
* Zapisz przechwycone dane o ruchu. W menu głównym, przejdź do **pliku**, kliknij przycisk **zapisać**, a następnie kliknij przycisk **wszystkie sesje**: dzięki temu można zapisać dane w pliku archiwum sesji. Załaduj ponownie archiwum sesji później do analizy lub wysłać żądanie pomocy technicznej firmy Microsoft.

Aby ograniczyć ilość ruchu sieciowego, który przechwytuje Fiddler, możesz użyć filtrów, które są konfigurowane w **filtry** kartę. Poniższy zrzut ekranu przedstawia filtr, który przechwytuje tylko ruch wysyłany do **contosoemaildist.table.core.windows.net** końcowego magazynu:

![][5]

### <a name="appendix-2"></a>Dodatek 2: Przy użyciu programu Wireshark do przechwytywania ruchu sieciowego
[Wireshark](http://www.wireshark.org/) jest analizatora protokołów sieciowych, który umożliwia wyświetlenie informacji szczegółowych pakietów dla szerokiego zakresu protokołów sieciowych.

Poniższej procedury przedstawiono sposób przechwytywania pakietów szczegółowe informacje o ruchu z komputera lokalnego zainstalowaną Wireshark usłudze tabel na koncie magazynu Azure.

1. Uruchamianie programu Wireshark na komputerze lokalnym.
2. W **Start** wybierz lokalnego interfejsu sieciowego lub interfejsów, które są połączone z Internetem.
3. Kliknij przycisk **przechwytywania opcje**.
4. Dodaj filtr do **filtr przechwytywania** pola tekstowego. Na przykład **hosta contosoemaildist.table.core.windows.net** skonfiguruje Wireshark przechwytywania tylko pakiety wysyłane do lub z punkt końcowy usługi tabel w **contosoemaildist** konta magazynu. Zapoznaj się z [pełną listę filtrów przechwytywania](http://wiki.wireshark.org/CaptureFilters).
   
   ![][6]
5. Kliknij przycisk **Start**. Wireshark teraz Przechwyć wszystkie pakiety wysyłania do lub z punkt końcowy usługi tabel, ponieważ na komputerze lokalnym za pomocą aplikacji klienta.
6. Po zakończeniu, kliknij menu głównego **przechwytywania** , a następnie **zatrzymać**.
7. Aby zapisać przechwycone dane w pliku przechwytywania programu Wireshark, w menu głównym kliknij **pliku** , a następnie **zapisać**.

WireShark wyróżnione wszelkie błędy, które istnieją w **packetlist** okna. Można również użyć **Expert informacji** okna (kliknij **Analizuj**, następnie **Expert informacji**) do wyświetlania podsumowania błędów i ostrzeżeń.

![][7]

Można również wyświetlić dane TCP, jak warstwa aplikacji widzi ona danych TCP prawym przyciskiem myszy i wybierając **wykonaj strumieniem TCP**. Jest to przydatne, jeśli przechwycić z zrzutu bez pliku przechwytywania. Aby uzyskać więcej informacji, zobacz [następujących strumienie TCP](http://www.wireshark.org/docs/wsug_html_chunked/ChAdvFollowTCPSection.html).

![][8]

> [!NOTE]
> Aby uzyskać więcej informacji o używaniu programu Wireshark, zobacz [przewodnik użytkowników programu Wireshark](http://www.wireshark.org/docs/wsug_html_chunked).
> 
> 

### <a name="appendix-3"></a>Dodatek 3: Przy użyciu programu Microsoft Message Analyzer do przechwytywania ruchu sieciowego
Możesz użyć programu Microsoft Message Analyzer do przechwytywania ruchu HTTP i HTTPS w podobny sposób jak Fiddler i przechwytywania ruchu sieciowego w podobny sposób jak Wireshark.

#### <a name="configure-a-web-tracing-session-using-microsoft-message-analyzer"></a>Konfigurowanie śledzenia sesji sieci web przy użyciu programu Microsoft Message Analyzer
Aby skonfigurować sesję śledzenia sieci web dla ruchu HTTP i HTTPS przy użyciu programu Microsoft Message Analyzer, uruchom aplikację Microsoft Message Analyzer, a następnie na **pliku** menu, kliknij przycisk **przechwytywania/Trace**. Na liście dostępnych śledzenia scenariuszy, wybierz **serwera Proxy sieci Web**. Następnie w **konfiguracji ze scenariusza śledzenia** panelu w **HostnameFilter** pole tekstowe, Dodaj nazwy punktów końcowych magazynu (można odszukać te nazwy w [portalu Azure](https://portal.azure.com)). Na przykład jeśli nazwą konta magazynu Azure jest **contosodata**, należy dodać następujące polecenie, aby **HostnameFilter** pole tekstowe:

```
contosodata.blob.core.windows.net contosodata.table.core.windows.net contosodata.queue.core.windows.net
```

> [!NOTE]
> Znak odstępu oddziela nazwy hostów.
> 
> 

Po osiągnięciu gotowości rozpocząć zbieranie danych śledzenia, kliknij przycisk **Start With** przycisku.

Aby uzyskać więcej informacji na temat programu Microsoft Message Analyzer **serwera Proxy sieci Web** śledzenia, zobacz [dostawcy Microsoft-PEF-WebProxy](http://technet.microsoft.com/library/jj674814.aspx).

Wbudowane **serwera Proxy sieci Web** śledzenia w programie Microsoft Message Analyzer jest oparta na Fiddler; można przechwytywać ruch HTTPS po stronie klienta i wyświetlić wiadomości niezaszyfrowane HTTPS. **Serwera Proxy sieci Web** śledzenia działa przez konfigurowanie lokalnego serwera proxy dla całego ruchu HTTP i HTTPS, który umożliwia on dostęp do wiadomości niezaszyfrowane.

#### <a name="diagnosing-network-issues-using-microsoft-message-analyzer"></a>Diagnozowanie problemów z siecią przy użyciu programu Microsoft Message Analyzer
Oprócz przy użyciu programu Microsoft Message Analyzer **serwera Proxy sieci Web** śledzenia do przechwytywania szczegóły ruchu HTTP/HTTPs między aplikacji klienckiej i usługi magazynu, można również użyć wbudowanych **lokalnego warstwy łącza** śledzenia do przechwycenia informacji pakietów sieciowych. Ta umożliwia przechwytywanie danych podobny do przechwycenia z programu Wireshark i diagnozowania sieci problemy takie jak porzuconych pakietów.

Poniższy zrzut ekranu przedstawia przykład **lokalnego warstwy łącza** śledzenia z niektórymi **informacyjny** w wiadomości **DiagnosisTypes** kolumny. Kliknięcie ikony w **DiagnosisTypes** kolumna zawiera treść komunikatu. W tym przykładzie serwera retransmitowane wiadomości w #305, ponieważ nie otrzymano potwierdzenia z klienta:

![][9]

Utworzenie sesji śledzenia programu Microsoft Message Analyzer, można określić filtrów w celu zmniejszenia szumu w śledzeniu. Na **przechwytywania / Trace** służącego do określania śledzenia, kliknij na **Konfiguruj** znajdujący się obok podsekcji **Microsoft-Windows-NDIS-PacketCapture**. Poniższy zrzut ekranu przedstawia konfigurację filtrujące ruch TCP dla adresów IP trzy usług magazynu:

![][10]

Aby uzyskać więcej informacji o trace Microsoft komunikatów analizatora lokalnego warstwy łącza, zobacz [dostawcy Microsoft-PEF-NDIS-PacketCapture](http://technet.microsoft.com/library/jj659264.aspx).

### <a name="appendix-4"></a>Dodatek 4: Przy użyciu programu Excel, aby wyświetlić metryki i dane dziennika
Wiele narzędzi umożliwiają pobranie metryki magazynu danych z magazynu tabel platformy Azure w formacie rozdzielanym, który ułatwia załadować dane do programu Excel do wyświetlania i analizy. Magazyn rejestrowania danych z magazynu obiektów blob platformy Azure jest już w formacie rozdzielanym, który można załadować do programu Excel. Jednak należy dodać odpowiednie nagłówki kolumn na podstawie informacji w [Format dziennika analityka magazynu](http://msdn.microsoft.com/library/azure/hh343259.aspx) i [schemat tabeli metryki analityka magazynu](http://msdn.microsoft.com/library/azure/hh343264.aspx).

Aby zaimportować rejestrowania magazynu danych do programu Excel po pobraniu go z magazynu obiektów blob:

* Na **danych** menu, kliknij przycisk **tekst z**.
* Przejdź do pliku dziennika, które chcesz wyświetlić, a następnie kliknij przycisk **importu**.
* W kroku 1 **Kreatora importu tekstu**, wybierz pozycję **rozdzielany**.

W kroku 1 **Kreatora importu tekstu**, wybierz pozycję **średnik** jako ogranicznik tylko i wybierz polecenie podwójnego cudzysłowu jako **kwalifikator tekstu**. Następnie kliknij przycisk **Zakończ** i wybierz lokalizację danych w skoroszycie.

### <a name="appendix-5"></a>Dodatek 5: Monitorowanie za pomocą usługi Application Insights dla programu Visual Studio Team Services
Umożliwia także funkcji usługi Application Insights dla programu Visual Studio Team Services jako część wydajności i dostępności monitorowania. To narzędzie można:

* Upewnij się, że usługi sieci web jest dostępna i elastyczny. Czy aplikacja jest witryną sieci web lub aplikacji urządzenia, który używa usługi sieci web, jego test adresu URL co kilka minut z lokalizacji na całym świecie i informacją o tym, jeśli występuje problem.
* Szybkie diagnozowanie problemów z wydajnością ani wyjątków w usłudze sieci web. Dowiedz się, jeśli są rozciągnięcia procesora CPU lub innych zasobów, odczytać śladów stosu wyjątków i łatwo wyszukiwać dane dziennika śledzenia. Jeżeli wydajność aplikacji spadnie poniżej akceptowalnego limity, firma Microsoft może wysyłać Ci wiadomości e-mail. Można monitorować usługi sieci web .NET i Java.

Więcej informacji można znaleźć [co to jest usługa Application Insights](../../application-insights/app-insights-overview.md).

<!--Anchors-->
[Wprowadzenie]: #introduction
[Jak jest zorganizowana w tym przewodniku]: #how-this-guide-is-organized

[monitorowania usługi magazynu]: #monitoring-your-storage-service
[Monitorowanie kondycji usługi]: #monitoring-service-health
[Monitorowanie wydajności]: #monitoring-capacity
[Monitorowanie dostępności]: #monitoring-availability
[Monitorowanie wydajności]: #monitoring-performance

[diagnozowanie problemów z magazynowaniem]: #diagnosing-storage-issues
[Problemy z usługi kondycji]: #service-health-issues
[problemy z wydajnością]: #performance-issues
[Diagnozowanie błędów]: #diagnosing-errors
[Problemy z emulatora magazynu]: #storage-emulator-issues
[Narzędzia rejestrowania magazynu]: #storage-logging-tools
[Przy użyciu narzędzia rejestracji w sieci]: #using-network-logging-tools

[śledzenia End-to-end]: #end-to-end-tracing
[Korelowanie dane dziennika]: #correlating-log-data
[Identyfikator żądania klienta]: #client-request-id
[identyfikator żądania serwera]: #server-request-id
[Znaczniki czasu]: #timestamps

[wskazówki rozwiązywania problemów]: #troubleshooting-guidance
[metryki pokazują AverageE2ELatency wysoki i niski AverageServerLatency]: #metrics-show-high-AverageE2ELatency-and-low-AverageServerLatency
[Metryki wskazują niską wartość AverageE2ELatency i niską wartość AverageServerLatency, ale na kliencie występuje duże opóźnienie]: #metrics-show-low-AverageE2ELatency-and-low-AverageServerLatency
[Metryki wskazują wysoką wartość AverageServerLatency]: #metrics-show-high-AverageServerLatency
[Występują nieoczekiwane opóźnienia w dostarczaniu komunikatów w kolejce]: #you-are-experiencing-unexpected-delays-in-message-delivery

[metryki spowodować wzrost PercentThrottlingError]: #metrics-show-an-increase-in-PercentThrottlingError
[Przejściowa wzrost PercentThrottlingError]: #transient-increase-in-PercentThrottlingError
[Stałe zwiększanie PercentThrottlingError błąd]: #permanent-increase-in-PercentThrottlingError
[metryki spowodować wzrost PercentTimeoutError]: #metrics-show-an-increase-in-PercentTimeoutError
[Metryki wskazują wzrost wartości PercentNetworkError]: #metrics-show-an-increase-in-PercentNetworkError

[Klient odbiera komunikaty HTTP 403 (Dostęp zabroniony)]: #the-client-is-receiving-403-messages
[Klient odbiera komunikaty HTTP 404 (nie znaleziono)]: #the-client-is-receiving-404-messages
[Klient lub inny proces wcześniej usunięty obiekt]: #client-previously-deleted-the-object
[Problem autoryzacji dostępu sygnatury dostępu Współdzielonego]: #SAS-authorization-issue
[Kod JavaScript po stronie klienta nie ma uprawnień dostępu do tego obiektu]: #JavaScript-code-does-not-have-permission
[Błąd sieci]: #network-failure
[Klient odbiera komunikaty HTTP 409 (konflikt)]: #the-client-is-receiving-409-messages

[metryki pokazują PercentSuccess niskim lub wpisy dziennika analytics ma operacji ze stanem transakcji ClientOtherErrors]: #metrics-show-low-percent-success
[Metryki pojemności Pokaż nieoczekiwane zwiększenie wykorzystania pojemności magazynu]: #capacity-metrics-show-an-unexpected-increase
[Problem wynika z przy użyciu emulatora magazynu dla rozwoju lub testowania]: #your-issue-arises-from-using-the-storage-emulator
[Funkcja "X" nie działa w emulatorze magazynu]: #feature-X-is-not-working
[Błąd "wartość dla jednego z nagłówków HTTP nie jest w nieprawidłowym formacie" przy użyciu emulatora magazynu]: #error-HTTP-header-not-correct-format
[Uruchomiony emulator magazynu wymaga uprawnień administracyjnych]: #storage-emulator-requires-administrative-privileges
[Pojawiły się problemy z instalacją zestawu Azure SDK dla platformy .NET]: #you-are-encountering-problems-installing-the-Windows-Azure-SDK
[Inny problem z usługą magazynu]: #you-have-a-different-issue-with-a-storage-service

[dodatki]: #appendices
[dodatek 1: przy użyciu programu Fiddler do przechwytywania ruchu HTTP i HTTPS]: #appendix-1
[dodatek 2: do przechwytywania ruchu sieciowego przy użyciu programu Wireshark]: #appendix-2
[dodatku 3: do przechwytywania ruchu sieciowego przy użyciu programu Microsoft Message Analyzer]: #appendix-3
[Dodatek 4: Przy użyciu programu Excel, aby wyświetlić metryki i dane dziennika]: #appendix-4
[Dodatek 5: Monitorowanie za pomocą usługi Application Insights dla programu Visual Studio Team Services]: #appendix-5

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
