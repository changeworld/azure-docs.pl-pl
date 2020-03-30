---
title: Wprowadzenie do mikrousług na platformie Azure
description: Omówienie dlaczego tworzenie aplikacji w chmurze z podejściem mikrousług jest ważne dla nowoczesnego tworzenia aplikacji i jak usługa Azure Service Fabric zapewnia platformę, aby to osiągnąć.
ms.topic: conceptual
ms.date: 01/07/2020
ms.custom: sfrev
ms.openlocfilehash: af18a6cb45808c0af5ec2782a3fd2100e3b7bf99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75750619"
---
# <a name="why-use-a-microservices-approach-to-building-applications"></a>Dlaczego warto używać podejścia mikrousług do tworzenia aplikacji

Dla programistów faktoring aplikacji na części składowe nie jest niczym nowym. Zazwyczaj stosuje się podejście warstwowe, z magazynem zaplecza, logiką biznesową warstwy środkowej i interfejsem użytkownika front-end.Typically, a tiered approach is used, with a back-end store, middle-tier business logic, and a front-end user interface (UI). Co *zmieniło* się w ciągu ostatnich kilku lat jest to, że deweloperzy są tworzenie aplikacji rozproszonych dla chmury.

Oto kilka zmieniających się potrzeb biznesowych:

* Usługa, która jest budowana i obsługiwana na dużą skalę, aby dotrzeć do klientów w nowych regionach geograficznych.
* Szybsze dostarczanie funkcji i możliwości, aby w zwinny sposób reagować na potrzeby klientów.
* Ulepszone wykorzystanie zasobów w celu zmniejszenia kosztów.

Te potrzeby biznesowe mają wpływ na *sposób* tworzenia aplikacji.

Aby uzyskać więcej informacji na temat podejścia platformy Azure do mikrousług, zobacz [Mikrousługi: Rewolucja aplikacji obsługiwana przez chmurę.](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/)

## <a name="monolithic-vs-microservices-design-approach"></a>Podejście do projektowania monolitycznych i mikrousług

Aplikacje ewoluują w miarę czasu. Udane aplikacje ewoluują, będąc użytecznymi dla ludzi. Nieudane aplikacje nie ewoluują i są ostatecznie przestarzałe. Oto pytanie: ile wiesz o swoich wymaganiach dzisiaj i co będą w przyszłości? Załóżmy na przykład, że budujemy aplikację raportowania dla działu w firmie. Masz pewność, że aplikacja ma zastosowanie tylko w zakresie firmy i że raporty nie będą przechowywane długo. Twoje podejście będzie się różnić od tego, powiedzmy, budowania usługi, która dostarcza treści wideo dziesiątkom milionów klientów.

Czasami, coraz coś za drzwiami jako dowód koncepcji jest czynnikiem napędowym. Wiesz, że aplikacja może zostać przeprojektowana później. Nie ma sensu nadmiernie inżynierii coś, co nigdy nie przyzwyczaja. Z drugiej strony, gdy firmy budują dla chmury, oczekuje się wzrostu i użycia. Wzrost i skala są nieprzewidywalne. Chcemy szybko prototypować, a jednocześnie wiemy, że jesteśmy na ścieżce, która poradzi sobie z przyszłym sukcesem. Jest to podejście lean startup: budować, mierzyć, uczyć się i iterować.

W erze klient/serwer skupiliśmy się na tworzeniu aplikacji warstwowych przy użyciu określonych technologii w każdej warstwie. Termin *monolityczne* zastosowanie pojawił się do opisania tych podejść. Interfejsy zwykle znajdują się między warstwami, a bardziej szczelnie sprzężony projekt był używany między składnikami w każdej warstwie. Deweloperzy zaprojektowali i uwzględnili klasy, które zostały skompilowane w bibliotekach i połączone ze sobą w kilka plików wykonywalnych i bibliotek DLL.

