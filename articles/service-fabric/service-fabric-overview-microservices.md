---
title: Wprowadzenie do mikrousług na platformie Azure | Dokumentacja firmy Microsoft
description: Omówienie tworzenia aplikacji w chmurze dzięki podejściu mikrousług jest ważna do tworzenia nowoczesnych aplikacji oraz jak usługi Azure Service Fabric udostępnia platformę można to osiągnąć.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: fae2be85-0ab4-4cd3-9d1f-e0d95fe1959b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/02/2017
ms.author: atsenthi
ms.openlocfilehash: 1e02e4fdf0dbe04d8756fc6355c6a9e414b27d2b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60719156"
---
# <a name="why-a-microservices-approach-to-building-applications"></a>Dlaczego mikrousługi podejście do tworzenia aplikacji?

Jako programiści nie ma nic nowego: w jaki sposób firma Microsoft rozważać wyprowadzenie aplikacji do części składowych. Zazwyczaj warstwowego rozwiązania jest pobierana z magazynu zaplecza, logika biznesowa warstwy środkowej i interfejsem frontonu użytkownika (UI). Co *ma* zmienione w ciągu ostatnich kilku lat jest, że, jako programiści, tworzymy aplikacji rozproszonych w chmurze.

Zmieniających się potrzeb biznesowych są:

* Usługa, która jest wbudowana i działa na dużą skalę, aby dotrzeć do klientów w nowych regionach geograficznych.
* Szybsze dostarczanie funkcji i możliwości, aby móc reagować na żądania klientów elastyczne.
* Wykorzystanie zasobów ulepszony, aby ograniczyć koszty.

Tych potrzeb biznesowych mają wpływ na *jak* możemy tworzyć aplikacje.

Aby uzyskać więcej informacji na temat podejścia platformy Azure do mikrousług odczytu [Mikrousług: Rewolucja w aplikacjach obsługiwana przez chmurę](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/).

## <a name="monolithic-vs-microservice-design-approach"></a>Monolityczne, a na etapie projektowania mikrousług

Rozwój aplikacji wraz z upływem czasu. Udane aplikacje rozwój dzięki przydatne dla osób. Aplikacje nie powiedzie nie ewolucji i po pewnym czasie są przestarzałe. Pytanie jest: Ile informacje dotyczące wymagań już dziś i co one będzie w przyszłości? Na przykład załóżmy, że możesz tworzyć aplikacji do raportowania dla działu. Masz pewność, że aplikacja ma zastosowanie tylko w zakresie swojej firmy i że raporty są krótkotrwałe. Wybór podejście różni się od, powiedz, kompilowanie to usługa, która dostarcza zawartość wideo dziesiątki milionów klientów.

Czasami pobraniem czegoś spory jako Weryfikacja koncepcji jest współczynnik opracowuje, gdy wiesz, że później przeprojektowany aplikacji. Istnieje mały punkt w nadmiernie inżynierii coś, który nigdy nie zostanie wykorzystany. Jest zwykle kompromis inżynierów. Z drugiej strony mówiąc firmy dotyczące tworzenia aplikacji dla chmury, oczekiwania jest wskaźnik wzrostu i użycia. Problem polega na rozwój i skalowanie są nieprzewidywalne. Chcielibyśmy można było prototypu szybko codzienne również jesteśmy na ścieżce do czynienia z sukcesem w przyszłości. Jest to metoda startupu: kompilowanie, mierzenie, Dowiedz się i iteracji.

Podczas era klient serwer firma Microsoft pielęgnowane skoncentrowanie się na tworzeniu aplikacji warstwowych przy użyciu określonych technologii w przypadku każdej warstwy. Termin *monolityczne* aplikacji pojawiło się w przypadku tych metod. Interfejsy rozszerzenia się między warstwami i bardziej ściśle sprzężonych projektu zostało użyte między składnikami w każdej warstwie. Deweloperzy zaprojektowane i uwzględniona klas, które zostały skompilowane w bibliotekach i powiązane w kilku plików wykonywalnych i bibliotek DLL. 

