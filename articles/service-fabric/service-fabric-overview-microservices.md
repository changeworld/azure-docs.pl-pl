---
title: Wprowadzenie do mikrousług na platformie Azure | Dokumentacja firmy Microsoft
description: Omówienie tworzenia aplikacji w chmurze dzięki podejściu mikrousług jest ważna do tworzenia nowoczesnych aplikacji oraz jak usługi Azure Service Fabric udostępnia platformę można to osiągnąć.
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: ''
ms.assetid: fae2be85-0ab4-4cd3-9d1f-e0d95fe1959b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/02/2017
ms.author: msfussell
ms.openlocfilehash: 04342be06430747ef64cb69c27ee93e6896775e5
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/17/2018
ms.locfileid: "39070875"
---
# <a name="why-a-microservices-approach-to-building-applications"></a>Dlaczego mikrousługi podejście do tworzenia aplikacji?
Jako programiści nie ma nic nowego: w jaki sposób firma Microsoft rozważać wyprowadzenie aplikacji do części składowych. Jest centralnym modelu obiektu orientacji, abstrakcje oprogramowania i componentization. Obecnie ta factorization zwykle formę klasy i interfejsy między biblioteki udostępnione i warstw technologicznych. Zazwyczaj warstwowego rozwiązania jest pobierana z magazynu zaplecza, logika biznesowa warstwy środkowej i interfejsem frontonu użytkownika (UI). Co *ma* zmienione w ciągu ostatnich kilku lat jest, że, jako programiści, tworzymy rozproszone aplikacje znajdujące się w chmurze i na podstawie przez firmę.

Zmieniających się potrzeb biznesowych są:

* Usługa, która jest wbudowana i działa na dużą skalę komunikować się z klientami w nowych regionach geograficznych (na przykład).
* Szybsze dostarczanie funkcji i możliwości, aby móc reagować na żądania klientów elastyczne.
* Wykorzystanie zasobów ulepszony, aby ograniczyć koszty.

Tych potrzeb biznesowych mają wpływ na *jak* możemy tworzyć aplikacje.

Aby uzyskać więcej informacji na temat podejścia platformy Azure do mikrousług odczytu [Mikrousługi: Rewolucja w aplikacjach obsługiwana przez chmurę](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/).

## <a name="monolithic-vs-microservice-design-approach"></a>Monolityczne, a na etapie projektowania mikrousług
Wszystkie aplikacje są rozwijane wraz z upływem czasu. Udane aplikacje rozwój dzięki przydatne dla osób. Aplikacje nie powiedzie nie ewolucji i po pewnym czasie są przestarzałe. Staje się pytanie: ile informacje dotyczące wymagań już dziś i co one będzie w przyszłości? Na przykład załóżmy, że możesz tworzyć aplikacji do raportowania dla działu. Masz pewność, że aplikacja pozostaje w zakresie swojej firmy i że raporty są krótkotrwałe. Wybór podejście różni się od, powiedz, kompilowanie to usługa, która dostarcza zawartość wideo dziesiątki milionów klientów. 

Czasami pobraniem czegoś spory jako Weryfikacja koncepcji jest współczynnik opracowuje, gdy wiesz, że później przeprojektowany aplikacji. Istnieje mały punkt w nadmiernie inżynierii coś, który nigdy nie zostanie wykorzystany. Jest zwykle kompromis inżynierów. Z drugiej strony mówiąc firmy dotyczące tworzenia aplikacji dla chmury, oczekiwania jest wskaźnik wzrostu i użycia. Problem polega na rozwój i skalowanie są nieprzewidywalne. Chcielibyśmy można było prototypu szybko codzienne również jesteśmy na ścieżce do czynienia z sukcesem w przyszłości. Jest to metoda startupu: kompilowanie, mierzenie, Dowiedz się i iteracji.

Podczas era klient serwer firma Microsoft pielęgnowane skoncentrowanie się na tworzeniu aplikacji warstwowych przy użyciu określonych technologii w przypadku każdej warstwy. Termin *monolityczne* aplikacji pojawiło się w przypadku tych metod. Interfejsy rozszerzenia się między warstwami i bardziej ściśle sprzężonych projektu zostało użyte między składnikami w każdej warstwie. Deweloperzy zaprojektowane i uwzględniona klas, które zostały skompilowane w bibliotekach i powiązane w kilku plików wykonywalnych i bibliotek DLL. 