Monolityczne podejście projektowe przynosi korzyści. Aplikacje monolityczne są często prostsze w projektowaniu, a wywołania między składnikami są szybsze, ponieważ te wywołania są często za pomocą komunikacji międzyprocesowej (IPC). Ponadto, każdy testuje jeden produkt, który wydaje się być bardziej efektywne wykorzystanie zasobów ludzkich. Wadą jest to, że istnieje ścisłe sprzężenie między warstwami warstwowymi i nie można skalować poszczególnych komponentów. Jeśli musisz wykonać poprawki lub uaktualnienia, musisz poczekać, aż inni zakończą testy. Trudniej jest być zwinnym.

Mikrousługi rozwiązać te wady i bardziej zgodne z poprzednimi wymaganiami biznesowymi. Ale mają one również zarówno korzyści, jak i zobowiązania. Korzyści mikrousług są, że każdy z nich zazwyczaj hermetyzuje prostsze funkcje biznesowe, które można skalować w górę lub w dół, testować, wdrażać i zarządzać niezależnie. Jedną z ważnych zalet podejścia mikrousług jest to, że zespoły są napędzane bardziej przez scenariusze biznesowe niż przez technologię. Mniejsze zespoły opracowują mikrousługi na podstawie scenariusza klienta i używają wszelkich technologii, które mają być używane.

Innymi słowy organizacja nie musi standaryzować technologii do obsługi aplikacji mikrousług. Poszczególne zespoły, które są właścicielami usług, mogą robić to, co ma dla nich sens w oparciu o wiedzę zespołową lub to, co jest najbardziej odpowiednie do rozwiązania problemu. W praktyce zestaw zalecanych technologii, takich jak określony magazyn NoSQL lub struktura aplikacji sieci web, jest preferowany.

Wadą mikrousług jest, że trzeba zarządzać bardziej oddzielne jednostki i radzić sobie z bardziej złożonych wdrożeń i wersji. Ruch sieciowy między mikrousługami zwiększa, podobnie jak odpowiednie opóźnienia sieci. Wiele gadatliwych, ziarnistych usług może spowodować koszmar wydajności. Bez narzędzi ułatwiających przeglądanie tych zależności trudno jest zobaczyć cały system.

Standardy sprawiają, że podejście mikrousług działa, określając sposób komunikowania się i tolerowanie tylko rzeczy, których potrzebujesz z usługi, a nie sztywne kontrakty. Ważne jest, aby zdefiniować te kontrakty z góry w projekcie, ponieważ usługi aktualizują się niezależnie od siebie. Innym opisem ukutowanym do projektowania za pomocą podejścia mikrousług jest "drobnoziarnista architektura zorientowana na usługi (SOA)".

***W najprostszym podejściu do projektowania mikrousług jest o niezwiązanej federacji usług, z niezależnymi zmianami do każdego i uzgodnionych standardów komunikacji.***

W miarę tworzenia większej liczby aplikacji w chmurze, ludzie odkryli, że ten rozkład ogólnej aplikacji do niezależnych usług skoncentrowanych na scenariuszach jest lepszym podejściem długoterminowym.

## <a name="comparison-between-application-development-approaches"></a>Porównanie podejść do tworzenia aplikacji

![Tworzenie aplikacji platformy sieci szkieletowej usług][Image1]

1) Aplikacja monolityczna zawiera funkcje specyficzne dla domeny i jest zwykle podzielona na warstwy funkcjonalne, takie jak sieć Web, biznes i dane.

2) Skalowanie aplikacji monolitycznych przez klonowanie go na wielu serwerach/maszynach wirtualnych/kontenerach.

3) Aplikacja mikrousługi oddziela funkcje do oddzielnych mniejszych usług.

4) Podejście mikrousług skaluje się w poziomie, wdrażając każdą usługę niezależnie, tworząc wystąpienia tych usług na serwerach/maszynach wirtualnych/kontenerach.