Istnieją korzyści z takiego podejścia monolityczny projekt. Jest często łatwiejsze do projektowania, a ma szybszy wywołań między składnikami, ponieważ te wywołania są często za pośrednictwem komunikacji międzyprocesowej (IPC). Ponadto wszyscy testy jednego produktu jest zwykle więcej osób zasobów wydajne. Minusem jest, że istnieje ścisłego sprzężenia między warstwami warstwowe, i nie można skalować poszczególne składniki. Jeśli zachodzi potrzeba wykonania uaktualnienia i poprawki, trzeba czekać na zakończenie testów przez inne osoby. Trudno bardziej efektywnie.

Mikrousługi rozwiązać te wad i więcej ściśle dopasować wymagań biznesowych, ale mają również korzyści i zobowiązań. Korzyści wynikające z mikrousług to, że każdy z nich zazwyczaj hermetyzuje prostsze funkcji biznesowych, które można skalować w górę lub w dół, testowanie, wdrażanie i niezależnie zarządzać. Co ważne korzyści wynikające z podejścia z zastosowaniem mikrousług jest to, czy zespoły są oparte na więcej scenariuszy biznesowych niż przez technologię — co sprzyja warstwowego rozwiązania. W praktyce mniejszych zespołach opracowywanie mikrousług, oparta na scenariuszu klienta i używać dowolnej technologii, które postanowili.

Innymi słowy organizacja nie musi standaryzacji tech do obsługi aplikacji mikrousług. Poszczególne zespoły, własnych usług można zrobić, co ma sens dla nich oparty na wiedzy zespołu lub co to jest najbardziej odpowiednie do rozwiązania problemu. W praktyce zbiór zalecanych technologii, takich jak określonego NoSQL przechowywania lub platforma aplikacji sieci web, jest preferowane.

Wadą mikrousług jest dostępna w zajmujących się bardziej złożonych wdrożeń i przechowywanie wersji i zarządzanie nimi zwiększonej liczby osobne jednostki. Ruch sieciowy między mikrousługi zwiększa oraz odpowiednie opóźnienia sieci. Wiele z dużą liczbą, precyzyjnych usług są przepis na okropnej wydajności. Bez narzędzi, aby ułatwić przeglądanie te zależności, trudno jest "zobacz" całego systemu.

Standardy wprowadzić podejścia z zastosowaniem mikrousług działają przez wyrażenie zgody na temat sposobu komunikowania się i jest odporny na błędy czynności, należy przy użyciu usługi, a nie sztywne umów. Należy zdefiniować te umowy na początku projektu, ponieważ aktualizacja usług niezależnie od siebie nawzajem. Inny opis ukuł do projektowania dzięki podejściu mikrousług jest "szczegółowych zorientowane na usługę architektura (SOA)."

***W najprostszym podejście do projektowania mikrousług jest o odłączony federacji usług, niezależnie od zmiany do każdego i uzgodnione standardów komunikacji.***

Jak są produkowane więcej aplikacji w chmurze, osoby odkryli, to lepiej długoterminową to dekompozycji ogólną aplikacji do usługi niezależne, scenariusz skoncentrowany.

## <a name="comparison-between-application-development-approaches"></a>Porównanie podejścia do projektowania aplikacji

![Tworzenie aplikacji platformy Service Fabric][Image1]

1) Monolityczną aplikację zawiera funkcje specyficzne dla domeny i zazwyczaj jest dzielona przez funkcjonalności warstwy sieci web, praca i danych.

2) Skalowanie monolitycznych aplikacji przez Sklonowanie go na wiele serwerów/maszyn wirtualnych/kontenerów.

3) Aplikacja mikrousług oddziela funkcje na osobne usługi mniejsze.

4) Skaluje podejście mikrousług na zewnątrz, wdrażając każdej usługi niezależne, tworzenia wystąpień tych usług dla serwerów/maszyn wirtualnych/kontenerów.