Istnieją korzyści z takiego podejścia monolityczny projekt. Często jest to prostsza do projektowania, a ma ona szybsze wywołań między składnikami, ponieważ te wywołania są często za pośrednictwem komunikacji międzyprocesowej (IPC). Ponadto wszyscy testy jednego produktu jest zwykle więcej osób zasobów wydajne. Minusem jest, że istnieje ścisłego sprzężenia między warstwami warstwowe, i nie można skalować poszczególne składniki. Jeśli zachodzi potrzeba wykonania uaktualnienia i poprawki, trzeba czekać na zakończenie testów przez inne osoby. Trudno bardziej efektywnie.

Mikrousługi rozwiązać te wad i więcej ściśle dopasować wymagań biznesowych, ale mają również korzyści i zobowiązań. Korzyści mikrousługi są na tym, że każdy z nich zazwyczaj hermetyzuje prostsze funkcji biznesowych, która skalować w górę lub w dół testów, wdrażania i niezależnie zarządzać. Co ważne korzyści wynikające z podejścia z zastosowaniem mikrousług jest to, czy zespoły są oparte na więcej różnych scenariuszy biznesowych niż według technologii, co sprzyja warstwowego rozwiązania. W praktyce mniejszych zespołach opracowywanie mikrousług, oparta na scenariuszu klienta i używać dowolnej technologii, które postanowili. 

Innymi słowy organizacja nie musi standaryzacji tech do obsługi aplikacji mikrousług. Poszczególne zespoły, własnych usług można zrobić, co ma sens dla nich oparty na wiedzy zespołu lub co to jest najbardziej odpowiednie do rozwiązania problemu. W praktyce zbiór zalecanych technologii, takich jak określonego NoSQL przechowywania lub platforma aplikacji sieci web, jest preferowane.

Wadą mikrousług jest dostępna w zajmujących się bardziej złożonych wdrożeń i przechowywanie wersji i zarządzanie nimi zwiększonej liczby osobne jednostki. Ruch sieciowy między mikrousługi zwiększa oraz odpowiednie opóźnienia sieci. Wiele z dużą liczbą, precyzyjnych usług są przepis na okropnej wydajności. Bez narzędzi, aby ułatwić przeglądanie te zależności, trudno jest "zobacz" całego systemu. 

Standardy wprowadzić podejścia z zastosowaniem mikrousług działają przez wyrażenie zgody na temat sposobu komunikowania się i jest odporny na błędy czynności, należy przy użyciu usługi, a nie sztywne umów. Należy zdefiniować te umowy na początku projektu, ponieważ aktualizacja usług niezależnie od siebie nawzajem. Inny opis ukuł do projektowania dzięki podejściu mikrousług jest "szczegółowych zorientowane na usługę architektura (SOA)."

***W najprostszym podejście do projektowania mikrousług jest o odłączony federacji usług, niezależnie od zmiany do każdego i uzgodnione standardów komunikacji.***

Jak więcej aplikacji w chmurze są tworzone, osoby dowiedzieć się, że ten dekompozycji ogólną aplikacji do usługi niezależne, scenariusz skoncentrowany lepiej długoterminową.

## <a name="comparison-between-application-development-approaches"></a>Porównanie podejścia do projektowania aplikacji
![Tworzenie aplikacji platformy Service Fabric][Image1]

1) Monolityczną aplikację zawiera funkcje specyficzne dla domeny i zazwyczaj jest dzielona przez funkcjonalności warstw, takich jak sieci web, biznesowych i danych.

2) Skalowanie monolitycznych aplikacji przez Sklonowanie go na wiele serwerów/maszyn wirtualnych/kontenerów.

3) Aplikacja mikrousług oddziela funkcje na osobne usługi mniejsze.

4) Skaluje podejście mikrousług na zewnątrz, wdrażając każdej usługi niezależne, tworzenia wystąpień tych usług dla serwerów/maszyn wirtualnych/kontenerów.

Projektowanie za pomocą mikrousługi podejście nie jest panaceum na wszystkie projekty, ale jej dostosowanie z celów biznesowych, opisanych wcześniej. Począwszy od podejścia monolitycznego może być akceptowalne, jeśli wiesz, że użytkownik będzie miał możliwość przerabiać kod później na mikrousługach. Zazwyczaj zaczynają się od aplikacji monolitycznej i powoli Podziel je w etapach, począwszy od obszarów funkcjonalnych, które muszą być bardziej skalowalna lub agile.

