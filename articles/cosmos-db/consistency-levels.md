---
title: Poziomy spójności w usłudze Azure DB rozwiązania Cosmos | Dokumentacja firmy Microsoft
description: Azure DB rozwiązania Cosmos ma pięć poziomy spójności, aby ułatwić równoważenie ostatecznego spójności, dostępnością i opóźnieniem kompromis.
keywords: eventual consistency, azure cosmos db, azure, Microsoft azure
services: cosmos-db
author: SnehaGunda
manager: kfile
documentationcenter: ''
ms.assetid: 3fe51cfa-a889-4a4a-b320-16bf871fe74c
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2018
ms.author: sngun
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0f38d05dc720dd596c81a51abf7040ac062e8158
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="tunable-data-consistency-levels-in-azure-cosmos-db"></a>Poziomy spójności danych dostosowywalne w usłudze Azure DB rozwiązania Cosmos
Azure DB rozwiązania Cosmos jest zaprojektowany od podstaw się z globalnego dystrybucji pamiętać dla każdego modelu danych. Zaprojektowano go do zapewnienia gwarancje przewidywalną małe opóźnienia i wielu modeli dobrze zdefiniowany swobodna spójności. Obecnie bazy danych Azure rozwiązania Cosmos zawiera pięć poziomów spójności: silne, nieaktualność, sesji, prefiks spójne i "ostateczna". Nieaktualność, sesji prefiks spójne i ostatecznego są nazywane "swobodna spójności modeli" świadczą one mniej spójności niż silne, czyli większości wysokiej spójny model dostępne. 

Oprócz **silne** i **spójność ostateczna** modeli często oferowane przez rozproszonych baz danych, bazy danych rozwiązania Cosmos Azure udostępnia trzy modele więcej starannie wersja i operationalized spójności:  **ograniczone nieaktualności**, **sesji**, i **spójne prefiks**. Przydatność każdego z tych poziomów spójności została zweryfikowana względem rzeczywistych przypadków użycia. Zbiorczo te poziomy spójności pięć umożliwiają należy dobrze uzasadnione kompromis między spójności, dostępnością i opóźnieniem. 

W poniższego klipu wideo Andrew Liu Menedżera programów DB rozwiązania Cosmos Azure przedstawiono funkcje gotowe dystrybucji globalnego.

