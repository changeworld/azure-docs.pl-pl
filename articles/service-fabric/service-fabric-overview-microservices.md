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
ms.date: 06/18/2019
ms.author: atsenthi
ms.openlocfilehash: 5bcb52165c7cae18b807eff03c80b51eae8e2717
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67204806"
---
# <a name="why-use-a-microservices-approach-to-building-applications"></a>Dlaczego warto korzystać z podejścia mikrousług do tworzenia aplikacji?

Dla deweloperów uwzględniając aplikacji do części składowych to nic nowego. Zazwyczaj podejście warstwowe wykorzystane i dysponują magazynu zaplecza, logika biznesowa warstwy środkowej i interfejsem frontonu użytkownika (UI). Co *ma* zmienione w ciągu ostatnich kilku lat jest deweloperzy tworzą aplikacji rozproszonych w chmurze.

Poniżej przedstawiono niektóre zmieniających się potrzeb biznesowych:

* Usługa, która ma utworzone i obsługiwane w dużej skali, aby dotrzeć do klientów w nowych regionach geograficznych.
* Szybsze dostarczanie funkcji i możliwości, aby odpowiadać na żądania klientów elastyczne.
* Wykorzystanie zasobów ulepszony, aby ograniczyć koszty.

Tych potrzeb biznesowych mają wpływ na *jak* możemy tworzyć aplikacje.

Aby uzyskać więcej informacji na temat podejścia platformy Azure do mikrousług, zobacz [Mikrousług: Rewolucja w aplikacjach obsługiwana przez chmurę](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/).

## <a name="monolithic-vs-microservices-design-approach"></a>Monolityczne, a na etapie projektowania mikrousług

Rozwój aplikacji wraz z upływem czasu. Udane aplikacje rozwój dzięki przydatne dla osób. Aplikacje nie powiedzie się rozwijać, nie i po pewnym czasie są przestarzałe. Oto pytanie: jak wiedzę na temat wymagań dotyczących już dziś i jakie będą w przyszłości? Załóżmy na przykład, że w przypadku tworzenia aplikacji do raportowania dla działu w firmie. Jesteś się, że aplikacja ma zastosowanie tylko w zakresie swojej firmy i że raportów nie będzie aktualizowana długie. Daną metodę będzie inny niż, powiedz, kompilowanie to usługa, która dostarcza zawartość wideo dziesiątki milionów klientów.

Czasami pobraniem czegoś spory jako Weryfikacja koncepcji jest współczynnik jazdy. Wiesz, że aplikacja może przeprojektowany w dalszej części. Istnieje mały punkt w nadmiernie inżynierii coś, który nigdy nie zostanie wykorzystany. Z drugiej strony gdy firmom tworzenie rozwiązań dla chmury, oczekiwania jest wskaźnik wzrostu i użycia. Rozwój i skalowanie są nieprzewidywalne. Chcemy prototypu szybko podczas wiedząc, że jesteśmy na ścieżkę, która może obsłużyć przyszłych sukcesów. Jest to metoda startupu: kompilowanie, mierzenie, Dowiedz się i iteracji.

Podczas era klient/serwer firma Microsoft pielęgnowane skoncentrowanie się na tworzeniu aplikacji warstwowych przy użyciu określonych technologii w przypadku każdej warstwy. Termin *monolityczne* aplikacji pojawiło się do opisania tych metod. Interfejsy rozszerzenia się między warstwami i bardziej ściśle sprzężonych projektu zostało użyte między składnikami w każdej warstwie. Deweloperzy zaprojektowane i uwzględniona klas, które zostały skompilowane w bibliotekach i powiązane w kilku plików wykonywalnych i bibliotek DLL.

Istnieją korzyści podejścia monolityczny projekt. Aplikacje monolityczne są często łatwiejsze do projektowania i połączeń między składnikami są szybsze, ponieważ te wywołania są często za pośrednictwem komunikacji międzyprocesowej (IPC). Ponadto wszyscy testy jednego produktu, który jest zwykle bardziej efektywne wykorzystywanie zasobów ludzkich. Minusem jest, że istnieje ścisłego sprzężenia między warstwami warstwowe, i nie można skalować poszczególne składniki. Należy wykonać uaktualnienia i poprawki, trzeba czekać na zakończenie testów przez inne osoby. Trudniej jest elastyczność.

