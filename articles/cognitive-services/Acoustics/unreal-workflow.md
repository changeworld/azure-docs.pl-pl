---
title: Samouczek dotyczący projektowania Unreali projektu
titlesuffix: Azure Cognitive Services
description: W tym samouczku opisano przepływ pracy projektowania pod kątem hałasu związanych z projektem w Unreal i Wwise.
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
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2019
ms.locfileid: "68854244"
---
# <a name="project-acoustics-unrealwwise-design-tutorial"></a>Samouczek projektowy Unreal/Wwise
W tym samouczku opisano konfigurację projektu i przepływ pracy dla hałasu związanych z projektem w Unreal i Wwise.

Wymagania wstępne dotyczące oprogramowania:
* Projekt Unreal z Wwiseami i Unreal wtyczki projektu

Aby uzyskać projekt Unreal z Akustycznością projektu, możesz:
* Postępuj zgodnie z instrukcjami dotyczącymi [integracji Unreali projektu](unreal-integration.md) , aby dodać akustyczne projektu do projektu Unreal
* Lub użyj przykładowego [projektu akustycznego projektu](unreal-quickstart.md).

## <a name="setup-project-wide-wwise-properties"></a>Konfigurowanie właściwości Wwise na poziomie projektu
Wwise ma globalne zakłócić i zamknięcia krzywe, które wpływają na to, jak wtyczka akustyczna projektu napędy DSP audio Wwise.

### <a name="design-wwise-occlusion-curves"></a>Projektowanie krzywych Wwise zamknięcia
Gdy akustyczny jest aktywny, reaguje na wolumin zamknięcia, filtr z niską passem (LPF) i krzywe High-Pass Filter (HPF) ustawione w Wwise. Zalecamy ustawienie typu krzywej głośności na liniowy z wartością-100 dB dla wartości zamknięcia 100.

Jeśli to ustawienie ma wartość, jeśli symulacja akustyczna projektu obliczy zamknięcia z-18 dB, przejdziemy do poniższej krzywej w X = 18 i odpowiadającą jej wartością Y jest zastosowane tłumienie. Aby wykonać połowę zamknięcia, należy ustawić punkt końcowy na-50 dB, a nie 100 dB lub do-200 dB do exaggerate zamknięcia. Możesz dostosować i dostosować dowolną krzywą, która najlepiej sprawdza się w przypadku gry.
 
![Zrzut ekranu przedstawiający Edytor krzywej Wwise zamknięcia](media/wwise-occlusion-curve.png)

### <a name="disable-wwise-obstruction-curves"></a>Wyłącz krzywe przeszkód Wwise
Krzywe przeszkód Wwise wpływają na suchy poziom izolacji, ale akustyczne w projekcie używa formantów i symulacji projektu, aby wymusić przełożenie mokre/suche. Zalecamy wyłączenie krzywej głośności przeszkód. Aby zaprojektować wetness, użyj kontrolki dopasowywania wetness opisanej w dalszej części.
 
Jeśli używasz krzywych LPF/HPF do innych celów, upewnij się, że zostały ustawione na wartość Y = 0 w X = 0 (oznacza to, że nie ma żadnych LPF ani HPF, gdy nie ma przeszkód).

![Zrzut ekranu przedstawiający Edytor krzywej Wwise przeszkody](media/wwise-obstruction-curve.png)

### <a name="design-project-acoustics-mixer-parameters"></a>Parametry miksera hałasu projektu projektu
Właściwości globalne Reverb można kontrolować, odwiedzając kartę wtyczka miksera magistrali dźwiękowej. Kliknij dwukrotnie "mikser" w projekcie (niestandardowy) ", aby otworzyć panel Ustawienia wtyczki miksera.

Możesz również sprawdzić, czy wtyczka miksera ma opcję "wykonaj Spatialization". Jeśli wolisz korzystać z wbudowanej spatialization akustycznej, zaznacz pole wyboru "wykonaj Spatialization" i wybierz jedną z HRTF lub panoramowania. Upewnij się, że wyłączono wszystkie skonfigurowane suche pomocnicze Busses, w przeciwnym razie Podwójna ścieżka zostanie posłuchana. Użyj wskaźnika "wetness Dopasuj" i "Reverb skali czasu", aby wykorzystać globalną kontrolę na potrzeby mieszania Reverb. Należy pamiętać, że należy ponownie uruchomić Unreal, a następnie wygenerować soundbanks przed naciśnięciem przycisku odtwarzania, aby pobrać zmiany konfiguracji wtyczki miksera, takie jak pole wyboru "wykonaj Spatialization".

