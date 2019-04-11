---
title: Samouczek Unreal projektowania Akustyka projektu
titlesuffix: Azure Cognitive Services
description: W tym samouczku opisano przepływ pracy projektowania dla projektu Akustyka Unreal i Wwise.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: kegodin
ms.openlocfilehash: 38276757d0472582c3cf5035e1f52d34158a7e38
ms.sourcegitcommit: 6e32f493eb32f93f71d425497752e84763070fad
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/10/2019
ms.locfileid: "59470019"
---
# <a name="project-acoustics-unrealwwise-design-tutorial"></a>Samouczek projektowania Unreal/Wwise Akustyka projektu
W tym samouczku opisano konfigurację projektu i przepływu pracy dla projektu Akustyka Unreal i Wwise.

Wstępnie wymagane oprogramowanie:
* Unreal projektu z wtyczek Wwise Akustyka projektu i Unreal

Aby uzyskać Unreal projektu z projektu Akustyka, możesz wykonywać następujące czynności:
* Postępuj zgodnie z [integracji Project Akustyka Unreal](unreal-integration.md) instrukcjami, aby dodać Akustyka projektu do projektu Unreal
* Możesz też korzystać z [Akustyka projektu przykładowego projektu](unreal-quickstart.md).

## <a name="setup-project-wide-wwise-properties"></a>Konfigurowanie właściwości Wwise całego projektu
Wwise ma globalne przeszkody i krzywych zamknięcia, które wpływają na jak wtyczki projektu Akustyka dyski procesor DSP Wwise audio.

### <a name="design-wwise-occlusion-curves"></a>Projektowanie Wwise zamknięcia krzywych
Gdy aktywny jest projekt Akustyka odpowie na woluminie zamknięcia filtr niskim (LPF) i krzywe wysokiej filtrem (HPF), możesz ustawić w Wwise. Firma Microsoft zaleca ustawienie liniowej z wartością dB-100 wartości zamknięcia 100 typu krzywej woluminu.

To ustawienie, jeśli Symulacja Akustyka projektu oblicza zamknięcia DB-18 go zostanie użyty jako wejście do poniżej krzywej X = 18 i odpowiednie Y wartość tłumienie stosowane. W celu zamknięcia połowa należy ustawić punkt końcowy-50 dB zamiast bazy danych-100 lub dB-200 exaggerate zamknięcia. Można dostosować i dostosowywać wszystkie krzywą, która działa najlepiej w grze.
 
![Zrzut ekranu Wwise zamknięcia krzywa edytora](media/wwise-occlusion-curve.png)

### <a name="disable-wwise-obstruction-curves"></a>Wyłącz Wwise przeszkoda krzywych
Krzywe przeszkoda Wwise wpływa na poziom susz w izolacji, lecz Akustyka projektu używa projektu, formantów i symulacji do wymuszania współczynniki suchego /. Zaleca się wyłączenie przeszkoda krzywej woluminu. Aby zaprojektować wetness, formant dostosować Wetness opisanym w dalszej części.
 
Jeśli używasz krzywych LPF/HPF przeszkoda do innych celów, upewnij się, że ustawiono je Y = 0 X = 0 (czyli jest brak LPF lub HPF po nie przeszkód).

![Zrzut ekranu Wwise przeszkoda krzywej edytora](media/wwise-obstruction-curve.png)

### <a name="design-project-acoustics-mixer-parameters"></a>Projektowanie projektu Akustyka mixer parametrów
Właściwości globalne pogłosu można kontrolować, przechodząc do karty wtyczka mixer magistrali Akustyka projektu. Kliknij dwukrotnie "Projektu Akustyka Mixer (niestandardowy)" Aby otworzyć panel ustawień wtyczki mixer.

Można również zobaczyć, że wtyczka mixer ma jedną z opcji "Wykonaj Spatialization". Jeśli zostanie wykorzystany raczej projektu akustyczny spatialization wbudowanych, zaznacz pole wyboru "Spatialization wykonania" i wybrać HRTF lub sekcja Panoramowanie. Upewnij się wyłączyć wszelkie magistrale susz Aux, które zostały skonfigurowane, w przeciwnym razie otrzymasz bezpośrednią ścieżkę dwa razy. Użyj "Dostosuj Wetness" i "Współczynnik skali czasu pogłosu" sprawowanie kontroli globalnej na różnych pogłosu. Należy pamiętać, należy ponownie uruchomić Unreal, a następnie ponownie wygenerować soundbanks przed osiągnięcia play, aby wczytać mixer wtyczki config zmiany, takie jak pola wyboru "Spatialization wykonania".