Mikrousługi adresu tych wad i więcej ściśle dopasować wymagań biznesowych. Jednak mają one również korzyści i zobowiązań. Korzyści wynikające z mikrousług to, że każdy z nich zazwyczaj hermetyzuje prostsze funkcji biznesowych, które można skalować w górę lub w dół, testowanie, wdrażanie i niezależnie zarządzać. Co ważne korzyści wynikające z podejścia mikrousług jest to, czy zespoły są oparte na więcej różnych scenariuszy biznesowych niż przez technologię. Mniejszych zespołach twórz mikrousługi oparta na scenariuszu klienta i użycia dowolnej technologii, które mają być użyte.

Innymi słowy organizacja nie musi standaryzacji tech do obsługi aplikacji mikrousług. Poszczególne zespoły, własnych usług można zrobić, co ma sens dla nich oparty na wiedzy zespołu lub co to jest najbardziej odpowiednie do rozwiązania problemu. W praktyce zbiór zalecanych technologii, takich jak określonego magazynu NoSQL lub platforma aplikacji sieci web, jest preferowane.

Wadą mikrousług jest, że trzeba zarządzać bardziej osobne jednostki i bardziej złożonych wdrożeń i przechowywania wersji. Ruch sieciowy między mikrousługi zwiększa jak odpowiednie opóźnienia sieci. Wiele z dużą liczbą, precyzyjnych usług może spowodować okropnej wydajności. Bez narzędzi, aby wyświetlić te zależności jest trudne do zobaczenia całego systemu.

Standardy uczynienia mikrousług działają przez określenie sposobu komunikowania się i tolerować tylko elementy, które są potrzebne przy użyciu usługi, a nie sztywne umów. Należy zdefiniować te umowy na początku projektu, ponieważ aktualizacja usług niezależnie od siebie nawzajem. Inny opis ukuł do projektowania dzięki podejściu mikrousług jest "szczegółowych zorientowane na usługę architektura (SOA)."

***W najprostszym podejście do projektowania mikrousług jest o odłączony federacji usług, niezależnie od zmiany do każdego i standardów uzgodnionego komunikacji.***

Ponieważ coraz więcej aplikacji w chmurze są tworzone, osób odkryli, to lepiej długoterminową to dekompozycji cała aplikacja niezależnego i scenariusz skoncentrowany usług.

## <a name="comparison-between-application-development-approaches"></a>Porównanie podejścia do projektowania aplikacji

![Tworzenie aplikacji platformy Service Fabric][Image1]

1) Aplikacji monolitycznej zawiera funkcje specyficzne dla domeny i zazwyczaj jest podzielony na funkcjonalności warstwy sieci web, biznesowych i danych.

2) Skalowanie aplikacji monolitycznej przez Sklonowanie go na wiele serwerów/maszyn wirtualnych/kontenerów.

3) Aplikacja mikrousług oddziela funkcje na osobne usługi mniejsze.

4) Skaluje podejście mikrousług na zewnątrz, wdrażając każdej usługi niezależne, tworzenia wystąpień tych usług dla serwerów/maszyn wirtualnych/kontenerów.

Projektowanie z mikrousług podejście jest odpowiednie dla wszystkich projektów, ale jej dostosowanie z celów biznesowych, opisanych wcześniej. Począwszy od podejścia monolitycznego może mieć sens, jeśli wiesz, że będziesz mieć możliwość przerabiać kod później na mikrousługach. Zazwyczaj zaczynają się od aplikacji monolitycznej i powoli Podziel je w etapach, począwszy od obszarów funkcjonalnych, które muszą być bardziej skalowalna lub agile.