![Zrzut ekranu opcji wtyczki Wwise miksera projektu](media/mixer-plugin-global-settings.png)

## <a name="set-project-acoustics-design-controls-in-the-wwise-actor-mixer-hierarchy"></a>Ustaw kontrolki projektowe akustyczne projektu w hierarchii Wwise aktora
Aby kontrolować parametry pojedynczego miksera aktora, kliknij dwukrotnie na mikser aktora, a następnie kliknij kartę wtyczka miksera. W tym miejscu będziesz mieć możliwość zmiany parametrów na poziomie dźwięku. Te wartości łączą się z zestawami z Unreal (opisane poniżej). Na przykład, jeśli wtyczka Unreale w projekcie ustawia korektę na zewnątrz dla obiektu na 0,5, a Wwise ustawia go na-0,25, wynikiem dostosowania na zewnątrz zastosowanego do tego dźwięku jest 0,25.

![Zrzut ekranu przedstawiający ustawienia miksera dźwięku w Wwiseej hierarchii miksera](media/per-sound-mixer-settings.png)

### <a name="ensure-the-aux-bus-has-dry-send-and-output-bus-has-wet-send"></a>Upewnij się, że magistrala AUX ma suchy wysyłanie, a magistrala wyjściowa ma do nich wysyłanie.
Należy pamiętać, że wymagana konfiguracja miksera aktora polega na rozmieszczeniu zwykłych i mokrych routingu w Wwise. Generuje sygnał Reverb na magistrali wyjściowej miksera aktora (ustawiany na magistrali akustycznej projektu) i wysuszy sygnał wzdłuż magistrali AUX zdefiniowanej przez użytkownika. Ten Routing jest wymagany ze względu na funkcje interfejsu API wtyczki Wwise, które jest używane przez wtyczkę projektu.

![Zrzut ekranu edytora Wwise pokazujący wskazówki dotyczące projektowania głosu dla hałasu związanych z projektem](media/voice-design-guidelines.png)
 
### <a name="set-up-distance-attenuation-curves"></a>Skonfiguruj krzywe tłumienia odległości
Upewnij się, że wszystkie krzywe tłumienia używane przez miksery aktora przy użyciu funkcji akustycznych projektu mają zdefiniowany przez użytkownika atrybut AUX "wyjściowy magistrala". Wwise domyślnie dla nowo utworzonych krzywych tłumienia. W przypadku migrowania istniejącego projektu Sprawdź ustawienia krzywej.

Domyślnie symulacja akustyczna projektu ma promień 45 metrów wokół lokalizacji odtwarzacza. Zwykle zalecamy ustawienie krzywej tłumienia na 200 dB wokół tej odległości. Ta odległość nie jest twardym ograniczeniem. W przypadku niektórych dźwięków, takich jak broń, może być potrzebny większy promień. W takich przypadkach należy zapamiętać, że tylko geometria w obrębie 45 m lokalizacji odtwarzacza będzie uczestniczyć. Jeśli gracz znajduje się w pokoju, a źródło dźwięku znajduje się poza miejscem i 100 mln, zostanie prawidłowo zamkniętee. Jeśli źródło znajduje się w pokoju, a gracz jest poza i 100 m, nie zostanie prawidłowo zamknięte.

![Zrzut ekranu przedstawiający krzywe tłumienia Wwise](media/atten-curve.png)

### <a name="post-mixer-equalization"></a>Wyrównywanie po mikserze ###
 Jednym z nich może być potrzeba dodania korektora po mikserze. Magistralę akustyczną projektu można traktować jako typową magistralę Reverb (w domyślnym trybie reverb) i umieścić w niej filtr w celu przeprowadzenia korektora. Możesz zobaczyć próbkę tego przykładu w projekcie Wwise przykładowy projekt.

![Zrzut ekranu przedstawiający Wwise po zakończeniu miksera](media/wwise-post-mixer-eq.png)

Na przykład filtr o wysokiej przebiegu może pomóc w obsłudze niskich pól, które zwracają wysięgnik, nierealistyczny Reverb. Możesz również uzyskać więcej kontroli po tworzenie przez dostosowanie instrukcji EQ do RTPCs, co pozwala na zmianę koloru Reverb w czasie gry.

