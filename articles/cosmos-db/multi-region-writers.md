---
title: Wielu wzorców w skali globalnej z bazy danych Azure rozwiązania Cosmos | Dokumentacja firmy Microsoft
description: ''
services: cosmos-db
author: rimman
manager: kfile
ms.service: cosmos-db
ms.workload: data-services
ms.topic: article
ms.date: 05/07/2018
ms.author: rimman
ms.openlocfilehash: 2da6b4e957c7e44f399866fd11853363f7424e7d
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
---
# <a name="multi-master-at-global-scale-with-azure-cosmos-db"></a>Wielu wzorzec w obsłudze globalnych z bazy danych Azure rozwiązania Cosmos 
 
Opracowywanie globalnie rozproszone aplikacji odpowiadających z opóźnieniami lokalnych podczas utrzymywania spójne widoki danych na całym świecie jest trudne problem. Klienci Użyj globalnie rozproszone baz danych, ponieważ należy poprawić opóźnienia dostępu do danych, osiągnąć wysoką dostępność i zapewnia gwarantowane odzyskiwaniem (4) do wymagań biznesowych. Wiele wzorca w usłudze Azure DB rozwiązania Cosmos zapewnia wysoką dostępność (99,999%), jednocyfrowej milisekundy opóźnienia, aby zapisywać danych i skalowalność z obsługą rozwiązania wbudowanej kompleksowy i elastyczny. Te funkcje znacząco upraszcza projektowanie aplikacji rozproszonych globalnie. Dla aplikacji rozproszonych globalnie Obsługa wielu wzorców jest niezwykle istotne. 

![Architektura wieloma serwerami głównymi](./media/multi-region-writers/multi-master-architecture.png)

Z obsługą wielu wzorców bazy danych rozwiązania Cosmos platformy Azure możesz wykonać zapisy kontenerów danych (na przykład, kolekcje, wykresów, tabel) wszędzie na świecie. Można aktualizować dane w dowolnym regionie skojarzony z konta bazy danych. Te aktualizacje danych można propagować asynchronicznie. Oprócz szybki dostęp i opóźnienie zapisu danych master wielu stanowi praktyczne rozwiązanie dla trybu failover i równoważenia obciążenia problemy. Podsumowując, z bazy danych Azure rozwiązania Cosmos otrzymasz opóźnienie zapisu < 10 ms na 99-ty percentyl w dowolnym miejscu w świecie, 99,999% zapisu i odczytu dostępności w dowolnym miejscu świecie i możliwość skalowania zarówno zapisu i odczytu przepływności wszędzie na świecie.   

## <a name="a-simple-multi-master-example--content-publishing"></a>Publikowanie prosty przykład wieloma serwerami głównymi — zawartości  

Przyjrzyjmy się scenariusz rzeczywistych, w którym opisano, jak korzystać z wieloma serwerami głównymi obsługi z bazy danych Azure rozwiązania Cosmos. Należy wziąć pod uwagę zawartości platformy publikowania oparte na usłudze Azure DB rozwiązania Cosmos. Poniżej przedstawiono niektóre wymagania, które musi spełniać ta platforma obsługi użytkowników dla konsumentów i wydawców. 

* Zarówno twórcy, jak i subskrybentów są rozkładane całego świata.  

* Autorzy należy opublikować artykułów (Zapisz) do ich lokalnej regionu (najbliższego).  

* Autorzy mają czytników/subskrybentów swoich artykułów, które są dystrybuowane na całym świecie.  

* Subskrybenci należy uzyskać powiadomienie, gdy nowe artykuły są publikowane.  

* Subskrybenci musi mieć możliwość odczytu artykuły z ich lokalnego regionu. Powinny być również możliwość dodawania przeglądami tych artykułów.  

* Każda osoba, która tym autora artykułów powinna być stanie widoku wszystkie przeglądy dołączony do artykułów z lokalnego regionu.  

Zakładając, że miliony konsumentów i wydawców z miliardów artykułów, wkrótce mamy rozwiązanie problemów skali wraz z gwarantujących miejscowości dostępu. Przypadek użycia jest kandydatem idealne dla wielu wzorca bazy danych Azure rozwiązania Cosmos. 

## <a name="benefits-of-having-multi-master-support"></a>Obsługa wielu wzorców korzyści 

