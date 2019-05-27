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
ms.openlocfilehash: f3e05f213821b053f8cf6abbbc50a14e9ea62295
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66166342"
---
# <a name="internet-of-things-iot-security-architecture"></a>Architektura zabezpieczeń Internetu rzeczy (IoT)

Podczas projektowania systemu, ważne jest zrozumienie potencjalnych zagrożeń dla tego systemu i dodanie odpowiednich mechanizmów obronnych w związku z tym, ponieważ system został zaprojektowany i zaprojektowana. Należy zaprojektować produktu od samego początku z myślą o bezpieczeństwie, ponieważ zrozumienie, jak atakujący może być w stanie naruszyć bezpieczeństwo systemu, pomaga upewnić się, że odpowiednie środki zaradcze są stosowane od samego początku.

## <a name="security-starts-with-a-threat-model"></a>Zabezpieczenia zaczyna się od model zagrożeń

Firma Microsoft długo została użyta modele zagrożeń dla swoich produktów i podejścia biznesowego uczyniło zagrożeń firmy publicznie dostępnych procesu modelowania. Środowisko firmy pokazuje, że modelowania ma nieoczekiwany korzyści po bezpośrednim znajomości jakie zagrożenia są najbardziej dotyczących. Na przykład tworzy również ścieżek Otwórz omówienie z innymi osobami spoza zespołu rozwoju, co może prowadzić do nowych pomysłów i ulepszenia produktu.

Celem modelowanie zagrożeń jest zrozumienie, jak osoba atakująca może być w stanie naruszyć bezpieczeństwo systemu, a następnie upewnij się, że zostały spełnione odpowiednie środki zaradcze. Wymusza modelowania zagrożeń, zespół projektowy, należy wziąć pod uwagę czynniki identycznie systemu, a nie po system jest wdrażany. Ten fakt ma kluczowe znaczenie, ponieważ modernizacji zabezpieczeń mechanizmów obronnych w celu wiele urządzeń, w tym polu jest niewykonalne, występowania błędów i pozostawia klientów na ryzyko.

Wiele zespołów programistycznych do doskonałą zadanie przechwytywania wymagań funkcjonalności dla systemu, które korzystną dla klientów. Jednak zidentyfikowanie-oczywisty sposób czy ktoś może wykorzystywać system jest trudniejsze. Modelowanie zagrożeń może pomóc zespołom programistycznym zrozumieć do czego służą osoba atakująca może i dlaczego. Modelowanie zagrożeń jest strukturą procesu, który tworzy decyzji projektowych dyskusji na temat zabezpieczeń w systemie, a także zmiany w projekcie zostaną wprowadzone po drodze wpływ na zabezpieczenia. Model zagrożeń jest po prostu dokumentu, niniejszy reprezentuje również idealny sposób, aby zapewnić ciągłość działalności biznesowej wiedzy, przechowywania lekcji pokazaliśmy i pomocy nowego zespołu dołączyć szybko. Na koniec wynikiem modelowanie zagrożeń jest umożliwienie innych aspektów zabezpieczeń, takich jak zobowiązań zabezpieczeń, jakie chcesz zapewnić klientom. Te zobowiązania w połączeniu z modelowanie zagrożeń informuje i dysku testowania rozwiązania Internetu rzeczy (IoT).

### <a name="when-to-do-threat-modeling"></a>Kiedy modelowanie wątkowości