Projektowanie za pomocą mikrousługi podejście nie jest panaceum na wszystkie projekty, ale jej dostosowanie z celów biznesowych, opisanych wcześniej. Począwszy od podejścia monolitycznego może być akceptowalne, jeśli wiesz, że użytkownik będzie miał możliwość przerabiać kod później na mikrousługach. Zazwyczaj zaczynają się od aplikacji monolitycznej i powoli Podziel je w etapach, począwszy od obszarów funkcjonalnych, które muszą być bardziej skalowalna lub agile.

Podejścia z zastosowaniem mikrousług oznacza to, czy tworzysz aplikację wiele małych usług. Usługi są uruchomione w kontenerach, które są wdrażane w klastrze maszyn. Mniejszych zespołów projektowania usługi, która koncentruje się na scenariuszu niezależnie przetestować, wersji, wdrażanie i skalowanie poszczególnych usług, dzięki czemu można rozwijać całej aplikacji.

## <a name="what-is-a-microservice"></a>Co to jest mikrousługi?

Istnieją różne definicje mikrousług. Jednak większość następujące właściwości mikrousługi są powszechnie uzgodnionych:

* Hermetyzuj scenariuszy biznesowych lub klienta. Co to jest problem, który rozwiązywanych?
* Opracowane przez niewielkiemu zespołowi inżynierów.
* Napisane w dowolnym języku programowania i korzystaj z dowolnej platformy.
* Składają się z kodu i (opcjonalnie) stanu, które są oddzielnie wersjonowanych, wdrażanie i skalowanie.
* Interakcja z innymi mikrousług za pośrednictwem dobrze zdefiniowanych interfejsów i protokołów.
* Mają unikatowe nazwy (URL) używany do rozpoznawania ich lokalizacji.
* Pozostają spójne i dostępne w razie awarii.

Podsumowanie:

***Aplikacje Mikrousług składają się z małych, oddzielnie wersjonowanych i skalowalna nakierowanych na klienta usługi, komunikujących się ze sobą za pośrednictwem standardowych protokołów z dobrze określonymi interfejsami.***

### <a name="written-in-any-programming-language-and-use-any-framework"></a>Napisane w dowolnym języku programowania i korzystaj z dowolnej platformy

Jako programiści chcemy mieć swobodę wyboru języka lub struktury, która ma, w zależności od nasze umiejętności lub na potrzeby usługi. W niektórych usług może być wartość korzyści w zakresie wydajności przede wszystkim innego języka c++. W innych usługach łatwość zarządzanych Programowanie w języku C# lub Java może być najważniejsze. W niektórych przypadkach może być konieczne użycie biblioteki partnera, technologii magazynowania danych lub środki udostępnianie usługi dla klientów.

Po użytkownik wybrał technologii, możesz się zarządzanie operacyjne lub cykl życia i skalowania usługi.

### <a name="allows-code-and-state-to-be-independently-versioned-deployed-and-scaled"></a>Umożliwia kodu i stanie się oddzielnie wersjonowanych, wdrażanie i skalowanie

Jednak istnieje możliwość zapisu mikrousługi, kod i, opcjonalnie stanu, należy niezależnie wdrożenia, uaktualnienie i skalowanie. Jest to jeden z problemów trudniejsze do rozwiązania, ponieważ jego sprowadza się do wybranych przez siebie technologii. Aby uzyskać opis skalowanie, jak partycja (lub fragmentu) kod i stan staje się wyzwaniem. Korzystając z kodem i stanem oddzielne technologii, co jest typowe już dziś, skrypty wdrażania usługi mikrousług muszą mieć możliwość skalowania oba te. Dotyczy to również sprawności i elastyczności, dlatego można przeprowadzić uaktualnienie niektórych mikrousługi bez konieczności aktualizacji wszystkich z nich jednocześnie.

Zwracanie do monolityczne i podejścia z zastosowaniem mikrousług, przez chwilę, na poniższym diagramie przedstawiono różnice w przypadku zastosowania podejścia do przechowywania stanu.

