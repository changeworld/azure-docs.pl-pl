---
title: Usługa Azure Storage wydajność i skalowalność Lista kontrolna | Dokumentacja firmy Microsoft
description: Lista kontrolna sprawdzonych rozwiązań dotyczących użycia z usługą Azure Storage w tworzenie wydajnych aplikacji.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 12/08/2016
ms.author: rogarana
ms.subservice: common
ms.openlocfilehash: b8451a1195ab64d3cd7afda074d786a3209ce785
ms.sourcegitcommit: ad3e63af10cd2b24bf4ebb9cc630b998290af467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2019
ms.locfileid: "58793972"
---
# <a name="microsoft-azure-storage-performance-and-scalability-checklist"></a>Lista kontrolna dotycząca wydajności i skalowalności usługi Microsoft Azure Storage
## <a name="overview"></a>Przegląd
Po wydaniu usługi Microsoft Azure Storage firma Microsoft opracowała wiele sprawdzone rozwiązania dotyczące korzystania z tych usług w sposób wydajny, a ten artykuł służy do konsolidowania najważniejsze z nich do listy styl listy kontrolnej. Części tego artykułu jest pomoc deweloperom aplikacji, sprawdź, czy używasz sprawdzone rozwiązania z usługą Azure Storage i pomoc w identyfikacji innych sprawdzone rozwiązania, należy rozważyć przyjęcie. W tym artykule nie jest podejmowana próba obejmują co możliwości optymalizacji wydajności i skalowalności — wyklucza te, które są małe w ich wpływ lub nie szerokie zastosowanie. W zakresie, w jakim można przewidzieć zachowania aplikacji podczas projektowania, warto zachować te pamiętać na wczesnym etapie, aby uniknąć projekty, które będą uruchamiane w problemy z wydajnością.  

Każdy deweloper aplikacji przy użyciu usługi Azure Storage powinien trwać przeczytaj ten artykuł, i sprawdź jej aplikacji następuje każdego sprawdzonych rozwiązań wymienione poniżej.  

## <a name="checklist"></a>Lista kontrolna
W tym artykule organizuje sprawdzonych rozwiązań w następujących grupach. Sprawdzone rozwiązania mające zastosowanie do:  

* Wszystkie usługi Azure Storage (obiekty BLOB, tabel, kolejek i plików)
* Obiekty blob
* Tabele
* Kolejki  

