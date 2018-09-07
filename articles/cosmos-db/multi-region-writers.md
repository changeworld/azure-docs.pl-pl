---
title: Wielu wzorców w skali globalnej dzięki usłudze Azure Cosmos DB | Dokumentacja firmy Microsoft
description: ''
services: cosmos-db
author: rimman
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: rimman
ms.openlocfilehash: faca70ccc1b96a8cefd65a941abde579ce54a616
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/07/2018
ms.locfileid: "44050225"
---
# <a name="multi-master-at-global-scale-with-azure-cosmos-db"></a>Multi-Master w skali globalnej usługa Azure Cosmos DB 
 
Tworzenie globalnie rozproszonych aplikacji, które odpowiada przy użyciu lokalnego opóźnienia, utrzymywanie spójnej widoki danych na całym świecie jest wyzwaniem. Klienci używać globalnie rozproszonej bazy danych, gdyż wymagają one Zmniejszaj opóźnienia dostępu do danych, uzyskania danych o wysokiej dostępności, upewnij się, odzyskiwania po awarii gwarantowane, a do ich wymagań biznesowych. Multi-Master w usłudze Azure Cosmos DB zapewnia wysoki poziom dostępności (99,999%), milisekundowe opóźnienie do zapisania danych i skalowalność za pomocą wbudowanej kompleksowe i elastyczne rozwiązania w pomocy technicznej. Te funkcje są znacznie upraszcza tworzenie aplikacji dystrybuowanych globalnie. Dla aplikacji dystrybuowanych globalnie Obsługa wielu wzorców jest niezwykle istotne. 

![Architektura wielu wzorców](./media/multi-region-writers/multi-master-architecture.png)

Z obsługą wielu wzorców usługi Azure Cosmos DB można wykonać zapisu w kontenerach danych (na przykład, kolekcje, wykresów, tabel) rozproszonych w dowolnym miejscu na świecie. Możesz aktualizować dane w dowolnym regionie, który jest skojarzony z Twoim kontem bazy danych. Te aktualizacje danych możesz propagować asynchronicznie. Oprócz zapewniania szybkiego dostępu i opóźnienie zapisu danych, Multi-Master udostępnia praktyczne rozwiązanie dla trybu failover i równoważenia obciążenia problemów. Podsumowując, za pomocą usługi Azure Cosmos DB uzyskasz opóźnienie zapisu < 10 ms w 99. percentylu dowolnym miejscu na świecie, zapisu przez 99,999% czasu i dostępność do odczytu w dowolnym miejscu na świecie i możliwość skalowania zarówno zapisu i odczytu przepływność w dowolnym miejscu na świecie.   

