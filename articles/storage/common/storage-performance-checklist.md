---
title: Lista kontrolna wydajności i skalowalności usługi Azure Storage | Microsoft Docs
description: Lista kontrolna sprawdzonych praktyk do użycia z usługą Azure Storage w przypadku tworzenia aplikacji wykonujących aplikacje.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 06/07/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: ee216bd4d6994179e347465c30039f2f8e293c85
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/16/2019
ms.locfileid: "68233017"
---
# <a name="microsoft-azure-storage-performance-and-scalability-checklist"></a>Lista kontrolna wydajności i skalowalności Microsoft Azure Storage

Od momentu wydania usług Microsoft Azure Storage, firma Microsoft opracowała kilka sprawdzonych praktyk związanych z korzystaniem z tych usług w sposób właściwy, a ten artykuł służy do konsolidacji najważniejszych z nich do listy stylów list kontrolnych. Zamiarem tego artykułu jest ułatwienie deweloperom aplikacji zweryfikowania, że korzystają z sprawdzonych praktyk w usłudze Azure Storage i pomagają im identyfikować inne sprawdzone praktyki, które powinny rozważyć przyjęcie. Ten artykuł nie podejmuje próby zakrycia każdej możliwej optymalizacji wydajności i skalowalności — wyklucza te, które są niewielkie w ich wpływie lub nie są szeroko stosowane. W takim zakresie, w jakim zachowanie aplikacji może być przewidywane podczas projektowania, warto pamiętać o wczesnym zapewnieniu, aby uniknąć projektów, które będą działały w przypadku problemów z wydajnością.  

Każdy deweloper aplikacji korzystający z usługi Azure Storage powinien odczytać ten artykuł, a następnie sprawdzić, czy ich aplikacje są zgodne z poszczególnymi opisanymi praktykami wymienionymi poniżej.  

## <a name="checklist"></a>Lista kontrolna

Ten artykuł organizuje sprawdzone praktyki w następujących grupach. Sprawdzone praktyki mające zastosowanie do:  

* Wszystkie usługi Azure Storage (obiekty blob, tabele, kolejki i pliki)
* Obiekty blob
* Tabele
* Kolejki  

