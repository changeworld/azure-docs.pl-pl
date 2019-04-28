---
title: Samouczek projektu środowiska Unity Akustyka projektu
titlesuffix: Azure Cognitive Services
description: W tym samouczku opisano przepływ pracy projektowania dla Akustyka projektu na platformie Unity.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: kegodin
ms.openlocfilehash: 01783aa12f586f61583b1503c796f9b523770104
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61433107"
---
# <a name="project-acoustics-unity-design-tutorial"></a>Samouczek projektu środowiska Unity Akustyka projektu
W tym samouczku opisano narzędzia do projektowania i przepływ pracy dla projektu Akustyka na platformie Unity.

Wymagania wstępne:
* Unity 2018.2 + dla Windows
* Sceny z zasobem Akustyka w ramach aparatu Unity

Na potrzeby tego samouczka możesz uzyskać sceny Unity z zasobem ramach Akustyka na dwa sposoby:
* [Dodaj Akustyka projektu do projektu środowiska Unity](unity-integration.md), następnie [pobrać konta usługi Azure Batch](create-azure-account.md), następnie [tworzenie sceny aparatu Unity](unity-baking.md)
* Możesz też korzystać z [Unity Akustyka projektu przykładowej zawartości](unity-quickstart.md).

## <a name="review-design-process-concepts"></a>Przegląd pojęć dotyczących projektowania procesów
Akustyka projekt używa typowe metody przetwarzania sygnałów (DSP) cyfrowego audio do przetwarzania, Twoich źródeł i umożliwia kontrolę nad Akustyka znane właściwości, w tym zamknięcia suchego/mieszanego i długość tail reverberation (RT60). Jednak podstawowe [koncepcji procesu projektowania Akustyka projektu](design-process.md) jest, że zamiast bezpośrednio ustawienie tych właściwości, możesz kontrolować, jak wyniki symulacji są używane do kierowania tych właściwości. Ustawienia domyślne dla każdego formantu reprezentują Akustyka fizycznie dokładne.

## <a name="design-acoustics-for-each-source"></a>Akustyka projektu dla każdego źródła
Projekt Akustyka oferuje pewną liczbę Akustyka specyficznymi dla źródła projektu kontrolki. Dzięki temu można kontrolować różne w scenie, podkreślając pewne źródła i cofnięcia emphasizing, inne osoby.

### <a name="adjust-distance-based-attenuation"></a>Dostosuj tłumienie na podstawie odległości
Audio DSP dostarczone przez **Akustyka projektu** wtyczki spatializer Unity szanuje tłumienie opartego na źródło wbudowana do aparatu Unity Editor. Formanty na podstawie odległości tłumienie znajdują się w **źródła Audio** składnik znaleziony w **Inspektor** panelu dźwięk źródeł, w obszarze **3D ustawienia dźwięku**:

![Zrzut ekranu Unity odległość tłumienie opcji — panel](media/distance-attenuation.png)

Akustyka wykonuje obliczenia w polu "symulacji region" skupia się wokół lokalizacji odtwarzacza. Źródła dźwięku jest oddalone przed graczem, znajduje się poza tym regionem symulacji tylko geometrii w ramach tego pola będzie mieć wpływ na dźwięk propagacji (na przykład powoduje zamknięcia.), który jest dobrze działa, gdy occluders znajdują się w pobliżu odtwarzacza. Jednak w przypadkach, gdy działa on w wolnym miejscu, ale occluders zbliżenia odległe źródła dźwięku dźwięk może stać się nierealistycznie disoccluded. Nasze rozwiązania sugerowane jest zapewnienie w takich przypadkach dźwięku tłumienie spadnie na 0 w około 45 m, odległość odtwarzacza krawędzią pola w poziomie domyślne.

![Zrzut ekranu SpeakerMode Unity panel opcji](media/speaker-mode.png)