> [!IMPORTANT]
> Obsługa wielu wzorców jest dostępna w prywatnej wersji zapoznawczej, aby użyć wersji zapoznawczej [Zarejestruj](#sign-up-for-multi-master-support) teraz.

## <a name="sign-up-for-multi-master-support"></a>Zarejestruj obsługę wielu wzorców

Jeśli masz już subskrypcję platformy Azure, możesz zarejestrować się do dołączenia do programu Podgląd wielu wzorców w witrynie Azure portal. Jeśli jesteś nowym użytkownikiem platformy Azure, utwórz konto [bezpłatnej wersji próbnej](https://azure.microsoft.com/free), aby uzyskać bezpłatny dostęp do usługi Azure Cosmos DB przez 12 miesięcy. Wykonaj poniższe kroki, aby zawnioskować o dostęp do programu Podgląd wielu wzorców.

1. W witrynie [Azure Portal](https://portal.azure.com) kliknij kolejno pozycje **Utwórz zasób** > **Bazy danych** > **Azure Cosmos DB**.  

2. Na stronie nowego konta Podaj nazwę konta usługi Azure Cosmos DB, wybierz interfejs API, subskrypcji, grupy zasobów i lokalizacji.  

3. Następnie wybierz pozycję **Zarejestruj, aby wyświetlić podgląd już dziś** pod polem Multi wzorca (wersja zapoznawcza).  

   ![Zarejestruj się w wielu wzorców (wersja zapoznawcza)](./media/multi-region-writers/sign-up-for-multi-master-preview.png)

4. W **Zarejestruj, aby wyświetlić podgląd już dziś** okienku kliknij **OK**. Po przesłaniu żądania stan zmieni się na **oczekujące na zatwierdzenie** w bloku tworzenia konta.  

Po zatwierdzeniu żądania otrzymasz wiadomość e-mail z powiadomieniem że Twoje żądanie zostało zatwierdzone. Ze względu na dużą liczbę żądań otrzymasz powiadomienie w ciągu tygodnia. Do ukończenia żądania nie jest wymagane utworzenie biletu pomocy technicznej. Żądania będą przeglądane w kolejności, w której zostały odebrane.

## <a name="a-simple-multi-master-example--content-publishing"></a>Publikowanie prosty przykład wielu wzorców — zawartość  

Spójrzmy na rzeczywistych scenariuszy, w którym opisano sposób obsługi wielu wzorców za pomocą usługi Azure Cosmos DB. Należy wziąć pod uwagę zawartości platformy publikowania oparte na usłudze Azure Cosmos DB. Poniżej przedstawiono niektóre wymagania, które ta platforma musi spełniać świetnego środowiska użytkownika dla wydawcy i konsumentów. 

* Autorzy i subskrybentów zostały rozmieszczone całego świata.  

* Autorzy musisz opublikować ich lokalnych regionów (najbliższego) artykułów (Zapisz).  

* Autorzy mają czytelnicy/abonentów swoich artykułów są dystrybuowane na całym świecie.  

* Subskrybenci powinien otrzymaj powiadomienie, gdy nowe artykuły są publikowane.  

* Subskrybenci musi mieć możliwość zapoznaj się z artykułami z ich lokalnych regionów. Również powinno być możliwe dodanie recenzji do tych artykułów.  

* Każda osoba tym autora artykułów powinny być możliwe Wyświetl wszystkie przeglądy dołączone do artykułów z regionu lokalnego.  

Miliony konsumentów i wydawców z miliardami artykułów, zakładając, że wkrótce mamy rozwiązanie problemów są skalowane wraz z gwarantujących miejscowość dostępu. Przypadek użycia jest idealny Release candidate dla wielu serwera głównego usługi Azure Cosmos DB. 

## <a name="benefits-of-having-multi-master-support"></a>Korzyści obsługi wielu wzorców 

Obsługa wielu wzorców ma zasadnicze znaczenie dla aplikacji dystrybuowanych globalnie. Multi-Master składa się z [wielu regionów wzorca](distribute-data-globally.md) , równie uczestniczyć w modelu zapisu miejscu (aktywny / aktywny wzorzec) i jest używany, aby upewnić się, że dane są dostępne w dowolnej chwili gdy jej potrzebujesz. Aktualizacje wprowadzone w poszczególnych regionów asynchronicznie są propagowane do innych regionów, (które z kolei wzorca regionów w swoim własnym). Regiony platformy Azure Cosmos DB automatycznie działających jako główny regionów w konfiguracji o wielu wzorców współpracują w celu zbiegają się je wszystkie repliki i upewnij się, [globalnego spójność i integralność danych](consistency-levels.md). Na poniższej ilustracji przedstawiono replikacji odczytu/zapisu dla pojedynczego elementu głównego i Multi-Master.

![Pojedynczego elementu głównego i wielu wzorców](./media/multi-region-writers/single-vs-multi-master.png)

Implementowanie Multi-Master na własną rękę dodaje obciążeń na deweloperów. Klientów na dużą skalę, którzy próbuje zaimplementować Multi-Master własnych może poświęcać setek godzin, konfigurowania i testowania na całym świecie Konfiguracja wielu wzorców, oraz oferują to dedykowany zestaw inżynierów, którego jedynym zadaniem jest monitorowanie i obsługa wielu danych głównych Replikacja. Tworzenie i zarządzanie nimi Konfiguracja wielu wzorców na własną rękę trwa pewien czas, zasoby innowacje w samej aplikacji i powoduje znacznie wyższe koszty. Usługa Azure Cosmos DB zapewnia obsługę wielu wzorców "out-of--box" i eliminuje to obciążenie od deweloperów.  

Podsumowując Multi-Master zapewnia następujące korzyści:

* **Lepsze odzyskiwanie po awarii, zapisu, dostępność i tryb failover**-Multi-Master może służyć do zachowania o wysokiej dostępności bazy danych o kluczowym znaczeniu w większym stopniu. Na przykład wielu wzorców bazy danych można replikować dane z jednego regionu do regionu pracy awaryjnej, gdy region podstawowy staje się niedostępny z powodu awarii lub regionalnych awarii. Region trybu failover będzie służyć jako w pełni funkcjonalne regionu głównego do obsługi tej aplikacji. Multi-Master można zapewnić lepszą ochronę "przeżywalność" w odniesieniu do klęski żywiołowe, awarii zasilania lub uszkodzenie, ponieważ pozostałych regionach, mogą korzystać z geograficznie różnych wielu serwerów głównych przy użyciu zapisu gwarantowanej dostępności > przez 99,999% czasu. 

* **Ulepszone opóźnienie zapisu dla użytkowników końcowych** — im bliżej danych (które obsługujących) to użytkownikom końcowym, tym lepsze środowisko będzie. Na przykład, jeśli istnieją użytkownicy, w Europie, ale baza danych znajduje się w Stanach Zjednoczonych i Australii, dodano opóźnienia jest około 140 ms i 300 ms dla swoich regionach. Opóźnienia są nie do przyjęcia zaczynać do wielu popularnych gier, wymagania dotyczące bankowości i aplikacji (sieć web lub mobilnej). Opóźnienie odgrywa rolę ogromna w wrażenie klienta środowiska wysokiej jakości i został okazało się mieć wpływ na zachowanie użytkowników do pewnego stopnia zauważalne. Jak zwiększa technologii i szczególnie w przypadku pojawienie się AR, VR i p. wymagające jeszcze bardziej zaawansowane i realistyczne środowiska deweloperów teraz niezbędne do tworzenia systemów oprogramowania wymagania dotyczące opóźnień rygorystyczne. W związku z tym dostępne lokalnie aplikacje i dane (zawartość dla aplikacji) jest niezwykle ważne. Dzięki Multi-Master w usłudze Azure Cosmos DB jest tak szybko, jak lokalny regularnych operacji odczytu i zapisu i ulepszone globalnie przez rozkład geograficzny.  

* **Zwiększona skalowalność zapisu i przepływność zapisu** — Multi-Master zapewni wyższą przepływność i większe wykorzystanie jednocześnie oferując wiele modeli spójności z poprawności gwarantuje i wspierana przez umowy SLA. 

  ![Skalowanie przepływności zapisu, zapewniając Multi-Master](./media/multi-region-writers/scale-write-throughput.png)

* **Lepsza obsługa odłączonych środowiskach (na przykład urządzenia brzegowe)** -Multi-Master umożliwia użytkownikom powtórzeniem wszystkie lub podzbiór danych z urządzenia usługi edge do najbliższego regionu w środowisku bez połączenia. Ten scenariusz jest typowy dla zautomatyzowanych systemów, gdzie osoby komputera przenośnego (urządzenie rozłączona) przechowuje podzbiór danych związanych z poszczególnych sprzedawcy działu sprzedaży. Główny regiony w chmurze, które znajdują się w dowolnym miejscu na świecie może działać jako obiekt docelowy kopiowanie danych z urządzeń brzegowych zdalnego.  

* **Równoważenie obciążenia** — przy użyciu wielu wzorca obciążenia w aplikacji można ponownie zbilansowana przez przeniesienie użytkowników/obciążeń z obszarem obciążonych do regionów, w którym obciążenia jest równomiernie rozłożona. Można ją łatwo rozszerzyć pojemność przez dodanie nowego regionu, a następnie przechodząc niektóre operacje zapisu do nowego regionu zapisu. 

* **Lepsze wykorzystanie zaprowizowaną pojemnością** — Multi-Master, w przypadku obciążeń procesów i mieszanych mogą saturate zaprowizowaną pojemnością w wielu regionach...  W niektórych przypadkach można redystrybuować odczyty i zapisy bardziej równomiernie, dzięki czemu wymaga mniej przepustowości do zainicjowania obsługi administracyjnej i prowadzi do bardziej oszczędności kosztów dla klientów.  

* **Architektury aplikacji prostszy i bardziej odporne** — przenoszenie Konfiguracja wielu wzorców aplikacji gwarantowaną odporność danych.  Za pomocą usługi Azure Cosmos DB, ukrywanie wszystkich złożoności co znacznie uprości architektura i projekt aplikacji. 

* **Bez ponoszenia ryzyka testowania trybu failover** — testowanie trybu Failover nie będzie miało wszelkich pogorszeniach na przepływność zapisu. Multi-Master wszystkie inne regiony są wzorców pełnej, więc trybu failover nie będzie miało dużo wpływ na przepływność zapisu.  

* **Obniż całkowity koszt Ownership(TCO) i metodyki DevOps** -spotkania, skalowalność, wydajność, dystrybucji globalnej, cele czasu odzyskiwania są często kosztowne z powodu drogich dodatków lub konserwacja infrastruktury kopii zapasowych, które są przechowywane do momentu awarii ataki. Za pomocą usługi Azure Cosmos DB Multi-Master wspierana przez wiodących w branży umowy SLA deweloperów już nie wymagają tworzenie i konserwowanie "logikę zaplecza pośredniczącego" się i zyskaj spokój obciążeń o kluczowym znaczeniu. 

## <a name="use-cases-where-multi-master-support-is-needed"></a>Przypadki użycia, gdy potrzebna jest obsługa wielu wzorców

Istnieją liczne przypadki użycia dla wielu serwera głównego w usłudze Azure Cosmos DB: 

* **IoT** -Multi-Master usługi Azure Cosmos DB Umożliwia uproszczoną implementację rozproszone przetwarzanie danych IoT. Wdrożeń rozproszone geograficznie krawędzi, korzystających z wolnego konflikt CRDT replikowane dane typy często zachodzi potrzeba śledzenia danych szeregów czasowych z wielu lokalizacji. Każde urządzenie może adresem IP, do jednego z regionów najbliższego i urządzenia mogą być przesyłane (na przykład samochód) i mogą być dynamicznie rehomed do zapisu do innego regionu.  

* **Handlu elektronicznego** -zapewniając doskonałe środowisko użytkownika w scenariuszach handlu elektronicznego wymaga wysokiej dostępności i odporności na scenariuszy awarii. W przypadku, gdy region zakończy się niepowodzeniem, sesje użytkowników, zakupy koszyki active życzyć listy muszą zostać bezproblemowo pobrana przez innego regionu bez utraty stanu. W międzyczasie aktualizacje wprowadzone przez użytkownika muszą być odpowiednio obsługiwane (przykład dodaje i usuwa z koszyka trzeba przekazać za pośrednictwem). Za pomocą Multi-Master usługi Azure Cosmos DB można bezpiecznie, obsłużyć takie scenariusze z płynne przejście między regionami active przy zachowaniu spójny widok z punktu widzenia użytkownika. 

* **Wykrywanie oszustw/anomalii** — często w przypadku aplikacji, które monitorowania aktywności użytkownika lub konto działania są globalnie rozproszone i musi śledzić kilka zdarzeń jednocześnie. Podczas tworzenia i utrzymywania wyniki dla użytkownika, akcji w różnych regionach geograficznych jednocześnie należy zaktualizować wyniki zapewnienie wbudowanych metryk ryzyka. Usługa Azure Cosmos DB może zapewnić, że deweloperzy nie muszą obsługiwać scenariusze konflikt na poziomie aplikacji. 

* **Współpraca** — aplikacje, które rangi w oparciu o popularność artykułów, takich jak towarów na sprzedaży lub multimediów do użycia itd. Śledzenie popularność, wiele regionów geograficznych uzyskać skomplikowane, zwłaszcza w przypadku opłat licencyjnych, które muszą być płatna lub rzeczywistego czasu reklamy decyzji w zakresie. Klasyfikacja, sortowanie i raportowania w wielu regionach na całym świecie, w czasie rzeczywistym za pomocą usługi Azure Cosmos DB umożliwia deweloperom tworzenie funkcji przy minimalnym nakładzie pracy i bez obniżania opóźnienia. 

* **Pomiaru** — zliczanie i regulacji użycia (np. wywołań interfejsu API używane minut transakcji na sekundę) można zaimplementować globalnie na ogromną przy użyciu usługi Azure Cosmos DB Multi-Master. Rozwiązywanie konfliktów wbudowanych gwarantuje zarówno dokładność liczb i rozporządzenie w czasie rzeczywistym. 

* **Personalizacja** — tego, czy jest obsługa geograficznie rozproszonych liczniki, które wyzwalać akcje, takie jak lojalności punkty, nagrody lub wykonania sesji użytkownika spersonalizowanych widoków, wysoką dostępność i uproszczony, rozproszone geograficznie Zliczanie udostępniane przez usługi Azure Cosmos DB umożliwia dostarczanie aplikacji o wysokiej wydajności na ogromną. 

## <a name="conflict-resolution-with-multi-master"></a>Rozwiązywanie konfliktów z wieloma główną 

Za pomocą Multi-Master żądania często jest czy dwie (lub więcej) replik ten sam rekord może zostać zaktualizowany jednocześnie przez różne składniki zapisywania w co najmniej dwóch różnych regionach. Jednoczesne operacje zapisu może prowadzić do dwóch różnych wersji tego samego rekordu, jak i bez wbudowanej rozwiązania, a sama aplikacja musi wykonać rozwiązywanie konfliktów, aby rozwiązać ten niespójności.  

**Przykład** -Załóżmy, że używasz usługi Azure Cosmos DB, ponieważ w trwałości sklepie najbardziej aplikacją koszyka zakupów i ta aplikacja jest wdrażana w dwóch regionach: wschodnie stany USA i zachodnie stany USA.  Jeśli mniej więcej w tym samym czasie, użytkownik w San Francisco dodaje element do jego koszyka (na przykład książka) podczas procesu zarządzania magazynu w regionie wschodnie stany USA unieważnia innego elementu koszyka zakupów (na przykład nowy numer telefonu) dla tego samego użytkownika w odpowiedzi na s powiadomienie o upplier, który miał zostało odroczone, Data wydania. W momencie T1 rekordy koszyka zakupów w dwóch regionach są różne. Bazy danych użyje jego replikacji i mechanizm rozwiązywania konfliktów, aby rozwiązać ten niespójności i po pewnym czasie jednej z dwóch wersji koszyka wybrane. Przy użyciu algorytmów heurystycznych rozwiązywania konfliktów, które zostały najczęściej stosowane przez wielu wzorców bazy danych (na przykład ostatniego zapisu wins), nie jest możliwe dla użytkownika lub aplikacji w celu przewidywania, która wersja zostanie wybrany. W obu przypadkach dane nie zostaną utracone lub mogą wystąpić nieoczekiwane zachowanie. Jeśli wybrano wersję region Pacyfiku, następnie wybranych przez użytkownika nowego elementu zakupu (książka) zostaną utracone, a wybranie regionu zachodnie stany następnie poprzednio wybranego elementu (czyli telefon) jest nadal w koszyku. W obu przypadkach informacje zostaną utracone. Na koniec jakiś inny proces sprawdzania, zakupy koszyka między czasu T1 a T2 będzie również zachowanie deterministyczna. Na przykład proces w tle, która wybiera magazynu realizacji i aktualizuje koszyka koszty wysyłki dałby w efekcie wyniki, które powodują konflikt z ostateczną zawartość koszyka. Jeśli proces działa w regionie zachodnie Stany i alternatywne 1 staje się mogą stać się rzeczywistością, czy obliczeń koszty wysyłki dla dwóch elementów, nawet jeśli koszyka wkrótce może mieć tylko jeden element, książki. 

Usługa Azure Cosmos DB implementuje logikę obsługi powodujące konflikt zapisu w aparacie bazy danych sam. Usługa Azure Cosmos DB oferuje **obsługę rozwiązywania konfliktów kompleksowe i elastyczne** , udostępniając kilka konflikt rozpoznawania modeli, w tym automatyczne (bez konfliktów CRDT zreplikowana typy danych), ostatniego zapisu Wins (LWW) i niestandardowe () Procedurę składowaną) do rozwiązywania konfliktów automatyczne. Modele rozwiązywania konfliktów gwarancje poprawność i spójność i usuwania obciążenia deweloperom trzeba się zajmować spójnością, dostępnością, wydajnością, opóźnienie replikacji i złożone kombinacje zdarzenia pracy awaryjnej geo i konfliktów zapisu między regionami.  

  ![Rozwiązywanie konfliktów tego wzorca](./media/multi-region-writers/multi-master-conflict-resolution-blade.png)

Masz 3 typy modeli rozpoznawania konfliktu oferowane przez usługę Azure Cosmos DB. Semantyka modeli rozpoznawania konfliktu są następujące: 

**Automatyczne** — jest to domyślne zasady rozwiązywania konfliktów. Wybranie tych zasad powoduje, że usługa Azure Cosmos DB automatycznie rozwiązać konfliktu aktualizacji po stronie serwera i zapewniają gwarancji silnej —-spójności ostatecznej. Wewnętrznie usługa Azure Cosmos DB implementuje automatyczne konfliktów, wykorzystaniem konflikt — bezpłatne-replikowane — typy danych (CRDTs) w aparacie bazy danych.  

**Ostatni zapis usługi Wins (LWW)** — Wybieranie, ta zasada pozwala rozwiązać konflikty zależności zdefiniowane przez system synchronizowanych właściwość sygnatury czasowej lub niestandardowych właściwości zdefiniowanych w rekordy powodujące konflikt wersji. Rozwiązywanie konfliktów się dzieje po stronie serwera i wersji przy użyciu najnowszych sygnatura czasowa jest wybrany jako zwycięzca.  

**Niestandardowe** -logiki rozwiązywania konfliktów zdefiniowanymi przez aplikację można zarejestrować, rejestrując procedury składowanej. Procedura składowana będzie pobrać wywoływane po wykryciu konfliktów aktualizacji pod nadzorem transakcji bazy danych, po stronie serwera. Jeśli wybierzesz opcję, ale nie można zarejestrować procedury składowanej (lub procedurę składowaną zgłasza wyjątek w czasie wykonywania), możesz uzyskać dostęp do wszystkich wersji powodujące konflikt za pośrednictwem kanału informacyjnego konflikty i rozwiąż je pojedynczo.  

## <a name="next-steps"></a>Kolejne kroki  

W tym artykule możesz dzięki modelom uczenia jak globalnie dystrybuowane Multi-Master za pomocą usługi Azure Cosmos DB. Następnie zapoznaj się z następującymi zasobami: 

* [Dowiedz się więcej o sposobie obsługi dystrybucję globalną w usłudze Azure Cosmos DB](distribute-data-globally.md)  

* [Więcej informacji na temat automatycznego przejścia w tryb failover w usłudze Azure Cosmos DB](regional-failover.md)  

* [Dowiedz się więcej o globalnej spójności za pomocą usługi Azure Cosmos DB](consistency-levels.md)  

* Programowanie z użyciem wielu regionów za pomocą usługi Azure Cosmos DB — [interfejsu API SQL](tutorial-global-distribution-sql-api.md), [interfejsu API usługi MongoDB](tutorial-global-distribution-mongodb.md), lub [interfejsu API tabel](tutorial-global-distribution-table.md)  