Projektowanie za pomocą podejścia mikrousług nie jest odpowiednie dla wszystkich projektów, ale nie jest bardziej zgodne z celami biznesowymi opisanymi wcześniej. Począwszy od podejścia monolitycznego może mieć sens, jeśli wiesz, że będziesz miał możliwość przerobienia kodu później do projektu mikrousług. Częściej zaczynasz od aplikacji monolitycznych i powoli rozbijasz ją etapami, zaczynając od obszarów funkcjonalnych, które muszą być bardziej skalowalne lub elastyczne.

Korzystając z podejścia mikrousług, można skomponować aplikację wielu małych usług. Te usługi są uruchamiane w kontenerach, które są wdrażane w klastrze komputerów. Mniejsze zespoły opracowują usługę, która koncentruje się na scenariuszu i niezależnie testować, wersji, wdrażania i skalowania każdej usługi, dzięki czemu cała aplikacja może ewoluować.

## <a name="what-is-a-microservice"></a>Co to jest mikrousługa?

Istnieją różne definicje mikrousług. Ale większość z tych cech mikrousług są powszechnie akceptowane:

* Hermetyzowanie scenariusza klienta lub biznesowego. Jaki problem rozwiązujesz?
* Opracowany przez mały zespół inżynierów.
* Napisany w dowolnym języku programowania, przy użyciu dowolnej struktury.
* Składa się z kodu i opcjonalnie stan, z których oba są niezależnie wersjona, wdrożone i skalowane.
* Interakcja z innymi mikrousługami za pomocą dobrze zdefiniowanych interfejsów i protokołów.
* Mają unikatowe nazwy (adresy URL), które są używane do rozpoznawania ich lokalizacji.
* Pozostają spójne i dostępne w obecności awarii.

Podsumowując:

***Aplikacje mikrousługowe składają się z małych, niezależnie wersjonowanych i skalowalnych usług zorientowanych na klienta, które komunikują się ze sobą za pomocą standardowych protokołów z dobrze zdefiniowanymi interfejsami.***

### <a name="written-in-any-programming-language-using-any-framework"></a>Napisane w dowolnym języku programowania, przy użyciu dowolnej struktury

Jako deweloperzy chcemy mieć swobodę wyboru języka lub struktury, w zależności od naszych umiejętności i potrzeb usługi, którą tworzymy. W przypadku niektórych usług można wycenić korzyści wydajności języka C++ powyżej czegokolwiek innego. Dla innych łatwość zarządzanego rozwoju, który można uzyskać z języka C# lub Java może być ważniejsze. W niektórych przypadkach może być konieczne użycie określonej biblioteki partnerów, technologii przechowywania danych lub metody do uwidaczniania usługi klientom.

Po wybraniu technologii należy wziąć pod uwagę zarządzanie operacyjne lub cyklu życia i skalowanie usługi.

### <a name="allows-code-and-state-to-be-independently-versioned-deployed-and-scaled"></a>Umożliwia niezależne przechowywanie, wdrażanie i skalowanie kodu i stanu

Bez względu na to, jak piszesz mikrousług, kod i opcjonalnie stan, należy niezależnie wdrożyć, uaktualnić i skalować. Ten problem jest trudny do rozwiązania, ponieważ sprowadza się do wyboru technologii. Do skalowania, zrozumienie, jak partycjonować (lub niezależnego fragmentu) zarówno kodu i stanu jest trudne. Gdy kod i stan używać różnych technologii, co jest powszechne w dzisiejszych czasach skryptów wdrażania dla mikrousługi muszą być w stanie skalować je zarówno. Ta separacja jest również o elastyczność i elastyczność, dzięki czemu można uaktualnić niektóre mikrousług bez konieczności uaktualniania wszystkich z nich naraz.

Wróćmy do naszego porównania podejścia monolityczne i mikrousług na chwilę. Ten diagram pokazuje różnice w podejściach do przechowywania stanu:

#### <a name="state-storage-for-the-two-approaches"></a>Składowanie państwa dla tych dwóch podejść

![Magazyn stanu platformy sieci szkieletowej usług][Image2]

***Podejście monolityczne, po lewej stronie, ma jedną bazę danych i warstwy określonych technologii.***

