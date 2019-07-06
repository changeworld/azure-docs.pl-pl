---
title: Rejestrowanie niestandardowych voice przykłady — usługi mowy
titleSuffix: Azure Cognitive Services
description: Należy głosu niestandardowe jakości produkcyjnej Przygotowywanie skryptu niezawodne, zatrudniania dobre głosu talent, i rejestrując profesjonalnie.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: ad2a3cd73fe97415f0d6835d70e15bf90349696c
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2019
ms.locfileid: "67604902"
---
# <a name="record-voice-samples-to-create-a-custom-voice"></a>Próbki głosu rekord do utworzenia niestandardowych voice

Tworzenie wysokiej jakości produkcji głosu niestandardowego od podstaw nie jest zwykłych przedsiębiorstwa. Głównym składnikiem niestandardowych voice jest duży zbiór próbki audio ludzkiej mowy. Ważne jest, nagrania audio można wysokiej jakości. Wybierz talent głosowej, doświadczeniem wprowadzania tych rodzajów nagrania, a ich rejestrowane przez inżyniera właściwe rejestrowania przy użyciu profesjonalnych sprzętu.

Zanim dokonasz nagrania, jednak należy skryptu: wyrazy, które będzie używany przez usługi talent głosu do utworzenia próbki audio. Aby uzyskać najlepsze wyniki skryptu musi mieć dobre pokrycie fonetycznych i różnych wystarczające do nauczenia modelu niestandardowego głosu.

Wiele szczegółów małą ale ważną przejdź do procesu tworzenia nagranie głosowe professional. Ten przewodnik jest plan działania dla procesu, który pomoże Ci dobre, spójne wyniki.

> [!TIP]
> Dla najwyższej jakości wyników należy wziąć pod uwagę interesujące Microsoft pomagające w tworzeniu niestandardowych głosu. Firma Microsoft oferuje rozbudowane środowisko tworzenia wysokiej jakości głosów dla własnej produktów, w tym Cortany i usługi Office.

## <a name="voice-recording-roles"></a>Role nagrywania głosu

Istnieją cztery podstawowe role w projekcie nagrywanie głosu niestandardowe:

Rola|Przeznaczenie
-|-
Talent głosu        |Głos tę osobę stanowią podstawę niestandardowe głosu.
Rejestrowanie inżynier ds.  |Nadzoruje technicznych aspektów rejestrowania i działa urządzenia rejestrującego.
Dyrektor ds.            |Przygotowuje skrypt i autokarami wydajności talent głosu.
Edytor              |Kończenie znajdujących się w pliki audio i przygotować je do przekazania do portalu usługi Custom Voice.

Osoba może podać więcej niż jednej roli. W przewodniku założono, że będzie można przede wszystkim wypełnianie rolą dyrektora i zatrudniania talent głosu i odtwarzania nagrania. Jeśli chcesz wprowadzić nagrania samodzielnie, ten artykuł zawiera pewne informacje o roli inżyniera rejestrowania. Rolę edytor nie jest potrzebny, dopóki sesji, dzięki czemu mogą być wykonywane przez dyrektora lub odtwarzania nagrania.

## <a name="choose-your-voice-talent"></a>Wybierz swoje talent głosu

Aktorów z doświadczeniem w pracach znak głosowe lub voiceover należy talent dobre niestandardowych voice. Ponadto często można znaleźć talent odpowiedni announcers i przeglądarki grup dyskusyjnych.

Wybieranie głosowe talent, którego naturalnego głosu możesz np. Istnieje możliwość tworzenia głosów unikatowy "character", ale jest znacznie trudniejsze dla większości talent wykonać spójnie i nakładu pracy może spowodować, że obciążenie głosu.

> [!TIP]
> Ogólnie rzecz biorąc, należy unikać rozpoznawalnych głosów do tworzenia niestandardowych voice — chyba że oczywiście dowiesz się, jak utworzyć głosu osobistości. Głosy mniejszym znane są zazwyczaj mniej rozprasza uwagę użytkowników.