Obsługa wielu wzorców jest istotne dla aplikacji rozproszonych globalnie. Wzorzec wielu składa się z [wielu regionach wzorca](distribute-data-globally.md) który równomiernie uczestniczyć w modelu zapisu dowolnym (aktywny aktywny wzorzec) i jest używany do upewnij się, że dane są dostępne w dowolnym momencie których należy. Aktualizacje wprowadzone w poszczególnych region asynchronicznie są propagowane do innych regionów (które z kolei regionów głównego w ich własnych). Regiony platformy Azure DB rozwiązania Cosmos automatycznie działających jako główny regiony w konfiguracji z wieloma serwerami głównymi pracy obsłużone danych wszystkie repliki i upewnij się, [globalne spójności i integralność danych](consistency-levels.md). Na poniższej ilustracji przedstawiono replikacji odczytu/zapisu, punktowe i iloczyn wzorca.

![Główne i wieloma serwerami głównymi](./media/multi-region-writers/single-vs-multi-master.png)

Implementującej wzorca wielu samodzielnie dodaje obciążeń na deweloperów. Na dużą skalę klientów, którzy podejmą próbę wdrożenia wielu wzorzec na ich własnych może poświęcać setki godzin, konfigurowania i testowania na całym świecie konfiguracji wielu wzorców i wiele ma dedykowany zbiór engineers, którego jedynym zadaniem jest monitorowanie i obsługa wielu danych głównych Replikacja. Tworzenie i zarządzanie wieloma serwerami głównymi Instalatora na własny czas, zasoby wprowadzają samej aplikacji i powoduje znacznie wyższe koszty. Azure DB rozwiązania Cosmos zapewnia obsługę wielu wzorców "out of box" i usuwa ten narzut deweloperów.  

Podsumowując, wielu wzorca zapewnia następujące korzyści:

* **Lepsze odzyskiwania po awarii, zapis dostępności i pracy awaryjnej**-master wielu może służyć do zachowania wysokiej dostępności krytycznym bazy danych w większym stopniu. Na przykład wielu głównej bazy danych można replikować dane z jednego regionu w regionie trybu failover gdy regionu podstawowego staje się niedostępny z powodu awarii lub regionalnej awarii. Region trybu failover będzie służyć jako funkcjonalnej region wzorca do obsługi aplikacji. Wzorzec wielu można zapewnić lepszą ochronę "przeżywalność" w odniesieniu do klęski żywiołowej, awarie zasilania sabotażu i/lub ponieważ pozostałych regionów może znajdować się w geograficznie różnych wzorców wielu przy użyciu blokady % > 99,999 zapisu gwarantuje dostępność. 

* **Ulepszone dla użytkowników końcowych opóźnienie zapisu** — bliżej danych (do którego obsługujesz) jest dla użytkowników końcowych lepsze środowisko zostanie. Na przykład, jeśli użytkownicy w Europie, ale baza danych znajduje się w Stanach Zjednoczonych lub Australii, dodano opóźnienia jest około 140 ms i 300 ms dla swoich regionach. Opóźnienia są niedopuszczalne do uruchomienia z wielu popularnych gier, wymagania dotyczące bankowości lub interakcyjne aplikacji (sieć web lub mobilnej). Opóźnienia odgrywa ogromnych rolę w klienta z punktu widzenia użytkownika obsługi wysokiej jakości i została uznana wpłynąć na zachowanie użytkowników w pewnym stopniu zauważalne. Jak technologię zwiększa i szczególnie w przypadku pojawienie się AR, rzeczywistości Wirtualnej i MR wymagające więcej środowiska bez ramek i danych, deweloperzy teraz niezbędne do tworzenia oprogramowania systemy z opóźnienia rygorystycznych wymagań. W związku z tym lokalnie dostępne aplikacje i dane (zawartość aplikacji) ma większe znaczenie. Z wieloma główny w usłudze Azure DB rozwiązania Cosmos wydajność jest tak szybko, jak lokalny regularne odczytuje i zapisuje i rozszerzony globalnie przez geograficznie dystrybucji.  

* **Zwiększona skalowalność zapisu i przepływność zapisu** — wielu wzorca zapewni wyższej przepustowości i wykorzystania, jednocześnie zapewniając wielu modeli spójności z poprawności gwarantuje i umów SLA kopię zapasową. 

  ![Skalowania przepływność zapisu z wieloma głównego](./media/multi-region-writers/scale-write-throughput.png)