***Podejście mikrousług, po prawej stronie, ma wykres połączonych mikrousług, gdzie stan jest zazwyczaj ograniczone do mikrousługi i różne technologie są używane.***

W podejściu monolitycznym aplikacja zazwyczaj używa pojedynczej bazy danych. Zaletą korzystania z jednej bazy danych jest to, że znajduje się w jednej lokalizacji, co ułatwia wdrożenie. Każdy składnik może mieć jedną tabelę do przechowywania jego stanu. Zespoły muszą ściśle oddzielić stan, co jest wyzwaniem. Nieuchronnie ktoś będzie kuszony, aby dodać kolumnę do istniejącej tabeli klienta, utworzyć sprzężenie między tabelami i utworzyć zależności w warstwie magazynu. Po tym przypadku nie można skalować poszczególnych składników.

W podejściu mikrousług każda usługa zarządza i przechowuje swój własny stan. Każda usługa jest odpowiedzialna za skalowanie kodu i stanu razem, aby spełnić wymagania usługi. Wadą jest to, że gdy trzeba utworzyć widoki lub kwerendy, danych aplikacji, należy wykonać kwerendę w wielu magazynach stanu. Ten problem jest zazwyczaj rozwiązywane przez oddzielne mikrousługi, która tworzy widok w kolekcji mikrousług. Jeśli musisz uruchomić wiele zaimprowiza zaimprowizowanej kwerendy na dane, należy rozważyć zapisanie danych każdej mikrousługi do usługi hurtowni danych do analizy w trybie offline.

Mikrousługi są wersjona. Jest możliwe dla różnych wersji mikrousługi do uruchamiania obok siebie. Nowsza wersja mikrousługi może zakończyć się niepowodzeniem podczas uaktualniania i muszą zostać wycofane do wcześniejszej wersji. Przechowywanie wersji jest również przydatne w przypadku testowania A/B, w którym różni użytkownicy doświadczają różnych wersji usługi. Na przykład jest wspólne uaktualnienie mikrousługi dla określonego zestawu klientów, aby przetestować nowe funkcje przed wprowadzeniem go szerzej.

### <a name="interacts-with-other-microservices-over-well-defined-interfaces-and-protocols"></a>Współdziała z innymi mikrousługami za pomocą dobrze zdefiniowanych interfejsów i protokołów

W ciągu ostatnich 10 lat opublikowano obszerne informacje opisujące wzorce komunikacji w architekturach zorientowanych na usługi. Ogólnie rzecz biorąc, komunikacja usługi używa podejścia REST z protokołami HTTP i TCP i XML lub JSON jako format serializacji. Z punktu widzenia interfejsu chodzi o przyjęcie podejścia do projektowania stron internetowych. Ale nic nie powinno powstrzymać Cię od używania protokołów binarnych lub własnych formatów danych. Wystarczy pamiętać, że ludzie będą mieli trudniej przy użyciu mikrousług, jeśli te protokoły i formaty nie są otwarcie dostępne.

### <a name="has-a-unique-name-url-used-to-resolve-its-location"></a>Ma unikatową nazwę (URL) używaną do rozpoznawania jego lokalizacji

Mikrousługi musi być adresowalny wszędzie tam, gdzie jest uruchomiony. Jeśli myślisz o komputerach i który z nich jest uruchomiony określonej mikrousługi, rzeczy mogą się zepsuć szybko.

W ten sam sposób, że usługa DNS rozpoznaje określony adres URL do określonego komputera, mikrousługa wymaga unikatowej nazwy, dzięki czemu jego bieżąca lokalizacja jest wykrywalna. Mikrousługi potrzebują adresowalnych nazw, które są niezależne od infrastruktury, na której są uruchomione. Oznacza to, że istnieje interakcja między sposób wdrażania usługi i jak jest odnaleziony, ponieważ musi istnieć rejestr usługi. Gdy komputer ulegnie awarii, usługa rejestru musi poinformować, gdzie usługa została przeniesiona do.

### <a name="remains-consistent-and-available-in-the-presence-of-failures"></a>Pozostaje spójny i dostępny w obecności awarii

