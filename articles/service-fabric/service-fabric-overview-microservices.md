---
title: Wprowadzenie do mikrousług na platformie Azure | Dokumentacja firmy Microsoft
description: Przegląd dlaczego jest ważne na potrzeby opracowywania aplikacji modern tworzenia aplikacji w chmurze z podejścia mikrousług oraz jak sieć szkieletowa usług Azure udostępnia platformę można to osiągnąć.
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: ''
ms.assetid: fae2be85-0ab4-4cd3-9d1f-e0d95fe1959b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/02/2017
ms.author: msfussell
ms.openlocfilehash: d56b78a0274842c012e57f80f0c2fe5619a62962
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="why-a-microservices-approach-to-building-applications"></a>Dlaczego mikrousług podejścia do tworzenia aplikacji?
Jako deweloperów, a nie ma nowych w jak naszym zdaniem o factoring aplikacji na części składnika. Jest centralna modelu obiektu orientacji, abstrakcje oprogramowania i componentization. Obecnie ta factorization zwykle formę klasy i interfejsy między biblioteki udostępnione i warstw technologicznych. Zazwyczaj warstwowego podejścia jest pobierana z magazynu zaplecza, logika biznesowa warstwy środkowej i interfejs frontonu użytkownika (UI). Co *ma* zmienione w ciągu kilku ostatnich lat jest, że firma Microsoft, jako deweloperom tworzenia aplikacji, które są dla chmury rozproszonych i obsługiwanego przez firmę.

Są zmieniających się potrzeb biznesowych:

* Usługa jest utworzony i działa na dużą skalę w celu dotarcia do klientów w nowych regionów geograficznych (na przykład).
* Szybsze dostarczania funkcji i możliwości, aby można było odpowiadanie na potrzeby klientów w sposób agile.
* Wykorzystanie zasobów ulepszone zmniejszyć koszty.

Tych potrzeb biznesowych mają wpływ na *jak* budujemy aplikacji.

Aby uzyskać więcej informacji na temat podejścia platformy Azure do mikrousług odczytu [Mikrousług: obrotów aplikacji obsługiwane przez usługę w chmurze](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/).

## <a name="monolithic-vs-microservice-design-approach"></a>Wbudowanymi a mikrousługi na etapie projektowania
Wszystkie aplikacje rozwijać, wraz z upływem czasu. Pomyślne aplikacji rozwijać, jest przydatne do osób. Aplikacji nie powiedzie się nie rozwijać i po pewnym czasie są przestarzałe. Staje się pytanie: jaka informacje dotyczące wymagań dzisiaj i jakie będą one być w przyszłości? Na przykład załóżmy, że tworzysz aplikację raportowania dla działu. Masz pewność, że aplikacja pozostaje w zakresie firmy oraz czy raporty są krótkim okresie. Wybór metody różni się od, powiedz, tworzenia usługi, która dostarcza zawartość wideo na dziesiątki milionów klientów. 

Czasami pobraniem czegoś limit drzwi jako Weryfikacja koncepcji jest współczynnik pobudzenie, gdy znasz później przeprojektowany aplikacji. Brak małego punktu w nadmiernie inżynierii coś, który nigdy nie jest używany. Jest to zwykły engineering zależność. Z drugiej strony mówiąc firmy dotyczących tworzenia chmury, oczekuje się wzrostu i użycia. Problem polega na wzrostu i skali są nieprzewidywalne. Chcemy można było prototypu szybko a także są nam na ścieżce radzenia sobie z przyszłego sukcesu. Jest to metoda uruchamiania gotowa: kompilacji, miary, Dowiedz się więcej i iteracji.

Podczas era klient serwer możemy pielęgnowane skupić się na tworzeniu aplikacji warstwowych przy użyciu określonych technologii w każdej warstwie. Termin *wbudowanymi* aplikacji pojawiło się do tych metod. Interfejsy pielęgnowane się między warstwami, a ściślej sprzężonego projektu zostało użyte między składnikami w każdej warstwie. Deweloperzy zaprojektowany i wzięciu pod uwagę klasy, które zostały skompilowane w bibliotekach i powiązane w kilku plików wykonywalnych i bibliotek DLL. 

