---
title: Tutorial projektu Akustyka Nierealna Projekt
titlesuffix: Azure Cognitive Services
description: W tym samouczku opisano przepływ pracy projektu dla akustyki projektu w unreal i wwise.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: 817a11171c5b4b4ef205e5fbb04f9b6d6d85b248
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68854244"
---
# <a name="project-acoustics-unrealwwise-design-tutorial"></a>Projekt Akustyka Unreal / Wwise Design Tutorial
W tym samouczku opisano konfigurację projektu i przepływ pracy dla akustyki projektu w unreal i Wwise.

Wymagania wstępne dotyczące oprogramowania:
* Nierealny projekt z wtyczek Project Acoustics Wwise i Unreal

Aby uzyskać projekt Unreal z akustyką projektu, możesz:
* Postępuj zgodnie z instrukcjami [integracji Projektu Akustyka Nierealna,](unreal-integration.md) aby dodać akustykę projektu do projektu Unreal
* Można też użyć [przykładowego projektu Akustyka projektu](unreal-quickstart.md).

## <a name="setup-project-wide-wwise-properties"></a>Konfigurowanie właściwości w usłudze Wwise w całym projekcie
Wwise has global obstruction and occlusion curves that affect how the Project Acoustics plugin drives the Wwise audio DSP.

### <a name="design-wwise-occlusion-curves"></a>Projektowanie krzywych okluzji Wwise
Gdy akustyka projektu jest aktywna, reaguje na krzywe niedrożności, filtr dolnych (LPF) i filtr wysokiego przejścia (HPF) ustawione w programie Wwise. Zalecamy ustawienie typu krzywej głośności na liniową z wartością -100 dB dla wartości okluzji 100.

Przy tym ustawieniu, jeśli symulacja Akustyka projektu oblicza okluzję -18 dB, zostanie ona wprowadzona do poniższej krzywej na X=18, a odpowiednia wartość Y jest zastosowanym tłumieniem. Aby wykonać połowę okluzji, ustaw punkt końcowy na -50 dB zamiast -100 dB lub -200 dB, aby wyolbrzymić okluzję. Możesz dostosować i dostroić dowolną krzywą, która najlepiej sprawdza się w Twojej grze.
 
![Zrzut ekranu przedstawiający edytor krzywej okluzji Wwise](media/wwise-occlusion-curve.png)

### <a name="disable-wwise-obstruction-curves"></a>Wyłącz krzywe przeszkody Wwise
Krzywe przeszkody Wwise wpływają na poziom suchości w izolacji, ale projekt Akustyka wykorzystuje elementy sterujące projektowe i symulację do wymuszania proporcji mokro/suche. Zalecamy wyłączenie krzywej objętości przeszkód. Aby zaprojektować wilgotność, należy użyć opisanego później formantu Wetness Adjust.
 
Jeśli używasz krzywych przeszkód LPF/HPF do innych celów, upewnij się, że ustawiłeś je na Y=0 przy X=0 (oznacza to, że nie ma LPF lub HPF, gdy nie ma przeszkód).

![Zrzut ekranu przedstawiający edytor krzywej przeszkody Wwise](media/wwise-obstruction-curve.png)

### <a name="design-project-acoustics-mixer-parameters"></a>Parametry miksera Akustyki Projektu Projektu
Globalne właściwości pogłosu można kontrolować, odwiedzając kartę wtyczki miksera w programie Project Acoustics Bus. Kliknij dwukrotnie "Project Acoustics Mixer (Custom)", aby otworzyć panel ustawień wtyczki miksera.

Widać również, że wtyczka miksera ma opcję "Wykonaj spatialization". Jeśli wolisz używać wbudowanej spatialization programu Project Acoustic, zaznacz pole wyboru "Wykonywanie przestrzennej" i wybierz jedną z opcji HRTF lub Panning. Pamiętaj, aby wyłączyć skonfigurowane autobusy Dry Aux, w przeciwnym razie usłyszysz bezpośrednią ścieżkę dwa razy. Użyj "Wetness Adjust" i "Reverb Time Scale Factor", aby sprawować globalną kontrolę nad mieszanką pogłosu. Uwaga należy ponownie uruchomić Unreal, a następnie ponownie regenerować soundbanks przed naciśnięciem play odebrać mixer plugin zmiany konfiguracji, takie jak "Perform Spatialization" pole wyboru.

