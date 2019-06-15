---
title: Strategie wdrażania i najlepsze rozwiązania dotyczące optymalizacji wydajności — Usługa Azure Search
description: Dowiedz się, techniki i najlepsze rozwiązania dotyczące dostrajania wydajności usługi Azure Search i konfigurowania optymalnej skali.
author: LiamCavanagh
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 03/02/2019
ms.author: liamca
ms.custom: seodec2018
ms.openlocfilehash: 32352a857f0a74dc008dc1ad76b4a5951a36b956
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65024546"
---
# <a name="deployment-strategies-and-best-practices-for-optimizing-performance-on-azure-search"></a>Strategie wdrażania i najlepsze rozwiązania dotyczące optymalizacji wydajności w usłudze Azure Search

W tym artykule opisano najlepsze rozwiązania w przypadku zaawansowanych scenariuszy z zaawansowanymi wymaganiami dotyczącymi skalowalności i dostępności. 

## <a name="develop-baseline-numbers"></a>Tworzenie numerów linii bazowej
Podczas optymalizacji wydajności wyszukiwania, należy skoncentrować się na skrócenie czasu wykonywania zapytania. Aby to zrobić, musisz wiedzieć, obciążenie zapytaniami typowe wygląda następująco. Poniższe wskazówki mogą pomóc w przyjeździe do linii bazowej kwerendy numerów.

1. Wybierz z docelowym czasem oczekiwania (lub maksymalną ilość czasu), które żądania w typowym wyszukiwaniu powinien wykonać, aby zakończyć.
2. Tworzenie i testowanie rzeczywiste obciążenie dla usługi wyszukiwania przy użyciu realistycznej zestaw danych, aby zmierzyć te stawki opóźnienia.
3. Uruchom o niskiej liczbie zapytań na sekundę (QPS) i stopniowo zwiększać liczbę wykonane w teście, dopóki opóźnienie kwerendy nie spadnie poniżej zdefiniowanego docelowym czasem oczekiwania. Jest to ważne testów porównawczych pomocne dla skalowania w miarę wzrostu użycia w aplikacji.
4. Wszędzie tam, gdzie to możliwe, należy ponownie używać połączeń HTTP. Korzystając z zestawu .NET SDK usługi Azure Search, oznacza to, należy używać ponownie wystąpienie lub [SearchIndexClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient) wystąpienia i korzystają z interfejsu API REST, należy używać ponownie pojedynczego HttpClient.
5. Różnią się treść żądania zapytań, więc wyszukiwania odbywa się za pośrednictwem różnych części Twojego indeksu. Zmiana jest ważne, ponieważ jeśli takie same żądania wyszukiwania są wykonywane stale, buforowania danych, rozpocznie się zapewnienie wydajności wyglądają lepiej niż go przy użyciu bardziej różnorodnych zapytania ustawić.
6. Tak, aby pobrać różnego rodzaju zapytania, różnią się struktury żądań zapytań. Nie wszystkie zapytania wyszukiwania wykonuje się na tym samym poziomie. Na przykład sugestii wyszukiwania lub wyszukiwania dokumentu jest zwykle szybsze niż zapytanie o znaczna liczba zestawów reguł i filtrów. Kompozycja testu powinien zawierać różne zapytania około zgodnie z tym samym, zgodnie z oczekiwaniami w środowisku produkcyjnym.  

Podczas tworzenia tych testów obciążenia, istnieją pewne cechy usługi Azure Search, które należy uwzględnić:

+ Można przeciążać usługi wypychając zbyt wiele kwerend wyszukiwania, w tym samym czasie. W takim przypadku zobaczysz kody odpowiedzi HTTP 503. Aby uniknąć 503 podczas testowania, należy uruchomić z różnymi zakresami żądania wyszukiwania, aby zobaczyć różnice w kursach opóźnienia, jak dodać więcej żądań wyszukiwania.