![Zrzut ekranu projektu Akustyka Wwise mixer wtyczki opcje](media/mixer-plugin-global-settings.png)

## <a name="set-project-acoustics-design-controls-in-the-wwise-actor-mixer-hierarchy"></a>Ustaw Akustyka projektu projektu kontrolki w hierarchii Wwise mixer aktora
Aby parametry sterujące poszczególnych mixer aktora kliknij dwukrotnie Mixer aktora, a następnie kliknij na karcie wtyczki Mixer. W tym miejscu będzie można zmienić żadnych parametrów na poziomie poszczególnych dźwięku. Te wartości, połącz się z tymi ustawiona po stronie unreal Engine (opisanych poniżej). Na przykład jeśli projektu Akustyka Unreal wtyczka ustawia Outdoorness dopasowania obiektu do 0,5 i ustawia Wwise jego -0.25, Wynikowe dopasowanie Outdoorness dotyczą, które dźwięku jest 0,25.

![Zrzut ekranu przedstawiający ustawienia mixer dźwięku w hierarchii mixer aktora Wwise dla](media/per-sound-mixer-settings.png)

### <a name="ensure-the-aux-bus-has-dry-send-and-output-bus-has-wet-send"></a>Upewnij się, Service bus aux ma susz wysyłania i magistrali danych wyjściowych ma mokre wysyłania
Należy pamiętać, że instalacji wymagane mixer aktora wymiany zwykle susz i mokrą routingu w Wwise. Generuje sygnału pogłosu na magistrali danych wyjściowych mixer aktora (zestaw projektu Akustyka magistrali) i susz sygnału wzdłuż magistrali aux zdefiniowanych przez użytkownika. Marszruty jest wymagana ze względu na funkcje wtyczki mixer Wwise interfejsu API, które wtyczka Wwise Akustyka projektu.

![Zrzut ekranu Wwise edytora przedstawiający wytyczne dotyczące projektowania głosu Akustyka projektu](media/voice-design-guidelines.png)
 
### <a name="set-up-distance-attenuation-curves"></a>Konfigurowanie odległość tłumienie krzywych
Upewnij się, wszystkie krzywej tłumienie posługują się mieszarki aktora, za pomocą projektu Akustyka mają zdefiniowane przez użytkownika aux, Wyślij ustawiona na "danych wyjściowych magistrali woluminu". Wwise robi to domyślnie dla nowo utworzonej tłumienia krzywych. Jeśli przenosisz istniejący projekt, należy sprawdzić ustawienia krzywej.

Domyślnie symulacji Akustyka projekt ma promień 45 liczniki wokół lokalizacji odtwarzacza. Ogólnie zaleca się ustawienie z krzywej tłumienie dB-200 wokół tej odległości. Odległość ta nie jest ograniczenie twardych. Dla niektórych brzmi, jak broni, możesz zechcieć większych usługi radius. W takich przypadkach zastrzeżenie: to, czy tylko geometrii w ciągu 45 m lokalizacji odtwarzacza będzie uczestniczył. Jeśli źródła dźwięku znajduje się poza pokój i natychmiast 100 mln odtwarzacz jest w pomieszczeniu, zostaną prawidłowo zamknięte. Jeśli źródłem jest w pomieszczeniu, a gracz jest poza i natychmiast 100 mln, nie będą prawidłowo zamknięte.

![Zrzut ekranu Wwise tłumienie krzywych](media/atten-curve.png)

### <a name="post-mixer-equalization"></a>Opublikuj wyrównywania Mixer ###
 Jeden element, który chcesz zrobić, to dodanie korektor mixer post. Można traktować magistrali Akustyka projektu jako magistrala typowe pogłosu (w domyślnym trybie pogłosu) i umieść filtru na nim celu wyrównywania. Możesz zobaczyć próbka w projekcie Akustyka Wwise przykładowym projekcie.

![Zrzut ekranu Wwise po mixer EQ](media/wwise-post-mixer-eq.png)

