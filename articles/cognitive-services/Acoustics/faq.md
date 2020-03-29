---
title: Projekt Akustyka Często zadawane pytania
titlesuffix: Azure Cognitive Services
description: Ta strona zawiera odpowiedzi na często zadawane pytania dotyczące akustyki projektu, w tym instrukcje pobierania i proces pieczenia.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: fa4b6499260219b0eb8ea4df4b4ccfd5263b57bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "75770207"
---
# <a name="project-acoustics-frequently-asked-questions"></a>Projekt Akustyka Często zadawane pytania

## <a name="what-is-project-acoustics"></a>Co to jest akustyka projektu?

Pakiet wtyczek Project Acoustics to system akustyki, który oblicza zachowanie fal dźwiękowych przed czasem wykonywania, podobny do oświetlenia statycznego. Chmura wykonuje ciężkie podnoszenie obliczeń fizyki fal, więc koszt procesora w czasie wykonywania jest niski.  

## <a name="where-can-i-download-the-plugin"></a>Gdzie mogę pobrać wtyczkę?

Wtyczkę [Project Acoustics Unity](https://www.microsoft.com/download/details.aspx?id=57346) lub [wtyczkę Project Acoustics Unreal](https://www.microsoft.com/download/details.aspx?id=58090)można pobrać.

## <a name="does-project-acoustics-support-ltxgt-platform"></a>Czy Projekt Akustyka obsługuje &lt;platformę x?&gt;

Wsparcie platformy Project Acoustics ewoluuje w zależności od potrzeb klientów. Skontaktuj się z nami na [forum project akustyki,](https://github.com/microsoft/ProjectAcoustics/issues) aby uzyskać informacje na temat wsparcia dla dodatkowych platform.

## <a name="is-azure-used-at-runtime"></a>Czy platforma Azure jest używana w czasie wykonywania?

Nie, integracja z chmurą jest używana tylko podczas etapu wstępnego przetwarzania w ramach konfiguracji sceny.
 
## <a name="what-is-simulation-input"></a>Co to jest wejście symulacji? 

Wejście symulacji to scena 3D, środowisko wirtualne lub poziom gry. Project Acoustics wykonuje symulacje fal wolumetrycznych 3D, które dokładnie modelują fizykę dźwięku, w tym płynne okluzje i rozpraszanie.
 
## <a name="what-is-the-runtime-cost"></a>Jaki jest koszt środowiska wykonawczego?

Akustyka zajmuje około 0,01% procesora na źródło na klatkę. Użycie pamięci RAM zależy od rozmiaru sceny i może wynosić od 10 do 100 MB.
 
## <a name="do-i-need-to-simplify-the-level-geometry-control-triangle-count-make-meshes-watertight"></a>Czy muszę uprościć geometrię poziomu? Liczba trójkątów sterujących? Czy siatki wodoszczelne?

Nie. System pochłonie szczegółową geometrię poziomu bezpośrednio. Będzie voxelized do przetwarzania wewnętrznego.
 
## <a name="whats-in-the-runtime-lookup-table"></a>Co jest w tabeli odnośności środowiska uruchomieniowego?

Plik ACE zawiera tabelę parametrów akustycznych między licznymi parami lokalizacji źródłowej i detektora, a także woxelowaną geometrią sceny używaną do interpolacji parametrów.
 
## <a name="can-project-acoustics-handle-moving-sources"></a>Czy projekt Akustyka może obsługiwać przenoszenie źródeł?

Tak, program Project Acoustics konsultuje tabelę odnośnych i aktualizuje DSP audio dla każdego znacznika, dzięki czemu może obsługiwać ruchome źródła i odbiornik.
 
## <a name="can-project-acoustics-handle-dynamic-geometry-closing-doors-walls-blown-away"></a>Czy projekt Akustyka może obsługiwać geometrię dynamiczną? Zamykanie drzwi? Ściany zdmuchnięte?

Nie. Parametry akustyczne są wstępnie komputowane na podstawie stanu statycznego poziomu gry. Sugerujemy pozostawienie geometrii drzwi z akustyki, a następnie zastosowanie dodatkowego okluzji w oparciu o stan zniszczalnych i ruchomych obiektów gry przy użyciu ustalonych technik.
 
## <a name="does-project-acoustics-use-acoustic-materials"></a>Czy Projekt Akustyka wykorzystuje materiały akustyczne?

Tak. Materiały są zbierane z fizycznych nazw materiałów na twoim poziomie, co prowadzi do absorpcji.
 
## <a name="what-do-the-probes-represent"></a>Co reprezentują "sondy"?

Sondy są próbką możliwych lokalizacji graczy. Każda sonda reprezentuje oddzielną symulację fali sceny pochodzącej z lokalizacji sondy. W czasie wykonywania parametry akustyczne dla lokalizacji odbiornika są interpolowane z pobliskich lokalizacji sondy.
 
## <a name="why-spend-so-much-compute-in-the-cloud-what-does-it-buy-me"></a>Po co wydawać tyle obliczeń w chmurze? Co mnie kupuje?

Projekt Akustyka zapewnia dokładne i niezawodne parametry akustyczne nawet w ultra-złożonych środowiskach wirtualnych, z uwzględnieniem każdego aspektu architektonicznego. Zapewnia płynną okluzję i niedrożność oraz dynamiczną zmienność pogłosu bez ręcznej pracy tomów rysunku. Wszystko przy zachowaniu światła na procesorze podczas wykonywania.

## <a name="what-exactly-happens-during-baking"></a>Co dokładnie dzieje się podczas "pieczenia"?

Piec składa się z symulacji fal akustycznych regionów symulacji prostopadłościanu wyśrodkowanych na każdej sondzie słuchacza.

## <a name="is-my-source-content-secure"></a>Czy moja zawartość źródłna jest bezpieczna?

Akustyka projektu nie przekazuje geometrii sceny źródłowej do chmury. Zamiast tego symulacja działa na voxelization sceny, która jest połączona z danymi lokalizacji sondy i przechowywane w formacie zastrzeżonym.     

## <a name="next-steps"></a>Następne kroki
* Wypróbuj [przykładową zawartość unity projektu akustyka](unity-quickstart.md) lub [nierealisyjną zawartość próbki](unreal-quickstart.md)