Korzystając z podejścia mikrousług, tworzysz aplikację wiele małych usług. Usługi te działają w kontenerach, które są wdrażane w klastrze maszyn. Mniejszych zespołów projektowania usługi, która koncentruje się na scenariuszu niezależnie przetestować, wersji, wdrażanie i skalowanie poszczególnych usług, dzięki czemu można rozwijać całej aplikacji.

## <a name="what-is-a-microservice"></a>Co to jest mikrousługi?

Istnieją różne definicje mikrousług. Jednak większość z tych cech mikrousługi są powszechnie akceptowane:

* Hermetyzuj scenariuszy biznesowych lub klienta. Jaki problem to rozwiązania?
* Opracowane przez niewielkiemu zespołowi inżynierów.
* Napisane w dowolnym języku programowania za pomocą dowolnej platformy.
* Składają się z kodu, a opcjonalnie stanu, które są oddzielnie wersjonowanych, wdrażanie i skalowanie.
* Interakcja z innymi mikrousług za pośrednictwem dobrze zdefiniowanych interfejsów i protokołów.
* Mają unikatowe nazwy (URL), które są używane do rozpoznawania ich lokalizacji.
* Pozostają spójne i dostępne w razie awarii.

Do podsumowania który:

***Aplikacje Mikrousług składają się z małych, oddzielnie wersjonowanych i skalowalna nakierowanych na klienta usługi, komunikujących się ze sobą za pośrednictwem standardowych protokołów z dobrze określonymi interfejsami.***

### <a name="written-in-any-programming-language-using-any-framework"></a>Napisane w dowolnym języku programowania za pomocą dowolnej platformy

Jako programiści chcemy mieć swobodę wyboru języka lub struktury, w zależności od potrzeb, usługi, którą tworzymy i nasze umiejętności. W przypadku niektórych usług może być wartość korzyści dotyczących wydajności C++ nad czymkolwiek. Dla innych osób, łatwość tworzenia zarządzanego, który można pobrać z C# lub Java może być niezwykle ważne. W niektórych przypadkach może być konieczne użycie biblioteki partnera, technologii magazynowania danych lub metody do udostępniania usługi klientom.

Po dokonaniu wyboru technologii, należy wziąć pod uwagę operacyjnej lub cyklu życia zarządzania i skalowania usługi.

### <a name="allows-code-and-state-to-be-independently-versioned-deployed-and-scaled"></a>Umożliwia kodu i stanie się oddzielnie wersjonowanych, wdrażanie i skalowanie

Niezależnie od tego, jak pisać mikrousługi, kodu i, opcjonalnie, stan powinien niezależnie wdrażanie, uaktualnianie i skalowanie. Ten problem jest trudne do rozwiązania, ponieważ jego sprowadza się do wybranych przez siebie technologii. Aby uzyskać opis skalowanie, jak partycja (lub fragmentu) zarówno kod, jak i stan staje się wyzwaniem. Korzystając z kodu i stan różnych technologii, co jest typowe już dziś, skrypty wdrażania usługi mikrousług muszą mieć możliwość skalowania oba te. Ta separacja dotyczy również sprawności i elastyczności, dlatego można przeprowadzić uaktualnienie niektórych mikrousługi bez konieczności aktualizacji wszystkich z nich jednocześnie.

Powrócimy do naszych porównanie podejścia monolitycznego i mikrousług na chwilę. Ten diagram przedstawia różnice w podejścia do przechowywania stanu:

#### <a name="state-storage-for-the-two-approaches"></a>Stan magazynu w przypadku dwóch metod

![Magazynu stanu platformy usługi Service Fabric][Image2]

***Podejścia monolitycznego po lewej stronie, zawiera pojedynczą bazę danych i warstwy określonych technologii.***

***Podejście mikrousług, po prawej stronie zawiera wykres wzajemnie połączonych mikrousług, których stan jest zwykle ograniczone do mikrousług i różne technologie są używane.***