Najważniejszym czynnikiem dotyczące wybierania głosowego talent jest spójność. Nagrań powinny wszystkie dźwięku tak jak zostały wprowadzone w dniu, w tym samym pomieszczeniu. Można podejście to idealne rozwiązanie za pomocą nagrywania dobre praktyki i inżynierii.

Talent Twojego głosu jest druga połowa równania. Muszą być w stanie porozmawiać z szybkości spójne, poziom głośności, skoku i sygnału. Wyczyść diction to. Talent musi być w stanie zachować ścisłą kontrolę ich odmiany pomysłu, emocjonalnej wpływ i mannerisms mowy.

Nagranie próbki głosu niestandardowego może być bardziej fatiguing niż inne rodzaje pracy głosu. Większość talent głosowych można rejestrować dla dwóch lub trzech godzin dziennie. Jeśli to możliwe ograniczyć sesje trzy lub cztery tygodnia, dzień, wyłączanie wewnętrzne.

Nagrania dla modelu głosowego powinna być LOS neutralne. Oznacza to, że sad wypowiedź nie można odczytać w sposób sad. Nastrój można dodać do syntezatora mowy później za pomocą kontrolki prosody. Praca z Twojego głosu talent, tworzenie "osoby", który definiuje ogólny dźwięku i emocjonalnej tonacji custom voice. W procesie będzie wskazanie "neutralne" brzmienia dla tej osoby.

Osoba może być na przykład naturalnie upbeat osobowość. Dlatego "" głosu może wykonać notatkę optimism nawet wtedy, gdy jest używany język będzie neutralny. Jednak cechy osobowości, powinny być subtelnym, spójne. Posłuchaj odczyty przez istniejące głosów do poznać elementy wskazywane dla.

> [!TIP]
> Zazwyczaj będziesz chciał właścicielem nagrania głosowe, wprowadzone. Talent Twojego głosu powinna być podatna na kontrakt pracy dla zatrudnienia dla projektu.

## <a name="create-a-script"></a>Tworzenie skryptu

Punkt początkowy żadnych niestandardowych voice rejestrowania sesji jest skryptu, który zawiera wypowiedzi wymawiane przez talent Twojego głosu. (Termin "wypowiedzi" obejmuje pełną zdania i fraz krótszy).