#### <a name="state-storage-between-application-styles"></a>Stan magazynu między style aplikacji

![Magazynu stanu platformy usługi Service Fabric][Image2]

***Podejścia monolitycznego po lewej stronie zawiera pojedynczą bazę danych i warstwy określonych technologii.***

***Podejście mikrousług po prawej stronie zawiera wykres wzajemnie połączonych mikrousług, których stan jest zwykle ograniczone do mikrousług i różne technologie są używane.***

W przypadku zastosowania podejścia monolitycznego aplikacja zwykle korzysta z pojedynczej bazy danych. Zaletą jest pojedynczej lokalizacji, co pozwala na łatwe do wdrożenia. Każdy składnik może mieć jednej tabeli do przechowywania jego stanu. Zespoły muszą ściśle oddzielić stanu, co jest trudne. Brak nieuchronnie temptations, aby dodać nową kolumnę do istniejącej tabeli odbiorcy, do sprzężenia między tabelami i tworzenie zależności w warstwie magazynu. Po tak się stanie, nie można skalować poszczególne składniki.

W przypadku zastosowania podejścia mikrousług każda usługa zarządza i przechowuje swój własny stan. Każda usługa jest odpowiedzialna skalowanie w kodzie i stanu ze sobą, aby sprostać wymaganiom usługi. Wadą jest to, gdy trzeba utworzyć widoków lub kwerend danych Twojej aplikacji, należy do wykonywania zapytań w wielu sklepach stanu. Zazwyczaj ten został rozwiązany przez oddzielne mikrousług, który tworzy widok na zbiór mikrousług. Jeśli musisz wykonać wiele spontanicznej zapytań na danych, każda mikrousługa należy rozważyć, zapisywanie danych do usługi magazynowania danych do analizy w trybie offline.

Mikrousługi są wersjonowane i jest możliwe, może być uruchomiona side-by-side różne wersje mikrousługi. Nowsza wersja mikrousługi może zakończyć się niepowodzeniem podczas uaktualniania i trzeba go z powrotem obniżyć do wcześniejszej wersji. Przechowywanie wersji jest również przydatne w przypadku testowania A/B-style, gdzie różni użytkownicy środowisko różnych wersji usługi. Na przykład jest wspólne dla uaktualnienie mikrousług dla określonej grupy klientów, aby przetestować nową funkcję przed udostępnieniem jej więcej powszechnie.

### <a name="interacts-with-other-microservices-over-well-defined-interfaces-and-protocols"></a>Współdziała z innymi mikrousług za pośrednictwem dobrze zdefiniowanych interfejsów i protokołów

Rozbudowane materiały dotyczące architektury zorientowanej na usługi został opublikowany w ciągu ostatnich 10 lat opisujące wzorców komunikacyjnych. Ogólnie rzecz biorąc komunikacja usługi użyto metody REST przy użyciu protokołów HTTP i TCP, a XML lub JSON jako format serializacji. Z perspektywy interfejsu to kwestia stosujących podejście do projektowania sieci web. Ale nic nie zatrzymuje z przy użyciu protokołów binarne lub własnych formatów danych. Należy przygotować osób trudniejsze godziny za pomocą mikrousługi, jeśli te protokoły i formatów nie są dostępne w sposób jawny.

### <a name="has-a-unique-name-url-used-to-resolve-its-location"></a>Ma unikatową nazwę (adres URL) używany do rozpoznawania lokalizacji

Twoje mikrousług należy adresowalnych wszędzie tam, gdzie jest uruchomiony. Jeśli myślisz o maszynach, który z nich działa określonego mikrousług rzeczy niedługo szybko. 

W taki sam sposób, że DNS jest rozpoznawana jako określonego adresu URL określonego komputera z mikrousług musi unikatową nazwę, aby odnaleźć jego bieżącej lokalizacji. Mikrousługi muszą adresowalnych nazw, które są niezależne od infrastruktury, które są uruchomione. Oznacza to, że istnieje interakcji między jak usługa zostanie wdrożona, i jak okaże się, ponieważ musi istnieć rejestr usług. W przypadku awarii komputera Usługa rejestru musisz poinformować, gdy usługa została przeniesiona do.

