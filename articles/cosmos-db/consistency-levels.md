---
title: Poziomy spójności w usłudze Azure Cosmos DB | Dokumentacja firmy Microsoft
description: Usługa Azure Cosmos DB ma pięć poziomów spójności, aby ułatwić równoważenie ostatecznej spójnością, dostępnością i opóźnieniem wad i zalet.
keywords: eventual consistency, azure cosmos db, azure, Microsoft azure
services: cosmos-db
author: aliuy
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 03/27/2018
ms.author: andrl
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8d95790dc09f6d26c6ae749ed0cd386053c5cb35
ms.sourcegitcommit: 7b845d3b9a5a4487d5df89906cc5d5bbdb0507c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/14/2018
ms.locfileid: "42058180"
---
# <a name="tunable-data-consistency-levels-in-azure-cosmos-db"></a>Poziomy spójności dostosowania danych w usłudze Azure Cosmos DB
Usługa Azure Cosmos DB została zaprojektowana od podstaw się o globalnej dystrybucji, należy pamiętać, dla każdego modelu danych. Jest przeznaczony do oferty gwarancje przewidywalne małe opóźnienia i wiele dobrze zdefiniowanych swobodna modeli spójności. Obecnie usługa Azure Cosmos DB oferuje pięć poziomów spójności: silne, powiązana nieaktualność, sesja, spójny prefiks i "eventual". Powiązana nieaktualność, sesja, spójny prefiks i ostateczna są określane jako "modele rozluźnionej spójności", ponieważ zapewniają one mniej spójności od jego siły, czyli większość wysoce spójny model, dostępne. 

Oprócz **silne** i **spójności ostatecznej** modele często oferowane przez rozproszonych baz danych, usługi Azure Cosmos DB oferuje trzy więcej modeli spójności uważnie wersja i zoperacjonalizowanej:  **powiązana nieaktualność**, **sesji**, i **spójny prefiks**. Użyteczność każdego z tych poziomów spójności został zweryfikowany w kontekście świata rzeczywistego, przypadki użycia. Zbiorczo te poziomy spójności pięć umożliwiają wykonywanie dobrze uzasadnione kompromis między spójnością, dostępnością i opóźnieniem. 

W poniższym klipie wideo Azure Menedżer programu usługi Cosmos DB Andrew Liu pokazuje funkcje kompleksowa dystrybucja globalna.