Wypowiedzi w skrypcie mogą pochodzić z dowolnego miejsca: Fikcja bez fikcja, zapisy przemówienie, wiadomości i wszystko inne dostępne w drukowanego formularza. Chcąc upewnij się, że Twój głos jest dobrze nadaje się do konkretnych rodzajów wyrazy (na przykład medycznych terminologii lub żargon programowania), można dołączyć zdania z uniwersyteckie dokumentów lub dokumentów technicznych. Krótkie omówienie potencjalnych kwestii prawnych, zobacz ["Legalities"](#legalities) sekcji. Można także napisać własny tekst.

Swoje wypowiedzi nie muszą pochodzić z tego samego źródła lub tego samego rodzaju źródła. Jeszcze nie muszą mieć związek z siebie nawzajem. Jednak jeśli wykonasz Użyj ustawić wyrażenia (na przykład, "użytkownik pomyślnie zalogował się") w aplikacji funkcji rozpoznawania mowy, upewnij się uwzględnić je w skrypcie. Zapewni to Twój głos niestandardowe zwiększa prawdopodobieństwo dobrze ustalające tych wyrażeń. I należy zdecydujesz się używać rejestrowania zamiast syntezatora mowy, będą już mieć go przy użyciu tego samego głosu.

Kluczowa jest Wybieranie głosowe talent, różnych jest kluczowa dobre skryptu. Skrypt powinien zawierać wiele inne słowa i zdania z różnymi rodzajami długości zdania, struktur i nastrój. Każdy dźwięk w języku powinien być reprezentowana wiele razy, a w wielu kontekstach (o nazwie *fonetycznych pokrycia*).

Co więcej tekst powinno zawierać wszystkie sposoby, może być reprezentowany w formie pisemnej określony dźwięk i umieść każdy dźwięk w różnych miejscach w zdaniach. Deklaratywne zdania i pytania należy uwzględnione i odczytać przy użyciu odpowiednich maksimów.

Jest trudne do pisania skryptu, który zapewnia *wystarczający* danych, aby zezwolić portalowi mowy niestandardowego do tworzenia dobrych głosu. W praktyce Najprostszym sposobem, aby skrypt, który zapewnia niezawodne fonetycznych pokrycia jest zawierają dużą liczbę próbek. Standardowa głosy, które firma Microsoft udostępnia zostały skompilowane z dziesiątkami tysięcy wypowiedzi. Należy być przygotowanym do rejestrowania kilka do kilku tysięcy wypowiedzi w co najmniej do tworzenia niestandardowych głosu jakości produkcyjnej.

Sprawdź skrypt dokładnie pod kątem błędów. Jeśli to możliwe mają ktoś zbyt je sprawdzić. Po uruchomieniu przy użyciu skryptu za pomocą usługi talent, będzie prawdopodobnie efektywnej kilka więcej błędów.

### <a name="script-format"></a>Format skryptu

Można napisać skrypt w programie Microsoft Word. Skrypt jest w trakcie sesji nagrywania, dzięki czemu możesz skonfigurować go dowolnie wybrany okaże się proste w użyciu. Utwórz plik tekstowy, który jest wymagany przez portal Custom Voice oddzielnie.

Format podstawowe skrypt zawiera trzy kolumny:

* Liczba wypowiedź, zaczynając od 1. Numerowanie ułatwia wszystkim użytkownikom w programie studio do odwoływania się do określonego wypowiedź ("spróbujmy numer 356 ponownie"). Numerowania funkcji akapitów programu Word umożliwia automatycznie liczba wierszy tabeli.
* Pustej kolumnie, gdzie będzie zapisu liczba take lub kod każdego wypowiedź, co pomaga znaleźć go w nagraniu Zakończono czasu.
* Tekst wypowiedź, sam.

![Przykładowy skrypt](media/custom-voice/script.png)

> [!NOTE]
> Większość studios rejestrowania w skrócie segmenty nazywane *przyjmuje*. Każda zawiera zazwyczaj wypowiedzi 10 do 24. Po prostu można zauważyć, że liczba take jest wystarczające, aby znaleźć wypowiedź później. Rejestrowania w programie studio, który chce mieć dłuższy nagrania, należy zamiast tego należy zwrócić uwagę kodu w czasie. Studio będzie wyświetlana wyraźną czasu.

Po każdym wierszu, aby zapisać informacje o, należy pozostawić wystarczającej ilości miejsca. Pamiętaj, że nie wypowiedź jest podzielony między stronami. Numer strony i wydrukuj skryptu po jednej stronie papieru.

Drukuj trzy kopie skryptu: jeden dla talent, jeden dla serwisant i jeden dla Dyrektor (ty). Użyj klipu dokument zamiast staples: wykonawcy doświadczonym głosu będą oddzielać strony, aby uniknąć szumu jako strony są wyłączone.

### <a name="legalities"></a>Legalities

Prawa autorskiego aktora odczytywanie chronionych prawem autorskim tekst może być wydajność, dla którego należy skompensować Autor pracy. To wydajności nie będzie rozpoznawalny w produkcie końcowym niestandardowych voice. Nawet w takim przypadku legalności przy użyciu prawem autorskim do tego celu jest utrwalonego. Firma Microsoft nie udostępnia porady prawne związane z tym problemem; Zapoznaj się z doradcą własne.

Na szczęście istnieje możliwość całkowicie uniknąć tych problemów. Dostępnych jest wiele źródeł tekstu, których można używać bez zgody lub licencji.

|Tekst źródłowy|Opis|
|-|-|
|[Korpus Combat CMU](http://festvox.org/cmu_arctic/)|Około 1100 zdania wybrana w zaufanym działa poza o prawach autorskich przeznaczone dla projektów synteza mowy. Doskonałe punkt początkowy.|
|Już nie działa<br>w ramach praw autorskich|Zazwyczaj działa opublikowane przed 1923. Dla języka angielskiego [Gutenberg projektu](https://www.gutenberg.org/) udostępnia dziesiątki tysięcy takiego działania. Możesz skoncentrować się na nowsze działa jako język będzie bliżej nowoczesny język angielski.|
|Dla instytucji rządowych&nbsp;działa|Działania utworzone przez Rząd Stanów Zjednoczonych są nie chronionych prawem autorskim należących w Stanach Zjednoczonych, jednak dla instytucji rządowych mogą oświadczenia praw autorskich w innych krajach/regionach.|
|W domenie publicznej|Działa w przypadku jawnie disclaimed które prawa autorskie lub które zostały wyposażone w dedykowane do domeny publicznej. Może nie być możliwe do odstąpienia od praw autorskich w całości na niektóre jurysdykcje.|
|Permissively licencjonowane działa|Firma Creative Commons lub licencji dokumentacji bezpłatna (GFDL) GNU, takich jak działa rozpowszechniane na mocy licencji. Wikipedia używa GFDL. Dlatego niektórych licencji, jednak może nałożyć ograniczenia dotyczące wydajności licencjonowane zawartości, która może mieć wpływ na tworzenie modelu niestandardowego głosu, należy uważnie przeczytać licencji.|

## <a name="recording-your-script"></a>Rejestrowanie skryptu

Zapisz skrypt w studio profesjonalnych nagrywania, który specjalizuje się w pracach głosu. Będą mieć stoisku nagrywania, odpowiednie urządzenia i odpowiednich osób do jej działania. Warto nie skimp na rejestrowanie.

Omówienia projektu z programu studio nagrywanie inżynier i nasłuchiwania rad. Nagrywanie powinny mieć niewielkiego lub żadnego dynamicznym zakresie kompresji (maksymalnie 4:1). Koniecznie że audio mają spójne wolumin i wysoki współczynnik sygnał do szumu, będąc bezpłatnie niechciane dźwięki.

### <a name="do-it-yourself"></a>Wykonaj samodzielnie

Jeśli chcesz nagrywania samodzielnie, nie trzeba przechodzić w studio nagrywania, poniżej przedstawiono krótkie podstawowe informacje. Dzięki rozłożeniu częstsze podkastów i macierzystego nagrywania jest łatwiejsze niż kiedykolwiek Aby znaleźć porady dotyczące rejestrowania dobre i zasobów w tryb online.

Twoje "stoisku nagrywanie" powinna być małym pomieszczeniu bez zauważalnego echo lub "pomieszczenie sygnału". Należy go jako cichy i soundproof, jak to możliwe. Zasłonami na tablicach może służyć do zmniejszenia echa i zneutralizować lub "deaden" dźwięk pokoju.

Korzystać z mikrofonu zwrotną studio wysokiej jakości ("mic" skrócie) przeznaczone do nagrywania głosu. Sennheiser AKG i nawet nowszej mikrofonu powiększenia może przynieść dobre wyniki. Kup zadajemy lub wynajmować z przedsiębiorstwa lokalnego audiowizualnych wypożyczeń. Zwróć uwagę na jeden z interfejsem USB. Ten typ kontroli integralności uprawnień wygodnie łączy elementu mikrofonu, preamp i konwerter analogowy cyfrowej w jeden pakiet, upraszczając do przyłączania.

Można także użyć analogowy mikrofonu. Wiele domów wypożyczeń oferują wyposażona we znak głosowe, ich mikrofon "zbioru". Należy zauważyć, że korzysta z profesjonalnych narzędzi analogowy zrównoważone XLR łączników, a nie plug 1/4 cala, używanym w urządzeń na odbiorców. Jeśli przejdziesz analogowy, należy także preamp oraz interfejs audio komputera za pomocą tych łączników.

Na autonomicznych lub nagle zainstalować mikrofon i zainstaluj pop filtr przed mikrofonu w celu wyeliminowania hałas "plosive" Spółgłoski, takich jak "p" i "b". Niektóre mikrofon są dostarczane z instalacji zawieszenia, który izoluje ich od wibracje w autonomicznych, co jest przydatne.

Talent głosu musi pozostać w odległości spójne z mikrofonu. W systemie taśmy Zaokrąglenie w dół do oznaczania, gdzie powinna występować. Preferuje talent siedzieć, należy zwrócić szczególną uwagę do monitorowania odległość sprawdzanie integralności komunikatu i uniknij szumu spowodowanego krzesło.

Użyj autonomicznej do przechowywania skryptu. Należy unikać łowienia rozszerzanej, dzięki czemu może odzwierciedlać, dźwięk w kierunku mikrofonu.

Osoby rejestrujące działania — serwisant — powinien znajdować się w oddzielnych pokoju z talent, za pomocą jakiś sposób, aby komunikować się z talent w stoisku rejestrowania ( *talkback obwodu).*

Nagrywanie powinna zawierać jako szumu, jak to możliwe, mając na celu współczynnika sygnał do szumu 80-db lub nowszy.

Ściśle słuchać nagranie wyciszenia w sieci "stoisku," ustalenie, gdzie wszystkie hałasu pochodzi z i wyeliminować przyczyny. Wspólnych źródeł szumu znajdują się otwory wentylacyjne, fluorescencyjnego stateczników światła, ruch na drogach i wentylatory urządzeń (komputerów przenośnych nawet może być fanów) w pobliżu. Kable i mikrofon można wczytać elektrycznych hałas pobliskich połączeń programu AC, zwykle szum lub rozgłaszania. Rozgłaszania może również być spowodowane *pętli podstaw*, której przyczyną urządzenia podłączone do więcej niż jednego obwodu elektrycznego.

> [!TIP]
> W niektórych przypadkach można wyeliminować szumu z nagrań, mimo że zawsze zaleca się zatrzyma źródła za pomocą korektora lub oprogramowania redukcji szumów wtyczki.

Ustawić poziomy, tak że większość dostępne dynamicznym zakresie rejestracja cyfrowa jest używany bez overdriving. Oznacza to zestaw audio Głośna, ale nie więc głos zakłócona staje się jej. Przykład przebiegu dobre nagranie przedstawiono na poniższej ilustracji:

![Fali dobre nagrywanie](media/custom-voice/good-recording.png)

W tym miejscu jest używany przez większość zakresu (wysokość), ale najwyższej wartości szczytowe sygnału nie skontaktować się z góry lub u dołu okna. Widać również że wyciszenia w nagraniu przybliża linii poziomej alokowania elastycznego wskazujący floor szumu niski. To nagranie ma akceptowalnym zakresem dynamiczne i sygnał / szum.

Rekord bezpośrednio do komputera za pośrednictwem interfejsu audio wysokiej jakości lub port USB, w zależności od mikrofonu, z którego korzystasz. Dla analogowy, proste łańcucha audio: Sprawdzanie integralności komunikatu, preamp, interfejs audio, komputer. Możesz licencjonować zarówno [Avid narzędzi dla specjalistów](https://www.avid.com/en/pro-tools) i [Adobe Audition](https://www.adobe.com/products/audition.html) miesięcznych kosztów uzasadnione. W przypadku bardzo ścisłej swój budżet, wypróbuj bezpłatne [Audacity](https://www.audacityteam.org/).

Zarejestruj monophonic do 44,1 kHz 16-bitowego (jakością ciągłego wdrażania) lub Lepsza. Bieżący stan systemu — najnowocześniejsze to 48 kHz 24-bitowego, jeśli sprzęt obsługuje tę funkcję. Użytkownik będzie obniżenie częstotliwości próbkowania audio do 16 kHz 16-bitowych przed przesłaniem jej do portalu usługi Custom Voice. Jednak warto korzystać z wysokiej jakości oryginalne nagranie w przypadku zmiany są potrzebne.

Najlepiej, jeśli mają różne osoby, które pełnią role dyrektora, inżynier i talent. Nie należy próbować robić wszystko samodzielnie. W uszczypnięcia jedna osoba może być zarówno dyrektor, jak i odtwarzania.

### <a name="before-the-session"></a>Przed sesji

Aby uniknąć marnowania czasu studio, uruchom za pomocą skryptu z Twojego głosu talent przed sesji nagrywania. Podczas talent głosu staje się zapoznać się z tekstu, one objaśnienia wymowy wszelkich nieznanych wyrazów.

> [!NOTE]
> Większość studios nagrywanie oferują elektronicznego wyświetlanie skryptów w stoisku rejestrowania. W tym przypadku wpisz notatki run-through bezpośrednio do dokumentu przez skrypt. Nadal należy zanotować podczas sesji, jednak z kopią papieru. Większość inżynierowie będą chcieli zbyt kopię twardych. I będzie nadal potrzebujesz trzeciego drukowane kopiowania do przechowywania kopii zapasowych dla talent, w przypadku, gdy komputer znajduje się w dół.

Talent Twojego głosu może poprosić które program word ma wyróżniono w wypowiedź ("word operacyjne"). Poinformuj ich, ma naturalny odczytu ze nie szczególnym. Nacisk można dodać, gdy jest syntezatora mowy; nie należy w ramach oryginalnym nagraniu.

Bezpośrednie talent wyraźnie Wymowa słów. Każdy wyraz skrypt powinien występować podczas zapisywania. Dźwięki nie powinny zostać pominięty lub slurred ze sobą, co jest często spotykane w zwykłych mowy *, chyba że zostały one zapisane w ten sposób w skrypcie*.

|Napisany tekst|Niechciane Wymowa zwykłych|
|-|-|
|nigdy nie będzie można zrezygnować|nigdy nie będzie można zrezygnować|
|Istnieją cztery światła|dostępne są cztery światła|
|w jaki sposób dane pogody już dzisiaj|jak jest th "pogodowe już dziś|
|Zacznij korzystać Moje nieco znajomego|Załóżmy, że hello do mojego lil "friend|

Należy talent *nie* dodać różne pauzy między wyrazami. Zdania nadal przepływu naturalnie, nawet podczas podawania nieco formalnych. Dobrym rozwiązaniem wykonywania tego rozróżnienia może potrwać rozwiązaniem, aby uzyskać odpowiednie.

### <a name="the-recording-session"></a>Sesji nagrywania

Utwórz odwołanie rejestrowania, lub *pliku dopasowania* z typowym wypowiedź na początku sesji. Poproś talent, aby powtórzyć ten wiersz z każdej strony lub celu. Za każdym razem, porównaj nowe nagranie do odwołania. Praktyka ta pomaga talent, pozostają spójne w woluminie, tempo, skoku i maksimów. W tym samym czasie serwisant pliku można użyć dopasowania jako odwołanie dla poziomów i ogólną spójność dźwięku.

Plik dopasowanie jest szczególnie ważne, gdy Wznów nagrywanie po podziale lub na kolejny dzień. Można go odtworzyć kilka razy for talent, i poproś powtórzyć za każdym razem, dopóki nie są one również dopasowania.

Przeprowadzić szkolenia dotyczące usługi talent głębokiego utraty tchu i wstrzymania na chwilę przed każdym wypowiedź. Zapisz kilka sekund wyciszenia między wypowiedzi. Wyrazy powinna występować w taki sam sposób każdorazowo, gdy są wyświetlane, biorąc pod uwagę kontekstu. Na przykład "Zapisz" jako zlecenie jest wymawiany inaczej od "rekordu" rzeczownik.

Zarejestruj dobre pięć sekund wyciszenia przed pierwszym rejestrowania do przechwytywania "sygnału pokoju." Praktyka ta pomaga w portalu usługi Custom Voice kompensuje wszelkie pozostałe szumu w nagrania.

> [!TIP]
> Wszystko, czego naprawdę potrzebne do przechwytywania jest głos talent, dzięki czemu można podejmować monophonic nagrywanie (jednego kanału) tylko wiersze. Jednak jeśli rejestrujesz stereo można użyć drugiego kanału do rejestrowania chatter w pomieszczeniu sterowania, aby przechwycić dyskusję na temat konkretnych wierszy lub trwa. Usuń tę ścieżkę, z wersji, który jest przekazywany do portalu usługi Custom Voice.

Posłuchaj ściśle, użycie słuchawek wydajności talent głosu. Szukasz diction dobra, ale fizycznych, poprawna wymowa i braku niechciane dźwięki. Nie wahaj się poproś użytkownika talent, aby ponownie zarejestrować wypowiedź, które nie spełniają te standardy.

> [!TIP]
> Jeśli używasz dużej liczby wypowiedzi pojedynczego wypowiedź utracić znaczącego wpływu na wynikowe niestandardowych voice. Może być wskazane więcej po prostu pamiętać wypowiedzi z problemami, wykluczyć je z zestawu danych i zobacz, jak Twój głos niestandardowych, okaże się nim. Zawsze możesz wrócić do studio i później zarejestrować brakujących przykładów.

Należy pamiętać, liczba take lub kod, nad skryptem czasu dla każdego wypowiedź. Poproś inżynier do oznaczania każdej wypowiedź w metadanych nagrywania lub także arkuszu wskaźnika.

Podjąć podziały regularnych i zapewniają spożywczy ułatwiające Twojego głosu talent, utrzymania głosu w dobrym stanie.

### <a name="after-the-session"></a>Po zakończeniu

Rejestrowanie nowoczesnych studios uruchamiane na komputerach. Na końcu sesji pojawi się jeden lub więcej plików audio, nie taśmy. Te pliki będą prawdopodobnie być w formacie WAV lub AIFF jakości CD (44,1 kHz 16-bitowa) lub Lepsza. 48 kHz 24-bitowego to typowe i pożądane. Większe częstotliwości próbkowania, takich jak 96 kHz, zazwyczaj nie są potrzebne.

Portal Custom Voice wymaga każdego wypowiedź podana w jej własnym pliku. Każdy plik dźwiękowy dostarczanych przez studio zawiera wiele wypowiedzi. Głównym zadaniem poprodukcyjnych więc podzielić nagrania i przygotować je do przesłania. Inżynier nagrywanie mógł zostać umieścić znaczniki w pliku (lub podany arkusz oddzielne sygnalizacji) aby wskazać, gdzie rozpoczyna się każdego wypowiedź.

Notatki w taki sposób, aby znaleźć konkretny spowoduje przejście ma, a następnie użyć dźwięk, takich jak edytowanie, narzędzie [Avid narzędzi dla specjalistów](https://www.avid.com/en/pro-tools), [Adobe Audition](https://www.adobe.com/products/audition.html), lub bezpłatnych [Audacity](https://www.audacityteam.org/), aby skopiować każdy Wypowiedź do nowego pliku.

Pozostaw tylko przez około 0,2 sekund wyciszenia na początku i końcu każdego klipu, z wyjątkiem pierwszej. Ten plik powinien zaczynać pełne pięć sekund wyciszenia. Nie należy używać edytora audio do dyskretnej części pliku "zero out". W tym "tonu pokoju" pomoże głosu niestandardowe algorytmy kompensuje wszelkie hałas w tle pozostałych.

Posłuchaj dokładnie do każdego pliku. Na tym etapie można edytować małych niechciane dźwięki, które zdarzyło Ci się przeoczyć podczas nagrywania, takich jak smack nieznaczne lip, przed wierszem, ale należy uważać, aby nie usunąć wszystkie rzeczywiste mowy. Jeśli nie można naprawić plik, usuń go z zestawu danych i należy pamiętać, że zostało to zrobione.

Konwertuj każdy plik do 16 bitów i częstotliwość próbkowania, 16 kHz przed zapisaniem, a jeśli zapisane studio chatter Usuń drugiego kanału. Zapisz każdego pliku w formacie WAV, nazw plików z liczbą wypowiedź ze skryptu.

Na koniec Utwórz *transkrypcji* który kojarzy każdy plik WAV z to tekstowa wersja odpowiedniego wypowiedź. [Tworzenie niestandardowych voice czcionki](how-to-customize-voice-font.md) zawiera szczegółowe informacje z wymaganym formatem. Możesz skopiować tekst bezpośrednio ze skryptu. Następnie utwórz plik Zip plików WAV i transkrypcji tekstu.

Archiwizowanie oryginalnego nagrania w bezpiecznym miejscu, w przypadku, gdy będą potrzebne później. Zachowaj skryptu i notatki, zbyt.

## <a name="next-steps"></a>Kolejne kroki

Możesz już przekazywać nagrań i utworzyć niestandardowe głosu.

> [!div class="nextstepaction"]
> [Tworzenie niestandardowych voice czcionek](how-to-customize-voice-font.md)
