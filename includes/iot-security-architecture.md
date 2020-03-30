---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: iot-fundamentals
author: robinsh
ms.service: iot-fundamentals
ms.topic: include
ms.date: 08/07/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: a2eafd6bb34b897f3492ddcffd6841f0fabc4ca7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73034550"
---
Podczas projektowania systemu, ważne jest, aby zrozumieć potencjalne zagrożenia dla tego systemu i dodać odpowiednie mechanizmy obronne odpowiednio, jak system jest zaprojektowany i zaprojektowany. Ważne jest, aby zaprojektować produkt od początku z myślą o zabezpieczeniach, ponieważ zrozumienie, w jaki sposób osoba atakująca może być w stanie naruszyć system, pomaga upewnić się, że odpowiednie środki zaradcze są na miejscu od samego początku.

## <a name="security-starts-with-a-threat-model"></a>Zabezpieczenia zaczynają się od modelu zagrożenia

Firma Microsoft od dawna używa modeli zagrożeń dla swoich produktów i publicznie udostępniła proces modelowania zagrożeń. Doświadczenie firmy pokazuje, że modelowanie ma nieoczekiwane korzyści wykraczające poza natychmiastowe zrozumienie, jakie zagrożenia są najbardziej niepokojące. Na przykład tworzy również drogę do otwartej dyskusji z innymi osobami spoza zespołu programistów, co może prowadzić do nowych pomysłów i ulepszeń w produkcie.

Celem modelowania zagrożeń jest zrozumienie, w jaki sposób osoba atakująca może być w stanie naruszyć system, a następnie upewnić się, że odpowiednie środki zaradcze są w miejscu. Modelowanie zagrożeń zmusza zespół projektowy do rozważenia środków zaradczych, ponieważ system jest zaprojektowany, a nie po wdrożeniu systemu. Fakt ten jest niezwykle ważny, ponieważ doposażenie zabezpieczeń w mnóstwo urządzeń w terenie jest nie wykonalne, podatne na błędy i naraża klientów na ryzyko.

Wiele zespołów programistycznych wykonuje doskonałą pracę, rejestrując wymagania funkcjonalne dla systemu, które przynoszą korzyści klientom. Jednak zidentyfikowanie nieoczywistych sposobów, w jakie ktoś może niewłaściwie wykorzystać system, jest trudniejsze. Modelowanie zagrożeń może pomóc zespołom deweloperów zrozumieć, co osoba atakująca może zrobić i dlaczego. Modelowanie zagrożeń to ustrukturyzowany proces, który tworzy dyskusję na temat decyzji dotyczących projektowania zabezpieczeń w systemie, a także zmian w projekcie, które są wprowadzane po drodze, które wpływają na bezpieczeństwo. Chociaż model zagrożeń jest po prostu dokumentem, ta dokumentacja stanowi również idealny sposób na zapewnienie ciągłości wiedzy, zachowanie wyciągniętych wniosków i szybkie pomaganie nowemu zespołowi na pokładzie. Wreszcie wynik modelowania zagrożeń jest umożliwienie rozważenia innych aspektów zabezpieczeń, takich jak jakie zobowiązania w zakresie zabezpieczeń chcesz dostarczyć klientom. Te zobowiązania w połączeniu z modelowania zagrożeń informowania i badania dysku rozwiązania Internet of Things (IoT).

### <a name="when-to-do-threat-modeling"></a>Kiedy zrobić modelowanie zagrożeń