![Zrzut ekranu przedstawiający opcje wtyczki miksera Project Acoustics Wwise](media/mixer-plugin-global-settings.png)

## <a name="set-project-acoustics-design-controls-in-the-wwise-actor-mixer-hierarchy"></a>Ustawianie formanty projektu Akustyka projektu w hierarchii aktora-miksera Wwise
Aby kontrolować parametry poszczególnych aktora miksera, kliknij dwukrotnie na Actor-Mixer, a następnie kliknij na jego mixer plug-in kartę. Tutaj będziesz mógł zmienić wszystkie parametry na poziomie dźwięku. Wartości te łączą się z wartościami ustawionymi od strony Unreal (opisane poniżej). Na przykład jeśli wtyczka Akustyka projektu ustawia regulację zewnętrzna na obiekcie na 0,5, a Wwise ustawi ją na -0,25, wynikowa regulacja zewnętrzna zastosowana do tego dźwięku wynosi 0,25.

![Zrzut ekranu przedstawiający ustawienia miksera dźwięku w hierarchii mieszania aktorów Wwise'a](media/per-sound-mixer-settings.png)

### <a name="ensure-the-aux-bus-has-dry-send-and-output-bus-has-wet-send"></a>Upewnij się, że magistrala aux ma suche wysyłanie, a magistrala wyjściowa ma mokre wysyłanie
Należy pamiętać, że wymagana konfiguracja miksera aktora wymienia zwykłe trasy suche i mokre w Wwise. Wytwarza sygnał pogłosu na magistrali wyjściowej miksera aktora (ustawionej na Project Acoustics Bus) i suchym sygnale wzdłuż zdefiniowanej przez użytkownika magistrali aux. Routing ten jest wymagany ze względu na funkcje interfejsu API wtyczki miksera Wwise, z których korzysta wtyczka Project Acoustics Wwise.

![Zrzut ekranu przedstawiający edytor Wwise przedstawiający wskazówki dotyczące projektowania głosu dla projektu Akustyka](media/voice-design-guidelines.png)
 
### <a name="set-up-distance-attenuation-curves"></a>Ustawianie krzywych tłumienia odległości
Upewnij się, że każda krzywa tłumienia używana przez miksery aktorów przy użyciu akustyki projektu mają zdefiniowane przez użytkownika aux send ustawione na "głośność magistrali wyjściowej". Wwise robi to domyślnie dla nowo utworzonych krzywych tłumienia. Jeśli przeprowadzasz migrację istniejącego projektu, sprawdź ustawienia krzywej.

Domyślnie symulacja akustyka projektu ma promień 45 metrów wokół lokalizacji odtwarzacza. Ogólnie zalecamy ustawienie krzywej tłumienia na -200 dB wokół tej odległości. Ta odległość nie jest trudnym ograniczeniem. Dla niektórych brzmi jak broni może chcesz większy promień. W takich przypadkach zastrzeżeniem jest to, że tylko geometria w odległości 45 m od lokalizacji gracza będzie uczestniczyć. Jeśli gracz znajduje się w pokoju, a źródło dźwięku znajduje się poza pokojem i 100 m od hotelu, zostanie ono prawidłowo zasłone. Jeśli źródło znajduje się w pokoju, a gracz znajduje się na zewnątrz i 100 m od hotelu, nie będzie właściwie zasłonięty.

![Zrzut ekranu przedstawiający krzywe tłumienia Wwise](media/atten-curve.png)

### <a name="post-mixer-equalization"></a>Korekta miksera pocztowego ###
 Inną rzeczą, którą możesz chcieć zrobić, to dodać korektor miksera post. Magistralę Akustyka projektu można traktować jako typową magistralę pogłosu (w domyślnym trybie pogłosu) i umieścić na niej filtr, aby wykonać wyrównanie. Możesz zobaczyć próbkę tego w projekcie Akustyka projektu Wwise przykładowy projekt.

![Zrzut ekranu przedstawiający korektor po mikserze Wwise](media/wwise-post-mixer-eq.png)

Na przykład filtr wysokiego przejścia może pomóc w obsłudze basów z nagrań z bliskiego pola, które dają boomy, nierealistyczny pogłos. Możesz również uzyskać większą kontrolę po wypieku, dostosowując korektor za pomocą KONTROLERÓW RTPC, co pozwala na zmianę koloru pogłosu w czasie gry.

## <a name="set-up-scene-wide-project-acoustics-properties"></a>Konfigurowanie właściwości akustyka projektu w całej scenie

Akustyka Space aktor udostępnia wiele formantów, które modyfikują zachowanie systemu i są przydatne w debugowaniu.

![Zrzut ekranu przedstawiający nierealne elementy sterujące przestrzenią akustyczną](media/acoustics-space-controls.png)

* **Dane akustyki:** To pole musi być przypisane do zasób akustyki pieczone z katalogu Zawartość/Akustyka. Wtyczka Project Acoustics automatycznie doda katalog Zawartość/Akustyka do katalogów spakowanych projektu.
* **Rozmiar płytki:** Zakresy regionu wokół odbiornika, który ma dane akustyki załadowany do pamięci RAM. Tak długo, jak sondy odbiornika bezpośrednio wokół odtwarzacza są ładowane w, wyniki są takie same jak ładowanie danych akustycznych dla wszystkich sond. Większe kafelki zużywają więcej pamięci RAM, ale zmniejszają liczbę we/wy dysku
* **Automatyczny strumień:** Po włączeniu automatycznie ładuje się w nowych kafelkach, gdy odbiornik osiągnie krawędź załadowanego regionu. Po wyłączeniu musisz ręcznie załadować nowe kafelki za pomocą kodu lub planów
* **Skala pamięci podręcznej:** steruje rozmiarem pamięci podręcznej używanej do zapytań akustycznych. Mniejsza pamięć podręczna zużywa mniej pamięci RAM, ale może zwiększyć użycie procesora CPU dla każdego zapytania.
* **Akustyka włączona:** Formant debugowania, aby umożliwić szybkie przełączanie A /B symulacji akustyki. Ten formant jest ignorowany w konfiguracjach wysyłki. Formant jest przydatny do znajdowania, jeśli określony błąd audio pochodzi z obliczeń akustyki lub innego problemu w projekcie Wwise.
* **Aktualizuj odległości:** Użyj tej opcji, jeśli chcesz użyć wstępnie upieczonych informacji akustyki dla zapytań o odległość. Te zapytania są podobne do promieni, ale zostały wstępnie obliczone, więc zajmują znacznie mniej procesora. Przykład użycia jest dla dyskretnych odbić od najbliższej powierzchni do odbiornika. Aby w pełni wykorzystać tę dźwignię, musisz użyć kodu lub planów, aby zbadać odległości.
* **Statystyki rysowania:** Podczas gdy `stat Acoustics` UE może dostarczyć ci informacje o procesorze, ten wyświetlacz stanu pokaże aktualnie załadowany plik ACE, użycie pamięci RAM i inne informacje o stanie w lewym górnym rogu ekranu.
* **Rysuj Voxels:** Nakładanie woksiątek w pobliżu odbiornika pokazujące siatkę voxel używaną podczas interpolacji środowiska uruchomieniowego. Jeśli emiter znajduje się wewnątrz środowiska uruchomieniowego voxel, zakończy się niepowodzeniem zapytania akustyczne.
* **Sondy rysowania:** Pokaż wszystkie sondy dla tej sceny. Będą to różne kolory w zależności od ich stanu obciążenia.
* **Narysuj odległości:** Jeśli opcja Aktualizuj odległości jest włączona, spowoduje to wyświetlenie pola na najbliższej powierzchni odbiornika w ilościowych kierunkach wokół odbiornika.

## <a name="actor-specific-acoustics-design-controls"></a>Sterowanie projektem akustyki specyficzne dla aktora
Te formanty projektu są ograniczone do pojedynczego składnika audio w Unreal.

![Zrzut ekranu przedstawiający nierealne elementy sterujące komponentami audio](media/audio-component-controls.png)

* **Mnożnik okluzji:** Steruje efektem okluzji. Wartości > 1 wzmocnią okluzję. Wartości <1 zminimalizują go.
* **Regulacja wilgotności:** Dodatkowe pogłos dB
* **Mnożnik czasu rozpadu:** Steruje RT60 mnogie, w oparciu o dane wyjściowe symulacji akustyki
* **Regulacja zewnętrzności:** Steruje, jak na zewnątrz jest pogłos. Wartości bliższe 0 są bardziej wewnętrzne, bliżej 1 są bardziej na zewnątrz. Ta regulacja jest addytywna, więc ustawienie go na -1 wymusi w pomieszczeniach, ustawienie go na +1 wymusi na zewnątrz.
* **Transmisja Db:** Renderuj dodatkowy dźwięk przez ścianę dzięki tej głośności w połączeniu z tłumieniem odległości na podstawie linii wzroku.
* **Mokra odległość osnowa:** Dostosowuje charakterystyki pogłosu na źródle tak, jakby znajdowało się bliżej/dalej, bez wpływu na ścieżkę bezpośrednią.
* **Graj na starcie:** Przełącz, aby określić, czy dźwięk ma być odtwarzany automatycznie podczas rozpoczynania sceny. Domyślnie włączony.
* **Pokaż parametry akustyczne:** Wyświetla informacje debugowania bezpośrednio na górze składnika w grze. (tylko w przypadku konfiguracji niezwiązanych z wysyłką)

## <a name="blueprint-functionality"></a>Funkcjonalność planu
Akustyka Space aktor jest dostępny za pośrednictwem planu, zapewniając funkcje, takie jak ładowanie mapy lub modyfikowanie ustawień za pomocą skryptów poziomu. Podamy tutaj dwa przykłady.

### <a name="add-finer-grained-control-over-streaming-load"></a>Dodaj lepszą kontrolę nad obciążeniem przesyłania strumieniowego
Aby samodzielnie zarządzać strumieniowaniem danych akustycznych zamiast przesyłania strumieniowego automatycznie na podstawie pozycji gracza, można użyć funkcji schematu wymuszenia ładowania kafelka:

![Zrzut ekranu przedstawiający opcje przesyłania strumieniowego planu w trybie Unreal](media/blueprint-streaming.png)

* **Cel:** Aktor AcousticsSpace
* **Pozycja środkowa:** Centrum regionu, który wymaga załadowania danych
* **Rozładuj sondy zewnętrzne płytki:** Jeśli ta opcja jest zaznaczona, wszystkie sondy niew nowym regionie zostaną wyładowane z pamięci RAM. Jeśli ta zaznaczona jest niezaznaczona, nowy region jest ładowany do pamięci, pozostawiając istniejące sondy również załadowane do pamięci
* **Blok po zakończeniu:** Sprawia, że ładowanie kafelków jest operacją synchroniką

Rozmiar kafelka musi być już ustawiony przed wywołaniem wymuszania płytki ładunia. Na przykład można zrobić coś takiego, aby załadować plik ACE, ustawić rozmiar kafelka i przesyłać strumieniowo w regionie:

![Zrzut ekranu przedstawiający opcje konfiguracji przesyłania strumieniowego w obszarze Unreal](media/streaming-setup.png)

Funkcja planu akustyki obciążenia używane w tym przykładzie ma następujące parametry:

* **Cel:** Aktor AcousticsSpace.
* **Nowy piec:** Zasób danych akustyki do załadowania. Pozostawienie tego pustego/ustawienia na wartość null spowoduje zwolnienie bieżącego pieca bez ładowania nowego.

### <a name="optionally-query-for-surface-proximity"></a>Opcjonalnie zapytanie o bliskość powierzchni
Jeśli chcesz zobaczyć, jak blisko powierzchnie znajdują się w określonym kierunku wokół odbiornika, można użyć funkcji Odległość kwerendy. Ta funkcja może być przydatna do jazdy kierunkowe opóźnione odbicia, lub dla innych logiki gry napędzany przez bliskość powierzchni. Kwerenda jest tańsza niż rzutowania promieniami, ponieważ wyniki są pobierane z tabeli wyszukiwania akustyki.

![Zrzut ekranu przedstawiający przykładowe zapytanie odległości planu](media/distance-query.png)

* **Cel:** Aktor AcousticsSpace
* **Kierunek wyglądu:** Kierunek, w jakim należy się wysuwać, wyśrodkowany na odbiorniku
* **Odległość:** Jeśli kwerenda zakończy się pomyślnie, odległość do najbliższej powierzchni
* **Wartość zwrotu:** Wartość logiczna — wartość true, jeśli kwerenda powiodła się, w przeciwnym razie fałsz

## <a name="next-steps"></a>Następne kroki
* Poznaj pojęcia stojące za [procesem projektowania](design-process.md)
* [Tworzenie konta platformy Azure](create-azure-account.md) w celu upieczenia własnej sceny