>[!VIDEO https://www.youtube.com/embed/-4FsGysVD14]

## <a name="distributed-databases-and-consistency"></a>Rozproszonych baz danych i spójność
Komercyjnie rozpowszechniane bazy danych można podzielić na dwie kategorie: bazy danych, które nie są one dobrze zdefiniowanych sprawdzalnych opcji spójności na wszystkich i baz danych, które oferują dwie skrajne opcje programowania (spójność silna lub ostateczna). 

Pierwsze zmuszają deweloperów aplikacji do zajmowania się drobnymi szczegółami protokołów replikacji i wymagają dokonywania trudnych wyborów między spójnością, dostępnością, opóźnieniem i przepływnością. Drugie kładą nacisk na konieczność wyboru między dwoma skrajnościami. Pomimo mnogości badań i ponad 50 propozycji modeli spójności społeczność związana z rozpowszechnianymi bazami danych nie była w stanie skomercjalizować poziomów spójności poza spójnością silną i ostateczną. Usługa cosmos DB umożliwia deweloperom wybranie pięć dokładnie zdefiniowanych modeli spójności wzdłuż spektrum spójności — silna, powiązana nieaktualność [sesji](http://dl.acm.org/citation.cfm?id=383631), spójny prefiks i "eventual". 

![Usługa Azure Cosmos DB oferuje wiele dobrze zdefiniowanych (swobodniejszych) modeli spójności do wyboru](./media/consistency-levels/five-consistency-levels.png)

W poniższej tabeli przedstawiono poszczególne gwarancje oferowane przez każdy poziom spójności.
 
**Poziomy spójności i gwarancje**

| Poziom spójności | Gwarancje |
| --- | --- |
| Silna | Atomowych. Operacje odczytu mają gwarancję zwracają najbardziej aktualną wersję elementu.|
| Powiązana nieaktualność | Spójny prefiks. Odczyty opóźnione w stosunku do zapisów o co najwyżej k prefiksów lub interwał równy t |
| Sesja   | Spójny prefiks. Monotoniczne odczyty, monotoniczne zapisy, odczytywanie swoich zapisów, zapisy następują po odczytach |
| Spójny prefiks | Zwracane aktualizacje to pewne prefiksy ze wszystkich aktualizacji, bez przerw |
| Ostateczna  | Odczyty poza kolejnością |

Na koncie usługi Cosmos DB można skonfigurować domyślny poziom spójności (i później przesłonić spójność dla określonego żądania odczytu). Wewnętrznie domyślny poziom spójności ma zastosowanie do danych w zestawach partycji, które może obejmować regiony. Preferuj powiązana nieaktualność, około 73% spójność sesji Użyj dzierżaw usługi Azure Cosmos DB i 20%. Około 3% klientów usługi Azure Cosmos DB eksperymentować z różnymi poziomami spójności początkowo przed rozpoczęciem na wybranie określonych spójności w swojej aplikacji. Tylko 2% dzierżaw usługi Azure Cosmos DB musi zostać zastąpiona poziomów spójności na podstawie danego żądania. 

W usłudze Cosmos DB odczytuje udostępnione pod sesja, spójny prefiks, i spójności ostatecznej dwa razy są jak tania jako odczyty z spójnością silną lub ograniczonych nieaktualność. Usługa cosmos DB ma branży kompleksowe umowy SLA, w tym gwarancje spójności oraz dostępność, przepływność i opóźnienie. Usługa Azure Cosmos DB wykorzystuje [sprawdzania atomowych](http://dl.acm.org/citation.cfm?id=1806634), który działa w sposób ciągły telemetrii usługi i jawnie raporty naruszenia wszelkich spójności. Dla powiązana nieaktualność usługi Azure Cosmos DB, monitoruje i zgłasza wszelkie naruszenia granic k i t. Dla wszystkich pięciu poziomów rozluźnionej spójności, również raporty usługi Azure Cosmos DB [probabilistically powiązana nieaktualność metryki](http://dl.acm.org/citation.cfm?id=2212359) bezpośrednio do użytkownika.  

## <a name="service-level-agreements"></a>Umowy dotyczące poziomu usług

Usługa Azure Cosmos DB oferuje kompleksową dostępność przez 99,99% [umowy SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/) które przepływności gwarancji, spójnością, dostępnością i opóźnieniem dla usługi Azure Cosmos DB, bazy danych kont ograniczone do jednego regionu platformy Azure skonfigurowano dowolne pięć spójności poziomy lub konta bazy danych obejmujące wiele regionów platformy Azure skonfigurowano przy użyciu dowolnego z czterech poziomów rozluźnionej spójności. Ponadto niezależnie od wybranego poziomu spójności, usługi Azure Cosmos DB oferuje umowy SLA 99,999% dostępności odczytu dla konta bazy danych obejmujące co najmniej dwóch regionach platformy Azure.

## <a name="scope-of-consistency"></a>Zakres spójności
Poziom szczegółowości spójności jest ograniczony do żądań pojedynczego użytkownika. Żądania zapisu może odpowiadać insert, replace, upsert lub usunąć transakcji. Podobnie jak w przypadku zapisu transakcji odczytu/zapytania również obejmuje żądań pojedynczego użytkownika. Użytkownik może być konieczne stronicowanie za pośrednictwem duży zestaw wyników, obejmujące wiele partycji, ale każdy odczyt transakcja jest ograniczone do pojedynczej strony i udostępniana z w obrębie jednej partycji.

## <a name="consistency-levels"></a>Poziomy spójności
Domyślny poziom spójności na Twoje konto bazy danych, która ma zastosowanie do wszystkich kontenerów (i baz danych) można skonfigurować w ramach konta usługi Cosmos DB. Domyślnie wszystkie operacje odczytu i zapytania względem zasobów zdefiniowanych przez użytkownika używa domyślny poziom spójności określony na koncie bazy danych. Umożliwiają nieprzestrzeganie poziomu spójności odczytu/zapytania określonego żądania przy użyciu w każdej z obsługiwanych interfejsów API. Istnieje pięć typów poziomów spójności obsługiwane przez protokół replikacji usługi Azure Cosmos DB, zapewniających wyczyść kompromis między wydajnością, a gwarancje spójności określonych, zgodnie z opisem w tej sekcji.

<a id="strong"></a>
**Silne**: 

* Zapewnia wysoki poziom spójności [atomowych](https://aphyr.com/posts/313-strong-consistency-models) gwarancja odczyty gwarancję zwracania najbardziej aktualną wersję elementu. 
* Wysoki poziom spójności gwarantuje, że operacja zapisu jest tylko widoczne po jest trwale zatwierdzone przez kworum z większością replik. Zapis albo synchronicznie stawia trwale zarówno podstawowego, jak i kworum replik pomocniczych lub została ona przerwana. Odczyt jest zawsze potwierdzane przez większością odczytu kworum, klient nigdy nie zobaczy zapisu niezatwierdzone lub częściowe i zawsze musi odczytać najnowszy potwierdzony zapis. 
* Konta usługi Cosmos DB platformy Azure, które są skonfigurowane do używania wysoki poziom spójności nie można skojarzyć więcej niż jednego regionu platformy Azure przy użyciu swojego konta usługi Azure Cosmos DB.  
* Koszt operacji odczytu (pod względem [jednostek żądania](request-units.md) używane) za pomocą silnej spójności jest wyższa niż sesji oraz "eventual", ale taka sama jak powiązana nieaktualność.

<a id="bounded-staleness"></a>
**Powiązana nieaktualność**: 

* Powiązana nieaktualność gwarancje spójności, które odczytami może być opóźniona zapisów o co najwyżej *K* wersje lub prefiksy elementu lub *t* przedział czasu. 
* W związku z tym, podczas wybierania powiązana nieaktualność, "nieaktualność" można skonfigurować na dwa sposoby: numer wersji *K* elementu za pomocą którego odczyty opóźnione w stosunku zapisami i przedział czasu *t* 
* Powiązana nieaktualność oferty całkowitej globalnej kolejności z wyjątkiem w ramach "okno nieaktualność." Monotoniczny gwarancje odczytu istnieją w obrębie regionu zarówno wewnątrz i na zewnątrz "nieaktualność okna." 
* Powiązana nieaktualność zapewnia gwarancję spójności silniejsza niż sesja, spójny prefiks lub spójność ostateczna. Dla aplikacji dystrybuowanych globalnie firma Microsoft zaleca, powiązana nieaktualność możesz używać dla scenariuszy, gdzie chcesz mieć wysoki poziom spójności, jednak dostępność przez 99,99% dostępności i małym opóźnieniu.   
* Kont usługi Cosmos DB platformy Azure, które są skonfigurowane przy użyciu spójności powiązana nieaktualność można skojarzyć dowolną liczbę regionów platformy Azure przy użyciu swojego konta usługi Azure Cosmos DB. 
* Koszt operacji odczytu (w postaci liczby RU) przy użyciu powiązana nieaktualność jest wyższa niż sesji i spójność ostateczną, ale taka sama jak silnej spójności.

<a id="session"></a>
**Sesja**: 

* W przeciwieństwie do modeli spójności globalnego, oferowane przez poziomów spójności silnych i powiązana nieaktualność spójność sesji jest ograniczony do sesji klienta. 
* Spójność sesji to idealne rozwiązanie w przypadku wszystkich scenariuszy, gdzie zaangażowana jest sesję użytkownika lub urządzenia, ponieważ gwarantuje monotoniczne odczyty, zapisy monotoniczny i odczytu gwarantuje własne operacje zapisu (RYW). 
* Spójność sesji zapewnia przewidywalnej spójności dla sesji i maksymalna przepływność zapisu, jednocześnie oferując najniższe opóźnienia zapisów i operacji odczytu. 
* Kont usługi Cosmos DB platformy Azure, które są skonfigurowane przy użyciu spójność sesji można skojarzyć dowolną liczbę regionów platformy Azure przy użyciu swojego konta usługi Azure Cosmos DB. 
* Koszt operacji odczytu (w postaci liczby RU) z poziomu spójności sesji jest mniej niż silnych i powiązana nieaktualność, ale więcej niż spójność.

<a id="consistent-prefix"></a>
**Spójny prefiks**: 

* Spójny prefiks gwarantuje, że w przypadku braku dalszy zapis replik w grupie ostatecznie zbiegają się. 
* Spójny prefiks gwarantuje, że odczyty nigdy nie zobaczy zapisów poza kolejnością. Jeśli zapisy były wykonywane w kolejności `A, B, C`, a następnie klient będzie widział albo `A`, `A,B`, lub `A,B,C`, ale nigdy nie poza kolejnością takich jak `A,C` lub `B,A,C`.
* Kont usługi Cosmos DB platformy Azure, które są skonfigurowane przy użyciu spójności spójny prefiks można skojarzyć dowolną liczbę regionów platformy Azure przy użyciu swojego konta usługi Azure Cosmos DB. 

<a id="eventual"></a>
**Ostateczna**: 

* Spójność ostateczna gwarantuje, że w przypadku braku dalszy zapis replik w grupie ostatecznie zbiegają się. 
* Spójności ostatecznej jest najsłabsza formą spójności, w których klient może pobrać wartości, które są starsze niż te, które miały zauważony.
* Spójność ostateczna zapewnia najsłabsza spójności odczytu, ale oferuje najmniejszego opóźnienia do odczytów i zapisów.
* Konta platformy Azure Cosmos DB, które są skonfigurowane przy użyciu spójności ostatecznej można skojarzyć dowolną liczbę regionów świadczenia usługi Azure za pomocą konta usługi Azure Cosmos DB. 
* Koszt operacji odczytu (ru) w zakresie spójności ostatecznej poziom jest najniższy wszystkich poziomów spójności usługi Azure Cosmos DB.

## <a name="configuring-the-default-consistency-level"></a>Konfigurowanie domyślny poziom spójności
1. W [witryny Azure portal](https://portal.azure.com/), na pasku dostępu kliknij **usługi Azure Cosmos DB**.
2. Na stronie usługi **Azure Cosmos DB** wybierz konto bazy danych do modyfikacji.
3. Na stronie konta kliknij **domyślna spójność**.
4. W **ustawienie domyślna spójność** stronie, wybierz nowy poziom spójności i kliknij **Zapisz**.
   
    ![Zrzut ekranu: wyróżnianie ikonę ustawień i wpis ustawienie domyślna spójność](./media/consistency-levels/database-consistency-level-1.png)

## <a name="consistency-levels-for-queries"></a>Poziomy spójności w przypadku zapytań
Domyślnie dla zasobów zdefiniowanych przez użytkownika poziomu spójności dla zapytań jest taka sama jak poziomu spójności dla operacji odczytu. Domyślnie zaktualizowaniu indeksu synchronicznie na każdym insert, Zamień lub Usuń element do kontenera usługi Cosmos DB. Dzięki temu zapytań, aby przestrzegać tego samego poziomu spójności, jak w przypadku odczytów punktu. Azure Cosmos DB jest zoptymalizowane pod kątem zapisu i obsługuje stałą woluminy zapisy, konserwacja indeksu synchroniczne i obsługuje zapytania zgodne, można skonfigurować niektóre kontenerów, aby zaktualizować ich indeksu opóźnieniem. Dalsze opóźnieniem indeksowania zwiększa wydajność zapisu i jest idealny dla scenariuszy pozyskiwania zbiorcze, gdy obciążenie jest głównie odczytu dużą ilością zawartości.  

| Tryb indeksowania | Czyta | Zapytania |
| --- | --- | --- |
| Spójność (ustawienie domyślne) |Wybierz jedną z sesji silne, powiązana nieaktualność, spójny prefiks i ostateczna |Wybierz jedną z silne, powiązana nieaktualność, sesja lub ostateczna |
| Lazy |Wybierz jedną z sesji silne, powiązana nieaktualność, spójny prefiks i ostateczna |Ostateczna |
| Brak |Wybierz jedną z sesji silne, powiązana nieaktualność, spójny prefiks i ostateczna |Nie dotyczy |

Jak z żądaniami odczytu, możesz obniżyć poziom spójności żądania określonej kwerendy w każdy interfejs API.

## <a name="consistency-levels-for-the-mongodb-api"></a>Poziomy spójności dla interfejsu API usługi MongoDB

Usługa Azure Cosmos DB implementuje obecnie bazy danych MongoDB w wersji 3.4, która ma dwa ustawienia spójności silną i ostateczną. Ze względu na to, że usługa Azure Cosmos DB obejmuje wiele interfejsów API, ustawienia spójności mają zastosowanie na poziomie konta i wymuszanie spójności jest kontrolowane przez poszczególne interfejsy API.  Przed wprowadzeniem usługi MongoDB 3.6 koncepcja spójności sesji nie istniała, więc w przypadku ustawienia konta interfejsu API usługi MongoDB do korzystania ze spójności sesji spójność zostanie obniżona do ostatecznej podczas używania interfejsów API usługi MongoDB. Jeśli potrzebujesz gwarancji odczytu własnego zapisu do konta interfejsu API usługi MongoDB, domyślny poziom spójności dla konta powinien zostać ustawiony na silny lub na powiązaną nieaktualność.

## <a name="next-steps"></a>Kolejne kroki
Jeśli chcesz zrobić więcej informacje o poziomy spójności i wady, zaleca się następujące zasoby:

* [Wyjaśnienie pojęcia spójności danych replikowanych baseballu (wideo) przez Doug Terry](https://www.youtube.com/watch?v=gluIh8zd26I)
* [Wyjaśnienie pojęcia spójności danych replikowanych baseballu (dokument oficjalny) przez Doug Terry](http://research.microsoft.com/pubs/157411/ConsistencyAndBaseballReport.pdf)
* [Sesja gwarancje słabo spójności replikowanych danych](http://dl.acm.org/citation.cfm?id=383631)
* [Wady i zalety spójności w nowoczesny wygląd systemy bazy danych dystrybucji: limit to tylko część wątku](https://www.computer.org/web/csdl/index/-/csdl/mags/co/2012/02/mco2012020037-abs.html)
* [Powiązana nieaktualność probabilistyczny (PBS) dla praktyczne kworum częściowe](http://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf)
* [Ostateczna spójne — poprawiony](http://allthingsdistributed.com/2008/12/eventually_consistent.html)
* [Obciążenia, wydajność i dostępność systemów kworum dziennika SIAM obliczeniowych](http://epubs.siam.org/doi/abs/10.1137/S0097539795281232)
* [Seria: atomowych kompletne i automatycznego sprawdzania, materiały z konferencji ACM SIGPLAN 2010 na programowania języka projektowania i implementacji](http://dl.acm.org/citation.cfm?id=1806634)
* [Probabilistically powiązana nieaktualność uzyskać praktyczne kworum częściowe](http://dl.acm.org/citation.cfm?id=2212359)
