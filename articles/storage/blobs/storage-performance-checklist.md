---
title: Lista kontrolna wydajności i skalowalności magazynu obiektów Blob — usługa Azure Storage
description: Lista kontrolna sprawdzonych praktyk do użycia z magazynem obiektów Blob w opracowywaniu aplikacji o wysokiej wydajności.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: b94725d4d3eb9fd6f13a39d00486b4ab085b9ef9
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80473933"
---
# <a name="performance-and-scalability-checklist-for-blob-storage"></a>Lista kontrolna wydajności i skalowalności magazynu obiektów Blob

Firma Microsoft opracowała szereg sprawdzonych praktyk w zakresie tworzenia aplikacji o wysokiej wydajności z magazynem obiektów Blob. Ta lista kontrolna identyfikuje kluczowe praktyki, które deweloperzy mogą stosować w celu optymalizacji wydajności. Należy pamiętać o tych praktykach podczas projektowania aplikacji i w trakcie całego procesu.

Usługa Azure Storage ma cele dotyczące skalowalności i wydajności dla pojemności, szybkości transakcji i przepustowości. Aby uzyskać więcej informacji na temat celów skalowalności usługi Azure Storage, zobacz [Cele skalowalności i wydajności dla standardowych kont magazynu](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) oraz cele [skalowalności i wydajności magazynu obiektów Blob.](scalability-targets.md)

## <a name="checklist"></a>Lista kontrolna

W tym artykule organizuje sprawdzone praktyki dotyczące wydajności do listy kontrolnej, którą można wykonać podczas tworzenia aplikacji magazynu obiektów Blob.

