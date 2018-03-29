---
title: Azure kontrolną wydajności i skalowalności magazynu | Dokumentacja firmy Microsoft
description: Lista kontrolna sprawdzonych rozwiązań do użycia z usługą Azure Storage w tworzeniu wydajność aplikacji.
services: storage
documentationcenter: ''
author: roygara
manager: jeconnoc
editor: tysonn
ms.assetid: 959d831b-a4fd-4634-a646-0d2c0c462ef8
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2016
ms.author: rogarana
ms.openlocfilehash: 945289a172270eea56625287baf437fd4b70c7f3
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="microsoft-azure-storage-performance-and-scalability-checklist"></a>Lista kontrolna dotycząca wydajności i skalowalności usługi Microsoft Azure Storage
## <a name="overview"></a>Przegląd
Po wydaniu usługi Microsoft Azure Storage firma Microsoft opracowała liczba sprawdzonych wskazówki dotyczące korzystania z tych usług w sposób wydajności, a w tym artykule służy do konsolidowania najważniejszych je na listę kontrolną stylu. Części tego artykułu jest ułatwia deweloperom aplikacji, sprawdź, czy używasz sprawdzonych rozwiązań z usługą Azure Storage oraz pomoc w identyfikacji innych sprawdzonych rozwiązań, należy rozważyć stosowanie. W tym artykule nie jest podejmowana próba obejmuje co możliwe optymalizacji wydajności i skalowalności —, ale nie uwzględnia te, które są niewielkie ich wpływ lub nie ogólnie dotyczy. W takim zakresie, w jakim można przewidzieć zachowania aplikacji podczas projektowania, warto zachować te pamiętać na wczesnym etapie, aby uniknąć projektów, które będą uruchamiane na problemy z wydajnością.  

Każdy deweloper aplikacji przy użyciu usługi Azure Storage powinien trwać przeczytaj ten artykuł, i sprawdź, czy aplikacji lub jej zgodny z każdego sprawdzonych rozwiązań wymienionych poniżej.  

## <a name="checklist"></a>Lista kontrolna
W tym artykule organizuje sprawdzonych rozwiązań w następujących grup. Sprawdzonych rozwiązań mające zastosowanie do:  

* Wszystkie usługi Azure Storage (obiekty BLOB, tabel, kolejek i plików)
* Obiekty blob
* Tabele
* Kolejki  