[Modelowanie zagrożeń](https://www.microsoft.com/en-us/sdl/adopt/threatmodeling.aspx) zapewnia największą wartość w przypadku, gdy dołączyć go w fazie projektowania. Podczas projektowania, masz największą elastyczność, aby wprowadzić zmiany w celu wyeliminowania zagrożenia. Eliminując zagrożenia zgodnie z projektem jest pożądanego rezultatu. Jest znacznie prostsze niż dodawanie środki zaradcze, ich testowania i zapewnienia są zawsze aktualne, a ponadto takie eliminacji nie zawsze jest możliwe. Trudniej można eliminować zagrożenia, ponieważ produkt staje się bardziej dojrzałych i z kolei ostatecznie wymaga więcej pracy i kompromisy znacznie trudniejsze niż zagrożeń na wczesnym etapie modelowania w trakcie opracowywania.

### <a name="what-to-consider-for-threat-modeling"></a>Weź pod uwagę modelowanie wątkowości

Należy rozważyć rozwiązanie jako całości, a także skoncentrować się na następujących obszarach:

* Funkcje zabezpieczeń i prywatności
* Funkcje, których błędy są istotne zabezpieczeń
* Funkcje, które w ogóle granicy zaufania

### <a name="who-performs-threat-modeling"></a>Kto wykonuje modelowanie zagrożeń

Modelowanie zagrożeń jest procesem, jak każdy inny. To dobry pomysł, aby traktować dokument modelu zagrożeń, takich jak jakikolwiek inny składnik rozwiązania i zweryfikuje go. Wiele zespołów programistycznych do doskonałą zadanie przechwytywania wymagań funkcjonalności dla systemu, które korzystną dla klientów. Jednak zidentyfikowanie-oczywisty sposób czy ktoś może wykorzystywać system jest trudniejsze. Modelowanie zagrożeń może pomóc zespołom programistycznym zrozumieć do czego służą osoba atakująca może i dlaczego.

### <a name="how-to-perform-threat-modeling"></a>Jak wykonać modelowania zagrożeń

Zagrożenia, modelowanie procesu składa się z czterech krokach; dostępne są następujące czynności:

* Model aplikacji
* Wyliczanie zagrożenia
* Eliminuj zagrożenia
* Sprawdź poprawność środkach ograniczania ryzyka

#### <a name="the-process-steps"></a>Kroki procesu

Trzy reguły akceptacji pamiętać podczas tworzenia modelu zagrożeń:

1. Utwórz diagram poza architektury referencyjnej.

2. Uruchom najpierw szeroki zakres. Zapoznaj się z omówieniem i zrozumieć system jako całość, przed rozpoczęciem pracy głębokiego. Takie podejście pomaga upewnić się, że zostanie rozszerzony w odpowiednich miejscach.

3. Proces dysku, nie przegap proces dysku, możesz. Znajdowanie problemu w fazie modelowania, aby go eksplorować. Rozpocznij! Nie zatwierdzanych slavishly wykonaj następujące kroki.

#### <a name="threats"></a>Zagrożenia

Cztery podstawowe elementy model zagrożeń są:

* Procesy, takie jak usługi sieci web, usług systemu Win32, i * nix demonów. Niektóre jednostki złożone (na przykład bram działających w terenie i czujniki) może być usunięte, jako proces, gdy techniczne w Przechodzenie do szczegółów w tych obszarach nie jest możliwe.

* Magazyny danych (wszędzie tam, gdzie dane są przechowywane, np. plik konfiguracji lub bazy danych)

* Przepływ danych (gdzie dane są przenoszone między innymi elementami w aplikacji)

* Podmioty zewnętrzne (wszystkie elementy, które wchodzi w interakcję z systemem, ale nie jest pod kontrolą aplikacji, przykłady obejmują użytkowników oraz urządzeń źródła danych)

Wszystkie elementy na diagramie architektury podlegają zagrożenia; w tym artykule skrót klawiszowy krok. Odczyt [modelowania zagrożeń ponownie, STRIDE](https://blogs.msdn.microsoft.com/larryosterman/2007/09/04/threat-modeling-again-stride/) Aby dowiedzieć się więcej o elementach krok.

Różne elementy na diagramie aplikacja jest zależna od niektórych zagrożenia krok:

* Procesy podlegają STRIDE
* Przepływ danych jest zależna od identyfikatora TID
* Magazyny danych podlegają TID, a czasami R, w przypadku magazynów danych plików dziennika.
* Podmiotów zewnętrznych jest zależna od SRD

## <a name="security-in-iot"></a>Zabezpieczenia w IoT

Połączone urządzenia specjalnych dokonano znaczących potencjalnych obszarów interakcji i wzorce interakcji, które należy rozważyć udostępnia platformę do zabezpieczania cyfrowego dostępu do tych urządzeń. Termin "access cyfrowego" jest używany tutaj do odróżnienia od żadnych operacji, które są przeprowadzane za pośrednictwem urządzenia bezpośrednie interakcje gdzie zabezpieczenia dostępu jest oferowana w ramach kontroli fizyczny dostęp. Na przykład umieszczenie na urządzeniu w pomieszczeniu z blokadą na drzwi biblioteki. Gdy nie można odmówić dostępu fizycznych przy użyciu oprogramowania i sprzętu, można podjąć środki w celu uniemożliwić fizyczny dostęp z wiodących na zakłócenia systemu.

Gdy eksplorujesz wzorce interakcji Przyjrzyj się "control urządzenia" i "dane urządzenie" na tym samym poziomie uwagi. "Urządzenie control" mogą być klasyfikowane jako wszelkie informacje, który został dostarczony do urządzenia przez każdą stronę w celu zmiany lub wywieranie wpływu na jego zachowanie w kierunku jej stan lub stan swojego środowiska. "Dane urządzenie" mogą być klasyfikowane jako wszystkie informacje, które urządzenia emituje do drugiej strony, o stanie i zaobserwowanego stanie jego środowiska.

W celu optymalizacji najlepszych rozwiązań dotyczących zabezpieczeń, zalecane jest, że typowej architektury IoT jest podzielony na kilku składników/stref jako część zagrożeń modelowania wykonywania. Te strefy są w pełni opisane w tej sekcji i obejmują:

* Urządzenia,
* Bramy w terenie
* Bramy w chmurze i
* Usługi.

Strefy są szerokie sposób podzielić rozwiązania; Każda strefa często ma własne wymagania dotyczące danych i uwierzytelniania i autoryzacji. Strefy można służyć do uszkodzenia izolacji i ograniczyć wpływ niski zaufania strefy na wyższe zaufania strefy.

Każda strefa jest rozdzielone przez granicę zaufania, który jest rejestrowany jako kropkowana czerwona linia na poniższym diagramie. Reprezentuje przejście/informacje o danych z jednego źródła do innego. Podczas tego przejścia/informacje o danych może podlegać Spoofing, Tampering, Repudiation, ujawnienie informacji, "odmowa usługi" i podniesienia uprawnień (krok).

![IoT Security Zones](media/iot-security-architecture/iot-security-architecture-fig1.png) 

KROK, włączenie pełnego 360 poddawane są również składniki przedstawiony w ramach każdego widoku rozwiązanie do modelowania zagrożeń. Poniższe sekcje rozwinięcia poszczególne składniki i określonych bezpieczeństwem oraz rozwiązań, które należy wprowadzić w miejscu.

W poniższych sekcjach omówiono składniki standardowe, które zwykle znajdują się w tych strefach.

### <a name="the-device-zone"></a>Strefę urządzenia

Środowisko urządzenia jest natychmiastowe fizycznych ilość wolnego miejsca wokół urządzenia w przypadku, gdy jest to fizyczny dostęp i/lub "sieci lokalnej" peer-to-peer dostęp do wersji cyfrowej na urządzeniu jest możliwe. "Sieć lokalna" zakłada się, że sieć jest odrębna i odizolowanego od — ale potencjalnie mostkowania do publicznej sieci Internet, która obejmuje wszystkie technologię urządzenia bezprzewodowego radiowego, która zezwala na komunikację peer-to-peer urządzeń. Robi *nie* obejmują dowolnej technologii wirtualizacji sieci, tworzenia żądającym sieci lokalnej i również nie obejmować sieci operator publiczny, które wymagają żadne dwa urządzenia komunikują się przez sieć publiczna przestrzeń, jeśli były wprowadź relacji komunikacji peer-to-peer.

### <a name="the-field-gateway-zone"></a>Strefa bramy pola

Bramy w terenie jest urządzeń/urządzenia lub niektóre oprogramowanie komputerowe serwera ogólnego przeznaczenia, który działa jako włącznik komunikacji oraz w razie potrzeby jako system kontroli urządzenia, a następnie centrum przetwarzania danych urządzenia. Strefa bramy pola obejmuje bramy w terenie, sama i wszystkie urządzenia, które są dołączone do niego. Jak wskazuje nazwa, bram działających w terenie działają urządzenia zewnętrzne dedykowane przetwarzania danych, są zazwyczaj powiązane lokalizacji, potencjalnie podlegają fizycznymi włamaniami i ma ograniczoną nadmiarowości operacyjnej. Wszystko na powiedzieć, że bramy w terenie często jest to jedna touch i przeszkadzają codzienne jego funkcją jest.

Bramy w terenie różni się od routera ledwie ruchu, że posiada ona aktywną rolę w zarządzaniu dostępem i przepływem informacji, co oznacza, że jest to aplikacja rozwiązany jednostki i połączenie sieciowe lub sesję terminalu. Urządzenie NAT lub zaporą, natomiast nie kwalifikuje się jako bram działających w terenie, ponieważ nie są one jawne połączenie lub terminale sesji, ale raczej połączeń trasy (lub blok) lub za pośrednictwem ich sesji. Bramy w terenie ma dwa różne obszary powierzchni. Jeden twarzy urządzeń, które są dołączone do niego i reprezentuje wewnątrz strefy, a druga twarzy wszystkie podmioty zewnętrzne co stanowi krawędzi strefy.

### <a name="the-cloud-gateway-zone"></a>Strefa bramy chmury

Brama chmury to system, który umożliwia zdalnej komunikacji z i do urządzenia lub bram działających w terenie z wielu różnych lokacji w sieci publicznej przestrzeni, zwykle w kierunku kontroli opartej na chmurze i system analizy danych, biuro w Federacji takich systemów. W niektórych przypadkach brama chmury może natychmiast ułatwienia dostępu do urządzeń specjalnych z terminali, takich jak tablety lub telefony. W kontekście omówionych w tym miejscu "chmura" jest przeznaczona do odwoływania się do systemu dedykowanych przetwarzania danych, który nie jest powiązany z tym samym miejscu co podłączonych urządzeń lub bram działających w terenie. Również w strefie chmury, środki operacyjne uniemożliwić docelowych fizyczny dostęp i niekoniecznie nie są widoczne w infrastrukturze "chmura publiczna".  

Brama chmury potencjalnie mogą być mapowane na nakładce wirtualizacji sieci, aby uwolnić przez bramę chmury i wszystkich podłączonych urządzeń lub bram działających w terenie od innego ruchu sieciowego. Brama chmury nie jest systemem kontroli urządzenia lub przetwarzania lub magazynu danych dotyczących urządzeń; Interfejs tych urządzeń przy użyciu bramy w chmurze. Strefa bramy chmury obejmuje brama chmury wraz ze wszystkich bram działających w terenie i bezpośrednio lub pośrednio dołączone do niego urządzenia. Krawędzi strefy jest różne obszar powierzchni, gdzie komunikują się za pośrednictwem wszystkich podmiotów zewnętrznych.

### <a name="the-services-zone"></a>Strefa usługi

"Usługa" jest zdefiniowany dla tego kontekstu jako części oprogramowania lub moduł, który komunikuje się z urządzeniami za pośrednictwem bramy pola lub chmury i przeanalizuje dane, a także dla poleceń i kontroli. Usługi są mediatorów. One działa na podstawie ich tożsamości kierunku bram i innych podsystemów, przechowywanie i analizowanie danych, autonomicznie wysyłania poleceń do urządzeń na podstawie analizy danych lub harmonogramów i spowodować ujawnienie informacji i kontrolowanie możliwości do autoryzowanych użytkowników końcowych.

### <a name="information-devices-versus-special-purpose-devices"></a>Informacje o urządzeniach a specjalnymi urządzeniami

Komputery, telefony i tablety są głównie interaktywne informacji urządzenia. Telefony komórkowe i tablety są jawnie zoptymalizowane pod kątem wokół maksymalizując okres istnienia baterii. One najlepiej wyłączyć częściowo podczas nie od razu interakcji z osobą, lub gdy nie udostępniają usługi takie jak odtwarzanie muzyki lub przeprowadzi ich właściciela do określonej lokalizacji. Z punktu widzenia systemy te informacje o technologii urządzenia głównie pełnią funkcję serwerów proxy do osób. Są to "ludzie elementy wykonawcze" sugerujące, akcje i "osoby czujniki" zbieranie danych wejściowych.

Specjalnymi urządzeniami, od czujników temperatury proste linii produkcyjnych fabryki złożonych z tysiącami elementów wewnątrz nich różnią się. Te urządzenia są bardziej ograniczone w celu, a nawet wtedy, gdy zapewniają część interfejsu użytkownika, dużej mierze są ograniczone do komunikacji z usługą lub być zintegrowane z zasobami w świecie fizycznym. Ich pomiaru i zgłaszać okoliczności środowiska, Włącz zawory, kontrolować servos, dźwiękowych alarmy, Przełącz światła i wykonywać inne zadania. Aby pracować z nimi pomagają, dla których urządzenie z systemem informacji jest zbyt ogólna, zbyt kosztowne, zbyt duże lub zbyt kruchy. Natychmiast konkretnego celu decyduje o ich projektu technicznego, również dostępne pieniężnych budżet ich produkcji i okresu istnienia zaplanowanych operacji. Kombinacja tych dwóch czynników klucza ogranicza dostępne operacyjnej budżetu energii, zużycia fizycznych i związku z tym dostępną pamięć, obliczeń i funkcje zabezpieczeń.

Jeśli coś "zbliża się problem" Automatyczna lub z zdalnego kontrolowania urządzeń, na przykład wady fizycznych lub logicznych kontroli wady willful nieautoryzowanego dostępu i manipulowania. Wiele produkcyjnych może zostać zniszczone, budynki może być looted lub wbudowany w dół i osób może być poszkodowana, a nawet struktury. Jest to zupełnie inne klasy szkodę niż ktoś maxing się limit skradzionej karty kredytowej. Na pasku zabezpieczeń dla urządzeń, które należy przenieść elementy, a także do danych z czujników, które ostatecznie powoduje przekroczenie poleceń, które powodują rzeczy przenieść, musi być wyższa niż w handlu elektronicznego lub scenariusz bankowości.

### <a name="device-control-and-device-data-interactions"></a>Kontroli urządzeń i interakcje z danymi urządzenia

Połączone urządzenia specjalnych dokonano znaczących potencjalnych obszarów interakcji i wzorce interakcji, które należy rozważyć udostępnia platformę do zabezpieczania cyfrowego dostępu do tych urządzeń. Termin "access cyfrowego" jest używany tutaj do odróżnienia od żadnych operacji, które są przeprowadzane za pośrednictwem urządzenia bezpośrednie interakcje gdzie zabezpieczenia dostępu jest oferowana w ramach kontroli fizyczny dostęp. Na przykład umieszczenie na urządzeniu w pomieszczeniu z blokadą na drzwi biblioteki. Gdy nie można odmówić dostępu fizycznych przy użyciu oprogramowania i sprzętu, można podjąć środki w celu uniemożliwić fizyczny dostęp z wiodących na zakłócenia systemu.

Gdy eksplorujesz wzorce interakcji, Przyjrzyj się "control urządzenia" i "urządzenie danych" na tym samym poziomie uwagi podczas modelowania zagrożeń. "Urządzenie control" mogą być klasyfikowane jako wszelkie informacje, który został dostarczony do urządzenia przez każdą stronę w celu zmiany lub wywieranie wpływu na jego zachowanie w kierunku jej stan lub stan swojego środowiska. "Dane urządzenie" mogą być klasyfikowane jako wszystkie informacje, które urządzenia emituje do drugiej strony, o stanie i zaobserwowanego stanie jego środowiska.

## <a name="performing-threat-modeling-for-the-azure-iot-reference-architecture"></a>Wykonywanie dla architektura referencyjna IoT platformy Azure do modelowania zagrożeń

Firma Microsoft używa framework opisane wcześniej w celu dla usługi Azure IoT do modelowania zagrożeń. Poniższa sekcja używa konkretny przykład architektura referencyjna IoT platformy, aby zademonstrować, jak wziąć pod uwagę dla IoT do modelowania zagrożeń i jak rozwiązać zagrożeniami zidentyfikowanymi. W tym przykładzie identyfikuje cztery główne obszary koncentracji uwagi:

* Urządzeniami i źródłami danych
* Transport danych
* Urządzenia i przetwarzania zdarzeń i
* Prezentacja

![Dla usługi Azure IoT do modelowania zagrożeń](media/iot-security-architecture/iot-security-architecture-fig2.png)

Na poniższym diagramie przedstawiono uproszczony widok architektury IoT firmy Microsoft przy użyciu modelu Diagram przepływu danych, który jest używany przez narzędzie do modelowania zagrożeń firmy Microsoft:

![Dla usługi Azure IoT przy użyciu narzędzia do modelowania zagrożeń MS do modelowania zagrożeń](media/iot-security-architecture/iot-security-architecture-fig3.png)

Należy zauważyć, że architektura oddziela możliwości urządzenia i bramy. Takie podejście umożliwia użytkownikowi korzystanie z urządzenia bramy, które są bardziej bezpieczne: są w stanie komunikować się z bramy w chmurze przy użyciu protokołów bezpieczny, co zwykle wymaga większej przetwarzania, natywne urządzenia — na przykład termostat — można Podaj samodzielnie. W strefie usługi Azure założono, że bramy w chmurze jest reprezentowana przez usługę Azure IoT Hub.

### <a name="device-and-data-sourcesdata-transport"></a>Transport źródeł/danych urządzenia i danych

W tej sekcji przedstawiono architekturę opisanych zapewniający modelowanie zagrożeń i zawiera omówienie sposobu rozwiązać niektóre problemy związane. Ten przykład koncentruje się na podstawowych elementów modelu zagrożeń:

* Przetwarza (zarówno w obszarze usługi kontroli i elementy zewnętrzne)
* Komunikacja (nazywane również przepływów danych)
* Magazyn (nazywany także magazyny danych)

#### <a name="processes"></a>Procesy

W każdej kategorii, opisane w architekturze usługi Azure IoT, w tym przykładzie próbuje zmniejszyć liczbę różnych zagrożeń na różnych etapach/informacje o danych istnieje w: proces, komunikacji i magazynu. Poniżej przedstawiono omówienie najbardziej typowymi dla kategorii "procesu", następuje omówienie, jak te zagrożenia mogą najlepiej skorygowane:

**Fałszowanie (S)**: Osoba atakująca może wyodrębnić materiału klucza kryptograficznego z urządzenia, albo w oprogramowania lub poziomie sprzętu, a następnie dostępu do systemu za pomocą innego urządzenia fizycznego lub wirtualnego z tożsamością urządzenia materiału klucza jest zajęty z. Dobre ilustracja to zdalnego sterowania, który można wyłączyć wszelkie TV i które są prankster popularnych narzędzi.

**Odmowa usługi (D)**: Urządzenie można pozbawić możliwości funkcjonowania lub komunikowania się przez zakłócenie częstotliwości radiowych lub przecięcie przewodów. Na przykład kamera nadzoru wizyjnego, którą celowo pozbawiono zasilania lub połączenia sieciowego, nie może w ogóle zgłaszać danych.

**Manipulowanie (T)**: Atakujący może częściowo lub całkowicie zastąpić oprogramowanie działające na urządzeniu, potencjalnie umożliwiając temu oprogramowaniu wykorzystanie prawdziwej tożsamości urządzenia, jeśli dane dotyczące klucza lub urządzenia kryptograficzne przechowujące dane dotyczące klucza były dostępne dla takiego nielegalnego programu. Na przykład osoba atakująca może wykorzystać wyodrębnione materiału klucza do przechwycenia i pominąć dane z urządzenia do ścieżki komunikacji i zastąp go false dane, które jest uwierzytelniana przy użyciu skradzionych materiału klucza.

**Ujawnienie informacji, (I)**: Jeśli urządzenie działa manipulować oprogramowania, takie oprogramowanie manipulować potencjalnie mogą spowodować przeciek tych danych nieautoryzowanym osobom. Na przykład osoba atakująca może wykorzystać wyodrębnione materiału klucza iniekcję się do ścieżki komunikacji między urządzenia i bramy kontrolera lub pola lub brama chmury do Lewarek poza informacji.

**Podniesienie poziomu uprawnień (E)**: Urządzenie realizujące określoną funkcję może zostać zmuszone do robienia czegoś innego. Na przykład można zwiódł już zawór, który jest zaprogramowane, aby otworzyć połowie, aby otworzyć aż.

| **Składnik** | **Przed zagrożeniami** | **Środki zaradcze** | **Ryzyko** | **Implementacja** |
| --- | --- | --- | --- | --- |
| Urządzenie |N |Przypisywanie tożsamości na urządzeniu i uwierzytelniania urządzenia |Wymiana urządzenia lub częścią urządzenia za pomocą innego urządzenia. Skąd wiadomo, że mówienia na właściwym urządzeniu? |Uwierzytelniania urządzenia, za pomocą zabezpieczeń TLS (Transport Layer) lub protokołu IPSec. Infrastruktura powinien obsługiwać za pomocą klucz wstępny (PSK) na tych urządzeniach, które nie obsługują pełne asymetrycznego kryptografii. Korzystać z usługi Azure AD [OAuth](https://www.rfc-editor.org/pdfrfc/rfc6755.txt.pdf) |
|| TRID |Zastosować tamperproof mechanizmy do urządzenia, na przykład, co utrudnia się niemożliwe do wyodrębnienia z urządzenia kluczy i innymi materiałami kryptograficznymi. |Ryzyko jest, jeśli ktoś jest manipulowanie nimi urządzeń (zakłócenia fizycznej). Jak na pewno, że urządzenie nie została naruszona. |Najbardziej efektywne środki zaradcze jest TPM możliwości module (TPM), która umożliwia przechowywanie kluczy w specjalnych obwody na układ, z którego nie można odczytać kluczy, ale można używać tylko dla operacji kryptograficznych, które używają klucza, ale nigdy nie ujawni klucz. Szyfrowanie pamięci urządzenia. Zarządzanie kluczami dla tego urządzenia. Podpisywanie kodu. |
|| E |Zapewniający kontrolę dostępu do urządzenia. Schemat autoryzacji. |Jeśli urządzenie umożliwia poszczególne akcje do wykonania zależnie od polecenia ze źródła zewnętrznego lub nawet ze złamanymi zabezpieczeniami czujniki, umożliwia ataku wykonywać operacje gdzie indziej dostępny. |Posiadanie schemat autoryzacji dla urządzenia |
| Bramy w terenie |N |Uwierzytelnianie bramy w terenie bramy w chmurze (takie jak certyfikat na podstawie, klucza Wstępnego, lub oparta na oświadczeniach). |Jeśli ktoś podszywały się pod bramy w terenie, następnie go może być wyświetlany jako dowolnego urządzenia. |TLS RSA/PSK, IPSec, [RFC 4279](https://tools.ietf.org/html/rfc4279). Te same Najważniejsze kwestie magazynu i zaświadczenie urządzeń znajdujących się w ogólne — Najlepszy przypadek jest używanie modułu TPM. Rozszerzenie 6LowPAN protokołu IPSec do obsługi bezprzewodowej sieci czujników (WSN). |
|| TRID |Ochrona bramy w terenie przed naruszeniem (TPM)? |Fałszowanie atakami nakłonienia myśleć bramy chmury, które rozmawia bramy w terenie może spowodować ujawnienie informacji i nieuprawnionej modyfikacji danych |Pamięć szyfrowania, modułu TPM firmy, uwierzytelniania. |
|| E |Mechanizm kontroli dostępu dla bramy w terenie | | |

Poniżej przedstawiono kilka przykładów zagrożenia w tej kategorii:

**Fałszowanie**: Osoba atakująca może wyodrębnić materiału klucza kryptograficznego z urządzenia, albo w oprogramowania lub poziomie sprzętu, a następnie dostępu do systemu za pomocą innego urządzenia fizycznego lub wirtualnego z tożsamością urządzenia materiału klucza jest zajęty z.

**Odmowa usługi**: Urządzenie można pozbawić możliwości funkcjonowania lub komunikowania się przez zakłócenie częstotliwości radiowych lub przecięcie przewodów. Na przykład kamera nadzoru wizyjnego, którą celowo pozbawiono zasilania lub połączenia sieciowego, nie może w ogóle zgłaszać danych.

**Manipulowanie**: Atakujący może częściowo lub całkowicie zastąpić oprogramowanie działające na urządzeniu, potencjalnie umożliwiając temu oprogramowaniu wykorzystanie prawdziwej tożsamości urządzenia, jeśli dane dotyczące klucza lub urządzenia kryptograficzne przechowujące dane dotyczące klucza były dostępne dla takiego nielegalnego programu.

**Manipulowanie**: Kamera nadzoru, która jest wyświetlany obraz widoczne spektrum pusty korytarzowych może mieć na celu fotografii takich korytarzowych. Czujnik dymu lub ognia może zgłaszać ktoś zawierający jaśniejszego pod nim. W obu przypadkach urządzenie może być z technicznego punktu widzenia pełni godna zaufania do systemu, ale zgłasza manipulować informacji.

**Manipulowanie**: Osoba atakująca może wykorzystać wyodrębnione materiału klucza do przechwycenia i pominąć dane z urządzenia do ścieżki komunikacji i zastąp go false dane, które jest uwierzytelniana przy użyciu skradzionych materiału klucza.

**Manipulowanie**: Osoba atakująca może całkowicie lub częściowo zastąpić oprogramowanie działające na urządzeniu, potencjalnie umożliwiając zamieniono oprogramowania wykorzystywać oryginalnego tożsamości urządzenia materiału klucza lub funkcji kryptograficznych zawierający kluczy materiały były dostępne Aby program nielegalnemu dostępowi.

**Ujawnienie informacji**: Jeśli urządzenie działa manipulować oprogramowania, takie oprogramowanie manipulować potencjalnie mogą spowodować przeciek tych danych nieautoryzowanym osobom.

**Ujawnienie informacji**: Osoba atakująca może wykorzystać wyodrębnione materiału klucza iniekcję się do ścieżki komunikacji między urządzenia i bramy kontrolera lub pola lub brama chmury do Lewarek poza informacji.

**Odmowa usługi**: Urządzenie może wyłączone lub włączone w trybie, gdy komunikacja nie jest możliwe (jest to zamierzone, w wielu maszyn przemysłowych).

**Manipulowanie**: Urządzenie można tak skonfigurować, aby działają w stan nieznany system kontroli (poza odwzorowania znane parametry), a zatem mogą udostępniać dane, które mogą zostać błędnie zinterpretowane

**Podniesienie uprawnień**: Urządzenie realizujące określoną funkcję może zostać zmuszone do robienia czegoś innego. Na przykład można zwiódł już zawór, który jest zaprogramowane, aby otworzyć połowie, aby otworzyć aż.

**Odmowa usługi**: Urządzenia mogą być uwzględniane w stanie, gdy komunikacja nie jest możliwe.

**Manipulowanie**: Urządzenia mogą być konfigurowane, działają w stan nieznany system kontroli (poza odwzorowania znane parametry), a zatem mogą udostępniać dane, które mogą zostać błędnie zinterpretowane.

**Fałszowanie/naruszeniem/odrzucenie**: Jeśli nie jest zabezpieczone (czyli rzadko w przypadku klienta zdalnego sterowania), osoba atakująca może anonimowo manipulowania stan urządzenia. Dobre ilustracja to zdalnego sterowania, który można wyłączyć wszelkie TV i które są prankster popularnych narzędzi.

#### <a name="communication"></a>Komunikacja

Zagrożenia wokół ścieżka komunikacji między urządzeniami, urządzeń i bram działających w terenie i bramy urządzeń i w chmurze. Poniższa tabela zawiera pewne wskazówki wokół otwarte gniazda na urządzenie/sieci VPN:

| **Składnik** | **Przed zagrożeniami** | **Środki zaradcze** | **Ryzyko** | **Implementacja** |
| --- | --- | --- | --- | --- |
| Urządzenia IoT Hub |IDENTYFIKATORA TID |(D) TLS (PSK/RSA) do szyfrowania ruchu sieciowego |Podsłuchiwaniu lub zakłóca komunikacji między urządzeniem i bramą |Zabezpieczenia na poziomie protokołu. Dzięki protokoły niestandardowe musisz dowiedzieć się, jak można je chronić. W większości przypadków komunikacja odbywa się z urządzenia do Centrum IoT Hub (urządzenie inicjuje połączenie). |
| Urządzenie do urządzenia |IDENTYFIKATORA TID |(D) (PSK/RSA) do szyfrowania ruchu TLS. |Odczytywanie danych przesyłanych między urządzeniami. Manipulowanie danymi. Przeciążanie urządzenia przy użyciu nowych połączeń |Zabezpieczenia na poziomie protokołu (MQTT/AMQP/HTTP/CoAP. Dzięki protokoły niestandardowe musisz dowiedzieć się, jak można je chronić. Ograniczania ryzyka dla zagrożeń DoS ma nawiązać komunikację równorzędną między urządzeniami za pośrednictwem bramy chmury lub pola i je tylko act jako klientów do sieci. Komunikacja równorzędna może spowodować bezpośrednie połączenie między komputerami równorzędnymi po o zostały obsługiwanych przez brokera przez bramę |
| Urządzenie zewnętrznej jednostki |IDENTYFIKATORA TID |Silne parowania z zewnętrznej jednostki do urządzenia |Podsłuchiwaniu połączenia z urządzeniem. Zakłóca komunikacji z urządzeniem |Bezpiecznie parowanie zewnętrznej jednostki na urządzeniu NFC/Bluetooth LE. Kontrolowanie panelu operacyjnej urządzenia (fizyczny) |
| Bramy w terenie bramy chmury |IDENTYFIKATORA TID |(PSK/RSA) do szyfrowania ruchu TLS. |Podsłuchiwaniu lub zakłóca komunikacji między urządzeniem i bramą |Zabezpieczenia na poziomie protokołu (MQTT/AMQP/HTTP/CoAP). Dzięki protokoły niestandardowe musisz dowiedzieć się, jak można je chronić. |
| Urządzenie bramy w chmurze |IDENTYFIKATORA TID |(PSK/RSA) do szyfrowania ruchu TLS. |Podsłuchiwaniu lub zakłóca komunikacji między urządzeniem i bramą |Zabezpieczenia na poziomie protokołu (MQTT/AMQP/HTTP/CoAP). Dzięki protokoły niestandardowe musisz dowiedzieć się, jak można je chronić. |

Poniżej przedstawiono kilka przykładów zagrożenia w tej kategorii:

**Odmowa usługi**: Ograniczone urządzeń z systemem ogólnie DoS zagrożeń podczas ich aktywnie nasłuchuje połączeń przychodzących lub niechciane datagramów w sieci, ponieważ osoba atakująca może otwierać wiele połączeń w sposób równoległy i nie usługa je lub usługi ich powoli lub urządzenie może zostać nadmiernej ilości danych z niepożądanym ruchem sieciowym. W obu przypadkach urządzenia mogą skutecznie przestać działać w sieci.

**Fałszowania, ujawnienia informacji**: Ograniczone i urządzenia specjalnych często mają zabezpieczeń jeden dla wszystkich urządzeń, takich jak hasła lub numeru PIN ochrony lub całkowicie polegać na ufanie sieci, co oznacza, że przyznać dostęp do informacji, gdy urządzenie znajduje się w tej samej sieci i sieci często tylko jest chroniony przy użyciu klucza wstępnego. Oznacza to, gdy nie są ujawniane wspólny klucz tajny na urządzeniu lub w sieci, istnieje możliwość kontrolę na urządzeniem lub obserwować dane emitowane przez urządzenie.  

**Fałszowanie**: osoba atakująca może przechwycić lub częściowo zastąpić emisji i podszywały się pod inicjatorem (człowiek pośrodku)

**Manipulowanie**: osoba atakująca może przechwycić lub częściowo zastąpić emisji i wysłać fałszywe informacje 

**Ujawnienie informacji:** osoba atakująca może podsłuchiwać emisji i uzyskiwania informacji bez autoryzacji **"odmowa usługi":** osoba atakująca może jam emisji sygnału i odmawiać go dystrybucji informacji

#### <a name="storage"></a>Magazyn

Każdego urządzenia i pole brama ma jakiegoś typu magazynu (tymczasowe dla usługi kolejkowania danych, magazyn obrazów systemu operacyjnego (OS)).

| **Składnik** | **Przed zagrożeniami** | **Środki zaradcze** | **Ryzyko** | **Implementacja** |
| --- | --- | --- | --- | --- |
| Urządzenia pamięci masowej |TRID |Szyfrowanie magazynu, podpisywania dzienniki |Odczytywanie danych z magazynu (dane osobowe), manipulowanie danymi telemetrii. Manipulowanie w kolejce lub polecenia kontroli danych w pamięci podręcznej. Manipulowanie pakietów aktualizacji konfiguracji lub oprogramowania układowego podczas buforowane lub lokalnie w kolejce może prowadzić do składników systemu operacyjnego i/lub system naruszenia bezpieczeństwa |Szyfrowanie, kod uwierzytelniania wiadomości (MAC) lub podpis cyfrowy. Gdy kontrola dostępu możliwa, silne za pośrednictwem dostępu do zasobów kontrolować list (kontroli dostępu ACL) lub uprawnień. |
| Obraz systemu operacyjnego urządzenia |TRID | |Manipulowanie systemu operacyjnego / zastępowanie składników systemu operacyjnego |Tylko do odczytu partycji systemu operacyjnego podpisany obrazu systemu operacyjnego, szyfrowania |
| Magazyn bramy w terenie (kolejkowania danych) |TRID |Szyfrowanie magazynu, podpisywania dzienniki |Odczytywanie danych z magazynu (dane osobowe), manipulowanie danymi telemetrii ingerencji w kolejce lub polecenia kontroli danych w pamięci podręcznej. Manipulowanie pakietów aktualizacji konfiguracji lub oprogramowania układowego, (przeznaczone dla urządzeń lub bramy w terenie) podczas buforowane lub lokalnie w kolejce może prowadzić do składników systemu operacyjnego i/lub system naruszenia bezpieczeństwa |BitLocker |
| Obraz systemu operacyjnego bramy pola |TRID | |Manipulowanie systemu operacyjnego / zastępowanie składników systemu operacyjnego |Tylko do odczytu partycji systemu operacyjnego podpisany obrazu systemu operacyjnego, szyfrowania |

### <a name="device-and-event-processingcloud-gateway-zone"></a>Urządzeń i zdarzeń strefy bramy przetwarzania/w chmurze

Brama chmury to system, który umożliwia zdalnej komunikacji z i do urządzenia lub bram działających w terenie z wielu różnych lokacji w sieci publicznej przestrzeni, zwykle w kierunku kontroli opartej na chmurze i system analizy danych, biuro w Federacji takich systemów. W niektórych przypadkach brama chmury może natychmiast ułatwienia dostępu do urządzeń specjalnych z terminali, takich jak tablety lub telefony. W kontekście omówionych w tym miejscu "chmura" jest przeznaczona do odwoływania się do systemu dedykowanych przetwarzania danych, który nie jest powiązany z tym samym miejscu co podłączonych urządzeń lub bram działających w terenie i gdzie środki operacyjne uniemożliwić docelowych fizyczny dostęp, ale nie jest koniecznie do " Infrastruktura chmury publicznej". Brama chmury potencjalnie mogą być mapowane na nakładce wirtualizacji sieci, aby uwolnić przez bramę chmury i wszystkich podłączonych urządzeń lub bram działających w terenie od innego ruchu sieciowego. Brama chmury nie jest systemem kontroli urządzenia lub przetwarzania lub magazynu danych dotyczących urządzeń; Interfejs tych urządzeń przy użyciu bramy w chmurze. Strefa bramy chmury obejmuje brama chmury wraz ze wszystkich bram działających w terenie i bezpośrednio lub pośrednio dołączone do niego urządzenia.

Brama chmury jest przede wszystkim niestandardowych utworzonych oprogramowanie jako usługa z udostępniane punkty końcowe, do których łączenie bramy w terenie i urządzeń. Jako takie muszą być zaprojektowane z myślą o bezpieczeństwie. Postępuj zgodnie z [SDL](https://www.microsoft.com/sdl) proces projektowania i tworzenia tej usługi.

#### <a name="services-zone"></a>Strefa usługi

System kontroli (lub kontroler) jest rozwiązanie programowe, z urządzenia, lub bramy w terenie lub brama chmury na potrzeby kontrolowania jednego lub wielu urządzeń i/lub do zbierania i/lub przechowywać i/lub analizować dane urządzenie, aby obejrzeć prezentację, interfejsem lub cele kolejne kontrolki. Systemy kontroli są tylko jednostki w zakresie tej dyskusji, która natychmiast może ułatwić interakcji z użytkownikami. Wyjątki są pośrednich fizycznych powierzchni na urządzeniach, takich jak przełącznik, który umożliwia użytkownikowi wyłączanie urządzenia lub zmieniać jej inne właściwości i dla których nie ma odpowiednika funkcjonalności, który jest możliwy cyfrowo.

Pośredni powierzchnie kontroli fizycznej dotyczą w przypadku, gdy regulujące logiki ogranicza funkcji powierzchni fizyczny, taki sposób, że zdalne mogą być inicjowane equivalent — funkcja lub można uniknąć konfliktów danych wejściowych z danymi wejściowymi zdalnego — przykład intermediated Sterowanie pod względem koncepcyjnym są dołączone do systemu lokalnym systemie kontroli, który korzysta z taką samą funkcjonalność podstawowego, jak inne systemy zdalnego sterowania, które urządzenia mogą być dołączone do równolegle. Najważniejszych zagrożeń bezpieczeństwa chmury obliczeniowej, może być odczytany przy [Cloud Security Alliance (CSA)](https://cloudsecurityalliance.org/articles/csa-releases-top-threats-to-cloud-computing-deep-dive/) strony.

## <a name="additional-resources"></a>Dodatkowe zasoby

Aby uzyskać więcej informacji zobacz następujące artykuły:

* [Narzędzie do modelowania zagrożeń SDL](https://www.microsoft.com/sdl/adopt/threatmodeling.aspx)
* [Architektura referencyjna IoT platformy Azure firmy Microsoft](https://azure.microsoft.com/updates/microsoft-azure-iot-reference-architecture-available/)
