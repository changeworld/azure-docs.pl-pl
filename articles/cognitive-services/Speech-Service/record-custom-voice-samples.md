---
title: Jak zarejestrować dźwięk przykłady do tworzenia niestandardowych voice | Dokumentacja firmy Microsoft
titleSuffix: Microsoft Cognitive Services
description: Należy głosu costum jakości produkcyjnej Przygotowywanie skryptu niezawodne, zatrudniania dobre głosu talent, i rejestrując profesjonalnie.
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 07/5/2018
ms.author: v-jerkin
ms.openlocfilehash: 7b58dc6e9bbfbf69358b30d29d93da6f6d6dc899
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2018
ms.locfileid: "37922577"
---
# <a name="how-to-record-voice-samples-for-a-custom-voice"></a>Jak rejestrować próbki głosu dla niestandardowych voice

Tworzenie wysokiej jakości produkcji głosu niestandardowego od podstaw nie jest zwykłych przedsiębiorstwa. Głównym składnikiem niestandardowych voice jest duży zbiór próbki audio ludzkiej mowy. Ważne jest, nagrania audio można wysokiej jakości. Wybierz głosu talent, który zawiera środowisko, dzięki czemu te rodzaje nagrania, a ich rejestrowane przez inżyniera właściwe rejestrowania przy użyciu profesjonalnych sprzętu.

Zanim dokonasz nagrania, jednak należy skryptu: wyrazy, które będzie używany przez usługi talent głosu do utworzenia próbki audio. Aby uzyskać najlepsze wyniki skryptu musi mieć dobre pokrycie fonetycznych i różnych wystarczające do nauczenia modelu niestandardowego głosu.

Wiele składników przechodzi w dobrej głosu niestandardowych. Ten przewodnik stanowi wprowadzenie do procesu, ze szczególnym naciskiem na problemy, które mogą wystąpić.

> [!TIP]
> Dla najwyższej jakości wyników należy wziąć pod uwagę interesujące Microsoft pomagające w tworzeniu niestandardowych głosu. Firma Microsoft oferuje rozbudowane środowisko tworzenia wysokiej jakości głosów dla własnej produktów, w tym Cortany i usługi Office.

## <a name="voice-recording-roles"></a>Role nagrywania głosu

Istnieją cztery podstawowe role w projekcie nagrywanie głosu niestandardowych.

Rola|Przeznaczenie
-|-
Talent głosu        |Osoba, której głosu będą podstawą niestandardowych voice.
Rejestrowanie inżynier ds.  |Osoba, nadzoruje technicznych aspektów nagrywania, która działa urządzenia rejestrującego.
Dyrektor ds.            |Przygotowuje skrypt i autokarami wydajności talent głosu.
Edytor              |Kończenie znajdujących się w pliki audio i przygotować je do przekazania do portalu usługi Custom Voice.

Osoba może podać więcej niż jednej roli. W przewodniku założono, że można będzie się przede wszystkim wypełnianie rolą dyrektora i zatrudniania głosu talent i odtwarzania nagrania. Istnieje pewne informacje o roli inżyniera nagrywania, w przypadku, gdy chcesz wprowadzić nagrania samodzielnie.

## <a name="choosing-voice-talent"></a>Wybieranie głosowe talent

Aktorów z doświadczeniem w pracach znak głosowe lub voiceover należy talent dobre niestandardowych voice. Ponadto często można znaleźć talent odpowiedni announcers i przeglądarki grup dyskusyjnych.

Wybieranie głosowe talent, którego naturalnego głosu możesz np. Istnieje możliwość tworzenia głosów unikatowy "character", ale jest znacznie trudniejsze dla większości talent wykonać spójnie i nakładu pracy może spowodować, że obciążenie głosu.

> [!TIP]
> Ogólnie rzecz biorąc, należy unikać rozpoznawalnych głosów do tworzenia niestandardowych voice — chyba że oczywiście dowiesz się, jak utworzyć głosu osobistości. Głosy mniejszym znane są zazwyczaj mniej rozprasza uwagę użytkowników.

