---
title: Nagrywanie niestandardowych próbek głosu — usługa mowy
titleSuffix: Azure Cognitive Services
description: Spersonaliuj niestandardowy głos o jakości produkcyjnej, przygotowując solidny skrypt, zatrudniając dobry talent głosowy i nagrywając profesjonalnie.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: erhopf
ms.openlocfilehash: b076c642f72e45f58be61d67e887e11b6ccb0aba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74975860"
---
# <a name="record-voice-samples-to-create-a-custom-voice"></a>Nagrywanie próbek głosu w celu utworzenia niestandardowego głosu

Tworzenie wysokiej jakości produkcji niestandardowy głos od podstaw nie jest przypadkowym przedsięwzięciem. Centralnym elementem niestandardowego głosu jest duża kolekcja próbek audio ludzkiej mowy. Ważne jest, aby te nagrania audio były wysokiej jakości. Wybierz talent głosowy, który ma doświadczenie w tworzeniu tego rodzaju nagrań, i poproś ich o nagranie przez kompetentnego inżyniera nagrywania przy użyciu profesjonalnego sprzętu.

Zanim jednak będzie można wykonać te nagrania, potrzebujesz skryptu: słów, które zostaną wypowiedziane przez twój talent głosowy, aby stworzyć próbki audio. Aby uzyskać najlepsze wyniki, skrypt musi mieć dobry zasięg fonetyczny i wystarczającą różnorodność, aby trenować niestandardowy model głosu.

Wiele małych, ale ważnych szczegółów wchodzi w tworzenie profesjonalnego nagrywania głosu. Ten przewodnik jest planem działania dla procesu, który pomoże Ci uzyskać dobre, spójne wyniki.

> [!TIP]
> Aby uzyskać najwyższą jakość wyników, rozważ zaangażowanie firmy Microsoft w celu opracowania niestandardowego głosu. Firma Microsoft ma duże doświadczenie w produkcji wysokiej jakości głosów dla własnych produktów, w tym Cortany i pakietu Office.

## <a name="voice-recording-roles"></a>Role nagrywania głosu

Istnieją cztery podstawowe role w niestandardowym projekcie nagrywania głosu:

Rola|Przeznaczenie
-|-
Talent głosowy        |Głos tej osoby będzie podstawą niestandardowego głosu.
Inżynier nagrywania  |Nadzoruje techniczne aspekty nagrywania i obsługuje urządzenie rejestrujące.
Dyrektor            |Przygotowuje scenariusz i trenerów talent głosu wydajność.
Edytor              |Finalizuje pliki audio i przygotowuje je do przesłania do portalu Niestandardowy głos.

Osoba może wypełnić więcej niż jedną rolę. Ten przewodnik zakłada, że będziesz przede wszystkim wypełniać rolę reżysera i zatrudniać zarówno talent głosowy, jak i inżyniera nagrywania. Jeśli chcesz zrobić nagrania samodzielnie, ten artykuł zawiera pewne informacje na temat roli inżyniera nagrywania. Rola edytora nie jest potrzebna dopiero po sesji, więc może być wykonywana przez reżysera lub inżyniera nagrywania.

## <a name="choose-your-voice-talent"></a>Wybierz swój talent głosowy

Aktorzy z doświadczeniem w pracy lektora lub postaci głosowej tworzą dobry niestandardowy talent głosowy. Często można również znaleźć odpowiedni talent wśród spikerów i czytelników.

Wybierz talent głosowy, którego naturalny głos ci się podoba. Możliwe jest tworzenie unikalnych "charakter" głosy, ale jest to o wiele trudniejsze dla większości talentów, aby wykonać je konsekwentnie, a wysiłek może powodować obciążenie głosu.

> [!TIP]
> Ogólnie rzecz biorąc, unikaj używania rozpoznawalnych głosów do tworzenia niestandardowego głosu — o ile oczywiście twoim celem nie jest uzyskanie głosu celebrytów. Mniej znane głosy są zwykle mniej rozpraszające dla użytkowników.