* **Lepszą obsługę środowiskach rozłączonych (na przykład urządzenia brzegowe)** -master wielu umożliwia użytkownikom replikowania wszystkie lub podzbiór danych z urządzenia do najbliżej regionu w środowisku bez połączenia. Ten scenariusz jest typowy dla systemów automatyzacji, w której poszczególne laptopów (bez połączenia urządzenia) przechowuje podzbiór danych dotyczących poszczególnych sprzedawcy działu sprzedaży. Regiony wzorca w chmurze, które znajdują się w dowolnym miejscu na świecie może działać jako miejsce docelowe kopii z urządzenia brzegowe zdalnego.  

* **Równoważenie obciążenia** — przy użyciu wielu wzorca obciążenia w aplikacji można rebalanced przez przeniesienie użytkowników/obciążeń z obciążonego regionu w regionach, gdzie jest równomiernego obciążenia. Zapisu pojemność można łatwo rozszerzyć przez dodanie nowego regionu, a następnie przechodząc niektórych operacji zapisu do nowego regionu. 

* **Lepsze wykorzystanie pojemności elastycznie** — z wieloma główny, w przypadku obciążeń intensywnie zapisu i mieszanych użytkownik może zużyć całą pojemność elastycznie w różnych regionach.  W niektórych przypadkach można ponownie rozesłać odczyty i zapisy bardziej równomiernie, dzięki czemu wymaga mniej przepustowości można zainicjować obsługi administracyjnej i prowadzi do bardziej oszczędności, dla klientów.  

* **Architekturach aplikacji prostszy i bardziej odporne** — aplikacje przechodzenia do konfiguracji z wieloma serwerami głównymi uzyskać zagwarantować odporność danych.  Azure DB rozwiązania Cosmos ukrywanie wszystkich złożoności znacznie upraszcza się projekt aplikacji i architektury. 

* **Bez ryzyka testowania trybu failover** -testowania trybu Failover nie będzie mieć obniżenia na przepływność zapisu. Z wieloma głównego innych regionów są pełne wzorce, więc trybu failover nie mają dużo wpływ na przepływność zapisu.  

* **Obniżyć całkowity koszt Ownership(TCO) i deweloperzy** -spotkania skalowalność, wydajność, globalne dystrybucji zakresie docelowego czasu odzyskiwania są często kosztowne z powodu dodatków kosztowne i obsługa infrastruktury kopii zapasowych, który jest w stanie spoczynku dopiero po awarii ataki. Z bazy danych Azure rozwiązania Cosmos główny wielu umów SLA branży kopię zapasową deweloperzy już wymagają tworzenia i utrzymywania "logiki sklejki wewnętrznej bazy danych", się i uzyskać spokój umysłu obciążeniami krytycznym. 

## <a name="use-cases-where-multi-master-support-is-needed"></a>Gdzie jest potrzebna obsługa wielu wzorców przypadki użycia

Istnieje wiele przypadków użycia wielu wzorca w usłudze Azure DB rozwiązania Cosmos: 

* **IoT** -uproszczoną implementację rozproszonego przetwarzania danych IoT umożliwia wielu wzorca bazy danych Azure rozwiązania Cosmos. Rozproszona geograficznie krawędzi wdrożeń, korzystających z wolnego konflikt CRDT zreplikowanych danych typów często muszą śledzić czas serii danych z wielu lokalizacji. Każde urządzenie może podłączony do jednego z najbliższego regionów, a urządzenia mogą być przesyłane (na przykład samochód) i mogą być dynamicznie rehomed można zapisać w innym regionie.  

* **Handlu elektronicznego** -zapewnienie obsługi użytkowników w scenariuszach handlu elektronicznego wymaga wysokiej dostępności i odporności na scenariuszy awarii. W przypadku, gdy region nie powiedzie się, sesje użytkowników, zakupy wózkach sklepowych active chcesz bezproblemowo pobrania przez inny region bez utraty stanu listy. W międzyczasie aktualizacje wprowadzone przez użytkownika musi zostać obsłużone odpowiednio (przykład dodaje i usuwa z koszyka musi przesyłanych). Przy użyciu wielu wzorca bazy danych Azure rozwiązania Cosmos można bezpiecznie, obsłużyć takich scenariuszy z przejścia między regionami active przy zachowaniu spójności widoku z punktu widzenia użytkownika. 