### <a name="remains-consistent-and-available-in-the-presence-of-failures"></a>Pozostają spójne i dostępne w razie awarii

Zajmowanie się nieoczekiwanych awarii jest jednym z najtrudniejszych problemów do rozwiązania, szczególnie w rozproszonym systemie. Większość kodu, który możemy napisać deweloperów jest obsługa wyjątków i jest to również gdzie najlepiej jest zużywany czas podczas testowania. Problem polega na bardziej skomplikowane niż pisanie kodu w celu obsługi błędów. Co się stanie, gdy maszyna, na którym jest uruchomione mikrousługa nie powiedzie się? Nie tylko potrzebne na wykrycie awarii (twardego problem samodzielnie), ale również konieczne ponowne uruchomienie usługi mikrousług.

Ze względu na dostępność mikrousługi musi być odporny na awarie i aby ponownie uruchomić na innym komputerze. Oprócz uruchomiony kod są odporne na błędy nie powinny być utraty danych, a dane muszą zachować spójność.

Odporność jest trudny do osiągnięcia w przypadku niepowodzenia podczas uaktualniania aplikacji. Mikrousłudze oraz pracy z wdrożenia systemu, nie trzeba odzyskać. Należy zdecydować, czy może on nadal przejść od razu do nowszej wersji lub zamiast niego wrócić do poprzedniej wersji w celu utrzymania spójnego stanu. Pytania, takie jak czy maszyn wystarcza są dostępne do zachowania przenoszenie do przodu i sposób odzyskać poprzednie wersje mikrousługi należy wziąć pod uwagę. Wymaga to mikrousług emitowanie informacji o kondycji na podejmowanie tych decyzji.

### <a name="reports-health-and-diagnostics"></a>Raporty Kondycja i Diagnostyka

Może wydawać się oczywiste, jest często pomijane, ale mikrousługi należy zgłosić, jego kondycja i Diagnostyka. W przeciwnym razie jest mały wgląd w jego kondycji z perspektywy operacji. Korelowanie zdarzeń diagnostycznych na zestaw niezależnych usług i rozwiązywania problemów związanych z niesymetryczności zegara komputera rozsądnie kolejności zdarzeń, może być trudne. W ten sam sposób, że możesz korzystać z mikrousług za pośrednictwem protokołów uzgodnionego i formatów danych musisz standaryzacji jak kondycji i zdarzenia diagnostyczne, które będzie ostatecznie znajdą się w magazynie zdarzeń do wykonywania zapytań i wyświetlania dziennika. W podejściu z mikrousług go ma klucza różne zespoły dojdą do Porozumienia w postaci pojedynczego logowania. Musi istnieć spójnego podejścia do wyświetlania zdarzeń diagnostycznych w aplikacji jako całości.

Kondycja różni się od diagnostyki. Kondycja jest o mikrousługach raportowania swojego bieżącego stanu, aby podjąć odpowiednie działania. Dobrym przykładem jest praca z mechanizmów uaktualnieniu i wdrożeniu w celu zapewnienia dostępności. Mimo że usługa może być aktualnie niepoprawny stan z powodu awarii procesu lub ponowne uruchomienie komputera, usługi może nadal działać. Ostatnią czynnością, jaką potrzebne jest zapewnienie to niższa, wykonując uaktualnienie. Najlepszym rozwiązaniem jest w celu badania lub poczekaj na mikrousługach odzyskać. Zdarzenia dotyczące kondycji z mikrousługi pomagają nam podejmowanie świadomych decyzji, a w efekcie pomagają stworzyć samonaprawiania usług.

## <a name="service-fabric-as-a-microservices-platform"></a>Usługa Service Fabric, jest to platforma mikrousług