Istnieją korzyści podejście wbudowanymi projektu. Często jest łatwiejsze do projektowania, i ma szybsze połączenia między składnikami, ponieważ te wywołania są często za pośrednictwem komunikacji międzyprocesowej (IPC). Ponadto wszyscy testy jeden produkt jest więcej osób resource wydajne. Wadą podwyższonego jest brak ścisłej sprzężenia między warstwami warstwowych, czy nie skalowania poszczególnych składników. Trzeba wykonać poprawki lub aktualizacje, należy czekać do zakończenia testowania ich przez innych. Jest trudniej jest elastyczne.

Mikrousług adresów tych downsides i więcej ściśle Dopasuj wymagania biznesowe, ale ma także zarówno korzyści i zobowiązań. Zalety mikrousług są, że każdy z nich zazwyczaj hermetyzuje prostsze możliwości biznesowe, które skalować w górę lub w dół testu, wdrażanie i zarządzanie nimi niezależnie. Korzyścią ważne podejścia mikrousługi to, czy zespoły są więcej niż różnych scenariuszy biznesowych regulowane przez technologii, która zachęca warstwowego podejścia. W praktyce niewielkich zespołów opracowanie mikrousługi, oparta na scenariuszu klienta i używać technologii, dowolnego wybranego terminu. 

Innymi słowy organizacja nie wymaga normalizacji techniczna to zachowanie aplikacji mikrousługi. Zespoły poszczególnych czy własne usługi można zrobić, co ma sens dla nich oparte na wiedzy zespołu lub odpowiednio rozwiązać problem. W praktyce zbiór zalecanych technologii, takich jak NoSQL określonego magazynu lub sieci web struktury aplikacji, zalecane jest.

Wadą podwyższonego mikrousług polega na zarządzanie większą liczbą osobne jednostki i zajmujących się bardziej złożone wdrożenia i kontroli wersji. Ruch sieciowy między mikrousług zwiększa się oraz odpowiedniego opóźnienia sieciowe. Wiele usług chatty, szczegółowe są przepisu dla nightmare wydajności. Bez narzędzia ułatwiające widoku te zależności, trudno jest "w temacie" całego systemu. 

Standardy uczynienia mikrousługi pracy przez wyrażenie zgody na temat sposobu komunikacji i jest odporna na czynności należy z usługi, a nie sztywne umów. Ważne jest określenie tych umów do góry w projekcie, ponieważ usługi zaktualizować niezależnie od siebie nawzajem. Opis innego coined projektowanie podejście mikrousług jest "szczegółowych zorientowane na usługę architektura (SOA)."

***W najprostszym podejście do projektowania mikrousług dotyczy rozdzielonymi federacji usług, niezależnie od zmian do każdego i ustalonym standardów komunikacji.***

Jak więcej aplikacji w chmurze są tworzone, osób dowiedzieć się, że ten dekompozycji ogólną aplikacji do usług niezależnych, fokus scenariusz lepiej długoterminową.

## <a name="comparison-between-application-development-approaches"></a>Porównanie metod tworzenia aplikacji
![Tworzenie aplikacji platformy Service Fabric][Image1]

1) Wbudowanymi aplikacji zawiera funkcje właściwe dla domeny i jest zwykle rozdzielonych funkcjonalności warstw, takich jak sieci web, biznesowych i danych.

2) Skalowanie aplikacji wbudowanymi w klonowania go na wielu serwerach/maszyn wirtualnych/kontenerów.

3) Aplikacja mikrousługi oddziela funkcji do poszczególnych usług mniejsze.

4) Skaluje podejście mikrousług przez wdrażania każdej usługi niezależnie, tworzenia wystąpień tych usług między serwerami/maszyn wirtualnych/kontenerów.

Projektowanie z mikrousługi podejście nie jest panaceum dla wszystkich projektów, ale Dopasuj dokładniejsze z celów biznesowych opisany wcześniej. Począwszy od wbudowanymi podejście może być do przyjęcia, jeśli wiadomo, że użytkownik będzie miał możliwość później zmian kodu w projekcie mikrousług. Zazwyczaj rozpoczyna się od wbudowanymi aplikacji i powoli Podziel je etapami, począwszy od obszarów funkcjonalnych, które muszą być bardziej skalowalny lub elastyczne.