Radzenie sobie z nieoczekiwanymi awariami jest jednym z najtrudniejszych problemów do rozwiązania, zwłaszcza w systemie rozproszonym. Większość kodu, który piszemy jako deweloperzy jest do obsługi wyjątków. Podczas testowania spędzamy również najwięcej czasu na obsłudze wyjątków. Proces jest bardziej zaangażowany niż pisanie kodu do obsługi błędów. Co się stanie, gdy komputer, na którym jest uruchomiona mikrousługa, nie powiedzie się? Musisz wykryć awarię, która sama w sobie jest trudnym problemem. Ale należy również ponownie uruchomić mikrousługi.

Aby uzyskać dostępność mikrousługi musi być odporne na awarie i można ponownie uruchomić na innym komputerze. Oprócz tych wymagań dotyczących odporności dane nie powinny być tracone, a dane muszą pozostać spójne.

Odporność jest trudne do osiągnięcia, gdy błędy zdarzają się podczas uaktualniania aplikacji. Mikrousługi, pracy z systemem wdrażania, nie trzeba odzyskać. Musi określić, czy można kontynuować przejście do nowszej wersji lub wycofać do poprzedniej wersji, aby zachować spójny stan. Należy wziąć pod uwagę kilka pytań, takich jak czy wystarczającej liczby maszyn są dostępne do kontynuowania do przodu i jak odzyskać poprzednie wersje mikrousługi. Aby podjąć te decyzje, należy mikrousługi do emitowania informacji o kondycji.

### <a name="reports-health-and-diagnostics"></a>Raporty dotyczące zdrowia i diagnostyki

Może się wydawać oczywiste i często pomijane, ale mikrousługi musi zgłosić jego kondycji i diagnostyki. W przeciwnym razie masz mały wgląd w jego kondycji z punktu widzenia operacji. Korelowanie zdarzeń diagnostycznych w ramach zestawu niezależnych usług i radzenie sobie z przekrzywieniami zegara maszynowego w celu uzyskania sensu kolejności zdarzeń jest wyzwaniem. W ten sam sposób, w jaki wchodzisz w interakcję z mikrousługą za pomocą uzgodnionych protokołów i formatów danych, należy ustandaryzować sposób rejestrowania zdarzeń kondycji i diagnostyki, które ostatecznie znajdą się w magazynie zdarzeń do wykonywania zapytań i wyświetlania. Z podejściem mikrousług różne zespoły muszą uzgodnić format rejestrowania pojedynczego. Musi istnieć spójne podejście do wyświetlania zdarzeń diagnostycznych w aplikacji jako całości.

Zdrowie różni się od diagnostyki. Kondycja jest o mikrousługi raportowania jego bieżącego stanu do podjęcia odpowiednich działań. Dobrym przykładem jest praca z mechanizmami uaktualniania i wdrażania w celu utrzymania dostępności. Chociaż usługa może być obecnie w złej kondycji z powodu awarii procesu lub ponownego uruchomienia komputera, usługa może nadal działać. Ostatnią rzeczą, której potrzebujesz, jest pogorszyć sytuację, rozpoczynając uaktualnienie. Najlepszym rozwiązaniem jest zbadanie najpierw lub czas dla mikrousługi do odzyskania. Zdarzenia dotyczące kondycji z mikrousługi pomagają nam podejmować świadome decyzje, a w efekcie pomagają tworzyć usługi samoleczenia.

## <a name="guidance-for-designing-microservices-on-azure"></a>Wskazówki dotyczące projektowania mikrousług na platformie Azure