Najważniejszym czynnikiem przy wyborze talentu głosowego jest spójność. Twoje nagrania powinny brzmieć tak, jakby zostały wykonane tego samego dnia w tym samym pomieszczeniu. Możesz podejść do tego ideału poprzez dobre praktyki nagrywania i inżynierii.

Twój głos talent jest drugą połowę równania. Muszą być w stanie mówić ze stałą szybkością, poziomem głośności, tonem i tonem. Wyraźna dykcja jest koniecznością. Talent musi również być w stanie ściśle kontrolować ich zmienność boiska, emocjonalny wpływ, i maniery mowy.

Nagrywanie niestandardowych próbek głosu może być bardziej fatiguing niż inne rodzaje pracy głosowej. Większość talentów głosowych może nagrywać przez dwie lub trzy godziny dziennie. Ogranicz sesje do trzech lub czterech w tygodniu, z dniem wolnym między nimi, jeśli to możliwe.

Nagrania wykonane dla modelu głosu powinny być neutralne emocjonalnie. Oznacza to, że smutna wypowiedź nie powinna być odczytywana w smutny sposób. Nastrój można dodać do syntetyzowanej mowy później za pomocą kontroli prozody. Pracuj ze swoim talentem głosowym, aby opracować "personę", która definiuje ogólny dźwięk i emocjonalny ton niestandardowego głosu. W tym procesie, będziesz wskazać, co "neutralne" brzmi jak dla tej persona.

Persona może mieć, na przykład, naturalnie optymistyczną osobowość. Tak więc "ich" głos może nosić nutę optymizmu, nawet jeśli mówią neutralnie. Jednak taka cecha osobowości powinna być subtelna i spójna. Słuchaj odczytów przez istniejące głosy, aby zorientować się, do czego dążysz.

> [!TIP]
> Zazwyczaj będziesz chciał posiadać nagrania głosowe, które robisz. Twój talent głosowy powinien być podatny na umowę o pracę na wynajem dla projektu.

## <a name="create-a-script"></a>Tworzenie skryptu

Punktem wyjścia dowolnej niestandardowej sesji nagrywania głosu jest skrypt, który zawiera wypowiedzi, które mają być wypowiedziane przez talent głosu. (Termin "wypowiedzi" obejmuje zarówno pełne zdania, jak i krótsze frazy).

