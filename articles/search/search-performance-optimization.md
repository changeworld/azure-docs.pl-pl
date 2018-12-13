---
title: Usługa Azure Search wydajność i optymalizacja uwagi — usługa Azure Search
description: Dowiedz się, techniki i najlepsze rozwiązania dotyczące dostrajania wydajności usługi Azure Search i konfigurowania optymalnej skali.
author: LiamCavanagh
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 05/01/2017
ms.author: liamca
ms.custom: seodec2018
ms.openlocfilehash: 0a98e7f05e766d47a5ea9293409a74a6fafbf837
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/12/2018
ms.locfileid: "53310256"
---
# <a name="azure-search-performance-and-optimization-considerations"></a>Usługa Azure Search wydajność i optymalizacja zagadnienia
Doskonałe przeszukiwanie jest kluczem do sukcesu dla wielu urządzeń przenośnych i aplikacji sieci web. Z nieruchomości używane samochodu rynków online wykazów szybkiego wyszukiwania i odpowiednie wyniki wpłynie na obsługę klientów. Ten dokument jest przeznaczony do pomocy odkryjesz, że obsługuje najlepsze rozwiązania dotyczące sposobu uzyskania maksymalnie wykorzystać możliwości usługi Azure Search, szczególnie w przypadku zaawansowanych scenariuszy za pomocą zaawansowanych wymagania dotyczące skalowalności, wielu języków lub niestandardowych klasyfikacji.  Ponadto w tym dokumencie opisano elementy wewnętrzne i obejmuje metod, które wydajnie pracować w aplikacji klienta w rzeczywistych warunkach.

## <a name="performance-and-scale-tuning-for-search-services"></a>Wydajności i skalowania automatycznego dostrajania dla usługi wyszukiwania
Firma Microsoft wszystkie służą do wyszukiwarki Bing i Google oraz o wysokiej wydajności, które oferują one.  W rezultacie gdy klienci korzystają z włączoną funkcją wyszukiwania w sieci web lub aplikacji mobilnej, będzie oczekiwać podobne charakterystyki wydajności.  Podczas optymalizacji wydajności wyszukiwania, jednym z najlepszych metod jest skupić się na czas oczekiwania, czyli czasu potrzebnego zapytania, aby zakończyć i zwracają wyniki.  Optymalizacja dla opóźnienie wyszukiwania jest ważne, aby:

1. Wybierz z docelowym czasem oczekiwania (lub maksymalną ilość czasu), które żądania w typowym wyszukiwaniu powinien wykonać, aby zakończyć.
2. Tworzenie i testowanie rzeczywiste obciążenie dla usługi wyszukiwania przy użyciu realistycznej zestaw danych, aby zmierzyć te stawki opóźnienia.
3. Rozpocznij od małej liczbie zapytań na sekundę (QPS) i zwiększyć liczbę wykonane w teście, dopóki opóźnienie kwerendy nie spadnie poniżej zdefiniowanego docelowym czasem oczekiwania w dalszym ciągu.  Jest to ważne testów porównawczych pomocne dla skalowania w miarę wzrostu użycia w aplikacji.
4. Wszędzie tam, gdzie to możliwe, należy ponownie używać połączeń HTTP.  Korzystając z zestawu .NET SDK usługi Azure Search, oznacza to, należy używać ponownie wystąpienie lub [SearchIndexClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient) wystąpienia i korzystają z interfejsu API REST, należy używać ponownie pojedynczego HttpClient.

Podczas tworzenia tych testów obciążenia, istnieją pewne cechy usługi Azure Search, które należy uwzględnić:

1. Istnieje możliwość wypychania tak wiele wyszukiwania zapytań w tym samym czasie, że zasoby dostępne w usłudze Azure Search będzie przeciążeniu.  W takim przypadku zobaczysz kody odpowiedzi HTTP 503.  Z tego powodu najlepiej rozpocząć z różnymi zakresami żądania wyszukiwania, aby zobaczyć różnice w kursach opóźnienia, jak dodać więcej żądań wyszukiwania.
2. Przekazywanie zawartości do usługi Azure Search będzie miało wpływ na ogólną wydajność i czas oczekiwania usługi Azure Search.  Jeśli spodziewasz się do przesyłania danych, gdy użytkownicy wprowadzają wyszukiwania, należy wykonać to obciążenie pod uwagę w testach.
3. Nie wszystkie zapytania wyszukiwania zostanie przeprowadzone na tym samym poziomy wydajności.  Na przykład sugestii wyszukiwania lub wyszukiwania dokumentu zazwyczaj wykona szybciej niż zapytanie o znaczna liczba zestawów reguł i filtrów.  Najlepiej wykonać różne zapytania, który powinna się pojawić się pod uwagę podczas tworzenia testów.  
4. Zmiana żądań wyszukiwania jest ważne, ponieważ jeśli takie same żądania wyszukiwania są wykonywane stale, buforowania danych, rozpocznie się zapewnienie wydajności wyglądają lepiej niż go przy użyciu bardziej różnorodnych zapytania ustawić.