Odwiedź centrum architektury platformy Azure, aby uzyskać wskazówki dotyczące [projektowania i tworzenia mikrousług na platformie Azure.](https://docs.microsoft.com/azure/architecture/microservices/)

## <a name="service-fabric-as-a-microservices-platform"></a>Sieci szkieletowej usług jako platformy mikrousług

Usługa Azure Service Fabric pojawiła się, gdy firma Microsoft przeszła z dostarczania produktów pudełkowych, które były zazwyczaj monolityczne, do dostarczania usług. Środowisko tworzenia i obsługi dużych usług, takich jak Azure SQL Database i Azure Cosmos DB, ukształtowało sieci szkieletowej usług. Platforma ewoluowała z czasem, gdy przyjmowało ją więcej usług. Sieci szkieletowej usług musiał działać nie tylko na platformie Azure, ale także w autonomicznych wdrożeniach systemu Windows Server.

***Celem sieci szkieletowej usług jest rozwiązanie trudnych problemów związanych z tworzeniem i uruchamianiem usługi oraz efektywne korzystanie z zasobów infrastruktury, dzięki czemu zespoły mogą rozwiązywać problemy biznesowe przy użyciu podejścia mikrousług.***

Sieci szkieletowej usług ułatwia tworzenie aplikacji, które używają podejścia mikrousług, zapewniając:

* Platforma, która zapewnia usługi systemowe do wdrażania, uaktualniania, wykrywania i ponownego uruchamiania usług, odnajdywanie usług, kierowanie komunikatów, zarządzanie stanem i monitorowanie kondycji.
* Możliwość wdrażania aplikacji działających w kontenerach lub jako procesy. Usługa Fabric jest koordynatorem kontenerów i procesów.
* Wydajne programowanie interfejsów API ułatwiające tworzenie aplikacji jako mikrousług: [ASP.NET Core, Reliable Actors i Reliable Services.](service-fabric-choose-framework.md) Na przykład można uzyskać informacje o kondycji i diagnostyki lub można skorzystać z wbudowanej wysokiej dostępności.

***Sieci szkieletowej usług jest niezależny o tym, jak tworzyć usługi i można użyć dowolnej technologii. Ale zapewnia wbudowane programowanie interfejsów API, które ułatwiają tworzenie mikrousług.***

### <a name="migrating-existing-applications-to-service-fabric"></a>Migrowanie istniejących aplikacji do sieci szkieletowej usług

Usługa Sieci szkieletowej umożliwia ponowne użycie istniejącego kodu i zmodernizować go za pomocą nowych mikrousług. Istnieje pięć etapów modernizacji aplikacji i można rozpocząć i zatrzymać na dowolnym etapie. Etapy są następujące:

1) Zacznij od tradycyjnej aplikacji monolitycznej.  
2) Migracji. Kontenery lub pliki wykonywalne gościa do hostowania istniejącego kodu w sieci szkieletowej usług.  
3) Modernizacji. Dodaj nowe mikrousługi wraz z istniejącym kodem konteneryzowanym.  
4) Innowacji. Podziel aplikację monolityczną na mikrousługi na podstawie potrzeb.  
5) Przekształć aplikacje w mikrousługi. Przekształć istniejące aplikacje monolityczne lub stwórz nowe aplikacje typu greenfield.

![Migracja do mikrousług][Image3]

Pamiętaj, że możesz *rozpocząć i zatrzymać się na każdym z tych etapów.* Nie musisz przechodzić do następnego etapu. 

Spójrzmy na przykłady dla każdego z tych etapów.

**Migracji**  
Z dwóch powodów wiele firm migruje istniejące aplikacje monolityczne do kontenerów:

* Redukcja kosztów, albo z powodu konsolidacji i usuwania istniejącego sprzętu, albo z powodu uruchamiania aplikacji o większej gęstości.
* Spójna umowa wdrożeniowa między programami a operacjami.

Redukcje kosztów są proste. W firmie Microsoft wiele istniejących aplikacji jest konteneryzowanych, co prowadzi do milionów dolarów oszczędności. Spójne wdrażanie jest trudniejsze do oceny, ale równie ważne. Oznacza to, że deweloperzy mogą wybrać technologie, które im odpowiadają, ale operacje zaakceptują tylko jedną metodę wdrażania aplikacji i zarządzania nimi. Łagodzi operacje z konieczności radzenia sobie ze złożonością obsługi różnych technologii bez zmuszania deweloperów do wyboru tylko niektórych. Zasadniczo każda aplikacja jest konteneryzowany do obrazów wdrożenia samodzielnego.

