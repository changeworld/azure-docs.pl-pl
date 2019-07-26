---
title: Strategie wdrażania i najlepsze rozwiązania w zakresie optymalizacji wydajności — Azure Search
description: Poznaj techniki i najlepsze rozwiązania dotyczące dostrajania Azure Search wydajności i konfigurowania optymalnej skali.
author: LiamCavanagh
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 03/02/2019
ms.author: liamca
ms.custom: seodec2018
ms.openlocfilehash: a4578e26df5a6c29e80a0bbd2e0a30725e3733ee
ms.sourcegitcommit: c71306fb197b433f7b7d23662d013eaae269dc9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/22/2019
ms.locfileid: "68370639"
---
# <a name="deployment-strategies-and-best-practices-for-optimizing-performance-on-azure-search"></a>Strategie wdrażania i najlepsze rozwiązania w zakresie optymalizacji wydajności na Azure Search

W tym artykule opisano najlepsze rozwiązania dla zaawansowanych scenariuszy z zaawansowanymi wymaganiami dotyczącymi skalowalności i dostępności. 

## <a name="develop-baseline-numbers"></a>Opracowywanie numerów linii bazowej
Podczas optymalizowania pod kątem wydajności wyszukiwania należy skoncentrować się na skróceniu czasu wykonywania zapytania. Aby to zrobić, musisz wiedzieć, jak wygląda typowe obciążenie zapytania. Poniższe wskazówki mogą pomóc dotrzeć do numerów bazowych zapytań.

1. Wybierz oczekiwany czas oczekiwania (lub maksymalną ilość czasu), przez który należy wykonać typowe żądanie wyszukiwania.
2. Twórz i testuj rzeczywiste obciążenie usługi wyszukiwania za pomocą realistycznego zestawu danych, aby zmierzyć te stawki opóźnienia.
3. Zacznij od niskiej liczby zapytań na sekundę (zapytań) i stopniowo Zwiększaj liczbę wykonywanych testów do momentu spadku opóźnienia zapytania poniżej zdefiniowanego opóźnienia docelowego. Jest to ważny test, który ułatwia planowanie skalowania w miarę zwiększania użycia aplikacji.
4. Gdy to możliwe, należy ponownie użyć połączeń HTTP. Jeśli używasz zestawu SDK Azure Search .NET, oznacza to, że należy ponownie użyć wystąpienia lub wystąpienia [SearchIndexClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient) , a jeśli korzystasz z interfejsu API REST, należy ponownie użyć pojedynczej HttpClient.
5. Różnej treści żądań zapytań, dzięki czemu wyszukiwanie odbywa się na różnych częściach indeksu. Odmiana jest ważna, ponieważ w przypadku ciągłego wykonywania tych samych żądań wyszukiwania buforowanie danych rozpocznie się, aby wydajność była lepsza niż w przypadku bardziej nieznaczącego zestawu zapytań.
6. Różnicowanie struktury żądań zapytań w celu uzyskania różnych typów zapytań. Nie każde zapytanie wyszukiwania wykonuje się na tym samym poziomie. Na przykład wyszukiwanie dokumentów lub sugestia wyszukiwania jest zwykle szybsze niż zapytanie o znaczną liczbę aspektów i filtrów. Kompozycja testowa powinna zawierać różne zapytania, w przybliżeniu tak samo jak w środowisku produkcyjnym.  

Podczas tworzenia tych obciążeń testowych istnieją pewne cechy Azure Search, które należy wziąć pod uwagę:

+ Istnieje możliwość przeciążenia usługi przez wypychanie zbyt wielu zapytań wyszukiwania jednocześnie. W takim przypadku zobaczysz kody odpowiedzi HTTP 503. Aby uniknąć 503 podczas testowania, Zacznij od różnych zakresów żądań wyszukiwania, aby zobaczyć różnice w współczynniku opóźnień podczas dodawania kolejnych żądań wyszukiwania.

+ Azure Search nie uruchamia zadań indeksowania w tle. Jeśli usługa obsługuje współbieżne obciążenia zapytań i indeksowania, należy to uwzględnić, wprowadzając zadania indeksowania do testów zapytań lub przez Eksplorowanie opcji uruchamiania zadań indeksowania w godzinach szczytu.