| Gotowe | Obszar | Kategoria | Pytanie |
| --- | --- | --- | --- |
| &nbsp; | Wszystkie usługi |Cele skalowalności |[Aplikacja zaprojektowano w celu uniknięcia zbliża się cele skalowalności?](#subheading1) |
| &nbsp; | Wszystkie usługi |Cele skalowalności |[Konwencja nazewnictwa zaprojektowana w celu umożliwienia lepszego równoważenia obciążenia?](#subheading47) |
| &nbsp; | Wszystkie usługi |Networking |[Urządzenia po stronie klienta mają wystarczająco dużej przepustowości i małych opóźnień w celu osiągnięcia wydajności potrzebne?](#subheading2) |
| &nbsp; | Wszystkie usługi |Networking |[Czy urządzenia po stronie klienta mają łącze wystarczająco wysokiej jakości?](#subheading3) |
| &nbsp; | Wszystkie usługi |Networking |[Aplikacja klienta znajduje się "w pobliżu" konto magazynu?](#subheading4) |
| &nbsp; | Wszystkie usługi |Dystrybucja zawartości |[Czy używasz usługi CDN do dystrybucji zawartości?](#subheading5) |
| &nbsp; | Wszystkie usługi |Bezpośredni dostęp do klienta |[Czy używasz mechanizmu CORS i sygnaturę dostępu Współdzielonego umożliwia bezpośredni dostęp do magazynu, a nie serwera proxy?](#subheading6) |
| &nbsp; | Wszystkie usługi |Buforowanie |[Uzyskuje się rzadko swojej pamięci podręcznej danych aplikacji, które jest często używana i zmiany?](#subheading7) |
| &nbsp; | Wszystkie usługi |Buforowanie |[Aplikacja jest przetwarzanie wsadowe aktualizacji (buforowanie ich po stronie klienta, a następnie przekazywanie w większych zestawów)?](#subheading8) |
| &nbsp; | Wszystkie usługi |Konfiguracja platformy .NET |[Czy skonfigurowano klientowi korzystanie z wystarczającą liczbą równoczesnych połączeń?](#subheading9) |
| &nbsp; | Wszystkie usługi |Konfiguracja platformy .NET |[Czy skonfigurowano .NET do użycia z wystarczającą liczbą wątków?](#subheading10) |
| &nbsp; | Wszystkie usługi |Konfiguracja platformy .NET |[Czy używasz platformy .NET 4.5 lub nowszej, który poprawiła się wyrzucanie elementów bezużytecznych?](#subheading11) |
| &nbsp; | Wszystkie usługi |Równoległość |[Mają zapewniony, równoległości jest odpowiednio ograniczone tak, aby nie przeciążają swoje zdolności klienta lub cele skalowalności?](#subheading12) |
| &nbsp; | Wszystkie usługi |Narzędzia |[Możesz korzystać z najnowszej wersji programu Microsoft znajdują się narzędzia i biblioteki klienckie?](#subheading13) |
| &nbsp; | Wszystkie usługi |Ponowne próby |[Czy przy użyciu wykładniczego wycofywania zasad ograniczania błędów i przekroczeń limitu czasu ponawiania?](#subheading14) |
| &nbsp; | Wszystkie usługi |Ponowne próby |[Jest unikanie ponownych prób Twojej aplikacji bez możliwości ponowienia próby błędów?](#subheading15) |
| &nbsp; | Obiekty blob |Cele skalowalności |[Czy masz dużą liczbę klientów uzyskujących dostęp do pojedynczego obiektu jednocześnie?](#subheading46) |
| &nbsp; | Obiekty blob |Cele skalowalności |[Aplikacja przebywa w tę docelową skalowalność przepustowości lub operacji dla pojedynczego obiektu blob?](#subheading16) |
| &nbsp; | Obiekty blob |Kopiowanie obiektów blob |[Kopiowanie blob znajdują się w sposób efektywny?](#subheading17) |
| &nbsp; | Obiekty blob |Kopiowanie obiektów blob |[Czy używasz narzędzia AzCopy do zbiorczego kopiowania obiektów blob?](#subheading18) |
| &nbsp; | Obiekty blob |Kopiowanie obiektów blob |[Czy używasz usługi Azure Import/Export do przesyłania dużych ilości danych?](#subheading19) |
| &nbsp; | Obiekty blob |Use Metadata |[Czy przechowywania często używanych metadane dotyczące obiektów blob w ich metadanych?](#subheading20) |
| &nbsp; | Obiekty blob |Szybkie przekazywanie |[Podczas próby szybko przekazać jeden obiekt blob, przekazujesz bloków w sposób równoległy?](#subheading21) |
| &nbsp; | Obiekty blob |Szybkie przekazywanie |[Podczas próby szybko przekazać wiele obiektów blob, przekazujesz obiektów blob w sposób równoległy?](#subheading22) |
| &nbsp; | Obiekty blob |Popraw typ obiektu Blob |[Czy używasz stronicowych obiektów blob lub blokowych obiektów blob, jeśli jest potrzebne?](#subheading23) |
| &nbsp; | Tabele |Cele skalowalności |[Czy Zbliżasz cele skalowalności dla jednostek na sekundę](#subheading24) |
| &nbsp; | Tabele |Konfigurowanie |[Czy używasz JSON dla żądań tabeli?](#subheading25) |
| &nbsp; | Tabele |Konfigurowanie |[Możesz wyłączyć Nagle'a zwiększa wydajność żądań o małym rozmiarze?](#subheading26) |
| &nbsp; | Tabele |Tabele i partycje |[Masz można prawidłowo podzielona na partycje dane?](#subheading27) |
| &nbsp; | Tabele |Partycje w warstwie gorąca |[Jest unikanie wzorców tylko do dołączania i tylko dołączana?](#subheading28) |
| &nbsp; | Tabele |Partycje w warstwie gorąca |[Czy operacje wstawiania/aktualizacji są dystrybuowane w wielu partycjach?](#subheading29) |
| &nbsp; | Tabele |Zakres kwerendy |[Zostały tak zaprojektowane schematu do obsługi zapytań o punkt ma być używany w większości przypadków i zapytania tabeli należy używać oszczędnie?](#subheading30) |
| &nbsp; | Tabele |Gęstość zapytania |[Czy skanowania zazwyczaj tylko zapytania i zwraca wiersze, które Twoja aplikacja będzie używać?](#subheading31) |
| &nbsp; | Tabele |Ograniczenie zwrócone dane |[Czy używasz filtrowania w celu uniknięcia zwracanie jednostki, które nie są potrzebne?](#subheading32) |
| &nbsp; | Tabele |Ograniczenie zwrócone dane |[Czy używasz projekcji w celu uniknięcia zwracanie właściwości, które nie są potrzebne?](#subheading33) |
| &nbsp; | Tabele |Denormalizacja |[Twoje dane mają nieznormalizowane tak, aby uniknąć nieefektywne zapytania lub wielu żądań odczytu podczas próby pobrania danych?](#subheading34) |
| &nbsp; | Tabele |Wstawiania/aktualizowania/usuwania |[Czy możesz dzielenia na partie żądania, które muszą być transakcyjne i może odbywać się w tym samym czasie, aby zmniejszyć rund?](#subheading35) |
| &nbsp; | Tabele |Wstawiania/aktualizowania/usuwania |[Jest unikanie pobierania jednostki tylko w celu ustalenia, czy należy wywołać, insert nebo update?](#subheading36) |
| &nbsp; | Tabele |Wstawiania/aktualizowania/usuwania |[Bierzesz pod uwagę przechowywanie serii danych, które często mają zostać pobrane ze sobą w pojedynczą jednostkę jako właściwości zamiast wielu jednostek?](#subheading37) |
| &nbsp; | Tabele |Wstawiania/aktualizowania/usuwania |[Dla jednostek, które będą zawsze pobierane ze sobą i mogą być zapisywane w partiach (na przykład, dane szeregów czasowych) bierzesz pod uwagę przy użyciu obiektów blob zamiast tabel?](#subheading38) |
| &nbsp; | Kolejki |Cele skalowalności |[Czy Zbliżasz cele skalowalności dla komunikatów na sekundę](#subheading39) |
| &nbsp; | Kolejki |Konfigurowanie |[Możesz wyłączyć Nagle'a zwiększa wydajność żądań o małym rozmiarze?](#subheading40) |
| &nbsp; | Kolejki |Rozmiar komunikatu |[Czy compact Twojej wiadomości zwiększa wydajność kolejki?](#subheading41) |
| &nbsp; | Kolejki |Zbiorcze pobieranie |[Czy pobieranie wielu komunikatów w ramach jednej operacji "Pobierz"?](#subheading42) |
| &nbsp; | Kolejki |Częstotliwość sondowania |[Są możesz sondowania wystarczająco często, aby zmniejszyć opóźnienie postrzegany aplikacji?](#subheading43) |
| &nbsp; | Kolejki |Aktualizacja wiadomości |[Czy używasz UpdateMessage do przechowywania postęp w przetwarzanie komunikatów, unikając konieczności Przetwarzaj ponownie cały komunikat, jeśli wystąpi błąd?](#subheading44) |
| &nbsp; | Kolejki |Architektura |[Czy używasz kolejek na całej aplikacji bardziej skalowalna przechowując obciążeń długo działających poza ścieżki krytycznej i niezależne skalowanie, następnie?](#subheading45) |

## <a name="allservices"></a>Wszystkie usługi
W tej sekcji przedstawiono sprawdzone rozwiązania, które mają zastosowanie do użycia usług Azure Storage (obiekty BLOB, tabel, kolejek lub plików).  

### <a name="subheading1"></a>Cele skalowalności
Usługa Azure Storage, sama ma ograniczenie do 250 kont magazynu na region na subskrypcję. W przypadku osiągnięcia tego limitu nie można utworzyć kolejnych kont magazynu w tej kombinacji subskrypcji/regionu.

Każda z usług Azure Storage ma wartości docelowe skalowalności pojemność (GB), stawka za transakcje i przepustowość. Jeśli aplikacja zbliża się lub przekroczy cele skalowalności, może to wystąpić opóźnienia zwiększone transakcji lub ograniczenia przepustowości. Gdy Usługa magazynu ogranicza twoją aplikację, usługi rozpoczyna się do zwrócenia "503 Serwer jest zajęty" lub "500 limit czasu operacji" kodami błędów niektóre transakcje magazynu. W tej sekcji omówiono zarówno ogólne podejście do rozwiązywania problemów związanych z cele skalowalności i cele skalowalności przepustowości w szczególności. Kolejnych sekcjach, które zajmują się indywidualne magazynowania omawia cele skalowalności w kontekście określonej usługi:  

* [Obiekt blob przepustowości i żądań na sekundę](#subheading16)
* [Tabela jednostek na sekundę](#subheading24)
* [Wiadomości w kolejce na sekundę](#subheading39)  

#### <a name="sub1bandwidth"></a>Cel skalowalności przepustowości dla wszystkich usług
W czasie pisania cele przepustowości w Stanach Zjednoczonych konto magazyn geograficznie nadmiarowy (GRS) są 10 gigabity na sekundę (GB/s) dla ruchu przychodzącego (dane wysyłane do konta magazynu) i 20 GB/s dla danych wychodzących (dane wysyłane z konta magazynu). Konto magazynu lokalnie nadmiarowego (LRS), limity są wyższe — 20 GB/s dla ruchu przychodzącego i 30 GB/s dla ruchu wychodzącego.  Limity przepustowości międzynarodowych może być niższa i można znaleźć na naszej [strony cele skalowalności](https://msdn.microsoft.com/library/azure/dn249410.aspx).  Aby uzyskać więcej informacji na temat opcji nadmiarowości magazynu Zobacz linki poniżej przydatne zasoby.  

#### <a name="what-to-do-when-approaching-a-scalability-target"></a>Co należy zrobić podczas kontaktowania się z docelową skalowalność
Jeśli zbliża się limit kont magazynu, posiadane w połączeniu określonej subskrypcji/regionu, należy ocenić aplikacji i użycia kont magazynu i określić, jeśli którykolwiek z tych warunków zastosować.

* Używanie kont magazynu jako dyski niezarządzane i dodanie tych dysków do maszyny wirtualnej. W tym scenariuszu zaleca się używanie [usługi managed disks](../../virtual-machines/windows/managed-disks-overview.md), ponieważ obsługują storage dotyczące skalowalności dysku dla Ciebie bez konieczności tworzenia i zarządzania kontami magazynu.
* Na podstawie poszczególnych klientów na potrzeby izolacji danych, przy użyciu jednego konta magazynu. W tym scenariuszu firma Microsoft zaleca używanie kontenerów magazynu dla każdego klienta, a nie całe konto magazynu. Usługa Azure Storage umożliwia teraz określenie kontroli dostępu opartej na rolach na poszczególnych [podstawy kontenera](storage-auth-aad-rbac-portal.md).
* Zwiększenie skalowalności pojemność zdarzeń przychodzących/wychodzących/operacji We/Wy/przy użyciu wielu kont magazynu do fragmentu. W tym scenariuszu, jeśli to możliwe, zaleca się korzystać z [zwiększenie limitów](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/) kont magazynu w warstwie standardowa w celu zmniejszenia liczby kont magazynu wymagana dla obciążenia.

Jeśli aplikacja zbliża się do wartości docelowe skalowalności pojedynczego konta magazynu, należy rozważyć przyjęcie jedną z następujących metod:  

* Ponowne rozpatrzenie obciążenia, które powoduje, że aplikacja podejście lub przekracza tę docelową skalowalność. Można zaprojektować, jest inaczej użycie mniejszej przepustowości lub pojemności lub mniejszej liczby transakcji?
* Jeśli aplikacja może przekraczać jeden cele skalowalności, należy utworzyć wiele kont magazynu i partycji danych aplikacji na tych wielu kontach magazynu. Jeśli używasz tego wzorca, następnie należy projektowanie aplikacji tak, aby dodać więcej kont magazynu w przyszłości do równoważenia obciążenia. W czasie pisania Każda subskrypcja platformy Azure może mieć maksymalnie 100 kont magazynu.  Konta magazynu mają również bezpłatnie niż użycie pod względem — dane przechowywane, transakcje, wprowadzonych lub przesłanych danych.
* Jeśli aplikacja osiągnie cele przepustowości, należy wziąć pod uwagę kompresji danych w kliencie w celu zmniejszenia obciążenia przepustowości wymagane, aby wysyłać dane do usługi storage.  Chociaż może to oszczędzić przepustowość i zwiększyć wydajność sieci, może też mieć pewne negatywnego wpływu.  Należy ocenić wpływ na wydajność tej ze względu na wymagania dodatkowego przetwarzania do kompresowania i dekompresowania danych w kliencie. Ponadto przechowywane skompresowane dane mogą utrudnić do rozwiązywania problemów, ponieważ może to być trudniejsze do wyświetlania przechowywanych danych przy użyciu standardowych narzędzi.
* Jeśli aplikacja osiągnie cele skalowalności, upewnij się, że używasz wykładniczego wycofywania ponownych prób (zobacz [ponownych prób](#subheading14)).  Zaleca się upewnić się, nigdy nie podejście cele skalowalności (przy użyciu jednej z metod powyżej), ale daje to pewność, że aplikacja nie będzie po prostu ponawiać szybko, co gorsza ograniczania.  

#### <a name="useful-resources"></a>Przydatne zasoby
Poniższe linki zawierają dodatkowe szczegóły dotyczące cele skalowalności:

* Zobacz [usługi Azure Storage dotyczące skalowalności i cele wydajności](storage-scalability-targets.md) uzyskać informacji na temat cele skalowalności.
* Zobacz [replikacja usługi Azure Storage](storage-redundancy.md) oraz wpis w blogu [Opcje nadmiarowości magazynu platformy Azure i Magazyn geograficznie nadmiarowy dostęp do odczytu](https://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx) uzyskać informacji na temat opcji nadmiarowości magazynu.
* Aby uzyskać aktualne informacje na temat cen usług platformy Azure, zobacz [cennik usługi Azure](https://azure.microsoft.com/pricing/overview/).  

### <a name="subheading47"></a>Konwencją nazewnictwa partycji
Usługa Azure Storage korzysta z opartej na zakresie schematu partycjonowania do skalowania i równoważenia obciążenia systemu. Klucz partycji służy do partycji, które dane do zakresów i tych zakresów są równoważenia obciążenia w systemie. Oznacza to, że konwencji nazewnictwa, takie jak porządkowanie leksykalne (na przykład msftpayroll, msftperformance, msftemployees itp.) lub przy użyciu sygnatur czasowych (log20160101, log20160102, log20160102 itp.) będzie to przystosowany do partycji jest potencjalnie wspólnie przechowywane na tym samym serwerze partycji, aż operacja usługi równoważenia obciążenia dzieli je na zewnątrz na mniejsze zakresów. Na przykład wszystkie obiekty BLOB w kontenerze mogą służyć za pojedynczego serwera do momentu obciążenia tych obiektów blob wymaga dalszych ponowne równoważenie zakresu partycji. Podobnie grupy mniej obciążonych kont przy użyciu ich nazw, w kolejności leksykalne mogą być obsługiwani przez jeden serwer, aż do obciążenia na jednym lub wszystkich tych kont wymagają podzielone między wiele serwerów partycji. Każda operacja równoważenia obciążenia może mieć wpływ na opóźnienia wywołań magazynu podczas operacji. Zdolność systemu do obsługi nagłych wzrostów ruchu do partycji jest ograniczona skalowalność serwera jednej partycji do momentu operacji równoważenia obciążenia w kopnięć i rebalances zakres kluczy partycji.  

Możesz wykonać niektóre najlepsze rozwiązania, aby zmniejszyć częstotliwość takich operacji.  

* Sprawdź konwencji nazewnictwa używanych w przypadku kont, kontenerów, obiektów blob, tabele i kolejki, dokładnie. Należy rozważyć dodanie przedrostka nazwy kont za pomocą skrótu 3-cyfrowy przy użyciu funkcji skrótu, który najlepiej odpowiada Twoim potrzebom.  
* Organizowania danych przy użyciu sygnatur czasowych lub identyfikatory numeryczne należy upewnić się, że nie używasz wzorców ruchu tylko do dołączania (lub tylko dołączana). Wzorce te nie są odpowiednie dla zakresu — na podstawie partycjonowania systemu i może doprowadzić do całego ruchu, przechodząc do pojedynczej partycji i skutecznie ograniczenie systemu z równoważenia obciążenia. Na przykład w przypadku codziennych operacji korzystających z obiektu blob z sygnaturą czasową takich jak RRRRMMDD cały ruch do tego codziennej pracy jest kierowane do pojedynczego obiektu, który jest obsługiwany przez serwer z jedną partycją. Sprawdź, czy na limity obiektów blob na partycję limity potrzeb i rozważ podzielenie tej operacji na wielu obiektów blob, jeśli to konieczne. Podobnie jeśli przechowujesz dane szeregów czasowych w tabelach wszystko, co może być ruch skierowany do ostatniego część obszaru nazw kluczy. Jeśli musisz użyć sygnatur czasowych lub identyfikatory numeryczne, prefiks Identyfikatora znakiem numeru 3-cyfrowy lub w przypadku sygnatury czasowe prefiks część sekund w czasie, takie jak ssyyyymmdd. Wyświetlanie listy i badanie działań rutynowo wykonywane, jeśli funkcja wyznaczania wartości skrótu, która powoduje ograniczenie liczby zapytań. W innych przypadkach losowe prefiks, który może być wystarczające.  
* Aby uzyskać dodatkowe informacje na temat schematu partycjonowania używane w usłudze Azure Storage, przeczytaj dokument SOSP [tutaj](https://sigops.org/sosp/sosp11/current/2011-Cascais/printable/11-calder.pdf).

### <a name="networking"></a>Networking
Gdy interfejs API wywołuje sprawy, często ograniczenia sieci fizycznej aplikacji mieć znaczący wpływ na wydajność. Poniżej opisano niektóre ograniczenia, o których użytkownicy mogą wystąpić.  

#### <a name="client-network-capability"></a>Klient dostępu do sieci
##### <a name="subheading2"></a>Przepływność
Przepustowość problem jest często możliwości klienta. Na przykład podczas jednego konta magazynu może obsługiwać 10 GB/s lub więcej z transferem danych przychodzących (zobacz [cele skalowalności przepustowości](#sub1bandwidth)), szybkość sieci w wystąpieniu "Małe" Rola procesu roboczego platformy Azure obsługuje jedynie około 100 MB/s. Większych wystąpień platformy Azure mają kart sieciowych o większej pojemności, dlatego należy rozważyć użycie większego wystąpienia lub więcej maszyn wirtualnych, jeśli potrzebujesz wyższych limitów sieci z jednego komputera. Jeśli uzyskujesz dostęp do usługi Storage w aplikacji lokalnej, wówczas ta sama zasada dotyczy: zapoznać się z funkcjami sieci na urządzeniu klienckim i łączność sieciową do lokalizacji magazynu platformy Azure i jedną zwiększenia je stosownie do potrzeb lub projektowania usługi do działania aplikacji w ramach ich możliwości.  

##### <a name="subheading3"></a>Link jakości
Podobnie jak w przypadku każde użycie sieci, należy pamiętać, że warunki sieciowe skutkuje błędów i utraty pakietów spowolni skuteczne przepływności.  Za pomocą programu WireShark lub Monitor sieci może pomóc w zdiagnozowaniu tego problemu.  

##### <a name="useful-resources"></a>Przydatne zasoby
Aby uzyskać więcej informacji na temat rozmiarów maszyn wirtualnych i przydzielonej przepustowości, zobacz [rozmiarów maszyn wirtualnych Windows](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) lub [rozmiarów maszyn wirtualnych systemu Linux](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).  

#### <a name="subheading4"></a>Lokalizacja
W dowolnym środowisku rozproszonym wprowadzenie do klienta, które się w pobliżu serwer zapewnia najlepszą wydajność. Aby uzyskać dostęp do usługi Azure Storage zapewnia najniższe opóźnienie, najlepszą lokalizację dla klienta mieści się w tym samym regionie platformy Azure. Na przykład jeśli masz witrynę sieci Web platformy Azure, która używa usługi Azure Storage, ma oba te szukać w jednym regionie (na przykład zachodnie stany USA lub Azja południowo-wschodnia). Powoduje to zmniejszenie opóźnień i kosztów — w czasie pisania, przepustowości w jednym regionie jest bezpłatny.  

Jeśli Twój klient, który aplikacje nie są hostowane w obrębie platformy Azure (np. aplikacji urządzenia przenośnego lub usług enterprise lokalnych), następnie ponownie umieszczenie na koncie magazynu w regionie blisko urządzenia, które zostanie do niego dostęp i będzie ogólnie zmniejszyć opóźnienie. Jeśli klienci są szeroko rozproszone (na przykład niektóre w Ameryce Północnej i niektóre w Europie), a następnie należy rozważyć użycie wielu kont magazynu: jeden znajduje się w regionie Ameryki Północnej i jeden WE regionu Europy. Pomoże to zmniejszenie opóźnień dla użytkowników w obu regionach. To podejście jest łatwiejsze do implementacji, jeśli dane aplikacji są przechowywane jest przeznaczony dla poszczególnych użytkowników i nie wymaga replikowania danych między kontami magazynu.  Szeroka dystrybucja zawartości zaleca się sieci CDN — zobacz następną sekcję, aby uzyskać więcej informacji.  

### <a name="subheading5"></a>Dystrybucja zawartości
Czasami aplikacja musi obsługiwać tę samą zawartość do wielu użytkowników (na przykład produktu pokaz wideo używane na stronie głównej witryny sieci Web), znajdujący się w tym samym lub wielu regionach. W tym scenariuszu należy używać Content Delivery Network (CDN) takich jak usługa Azure CDN, a sieci CDN mogą używać usługi Azure storage jako źródła danych. W przeciwieństwie do konta usługi Azure Storage znajdujące się w jednym regionie, który nie można dostarczać zawartość z niskim opóźnieniem do innych regionów usługi Azure CDN używa serwerów w wielu centrach danych na całym świecie. Ponadto usługi CDN, zazwyczaj może obsługiwać znacznie wyższe limity wychodzących niż pojedynczego konta magazynu.  

Aby uzyskać więcej informacji na temat usługi Azure CDN, zobacz [wysokiej dostępności treści Azure](https://azure.microsoft.com/services/cdn/).  

### <a name="subheading6"></a>Przy użyciu sygnatury dostępu Współdzielonego i mechanizmu CORS
Gdy musisz autoryzować kodu takiego jak JavaScript w przeglądarce sieci web lub aplikacji mobilnej na dostęp do danych w usłudze Azure Storage, jedno z podejść jest użycie aplikacji w roli sieci web jako serwer proxy: urządzenie użytkownika uwierzytelnia się za pomocą roli sieci web , który z kolei autoryzuje dostępu do zasobów magazynu. W ten sposób można uniknąć, klucze konta magazynu na urządzeniach niebezpieczne udostępnianie. Jednak to powoduje znaczne obciążenie roli sieci web ponieważ wszystkie dane przesyłane między urządzeniami użytkowników i usługi storage musi przechodzić przez rolę sieci web. Można uniknąć, rola sieć web jako serwer proxy dla usługi storage przy użyciu dostępu współdzielonego Signatures (SAS), czasami w połączeniu z nagłówkami współużytkowanie zasobów między źródłami (CORS). Przy użyciu sygnatury dostępu Współdzielonego, można zezwolić urządzeniu użytkownika wysyłać żądania bezpośrednio do usługi storage przy użyciu tokenu ograniczonego dostępu. Na przykład jeśli użytkownik chce Przekaż zdjęcie do aplikacji, roli sieci web można Generowanie i wysłać do urządzenia użytkownika tokenu sygnatury dostępu Współdzielonego, który przyznaje uprawnienia do zapisu do konkretnego obiektu blob lub kontenera dla następnych 30 minut (po upływie których token sygnatury dostępu Współdzielonego wygasa).

Zwykle przeglądarki nie zezwoli JavaScript na stronie hostowanej przez witrynę internetową na jedną domenę do wykonywania określonych operacji, takich jak "PUT" do innej domeny. Na przykład jeśli hostowania na "contosomarketing.cloudapp.net" Rola sieć web i chcesz użyć JavaScript po stronie klienta, aby przekazać obiekt blob konta magazynu "contosoproducts.blob.core.windows.net" przeglądarki "tego samego źródła policy" zabraniają tej operacji. CORS to funkcja przeglądarki, która umożliwia domeny docelowej (w tym przypadku konta magazynu) do komunikacji z przeglądarki, że subskrypcja ufa żądań pochodzących z domeny źródłowej (w tym przypadku roli sieci web).  

Obie te technologie mogą pomóc w uniknięciu niepotrzebne obciążenie (i wąskie gardła) w aplikacji sieci web.  

#### <a name="useful-resources"></a>Przydatne zasoby
Aby uzyskać więcej informacji na temat sygnatury dostępu Współdzielonego, zobacz [sygnatur dostępu współdzielonego, część 1: Opis modelu sygnatur dostępu Współdzielonego](../storage-dotnet-shared-access-signature-part-1.md).  

Aby uzyskać więcej informacji na temat mechanizmu CORS zobacz [obsługi udostępniania zasobów między źródłami (CORS) dla usług Azure Storage](https://msdn.microsoft.com/library/azure/dn535601.aspx).  

### <a name="caching"></a>Buforowanie
#### <a name="subheading7"></a>Pobieranie danych
Ogólnie rzecz biorąc pobieranie danych z usługi po jest lepsza niż wprowadzenie go dwukrotnie. Rozważmy przykład aplikacji sieci web MVC uruchomionej w roli sieci web, który już ma pobrać obiekt blob 50 MB z usługi storage, która będzie służyć jako zawartość dla użytkownika. Aplikacja może następnie pobrać tego samego obiektu blob, za każdym razem, gdy użytkownik zażąda go lub go można w pamięci podręcznej ją lokalnie na dysku i ponowne użycie pamięci podręcznej wersji kolejne żądania użytkowników. Ponadto zawsze, gdy użytkownik zażąda danych, aplikacja może problem UZYSKAĆ z nagłówkiem warunkowego czas modyfikacji, które pozwoliłoby uniknąć pobierania całego obiektu blob, jeśli nie zostały zmodyfikowane. Tego samego wzorca można zastosować do pracy z jednostkami tabeli.  

W niektórych przypadkach może się okazać, że aplikację można założyć, obiekt blob pozostaje ważny przez krótki okres, po pobraniu go i że w tym okresie aplikacji nie trzeba sprawdzić, jeśli obiekt blob został zmodyfikowany.

Konfiguracja, wyszukiwania i inne dane, które są zawsze używane przez aplikację są doskonałymi kandydatami do buforowania.  

Na przykład jak uzyskać właściwości obiektu blob, aby odnaleźć daty ostatniej modyfikacji przy użyciu platformy .NET, zobacz [zestawu i pobieranie właściwości oraz metadanych](../blobs/storage-properties-metadata.md). Aby uzyskać więcej informacji na temat warunkowego pliki do pobrania, zobacz [warunkowo odświeżyć lokalną kopię obiektu Blob](https://msdn.microsoft.com/library/azure/dd179371.aspx).  

#### <a name="subheading8"></a>Przekazywanie danych w plikach wsadowych
W niektórych scenariuszach możesz agregować dane lokalnie i następnie okresowo przekaż go w zadaniu wsadowym zamiast przekazywania każdego fragmentu danych natychmiast. Na przykład aplikacja sieci web może przechowywać plik dziennika działań: aplikacji albo można przekazać szczegółowe informacje o każdym działaniu, zdarza się to jako jednostkę tabeli (co wymaga wielu operacji magazynu) lub jego można zapisać szczegółów działania w lokalnym pliku dziennika, a następnie Okresowo należy przekazać wszystkie szczegóły działań jako rozdzielonym pliku do obiektu blob. W przypadku każdego wpisu dziennika 1 KB rozmiaru, możesz przekazać tysięcy w ramach jednej transakcji "Blob umieścić" (możesz przekazać obiekt blob do 64 MB w rozmiarze w ramach jednej transakcji). Jeśli komputer lokalny ulegnie awarii przed przekazywania, potencjalnie spowoduje utratę niektórych danych dziennika: Deweloper aplikacji musi projektowania możliwości urządzeń klienckich lub błędy przekazywania.  Jeśli dane o aktywności musi zostać pobrane dla timespans (nie tylko pojedyncze działanie), BLOB są zalecane przez tabele.

### <a name="net-configuration"></a>Konfiguracja platformy .NET
Jeśli używasz programu .NET Framework, w tej sekcji przedstawiono kilka ustawień szybkiej konfiguracji, które można wprowadzić znaczne ulepszenia wydajności w.  Jeśli używasz innych języków, należy sprawdzić, jeśli podobne uniwersalne w wybranym języku.  

#### <a name="subheading9"></a>Zwiększenia domyślnego limitu połączeń
Na platformie .NET poniższy kod powoduje zwiększenie domyślny limit połączeń (która jest zwykle 2 w środowisku klienta lub 10 w środowisku serwera) do 100. Zazwyczaj należy przypisać wartość około liczbę wątków używanych przez aplikację.  

```csharp
ServicePointManager.DefaultConnectionLimit = 100; //(Or More)  
```

Należy ustawić limit połączeń, przed otwarciem żadnych połączeń.  

Dla innych języków programowania zobacz dokumentację danego języka, aby określić, jak ustawić limit połączeń.  

Aby uzyskać więcej informacji, zobacz wpis w blogu [usług sieci Web: Połączenia współbieżne](https://blogs.msdn.com/b/darrenj/archive/2005/03/07/386655.aspx).  

#### <a name="subheading10"></a>Zwiększ ThreadPool Min wątków, jeśli przy użyciu kodu synchronicznego przy użyciu zadań asynchronicznych
Ten kod zwiększy wątków z puli min wątków:  

```csharp
ThreadPool.SetMinThreads(100,100); //(Determine the right number for your application)  
```

Aby uzyskać więcej informacji, zobacz [metoda ThreadPool.SetMinThreads](https://msdn.microsoft.com/library/system.threading.threadpool.setminthreads%28v=vs.110%29.aspx).  

#### <a name="subheading11"></a>Skorzystaj z zalet platformy .NET 4.5 wyrzucania elementów bezużytecznych
Użyj platformy .NET 4.5 lub nowszy dla aplikacji klienckiej, aby wykorzystać ulepszenia wydajności w systemie serwer wyrzucania elementów bezużytecznych.

Aby uzyskać więcej informacji, zobacz artykuł [Przegląd o ulepszenia wydajności w .NET 4.5](https://msdn.microsoft.com/magazine/hh882452.aspx).  

### <a name="subheading12"></a>Niepowiązane równoległości
Równoległość mogą być bardzo przydatne na potrzeby wydajności, należy zachować ostrożność, korzystając z równoległości niepowiązane (brak limitu liczby wątków i/lub żądań równoległych) przekazywanie lub pobieranie danych za pomocą wielu procesów roboczych na dostęp do wielu partycji (kontenery, kolejki, lub partycje tabeli) w tym samym koncie magazynu lub dostępu do wielu elementów w tej samej partycji. W przypadku niepowiązanych równoległości, aplikacja może być dłuższa niż możliwości urządzeń klienckich lub skalowalności konta magazynu jest przeznaczony dla skutkuje większe opóźnienia i ograniczania przepustowości.  

### <a name="subheading13"></a>Narzędzia i biblioteki klienta magazynu
Zawsze używaj najnowszej firmy Microsoft, pod warunkiem klienta biblioteki i narzędzia. W czasie pisania są dostępne dla platformy .NET, Windows Phone, Windows Runtime, Java i C++ biblioteki klienta, a także biblioteki podglądu dla innych języków. Ponadto firma Microsoft wydała poleceń cmdlet programu PowerShell i polecenia wiersza polecenia platformy Azure do pracy z usługą Azure Storage. Firma Microsoft aktywnie opracowuje tych narzędzi z wydajność Pamiętaj, jednocześnie dbając o ich aktualne za pomocą najnowszej wersji usługi i gwarantuje, że wiele praktyk sprawdzonej wydajności wewnętrznie obsługują.  

### <a name="retries"></a>Ponowne próby
#### <a name="subheading14"></a>Ograniczanie ServerBusy
W niektórych przypadkach usługa magazynu może ograniczać aplikacji lub po prostu nie będzie mogła obsłużyć żądania ze względu na pewne stan przejściowy i zwrócić komunikat "503 Serwer jest zajęty" lub "500 limit czasu".  Może to nastąpić, jeśli aplikacja zbliża się do dowolnej cele skalowalności lub jeśli system jest ponowne równoważenie podzielonym na partycje dane, aby umożliwić większą przepływność.  Aplikacja kliencka zazwyczaj powinien ponownie wykonać operację, która powoduje występowanie takiego komunikatu o błędzie: próba później tego samego żądania mogły się powieść. Jednak usługa magazynu jest ograniczanie aplikacji, ponieważ przekracza cele skalowalności lub nawet wtedy, gdy usługa nie może obsłużyć żądania innego powodu, agresywne ponownych prób zwykle należy gorsza problem. Z tego powodu należy używać wykładniczego ponownie wył. klient biblioteki to zachowanie. Na przykład aplikacja może ponowienie próby po 2 sekundy, a następnie 4 sekundy, 10 sekund, a następnie 30 sekund, a następnie zrezygnuje całkowicie. Takie zachowanie wynika w aplikacji znacznie zmniejszyć obciążenie usługi programu zamiast powiększając istniejące wszelkie problemy.  

Błędy związane z łącznością mogą być ponawiane natychmiast, ponieważ nie są wynikiem ograniczania przepustowości i powinny to być błąd przejściowy.  

#### <a name="subheading15"></a>Błędy bez możliwości ponowienia próby
Biblioteki klienckie są znane błędy, które mogą ponownych prób, a które nie są. Jednak jeśli piszesz kod wykonywanych przy użyciu interfejsu API REST magazynu należy pamiętać, występują błędy, które nie powinny kolejnej próby: na przykład 400 (złe żądanie) odpowiedź wskazuje, że aplikacja kliencka wysłane żądanie, które nie może zostać przetworzona, ponieważ nie był w formularzu oczekiwane. Już to żądanie spowoduje, że tę samą odpowiedź każdym razem, dlatego nie ma sensu ponawianie próby go. Jeśli piszesz kod wykonywanych przy użyciu interfejsu API REST magazynu należy pamiętać, co oznaczają kody błędów i właściwy sposób, aby spróbować ponownie (lub nie) dla każdego z nich.  

#### <a name="useful-resources"></a>Przydatne zasoby
Aby uzyskać więcej informacji o kodach błędów magazynu, zobacz [stanu i kodów błędów](https://msdn.microsoft.com/library/azure/dd179382.aspx) w witrynie sieci web Microsoft Azure.  

## <a name="blobs"></a>Obiekty blob
Oprócz sprawdzonych rozwiązań dotyczących [wszystkich usług](#allservices) opisanych powyżej, poniżej sprawdzone rozwiązania dotyczą wyłącznie na usługę blob service.  

### <a name="blob-specific-scalability-targets"></a>Cele skalowalności na specyficznych dla obiektów blob
#### <a name="subheading46"></a>Wielu klientów jednocześnie dostęp do pojedynczego obiektu
Jeśli masz dużą liczbę klientów uzyskujących dostęp do pojedynczego obiektu jednocześnie, należy wziąć pod uwagę na obiekt i magazynu cele skalowalności konta. Dokładna liczba klientów, którzy mogą uzyskiwać dostęp do pojedynczego obiektu będą się różnić w zależności od czynników, takich jak liczba klientów równocześnie, żądanie obiektu rozmiar obiektu, sieci warunki itp.

Jeśli obiekt mogą być dystrybuowane za pośrednictwem sieci CDN, takie jak obrazy lub filmy wideo są obsługiwane w witrynie sieci Web, a następnie należy używać sieci CDN. Zobacz [tutaj](#subheading5).

W innych scenariuszach, takich jak symulacje naukowych, gdzie dane są poufne masz dwie opcje. Pierwsza to aby przesunąć dostęp do Twojego obciążenia w taki sposób, że obiekt jest dostępny w okresie czas uzyskiwany jednocześnie w porównaniu. Alternatywnie możesz tymczasowo skopiować obiekt do wielu kont magazynu, zwiększając w ten sposób łączna liczba operacji We/Wy dla każdego obiektu i na kontach magazynu. Podczas testowania ograniczone znaleziono, że około 25 maszyn wirtualnych może być jednocześnie pobierany 100-Gigabajtowego obiektu blob w sposób równoległy (każda maszyna wirtualna została przekształcają pliki do pobrania przy użyciu 32 wątków). Jeśli masz 100 klientów, bez potrzeby dostępu do obiektu, najpierw skopiuj go do drugiego konta magazynu, a następnie miał 50 pierwszych maszyn wirtualnych dostęp do pierwszego obiektu blob i drugi 50 VMs dostęp do drugiego obiektu blob. Wyniki zależy od swoje zachowanie aplikacji, więc należy sprawdzić, to podczas projektowania. 

#### <a name="subheading16"></a>Przepustowość i operacji na obiekt Blob
Może odczytać lub zapisać do jednego obiektu blob w lokalizacji maksymalnie 60 MB na sekundę (jest to około 480 MB/s, która rozszerza możliwości wiele sieci po stronie klienta (w tym fizycznej karty Sieciowej na urządzeniu klienckim). Ponadto pojedynczy obiekt blob obsługuje maksymalnie 500 żądań na sekundę. Jeśli wielu klientów, którzy muszą odczytywać tego samego obiektu blob może być przekroczenia tych ograniczeń, należy rozważyć użycie sieci CDN do dystrybucji obiektu blob.  

Aby uzyskać więcej informacji na temat docelowa przepustowość dla obiektów blob, zobacz [usługi Azure Storage dotyczące skalowalności i cele wydajności](storage-scalability-targets.md).  

### <a name="copying-and-moving-blobs"></a>Kopiowanie i przenoszenie obiektów blob
#### <a name="subheading17"></a>Copy Blob
Magazyn interfejsu API REST w wersji 2012-02-12 wprowadzone przydatne możliwość kopiowania obiektów blob na kontach: aplikacja kliencka wydać z usługi storage, aby skopiować obiekt blob z innego źródła (prawdopodobnie w innego konta magazynu), a następnie umożliwił wykonaj usługi Kopia asynchronicznie. Może to znacznie zmniejszyć przepustowości wymaganej dla aplikacji, podczas migracji danych z innych kont magazynu, ponieważ nie trzeba pobierać i przekazywać dane.  

Pierwsza kwestia, jest jednak, że podczas kopiowania między kontami magazynu, nie ma żadnej gwarancji czasu na gdy zostanie ukończone kopii. Jeśli Twoja aplikacja potrzebuje do ukończenia kopiowania obiektów blob, szybko pod kontrolą, może być lepszym rozwiązaniem skopiować obiektu blob, pobierając go z maszyną wirtualną i przekazać go do miejsca docelowego.  Pełne przewidywalność w takiej sytuacji upewnij się, że kopia jest wykonywane przez maszynę Wirtualną z systemem w tym samym regionie platformy Azure; w przeciwnym razie warunki sieciowe mogą i prawdopodobnie będzie mają wpływ na wydajność kopii.  Ponadto można monitorować postęp asynchronicznych kopii programowo.  

Kopie w obrębie tego samego konta magazynu, sama zwykle odbywa się szybko.  

Aby uzyskać więcej informacji, zobacz [obiektu Blob kopiowania](https://msdn.microsoft.com/library/azure/dd894037.aspx).  

#### <a name="subheading18"></a>Korzystanie z narzędzia AzCopy
Zespół usługi Azure Storage została wydana narzędzie wiersza polecenia "Narzędzia AzCopy" który jest przeznaczony do pomocy za pomocą zbiorczego transferu wiele obiektów blob do z i na kontach magazynu.  To narzędzie jest zoptymalizowany pod kątem tego scenariusza i mogą osiągnąć dużą szybkością.  Zaleca się jej użycie przekazywania masowego, pobieranie i kopiowanie scenariuszy. Aby uzyskać więcej informacji i pobierz go, zobacz [Transfer danych za pomocą wiersza polecenia Azcopy](storage-use-azcopy.md).  

#### <a name="subheading19"></a>Usługa Azure Import/Export
Dla dużych ilości danych (więcej niż 1 TB) Magazyn Azure oferuje usługę Import/Export, która umożliwia wysyłanie i pobieranie z usługi blob storage przez wysyłanie dysków twardych.  Możesz Niech Twoje dane na dysku twardym i wysyłać je do firmy Microsoft w celu przekazania lub wysyłać pusty dysk twardy do firmy Microsoft, aby pobrać dane.  Aby uzyskać więcej informacji, zobacz [Use the Microsoft Azure Import/Export Service to Transfer Data to Blob Storage (Przesyłanie danych do usługi Blob Storage za pomocą usługi Microsoft Azure Import/Export)](../storage-import-export-service.md).  Może to być znacznie bardziej efektywne niż Trwa przekazywanie/pobieranie tego woluminu danych za pośrednictwem sieci.  

### <a name="subheading20"></a>Używanie metadanych
Usługa blob obsługuje żądania head, które mogą obejmować metadane dotyczące obiektów blob. Na przykład w razie aplikacji danych EXIF poza zdjęcie może pobrania zdjęcia i Wyodrębnij jego zawartość. Aby oszczędzić przepustowość i zwiększyć wydajność, aplikacja może przechowywać danych EXIF w metadanych obiektu blob, aplikacja przekazywane zdjęcie: można później odzyskać danych EXIF w metadanych przy użyciu tylko żądania HEAD, co znaczne ograniczenie użycia przepustowości i czas przetwarzania potrzebny do wyodrębniania danych EXIF każdorazowo obiekt blob jest do odczytu. Jest to przydatne w scenariuszach, gdzie potrzebujesz tylko metadane i nie pełnej zawartości obiektu blob.  Tylko w rozmiarze 8 KB metadanych mogą być przechowywane na obiekt blob (usługa nie będzie akceptować żądania magazynu wyższej), więc jeśli dane nie mieści się w tym rozmiar, nie można używać tego podejścia.  

Na przykład sposobu uzyskania metadanych obiektu blob przy użyciu platformy .NET, zobacz [zestawu i pobieranie właściwości oraz metadanych](../blobs/storage-properties-metadata.md).  

### <a name="uploading-fast"></a>Szybkie przekazywanie
Szybko przekazać obiekty BLOB, jest pierwsze pytanie do odpowiedzi: czy przekazywanie jeden obiekt blob lub wiele?  Użyj poniższych wskazówek, aby określić poprawną metodę do użycia w zależności od scenariusza.  

#### <a name="subheading21"></a>Szybkie przekazywanie jeden duży obiekt blob
Aby szybko przekazać jeden duży obiekt blob, Twoja aplikacja kliencka należy przekazać jej bloków lub stron równolegle (co mając na uwadze cele skalowalności dla poszczególnych obiektów blob i konta magazynu jako całość).  Oficjalnych bibliotek udostępnionych przez firmę Microsoft klienta RTM magazynu (.NET, Java) mają możliwość to zrobić.  Dla każdej wersji biblioteki, użyj poniżej określonego obiektu/właściwości można ustawić poziom współbieżności:  

* .NET: Ustaw ParallelOperationThreadCount w obiekcie BlobRequestOptions ma być używany.
* Java/Android: Use BlobRequestOptions.setConcurrentRequestCount()
* Node.js: Użyj parallelOperationThreadCount od opcji żądania lub usługi blob.
* C++: Use the blob_request_options::set_parallelism_factor method.

#### <a name="subheading22"></a>Szybkie przekazywanie wiele obiektów blob
Aby szybko przekazać wiele obiektów blob, przekazywanie obiektów blob w sposób równoległy. To jest szybsza niż przekazywanie pojedynczego obiektów blob w czasie z bloku równoległe przekazywanie, ponieważ rozprzestrzenia się przekazywania na wielu partycjach usługi storage. Pojedynczy obiekt blob obsługuje tylko przepływność 60 MB na sekundę (około 480 MB/s). W momencie pisania tego konta amerykańskiej LRS obsługuje maksymalnie 20 GB transferu danych przychodzących, która jest o wiele więcej niż obsługiwane przez poszczególne obiektów blob.  [Narzędzie AzCopy](#subheading18) wykonuje przekazywanie w równoległych domyślnie i jest zalecane w przypadku tego scenariusza.  

### <a name="subheading23"></a>Wybierając odpowiedni typ obiektu blob
Usługa Azure Storage obsługuje dwa typy obiektów blob: *strony* obiektów blob i *bloku* obiektów blob. Dla scenariusza użycia danego typu blob wybór ten wpłynie wydajności i skalowalności rozwiązania. Blokowe obiekty BLOB są odpowiednie w przypadku, gdy użytkownik chce efektywnie przekazywanie dużych ilości danych: na przykład, aplikacja kliencka może być konieczne przekazywanie zdjęcia lub filmu wideo do usługi blob storage. Stronicowe obiekty BLOB są odpowiednie, jeśli aplikacja musi wykonać losowe operacje zapisu na danych: na przykład wirtualnych dysków twardych platformy Azure są przechowywane jako stronicowe obiekty BLOB.  

Aby uzyskać więcej informacji, zobacz [omówienie blokowych obiektów blob, Uzupełnialnych obiektów blob i stronicowe obiekty BLOB](https://msdn.microsoft.com/library/azure/ee691964.aspx).  

## <a name="tables"></a>Tabele
Oprócz sprawdzonych rozwiązań dotyczących [wszystkich usług](#allservices) opisanych powyżej, poniżej sprawdzone rozwiązania mają zastosowanie szczególnie w usłudze table service.  

### <a name="subheading24"></a>Cele skalowalności na specyficznych dla tabeli
Oprócz ograniczenia przepustowości całe konto magazynu tabele zawierają następujące limit skalowalności określone.  System będzie równoważenia obciążenia z wzrostem ruchu, ale jeśli lawinowo gwałtownym wzrostem ruchu może nie można uzyskiwać natychmiastowy dostęp tego woluminu przepływności.  Deseń lawinowo, należy się spodziewać się ograniczania przepustowości i/lub przekroczenia limitu czasu podczas serii jako magazyn usługa automatycznie równoważy obciążenie limit tabeli.  Rozwijanie powoli zazwyczaj ma lepsze wyniki, jak widać, czas systemowy, który umożliwia zrównoważenie obciążenia odpowiednio.  

#### <a name="entities-per-second-account"></a>Jednostek na sekundę (konto)
Limit skalowalności do uzyskiwania dostępu do tabel jest maksymalnie 20 000 jednostek (1 KB) na sekundę dla konta.  Ogólnie rzecz biorąc każda jednostka, która jest wstawiany zaktualizowany, usunięty lub skanowania liczby kierunku ten element docelowy.  Dlatego Wstawianie wsadowe, która zawiera 100 jednostek jest traktowany jako 100 jednostek.  Zapytanie, które skanuje 1000 jednostek i zwraca 5 jest traktowany jako 1000 jednostek.  

#### <a name="entities-per-second-partition"></a>Jednostek na sekundę (partycja)
W ramach jednej partycji tę docelową skalowalność na potrzeby uzyskiwania dostępu do tabel jest 2000 jednostek (1 KB) na sekundę, przy użyciu tego samego zliczanie zgodnie z opisem w poprzedniej sekcji.  

### <a name="configuration"></a>Konfigurowanie
W tej sekcji przedstawiono kilka ustawień szybkiej konfiguracji, które można użyć, aby wprowadzić znaczne ulepszenia wydajności w usłudze table service:  

#### <a name="subheading25"></a>Przy użyciu formatu JSON
Począwszy od wersji usługi storage 2013-08-15, usłudze table service obsługuje, przy użyciu zamiast formatu AtomPub oparty na formacie XML do przesyłania danych tabeli. To może zmniejszyć rozmiary obciążeń żądań, o ile 75% i może znacznie poprawić wydajność aplikacji.

Aby uzyskać więcej informacji, zobacz wpis [tabele platformy Azure firmy Microsoft: Wprowadzenie do formatu JSON](https://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/05/windows-azure-tables-introducing-json.aspx) i [Format ładunku dla operacji usługi tabeli](https://msdn.microsoft.com/library/azure/dn535600.aspx).

#### <a name="subheading26"></a>Nagle Off
Algorytm Nagle'a firmy jest często stosowana w sieciach TCP/IP w celu zwiększenia wydajności sieci. Jednak nie jest optymalna w każdych okolicznościach (na przykład wysoce interaktywnych środowisk). Dla usługi Azure Storage algorytm Nagle'a firmy ma negatywny wpływ na wydajność żądań do tabel i kolejek usługi i należy ją wyłączyć, jeśli jest to możliwe.

### <a name="schema"></a>Schemat
Jak reprezentują i wykonuj zapytania na danych jest największy pojedynczy czynnik, który wpływa na wydajność usługi table service. Gdy każda aplikacja jest inna, w tej sekcji przedstawiono pewne ogólne sprawdzonych rozwiązań, które odnoszą się do:  

* Projektowaniu tabel
* Wydajne zapytania
* Aktualizacje wydajne danych  

#### <a name="subheading27"></a>Tabele i partycje
Tabele są podzielone na partycje. Każda jednostka, przechowywane w partycji udostępnia ten sam klucz partycji i kluczu unikatowych wierszach, aby zidentyfikować go w ramach tej partycji. Partycje zapewniają korzyści, ale także wprowadzają limity skalowalności.  

* Korzyści: Można aktualizować jednostek w tej samej partycji w jednym, atomic, batch transakcji, która zawiera maksymalnie 100 operacji przechowywania (ograniczenie całkowitego rozmiaru 4 MB). Zakładając, że taką samą liczbę jednostek, które mają zostać pobrane, możesz także zbadać danych w jednej partycji efektywniej niż danych z różnych partycji (chociaż należy zapoznać się otrzymać dalsze zalecenia na wykonywanie zapytań o dane tabeli).
* Limit skalowalności: Dostęp do jednostek przechowywanych w jednej partycji nie może być równoważenia obciążenia ponieważ partycje obsługuje transakcje niepodzielne partii. Z tego powodu tę docelową skalowalność dla pojedynczej tabeli partycji jest mniejszy niż usługi tabeli jako całości.  

Ze względu na te cechy tabele i partycje powinna przyjąć następujące zasady projektowania:  

* Dane, które Twoja aplikacja kliencka często zaktualizowane lub wykonać zapytania w tej samej jednostki logicznej pracy powinny znajdować się w tej samej partycji.  Może to być, ponieważ aplikacja jest agregowanie zapisów lub chcesz korzystać z zalet operacji wsadowych atomic.  Ponadto danych w jednej partycji mogą być bardziej efektywnie wyszukiwane w pojedyncze zapytanie niż odzyskiwanie danych między partycjami.
* Dane, które Twoja aplikacja kliencka nie wstawiania/aktualizacji lub zapytania w tej samej jednostki logicznej pracy (jednego zapytania lub aktualizację partii) powinien znajdować się w oddzielnych partycjach.  OneNote ważne jest, czy nie ma żadnego limitu liczby kluczy partycji w jednej tabeli, więc po miliony kluczy partycji nie jest problemem i nie ma wpływu na wydajność.  Na przykład jeśli aplikacja jest popularne witryny sieci Web przy użyciu nazwy logowania użytkownika, przy użyciu Identyfikatora użytkownika jako klucza partycji może być dobrym wyborem.  

#### <a name="hot-partitions"></a>Partycje w warstwie gorąca
Gorącą partycją to taki, który otrzymuje nieproporcjonalnie duży ruch do konta usługi i nie może być ze zrównoważonym obciążeniem ponieważ jednej partycji.  Ogólnie rzecz biorąc gorąca partycji tworzone są dwa sposoby:  

##### <a name="subheading28"></a>Tylko Dołącz i dołączana tylko wzorców
Wzorzec "Tylko dołączanie" jest jednym gdzie (większość lub wszystkie) ruchu do danego klucza produktu, zwiększa i zmniejsza zgodnie z bieżącą godzinę.  Przykładem jest, jeśli jako klucza partycji dla danych dziennika aplikacji używana bieżącą datę.  Skutkuje to wszystkie operacje wstawiania przerywaj ostatniej partycji w tabeli, a system nie może załadować saldo, ponieważ wszystkie operacje zapisu przechodzenia na koniec tabeli.  Jeśli wielkość ruchu sieciowego do tej partycji przekracza tę docelową skalowalność poziomu partycji, a następnie spowoduje ograniczenie.  Zaleca się upewnić się, że ruch jest wysyłany do wielu partycji, aby włączyć równoważenie obciążenia żądań w tabeli.  

##### <a name="subheading29"></a>Dane o dużym natężeniu ruchu
Jeśli schemat partycjonowania w wyniku jedną partycję, która zawiera tylko dane o wiele bardziej używane od innych partycji, ograniczania przepływności mogą również wystąpić jako partycja zbliża się tę docelową skalowalność w jednej partycji.  Zaleca się upewnić się, że schemat partycji powoduje nie jednej partycji, zbliża się cele skalowalności.  

#### <a name="querying"></a>Wykonywanie zapytania
W tej sekcji opisano sprawdzone rozwiązania na potrzeby wykonywania zapytań w usłudze table service.  

##### <a name="subheading30"></a>Zakres kwerendy
Istnieje kilka sposobów, aby określić zakres jednostek do wykonywania zapytań.  Poniżej przedstawiono omówienie używa każdego.  

Ogólnie rzecz biorąc Unikaj skanowania (zapytań, większa niż jeden podmiot), ale jeśli musisz przeprowadzić skanowanie, spróbuj organizowania danych, tak aby usługi skanowania pobierać dane, które wymagają bez skanowanie lub zwracania znacznej ilości jednostek, które nie są potrzebne.  

###### <a name="point-queries"></a>Zapytań o punkt
Kwerendy punktu pobiera dokładnie jedną jednostkę. Robi to poprzez określenie klucza partycji i klucz wiersza jednostki do pobrania. Te zapytania są wydajne i należy ich używać, gdy jest to możliwe.  

###### <a name="partition-queries"></a>Partycja zapytania
Zapytanie partycji jest zapytanie, które pobiera zestaw danych, która udostępnia wspólny klucz partycji. Zazwyczaj zapytanie określa zakres wartości klucza wiersza lub zakres wartości dla niektórych właściwości jednostki, oprócz klucza partycji. Są one mniej wydajne niż zapytań o punkt i powinny być używane rzadko.  

###### <a name="table-queries"></a>Tabela zapytania
Zapytanie tabeli jest zapytanie, które pobiera zestaw jednostek, który nie udostępnia wspólny klucz partycji. Te zapytania nie są wydajne i nie należy ich, jeśli jest to możliwe.  

##### <a name="subheading31"></a>Gęstość zapytania
Innym czynnikiem klucza w wydajności zapytania jest liczby jednostek zwróconych w porównaniu do liczby jednostek przeskanowane w celu znalezienia zwróconego zestawu. Jeśli aplikacja wykonuje zapytania tabeli za pomocą filtru dla wartości właściwości, czy tylko 1% udziały danych zapytania będą skanować 100 jednostek dla każdej jednostki jednego, który zwraca. Cele skalowalności tabeli omówionych wcześniej wszystkie odnoszą się do liczby jednostek skanowania, a nie liczby jednostek zwróconych: zapytania niskiej gęstości mogą łatwo spowodować usłudze table service ograniczać aplikacji, ponieważ wymaga skanowania, tak wiele jednostek do pobranie jednostki, którego szukasz.  Zobacz sekcję poniżej na [denormalizacja](#subheading34) Aby uzyskać więcej informacji na temat tego uniknąć.  

##### <a name="limiting-the-amount-of-data-returned"></a>Ograniczenie ilości danych zwracanych
###### <a name="subheading32"></a>Filtrowanie
W przypadku, gdy wiesz, że kwerenda będzie zwracać jednostek, które nie wymagają w aplikacji klienckiej, należy wziąć pod uwagę przy użyciu filtru, aby zmniejszyć rozmiar zwróconego zestawu. Podczas gdy jednostki nie jest zwracana do klienta nadal wliczają się do ograniczeń skalowalności, ze względu na rozmiar ładunku mniejsze sieci i ograniczoną liczbę jednostek, które Twoja aplikacja kliencka musi przetworzyć zwiększy się wydajność aplikacji.  Zobacz powyżej Uwaga na [gęstość zapytania](#subheading31), jednak — cele skalowalności odnoszą się do liczby jednostek skanowania, dzięki czemu zapytania, który odfiltrowuje wielu jednostek może nadal spowodować ograniczanie przepustowości, nawet jeśli kilka jednostek są zwracane.  

###### <a name="subheading33"></a>Projekcja
Jeśli Twoja aplikacja kliencka potrzebuje tylko ograniczony zestaw właściwości między jednostkami w tabeli, umożliwia ograniczenie rozmiaru zestawu danych zwróconego przez osoby projekcji. Podobnie jak w przypadku filtrowania, pozwala to zmniejszyć obciążenie sieci i przetwarzania klienta.  

##### <a name="subheading34"></a>Denormalizacja
W przeciwieństwie do pracy z relacyjnych baz danych, sprawdzonych rozwiązań dotyczących wydajne wykonywanie zapytań o dane tabeli prowadzić denormalizing danych. Oznacza to, powielanie te same dane w wielu jednostek (po jednym dla każdego klucza, możesz użyć, aby znaleźć dane) aby ograniczyć liczbę jednostek, które zapytania wymaga skanowania można znaleźć danych klienta wymaga, zamiast konieczności skanowania dużej liczby obiektów, aby znaleźć dane aplikacji wymaga się instalowania aplikacji.  Na przykład w witrynie internetowej handlu elektronicznego, warto znajdowanie zamówienia zarówno według Identyfikatora klienta (należy nadać mi zamówień tego klienta) oraz według daty (należy nadać mi zamówienia w dniu).  W usłudze Table Storage, najlepiej przechowywać jednostki (lub odwołanie do niej) jest dwa razy — raz z nazwą tabeli klucza podstawowego i RK, aby ułatwić znajdowanie przez klienta identyfikator, jeden raz ułatwiające znalezienie go według daty.  

#### <a name="insertupdatedelete"></a>Wstawiania/aktualizowania/usuwania
W tej sekcji opisano sprawdzonych rozwiązań dotyczących modyfikowania jednostek przechowywanych w usłudze table service.  

##### <a name="subheading35"></a>Przetwarzanie wsadowe
Transakcje usługi Batch są określane jako transakcje grupy jednostek (ETG) w usłudze Azure Storage; wszystkie operacje w ramach ETG musi być w jednej partycji w jednej tabeli. Jeśli to możliwe, należy użyć ETGs przeprowadzić wstawiania, aktualizacji i usuwania w partiach. To zmniejsza liczbę rund z aplikacji klienta do serwera, zmniejsza liczbę transakcji do rozliczenia (ETG jest liczona jako pojedynczą transakcję, dla celów rozliczeniowych i może zawierać do 100 operacji magazynu) i umożliwia atomic aktualizacje, (wszystkie wersje operacje kończą się pomyślnie lub nie powiedzie się w obrębie ETG). Środowiska z wysokimi opóźnieniami, takich jak urządzenia przenośne będzie znacznie korzyści ze stosowania ETGs.  

##### <a name="subheading36"></a>UPSERT
Użyj tabeli **Upsert** operacje wszędzie tam, gdzie to możliwe. Istnieją dwa rodzaje **Upsert**, które mogą być bardziej efektywne niż tradycyjne **Wstaw** i **aktualizacji** operacje:  

* **InsertOrMerge**: Użyj tego ustawienia podczas przekazywania podzbioru właściwości jednostki, ale nie wiesz, czy ta jednostka już istnieje. Jeśli jednostka istnieje, to wywołanie aktualizuje właściwości zawarte w **Upsert** operacji i pozostawia wszystkich istniejących właściwości są one, jeśli jednostka nie istnieje, Nowa jednostka do wstawienia. Jest to podobne do rzutowania w zapytaniu, w tym, że musisz przekazać właściwości, które zmieniają się.
* **InsertOrReplace**: Użyj tego, gdy chcesz przekazać nowe jednostki, ale nie masz pewności, czy już istnieje. Należy używać tylko to, gdy wiesz, że nowo przesłanym jednostki jest całkowicie poprawny całkowicie zastępuje stare jednostki. Na przykład chcesz zaktualizować jednostki, która przechowuje lokalizację bieżącego użytkownika, niezależnie od tego, czy aplikacja wcześniej zapisanych danych lokalizacji dla użytkownika; Nowa jednostka lokalizacji zostało zakończone, a nie ma potrzeby dowolnych informacji z dowolnej poprzedniej jednostki.

##### <a name="subheading37"></a>Przechowywanie serii danych w pojedynczej jednostki
Czasami aplikacja przechowuje serie danych, która często potrzebuje do pobrania w całości: na przykład aplikacja może śledzić użycie procesora CPU wraz z upływem czasu do wykreślenia wykresu stopniowe danych z ostatnich 24 godzinach. Jedno z podejść jest jedną jednostkę tabeli na godzinę, o każdej jednostki reprezentującą określoną godzinę i przechowywanie użycie procesora CPU dla danej godziny. Do wykreślenia te dane, aplikacja musi pobrać jednostek zawierających dane z ostatnich 24 godzin.  

Alternatywnie aplikacja może przechowywać użycie procesora CPU dla każdej godziny jako osobne właściwości pojedynczej jednostki: Aby zaktualizować co godzinę, aplikacja może używać jednej **InsertOrMerge Upsert** wywołanie, aby zaktualizować wartość ostatniego Godzina. Przedstawianie danych, aplikacja tylko musi pobrać pojedynczą jednostkę zamiast 24, dzięki czemu wydajne zapytania (zobacz powyżej dyskusji na [kwerendy zakresu](#subheading30)).

##### <a name="subheading38"></a>Przechowywanie ustrukturyzowanych danych w obiektach blob
Czasami ustrukturyzowanych danych tak, jak powinien przeprowadzić w tabelach, ale zakresy jednostki są zawsze pobierane ze sobą i można wstawiać usługi batch.  Dobrym przykładem jest plik dziennika.  W takim przypadku można partii dzienników z kilku minut, umieść je, a następnie kilka minut dzienników w czasie jak również są zawsze pobierane.  W tym przypadku wydajności, go lepiej jest użyć zamiast tabel, obiektów blob, ponieważ mogą znacznie zmniejszyć liczbę obiekty napisane/zwracane, a także zazwyczaj liczbę żądań, które muszą.  

## <a name="queues"></a>Kolejki
Oprócz sprawdzonych rozwiązań dotyczących [wszystkich usług](#allservices) opisanych powyżej, poniżej sprawdzone rozwiązania mają zastosowanie szczególnie w przypadku usługi kolejki.  

### <a name="subheading39"></a>Limity skalowalności
Kolejka może przetworzyć około 2000 komunikatów (1 KB) na sekundę (AddMessage GetMessage i DeleteMessage są traktowane jak jeden komunikat w tym miejscu). Jeśli jest niewystarczająca dla aplikacji, należy użyć wielu kolejek i rozłożyć wiadomości między nimi.  

Wyświetl bieżące cele skalowalności w [usługi Azure Storage dotyczące skalowalności i cele wydajności](storage-scalability-targets.md).  

### <a name="subheading40"></a>Nagle Off
Zobacz sekcję dotyczącą konfiguracji tabeli, która w tym artykule omówiono algorytm Nagle'a — algorytm Nagle'a jest zazwyczaj negatywnie wpływać na wydajność żądań kolejki i należy je wyłączyć.  

### <a name="subheading41"></a>Rozmiar komunikatu
Kolejki, wydajność i skalowalność zmniejszenie wzrostem rozmiaru wiadomości. W komunikacie, należy umieścić tylko te informacje, które wymaga odbiorcy.  

### <a name="subheading42"></a>Pobieranie usługi Batch
Możesz pobrać maksymalnie 32 komunikaty z kolejki w ramach jednej operacji. Może to zmniejszyć liczbę natężenie ruchu od aplikacji klienckiej, która jest szczególnie przydatne w przypadku środowisk, takich jak urządzenia przenośne dużymi opóźnieniami.  

### <a name="subheading43"></a>Interwał sondowania kolejki
Większość aplikacji sondowania pod kątem komunikatów z kolejki, która może być jednym z największych źródeł transakcje dla tej aplikacji. Należy uważnie wybierz swoje interwał sondowania: sondowania zbyt częste może spowodować, że aplikacji podejście cele skalowalności dla kolejki. Jednak w 200 000 transakcji dla $0.01 (w momencie pisania), przez pojedynczy procesor sondowania po każdej sekundzie przez jeden miesiąc będzie kosztować mniej niż 15 centów, więc koszt jest zwykle czynnikiem, który ma wpływ na wybór interwału sondowania.  

Koszt aktualności informacji, zobacz [cennik usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage/).  

### <a name="subheading44"></a>UpdateMessage
Możesz użyć **UpdateMessage** zwiększenie limitu czasu niewidoczności, lub można zaktualizować informacji o stanie wiadomości. Gdy jest to zaawansowane, należy pamiętać, że każdy **UpdateMessage** operacji liczy się do tę docelową skalowalność. Jednak może to być to podejście znacznie bardziej efektywne niż posiadanie przepływu pracy, który przekazuje zadania z jedną kolejką na następny jako krok po kroku przez zadanie jest ukończone. Za pomocą **UpdateMessage** operacji umożliwia aplikacji w taki sposób zapisać stan zadania do wiadomości, a następnie kontynuować pracę, zamiast requeuing wiadomości do kolejnego kroku zadania, za każdym razem, gdy krok jest wykonywany.  

Aby uzyskać więcej informacji, zobacz artykuł [jak: Zmień zawartość komunikatu w kolejce](../queues/storage-dotnet-how-to-use-queues.md#change-the-contents-of-a-queued-message).  

### <a name="subheading45"></a>Architektura aplikacji
Należy użyć kolejek się skalowalne architektury aplikacji. Poniżej wymieniono niektóre sposoby kolejek można użyć, aby Twoje aplikacje były bardziej skalowalne:  

* Tworzenie zaległości pracy do przetwarzania i wygładzania nagłych obciążeń w aplikacji, można użyć kolejki. Na przykład można kolejkować się żądań od użytkowników do wykonywania pracy o znacznym wykorzystaniu procesora, takich jak zmiany rozmiaru przekazanych obrazów.
* Kolejki umożliwia rozdzielenie poszczególnych części aplikacji, dzięki czemu można je skalować niezależnie. Na przykład frontonu sieci web można umieścić wyniki badań od użytkowników w kolejce do nowszych analizy i magazynowania. Można dodać więcej wystąpień roli procesu roboczego, aby przetwarzać dane w kolejce zgodnie z potrzebami.  

## <a name="conclusion"></a>Podsumowanie
W tym artykule omówiono niektóre z najczęściej stosowanych, sprawdzonych rozwiązań dotyczących optymalizacji wydajności w przypadku korzystania z usługi Azure Storage. Zachęcamy każdego dewelopera aplikacji do oceny swoich aplikacji pod kątem powyższych rozwiązań oraz do postępowania zgodnie z zaleceniami mającymi na celu uzyskanie optymalnej wydajności aplikacji korzystających z usługi Azure Storage.