> [!NOTE]
> [Testowanie programu Visual Studio obciążenia](https://www.visualstudio.com/docs/test/performance-testing/run-performance-tests-app-before-release) jest naprawdę dobre sposób wykonywania Twojego testu porównawczego testy jako umożliwia wykonywanie żądań HTTP, która byłaby potrzebna do wykonywania zapytań względem usługi Azure Search i umożliwia przetwarzanie równoległe żądania.
> 
> 

## <a name="scaling-azure-search-for-high-query-rates-and-throttled-requests"></a>Skalowanie usługi Azure Search zapytania o wysokiej szybkości i ograniczenia żądania
Otrzymujesz zbyt wiele żądań ograniczenia lub przekracza ceny opóźnienie docelowej z obciążenie zapytaniami zwiększona, możesz przejrzeć zmniejszyć opóźnienie kursy w jeden z dwóch sposobów:

1. **Zwiększ repliki:**  Replika jest jak kopię danych, dzięki czemu usługa Azure Search równoważyć obciążenie żądaniami względem wielu kopii.  Wszystkie Równoważenie obciążenia i replikacja danych za pośrednictwem replik jest zarządzana przez usługę Azure Search można zmienić liczbę replik przydzielone dla usługi w dowolnym momencie.  Możesz przydzielić maksymalnie 12 replik w standardową usługę wyszukiwania i 3 repliki w usłudze wyszukiwania podstawowego. Repliki mogą być dostosowane z [witryny Azure portal](search-create-service-portal.md) lub [PowerShell](search-manage-powershell.md).
2. **Zwiększ warstwy wyszukiwania:**  Usługa Azure Search jest dostępna w [liczba warstw](https://azure.microsoft.com/pricing/details/search/) i każdy z tych warstw oferuje różne poziomy wydajności.  W niektórych przypadkach może mieć wiele zapytań warstwy, które znajdują się na nie może dostarczyć stawek wystarczająco małe opóźnienia, nawet wtedy, gdy replik maksymalnego limitu.  W takim przypadku warto rozważyć korzystanie z jednej z warstw wyszukiwania wyższej, np. warstwa S3 wyszukiwania platformy Azure, która dobrze nadaje się dla scenariuszy z dużą liczbą dokumentów i obciążeń związanych z zapytaniami w zastosowaniach wymagających maksymalnej.

## <a name="scaling-azure-search-for-slow-individual-queries"></a>Skalowanie usługi Azure Search wolnych pojedynczych zapytań
Kolejny powód, dlaczego stawki opóźnienie może działać powoli pochodzi z jednego zapytania trwa zbyt długo.  W tym przypadku Dodawanie replik nie poprawi stawki opóźnienia.  W tym przypadku dostępne są dwie opcje:

1. **Zwiększ partycje** partycja to mechanizm służący do podziału danych na dodatkowe zasoby.  Z tego powodu po dodaniu drugiej partycji danych pobiera dzielenie na dwie kolumny.  Trzeci partycji dzieli indeksu na trzech itp.  Ma to wpływ, że w niektórych przypadkach spowalniać zapytania będą działać szybciej ze względu na przetwarzanie równoległe obliczenia.  Istnieje kilka przykładów gdzie Zaobserwowaliśmy tego przetwarzania równoległego, bardzo dobrze współpracować z zapytania o niskiej selektywność zapytania.  Składa się z zapytania, który pasuje wiele dokumentów lub podczas tworzenia kategorii wyszukanych informacji musi dostarczają liczby przez dużą liczbę dokumentów.  Ponieważ istnieje wiele obliczeń potrzebne, aby oceniać dokładność dokumentów lub do zliczania liczby dokumentów, dodanie dodatkowych partycji może pomóc zapewnić dodatkowe obliczeń.  
   
   Może to być maksymalnie 12 partycjami w standardową usługę wyszukiwania i 1 partycji w usłudze wyszukiwania podstawowego.  Partycje mogą być dostosowane z [witryny Azure portal](search-create-service-portal.md) lub [PowerShell](search-manage-powershell.md).
2. **Limit dużej kardynalności pola:** Pole dużej kardynalności składa się z aspektów lub Filtrowanie pola, które ma znaczącą liczbę unikatowych wartości, a w rezultacie zajmuje dużo zasobów w celu obliczenia wyników za pośrednictwem.   Na przykład ustawienie pola Identyfikator produktu lub opisu jako obsługująca filtrowanie/tworzenie aspektów czyniłyby dla dużej kardynalności, ponieważ większość wartości z dokumentu do dokumentu są unikatowe. Wszędzie tam, gdzie to możliwe, należy ograniczyć liczbę pól dużej kardynalności.
3. **Zwiększ warstwy wyszukiwania:**  Przenoszenie do wyższej warstwy usługi Azure Search może być inny sposób, aby zwiększyć wydajność spowalniać zapytania.  Każdy z wyższej warstwy są także szybszy Procesor i większa ilość pamięci, które mogą mieć pozytywny wpływ na wydajność zapytań.

## <a name="scaling-for-availability"></a>Skalowanie na potrzeby dostępności
Repliki nie tylko zmniejszyć opóźnienie zapytań, ale można również zezwolić w celu zapewnienia wysokiej dostępności.  Za pomocą pojedynczej repliki należy oczekiwać okresowe przestoje związane z ponownym uruchomieniu serwera po aktualizacji oprogramowania lub inne zdarzenia konserwacji, które zostanie przeprowadzona.  W wyniku należy wziąć pod uwagę, jeśli aplikacja wymaga wysokiej dostępności wyszukiwania (zapytania) oraz operacje zapisu (indeksowania zdarzenia).  Usługa Azure Search udostępnia opcje umowa SLA na wszystkich ofert płatnych wyszukiwania z następującymi atrybutami:

* 2 repliki w celu zapewnienia wysokiej dostępności obciążeń (zapytania) tylko do odczytu
* co najmniej 3 replik wysokiej dostępności z obciążeniami odczytu i zapisu (zapytań i indeksowania)

Aby uzyskać szczegółowe informacje na temat tego, odwiedź [Umowa dotycząca poziomu usług Azure Search](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

Ponieważ repliki są kopie danych, o wiele replik umożliwia usłudze Azure Search na komputerze ponownego uruchamiania i obsługi dla jednej z replik, jednocześnie zapewniając zapytania, aby nadal być wykonywana przed innymi replikami.  Z tego powodu należy również wziąć pod uwagę, jak to przestojów mogą mieć wpływ na zapytania, które są obecnie musi być wykonywane względem co mniej kopię danych.

## <a name="scaling-geo-distributed-workloads-and-provide-geo-redundancy"></a>Skalowanie rozproszone geograficznie obciążeń i zapewniają nadmiarowość geograficzna
W przypadku obciążeń rozproszone geograficznie znajdziesz, że użytkownicy mieszkają oni daleko od centrum danych, w którym jest hostowana usługa Azure Search większe opóźnienia.  Z tego powodu często jest posiadanie wielu usług wyszukiwania w regionach, znajdujących się bliżej do tych użytkowników.  Usługa Azure Search nie jest aktualnie dostępny zautomatyzowaną metodę replikacji geograficznej indeksów usługi Azure Search w regionach, ale istnieje kilka technik, które mogą być używane, które mogą ułatwić ten proces proste do wdrożenia i zarządzania nimi. Te są opisane w kolejnych sekcjach.

Celem rozproszone geograficznie zestaw usług wyszukiwania ma dwa lub więcej indeksów, dostępne w co najmniej dwóch regionach, w której użytkownik zostanie skierowany do usługi Azure Search, która zapewnia najniższe opóźnienie, jak pokazano w tym przykładzie:

   ![Cross kartę usługi według regionów][1]

### <a name="keeping-data-in-sync-across-multiple-azure-search-services"></a>Synchronizacja danych w wielu usługach Azure Search
Dostępne są dwie opcje przechowywania wyszukiwania rozproszonego usługi synchronizacji składające się z przy użyciu [indeksatora wyszukiwania w usłudze Azure](search-indexer-overview.md) lub interfejsu API wypychania (nazywane także [interfejsu API REST usługi Azure Search](https://docs.microsoft.com/rest/api/searchservice/)).  

### <a name="azure-search-indexers"></a>Indeksatory w usłudze Azure Search
Jeśli używasz usługi Azure Search Indexer już importujesz zmiany danych z centralnego magazynu danych, takich jak bazy danych SQL Azure lub usługi Azure Cosmos DB. Podczas tworzenia nowego wyszukiwania usługi, możesz po prostu również utworzyć nowy indeksator wyszukiwania platformy Azure dla tej usługi, który wskazuje na to tego samego magazynu danych. W ten sposób zawsze wtedy, gdy nowe zmiany wejść w magazynie danych one będą następnie indeksowane przez różne indeksatorów.  

Oto przykład jak będzie wyglądać danej architekturze.

   ![Pojedyncze źródło danych przy użyciu kombinacji usługi i rozproszone indeksatora][2]

### <a name="push-api"></a>Interfejsu API wypychania
Jeśli używasz usługi Azure Search interfejsu API wypychania do [zaktualizować zawartość w indeksie usługi Azure Search](https://docs.microsoft.com/rest/api/searchservice/update-index), można zachować różne usługi wyszukiwania w synchronizacji, wypychając zmiany do wszystkich usług wyszukiwania, zawsze wtedy, gdy wymagana jest aktualizacja.  W ten sposób jest ważne upewnić się obsługiwać przypadki, gdzie niepowodzenia aktualizacji do usługi wyszukiwania jednego i co najmniej jedna aktualizacja powiodła się.

## <a name="leveraging-azure-traffic-manager"></a>Korzystanie z usługi Azure Traffic Manager
[Usługa Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) umożliwia kierowanie żądań do wielu zlokalizowane geograficznie witryn sieci Web, które następnie są wspierane przez wiele usług wyszukiwania platformy Azure.  Jedną z zalet usługi Traffic Manager to, że można ją sondy usługi Azure Search, aby upewnić się, że jest dostępny i przekierowywać użytkowników do usługi wyszukiwania alternatywne w przypadku przestoju.  Ponadto jeśli są routingu żądań wyszukiwania za pośrednictwem witryny sieci Web systemu Azure, usługi Azure Traffic Manager umożliwia należy załadować saldo przypadki, gdzie witryny sieci Web działa, ale nie w usłudze Azure Search.  Oto przykład jakie architektury, która korzysta z usługi Traffic Manager.

   ![Między + tab usług według regionów, przy użyciu centralnej Traffic Manager][3]

## <a name="monitoring-performance"></a>Monitorowanie wydajności
Usługa Azure Search udostępnia możliwość analizowania i monitorowania wydajności usługi za pośrednictwem [analiza ruchu wyszukiwania (STA)](search-traffic-analytics.md). Za pomocą STA można opcjonalnie logowania do konta usługi Azure Storage, które następnie mogą być przetwarzane do analizy lub wizualizowane w usłudze Power BI operacji wyszukiwania pojedynczych, jak również zagregowanych metryk.  Przy użyciu metryk STA, możesz przeglądać statystyki wydajności, takie jak średnia liczba zapytań lub zapytań czasy reakcji.  Ponadto rejestrowanie operacji pozwala przejść do szczegółów operacji wyszukiwania określonych.

STA jest przydatnym narzędziem do zrozumienia stawki opóźnienia z punktu widzenia tej usługi Azure Search.  Ponieważ metryki wydajności zapytań, które są rejestrowane są na podstawie czasu potrzebnego przez zapytanie do pełnego przetworzenia w usłudze Azure Search (od momentu, w którym jest wymagane, gdy zostanie wysłane), jesteś w stanie skorzystać z tej można ustalić, czy po stronie usługi Azure Search lub szczegółowymi informacjami dotyczącymi problemów z opóźnieniem Środowisko IDE, usługi, np. informacji od opóźnienia sieci.  

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej na temat cen limity usług i warstw dla każdego z nich, zobacz [limitów w usłudze Azure Search usług](search-limits-quotas-capacity.md).

Odwiedź stronę [planowania pojemności](search-capacity-planning.md) Aby dowiedzieć się więcej na temat kombinacji partycji i replik.

Aby uzyskać więcej Przechodzenie do szczegółów na wydajność i obejrzeć kilka prezentacji, jak zaimplementować optymalizacje omówionych w tym artykule Obejrzyj poniższy film wideo:

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON319/player]
> 
> 

<!--Image references-->
[1]: ./media/search-performance-optimization/geo-redundancy.png
[2]: ./media/search-performance-optimization/scale-indexers.png
[3]: ./media/search-performance-optimization/geo-search-traffic-mgr.png