Aby podsumować, podejścia z zastosowaniem mikrousług jest tworzą aplikację wiele małych usług. Usługi są uruchomione w kontenerach, które są wdrażane w klastrze maszyn. Mniejszych zespołów projektowania usługi, która koncentruje się na scenariuszu niezależnie przetestować, wersji, wdrażanie i skalowanie poszczególnych usług, dzięki czemu można rozwijać całej aplikacji.

## <a name="what-is-a-microservice"></a>Co to jest mikrousługi?
Istnieją różne definicje mikrousług. Wyszukiwania w Internecie znajdziesz wiele przydatnych zasobów, które zapewniają własne punkty widzenia i definicji. Jednak większość następujące właściwości mikrousługi są powszechnie uzgodnionych:

* Hermetyzuj scenariuszy biznesowych lub klienta. Co to jest problem, który rozwiązywanych?
* Opracowane przez niewielkiemu zespołowi inżynierów.
* Napisane w dowolnym języku programowania i korzystaj z dowolnej platformy.
* Składają się z kodu i (opcjonalnie) stanu, które są oddzielnie wersjonowanych, wdrażanie i skalowanie.
* Interakcja z innymi mikrousług za pośrednictwem dobrze zdefiniowanych interfejsów i protokołów.
* Mają unikatowe nazwy (URL) używany do rozpoznawania ich lokalizacji.
* Pozostają spójne i dostępne w razie awarii.

Umożliwia podsumowanie tych cech do:

***Aplikacje Mikrousług składają się z małych, oddzielnie wersjonowanych i skalowalna nakierowanych na klienta usługi, komunikujących się ze sobą za pośrednictwem standardowych protokołów z dobrze określonymi interfejsami.***

Omówiono dwa pierwsze punkty w poprzedniej sekcji, a teraz możemy rozwinięte i inne wyjaśnienia.

### <a name="written-in-any-programming-language-and-use-any-framework"></a>Napisane w dowolnym języku programowania i korzystaj z dowolnej platformy
Jako programiści będziemy swobodę wyboru języka lub struktury, która ma, w zależności od nasze umiejętności lub na potrzeby usługi. W niektórych usług może być wartość korzyści w zakresie wydajności przede wszystkim innego języka c++. W innych usługach łatwość zarządzanych Programowanie w języku C# lub Java może być najważniejsze. W niektórych przypadkach może być konieczne użycie biblioteki partnera, technologii magazynowania danych lub środki udostępnianie usługi dla klientów.

Po użytkownik wybrał technologii, możesz się zarządzanie operacyjne lub cykl życia i skalowania usługi.

### <a name="allows-code-and-state-to-be-independently-versioned-deployed-and-scaled"></a>Umożliwia kodu i stanie się oddzielnie wersjonowanych, wdrażanie i skalowanie
Jednak istnieje możliwość zapisu, mikrousługi, kod i opcjonalnie stan powinien niezależnie wdrażanie, uaktualnianie i skalowanie. To faktycznie trudniejsze problemów, aby rozwiązać, ponieważ jego sprowadza się do wybranych przez siebie technologii. Aby uzyskać opis skalowanie, jak partycja (lub fragmentu) kod i stan staje się wyzwaniem. Korzystając z kodem i stanem oddzielne technologii, co jest typowe już dziś, muszą być w stanie poradzić sobie ze skalowaniem, oba te skrypty wdrażania usługi mikrousług. Dotyczy to również sprawności i elastyczności, dlatego można przeprowadzić uaktualnienie niektórych mikrousługi bez konieczności aktualizacji wszystkich z nich jednocześnie.

Zwracanie do monolityczne i podejścia z zastosowaniem mikrousług, przez chwilę, na poniższym diagramie przedstawiono różnice w przypadku zastosowania podejścia do przechowywania stanu.

#### <a name="state-storage-between-application-styles"></a>Stan magazynu między style aplikacji
![Magazynu stanu platformy usługi Service Fabric][Image2]

***Podejścia monolitycznego po lewej stronie zawiera pojedynczą bazę danych i warstwy określonych technologii.***