> [!NOTE]
> [Testowanie obciążenia programu Visual Studio](https://www.visualstudio.com/docs/test/performance-testing/run-performance-tests-app-before-release) to naprawdę dobry sposób na przeprowadzenie testów porównawczych, ponieważ umożliwia wykonywanie żądań HTTP w miarę potrzeb do wykonywania zapytań dotyczących Azure Search i umożliwia przetwarzanie równoległe żądań.
> 
> 

## <a name="scaling-for-high-query-volume-and-throttled-requests"></a>Skalowanie dla dużej liczby zapytań i żądań z ograniczeniami
Gdy otrzymujesz zbyt wiele żądań z ograniczeniami lub przekroczą docelową szybkość opóźnienia z zwiększonego obciążenia zapytania, możesz przyjrzeć się zmniejszeniu szybkości opóźnienia na jeden z dwóch sposobów:

1. **Zwiększ repliki:**  Replika jest taka sama jak Kopia danych umożliwiająca Azure Search równoważenia obciążenia żądaniami w wielu kopiach.  Wszystkie obciążenia i replikacja danych między replikami są zarządzane przez Azure Search i można w dowolnym momencie zmienić liczbę replik przyznanych dla usługi.  Możesz przydzielić maksymalnie 12 replik w standardowej usłudze wyszukiwania i 3 replikach w podstawowej usłudze wyszukiwania. Repliki można dostosować za pomocą [Azure Portal](search-create-service-portal.md) lub [programu PowerShell](search-manage-powershell.md).
2. **Zwiększ poziom wyszukiwania:**  Azure Search zawiera [wiele warstw](https://azure.microsoft.com/pricing/details/search/) , a każda z tych warstw oferuje różne poziomy wydajności.  W niektórych przypadkach może istnieć wiele zapytań, w przypadku których nie można zapewnić wystarczająco małych opóźnień, nawet gdy repliki są maxed. W takim przypadku warto rozważyć użycie jednej z wyższych warstw wyszukiwania, takich jak warstwa Azure Search S3, która jest odpowiednia dla scenariuszy z dużą liczbą dokumentów i bardzo wysokich obciążeń związanych z zapytaniami.

## <a name="scaling-for-slow-individual-queries"></a>Skalowanie dla powolnych pojedynczych zapytań
Kolejną przyczyną częstych opóźnień jest wykonanie pojedynczej kwerendy trwa zbyt długo. W takim przypadku Dodawanie replik nie będzie pomocne. Dostępne są dwie opcje, które mogą pomóc w następujących kwestiach:

1. **Zwiększ partycje** Partycja to mechanizm dzielenia danych między dodatkowe zasoby. Dodanie drugiej partycji dzieli dane na dwie, trzecia partycja dzieli ją na trzy itd. Jeden dodatni efekt uboczny polega na tym, że wolniejsze zapytania czasami przyspieszają działanie z powodu równoległego przetwarzania danych. Zanotowano przetwarzanie równoległe na temat niskich zapytań selektywnych, takich jak zapytania, które pasują do wielu dokumentów, lub aspektów dostarczających liczniki w dużej liczbie dokumentów. Ponieważ do oceny dokładność dokumentów lub obliczenia liczby dokumentów wymagane jest znaczące obliczenie, dodawanie dodatkowych partycji ułatwia wykonywanie zapytań.  
   
   W usłudze wyszukiwania w warstwie Standardowa może znajdować się maksymalnie 12 partycji i 1 partycja.  Partycje można dostosować za pomocą [Azure Portal](search-create-service-portal.md) lub [programu PowerShell](search-manage-powershell.md).

2. **Ogranicz wysokie wartości pól kardynalności:** Pole wysoka Kardynalność składa się z pola z możliwością tworzenia lub filtrowania, które ma znaczną liczbę unikatowych wartości, a w efekcie zużywa znaczące zasoby podczas obliczania wyników. Na przykład ustawienie identyfikatora produktu lub pola opisu jako wartości do przefiltrowania/filtrowania będzie traktowane jako wysoka Kardynalność, ponieważ większość wartości z dokumentu do dokumentu jest unikatowa. Wszędzie tam, gdzie to możliwe, należy ograniczyć liczbę pól o dużej kardynalności.

3. **Zwiększ poziom wyszukiwania:**  Przechodzenie do wyższej warstwy Azure Search może być innym sposobem poprawy wydajności wolnych zapytań. Każda wyższa warstwa zapewnia szybszy procesor CPU i więcej pamięci, z których oba mają pozytywny wpływ na wydajność zapytań.

## <a name="scaling-for-availability"></a>Skalowanie pod kątem dostępności
Repliki nie tylko skracają opóźnienia zapytań, ale mogą też zapewnić wysoką dostępność. W przypadku pojedynczej repliki należy oczekiwać okresowego przestoju spowodowanego ponownym uruchomieniem serwera po aktualizacji oprogramowania lub w przypadku innych zdarzeń konserwacji, które nastąpią.  W związku z tym ważne jest, aby wziąć pod uwagę, czy aplikacja wymaga wysokiej dostępności wyszukiwania (zapytania), a także zapisów (zdarzenia indeksowania). Azure Search oferuje opcje umowy SLA dotyczącej wszystkich płatnych ofert wyszukiwania o następujących atrybutach:

* 2 repliki w celu zapewnienia wysokiej dostępności obciążeń tylko do odczytu (zapytania)
* 3 lub więcej replik wysokiej dostępności obciążeń odczytu i zapisu (zapytania i indeksowanie)

Aby uzyskać więcej informacji na ten temat, odwiedź [Umowa dotycząca poziomu usług Azure Search](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

Ponieważ repliki są kopiami danych, dzięki czemu wiele replik umożliwia Azure Search wykonywanie ponownych uruchomień maszyn i ich konserwację w odniesieniu do jednej repliki, jednocześnie pozwalając na kontynuowanie wykonywania zapytania na innych replikach. Z drugiej strony, Jeśli przełączysz repliki, nastąpi pogorszenie wydajności zapytania, przy założeniu, że te repliki były zasobami wykorzystywanymi.

## <a name="scaling-for-geo-distributed-workloads-and-geo-redundancy"></a>Skalowanie dla obciążeń rozproszonych geograficznie i nadmiarowości geograficznej
W przypadku obciążeń rozproszonych geograficznie użytkownicy, którzy znajdują się daleko od Azure Search hostingu centrum danych, będą mieć wyższe stawki opóźnienia. Jednym środkiem ograniczającym jest zainicjowanie obsługi wielu usług wyszukiwania w regionach z bliższą bliskością tych użytkowników. Azure Search obecnie nie zapewnia zautomatyzowanej metody replikowania geograficznie Azure Search indeksów między regionami, ale istnieje kilka technik, które mogą być używane, aby ten proces był prosty do wdrożenia i zarządzania. Są one opisane w następnych sekcjach.

Celem rozproszonego geograficznie zestawu usług wyszukiwania jest posiadanie co najmniej dwóch indeksów dostępnych w co najmniej dwóch regionach, w których użytkownik jest kierowany do usługi Azure Search, która zapewnia najniższe opóźnienie, jak pokazano w tym przykładzie:

   ![Wiele kart usług według regionów][1]

### <a name="keeping-data-in-sync-across-multiple-azure-search-services"></a>Utrzymywanie synchronizacji danych w wielu Azure Search usługach
Istnieją dwie opcje utrzymywania synchronizacji usług wyszukiwania rozproszonego, które składają się z użycia indeksatora [Azure Search](search-indexer-overview.md) lub interfejsu API wypychania (nazywanego również [interfejsem API REST Azure Search](https://docs.microsoft.com/rest/api/searchservice/)).  

### <a name="use-indexers-for-updating-content-on-multiple-services"></a>Używanie indeksatorów do aktualizowania zawartości w wielu usługach

Jeśli używasz już indeksatora w jednej usłudze, możesz skonfigurować drugi indeksator dla drugiej usługi tak, aby korzystał z tego samego obiektu źródła danych, pobierając dane z tej samej lokalizacji. Każda usługa w każdym regionie ma swój własny indeksator i docelowy indeks (indeks wyszukiwania nie jest udostępniony, co oznacza, że dane są duplikowane), ale każdy indeksator odwołuje się do tego samego źródła danych.

Poniżej znajduje się wysoce wysoki poziom wizualizacji, która będzie wyglądać na architekturę.

   ![Pojedyncze źródło danych z rozproszoną usługą indeksatora i kombinacjami usług][2]

### <a name="use-rest-apis-for-pushing-content-updates-on-multiple-services"></a>Używanie interfejsów API REST do wypychania aktualizacji zawartości w wielu usługach
Jeśli używasz interfejsu API REST Azure Search do wypychania [zawartości w indeksie Azure Search](https://docs.microsoft.com/rest/api/searchservice/update-index), możesz zapewnić synchronizację różnych usług wyszukiwania przez wypychanie zmian do wszystkich usług wyszukiwania, gdy wymagana jest aktualizacja. W kodzie, pamiętaj, aby obsługiwać przypadki, w których aktualizacja jednej usługi wyszukiwania kończy się niepowodzeniem, ale powiodła się dla innych usług wyszukiwania.

## <a name="leverage-azure-traffic-manager"></a>Korzystanie z usługi Azure Traffic Manager
[Usługa Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) umożliwia kierowanie żądań do wielu witryn sieci Web zlokalizowanych w lokalizacji geograficznej, które są następnie obsługiwane przez wiele usług Azure Search. Jedną z zalet Traffic Manager jest to, że może ona sondować Azure Search, aby upewnić się, że jest ona dostępna i będzie kierować użytkowników do alternatywnych usług wyszukiwania w przypadku przestoju. Ponadto w przypadku routingu żądań wyszukiwania za pomocą usługi Azure Web Sites usługa Azure Traffic Manager umożliwia Równoważenie obciążenia, w przypadku których witryna internetowa jest niedostępna, ale nie Azure Search. Oto przykład architektury, która wykorzystuje Traffic Manager.

   ![Wiele kart usług według regionów z centralnym Traffic Manager][3]

## <a name="monitor-performance"></a>Monitorowanie wydajności
Azure Search umożliwia analizowanie i monitorowanie wydajności usługi za pomocą [analizy ruchu wyszukiwania](search-traffic-analytics.md). Po włączeniu tej funkcji i dodaniu instrumentacji do aplikacji klienckiej można opcjonalnie rejestrować pojedyncze operacje wyszukiwania, a także zagregowane metryki na koncie usługi Azure Storage, które można następnie przetworzyć w celu analizy lub wizualizacji w Power BI. Metryki przechwyty w ten sposób zapewniają statystyki wydajności, takie jak średnia liczba zapytań lub czasy odpowiedzi na zapytanie. Ponadto rejestrowanie operacji umożliwia przechodzenie do szczegółów określonych operacji wyszukiwania.

Analiza ruchu jest przydatna do poznania stawek opóźnienia z tej Azure Search perspektywą. Ponieważ zarejestrowane metryki wydajności zapytań są oparte na czasie, w którym zapytanie ma być w pełni przetwarzane w Azure Search (od momentu, w którym żądanie jest wysyłane), można użyć tego do ustalenia, czy problemy z opóźnieniami pochodzą z usługi Azure Search lub po stronie usług środowisko IDE usługi, takie jak z opóźnienia sieci.  

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej o limitach warstw cenowych i usług dla każdej z nich, zobacz [limity usługi w Azure Search](search-limits-quotas-capacity.md).

Odwiedź stronę [Planowanie pojemności](search-capacity-planning.md) , aby dowiedzieć się więcej na temat kombinacji partycji i repliki.

Aby uzyskać więcej szczegółów na temat wydajności i zobaczyć, jak zaimplementować optymalizacje omówione w tym artykule, Obejrzyj następujące wideo:

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON319/player]
> 
> 

<!--Image references-->
[1]: ./media/search-performance-optimization/geo-redundancy.png
[2]: ./media/search-performance-optimization/scale-indexers.png
[3]: ./media/search-performance-optimization/geo-search-traffic-mgr.png