W przypadku zastosowania podejścia monolitycznego aplikacja zwykle korzysta z pojedynczej bazy danych. Zaletą używania jednej bazy danych jest ona w jednym miejscu, co pozwala na łatwe do wdrożenia. Każdy składnik może mieć jednej tabeli do przechowywania jego stanu. Zespoły muszą ściśle oddzielić stanu, co jest trudne. Siłą rzeczy ktoś będzie wydawać się dodać kolumnę do istniejącej tabeli odbiorcy, do sprzężenia między tabelami i tworzenie zależności w warstwie magazynu. Po tak się stanie, nie można skalować poszczególne składniki.

W przypadku zastosowania podejścia mikrousług każda usługa zarządza i przechowuje swój własny stan. Każda usługa jest odpowiedzialna skalowanie w kodzie i stanu ze sobą, aby sprostać wymaganiom usługi. Wadą jest to, gdy potrzebujesz do tworzenia widoków lub kwerend danych Twojej aplikacji, należy do wykonywania zapytań w wielu sklepach stanu. Najczęściej można rozwiązać ten problem przez oddzielne mikrousług, który tworzy widok w kolekcji z mikrousług. Jeśli musisz uruchomić wiele spontanicznej zapytań na danych, należy rozważyć, zapisywanie danych poszczególne mikrousługi w usługi magazynowania danych do analizy w trybie offline.

Mikrousługi są numerów wersji. Istnieje możliwość dla różnych wersji mikrousług, aby uruchomić obok siebie. Nowsza wersja mikrousługi może zakończyć się niepowodzeniem podczas uaktualniania i trzeba go z powrotem obniżyć do wcześniejszej wersji. Przechowywanie wersji jest również przydatne w przypadku A / B, testowanie, gdzie różni użytkownicy środowisko różnych wersji usługi. Na przykład jest wspólne dla uaktualnienie mikrousług dla określonej grupy klientów, aby przetestować nową funkcję przed udostępnieniem jej więcej powszechnie.

### <a name="interacts-with-other-microservices-over-well-defined-interfaces-and-protocols"></a>Współdziała z innymi mikrousług za pośrednictwem dobrze zdefiniowanych interfejsów i protokołów

W ciągu ostatnich 10 lat został opublikowany wyczerpujące informacje opisujące wzorce komunikacji w architektury zorientowanej na usługi. Ogólnie rzecz biorąc komunikacja usługi użyto metody REST przy użyciu protokołów HTTP i TCP i XML lub JSON jako format serializacji. Z perspektywy interfejsu to kwestia podejście projektu sieci web. Ale nic nie należy uniemożliwić korzystanie z protokołów binarne lub własnych formatów danych. Po prostu należy pamiętać, że użytkownicy będą mieli czas trudniejsze, za pomocą mikrousługi, jeśli te protokoły i formatów nie są dostępne w sposób jawny.

### <a name="has-a-unique-name-url-used-to-resolve-its-location"></a>Ma unikatową nazwę (adres URL) używany do rozpoznawania lokalizacji

Twoje mikrousług należy adresowalnych wszędzie tam, gdzie jest uruchomiony. Jeśli myślisz o maszyn i który z nich działa określonego mikrousług, mogą wystąpić zły szybko.

W taki sam sposób, że DNS jest rozpoznawana jako określonego adresu URL określonego komputera z mikrousług musi unikatową nazwę, aby odnaleźć jego bieżącej lokalizacji. Mikrousługi należy adresowalnych nazw, które są niezależne od infrastruktury, w których są one uruchamiane w. Oznacza to, że istnieje interakcji między jak usługa zostanie wdrożona, i jak okaże się, ponieważ musi istnieć rejestr usług. W przypadku maszyny nie powiedzie się, usługa Rejestr trzeba poinformować Cię, gdy usługa została przeniesiona do.

### <a name="remains-consistent-and-available-in-the-presence-of-failures"></a>Pozostają spójne i dostępne w razie awarii