| Gotowe | Obszar | Kategoria | Pytanie |
| --- | --- | --- | --- |
| &nbsp; | Wszystkie usługi |Wartości docelowe skalowalności |[Aplikacja zaprojektowano w celu uniknięcia zbliża się wartości docelowe skalowalności?](#subheading1) |
| &nbsp; | Wszystkie usługi |Wartości docelowe skalowalności |[Konwencja nazewnictwa zaprojektowana w celu umożliwienia lepszego równoważenia obciążenia?](#subheading47) |
| &nbsp; | Wszystkie usługi |Networking |[Czy urządzenia po stronie klienta mają wystarczająco dużej przepustowości i małe opóźnienia do osiągnięcia wydajności potrzebne?](#subheading2) |
| &nbsp; | Wszystkie usługi |Networking |[Czy urządzenia po stronie klienta mają łącze wystarczająco wysokiej jakości?](#subheading3) |
| &nbsp; | Wszystkie usługi |Networking |[Aplikacja kliencka znajduje się "na koncie magazynu u"?](#subheading4) |
| &nbsp; | Wszystkie usługi |Dystrybucja zawartości |[Używasz CDN do dystrybucji zawartości?](#subheading5) |
| &nbsp; | Wszystkie usługi |Bezpośredni dostęp klienta |[Używasz SAS i mechanizmu CORS umożliwia bezpośredni dostęp do magazynu, zamiast serwera proxy?](#subheading6) |
| &nbsp; | Wszystkie usługi |Buforowanie |[Jest rzadko buforowania danych aplikacji, często używany i zmiany?](#subheading7) |
| &nbsp; | Wszystkie usługi |Buforowanie |[Aplikacja jest przetwarzanie wsadowe aktualizacji (buforowanie je po stronie klienta, a następnie przekazywania w większych zestawów)?](#subheading8) |
| &nbsp; | Wszystkie usługi |Konfiguracja .NET |[Czy skonfigurowano z klienta do używania wystarczającą liczbę równoczesnych połączeń?](#subheading9) |
| &nbsp; | Wszystkie usługi |Konfiguracja .NET |[Czy skonfigurowano platformy .NET, użyj wystarczającą liczbę wątków?](#subheading10) |
| &nbsp; | Wszystkie usługi |Konfiguracja .NET |[Używasz .NET 4.5 lub nowszy, która została ulepszona wyrzucanie elementów bezużytecznych?](#subheading11) |
| &nbsp; | Wszystkie usługi |Równoległość |[Ma zapewnić że równoległości jest ograniczone odpowiednio tak, aby nie przeciążać możliwości klienta lub wartości docelowe skalowalności?](#subheading12) |
| &nbsp; | Wszystkie usługi |Narzędzia |[Za pomocą najnowszej wersji programu Microsoft znajdują się narzędzia i bibliotek klienta?](#subheading13) |
| &nbsp; | Wszystkie usługi |Ponowne próby |[Czy na pewno przy użyciu wykładniczego wycofywania ponawiania zasady ograniczania błędów i limity czasu?](#subheading14) |
| &nbsp; | Wszystkie usługi |Ponowne próby |[Jest unikanie ponownych prób Twojej aplikacji niepowtarzającego błędów?](#subheading15) |
| &nbsp; | Obiekty blob |Wartości docelowe skalowalności |[Masz dużą liczbę klientów uzyskujących dostęp do pojedynczego obiektu równocześnie?](#subheading46) |
| &nbsp; | Obiekty blob |Wartości docelowe skalowalności |[Aplikacja przebywa w celu skalowalność przepustowości lub operacji dla pojedynczego obiektu blob?](#subheading16) |
| &nbsp; | Obiekty blob |Kopiowanie obiektów blob |[Czy kopiowanie blob wydajne?](#subheading17) |
| &nbsp; | Obiekty blob |Kopiowanie obiektów blob |[Używasz narzędzia AzCopy do kopiowania zbiorczego obiektów blob?](#subheading18) |
| &nbsp; | Obiekty blob |Kopiowanie obiektów blob |[Import/Eksport Azure jest używany do transferowania dużych woluminów danych?](#subheading19) |
| &nbsp; | Obiekty blob |Używanie metadanych |[Są przechowywania często używanych metadane dotyczące obiektów blob w ich metadanych?](#subheading20) |
| &nbsp; | Obiekty blob |Przekazywanie Fast |[Podczas próby przekazania szybko jednego obiektu blob, przekazujesz bloki równocześnie?](#subheading21) |
| &nbsp; | Obiekty blob |Przekazywanie Fast |[Podczas próby szybko przekazać wiele obiektów blob, przekazujesz obiekty BLOB równocześnie?](#subheading22) |
| &nbsp; | Obiekty blob |Popraw typu obiektu Blob |[Używasz stronicowych obiektów blob lub blokowych obiektów blob, gdy jest to potrzebne?](#subheading23) |
| &nbsp; | Tabele |Wartości docelowe skalowalności |[Są zbliża się wartości docelowe skalowalności dla jednostek na sekundę?](#subheading24) |
| &nbsp; | Tabele |Konfigurowanie |[Używasz JSON dla żądań tabeli?](#subheading25) |
| &nbsp; | Tabele |Konfigurowanie |[Możesz wyłączyć Nagle'a poprawić wydajność żądań małych?](#subheading26) |
| &nbsp; | Tabele |Tabele i partycji |[Ma należy prawidłowo podzielona na partycje danych?](#subheading27) |
| &nbsp; | Tabele |Partycje dynamicznej |[Są jest unikanie tylko Dołącz i tylko dołączy wzorców?](#subheading28) |
| &nbsp; | Tabele |Partycje dynamicznej |[Operacji wstawiania/aktualizacji są rozkładane między wiele partycji?](#subheading29) |
| &nbsp; | Tabele |Zakres kwerendy |[Możesz zaprojektowany schemat punktu zapytania do użycia w większości przypadków i zapytania tabeli, aby być używane rzadko?](#subheading30) |
| &nbsp; | Tabele |Gęstość zapytania |[Czy skanowania zazwyczaj tylko zapytania i zwracanie wszystkich wierszy, które będą korzystać z aplikacji?](#subheading31) |
| &nbsp; | Tabele |Ograniczenie zwrócone dane |[Używasz filtrowanie w celu uniknięcia zwracających jednostki, które nie są potrzebne?](#subheading32) |
| &nbsp; | Tabele |Ograniczenie zwrócone dane |[Aby uniknąć zwracanie właściwości, które nie są wymagane jest używany projekcji?](#subheading33) |
| &nbsp; | Tabele |Denormalization |[Dane mają nieznormalizowane tak, aby uniknąć nieefektywne zapytania lub wiele żądań odczytu podczas próby pobrania danych?](#subheading34) |
| &nbsp; | Tabele |Wstawiania/aktualizowania/usuwania |[Czy można przetwarzanie wsadowe żądań wymagających transakcyjnej lub może odbywać się w tym samym czasie, aby zmniejszyć przechodzenia?](#subheading35) |
| &nbsp; | Tabele |Wstawiania/aktualizowania/usuwania |[Jest unikanie pobierania jednostki tylko w celu określenia, czy do wywołania, insert lub update?](#subheading36) |
| &nbsp; | Tabele |Wstawiania/aktualizowania/usuwania |[Bierzesz pod uwagę przechowywanie serii danych, które często mają zostać pobrane ze sobą w pojedynczej jednostki jako właściwości zamiast wiele jednostek?](#subheading37) |
| &nbsp; | Tabele |Wstawiania/aktualizowania/usuwania |[Dla jednostek, które będą zawsze pobierane ze sobą i mogą być zapisywane w partiach (np. czasu serii danych) bierzesz pod uwagę zamiast tabel za pomocą obiektów blob?](#subheading38) |
| &nbsp; | Kolejki |Wartości docelowe skalowalności |[Są zbliża się wartości docelowe skalowalności komunikatów na sekundę?](#subheading39) |
| &nbsp; | Kolejki |Konfigurowanie |[Możesz wyłączyć Nagle'a poprawić wydajność żądań małych?](#subheading40) |
| &nbsp; | Kolejki |Rozmiar komunikatu |[Czy Twoje compact wiadomości do zwiększenia wydajności kolejki?](#subheading41) |
| &nbsp; | Kolejki |Zbiorcze pobieranie |[Czy pobieranych wiele komunikatów w ramach jednej operacji "Get"?](#subheading42) |
| &nbsp; | Kolejki |Częstotliwość sondowania |[Są możesz sondowania wystarczająco często, aby zmniejszyć opóźnienia postrzegana aplikacji?](#subheading43) |
| &nbsp; | Kolejki |Komunikat dotyczący aktualizacji |[UpdateMessage jest używany do przechowywania postęp w przetwarzanie komunikatów, co pozwala uniknąć konieczności ponownie przetworzyć cały komunikat, jeśli wystąpi błąd?](#subheading44) |
| &nbsp; | Kolejki |Architektura |[Kolejki jest używany do całej aplikacji skalowalność za utrzymywanie obciążeń długotrwałe poza ścieżkę krytyczną i następnie skalować niezależnie?](#subheading45) |

## <a name="allservices"></a>Wszystkie usługi
Ta sekcja zawiera sprawdzonych rozwiązań, które mają zastosowanie do użycia usług Azure Storage (obiekty BLOB, tabel, kolejek lub plików).  

### <a name="subheading1"></a>Wartości docelowe skalowalności
Każda z usług magazynu Azure ma wartości docelowe skalowalności pojemność (GB), szybkości transakcji i przepustowości. Jeśli aplikacja zbliża się lub przekroczy wartości docelowe skalowalności, napotkać opóźnienia zwiększona transakcji lub ograniczenia przepustowości. Gdy usługa Magazyn ogranicza aplikacji, usługi rozpoczyna się do zwrócenia "503 Serwer jest zajęty" lub "500 limit czasu operacji" kody błędów dla niektórych transakcji magazynu. W tej sekcji omówiono zarówno ogólne podejście do pracy nad cele dotyczące skalowalności przepustowości i wartości docelowe skalowalności w szczególności. Kolejnych sekcjach, które zajmują się usługi magazynu omówiono w nim wartości docelowe skalowalności w kontekście tej określonej usługi:  

* [Obiekt blob przepustowość i żądań na sekundę](#subheading16)
* [Tabela jednostek na sekundę](#subheading24)
* [Wiadomości w kolejce na sekundę](#subheading39)  

#### <a name="sub1bandwidth"></a>Cel skalowalność przepustowości dla wszystkich usług
Zapisywanie obiektów docelowych przepustowości w Stanach Zjednoczonych dla konta magazynu geograficznie nadmiarowego (GRS) są 10 GB/s na sekundę (GB/s) transfer danych przychodzących (dane wysyłane do konta magazynu) i 20 GB/s dla transfer danych wychodzących (dane przesyłane z konta magazynu). Konto magazyn lokalnie nadmiarowy (LRS), limity są wyższe — 20 GB/s dla przychodzące i 30 GB/s za wyjście.  Limity przepustowości międzynarodowych może być niższa i można znaleźć w naszych [strony elementy docelowe skalowalności](http://msdn.microsoft.com/library/azure/dn249410.aspx).  Aby uzyskać więcej informacji na temat opcji nadmiarowość magazynu, zobacz linki w [przydatne zasoby](#sub1useful) poniżej.  

#### <a name="what-to-do-when-approaching-a-scalability-target"></a>Co zrobić, gdy zbliża się docelowy skalowalności
Jeśli aplikacja zbliża się do wartości docelowe skalowalności konta pojedynczy magazyn, należy wziąć pod uwagę przyjmowanie jedną z następujących metod:  

* Rozważenia obciążeniem, które powoduje, że aplikacja podejścia lub przekraczają docelowy skalowalności. Można projektować, jest inaczej użycie mniejszej przepustowości lub pojemności lub mniejszą liczbą transakcji?
* Jeśli aplikacja może przekraczać jedną z wartości docelowe skalowalności, należy utworzyć wiele kont magazynu i partycji danych aplikacji w wielu kont magazynu. Jeśli używasz tego wzorca, następnie należy koniecznie Utwórz projekt swojej aplikacji tak, aby dodać więcej kont magazynu w przyszłości do równoważenia obciążenia. W czasie zapisywania każdej subskrypcji platformy Azure może mieć maksymalnie 100 kont magazynu.  Konta magazynu ma także żadnego kosztu innych niż użycie pod względem przechowywanych danych, transakcji wykonanych lub przesyłane dane.
* Jeśli aplikacja trafienia cele przepustowości, należy wziąć pod uwagę kompresowania danych na komputerze klienckim, aby ograniczyć przepustowość wymagana do wysyłania danych do usługi magazynu.  Należy pamiętać, że może to oszczędzić przepustowość i zwiększyć wydajność sieci, mogą mieć jednak również pewne negatywnego wpływu.  Należy ocenić wpływ na wydajność to ze względu na wymagania dodatkowego przetwarzania kompresji i dekompresji danych w kliencie. Ponadto przechowywania skompresowanych danych może utrudnić więcej do rozwiązywania problemów, ponieważ może to być trudniejsze do wyświetlenia przechowywanych danych przy użyciu standardowych narzędzi.
* Jeśli aplikacja trafienia wartości docelowe skalowalności, upewnij się, że używasz wykładniczego wycofywania ponownych prób (zobacz [ponownych prób](#subheading14)).  Warto upewnij się, że nigdy nie próbują wartości docelowe skalowalności (przy użyciu jednej z metod powyżej), ale to zagwarantować, że aplikacja nie tylko ponawiać szybko, co gorsza ograniczenie.  

#### <a name="useful-resources"></a>Przydatne zasoby
Poniższe linki udostępniają dodatkowe szczegóły na wartości docelowe skalowalności:

* Zobacz [cele dotyczące wydajności i skalowalności magazynu Azure](storage-scalability-targets.md) informacji o wartości docelowe skalowalności.
* Zobacz [replikacja usługi Azure Storage](storage-redundancy.md) i wpis w blogu [opcje nadmiarowość magazynu Azure i dostęp do odczytu z magazynu geograficznie nadmiarowego magazynu](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx) informacji o opcjach nadmiarowość magazynu.
* Aby uzyskać aktualne informacje o cenach dla usług Azure, zobacz [cennik platformy Azure](https://azure.microsoft.com/pricing/overview/).  

### <a name="subheading47"></a>Konwencja nazewnictwa partycji
Usługa Azure Storage wykorzystuje schemat partycjonowania opartej na zakresie równowagi skala i wielkość obciążenia systemu. Klucz partycji służy do partycji, które dane do zakresów i tych zakresów są równoważeniem obciążenia w systemie. Oznacza to, że konwencji nazewnictwa, takich jak leksykalne porządkowanie (np. msftpayroll, msftperformance, msftemployees itp.) lub przy użyciu sygnatury czasowe (log20160101, log20160102, log20160102 itp.) będą spowodować w przyszłości zmianę partycji umieszczone potencjalnie znajdujących się na tym samym Serwer partycji, dopóki operacja z równoważeniem obciążenia dzieli je na mniejsze zakresów. Na przykład wszystkie obiekty BLOB w kontenerze, mogą być udostępniane przez jeden serwer, dopóki obciążenia na te obiekty BLOB wymaga dalszych ponowne równoważenie zakresów partycji. Podobnie grupa kont lekkim załadowany z ich nazw, w kolejności leksykalne może być obsługiwana przez pojedynczego serwera do obciążenia na jednym lub wymagają wszystkie te konta można podzielić na wielu serwerach partycji. Każdej operacji równoważenia obciążenia może mieć wpływ na opóźnienie magazynu wywołań podczas operacji. Możliwość systemu obsługi nagłym serii ruchu do partycji jest ograniczona skalowalność serwera jednej partycji do momentu operacji funkcji równoważenia obciążenia w kopnięć i rebalances zakresem kluczy partycji.  

Możesz wykonać najważniejsze wskazówki, aby zmniejszyć częstotliwość takich operacji.  

* Sprawdź, czy używane w przypadku kont, kontenerów, obiektów blob, tabel i kolejek, ściśle konwencji nazewnictwa. Należy rozważyć prefiksu nazwy kont z 3-cyfrowy skrót przy użyciu funkcji skrótu, który najlepiej odpowiada Twoim potrzebom.  
* Organizowanie danych za pomocą znacznika czasu lub identyfikatory numeryczne, masz upewnij się, że nie używasz wzorce ruchu tylko Dołącz (lub tylko dołączenie wartości). Te wzorce nie są odpowiednie dla zakresu — na podstawie partycjonowania systemu i może prowadzić do całego ruchu, przechodząc do jednej partycji i efektywnie ograniczenie systemu z równoważenia obciążenia. Na przykład jeśli masz codziennych operacji korzystających z obiektu blob z sygnaturą czasową takich jak RRRRMMDD cały ruch do tego codziennej pracy jest kierowany do jednego obiektu, który jest obsługiwany przez serwer z jedną partycją. Sprawdź czy dla obiekt blob limity dla każdej partycji limity potrzeb i Podziel tej operacji w wielu obiektów blob, jeśli to konieczne. Podobnie jeśli czas serii danych są przechowywane w tabelach, wszystkie ruchu może być kierowany do ostatniego części klucza przestrzeni nazw. Jeśli musisz użyć sygnatury czasowe lub identyfikatory numeryczne, prefiks identyfikatora znakiem, 3-cyfrowy, lub w przypadku sygnatury czasowe prefiks część czasu, takich jak ssyyyymmdd sekund. Jeśli wykonywane są regularnie wyświetlania i badania operacje, wybierz funkcję wyznaczania wartości skrótu, która powoduje ograniczenie liczby zapytań. W innych przypadkach może być wystarczający losowe prefiks.  
* Dla dodatkowe informacje na temat schemat partycjonowania używane w magazynie Azure, przeczytaj dokument SOSP [tutaj](http://sigops.org/sosp/sosp11/current/2011-Cascais/printable/11-calder.pdf).

### <a name="networking"></a>Networking
Gdy sprawy wywołania interfejsu API, często ograniczeń sieci fizycznej aplikacji mieć znaczący wpływ na wydajność. Poniżej opisano niektóre ograniczenia, które użytkownicy mogą wystąpić.  

#### <a name="client-network-capability"></a>Możliwość sieci klienta
##### <a name="subheading2"></a>Przepływność
Przepustowość problem jest często możliwości klienta. Na przykład, gdy konto magazynu pojedynczego może obsłużyć 10 GB/s lub więcej transfer danych przychodzących (zobacz [wartości docelowe skalowalności przepustowości](#sub1bandwidth)), szybkość sieci w wystąpieniu roli procesu roboczego platformy Azure "Małe" obsługuje jedynie około 100 MB/s. Większy wystąpieniach platformy Azure ma kart sieciowych o większej pojemności, więc należy rozważyć użycie większych wystąpienia lub więcej maszyny Wirtualnej, jeśli potrzebujesz wyższe limity sieci z jednego komputera. Jeśli uzyskujesz dostęp do usługi magazynu z aplikacji w lokalnym, a następnie ta zasada dotyczy: zrozumieć funkcje sieci urządzeń klienckich i łączność sieciową do lokalizacji magazynu Azure i albo poprawić je w razie potrzeby bądź projektowania programu Aplikacja do pracy w ich możliwości.  

##### <a name="subheading3"></a>Jakość łącza
Podobnie jak w przypadku bez użycia sieci należy pamiętać, że warunków sieciowych, co zapewnia błędów i utraty pakietów spowolni skuteczne przepływności.  Za pomocą programu WireShark ani NetMon może pomóc w zdiagnozowaniu tego problemu.  

##### <a name="useful-resources"></a>Przydatne zasoby
Aby uzyskać więcej informacji na temat rozmiarów maszyn wirtualnych i przydzielonej przepustowości, zobacz [rozmiarów maszyn wirtualnych systemu Windows](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) lub [rozmiarów maszyn wirtualnych systemu Linux](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).  

#### <a name="subheading4"></a>Lokalizacja
W dowolnym środowisku rozproszonym umieszczenie klienta się w pobliżu serwer zapewnia najlepszą wydajność. Do uzyskiwania dostępu do usługi Azure Storage o najniższym opóźnieniu, najlepiej lokalizacji klienta jest w tym samym regionie Azure. Na przykład jeśli masz witryny sieci Web platformy Azure, która używa usługi Azure Storage, powinien oba te zlokalizować w pojedynczym regionie (na przykład nam zachodnie lub Azja południowo-wschodnia). Zmniejsza to opóźnienie i koszt — w czasie zapisywania, jest wolne przepustowości w pojedynczym regionie.  

Jeśli Twoje aplikacje nie znajdują się w obrębie platformy Azure (np. aplikacje urządzenia przenośnego lub lokalne usługi enterprise), następnie ponownie klienta umieszczenie konta magazynu w regionie się w pobliżu urządzenia, które będą miały dostęp, zazwyczaj zmniejsza opóźnienia. Jeśli klienci szeroko są dystrybuowane (na przykład niektóre w Ameryce Północnej, a niektóre w Europie), a następnie należy rozważyć użycie wielu kont magazynu: jeden znajdujący się w Ameryce Północnej regionu i co Europejskich regionu. Dzięki temu można zmniejszyć opóźnienia dla użytkowników w obu regionach. Ta metoda jest zwykle łatwiej można wdrożenie, jeśli dane aplikacji są przechowywane są specyficzne dla poszczególnych użytkowników i nie wymaga replikowania danych między kontami magazynu.  Do szerokiej dystrybucji zawartości zaleca się CDN — zobacz następną sekcję, aby uzyskać więcej informacji.  

### <a name="subheading5"></a>Dystrybucja zawartości
Czasami aplikacja musi obsługiwać tę samą zawartość do wielu użytkowników (np. produktu pokaz wideo użyty na stronie głównej witryny sieci Web), znajdujące się w tym samym lub wielu regionach. W tym scenariuszu należy użyć dostarczania sieci zawartości (CDN) takie jak usługi Azure CDN, a CDN użyje magazynu Azure jako źródła danych. W przeciwieństwie do konta usługi Azure Storage znajdujące się w pojedynczym regionie, który może dostarczyć zawartość z niskim opóźnieniem do innych regionów usługi Azure CDN używa serwerów w wielu centrach danych na całym świecie. Ponadto CDN zazwyczaj może obsługiwać znacznie wyższe limity wyjście niż konto jednego magazynu.  

Aby uzyskać więcej informacji na temat usługi Azure CDN, zobacz [Azure CDN](https://azure.microsoft.com/services/cdn/).  

### <a name="subheading6"></a>Przy użyciu sygnatury dostępu Współdzielonego i mechanizmu CORS
Gdy musisz autoryzować kodu, takich jak JavaScript w przeglądarce sieci web lub aplikacji telefonu komórkowego uzyskują dostęp do danych w usłudze Azure Storage, jednym z podejść jest użycie aplikacji w roli sieci web jako serwer proxy: urządzenie użytkownika jest uwierzytelniany w usłudze roli sieci web , który z kolei jest uwierzytelniany w usłudze usługi magazynu. W ten sposób można uniknąć, udostępnianie kluczy konta magazynu na urządzeniach niezabezpieczonych. Jednak ten zostaje umieszczony duże obciążenie w roli sieci web ponieważ wszystkich danych przesyłanych między usług magazynu i urządzeń użytkownika musi przechodzić przez rolę sieci web. Możesz uniknąć używania roli sieci web jako serwer proxy usługi magazynu przy użyciu dostępu sygnatur dostępu Współdzielonego, czasami w połączeniu z nagłówkami współużytkowanie zasobów między źródłami (CORS). Przy użyciu sygnatury dostępu Współdzielonego, można zezwolić na urządzeniu użytkownika na wysyłanie żądań bezpośrednio do usługi magazynu za pomocą tokenu ograniczony dostęp. Na przykład jeśli użytkownik chce Przekaż zdjęcie do aplikacji, roli sieci web można wygenerować i wysłać do urządzenia użytkownika tokenu sygnatury dostępu Współdzielonego, który przyznaje uprawnienia do zapisu do konkretnego obiektu blob lub kontener dalej 30 minut (po którym token sygnatury dostępu Współdzielonego wygasa).

Zwykle przeglądarce nie zezwala na JavaScript na stronie hostowanej przez witrynę sieci Web w jednej domenie wykonywanie określonych operacji, takich jak "PUT" do innej domeny. Na przykład, jeśli host rolę sieci web w "contosomarketing.cloudapp.net" i chcesz użyć JavaScript po stronie klienta w celu przekazania obiektu blob na koncie magazynu na "contosoproducts.blob.core.windows.net," przeglądarki w "tego samego źródła policy" będzie zabraniać tej operacji. CORS jest funkcją przeglądarki, która pozwala domeny docelowej (w tym przypadku konta magazynu) na komunikowanie się w przeglądarce, że subskrypcja ufa żądań pochodzących z domeny źródłowej (w tym przypadku roli sieci web).  

Oba te technologie mogą pomóc uniknąć niepotrzebnego obciążenia (i wąskich gardeł) w aplikacji sieci web.  

#### <a name="useful-resources"></a>Przydatne zasoby
Aby uzyskać więcej informacji na temat sygnatury dostępu Współdzielonego, zobacz [sygnatury dostępu współdzielonego, część 1: opis modelu sygnatur dostępu Współdzielonego](../storage-dotnet-shared-access-signature-part-1.md).  

Aby uzyskać więcej informacji na temat CORS, zobacz [udostępniania zasobów między źródłami (CORS) obsługę usług magazynu Azure](http://msdn.microsoft.com/library/azure/dn535601.aspx).  

### <a name="caching"></a>Buforowanie
#### <a name="subheading7"></a>Pobieranie danych
Ogólnie rzecz biorąc pobieranie danych z usługą raz jest lepszym rozwiązaniem niż pobierania ich dwa razy. Rozważmy przykładową aplikację sieci web MVC działającą w roli sieci web została już pobrana obiektu blob 50MB z usługi magazynowania jako zawartości dla użytkownika. Aplikacja może następnie pobrać tego samego obiektu blob, za każdym razem, gdy użytkownik zażąda go lub go można buforować lokalnie na dysku i ponowne użycie pamięci podręcznej wersji kolejne żądania użytkowników. Ponadto gdy użytkownik zażąda danych aplikacji można problem UZYSKAĆ z nagłówkiem warunkowego czas modyfikacji, który będzie uniknąć pobierania całego obiektu blob, jeśli nie została zmodyfikowana. Można użyć tego samego wzorca do pracy z jednostek tabeli.  

W niektórych przypadkach może się okazać, że aplikacja może przyjmować obiektu blob pozostaje ważny przez krótki czas, po jej pobraniu i że w tym okresie aplikacji nie trzeba sprawdzić, czy został zmodyfikowany obiektu blob.

Konfiguracja, wyszukiwania i inne dane, które są zawsze używane przez aplikację są obiekty doskonale nadające się do buforowania.  

Na przykład jak uzyskać właściwości obiektu blob, aby odnaleźć daty ostatniej modyfikacji, przy użyciu platformy .NET, zobacz [zestawu i pobrać właściwości i metadane](../blobs/storage-properties-metadata.md). Aby uzyskać więcej informacji na temat warunkowego pliki do pobrania, zobacz [warunkowo Odśwież lokalną kopię obiektu Blob](http://msdn.microsoft.com/library/azure/dd179371.aspx).  

#### <a name="subheading8"></a>Przekazywanie danych w partiach
W niektórych scenariuszach aplikacji agregowanie danych lokalnie i następnie okresowo przekaż go w partii, zamiast natychmiast przekazywania każdego elementu danych. Na przykład aplikacja sieci web może przechowywać plik dziennika działań: aplikacji albo można przekazać szczegółowe informacje o każdym działaniu, ponieważ występuje jako element tabeli (co wymaga wielu operacji magazynu), lub można go zapisać szczegóły aktywności do lokalnego pliku dziennika, a następnie Okresowo należy przekazać wszystkie szczegóły aktywności jako rozdzielonym pliku do obiektu blob. W przypadku każdego wpisu dziennika o rozmiarze 1KB rozmiaru, możesz przekazać tysięcy w ramach jednej transakcji "Umieszczanie obiektu Blob" (możesz przekazać obiekt blob rozmiar w ramach pojedynczej transakcji do 64MB). Oczywiście jeśli komputer lokalny ulegnie awarii przed przekazywania, potencjalnie utracisz niektóre dane dziennika: Deweloper aplikacji musi projektowania możliwości urządzeń klienckich lub przekazywanie błędów.  Jeśli dane działanie muszą zostać pobrane dla timespans (nie tylko pojedyncze działanie), obiekty BLOB są zalecane przez tabel.

### <a name="net-configuration"></a>Konfiguracja .NET
Jeśli przy użyciu programu .NET Framework, w tej sekcji przedstawiono kilka ustawień konfiguracyjnych szybki, których można dokonać znaczną poprawę wydajności.  Jeśli korzystanie z innych języków, sprawdź, czy podobne uniwersalne w wybranym języku.  

#### <a name="subheading9"></a>Zwiększenia domyślnego limitu połączenia
W środowisku .NET poniższy kod zwiększa domyślny limit połączeń (która jest zazwyczaj 2 w środowisku klienta lub 10 w środowisku serwera) do 100. Zazwyczaj należy ustawić wartość do około liczbę wątków używanych przez aplikację.  

```csharp
ServicePointManager.DefaultConnectionLimit = 100; //(Or More)  
```

Należy ustawić limit połączeń przed otwarciem wszystkie połączenia.  

Dla innych języków programowania zobacz dokumentację tego języka Aby określić, jak ustawić limit połączeń.  

Aby uzyskać dodatkowe informacje, zobacz we wpisie blogu [usług sieci Web: równoczesnych połączeń](http://blogs.msdn.com/b/darrenj/archive/2005/03/07/386655.aspx).  

#### <a name="subheading10"></a>Zwiększ wątków Min puli wątków, jeśli przy użyciu synchronicznej kodu z zadań asynchronicznych
Ten kod spowoduje zwiększenie wątków z puli wątków min:  

```csharp
ThreadPool.SetMinThreads(100,100); //(Determine the right number for your application)  
```

Aby uzyskać więcej informacji, zobacz [metody ThreadPool.SetMinThreads](http://msdn.microsoft.com/library/system.threading.threadpool.setminthreads%28v=vs.110%29.aspx).  

#### <a name="subheading11"></a>Korzystać z platformy .NET 4.5 wyrzucanie elementów bezużytecznych
Umożliwia .NET 4.5 lub nowszej do aplikacji klienckiej, korzystając z usprawnień wydajności w pamięci serwera.

Aby uzyskać więcej informacji, zobacz artykuł [Omówienie programu poprawy wydajności w programie .NET 4.5](http://msdn.microsoft.com/magazine/hh882452.aspx).  

### <a name="subheading12"></a>Niepowiązane równoległości
Równoległość mogą być doskonałą wydajność, należy zachować ostrożność przekazywanie lub pobieranie danych przy użyciu wielu procesów roboczych na dostęp do wielu partycji przy użyciu niepowiązany równoległości (brak limitu liczby wątków i/lub równoległych żądań) (kontenerów, kolejki, lub Tabela partycji) w tym samym koncie magazynu lub dostępu do wielu elementów w tej samej partycji. W przypadku niepowiązanego równoległości, aplikacja może przekroczyć możliwości urządzeń klienckich lub skalowalności konta magazynu elementów docelowych, co powoduje dłuższe czasy oczekiwania i ograniczania przepustowości.  

### <a name="subheading13"></a>Narzędzia i biblioteki klienta magazynu
Należy zawsze używać najnowszej bibliotek klienta i narzędzia firmy Microsoft. W czasie zapisywania są dostępne dla platformy .NET, Windows Phone środowiska wykonawczego systemu Windows, Java i C++ bibliotek klienta, a także biblioteki podglądu dla innych języków. Ponadto firma Microsoft wydała poleceń cmdlet programu PowerShell i polecenia wiersza polecenia platformy Azure do pracy z usługą Azure Storage. Microsoft aktywnie rozwija tych narzędzi z wydajnością na uwadze, jednocześnie dbając o ich na bieżąco z najnowszymi wersjami usługi i gwarantuje, że wiele praktyk sprawdzonych wydajności wewnętrznie obsługują.  

### <a name="retries"></a>Ponowne próby
#### <a name="subheading14"></a>Ograniczanie ServerBusy
W niektórych przypadkach usługa magazynu może ograniczyć aplikacji lub może się zdarzyć, nie można obsłużyć żądania ze względu na niektórych stan przejściowy i zwraca komunikat "503 Serwer jest zajęty" lub "500 Timeout".  Może to nastąpić, jeśli aplikacja zbliża się do dowolnej wartości docelowe skalowalności lub jeśli system jest ponowne równoważenie dane podzielonym na partycje, aby umożliwić wyższej przepustowości.  Aplikacja kliencka zazwyczaj ponów operację, powodujący takie wystąpił błąd: próba jednym żądaniu później będzie możliwe. Jednak usługa magazynu jest ograniczanie aplikacji, ponieważ jest powyżej wartości docelowe skalowalności lub nawet wtedy, gdy usługa nie może obsłużyć żądania z innego powodu, agresywne ponownych prób zwykle należy co gorsza problem. Z tego powodu należy używać wykładniczej wycofania (klient biblioteki ustawienie domyślne to zachowanie). Na przykład aplikacja może ponów próbę po 2 sekundy 4 sekundy, a następnie 10 sekund, a następnie 30 sekund, a następnie nadaj całkowicie. Powoduje to zachowanie aplikacji znacznie zmniejszyć jego obciążenia w usłudze zamiast powiększając istniejące problemów.  

Należy pamiętać, że błędy połączenia można ponowić natychmiast, ponieważ nie są wynikiem ograniczania przepustowości i powinny być przejściowy.  

#### <a name="subheading15"></a>Niepowtarzającego — błędy
Znane błędy, które mogą ponownych prób, a które nie są są bibliotek klienckich. Podczas pisania kodu przed interfejs API REST magazynu, pamiętaj, występują błędy, które nie powinny ponawiania: na przykład 400 (nieprawidłowe żądanie) odpowiedź wskazuje, czy aplikacja kliencka wysłane żądanie, które nie może być przetworzone, ponieważ nie było w formularzu oczekiwanego. Ponowne wysyłanie żądania spowoduje, że ta sama odpowiedź zawsze, więc nie ma żadnego punktu w ponawianie próby jego. Podczas pisania kodu przed interfejs API REST magazynu, należy pamiętać, co oznaczają kody błędów i odpowiednie sposób, aby ponowić próbę (lub nie) dla każdego z nich.  

#### <a name="useful-resources"></a>Przydatne zasoby
Aby uzyskać więcej informacji o kodach błędów magazynu, zobacz [stanu i kodów błędów](http://msdn.microsoft.com/library/azure/dd179382.aspx) w witrynie sieci web Microsoft Azure.  

## <a name="blobs"></a>Obiekty blob
Oprócz sprawdzonych rozwiązań dla [wszystkie usługi](#allservices) opisanych powyżej, poniżej sprawdzonych rozwiązań dotyczą przede wszystkim usługi blob.  

### <a name="blob-specific-scalability-targets"></a>Wartości docelowe skalowalności specyficzne dla obiektów blob
#### <a name="subheading46"></a>Wielu klientów jednocześnie dostęp do pojedynczego obiektu
Jeśli masz dużą liczbę klientów uzyskujących dostęp do pojedynczego obiektu jednocześnie należy wziąć pod uwagę na wartości docelowe skalowalności konta obiekt i magazynu. Dokładna liczba klientów, którzy mogą uzyskiwać dostęp do pojedynczego obiektu będą się różnić w zależności od czynników, takich jak liczba klientów żądać obiektu jednocześnie, rozmiar obiektu, sieci warunki itp.

Jeśli obiekt może być dystrybuowane za pośrednictwem obsługiwanych CDN, taką jak obrazy lub filmy wideo z witryny internetowej, a następnie należy używać sieci CDN. Zobacz [tutaj](#subheading5).

W innych sytuacjach, takich jak symulacje naukowych, gdzie jest poufne dane dostępne są dwie opcje. Pierwsza to aby przesunąć dostępu właściwe dla obciążenia w taki sposób, że obiekt jest dostępny w danym okresie czasu vs uzyskiwany jednocześnie. Alternatywnie możesz tymczasowo skopiować obiekt do wielu kont magazynu, co pozwala zwiększyć całkowitą liczbę IOPS dla każdego obiektu i wielu kont magazynu. Podczas testowania ograniczone znaleziono, że około 25 maszyn wirtualnych jednocześnie można pobrać obiektu blob 100GB, równoległe (każda maszyna wirtualna została parallelizing pobierania za pomocą wątków 32). Jeśli użytkownik ma 100 klientów wymagające dostępu do obiektu, najpierw skopiować go do drugiego konta magazynu i mieć najpierw 50 maszyn wirtualnych dostęp pierwszy obiekt blob i drugi 50 dostęp maszyny wirtualne drugiej obiektów blob. Wyniki różni się zależnie od Twoje zachowanie aplikacji, więc należy przetestować, to podczas projektowania. 

#### <a name="subheading16"></a>Przepustowości i operacji na obiektów Blob
Można odczytać lub zapisać do pojedynczego obiektu blob na maksymalnie 60 MB na sekundę (jest to około 480 MB/s przekracza możliwości wiele sieci po stronie klienta (łącznie z fizycznej karty Sieciowej na urządzeniu klienckim). Ponadto pojedynczego obiektu blob obsługuje maksymalnie 500 żądań na sekundę. Jeśli wielu klientów, którzy potrzebują do odczytu tego samego obiektu blob i może zostać przekroczona tych ograniczeń, należy rozważyć przy użyciu CDN dystrybucji obiektu blob.  

Aby uzyskać więcej informacji o przepływności docelowej dla obiektów blob, zobacz [cele dotyczące wydajności i skalowalności magazynu Azure](storage-scalability-targets.md).  

### <a name="copying-and-moving-blobs"></a>Kopiowanie i przenoszenie obiektów blob
#### <a name="subheading17"></a>Copy Blob
Przydatne możliwość kopiowania obiektów blob na kontach wprowadzone magazynu interfejsu API REST wersji 2012-02-12: aplikacja kliencka poinstruować usługę magazynu można skopiować obiektu blob z innego źródła (prawdopodobnie w innego konta magazynu), a następnie umożliwił wykonaj usługi Kopia asynchronicznie. Może to znacznie zmniejszyć przepustowość potrzebne dla aplikacji, gdy są migrację danych z innych kont magazynu, ponieważ nie trzeba pobrać i przekazać dane.  

Pierwsza kwestia, jest jednak, że jeśli kopiowanie odbywa się między kontami magazynu, nie ma żadnej gwarancji czas na podczas kopiowania zostanie zakończony. Jeśli aplikacja musi wykonać kopiowania obiektów blob, szybko pod kontrolą, lepszym rozwiązaniem byłoby można skopiować obiektu blob, pobierając go z maszyną wirtualną i przekazać go do lokalizacji docelowej.  W przypadku pełnego przewidywalności w takiej sytuacji upewnij się, że kopię jest wykonywane przez maszynę Wirtualną w tym samym regionie Azure, w przeciwnym razie warunków sieciowych może i prawdopodobnie zostaną mają wpływ na wydajność kopii.  Ponadto można monitorować postęp asynchroniczne kopiowania programowo.  

Należy pamiętać, kopie w ramach tego samego konta magazynu, sama są zazwyczaj zakończenie szybko.  

Aby uzyskać więcej informacji, zobacz [kopiowania obiektu Blob](http://msdn.microsoft.com/library/azure/dd894037.aspx).  

#### <a name="subheading18"></a>Przy użyciu programu AzCopy
Zespół usługi Azure Storage wydała narzędzie wiersza polecenia "AzCopy" który mają na celu pomoc zbiorczego transferu wiele obiektów blob do z i wielu kont magazynu.  To narzędzie jest zoptymalizowana w tym scenariuszu i można osiągnąć dużą szybkością.  Zaleca się jego użycie zbiorczego przekazywanie, pobieranie i scenariuszy kopii. Aby uzyskać więcej informacji i pobierz go, zobacz [Transfer danych za pomocą wiersza polecenia Azcopy](storage-use-azcopy.md).  

#### <a name="subheading19"></a>Usługa Azure Import/Eksport
Bardzo duże woluminy danych (więcej niż 1TB) Magazyn Azure oferuje usługi Import/eksport, która umożliwia wysyłanie i pobieranie z magazynu obiektów blob wysyłanie dysków twardych.  Można dane są umieszczane na dysku twardym i wysyłać do firmy Microsoft, przekazywania lub wysyłać pusty dysk twardy do firmy Microsoft do pobierania danych.  Aby uzyskać więcej informacji, zobacz [korzystanie z usługi Microsoft Azure importu/eksportu przesyłanie danych do magazynu obiektów Blob](../storage-import-export-service.md).  Może to być znacznie skuteczniejsza niż przekazywanie/pobieranie ten wolumin danych za pośrednictwem sieci.  

### <a name="subheading20"></a>Używanie metadanych
Usługa blob obsługuje żądania head, które mogą zawierać metadane dotyczące obiektów blob. Na przykład w razie potrzeby danych EXIF poza zdjęcie aplikacji go można pobrać zdjęcia i wyodrębnij go. Aby oszczędzić przepustowość i zwiększyć wydajność, aplikacja może zapisać danych EXIF w metadane obiektu blob, gdy aplikacja przekazywane zdjęcie: można następnie pobrać danych EXIF w metadanych za pomocą tylko żądania HEAD, zapisywanie znaczących przepustowości i czas przetwarzania potrzebne, aby wyodrębnić dane EXIF zawsze obiektu blob jest do odczytu. Powinien to być przydatne w scenariuszach, w których należy tylko metadane i nie pełnej zawartości obiektu blob.  Należy pamiętać, że tylko 8 KB metadanych mogą być przechowywane na obiekt blob (usługa nie akceptuje żądań do magazynu większą), więc jeśli dane nie mieści się w tym rozmiar, nie można używać tej metody.  

Na przykład sposobu uzyskania metadane obiektu blob przy użyciu platformy .NET, zobacz [zestawu i pobrać właściwości i metadane](../blobs/storage-properties-metadata.md).  

### <a name="uploading-fast"></a>Przekazywanie Fast
Szybko przekazać obiektów blob, odpowiedzi na pierwsze pytanie jest: czy przekazywanie obiektu blob jeden lub wiele?  Użyj poniższych wskazówek, aby określić poprawną metodę, w zależności od danego scenariusza.  

#### <a name="subheading21"></a>Szybkie przekazywanie jednego dużych obiektów blob
Szybko przekazać jeden duży obiekt blob, aplikacja kliencka przekazać jego bloków lub stron równoległe (co w trosce o wartości docelowe skalowalności poszczególne obiekty BLOB i konto magazynu jako całość).  Należy zauważyć, że oficjalnego bibliotek udostępnionych przez firmę Microsoft klienta RTM magazynu (.NET, Java) możliwość to zrobić.  Dla każdego z bibliotekami używać poniżej określonego obiektu/właściwości można ustawić poziomu współbieżności:  

* .NET: Zestaw ParallelOperationThreadCount w obiekcie BlobRequestOptions do użycia.
* Java/Android: Use BlobRequestOptions.setConcurrentRequestCount()
* Node.js: Użyj parallelOperationThreadCount opcji żądania lub usługi blob.
* C++: Metoda blob_request_options::set_parallelism_factor.

#### <a name="subheading22"></a>Szybko przekazać wiele obiektów blob
Aby szybko przekazać wiele obiektów blob, Przekaż obiekty BLOB równolegle. Jest to szybsza niż przekazywanie jednego obiekty BLOB w czasie z bloku równoległego przekazywania ponieważ rozprzestrzenia się przekazywania między wieloma partycjami usługi magazynu. Pojedynczego obiektu blob obsługuje tylko przepustowości 60 MB na sekundę (około 480 MB/s). W czasie zapisywania konta amerykańskiej LRS obsługuje maksymalnie 20 wejściowych GB/s, czyli znacznie większe niż obsługiwana przez poszczególnych obiektów blob.  [Narzędzie AzCopy](#subheading18) wykonuje przekazywanie równoległe domyślnie i jest zalecany dla tego scenariusza.  

### <a name="subheading23"></a>Wybierając odpowiedni typ obiektów blob
Usługa Azure Storage obsługuje dwa typy obiektów blob: *strony* obiekty BLOB i *bloku* obiektów blob. Użycie danego scenariusza wybór typu obiektu blob wpłynie na wydajności i skalowalności rozwiązania. Blokowe obiekty BLOB są odpowiednie, jeśli chcesz przekazać wydajnie dużych ilości danych: na przykład aplikacja kliencka może być konieczne do przekazania do magazynu obiektów blob zdjęć i wideo. Stronicowe obiekty BLOB są odpowiednie, jeśli aplikacja musi przeprowadzić losowe zapisy na danych: na przykład Azure wirtualne dyski twarde są przechowywane jako stronicowych obiektów blob.  

Aby uzyskać więcej informacji, zobacz [opis blokowych obiektów blob, Dołącz obiektów blob i stronicowe obiekty BLOB](http://msdn.microsoft.com/library/azure/ee691964.aspx).  

## <a name="tables"></a>Tabele
Oprócz sprawdzonych rozwiązań dla [wszystkie usługi](#allservices) opisanych powyżej, poniżej sprawdzonych rozwiązań dotyczą przede wszystkim usługi tabel.  

### <a name="subheading24"></a>Wartości docelowe skalowalności specyficzna dla tabeli
Oprócz ograniczenia przepustowości całe konto magazynu tabele mają następujące ograniczenia określonych skalowalności.  Należy pamiętać, system będzie równoważenia obciążenia jako Twoje wzrost ruchu, że jeśli seria nagłym ruchu, nie można natychmiast uzyskać ten wolumin przepływności.  Jeśli deseń seria, należy się spodziewać ograniczania przepustowości i/lub przekroczenia limitu czasu podczas serii jako magazyn usługi automatycznie równoważy obciążenie limit tabeli.  Rozwijanie powoli zwykle ma lepsze wyniki jako udostępnia czas systemowy na potrzeby równoważenia obciążenia odpowiednio.  

#### <a name="entities-per-second-account"></a>Jednostek na sekundę (konto)
Limit skalowalności do uzyskiwania dostępu do tabel jest maksymalnie 20 000 jednostek (o rozmiarze 1KB każdego) na drugi dla konta.  Ogólnie rzecz biorąc każdy obiekt, który dodaje się zaktualizowany, usunięty lub skanowania liczby kierunku ten element docelowy.  Dlatego Wstawianie wsadowe, która zawiera 100 jednostek jest traktowane jako 100 jednostek.  Zapytanie, które skanuje 1000 jednostek i zwraca 5 jest traktowane jako 1000 jednostek.  

#### <a name="entities-per-second-partition"></a>Jednostek na sekundę (partycja)
W ramach jednej partycji docelowej skalowalność do uzyskiwania dostępu do tabel jest 2000 jednostek (1KB) na sekundę, przy użyciu tego samego zliczanie zgodnie z opisem w poprzedniej sekcji.  

### <a name="configuration"></a>Konfigurowanie
W tej sekcji przedstawiono kilka ustawień konfiguracji szybki, które można udostępnić znaczną poprawę wydajności w usłudze tabel:  

#### <a name="subheading25"></a>Użyj formatu JSON
Począwszy od wersji usług magazynu 2013-08-15, usługa tabel obsługuje za pomocą JSON zamiast formatu AtomPub opartych na języku XML do przenoszenia danych z tabeli. Można zmniejszyć rozmiar ładunku jak 75% i może znacznie poprawić wydajność aplikacji.

Aby uzyskać więcej informacji, zobacz wpis [Microsoft Azure tabel: wprowadzenie JSON](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/05/windows-azure-tables-introducing-json.aspx) i [Format ładunku dla operacji usługi tabeli](http://msdn.microsoft.com/library/azure/dn535600.aspx).

#### <a name="subheading26"></a>Nagle'a wyłączone
Algorytm Nagle'a firmy jest często stosowana w sieciach TCP/IP w celu zwiększenia wydajności sieci. Jednak nie jest optymalne we wszystkich okolicznościach (na przykład wysokiej interakcyjne środowiska). Usługi Azure Storage algorytm Nagle'a firmy ma negatywny wpływ na wydajność żądań do tabeli i kolejki usług i należy go wyłączyć, jeśli to możliwe.  

Aby uzyskać więcej informacji, zobacz nasze blogu [nie przyjazną kierunku małych żądań jest algorytm Nagle'a w](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/06/25/nagle-s-algorithm-is-not-friendly-towards-small-requests.aspx), co wyjaśnia, dlaczego algorytm Nagle'a w słabo współdziała z tabeli i kolejki żądań i pokazuje, jak ją wyłączyć na kliencie aplikacja.  

### <a name="schema"></a>Schemat
Jak reprezentują i wyszukiwanie danych jest najważniejszych pojedynczego czynnik, który wpływa na wydajność usługi tabel. Gdy każda aplikacja jest inny, w tej sekcji opisano niektóre ogólne wskazówki sprawdzonych, które odnoszą się do:  

* Projektowaniu tabel
* Wydajność kwerendy
* Aktualizacje wydajne danych  

#### <a name="subheading27"></a>Tabele i partycji
Tabele są podzielone na partycje. Każdy podmiot przechowywane w partycji współużytkują ten sam klucz partycji i ma klucz wiersza unikatowy, aby zidentyfikować go w ramach tej partycji. Partycje zapewniają korzyści, ale również wprowadzić limity skalowalności.  

* Korzyści: Można aktualizować jednostek w tej samej partycji w jednym, atomic, partii transakcji, która zawiera maksymalnie 100 przechowywania operacji (limit to łączny rozmiar 4MB). Zakładając, że taką samą liczbę jednostek, które mają zostać pobrane, możesz także zbadać danych w jednej partycji wydajniej danych obejmującej partycji (mimo że Dowiedz się więcej zaleceń dotyczących przeszukiwaniem danych w tabeli).
* Limit skalowalności: dostęp do jednostek przechowywanych w jednej partycji nie może być równoważeniem obciążenia ponieważ partycje obsługuje atomic partii transakcji. Z tego powodu cel skalowalność dla pojedynczej tabeli partycji jest niższa niż usługi tabeli jako całość.  

Ze względu na tę charakterystykę tabel i partycje powinna przyjąć następujące zasady projektowania:  

* Dane aplikacji klienckiej często zaktualizowane lub wykonać zapytania w tej samej jednostki logicznej pracy powinien znajdować się w tej samej partycji.  Może to być, ponieważ aplikacja jest agregowanie zapisy lub chcesz korzystać z operacji wsadowych atomic.  Ponadto danych w jednej partycji mogą być bardziej wydajne wyszukiwane w pojedynczego zapytania niż dane na partycje.
* Dane aplikacji klienckiej nie wstawiania/aktualizacji lub zapytania w tej samej jednostki logicznej pracy (pojedynczego zapytania lub aktualizację partii) powinien znajdować się w osobnych partycji.  Jeden ważna uwaga jest, że nie ma żadnego limitu liczby kluczy partycji w jednej tabeli, więc o miliony kluczy partycji nie jest problem i nie ma wpływu na wydajność.  Na przykład jeśli aplikacja jest popularnych witryny sieci Web z nazwą logowania użytkownika, za pomocą identyfikatora użytkownika jako klucza partycji może być dobrym rozwiązaniem.  

#### <a name="hot-partitions"></a>Partycje dynamicznej
Gorących partycji jest taki, który odbiera nieproporcjonalnie ruchu do konta, a nie może być o zrównoważonym obciążeniu ponieważ jest on jednej partycji.  Ogólnie rzecz biorąc gorących partycje są tworzone dwa sposoby:  

##### <a name="subheading28"></a>Tylko dołączyć i dołączy tylko wzorce
Wzorzec "Dołącz tylko" jest jednym gdzie (większość lub wszystkie) ruchu na PK danego ułatwia i zmniejsza zgodnie z bieżącą godzinę.  Przykładem jest, jeśli jako klucza partycji dla danych dziennika aplikacji używana bieżącą datę.  W efekcie wszystkie wstawia przerywaj ostatniej partycji w tabeli, a system nie może załadować saldo, ponieważ wszystkie zapisów będzie koniec tabeli.  Jeśli wielkość ruchu sieciowego do tej partycji przekracza docelowy skalowalność poziomu partycji, a następnie spowoduje ograniczania.  Zaleca się upewnić się, że ruch wysyłany do wielu partycji, aby włączyć usługę równoważenia obciążenia żądań w tabeli.  

##### <a name="subheading29"></a>Dane o dużym natężeniu ruchu
Jeśli Twoje schemat partycjonowania powoduje jednej partycji, zawierający tylko dane używane znacznie bardziej niż pozostałe partycje, może również zostać wyświetlony ograniczenie w postaci partycji zbliża się cel skalowalność dla jednej partycji.  Zaleca się upewnić się, że schematem partycji powoduje nie jednej partycji zbliża się wartości docelowe skalowalności.  

#### <a name="querying"></a>Wykonywanie zapytania
W tej sekcji opisano sprawdzonych rozwiązań do wykonywania zapytań w usłudze tabel.  

##### <a name="subheading30"></a>Zakres kwerendy
Istnieje kilka sposobów, aby określić zakres jednostek do zapytania.  Poniżej przedstawiono omówienie używa każdego z nich.  

Ogólnie rzecz biorąc uniknąć skanowania (większe niż pojedynczy element zapytania), ale musi podczas skanowania, spróbuj organizowania danych tak, aby Twoje skanowania pobierać dane, które wymagają bez skanowania i zwracanie znacznej ilości jednostek, które nie są potrzebne.  

###### <a name="point-queries"></a>Punkt zapytań
Zapytanie punktu pobiera dokładnie jedną jednostkę. Robi to przez określenie klucz partycji i klucz wiersza jednostki do pobrania. Te zapytania są bardzo wydajny i należy ich używać, gdy jest to możliwe.  

###### <a name="partition-queries"></a>Zapytania partycji
Zapytanie partycji jest kwerendę, która pobiera zestawu danych, które współużytkują wspólnego klucza partycji. Zazwyczaj zapytanie określa zakres wartości klucza wiersza lub zakres wartości dla niektórych właściwości jednostki, oprócz klucza partycji. Te są mniej wydajne niż punkt zapytań i powinny być używane rzadko.  

###### <a name="table-queries"></a>Zapytania w tabeli
Zapytanie tabeli jest kwerendę, która pobiera zestaw jednostek, który nie współużytkują wspólnego klucza partycji. Te zapytania nie są wydajne i nie należy ich, jeśli to możliwe.  

##### <a name="subheading31"></a>Gęstość zapytania
Innym czynnikiem klucza wydajności zapytania jest liczba zwróconych w porównaniu do liczby jednostek skanowania, aby znaleźć zestaw zwrócony. Jeśli aplikacja przeprowadza zapytanie tabeli z filtrem dla wartości właściwości tylko 1% udziałów danych, zapytanie będzie zeskanować 100 jednostek dla każdej jednostki jeden, zwracana. Wartości docelowe skalowalności tabeli omówionych wcześniej wszystkich odnoszą się do liczby jednostek skanowania, a nie liczba zwróconych: gęstość niski zapytania łatwo może spowodować usługi tabel do ograniczania aplikacji, ponieważ musi skanować tak wiele podmiotów pobiera obiekt, do którego szukasz.  Zobacz sekcję poniżej na [denormalization](#subheading34) Aby uzyskać więcej informacji na temat tego uniknąć.  

##### <a name="limiting-the-amount-of-data-returned"></a>Ograniczenia ilości zwracanych danych
###### <a name="subheading32"></a>Filtrowanie
W przypadku, gdy wiesz, że kwerendy zwróci jednostek, które nie wymagają aplikacji klienckiej, należy wziąć pod uwagę przy użyciu filtru, aby zmniejszyć rozmiar zwróconego zestawu. Podczas jednostki nie jest zwracana do klienta wliczane nadal limity skalowalności, ze względu na rozmiar ładunku zmniejszenie sieci i ograniczoną liczbę jednostek spełniających aplikacja kliencka musi przetworzyć poprawia wydajność aplikacji.  Można znaleźć w powyższym Uwaga na [gęstość zapytania](#subheading31), jednak — wartości docelowe skalowalności odnoszą się do liczby jednostek skanowania, więc zapytania, który odfiltrowuje wiele jednostek może nadal spowodować ograniczania przepustowości, nawet jeśli są zwracane jednostki kilku.  

###### <a name="subheading33"></a>Projekcji
Jeśli aplikacja kliencka musi tylko ograniczony zestaw właściwości z jednostek w tabeli, można użyć projekcji Aby ograniczyć rozmiar zwróconego zestawu danych. Podobnie jak w przypadku filtrowania, pozwala to zmniejszyć obciążenie sieci i przetwarzania klienta.  

##### <a name="subheading34"></a>Denormalization
W przeciwieństwie do pracy z relacyjnych baz danych, sprawdzonych rozwiązań dla wydajnie przeszukiwaniem danych w tabeli prowadzić do denormalizing danych. Oznacza to, powielanie tych samych danych w wielu jednostek (po jednym dla każdego klucza, aby znaleźć dane, można użyć) aby zminimalizować liczbę jednostek spełniających kwerendy skanowania musi znaleźć dane klienta musi, zamiast czekać, aż do skanowania dużej liczby jednostek, aby znaleźć dane aplikacji wymaga instalowania aplikacji.  Na przykład w witrynie internetowej handlu elektronicznego, warto znajdowanie zamówienia obu według Identyfikatora klienta (Udostępnij ten zamówienia) oraz według daty (Udostępnij zamówień w dniu).  W magazynie tabel, warto przechowywać jednostka (lub odwołanie do niej) dwukrotnie — raz z nazwą tabeli klucza podstawowego i RK, aby ułatwić znajdowanie przez klienta IDENTYFIKATORA, raz ułatwia on Znajdowanie według daty.  

#### <a name="insertupdatedelete"></a>Wstawiania/aktualizowania/usuwania
W tej sekcji opisano sprawdzonych rozwiązań modyfikowania jednostek przechowywanych w usłudze tabel.  

##### <a name="subheading35"></a>Przetwarzanie wsadowe
Partie transakcji są określane jako jednostki grupy transakcji (ETG) w usłudze Azure Storage; wszystkie operacje w ramach ETG musi znajdować się na jednej partycji w jednej tabeli. Jeśli to możliwe, należy użyć ETGs do wykonania operacji wstawiania, aktualizacji i usunięć w partiach. To zmniejsza liczbę rund aplikację klienta na serwerze, zmniejsza liczbę transakcji rozliczeniowy (ETG traktowana jako pojedynczej transakcji na potrzeby rozliczeń i może zawierać maksymalnie 100 operacje magazynu) i umożliwia atomic aktualizacje, (wszystkie wersje operacje powiedzie się lub nie powiedzie się w obrębie ETG). Środowiska z wysokimi opóźnieniami, takich jak urządzenia przenośne będzie znacznie korzystać z ETGs.  

##### <a name="subheading36"></a>UPSERT
Użyj tabeli **Upsert** operacji tam gdzie to możliwe. Istnieją dwa typy **Upsert**, które może być skuteczniejsza niż tradycyjny **Wstaw** i **aktualizacji** operacje:  

* **InsertOrMerge**: Użyj go, jeśli chcesz przekazać podzbioru właściwości jednostki, ale nie masz pewności, czy ta jednostka już istnieje. Jednostka istnieje, to wywołanie aktualizuje właściwości zawarte w **Upsert** operację i wszystkich istniejących właściwości są one, jeśli jednostka nie istnieje, Nowa jednostka do wstawienia. Jest podobny do sposobu używania rzutowania w zapytaniu, w tym należy przekazać właściwości, które są zmieniane.
* **InsertOrReplace**: Użyj go, jeśli chcesz przekazać całkowicie nowe jednostki, ale nie masz pewności, czy już istnieje. Należy używać tylko to, gdy wiesz, że nowo przesłanym jednostki jest całkowicie prawidłowe, ponieważ całkowicie zastępuje stary jednostki. Na przykład chcesz zaktualizować jednostki, która przechowuje bieżącej lokalizacji użytkownika niezależnie od tego, czy aplikacja wcześniej przechowywanych danych lokalizacji dla użytkownika; Nowa jednostka lokalizacji została ukończona, a nie są wszystkie informacje z poprzedniego jednostki.

##### <a name="subheading37"></a>Przechowywanie danych serii w pojedynczej jednostki
Czasami aplikacja przechowuje serii danych, który często musi pobrać jednocześnie: na przykład aplikacja może śledzić użycie procesora CPU w czasie do wykreślenia wykresu stopniowego dane z ostatnich 24 godzinach. Jednym z podejść jest jedną jednostkę tabeli na godzinę, z każdej jednostki reprezentującą określoną godzinę i przechowywania użycie procesora CPU dla danej godziny. Do wykreślenia tych danych, aplikacja musi pobrać jednostek przechowywania danych z 24 godziny ostatniego.  

Alternatywnie aplikacji można przechowywać użycie procesora CPU dla każdej godziny jako osobne właściwość pojedynczej jednostki: Aby zaktualizować co godzinę, aplikacja może używać pojedynczego **InsertOrMerge Upsert** wywołania do aktualizowania wartości dla najnowszej Godzina. Do wykreślenia danych, aplikacja tylko musi pobrać pojedynczą jednostką zamiast 24, co bardzo wydajny zapytania (zobacz powyżej dyskusji na [kwerendy zakresu](#subheading30)).

##### <a name="subheading38"></a>Przechowywanie danych strukturalnych w obiektach blob
Czasami strukturalnych danych tak, jak powinien znajdować się w tabelach, ale zakresy jednostek są zawsze pobierane ze sobą i można wstawiać partii.  Dobrym przykładem jest plik dziennika.  W takim przypadku można partii kilka minut dzienników, umieść je i następnie kilka minut dzienników również jednocześnie są zawsze pobierane.  W takim przypadku wydajność, najlepiej użyć obiektów blob zamiast tabel, ponieważ może znacznie zmniejszyć liczbę obiektów zapisywane/zwrócona, a także zwykle liczba żądań, które muszą wprowadzone.  

## <a name="queues"></a>Kolejki
Oprócz sprawdzonych rozwiązań dla [wszystkie usługi](#allservices) opisanych powyżej, poniżej sprawdzonych rozwiązań dotyczą przede wszystkim usługi kolejki.  

### <a name="subheading39"></a>Limity skalowalności
Kolejka może przetworzyć około 2000 wiadomości (1KB) na sekundę (każdego AddMessage, GetMessage i DeleteMessage licznik jako tutaj komunikat). Jeśli to jest niewystarczająca dla aplikacji, należy użyć wielu kolejek i rozmieszczenie do nich wiadomości.  

Wyświetl bieżące wartości docelowe skalowalności w [cele dotyczące wydajności i skalowalności magazynu Azure](storage-scalability-targets.md).  

### <a name="subheading40"></a>Nagle'a wyłączone
Zobacz sekcję dotyczącą konfiguracji tabeli omówiono algorytm Nagle'a — algorytm Nagle'a jest zazwyczaj zły wydajności kolejki żądań i należy go wyłączyć.  

### <a name="subheading41"></a>Rozmiar komunikatu
Wydajność i skalowalność spadku kolejki miarę zwiększania rozmiaru komunikatu. Tylko te informacje, które odbiorca musi należy umieścić w wiadomości.  

### <a name="subheading42"></a>Pobieranie partii
Można pobrać maksymalnie 32 wiadomości z kolejki w ramach jednej operacji. To pozwala zmniejszyć liczbę dwukierunkowe przesyłanie danych z aplikacji klienta, co jest szczególnie przydatne w środowiskach, takich jak urządzenia przenośne, duże opóźnienie.  

### <a name="subheading43"></a>Interwał sondowania kolejki
Większość aplikacji sondować wiadomości z kolejki, które może być jedną z największych źródeł transakcje dla tej aplikacji. Wybierz rozsądny sposób interwału sondowania: sondowanie zbyt często może spowodować, że aplikacja do wartości docelowe skalowalności dla kolejki. Jednak w 200 000 transakcji dla $0,01 (w czasie zapisywania), przez pojedynczy procesor sondowania, gdy w ciągu sekundy przez miesiąc czy koszt mniej niż 15 centów, więc koszt nie jest zwykle czynnik, który ma wpływ na wybór interwału sondowania.  

Koszt aktualne informacje, zobacz [cennik usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage/).  

### <a name="subheading44"></a>UpdateMessage
Można użyć **UpdateMessage** Aby zwiększyć limit czasu niewidoczności lub zaktualizować informacje o stanie wiadomości. Jest to zaawansowane, należy pamiętać, że każdy **UpdateMessage** operacji liczy się docelowy skalowalności. Jednak może to być bardziej efektywnego podejścia niż w przypadku przepływu pracy, który przekazuje zadania z jednej kolejki do następnego, ponieważ każdy krok zadanie zostało zakończone. Przy użyciu **UpdateMessage** operacji pozwala aplikacji Zapisz stan zadania do wiadomości, a następnie kontynuuj pracę, zamiast ponownie usługi kolejkowania komunikatów do następnego kroku zadania za każdym razem, gdy wykonuje kroku.  

Aby uzyskać więcej informacji, zobacz artykuł [porady: zmiana zawartości komunikatu w kolejce](../queues/storage-dotnet-how-to-use-queues.md#change-the-contents-of-a-queued-message).  

### <a name="subheading45"></a>Architektura aplikacji
Aby skalowalne architektury aplikacji należy używać kolejek. Poniżej wymieniono niektóre sposoby użycia kolejek dokonanie skalowalność aplikacji:  

* Tworzenie zaległości pracy do przetwarzania i wygładzanie obciążeń w aplikacji, można użyć kolejki. Na przykład użytkownik może kolejki żądania od użytkowników do wykonywania pracy znacznym procesora, takich jak zmiana rozmiaru obrazów przekazane.
* Kolejek umożliwia rozdzielenie części aplikacji, dzięki czemu można je skalować niezależnie. Na przykład frontonu sieci web mógł umieścić wyników ankiety od użytkowników w kolejce dla nowszej analizy i magazynu. Można dodać więcej wystąpień roli procesu roboczego do przetwarzania danych kolejki zgodnie z potrzebami.  

## <a name="conclusion"></a>Podsumowanie
W tym artykule omówiono niektóre z najczęściej, sprawdzonych rozwiązań dla optymalizacji wydajności w przypadku korzystania z usługi Azure Storage. Zachęcamy każdego dewelopera aplikacji do oceny swoich aplikacji pod kątem powyższych rozwiązań oraz do postępowania zgodnie z zaleceniami mającymi na celu uzyskanie optymalnej wydajności aplikacji korzystających z usługi Azure Storage.