***Podejście mikrousług po prawej stronie zawiera wykres wzajemnie połączonych mikrousług, których stan jest zwykle ograniczone do mikrousług i różne technologie są używane.***

W przypadku zastosowania podejścia monolitycznego zwykle aplikacja korzysta z pojedynczej bazy danych. Zaletą jest pojedynczej lokalizacji, co pozwala na łatwe do wdrożenia. Każdy składnik może mieć jednej tabeli do przechowywania jego stanu. Zespoły muszą ściśle oddzielić stanu, co jest trudne. Brak nieuchronnie temptations, aby dodać nową kolumnę do istniejącej tabeli odbiorcy, do sprzężenia między tabelami i tworzenie zależności w warstwie magazynu. Po tak się stanie, nie można skalować poszczególne składniki. 

W przypadku zastosowania podejścia mikrousług każda usługa zarządza i przechowuje swój własny stan. Każda usługa jest odpowiedzialna skalowanie w kodzie i stanu ze sobą, aby sprostać wymaganiom usługi. Wadą jest to, gdy trzeba utworzyć widoków lub kwerend danych Twojej aplikacji, należy do wykonywania zapytań w stan różnych sklepach. Zazwyczaj ten został rozwiązany przez oddzielne mikrousług, który tworzy widok na zbiór mikrousług. Jeśli musisz wykonać wiele spontanicznej zapytań na danych, każda mikrousługa należy rozważyć, zapisywanie danych do usługi magazynowania danych do analizy w trybie offline.

Przechowywanie wersji jest specyficzny dla wdrożonej wersji mikrousługi tak wiele, różne wersje wdrażanie i uruchamianie obok siebie. Przechowywanie wersji jest przeznaczona dla scenariuszy, w którym nowszą wersję mikrousługi zakończy się niepowodzeniem podczas uaktualniania i wymaga wrócić do wcześniejszej wersji. Innego scenariusza pod kątem przechowywania wersji wykonuje testowania A/B-style, gdzie różni użytkownicy środowisko różnych wersji usługi. Na przykład jest wspólne dla uaktualnienie mikrousług dla określonej grupy klientów, aby przetestować nową funkcję przed udostępnieniem jej więcej powszechnie. Po zarządzania cyklem życia mikrousług to teraz dotarliśmy do komunikacji między nimi.

### <a name="interacts-with-other-microservices-over-well-defined-interfaces-and-protocols"></a>Współdziała z innymi mikrousług za pośrednictwem dobrze zdefiniowanych interfejsów i protokołów
W tym temacie wymaga nieco uwagi w tym miejscu, ponieważ obszerne materiały dotyczące architektury zorientowanej na usługi, które zostały opublikowane w ciągu ostatnich 10 lat w tym artykule opisano wzorców komunikacyjnych. Ogólnie rzecz biorąc komunikacja usługi użyto metody REST przy użyciu protokołów HTTP i TCP i XML lub JSON jako format serializacji. Z perspektywy interfejsu to kwestia stosujących podejście do projektowania sieci web. Ale nic nie zatrzymuje z przy użyciu protokołów binarne lub własnych formatów danych. Należy przygotować osób trudniejsze godziny za pomocą mikrousługi, jeśli te protokoły i formatów nie są dostępne w sposób jawny.

### <a name="has-a-unique-name-url-used-to-resolve-its-location"></a>Ma unikatową nazwę (adres URL) używany do rozpoznawania lokalizacji
Należy pamiętać o tym, jak firma Microsoft nadal informujący o tym, czy podejścia z zastosowaniem mikrousług przypomina sieci web? Jak sieci web usługi mikrousług musi być adresy, wszędzie tam, gdzie jest uruchomiony. Jeśli myślisz o maszynach, który z nich działa określonego mikrousług rzeczy niedługo szybko. 

W ten sam sposób, że DNS jest rozpoznawana jako określonego adresu URL określonego komputera z mikrousług musi mieć unikatową nazwę, tak, aby jego bieżącej lokalizacji wykrywalne. Mikrousługi należy adresowalnych nazw, które były niezależnie od infrastruktury, które są uruchomione. Oznacza to, że istnieje interakcji między jak usługa zostanie wdrożona, i jak okaże się, ponieważ musi istnieć rejestr usług. Tak samo gdy maszyna nie powiedzie się, Usługa rejestru musi zorientować się, gdzie usługa jest obecnie uruchomiona. 