Podsumowując, podejście mikrousługi jest utworzenie aplikacji z wielu usług mała. Usługi uruchamiane w kontenerach, które zostały wdrożone w klastrze maszyn. Niewielkich zespołów opracowanie to usługa, która koncentruje się na scenariuszu i niezależnie przetestować, wersji, wdrażanie i skalowanie poszczególnych usług, dzięki czemu można rozwijać całej aplikacji.

## <a name="what-is-a-microservice"></a>Co to jest mikrousługi?
Istnieją różne definicje mikrousług. Jeśli wyszukiwania w Internecie, można znaleźć wiele przydatne zasoby, które zapewniają własne widoki i definicje. Jednak większość następujące właściwości mikrousług powszechnie uzgodnionych:

* Hermetyzuj scenariuszy biznesowych lub klienta. Co to jest są rozwiązywania tego problemu?
* Opracowane przez małe zespołu inżynieryjnego.
* Napisane w dowolnym języku programowania i za pomocą dowolnej architektury.
* Składają się z kodu i (opcjonalnie) stanu, które są niezależnie określonej wersji, wdrożone i skalowania.
* Interakcje z innymi mikrousług za pośrednictwem protokołów i interfejsów dobrze zdefiniowany.
* Mają unikatowe nazwy (URL) używany do rozpoznawania ich lokalizacji.
* Pozostają spójne i dostępne w razie awarii.

Umożliwia podsumowanie tych właściwości na:

***Aplikacje Mikrousługi składają się z małych niezależnie numerów wersji i skalowalne skoncentrowane na kliencie usług, które komunikują się ze sobą za pośrednictwem standardowych protokołów z dobrze zdefiniowanych interfejsów.***

Firma Microsoft omówione dwa pierwsze punkty w poprzedniej sekcji, a teraz możemy rozszerzają i innych wyjaśnienia.

### <a name="written-in-any-programming-language-and-use-any-framework"></a>Napisane w dowolnym języku programowania i za pomocą dowolnej architektury
Jako deweloperów będziemy swobodę języka lub struktury chcemy, w zależności od naszych umiejętności lub na potrzeby usługi. W niektórych usług może wartości zwiększenia wydajności C++ przede wszystkim else. W innych usługach może być najważniejszych łatwość zarządzanych programowania w języku C# lub języka Java. W niektórych przypadkach może być konieczne użycie biblioteki partnera, technologii magazynowania danych lub oznacza ujawnienia usługi dla klientów.

Po wykonaniu wybrane technologii, możesz się zarządzanie operacyjne lub cykl życia i skalowania usługi.

### <a name="allows-code-and-state-to-be-independently-versioned-deployed-and-scaled"></a>Umożliwia kodu i stanu niezależnie określonej wersji, wdrożyć i skalowania
Jednak chcesz zapisać Twoje mikrousług, kod i opcjonalnie stan powinien niezależnie wdrożyć, uaktualniania i skalowania. To jest jednym z problemów trudne do rozwiązania, ponieważ pochodzi on w dół do Wybór technologii. Do skalowania, zrozumienia sposobu partycji (lub fragmentacji) kod i stanu jest trudne. Kod i stan użycia osobnych technologii, które jest obecnie wspólne, skryptów wdrożenia dla użytkownika mikrousługi muszą być w stanie poradzić sobie z skalowanie oba te. Dotyczy to również o sprawność działania oraz elastyczność, aby móc uaktualnić niektórych mikrousług bez uaktualniania wszystkich z nich na raz.

Wracając do wbudowanymi i mikrousługi podejście, na chwilę, na poniższym diagramie przedstawiono różnice w podejście do przechowywania stanu.

#### <a name="state-storage-between-application-styles"></a>Stanu przechowywania między style aplikacji
![Magazynu stanu platformy sieci szkieletowej usług][Image2]

***Podejście wbudowanymi po lewej stronie ma pojedynczej bazy danych i warstwy określonych technologii.***

***Metody mikrousług po prawej stronie jest wykres połączonych mikrousług, których stan jest zwykle zakresem mikrousługi i różne technologie są używane.***