Usługa Azure Service Fabric powstało podczas Microsoft przeszła ze dostarczanie spakowany produktów, które były zazwyczaj monolitycznych w stylu, dostarczaniu usług. Środowisko tworzenia i obsługi dużych usług, takich jak Azure SQL Database i Azure Cosmos DB w kształcie usługi Service Fabric. Platforma ewoluował wraz z upływem czasu coraz więcej usług przyjęty go. Usługa Service Fabric musiał uruchomić nie tylko na platformie Azure, ale także w autonomicznych wdrożeniach systemu Windows Server.

***Celem usługi Service Fabric jest rozwiązywaniu trudnych problemów, kompilowania i uruchamiania usługi i wykorzystania zasobów infrastruktury, dzięki czemu zespoły mogą rozwiązać problemy biznesowe, przy użyciu podejścia mikrousług.***

Usługa Service Fabric pomaga w tworzeniu aplikacji, które używają podejście mikrousług, zapewniając:

* Platforma, która zapewnia usługi systemowe na wdrażanie, uaktualnianie, wykryć, ponowne uruchomienie usługi nie powiodło się, Odkryj usługi, kierowanie komunikatów w postaci, zarządzanie stanem i monitorowanie kondycji.
* Możliwość wdrażania aplikacji albo uruchomione w kontenerach lub procesach. Service Fabric to kontener i procesów programu orchestrator.
* Produktywność programowania interfejsy API, aby ułatwić Ci budowanie aplikacji zgodnie z mikrousług: [ASP.NET Core, elementów Reliable Actors oraz usług Reliable Services](service-fabric-choose-framework.md). Na przykład możesz uzyskać informacje, kondycja i Diagnostyka lub możesz skorzystać z wbudowaną wysoką dostępność.

***Usługa Service Fabric jest niezależny, w jaki sposób opracować swoje usługi, a następnie można użyć dowolnej technologii. Jednak zapewnia wbudowane programowania interfejsów API, które ułatwiają tworzenie mikrousług.***

### <a name="migrating-existing-applications-to-service-fabric"></a>Migrowanie istniejących aplikacji do usługi Service Fabric

Usługa Service Fabric umożliwia ponowne używanie istniejącego kodu, które następnie mogą być zmodernizowane przy użyciu nowych mikrousług. Modernizacja aplikacji, składa się z pięciu etapów i można uruchomić i zatrzymać w dowolnym z etapów. Są to:

1) Zacznij od tradycyjnych aplikacji monolitycznej.  
2) Przenoszenie i Shift - hosta istniejący kod w usłudze Service Fabric za pomocą kontenerów lub plików wykonywalnych gościa.  
3) Modernizacja - nowych mikrousług dodany wraz z istniejącego kodu konteneryzowanych.  
4) Wprowadzaj innowacje — Podziel monolityczną na mikrousługi wyłącznie zgodnie z potrzebami.  
5) Przekształcane na mikrousługi — Przekształcanie istniejących aplikacji monolitycznej, czy tworzysz nowe aplikacje od podstaw.

![Migracja do Mikrousług][Image3]

Ważne jest, aby ponownie podkreślić, że możesz **uruchomić i zatrzymać w dowolnym z tych etapów**. Nie są konieczne postępu do kolejnego etapu. Teraz Przyjrzyjmy przykłady dla każdego z tych etapów.

**Lift- and -Shift**  
Duża liczba przedsiębiorstw są podnoszenia i przesunięcie istniejących aplikacji monolitycznych w kontenerach dwóch powodów:

- Zmniejszenie kosztów z powodu konsolidacji i usunięcie istniejącego sprzętu lub uruchamianie aplikacji na zwiększenie gęstości.
- Spójne wdrażanie kontrakt między środowiskami deweloperskim i operacji.

Obniżenie kosztów są zrozumiałe, a w ramach firmy Microsoft, dużej liczby istniejących aplikacji są trwa kontenerowych nimi po prostu, można zaoszczędzić miliony dolarów. Spójne wdrażanie jest trudniejsze do oceny, ale równie ważne. Oznacza to, że deweloperzy nadal może być swobodę wyboru technologii, która odpowiada, ale operacje będzie akceptować tylko jeden sposób, aby wdrażać te aplikacje i zarządzać. Eliminuje ona operacje z konieczności złożoność wielu różnych technologii lub wymuszenie deweloperzy mogą wybrać tylko niektóre z nich. Zasadniczo każda aplikacja jest konteneryzowana w obrazach niezależna wdrożenia.