Zajmowanie się nieoczekiwanych awarii jest jednym z najtrudniejszych problemów do rozwiązania, szczególnie w rozproszonym systemie. Większość kodu, który możemy napisać deweloperów służy do obsługi wyjątków. Podczas testowania, również wydajemy najwięcej czasu na obsługę wyjątków. Ten proces jest bardziej skomplikowane niż pisanie kodu w celu obsługi błędów. Co się stanie, gdy maszyna, na którym jest uruchomiony mikrousług nie powiodło się? Należy na wykrycie awarii, czyli twardych problem samodzielnie. Można jednak również konieczne ponowne uruchomienie usługi mikrousług.

Aby zapewnić dostępność mikrousługi musi być odporna na awarie i aby ponownie uruchomić na innym komputerze. Oprócz tych wymagań dotyczących odporności dane nie powinny zostać utracone i trzeba zachować spójność danych.

Odporność jest trudny do osiągnięcia w przypadku niepowodzenia podczas uaktualniania aplikacji. Mikrousłudze oraz pracy z wdrożenia systemu, nie trzeba odzyskać. Należy określić, czy może on nadal przejść od razu do nowszej wersji ani wrócić do poprzedniej wersji do utrzymania spójnego stanu. Należy wziąć pod uwagę na kilka pytań, takich jak czy maszyn wystarcza są dostępne do zachowania przenoszenie do przodu i sposób odzyskać poprzednie wersje mikrousług. Podejmowanie tych decyzji, potrzebujesz mikrousług emitowanie informacji o kondycji.

### <a name="reports-health-and-diagnostics"></a>Raporty Kondycja i Diagnostyka

Mogą wydawać się oczywiste, jest często pomijane, ale mikrousługi musi zgłosić jego kondycja i Diagnostyka. W przeciwnym razie masz mały wgląd w jego kondycji z perspektywy operacji. Korelowanie zdarzeń diagnostycznych na zestaw niezależnych usług i rozwiązywania problemów związanych z niesymetryczności zegara komputera rozsądnie kolejności zdarzeń, może być trudne. W ten sam sposób, że możesz korzystać z mikrousług za pośrednictwem protokołów uzgodnionego i formatów danych musisz standaryzacji jak kondycji i zdarzenia diagnostyczne, które będzie ostatecznie znajdą się w magazynie zdarzeń do wykonywania zapytań i wyświetlania dziennika. Dzięki podejściu mikrousług różne zespoły muszą uzgodnić format pojedynczego logowania. Musi istnieć spójnego podejścia do wyświetlania zdarzeń diagnostycznych w aplikacji jako całości.

Kondycja różni się od diagnostyki. Kondycja jest o mikrousługach raportowania swojego bieżącego stanu, aby podjąć odpowiednie działania. Dobrym przykładem jest praca z mechanizmów uaktualnieniu i wdrożeniu w celu zapewnienia dostępności. Chociaż usługa może być aktualnie w złej kondycji ze względu na awarię procesu lub ponowne uruchomienie komputera, usługa nadal może być operacyjnej. Ostatnią czynnością, jaką potrzebne jest gorsza sytuacji przez rozpoczęciem uaktualniania. Najlepszym rozwiązaniem jest badać w pierwszym lub poczekaj na mikrousługach odzyskać. Zdarzenia dotyczące kondycji z mikrousługi pomagają nam podejmowanie świadomych decyzji, a w efekcie pomagają stworzyć samonaprawiania usług.