W podejściu wbudowanymi zwykle aplikacja używa pojedynczej bazy danych. Zaletą jest jego pojedynczej lokalizacji, co ułatwia wdrażanie. Poszczególnych składników mogą mieć pojedynczej tabeli, aby zapisać jej stan. Zespoły należy ściśle oddzielnych stanu, który jest żądanie. Brak natychmiastową temptations Dodaj nową kolumnę do istniejącej tabeli klienta, czy sprzężenie tabel i utworzyć zależności w warstwie magazynu. Po dzieje się tak, nie można skalować poszczególnych składników. 

W ujęciu mikrousług każdej usługi zarządza i zapisuje stan własny. Każda usługa jest odpowiedzialna za skalowanie w kodzie i stan ze sobą w celu spełnienia wymagań usługi. Wadą interfejsu jest gdy trzeba utworzyć widoki lub kwerendy danych aplikacji, należy zapytania w stan różnych sklepach. Zazwyczaj jest to rozwiązać dzięki użyciu oddzielnych mikrousługi, który tworzy widok w kolekcji mikrousług. Jeśli zachodzi potrzeba wykonania wielu zapytań natychmiast na danych, każdy mikrousługi należy rozważyć zapisywania danych do usługi magazynowania danych do analizy ruchu w trybie offline.

Przechowywanie wersji jest specyficzna dla wdrożonej wersji mikrousługi tak wiele, różne wersje wdrożenia i uruchomienia obok siebie. Przechowywanie wersji dotyczy scenariuszy, w którym nowsza wersja mikrousługi zakończy się niepowodzeniem podczas uaktualniania i potrzebuje do przywrócenia starszej wersji. Wykonuje innej sytuacji dla wersji testowania A/B-style, gdzie różni użytkownicy występują różne wersje usługi. Na przykład jest wspólne dla uaktualnienia mikrousługi dla określonej grupy klientów, aby przetestować nową funkcję przed udostępnieniem jej powszechnie się więcej. Po zarządzania cyklem życia mikrousług to teraz oferuje nam komunikację między nimi.

### <a name="interacts-with-other-microservices-over-well-defined-interfaces-and-protocols"></a>Współdziała z innymi mikrousług za pośrednictwem protokołów i interfejsów dobrze zdefiniowany
W tym temacie musi małego uwagi, ponieważ szeroką gamę materiały informacje o architekturze zorientowane na usługę, która została opublikowana w ciągu ostatnich 10 lat opisuje wzorce komunikacji. Ogólnie rzecz biorąc komunikacja usługi używa podejście REST z protokołów HTTP i TCP i XML lub JSON jako formatu serializacji. Z perspektywy interfejsu jest o obejmującego podejście przyjęte w projekcie sieci web. Ale nie zatrzymuje z przy użyciu protokołów binarnych lub własnych formatów danych. Należy przygotować osobom przeszkodę raz przy użyciu programu mikrousług, jeśli te protokoły i formaty nie są dostępne w sposób jawny.

### <a name="has-a-unique-name-url-used-to-resolve-its-location"></a>Ma unikatową nazwę (URL) używany do rozpoznawania lokalizacji
Należy pamiętać o tym, jak firma Microsoft zachować informujący o tym, że podejście mikrousługi przypomina sieci web? Jak sieci web z mikrousługi musi być adresowanego wszędzie tam, gdzie jest uruchomiona. Jeśli myślisz o maszyny, która z nich jest uruchomiona określonego mikrousługi rzeczy niedługo szybko. 

W taki sam sposób, że usługa DNS rozpoznaje określonego adresu URL do określonego komputera z mikrousługi musi mieć unikatową nazwę, dzięki czemu jego bieżącej lokalizacji będzie wykrywalny. Mikrousług muszą mieć nazwy adresowanego, które stały się niezależnie od infrastruktury, które działają na. Oznacza to, że istnieje interakcji między sposób wdrażania usługi i jak okaże się, ponieważ musi istnieć rejestru usługi. Jednakowo w przypadku awarii komputera Usługa rejestru musi zorientować się, gdy usługa jest teraz uruchomiona. 