+ Usługa Azure Search nie powoduje uruchomienia zadania indeksowania w tle. Jeśli usługa obsługuje z zapytaniami i indeksowaniem obciążeń jednocześnie, to uwzględniać wprowadzając albo zadania indeksowania do testów zapytania lub za pośrednictwem opcje uruchamiania zadania indeksowania podczas poza godzinami szczytu.

> [!NOTE]
> [Testowanie programu Visual Studio obciążenia](https://www.visualstudio.com/docs/test/performance-testing/run-performance-tests-app-before-release) jest naprawdę dobre sposób wykonywania Twojego testu porównawczego testy jako umożliwia wykonywanie żądań HTTP, która byłaby potrzebna do wykonywania zapytań względem usługi Azure Search i umożliwia przetwarzanie równoległe żądania.
> 
> 

## <a name="scaling-for-high-query-volume-and-throttled-requests"></a>Ograniczone żądania i skalowania dla zapytania wysoki woluminu
Otrzymujesz zbyt wiele żądań ograniczone lub przekracza ceny opóźnienie docelowej z obciążenie zapytaniami zwiększona, możesz przejrzeć zmniejszyć opóźnienie kursy w jeden z dwóch sposobów:

1. **Zwiększ repliki:**  Replika jest jak kopię danych, dzięki czemu usługa Azure Search równoważyć obciążenie żądaniami względem wielu kopii.  Wszystkie Równoważenie obciążenia i replikacja danych za pośrednictwem replik jest zarządzana przez usługę Azure Search można zmienić liczbę replik przydzielone dla usługi w dowolnym momencie.  Możesz przydzielić maksymalnie 12 replik w standardową usługę wyszukiwania i 3 repliki w usłudze wyszukiwania podstawowego. Repliki mogą być dostosowane z [witryny Azure portal](search-create-service-portal.md) lub [PowerShell](search-manage-powershell.md).
2. **Zwiększ warstwy wyszukiwania:**  Usługa Azure Search jest dostępna w [liczba warstw](https://azure.microsoft.com/pricing/details/search/) i każdy z tych warstw oferuje różne poziomy wydajności.  W niektórych przypadkach może mieć wiele zapytań warstwy, które znajdują się na nie może dostarczyć stawek wystarczająco małe opóźnienia, nawet wtedy, gdy replik maksymalnego limitu. W takim przypadku warto rozważyć korzystanie z jednej z warstw wyszukiwania wyższej, np. warstwa S3 wyszukiwania platformy Azure, która dobrze nadaje się dla scenariuszy z dużą liczbą dokumentów i obciążeń związanych z zapytaniami w zastosowaniach wymagających maksymalnej.

## <a name="scaling-for-slow-individual-queries"></a>Skalowanie na potrzeby wolnych pojedynczych zapytań
Inną przyczyną szybkości duże opóźnienia jest pojedyncze zapytanie trwała zbyt długo, aby zakończyć. W tym przypadku Dodawanie replik nie pomoże. Możliwe opcje, które mogą pomóc następujące dwie opcje:

1. **Zwiększ partycje** partycja to mechanizm służący do podziału danych na dodatkowe zasoby. Dodawanie drugiej partycji dzieli dane na dwa, trzeci partycji dzieli je na 3 i tak dalej. Jeden dodatni efekt uboczny to, że wolniejsze zapytania czasami działać szybciej z powodu przetwarzania równoległego. Firma Microsoft zostały zanotowane przetwarzania równoległego zapytań selektywność niski, takich jak zapytania, które odpowiadają wiele dokumentów lub aspektami podanie liczby przez dużą liczbę dokumentów. Ponieważ znaczące obliczenie jest wymagany do oceniania dokładność dokumentów lub do zliczania liczby dokumentów, dodając dodatkowe partycje pomaga szybciej wprowadzać zapytania.  
   
   Może to być maksymalnie 12 partycjami w standardową usługę wyszukiwania i 1 partycji w usłudze wyszukiwania podstawowego.  Partycje mogą być dostosowane z [witryny Azure portal](search-create-service-portal.md) lub [PowerShell](search-manage-powershell.md).

2. **Limit dużej kardynalności pola:** Pole dużej kardynalności składa się z aspektów lub Filtrowanie pola, które ma znaczącą liczbę unikatowych wartości, a w rezultacie zużywa znaczące ilości zasobów podczas obliczania wyników. Na przykład ustawienie pola Identyfikator produktu lub opisu jako obsługująca filtrowanie/tworzenie aspektów jest traktowany jako dużej kardynalności, ponieważ większość wartości z dokumentu do dokumentu są unikatowe. Wszędzie tam, gdzie to możliwe, należy ograniczyć liczbę pól dużej kardynalności.

3. **Zwiększ warstwy wyszukiwania:**  Przenoszenie do wyższej warstwy usługi Azure Search może być inny sposób, aby zwiększyć wydajność spowalniać zapytania. Każdej warstwy wyższej zapewnia szybsze procesory CPU i pamięci, które mają pozytywny wpływ na wydajność zapytań.

## <a name="scaling-for-availability"></a>Skalowanie na potrzeby dostępności
Repliki nie tylko zmniejszyć opóźnienie zapytań, ale można również zezwolić w celu zapewnienia wysokiej dostępności. Za pomocą pojedynczej repliki należy oczekiwać okresowe przestoje związane z ponownym uruchomieniu serwera po aktualizacji oprogramowania lub inne zdarzenia konserwacji, które zostanie przeprowadzona.  W wyniku należy wziąć pod uwagę, jeśli aplikacja wymaga wysokiej dostępności wyszukiwania (zapytania) oraz operacje zapisu (indeksowania zdarzenia). Usługa Azure Search udostępnia opcje umowa SLA na wszystkich ofert płatnych wyszukiwania z następującymi atrybutami:

* 2 repliki w celu zapewnienia wysokiej dostępności obciążeń (zapytania) tylko do odczytu
* co najmniej 3 replik wysokiej dostępności z obciążeniami odczytu i zapisu (zapytań i indeksowania)

Aby uzyskać szczegółowe informacje na temat tego, odwiedź [Umowa dotycząca poziomu usług Azure Search](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

Repliki są kopie danych, o wiele replik umożliwia usłudze Azure Search na komputerze ponownego uruchamiania i obsługi dla jednej z replik zezwalając wykonywania zapytania kontynuować z innymi replikami. Z drugiej strony Jeśli nie podejmiesz natychmiast replik, będzie wiąże się spadku wydajności zapytań, przy założeniu, że repliki te zostały niewystarczająco wykorzystywanych zasobów.

## <a name="scaling-for-geo-distributed-workloads-and-geo-redundancy"></a>Skalowanie rozproszone geograficznie obciążenia i nadmiarowości geograficznej
W przypadku obciążeń rozproszone geograficznie użytkowników, którzy znajdują się oni daleko od centrum danych hostującego usługi Azure Search mają większe opóźnienia. Jeden środki zaradcze jest do udostępniania wielu usług wyszukiwania w regionach z zbliża dla tych użytkowników. Usługa Azure Search nie jest aktualnie dostępny zautomatyzowaną metodę replikacji geograficznej indeksów usługi Azure Search w regionach, ale istnieje kilka technik, które mogą być używane, które mogą ułatwić ten proces proste do wdrożenia i zarządzania nimi. Te są opisane w kolejnych sekcjach.

Celem rozproszone geograficznie zestaw usług wyszukiwania ma dwa lub więcej indeksów, dostępne w co najmniej dwóch regionach, w którym użytkownik jest kierowany do usługi Azure Search, która zapewnia najniższe opóźnienie, jak pokazano w tym przykładzie:

   ![Cross kartę usługi według regionów][1]

### <a name="keeping-data-in-sync-across-multiple-azure-search-services"></a>Synchronizacja danych w wielu usługach Azure Search
Dostępne są dwie opcje przechowywania wyszukiwania rozproszonego usługi synchronizacji składające się z przy użyciu [indeksatora wyszukiwania w usłudze Azure](search-indexer-overview.md) lub interfejsu API wypychania (nazywane także [interfejsu API REST usługi Azure Search](https://docs.microsoft.com/rest/api/searchservice/)).  

### <a name="use-indexers-for-updating-content-on-multiple-services"></a>Korzystać z indeksatorów w celu zaktualizowania zawartości na wiele usług

Jeśli już używasz indeksatora w jednej usłudze, można skonfigurować drugi indeksatora na drugi usługę do używania tego samego obiektu źródła danych, pobieranie danych z tej samej lokalizacji. Każda usługa w każdym regionie ma swój własny indeksatora i indeksu docelowego (indeksu wyszukiwania nie jest udostępniony, co oznacza, że dane są powielane), ale każdy indeksatora odwołuje się do tego samego źródła danych.

Poniżej przedstawiono wizualizację wysokiego poziomu, jak będzie wyglądać danej architekturze.

   ![Pojedyncze źródło danych przy użyciu kombinacji usługi i rozproszone indeksatora][2]

### <a name="use-rest-apis-for-pushing-content-updates-on-multiple-services"></a>Używanie interfejsów API REST do wypychania aktualizacji zawartości od wielu usług
Jeśli używasz usługi Azure Search interfejs API REST do [wypychania zawartości w indeksie usługi Azure Search](https://docs.microsoft.com/rest/api/searchservice/update-index), można zachować różne usługi wyszukiwania w synchronizacji, wypychając zmiany do wszystkich usług wyszukiwania, zawsze wtedy, gdy wymagana jest aktualizacja. W kodzie upewnij się obsługiwać przypadki, gdzie aktualizacji do jednego wyszukiwania usługi nie powiedzie się, ale nie powiedzie się z innymi usługami wyszukiwania.

## <a name="leverage-azure-traffic-manager"></a>Korzystać z usługi Azure Traffic Manager
[Usługa Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) umożliwia kierowanie żądań do wielu zlokalizowane geograficznie witryn sieci Web, które następnie są wspierane przez wiele usług wyszukiwania platformy Azure. Jedną z zalet usługi Traffic Manager to, że można ją sondy usługi Azure Search, aby upewnić się, że jest dostępny i przekierowywać użytkowników do usługi wyszukiwania alternatywne w przypadku przestoju. Ponadto jeśli są routingu żądań wyszukiwania za pośrednictwem witryny sieci Web systemu Azure, usługi Azure Traffic Manager umożliwia należy załadować saldo przypadki, gdzie witryny sieci Web działa, ale nie w usłudze Azure Search. Oto przykład jakie architektury, która korzysta z usługi Traffic Manager.

   ![Między + tab usług według regionów, przy użyciu centralnej Traffic Manager][3]

## <a name="monitor-performance"></a>Monitorowanie wydajności
Usługa Azure Search udostępnia możliwość analizowania i monitorowania wydajności usługi za pośrednictwem [analiza ruchu wyszukiwania](search-traffic-analytics.md). Gdy możesz włączyć tę funkcję i dodać Instrumentację do aplikacji klienckiej operacji wyszukiwania pojedynczych, jak również zagregowanych metryk można opcjonalnie logowania do konta usługi Azure Storage, które następnie mogą być przetwarzane do analizy lub wizualizowane w usłudze Power BI. Przechwytywanie metryki w ten sposób zapewniają statystyki wydajności, takie jak średnia liczba zapytań lub zapytań czasy reakcji. Ponadto rejestrowanie operacji pozwala przejść do szczegółów operacji wyszukiwania określonych.

Analiza ruchu jest przydatne dla zrozumienia, opóźnienie stawki z punktu widzenia tej usługi Azure Search. Ponieważ metryki wydajności zapytań, które są rejestrowane są na podstawie czasu potrzebnego przez zapytanie do pełnego przetworzenia w usłudze Azure Search (od momentu, w którym jest wymagane, gdy zostanie wysłane), jesteś w stanie skorzystać z tej można ustalić, czy po stronie usługi Azure Search lub szczegółowymi informacjami dotyczącymi problemów z opóźnieniem Środowisko IDE, usługi, np. informacji od opóźnienia sieci.  

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