Ten sposób dotarliśmy do następnego tematu: odporności i spójności.

### <a name="remains-consistent-and-available-in-the-presence-of-failures"></a>Pozostają spójne i dostępne w razie awarii
Zajmowanie się nieoczekiwanych awarii jest jednym z najtrudniejszych problemów do rozwiązania, szczególnie w rozproszonym systemie. Większość kodu, który możemy napisać deweloperów jest obsługa wyjątków i jest to również gdzie najlepiej jest zużywany czas podczas testowania. Problem polega na bardziej skomplikowane niż pisanie kodu w celu obsługi błędów. Co się stanie, gdy maszyna, na którym jest uruchomione mikrousługa nie powiedzie się? Nie tylko potrzebne wykryć ten błąd mikrousługi (twardego problem samodzielnie), ale należy również coś o ponowne uruchomienie usługi mikrousług. 

Mikrousługi musi być odporny na awarie i uruchom ponownie często na innej maszynie ze względów dostępności. To również sprowadza się do stanu, który został zapisany w imieniu mikrousług, gdzie mikrousługi można odzyskać tego stanu z i czy mikrousług jest możliwe ponowne uruchomienie pomyślnie. Innymi słowy musi istnieć odporność w obliczeniowej (ten proces powoduje ponowne uruchomienie.), a także odporności w stanie lub danych (bez utraty danych i dane pozostają spójne).

Problemy odporność jest rozliczana w innych scenariuszach, takich jak kiedy zdarzają się błędy podczas uaktualniania aplikacji. Mikrousłudze oraz pracy z wdrożenia systemu, nie trzeba odzyskać. Potrzebny jest również zdecydować, czy może on nadal przejść od razu do nowszej wersji lub zamiast niego wrócić do poprzedniej wersji do utrzymania spójnego stanu. Pytania, takie jak czy maszyn wystarcza są dostępne do zachowania przenoszenie do przodu i sposób odzyskać poprzednie wersje mikrousługi należy wziąć pod uwagę. Wymaga to mikrousług emitowanie informacji o kondycji, aby można było podjąć te decyzje.

### <a name="reports-health-and-diagnostics"></a>Raporty Kondycja i Diagnostyka
Może wydawać się oczywiste, jest często pomijane, ale mikrousługi należy zgłosić, jego kondycja i Diagnostyka. W przeciwnym razie jest mały wgląd z perspektywy operacji. Korelowanie zdarzeń diagnostycznych na zestaw niezależnych usług i korzystające z niesymetryczności zegara komputera rozsądnie kolejność zdarzeń jest trudne. W taki sam sposób możesz korzystać z mikrousług za pośrednictwem protokołów uzgodnionego i formatów danych wynika istnieje potrzeba normalizacji w sposób kondycji i zdarzenia diagnostyczne, które ostatecznie znajdą się w magazynie zdarzeń do wykonywania zapytań i wyświetlania dziennika. W podejściu z mikrousług jest klucz, różne zespoły dojdą do Porozumienia w postaci pojedynczego logowania. Musi istnieć spójnego podejścia do wyświetlania zdarzeń diagnostycznych w aplikacji jako całości.

Kondycja różni się od diagnostyki. Kondycja jest o mikrousługach raportowania swojego bieżącego stanu, aby podjąć odpowiednie działania. Dobrym przykładem jest praca z mechanizmów uaktualnieniu i wdrożeniu w celu zapewnienia dostępności. Mimo że usługa może być aktualnie niepoprawny stan z powodu awarii procesu lub ponowne uruchomienie komputera, usługi może nadal działać. Ostatnią czynnością, jaką potrzebne jest zapewnienie to niższa, wykonując uaktualnienie. Najlepszym rozwiązaniem jest w celu badania lub poczekaj na mikrousługach odzyskać. Zdarzenia dotyczące kondycji z mikrousługi pomagają nam podejmowanie świadomych decyzji, a w efekcie pomagają stworzyć samonaprawiania usług.