## <a name="guidance-for-designing-microservices-on-azure"></a>Wskazówki dotyczące projektowania mikrousług na platformie Azure 
Odwiedź Centrum architektury platformy Azure, aby uzyskać wskazówki na [projektowanie i tworzenie mikrousług na platformie Azure](https://docs.microsoft.com/azure/architecture/microservices/).

## <a name="service-fabric-as-a-microservices-platform"></a>Usługa Service Fabric, jest to platforma mikrousług

Usługa Azure Service Fabric powstało podczas Microsoft przeszła ze dostarczanie spakowany produktów, które były zazwyczaj monolityczne, dostarczaniu usług. Środowisko tworzenia i obsługi dużych usług, takich jak Azure SQL Database i Azure Cosmos DB w kształcie usługi Service Fabric. Platforma ewoluował wraz z upływem czasu, jak zastosowały więcej usług. Usługa Service Fabric musiał uruchomić nie tylko na platformie Azure, ale także w autonomicznych wdrożeniach systemu Windows Server.

***Celem usługi Service Fabric jest rozwiązywaniu trudnych problemów, kompilowania i uruchamiania usługi i używanie zasobów infrastruktury, dzięki czemu zespoły mogą rozwiązywaniu problemów biznesowych przy użyciu podejścia mikrousług.***

Usługa Service Fabric pomaga w tworzeniu aplikacji, które używają podejście mikrousług, zapewniając:

* Platforma, która zapewnia usługi systemowe na wdrażanie, uaktualnianie, wykryć, ponowne uruchomienie usługi nie powiodło się, Odkryj usługi, kierowanie komunikatów w postaci, zarządzanie stanem i monitorowanie kondycji.
* Możliwość wdrażania aplikacji albo uruchomione w kontenerach lub procesach. Service Fabric to kontener i procesów programu orchestrator.
* Produktywność programowania interfejsów API, aby pomóc Ci tworzyć aplikacje, jak mikrousługi: [ASP.NET Core, elementów Reliable Actors oraz usług Reliable Services](service-fabric-choose-framework.md). Na przykład możesz uzyskać informacje, kondycja i Diagnostyka lub możesz skorzystać z wbudowaną wysoką dostępność.

***Usługa Service Fabric jest niezależny od o jak opracować swoje usługi, a następnie można użyć dowolnej technologii. Ale udostępnia wbudowane programowania interfejsów API, które ułatwiają tworzenie mikrousług.***

### <a name="migrating-existing-applications-to-service-fabric"></a>Migrowanie istniejących aplikacji do usługi Service Fabric

Usługa Service Fabric umożliwia ponowne używanie istniejącego kodu i zmodernizować go za pomocą nowych mikrousług. Modernizacja aplikacji, składa się z pięciu etapów i możesz uruchamiać i zatrzymywać na każdym etapie. Dostępne są następujące etapy:

1) Zacznij od tradycyjnych aplikacji monolitycznej.  
2) Przeprowadź migrację. Hostuj istniejący kod w usłudze Service Fabric przy użyciu kontenerów lub plików wykonywalnych gościa.  
3) Modernizuj. Dodaj nowych mikrousług wraz z istniejącego kodu konteneryzowanych.  
4) Innowacje. Podziel monolityczną aplikację na mikrousługi zgodnie z potrzebami.  
5) Przekształcanie aplikacji — na mikrousługi. Przekształcenie istniejących aplikacji monolitycznych lub tworzyć nowe aplikacje od podstaw.

![Migracja do mikrousług][Image3]

Należy pamiętać, że możesz *uruchomić i zatrzymać w dowolnym z tych etapów*. Nie masz do postępu do kolejnego etapu. 

Przyjrzyjmy się przykłady dla każdego z tych etapów.

**Migrate (Migracja)**  
Dwa ze względu na wiele firm migrowania istniejących aplikacji monolitycznych do kontenerów:

* Zmniejszenie kosztów, ze względu na konsolidacji i usunięcie istniejącego sprzętu lub ze względu na uruchamianie aplikacji na zwiększenie gęstości.
* Kontrakt spójne wdrażanie, między środowiskami deweloperskim i operacji.

Obniżenie kosztów są oczywiste. W firmie Microsoft wiele istniejących aplikacji są jest konteneryzowana, co prowadzi do milionów dolarów oszczędności. Spójne wdrażanie jest trudniejsze do oceny, ale równie ważne. Oznacza to deweloperzy mogą wybrać technologie, które wskazują ich, że operacje będzie akceptować tylko pojedynczą metodę wdrażania aplikacji i zarządzaniem nimi. Eliminuje ona operacje z konieczności złożoność obsługi różnych technologii bez wymuszania deweloperom wybierz tylko niektóre z nich. Zasadniczo każda aplikacja jest konteneryzowana w obrazach niezależna wdrożenia.

