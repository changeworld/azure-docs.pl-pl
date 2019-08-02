---
title: Często zadawane pytania dotyczące akustycznych projektów
titlesuffix: Azure Cognitive Services
description: Ta strona zawiera odpowiedzi na często zadawane pytania dotyczące akustycznych projektów, w tym instrukcji pobierania i procesu tworzenie.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: kegodin
ROBOTS: NOINDEX
ms.openlocfilehash: a965fc020c5c534616459ad661b71ac67dbc2425
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704812"
---
# <a name="project-acoustics-frequently-asked-questions"></a>Często zadawane pytania dotyczące akustycznych projektów

## <a name="what-is-project-acoustics"></a>Co to jest akustyka projektu?

Pakiet protokołów akustycznych w projekcie to system akustyczny, który oblicza zachowanie dźwięku Wave przed środowiskiem uruchomieniowym, zbliżone do statycznego oświetlenia. Chmura wykonuje intensywną transpozycję obliczeń fizyki Wave, więc koszt procesora CPU w czasie wykonywania jest niski.  

## <a name="where-can-i-download-the-plugin"></a>Gdzie można pobrać wtyczkę?

Możesz pobrać wtyczkę [aparatu Unity akustyczną projektu](https://www.microsoft.com/download/details.aspx?id=57346) lub [wtyczkę Unreali projektu](https://www.microsoft.com/download/details.aspx?id=58090).

## <a name="does-project-acoustics-support-ltxgt-platform"></a>Czy akustyczne projektu obsługuje &lt;platformę x&gt; ?

Obsługa platformy akustycznej w projekcie zmienia się w zależności od potrzeb klientów. Skontaktuj się z nami na [forum problemu akustycznego projektu](https://github.com/microsoft/ProjectAcoustics/issues) , aby dowiedzieć się więcej o obsłudze dodatkowych platform.

## <a name="is-azure-used-at-runtime"></a>Czy platforma Azure jest używana w czasie wykonywania?

Nie, integracja z chmurą jest używana tylko podczas etapu wstępnego obliczania w ramach konfiguracji sceny.
 
## <a name="what-is-simulation-input"></a>Co to jest dane wejściowe symulacji? 

Dane wejściowe symulacji to scena 3D, środowisko wirtualne lub poziom gry. Akustyczne projektu wykonują trójwymiarowe symulacje Wave, które są ściśle solidne, w tym płynne zamknięcia i rozpraszanie.
 
## <a name="what-is-the-runtime-cost"></a>Jaki jest koszt czasu wykonywania?

Wartości akustyczne zajmują 0,01% czasu procesora CPU na klatkę. Użycie pamięci RAM zależy od rozmiaru sceny i może należeć do zakresu od 10 do 100 MB.
 
## <a name="do-i-need-to-simplify-the-level-geometry-control-triangle-count-make-meshes-watertight"></a>Czy muszę uprościć geometrię poziomu? Liczba trójkątów formantów? Czy sprawić, aby siatki były wodoszczelne?

Nie. System pozyskuje dane geometryczne na poziomie szczegółowości bezpośrednio. Zostanie voxelized do wewnętrznego przetwarzania.
 
## <a name="whats-in-the-runtime-lookup-table"></a>Co znajduje się w tabeli odnośników środowiska uruchomieniowego?

Plik ACE zawiera tabelę parametrów akustycznych między wieloma parami lokalizacji źródła i odbiornika, a także geometrii sceny voxelized używane do interpolacji parametrów.
 
## <a name="can-project-acoustics-handle-moving-sources"></a>Czy w projekcie mogą być obsługiwane przenoszone źródła?

Tak, akustyczne projektu sprawdza tabelę odnośników i aktualizuje DSP audio na każdym z nich, aby obsługiwały przeniesienie źródeł i odbiornik.
 
## <a name="can-project-acoustics-handle-dynamic-geometry-closing-doors-walls-blown-away"></a>Czy w projekcie mogą być obsługiwane dynamiczne geometrie? Zamykasz drzwi? Ściany rozwiniętą z dala?

Nie. Parametry akustyczne są wstępnie obliczane w oparciu o stan statyczny poziomu gry. Sugerujemy pozostawienie geometrii drzwi z dźwiękiem, a następnie zastosowanie dodatkowych zamknięcia na podstawie stanu zniszczalnych i ruchomych obiektów gry przy użyciu technik ustanowionych.
 
## <a name="does-project-acoustics-use-acoustic-materials"></a>Czy akustyczne projektu wykorzystują Materiały akustyczne?

Tak. Materiały są pobierane z nazw materiału fizycznego na poziomie, co absorptivity.
 
## <a name="what-do-the-probes-represent"></a>Co reprezentuje "sondy"?

Sondy to próbkowanie możliwych lokalizacji odtwarzacza. Każda sonda reprezentuje oddzielną symulację fali sceny pochodzącej z lokalizacji sondowania. W czasie wykonywania parametry akustyczne dla lokalizacji odbiornika są interpolowane z lokalizacji sondy w pobliżu.
 
## <a name="why-spend-so-much-compute-in-the-cloud-what-does-it-buy-me"></a>Dlaczego warto poświęcać wiele obliczeń w chmurze? Co kupuje?

Funkcja akustyczna projektu zapewnia dokładne i niezawodne parametry akustyczne nawet dla bardzo złożonych środowisk wirtualnych, uwzględniając każdy aspekt architektury. Zapewnia ona płynną zamknięciaę i przeszkodę oraz dynamiczną odmianę Reverb bez ręcznej pracy w zakresie rysowania woluminów. Wszystkie, podczas gdy środowisko uruchomieniowe jest pozostałe.

## <a name="what-exactly-happens-during-baking"></a>Co dokładnie się dzieje podczas "pieczenie"?

Tworzenie składa się z symulowanych fal akustycznych regionów symulacji Cuboid wyśrodkowanych przez każdą sondę odbiornika.

## <a name="next-steps"></a>Kolejne kroki
* Wypróbuj [zawartość przykładową Unity](unity-quickstart.md) lub [Unreal przykładowej](unreal-quickstart.md) zawartości