Dzięki temu udostępniają nam następnym temacie: odporność i spójność.

### <a name="remains-consistent-and-available-in-the-presence-of-failures"></a>Pozostaje spójna i dostępne w razie awarii
Zajmowanie nieoczekiwanych awarii jest jednym z najtrudniejsze problemy można rozwiązać, szczególnie w rozproszonym systemie. Większość kodu, który możemy zapisać jako deweloperzy jest obsługa wyjątków i jest to również gdzie jest zużywany czas najbardziej podczas testowania. Problem jest bardziej skomplikowane niż pisania kodu do obsługi błędów. Co się stanie, gdy maszyny, na którym jest uruchomiona mikrousługi nie powiodło się? Nie tylko muszą wykryć ten błąd mikrousługi (twarde problem samodzielnie), ale może też być konieczne coś o ponowne uruchomienie programu mikrousługi. 

Mikrousługi musi być odporność na awarie i często Uruchom ponownie na innym komputerze przyczyn dostępności. To również zawiera stanu, który został zapisany w imieniu mikrousługi, gdzie mikrousługi można odzyskać tego stanu z i czy jest możliwe ponowne uruchomienie pomyślnie mikrousługi. Innymi słowy musi istnieć odporność w obliczeń (proces ponownego uruchomienia), a także odporność w stanie lub danych (brak utraty danych i dane pozostają spójne).

Problemy odporności są ich w innych scenariuszach, na przykład po awarii mają miejsce podczas uaktualniania aplikacji. Mikrousługi, Praca w systemie wdrożenia, nie musisz odzyskać. Należy także zdecydować, czy może on nadal przejście do nowszej wersji lub zamiast tego przywrócenie poprzedniej wersji, aby zachować w spójnym stanie. Pytania, takie jak czy maszyn wystarcza są dostępne do zachowania przenoszenie do przodu i jak odzyskać poprzednie wersje mikrousługi należy wziąć pod uwagę. Wymaga to mikrousługi można wyemitować informacji o kondycji, aby można było podejmowanie tych decyzji.

### <a name="reports-health-and-diagnostics"></a>Raporty Kondycja i Diagnostyka
Może wydawać się oczywiste i jest często pomijane, ale mikrousługi muszą zgłosić, jego kondycja i Diagnostyka. W przeciwnym razie jest niewiele wglądu z perspektywy operacji. Korelowanie zdarzeń diagnostycznych przez zestaw usług niezależnych i zajmujących maszyny zegara pochyla rozsądnie kolejność zdarzeń jest trudne. W taki sam sposób interakcji z mikrousługi za pośrednictwem protokołów ustalonym i formatów danych pojawia się potrzeba zbudowania normalizacji w sposób kondycji i zdarzeń diagnostycznych, które ostatecznie kończą w magazynie zdarzeń do badania i wyświetlania dziennika. W podejściu mikrousług jest klucz czy różnych zespołów wyrażanie zgody na format jednego rejestrowania. Musi istnieć jednolity sposób wyświetlania zdarzeń diagnostycznych w aplikacja jako całość.

Kondycja jest inny niż diagnostyki. Kondycja jest o mikrousługi raportowania swojego bieżącego stanu podjęcia odpowiednich działań. Dobrym przykładem jest praca z uaktualnieniu i wdrożeniu mechanizmów w celu zapewnienia dostępności. Mimo że usługa może być aktualnie niepoprawny stan z powodu awarii procesu lub ponowne uruchomienie komputera, usługi może nadal działać. Ostatnim zadaniem, które są potrzebne jest zapewnienie to pogarsza się wraz z wykonując uaktualnienie. Najlepszym rozwiązaniem jest przeprowadzenie dochodzenia lub poczekać na mikrousługi do odzyskania. Zdarzenia kondycji z mikrousługi Pomóż nam podejmowania świadomych decyzji, a w efekcie ułatwić tworzenie usług samonaprawiania.