Na przykład filtr wysoki — dostęp próbny może pomóc obsługi niskich z nagrań NFC, które pogłosu boomy, określenie. Możesz również uzyskać większą kontrolę po tworzenie, dostosowując EQ za pośrednictwem RTPCs, dzięki czemu możesz zmienić kolor pogłosu podczas gry.

## <a name="set-up-scene-wide-project-acoustics-properties"></a>Ustawianie właściwości projektu Akustyka całej sceny

Aktor miejsca Akustyka udostępnia wiele formantów, które modyfikują zachowanie systemu i są przydatne podczas debugowania.

![Zrzut ekranu przedstawiający Unreal Akustyka formanty](media/acoustics-space-controls.png)

* **Dane Akustyka:** To pole musi być przypisany do zasobu w ramach Akustyka z katalogu zawartości/Akustyka. Wtyczka Akustyka projektu spowoduje automatyczne dodanie katalogu zawartości/Akustyka spakowanych katalogi projektu.
* **Rozmiar fragmentu:** Zakresy obszaru wokół odbiornik, który chcesz Akustyka dane załadowane do pamięci RAM. Tak długo, jak długo odbiornik natychmiast sondy wokół odtwarzacz są ładowane w, wyniki są takie same jak ładowanie dane akustyczne dla wszystkich sondy. Większe Kafelki Użyj więcej pamięci RAM, ale Zmniejsz We/Wy dysku
* **Stream automatycznie:** Po włączeniu automatycznie ładuje w nowych kafelków odbiornik osiągnie krawędzi obszaru załadowane. Po wyłączeniu musisz załadować nowych kafelków ręcznie za pomocą kodu lub plany
* **Skalowanie pamięci podręcznej:** steruje rozmiarem pamięci podręcznej, używany do przesyłania zapytań akustyczna. Mniejsze cache używa mniejszą ilość pamięci RAM, ale może zwiększyć użycie procesora CPU dla każdego zapytania.
* **Poziom hałasu podczas włączone** Debugowanie kontrolki umożliwiające szybkie A / B, przełączając symulacji Akustyka. Ten formant jest ignorowany w wysyłce konfiguracje. Kontrolka jest przydatny do znajdowania, jeśli konkretnego błędu audio pochodzi z obliczenia Akustyka lub innym problemem w w projekcie Wwise.
* **Zaktualizuj odległości:** Użyj tej opcji, jeśli chcesz użycie informacji o wstępnie ramach Akustyka odległość zapytań. Te zapytania są podobne do rzutowania ray, ale są one wstępnie obliczonych więc warto poświęcić dużo mniej procesora CPU. Przykład użycia jest odrębny odbić powierzchni najbliższy do odbiornika. W pełni wykorzystać to, należy za pomocą kodu lub plany odległości zapytania.
* **Rysowanie statystyki:** Podczas gdy w UE `stat Acoustics` może zapewnić Ci informacje o Procesorze, na tym ekranie stanu będzie widoczna aktualnie załadowanych plików ACE, użycie pamięci RAM oraz inne informacje o stanie w górnym lewym rogu ekranu.
* **Rysowanie Voxels:** Zamknij, aby nakładki voxels odbiornika przedstawiający siatki voxel używane podczas interpolacji czasu wykonywania. Nadajnik znajduje się wewnątrz voxel środowiska uruchomieniowego, zakończy się niepowodzeniem zapytań akustyczna.
* **Rysowanie sondy:** Pokaż sondy to sceny. Będą one różne kolory w zależności od stanu obciążenia.
* **Rysowanie odległości:** Jeśli odległości aktualizacji jest włączona, spowoduje to wyświetlenie polem powierzchni najbliższego połączenia z odbiornikiem w wykonywanie kwantyzowanych kierunkach odbiornika.

## <a name="actor-specific-acoustics-design-controls"></a>Formanty projektowania Akustyka specyficzne dla aktora
Te kontrolki projektu są ograniczone do poszczególnych składników audio w Unreal.

![Zrzut ekranu przedstawiający formantów Unreal składnika Audio](media/audio-component-controls.png)