| Gotowe | Obszar | Kategoria | Pytanie |
| --- | --- | --- | --- |
| &nbsp; | Wszystkie usługi |Tarcze skalowalności |[Czy aplikacja została zaprojektowana tak, aby uniknąć zbliżania się celów skalowalności?](#subheading1) |
| &nbsp; | Wszystkie usługi |Tarcze skalowalności |[Czy Twoja Konwencja nazewnictwa została zaprojektowana w celu umożliwienia lepszego równoważenia obciążenia?](#subheading47) |
| &nbsp; | Wszystkie usługi |Networking |[Czy urządzenia po stronie klienta mają dostatecznie wysoką przepustowość i małe opóźnienia w celu osiągnięcia wymaganej wydajności?](#subheading2) |
| &nbsp; | Wszystkie usługi |Networking |[Czy urządzenia po stronie klienta mają wystarczająco dużo linku jakości?](#subheading3) |
| &nbsp; | Wszystkie usługi |Networking |[Czy aplikacja kliencka znajduje się "blisko" konta magazynu?](#subheading4) |
| &nbsp; | Wszystkie usługi |Dystrybucja zawartości |[Czy używasz sieci CDN do dystrybucji zawartości?](#subheading5) |
| &nbsp; | Wszystkie usługi |Bezpośredni dostęp klienta |[Czy używasz sygnatury dostępu współdzielonego i mechanizmu CORS, aby zezwalać na bezpośredni dostęp do magazynu zamiast serwera proxy?](#subheading6) |
| &nbsp; | Wszystkie usługi |Buforowanie |[Czy aplikacja jest używana do buforowania danych, które są wielokrotnie używane i rzadko zmieniane?](#subheading7) |
| &nbsp; | Wszystkie usługi |Buforowanie |[Czy aktualizacje wsadowe aplikacji (buforowanie po stronie klienta, a następnie przekazywanie w większych zestawach)?](#subheading8) |
| &nbsp; | Wszystkie usługi |Konfiguracja platformy .NET |[Czy skonfigurowano klienta tak, aby używał wystarczającej liczby jednoczesnych połączeń?](#subheading9) |
| &nbsp; | Wszystkie usługi |Konfiguracja platformy .NET |[Czy skonfigurowano platformę .NET do używania wystarczającej liczby wątków?](#subheading10) |
| &nbsp; | Wszystkie usługi |Konfiguracja platformy .NET |[Czy używasz programu .NET 4,5 lub nowszego, który ma ulepszone odzyskiwanie pamięci?](#subheading11) |
| &nbsp; | Wszystkie usługi |Równoległości |[Czy zapewniono, że równoległość jest odpowiednio ograniczona, aby nie przeciążać możliwości klientów ani celów skalowalności?](#subheading12) |
| &nbsp; | Wszystkie usługi |Narzędzia |[Czy używasz najnowszej wersji dostarczonych przez firmę Microsoft bibliotek i narzędzi klienta?](#subheading13) |
| &nbsp; | Wszystkie usługi |Ponowne próby |[Czy używasz wykładniczej zasady ponawiania wycofywania do ograniczania błędów i limitów czasu?](#subheading14) |
| &nbsp; | Wszystkie usługi |Ponowne próby |[Czy aplikacja unika ponawiania prób w przypadku błędów, które nie są ponawiane?](#subheading15) |
| &nbsp; | Obiekty blob |Tarcze skalowalności |[Czy masz dużą liczbę klientów uzyskujących dostęp do pojedynczego obiektu jednocześnie?](#subheading46) |
| &nbsp; | Obiekty blob |Tarcze skalowalności |[Czy Twoja aplikacja jest zastosowana w celu zapewnienia przepustowości lub skalowalności operacji dla pojedynczego obiektu BLOB?](#subheading16) |
| &nbsp; | Obiekty blob |Kopiowanie obiektów BLOB |[Czy są kopiowane obiekty blob w wydajny sposób?](#subheading17) |
| &nbsp; | Obiekty blob |Kopiowanie obiektów BLOB |[Czy używasz AzCopy do zbiorczego kopiowania obiektów BLOB?](#subheading18) |
| &nbsp; | Obiekty blob |Kopiowanie obiektów BLOB |[Czy używasz usługi Azure Import/Export do transferowania dużych ilości danych?](#subheading19) |
| &nbsp; | Obiekty blob |Użyj metadanych |[Czy są przechowywane często używane metadane dotyczące obiektów BLOB w swoich metadanych?](#subheading20) |
| &nbsp; | Obiekty blob |Szybkie przekazywanie |[Czy podczas próby przekazania jednego obiektu BLOB szybko przekazująsz bloki?](#subheading21) |
| &nbsp; | Obiekty blob |Szybkie przekazywanie |[Czy podczas próby przeładowania wielu obiektów BLOB szybko przekazujesz obiekty blob?](#subheading22) |
| &nbsp; | Obiekty blob |Prawidłowy typ obiektu BLOB |[Czy używasz stronicowych obiektów blob lub blokowych obiektów BLOB w razie potrzeby?](#subheading23) |
| &nbsp; | Tabele |Tarcze skalowalności |[Czy zbliżasz się do elementów docelowych skalowalności dla jednostek na sekundę?](#subheading24) |
| &nbsp; | Tabele |Konfigurowanie |[Czy używasz formatu JSON dla żądań tabeli?](#subheading25) |
| &nbsp; | Tabele |Konfigurowanie |[Czy zostały wyłączone nagle w celu zwiększenia wydajności małych żądań?](#subheading26) |
| &nbsp; | Tabele |Tabele i partycje |[Czy masz poprawnie partycjonowane dane?](#subheading27) |
| &nbsp; | Tabele |Partycje aktywne |[Czy unikasz wzorców tylko do dołączania i tylko do prefiksu?](#subheading28) |
| &nbsp; | Tabele |Partycje aktywne |[Czy operacje wstawiania/aktualizowania są rozłożone na wiele partycji?](#subheading29) |
| &nbsp; | Tabele |Zakres zapytania |[Czy schemat został zaprojektowany w taki sposób, aby zezwalał na używanie zapytań do punktów w większości przypadków, a zapytania tabeli, które mają być używane oszczędnie?](#subheading30) |
| &nbsp; | Tabele |Gęstość zapytania |[Czy zapytania zwykle skanują i zwracają wiersze, które będą używane przez aplikację?](#subheading31) |
| &nbsp; | Tabele |Ograniczanie zwracanych danych |[Czy używasz filtrowania, aby uniknąć zwracania jednostek, które nie są potrzebne?](#subheading32) |
| &nbsp; | Tabele |Ograniczanie zwracanych danych |[Czy używasz projekcji, aby uniknąć zwracania właściwości, które nie są potrzebne?](#subheading33) |
| &nbsp; | Tabele |Denormalizacja |[Czy masz rozznormalizowane dane, aby uniknąć nieefektywnych zapytań lub wielu żądań odczytu podczas próby pobrania danych?](#subheading34) |
| &nbsp; | Tabele |Wstaw/Aktualizuj/Usuń |[Czy są przetwarzane zbiorczo żądania, które muszą być transakcyjne lub mogą być wykonywane w tym samym czasie w celu zmniejszenia liczby operacji rundy?](#subheading35) |
| &nbsp; | Tabele |Wstaw/Aktualizuj/Usuń |[Czy unikasz pobierania jednostki tylko w celu ustalenia, czy chcesz wywołać Wstawianie czy aktualizowanie?](#subheading36) |
| &nbsp; | Tabele |Wstaw/Aktualizuj/Usuń |[Czy należy wziąć pod uwagę przechowywanie serii danych, które często będą pobierane razem w jednej jednostce jako właściwości zamiast wielu jednostek?](#subheading37) |
| &nbsp; | Tabele |Wstaw/Aktualizuj/Usuń |[W przypadku jednostek, które będą zawsze pobierane razem i mogą być zapisywane w partiach (na przykład dane szeregów czasowych), należy wziąć pod uwagę użycie obiektów BLOB zamiast tabel?](#subheading38) |
| &nbsp; | Kolejki |Tarcze skalowalności |[Czy zbliżasz się do skalowalnych elementów docelowych dla komunikatów na sekundę?](#subheading39) |
| &nbsp; | Kolejki |Konfigurowanie |[Czy zostały wyłączone nagle w celu zwiększenia wydajności małych żądań?](#subheading40) |
| &nbsp; | Kolejki |Rozmiar komunikatu |[Czy komunikaty są kompaktowe, aby zwiększyć wydajność kolejki?](#subheading41) |
| &nbsp; | Kolejki |Pobieranie zbiorcze |[Czy pobierasz wiele komunikatów w ramach jednej operacji "Get"?](#subheading42) |
| &nbsp; | Kolejki |Częstotliwość sondowania |[Czy trwa sondowanie na tyle często, aby zmniejszyć postrzegane opóźnienie aplikacji?](#subheading43) |
| &nbsp; | Kolejki |Aktualizuj komunikat |[Czy używasz UpdateMessage do przechowywania postępu w przetwarzaniu komunikatów, unikając konieczności ponownego przetwarzania całej wiadomości w przypadku wystąpienia błędu?](#subheading44) |
| &nbsp; | Kolejki |Architektura |[Czy używasz kolejek, aby zwiększyć skalowalność całej aplikacji przez utrzymywanie długotrwałych obciążeń z ścieżki krytycznej i skalowalność niezależnie?](#subheading45) |

## <a name="allservices"></a>Wszystkie usługi

W tej sekcji przedstawiono sprawdzone praktyki dotyczące korzystania z dowolnych usług Azure Storage (obiektów blob, tabel, kolejek lub plików).  

### <a name="subheading1"></a>Tarcze skalowalności

Sam magazyn platformy Azure ma limit 250 kont magazynu na region na subskrypcję. W przypadku osiągnięcia tego limitu nie będzie można utworzyć kolejnych kont magazynu w tej kombinacji subskrypcji/regionu.

Każda z usług Azure Storage ma tarcze skalowalności dla pojemności (GB), liczby transakcji i przepustowości. Jeśli aplikacja zbliża się lub przekroczy elementy docelowe skalowalności, może wystąpić zwiększenie opóźnień transakcji lub ograniczenie przepustowości. Gdy usługa magazynu ogranicza swoją aplikację, usługa zaczyna zwracać kody błędów "503 serwer zajęty" lub "limit czasu operacji 500" dla niektórych transakcji magazynu. W tej części omówiono ogólny sposób postępowania z celami skalowalności i celami skalowalności przepustowości. Dalsze sekcje, które zajmują się poszczególnymi usługami magazynu omawiają elementy docelowe skalowalności w kontekście tej konkretnej usługi:  

* [Przepustowość obiektów blob i żądania na sekundę](#subheading16)
* [Jednostki tabeli na sekundę](#subheading24)
* [Komunikaty w kolejce na sekundę](#subheading39)  

#### <a name="sub1bandwidth"></a>Cel skalowalności przepustowości dla wszystkich usług

W momencie pisania, cele przepustowości w Stanach Zjednoczonych dla magazynu geograficznie nadmiarowego (GRS) są 10 Gigabit na sekundę (GB/s) dla ruchu przychodzącego (dane wysyłane do konta magazynu) i 20 GB/s dla danych wychodzących (dane wysyłane z konta magazynu). W przypadku konta usługi Magazyn lokalnie nadmiarowy (LRS) limity są wyższe — 20 GB/s w przypadku ruchu przychodzącego i 30 GB/s na ruch wychodzący.  Limity przepustowości międzynarodowej mogą być niższe i można je znaleźć na naszej [stronie elementów docelowych skalowalności](https://msdn.microsoft.com/library/azure/dn249410.aspx).  Aby uzyskać więcej informacji na temat opcji nadmiarowości magazynu, zobacz linki w obszarze przydatne zasoby poniżej.  

#### <a name="what-to-do-when-approaching-a-scalability-target"></a>Co należy zrobić podczas zbliżania się celu skalowalności

Jeśli zbliżasz się do limitu kont magazynu, które możesz mieć w określonej kombinacji subskrypcji/regionu, Oceń swoją aplikację i użycie kont magazynu i ustal, czy którykolwiek z tych warunków ma zastosowanie.

* Używanie kont magazynu jako dysków niezarządzanych i Dodawanie tych dysków do maszyn wirtualnych. W tym scenariuszu zalecamy używanie [dysków zarządzanych](../../virtual-machines/windows/managed-disks-overview.md), ponieważ obsługują one skalowalność dysku magazynu, bez konieczności tworzenia poszczególnych kont magazynu i zarządzania nimi.
* Korzystanie z jednego konta magazynu dla poszczególnych klientów na potrzeby izolacji danych. W tym scenariuszu zalecamy używanie kontenerów magazynu dla każdego klienta, a nie całego konta magazynu. Usługa Azure Storage umożliwia teraz określenie kontroli dostępu opartej na rolach dla poszczególnych [kontenerów](storage-auth-aad-rbac-portal.md).
* Używanie wielu kont magazynu do fragmentu w celu zwiększenia skalowalności ruchu przychodzącego/wychodzącego/operacji we/wy na sekundę. W tym scenariuszu, jeśli jest to możliwe, zalecamy skorzystanie z [zwiększonych limitów](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/) kont magazynu w warstwie Standardowa, aby zmniejszyć liczbę kont magazynu wymaganych do obciążania.

Jeśli aplikacja zbliża się do celów skalowalności dla jednego konta magazynu, należy rozważyć przyjęcie jednej z następujących metod:  

* Zapoznaj się z obciążeniem, które powoduje, że aplikacja może obsłużyć lub przekroczyć obiekt docelowy skalowalności. Czy można projektować inaczej, aby używać mniejszej przepustowości lub pojemności lub mniejszej liczby transakcji?
* Jeśli aplikacja musi przekroczyć jeden z celów skalowalności, należy utworzyć wiele kont magazynu i podzielić na partycje dane aplikacji na te wiele kont magazynu. Jeśli używasz tego wzorca, pamiętaj, aby zaprojektować aplikację tak, aby w przyszłości można było dodać więcej kont magazynu do równoważenia obciążenia. W czasie pisania Każda subskrypcja platformy Azure może mieć do 250 kont magazynu na region (w przypadku wdrożenia z modelem Azure Resource Manager).  Konta magazynu nie mają też kosztu innego niż użycie w odniesieniu do danych przechowywanych, wykonanych transakcji lub przesłanych danych.
* Jeśli aplikacja trafi na tarcze przepustowości, rozważ kompresję danych na kliencie, aby zmniejszyć przepustowość wymaganą do wysłania danych do usługi Storage.  Chociaż może to spowodować oszczędność przepustowości i zwiększenie wydajności sieci, może to również mieć negatywny wpływ na.  Należy oszacować wpływ tego wpływu na wydajność ze względu na dodatkowe wymagania dotyczące przetwarzania kompresji i dekompresowania danych w kliencie. Ponadto przechowywanie skompresowanych danych może utrudniać Rozwiązywanie problemów, ponieważ może być trudniejsze do wyświetlenia przechowywanych danych przy użyciu standardowych narzędzi.
* Jeśli aplikacja trafi tarcze skalowalności, upewnij się, że używasz wykładniczej wycofywania do ponawiania prób (zobacz [ponownych prób](#subheading14)).  Lepiej jest upewnić się, że nigdy nie zbliża się do obiektów docelowych skalowalności (przy użyciu jednej z powyższych metod), ale zapewnia to, że aplikacja nie będzie po prostu ponawiać próby, powodując gorszą przepływność.  

#### <a name="useful-resources"></a>Przydatne zasoby

Poniższe linki zawierają dodatkowe szczegóły dotyczące skalowalności:

* Informacje o obiektach docelowych skalowalności [i wydajności usługi Azure Storage](storage-scalability-targets.md) można znaleźć w temacie.
* Aby uzyskać informacje na temat opcji nadmiarowości magazynu, zobacz [Replikacja usługi Azure Storage](storage-redundancy.md) i wpis w blogu [Opcje nadmiarowości usługi Azure Storage oraz dostęp do odczytu geograficznie](https://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx) nadmiarowego.
* Aby uzyskać aktualne informacje o cenach dla usług platformy Azure, zobacz [Cennik platformy Azure](https://azure.microsoft.com/pricing/overview/).  

### <a name="subheading47"></a>Konwencja nazewnictwa partycji

Usługa Azure Storage korzysta z schematu partycjonowania opartego na zakresie do skalowania i równoważenia obciążenia systemu. Klucz partycji (konto + kontener + obiekt BLOB) służy do partycjonowania danych do zakresów, a te zakresy są zrównoważone obciążenie w systemie. Oznacza to, że konwencje nazewnictwa, takie jak porządkowanie leksykalne (na przykład moja *Lista płac*, moja *wydajność*, *pracownicy*itp.) lub użycie sygnatur czasowych (*log20160101*, *log20160102*, *log20160102*itp.), będą udzielać sama do partycji, które mogą się znajdować na tym samym serwerze partycji, dopóki operacja równoważenia obciążenia nie podzieli ich na mniejsze zakresy. Na przykład wszystkie obiekty blob w kontenerze mogą być obsługiwane przez jeden serwer do momentu, aż obciążenie tych obiektów BLOB wymaga ponownego zrównoważenia zakresów partycji. Analogicznie, Grupa jasno załadowanych kont z ich nazwami uporządkowanymi w kolejności leksykalnej może być obsługiwana przez pojedynczy serwer do momentu, gdy obciążenie jednego lub wszystkich tych kont nie będzie wymagało podzielenia między serwery z wieloma partycjami. Każda operacja równoważenia obciążenia może mieć wpływ na opóźnienie wywołań magazynu podczas operacji. Zdolność systemu do obsługi nagłego rozłożenia ruchu do partycji jest ograniczona przez skalowalność serwera pojedynczej partycji, dopóki operacja równoważenia obciążenia nie zostanie rozpoczęta i ponownie zrównoważy zakres kluczy partycji.

Aby zmniejszyć częstotliwość takich operacji, można wykonać kilka najlepszych rozwiązań.  

* Jeśli to możliwe, użyj większych rozmiarów obiektów blob lub Put bloków (więcej niż 4 MiB dla kont standardowych i więcej niż 256 KiB dla kont Premium), aby uaktywnić blokowy obiekt BLOB o wysokiej przepływności (HTBB). HTBB zapewnia wysoką wydajność, która nie ma wpływ na nazewnictwo partycji.
* Należy dokładnie zapoznać się z konwencją nazewnictwa używaną dla kont, kontenerów, obiektów blob, tabel i kolejek. Należy rozważyć prefiksowanie nazw kont, kontenerów lub obiektów blob z 3-cyfrowym skrótem przy użyciu funkcji tworzenia skrótów, która najlepiej odpowiada Twoim potrzebom.  
* Jeśli organizujesz dane przy użyciu sygnatur czasowych lub identyfikatorów liczbowych, musisz upewnić się, że nie używasz wzorców ruchu tylko do dołączania (lub tylko do odczytu). Wzorce te nie są odpowiednie dla systemu partycjonowania opartego na zakresie i mogą prowadzić do całego ruchu kierowanego do jednej partycji i ograniczając system od efektywnego równoważenia obciążenia. Na przykład jeśli masz codzienne operacje, które używają obiektu BLOB z sygnaturą czasową, taką jak *RRRRMMDD*, cały ruch dla tej codziennej operacji jest kierowany do pojedynczego obiektu, który jest obsługiwany przez serwer z jedną partycją. Sprawdź, czy limity na obiekt BLOB i limity partycji spełniają Twoje potrzeby, i rozważ przerwanie tej operacji do wielu obiektów BLOB w razie potrzeby. Podobnie, Jeśli przechowujesz dane szeregów czasowych w tabelach, cały ruch może być kierowany do ostatniej części przestrzeni nazw kluczy. Jeśli musisz użyć sygnatur czasowych lub identyfikatorów liczbowych, poprzedź identyfikator prefiksem 3-cyfrowym lub w przypadku sygnatur czasowych prefiks części sekund czasu, takich jak *ssyyyymmdd*. Jeśli operacje tworzenia listy i wykonywania zapytań są wykonywane rutynowo, należy wybrać funkcję tworzenia skrótów, która będzie ograniczać liczbę zapytań. W innych przypadkach losowy prefiks może być wystarczający.  
* Aby uzyskać dodatkowe informacje o schemacie partycjonowania używanym w usłudze [Azure Storage, zobacz Azure Storage: Usługa magazynu w chmurze o wysokiej dostępności z silną spójnością](https://sigops.org/sosp/sosp11/current/2011-Cascais/printable/11-calder.pdf).

### <a name="networking"></a>Networking

Chociaż wywołania interfejsu API są zależne, często ograniczenia sieci fizycznej aplikacji mają znaczny wpływ na wydajność. Poniżej opisano niektóre ograniczenia, które mogą napotkać użytkownicy.  

#### <a name="client-network-capability"></a>Możliwość sieci klienta

##### <a name="subheading2"></a>Kazany

W przypadku przepustowości problem jest często możliwością klienta programu. Na przykład, chociaż jedno konto magazynu może obsługiwać 10 GB/s lub więcej ruchu przychodzącego (zobacz [elementy docelowe skalowalności przepustowości](#sub1bandwidth)), szybkość sieci w przypadku wystąpienia roli procesu roboczego platformy Azure "małe" ma maksymalnie 100 MB/s. Większe wystąpienia platformy Azure mają karty sieciowe o większej pojemności, dlatego należy rozważyć użycie większego wystąpienia lub większej liczby maszyn wirtualnych, jeśli potrzebne są wyższe limity sieci z jednej maszyny. Jeśli uzyskujesz dostęp do usługi magazynu z aplikacji lokalnej, ta sama reguła ma zastosowanie: omówienie możliwości sieciowych urządzenia klienckiego i połączenia sieciowego z lokalizacją usługi Azure Storage, a następnie popraw je w razie potrzeby lub Zaprojektuj Aplikacja do pracy w ramach możliwości.  

##### <a name="subheading3"></a>Jakość łącza

Podobnie jak w przypadku dowolnego użycia sieci należy pamiętać, że warunki sieci powodujące błędy i utrata pakietów spowodują spowolnienie przepływności.  Korzystanie z programu WireShark lub NetMon może pomóc w zdiagnozowaniu tego problemu.  

##### <a name="useful-resources"></a>Przydatne zasoby

Aby uzyskać więcej informacji o rozmiarach maszyn wirtualnych i przydzieloną przepustowość, zobacz [rozmiary maszyn wirtualnych systemu Windows](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) lub [rozmiary maszyn wirtualnych z systemem Linux](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).  

#### <a name="subheading4"></a>Przeniesienie

W każdym środowisku rozproszonym, umieszczenie klienta w sąsiedztwie z serwerem zapewnia najlepszą wydajność. Aby uzyskać dostęp do usługi Azure Storage z najniższym opóźnieniem, Najlepsza lokalizacja klienta jest w tym samym regionie świadczenia usługi Azure. Jeśli na przykład masz witrynę sieci Web systemu Azure korzystającą z usługi Azure Storage, należy ją zlokalizować zarówno w jednym regionie (na przykład zachodnie stany USA, Azja Południowo-Wschodnia). Pozwala to skrócić czas oczekiwania i koszt — w czasie pisania użycie przepustowości w jednym regionie jest bezpłatne.  

Jeśli aplikacje klienckie nie są hostowane na platformie Azure (np. w przypadku aplikacji urządzenia przenośnego lub lokalnych usług przedsiębiorstwa), wówczas ponowne umieszczenie konta magazynu w regionie blisko urządzeń, które mają do niego dostęp, spowoduje zwykle skrócenie opóźnienia. Jeśli klienci są rozległie dystrybuowani (na przykład niektóre w Ameryka Północna, a niektóre w Europie), należy rozważyć użycie wielu kont magazynu: jeden w regionie Ameryki Północnej i jeden w regionie Europy. Pomoże to ograniczyć opóźnienia dla użytkowników w obu regionach. To podejście jest łatwiejsze do wdrożenia, jeśli dane przechowywane przez aplikacje są specyficzne dla poszczególnych użytkowników i nie wymagają replikowania danych między kontami magazynu.  Aby uzyskać szeroką dystrybucję zawartości, zalecane jest, aby uzyskać więcej informacji, zobacz następną sekcję.  

### <a name="subheading5"></a>Dystrybucja zawartości

Czasami aplikacja musi obsłużyć tę samą zawartość dla wielu użytkowników (na przykład wideo demonstracyjne dla produktu używanego na stronie głównej witryny sieci Web), znajdujące się w jednym lub wielu regionach. W tym scenariuszu należy użyć Content Delivery Network (CDN), takiego jak Azure CDN, a sieć CDN będzie używać usługi Azure Storage jako źródła danych. W przeciwieństwie do konta usługi Azure Storage, które istnieje w jednym regionie i który nie może dostarczać zawartości o małym opóźnieniu do innych regionów, Azure CDN używa serwerów w wielu centrach danych na całym świecie. Ponadto sieć CDN może zazwyczaj obsługiwać znacznie wyższe limity ruchu wychodzącego niż pojedyncze konto magazynu.  

Aby uzyskać więcej informacji na temat Azure CDN, zobacz [Azure CDN](https://azure.microsoft.com/services/cdn/).  

### <a name="subheading6"></a>Używanie sygnatury dostępu współdzielonego i mechanizmu CORS

W przypadku konieczności autoryzowania kodu, takiego jak JavaScript w przeglądarce sieci Web użytkownika lub aplikacji mobilnej dla urządzeń przenośnych w celu uzyskania dostępu do danych w usłudze Azure Storage, jedno podejście polega na użyciu aplikacji w roli sieci Web jako serwera proxy: urządzenie użytkownika uwierzytelnia się za pomocą roli sieci Web , które z kolei autoryzują dostęp do zasobów magazynu. W ten sposób można uniknąć ujawniania kluczy konta magazynu na niezabezpieczonych urządzeniach. Powoduje to jednak znaczne obciążenie roli sieci Web, ponieważ wszystkie dane przesyłane między urządzeniem użytkownika a usługą magazynu muszą być przekazywane przez rolę sieci Web. Możesz uniknąć używania roli sieci Web jako serwera proxy dla usługi magazynu, używając sygnatur dostępu współdzielonego (SAS), czasami w połączeniu z nagłówkami udostępniania zasobów między źródłami (CORS). Za pomocą SYGNATURy dostępu współdzielonego można zezwolić urządzeniu użytkownika na wykonywanie żądań bezpośrednio do usługi magazynu przy użyciu ograniczonego tokenu. Na przykład jeśli użytkownik chce przekazać zdjęcie do aplikacji, rola sieci Web może generować i wysyłać do urządzenia użytkownika token sygnatury dostępu współdzielonego, który przyznaje uprawnienia do zapisu do określonego obiektu BLOB lub kontenera przez następne 30 minut (po upływie którego token sygnatury dostępu współdzielonego wygaśnie).

Zwykle przeglądarka nie zezwoli na kod JavaScript na stronie hostowanej przez witrynę sieci Web w jednej domenie, aby wykonać określone operacje, takie jak "PUT" w innej domenie. Na przykład w przypadku hostowania roli sieci Web pod adresem "contosomarketing.cloudapp.net" i użycia kodu JavaScript po stronie klienta do przekazania obiektu BLOB do konta magazynu w lokalizacji "contosoproducts.blob.core.windows.net" te zasady będą zabraniać tej operacji. Mechanizmu CORS to funkcja przeglądarki umożliwiająca domenie docelowej (w tym przypadku konto magazynu) komunikowanie się z przeglądarką, która ufa żądaniami pochodzącymi z domeny źródłowej (w tym przypadku rolą sieci Web).  

Obie te technologie mogą pomóc uniknąć niepotrzebnego obciążenia (i wąskich gardeł) w aplikacji sieci Web.  

#### <a name="useful-resources"></a>Przydatne zasoby

Aby uzyskać więcej informacji na temat SAS [, zobacz sygnatury dostępu współdzielonego, część 1: Omówienie modelu](../storage-dotnet-shared-access-signature-part-1.md)SAS.  

Aby uzyskać więcej informacji na temat mechanizmu CORS, zobacz temat [Obsługa udostępniania zasobów między źródłami (CORS) dla usług Azure Storage](https://msdn.microsoft.com/library/azure/dn535601.aspx).  

### <a name="caching"></a>Buforowanie

#### <a name="subheading7"></a>Pobieranie danych

Ogólnie rzecz biorąc, pobieranie danych z usługi jest lepsze niż dwa razy. Rozważmy przykład aplikacji sieci Web MVC działającej w roli sieci Web, która już pobrała obiekt BLOB 50-MB z usługi Storage, aby zapewnić użytkownikowi zawartość. Następnie aplikacja może pobrać ten sam obiekt BLOB za każdym razem, gdy użytkownik zażąda tego lub może buforować go lokalnie na dysku i ponownie używać buforowanej wersji dla kolejnych żądań użytkownika. Ponadto zawsze, gdy użytkownik zażąda danych, aplikacja może wydać polecenie GET z nagłówkiem warunkowym w celu zmodyfikowania czasu, co może uniemożliwić pobranie całego obiektu BLOB, jeśli nie został on zmodyfikowany. Ten sam wzorzec można zastosować do pracy z jednostkami tabeli.  

W niektórych przypadkach można zdecydować, że aplikacja może założyć, że obiekt BLOB pozostaje ważny przez krótki czas po jego pobraniu i w tym okresie aplikacja nie musi sprawdzać, czy obiekt BLOB został zmodyfikowany.

Konfiguracja, wyszukiwanie i inne dane, które są zawsze używane przez aplikację, są doskonałymi kandydatami do buforowania.  

Aby uzyskać więcej informacji na temat pobierania warunkowego, zobacz [Określanie nagłówków warunkowych dla operacji BLOB Service](/rest/api/storageservices/specifying-conditional-headers-for-blob-service-operations).  

#### <a name="subheading8"></a>Przekazywanie danych w partiach

W niektórych scenariuszach aplikacji można agregować dane lokalnie, a następnie okresowo przekazać je do partii, zamiast bezpośrednio przekazywać dane. Na przykład aplikacja sieci Web może przechowywać plik dziennika działań: aplikacja może przekazać szczegółowe informacje o każdym działaniu, tak jak w przypadku jednostki tabeli (która wymaga wielu operacji magazynowania) lub może zapisać szczegóły działania w lokalnym pliku dziennika, a następnie okresowo Przekazuj wszystkie szczegóły działania jako rozdzielany plik do obiektu BLOB. Jeśli każdy wpis dziennika ma rozmiar 1 KB, można przekazać tysiące w jednej transakcji "Put BLOB" (można przekazać obiekt BLOB o rozmiarze do 64 MB w jednej transakcji). Jeśli komputer lokalny ulegnie awarii przed przekazaniem, może to spowodować utratę niektórych danych dziennika: Deweloper aplikacji musi projektować w celu uzyskania możliwości niepowodzenia urządzenia klienckiego lub przeładowania.  Jeśli dane dotyczące aktywności muszą zostać pobrane dla przedziałów czasu (nie tylko pojedyncze działanie), obiekty blob są zalecane w porównaniu z tabelami.

### <a name="net-configuration"></a>Konfiguracja platformy .NET

W przypadku korzystania z .NET Framework Ta sekcja zawiera kilka ustawień szybkiego konfigurowania, których można użyć w celu zwiększenia wydajności.  Jeśli używasz innych języków, sprawdź, czy podobne koncepcje dotyczą wybranego języka.  

#### <a name="subheading9"></a>Zwiększ domyślny limit połączeń

W programie .NET Poniższy kod zwiększa domyślny limit połączeń (zwykle 2 w środowisku klienta lub 10 w środowisku serwera) do 100. Zazwyczaj należy ustawić wartość na około liczbę wątków używanych przez aplikację.  

```csharp
ServicePointManager.DefaultConnectionLimit = 100; //(Or More)  
```

Przed otwarciem wszystkich połączeń należy ustawić limit połączeń.  

W przypadku innych języków programowania zapoznaj się z dokumentacją tego języka, aby określić, jak ustawić limit połączeń.  

Aby uzyskać więcej informacji, zobacz wpis [w blogu usługi sieci Web: Połączenia](https://blogs.msdn.com/b/darrenj/archive/2005/03/07/386655.aspx)współbieżne.  

#### <a name="subheading10"></a>Zwiększ minimalną liczbę wątków, jeśli używasz kodu synchronicznego z zadaniami asynchronicznymi

Ten kod spowoduje zwiększenie minimalnej liczby wątków w puli wątków:  

```csharp
ThreadPool.SetMinThreads(100,100); //(Determine the right number for your application)  
```

Aby uzyskać więcej informacji, zobacz [Metoda wątków. SetMinThreads —](https://msdn.microsoft.com/library/system.threading.threadpool.setminthreads%28v=vs.110%29.aspx).  

#### <a name="subheading11"></a>Korzystanie z programu .NET 4,5 i wyższego wyrzucania elementów bezużytecznych

Użyj programu .NET 4,5 lub nowszego dla aplikacji klienckiej, aby wykorzystać ulepszenia wydajności w wyrzucaniu elementów bezużytecznych serwera.

Aby uzyskać więcej informacji, zobacz artykuł [Omówienie ulepszeń wydajności w programie .net 4,5](https://msdn.microsoft.com/magazine/hh882452.aspx).  

### <a name="subheading12"></a>Nieograniczona równoległość

Chociaż równoległość może być znakomitym rozwiązaniem dla wydajności, należy zachować ostrożność przy użyciu nieograniczonej równoległości (bez limitu liczby wątków i/lub żądań równoległych) w celu przekazywania lub pobierania danych przy użyciu wielu procesów roboczych, aby uzyskać dostęp do wielu partycji (kontenerów, kolejek lub partycje tabeli) na tym samym koncie magazynu lub w celu uzyskania dostępu do wielu elementów w tej samej partycji. Jeśli równoległość jest nieograniczona, aplikacja może przekroczyć możliwości urządzenia klienckiego lub elementy docelowe skalowalności konta magazynu, co spowodowało dłuższe opóźnienia i ograniczanie przepustowości.  

### <a name="subheading13"></a>Biblioteki i narzędzia klienta magazynu

Zawsze korzystaj z najnowszych bibliotek i narzędzi klienta dostarczonych przez firmę Microsoft. W momencie pisania istnieją biblioteki klienckie dostępne dla platformy .NET, Windows Phone, środowisko wykonawcze systemu Windows, Java i, a C++także bibliotek w wersji zapoznawczej dla innych języków. Ponadto firma Microsoft wyłączyła poleceń cmdlet programu PowerShell i interfejsu wiersza polecenia platformy Azure służących do pracy z usługą Azure Storage. Firma Microsoft aktywnie opracowuje te narzędzia z uwzględnieniem wydajności i zapewnia ich aktualność przy użyciu najnowszych wersji usługi i gwarantuje, że obsługa wielu sprawdzonych rozwiązań dotyczących wydajności jest zastosowana wewnętrznie.  

### <a name="retries"></a>Ponowne próby

#### <a name="subheading14"></a>Ograniczenia i błędy zajętości serwera

W niektórych przypadkach usługa magazynu może ograniczać swoją aplikację lub po prostu nie może wykonać żądania z powodu przejściowego warunku i zwrócić komunikat "503 serwer zajęty" lub "500 limit czasu".  Taka sytuacja może wystąpić, jeśli aplikacja zbliża się do któregokolwiek z obiektów docelowych skalowalności lub system ponownie zrównoważy dane partycjonowane w celu zapewnienia większej przepływności.  Aplikacja kliencka powinna zwykle ponowić próbę wykonania operacji, która powoduje wystąpienie tego błędu: próba przeprowadzenia tego samego żądania później może powieść się. Jeśli jednak usługa magazynu ogranicza swoją aplikację, ponieważ przekracza ona elementy docelowe skalowalności, a nawet jeśli usługa nie mogła obsłużyć żądania z jakiegoś innego powodu, agresywne ponowne próby zwykle sprawiają, że problem będzie gorszy. Z tego powodu należy wycofać wartość wykładniczą (biblioteki klienckie domyślnie to zachowanie). Na przykład aplikacja może ponowić próbę po upływie 2 sekund, następnie 4 sekund, następnie 10 sekund, a następnie 30 sekundach, a następnie zadawać całkowicie. W wyniku tego działania aplikacja znacznie zmniejsza obciążenie usługi, a nie zaostrza jakichkolwiek problemów.  

Błędy łączności mogą być podejmowane natychmiast, ponieważ nie są one wynikiem ograniczenia przepustowości i powinny być przejściowe.  

#### <a name="subheading15"></a>Błędy nieponowień

Biblioteki klienckie są świadome tego, których błędów można ponowić, a które nie. Jeśli jednak piszesz własny kod przy użyciu interfejsu API REST usługi Storage, pamiętaj, że niektóre błędy nie powinny być ponawiane: na przykład odpowiedź 400 (złe żądanie) wskazuje, że aplikacja kliencka wysłała żądanie, którego nie można przetworzyć, ponieważ nie było w oczekiwanym formularzu. Ponowne wysłanie tego żądania będzie powodowało taką samą odpowiedź za każdym razem, więc nie ma żadnego punktu na ponowną próbę. Jeśli piszesz własny kod w interfejsie API REST usługi Storage, weź pod uwagę znaczenie kodów błędów i odpowiedni sposób ponawiania prób (lub nie) dla każdego z nich.  

#### <a name="useful-resources"></a>Przydatne zasoby

Aby uzyskać więcej informacji na temat kodów błędów magazynu, zobacz [stan i kody błędów](https://msdn.microsoft.com/library/azure/dd179382.aspx) w witrynie sieci Web Microsoft Azure.  

## <a name="blobs"></a>Obiekty blob

Oprócz sprawdzonych praktyk dla [wszystkich usług](#allservices) opisanych wcześniej, następujące sprawdzone rozwiązania mają zastosowanie w odniesieniu do BLOB Service.  

### <a name="blob-specific-scalability-targets"></a>Tarcze skalowalności specyficzne dla obiektów BLOB

#### <a name="subheading46"></a>Wielu klientów uzyskuje dostęp do pojedynczego obiektu współbieżnie

Jeśli masz dużą liczbę klientów, którzy uzyskują dostęp do pojedynczego obiektu współbieżnie, musisz wziąć pod uwagę elementy docelowe skalowalności dla obiektów i kont magazynu. Dokładna liczba klientów, którzy mogą uzyskać dostęp do pojedynczego obiektu, różni się w zależności od czynników, takich jak liczba klientów żądających obiektu jednocześnie, rozmiar obiektu, warunki sieci itp.

Jeśli obiekt może być dystrybuowany za pośrednictwem sieci CDN, takiego jak obrazy lub wideo obsługiwane przez witrynę sieci Web, należy użyć sieci CDN. Zobacz [tutaj](#subheading5).

W innych scenariuszach, takich jak symulacje naukowe, w przypadku których dane są poufne, są dostępne dwie opcje. Pierwszy polega na rozdzieleniu dostępu do obciążenia w taki sposób, że dostęp do obiektu jest uzyskiwany w określonym czasie, a jednocześnie jest dostępny jednocześnie. Alternatywnie można tymczasowo skopiować obiekt na wiele kont magazynu w taki sposób, aby zwiększyć łączną liczbę operacji we/wy na jeden obiekt i na kontach magazynu. W ograniczonych testach znaleźliśmy, że około 25 maszyn wirtualnych mogło jednocześnie pobrać 100 GB obiektów BLOB (w przypadku każdej maszyny wirtualnej przekształcają pobieranie przy użyciu wątków 32). Jeśli masz 100 klientów potrzebnych do uzyskania dostępu do obiektu, najpierw skopiuj go na drugie konto magazynu, a następnie wybierz pierwsze 50 maszyny wirtualne, które uzyskują dostęp do pierwszego obiektu BLOB, a drugie, 50 maszyny wirtualne uzyskują dostęp do drugiego obiektu BLOB. Wyniki będą się różnić w zależności od zachowania aplikacji, dlatego należy je przetestować podczas projektowania. 

#### <a name="subheading16"></a>Przepustowość i operacje na obiekt BLOB

Można odczytać lub zapisać w pojedynczym obiekcie blob o maksymalnym rozmiarze 60 MB/s (około 480 MB/s, który przekracza możliwości wielu sieci po stronie klienta (w tym fizycznej karty sieciowej na urządzeniu klienckim). Dodatkowo pojedynczy obiekt BLOB obsługuje do 500 żądań na sekundę. Jeśli istnieje wielu klientów, którzy muszą odczytać ten sam obiekt BLOB i można przekroczyć te limity, należy rozważyć użycie sieci CDN do dystrybucji obiektu BLOB.  

Aby uzyskać więcej informacji na temat docelowej przepływności dla obiektów blob, zobacz [cele dotyczące skalowalności i wydajności usługi Azure Storage](storage-scalability-targets.md).  

### <a name="copying-and-moving-blobs"></a>Kopiowanie i przeniesienie obiektów BLOB

#### <a name="subheading17"></a>Kopiuj obiekt BLOB

Interfejs API REST usługi Storage w wersji 2012-02-12 wprowadził przydatną możliwość kopiowania obiektów BLOB między kontami: aplikacja kliencka może nakazać usłudze magazynu skopiowanie obiektu BLOB z innego źródła (prawdopodobnie w innym koncie magazynu), a następnie pozwolić na wykonanie usługi kopia asynchroniczna. Może to znacznie zmniejszyć przepustowość potrzebną dla aplikacji podczas migrowania danych z innych kont magazynu, ponieważ nie jest konieczne pobieranie i przekazywanie danych.  

Jednak w przypadku kopiowania między kontami magazynu nie ma gwarancji czasu, kiedy kopia zostanie ukończona. Jeśli aplikacja musi szybko wykonać kopię obiektu BLOB w kontrolce, lepszym rozwiązaniem może być skopiowanie obiektu BLOB przez pobranie go na maszynę wirtualną, a następnie przekazanie go do miejsca docelowego.  Aby zapewnić pełną przewidywalność w takiej sytuacji, należy się upewnić, że kopia jest wykonywana przez maszynę wirtualną działającą w tym samym regionie świadczenia usługi Azure, lub w przeciwnym razie może to mieć wpływ na wydajność kopiowania.  Ponadto można programowo monitorować postęp kopii asynchronicznej.  

Kopie w ramach samego konta magazynu są zwykle wykonywane szybko.  

Aby uzyskać więcej informacji, zobacz [Kopiuj obiekt BLOB](https://msdn.microsoft.com/library/azure/dd894037.aspx).  

#### <a name="subheading18"></a>Użyj AzCopy

Zespół usługi Azure Storage udostępnił narzędzie wiersza polecenia "AzCopy", które umożliwia zbiorcze Transferowanie wielu obiektów BLOB do, z i między kontami magazynu.  To narzędzie jest zoptymalizowane pod kątem tego scenariusza i może osiągać wysokie szybkości transferu.  Jest to zalecane w przypadku scenariuszy przekazywania zbiorczego, pobierania i kopiowania. Aby dowiedzieć się więcej na ten temat i pobrać, zobacz [transfer danych za pomocą narzędzia wiersza polecenia AzCopy](storage-use-azcopy.md).  

#### <a name="subheading19"></a>Usługa Azure Import/Export

W przypadku dużych ilości danych (więcej niż 1 TB) usługa Azure Storage oferuje usługę Import/Export, która umożliwia przekazywanie i pobieranie z magazynu obiektów BLOB przez wysyłanie dysków twardych.  Możesz umieścić dane na dysku twardym i wysłać je do firmy Microsoft w celu przekazania lub wysłać pusty dysk twardy do firmy Microsoft w celu pobrania danych.  Aby uzyskać więcej informacji, zobacz [Use the Microsoft Azure Import/Export Service to Transfer Data to Blob Storage (Przesyłanie danych do usługi Blob Storage za pomocą usługi Microsoft Azure Import/Export)](../storage-import-export-service.md).  Może to być znacznie bardziej wydajne niż przekazywanie/Pobieranie tej ilości danych przez sieć.  

### <a name="subheading20"></a>Użyj metadanych

Blob service obsługuje żądania główne, które mogą zawierać metadane dotyczące obiektu BLOB. Na przykład jeśli aplikacja wymaga danych EXIF ze zdjęcia, może pobrać zdjęcie i wyodrębnić ją. Aby zaoszczędzić przepustowość i zwiększyć wydajność, aplikacja może przechowywać dane EXIF w metadanych obiektu BLOB, gdy aplikacja przekazała Zdjęcie: można następnie pobrać dane EXIF w metadanych przy użyciu tylko żądania elementu głównego, co oszczędza znaczną przepustowość i czas przetwarzania wymagany do wyodrębnienia danych EXIF za każdym razem, gdy obiekt BLOB zostanie odczytany. Jest to przydatne w scenariuszach, w których potrzebne są tylko metadane, a nie Pełna zawartość obiektu BLOB.  Na obiekt BLOB mogą być przechowywane tylko 8 KB metadanych (usługa nie akceptuje żądania przechowania więcej niż tej), więc jeśli dane nie mieszczą się w tym rozmiarze, korzystanie z tego podejścia może być niemożliwe.  

### <a name="rapid-uploading"></a>Szybkie przekazywanie

Aby szybko przekazać obiekty blob, najpierw należy odpowiedzieć na pytanie: czy są przekazywane jeden obiekt BLOB czy wiele?  Skorzystaj z poniższych wskazówek, aby określić poprawną metodę do użycia w zależności od danego scenariusza.  

#### <a name="subheading21"></a>Szybkie przekazywanie jednego dużego obiektu BLOB

Aby szybko przekazać pojedynczy duży obiekt BLOB, aplikacja kliencka powinna przekazywać równolegle swoje bloki lub strony (co jest świadome obiektów docelowych skalowalności dla poszczególnych obiektów blob i konta magazynu jako całości).  Oficjalne biblioteki klienckie magazynu RTM firmy Microsoft (.NET, Java) mają tę możliwość.  Dla każdej biblioteki Użyj poniższego określonego obiektu/właściwości, aby ustawić poziom współbieżności:  

* .NET: Ustaw ParallelOperationThreadCount dla obiektu BlobRequestOptions, który ma być używany.
* Java/Android: Use BlobRequestOptions.setConcurrentRequestCount()
* Node.js: Użyj parallelOperationThreadCount na temat opcji żądania lub Blob service.
* C++: Użyj metody blob_request_options:: set_parallelism_factor.

#### <a name="subheading22"></a>Szybkie przekazywanie wielu obiektów BLOB

Aby szybko przekazać wiele obiektów blob, należy przekazać obiekty blob równolegle. Jest to szybsze niż przekazywanie pojedynczych obiektów BLOB jednocześnie przy użyciu równoległych bloków przekazywania, ponieważ rozkłada je na wiele partycji usługi Storage. Pojedynczy obiekt BLOB obsługuje tylko przepływność wynoszącą 60 MB/s (około 480 MB/s). W chwili pisania konto LRS oparte na USA obsługuje do 20 GB/s danych przychodzących, które jest znacznie większe niż przepływność obsługiwana przez pojedynczy obiekt BLOB.  [AzCopy](#subheading18) domyślnie wykonuje operacje przekazywania równolegle i jest to zalecane w tym scenariuszu.  

### <a name="subheading23"></a>Wybieranie poprawnego typu obiektu BLOB

Usługa Azure Storage obsługuje dwa typy obiektów BLOB ** : stronicowe obiekty blob i *blokowe* obiekty blob. Dla danego scenariusza użycia wybór typu obiektu BLOB będzie miał wpływ na wydajność i skalowalność rozwiązania. Blokowe obiekty blob są odpowiednie, gdy chcesz efektywnie przekazywać duże ilości danych: na przykład aplikacja kliencka może wymagać przeładowania zdjęć lub wideo do usługi BLOB Storage. Stronicowe obiekty blob są odpowiednie, jeśli aplikacja musi wykonywać losowe operacje zapisu na danych: na przykład dyski VHD platformy Azure są przechowywane jako stronicowe obiekty blob.  

Aby uzyskać więcej informacji, zobacz [Omówienie blokowych obiektów blob, dołączanie obiektów blob i stronicowych obiektów BLOB](https://msdn.microsoft.com/library/azure/ee691964.aspx).  

## <a name="tables"></a>Tabele

Oprócz sprawdzonych praktyk dla [wszystkich usług](#allservices) opisanych wcześniej, następujące sprawdzone rozwiązania mają zastosowanie w odniesieniu do usługi Table Service.  

### <a name="subheading24"></a>Cele skalowalności specyficzne dla tabeli

Oprócz ograniczeń przepustowości całego konta magazynu tabele mają następujący określony limit skalowalności.  System będzie równoważyć obciążenie w miarę wzrostu ruchu, ale jeśli ruch ma nagłe rozerwania, nie można natychmiast uzyskać tej ilości przepływności.  Jeśli Twój wzorzec zawiera szeregi, należy oczekiwać, że ograniczanie i/lub przekroczenie limitu czasu podczas serii, ponieważ usługa magazynu automatycznie równoważy obciążenie tabeli.  Spowolnienie zwiększają się, ponieważ zapewnia czas systemowy odpowiednio do równoważenia obciążenia.  

#### <a name="entities-per-second-storage-account"></a>Jednostki na sekundę (konto magazynu)

Limit skalowalności dla dostępu do tabel to 20 000 jednostek (1 KB każdej) na sekundę dla konta.  Ogólnie rzecz biorąc, każda jednostka, która jest wstawiana, aktualizowana, usuwana lub skanowana, jest uwzględniana w tym miejscu docelowym.  Dzięki temu wstawka wsadowa zawierająca jednostki 100 będzie liczyć jako jednostki 100.  Zapytanie, które skanuje jednostki 1000 i zwraca wartość 5, będzie liczyć jako jednostki 1000.  

#### <a name="entities-per-second-partition"></a>Jednostki na sekundę (partycja)

W ramach jednej partycji obiekt docelowy skalowalności do uzyskiwania dostępu do tabel to 2 000 jednostek (1 KB każdej) na sekundę przy użyciu tego samego zliczania, jak opisano w poprzedniej sekcji.  

### <a name="configuration"></a>Konfigurowanie

Ta sekcja zawiera kilka ustawień szybkiego konfigurowania, których można użyć, aby zwiększyć wydajność usługi Table Service:  

#### <a name="subheading25"></a>Użyj JSON

Począwszy od usługi Storage w wersji 2013-08-15, usługa Table Service obsługuje używanie kodu JSON zamiast formatu AtomPub opartego na języku XML do przesyłania danych tabeli. Może to zmniejszyć rozmiar ładunku o nawet 75% i znacząco zwiększyć wydajność aplikacji.

Aby uzyskać więcej informacji, Zobacz tabele [Microsoft Azure publikowania: Wprowadzenie do](https://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/05/windows-azure-tables-introducing-json.aspx) formatu JSON i [ładunku dla operacji usługi Table Service](https://msdn.microsoft.com/library/azure/dn535600.aspx).

#### <a name="subheading26"></a>Wyłącz nagle

Algorytm nagle jest szeroko implementowany w sieciach TCP/IP jako środek w celu zwiększenia wydajności sieci. Nie jest to jednak optymalne we wszystkich sytuacjach (takich jak wysoce interaktywne środowiska). W przypadku usługi Azure Storage algorytm nagle ma negatywny wpływ na wydajność żądań do usług tabel i kolejek i należy go wyłączyć, jeśli jest to możliwe.

### <a name="schema"></a>Schemat

Sposób reprezentowania danych i wykonywania w nim zapytań jest największym czynnikiem wpływającym na wydajność usługi Table Service. Chociaż każda aplikacja jest różna, w tej sekcji opisano niektóre ogólne sprawdzone rozwiązania, które odnoszą się do:  

* Projektowanie tabel
* Wydajne zapytania
* Wydajne aktualizacje danych  

#### <a name="subheading27"></a>Tabele i partycje

Tabele są podzielone na partycje. Każda jednostka przechowywana na partycji ma ten sam klucz partycji i ma unikatowy klucz wiersza, aby zidentyfikować ją w ramach tej partycji. Partycje zapewniają korzyści, ale również wprowadzają limity skalowalności.  

* Korzyści: Jednostki można aktualizować w jednej, niepodzielnej transakcji wsadowej zawierającej do 100 oddzielnych operacji magazynowania (limit rozmiaru o rozmiarze 4 MB). Przy założeniu, że ta sama liczba jednostek ma być pobierana, można również wykonywać zapytania dotyczące danych w pojedynczej partycji bardziej wydajniej niż dane, które obejmują partycje (należy przeczytać, aby uzyskać dalsze zalecenia dotyczące wykonywania zapytań dotyczących danych tabeli).
* Limit skalowalności: Dostęp do jednostek przechowywanych w pojedynczej partycji nie może być zrównoważony pod względem obciążenia, ponieważ partycje obsługują niepodzielną transakcję wsadową. Z tego powodu cel skalowalności dla pojedynczej partycji tabeli jest niższy niż w przypadku usługi Table Service jako całości.  

Ze względu na te cechy tabel i partycji należy przyjąć następujące zasady dotyczące projektowania:  

* Dane, które aplikacja kliencka często aktualizowana lub zapytania w tej samej jednostce logicznej pracy, powinny znajdować się w tej samej partycji.  Może to być spowodowane tym, że aplikacja agreguje zapisy lub że chcesz wykorzystać niepodzielną operację wsadową.  Ponadto dane w pojedynczej partycji mogą być bardziej wydajne w pojedynczej kwerendzie niż dane między partycjami.
* Dane, które aplikacja kliencka nie wstawia/aktualizuje ani nie wykonują zapytania w tej samej logicznej jednostce pracy (pojedyncze zapytanie lub aktualizacja wsadowa), powinny znajdować się w oddzielnych partycjach.  Jedna ważna Uwaga polega na tym, że nie ma żadnych ograniczeń dotyczących liczby kluczy partycji w pojedynczej tabeli, tak aby miliony kluczy partycji nie było problemem i nie wpłynie to na wydajność.  Na przykład jeśli aplikacja jest popularną witryną sieci Web z logowaniem użytkownika, użycie identyfikatora użytkownika jako klucza partycji może być dobrym wyborem.  

#### <a name="hot-partitions"></a>Partycje aktywne

Partycja gorąca to taka, która otrzymuje nieproporcjonalny odsetek ruchu do konta i nie może być zrównoważona pod względem obciążenia, ponieważ jest jedną partycją.  Ogólnie rzecz biorąc, partycje gorące są tworzone jeden z dwóch sposobów:  

##### <a name="subheading28"></a>Tylko Dołącz wzorce i tylko poprzedź

Wzorzec "Append Only" to jeden tam, gdzie wszystkie (lub niemal wszystkie) ruchu do danego klucza podstawowego rosną i zmniejszają się w zależności od bieżącego czasu.  Przykładem jest to, że aplikacja użyła bieżącej daty jako klucza partycji dla danych dziennika.  Powoduje to wszystkie operacje wstawiania do ostatniej partycji w tabeli, a system nie może równoważyć obciążenia, ponieważ wszystkie zapisy przechodzą do końca tabeli.  Jeśli ilość ruchu do tej partycji przekracza miejsce docelowe skalowalności na poziomie partycji, spowoduje to ograniczenie przepustowości.  Lepiej jest zapewnić, że ruch jest wysyłany do wielu partycji, aby umożliwić Równoważenie obciążenia żądaniami w tabeli.  

##### <a name="subheading29"></a>Dane o dużym natężeniu ruchu

Jeśli schemat partycjonowania składa się z pojedynczej partycji, która ma tylko dane, które są znacznie bardziej używane niż inne partycje, można także sprawdzić ograniczenia, ponieważ ta partycja zbliża się do celu skalowalności dla jednej partycji.  Lepszym rozwiązaniem jest upewnienie się, że schemat partycji nie ma żadnej pojedynczej partycji, która zbliża się do celów skalowalności.  

#### <a name="querying"></a>Wykonywanie zapytania

W tej sekcji opisano sprawdzone praktyki dotyczące wykonywania zapytań dotyczących usługi Table Service.  

##### <a name="subheading30"></a>Zakres zapytania

Istnieje kilka sposobów określania zakresu jednostek do zapytania.  Poniżej przedstawiono omówienie użycia każdego z nich.  

Ogólnie rzecz biorąc, unikaj skanowania (zapytania większe niż pojedyncza jednostka), ale jeśli musisz skanować, spróbuj zorganizować dane tak, aby skany pobierają potrzebne dane bez skanowania lub zwracania znaczących niepotrzebnych jednostek.  

###### <a name="point-queries"></a>Zapytania punktu

Zapytanie o punkt pobiera dokładnie jedną jednostkę. W tym celu należy określić klucz partycji i klucz wiersza jednostki do pobrania. Te zapytania są wydajne i należy ich używać wszędzie tam, gdzie jest to możliwe.  

###### <a name="partition-queries"></a>Zapytania dotyczące partycji

Zapytanie partycji to zapytanie, które pobiera zestaw danych, które współużytkują wspólny klucz partycji. Zwykle zapytanie określa zakres wartości klucza wiersza lub zakres wartości dla niektórych właściwości jednostki oprócz klucza partycji. Są one mniej wydajne niż zapytania punktowe i powinny być używane oszczędnie.  

###### <a name="table-queries"></a>Zapytania tabeli

Zapytanie tabeli to zapytanie, które pobiera zestaw jednostek, które nie korzystają ze wspólnego klucza partycji. Te zapytania są niewydajne i należy je unikać, jeśli jest to możliwe.  

##### <a name="subheading31"></a>Gęstość zapytania

Drugim czynnikiem wydajności zapytania jest liczba jednostek zwróconych w porównaniu do liczby przeskanowanych jednostek w celu znalezienia zwracanego zestawu. Jeśli aplikacja wykonuje zapytanie tabeli z filtrem dla wartości właściwości, która ma tylko 1% udziałów danych, zapytanie przeskanuje jednostki 100 dla każdej jednostki, która zwraca. Obiekty docelowe skalowalności tabeli omawiane poprzednio odnoszą się do liczby przeskanowanych jednostek, a nie liczby zwróconych jednostek: niska gęstość zapytania może łatwo spowodować ograniczenie aplikacji do usługi Table Service, ponieważ musi ona skanować wiele jednostek do Pobierz szukaną jednostkę.  Więcej informacji na temat tego [](#subheading34) , jak można uniknąć, można znaleźć w sekcji poniżej.  

##### <a name="limiting-the-amount-of-data-returned"></a>Ograniczanie ilości zwracanych danych

###### <a name="subheading32"></a>Identyfikatorów

Jeśli wiesz, że zapytanie zwróci jednostki, które nie są potrzebne w aplikacji klienckiej, rozważ użycie filtru, aby zmniejszyć rozmiar zwracanego zestawu. Mimo że jednostki, które nie są zwracane do klienta, są nadal wliczane do ograniczeń skalowalności, wydajność aplikacji poprawi się z powodu zmniejszonego rozmiaru ładunku sieciowego i zredukowanej liczby jednostek, które aplikacja kliencka musi przetworzyć.  Zapoznaj się z powyższymi uwagami dotyczącymi [gęstości zapytań](#subheading31), jednak elementy docelowe skalowalności odnoszą się do liczby przeskanowanych jednostek, więc zapytanie filtrujące wiele jednostek może nadal powodować ograniczenie przepustowości, nawet jeśli zwracane są kilka jednostek.  

###### <a name="subheading33"></a>Stając

Jeśli aplikacja kliencka wymaga tylko ograniczonego zestawu właściwości z jednostek w tabeli, można użyć projekcji, aby ograniczyć rozmiar zwracanego zestawu danych. Podobnie jak w przypadku filtrowania, pozwala to zmniejszyć obciążenie sieci i przetwarzanie klienta.  

##### <a name="subheading34"></a>Denormalizacja

W przeciwieństwie do pracy z relacyjnymi bazami danych, sprawdzone rozwiązania do wydajnego wykonywania zapytań o dane tabeli prowadzą do denormalizacji danych. Oznacza to, że duplikowanie tych samych danych w wielu jednostkach (po jednym dla każdego klucza, którego można użyć do znajdowania danych) w celu zminimalizowania liczby jednostek, które muszą zostać przeskanowane przez zapytanie w celu znalezienia danych potrzebnych przez klienta, a nie konieczności skanowania dużej liczby jednostek w celu znalezienia danych aplikacji likacji.  Na przykład w witrynie internetowej handlu elektronicznego możesz chcieć znaleźć zamówienie zarówno według identyfikatora klienta, jak i daty (podać zamówienia tego klienta), a w dniu  W Table Storage najlepiej przechowywać jednostkę (lub odwołanie do niej) dwa razy — raz z nazwą tabeli, PK i RK, aby ułatwić znajdowanie według identyfikatora klienta, co ułatwia znalezienie go w dniu.  

#### <a name="insertupdatedelete"></a>Wstaw/Aktualizuj/Usuń

W tej sekcji opisano sprawdzone rozwiązania dotyczące modyfikowania jednostek przechowywanych w usłudze Table Service.  

##### <a name="subheading35"></a>Partie

Transakcje usługi Batch są nazywane transakcjami grupy jednostek (ETG) w usłudze Azure Storage. wszystkie operacje w ramach elementu ETG muszą znajdować się na jednej partycji w pojedynczej tabeli. Tam, gdzie to możliwe, użyj ETGs do wykonywania operacji wstawiania, aktualizacji i usuwania w partiach. Zmniejsza to liczbę operacji rundy z aplikacji klienckiej do serwera programu, ograniczając liczbę transakcji rozliczanych (ETG jako pojedynczą transakcję na potrzeby rozliczeń i może zawierać do 100) i włącza aktualizacje niepodzielne (wszystko operacje zakończone powodzeniem lub zakończone niepowodzeniem w ramach elementu ETG). Środowiska z dużymi opóźnieniami, takimi jak urządzenia przenośne, znacznie pomogą korzystać z usługi ETGs.  

##### <a name="subheading36"></a>Upsert

W miarę możliwości używaj tabeli **upsert** . Istnieją dwa typy **upsert**, które mogą być bardziej wydajne niż tradycyjne operacje **wstawiania** i **aktualizacji** :  

* **InsertOrMerge**: Użyj tego, jeśli chcesz przekazać podzestaw właściwości jednostki, ale nie ma pewności, czy jednostka już istnieje. Jeśli jednostka istnieje, to wywołanie aktualizuje właściwości zawarte w operacji **upsert** i pozostawia wszystkie istniejące właściwości, jeśli są one, jeśli jednostka nie istnieje, wstawia nową jednostkę. Jest to podobne do użycia projekcji w zapytaniu, w którym należy jedynie przekazać właściwości, które są zmieniane.
* **InsertOrReplace**: Użyj tego, jeśli chcesz przekazać całkowicie nową jednostkę, ale nie masz pewności, czy już istnieje. Tej funkcji należy używać tylko wtedy, gdy wiadomo, że nowo przekazana jednostka jest całkowicie poprawna, ponieważ całkowicie zastępuje starą jednostkę. Na przykład, chcesz zaktualizować jednostkę, która przechowuje bieżącą lokalizację użytkownika, niezależnie od tego, czy aplikacja ma poprzednio przechowywane dane lokalizacji dla użytkownika; Nowa jednostka lokalizacji została ukończona i nie są potrzebne żadne informacje z poprzedniej jednostki.

##### <a name="subheading37"></a>Przechowywanie serii danych w jednej jednostce

Czasami aplikacja przechowuje serie danych, które często mają być pobierane jednocześnie: na przykład aplikacja może śledzić użycie procesora w czasie w celu wykreślania przenoszonego wykresu danych z ostatnich 24 godzin. Jednym z metod jest posiadanie jednej jednostki tabeli na godzinę, dla każdej jednostki reprezentującej określoną godzinę i przechowywanie użycia procesora CPU przez daną godzinę. Aby wykreślić te dane, aplikacja musi pobrać jednostki przechowujące dane z 24 ostatnich godzin.  

Alternatywnie, aplikacja może przechowywać użycie procesora CPU przez każdą godzinę jako oddzielną Właściwość pojedynczej jednostki: Aby zaktualizować każdą godzinę, aplikacja może użyć pojedynczego wywołania **InsertOrMerge upsert** , aby zaktualizować wartość dla ostatniej godziny. Aby wykreślić dane, aplikacja musi pobrać tylko jedną jednostkę zamiast 24, co pozwala na wydajne zapytanie (patrz powyżej w omówieniu [zakresu zapytania](#subheading30)).

##### <a name="subheading38"></a>Przechowywanie danych strukturalnych w obiektach Blob

Czasami dane strukturalne uważają się za takie jak w tabelach, ale zakresy jednostek są zawsze pobierane razem i można dodać partię.  Dobrym przykładem jest plik dziennika.  W takim przypadku można wykonać wsadowe kilka minut dzienników, wstawić je, a następnie zawsze pobierać kilka minut dzienników jednocześnie.  W tym przypadku, w przypadku wydajności, lepiej jest używać obiektów BLOB zamiast tabel, ponieważ można znacząco ograniczyć liczbę obiektów, które są zapisywane/zwracane, a także zazwyczaj liczbę żądań, które są potrzebne.  

## <a name="queues"></a>Kolejki

Oprócz sprawdzonych praktyk dla [wszystkich usług](#allservices) opisanych wcześniej, następujące sprawdzone rozwiązania mają zastosowanie w odniesieniu do usługi kolejki.  

### <a name="subheading39"></a>Limity skalowalności

Pojedyncza Kolejka może przetwarzać około 2 000 komunikatów (1 KB każdej) na sekundę (każdy AddMessage, GetMessage i DeleteMessage Count jako komunikat). Jeśli jest to niewystarczające dla aplikacji, należy użyć wielu kolejek i rozłożyć komunikaty między nimi.  

Wyświetlaj bieżące elementy docelowe skalowalności w zakresie [skalowalności i wydajności usługi Azure Storage](storage-scalability-targets.md).  

### <a name="subheading40"></a>Wyłącz nagle

Zapoznaj się z sekcją w sekcji Konfiguracja tabeli, która omawia algorytm nagle — algorytm nagle jest zwykle nieodpowiedni dla wydajności żądań kolejki i należy go wyłączyć.  

### <a name="subheading41"></a>Rozmiar komunikatu

Wydajność i skalowalność kolejki zmniejsza się w miarę wzrostu rozmiaru wiadomości. W komunikacie należy umieścić tylko te informacje, których potrzebuje odbiornik.  

### <a name="subheading42"></a>Pobieranie wsadowe

Można pobrać maksymalnie 32 komunikatów z kolejki w ramach jednej operacji. Może to zmniejszyć liczbę roundtrip z aplikacji klienckiej, która jest szczególnie przydatna w środowiskach, takich jak urządzenia przenośne, z dużym opóźnieniem.  

### <a name="subheading43"></a>Interwał sondowania kolejki

Większość aplikacji sonduje komunikaty z kolejki, co może być jednym z największych źródeł transakcji dla danej aplikacji. Wielokrotnie wybieraj interwał sondowania: sondowanie zbyt często może spowodować, że aplikacja zbliża się do celów skalowalności dla kolejki. Jednak w przypadku 200 000 transakcji dla $0,01 (w momencie pisania) pojedynczy procesor sondowanie co sekundę przez miesiąc ma koszt mniejszy niż 15 centów, dzięki czemu koszt nie jest zwykle czynnikiem wpływającym na wybór interwału sondowania.  

Aby uzyskać aktualne informacje o kosztach, zobacz [Cennik usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage/).  

### <a name="subheading44"></a>Aktualizuj komunikat

Za pomocą operacji **Aktualizuj komunikat** można zwiększyć limit czasu niewidoczności lub zaktualizować informacje o stanie wiadomości. Chociaż jest to wydajne, należy pamiętać, że każda operacja **aktualizacji komunikatów** jest liczona względem celu skalowalności. Jednak może to być znacznie bardziej wydajne podejście niż posiadanie przepływu pracy, który przekazuje zadanie z jednej kolejki do następnego, ponieważ każdy krok zadania zostanie ukończony. Przy użyciu operacji **Aktualizuj komunikat** zezwala aplikacji na zapisanie stanu zadania w komunikacie, a następnie kontynuowanie pracy, zamiast umieszczania w kolejce komunikatu dla następnego kroku zadania za każdym razem, gdy krok zostanie ukończony.  

Aby uzyskać więcej informacji, zobacz artykuł [How to: Zmień zawartość komunikatu](../queues/storage-dotnet-how-to-use-queues.md#change-the-contents-of-a-queued-message)znajdującego się w kolejce.  

### <a name="subheading45"></a>Architektura aplikacji

Aby architektura aplikacji była skalowalna, należy użyć kolejek. Poniżej wymieniono niektóre sposoby używania kolejek w celu zapewnienia bardziej skalowalności aplikacji:  

* Za pomocą kolejek można tworzyć zaległości służbowe do przetwarzania i wygładzania obciążeń w aplikacji. Na przykład można kolejkować żądania od użytkowników w celu wykonywania dużej ilości pracy procesora, takich jak zmienianie rozmiarów przekazanych obrazów.
* Za pomocą kolejek można rozdzielić części aplikacji, aby umożliwić ich niezależne skalowanie. Na przykład fronton sieci Web może umieścić wyniki ankiety od użytkowników w kolejce na potrzeby późniejszej analizy i magazynu. Można dodać więcej wystąpień roli procesu roboczego, aby przetworzyć dane kolejki zgodnie z wymaganiami.  

## <a name="conclusion"></a>Wniosek

W tym artykule omówiono niektóre z najpopularniejszych, sprawdzonych rozwiązań dotyczących optymalizacji wydajności podczas korzystania z usługi Azure Storage. Zachęcamy każdego dewelopera aplikacji do oceny swoich aplikacji pod kątem powyższych rozwiązań oraz do postępowania zgodnie z zaleceniami mającymi na celu uzyskanie optymalnej wydajności aplikacji korzystających z usługi Azure Storage.