| Gotowe | Kategoria | Zagadnienie dotyczące projektu |
| --- | --- | --- |
| &nbsp; |Cele skalowalności |[Czy można zaprojektować aplikację do używania nie więcej niż maksymalnej liczby kont magazynu?](#maximum-number-of-storage-accounts) |
| &nbsp; |Cele skalowalności |[Czy unikasz zbliżania się do limitów pojemności i transakcji?](#capacity-and-transaction-targets) |
| &nbsp; |Cele skalowalności |[Czy duża liczba klientów uzyskujących dostęp do pojedynczego obiektu blob jednocześnie?](#multiple-clients-accessing-a-single-blob-concurrently) |
| &nbsp; |Cele skalowalności |[Czy aplikacja pozostaje w obrębie obiektów docelowych skalowalności dla pojedynczego obiektu blob?](#bandwidth-and-operations-per-blob) |
| &nbsp; |Partycjonowanie |[Czy twoja konwencja nazewnictwa została zaprojektowana w celu umożliwienia lepszego równoważenia obciążenia?](#partitioning) |
| &nbsp; |Networking |[Czy urządzenia po stronie klienta mają wystarczająco wysoką przepustowość i małe opóźnienia, aby osiągnąć wymaganą wydajność?](#throughput) |
| &nbsp; |Networking |[Czy urządzenia po stronie klienta mają wysokiej jakości łącze sieciowe?](#link-quality) |
| &nbsp; |Networking |[Czy aplikacja kliencka znajduje się w tym samym regionie co konto magazynu?](#location) |
| &nbsp; |Bezpośredni dostęp do klienta |[Czy używasz sygnatur dostępu współdzielonego (SAS) i współużytkowania zasobów między źródłami (CORS), aby umożliwić bezpośredni dostęp do usługi Azure Storage?](#sas-and-cors) |
| &nbsp; |Buforowanie |[Czy buforowanie danych aplikacji jest często dostępne i rzadko zmieniane?](#reading-data) |
| &nbsp; |Buforowanie |[Czy aplikacja wsadowania aktualizacji przez buforowanie ich na kliencie, a następnie przekazywanie ich w większych zestawach?](#uploading-data-in-batches) |
| &nbsp; |Konfiguracja .NET |[Czy używasz programu .NET Core 2.1 lub nowszego w celu uzyskania optymalnej wydajności?](#use-net-core) |
| &nbsp; |Konfiguracja .NET |[Czy klient skonfigurował do używania wystarczającej liczby równoczesnych połączeń?](#increase-default-connection-limit) |
| &nbsp; |Konfiguracja .NET |[Czy w przypadku aplikacji platformy .NET skonfigurowano program .NET do używania wystarczającej liczby wątków?](#increase-minimum-number-of-threads) |
| &nbsp; |Równoległości prostych |[Czy upewnij się, że równoległość jest ograniczona odpowiednio, tak aby nie przeciążać możliwości klienta lub podejście do celów skalowalności?](#unbounded-parallelism) |
| &nbsp; |narzędzia |[Czy korzystasz z najnowszych wersji bibliotek i narzędzi klienckich dostarczonych przez firmę Microsoft?](#client-libraries-and-tools) |
| &nbsp; |Ponowne próby |[Czy używasz zasad ponawiania próby z wykładniczym wycofywania dla błędów ograniczania przepustowości i limitów czasu?](#timeout-and-server-busy-errors) |
| &nbsp; |Ponowne próby |[Czy aplikacja unika ponownych prób dla błędów, których nie można ponowić?](#non-retryable-errors) |
| &nbsp; |Kopiowanie obiektów blob |[Czy kopiujesz obiekty BLOB w najbardziej efektywny sposób?](#blob-copy-apis) |
| &nbsp; |Kopiowanie obiektów blob |[Czy używasz najnowszej wersji programu AzCopy do operacji kopiowania zbiorczego?](#use-azcopy) |
| &nbsp; |Kopiowanie obiektów blob |[Czy używasz rodziny Azure Data Box do importowania dużych ilości danych?](#use-azure-data-box) |
| &nbsp; |Dystrybucja zawartości |[Czy używasz sieci CDN do dystrybucji treści?](#content-distribution) |
| &nbsp; |Korzystanie z metadanych |[Czy przechowujesz często używane metadane obiektów blob w ich metadanych?](#use-metadata) |
| &nbsp; |Szybkie przesyłanie |[Podczas próby szybkiego przesłania jednego obiektu blob, czy przesyłasz bloki równolegle?](#upload-one-large-blob-quickly) |
| &nbsp; |Szybkie przesyłanie |[Podczas próby szybkiego przesłania wielu obiektów blob, czy przesyłasz obiekty BLOB równolegle?](#upload-many-blobs-quickly) |
| &nbsp; |Typ obiektów blob |[Czy używasz stronicowych obiektów blob lub bloków obiektów blob w razie potrzeby?](#choose-the-correct-type-of-blob) |

## <a name="scalability-targets"></a>Cele skalowalności

Jeśli aplikacja zbliża się lub przekracza dowolny z celów skalowalności, może wystąpić zwiększone opóźnienia transakcji lub ograniczania przepustowości. Gdy usługa Azure Storage ogranicza aplikację, usługa zaczyna zwracać kody błędów 503 (serwer zajęty) lub 500 (limit czasu operacji). Unikanie tych błędów przez pozostawanie w granicach docelowych skalowalności jest ważną częścią zwiększenia wydajności aplikacji.

Aby uzyskać więcej informacji na temat celów skalowalności usługi Kolejka, zobacz [Cele skalowalności i wydajności usługi Azure Storage](/azure/storage/queues/scalability-targets#scale-targets-for-queue-storage).

### <a name="maximum-number-of-storage-accounts"></a>Maksymalna liczba kont magazynu

Jeśli zbliżasz się do maksymalnej liczby kont magazynu dozwolonych dla określonej kombinacji subskrypcji/regionu, oceń scenariusz i określ, czy ma zastosowanie którykolwiek z następujących warunków:

- Czy używasz kont magazynu do przechowywania dysków niezarządzanych i dodawania tych dysków do maszyn wirtualnych(VM)? W tym scenariuszu firma Microsoft zaleca używanie dysków zarządzanych. Dyski zarządzane są skalowane automatycznie i bez konieczności tworzenia poszczególnych kont magazynu i zarządzania nimi. Aby uzyskać więcej informacji, zobacz [Wprowadzenie do dysków zarządzanych platformy Azure](../../virtual-machines/windows/managed-disks-overview.md)
- Czy używasz jednego konta magazynu na klienta, w celu izolacji danych? W tym scenariuszu firma Microsoft zaleca użycie kontenera obiektów blob dla każdego klienta zamiast całego konta magazynu. Usługa Azure Storage umożliwia teraz przypisywanie ról kontroli dostępu opartej na rolach (RBAC) na podstawie kontenera. Aby uzyskać więcej informacji, zobacz [Udzielanie dostępu do danych obiektów blob i kolejek platformy Azure za pomocą rbac w witrynie Azure portal.](../common/storage-auth-aad-rbac-portal.md)
- Czy używasz wielu kont magazynu do niezależnego fragmentu, aby zwiększyć ruch przychodzący, ruch wychodzący, operacje we/wy na sekundę (IOPS) lub pojemność? W tym scenariuszu firma Microsoft zaleca korzystanie ze zwiększonych limitów dla kont magazynu, aby zmniejszyć liczbę kont magazynu wymaganych dla obciążenia, jeśli to możliwe. Skontaktuj się z [pomocą techniczną platformy Azure,](https://azure.microsoft.com/support/options/) aby zażądać zwiększenia limitów dla konta magazynu. Aby uzyskać więcej informacji, zobacz [Ogłaszanie większych, bardziej zakrojonych kont magazynu](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/).

### <a name="capacity-and-transaction-targets"></a>Cele dotyczące zdolności produkcyjnych i transakcji

Jeśli aplikacja zbliża się do celów skalowalności dla jednego konta magazynu, należy rozważyć przyjęcie jednego z następujących metod:  

- Jeśli aplikacja trafi docelowej transakcji, należy rozważyć użycie konta magazynu bloku obiektów blob, które są zoptymalizowane pod kątem wysokich stawek transakcji i opóźnienia niskie i spójne. Aby uzyskać więcej informacji, zobacz [Omówienie konta magazynu platformy Azure](../common/storage-account-overview.md).
- Ponowne rozważenie obciążenia, które powoduje, że aplikacja zbliża się lub przekracza cel skalowalności. Czy można zaprojektować go inaczej, aby zużywać mniej przepustowości lub pojemności lub mniej transakcji?
- Jeśli aplikacja musi przekraczać jeden z celów skalowalności, a następnie utworzyć wiele kont magazynu i partycjonować dane aplikacji na tych wielu kont magazynu. Jeśli używasz tego wzorca, a następnie należy zaprojektować aplikację, dzięki czemu można dodać więcej kont magazynu w przyszłości do równoważenia obciążenia. Same konta magazynowe nie mają żadnych kosztów innych niż użycie danych przechowywanych, dokonanych transakcji lub przesyłanych danych.
- Jeśli aplikacja zbliża się do docelowych przepustowości, należy wziąć pod uwagę kompresji danych po stronie klienta, aby zmniejszyć przepustowość wymaganą do wysyłania danych do usługi Azure Storage.
    Kompresja danych może oszczędzać przepustowość i poprawiać wydajność sieci, ale może mieć również negatywny wpływ na wydajność. Oceń wpływ dodatkowych wymagań dotyczących przetwarzania dodatkowych danych dotyczących kompresji i dekompresji danych po stronie klienta. Należy pamiętać, że przechowywanie skompresowanych danych może utrudnić rozwiązywanie problemów, ponieważ wyświetlanie danych przy użyciu standardowych narzędzi może być trudniejsze.
- Jeśli aplikacja zbliża się do celów skalowalności, upewnij się, że używasz wykładniczego wycofywania ponownych prób. Najlepiej jest unikać osiągnięcia docelowych skalowalności, implementując zalecenia opisane w tym artykule. Jednak przy użyciu wykładniczego wycofywania dla ponownych prób uniemożliwi aplikacji ponawianie próby szybko, co może pogorszyć ograniczanie przepustowości. Aby uzyskać więcej informacji, zobacz sekcję ["Timeout and Server Busy errors](#timeout-and-server-busy-errors)"

### <a name="multiple-clients-accessing-a-single-blob-concurrently"></a>Wielu klientów uzyskujących jednocześnie dostęp do pojedynczego obiektu blob

Jeśli masz dużą liczbę klientów uzyskujących dostęp do pojedynczego obiektu blob jednocześnie, należy wziąć pod uwagę zarówno dla obiektów blob i na konto magazynu cele skalowalności. Dokładna liczba klientów, którzy mogą uzyskać dostęp do pojedynczego obiektu blob będzie się różnić w zależności od czynników, takich jak liczba klientów żądających obiektu blob jednocześnie, rozmiar obiektu blob i warunki sieciowe.

Jeśli obiekt blob może być dystrybuowany za pośrednictwem sieci CDN, takich jak obrazy lub filmy wyświetlane z witryny sieci Web, można użyć sieci CDN. Aby uzyskać więcej informacji, zobacz sekcję ["Dystrybucja zawartości](#content-distribution)"

W innych scenariuszach, takich jak symulacje naukowe, w których dane są poufne, dostępne są dwie opcje. Pierwszym z nich jest rozłożone obciążenia dostępu w taki sposób, że obiekt blob jest dostępny w okresie czasu vs jest dostępny jednocześnie. Alternatywnie można tymczasowo skopiować obiekt blob do wielu kont magazynu, aby zwiększyć całkowitą liczbę kont We/Wy na obiekt blob i między kontami magazynu. Wyniki będą się różnić w zależności od zachowania aplikacji, więc należy przetestować wzorce współbieżności podczas projektowania.

### <a name="bandwidth-and-operations-per-blob"></a>Przepustowość i operacje na obiekt blob

Pojedynczy obiekt blob obsługuje maksymalnie 500 żądań na sekundę. Jeśli masz wielu klientów, którzy muszą odczytać ten sam obiekt blob i może przekroczyć ten limit, a następnie należy rozważyć użycie konta magazynu obiektów blob bloku. Konto magazynu obiektów blob bloku zapewnia wyższą szybkość żądania lub operacje we/wy na sekundę (IOPS).

Można również użyć sieci dostarczania zawartości (CDN), takich jak usługa Azure CDN do dystrybucji operacji na obiekcie blob. Aby uzyskać więcej informacji na temat usługi Azure CDN, zobacz [omówienie usługi Azure CDN](../../cdn/cdn-overview.md).  

## <a name="partitioning"></a>Partycjonowanie

Opis sposobu partycjonowania danych obiektów blob usługi Azure Storage jest przydatne do zwiększenia wydajności. Usługa Azure Storage może wyświetlać dane w jednej partycji szybciej niż dane obejmujące wiele partycji. Poprzez odpowiednie nazywanie obiektów blob, można zwiększyć wydajność odczytu żądań.

Magazyn obiektów blob używa schematu partycjonowania opartego na zakresie do skalowania i równoważenia obciążenia. Każdy obiekt blob ma klucz partycji składający się z pełnej nazwy obiektu blob (konto + kontener + obiekt blob). Klucz partycji służy do partycjonowania danych obiektów blob do zakresów. Zakresy są następnie równoważenia obciążenia w magazynie obiektów Blob.

Partycjonowanie oparte na zakresie oznacza, że konwencje nazewnictwa, które używają porządkowania leksykalne (na przykład *mypayroll,* *myperformance,* *myemployees*itp.) lub sygnatury czasowe *(log20160101*, *log20160102*, *log20160102*, itp.) są bardziej prawdopodobne, aby spowodować partycje są współlokowany na tej samej partycji serwera. , dopóki zwiększone obciążenie nie wymaga ich podziału na mniejsze zakresy. Współlokowanie obiektów blob na tym samym serwerze partycji zwiększa wydajność, więc ważną częścią zwiększenia wydajności obejmuje nazywanie obiektów blob w sposób, który organizuje je najbardziej efektywnie.

Na przykład wszystkie obiekty BLOB w kontenerze mogą być obsługiwane przez pojedynczy serwer, dopóki obciążenie tych obiektów blob wymaga dalszego równoważenia zakresów partycji. Podobnie grupa lekko załadowanych kont z ich nazwami umieszczona w kolejności leksykalne może być obsługiwana przez jeden serwer, dopóki obciążenie jednego lub wszystkich z tych kont nie wymaga ich podziału na wiele serwerów partycji.

Każda operacja równoważenia obciążenia może mieć wpływ na opóźnienie wywołań magazynu podczas operacji. Zdolność usługi do obsługi nagłego wybuchu ruchu do partycji jest ograniczona przez skalowalność serwera pojedynczej partycji, dopóki operacja równoważenia obciążenia nie zostanie rozpoczęta i ponownie równoważy zakres kluczy partycji.

Można wykonać niektóre najlepsze rozwiązania, aby zmniejszyć częstotliwość takich operacji.  

- Jeśli to możliwe, użyj rozmiarów obiektów blob lub bloków większych niż 4 MiB dla standardowych kont magazynu i większych niż 256 KiB dla kont magazynu w wersji premium. Większe rozmiary obiektów blob lub bloków automatycznie aktywują obiekty blob bloków o wysokiej przepływie. Obiekty blob bloków o wysokiej przepływności zapewniają wysoką wydajność pozyskiwania, na które nie ma wpływu nazewnictwo partycji.
- Sprawdź konwencję nazewnictwa używana dla kont, kontenerów, obiektów blob, tabel i kolejek. Należy wziąć pod uwagę prefiksowanie nazw kont, kontenerów lub obiektów blob z hash trzycyfrowy przy użyciu funkcji mieszania, która najlepiej odpowiada Twoim potrzebom.
- Jeśli dane są porządkować przy użyciu znaczników czasu lub identyfikatorów numerycznych, upewnij się, że nie używasz wzorca ruchu tylko do dołączania (lub tylko do dołączania). Te wzorce nie są odpowiednie dla systemu partycjonowania opartego na zakresie. Te wzorce mogą prowadzić do całego ruchu przechodzącego do jednej partycji i ograniczając system od skutecznego równoważenia obciążenia.

    Na przykład, jeśli masz codzienne operacje, które używają obiektu blob z sygnaturą czasową, takich jak *rrrrmdd,* a następnie cały ruch dla tej codziennej operacji jest kierowany do pojedynczego obiektu blob, który jest obsługiwany przez serwer pojedynczej partycji. Należy wziąć pod uwagę, czy limity na obiekt blob i limity na partycje spełniają twoje potrzeby i należy rozważyć podział tej operacji na wiele obiektów blob w razie potrzeby. Podobnie jeśli przechowujesz dane szeregów czasowych w tabelach, cały ruch może być kierowany do ostatniej części obszaru nazw klucza. Jeśli używasz identyfikatorów numerycznych, przedrostek identyfikatora z hash trzycyfrowy. Jeśli używasz sygnatur czasowych, prefiks sygnatura czasowa z wartością sekund, na przykład *ssyyyymmdd*. Jeśli aplikacja rutynowo wykonuje operacje wyświetlania listy i wykonywania zapytań, wybierz funkcję mieszania, która ograniczy liczbę zapytań. W niektórych przypadkach może być wystarczający losowy prefiks.
  
- Aby uzyskać więcej informacji na temat schematu partycjonowania używanego w usłudze Azure Storage, zobacz [Usługa Azure Storage: Usługa magazynu w chmurze o wysokiej dostępności z silną spójnością.](https://sigops.org/sosp/sosp11/current/2011-Cascais/printable/11-calder.pdf)

## <a name="networking"></a>Networking

Ograniczenia sieci fizycznej aplikacji mogą mieć znaczący wpływ na wydajność. W poniższych sekcjach opisano niektóre ograniczenia, które użytkownicy mogą napotkać.  

### <a name="client-network-capability"></a>Możliwości sieci klienta

Przepustowość i jakość łącza sieciowego odgrywają ważną rolę w wydajności aplikacji, zgodnie z opisem w poniższych sekcjach.

#### <a name="throughput"></a>Przepływność

W przypadku przepustowości problemem często są możliwości klienta. Większe wystąpienia platformy Azure mają karty sieciowe o większej pojemności, więc należy rozważyć użycie większego wystąpienia lub większej liczby maszyn wirtualnych, jeśli potrzebujesz wyższych limitów sieciowych z jednego komputera. Jeśli uzyskujesz dostęp do usługi Azure Storage z aplikacji lokalnej, stosuje się tę samą regułę: poznaj możliwości sieciowe urządzenia klienckiego i łączność sieciową z lokalizacją usługi Azure Storage i ulepsz je w razie potrzeby lub zaprojektuj aplikację do pracy w ramach ich możliwości.

#### <a name="link-quality"></a>Jakość łącza

Podobnie jak w przypadku każdego użycia sieci, należy pamiętać, że warunki sieciowe powodujące błędy i utratę pakietów spowolnią efektywną przepływność.  Korzystanie z WireShark lub NetMon może pomóc w diagnozowaniu tego problemu.  

### <a name="location"></a>Lokalizacja

W każdym środowisku rozproszonym umieszczenie klienta w pobliżu serwera zapewnia najlepszą wydajność. Aby uzyskać dostęp do usługi Azure Storage z najniższym opóźnieniem, najlepsza lokalizacja dla klienta znajduje się w tym samym regionie platformy Azure. Na przykład jeśli masz aplikację sieci web platformy Azure, która korzysta z usługi Azure Storage, a następnie zlokalizować je zarówno w jednym regionie, takich jak US West lub Azji Południowo-Wschodniej. Współlokowanie zasobów zmniejsza opóźnienie i koszty, ponieważ użycie przepustowości w jednym regionie jest bezpłatne.  

Jeśli aplikacje klienckie będą uzyskiwać dostęp do usługi Azure Storage, ale nie są hostowane na platformie Azure, takiej jak aplikacje urządzeń przenośnych lub lokalne usługi dla przedsiębiorstw, lokalizowanie konta magazynu w regionie w pobliżu tych klientów może zmniejszyć opóźnienia. Jeśli twoi klienci są szeroko rozpowszechnione (na przykład niektóre w Ameryce Północnej, a niektóre w Europie), a następnie należy rozważyć użycie jednego konta magazynu na region. Takie podejście jest łatwiejsze do zaimplementowania, jeśli dane przechowywane przez aplikację są specyficzne dla poszczególnych użytkowników i nie wymagają replikowania danych między kontami magazynu.

Aby uzyskać szeroką dystrybucję zawartości obiektu blob, należy użyć sieci dostarczania zawartości, takiej jak usługa Azure CDN. Aby uzyskać więcej informacji na temat usługi Azure CDN, zobacz [Usługa Azure CDN](../../cdn/cdn-overview.md).  

## <a name="sas-and-cors"></a>SAS i CORS

Załóżmy, że musisz autoryzować kod, taki jak JavaScript, który jest uruchomiony w przeglądarce sieci Web użytkownika lub w aplikacji na telefon komórkowy, aby uzyskać dostęp do danych w usłudze Azure Storage. Jednym z podejść jest tworzenie aplikacji usługi, która działa jako serwer proxy. Urządzenie użytkownika uwierzytelnia się za pomocą usługi, która z kolei autoryzuje dostęp do zasobów usługi Azure Storage. W ten sposób można uniknąć ujawniania kluczy konta magazynu na niezabezpieczonych urządzeniach. Jednak to podejście nakłada znaczne obciążenie na aplikację usługi, ponieważ wszystkie dane przesyłane między urządzeniem użytkownika i usługi Azure Storage musi przejść przez aplikację usługi.

Można uniknąć przy użyciu aplikacji usługi jako serwera proxy dla usługi Azure Storage przy użyciu sygnatur dostępu współdzielonego (SYGNAŁÓW DOSTĘPU WSPÓŁDZIELONEGO). Korzystając z usługi SAS, można włączyć urządzenia użytkownika do żądania bezpośrednio do usługi Azure Storage przy użyciu tokenu ograniczonego dostępu. Na przykład jeśli użytkownik chce przekazać zdjęcie do aplikacji, aplikacja usługi może wygenerować sygnatury dostępu Współdzielonego i wysłać go do urządzenia użytkownika. Token sygnatury dostępu Współdzielonego może udzielić uprawnień do zapisu w zasobie usługi Azure Storage przez określony interwał czasu, po którym token sygnatury dostępu Współdzielonego wygasa. Aby uzyskać więcej informacji na temat sygnatury dostępu współdzielonego, zobacz [Udzielanie ograniczonego dostępu do zasobów usługi Azure Storage przy użyciu sygnatur dostępu współdzielonego (SAS).](../common/storage-sas-overview.md)  

Zazwyczaj przeglądarka internetowa nie zezwala javascript na stronie, która jest obsługiwana przez witrynę sieci Web w jednej domenie do wykonywania niektórych operacji, takich jak operacje zapisu, do innej domeny. Zasady te, znane jako zasady tego samego źródła, uniemożliwiają złośliwemu skryptowi na jednej stronie uzyskanie dostępu do danych na innej stronie sieci Web. Jednak zasady tego samego źródła może być ograniczenie podczas tworzenia rozwiązania w chmurze. Udostępnianie zasobów między źródłami (CORS) to funkcja przeglądarki, która umożliwia domenie docelowej komunikowanie się z przeglądarką, w którym ufa żądań pochodzących z domeny źródłowej.

Załóżmy na przykład, że aplikacja sieci web uruchomiona na platformie Azure wywołuje żądanie zasobu do konta usługi Azure Storage. Aplikacja sieci web jest domeną źródłową, a konto magazynu jest domeną docelową. Usługę CORS można skonfigurować dla dowolnej usługi usługi Azure Storage do komunikowania się z przeglądarką sieci Web, zgodnie z którą żądania z domeny źródłowej są zaufane przez usługę Azure Storage. Aby uzyskać więcej informacji na temat usługi CORS, zobacz [obsługa udostępniania zasobów między źródłami (CORS) dla usługi Azure Storage](/rest/api/storageservices/Cross-Origin-Resource-Sharing--CORS--Support-for-the-Azure-Storage-Services).  
  
Zarówno SAS, jak i CORS mogą pomóc uniknąć niepotrzebnego obciążenia aplikacji sieci web.  

## <a name="caching"></a>Buforowanie

Buforowanie odgrywa ważną rolę w wydajności. W poniższych sekcjach omówiono buforowanie najlepszych praktyk.

### <a name="reading-data"></a>Odczyt danych

Ogólnie rzecz biorąc, odczyt danych raz jest lepszy niż odczytywanie ich dwa razy. Rozważmy przykład aplikacji sieci web, która została pobrana 50 MiB blob z usługi Azure Storage służyć jako zawartość dla użytkownika. W idealnym przypadku aplikacja buforuje obiekt blob lokalnie na dysku, a następnie pobiera wersję buforowaną dla kolejnych żądań użytkownika.

Jednym ze sposobów uniknięcia pobierania obiektu blob, jeśli nie został zmodyfikowany, ponieważ został buforowany jest zakwalifikowanie get operacji z nagłówka warunkowego dla czasu modyfikacji. Jeśli czas ostatniej modyfikacji jest po czasie, że obiekt blob został buforowany, a następnie obiektu blob jest pobierana i ponownie buforowane. W przeciwnym razie buforowany obiekt blob jest pobierany w celu uzyskania optymalnej wydajności.

Można również zdecydować się zaprojektować aplikację, aby założyć, że obiekt blob pozostaje niezmieniony przez krótki okres po pobraniu go. W takim przypadku aplikacja nie musi sprawdzać, czy obiekt blob został zmodyfikowany w tym przedziale czasu.

Dane konfiguracyjne, dane wyszukiwania i inne dane, które są często używane przez aplikację są dobrymi kandydatami do buforowania.  

Aby uzyskać więcej informacji na temat używania nagłówków warunkowych, zobacz [Określanie nagłówków warunkowych dla operacji usługi obiektów Blob](/rest/api/storageservices/specifying-conditional-headers-for-blob-service-operations).  

### <a name="uploading-data-in-batches"></a>Przekazywanie danych w partiach

W niektórych scenariuszach można agregować dane lokalnie, a następnie okresowo przesyłać je w partii zamiast natychmiast przesyłać każdy fragment danych. Załóżmy na przykład, że aplikacja sieci web przechowuje plik dziennika działań. Aplikacja może przekazać szczegóły każdego działania, jak to się dzieje z tabeli (co wymaga wielu operacji magazynu), lub można zapisać szczegóły działania do lokalnego pliku dziennika, a następnie okresowo przekazać wszystkie szczegóły działania jako plik rozdzielany do obiektu blob. Jeśli każdy wpis dziennika ma rozmiar 1 KB, można przekazać tysiące wpisów w jednej transakcji. Pojedyncza transakcja obsługuje przekazywanie obiektu blob o rozmiarze do 64 MiB. Deweloper aplikacji musi zaprojektować możliwość awarii urządzenia klienckiego lub przekazywania. Jeśli dane działania muszą być pobierane przez przedział czasu, a nie dla pojedynczego działania, a następnie przy użyciu magazynu obiektów Blob jest zalecane za pośrednictwem magazynu tabel.

## <a name="net-configuration"></a>Konfiguracja .NET

Jeśli używasz programu .NET Framework, w tej sekcji wymieniono kilka szybkich ustawień konfiguracji, których można użyć do znacznych ulepszeń wydajności.  Jeśli używasz innych języków, sprawdź, czy podobne pojęcia mają zastosowanie w wybranym języku.  

### <a name="use-net-core"></a>Użyj programu .NET Core

Tworzenie aplikacji usługi Azure Storage za pomocą platformy .NET Core 2.1 lub nowszej w celu skorzystania z ulepszeń wydajności. W miarę możliwości zalecane jest użycie programu .NET Core 3.x.

Aby uzyskać więcej informacji na temat poprawy wydajności w programie .NET Core, zobacz następujące wpisy w blogu:

- [Poprawa wydajności w programie .NET Core 3.0](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-core-3-0/)
- [Poprawa wydajności w programie .NET Core 2.1](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-core-2-1/)

### <a name="increase-default-connection-limit"></a>Zwiększ domyślny limit połączeń

W .NET następujący kod zwiększa domyślny limit połączenia (który jest zwykle dwa w środowisku klienckim lub dziesięć w środowisku serwera) do 100. Zazwyczaj należy ustawić wartość w przybliżeniu liczbę wątków używanych przez aplikację. Przed otwarciem jakichkolwiek połączeń ustaw limit połączenia.

```csharp
ServicePointManager.DefaultConnectionLimit = 100; //(Or More)  
```

W przypadku innych języków programowania zobacz dokumentację, aby ustalić, jak ustawić limit połączeń.  

Aby uzyskać więcej informacji, zobacz wpis w blogu [Usługi sieci Web: Połączenia współbieżne](https://blogs.msdn.microsoft.com/darrenj/2005/03/07/web-services-concurrent-connections/).  

### <a name="increase-minimum-number-of-threads"></a>Zwiększenie minimalnej liczby wątków

Jeśli używasz wywołań synchronicznych wraz z zadaniami asynchronialnymi, można zwiększyć liczbę wątków w puli wątków:

```csharp
ThreadPool.SetMinThreads(100,100); //(Determine the right number for your application)  
```

Aby uzyskać więcej informacji, zobacz [ThreadPool.SetMinThreads](/dotnet/api/system.threading.threadpool.setminthreads) metody.  

## <a name="unbounded-parallelism"></a>Niezwiązany równoległość

Podczas równoległości może być wielki dla wydajności, należy uważać na przy użyciu równoległości nieograniczony, co oznacza, że nie ma limitu wymuszane na liczbę wątków lub żądań równoległych. Należy ograniczyć równoległe żądania przekazywania lub pobierania danych, aby uzyskać dostęp do wielu partycji na tym samym koncie magazynu lub uzyskać dostęp do wielu elementów w tej samej partycji. Jeśli równoległość jest nieograniczona, aplikacja może przekroczyć możliwości urządzenia klienckiego lub cele skalowalności konta magazynu, co powoduje dłuższe opóźnienia i ograniczanie przepustowości.  

## <a name="client-libraries-and-tools"></a>Biblioteki i narzędzia klienckie

Aby uzyskać najlepszą wydajność, należy zawsze używać najnowszych bibliotek klienckich i narzędzi dostarczonych przez firmę Microsoft. Biblioteki klientów usługi Azure Storage są dostępne dla różnych języków. Usługa Azure Storage obsługuje również usługi PowerShell i interfejsu wiersza polecenia platformy Azure. Firma Microsoft aktywnie opracowuje te biblioteki i narzędzia klienckie z myślą o wydajności, aktualizuje je z najnowszymi wersjami usług i zapewnia, że wewnętrznie obsługują wiele sprawdzonych praktyk wydajności. Aby uzyskać więcej informacji, zobacz [dokumentację referencyjną usługi Azure Storage](/azure/storage/#reference).

## <a name="handle-service-errors"></a>Obsługa błędów usługi

Usługa Azure Storage zwraca błąd, gdy usługa nie może przetworzyć żądania. Zrozumienie błędów, które mogą być zwracane przez usługę Azure Storage w danym scenariuszu jest przydatne do optymalizacji wydajności.

### <a name="timeout-and-server-busy-errors"></a>Błędy limitu czasu i zajętości serwera

Usługa Azure Storage może ograniczyć aplikację, jeśli zbliży się do limitów skalowalności. W niektórych przypadkach usługa Azure Storage może nie być w stanie obsłużyć żądania z powodu niektórych stanów przejściowych. W obu przypadkach usługa może zwrócić błąd 503 (Zajęty serwerem) lub 500 (Limit czasu). Błędy te mogą również wystąpić, jeśli usługa jest równoważenie partycji danych, aby umożliwić wyższą przepływność. Aplikacja kliencka zazwyczaj należy ponowić próbę operacji, która powoduje jeden z tych błędów. Jednak jeśli usługa Azure Storage jest ograniczanie aplikacji, ponieważ przekracza cele skalowalności lub nawet jeśli usługa nie może obsługiwać żądania z innego powodu, agresywne ponownych prób może pogorszyć problem. Zaleca się użycie wykładniczej zasady ponawiania prób, a biblioteki klienckie domyślnie to zachowanie. Na przykład aplikacja może ponowić próbę po 2 sekundach, następnie 4 sekundy, następnie 10 sekund, a następnie 30 sekund, a następnie całkowicie zrezygnować. W ten sposób aplikacja znacznie zmniejsza jego obciążenie usługi, a nie zaostrzanie zachowanie, które może prowadzić do ograniczania przepustowości.  

Błędy łączności można ponowić natychmiast, ponieważ nie są one wynikiem ograniczania przepustowości i oczekuje się, że są przejściowe.  

### <a name="non-retryable-errors"></a>Błędy, które nie można ponowić

Biblioteki klienta obsługują ponownych prób ze świadomością, które błędy mogą być ponowione, a które nie. Jednak jeśli wywołujesz interfejs API REST usługi Azure Storage bezpośrednio, istnieją pewne błędy, które nie należy ponowić próbę. Na przykład błąd 400 (Złe żądanie) wskazuje, że aplikacja kliencka wysłała żądanie, które nie mogło zostać przetworzone, ponieważ nie było w oczekiwanej formie. Ponowne wysłanie tego żądania powoduje taką samą odpowiedź za każdym razem, więc nie ma sensu ponawiać jej próby. Jeśli wywołujesz interfejs API REST usługi Azure Storage bezpośrednio, należy pamiętać o potencjalnych błędów i czy powinny one być ponowione.

Aby uzyskać więcej informacji na temat kodów błędów usługi Azure Storage, zobacz [Stan i kody błędów](/rest/api/storageservices/status-and-error-codes2).

## <a name="copying-and-moving-blobs"></a>Kopiowanie i przenoszenie obiektów blob

Usługa Azure Storage udostępnia szereg rozwiązań do kopiowania i przenoszenia obiektów blob w ramach konta magazynu, między kontami magazynu oraz między systemami lokalnymi a chmurą. W tej sekcji opisano niektóre z tych opcji pod względem ich wpływu na wydajność. Aby uzyskać informacje na temat efektywnego przesyłania danych do magazynu obiektów Blob lub z niego, zobacz [Wybieranie rozwiązania platformy Azure do przesyłania danych.](../common/storage-choose-data-transfer-solution.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

### <a name="blob-copy-apis"></a>Interfejsy API kopiowania obiektów blob

Aby skopiować obiekty blob na kontach magazynu, użyj operacji [Umieść blok z adresu URL.](/rest/api/storageservices/put-block-from-url) Ta operacja synchronicznie kopiuje dane z dowolnego źródła adresu URL do bloku obiektu blob. Użycie `Put Block from URL` operacji może znacznie zmniejszyć wymaganą przepustowość podczas migracji danych między kontami magazynu. Ponieważ operacja kopiowania odbywa się po stronie usługi, nie trzeba pobierać i ponownie przekazywać danych.

Aby skopiować dane w ramach tego samego konta magazynu, należy użyć operacji [Kopiuj obiekt blob.](/rest/api/storageservices/Copy-Blob) Kopiowanie danych w ramach tego samego konta magazynu jest zazwyczaj wykonywane szybko.  

### <a name="use-azcopy"></a>Korzystanie z narzędzia AzCopy

Narzędzie wiersza polecenia AzCopy jest prostą i wydajną opcją zbiorczego transferu obiektów blob do, z i między kontami magazynu. AzCopy jest zoptymalizowany pod kątem tego scenariusza i może osiągnąć wysokie szybkości transferu. AzCopy w wersji 10 używa `Put Block From URL` operacji do kopiowania danych obiektów blob między kontami magazynu. Aby uzyskać więcej informacji, zobacz [Kopiowanie lub przenoszenie danych do usługi Azure Storage przy użyciu programu AzCopy w wersji 10](/azure/storage/common/storage-use-azcopy-v10).  

### <a name="use-azure-data-box"></a>Korzystanie z pola danych platformy Azure

Aby zaimportować duże ilości danych do magazynu obiektów Blob, należy rozważyć użycie rodziny Azure Data Box do transferu w trybie offline. Urządzenia data box dostarczone przez firmę Microsoft są dobrym wyborem do przenoszenia dużych ilości danych na platformę Azure, gdy jesteś ograniczony przez czas, dostępność sieci lub koszty. Aby uzyskać więcej informacji, zobacz [dokumentację usługi Azure DataBox](/azure/databox/).

## <a name="content-distribution"></a>Dystrybucja zawartości

Czasami aplikacja musi obsługiwać tę samą zawartość wielu użytkownikom (na przykład film demonstracyjny produktu używany na stronie głównej witryny sieci Web), znajdujący się w tym samym lub wielu regionach. W tym scenariuszu użyj sieci dostarczania zawartości (CDN), takich jak usługa Azure CDN do dystrybucji zawartości obiektu blob geograficznie. W przeciwieństwie do konta usługi Azure Storage, które istnieje w jednym regionie i które nie może dostarczać zawartości z małym opóźnieniem do innych regionów, usługa Azure CDN używa serwerów w wielu centrach danych na całym świecie. Ponadto sieć CDN zazwyczaj może obsługiwać znacznie wyższe limity ruchu wychodzącego niż pojedyncze konto magazynu.  

Aby uzyskać więcej informacji na temat usługi Azure CDN, zobacz [Usługa Azure CDN](../../cdn/cdn-overview.md).

## <a name="use-metadata"></a>Korzystanie z metadanych

Usługa obiektów Blob obsługuje żądania HEAD, które mogą zawierać właściwości obiektu blob lub metadane. Na przykład, jeśli aplikacja potrzebuje exif (wymówianym formacie obrazu) dane ze zdjęcia, może pobrać zdjęcie i wyodrębnić go. Aby zaoszczędzić przepustowość i zwiększyć wydajność, aplikacja może przechowywać dane Exif w metadanych obiektu blob, gdy aplikacja przekazuje zdjęcie. Następnie można pobrać dane Exif w metadanych przy użyciu tylko żądania HEAD. Pobieranie tylko metadanych, a nie pełnej zawartości obiektu blob oszczędza znaczną przepustowość i skraca czas przetwarzania wymagany do wyodrębnienia danych Exif. Należy pamiętać, że 8 KiB metadanych mogą być przechowywane na obiekt blob.  

## <a name="upload-blobs-quickly"></a>Szybkie przekazywanie obiektów blob

Aby szybko przekazać obiekty BLOB, najpierw określ, czy będziesz przesyłać jeden obiekt blob, czy wiele. Skorzystaj z poniższych wskazówek, aby określić poprawną metodę do użycia w zależności od scenariusza.  

### <a name="upload-one-large-blob-quickly"></a>Szybkie przesłanie jednego dużego obiektu blob

Aby szybko przekazać pojedynczy duży obiekt blob, aplikacja kliencka może przekazać swoje bloki lub strony równolegle, pamiętając o obiektach skalowalności dla poszczególnych obiektów blob i konta magazynu jako całości. Biblioteki klienta usługi Azure Storage obsługują przekazywanie równolegle. Na przykład można użyć następujących właściwości, aby określić liczbę równoczesnych żądań dozwolonych w .NET lub Java. Biblioteki klientów dla innych obsługiwanych języków zapewniają podobne opcje.

- Dla .NET, ustaw [właściwość BlobRequestOptions.ParallelOperationThreadCount.](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.paralleloperationthreadcount)
- W przypadku języka Java/Android wywołaj metodę [BlobRequestOptions.setConcurrentRequestCount(final Integer concurrentRequestCount).](/java/api/com.microsoft.azure.storage.blob.blobrequestoptions.setconcurrentrequestcount)

### <a name="upload-many-blobs-quickly"></a>Szybkie przesyłanie wielu obiektów blob

Aby szybko przekazać wiele obiektów blob, przekaż obiekty BLOB równolegle. Przekazywanie równolegle jest szybsze niż przekazywanie pojedynczych obiektów blob w czasie z równoległych bloków przekazywania, ponieważ rozprzestrzenia przekazywania na wielu partycjach usługi magazynu. AzCopy wykonuje przekazywanie równolegle domyślnie i jest zalecane w tym scenariuszu. Aby uzyskać więcej informacji, zobacz [Wprowadzenie do AzCopy](../common/storage-use-azcopy-v10.md).  

## <a name="choose-the-correct-type-of-blob"></a>Wybierz odpowiedni typ obiektu blob

Usługa Azure Storage obsługuje blokowe obiekty blob, dołączanie obiektów blob i stronicowych obiektów blob. W danym scenariuszu użycia wybór typu obiektu blob wpłynie na wydajność i skalowalność rozwiązania.

Blokowe obiekty BLOB są odpowiednie, gdy chcesz efektywnie przekazać duże ilości danych. Na przykład aplikacja kliencka, która przekazuje zdjęcia lub wideo do magazynu obiektów Blob będzie docelowe blok obiektów blob.

Dołączanie obiektów blob są podobne do blokowych obiektów blob, ponieważ składają się z bloków. Podczas modyfikowania dołączania obiektu blob, bloki są dodawane tylko na końcu obiektu blob. Dołączanie obiektów blob są przydatne w scenariuszach, takich jak rejestrowanie, gdy aplikacja musi dodać dane do istniejącego obiektu blob.

Obiekty BLOB strony są odpowiednie, jeśli aplikacja musi wykonywać losowe zapisy na danych. Na przykład dyski maszyny wirtualnej platformy Azure są przechowywane jako obiekty blob strony. Aby uzyskać więcej informacji, zobacz [Opis bloków obiektów blob, dołączanie obiektów blob i stronicowych obiektów blob](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs).  

## <a name="next-steps"></a>Następne kroki

- [Cele dotyczące skalowalności i wydajności pamięci masowej obiektów Blob](scalability-targets.md)
- [Cele dotyczące skalowalności i wydajności dla standardowych kont magazynu](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
- [Kody stanu i błędów](/rest/api/storageservices/Status-and-Error-Codes2)
