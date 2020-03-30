---
title: Skalowanie pod kątem wydajności
titleSuffix: Azure Cognitive Search
description: Poznaj techniki i najlepsze rozwiązania dotyczące dostrajania wydajności usługi Azure Cognitive Search i konfigurowania optymalnej skali.
manager: nitinme
author: LiamCavanagh
ms.author: liamca
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 7c2857de0613be400f83544e1dabe079b7497bbd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77212384"
---
# <a name="scale-for-performance-on-azure-cognitive-search"></a>Skaluj pod kątem wydajności w usłudze Azure Cognitive Search

W tym artykule opisano najlepsze rozwiązania dotyczące zaawansowanych scenariuszy z zaawansowanymi wymaganiami dotyczącymi skalowalności i dostępności.

## <a name="start-with-baseline-numbers"></a>Zacznij od liczb bazowych

Przed podjęciem większego wysiłku wdrożenia upewnij się, że wiesz, jak wygląda typowe obciążenie zapytania. Poniższe wskazówki mogą pomóc w dotarciu do numerów zapytań według planu bazowego.

1. Wybierz opóźnienie docelowe (lub maksymalną ilość czasu), który typowy żądanie wyszukiwania powinno potrwać do ukończenia.

1. Tworzenie i testowanie rzeczywistego obciążenia w usłudze wyszukiwania za pomocą realistycznego zestawu danych do pomiaru tych szybkości opóźnienia.

1. Zacznij od małej liczby zapytań na sekundę (QPS), a następnie stopniowo zwiększaj liczbę wykonaną w teście, aż opóźnienie zapytania spadnie poniżej wstępnie zdefiniowanego obiektu docelowego. Jest to ważny punkt odniesienia, który ułatwia planowanie skalowania w miarę zwiększania użycia aplikacji.

1. W miarę możliwości ponownie używaj połączeń HTTP. Jeśli używasz usługi Azure Cognitive Search .NET SDK, oznacza to, że należy ponownie użyć wystąpienia lub [SearchIndexClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient) wystąpienia, a jeśli używasz interfejsu API REST, należy ponownie użyć jednego HttpClient.

1. Zmieniaj treść żądań zapytań, tak aby wyszukiwanie odbywały się w różnych częściach indeksu. Odmiana jest ważna, ponieważ jeśli stale wykonujesz te same żądania wyszukiwania, buforowanie danych zacznie wyglądać lepiej niż w przypadku bardziej zróżnicowanego zestawu zapytań.

1. Zmień strukturę żądań zapytań, tak aby uzyskać różne typy zapytań. Nie każde zapytanie wyszukiwania wykonuje na tym samym poziomie. Na przykład wyszukiwanie dokumentu lub sugestia wyszukiwania jest zazwyczaj szybsze niż kwerenda ze znaczną liczbą aspektów i filtrów. Skład testu powinny zawierać różne zapytania, w przybliżeniu takie same proporcje, jak można oczekiwać w produkcji.  

Podczas tworzenia tych obciążeń testowych istnieją pewne cechy usługi Azure Cognitive Search, o których należy pamiętać:

+ Istnieje możliwość przeciążenia usługi przez wypychanie zbyt wielu zapytań w tym czasie. W takim przypadku zostaną wyświetlne kody odpowiedzi HTTP 503. Aby uniknąć 503 podczas testowania, należy rozpocząć od różnych zakresów żądań wyszukiwania, aby zobaczyć różnice w szybkości opóźnienia, jak dodać więcej żądań wyszukiwania.

+ Usługa Azure Cognitive Search nie uruchamia zadań indeksowania w tle. Jeśli usługa obsługuje obciążenia zapytań i indeksowania jednocześnie, należy wziąć to pod uwagę przez wprowadzenie zadań indeksowania do testów zapytań lub przez eksplorowanie opcji uruchamiania zadań indeksowania poza godzinami szczytu.