## <a name="service-fabric-as-a-microservices-platform"></a>Usługa Service Fabric, jest to platforma mikrousług
Usługa Azure Service Fabric powstało z przejścia przez firmę Microsoft z dostarczania produktów, pole, które były zazwyczaj monolitycznych w stylu, dostarczaniu usług. Środowisko tworzenia i obsługi dużych usług, takich jak Azure SQL Database i Azure Cosmos DB w kształcie usługi Service Fabric. Platforma ewoluował wraz z upływem czasu coraz więcej usług przyjęty go. Co ważniejsze usługi Service Fabric musiał uruchomić nie tylko na platformie Azure, ale także w autonomicznych wdrożeniach systemu Windows Server.

***Celem usługi Service Fabric jest rozwiązywaniu trudnych problemów, kompilowania i uruchamiania usługi i wykorzystania zasobów infrastruktury, dzięki czemu zespoły mogą rozwiązać problemy biznesowe, przy użyciu podejścia mikrousług.***

Usługa Service Fabric udostępnia trzy obszary ułatwiające tworzenie aplikacji korzystających z podejścia mikrousług:

* Platforma, która zapewnia usługi systemowe na wdrażanie, uaktualnianie, wykryć, ponowne uruchomienie usługi nie powiodło się, Odkryj usługi, kierowanie komunikatów w postaci, zarządzanie stanem i monitorowanie kondycji. Te usługi systemu obowiązuje udostępniają wiele z właściwości mikrousług opisany wcześniej.
* Możliwość wdrażania aplikacji albo uruchomione w kontenerach lub procesach. Service Fabric to kontener i procesów programu orchestrator.
* Produktywność programowania interfejsów API, aby ułatwić Ci budowanie aplikacji zgodnie z mikrousług: [ASP.NET Core, elementów Reliable Actors oraz usług Reliable Services](service-fabric-choose-framework.md). Można wybrać żadnego kodu do kompilacji z mikrousług. Jednak te interfejsy API wprowadzić bardziej bezpośredni zadania i integrują się z platformą dokładniej. Na przykład w ten sposób możesz uzyskać informacje, kondycja i Diagnostyka lub możesz skorzystać z wbudowaną wysoką dostępność.

***Usługa Service Fabric jest niezależny, w jaki sposób opracować swoje usługi, a następnie można użyć dowolnej technologii. Jednak zapewnia wbudowane programowania interfejsów API, które ułatwiają tworzenie mikrousług.***

### <a name="migrating-existing-applications-to-service-fabric"></a>Migrowanie istniejących aplikacji do usługi Service Fabric
Metoda umożliwiająca usługi Service Fabric przy użyciu klucza jest ponowne używanie istniejącego kodu, które następnie mogą być zmodernizowane przy użyciu nowych mikrousług. Modernizacja aplikacji, składa się z pięciu etapów i można uruchomić i zatrzymać w dowolnym z etapów. Są to:

1) Zacznij od tradycyjnych aplikacji monolitycznej.  
2) Przenoszenie i Shift - hosta istniejący kod w usłudze Service Fabric za pomocą kontenerów lub plików wykonywalnych gościa.  
3) Modernizacja - nowych mikrousług dodany wraz z istniejącego kodu konteneryzowanych.  
4) Wprowadzaj innowacje — Podziel monolityczną na mikrousługi wyłącznie zgodnie z potrzebami.  
5) Przekształcane na mikrousługi — Przekształcanie istniejących aplikacji monolitycznej, czy tworzysz nowe aplikacje od podstaw.

![Migracja do Mikrousług][Image3]

Ważne jest, aby ponownie podkreślić, że możesz **uruchomić i zatrzymać w dowolnym z tych etapów**. Nie są konieczne postępu do kolejnego etapu. Teraz Przyjrzyjmy przykłady dla każdego z tych etapów.

**Lift- and -Shift** -dużej liczby firmy są podnoszenia i przesunięcie istniejących aplikacji monolitycznych w kontenerach dwóch powodów:

- Zmniejszenie kosztów z powodu konsolidacji i usunięcie istniejącego sprzętu lub uruchamianie aplikacji na zwiększenie gęstości. 
- Spójne wdrażanie kontrakt między środowiskami deweloperskim i operacji.