## <a name="service-fabric-as-a-microservices-platform"></a>Sieć szkieletowa usług jako platforma mikrousług
Sieć szkieletowa usług Azure związane z przejście przez firmę Microsoft z dostarczeniem pole produktów, które były najczęściej wbudowanymi w stylu, do dostarczania usług. Środowisko tworzenia i obsługi dużych usług, takich jak bazy danych SQL Azure i bazy danych rozwiązania Cosmos platformy Azure, w kształcie sieci szkieletowej usług. Platforma ewoluował w czasie przyjęta coraz więcej usług go. Ważne sieci szkieletowej usług było uruchomić nie tylko na platformie Azure, ale także w autonomicznych wdrożeniach systemu Windows Server.

***Celem usługi sieć szkieletowa jest do rozwiązywania problemów twardych tworzenia i uruchamiania usługi i wykorzystania zasobów infrastruktury, tak, aby zespoły można rozwiązać problemy biznesowe przy użyciu metody mikrousług.***

Sieć szkieletowa usług oferuje trzy obszary do tworzenia aplikacji, które używają podejście mikrousług:

* Platforma, która zapewnia usługi systemowe, aby wdrożyć, uaktualniania, wykrywanie i ponowne uruchomienie usługi nie powiodło się, odnajdywanie usług, kierowania wiadomości, zarządzanie stanem i monitorowanie kondycji. Tych usług systemu Włącz obowiązywać wielu cech mikrousług opisany wcześniej.
* Możliwość wdrażania aplikacji albo uruchomiona w kontenerach lub jako procesów. Sieć szkieletowa usług to kontener i procesów programu orchestrator.
* Wydajność programowania interfejsów API do tworzenia aplikacji, co mikrousług: [platformy ASP.NET Core, Reliable Actors i niezawodne usługi](service-fabric-choose-framework.md). Można wybrać dowolny kod do kompilacji z mikrousługi. Jednak te interfejsy API upewnij zadanie bardziej bezpośrednie i integrują się z platformą jest dokładniejsze. Na przykład w ten sposób Kondycja i Diagnostyka informacji można uzyskać, lub możesz korzystać z wbudowaną wysoką dostępność.

***Sieć szkieletowa usług jest niezależny w sposobie tworzenia usługi, a można użyć innych technologii. Jednak zapewnia wbudowanej programowania interfejsów API, które ułatwiają tworzenie mikrousług.***

### <a name="migrating-existing-applications-to-service-fabric"></a>Migrowanie istniejących aplikacji sieci szkieletowej usług
Klucza podejście do sieci szkieletowej usług jest ponownie wykorzystać istniejący kod, który może być następnie modernizowana z nowego mikrousług. Składa się pięć etapów modernizacji aplikacji, a można uruchomić i zatrzymać na każdym z etapów. Są to;

1) Przejdź do tradycyjnych trybu wbudowanymi  
2) Podnieś i Shift - host istniejący kod w sieci szkieletowej usług za pomocą kontenerów lub pliki wykonywalne gościa.  
3) Modernizacji - mikrousług nowe dodane równolegle z istniejącego kodu konteneryzowanych.  
4) Innowacji — wbudowanymi podzielić mikrousług całkowicie oparty na potrzeby.  
5) Przekształcone w mikrousług - przekształcania istniejących aplikacji wbudowanymi lub tworzenia nowych aplikacji greenfield.

![Migracja do Mikrousług][Image3]

Ważne jest, aby ponownie podkreślić, że można **uruchomić i zatrzymać w dowolnym z tych etapów**. Użytkownik nie są konieczne przejść do kolejnego etapu. Teraz Przyjrzyjmy się przykłady dla każdego z tych etapów.

**Podnieś i przesunięcia** -dużej liczby firm są podnoszenia i przesunięcie istniejące aplikacje wbudowanymi w kontenerach dwóch powodów;

- Zmniejszenie kosztów, albo z powodu konsolidacji i usuwania istniejących aplikacji sprzętu lub działa w zwiększeniu. 
- Kontrakt spójne wdrażanie między rozwoju i operacji.