W wielu organizacjach zatrzymać w tym miejscu. Mają korzyści z kontenerów, a Usługa Service Fabric udostępnia wszystkie funkcje zarządzania, m.in. wdrożenia uaktualnień, przechowywanie wersji, wycofywania i monitorowanie kondycji.

**Modernizuj**  
Modernizacja jest dodanie nowych usług wraz z istniejącego kodu konteneryzowanych. Jeśli zamierzasz pisanie nowego kodu, najlepiej wykonać proces na niewielkie etapy do ścieżki mikrousług. Może to oznaczać, dodawania nowego punktu końcowego interfejsu API REST lub nowej logiki biznesowej. W ten sposób możesz uruchomić proces tworzenia nowych mikrousług i praktyki opracowywania i wdrażania ich.

**Wprowadzanie innowacji**  
To podejście mikrousług jest przystosowana do zmieniających się potrzeb biznesowych. Na tym etapie należy zdecydować, czy można uruchomić podziału monolitycznych do usług lub innowacje. Klasycznym przykładem jest, gdy bazy danych, którego używasz jako kolejka przepływu pracy staje się wąskim gardłem przetwarzania. Jak liczba przepływów pracy żądań rośnie, pracy musi być dystrybuowane do skalowania. Skorzystaj z tym określonym wystąpieniem aplikacji, która nie jest skalowany lub wymagający będą aktualizowane częściej, Podziel ją jako mikrousługi i wprowadzania innowacji.

**Przekształcanie aplikacji — na mikrousługi**  
Na tym etapie aplikacja jest w pełni składa się z (lub Podziel na) mikrousług. Aby uzyskać dostęp do tego punktu, wprowadzono podróż mikrousług. Można uruchomić w tym miejscu, ale aby to zrobić bez mikrousług platformy, aby ułatwić wymaga znaczących inwestycji.

### <a name="are-microservices-right-for-my-application"></a>Mikrousługi — jest odpowiednią dla mojej aplikacji?

Może. W firmie Microsoft jak zaczęła tworzenie rozwiązań dla chmury, ze względów biznesowych więcej zespołów wiele z nich rzędu milionów dolarów zalety biorąc podejście podobne do mikrousług. Bing, na przykład używa mikrousług lata. Dla innych zespołów podejście mikrousług był nowy. Zespoły Ustaliliśmy, że zostały trudnych problemów rozwiązać poza ich podstawowych obszarów sile. Jest to, dlaczego usługi Service Fabric zdobyte rozwinięty technologii do tworzenia usług.

Celem usługi Service Fabric jest zmniejszenie złożoności kompilowania aplikacji mikrousług, dzięki czemu nie trzeba przechodzić przez operacje przeprojektowania kosztownych tyle. Na początku są małe, skalowania w razie, wycofaniu usługi, dodać nowe i ewoluują wraz z użycia przez klientów. Zdajemy sobie sprawę, że istnieją wiele problemów do można rozwiązać się mikrousług bardziej przystępne dla większości deweloperów. Kontenery i model programowania aktora są przykładami małe kroki opisane w tym kierunku. Możemy się, że więcej innowacje pojawią się ułatwiają podejście mikrousług.


## <a name="next-steps"></a>Kolejne kroki

* [Mikrousługi: Rewolucja w aplikacjach obsługiwana przez chmurę](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/)
* [Centrum architektury platformy Azure: Tworzenie mikrousług na platformie Azure](https://docs.microsoft.com/azure/architecture/microservices/)
* [Najważniejsze wskazówki dotyczące platformy Azure aplikacji usługi Service Fabric i klastra](service-fabric-best-practices-overview.md)
* [Przegląd terminologii usługi Service Fabric](service-fabric-technical-overview.md)

[Image1]: media/service-fabric-overview-microservices/monolithic-vs-micro.png
[Image2]: media/service-fabric-overview-microservices/statemonolithic-vs-micro.png
[Image3]: media/service-fabric-overview-microservices/microservices-migration.png