## <a name="set-up-scene-wide-project-acoustics-properties"></a>Konfigurowanie właściwości akustycznych dla całej sceny

Aktor obszaru akustycznego uwidacznia wiele kontrolek, które modyfikują zachowanie systemu i są przydatne w debugowaniu.

![Zrzut ekranu kontrolek obszaru Unreal akustycznych](media/acoustics-space-controls.png)

* **Dane akustyczne:** Do tego pola należy przypisać element rozszerzania akustyczny z katalogu content/akustyczny. Wtyczka akustyczna projektu automatycznie dodaje katalog Content/akustyczny do spakowanych katalogów projektu.
* **Rozmiar kafelka:** Zakres regionu wokół odbiornika, który chce, aby dane akustyczne zostały załadowane do pamięci RAM. Tak długo, jak sondy odbiornika natychmiast wokół odtwarzacza są ładowane, wyniki są takie same jak ładowanie danych akustycznych dla wszystkich sond. Większe kafelki używają większej ilości pamięci RAM, ale zmniejsz liczbę operacji we/wy dysku
* **Strumień autostream:** Gdy ta funkcja jest włączona, program automatycznie ładuje nowe kafelki, gdy odbiornik osiągnie krawędź załadowanego regionu. Po wyłączeniu należy ręcznie załadować nowe kafelki za pośrednictwem kodu lub planów
* **Skala pamięci podręcznej:** określa rozmiar pamięci podręcznej używanej na potrzeby zapytań akustycznych. Mniejsza pamięć podręczna zużywa mniej pamięci RAM, ale może zwiększyć użycie procesora dla każdego zapytania.
* **Włączone akustyczne:** Kontrolka debugowania umożliwiająca szybkie włączenie/B przełączania symulacji akustycznej. Ten formant jest ignorowany w konfiguracjach wysyłki. Formant jest przydatny do znajdowania, czy konkretna usterka audio pochodzi z obliczeń akustycznych lub innego problemu w projekcie Wwise.
* **Odległość aktualizacji:** Użyj tej opcji, jeśli chcesz użyć informacji o wykorzystaniu rozszerzania na potrzeby zapytań dotyczących odległości. Te zapytania są podobne do rzutowania ray, ale zostały wstępnie obliczone, aby zająć dużo mniej CPU. Przykładowe użycie dotyczy odniesień dyskretnych od najbliższej powierzchni do odbiornika. Aby w pełni wykorzystać to rozwiązanie, należy użyć kodu lub planów do wykonywania zapytań na odległość.
* **Rysuj statystyki:** Mimo że program `stat Acoustics` UE może udostępnić informacje o procesorze CPU, ten stan wyświetlania będzie zawierać aktualnie załadowany plik ACE, użycie pamięci RAM oraz inne informacje o stanie w lewym górnym rogu ekranu.
* **Voxels remisu:** Nałóż voxels blisko odbiornika, który pokazuje siatkę Voxel używaną podczas interpolacji środowiska uruchomieniowego. Jeśli emitowanie znajduje się wewnątrz Voxel środowiska uruchomieniowego, spowoduje to niepowodzenie zapytań akustycznych.
* **Sondy rysowania:** Pokaż wszystkie sondy dla tej sceny. Będą one różne kolory w zależności od ich stanu ładowania.
* **Odległość rysowania:** Jeśli jest włączona funkcja odległość aktualizacji, spowoduje to wyświetlenie pola na najbliższej powierzchni odbiornika w Quantized.

## <a name="actor-specific-acoustics-design-controls"></a>Kontrolki projektowania charakterystyczne dla aktora
Te kontrolki projektowe należą do zakresu pojedynczego składnika audio w Unreal.

![Zrzut ekranu kontrolek składnika audio Unreal](media/audio-component-controls.png)