W wielu organizacjach zatrzymać w tym miejscu. Mają zalety kontenery i Usługa Service Fabric udostępnia wszystkie funkcje zarządzania z wdrożenia uaktualnień, przechowywanie wersji, wycofywanie zmian, kondycji monitorowania itp.

**Modernizacja**  
Dodanie nowych usług wraz z istniejącą kontenerowych nimi kodu. Jeśli zamierzasz pisanie nowego kodu, najlepiej chce wykonać proces na niewielkie etapy do ścieżki mikrousług. Może to dodawania nowego punktu końcowego interfejsu API REST lub nowej logiki biznesowej. W ten sposób, możesz rozpocząć tworzenie nowych mikrousług i praktyki opracowywania i wdrażania ich podróży.

**Wprowadzanie innowacji**  
To podejście mikrousług jest przystosowana do zmieniających się potrzeb biznesowych. Na tym etapie decyzja jest, czy należy uruchomić, dzieląc monolityczną aplikację na usługi lub innowacje. W tym przykładzie jest, gdy bazę danych jako kolejkę przepływu pracy staje się wąskim gardłem przetwarzania. Jak liczba przepływów pracy żądań rośnie, pracy musi być dystrybuowane do skalowania. Dla tego określonego elementu aplikacji, która nie jest skalowany lub wymagający będą aktualizowane częściej podzielić tę możliwość mikrousługi i wprowadzania innowacji.

**Przekształcone w mikrousługach**  
Jest to, gdzie aplikacja jest w pełni składa się z (lub rozłożona na) mikrousług. Aby uzyskać dostęp w tym miejscu, wprowadzono podróż mikrousług. Można uruchomić w tym miejscu, ale aby to zrobić bez mikrousług platformy, aby ułatwić jest znaczących inwestycji. 

### <a name="are-microservices-right-for-my-application"></a>Mikrousługi — jest odpowiednią dla mojej aplikacji?

Może. Firma Microsoft napotkał była, ponieważ coraz więcej zespołów w programie Microsoft rozpoczęła się do tworzenia w chmurze, ze względów biznesowych, wiele z nich rzędu milionów dolarów zalety biorąc podejście podobne do mikrousług. Bing, na przykład opracowuje przy użyciu mikrousług lata. Dla innych zespołów podejście mikrousług był nowy. Zespoły Ustaliliśmy, że zostały trudnych problemów rozwiązać poza ich podstawowych obszarów sile. Jest to, dlaczego usługi Service Fabric zdobyte rozwinięty jako technologia pozwala na tworzenie usług.

Celem usługi Service Fabric jest zmniejszenie złożoności tworzenia aplikacji mikrousług, tak aby nie trzeba przejść przez operacje przeprojektowania kosztownych tyle. Na początku są małe, skalowania w razie, wycofaniu usługi, dodać nowe i ewoluują wraz z użycia przez klientów. Zdajemy sobie sprawę, że istnieją wiele problemów do można rozwiązać się mikrousług bardziej przystępne dla większości deweloperów. Kontenery i model programowania aktora są przykłady małe kroki opisane w tym kierunku, a firma Microsoft pewności, że więcej innowacje pojawią się ułatwić.
 
<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="next-steps"></a>Kolejne kroki

* [Przegląd terminologii usługi Service Fabric](service-fabric-technical-overview.md)
* [Mikrousługi: Rewolucja w aplikacjach obsługiwana przez chmurę](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/)

[Image1]: media/service-fabric-overview-microservices/monolithic-vs-micro.png
[Image2]: media/service-fabric-overview-microservices/statemonolithic-vs-micro.png
[Image3]: media/service-fabric-overview-microservices/microservices-migration.png