Najważniejszym czynnikiem dotyczące wybierania głosowego talent jest spójność. W idealnym przypadku wszystkich nagrań powinien dźwięku, tak jak zostały wprowadzone w dniu, w tym samym pomieszczeniu. Można podejście to idealne rozwiązanie za pomocą nagrywania dobre praktyki i inżynierii. 

Talent Twojego głosu jest druga połowa równania. Użytkownik musi mieć możliwość Porozmawiaj z szybkości spójne, poziom głośności, skoku i sygnału. Wyczyść diction to. Twoje talent musi być również ściśle kontrolować własnego pomysłu odmiany, emocjonalnej wpływ i mannerisms mowy.

Nagranie próbki głosu niestandardowego może być bardziej fatiguing niż inne rodzaje pracy głosu. Większość talent głosowych można rejestrować dla dwóch lub trzech godzin dziennie. Ograniczenie sesji trzy lub cztery tygodnia, dzień poza między, jeśli jest to możliwe.

Nagrania dla modelu głosowego powinna być LOS neutralne. Oznacza to, że sad wypowiedź nie można odczytać w sposób sad. Nastrój można dodać do syntezatora mowy później. Praca z Twojego głosu talent, tworzenie "osoby", który definiuje ogólny dźwięku i emocjonalnej tonacji custom voice. W procesie będzie wskazanie "neutralne" brzmienia dla tej osoby.

Osoba może mieć na przykład naturalnie upbeat osobowość. Dlatego głosu może przenieść notatkę optimism nawet wtedy, gdy będzie neutralny mówić. Jednak cechy osobowości, powinny być subtelnym, spójne. Słuchać niektórych istniejących głosów, aby poznać elementy wskazywane na.

### <a name="legalities"></a>Legalities

Zazwyczaj będziesz chciał właścicielem nagrania głosowe, wprowadzone. Talent Twojego głosu powinna być podatna na kontrakt pracy dla zatrudnienia dla projektu.

## <a name="creating-a-script"></a>Tworzenie skryptu

Punkt początkowy żadnych niestandardowych voice rejestrowania sesji jest skryptu, który zawiera wypowiedzi wymawiane przez talent Twojego głosu. (Termin "wypowiedzi" obejmuje pełną zdania i fraz krótszy).