> [!Tip]
> Można symulować realistyczne obciążenie zapytania za pomocą narzędzi do testowania obciążenia. Spróbuj [przetestować obciążenie za pomocą usługi Azure DevOps](https://docs.microsoft.com/azure/devops/test/load-test/get-started-simple-cloud-load-test?view=azure-devops) lub użyj jednej z tych [alternatyw.](https://docs.microsoft.com/azure/devops/test/load-test/overview?view=azure-devops#alternatives)

## <a name="scale-for-high-query-volume"></a>Skaluj dla dużej liczby zapytań

Usługa jest przeciążony, gdy kwerendy trwa zbyt długo lub gdy usługa rozpoczyna upuszczanie żądań. Jeśli tak się stanie, można rozwiązać problem na jeden z dwóch sposobów:

+ **Dodawanie replik**  

  Każda replika jest kopią danych, dzięki czemu usługa może załadować żądania równoważenia dla wielu kopii.  Wszystkie równoważenie obciążenia i replikacja danych jest zarządzany przez usługę Azure Cognitive Search i można zmienić liczbę replik przydzielonych do usługi w dowolnym momencie. W usłudze wyszukiwania standardowego można przydzielić maksymalnie 12 replik i 3 repliki w usłudze wyszukiwania podstawowego. Repliki można dostosowywać za pomocą [portalu Azure](search-create-service-portal.md) lub [programu PowerShell.](search-manage-powershell.md)

+ **Tworzenie nowej usługi w wyższej warstwie**  

  Usługa Azure Cognitive Search jest dostępna w [wielu warstwach,](https://azure.microsoft.com/pricing/details/search/) a każda z nich oferuje różne poziomy wydajności. W niektórych przypadkach może mieć tak wiele zapytań, że warstwa, na której się znajdujesz, nie może zapewnić wystarczającego zwrotu, nawet jeśli repliki są maxed. W takim przypadku należy rozważyć przejście do warstwy o wyższej wydajności, takiej jak standardowa warstwa S3, zaprojektowanej dla scenariuszy o dużej liczbie dokumentów i bardzo wysokich obciążeniach zapytań.

## <a name="scale-for-slow-individual-queries"></a>Skaluj dla powolnych pojedynczych zapytań

Innym powodem dużych opóźnień jest pojedyncze zapytanie trwa zbyt długo, aby zakończyć. W takim przypadku dodanie replik nie pomoże. Dwie możliwe opcje, które mogą pomóc w następujących elementów:

+ **Zwiększanie partycji**

  Partycja dzieli dane na dodatkowe zasoby obliczeniowe. Dwie partycje podzielić dane na pół, trzecia partycja dzieli go na trzecie i tak dalej. Jednym z pozytywnych efektów ubocznych jest to, że wolniejsze kwerendy czasami działają szybciej dzięki przetwarzaniu równoległemu. Zauważyliśmy równoległość w kwerendach o niskiej selektywności, takich jak zapytania, które pasują do wielu dokumentów lub aspekty zapewniające liczbę dużych dokumentów. Ponieważ znaczne obliczenia jest wymagane do oceny trafności dokumentów lub zliczania liczby dokumentów, dodanie dodatkowych partycji pomaga kwerendy zakończyć szybciej.  
   
  W standardowej usłudze wyszukiwania może znajdować się maksymalnie 12 partycji i 1 partycja w usłudze wyszukiwania basic. Partycje można dostosowywać za pomocą [portalu Azure](search-create-service-portal.md) lub [programu PowerShell.](search-manage-powershell.md)

+ **Ogranicz pola wysokiej kardynalności**

  Pole wysokiej kardynalności składa się z pola, które można filtrować lub które ma znaczną liczbę unikatowych wartości, a w rezultacie zużywa znaczne zasoby podczas obliczania wyników. Na przykład ustawienie pola Identyfikator produktu lub Opis jako typowalny/filtrowania będzie liczone jako wysoka kardynalność, ponieważ większość wartości z dokumentu do dokumentu są unikatowe. Tam, gdzie to możliwe, ogranicz liczbę pól o wysokiej kardynalności.

+ **Zwiększ warstwę wyszukiwania**  

  Przejście do wyższej warstwy usługi Azure Cognitive Search może być innym sposobem zwiększenia wydajności powolnych zapytań. Każda wyższa warstwa zapewnia szybsze procesory i więcej pamięci, z których oba mają pozytywny wpływ na wydajność zapytań.

## <a name="scale-for-availability"></a>Skala dostępności

Repliki nie tylko pomagają zmniejszyć opóźnienie kwerendy, ale mogą również umożliwić wysoką dostępność. W przypadku pojedynczej repliki należy spodziewać się okresowych przestojów spowodowanych ponownym uruchomieniem serwera po aktualizacji oprogramowania lub w przypadku innych zdarzeń konserwacji, które wystąpią. W związku z tym należy wziąć pod uwagę, czy aplikacja wymaga wysokiej dostępności wyszukiwań (kwerendy), a także zapisy (indeksowanie zdarzeń). Usługa Azure Cognitive Search oferuje opcje umowy SLA we wszystkich płatnych ofertach wyszukiwania z następującymi atrybutami:

+ Dwie repliki o wysokiej dostępności obciążeń tylko do odczytu (kwerendy)

+ Trzy lub więcej replik dla wysokiej dostępności obciążeń odczytu i zapisu (kwerendy i indeksowanie)

Aby uzyskać więcej informacji na ten temat, odwiedź [umowę dotyczącą poziomu usług usługi usługi usługi Azure Cognitive Search.](https://azure.microsoft.com/support/legal/sla/search/v1_0/)

Ponieważ repliki są kopiami danych, posiadanie wielu replik umożliwia usłudze Azure Cognitive Search ponowne uruchomienie komputera i konserwację jednej repliki, podczas gdy wykonywanie zapytań jest kontynuowane w innych replikach. Z drugiej strony jeśli zabierzesz repliki, poniesiesz spadek wydajności kwerendy, przy założeniu, że te repliki były niedostatecznie wykorzystywanym zasobem.

## <a name="scale-for-geo-distributed-workloads-and-geo-redundancy"></a>Skalowanie dla obciążeń rozproszonych geograficznie i nadmiarowości geograficznej

W przypadku obciążeń rozproszonych geograficznie użytkownicy znajdujący się daleko od centrum danych hosta będą mieli wyższe opóźnienia. Jednym z czynników ograniczających zagrożenie jest aprowizowanie wielu usług wyszukiwania w regionach o bliższej odległości od tych użytkowników.

Usługa Azure Cognitive Search nie zapewnia obecnie zautomatyzowanej metody replikowania geograficznego indeksów usługi Azure Cognitive Search w różnych regionach, ale istnieją pewne techniki, które mogą być używane, które mogą ułatwić implementację tego procesu i zarządzanie nim. Są one opisane w następnych kilku sekcjach.

Celem geograficznie rozproszonego zestawu usług wyszukiwania jest udostępnienie co najmniej dwóch indeksów w dwóch lub więcej regionach, w których użytkownik jest kierowany do usługi Azure Cognitive Search, która zapewnia najniższe opóźnienie, jak pokazano w tym przykładzie:

   ![Krzyżowa karta usług według regionów][1]

### <a name="keep-data-synchronized-across-multiple-services"></a>Synchronizacja danych w wielu usługach

Istnieją dwie opcje synchronizacji rozproszonych usług wyszukiwania, które składają się z za pomocą [indeksatora usługi Azure Cognitive Search](search-indexer-overview.md) lub interfejsu API push (nazywanego również [interfejsem API REST usługi Azure Cognitive Search).](https://docs.microsoft.com/rest/api/searchservice/)  

### <a name="use-indexers-for-updating-content-on-multiple-services"></a>Używanie indeksatorów do aktualizowania zawartości w wielu usługach

Jeśli już używasz indeksatora w jednej usłudze, możesz skonfigurować drugi indeksator w drugiej usłudze, aby używał tego samego obiektu źródła danych, pobierając dane z tej samej lokalizacji. Każda usługa w każdym regionie ma swój własny indeksator i indeks docelowy (indeks wyszukiwania nie jest współużytkowany, co oznacza, że dane są duplikowane), ale każdy indeksator odwołuje się do tego samego źródła danych.

Oto wizualizacja wysokiego poziomu tego, jak ta architektura miałaby wyglądać.

   ![Pojedyncze źródło danych z rozproszonymi kombinacjami indeksatora i usług][2]

### <a name="use-rest-apis-for-pushing-content-updates-on-multiple-services"></a>Używanie interfejsów API REST do wypychania aktualizacji zawartości w wielu usługach

Jeśli używasz interfejsu API REST usługi Azure Cognitive Search do [wypychania zawartości w indeksie usługi Azure Cognitive Search,](https://docs.microsoft.com/rest/api/searchservice/update-index)możesz zachować synchronizację różnych usług wyszukiwania, wypychając zmiany do wszystkich usług wyszukiwania, gdy wymagana jest aktualizacja. W kodzie upewnij się, że do obsługi przypadków, w których aktualizacja do jednej usługi wyszukiwania kończy się niepowodzeniem, ale kończy się powodzeniem dla innych usług wyszukiwania.

## <a name="leverage-azure-traffic-manager"></a>Korzystanie z usługi Azure Traffic Manager

[Usługa Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) umożliwia kierowanie żądań do wielu witryn geograficznych, które są następnie wspierane przez wiele usług wyszukiwania. Jedną z zalet usługi Traffic Manager jest to, że może sondować usługę Azure Cognitive Search, aby upewnić się, że jest ona dostępna i kierować użytkowników do alternatywnych usług wyszukiwania w przypadku przestoju. Ponadto jeśli kierujesz żądania wyszukiwania za pośrednictwem witryn sieci Web platformy Azure, usługa Azure Traffic Manager umożliwia ładowanie przypadków równoważenia, w których witryna sieci Web jest w górę, ale nie usługa Azure Cognitive Search. Oto przykład architektury, która wykorzystuje menedżera ruchu.

   ![Cross-tab usług według regionu, z centralnym Menedżerem Ruchu][3]

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o warstwach cenowych i limitach usług dla każdego z nich, zobacz [Limity usług](search-limits-quotas-capacity.md). Zobacz [Planowanie pojemności,](search-capacity-planning.md) aby dowiedzieć się więcej o kombinacjach partycji i replik.

Aby uzyskać dyskusję na temat wydajności i demonstracji technik omówionych w tym artykule, obejrzyj następujący film:

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON319/player]
> 

<!--Image references-->
[1]: ./media/search-performance-optimization/geo-redundancy.png
[2]: ./media/search-performance-optimization/scale-indexers.png
[3]: ./media/search-performance-optimization/geo-search-traffic-mgr.png
