---
title: Azure zagadnienia wydajności i optymalizacji wyszukiwania | Dokumentacja firmy Microsoft
description: Dostrajanie wydajności usługi Azure Search i skonfigurować optymalnej skali
author: LiamCavanagh
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 05/01/2017
ms.author: liamca
ms.openlocfilehash: 89c0352723f1ed00784250b566902028af853d10
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
---
# <a name="azure-search-performance-and-optimization-considerations"></a>Azure zagadnienia wydajności i optymalizacji wyszukiwania
Wyszukiwanie dużą jest kluczem do powodzenia dla wielu urządzeń przenośnych i aplikacji sieci web. Z nieruchomości używane rynków samochodu online wykazów szybkiego wyszukiwania i odpowiednie wyniki będzie miało wpływ na wrażenia użytkowników. Ten dokument jest przeznaczony pomocy odnajdywanie obsługę najlepsze rozwiązania dotyczące sposobu uzyskania maksymalne wykorzystanie usługi Azure Search, szczególnie w przypadku zaawansowanych scenariuszy z zaawansowanymi wymaganiami skalowalności, obsługi wielu języków lub niestandardowych klasyfikacji.  Ponadto ten dokument przedstawia ustawienia wewnętrzne i obejmuje metodami działał efektywnie w aplikacjach rzeczywistych klientów.

## <a name="performance-and-scale-tuning-for-search-services"></a>Wydajność i skalę dostrajania dla usługi wyszukiwania
Firma Microsoft używane wyszukiwarki, takiej jak Bing czy Google oraz wysokiej wydajności, które oferują.  W związku z tym gdy klienci użyć wyszukiwania sieci web lub aplikacji mobilnej, będzie oczekiwać podobne charakterystyki wydajności.  Podczas optymalizacji wydajności wyszukiwania, jednym z najlepszych metod jest skupić się na opóźnienia, które jest czas kwerendy zakończenie, a następnie zwracają wyniki.  Podczas optymalizacji dla opóźnienia wyszukiwania ważne jest, aby:

1. Wybierz z docelowym czasem oczekiwania (lub maksymalną ilość czasu), który żądania wyszukiwania typowych powinien wykonać, aby zakończyć.
2. Tworzenie i testowanie rzeczywiste obciążenie wysyłane do usługi wyszukiwania z realistyczne zestawu danych do mierzenia tych wskaźników opóźnienia.
3. Rozpoczynać się od małej liczbie zapytania na sekundę (QPS) i kontynuować zwiększenie liczby wykonywanych w teście dopóki opóźnienie kwerendy nie spadnie poniżej zdefiniowanego docelowym czasem oczekiwania.  Jest to ważne testu porównawczego pomocne skali wraz z rozwojem aplikacji wykorzystania.
4. W miarę możliwości ponownego użycia połączenia HTTP.  Jeśli korzystasz z zestawu .NET SDK usługi Azure Search, oznacza to, należy używać ponownie wystąpienia lub [SearchIndexClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient) wystąpienia, i korzystasz z interfejsu API REST, należy używać ponownie pojedynczego HttpClient.

Podczas tworzenia tych testów obciążenia, istnieją pewne cechy usługi Azure Search, należy wziąć pod uwagę:

1. Użytkownik może wypchnąć tak wielu wyszukiwania zapytania w tym samym czasie, że będzie przeciążony zasoby dostępne w usłudze Azure Search.  W takim przypadku zobaczysz kody odpowiedzi HTTP 503.  Z tego powodu najlepiej zacząć z różnych zakresów żądania wyszukiwania, aby zobaczyć różnice w stawki opóźnienia, jak dodać więcej żądań wyszukiwania.
2. Przekazywanie zawartości do usługi Azure Search będzie mieć wpływ na ogólną wydajność i opóźnienia usługi Azure Search.  Jeśli chcesz wysłać danych, gdy użytkownicy wykonują wyszukiwania, ważne jest podjęcie to obciążenie pod uwagę w testach.
3. Nie wszystkie zapytania wyszukiwania będzie wykonywać w taki sam poziom wydajności.  Na przykład sugestii wyszukiwania lub wyszukiwania dokumentu zazwyczaj będzie wykonywać szybciej niż zapytanie z znaczących aspektów i filtry.  Najlepiej wykonać różne zapytania, które powinny być widoczne pod uwagę podczas tworzenia testów.  
4. Odmiana żądania wyszukiwania jest ważne, ponieważ Jeśli stale wykonać takie same żądania wyszukiwania, buforowanie danych zostanie uruchomiona dokonanie lepsze niż mogą za pomocą kwerendy więcej różnych ustawiana wydajności.

