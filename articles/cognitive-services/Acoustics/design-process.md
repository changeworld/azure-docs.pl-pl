---
title: Omówienie procesu projektowania dla Akustyka projektu
titlesuffix: Azure Cognitive Services
description: W tym dokumencie opisano sposób express zgodną z planem projektu we wszystkich fazach Akustyka projektu przepływu pracy.
services: cognitive-services
author: kegodin
manager: cgronlun
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 08/17/2018
ms.author: kegodin
ms.openlocfilehash: 7c0a48c22e6fe1d1904771915aef401ffa58d588
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55152541"
---
# <a name="design-process-overview"></a>Omówienie procesu projektowania
We wszystkich fazach Akustyka projektu przepływu pracy można wyrazić zgodną z planem projektu: wstępne tworzenie sceny instalacji, położenia źródła dźwięku i po Tworzenie projektu. Ten proces wymaga mniej znaczników skojarzony umieszczenie woluminy pogłosu przy zachowaniu projektanta kontrolę nad jak brzmi sceny.

## <a name="pre-bake-design"></a>Wstępne tworzenie projektu
Wstępne tworzenie sceny procesu instalacji tworzy sceny i metadanych, które są używane do symulowania dźwięku obejmuje, wybierając, które elementy sceny będą uczestniczyć w symulacji w celu zapewnienia occlusions, odbić i reverberation. Metadane dla sceny jest wybór akustyczny materiałów dla każdego elementu sceny. Akustyczny materiałów kontroli ilości energii dźwięku odzwierciedlone powrót po awarii z każdej powierzchni.

Współczynnik absorpcji domyślnego dla wszystkich powierzchni jest 0,04, jest wysoce odbicia. Aby uzyskać efekty estetycznych i gra, należy dostrajania współczynniki absorpcji różnych materiałów w scenie, które są szczególnie dobrze do odbiorników, gdy ich usłyszeć przejścia z jednego obszaru sceny do innego. Na przykład przejście z pokoju reverberant ciemny się jasne,-reverberant sceny na zewnątrz rozszerza wpływ przejścia. Aby uzyskać ten efekt, dostosowywanie współczynniki absorpcji na wyższe materiały sceny aktywność na świeżym powietrzu.

Czas reverberation danego materiału w pomieszczeniu odwrotnie jest powiązana z jego współczynnik, materiałami większość posiada absorpcji wartości z zakresu od 0,01 do 0,20. Materiały z współczynniki absorpcji poza tym zakresem są bardzo pochłaniający.

![Wykres czasu pogłosu](media/ReverbTimeGraph.png)

[Tworzenie interfejsu użytkownika, przeprowadzenie](bake-ui-walkthrough.md) opisano formanty wstępne tworzenie szczegółowo.

## <a name="sound-source-placement"></a>Umieszczanie źródła dźwięku
Wyświetlanie voxels oraz sondę w punktach w czasie wykonywania mogą pomóc w debugowanie problemów z źródeł dźwięku jest zablokowana wewnątrz geometrii voxelized. Aby przełączyć voxel siatki i badania punktów, kliknij odpowiednie pole wyboru w menu Gizmo w prawym górnym rogu widoku sceny. Jeśli źródła dźwięku znajduje się wewnątrz voxel tablicy, przenieś go do voxel powietrze.

![Elementy gizmo Menu](media/GizmosMenu.png)  

Wyświetlanie voxel może pomóc określić, jeśli składniki wizualne w grze mają transformacji zastosowanych do nich. Jeśli tak, należy zastosować taką samą transformację z hostingiem elementy GameObject **Menedżera Akustyka**.

### <a name="voxel-size-discrepancies"></a>Voxel rozmiar niezgodności
Można zauważyć, że rozmiar voxels używa się do ilustrowania, który sceny siatek uczestniczyć w tworzenie Akustyka różni się w widokach czasu i środowiska wykonawczego projektu. Ta różnica nie ma wpływu na jakość/stopień szczegółowości swoje częstotliwość symulacji wybrany, ale jest raczej biproduct użytkowania sceny voxelized środowiska uruchomieniowego. W czasie wykonywania "dostosowany" do obsługi interpolacji między lokalizacje źródłowe są voxels symulacji. Dzięki temu, projekt czasu pozycjonowanie źródeł dźwięku bliżej do siatek sceny niż pozwala rozmiar voxel symulacji — ponieważ źródeł wewnątrz voxel, które zawierają pod względem akustycznym traktowanej siatki nie należy wprowadzać żadnych dźwięku.