### <a name="adjust-occlusion-and-transmission"></a>Dostosuj zamknięcia i transmisji
Dołączanie **AcousticsAdjust** skrypt do źródła umożliwia dostrajanie parametrów dla tego źródła. Aby dołączyć skrypt, kliknij przycisk **Dodaj składnik** w dolnej części **Inspektor** panelu, a następnie przejdź do **Skrypty > Dostosuj Akustyka**. Skrypt zawiera sześć kontrolki:

![Zrzut ekranu AcousticsAdjust Unity skryptu](media/acoustics-adjust.png)

* **Włącz Akustyka** — Określa, czy Akustyka jest stosowany do tego źródła. Po usunięciu zaznaczenia źródła będzie spatialized w aplecie HRTFs przesuwanie, ale nie będzie żadnych Akustyka. Oznacza to nie przeszkoda, zamknięcia lub parametry dynamiczne reverberation, takie jak poziom i zanikania czasu. Reverberation nadal jest stosowany przy stałym poziomie i czas zanikania.
* **Zamknięcia** -dotyczą mnożnik zamknięcia poziom bazy danych, obliczone przez system Akustyka. Jeśli ta mnożnik jest większa niż 1, zamknięcia będzie exaggerated, podczas wartości mniejsza niż 1 Upewnij wpływ zamknięcia bardziej subtelne, a wartość 0 powoduje wyłączenie zamknięcia.
* **Przekazywanie (baza danych)** — Ustaw tłumienie (w bazie danych), spowodowane przez geometrii. Ustaw suwak do jego najniższym poziomie, aby wyłączyć przekazywanie. Akustyka spatializes początkowej audio susz jako odebranych wokół geometrii sceny (portaling). Przekazywanie udostępnia dodatkowe przybycia susz, który jest spatialized w kierunku linii wzroku. Należy pamiętać, że tłumienie odległość krzywą dla źródła są również stosowane.