> [!NOTE]
> [Visual Studio obciążenia testowania](https://www.visualstudio.com/docs/test/performance-testing/run-performance-tests-app-before-release) naprawdę dobrym sposobem wykonania testu z testów, ponieważ pozwala na wykonanie żądania HTTP, która byłaby potrzebna do wykonywania zapytań dotyczących usługi Azure Search i możliwość paralelizacja żądań.
> 
> 

## <a name="scaling-azure-search-for-high-query-rates-and-throttled-requests"></a>Skalowanie usługi Azure Search dla zapytania wysokiej szybkości i ograniczenie żądań
Gdy otrzymują zbyt wiele żądań z ograniczeniem przepustowości lub przekracza stawki opóźnienia sieci docelowej z zapytania zwiększenie obciążenia, mogą zobaczyć zmniejszyć opóźnienia kursy w jeden z dwóch sposobów:

1. **Zwiększ replik:** repliki przypomina kopię stosowanie usługi Azure Search w celu zrównoważenia obciążenia żądaniami przed wiele kopii danych.  Wszystkie Równoważenie obciążenia i replikacji danych między replikami jest zarządzane przez usługę Azure Search i liczba replik przydzielone dla usługi w dowolnej chwili można zmienić.  Możesz przydzielić maksymalnie 12 replik w standardową usługę wyszukiwania i 3 replik w usłudze wyszukiwania podstawowego. Repliki mogą być dostosowane z [portalu Azure](search-create-service-portal.md) lub [PowerShell](search-manage-powershell.md).
2. **Zwiększ wyszukiwania warstwy:** usługa Azure Search jest dostarczany [liczba warstw](https://azure.microsoft.com/pricing/details/search/) i każdy z tych warstw oferuje różne poziomy wydajności.  W niektórych przypadkach może być tak wiele zapytań warstwy, które znajdują się na nie może dostarczyć szybkości wystarczająco małe opóźnienia, nawet wtedy, gdy limit maksymalnego wykorzystania replik.  W takim przypadku warto wziąć pod uwagę wykorzystanie jednej z wyższych warstw wyszukiwania takich jak warstwę S3 wyszukiwanie Azure, która jest odpowiednia w scenariuszach z dużą liczbą dokumentów i bardzo dużej zapytania obciążeń.

## <a name="scaling-azure-search-for-slow-individual-queries"></a>Skalowanie usługi Azure Search dla poszczególnych zapytań powolne
Kolejny powód, dlaczego stawki opóźnienia może działać powoli pochodzi z pojedynczego zapytania zbyt długo.  W takim przypadku Dodawanie repliki nie spowoduje zwiększenie szybkości opóźnienia.  Dla tego przypadku dostępne są dwie opcje:

1. **Zwiększ partycje** partycja to mechanizm służący do dzielenia danych na dodatkowe zasoby.  Z tego powodu po dodaniu drugiej partycji, dane pobiera podzielić na dwa.  Trzeci partycji dzieli indeksu na trzech itp.  Ma to również wpływ, że w niektórych przypadkach powolne zapytania wykona szybciej z powodu paralelizacja obliczeń.  Istnieje kilka przykładów którym zaobserwowano tego paralelizacja bardzo dobrze współpracować zapytań, które mają niski wybieralność zapytania.  Ten krok składa się kwerend spełniających wiele dokumentów lub gdy tworzenie aspektów należy podać liczby za pośrednictwem dużej liczby dokumentów.  Ponieważ istnieje wiele obliczeń potrzebne wynik dokładność dokumentów lub liczba liczby dokumentów, dodawanie dodatkowe partycje mogą pomóc określić dodatkowe obliczeń.  
   
   Może istnieć maksymalnie 12 partycji w standardową usługę wyszukiwania i 1 partycji w usłudze wyszukiwania podstawowego.  Partycje mogą być dostosowane z [portalu Azure](search-create-service-portal.md) lub [PowerShell](search-manage-powershell.md).
2. **Limit wysokiej pola Kardynalność:** pola dużej kardynalności składa się z aspektów lub Filtrowanie pola, które ma znaczący liczbę unikatowych wartości i w związku z tym zajmuje dużo zasobów do obliczenia wyniki za pośrednictwem.   Na przykład ustawienie pola Identyfikator produktu lub opisu jako aspektów/filtrowanie spowodowałoby dla dużej kardynalności ponieważ większość wartości z dokumentu do dokumentu są unikatowe. Gdy jest to możliwe, należy ograniczyć liczbę pól dużej kardynalności.
3. **Zwiększ wyszukiwania warstwy:** przenoszenia do wyższego poziomu usługi Azure Search można poprawić wydajność kwerend wolne w inny sposób.  Każdy wyższego poziomu także szybsze Procesora i większa ilość pamięci, które mogą mieć pozytywny wpływ na wydajność kwerend.

## <a name="scaling-for-availability"></a>Skalowanie dla dostępności
Replik nie tylko zmniejszenia opóźnienia kwerendy, ale może również umożliwiać wysokiej dostępności.  Po aktualizacji oprogramowania lub inne zdarzenia konserwacji, które będą przeprowadzane z pojedynczą replikę, należy oczekiwać okresowe przestoje z powodu ponownego uruchomienia serwera.  W związku z tym należy wziąć pod uwagę, jeśli aplikacja wymaga wysokiej dostępności wyszukiwania (queries) oraz zapisów (indeksowania zdarzeń).  Usługa Azure Search udostępnia opcje SLA na wszystkie oferty płatnej wyszukiwania z następującymi atrybutami:

* 2 replik wysokiej dostępności obciążeń tylko do odczytu (queries)
* 3 lub więcej replik wysokiej dostępności obciążeń odczytu i zapisu (zapytań i indeksowanie)

Więcej szczegółów na ten, odwiedź stronę [umową dotyczącą poziomu usług wyszukiwanie Azure](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

Ponieważ repliki są kopie danych, mających wiele replik umożliwia wyszukiwanie Azure do komputera podczas ponownych rozruchów i konserwacji względem jednej replice w czasie, umożliwiając zapytania, aby kontynuować do wykonania innych replik.  Z tego powodu należy również wziąć pod uwagę, jak to przestoju może mieć wpływ na zapytania, które są obecnie musi być wykonywane co mniej kopię danych.

## <a name="scaling-geo-distributed-workloads-and-provide-geo-redundancy"></a>Skalowanie rozproszona geograficznie obciążeń i zapewnić nadmiarowość geograficzna
W przypadku obciążeń rozproszona geograficznie zostanie ustalone, że użytkownicy znajdujący się daleko od centrum danych, gdzie jest hostowana usługi Azure Search większe opóźnienia.  Z tego powodu należy często mają wiele usług wyszukiwania w regionach, które znajdują się w zbliża dla tych użytkowników.  Usługa Azure Search nie ma obecnie zautomatyzowaną metodę replikację geograficzną indeksów usługi Azure Search w regionach, ale istnieją pewne techniki, które mogą być używane które mogą ułatwić ten proces prosta do wdrożenia i zarządzania nimi. Te zostały opisane w kolejnych sekcjach kilku.

Celem rozproszona geograficznie zbiór usług wyszukiwania jest ma dwa lub więcej indeksów, dostępne w dwóch lub więcej regionach, gdzie użytkownika będą kierowane do usługi Azure Search, która udostępnia najniższym opóźnieniu, jak pokazano w poniższym przykładzie:

   ![Między — karta usług według regionu][1]

### <a name="keeping-data-in-sync-across-multiple-azure-search-services"></a>Synchronizacja danych w wielu usługach Azure Search
Dostępne są dwie opcje przechowywania wyszukiwania rozproszonego usługi synchronizacji składające się z przy użyciu [indeksatora wyszukiwania Azure](search-indexer-overview.md) lub interfejsu API Push (zwaną także [interfejsu API REST wyszukiwania Azure](https://docs.microsoft.com/rest/api/searchservice/)).  

### <a name="azure-search-indexers"></a>Indeksatory w usłudze Azure Search
Jeśli używasz indeksatora wyszukiwania Azure są już importowanie zmiany danych z centralnego magazynu danych, takich jak bazy danych SQL Azure lub bazy danych Azure rozwiązania Cosmos. Podczas tworzenia nowego wyszukiwania usługi, należy po prostu również tworzyć nowe indeksatora wyszukiwania Azure dla tej usługi, wskazujące tego samego magazynu danych. W ten sposób po wejścia nowych zmian w magazynie danych one będą następnie indeksowane według różnych indeksatorów.  

Oto przykład jak będzie wyglądać danej architekturze.

   ![Pojedyncze źródło danych z kombinacji usługi i rozproszone indeksatora][2]

### <a name="push-api"></a>Wypychanie interfejsu API
Jeśli używasz wyszukiwania Push interfejsu API Azure do [zaktualizować zawartość w indeksie usługi Azure Search](https://docs.microsoft.com/rest/api/searchservice/update-index), można zachować na różne usługi wyszukiwania w synchronizacji przez wypychanie zmiany do wszystkich usług wyszukiwania, gdy wymagana jest aktualizacja.  W ten sposób koniecznie upewnij się, że obsługa przypadkach niepowodzenia aktualizacji do usługi wyszukiwania jednego i co najmniej jednej aktualizacji powiodło się.

## <a name="leveraging-azure-traffic-manager"></a>Korzystanie z usług Azure Traffic Manager
[Menedżer ruchu Azure](../traffic-manager/traffic-manager-overview.md) umożliwia rozsyłanie żądań do wielu znajdujący się geograficznie witryn sieci Web, który następnie bazują na wielu usług wyszukiwanie Azure.  Jedną z zalet Menedżera ruchu jest, że może ona sondowania usługi Azure Search, upewnij się, że jest dostępny i kierować użytkowników do usługi wyszukiwania alternatywny w przypadku przestoju.  Ponadto jeśli są routingu żądań wyszukiwania do witryn sieci Web platformy Azure, Azure Traffic Manager umożliwia ładowania saldo przypadku witryny sieci Web się, ale nie usługi Azure Search.  Oto przykład jakie architektury, który korzysta z Menedżera ruchu.

   ![Między — karta usług według regionu, centralnej menedżera ruchu][3]

## <a name="monitoring-performance"></a>Monitorowanie wydajności
Usługa Azure Search udostępnia możliwość analizować i monitorować wydajność usługi za pośrednictwem [analizy ruchu wyszukiwania (STA)](search-traffic-analytics.md). Za pomocą STA można opcjonalnie logowania do konta usługi Azure Storage, które następnie mogą być przetwarzane do analizy lub wizualizowane w usłudze Power BI operacji wyszukiwania poszczególnych, jak również zagregowanej metryki.  Metryki STA można przeglądać statystyki, takie jak średnia liczba zapytań lub czas odpowiedzi na zapytania.  Ponadto rejestrowanie operacji umożliwia przejście do szczegółów wyszukiwania określonej operacji.

STA jest przydatnym narzędziem do zrozumienia stawki opóźnienia z punktu widzenia tej usługi Azure Search.  Ponieważ metryki wydajności kwerendy rejestrowane są oparte na czas kwerendy pełnego przetworzenia w usłudze Azure Search (od momentu, gdy jest wymagane, gdy zostaną wysłane), to można użyć do ustalenia, czy istnieją problemy z opóźnieniem są po stronie usługi Azure Search lub dokumentów IDE usługi, takie od opóźnienia sieci.  

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej na temat cen limitów warstw i usług dla każdego z nich, zobacz [usługi limity w usłudze Azure Search](search-limits-quotas-capacity.md).

Odwiedź stronę [planowania pojemności](search-capacity-planning.md) Aby dowiedzieć się więcej na temat kombinacji partycji i replik.

Aby uzyskać więcej Przechodzenie do szczegółów na wydajność i niektóre pokazów implementowania optymalizacje omówione w tym artykule film wideo następujące:

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON319/player]
> 
> 

<!--Image references-->
[1]: ./media/search-performance-optimization/geo-redundancy.png
[2]: ./media/search-performance-optimization/scale-indexers.png
[3]: ./media/search-performance-optimization/geo-search-traffic-mgr.png