Obniżenie kosztów są zrozumiałe, a w ramach firmy Microsoft, dużej liczby istniejących aplikacji są trwa kontenerowych nimi po prostu, można zaoszczędzić miliony dolarów. Spójne wdrażanie jest trudniejsze do oceny, ale równie ważne. Oznacza to, że deweloperzy nadal może być swobodę wyboru technologii, która odpowiada, ale operacje będzie akceptować tylko jeden sposób, aby wdrażać te aplikacje i zarządzać. Eliminuje ona operacje z konieczności złożoność wielu różnych technologii lub wymuszenie deweloperzy mogą wybrać tylko niektóre z nich. Zasadniczo każda aplikacja jest konteneryzowana w obrazach niezależna wdrożenia.

W wielu organizacjach zatrzymać w tym miejscu. Mają zalety kontenery i Usługa Service Fabric udostępnia wszystkie funkcje zarządzania z wdrożenia uaktualnień, przechowywanie wersji, wycofywanie zmian, kondycji monitorowania itp.

**Modernizacja** — jest dodanie nowych usług wraz z istniejącego kodu konteneryzowanych. Jeśli zamierzasz pisanie nowego kodu, najlepiej chce wykonać proces na niewielkie etapy do ścieżki mikrousług. Może to dodawania nowego punktu końcowego interfejsu API REST lub nowej logiki biznesowej. W ten sposób, możesz rozpocząć tworzenie nowych mikrousług i praktyki opracowywania i wdrażania ich podróży.

**Wprowadzaj innowacje** — Pamiętaj tych oryginalnego zmieniających się potrzeb biznesowych na początku tego artykułu, które pobudzają podejście mikrousług? W tym etapu, który jest decyzja, są one wykonywane w aplikacji z bieżącego, a jeśli tak, muszę uruchomić dzieląc monolityczną aplikację na usługi lub innowacje. W tym przykładzie jest, gdy bazę danych jako kolejki worflow staje się wąskim gardłem przetwarzania. Jak liczba przepływów pracy żądań rośnie, pracy musi być dystrybuowane do skalowania. Dzięki tym określonym wystąpieniem aplikacji, która nie jest skalowanie, lub na potrzeby aktualizowane częściej, Podziel tę możliwość w mikrousługę i wprowadzania innowacji. 

**Przekształcone na mikrousługi** — jest to, gdzie aplikacja jest w pełni składa się z (lub rozłożona na) mikrousług. Aby uzyskać dostęp w tym miejscu, wprowadzono podróż mikrousług. Można uruchomić w tym miejscu, ale aby to zrobić bez mikrousług platformy, aby ułatwić jest znaczących inwestycji. 

### <a name="are-microservices-right-for-my-application"></a>Mikrousługi — jest odpowiednią dla mojej aplikacji?
Może. Firma Microsoft napotkał była, ponieważ coraz więcej zespołów w programie Microsoft rozpoczęła się do tworzenia w chmurze, ze względów biznesowych, wiele z nich rzędu milionów dolarów zalety biorąc podejście podobne do mikrousług. Bing, na przykład opracowuje mikrousług w wyszukiwaniu lata. Dla innych zespołów podejście mikrousług był nowy. Zespoły Ustaliliśmy, że zostały trudnych problemów rozwiązać poza ich podstawowych obszarów sile. Jest to, dlaczego usługi Service Fabric zdobyte rozwinięty jako technologia pozwala na tworzenie usług.

Celem usługi Service Fabric jest zmniejszenie złożoności tworzenia aplikacji przy użyciu podejścia z zastosowaniem mikrousług, tak aby nie trzeba go za pośrednictwem jako wiele kosztowne operacje przeprojektowania. Na początku są małe, skalowania w razie, wycofaniu usługi, dodać nowe i rozwój klientowi użycie jest podejście. Zdajemy sobie sprawę, że istnieją wiele problemów do można rozwiązać się mikrousług bardziej przystępne dla większości deweloperów. Kontenery i model programowania aktora są przykłady małe kroki opisane w tym kierunku, a firma Microsoft pewności, że więcej innowacje pojawią się ułatwić.
 
<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="next-steps"></a>Kolejne kroki
* [Przegląd terminologii usługi Service Fabric](service-fabric-technical-overview.md)
* [Mikrousługi: Rewolucja w aplikacjach obsługiwana przez chmurę](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/)

[Image1]: media/service-fabric-overview-microservices/monolithic-vs-micro.png
[Image2]: media/service-fabric-overview-microservices/statemonolithic-vs-micro.png
[Image3]: media/service-fabric-overview-microservices/microservices-migration.png
