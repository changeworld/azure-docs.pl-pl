---
title: Lista kontrolna wydajności i skalowalności magazynu tabel — usługa Azure Storage
description: Lista kontrolna sprawdzonych praktyk do użycia z magazynowaniem tabel w opracowywaniu aplikacji o wysokiej wydajności.
services: storage
author: tamram
ms.service: storage
ms.topic: overview
ms.date: 10/10/2019
ms.author: tamram
ms.subservice: tables
ms.openlocfilehash: 89581c8ae2fbdbb55a2abfbd527c8fdcf4b65761
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "75749550"
---
# <a name="performance-and-scalability-checklist-for-table-storage"></a>Lista kontrolna wydajności i skalowalności magazynu tabel

Firma Microsoft opracowała szereg sprawdzonych praktyk w zakresie tworzenia aplikacji o wysokiej wydajności z magazynem tabel. Ta lista kontrolna identyfikuje kluczowe praktyki, które deweloperzy mogą stosować w celu optymalizacji wydajności. Należy pamiętać o tych praktykach podczas projektowania aplikacji i w trakcie całego procesu.

Usługa Azure Storage ma cele dotyczące skalowalności i wydajności dla pojemności, szybkości transakcji i przepustowości. Aby uzyskać więcej informacji na temat celów skalowalności usługi Azure Storage, zobacz [Cele skalowalności i wydajności dla standardowych kont magazynu](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json) oraz cele [skalowalności i wydajności magazynu tabel](scalability-targets.md).

## <a name="checklist"></a>Lista kontrolna

W tym artykule organizuje sprawdzone praktyki dotyczące wydajności do listy kontrolnej, którą można wykonać podczas tworzenia aplikacji magazynu tabel.