Poniżej przedstawiono dwa obrazy przedstawiający różnicę między voxels projektu (wstępne tworzenie) i voxels środowiska uruchomieniowego (po tworzenie), jak zwizualizować za wtyczki aparatu Unity:

Voxels czasu projektowania:

![VoxelsDesignTime](media/VoxelsDesignTime.png)

Voxels środowiska uruchomieniowego:

![VoxelsRuntime](media/VoxelsRuntime.png)

Decyzja w sprawie czy siatki voxel dokładnie reprezentuje siatek sceny architektury do konstrukcji należy za pomocą voxels trybu projektowania, nie wizualizacji środowiska uruchomieniowego voxels dostosowany.

## <a name="post-bake-design"></a>Po utworzeniu Tworzenie projektu
Tworzenie wyniki są przechowywane w pliku ACE jako parametry zamknięcia i reverberation dla wszystkich par lokalizacji odbiornika źródła w całym sceny. To fizycznie uzyskać dokładny wynik może służyć do Twojego projektu jako — jest i jest doskonałym punkt początkowy dla projektu. Proces projektowania po tworzenie określa reguły przekształcania parametry wynik tworzenie w czasie wykonywania.

### <a name="distance-based-attenuation"></a>Na podstawie odległości tłumienie
Audio DSP dostarczone przez **Acoustics Microsoft** wtyczki spatializer Unity szanuje tłumienie opartego na źródło wbudowana do aparatu Unity Editor. Formanty na podstawie odległości tłumienie znajdują się w **źródła Audio** składnik znaleziony w **Inspektor** panelu dźwięk źródeł, w obszarze **3D ustawienia dźwięku**:

![Tłumienie odległości](media/distanceattenuation.png)

Akustyka wykonuje obliczenia w polu "symulacji region" skupia się wokół lokalizacji odtwarzacza. Źródła dźwięku jest oddalone przed graczem, znajduje się poza tym regionem symulacji tylko geometrii w ramach tego pola będzie mieć wpływ na dźwięk propagacji (na przykład powoduje zamknięcia.), który jest dobrze działa, gdy occluders znajdują się w pobliżu odtwarzacza. Jednak w przypadkach, gdy działa on w wolnym miejscu, ale occluders zbliżenia odległe źródła dźwięku dźwięk może stać się nierealistycznie disoccluded. Nasze rozwiązania sugerowane jest zapewnienie w takich przypadkach dźwięku tłumienie spadnie na 0 w około 45m, odległość odtwarzacza krawędzią pola w poziomie domyślne.

### <a name="tuning-scene-parameters"></a>Dostrajanie parametrów sceny
Aby dostosować parametry dla wszystkich źródeł, kliknij na pasku kanału w mechanizmu Unity **Mixer Audio**i dostosowanie parametrów w **Mixer Akustyka** efekt.

![Dostosowywanie Mixer](media/MixerParameters.png)

### <a name="tuning-source-parameters"></a>Dostrajanie parametrów źródła
Dołączanie **AcousticsAdjust** skrypt do źródła umożliwia dostrajanie parametrów dla tego źródła. Aby dołączyć skrypt, kliknij przycisk **Dodaj składnik** w dolnej części **Inspektor** panelu, a następnie przejdź do **Skrypty > Dostosuj Akustyka**. Skrypt zawiera sześć kontrolki:

![AcousticsAdjust](media/AcousticsAdjust.png)