### <a name="adjust-reverberation"></a>Dostosuj reverberation
* **Wetness (baza danych)** — dopasowuje pogłosu zasilania, w bazie danych, zgodnie z odległości ze źródła. Wartości dodatnich wprowadzać dźwięk bardziej reverberant podczas wartości ujemnych wprowadzić bardziej susz dźwięku. Kliknij na formancie krzywej (wiersz zielony), aby wyświetlić Edytor krzywej. Zmodyfikuj krzywej kliknięcie lewym przyciskiem myszy, aby dodać punkty, a następnie przeciągając tych punktów w celu utworzenia funkcji, że chcesz użyć. Oś x jest odległość od źródłowego i osi y jest dostosowanie pogłosu w bazie danych. Aby uzyskać więcej informacji na temat edytowania krzywych zobacz [ręczne Unity](https://docs.unity3d.com/Manual/EditingCurves.html). Aby przywrócić wartość domyślną krzywej, kliknij prawym przyciskiem myszy **Wetness** i wybierz **resetowania**.
* **Decay Skala czasu** — dopasowuje mnożnik dla czas zanikania. Na przykład jeśli wynik tworzenie określa czas zanikania 750 milisekund, ale ta wartość jest równa 1,5, czas zanikania stosowane do źródła jest 1,125 milisekund.
* **Outdoorness** -addytywne korektę na system Akustyka szacowania jak "na zewnątrz" reverberation w źródle powinny dźwiękowych. Ustawienie wartości 1 spowoduje, że źródło zawsze dźwięku całkowicie na zewnątrz, podczas gdy ustawieniem dla niego wartość -1 spowoduje, że źródła dźwięku całkowicie pomieszczeniu.

Dołączanie **AcousticsAdjustExperimental** skrypt do źródła umożliwia eksperymentalne dostrajania parametrów dla tego źródła. Aby dołączyć skrypt, kliknij przycisk **Dodaj składnik** w dolnej części **Inspektor** panelu, a następnie przejdź do **Skrypty > Akustyka dostosować eksperymentalne**. Obecnie jest jedną eksperymentalna kontrolka:

![Zrzut ekranu AcousticsAdjustExperimental Unity skryptu](media/acoustics-adjust-experimental.png)

* **Percepcyjna Warp odległość** — Zastosuj wykładniczy Wypaczanie odległość, używany do obliczania współczynnik mokro próbnego. System Akustyka oblicza mokrą poziomy w całej przestrzeni, które różnią się płynnie z odległości i podaj odległość Percepcyjna podpowiedzi. Zniekształcania wartości większej niż 1 exaggerate ten efekt, zwiększając poziomy reverberation powiązane odległość, co dźwięk "odległe". Wypaczanie wartości mniejszej niż 1 Upewnij reverberation na podstawie odległości zmienić bardziej subtelne, a dźwięk więcej "bieżącej".

## <a name="design-acoustics-for-all-sources"></a>Akustyka projektu dla wszystkich źródeł
Aby dostosować parametry dla wszystkich źródeł, kliknij na pasku kanału w mechanizmu Unity **Mixer Audio**i dostosowanie parametrów w **Mixer Akustyka projektu** efekt.

![Zrzut ekranu z projektu Akustyka Unity Mixer Dostosowywanie panelu](media/mixer-parameters.png)

* **Dostosuj wetness** -dopasowuje pogłosu zasilania, w bazie danych, we wszystkich źródłach w scenie, oparte na odległość odbiornika źródła. Wartości dodatnich wprowadzać dźwięk bardziej reverberant podczas wartości ujemnych wprowadzić bardziej susz dźwięku.
* **Skala RT60** — mnożenia skalarne pogłosu czas.
* **Użyj sekcja Panoramowanie** — formanty czy audio jest wyświetlany jako binaural (0) lub multichannel przesuwanie (1). Dowolna wartość oprócz 1 wskazuje binaural. Binaural danych wyjściowych jest spatialized z HRTFs do użytku z programem słuchawki i wielokanałowego wyjściowego jest spatialized z VBAP do użycia w systemach osoby mówiącej wielokanałowy przestrzennego. Jeśli przy użyciu wielokanałowy panoramowania, upewnij się wybrać tryb głośnika, który pasuje do ustawienia urządzenia, znajdują się w **ustawienia projektu** > **Audio**.

## <a name="check-proper-sound-source-placement"></a>Sprawdzanie prawidłowego źródła dźwięku umieszczania
Źródeł dźwięku umieszczone wewnątrz zajętych voxels nie otrzyma traktowania akustyczny. Ponieważ voxels wykraczać poza geometrii sceny widoczny, istnieje możliwość umieścić źródła wewnątrz voxel, gdy pojawi się unoccluded przez geometrii visual. Możesz wyświetlić voxels Akustyka projektu, przełączając pola wyboru siatki voxel w **Gizmo** menu w prawym górnym rogu **sceny** widoku.

![Zrzut ekranu Unity Gizmo menu](media/gizmos-menu.png)  

Wyświetlanie voxel może również pomóc sprawdzić, czy składniki wizualne w grze się transformacji zastosowanych do nich. Jeśli tak, należy zastosować taką samą transformację z hostingiem elementy GameObject **Menedżera Akustyka**.

### <a name="bake-time-vs-run-time-voxels"></a>Tworzenie czasu a voxels w czasie wykonywania
Istnieje możliwość wyświetlenia voxels w oknie edytora w czasie projektowania gier i okna gry w środowisku uruchomieniowym. Rozmiar voxels różni się w tych widokach. Jest to spowodowane Akustyka interpolacji środowiska uruchomieniowego używa bardziej precyzyjną siatki voxel gładsze interpolacji wyników. Należy zweryfikować położenia źródła dźwięku za pomocą voxels środowiska uruchomieniowego.

Voxels czasu projektowania:

![Zrzut ekranu projektu Akustyka voxels w czasie projektowania](media/voxels-design-time.png)

Voxels środowiska uruchomieniowego:

![Zrzut ekranu projektu Akustyka voxels w czasie wykonywania](media/voxels-runtime.png)

## <a name="next-steps"></a>Kolejne kroki
* Poznaj analizy przypadków wyróżnianie pojęć dotyczących [Projektowanie procesu](design-process.md)