* **Wykrywanie oszustw/anomalii** -często w przypadku aplikacji, które monitorowania aktywności użytkownika lub konto działania są globalnie rozproszone i musi śledzić kilka zdarzeń jednocześnie. Podczas tworzenia i utrzymywania wyniki dla użytkownika, czynności w różnych regionach geograficznych jednocześnie należy zaktualizować wyniki, aby zachować wbudowanego metryki ryzyka. Azure DB rozwiązania Cosmos można mieć pewność, że programiści nie muszą obsługiwać scenariusze konfliktów na poziomie aplikacji. 

* **Współpraca** — dla aplikacji, które rangę oparte na popularne artykuły, takie jak towarów na sprzedaży lub nośnik zużywanych itp. Można uzyskać skomplikowane, śledzenia popularne w regionach geograficznych, zwłaszcza w przypadku opłaty licencyjne muszą być podjęcie decyzji reklamy czasu płatną lub rzeczywistej. Klasyfikacja, sortowanie i raportowania w wielu regionach na całym świecie, w czasie rzeczywistym z bazy danych rozwiązania Cosmos Azure umożliwia deweloperom tworzenie funkcji przy minimalnym nakładzie pracy i bez obniżania opóźnienia. 

* **Pomiaru** — zliczanie i regulacji użycia (takie jak wywołania interfejsu API używany minut transakcje na sekundę,) można implementować globalnie dla uproszczenia przy użyciu wielu wzorca bazy danych Azure rozwiązania Cosmos. Rozwiązywanie konfliktów wbudowanych zapewnia zarówno dokładność liczby i rozporządzenia w czasie rzeczywistym. 

* **Personalizacja** — Określa, czy jest utrzymanie geograficznie rozproszonych liczniki, które wyzwalają akcje, takie jak lojalność punktów nagrody lub implementacja sesji użytkownika spersonalizowanych widoków, wysokiej dostępności i uproszczone rozproszona geograficznie Zliczanie pochodzącymi z bazy danych Azure rozwiązania Cosmos, umożliwia aplikacji osiągnąć wysoką wydajność i prostota. 

## <a name="conflict-resolution-with-multi-master"></a>Rozwiązywanie konfliktów z wieloma głównego 

Z wieloma głównego żądania często jest że dwie (lub więcej) repliki ten sam rekord mogą być aktualizowane jednocześnie przez autorów różne w różnych regionach dwóch lub więcej. Jednoczesne zapisy może prowadzić do dwóch różnych wersji tego samego rekordu i bez rozwiązywania konfliktów wbudowane, a aplikacji należy wykonać rozwiązywania konfliktów, aby rozwiązać tę niespójność.  

**Przykład** -Załóżmy, że używasz bazy danych Azure rozwiązania Cosmos jako trwałości w sklepie aplikację koszyka zakupów i ta aplikacja jest wdrażana w dwóch regionach: wschodnie stany USA i zachodnie stany USA.  Jeśli mniej więcej w tym samym czasie użytkownika w Warszawie dodaje element do jego koszyk (na przykład książki) podczas procesu zarządzania spisu w wschodnie stany USA unieważnia innego elementu koszyka zakupów (na przykład nowy numer telefonu) dla tego samego użytkownika w odpowiedzi na s powiadomienie upplier, który miał zostało opóźnione Data wydania. W punkcie czasu T1 rekordy koszyka zakupów w dwóch regionach są różne. Bazy danych będzie używać jej replikacji i mechanizmu rozpoznawania konfliktu Aby rozwiązać tę niespójność i ostatecznie wybrać jedną z dwóch wersji koszyka zostaną. Przy użyciu heurystyki rozwiązania konfliktu najczęściej stosowane przez wielu wzorców baz danych (na przykład ostatniego zapisu wins), jest niemożliwe dla użytkownika lub aplikacji do prognozowania, która wersja zostanie wybrany. W obu przypadkach dane zostaną utracone lub mogą wystąpić nieoczekiwane zachowanie. Zaznaczenie wersji regionu wschodnie następnie wybór użytkownika nowy element zakupu (książka) jest utracone i zaznaczenie regionu zachodnie następnie wcześniej wybranego elementu (to znaczy phone) jest nadal koszyka. W obu przypadkach informacje zostaną utracone. Ponadto inny proces sprawdzania zakupy koszyka między czasu T1 a T2 ma również zachowanie deterministyczna. Na przykład dałby w efekcie proces w tle, który wybiera magazynu realizacji i aktualizuje koszyka koszty wysyłki wyniki, które powodują konflikt z ostatecznego zawartość koszyka. Jeśli proces jest uruchomiony w regionie Zachód i alternatywne 1 staje się stan, czy obliczeniowe koszty wysyłki dla dwóch elementów, mimo że koszyka wkrótce może mieć tylko jednego elementu, książce. 