* **Włącz Akustyka** — Określa, czy Akustyka jest stosowany do tego źródła. Po usunięciu zaznaczenia źródła będzie spatialized HRTFs, jednak bez Akustyka, co oznacza bez przeszkód, zamknięcia i parametry dynamiczne reverberation, takie jak poziom i zanikania czasu. Reverberation nadal jest stosowany przy stałym poziomie i czas zanikania.
* **Zamknięcia** -dotyczą mnożnik zamknięcia poziom bazy danych, obliczone przez system Akustyka. Jeśli ta mnożnik jest większa niż 1, zamknięcia będzie exaggerated, podczas wartości mniejsza niż 1 Upewnij wpływ zamknięcia bardziej subtelne, a wartość 0 powoduje wyłączenie zamknięcia.
* **Przekazywanie (baza danych)** — Ustaw tłumienie (w bazie danych), spowodowane przez geometrii. Ustaw suwak do jego najniższym poziomie, aby wyłączyć przekazywanie. Akustyka spatializes początkowej audio susz jako odebranych wokół geometrii sceny (portaling). Przekazywanie udostępnia dodatkowe przybycia susz, który jest spatialized w kierunku linii wzroku. Należy pamiętać, że tłumienie odległość krzywą dla źródła są również stosowane.
* **Wetness (baza danych)** — dopasowuje pogłosu zasilania, w bazie danych, zgodnie z odległości ze źródła. Wartości dodatnich wprowadzać dźwięk bardziej reverberant podczas wartości ujemnych wprowadzić bardziej susz dźwięku. Kliknij na formancie krzywej (wiersz zielony), aby wyświetlić Edytor krzywej. Zmodyfikuj krzywej kliknięcie lewym przyciskiem myszy, aby dodać punkty, a następnie przeciągając tych punktów w celu utworzenia funkcji, że chcesz użyć. Oś x jest odległość od źródłowego i osi y jest dostosowanie pogłosu w bazie danych. Zobacz ten [ręczne Unity](https://docs.unity3d.com/Manual/EditingCurves.html) Aby uzyskać więcej informacji na temat edytowania krzywych. Aby przywrócić wartość domyślną krzywej, kliknij prawym przyciskiem myszy **Wetness** i wybierz **resetowania**.
* **Decay Skala czasu** — dopasowuje mnożnik dla czas zanikania. Na przykład jeśli wynik tworzenie określa czas zanikania 750 milisekund, ale ta wartość jest równa 1,5, czas zanikania stosowane do źródła jest 1,125 milisekund.
* **Outdoorness** -addytywne korektę na system Akustyka szacowania jak "na zewnątrz" reverberation w źródle powinny dźwiękowych. To ustawienie na 1 spowoduje, że źródło zawsze dźwięku całkowicie na zewnątrz, podczas ustawienie go na wartość -1 spowoduje, że źródła dźwięku pomieszczeniu.

Różne źródła może wymagać różne ustawienia uzyskać pewne efekty estetycznych lub rozgrywkę. Okno dialogowe jest jednym z przykładów możliwe. Wyczyść ludzi jest bardziej attuned do reverberation w mowy, gdy okno dialogowe często konieczne jest zrozumiały dla rozgrywkę. Konta na w tym, bez konieczności szukania okno dialogowe bez diegetic, przenosząc **Wetness** w dół, dostosowując **Percepcyjna Warp odległość** parametru opisane poniżej, dodanie niektórych  **Przekazywanie** dla niektórych susz boost audio propagowanie przez ściany i/lub zmniejszając **zamknięcia** od 1 do ma więcej dźwięku odbierane za pośrednictwem portali.

Dołączanie **AcousticsAdjustExperimental** skrypt do źródła umożliwia eksperymentalne dostrajania parametrów dla tego źródła. Aby dołączyć skrypt, kliknij przycisk **Dodaj składnik** w dolnej części **Inspektor** panelu, a następnie przejdź do **Skrypty > Akustyka dostosować eksperymentalne**. Obecnie jest jedną eksperymentalna kontrolka:

![AcousticsAdjustExperimental](media/AcousticsAdjustExperimental.png)

* **Percepcyjna Warp odległość** — Zastosuj wykładniczy Wypaczanie odległość, używany do obliczania współczynnik mokro próbnego. System Akustyka oblicza mokrą poziomy w całej przestrzeni, które różnią się płynnie z odległości i podaj odległość Percepcyjna podpowiedzi. Wypaczanie wartości większe niż 1 exaggerate ten efekt przez coraz większe poziomy reverberation powiązane odległość, wprowadzania dźwięku "odległe", podczas gdy mniej niż 1 Upewnij Wypaczanie wartości bardziej subtelne, dzięki czemu dźwięk więcej zmian na podstawie odległości reverberation "przedstawia".