Wypowiedzi w skrypcie mogą pochodzić z dowolnego miejsca: Fikcja bez fikcja, zapisy przemówienie, wiadomości i wszystko inne dostępne w drukowanego formularza. Chcąc upewnij się, że Twój głos jest dobrze nadaje się do konkretnych rodzajów wyrazy (na przykład medycznych terminologii lub żargon programowania), można dołączyć zdania z uniwersyteckie dokumentów lub dokumentów technicznych. (Jednak zobaczyć [Legalities](#legalities) poniżej.) Można także napisać własny tekst.

Swoje wypowiedzi nie muszą pochodzić z tego samego źródła lub tego samego rodzaju źródła. Jeszcze nie muszą mieć związek z siebie nawzajem. Jednak jeśli wykonasz Użyj ustawić wyrażenia (na przykład, "użytkownik pomyślnie zalogował się") w aplikacji funkcji rozpoznawania mowy, upewnij się uwzględnić je w skrypcie. Ta będzie zapewniają niestandardowe głosu zwiększa prawdopodobieństwo dobrze ustalające tych wyrażeń. I jeśli powinny zdecydujesz się używać rejestrowania zamiast syntezatora mowy, będziesz już mieć go przy użyciu tego samego głosu jako syntezatora mowy.

Kluczowa jest Wybieranie głosowe talent, różnych jest kluczowa dobre skryptu. Skrypt powinien zawierać wiele inne słowa i zdania z różnymi rodzajami długości zdania, struktur i nastrój. Każdy dźwięk w języku powinien być reprezentowana wiele razy, a w wielu kontekstach (o nazwie *fonetycznych pokrycia).* 

Co więcej tekst powinno zawierać wszystkie sposoby, może być reprezentowany w formie pisemnej określony dźwięk i umieść każdy dźwięk w różnych miejscach w zdaniach. Deklaratywne zdania i pytania należy uwzględnione i odczytać przy użyciu odpowiednich maksimów.

Jest trudne do pisania skryptu, który zapewnia *wystarczający* danych, aby zezwolić portalowi mowy niestandardowego do tworzenia dobrych głosu. W praktyce Najprostszym sposobem, aby skrypt, który zapewnia niezawodne fonetycznych pokrycia jest zawierają dużą liczbę próbek. Głosy standard firmy Microsoft zostały skompilowane z dziesiątkami tysięcy wypowiedzi. Należy być przygotowanym do rejestrowania kilka do kilku tysięcy wypowiedzi do tworzenia niestandardowych głosu jakości produkcyjnej.

Sprawdź skrypt dokładnie pod kątem błędów. Jeśli to możliwe mają ktoś zbyt je sprawdzić. Po uruchomieniu przy użyciu skryptu za pomocą usługi talent, będzie prawdopodobnie efektywnej kilka więcej błędów.

### <a name="script-format"></a>Format skryptu

Można napisać skrypt w programie Microsoft Word. Skrypt jest w trakcie sesji nagrywania, dzięki czemu możesz skonfigurować go dowolnie wybrany okaże się proste w użyciu. Utwórz plik tekstowy, wymagane przez portal Custom Voice oddzielnie.

Format podstawowe skrypt zawiera trzy kolumny:

* Liczba wypowiedź, zaczynając od 1. Numerowanie ułatwi dla wszystkich użytkowników w programie studio do odwoływania się do określonego wypowiedź ("spróbujmy numer 356 ponownie"). Numerowania funkcji akapitów programu Word umożliwia automatyczne liczba wierszy tabeli.
* Pusta kolumna, w którym przedstawiono tworzenie kodu w czasie każdego wypowiedź: czas, w którym znajdziesz go w pliku audio zarejestrowane.
* Tekst wypowiedź, sam.

![Przykładowy skrypt](media/custom-voice/script.png)

Po każdym wierszu, aby zapisać informacje o, należy pozostawić wystarczającej ilości miejsca. Pamiętaj, że nie wypowiedź jest podzielony między stronami. Numer strony i Drukuj skryptu po jednej stronie papieru.

Drukuj trzy kopie skryptu: jeden dla talent, jeden dla serwisant i jeden dla Dyrektor (ty). Użyj klipu dokument zamiast staples: wykonawcy doświadczonym głosu będą oddzielać strony, aby uniknąć szumu jako strony są wyłączone.

### <a name="legalities"></a>Legalities

Prawa autorskiego aktora odczytywanie chronionych prawem autorskim tekstu może być wydajność, dla którego należy skompensować Autor pracy. To wydajności nie będzie rozpoznawalny w produkcie końcowym niestandardowych voice. Nawet w takim przypadku legalności przy użyciu prawem autorskim w tym celu nie jest dobrze udokumentowana. Firma Microsoft nie udostępnia porady prawne związane z tym problemem; Zapoznaj się z doradcą własne.

Na szczęście jest łatwe całkowicie uniknąć tych problemów. Dostępnych jest wiele źródeł tekstu, których można używać bez zgody lub licencji.

|||
|-|-|
|[Korpus Combat CMU](http://festvox.org/cmu_arctic/)|Około 1100 zdania wybrana w zaufanym działa poza o prawach autorskich przeznaczone dla projektów synteza mowy. Doskonałe punkt początkowy.|
|Już nie działa<br>w ramach praw autorskich|Zazwyczaj działa opublikowane przed 1923. Dla języka angielskiego Gutenberg projektu oferuje dziesiątki tysięcy takiego działania. Możesz skoncentrować się na nowsze działa jako język będzie bliżej nowoczesny język angielski.|
|Dla instytucji rządowych&nbsp;działa|Działania utworzone przez Rząd Stanów Zjednoczonych są nie chronionych prawem autorskim należących w Stanach Zjednoczonych, jednak dla instytucji rządowych mogą oświadczenia praw autorskich w innych krajach.|
|W domenie publicznej|Działa w przypadku jawnie disclaimed które prawa autorskie lub które zostały wyposażone w dedykowane do domeny publicznej.|
|Permissively licencjonowane działa|Działa rozpowszechniane na mocy licencji przykład licencji Creative Commons lub bezpłatnej licencji GNU w dokumentacji. Wikipedia używa GFDL. Dlatego niektórych licencji, jednak może nałożyć ograniczenia dotyczące wydajności licencjonowane zawartości, która może mieć wpływ na tworzenie modelu niestandardowego głosu, należy uważnie przeczytać licencji.|

## <a name="recording-your-script"></a>Rejestrowanie skryptu

Najlepiej należy Rejestruj skryptu w studio profesjonalnych nagrywania, który specjalizuje się w pracach voiceover. Będą mieć stoisku nagrywania, odpowiedniego sprzętu dla zadania i odpowiednich osób do jej działania. Warto nie skimp na rejestrowanie.

Omówienia projektu z programu studio nagrywanie inżynier i słuchać porad lub jej. Nagrywanie powinny mieć niewielkiego lub żadnego dynamicznym zakresie kompresji (maksymalnie 4:1). Koniecznie że audio mają spójne wolumin i wysoki współczynnik sygnał do szumu, będąc bezpłatnie niechciane dźwięki.

### <a name="doing-it-yourself"></a>Wykonując samodzielnie

Jeśli chcesz nagrywania samodzielnie, nie trzeba przechodzić w studio nagrywania, poniżej przedstawiono krótkie podstawowe informacje. Dzięki rozłożeniu częstsze podkastów i macierzystego nagrywania jest łatwiejsze niż kiedykolwiek Aby znaleźć porady dotyczące rejestrowania dobre i zasobów w tryb online.

Twoje "stoisku rejestrowania" powinny być małym pomieszczeniu bez zauważalnego echo lub "pomieszczenie sygnału" i jak cichy i soundproof, jak to możliwe. Zasłonami na tablicach może służyć do zmniejszenia echa i zneutralizować lub "deaden" dźwięk pokoju.

Korzystać z mikrofonu zwrotną studio wysokiej jakości ("mic") przeznaczony dla nagrywania głosu. Sennheiser AKG i nawet nowszej mikrofonu powiększenia może przynieść dobre wyniki. Zakup usługi sprawdzanie integralności komunikatu lub wynajmować z przedsiębiorstwa lokalnego audiowizualnych wypożyczeń. Zwróć uwagę na jeden z interfejsem USB. Ten typ kontroli integralności uprawnień wygodnie łączy elementu mikrofonu, preamp i konwerter analogowy cyfrowej w jeden pakiet, upraszczając do przyłączania.

Można także użyć analogowy mikrofonu. Wiele domów wypożyczeń oferują mikrofon "zbioru", będące wyposażona we znak głosowe, ich. Należy pamiętać, profesjonalnych narzędzi analogowy używa o zrównoważonym obciążeniu XLR łączników, a nie 1/4" Podłącz, używane w urządzeniach konsumenta. Jeśli przejdziesz analogowy, należy także preamp oraz interfejs audio komputera za pomocą tych łączników.

Zainstaluj mikrofonu na autonomicznych lub nagle i użyć pop filtru w frot mikrofonu w celu wyeliminowania hałas "plosive" dźwięki, np. "p" i "b". Niektóre mikrofon są dostarczane z instalacji zawieszenia, który izoluje ich od wibracje w autonomicznych, co jest przydatne.

Talent głosu musi pozostać w odległości spójne z mikrofonu. W systemie taśmy Zaokrąglenie w dół do oznaczania, gdzie powinna występować. Preferuje talent siedzieć, należy zwrócić szczególną uwagę do monitorowania odległość sprawdzanie integralności komunikatu i uniknij szumu spowodowanego krzesło.

Użyj autonomicznej do przechowywania skryptu. Należy unikać łowienia rozszerzanej, dzięki czemu może odzwierciedlać, dźwięk w kierunku mikrofonu.

Osoba faktycznie nagrywania — serwisant — powinien znajdować się w oddzielnych pokoju z talent, za pomocą jakiś sposób, aby komunikował się talent w rejestrowanie punktu ("obwód talkback").

Nagrywanie powinna zawierać jako szumu, jak to możliwe, mając na celu 80 współczynnik sygnał do szumu bazy danych lub nowszy.

Ściśle słuchać nagranie wyciszenia w sieci "stoisku," ustalenie, gdzie wszystkie hałasu pochodzi z i wyeliminować przyczyny. Wspólnych źródeł szumu znajdują się otwory wentylacyjne, fluorescencyjnego stateczników światła, ruch na drogach i wentylatory urządzeń (komputerów przenośnych nawet może mieć fanów) w pobliżu. Kable i mikrofon można wczytać elektrycznych hałas pobliskich połączeń programu AC, zwykle szum lub rozgłaszania.

> [!TIP]
> W niektórych przypadkach można użyć korektor lub oprogramowania redukcji szumów wtyczki do usuwania szumu z nagrań, chociaż jest zawsze najlepiej, zatrzymaj ją w jego źródle, gdy jest to możliwe.

Poziomy powinna być ustawiona, tak że większość dostępne dynamicznym zakresie rejestracja cyfrowa jest używany bez overdriving do zakłóceń. Oznacza to, głos, ale nie więc głos, zniekształci audio. Poniżej przedstawiono przykładowy przebiegu dobre nagranie.

![dobre nagranie dźwiękowe](media/custom-voice/good-recording.png)

Widać, że większość zakresu (wysokość) jest używany, ale najwyższej wartości szczytowe sygnału nie skontaktować się z góry lub u dołu ekranu. Widać również że wyciszenia w nagraniu przybliża linii poziomej alokowania elastycznego wskazujący floor szumu niski. To nagranie ma akceptowalnym zakresem dynamiczne i sygnał / szum.

Rekord bezpośrednio do komputera za pomocą interfejsu audio wysokiej jakości lub USB port, w zależności od rodzaju mic używasz. Proste łańcucha audio: Sprawdzanie integralności komunikatu, preamp, interfejs audio, komputer. Zarówno [Avid narzędzi dla specjalistów](http://www.avid.com/en/pro-tools) i [Adobe Audition](https://www.adobe.com/products/audition.html) mogą być licencjonowane co miesiąc opłat uzasadnione. W przypadku bardzo ścisłej swój budżet, wypróbuj bezpłatne [Audacity](https://www.audacityteam.org/).

Zarejestruj monophonic do 44,1 KHz 16-bitowego (jakością ciągłego wdrażania) lub Lepsza. Bieżący stan systemu — najnowocześniejsze to 48 KHz 24-bitowego, jeśli sprzęt obsługuje tę funkcję. Wykonasz próbkowanie audio do 16 KHz 16-bitowych przed przesłaniem jej do portalu usługi Custom Voice. Jednak warto korzystać z wysokiej jakości oryginalne nagranie w przypadku zmiany są potrzebne.

Najlepiej, jeśli mają różne osoby, które pełnią role dyrektora, inżynier i talent. Nie należy próbować robić wszystko samodzielnie! W uszczypnięcia Dyrektor ds. i odtwarzania może być jedna osoba.

### <a name="before-the-session"></a>Przed sesji

Aby uniknąć marnowania czasu studio, uruchom za pomocą skryptu z Twojego głosu talent przed sesji nagrywania. Talent głosu staje się zapoznać się z tekstu, można wyjaśnić Wymowa nieznanego słów.

> [!NOTE]
> Większość studios nagrywanie oferują elektronicznego wyświetlanie skryptów w stoisku rejestrowania. W tym przypadku wpisz notatki run-through bezpośrednio do dokumentu przez skrypt. Nadal należy zanotować podczas sesji, jednak z kopią papieru. Większość inżynierowie będą chcieli wydruku, zbyt. I będzie nadal potrzebujesz trzeci drukowane kopiowania do przechowywania kopii zapasowych dla talent, w przypadku, gdy komputer jest wyłączony.

Talent Twojego głosu może wystąpić, word, który ma zostać w wypowiedź. Aktorzy często wywołuje to "wyrazu operacyjne." Poinformuj ich, który ma naturalny odczytu ze nie szczególnym. Nacisk można dodać, gdy jest syntezatora mowy; nie należy w ramach oryginalnym nagraniu.

Bezpośrednie talent wyraźnie Wymowa słów. Każdy wyraz skrypt powinien występować podczas zapisywania. Dźwięki nie powinny zostać pominięty lub slurred ze sobą, co jest często spotykane w zwykłych mowy *, chyba że zostały one zapisane w ten sposób w skrypcie.*

|Napisany tekst|Niechciane Wymowa zwykłych|
|-|-|
|nigdy nie będzie można zrezygnować|nigdy nie będzie można zrezygnować|
|Istnieją cztery światła|dostępne są cztery światła|
|w jaki sposób dane pogody już dzisiaj|jak jest th "pogodowe już dziś|
|Moje pony mały|Moje lil "pony|

Należy talent *nie* dodać różne pauzy między wyrazami. Zdania nadal przepływu naturalnie, nawet podczas podawania nieco formalnych. Poprawnie wykonywania tego rozróżnienia może potrwać kilka rozwiązaniem, aby uzyskać odpowiednie.

### <a name="the-recording-session"></a>Sesji nagrywania

Utwórz odwołanie rejestrowania na wczesnym etapie sesji typowe wypowiedź i odtwarzać je talent głosu regularnie, aby pomóc im Utrzymuj spójność ich wydajność. Specjalista może służyć jako odwołanie dla poziomów i ogólną spójność dźwięku. Jest to szczególnie ważne w przypadku wznawiania rejestrowania po podziale lub na kolejny dzień.

Przeprowadzić szkolenia dotyczące usługi talent głębokiego utraty tchu i wstrzymania na chwilę przed każdym wypowiedź. Zapisz kilka sekund wyciszenia między wypowiedzi. Rozkład powinny być zgodne ze sobą. metronome pełnione przez słuchawki talent mogą być przydatne, jeśli one występują problemy. Wyrazy powinna występować w taki sam sposób każdorazowo, gdy są one wyświetlane.

Zarejestruj dobre pięć sekund wyciszenia przed pierwszym rejestrowania do przechwytywania "sygnału pokoju." Dzięki temu portal Custom Voice kompensuje wszelkie pozostałe szumu w nagrania.

> [!TIP]
> Jest tak naprawdę potrzebne do rejestrowania talent głosu, więc możesz wprowadzić monophonic nagrywanie (jednego kanału) tylko wiersze. Jednak podczas nagrywania stereo drugiego kanału można użyć do rejestrowania chatter w pomieszczeniu sterowania. Często jest to przydatne do odwoływania się do tego później. Aby usunąć tę ścieżkę wersji, przekazywane do portalu usługi Custom Voice.

Posłuchaj ściśle, użycie słuchawek wydajności talent głosu. Szukasz diction dobra, ale fizycznych, poprawna wymowa i braku niechciane dźwięki. Nie wahaj się poproś użytkownika talent, aby ponownie zarejestrować wypowiedź, które nie spełniają te standardy. 

> [!TIP] 
> Podczas rejestrowania dużej liczby wypowiedzi, utraty jednego wypowiedź nie mogą wpływać na wynikowej głosu w jakikolwiek sposób zauważalne. Dlatego może być bardziej celowe po prostu pamiętać wypowiedzi, które mają problemy, wykluczyć je z zestawu danych i zobacz, jak Twój głos niestandardowych, okaże się nim. Zawsze możesz wrócić do studio i później zarejestrować brakujących przykładów.

Większość studios ma duże wyświetlana "Kod czasu" wskazująca bieżący czas w nagraniu. Zapisz czas na skryptu dla każdego wypowiedź. Poproś specjalista, w przypadku ich oznaczyć każdy wypowiedź nagrywanie metadanych lub sygnalizacji arkusza również.

Skorzystaj z regularnych podziały umożliwiające Twojego głosu talent, utrzymania jej głosu w dobrym stanie. Udostępnij talent coś do napoju, aby zapobiec ich gardła wprowadzenie susz.

### <a name="after-the-session"></a>Po zakończeniu

Rejestrowanie nowoczesnych studios uruchamiane na komputerach. Na końcu sesji następnie pojawi się jeden lub więcej plików audio, nie taśmy. Te pliki będą prawdopodobnie być w formacie WAV lub AIFF jakości CD (44,1 KHz 16-bitowa) lub Lepsza. 48 kHz 24-bitowego to typowe i pożądane. Większe częstotliwości próbkowania, takich jak 96 KHz, zazwyczaj nie są potrzebne.

Studio prawdopodobnie będzie dostarczać jeden lub więcej plików audio, zawierające wiele wypowiedzi. Aby przekazać nagrania do portalu usługi Custom Voice, każdy wypowiedź musi być w jej własnym pliku. Inżynier rejestrowania może umieszczony znacznik w pliku (lub osobnych wskaźnika do listy) aby wskazać, gdzie rozpoczyna się każdego wypowiedź. Te metadane może służyć do wyodrębnienia wypowiedzi.

Musisz przejść przez pełnego rejestrowania i Udostępnij plik WAV dla każdego wypowiedź. Użyj notatki, aby znaleźć dokładnie wypowiedzi, a następnie użyć dźwięku, takich jak edytowanie narzędzie [Avid narzędzi dla specjalistów](http://www.avid.com/en/pro-tools), [Adobe Audition](https://www.adobe.com/products/audition.html), lub bezpłatnych [Audacity](https://www.audacityteam.org/) kopiowanie do Nowy plik.

Pozostaw tylko przez około 0,2 sekund wyciszenia na początku i końcu każdego klipu, z wyjątkiem pierwszej. Ten plik powinien zaczynać pełne pięć sekund wyciszenia. Nie należy używać w edytorze audio "zero out" silent części pliku. W tym "tonu pokoju" pomoże głosu niestandardowe algorytmy kompensuje wszelkie hałas w tle pozostałych.

Posłuchaj dokładnie do każdego pliku. Na tym etapie możesz edytować małych niechciane dźwięki, które zdarzyło Ci się przeoczyć podczas nagrywania — tak długo, jak te się nie nakładały żadnych rzeczywistych mowy. Jeśli nie można naprawić plik, usuń go z zestawu danych całkowicie, dzięki czemu Pamiętaj, że zostało to zrobione.

Próbkowanie każdy plik do 16 KHz i 16 bitów przed zapisaniem, a jeśli zarejestrował stereo, usuń drugi kanał. Zapisz każdego pliku w formacie WAV.

Archiwizowanie, oryginalnym rejestrowania w bezpiecznym miejscu, w razie potrzeby wrócić do niego później. Zachowaj skryptu i notatki, zbyt.

## <a name="next-steps"></a>Kolejne kroki

Możesz przekazać nagrań i utworzyć niestandardowe głosu!

> [!div class="nextstepaction"]
> [Tworzenie niestandardowych voice czcionek](how-to-customize-voice-font.md)