* **Mnożnik zamknięcia.:** Określa, czy efekt zamknięcia. Wartości > 1 zwiększysz zamknięcia. Wartości < 1 zminimalizuje go.
* **Dostosowanie wetness:** Dodatkowe pogłosu bazy danych
* **Decay mnożnik czasu:** Formanty RT60 multiplicatively, oparte na danych wyjściowych symulacji Akustyka
* **Dostosowanie outdoorness:** Określa, jak na zewnątrz jest reverberation. Wartości bliższa 0 są bardziej pomieszczeniu, bliżej 1 są bardziej na zewnątrz. To dopasowanie jest dodatku, więc ustawieniem dla niego wartość -1 będzie wymuszać pomieszczeniu, ustawieniem dla niego + 1 będzie wymuszać na zewnątrz.
* **Przekazywanie bazy danych:** Renderowanie dodatkowe dźwięku za pomocą tablicy z tym głośności w połączeniu z tłumienie linii wzroku na podstawie odległości.
* **Współczynnik mokrą Warp odległość:** Dostosowuje właściwości reverberation na "source", tak jakby bliżej/dalsze natychmiast, bez wywierania wpływu na bezpośrednią ścieżkę.
* **Odtwarzać, w menu Start:** Przełącz, aby określić, czy dźwięk powinna być automatycznie odtwarzana w menu start sceny. Domyślnie włączone.
* **Pokaż akustyczny parametry:** Wyświetla informacje debugowania bezpośrednio na składnik w grze. (tylko w przypadku konfiguracji bez wysyłania)

## <a name="blueprint-functionality"></a>Funkcje planu
Aktora Akustyka miejsca jest dostępna za pośrednictwem planu, dostarczanie funkcji, takich jak ładowanie mapy lub modyfikowanie ustawień za pośrednictwem poziomu skryptów. Firma Microsoft oferuje dwa przykłady w tym miejscu.

### <a name="add-finer-grained-control-over-streaming-load"></a>Dodaj bardziej szczegółowej kontroli nad przesyłania strumieniowego obciążenia
Do zarządzania dane akustyczne streaming sobie zamiast przesyłania strumieniowego automatycznie na podstawie player położenia, można użyć funkcji planu życie obciążenia kafelka:

![Zrzut ekranu z planu przesyłania strumieniowego opcje Unreal](media/blueprint-streaming.png)

* **Element docelowy:** Aktor AcousticsSpace
* **Pozycja Center:** Załadowano Centrum regionu, który potrzebuje danych
* **Zwolnij sondy poza kafelka:** Jeśli zaznaczone, wszystkie sondy w nowym regionie nie zostanie zwolniona z pamięci RAM. Jeśli nie jest zaznaczone, nowy region jest ładowany do pamięci podczas opuszczania istniejących sond również załadowanych do pamięci
* **Blok w momencie zakończenia:** Sprawia, że Kafelek obciążenia Operacja synchroniczna

Rozmiar fragmentu musi już ustawiony, przed wywołaniem życie obciążenia kafelka. Na przykład można wykonywać, podobny do poniższego, aby załadować plik ACE, ustawić rozmiar kafelka i przesyłanie strumieniowe w regionie:

![Opcje zrzutu ekranu przesyłania strumieniowego instalacji w Unreal](media/streaming-setup.png)

### <a name="optionally-query-for-surface-proximity"></a>Opcjonalnie zapytanie dla powierzchni odległości między elementami
Jeśli chcesz zobaczyć, jak blisko powierzchnie znajdują się w określonym kierunku wokół odbiornik, możesz użyć funkcji zapytania odległości. Ta funkcja może być przydatne, zachęcanie kierunkowe odbić opóźnione lub inną logikę gier prowadzone przez powierzchni odległości między elementami. Zapytanie jest mniej kosztowne niż rzutowania ray, ponieważ wyniki są pobierane z tabeli odnośników Akustyka.

![Zrzut ekranu przedstawiający przykładowe zapytanie odległość planu](media/distance-query.png)

* **Element docelowy:** Aktor AcousticsSpace
* **Kierunek wygląd:** Kierunek do wykonywania zapytań, a ich tematyka na odbiornik
* **Odległość:** Jeśli zapytanie odległość do najbliższej powierzchni zakończy się powodzeniem.
* **Wartość zwracana:** Wartość logiczna — wartość true, jeśli kwerenda zakończyła się pomyślnie, w przeciwnym razie wartość false

## <a name="next-steps"></a>Kolejne kroki
* Poznaj pojęcia dotyczące [Projektowanie procesu](design-process.md)
* [Utwórz konto platformy Azure](create-azure-account.md) wprowadzić własne sceny