Obniżenie kosztu są zrozumiałe, a w ramach firmy Microsoft, dużą liczbę istniejących aplikacji są trwa konteneryzowanych wystarczy, aby zapisać milionów dolarów. Spójne wdrażanie jest trudne do oceny, ale jednakowo jako ważne. Wyświetlany jest tekst deweloperzy nadal można wybrać technologii z tego pakietu ich jednak operacje przyjmuje tylko jeden sposób wdrażania i zarządzania nimi tych aplikacji. Go eliminuje operacje z konieczności złożoność wiele różnych technologii lub wymuszania deweloperom wybierać tylko niektóre z nich. Zasadniczo każda aplikacja jest konteneryzowanych obrazy niezależne wdrożenia.

W wielu organizacjach zatrzymać w tym miejscu. Mają zalet kontenery i sieci szkieletowej usług udostępnia możliwości zarządzania ukończone z wdrożenia uaktualnień, wersji, wycofywanie zmian, kondycji monitorowania itp.

**Modernizacji** — jest dodanie nowych usług, które będą widoczne obok istniejących konteneryzowanych kodu. Jeśli zamierzasz pisanie nowego kodu, najlepiej decyzji o zastosowaniu małe kroki do ścieżki mikrousług. Można to dodawania nowego punktu końcowego interfejsu API REST lub nowej logiki biznesowej. W ten sposób uruchamiania w podróży mikrousług nowe budynku i praktyki projektowania i wdrażania ich.

**Innowacji** — Pamiętaj tych oryginalnego zmieniających się potrzeb biznesowych na początku tego artykułu, które zwiększają podejście mikrousług? Na tym etapie, który jest są one wykonywane do mojej bieżącej aplikacji, a jeśli tak, należy uruchomić dzielenia monolityczna lub wprowadzają. W tym przykładzie jest w przypadku bazy danych staje się przetwarzanie "wąskie gardło", ponieważ jest on używany jako kolejka przepływu pracy. Jako liczba przepływów pracy żądań zwiększenie pracy musi być dystrybuowane do skalowania. Więc dla tej aplikacji, która nie jest określonym wystąpieniem skalowanie lub konieczne jest aktualizowane częściej, to limit podzielone mikrousługi i wprowadzać innowacje. 

**Przekształcone w mikrousług** — jest to, gdy aplikacja jest pełni złożony z (lub rozłożony na) mikrousług. Do osiągnięcia w tym miejscu, wprowadzono podróży mikrousług. Można uruchomić w tym miejscu, ale w tym celu bez mikrousług platformy, aby ułatwić jest znaczącą inwestycję. 

### <a name="are-microservices-right-for-my-application"></a>Czy mikrousług prawa do mojej aplikacji?
Może. Firma Microsoft napotkał został, że jak coraz więcej zespołów w programie Microsoft zaczęła kompilacji dla chmury powodów biznesowych, ile z nich realizowana zalet przyjmując podejście podobne mikrousługi. Bing, na przykład opracowuje mikrousług wyszukiwania lata. Inne zespoły podejście mikrousług zostało nowe. Zespoły wykryto, że wystąpiły problemy twardych rozwiązać poza ich obszarów core siły. Jest to, dlaczego sieci szkieletowej usług uzyskanych trakcji technologii wybranych do tworzenia usług.

Celem usługi sieć szkieletowa jest zmniejszenie złożoności kompilowania aplikacji za pomocą metody mikrousługi, dzięki czemu nie trzeba przechodzić przez jako wiele kosztownych redesigns. Zacznij od czegoś małego, skalowanie w razie potrzeby zastąpić usługi, dodać nowe i rozwijać klientowi użycie jest to rozwiązanie. Było wiadomo, że istnieje wiele problemów jeszcze, które należy rozwiązać dokonać bardziej przystępne mikrousług większość deweloperów. Kontenery i modelu programowania aktora przedstawiono małe kroki opisane w tym kierunku, a firma Microsoft pewności, że więcej innowacje pojawią się ułatwić.
 
<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="next-steps"></a>Kolejne kroki
* [Omówienie terminologii sieci szkieletowej usług](service-fabric-technical-overview.md)
* [Mikrousług: Ma obrotów aplikacji obsługiwane przez usługę w chmurze](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/)

[Image1]: media/service-fabric-overview-microservices/monolithic-vs-micro.png
[Image2]: media/service-fabric-overview-microservices/statemonolithic-vs-micro.png
[Image3]: media/service-fabric-overview-microservices/microservices-migration.png