Wypowiedzi w skrypcie mogą pochodzić z dowolnego miejsca: fikcji, non-fiction, transkrypcji przemówień, raportów prasowych i wszystkiego, co jest dostępne w formie drukowanej. Jeśli chcesz upewnić się, że Twój głos dobrze radzi sobie z konkretnymi rodzajami słów (takimi jak terminologia medyczna lub żargon programowania), możesz chcieć dołączyć zdania z prac naukowych lub dokumentów technicznych. Aby uzyskać krótką dyskusję na temat potencjalnych kwestii prawnych, zobacz sekcję ["Legalność".](#legalities) Możesz również napisać własny tekst.

Twoje wypowiedzi nie muszą pochodzić z tego samego źródła lub tego samego rodzaju źródła. Nie muszą nawet mieć ze sobą nic wspólnego. Jeśli jednak w aplikacji mowy użyjesz zestawów fraz (na przykład "Pomyślnie się zalogowałeś"), upewnij się, że są one uwzględniane w skrypcie. To da twojemu własnemu głosowi większą szansę na wypowiedzenie tych zwrotów. A jeśli zdecydujesz się użyć nagrania zamiast syntetyzowanej mowy, będziesz już mieć je w tym samym głosie.

Podczas gdy spójność jest kluczem do wyboru talentu głosowego, różnorodność jest cechą charakterystyczną dobrego skryptu. Skrypt powinien zawierać wiele różnych słów i zdań o różnych długościach zdań, strukturach i nastrojach. Każdy dźwięk w języku powinien być reprezentowany wielokrotnie i w wielu kontekstach *(tzw. zasięg fonetyczny).*

Ponadto tekst powinien zawierać wszystkie sposoby, w jakie dany dźwięk może być reprezentowany na piśmie, i umieszczać każdy dźwięk w różnych miejscach w zdaniach. Zarówno zdania deklaratywne, jak i pytania powinny być uwzględniane i odczytywane z odpowiednią intonacją.

Trudno jest napisać skrypt, który zapewnia *wystarczającą ilość* danych, aby umożliwić portalowi mowy niestandardowej tworzenie dobrego głosu. W praktyce najprostszym sposobem, aby skrypt, który osiąga niezawodne pokrycie fonetyczne jest uwzględnienie dużej liczby próbek. Standardowe głosy, które zapewnia firma Microsoft, zostały zbudowane z dziesiątek tysięcy wypowiedzi. Należy być przygotowanym do nagrywania od kilku do kilku tysięcy wypowiedzi co najmniej do tworzenia niestandardowego głosu jakości produkcji.

Sprawdź dokładnie skrypt pod kątem błędów. Jeśli to możliwe, niech ktoś inny też to sprawdzi. Po uruchomieniu przez skrypt ze swoim talentem, prawdopodobnie złapiesz jeszcze kilka błędów.

### <a name="script-format"></a>Format skryptu

Skrypt można napisać w programie Microsoft Word. Skrypt jest używany podczas sesji nagrywania, dzięki czemu można go skonfigurować w dowolny sposób, w jaki można łatwo pracować. Utwórz plik tekstowy wymagany przez portal Custom Voice oddzielnie.

Podstawowy format skryptu zawiera trzy kolumny:

* Liczba wypowiedź, począwszy od 1. Numerowanie ułatwia wszystkim w studio odwoływanie się do konkretnej wypowiedzi ("spróbujmy ponownie numer 356"). Za pomocą funkcji numerowania akapitu programu Word można automatycznie numerować wiersze tabeli.
* Pusta kolumna, w której zapiszesz numer lub kod czasu każdej wypowiedź, aby ułatwić znalezienie go w gotowym nagraniu.
* Tekst samego wypowiedź.

![Przykładowy skrypt](media/custom-voice/script.png)

> [!NOTE]
> Większość studiów nagrywa w krótkich segmentach znanych jako *trwa*. Każde take zazwyczaj zawiera 10 do 24 wypowiedzi. Po prostu zauważyć, że numer take jest wystarczające, aby znaleźć wypowiedź później. Jeśli nagrywasz w studiu, które woli nagrywać dłużej, warto zanotować kod czasu. Studio będzie miało widoczny wyświetlacz czasu.

Pozostaw wystarczająco dużo miejsca po każdym wierszu, aby pisać notatki. Upewnij się, że żadna wypowiedź nie jest podzielona między strony. Ponuń strony i wydrukuj skrypt na jednej stronie papieru.

Wydrukuj trzy kopie scenariusza: jeden dla talentu, jeden dla inżyniera i jeden dla reżysera (ciebie). Zamiast zszywek użyj spinacza do papieru: doświadczony artysta głosowy oddzieli strony, aby uniknąć hałasu podczas obracania stron.

### <a name="legalities"></a>Legalności

Zgodnie z prawem autorskim, czytanie przez aktora tekstu chronionego prawem autorskim może być spektaklem, za który autor utworu powinien otrzymać rekompensatę. Ta wydajność nie będzie rozpoznawalna w produkcie końcowym, niestandardowy głos. Mimo to legalność używania w tym celu utworu chronionego prawem autorskim nie jest dobrze ugruntowana. Firma Microsoft nie może udzielać porad prawnych w tej sprawie; skonsultuj się z własną radą.

Na szczęście można całkowicie uniknąć tych problemów. Istnieje wiele źródeł tekstu, którego możesz użyć bez zgody lub licencji.

|Źródło tekstu|Opis|
|-|-|
|[CMU Korpus Arktyczny](http://festvox.org/cmu_arctic/)|Około 1100 zdań wybranych z prac poza prawem autorskim specjalnie do wykorzystania w projektach syntezy mowy. Doskonały punkt wyjścia.|
|Działa już nie<br>zgodnie z prawami autorskimi|Zazwyczaj prace opublikowane przed 1923 rokiem. W języku angielskim [Project Gutenberg](https://www.gutenberg.org/) oferuje dziesiątki tysięcy takich prac. Możesz skupić się na nowszych pracach, ponieważ język będzie bliższy współczesnemu językowi angielskiemu.|
|Prace&nbsp;rządowe|Dzieła utworzone przez rząd Stanów Zjednoczonych nie są chronione prawami autorskimi w Stanach Zjednoczonych, chociaż rząd może rościć sobie prawa autorskie w innych krajach/regionach.|
|Domena publiczna|Utwory, do których prawa autorskie zostały wyraźnie zniechęcene lub które zostały przeznaczone do domeny publicznej. W niektórych jurysdykcjach zrzeczenie się praw autorskich może nie być możliwe.|
|Utwory licencjonowane|Utwory rozpowszechniane na licencji takiej jak Creative Commons lub GNU Free Documentation License (GFDL). Wikipedia używa GFDL. Niektóre licencje mogą jednak nakładać ograniczenia dotyczące wydajności licencjonowanej zawartości, które mogą mieć wpływ na tworzenie niestandardowego modelu głosu, dlatego należy uważnie przeczytać licencję.|

## <a name="recording-your-script"></a>Nagrywanie skryptu

Nagrywaj swój scenariusz w profesjonalnym studiu nagraniowym, które specjalizuje się w pracy głosowej. Będą mieli budkę nagraniową, odpowiedni sprzęt i odpowiednich ludzi do jego obsługi. Opłaca się nie skąpić na nagraniu.

Omów swój projekt z inżynierem nagrań studia i posłuchaj ich rad. Nagrywanie powinno mieć niewielką kompresję zakresu dynamicznego lub nie powinno być ono możliwe (maksymalnie 4:1). Bardzo ważne jest, aby dźwięk miał stałą głośność i wysoki stosunek sygnału do szumu, a jednocześnie był wolny od niepożądanych dźwięków.

### <a name="do-it-yourself"></a>Zrób to sam

Jeśli chcesz zrobić nagranie samodzielnie, zamiast iść do studia nagraniowego, oto krótki podkład. Dzięki wzrostowi nagrywania w domu i podcastingu, łatwiej niż kiedykolwiek znaleźć dobre porady i zasoby nagraniowe online.

Twoja "kabina nagraniowa" powinna być małym pomieszczeniem bez zauważalnego echa lub "tonu pomieszczenia". Powinien być tak cichy i dźwiękoszczelny, jak to możliwe. Zasłony na ścianach mogą być stosowane w celu zmniejszenia echa i neutralizacji lub "zakleszczać" dźwięk pomieszczenia.

Użyj wysokiej jakości studyjnego mikrofonu pojemnościowego (w skrócie mikrofonu) przeznaczonego do nagrywania głosu. Sennheiser, AKG, a nawet nowsze mikrofony Zoom mogą przynieść dobre rezultaty. Możesz kupić mikrofon lub wypożyczyć go w lokalnej wypożyczalni audiowizuacjowej. Poszukaj jednego z interfejsem USB. Ten typ mikrofonu wygodnie łączy element mikrofonu, przedwzmacniacza i konwerter analogowo-cyfrowy w jeden pakiet, upraszczając podłączanie.

Można również użyć mikrofonu analogowego. Wiele wypożyczalni oferuje "vintage" mikrofony znane ze swojego charakteru głosowego. Należy pamiętać, że profesjonalna przekładnia analogowa wykorzystuje zrównoważone złącza XLR, a nie wtyczkę 1/4 cala używaną w sprzęcie konsumenckim. Jeśli pójdziesz analogowy, będziesz również potrzebować przedwzmacniacz i interfejs audio komputera z tych złączy.

Zainstaluj mikrofon na stojaku lub wysięgniku i zainstaluj filtr pop przed mikrofonem, aby wyeliminować hałas z spółgłosek "plosive", takich jak "p" i "b". Niektóre mikrofony są wyposażone w uchwyt zawieszenia, który izoluje je od wibracji w stojaku, co jest pomocne.

Talent głosowy musi pozostawać w stałej odległości od mikrofonu. Użyj taśmy na podłodze, aby zaznaczyć, gdzie powinny stać. Jeśli talent woli siedzieć, należy zwrócić szczególną ostrożność, aby monitorować odległość mikrofonu i unikać hałasu krzesła.

Użyj stojaka, aby zatrzymać skrypt. Unikaj wędkowania stojaka, aby mógł odbijać dźwięk w kierunku mikrofonu.

Osoba obsługująca urządzenie rejestrujące — inżynier — powinna znajdować się w oddzielnym pomieszczeniu od talentu, w jakiś sposób porozmawiać z talentem w kabinie nagraniowej *(tor talkback).*

Nagrywanie powinno zawierać jak najmniej szumów, z celem 80-db stosunek sygnału do szumu lub lepiej.

Słuchaj uważnie nagrania ciszy w "kabinie", dowiedzieć się, gdzie każdy hałas pochodzi z, i wyeliminować przyczynę. Częstymi źródłami hałasu są otwory wentylacyjne, świetlówki, ruch na pobliskich drogach i wentylatory sprzętu (nawet notebooki mogą mieć wentylatory). Mikrofony i kable mogą odbierać hałas elektryczny z pobliskiego okablowania prądu przemiennego, zwykle szum lub szum. Brzęczenie może być również spowodowane przez *pętlę uziemienia,* co jest spowodowane posiadaniem sprzętu podłączonego do więcej niż jednego obwodu elektrycznego.

> [!TIP]
> W niektórych przypadkach można użyć korektora lub wtyczki oprogramowania redukcji szumów, aby usunąć szumy z nagrań, chociaż zawsze najlepiej jest zatrzymać go u źródła.

Ustaw poziomy tak, aby większość dostępnego zakresu dynamicznego nagrywania cyfrowego była używana bez nadmiernego. Oznacza to, że dźwięk jest głośny, ale nie tak głośny, że staje się zniekształcony. Przykład przebiegu dobrego nagrania jest pokazany na poniższej ilustracji:

![Dobry przebieg nagrywania](media/custom-voice/good-recording.png)

Tutaj używana jest większość zakresu (wysokości), ale najwyższe szczyty sygnału nie docierają do górnej lub dolnej części okna. Widać również, że cisza na nagraniu przybliża cienką poziomą linię, wskazującą niską podłogę. To nagranie ma akceptowalny zakres dynamiki i stosunek sygnału do szumu.

Nagrywaj bezpośrednio do komputera za pośrednictwem wysokiej jakości interfejsu audio lub portu USB, w zależności od używanego mikrofonu. Dla analogowych, zachować łańcuch audio proste: mikrofon, przedwzmacniacz, interfejs audio, komputer. Możesz licencjonować zarówno [Avid Pro Tools,](https://www.avid.com/en/pro-tools) jak i [Adobe Audition](https://www.adobe.com/products/audition.html) co miesiąc po rozsądnych kosztach. Jeśli budżet jest bardzo napięty, spróbuj wolnego [Audacity](https://www.audacityteam.org/).

Nagrywaj na poziomie 44,1 kHz 16-bitowy monofoniczny (jakość CD) lub lepszy. Aktualna najnowocześniejsza jest 48 kHz 24-bit, jeśli sprzęt obsługuje go. Przed przesłaniem go do portalu Custom Voice możesz pobrać próbkę dźwięku w dół do 16 kHz 16-bitowej. Mimo to opłaca się mieć wysokiej jakości oryginalne nagranie w przypadku, gdy potrzebne są zmiany.

Idealnie, mają różne osoby służą w roli reżysera, inżyniera i talentu. Nie próbuj robić tego wszystkiego sam. W celowniku, jedna osoba może być zarówno dyrektor i inżynier.

### <a name="before-the-session"></a>Przed sesją

Aby uniknąć marnowania czasu studyjnego, uruchom skrypt ze swoim talentem głosowym przed sesją nagraniową. Podczas gdy talent głosowy zapozna się z tekstem, mogą wyjaśnić wymowę nieznanych słów.

> [!NOTE]
> Większość studiów nagraniowych oferuje elektroniczne wyświetlanie skryptów w kabinie nagraniowej. W takim przypadku wpisz notatki przebiegu bezpośrednio w dokumencie skryptu. Nadal jednak będziesz chciał, aby kopia papierowa robić notatki podczas sesji. Większość inżynierów będzie chciała mieć również wydruk. I nadal będziesz chciał trzecią drukowaną kopię jako kopię zapasową dla talentu w przypadku, gdy komputer jest w dół.

Twój głos talent może zapytać, które słowo chcesz podkreślić w wypowiedzi ("słowo operacyjne"). Powiedz im, że chcesz naturalnej lektury bez szczególnego nacisku. Nacisk można dodać, gdy mowa jest syntetyzowana; nie powinno być częścią oryginalnego nagrania.

Skierować talent do wymawiania słów wyraźnie. Każde słowo skryptu powinno być wymawiane w formie pisemnej. Dźwięki nie powinny być pomijane lub niewyraźne razem, jak to jest powszechne w swobodnej mowie, *chyba że zostały napisane w ten sposób w skrypcie*.

|Tekst pisemny|Niechciana swobodna wymowa|
|-|-|
|nigdy się nie podda|nigdy się nie podda|
|są cztery światła|są cztery światła|
|jaka jest pogoda dzisiaj|jaka jest pogoda dzisiaj|
|przywitaj się z moim małym przyjacielem|przywitaj się z moim przyjacielem lil'|

Talent *nie* powinien dodawać wyraźnych przerw między słowami. Zdanie powinno nadal płynąć naturalnie, nawet gdy brzmi trochę formalnie. To drobne rozróżnienie może podjąć praktykę, aby uzyskać prawo.

### <a name="the-recording-session"></a>Sesja nagraniowa

Utwórz nagranie odwołania lub *plik dopasowania* typowego wypowiedź na początku sesji. Poproś talent, aby powtórzył tę linię na każdej lub więcej stronie. Za każdym razem należy porównać nowe nagranie z odniesieniem. Praktyka ta pomaga talent zachować spójność w objętości, tempo, wysokość i intonacji. Tymczasem inżynier może użyć pliku dopasowania jako odniesienia dla poziomów i ogólnej spójności dźwięku.

Plik dopasowania jest szczególnie ważny po wznowieniu nagrywania po przerwie lub w innym dniu. Będziesz chciał zagrać kilka razy dla talentu i poprosić ich o powtarzanie za każdym razem, dopóki nie pasują dobrze.

Trenerzy swój talent wziąć głęboki oddech i zatrzymać się na chwilę przed każdą wypowiedź. Nagraj kilka sekund ciszy między wypowiedziami. Słowa powinny być wymawiane w ten sam sposób za każdym razem, gdy się pojawiają, biorąc pod uwagę kontekst. Na przykład "record" jako zlecenie jest wymawiane inaczej niż "record" jako rzeczownik.

Nagraj dobre pięć sekund ciszy przed pierwszym nagraniem, aby uchwycić "dźwięk pokoju". Ta praktyka pomaga portalowi Custom Voice kompensować wszelkie pozostałe szumy w nagraniach.

> [!TIP]
> Wszystko, czego naprawdę potrzebujesz do uchwycenia, to talent głosowy, dzięki czemu możesz nagrać tylko swoje linie, aby nagrać monofoniczne (jednokanałowe) nagranie. Jeśli jednak nagrywasz w trybie stereo, możesz użyć drugiego kanału do nagrywania paplaniny w sterowni, aby uchwycić dyskusję na temat poszczególnych linii lub ma. Usuń tę ścieżkę z wersji przesłanej do portalu Niestandardowy głos.

Słuchaj uważnie, za pomocą słuchawek, do talentu głosowego wydajności. Szukasz dobrej, ale naturalnej dykcji, poprawnej wymowy i braku niechcianych dźwięków. Nie wahaj się poprosić swojego talentu, aby ponownie nagrać wypowiedź, która nie spełnia tych standardów.

> [!TIP]
> Jeśli używasz dużej liczby wypowiedzi, pojedynczy wypowiedź może nie mieć zauważalny wpływ na wynikowy głos niestandardowy. Bardziej celowe może być po prostu zanotowanie wszelkich wypowiedzi z problemami, wykluczenie ich z zestawu danych i sprawdzenie, jak twój niestandardowy głos się zmieni. Zawsze możesz wrócić do studia i nagrać pominięte próbki później.

Zanotuj numer podjęcia lub kod czasu w skrypcie dla każdej wypowiedź. Poproś inżyniera, aby zaznaczył każdą wypowiedź w metadanych lub arkuszu sygnalizacji nagrania.

Ruszaj regularne przerwy i zapewnij napój, aby pomóc twojemu talentowi głosowemu utrzymać swój głos w dobrej formie.

### <a name="after-the-session"></a>Po zakończeniu sesji

Nowoczesne studia nagraniowe działają na komputerach. Po zakończeniu sesji otrzymasz jeden lub więcej plików audio, a nie taśmę. Pliki te będą prawdopodobnie WAV lub AIFF w jakości CD (44.1 kHz 16-bit) lub lepiej. 24-bitowa 48 kHz jest powszechna i pożądana. Wyższe częstotliwość próbkowania, takie jak 96 kHz, na ogół nie są potrzebne.

Portal Niestandardowy głos wymaga, aby każda wypowiedź pod warunkiem, aby znajdować się w jego własnym pliku. Każdy plik audio dostarczony przez studio zawiera wiele wypowiedzi. Głównym zadaniem postprodukcyjnym jest więc podzielenie nagrań i przygotowanie ich do przesłania. Inżynier nagrywania może umieścić znaczniki w pliku (lub pod warunkiem, oddzielny arkusz sygnalizacji), aby wskazać, gdzie rozpoczyna się każda wypowiedź.

Użyj notatek, aby znaleźć dokładnie żądane, a następnie użyj narzędzia do edycji dźwięku, takiego jak [Avid Pro Tools,](https://www.avid.com/en/pro-tools) [Adobe Audition](https://www.adobe.com/products/audition.html)lub free [Audacity](https://www.audacityteam.org/), aby skopiować każdą wypowiedź do nowego pliku.

Zostaw tylko około 0,2 sekundy ciszy na początku i na końcu każdego klipu, z wyjątkiem pierwszego. Ten plik powinien zaczynać się od pełnej pięciosekundowej ciszy. Nie używaj edytora audio do "wyzerować" ciche części pliku. Włączenie "tonu pomieszczenia" pomoże algorytmom Custom Voice skompensować wszelkie resztki szumów tła.

Słuchaj każdego pliku uważnie. Na tym etapie możesz edytować małe niechciane dźwięki, które przegapiłeś podczas nagrywania, takie jak niewielkie wargi przed linią, ale uważaj, aby nie usunąć żadnej rzeczywistej mowy. Jeśli nie możesz naprawić pliku, usuń go z zestawu danych i zwróć uwagę, że to zrobiłeś.

Przekonwertować każdy plik do 16 bitów i częstotliwość próbkowania 16 kHz przed zapisaniem i, jeśli nagrałeś studio gadać, usunąć drugi kanał. Zapisz każdy plik w formacie WAV, nazywając pliki numerem wypowiedź ze skryptu.

Na koniec utwórz *transkrypcję,* która kojarzy każdy plik WAV z wersją tekstową odpowiedniej wypowiedź. [Tworzenie niestandardowych czcionek głosowych](how-to-customize-voice-font.md) zawiera szczegóły wymaganego formatu. Tekst można skopiować bezpośrednio ze skryptu. Następnie utwórz plik Zip plików WAV i transkrypcję tekstu.

Archiwizuj oryginalne nagrania w bezpiecznym miejscu na wypadek, gdyby były potrzebne później. Zachowaj również skrypt i notatki.

## <a name="next-steps"></a>Następne kroki

Możesz przesłać nagrania i utworzyć swój niestandardowy głos.

> [!div class="nextstepaction"]
> [Tworzenie niestandardowych czcionek głosowych](how-to-customize-voice-font.md)