| Gotowe | Kategoria | Zagadnienie dotyczące projektu |
| --- | --- | --- |
| &nbsp; |Cele skalowalności |[Czy można zaprojektować aplikację do używania nie więcej niż maksymalnej liczby kont magazynu?](#maximum-number-of-storage-accounts) |
| &nbsp; |Cele skalowalności |[Czy unikasz zbliżania się do limitów pojemności i transakcji?](#capacity-and-transaction-targets) |
| &nbsp; |Cele skalowalności |[Czy zbliżasz się do celów skalowalności dla jednostek na sekundę?](#targets-for-data-operations) |
| &nbsp; |Obsługa sieci |[Czy urządzenia po stronie klienta mają wystarczająco wysoką przepustowość i małe opóźnienia, aby osiągnąć wymaganą wydajność?](#throughput) |
| &nbsp; |Obsługa sieci |[Czy urządzenia po stronie klienta mają wysokiej jakości łącze sieciowe?](#link-quality) |
| &nbsp; |Obsługa sieci |[Czy aplikacja kliencka znajduje się w tym samym regionie co konto magazynu?](#location) |
| &nbsp; |Bezpośredni dostęp do klienta |[Czy używasz sygnatur dostępu współdzielonego (SAS) i współużytkowania zasobów między źródłami (CORS), aby umożliwić bezpośredni dostęp do usługi Azure Storage?](#sas-and-cors) |
| &nbsp; |Tworzenie partii |[Czy aktualizacje przetwarzania wsadów aplikacji przy użyciu transakcji grupy jednostek?](#batch-transactions) |
| &nbsp; |Konfiguracja .NET |[Czy używasz programu .NET Core 2.1 lub nowszego w celu uzyskania optymalnej wydajności?](#use-net-core) |
| &nbsp; |Konfiguracja .NET |[Czy klient skonfigurował do używania wystarczającej liczby równoczesnych połączeń?](#increase-default-connection-limit) |
| &nbsp; |Konfiguracja .NET |[Czy w przypadku aplikacji platformy .NET skonfigurowano program .NET do używania wystarczającej liczby wątków?](#increase-minimum-number-of-threads) |
| &nbsp; |Równoległości prostych |[Czy upewnij się, że równoległość jest ograniczona odpowiednio, tak aby nie przeciążać możliwości klienta lub podejście do celów skalowalności?](#unbounded-parallelism) |
| &nbsp; |Narzędzia |[Czy korzystasz z najnowszych wersji bibliotek i narzędzi klienckich dostarczonych przez firmę Microsoft?](#client-libraries-and-tools) |
| &nbsp; |Ponowne próby |[Czy używasz zasad ponawiania próby z wykładniczym wycofywania dla błędów ograniczania przepustowości i limitów czasu?](#timeout-and-server-busy-errors) |
| &nbsp; |Ponowne próby |[Czy aplikacja unika ponownych prób dla błędów, których nie można ponowić?](#non-retryable-errors) |
| &nbsp; |Konfigurowanie |[Czy używasz JSON dla żądań tabeli?](#use-json) |
| &nbsp; |Konfigurowanie |[Czy wyłączyłeś algorytm Nagle'a, aby poprawić wydajność małych żądań?](#disable-nagle) |
| &nbsp; |Tabele i partycje |[Czy poprawnie podzieliłeś swoje dane na partycje?](#schema) |
| &nbsp; |Gorące partycje |[Czy unikasz wzorców tylko do dołączania i dołączania?](#append-only-and-prepend-only-patterns) |
| &nbsp; |Gorące partycje |[Czy wstawia / aktualizacje rozłożone na wielu partycjach?](#high-traffic-data) |
| &nbsp; |Zakres zapytania |[Czy schemat został zaprojektowany tak, aby umożliwić korzystanie z zapytań punktowych w większości przypadków i kwerend tabeli, które mają być używane oszczędnie?](#query-scope) |
| &nbsp; |Gęstość zapytań |[Czy zapytania zazwyczaj tylko skanowania i zwracania wierszy, które aplikacja będzie używać?](#query-density) |
| &nbsp; |Ograniczanie zwracanych danych |[Czy używasz filtrowania, aby uniknąć zwracania jednostek, które nie są potrzebne?](#limiting-the-amount-of-data-returned) |
| &nbsp; |Ograniczanie zwracanych danych |[Czy używasz projekcji, aby uniknąć zwracania właściwości, które nie są potrzebne?](#limiting-the-amount-of-data-returned) |
| &nbsp; |Denormalizacja |[Czy zdenormalizowane dane w taki sposób, aby uniknąć nieefektywnych zapytań lub wielu żądań odczytu podczas próby uzyskania danych?](#denormalization) |
| &nbsp; |Wstawianie, aktualizowanie i usuwanie |[Czy wsadowanie żądań, które muszą być transakcyjne lub można zrobić w tym samym czasie, aby zmniejszyć round-trips?](#batching) |
| &nbsp; |Wstawianie, aktualizowanie i usuwanie |[Czy unikasz pobierania jednostki tylko w celu ustalenia, czy wywołać wstawianie lub aktualizowanie?](#upsert) |
| &nbsp; |Wstawianie, aktualizowanie i usuwanie |[Czy rozważano przechowywanie serii danych, które często będą pobierane razem w jednej jednostce jako właściwości zamiast wielu jednostek?](#storing-data-series-in-a-single-entity) |
| &nbsp; |Wstawianie, aktualizowanie i usuwanie |[Dla jednostek, które zawsze będą pobierane razem i mogą być zapisywane w partiach (na przykład dane szeregów czasowych), czy rozważano użycie obiektów blob zamiast tabel?](#storing-structured-data-in-blobs) |

## <a name="scalability-targets"></a>Cele skalowalności

Jeśli aplikacja zbliża się lub przekracza dowolny z celów skalowalności, może wystąpić zwiększone opóźnienia transakcji lub ograniczania przepustowości. Gdy usługa Azure Storage ogranicza aplikację, usługa zaczyna zwracać kody błędów 503 (serwer zajęty) lub 500 (limit czasu operacji). Unikanie tych błędów przez pozostawanie w granicach docelowych skalowalności jest ważną częścią zwiększenia wydajności aplikacji.

Aby uzyskać więcej informacji na temat celów skalowalności usługi Tabel, zobacz [Cele skalowalności i wydajności magazynu tabel](scalability-targets.md).

### <a name="maximum-number-of-storage-accounts"></a>Maksymalna liczba kont magazynu

Jeśli zbliżasz się do maksymalnej liczby kont magazynu dozwolonych dla określonej kombinacji subskrypcji/regionu, czy używasz wielu kont magazynu do niezależnego fragmentu w celu zwiększenia ruchu przychodzącego, ruchu wychodzącego, operacji we/wy na sekundę (IOPS) lub pojemności? W tym scenariuszu firma Microsoft zaleca korzystanie ze zwiększonych limitów dla kont magazynu, aby zmniejszyć liczbę kont magazynu wymaganych dla obciążenia, jeśli to możliwe. Skontaktuj się z [pomocą techniczną platformy Azure,](https://azure.microsoft.com/support/options/) aby zażądać zwiększenia limitów dla konta magazynu. Aby uzyskać więcej informacji, zobacz [Ogłaszanie większych, bardziej zakrojonych kont magazynu](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/).

### <a name="capacity-and-transaction-targets"></a>Cele dotyczące zdolności produkcyjnych i transakcji

Jeśli aplikacja zbliża się do celów skalowalności dla jednego konta magazynu, należy rozważyć przyjęcie jednego z następujących metod:  

- Ponowne rozważenie obciążenia, które powoduje, że aplikacja zbliża się lub przekracza cel skalowalności. Czy można zaprojektować go inaczej, aby zużywać mniej przepustowości lub pojemności lub mniej transakcji?
- Jeśli aplikacja musi przekraczać jeden z celów skalowalności, a następnie utworzyć wiele kont magazynu i partycjonować dane aplikacji na tych wielu kont magazynu. Jeśli używasz tego wzorca, a następnie należy zaprojektować aplikację, dzięki czemu można dodać więcej kont magazynu w przyszłości do równoważenia obciążenia. Same konta magazynowe nie mają żadnych kosztów innych niż użycie danych przechowywanych, dokonanych transakcji lub przesyłanych danych.
- Jeśli aplikacja zbliża się do docelowych przepustowości, należy wziąć pod uwagę kompresji danych po stronie klienta, aby zmniejszyć przepustowość wymaganą do wysyłania danych do usługi Azure Storage.
    Kompresja danych może oszczędzać przepustowość i poprawiać wydajność sieci, ale może mieć również negatywny wpływ na wydajność. Oceń wpływ dodatkowych wymagań dotyczących przetwarzania dodatkowych danych dotyczących kompresji i dekompresji danych po stronie klienta. Należy pamiętać, że przechowywanie skompresowanych danych może utrudnić rozwiązywanie problemów, ponieważ wyświetlanie danych przy użyciu standardowych narzędzi może być trudniejsze.
- Jeśli aplikacja zbliża się do celów skalowalności, upewnij się, że używasz wykładniczego wycofywania ponownych prób. Najlepiej jest unikać osiągnięcia docelowych skalowalności, implementując zalecenia opisane w tym artykule. Jednak przy użyciu wykładniczego wycofywania dla ponownych prób uniemożliwi aplikacji ponawianie próby szybko, co może pogorszyć ograniczanie przepustowości. Aby uzyskać więcej informacji, zobacz sekcję ["Timeout and Server Busy errors](#timeout-and-server-busy-errors)"

### <a name="targets-for-data-operations"></a>Cele dla operacji na danych

Równoważenie obciążenia usługi Azure Storage wraz ze wzrostem ruchu na koncie magazynu, ale jeśli ruch wykazuje nagłe wybuchy, może nie być w stanie natychmiast uzyskać ten wolumin przepływności. Spodziewaj się dławienia i/lub limitów czasu podczas serii, ponieważ usługa Azure Storage automatycznie równoważy obciążenie tabeli. Powolne zwiększanie się zapewnia lepsze wyniki, ponieważ system ma czas na odpowiednie równoważenie obciążenia.

#### <a name="entities-per-second-storage-account"></a>Jednostki na sekundę (konto magazynu)

Limit skalowalności dla uzyskiwania dostępu do tabel wynosi do 20 000 jednostek (po 1 KB) na sekundę dla konta. Ogólnie rzecz biorąc każda encja, która jest wstawiana, aktualizowana, usuwana lub skanowana, liczy się do tego celu. Tak więc wstawianie partii, który zawiera 100 jednostek będzie liczyć jako 100 jednostek. Kwerenda skanująca 1000 jednostek i zwracana wartość 5 będzie liczonych jako 1000 jednostek.

#### <a name="entities-per-second-partition"></a>Jednostki na sekundę (partycja)

W ramach jednej partycji miejsce docelowe skalowalności dostępu do tabel wynosi 2000 jednostek (1 KB każda) na sekundę, przy użyciu tego samego liczenia, jak opisano w poprzedniej sekcji.

## <a name="networking"></a>Obsługa sieci

Ograniczenia sieci fizycznej aplikacji mogą mieć znaczący wpływ na wydajność. W poniższych sekcjach opisano niektóre ograniczenia, które użytkownicy mogą napotkać.  

### <a name="client-network-capability"></a>Możliwości sieci klienta

Przepustowość i jakość łącza sieciowego odgrywają ważną rolę w wydajności aplikacji, zgodnie z opisem w poniższych sekcjach.

#### <a name="throughput"></a>Przepływność

W przypadku przepustowości problemem często są możliwości klienta. Większe wystąpienia platformy Azure mają karty sieciowe o większej pojemności, więc należy rozważyć użycie większego wystąpienia lub większej liczby maszyn wirtualnych, jeśli potrzebujesz wyższych limitów sieciowych z jednego komputera. Jeśli uzyskujesz dostęp do usługi Azure Storage z aplikacji lokalnej, stosuje się tę samą regułę: poznaj możliwości sieciowe urządzenia klienckiego i łączność sieciową z lokalizacją usługi Azure Storage i ulepsz je w razie potrzeby lub zaprojektuj aplikacji do pracy w ramach swoich możliwości.

#### <a name="link-quality"></a>Jakość łącza

Podobnie jak w przypadku każdego użycia sieci, należy pamiętać, że warunki sieciowe powodujące błędy i utratę pakietów spowolnią efektywną przepływność.  Korzystanie z WireShark lub NetMon może pomóc w diagnozowaniu tego problemu.  

### <a name="location"></a>Lokalizacja

W każdym środowisku rozproszonym umieszczenie klienta w pobliżu serwera zapewnia najlepszą wydajność. Aby uzyskać dostęp do usługi Azure Storage z najniższym opóźnieniem, najlepsza lokalizacja dla klienta znajduje się w tym samym regionie platformy Azure. Na przykład jeśli masz aplikację sieci web platformy Azure, która korzysta z usługi Azure Storage, a następnie zlokalizować je zarówno w jednym regionie, takich jak US West lub Azji Południowo-Wschodniej. Współlokowanie zasobów zmniejsza opóźnienie i koszty, ponieważ użycie przepustowości w jednym regionie jest bezpłatne.  

Jeśli aplikacje klienckie będą uzyskiwać dostęp do usługi Azure Storage, ale nie są hostowane na platformie Azure, takiej jak aplikacje urządzeń przenośnych lub lokalne usługi dla przedsiębiorstw, lokalizowanie konta magazynu w regionie w pobliżu tych klientów może zmniejszyć opóźnienia. Jeśli twoi klienci są szeroko rozpowszechnione (na przykład niektóre w Ameryce Północnej, a niektóre w Europie), a następnie należy rozważyć użycie jednego konta magazynu na region. Takie podejście jest łatwiejsze do zaimplementowania, jeśli dane przechowywane przez aplikację są specyficzne dla poszczególnych użytkowników i nie wymagają replikowania danych między kontami magazynu.

## <a name="sas-and-cors"></a>SAS i CORS

Załóżmy, że musisz autoryzować kod, taki jak JavaScript, który jest uruchomiony w przeglądarce sieci Web użytkownika lub w aplikacji na telefon komórkowy, aby uzyskać dostęp do danych w usłudze Azure Storage. Jednym z podejść jest tworzenie aplikacji usługi, która działa jako serwer proxy. Urządzenie użytkownika uwierzytelnia się za pomocą usługi, która z kolei autoryzuje dostęp do zasobów usługi Azure Storage. W ten sposób można uniknąć ujawniania kluczy konta magazynu na niezabezpieczonych urządzeniach. Jednak to podejście nakłada znaczne obciążenie na aplikację usługi, ponieważ wszystkie dane przesyłane między urządzeniem użytkownika i usługi Azure Storage musi przejść przez aplikację usługi.

Można uniknąć przy użyciu aplikacji usługi jako serwera proxy dla usługi Azure Storage przy użyciu sygnatur dostępu współdzielonego (SYGNAŁÓW DOSTĘPU WSPÓŁDZIELONEGO). Korzystając z usługi SAS, można włączyć urządzenia użytkownika do żądania bezpośrednio do usługi Azure Storage przy użyciu tokenu ograniczonego dostępu. Na przykład jeśli użytkownik chce przekazać zdjęcie do aplikacji, aplikacja usługi może wygenerować sygnatury dostępu Współdzielonego i wysłać go do urządzenia użytkownika. Token sygnatury dostępu Współdzielonego może udzielić uprawnień do zapisu w zasobie usługi Azure Storage przez określony interwał czasu, po którym token sygnatury dostępu Współdzielonego wygasa. Aby uzyskać więcej informacji na temat sygnatury dostępu współdzielonego, zobacz [Udzielanie ograniczonego dostępu do zasobów usługi Azure Storage przy użyciu sygnatur dostępu współdzielonego (SAS).](../common/storage-sas-overview.md)  

Zazwyczaj przeglądarka internetowa nie zezwala javascript na stronie, która jest obsługiwana przez witrynę sieci Web w jednej domenie do wykonywania niektórych operacji, takich jak operacje zapisu, do innej domeny. Zasady te, znane jako zasady tego samego źródła, uniemożliwiają złośliwemu skryptowi na jednej stronie uzyskanie dostępu do danych na innej stronie sieci Web. Jednak zasady tego samego źródła może być ograniczenie podczas tworzenia rozwiązania w chmurze. Udostępnianie zasobów między źródłami (CORS) to funkcja przeglądarki, która umożliwia domenie docelowej komunikowanie się z przeglądarką, w którym ufa żądań pochodzących z domeny źródłowej.

Załóżmy na przykład, że aplikacja sieci web uruchomiona na platformie Azure wywołuje żądanie zasobu do konta usługi Azure Storage. Aplikacja sieci web jest domeną źródłową, a konto magazynu jest domeną docelową. Usługę CORS można skonfigurować dla dowolnej usługi usługi Azure Storage do komunikowania się z przeglądarką sieci Web, zgodnie z którą żądania z domeny źródłowej są zaufane przez usługę Azure Storage. Aby uzyskać więcej informacji na temat usługi CORS, zobacz [obsługa udostępniania zasobów między źródłami (CORS) dla usługi Azure Storage](/rest/api/storageservices/Cross-Origin-Resource-Sharing--CORS--Support-for-the-Azure-Storage-Services).  
  
Zarówno SAS, jak i CORS mogą pomóc uniknąć niepotrzebnego obciążenia aplikacji sieci web.  

## <a name="batch-transactions"></a>Transakcje wsadowe

Usługa tabeli obsługuje transakcje wsadowe na jednostki, które znajdują się w tej samej tabeli i należą do tej samej grupy partycji. Aby uzyskać więcej informacji, zobacz [Wykonywanie transakcji grupy jednostek](/rest/api/storageservices/performing-entity-group-transactions).

## <a name="net-configuration"></a>Konfiguracja .NET

Jeśli używasz programu .NET Framework, w tej sekcji wymieniono kilka szybkich ustawień konfiguracji, których można użyć do znacznych ulepszeń wydajności.  Jeśli używasz innych języków, sprawdź, czy podobne pojęcia mają zastosowanie w wybranym języku.  

### <a name="use-net-core"></a>Użyj programu .NET Core

Tworzenie aplikacji usługi Azure Storage za pomocą platformy .NET Core 2.1 lub nowszej w celu skorzystania z ulepszeń wydajności. W miarę możliwości zalecane jest użycie programu .NET Core 3.x.

Aby uzyskać więcej informacji na temat poprawy wydajności w programie .NET Core, zobacz następujące wpisy w blogu:

- [Poprawa wydajności w programie .NET Core 3.0](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-core-3-0/)
- [Poprawa wydajności w programie .NET Core 2.1](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-core-2-1/)

### <a name="increase-default-connection-limit"></a>Zwiększ domyślny limit połączeń

W .NET następujący kod zwiększa domyślny limit połączenia (który jest zwykle 2 w środowisku klienckim lub 10 w środowisku serwera) do 100. Zazwyczaj należy ustawić wartość w przybliżeniu liczbę wątków używanych przez aplikację.  

```csharp
ServicePointManager.DefaultConnectionLimit = 100; //(Or More)  
```

Przed otwarciem jakichkolwiek połączeń ustaw limit połączenia.  

W przypadku innych języków programowania zobacz dokumentację tego języka, aby określić sposób ustawiania limitu połączeń.  

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

## <a name="configuration"></a>Konfigurowanie

W tej sekcji wymieniono kilka szybkich ustawień konfiguracji, których można użyć w celu dokonania znaczących ulepszeń wydajności w usłudze tabel:

### <a name="use-json"></a>Użyj JSON

Począwszy od wersji usługi magazynu 2013-08-15, usługa Tabela obsługuje przy użyciu JSON zamiast formatu AtomPub opartym na języku XML do przesyłania danych tabeli. Za pomocą JSON można zmniejszyć rozmiary ładunku nawet o 75% i może znacznie poprawić wydajność aplikacji.

Aby uzyskać więcej informacji, zobacz post [Microsoft Azure Tables: Introducing JSON](https://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/05/windows-azure-tables-introducing-json.aspx) and [Payload Format for Table Service Operations](https://msdn.microsoft.com/library/azure/dn535600.aspx).

### <a name="disable-nagle"></a>Wyłącz Nagle

Algorytm Nagle'a jest szeroko wdrażany w sieciach TCP/IP jako środek poprawy wydajności sieci. Jednak nie jest optymalny we wszystkich okolicznościach (takich jak środowiska wysoce interaktywne). Algorytm Nagle'ego ma negatywny wpływ na wydajność żądań do usługi Azure Table i należy ją wyłączyć, jeśli to możliwe.

## <a name="schema"></a>Schemat

Sposób reprezentowania i zapytania danych jest największym pojedynczym czynnikiem, który wpływa na wydajność usługi tabel. Chociaż każda aplikacja jest inna, w tej sekcji przedstawiono niektóre ogólne sprawdzone praktyki, które odnoszą się do:

- Projekt stołu
- Wydajne zapytania
- Wydajne aktualizacje danych

### <a name="tables-and-partitions"></a>Tabele i partycje

Tabele są podzielone na partycje. Każda jednostka przechowywana w partycji współużytkuje ten sam klucz partycji i ma unikatowy klucz wiersza, aby zidentyfikować go w tej partycji. Partycje zapewniają korzyści, ale także wprowadzają limity skalowalności.

- Korzyści: Można zaktualizować jednostki w tej samej partycji w pojedynczej, niepodzielnej transakcji wsadowej, która zawiera maksymalnie 100 oddzielnych operacji magazynowania (limit 4 MB całkowitego rozmiaru). Zakładając taką samą liczbę jednostek do pobrania, można również kwerendy danych w ramach jednej partycji bardziej efektywnie niż dane, które obejmuje partycje (choć czytaj dalej, aby uzyskać dalsze zalecenia dotyczące kwerendy danych tabeli).
- Limit skalowalności: Dostęp do jednostek przechowywanych w jednej partycji nie może być równoważenia obciążenia, ponieważ partycje obsługują atomowe transakcje wsadowe. Z tego powodu miejsce docelowe skalowalności dla poszczególnych partycji tabeli jest niższa niż dla usługi tabel jako całości.

Ze względu na te cechy tabel i partycji należy przyjąć następujące zasady projektowania:

- Zlokalizuj dane, które aplikacja kliencka często aktualizuje lub kwerendy w tej samej jednostce logicznej pracy w tej samej partycji. Na przykład zlokalizuj dane w tej samej partycji, jeśli aplikacja agreguje zapisy lub wykonujesz niepodzielne operacje wsadowe. Ponadto dane w jednej partycji mogą być bardziej efektywnie wyszukiwane w jednej kwerendzie niż dane na partycjach.
- Zlokalizuj dane, których aplikacja kliencka nie wstawia, nie aktualizuje ani nie wykonuje kwerendy w tej samej logicznej jednostce pracy (czyli w pojedynczej kwerendzie lub aktualizacji wsadowej) w oddzielnych partycjach. Należy pamiętać, że nie ma limitu liczby kluczy partycji w jednej tabeli, więc posiadanie milionów kluczy partycji nie stanowi problemu i nie wpłynie na wydajność. Na przykład jeśli aplikacja jest popularna witryna sieci Web z loginem użytkownika, przy użyciu identyfikatora użytkownika jako klucz partycji może być dobrym wyborem.

#### <a name="hot-partitions"></a>Gorące partycje

Partycja gorąca jest taki, który otrzymuje nieproporcjonalny procent ruchu do konta i nie może być równoważenia obciążenia, ponieważ jest to pojedyncza partycja. Ogólnie rzecz biorąc, gorące partycje są tworzone na dwa sposoby:

#### <a name="append-only-and-prepend-only-patterns"></a>Dołączanie tylko i dołączanie tylko wzorców

Wzorzec "Tylko dołączanie" jest taki, w którym cały (lub prawie wszystkie) ruchu do danego klucza partycji zwiększa się i zmniejsza w zależności od bieżącego czasu. Załóżmy na przykład, że aplikacja używa bieżącej daty jako klucza partycji dla danych dziennika. Ten projekt powoduje, że wszystkie wstawia będzie do ostatniej partycji w tabeli, a system nie może równoważyć obciążenia poprawnie. Jeśli wolumin ruchu do tej partycji przekracza docelową skalowalność na poziomie partycji, spowoduje to ograniczenie. Lepiej jest upewnić się, że ruch jest wysyłany do wielu partycji, aby włączyć równoważenie obciążenia żądań w całej tabeli.

#### <a name="high-traffic-data"></a>Dane o dużym natężeniu ruchu

Jeśli schemat partycjonowania powoduje, że pojedyncza partycja, która po prostu ma dane, które są znacznie bardziej używane niż inne partycje, można również zobaczyć ograniczanie, ponieważ ta partycja zbliża się do celu skalowalności dla pojedynczej partycji. Lepiej jest upewnić się, że schemat partycji powoduje, że żadna pojedyncza partycja zbliża się do celów skalowalności.

### <a name="querying"></a>Wykonywanie zapytania

W tej sekcji opisano sprawdzone praktyki dotyczące wykonywania zapytań o usługę tabel.

#### <a name="query-scope"></a>Zakres zapytania

Istnieje kilka sposobów, aby określić zakres jednostek do kwerendy. Na poniższej liście opisano każdą opcję dla zakresu kwerendy.

- **Kwerendy punktowe:**— kwerenda punktowa pobiera dokładnie jedną jednostkę, określając klucz partycji i klucz wiersza jednostki do pobrania. Te zapytania są wydajne i należy ich używać tam, gdzie to możliwe.
- **Zapytania o partycje:** Kwerenda partycji to kwerenda, która pobiera zestaw danych, który współumieje wspólny klucz partycji. Zazwyczaj kwerenda określa zakres wartości klucza wiersza lub zakres wartości dla niektórych właściwości jednostki oprócz klucza partycji. Te zapytania są mniej wydajne niż zapytania punktowe i powinny być używane oszczędnie.
- **Kwerendy tabeli:** Kwerenda tabeli to kwerenda pobierająca zestaw jednostek, które nie mają wspólnego klucza partycji. Te zapytania nie są skuteczne i należy ich unikać, jeśli to możliwe.

Ogólnie rzecz biorąc, należy unikać skanowania (kwerend większych niż pojedyncza jednostka), ale jeśli musisz skanować, spróbuj uporządkować dane, tak aby skany pobierał dane, których potrzebujesz bez skanowania lub zwracania znacznych ilości jednostek, które nie są potrzebne.

#### <a name="query-density"></a>Gęstość zapytań

Innym kluczowym czynnikiem wydajności kwerendy jest liczba jednostek zwróconych w porównaniu do liczby jednostek zeskanowanych w celu znalezienia zwróconego zestawu. Jeśli aplikacja wykonuje kwerendę tabeli z filtrem dla wartości właściwości, która tylko 1% udziałów danych, kwerenda skanuje 100 jednostek dla każdej jednostki zwraca. Omówione wcześniej cele skalowalności tabeli odnoszą się do liczby zeskanowanych jednostek, a nie do liczby zwróconych jednostek: niska gęstość zapytań może łatwo spowodować, że usługa Tabela ograniczy aplikację, ponieważ musi skanować tak wiele jednostek, aby pobierać jednostkę, której szukasz. Aby uzyskać więcej informacji na temat unikania ograniczania przepustowości, zobacz sekcję zatytułowaną [Denormalization](#denormalization).

#### <a name="limiting-the-amount-of-data-returned"></a>Ograniczanie ilości zwracanych danych

Jeśli wiadomo, że kwerenda zwróci jednostki, które nie są potrzebne w aplikacji klienckiej, należy rozważyć użycie filtru, aby zmniejszyć rozmiar zwracanego zestawu. Podczas gdy jednostki, które nie zostały zwrócone do klienta, nadal są wliczane do limitów skalowalności, wydajność aplikacji zostanie poprawina ze względu na zmniejszony rozmiar ładunku sieciowego i zmniejszoną liczbę jednostek, które aplikacja kliencka musi przetworzyć. Należy pamiętać, że cele skalowalności odnoszą się do liczby skanowanych jednostek, więc kwerenda, która odfiltruje wiele jednostek, może nadal powodować ograniczanie przepustowości, nawet jeśli zwracanych jest kilka jednostek. Aby uzyskać więcej informacji na temat efektywnego wykonywania zapytań, zobacz sekcję [zatytułowaną Gęstość kwerend](#query-density).

Jeśli aplikacja kliencka potrzebuje tylko ograniczony zestaw właściwości z jednostek w tabeli, można użyć projekcji, aby ograniczyć rozmiar zwracanego zestawu danych. Podobnie jak w filtrowaniu, projekcja pomaga zmniejszyć obciążenie sieci i przetwarzanie klienta.

#### <a name="denormalization"></a>Denormalizacja

W przeciwieństwie do pracy z relacyjnych baz danych, sprawdzone praktyki efektywnego wykonywania zapytań danych tabeli prowadzą do denormalizacji danych. Oznacza to, że powielanie tych samych danych w wielu jednostkach (po jednym dla każdego klucza, którego można użyć do znalezienia danych), aby zminimalizować liczbę jednostek, które kwerenda musi skanować w celu znalezienia danych, których potrzebuje klient, zamiast skanowania dużej liczby jednostek w celu znalezienia danych, których dane należy odnaleźć potrzeb aplikacji. Na przykład w witrynie e-commerce możesz znaleźć zamówienie zarówno według identyfikatora klienta (przekaż mi zamówienia tego klienta), jak i do daty (złóż mi zamówienia w dniu). W magazynie tabel najlepiej jest przechowywać jednostkę (lub odwołanie do niej) dwa razy — raz z nazwą tabeli, PK i RK, aby ułatwić znajdowanie według identyfikatora klienta, raz, aby ułatwić znalezienie go w terminie.  

### <a name="insert-update-and-delete"></a>Wstawianie, aktualizowanie i usuwanie

W tej sekcji opisano sprawdzone praktyki modyfikowania jednostek przechowywanych w usłudze tabel.  

#### <a name="batching"></a>Tworzenie partii

Transakcje wsadowe są nazywane transakcjami grupy jednostek w usłudze Azure Storage. Wszystkie operacje w ramach transakcji grupy jednostek musi znajdować się na jednej partycji w jednej tabeli. Jeśli to możliwe, użyj transakcji grupy jednostek do wykonywania wstawia, aktualizuje i usuwa w partiach. Korzystanie z transakcji grupy jednostek zmniejsza liczbę rund z aplikacji klienckiej do serwera, zmniejsza liczbę transakcji podlegających rozliczaniu (transakcja grupy jednostek jest liczna jako pojedyncza transakcja do celów rozliczeniowych i może zawierać maksymalnie 100 operacji magazynowania) i umożliwia aktualizacje atomowe (wszystkie operacje zakończyć się powodzeniem lub wszystkie zakończyć się niepowodzeniem w ramach transakcji grupy jednostek). Środowiska o dużych opóźnieniach, takie jak urządzenia przenośne, będą znacznie korzystać z wykorzystaniem transakcji grupy jednostek.  

#### <a name="upsert"></a>Upsert

W miarę możliwości używaj operacji **upsert tabeli.** Istnieją dwa **typy upsert**, z których oba mogą być bardziej wydajne niż tradycyjne operacje **wstawiania** i **aktualizacji:**  

- **InsertOrMerge**: Użyj tej operacji, jeśli chcesz przekazać podzbiór właściwości jednostki, ale nie masz pewności, czy jednostka już istnieje. Jeśli jednostka istnieje, to wywołanie aktualizuje właściwości uwzględnione w operacji **Upsert** i pozostawia wszystkie istniejące właściwości w takim stanie, w jakim są, jeśli jednostka nie istnieje, wstawia nową encję. Jest to podobne do korzystania z projekcji w kwerendzie, w tym wystarczy przekazać właściwości, które się zmieniają.
- **InsertOrReplace**: Użyj tej operacji, jeśli chcesz przekazać całkowicie nową jednostkę, ale nie masz pewności, czy już istnieje. Użyj tej operacji, gdy wiadomo, że nowo przekazana encja jest całkowicie poprawna, ponieważ całkowicie zastępuje starą encję. Na przykład chcesz zaktualizować jednostkę, która przechowuje bieżącą lokalizację użytkownika, niezależnie od tego, czy aplikacja wcześniej przechowywała dane o lokalizacji dla użytkownika; nowa jednostka lokalizacji została ukończona i nie potrzebujesz żadnych informacji z żadnej poprzedniej jednostki.

#### <a name="storing-data-series-in-a-single-entity"></a>Przechowywanie serii danych w jednej jednostce

Czasami aplikacja przechowuje serię danych, które często musi pobrać wszystkie na raz: na przykład aplikacja może śledzić użycie procesora CPU w czasie, aby wykreślić wykres kroczący danych z ostatnich 24 godzin. Jednym z podejść jest mieć jedną jednostkę tabeli na godzinę, z każdej jednostki reprezentujące określonej godziny i przechowywania użycia procesora CPU dla tej godziny. Aby wykreślić te dane, aplikacja musi pobrać jednostki przechowujące dane z 24 ostatnich godzin.  

Alternatywnie aplikacja może przechowywać użycie procesora CPU dla każdej godziny jako osobną właściwość pojedynczej jednostki: aby aktualizować co godzinę, aplikacja może użyć pojedynczego **wywołania InsertOrMerge Upsert,** aby zaktualizować wartość dla ostatniej godziny. Aby wykreślić dane, aplikacja musi tylko pobrać jedną jednostkę zamiast 24, co dla wydajnej kwerendy. Aby uzyskać więcej informacji na temat wydajności kwerendy, zobacz sekcję [zatytułowaną Zakres kwerendy](#query-scope)).

#### <a name="storing-structured-data-in-blobs"></a>Przechowywanie danych strukturalnych w obiektach blob

Jeśli wykonujesz wstawia wsadowe, a następnie pobierasz zakresy jednostek razem, należy rozważyć użycie obiektów blob zamiast tabel. Dobrym przykładem jest plik dziennika. Można partii kilka minut dzienników i wstawić je, a następnie pobrać kilka minut dzienników naraz. W takim przypadku wydajność jest lepsza, jeśli używasz obiektów blob zamiast tabel, ponieważ można znacznie zmniejszyć liczbę obiektów zapisywanych lub odczytywanych, a także ewentualnie liczbę żądań, które wymagają.  

## <a name="next-steps"></a>Następne kroki

- [Cele dotyczące skalowalności i wydajności pamięci masowej tabel](scalability-targets.md)
- [Cele dotyczące skalowalności i wydajności dla standardowych kont magazynu](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json)
- [Kody stanu i błędów](/rest/api/storageservices/Status-and-Error-Codes2)