Azure DB rozwiązania Cosmos implementuje logiki obsługi powodujące konflikt zapisy wewnątrz samego aparatu bazy danych. Udostępnia bazę danych systemu Azure rozwiązania Cosmos **Obsługa rozwiązywania konfliktów kompleksowy i elastyczny** oferując kilka konflikt rozpoznawania modeli, w tym automatyczne (bez konflikt CRDT replikowane typów danych), ostatniego zapisu Wins (LWW) i (niestandardowe Procedurę składowaną) do rozwiązywania konfliktów automatycznego. Modele rozwiązania konfliktu zagwarantować poprawność i spójność i usunąć obciążenia z deweloperom myśleć o spójności, dostępność, wydajność, opóźnienie replikacji i złożone kombinacje zdarzeń w tryb failover z magazynu geograficznie i konflikty między region zapisu.  

  ![Rozwiązywanie konfliktów iloczyn wzorca](./media/multi-region-writers/multi-master-conflict-resolution-blade.png)

Konieczne będzie 3 typów modeli rozwiązania konfliktu oferowane przez bazy danych Azure rozwiązania Cosmos. Semantyka modeli rozwiązania konfliktu są następujące: 

**Automatyczne** — jest to domyślne zasady rozwiązania konfliktu. Zaznaczenie tych zasad powoduje, że bazy danych rozwiązania Cosmos platformy Azure automatycznie rozwiązać konflikt aktualizacji po stronie serwera i podaj silne--spójność ostateczna gwarancje. Bazy danych Azure rozwiązania Cosmos implementuje wewnętrznie, rozwiązywania konfliktów automatyczne za korzystanie z usług konflikt — wolne-replikowane — typy danych (CRDTs) wewnątrz aparatu bazy danych.  

**Ostatniego zapisu usługi Wins (LWW)** — Wybieranie ta zasada umożliwia rozwiązywanie konfliktów zależności zdefiniowane w systemie synchronizowane właściwość sygnatury czasowej lub niestandardowe właściwości zdefiniowane w rekordy powodujące konflikt wersji. Rozwiązywanie konfliktów wykonywany po stronie serwera i wersji z najnowszą znacznik czasu został wybrany jako wygrał użytkownik.  

**Niestandardowe** — można zarejestrować aplikacji zdefiniowano konflikt rozpoznawania logiki rejestrując procedury składowanej. Procedura składowana zostanie uzyskać wywoływane w przypadku wykrycia konfliktów aktualizacji pod nadzorem transakcji bazy danych po stronie serwera. Jeśli wybierzesz opcję, ale nie można zarejestrować procedury składowanej (lub procedurę składowaną zgłasza wyjątek w czasie wykonywania), można uzyskać dostęp do wszystkich powodujące konflikt wersji za pomocą źródła konflikty i rozwiązać je pojedynczo.  

## <a name="next-steps"></a>Kolejne kroki  

W tym artykule należy wcześniej sposobu używania globalnie rozproszone wielu wzorca z bazy danych Azure rozwiązania Cosmos. Następnie Przyjrzyjmy się następujące zasoby: 

* [Dowiedz się więcej o sposobie bazy danych Azure rozwiązania Cosmos obsługi dystrybucji globalne](distribute-data-globally.md)  

* [Więcej informacji na temat automatycznego przechodzenia w tryb failover w usłudze Azure DB rozwiązania Cosmos](regional-failover.md)  

* [Dowiedz się więcej o globalnych spójności z bazy danych Azure rozwiązania Cosmos](consistency-levels.md)  

* Opracowywanie z wielu regionach przy użyciu bazy danych Azure rozwiązania Cosmos - [interfejsu API SQL](tutorial-global-distribution-sql-api.md), [API bazy danych MongoDB](tutorial-global-distribution-mongodb.md), lub [tabeli interfejsu API](tutorial-global-distribution-table.md)  