Wiele organizacji zatrzymuje się tutaj. Mają już zalety kontenerów, a usługa Service Fabric zapewnia pełne środowisko zarządzania, w tym wdrażanie, uaktualnienia, przechowywanie wersji, wycofywanie i monitorowanie kondycji.

**Modernizacji**  
Modernizacja jest dodanie nowych usług wraz z istniejącym kodem konteneryzowanym. Jeśli masz zamiar napisać nowy kod, najlepiej wykonać małe kroki w dół ścieżki mikrousług. Może to oznaczać dodanie nowego punktu końcowego interfejsu API REST lub nowej logiki biznesowej. W ten sposób można rozpocząć proces tworzenia nowych mikrousług i praktyce ich opracowywania i wdrażania.

**Innowacji**  
Podejście mikrousług uwzględnia zmieniające się potrzeby biznesowe. Na tym etapie należy zdecydować, czy rozpocząć dzielenie aplikacji monolitycznych na usługi, czy innowacje. Klasyczny przykład tutaj jest, gdy baza danych, która jest używany jako kolejka przepływu pracy staje się wąskie gardło przetwarzania. Wraz ze wzrostem liczby żądań przepływu pracy, praca musi być rozdzielona na skalę. Weź ten konkretny element aplikacji, który nie jest skalowanie lub które muszą być aktualizowane częściej i podzielić go jako mikrousługi i innowacji.

**Przekształcanie aplikacji w mikrousługi**  
Na tym etapie aplikacja jest w pełni składa się z (lub podzielić na) mikrousług. Aby osiągnąć ten punkt, dokonano podróży mikrousług. Możesz rozpocząć tutaj, ale aby to zrobić bez platformy mikrousług, aby pomóc wymaga znacznej inwestycji.

### <a name="are-microservices-right-for-my-application"></a>Czy mikrousługi są odpowiednie dla mojej aplikacji?

Może. W firmie Microsoft, ponieważ coraz więcej zespołów zaczęło tworzyć dla chmury ze względów biznesowych, wiele z nich zdawało sobie sprawę z korzyści płynących z przyjęcia podejścia podobnego do mikrousług. Bing, na przykład, używa mikrousług od lat. W przypadku innych zespołów podejście mikrousług było nowe. Zespoły odkryły, że istnieją trudne problemy do rozwiązania poza ich głównymi obszarami siły. Dlatego service fabric zyskał przyczepność jako technologia dla usług budowlanych.

Celem sieci szkieletowej usług jest zmniejszenie złożoności tworzenia aplikacji mikrousług, dzięki czemu nie trzeba przechodzić przez jak najwięcej kosztownych przeprojektowania. Zacznij od małych, skaluj w razie potrzeby, przestarzałe usługi, dodaj nowe i rozwijaj się wraz z użyciem klientów. Wiemy również, że istnieje wiele innych problemów, które nie zostały jeszcze rozwiązane, aby mikrousługi były bardziej przystępne dla większości deweloperów. Kontenery i model programowania aktora są przykładami małych kroków w tym kierunku. Jesteśmy pewni, że pojawi się więcej innowacji, aby ułatwić podejście mikrousług.

## <a name="next-steps"></a>Następne kroki

* [Mikrousługi: rewolucja aplikacji obsługiwana przez chmurę](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/)
* [Centrum architektury platformy Azure: tworzenie mikrousług na platformie Azure](https://docs.microsoft.com/azure/architecture/microservices/)
* [Najważniejsze wskazówki dotyczące aplikacji i klastrów usługi Azure Service](service-fabric-best-practices-overview.md)
* [Omówienie terminologii sieci szkieletowej usług](service-fabric-technical-overview.md)

[Image1]: media/service-fabric-overview-microservices/monolithic-vs-micro.png
[Image2]: media/service-fabric-overview-microservices/statemonolithic-vs-micro.png
[Image3]: media/service-fabric-overview-microservices/microservices-migration.png