[Modelowanie zagrożeń](https://www.microsoft.com/en-us/sdl/adopt/threatmodeling.aspx) oferuje największą wartość po włączeniu go do fazy projektowania. Podczas projektowania masz największą elastyczność, aby wprowadzać zmiany w celu wyeliminowania zagrożeń. Eliminacja zagrożeń według projektu jest pożądanym rezultatem. Jest to o wiele łatwiejsze niż dodawanie środków zaradczych, testowanie ich i zapewnienie ich utrzymania na poziomie obecnym, a ponadto taka eliminacja nie zawsze jest możliwa. Trudniej jest wyeliminować zagrożenia, ponieważ produkt staje się bardziej dojrzały, a z kolei ostatecznie wymaga więcej pracy i dużo trudniejszych kompromisów niż modelowanie zagrożeń na wczesnym etapie rozwoju.

### <a name="what-to-consider-for-threat-modeling"></a>Co należy wziąć pod uwagę przy modelowaniu zagrożeń

Należy przyjrzeć się rozwiązaniu jako całości, a także skupić się na następujących obszarach:

* Funkcje zabezpieczeń i prywatności
* Cechy, których awarie mają znaczenie dla bezpieczeństwa
* Funkcje, które dotykają granicy zaufania

### <a name="who-performs-threat-modeling"></a>Kto wykonuje modelowanie zagrożeń

Modelowanie zagrożeń jest procesem jak każdy inny. Jest to dobry pomysł, aby traktować dokument modelu zagrożenia jak każdy inny składnik rozwiązania i sprawdzić jego poprawność. Wiele zespołów programistycznych wykonuje doskonałą pracę, rejestrując wymagania funkcjonalne dla systemu, które przynoszą korzyści klientom. Jednak zidentyfikowanie nieoczywistych sposobów, w jakie ktoś może niewłaściwie wykorzystać system, jest trudniejsze. Modelowanie zagrożeń może pomóc zespołom deweloperów zrozumieć, co osoba atakująca może zrobić i dlaczego.

### <a name="how-to-perform-threat-modeling"></a>Jak przeprowadzić modelowanie zagrożeń

Proces modelowania zagrożeń składa się z czterech kroków; kroki są następujące:

* Modelowanie aplikacji
* Wyliczaj zagrożenia
* Eliminowanie zagrożeń
* Sprawdzanie poprawności środków zaradczych

#### <a name="the-process-steps"></a>Etapy procesu

Trzy zasady, o które należy pamiętać podczas tworzenia modelu zagrożenia:

1. Tworzenie diagramu z architektury odniesienia.

2. Zacznij najpierw szerokość. Zapoznaj się z przeglądem i zrozum cały system przed głębokim nurkowaniem. Takie podejście pomaga zapewnić, że można głęboko nurkować w odpowiednich miejscach.

3. Napęd procesu, nie pozwól, aby proces cię napędzał. Jeśli znajdziesz problem w fazie modelowania i chcesz go zbadać, idź do niego! Nie czuję, że musisz wykonać te kroki niewolniczo.

#### <a name="threats"></a>Zagrożenia

Cztery podstawowe elementy modelu zagrożenia to:

* Procesy, takie jak usługi sieci web, usługi Win32 i demony *nix. Niektóre złożone jednostki (na przykład bramy polowe i czujniki) mogą być abstraktowane jako proces, gdy techniczne przechodzenie do szczegółów w tych obszarach nie jest możliwe.

* Magazyny danych (przechowywane są w dowolnym miejscu, takie jak plik konfiguracyjny lub baza danych)

* Przepływ danych (gdzie dane przemieszczają się między innymi elementami w aplikacji)

* Zewnętrzne jednostki (wszystko, co wchodzi w interakcję z systemem, ale nie jest pod kontrolą aplikacji, przykłady obejmują użytkowników i kanałów satelitarnych)

Wszystkie elementy na diagramie architektonicznym podlegają różnym zagrożeniom; w tym artykule mnemonic STRIDE. Przeczytaj [ponownie modelowanie zagrożeń, KROK,](https://blogs.msdn.microsoft.com/larryosterman/2007/09/04/threat-modeling-again-stride/) aby dowiedzieć się więcej o elementach STRIDE.

Różne elementy diagramu aplikacji podlegają pewnym zagrożeniom STRIDE:

* Procesy podlegają stride
* Przepływy danych podlegają tid
* Magazyny danych podlegają TID, a czasami R, gdy magazyny danych są pliki dziennika.
* Podmioty zewnętrzne podlegają SRD

## <a name="security-in-iot"></a>Bezpieczeństwo w IoT

Podłączone urządzenia specjalnego przeznaczenia mają znaczną liczbę potencjalnych obszarów powierzchni interakcji i wzorców interakcji, z których wszystkie należy wziąć pod uwagę, aby zapewnić ramy dla zapewnienia cyfrowego dostępu do tych urządzeń. Termin "dostęp cyfrowy" jest tutaj używany do odróżnienia od wszelkich operacji, które są przeprowadzane poprzez bezpośrednią interakcję z urządzeniem, gdzie zabezpieczenia dostępu są zapewniane za pośrednictwem fizycznej kontroli dostępu. Na przykład umieszczenie urządzenia w pomieszczeniu z zamkiem na drzwiach. Chociaż nie można odmówić dostępu fizycznego przy użyciu oprogramowania i sprzętu, można podjąć środki zapobiegające fizycznemu dostępowi prowadzącemu do zakłóceń systemu.

Podczas eksplorowania wzorców interakcji przyjrzyj się "kontroli urządzenia" i "danych urządzenia" z tym samym poziomem uwagi. "Kontrola urządzenia" może być klasyfikowana jako wszelkie informacje, które są dostarczane do urządzenia przez dowolną stronę w celu zmiany lub wpływania na jego zachowanie w kierunku jego stanu lub stanu środowiska. "Dane urządzenia" mogą być klasyfikowane jako wszelkie informacje, które urządzenie emituje do jakiejkolwiek innej strony o jego stanie i obserwowanym stanie otoczenia.

Aby zoptymalizować najlepsze rozwiązania w zakresie zabezpieczeń, zaleca się, aby typowa architektura IoT została podzielona na kilka składników/stref w ramach wykonywania modelowania zagrożeń. Strefy te są opisane w pełni w całej tej sekcji i obejmują:

* Urządzenia
* Brama polowa,
* Bramy w chmurze i
* Usług.

Strefy są szerokim sposobem segmentacji rozwiązania; każda strefa często ma własne wymagania dotyczące uwierzytelniania i autoryzacji. Strefy mogą być również używane do izolacji uszkodzenia i ograniczyć wpływ stref niskiego zaufania na wyższe strefy zaufania.

Każda strefa jest oddzielona granicą zaufania, która jest notowana jako kropkowana czerwona linia na poniższym diagramie. Reprezentuje przejście danych/informacji z jednego źródła do drugiego. Podczas tego przejścia dane/informacje mogą podlegać spoofingowi, manipulowaniu, odrzucaniu, ujawnianiu informacji, odmowie usługi i podnoszeniu uprawnień (STRIDE).

![Strefy zabezpieczeń IoT](media/iot-security-architecture/iot-security-architecture-fig1.png) 

Komponenty przedstawione w każdej granicy są również poddawane stride, umożliwiając pełny widok modelowania zagrożeń 360 rozwiązania. W poniższych sekcjach opracowywane są elementy i szczególne kwestie bezpieczeństwa i rozwiązania, które należy wprowadzić.

W poniższych sekcjach omówiono standardowe składniki zwykle spotykane w tych strefach.

### <a name="the-device-zone"></a>Strefa urządzenia

Środowisko urządzenia to bezpośrednia przestrzeń fizyczna wokół urządzenia, w której możliwy jest fizyczny dostęp i/lub "sieć lokalna" równorzędny dostęp cyfrowy do urządzenia. "Sieć lokalna" jest uważana za sieć, która jest odrębna i izolowana od publicznego Internetu, ale potencjalnie zmostkowana, i obejmuje wszelkie bezprzewodowe technologie radiowe bliskiego zasięgu, które pozwalają na komunikację urządzeń typu peer-to-peer. *Nie* obejmuje żadnej technologii wirtualizacji sieci tworzącej iluzję takiej sieci lokalnej i nie obejmuje również publicznych sieci operatorów, które wymagają od dwóch urządzeń do komunikowania się w przestrzeni sieci publicznej, jeśli miały by wejść w relację komunikacji peer-to-peer.

### <a name="the-field-gateway-zone"></a>Strefa bramy polowej

Brama polowa to urządzenie/urządzenie lub oprogramowanie komputera ogólnego przeznaczenia serwera, które działa jako program komunikacji i potencjalnie jako system sterowania urządzeniami i centrum przetwarzania danych urządzenia. Strefa bramy polowej obejmuje samą bramę polową i wszystkie urządzenia, które są do niej podłączone. Jak sama nazwa wskazuje, bramy polowe działają poza dedykowanymi urządzeniami przetwarzania danych, są zwykle związane z lokalizacją, mogą podlegać fizycznemu włamaniom i mają ograniczoną redundancję operacyjną. Wszystko po to, aby powiedzieć, że brama polowa jest powszechnie rzeczą, którą można dotknąć i sabotować, wiedząc, jaka jest jej funkcja.

Brama polowa różni się od zwykłego routera ruchu, ponieważ odgrywa aktywną rolę w zarządzaniu przepływem dostępu i informacji, co oznacza, że jest to jednostka adresowaną do aplikacji i połączenie sieciowe lub terminal sesji. Natomiast urządzenie lub zapora NAT nie kwalifikuje się jako bramy pól, ponieważ nie są jawnymi terminalami połączeń lub sesji, ale raczej połączeniami lub sesjami marszruty (lub blokowymi) za ich pośrednictwem. Brama polowa ma dwa odrębne obszary powierzchni. Jeden stoi na urządzeniach, które są do niego dołączone i reprezentuje wnętrze strefy, a drugi stoi wszystkie strony zewnętrzne i jest krawędzią strefy.

### <a name="the-cloud-gateway-zone"></a>Strefa bramy w chmurze

Brama w chmurze to system, który umożliwia zdalną komunikację z i do urządzeń lub bram terenowych z kilku różnych lokacji w przestrzeni sieci publicznej, zazwyczaj w kierunku chmurowego systemu kontroli i analizy danych, federacji takich systemów. W niektórych przypadkach brama w chmurze może natychmiast ułatwić dostęp do urządzeń specjalnego przeznaczenia z terminali, takich jak tablety lub telefony. W kontekście omówionym w tym miejscu "chmura" ma odnosić się do dedykowanego systemu przetwarzania danych, który nie jest powiązany z tą samą lokacją co dołączone urządzenia lub bramy polowe. Również w strefie chmury środki operacyjne uniemożliwiają ukierunkowany dostęp fizyczny i niekoniecznie są narażone na infrastrukturę "chmury publicznej".  

Brama w chmurze może być potencjalnie mapowana na nakładkę wirtualizacji sieci, aby ocieplić bramę w chmurze i wszystkie dołączone do niej urządzenia lub bramy polowe od innego ruchu sieciowego. Sama brama w chmurze nie jest systemem sterowania urządzeniem ani obiektem przetwarzania lub przechowywania danych urządzenia; tych obiektów interfejsu z bramą chmury. Strefa bramy w chmurze zawiera samą bramę w chmurze wraz ze wszystkimi bramami polowymi i urządzeniami bezpośrednio lub pośrednio do niej dołączonymi. Krawędź strefy jest odrębną powierzchnią, za pośrednictwem której komunikują się wszystkie strony zewnętrzne.

### <a name="the-services-zone"></a>Strefa usług

"Usługa" jest zdefiniowana w tym kontekście jako dowolny składnik oprogramowania lub moduł, który współdziała z urządzeniami za pośrednictwem bramy w polu lub chmurze do zbierania i analizy danych, a także do dowodzenia i kontroli. Usługi są mediatorami. Działają pod swoją tożsamością w kierunku bram i innych podsystemów, przechowują i analizują dane, samodzielnie wydają polecenia urządzeniom na podstawie szczegółowych informacji lub harmonogramów danych oraz ujawniają informacje i możliwości kontroli autoryzowanym użytkownikom końcowym.

### <a name="information-devices-versus-special-purpose-devices"></a>Urządzenia informacyjne a urządzenia specjalnego przeznaczenia

Komputery, telefony i tablety są przede wszystkim interaktywnymi urządzeniami informacyjnymi. Telefony i tablety są wyraźnie zoptymalizowane pod kątem maksymalizacji żywotności baterii. Najlepiej wyłączyć częściowo, gdy nie od razu wchodzi w interakcję z osobą lub gdy nie świadczy usług, takich jak odtwarzanie muzyki lub prowadzenie właściciela do określonej lokalizacji. Z punktu widzenia systemów te urządzenia informatyczne działają głównie jako serwery proxy wobec ludzi. Są to "siłowniki ludzi" sugerujące działania i "czujniki ludzi" zbierające dane wejściowe.

Urządzenia specjalnego przeznaczenia, od prostych czujników temperatury po złożone fabryczne linie produkcyjne z tysiącami komponentów w nich, są różne. Urządzenia te są znacznie bardziej ograniczone w celu i nawet jeśli zapewniają one jakiś interfejs użytkownika, są one w dużej mierze ograniczone do współdziałania z lub być zintegrowane z zasobami w świecie fizycznym. Mierzą i zgłaszają warunki środowiskowe, zawory skrętowe, serwa sterujące, alarmy dźwiękowe, światła przełączane i wykonują wiele innych zadań. Pomagają one w pracy, dla której urządzenie informacyjne jest albo zbyt ogólne, zbyt drogie, zbyt duże lub zbyt kruche. Konkretny cel natychmiast dyktuje ich projekt techniczny, a także dostępny budżet pieniężny na ich produkcję i zaplanowaną eksploatację. Połączenie tych dwóch kluczowych czynników ogranicza dostępny budżet energii operacyjnej, fizyczny ślad, a tym samym dostępne możliwości przechowywania, obliczeń i zabezpieczeń.

Jeśli coś "pójdzie nie tak" z zautomatyzowanymi lub zdalnie sterowanymi urządzeniami, na przykład, wady fizyczne lub wady logiczne kontroli umyślnego nieautoryzowanego włamania i manipulacji. Partie produkcji mogą zostać zniszczone, budynki mogą zostać splądrowane lub spalone, a ludzie mogą zostać ranni, a nawet zginąć. Jest to zupełnie inna klasa uszkodzeń niż ktoś, kto wysunie limit skradzionej karty kredytowej. Pasek zabezpieczeń dla urządzeń, które sprawiają, że rzeczy przenieść, a także dla danych z czujników, które ostatecznie powoduje polecenia, które powodują rzeczy do poruszania się, musi być wyższa niż w każdym scenariuszu e-commerce lub bankowości.

### <a name="device-control-and-device-data-interactions"></a>Sterowanie urządzeniem i interakcje danych urządzenia

Podłączone urządzenia specjalnego przeznaczenia mają znaczną liczbę potencjalnych obszarów powierzchni interakcji i wzorców interakcji, z których wszystkie należy wziąć pod uwagę, aby zapewnić ramy dla zapewnienia cyfrowego dostępu do tych urządzeń. Termin "dostęp cyfrowy" jest tutaj używany do odróżnienia od wszelkich operacji, które są przeprowadzane poprzez bezpośrednią interakcję z urządzeniem, gdzie zabezpieczenia dostępu są zapewniane za pośrednictwem fizycznej kontroli dostępu. Na przykład umieszczenie urządzenia w pomieszczeniu z zamkiem na drzwiach. Chociaż nie można odmówić dostępu fizycznego przy użyciu oprogramowania i sprzętu, można podjąć środki zapobiegające fizycznemu dostępowi prowadzącemu do zakłóceń systemu.

Podczas eksplorowania wzorców interakcji przyjrzyj się "kontroli urządzenia" i "danych urządzenia" z tym samym poziomem uwagi podczas modelowania zagrożeń. "Kontrola urządzenia" może być klasyfikowana jako wszelkie informacje, które są dostarczane do urządzenia przez dowolną stronę w celu zmiany lub wpływania na jego zachowanie w kierunku jego stanu lub stanu środowiska. "Dane urządzenia" mogą być klasyfikowane jako wszelkie informacje, które urządzenie emituje do jakiejkolwiek innej strony o jego stanie i obserwowanym stanie otoczenia.

## <a name="performing-threat-modeling-for-the-azure-iot-reference-architecture"></a>Wykonywanie modelowania zagrożeń dla architektury referencyjnej Usługi IoT platformy Azure

Firma Microsoft używa struktury opisane wcześniej do modelowania zagrożeń dla usługi Azure IoT. W poniższej sekcji użyto konkretnego przykładu architektury odniesienia Usługi Azure IoT, aby zademonstrować, jak myśleć o modelowaniu zagrożeń dla IoT i jak radzić sobie z zidentyfikowanymi zagrożeniami. W tym przykładzie określono cztery główne obszary zainteresowania:

* Urządzenia i źródła danych,
* Transport danych,
* Przetwarzanie urządzeń i zdarzeń oraz
* Prezentacja

![Modelowanie zagrożeń dla usługi Azure IoT](media/iot-security-architecture/iot-security-architecture-fig2.png)

Poniższy diagram zawiera uproszczony widok architektury IoT firmy Microsoft przy użyciu modelu diagramu przepływu danych, który jest używany przez narzędzie Microsoft Threat Modeling Tool:

![Modelowanie zagrożeń dla usługi Azure IoT przy użyciu narzędzia ms threat modeling tool](media/iot-security-architecture/iot-security-architecture-fig3.png)

Należy pamiętać, że architektura oddziela możliwości urządzenia i bramy. Takie podejście umożliwia użytkownikowi korzystanie z urządzeń bramy, które są bezpieczniejsze: są one w stanie komunikować się z bramą w chmurze za pomocą bezpiecznych protokołów, co zwykle wymaga większego obciążenia przetwarzania, że natywne urządzenie - takie jak termostat - może świadczenia. W strefie usług platformy Azure załóżmy, że brama w chmurze jest reprezentowana przez usługę Azure IoT Hub.

### <a name="device-and-data-sourcesdata-transport"></a>Źródła urządzeń i danych/transport danych

W tej sekcji opisano architekturę przedstawioną wcześniej przez obiektyw modelowania zagrożeń i przedstawiono omówienie sposobu rozwiązania niektórych problemów związanych z tym problemem. W tym przykładzie koncentruje się na podstawowych elementów modelu zagrożenia:

* Procesy (zarówno pod kontrolą, jak i elementy zewnętrzne)
* Komunikacja (nazywana również przepływami danych)
* Magazyn (nazywany także magazynami danych)

#### <a name="processes"></a>Procesy

W każdej z kategorii opisanych w architekturze Azure IoT w tym przykładzie próbuje ograniczyć szereg różnych zagrożeń na różnych etapach dane/informacje istnieją w: proces, komunikacja i magazyn. Poniżej przedstawiono przegląd najczęstszych dla kategorii "proces", a następnie przegląd tego, w jaki sposób można najlepiej złagodzić te zagrożenia:

**Fałszowanie (S)**: Osoba atakująca może wyodrębnić materiał klucza kryptograficznego z urządzenia na poziomie oprogramowania lub sprzętu, a następnie uzyskać dostęp do systemu za pomocą innego urządzenia fizycznego lub wirtualnego pod tożsamością urządzenia, z których pobrano materiał klucza. Dobrą ilustracją jest pilotów, które można włączyć dowolny telewizor i które są popularne narzędzia żartownisia.

**Odmowa usługi (D)**: Urządzenie może być niezdolne do działania lub komunikowania się poprzez zakłócanie częstotliwości radiowych lub cięcie przewodów. Na przykład kamera nadzoru, która celowo miała połączenie zasilania lub sieci, nie może w ogóle zgłaszać danych.

**Manipulowanie (T)**: Osoba atakująca może częściowo lub całkowicie zastąpić oprogramowanie uruchomione na urządzeniu, potencjalnie umożliwiając zastąpione oprogramowanie w celu wykorzystania prawdziwej tożsamości urządzenia, jeśli materiał klucza lub obiekty kryptograficzne zawierające kluczowe materiały były dostępne dla nielegalnego programu. Osoba atakująca może na przykład wykorzystać wyodrębniony materiał klucza do przechwycenia i wygaszenia danych z urządzenia na ścieżce komunikacji i zastąpić je fałszywymi danymi uwierzytelnionymi skradzionym materiałem klucza.

**Ujawnienie informacji (I)**: Jeśli urządzenie działa z manipulowanym oprogramowaniem, takie zmanipulowane oprogramowanie może potencjalnie wyciekać dane do nieautoryzowanych stron. Na przykład osoba atakująca może wykorzystać wyodrębniony materiał klucza, aby wstrzyknąć się do ścieżki komunikacji między urządzeniem a bramą kontrolera lub bramy polowej lub bramy w chmurze, aby syfonować informacje.

**Podniesienie uprawnień (E)**: Urządzenie, które wykonuje określoną funkcję, może zostać zmuszone do zrobienia czegoś innego. Na przykład zawór, który jest zaprogramowany do otwierania w połowie drogi, można oszukać, aby otworzyć całą drogę.

| **Składnik** | **Zagrożenie** | **Środki zaradcze** | **Ryzyko** | **Wdrażanie** |
| --- | --- | --- | --- | --- |
| Urządzenie |S |Przypisywanie tożsamości do urządzenia i uwierzytelnianie urządzenia |Zastąpienie urządzenia lub części urządzenia innym urządzeniem. Skąd wiesz, że rozmawiasz z właściwym urządzeniem? |Uwierzytelnianie urządzenia przy użyciu zabezpieczeń warstwy transportowej (TLS) lub PROTOKOŁU IPSec. Infrastruktura powinna obsługiwać przy użyciu klucza wstępnego (PSK) na tych urządzeniach, które nie obsługują pełnej kryptografii asymetrycznej. Korzystanie z usługi Azure AD, [OAuth](https://www.rfc-editor.org/pdfrfc/rfc6755.txt.pdf) |
|| TRID (włas iem |Zastosuj mechanizmy odporne na manipulacje do urządzenia, na przykład, utrudniając wyodrębnienie kluczy i innych materiałów kryptograficznych z urządzenia. |Ryzyko jest, jeśli ktoś jest manipulowanie urządzeniem (zakłócenia fizyczne). Jak się upewniasz, że urządzenie nie zostało naruszone. |Najskuteczniejszym środkiem zaradczym jest funkcja modułu TPM (Trusted Platform Module), która umożliwia przechowywanie kluczy w specjalnych obwodach na chipie, z których klucze nie mogą być odczytywane, ale mogą być używane tylko do operacji kryptograficznych, które używają klucza, ale nigdy nie ujawniają klucza. Szyfrowanie pamięci urządzenia. Zarządzanie kluczami dla urządzenia. Podpisywanie kodu. |
|| E |Posiadanie kontroli dostępu urządzenia. Schemat autoryzacji. |Jeśli urządzenie pozwala na wykonywanie poszczególnych akcji na podstawie poleceń ze źródła zewnętrznego, a nawet czujników zagrożonych, umożliwia atakowi wykonywanie operacji, które nie są dostępne w inny sposób. |Mając schemat autoryzacji dla urządzenia |
| Brama polowa |S |Uwierzytelnianie bramy pola do bramy w chmurze (na przykład oparte na cert, PSK lub na podstawie oświadczeń). |Jeśli ktoś może sfałszować Field Gateway, może zaprezentować się jako dowolne urządzenie. |TLS RSA/PSK, IPSec, [RFC 4279](https://tools.ietf.org/html/rfc4279). Wszystkie te same problemy związane z przechowywaniem kluczy i zaświadczaniem urządzeń w ogóle - najlepszym przypadkiem jest użycie modułu TPM. 6LowPAN dla PROTOKOŁU IPSec do obsługi sieci czujników bezprzewodowych (WSN). |
|| TRID (włas iem |Ochrona bramy polowej przed manipulacją (TPM?) |Fałszowanie ataków, które oszukują bramę chmury myśląc, że rozmawia z bramą polowej, może spowodować ujawnienie informacji i manipulowanie danymi |Szyfrowanie pamięci, moduł TPM, uwierzytelnianie. |
|| E |Mechanizm kontroli dostępu dla bramy polej | | |

Oto kilka przykładów zagrożeń w tej kategorii:

**Fałszowanie:** Osoba atakująca może wyodrębnić materiał klucza kryptograficznego z urządzenia na poziomie oprogramowania lub sprzętu, a następnie uzyskać dostęp do systemu za pomocą innego urządzenia fizycznego lub wirtualnego pod tożsamością urządzenia, z których pobrano materiał klucza.

**Odmowa usługi**: Urządzenie może być niezdolne do działania lub komunikowania się poprzez zakłócanie częstotliwości radiowych lub cięcia przewodów. Na przykład kamera nadzoru, która celowo miała połączenie zasilania lub sieci, nie może w ogóle zgłaszać danych.

**Manipulowanie**: Osoba atakująca może częściowo lub całkowicie zastąpić oprogramowanie uruchomione na urządzeniu, potencjalnie umożliwiając zastąpione oprogramowanie w celu wykorzystania prawdziwej tożsamości urządzenia, jeśli materiał klucza lub obiekty kryptograficzne zawierające kluczowe materiały były dostępne dla nielegalnego programu.

**Manipulowanie**: Kamera monitoringu, która pokazuje obraz z widocznym widmem pustego korytarza, może być skierowana na zdjęcie takiego korytarza. Czujnik dymu lub ognia może zgłaszać kogoś trzymającego pod nim zapalniczkę. W obu przypadkach urządzenie może być technicznie w pełni godne zaufania do systemu, ale zgłasza manipulowane informacje.

**Manipulowanie:** Osoba atakująca może wykorzystać wyodrębniony materiał klucza do przechwycenia i stłumienia danych z urządzenia na ścieżce komunikacji i zastąpić je fałszywymi danymi uwierzytelnionymi skradzionym materiałem klucza.

**Manipulowanie**: Osoba atakująca może częściowo lub całkowicie zastąpić oprogramowanie uruchomione na urządzeniu, potencjalnie umożliwiając zastąpione oprogramowanie w celu wykorzystania prawdziwej tożsamości urządzenia, jeśli materiał klucza lub obiekty kryptograficzne zawierające kluczowe materiały były dostępne dla nielegalnego programu.

**Ujawnienie informacji**: Jeśli urządzenie jest uruchomione zmanipulowanym oprogramowaniem, takie zmanipulowane oprogramowanie może potencjalnie wyciekać dane do nieautoryzowanych stron.

**Ujawnienie informacji:** Osoba atakująca może wykorzystać wyodrębniony materiał klucza, aby wstrzyknąć się do ścieżki komunikacji między urządzeniem a administratorem lub bramą polową lub bramą w chmurze, aby syfonować informacje.

**Odmowa usługi:** Urządzenie można wyłączyć lub przekształcić w tryb, w którym komunikacja nie jest możliwa (co jest zamierzone w wielu maszynach przemysłowych).

**Manipulowanie**: Urządzenie można ponownie skonfigurować tak, aby działało w stanie nieznanym systemowi sterowania (poza znanymi parametrami kalibracji), a tym samym dostarczać dane, które mogą być błędnie zinterpretowane

**Podniesienie uprawnień:** Urządzenie, które wykonuje określoną funkcję, może zostać zmuszone do zrobienia czegoś innego. Na przykład zawór, który jest zaprogramowany do otwierania w połowie drogi, można oszukać, aby otworzyć całą drogę.

**Odmowa usługi:** Urządzenie można przekształcić w stan, w którym komunikacja nie jest możliwa.

**Manipulowanie**: Urządzenie można ponownie skonfigurować tak, aby działało w stanie nieznanym systemowi sterowania (poza znanymi parametrami kalibracji), a tym samym dostarczać dane, które mogą być błędnie zinterpretowane.

**Spoofing/Tampering/Repudiation**: Jeśli nie jest zabezpieczony (co rzadko ma miejsce w przypadku pilotów zdalnego sterowania konsumenta), osoba atakująca może anonimowo manipulować stanem urządzenia. Dobrą ilustracją jest pilotów, które można włączyć dowolny telewizor i które są popularne narzędzia żartownisia.

#### <a name="communication"></a>Komunikacja

Zagrożenia wokół ścieżki komunikacji między urządzeniami, urządzeniami i bramami polowymi oraz bramami urządzeń i chmury. Poniższa tabela zawiera wskazówki dotyczące otwartych gniazd na urządzeniu/sieci VPN:

| **Składnik** | **Zagrożenie** | **Środki zaradcze** | **Ryzyko** | **Wdrażanie** |
| --- | --- | --- | --- | --- |
| Centrum IoT urządzenia |Tid |(D) TLS (PSK/RSA) w celu zaszyfrowania ruchu |Podsłuchiwanie lub zakłócanie komunikacji między urządzeniem a bramą |Zabezpieczenia na poziomie protokołu. W niestandardowych protokołach musisz dowiedzieć się, jak je chronić. W większości przypadków komunikacja odbywa się z urządzenia do usługi IoT Hub (urządzenie inicjuje połączenie). |
| Urządzenie do urządzenia |Tid |(D) TLS (PSK/RSA) do szyfrowania ruchu. |Odczytywanie danych przesyłane między urządzeniami. Manipulowanie danymi. Przeciążanie urządzenia nowymi połączeniami |Zabezpieczenia na poziomie protokołu (MQTT/AMQP/HTTP/CoAP. W niestandardowych protokołach musisz dowiedzieć się, jak je chronić. Ograniczenie dla zagrożenia DoS jest równorzędne urządzenia za pośrednictwem bramy chmury lub pola i mają je działać tylko jako klienci do sieci. Komunikacja równorzędna może spowodować bezpośrednie połączenie między elementami równorzędnymi po tym, jak została wynegocjowana przez bramę |
| Urządzenie jednostki zewnętrznej |Tid |Silne parowanie elementu zewnętrznego z urządzeniem |Podsłuchiwanie połączenia z urządzeniem. Zakłócanie komunikacji z urządzeniem |Bezpieczne parowanie zewnętrznego urządzenia z urządzeniem NFC/Bluetooth LE. Sterowanie panelem operacyjnym urządzenia (fizyczne) |
| Brama w chmurze bramy polej |Tid |TLS (PSK/RSA) do szyfrowania ruchu. |Podsłuchiwanie lub zakłócanie komunikacji między urządzeniem a bramą |Zabezpieczenia na poziomie protokołu (MQTT/AMQP/HTTP/CoAP). W niestandardowych protokołach musisz dowiedzieć się, jak je chronić. |
| Brama chmury urządzeń |Tid |TLS (PSK/RSA) do szyfrowania ruchu. |Podsłuchiwanie lub zakłócanie komunikacji między urządzeniem a bramą |Zabezpieczenia na poziomie protokołu (MQTT/AMQP/HTTP/CoAP). W niestandardowych protokołach musisz dowiedzieć się, jak je chronić. |

Oto kilka przykładów zagrożeń w tej kategorii:

**Odmowa usługi:** Urządzenia ograniczone są zazwyczaj pod groźbą DoS, gdy aktywnie nasłuchują połączeń przychodzących lub niechcianych datagramów w sieci, ponieważ osoba atakująca może otworzyć wiele połączeń równolegle i nie obsługiwać ich lub obsługiwać je powoli, lub urządzenie może zostać zalane niechcianym ruchem. W obu przypadkach urządzenie może być skutecznie renderowane niesprawne w sieci.

**Fałszowanie, ujawnianie informacji**: Urządzenia ograniczone i urządzenia specjalnego przeznaczenia często mają jeden na wszystkich urządzeń zabezpieczeń, takich jak hasło lub ochrona pinów, lub całkowicie polegać na zaufaniu do sieci, co oznacza, że udzielają dostępu do informacji, gdy urządzenie jest w tej samej sieci, a ta sieć jest często chroniona tylko przez klucz udostępniony. Oznacza to, że po ujawnieniu udostępnionego klucza tajnego urządzenia lub sieci możliwe jest sterowanie urządzeniem lub obserwowanie danych emitowanych z urządzenia.  

**Podszywanie się**: osoba atakująca może przechwycić lub częściowo zastąpić transmisję i naciągać inicjatora (człowiek w środku)

**Manipulowanie**: osoba atakująca może przechwycić lub częściowo zastąpić transmisję i wysłać fałszywe informacje 

**Ujawnienie informacji:** osoba atakująca może podsłuchiwać emisję i uzyskiwać informacje bez autoryzacji **Odmowa usługi:** osoba atakująca może zablokować sygnał emisji i odmówić dystrybucji informacji

#### <a name="storage"></a>Magazyn

Każde urządzenie i brama polowa ma jakąś formę przechowywania (tymczasowe do kolejkowania danych, system operacyjny (OS) przechowywania obrazu).

| **Składnik** | **Zagrożenie** | **Środki zaradcze** | **Ryzyko** | **Wdrażanie** |
| --- | --- | --- | --- | --- |
| Przechowywanie urządzeń |TRID (włas iem |Szyfrowanie pamięci masowej, podpisywanie dzienników |Odczytywanie danych z magazynu (dane PII), manipulowanie danymi telemetrycznymi. Manipulowanie danymi kontroli poleceń w kolejce lub w pamięci podręcznej. Manipulowanie pakietami aktualizacji konfiguracji lub oprogramowania układowego w pamięci podręcznej lub w kolejce lokalnie może prowadzić do naruszenia zabezpieczeń systemu operacyjnego i/lub składników systemu |Szyfrowanie, kod uwierzytelniania wiadomości (MAC) lub podpis cyfrowy. W miarę możliwości silna kontrola dostępu za pośrednictwem list kontroli dostępu do zasobów (List ACL) lub uprawnień. |
| Obraz systemu operacyjnego urządzenia |TRID (włas iem | |Manipulowanie systemami operacyjnymi/wymiana komponentów systemu operacyjnego |Partycja systemu operacyjnego tylko do odczytu, podpisany obraz systemu operacyjnego, szyfrowanie |
| Magazyn bramy polowej (kolejkowanie danych) |TRID (włas iem |Szyfrowanie pamięci masowej, podpisywanie dzienników |Odczytywanie danych z magazynu (dane PII), manipulowanie danymi telemetrycznymi, manipulowanie danymi kontroli poleceń w kolejce lub w pamięci podręcznej. Manipulowanie pakietami aktualizacji konfiguracji lub oprogramowania układowego (przeznaczonymi dla urządzeń lub bramy polowej) podczas lokalnego buforowania lub umieszczania w kolejce może prowadzić do naruszenia zabezpieczeń systemu operacyjnego i/lub składników systemu |BitLocker |
| Obraz systemu operacyjnego bramy pola |TRID (włas iem | |Manipulowanie systemami operacyjnymi/wymiana komponentów systemu operacyjnego |Partycja systemu operacyjnego tylko do odczytu, podpisany obraz systemu operacyjnego, szyfrowanie |

### <a name="device-and-event-processingcloud-gateway-zone"></a>Strefa przetwarzania urządzeń i zdarzeń/bramy w chmurze

Brama w chmurze to system, który umożliwia zdalną komunikację z i do urządzeń lub bram terenowych z kilku różnych lokacji w przestrzeni sieci publicznej, zazwyczaj w kierunku chmurowego systemu kontroli i analizy danych, federacji takich systemów. W niektórych przypadkach brama w chmurze może natychmiast ułatwić dostęp do urządzeń specjalnego przeznaczenia z terminali, takich jak tablety lub telefony. W kontekście omówionym tutaj "chmura" ma odnosić się do dedykowanego systemu przetwarzania danych, który nie jest związany z tą samą lokacją co podłączone urządzenia lub bramy polowe, i gdzie środki operacyjne uniemożliwiają ukierunkowany dostęp fizyczny, ale niekoniecznie są " infrastruktury chmury publicznej". Brama w chmurze może być potencjalnie mapowana na nakładkę wirtualizacji sieci, aby ocieplić bramę w chmurze i wszystkie dołączone do niej urządzenia lub bramy polowe od innego ruchu sieciowego. Sama brama w chmurze nie jest systemem sterowania urządzeniem ani obiektem przetwarzania lub przechowywania danych urządzenia; tych obiektów interfejsu z bramą chmury. Strefa bramy w chmurze zawiera samą bramę w chmurze wraz ze wszystkimi bramami polowymi i urządzeniami bezpośrednio lub pośrednio do niej dołączonymi.

Brama w chmurze to w większości niestandardowe oprogramowanie działające jako usługa z odsłoniętymi punktami końcowymi, z którymi łączy się brama polowa i urządzenia. Jako takie musi być zaprojektowany z myślą o bezpieczeństwie. Postępuj zgodnie z procesem [SDL](https://www.microsoft.com/sdl) do projektowania i tworzenia tej usługi.

#### <a name="services-zone"></a>Strefa usług

System sterowania (lub kontroler) to rozwiązanie programowe, które łączy się z urządzeniem, bramą polową lub bramą w chmurze w celu kontrolowania jednego lub wielu urządzeń i/lub gromadzenia i/lub przechowywania i/lub analizowania danych urządzenia w celu prezentacji lub późniejszych celów kontrolnych. Systemy kontroli są jedynymi podmiotami w zakresie tej dyskusji, które mogą natychmiast ułatwić interakcję z ludźmi. Wyjątkami są pośrednie fizyczne powierzchnie kontrolne na urządzeniach, takie jak przełącznik, który umożliwia osobie wyłączenie urządzenia lub zmianę innych właściwości i dla których nie ma funkcjonalnego odpowiednika, do którego można uzyskać dostęp cyfrowo.

Pośrednie powierzchnie kontroli fizycznej to te, w których logika rządząca ogranicza funkcję fizycznej powierzchni sterującej, tak aby można było zainicjować równoważną funkcję zdalnie lub uniknąć konfliktów wejściowych ze zdalnym wejściem – takie pośrednie powierzchnie sterowania są koncepcyjnie dołączone do lokalnego systemu sterowania, który wykorzystuje te same podstawowe funkcje, co każdy inny system zdalnego sterowania, do którego urządzenie może być podłączone równolegle. Najważniejsze zagrożenia dla chmury obliczeniowej można odczytać na stronie [Cloud Security Alliance (CSA).](https://cloudsecurityalliance.org/articles/csa-releases-top-threats-to-cloud-computing-deep-dive/)

## <a name="additional-resources"></a>Zasoby dodatkowe

Aby uzyskać więcej informacji zobacz następujące artykuły:

* [Narzędzie do modelowania zagrożeń SDL](https://www.microsoft.com/sdl/adopt/threatmodeling.aspx)
* [Architektura referencyjna IoT platformy Microsoft Azure](https://azure.microsoft.com/updates/microsoft-azure-iot-reference-architecture-available/)