* **Mnożnik zamknięcia:** Kontroluje efekt zamknięcia. Wartości > 1 będą wzmocnić zamknięcia. Wartości < 1 zminimalizują ją.
* **Wetness:** Dodatkowa baza danych Reverb
* **Mnożnik czasu zanikania:** Kontroluje RT60 multiplicatively na podstawie danych wyjściowych symulacji akustycznej
* **Korekta na zewnątrz:** Kontroluje, jak działa reverberation. Wartości bliżej 0 to więcej niedrzwi, bliżej 1 jest więcej na zewnątrz. Ta korekta to dodatek, więc ustawienie go na wartość-1 spowoduje wymuszenie niedrzwi, ustawienie dla niego opcji + 1 spowoduje wymuszanie na zewnątrz.
* **Baza danych transmisji:** Renderowanie dodatkowego dźwięku za pośrednictwem ściany z tą głośnością w połączeniu z tłumieniem odległości opartego na przewodniku.
* **Wypaczenie odległości w stanie mokrym:** Dostosowuje charakterystyki reverberation w źródle tak, jakby były bliżej/dalej, bez wpływu na ścieżkę bezpośrednią.
* **Odtwórz przy uruchomieniu:** Przełącz, aby określić, czy dźwięk ma być odtwarzany automatycznie na początku sceny. Domyślnie włączone.
* **Pokaż parametry akustyczne:** Wyświetlaj informacje debugowania bezpośrednio na początku składnika w grze. (tylko w przypadku konfiguracji niezwiązanych z wysyłką)

## <a name="blueprint-functionality"></a>Funkcje planu
Aktor obszaru akustycznego jest dostępny za pośrednictwem strategii, zapewniającej funkcje, takie jak ładowanie mapy lub modyfikowanie ustawień za pośrednictwem obsługi skryptów na poziomie. W tym miejscu oferujemy dwa przykłady.

### <a name="add-finer-grained-control-over-streaming-load"></a>Dodawanie precyzyjnej kontroli nad obciążeniem przesyłania strumieniowego
Aby samodzielnie zarządzać transmisjami danych akustycznych zamiast przesyłania strumieniowego automatycznie na podstawie pozycji odtwarzacza, można użyć funkcji Wymuś obciążenie kafelka:

![Zrzut ekranu przedstawiający opcje przesyłania strumieniowego planów w Unreal](media/blueprint-streaming.png)

* **Obiektów** Aktor AcousticsSpace
* **Pozycja środka:** Środek regionu, który wymaga załadowania danych
* **Rozładowywania sondy poza kafelkiem:** W przypadku zaznaczenia tej opcji wszystkie sondy, które nie są w nowym regionie, zostaną zwolnione z pamięci RAM. W przypadku usunięcia zaznaczenia nowy region jest ładowany do pamięci, pozostawiając istniejące sondy ładowane również do pamięci
* **Blokuj po zakończeniu:** Sprawia, że kafelek ładuje operację synchroniczną

Rozmiar kafelka musi być już ustawiony przed wywołaniem wymuszenia obciążenia kafelka. Można na przykład wykonać czynności podobne do tego w celu załadowania pliku ACE, ustawienia rozmiaru kafelka i strumienia w regionie:

![Zrzut ekranu przedstawiający opcje konfiguracji przesyłania strumieniowego w Unreal](media/streaming-setup.png)

Funkcja planu danych obciążenia akustycznego użyta w tym przykładzie ma następujące parametry:

* **Obiektów** Aktor AcousticsSpace.
* **Nowy tworzenie:** Zasób danych akustycznych do załadowania. Pozostawienie tego pola pustego/ustawienie wartości null spowoduje zwolnienie bieżącego tworzenieu bez ładowania nowego.

### <a name="optionally-query-for-surface-proximity"></a>Opcjonalnie można wykonać zapytanie o bliskość powierzchni
Jeśli chcesz zobaczyć, jak zamknięte powierzchnie znajdują się w określonym kierunku wokół odbiornika, możesz użyć funkcji odległość zapytania. Ta funkcja może być przydatna w przypadku kierowania kierunku opóźnionych lub dla innych logiki gier, które są sterowane bliskością powierzchni. Zapytanie jest tańsze niż rzutowanie promieniowe, ponieważ wyniki są pobierane z tabeli odnośników.

![Zrzut ekranu przykładowego zapytania o odległość planu](media/distance-query.png)

* **Obiektów** Aktor AcousticsSpace
* **Kierunek wyglądu:** Kierunek zapytania w wyśrodkowany przez odbiornik
* **Miast** Jeśli zapytanie powiedzie się, odległość do najbliższej powierzchni
* **Wartość zwracana:** Wartość logiczna — prawda, jeśli kwerenda zakończyła się pomyślnie; w przeciwnym razie wartość false

## <a name="next-steps"></a>Następne kroki
* Zapoznaj się z pojęciami związanymi z [procesem projektowania](design-process.md)
* [Utwórz konto platformy Azure](create-azure-account.md) , aby tworzenie własną scenę