>[!VIDEO https://www.youtube.com/embed/-4FsGysVD14]

## <a name="distributed-databases-and-consistency"></a>Rozproszone baz danych i spójności
Komercyjnych rozproszonej bazy danych można podzielić na dwie kategorie: baz danych, które nie oferują opcji dobrze zdefiniowany możliwością ich kontrolowania spójności na wszystkich i baz danych, które oferują dwie możliwości programowania extreme (silne a spójność ostateczna). 

Pierwsze zmuszają deweloperów aplikacji do zajmowania się drobnymi szczegółami protokołów replikacji i wymagają dokonywania trudnych wyborów między spójnością, dostępnością, opóźnieniem i przepływnością. Drugie kładą nacisk na konieczność wyboru między dwoma skrajnościami. Pomimo mnogości badań i ponad 50 propozycji modeli spójności społeczność związana z rozpowszechnianymi bazami danych nie była w stanie skomercjalizować poziomów spójności poza spójnością silną i ostateczną. Rozwiązania cosmos DB umożliwia deweloperom wybór między pięć modeli dobrze zdefiniowany spójności wzdłuż spektrum spójności — siły, spójność powiązanej nieaktualności [sesji](http://dl.acm.org/citation.cfm?id=383631), prefiks spójne i "ostateczna". 

![Usługa Azure Cosmos DB oferuje wiele dobrze zdefiniowanych (swobodniejszych) modeli spójności do wyboru](./media/consistency-levels/five-consistency-levels.png)

W poniższej tabeli przedstawiono poszczególne gwarancje oferowane przez każdy poziom spójności.
 
**Poziomy spójności i gwarancje**

| Poziom spójności | Gwarancje |
| --- | --- |
| Silna | Linearizability. Odczyty dotrą do zwrócenia najnowszej wersji elementu.|
| Powiązana nieaktualność | Spójny prefiks. Odczyty opóźniona zapisy, przez co najwyżej k prefiksy lub interwał t |
| Sesja   | Spójny prefiks. Monotoniczne odczyty, monotoniczne zapisy, odczytywanie swoich zapisów, zapisy następują po odczytach |
| Spójny prefiks | Zwracane aktualizacje to pewne prefiksy ze wszystkich aktualizacji, bez przerw |
| Ostateczna  | Odczyty poza kolejnością |

Na koncie usługi Cosmos DB można skonfigurować domyślny poziom spójności (i później przesłonić spójność dla określonego żądania odczytu). Wewnętrznie domyślny poziom spójności stosuje się do danych w ramach zestawy partycji, które mogą obejmować regionów. Spójność powiązanej nieaktualności preferowane jest około 73% spójność sesji Użyj dzierżaw usługi Azure DB rozwiązania Cosmos i 20%. Około 3% klientów bazy danych Azure rozwiązania Cosmos wypróbować różne poziomy spójności początkowo przed rozpoczęciem na wybór spójności określonych aplikacji. Tylko 2% dzierżaw usługi Azure DB rozwiązania Cosmos zastąpić poziomy spójności na podstawie danego żądania. 

W DB rozwiązania Cosmos Odczyty podawane w sesji, prefiks spójne i spójność ostateczna dwukrotnie są jako tanie jako odczyty z nieaktualności silne lub ograniczonych. Rozwiązania cosmos bazy danych ma branży kompleksowe umów SLA, w tym gwarancje spójności oraz dostępność, przepustowości i opóźnień. Wykorzystuje bazę danych systemu Azure rozwiązania Cosmos [sprawdzania linearizability](http://dl.acm.org/citation.cfm?id=1806634), które stale pracuje nad telemetrii usługi i jawnie raporty naruszenie spójności dla Ciebie. Spójność powiązanej nieaktualności bazy danych Azure rozwiązania Cosmos monitoruje i raportuje wszelkie naruszenia granice k i t. Dla wszystkich pięciu poziomów spójności swobodna udostępnia również bazy danych Azure rozwiązania Cosmos [probabilistically ograniczone Metryka nieaktualności](http://dl.acm.org/citation.cfm?id=2212359) bezpośrednio do użytkownika.  

## <a name="service-level-agreements"></a>Umowy dotyczące poziomu usług

Azure DB rozwiązania Cosmos oferuje kompleksowe 99,99% [SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/) które przepływności gwarancji, spójności, dostępnością i opóźnieniem dla bazy danych Azure rozwiązania Cosmos bazy danych konta ograniczone do jednego regionu Azure skonfigurowany z żadnym z pięciu spójności poziomy, lub konta bazy danych obejmujących wiele regiony platformy Azure, skonfigurowanych za pomocą dowolnego z czterech poziomów spójności swobodna. Ponadto niezależnie od wyboru poziomu spójności, bazy danych rozwiązania Cosmos Azure oferuje 99,999% umowy SLA dla dostępności odczytu dla konta bazy danych obejmujących co najmniej dwóch regionach platformy Azure.

## <a name="scope-of-consistency"></a>Zakres spójności
Poziom szczegółowości spójności obejmuje żądanie jednego użytkownika. Żądanie zapisu może odpowiadać insert, replace, upsert lub usunąć transakcji. Podobnie jak w przypadku operacji zapisu, transakcji odczytu/zapytania ma również zakres na żądanie użytkownika. Użytkownik może być konieczna w dużej z podziałem na strony zestawu wyników, obejmujących wiele partycji, ale każda odczytać transakcji jest ograniczone do jednej strony i udostępniane przez w ramach jednej partycji.

## <a name="consistency-levels"></a>Poziomy spójności
Domyślny poziom spójności można skonfigurować na Twoim koncie bazy danych, która ma zastosowanie do wszystkich kolekcji (i baz danych) na koncie DB rozwiązania Cosmos. Domyślnie wszystkie odczyty i zapytań wystawiony na podstawie zasoby zdefiniowane przez użytkownika będą używać domyślnego poziomu spójności określonego na konto bazy danych. Może osłabić poziomu spójności odczytu/zapytania określonego żądania, używając w każdej z obsługiwanych interfejsów API. Istnieje pięć typów poziomy spójności obsługiwane przez protokół replikacji bazy danych Azure rozwiązania Cosmos zapewniające wyczyść zależność między spójności określone gwarancje i wydajności, zgodnie z opisem w tej sekcji.

<a id="strong"></a>
**Silne**: 

* Zapewnia wysoki poziom spójności [linearizability](https://aphyr.com/posts/313-strong-consistency-models) gwarancja odczyty gwarancji, aby powrócić do najnowszej wersji elementu. 
* Silna spójność gwarantuje, że zapis jest widoczny dopiero w po jest trwałym zatwierdzeniu przez większość kworum replik. Zapis jest albo synchronicznie trwałym zatwierdzeniu przez serwera podstawowego i kworum replik pomocniczych, lub jest zostało przerwane. Odczytu zawsze zostaje potwierdzony głosów kworum do odczytu, klient nigdy nie widzą zapisu niezatwierdzone lub jej część jest zawsze gwarantowane można odczytać najnowszych potwierdzonego zapisu. 
* Azure DB rozwiązania Cosmos kont, które są skonfigurowane do używania wysoki poziom spójności nie można skojarzyć więcej niż jeden region platformy Azure z użyciem konta bazy danych Azure rozwiązania Cosmos.  
* Koszt operacja odczytu (w postaci liczby [jednostek żądania](request-units.md) używane) z wysoki poziom spójności jest wyższy niż sesji i "ostateczna", ale taka sama jak spójność powiązanej nieaktualności.

<a id="bounded-staleness"></a>
**Ograniczone nieaktualności**: 

* Ograniczone nieaktualności gwarantuje spójności odczytami może opóźniona zapisy, przez co najwyżej *K* wersji lub prefiksy elementu lub *t* interwał czasu. 
* W związku z tym podczas wybierania ograniczone nieaktualności, "nieaktualności" można skonfigurować na dwa sposoby: numer wersji *K* elementu za pomocą którego odczytami opóźniona zapisami i przedział czasu *t* 
* Ograniczone nieaktualności oferty globalne zamówienia z wyjątkiem "okna nieaktualności." Istnieje monotoniczna gwarancje odczytu w obrębie regionu zarówno wewnątrz i na zewnątrz "nieaktualności okno." 
* Spójność powiązanej nieaktualności zapewnia gwarancję spójności silniejsze niż sesji, spójne prefiksu lub spójność ostateczna. Dla aplikacji rozproszonych globalnie zaleca się, że używasz spójność powiązanej nieaktualności w scenariuszach, gdzie chcesz mieć wysoki poziom spójności, jednak dostępności 99,99% i małe opóźnienia.   
* Azure kont rozwiązania Cosmos bazy danych, które są skonfigurowane przy użyciu spójność powiązanej nieaktualności można skojarzyć dowolną liczbę regiony platformy Azure z użyciem konta bazy danych Azure rozwiązania Cosmos. 
* Koszt operacja odczytu (pod względem używane RUs) z spójność powiązanej nieaktualności jest wyższy niż sesji i spójność ostateczna, ale taka sama jak wysoki poziom spójności.

<a id="session"></a>
**Sesja**: 

* W odróżnieniu od oferowanych przez poziomy spójności nieaktualności silne i ograniczonego modeli globalne spójności spójność sesji obejmuje sesji klienta. 
* Spójność sesji jest idealny dla wszystkich scenariuszy, w którym sesję użytkownika lub urządzenia uczestniczy, ponieważ gwarantuje monotoniczna odczyty, monotoniczna zapisu i odczytu gwarancje własne zapisów (RYW). 
* Spójność sesji zapewnia przewidywalną spójność sesji i maksymalnie odczytać przepływności jednocześnie zapewniając najmniejsza opóźnienia zapisów i odczytów. 
* Azure kont rozwiązania Cosmos bazy danych, które są skonfigurowane przy użyciu spójność sesji można skojarzyć dowolną liczbę regiony platformy Azure z użyciem konta bazy danych Azure rozwiązania Cosmos. 
* Koszt operacja odczytu (pod względem używane RUs) z poziomu spójności sesji jest mniejsze niż silne i ograniczonego nieaktualności, ale spójność ostateczna więcej niż.

<a id="consistent-prefix"></a>
**Prefiks spójne**: 

* Prefiks spójne gwarantuje, że w przypadku braku kolejnych zapisów repliki w grupie ostatecznie zbieżność. 
* Prefiks spójne gwarantuje, że odczyty nigdy nie zobaczyć zapisy poza kolejnością. Jeśli wykonano operacji zapisu w kolejności `A, B, C`, klient widzi albo `A`, `A,B`, lub `A,B,C`, ale nigdy nie poza kolejnością jak `A,C` lub `B,A,C`.
* Azure kont rozwiązania Cosmos bazy danych, które są skonfigurowane przy użyciu prefiksu spójne spójności można skojarzyć dowolną liczbę regiony platformy Azure z użyciem konta bazy danych Azure rozwiązania Cosmos. 

<a id="eventual"></a>
**Ewentualne**: 

* Spójność ostateczna gwarantuje, że w przypadku braku kolejnych zapisów repliki w grupie ostatecznie zbieżność. 
* Spójność ostateczna jest postaci najsłabszą spójność, których klient może pobrać wartości, które są starsze niż te, które miały zauważony.
* Spójność ostateczna zapewnia najsłabszą spójność odczytów, ale dzięki uzyskać najmniejsze opóźnienia zarówno odczytów i zapisów.
* Azure kont rozwiązania Cosmos bazy danych, które są skonfigurowane przy użyciu spójność ostateczna można skojarzyć dowolną liczbę regiony platformy Azure z użyciem konta bazy danych Azure rozwiązania Cosmos. 
* Koszt operacja odczytu (pod względem używane RUs) z spójność ostateczna poziom jest najniższa wszystkie poziomy spójności bazy danych Azure rozwiązania Cosmos.

## <a name="configuring-the-default-consistency-level"></a>Konfigurowanie poziomu spójności domyślne
1. W [portalu Azure](https://portal.azure.com/), na pasku przechodzenia kliknij **bazy danych Azure rozwiązania Cosmos**.
2. Na stronie usługi **Azure Cosmos DB** wybierz konto bazy danych do modyfikacji.
3. Na stronie konta, kliknij przycisk **domyślna spójność**.
4. W **domyślna spójność** , wybierz nowy poziom spójności i kliknij przycisk **zapisać**.
   
    ![Zrzut ekranu: wyróżnianie ikonę ustawień i spójności domyślny wpis](./media/consistency-levels/database-consistency-level-1.png)

## <a name="consistency-levels-for-queries"></a>Poziomy spójności dla zapytań
Domyślnie dla użytkownika zasobów poziomu spójności dla zapytań jest taka sama jak poziomu spójności dla odczytów. Domyślnie indeks jest aktualizowana synchronicznie na każdym insert, Zamień lub usuń elementu do kontenera DB rozwiązania Cosmos. Dzięki temu zapytania uwzględnić poziomu spójności co odczyty punktu. Podczas zapisu zoptymalizowany i obsługuje stałej ilości zapisy, indeks synchroniczne konserwacji i obsługująca spójne zapytania bazy danych Azure rozwiązania Cosmos można skonfigurować pewne kolekcji, aby zaktualizować ich indeksu w trybie opóźnienia. Dodatkowo indeksowanie z opóźnieniem zwiększa wydajność zapisu i jest idealne dla scenariuszy wprowadzanie zbiorczego obciążenia jest głównie ciężki odczytu.  

| Tryb indeksowania | Czyta | Zapytania |
| --- | --- | --- |
| Spójność (ustawienie domyślne) |Wybierz jedną z nieaktualności silne, ograniczone, sesji, spójne prefiksu lub ostatecznego |Wybierz jedną z nieaktualności silne, ograniczone, sesji lub ostatecznego |
| Lazy |Wybierz jedną z nieaktualności silne, ograniczone, sesji, spójne prefiksu lub ostatecznego |Ostateczna |
| Brak |Wybierz jedną z nieaktualności silne, ograniczone, sesji, spójne prefiksu lub ostatecznego |Nie dotyczy |

Jako z żądaniami odczytu, możesz obniżyć poziom spójności żądania określonej kwerendy w każdym interfejsu API.

## <a name="consistency-levels-for-the-mongodb-api"></a>Poziomy spójności dla interfejsu API bazy danych MongoDB

Azure DB rozwiązania Cosmos implementuje obecnie bazy danych MongoDB w wersji 3.4, mają dwa ustawienia spójności silne i "ostateczna". Ponieważ bazy danych rozwiązania Cosmos Azure multi-api, ustawienia zgodności są stosowane na poziomie konta i wymuszania spójności jest kontrolowany przez każdego interfejsu API.  Do 3,6 bazy danych MongoDB, nie było żadnych koncepcji spójność sesji, więc jeśli ustawisz konto bazy danych MongoDB interfejsu API, które będzie używane spójność sesji spójności jest obniżona do ostatecznego podczas korzystania z bazy danych MongoDB interfejsów API. Jeśli potrzebujesz gwarancji your właścicielem zapisu i odczytu konta bazy danych MongoDB interfejsu API, domyślny poziom spójności dla konta powinien być ustawiony na silne lub ograniczonych nieaktualności.

## <a name="next-steps"></a>Kolejne kroki
Jeśli chcesz zrobić więcej informacje o poziomy spójności i wady i zalety, zaleca się następujące zasoby:

* [Wyjaśniono spójności replikowanych danych za pośrednictwem baseball (klip wideo) przez Jakuba Dougowi](https://www.youtube.com/watch?v=gluIh8zd26I)
* [Wyjaśniono spójności replikowanych danych za pośrednictwem baseball (dokument oficjalny) przez Jakuba Dougowi](http://research.microsoft.com/pubs/157411/ConsistencyAndBaseballReport.pdf)
* [Gwarancje sesji dla słabo spójności replikowanych danych](http://dl.acm.org/citation.cfm?id=383631)
* [Wady i zalety spójności w projekcie: nowoczesnych systemów bazy danych dystrybucji: zakończenie jest tylko część wątku](http://computer.org/csdl/mags/co/2012/02/mco2012020037-abs.html)
* [Spójność powiązanej nieaktualności prawdopodobieństwa (PBS) do praktycznych częściowe kworum](http://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf)
* [Ostateczna spójne — poprawione](http://allthingsdistributed.com/2008/12/eventually_consistent.html)
* [Obciążenia, wydajność i dostępność systemów kworum dziennika SIAM obliczeniowych](http://epubs.siam.org/doi/abs/10.1137/S0097539795281232)
* [Wersje: pełne i automatyczne linearizability sprawdzania, postępowania 2010 konferencji ACM SIGPLAN programowania w języku projektowanie i wdrażanie](http://dl.acm.org/citation.cfm?id=1806634)
* [Probabilistically spójność powiązanej nieaktualności do praktycznych częściowe kworum](http://dl.acm.org/citation.cfm?id=2212359)
