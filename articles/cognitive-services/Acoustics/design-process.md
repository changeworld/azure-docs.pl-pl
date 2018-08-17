---
title: Omówienie procesu projektowania dla Akustyka — Cognitive Services
description: W tym dokumencie opisano sposób express zgodną z planem projektu we wszystkich fazach Akustyka projektu przepływu pracy.
services: cognitive-services
author: kegodin
manager: noelc
ms.service: cognitive-services
ms.component: acoustics
ms.topic: article
ms.date: 08/17/2018
ms.author: kegodin
ms.openlocfilehash: 3b35f78d66a6ae66b0a56818f5d4be455ce00de5
ms.sourcegitcommit: 1aedb52f221fb2a6e7ad0b0930b4c74db354a569
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/17/2018
ms.locfileid: "40181692"
---
# <a name="design-process-overview"></a>Omówienie procesu projektowania
We wszystkich fazach Akustyka projektu przepływu pracy można wyrazić zgodną z planem projektu: wstępne tworzenie projektu, źródła dźwięku umieszczania i po Tworzenie projektu. Ten proces wymaga mniej znaczników skojarzony umieszczenie woluminy pogłosu przy zachowaniu projektanta kontrolę nad jak brzmi sceny.

## <a name="pre-bake-design"></a>Wstępne tworzenie projektu
Proces projektowania wstępne tworzenie tworzy sceny i metadanych, które są używane do symulowania dźwięku obejmuje, wybierając, które elementy sceny będą uczestniczyć w symulacji w celu zapewnienia occlusions, odbić i reverberation. Metadane dla sceny jest wybór akustyczny materiałów dla każdego elementu sceny. Akustyczny materiałów kontroli ilości energii dźwięku odzwierciedlone powrót po awarii z każdej powierzchni.

Współczynnik absorpcji domyślnego dla wszystkich powierzchni jest 0,04, jest wysoce odbicia. Aby uzyskać efekty estetycznych i gra, należy dostrajania współczynniki absorpcji różnych materiałów w scenie, które są szczególnie dobrze do odbiorników, gdy ich usłyszeć przejścia z jednego obszaru sceny do innego. Na przykład przejście z pokoju reverberant ciemny się jasne,-reverberant sceny na zewnątrz rozszerza wpływ przejścia. Aby uzyskać ten efekt, dostosowywanie współczynniki absorpcji na wyższe materiały sceny aktywność na świeżym powietrzu.

Czas reverberation danego materiału w pomieszczeniu odwrotnie jest powiązana z jego współczynnik, materiałami większość posiada absorpcji wartości z zakresu od 0,01 do 0,20. Materiały z współczynniki absorpcji poza tym zakresem są bardzo pochłaniający.

![Wykres czasu pogłosu](media/ReverbTimeGraph.png)

[Tworzenie interfejsu użytkownika, przeprowadzenie](bake-ui-walkthrough.md) opisano formanty wstępne tworzenie szczegółowo.

## <a name="sound-source-placement"></a>Umieszczanie źródła dźwięku
Wyświetlanie voxels oraz sondę w punktach w czasie wykonywania mogą pomóc w debugowanie problemów z źródeł dźwięku jest zablokowana wewnątrz geometrii voxelized. Aby przełączyć voxel siatki i badania punktów, kliknij odpowiednie pole wyboru w menu Gizmo w prawym górnym rogu widoku sceny. Jeśli źródła dźwięku znajduje się wewnątrz voxel tablicy, przenieś go do voxel powietrze.

![Elementy gizmo Menu](media/GizmosMenu.png)  

Wyświetlanie voxel może pomóc określić, jeśli składniki wizualne w grze mają transformacji zastosowanych do nich. Jeśli tak, należy zastosować taką samą transformację z hostingiem elementy GameObject **Menedżera Akustyka**.

## <a name="post-bake-design"></a>Po utworzeniu Tworzenie projektu
Tworzenie wyniki są przechowywane w pliku ACE jako parametry zamknięcia i reverberation dla wszystkich par lokalizacji odbiornika źródła w całym sceny. To fizycznie uzyskać dokładny wynik może służyć do Twojego projektu jako — jest i jest doskonałym punkt początkowy dla projektu. Proces projektowania po tworzenie określa reguły przekształcania parametry wynik tworzenie w czasie wykonywania.

### <a name="distance-based-attenuation"></a>Na podstawie odległości tłumienie
Audio DSP dostarczone przez **Acoustics Microsoft** wtyczki spatializer Unity szanuje tłumienie opartego na źródło wbudowana do aparatu Unity Editor. Formanty na podstawie odległości tłumienie znajdują się w **źródła Audio** składnik znaleziony w **Inspektor** panelu dźwięk źródeł, w obszarze **3D ustawienia dźwięku**:

![Tłumienie odległości](media/distanceattenuation.png)

### <a name="tuning-scene-parameters"></a>Dostrajanie parametrów sceny
Aby dostosować parametry dla wszystkich źródeł, kliknij na pasku kanału w mechanizmu Unity **Mixer Audio**i dostosowanie parametrów w **Mixer Akustyka** efekt.

![Dostosowywanie Mixer](media/MixerParameters.png)

### <a name="tuning-source-parameters"></a>Dostrajanie parametrów źródła
Dołączanie **AcousticsSourceCustomization** skrypt do źródła umożliwia dostrajanie parametrów dla tego źródła. Aby dołączyć skrypt, kliknij przycisk **Dodaj składnik** w dolnej części **Inspektor** panelu, a następnie przejdź do **Skrypty > Dostosowywanie źródła Akustyka**. Skrypt ma trzy parametry:

![Dostosowywanie źródła](media/SourceCustomization.png)

* **Pogłosu dostosować Power** — dopasowuje pogłosu zasilania, w bazie danych. Wartości dodatnich wprowadzać dźwięk bardziej reverberant podczas wartości ujemnych wprowadzić bardziej susz dźwięku.
* **Decay Skala czasu** — dopasowuje mnożnik dla czas zanikania. Na przykład jeśli wynik tworzenie określa czas zanikania 750 milisekund, ale ta wartość jest równa 1,5, czas zanikania stosowane do źródła jest 1,125 milisekund.
* **Włącz Akustyka** — Określa, czy Akustyka jest stosowany do tego źródła. Po usunięciu zaznaczenia źródła będzie spatialized HRTFs, jednak bez Akustyka, co oznacza bez przeszkód, zamknięcia i parametry dynamiczne reverberation, takie jak poziom i zanikania czasu. Reverberation nadal jest stosowany przy stałym poziomie i czas zanikania.

Różne źródła może wymagać różne ustawienia uzyskać pewne efekty estetycznych lub rozgrywkę. Okno dialogowe jest jednym z przykładów możliwe. Wyczyść ludzi jest bardziej attuned do reverberation w mowy, gdy okno dialogowe często konieczne jest zrozumiały dla rozgrywkę. W tym może uwzględnić bez wprowadzania okno dialogowe bez diegetic, dostosowując możliwości pogłosu w dół.
