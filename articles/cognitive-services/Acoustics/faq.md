---
title: Projekt Akustyka — często zadawane pytania
titlesuffix: Azure Cognitive Services
description: Ta strona zawiera odpowiedzi na pytania często zadawane Akustyka projektu, w tym instrukcje pobierania i tworzenie procesu.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: resources
ms.date: 03/20/2019
ms.author: kegodin
ms.openlocfilehash: 3426681aad19bbe01c0f7e88ca16e79c0b490c36
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61335334"
---
# <a name="project-acoustics-frequently-asked-questions"></a>Projekt Akustyka — często zadawane pytania

## <a name="what-is-project-acoustics"></a>Co to jest akustyka projektu?

Pakiet Akustyka projektu wtyczek to system Akustyka obliczający zachowanie dźwięku przed środowiska uruchomieniowego, podobnie statyczne oświetlenia. Chmura ma skomplikowanymi fali fizyki obliczeń, więc koszt czasu wykonywania procesora CPU jest niska.  

## <a name="where-can-i-download-the-plugin"></a>Gdzie można pobrać wtyczkę?

Możesz pobrać [wtyczki projektu Akustyka Unity](https://www.microsoft.com/download/details.aspx?id=57346) lub [wtyczki projektu Akustyka Unreal](https://www.microsoft.com/download/details.aspx?id=58090).

## <a name="does-project-acoustics-support-x-platform"></a>Obsługuje Akustyka projektu <x> platformy?

Obsługa platform Akustyka projektu rozwoju zależnie od potrzeb klienta. Skontaktuj się z nami na [forów Akustyka projektu](https://social.msdn.microsoft.com/Forums/en-US/home?forum=projectacoustics) na uzyskiwanie informacji o obsłudze dotyczącymi dodatkowych platform.

## <a name="is-azure-used-at-runtime"></a>Azure jest używana w środowisku uruchomieniowym?

Nie, integracja z chmurą jest używane tylko na etapie precompute jako część instalacji sceny.
 
## <a name="what-is-simulation-input"></a>Co to jest symulacji dane wejściowe? 

Dane wejściowe symulacji są sceny 3D, poziomie grę lub środowisko wirtualne. Projekt Akustyka wykonuje 3D wave pomiarowej symulacje, które modelują fizyki dźwięku ściśle, łącznie z płynnym zamknięcia i rozproszenie.
 
## <a name="what-is-the-runtime-cost"></a>Jaki jest koszt środowiska uruchomieniowego?

Akustyka zajmuje około 0,01% procesora CPU na źródło na klatkę. Użycie pamięci RAM zależy od rozmiaru sceny i należą do zakresu od 10 do 100 MB.
 
## <a name="do-i-need-to-simplify-the-level-geometry-control-triangle-count-make-meshes-watertight"></a>Czy potrzebuję uprościć poziomu geometrii? Liczba trójkąt kontroli? Oczek, że wodoszczelne?

Nie. System będzie pozyskiwać szczegółowe geometrii poziomu bezpośrednio. Będzie ona voxelized dla wewnętrznego przetwarzania.
 
## <a name="whats-in-the-runtime-lookup-table"></a>Co znajduje się w tabeli odnośników czasu wykonywania?

Zawiera plik ACE jest tabelą parametrów akustycznych między wiele źródła i pary lokalizacji odbiornik, jak również geometrii sceny voxelized używane do interpolacji parametru.
 
## <a name="can-project-acoustics-handle-moving-sources"></a>Projekt Akustyka dzienniki może obsłużyć przenoszenia źródeł?

Tak, Akustyka projektu sprawdza tabelę odnośników i aktualizuje audio DSP na każdy takt, więc może obsługiwać przenoszenia źródeł i odbiornika.
 
## <a name="can-project-acoustics-handle-dynamic-geometry-closing-doors-walls-blown-away"></a>Akustyka projektu, może obsługiwać dynamiczne geometrii? Zamyka drzwi? Ściany fascynujące natychmiast?

Nie. Parametrów akustycznych są wstępnie obliczone oparte na stanie statyczne poziomu gier. Zalecamy pozostawienie geometrii drzwi poza Akustyka, a następnie zastosowanie dodatkowych zamknięcia na podstawie stanu zniszczalnych i przydzielaj obiektów gry przy użyciu ustalone technik.
 
## <a name="does-project-acoustics-use-acoustic-materials"></a>Akustyka projekt używa akustyczny materiałów?

Tak. Fizyczny nazw materiału w Twoim poziomie nawyki absorptivity pobrane materiały.
 
## <a name="what-do-the-probes-represent"></a>Co uwzględnia "sondy"?

Sondy są próbkowania lokalizacji możliwe odtwarzacza. Każdy sondy reprezentuje symulację oddzielne wave sceny pochodzące lokalizacji sondowania. W czasie wykonywania parametrów akustycznych dla lokalizacji odbiornika są interpolowane w pobliżu lokalizacji funkcji badania.
 
## <a name="why-spend-so-much-compute-in-the-cloud-what-does-it-buy-me"></a>Dlaczego warto poświęcić tak dużej ilości zasobów obliczeniowych w chmurze? Co to on kupić mnie?

Projekt Akustyka zapewnia dokładnych i wiarygodnych parametrów akustycznych nawet w przypadku niezwykle złożonych środowisk wirtualnych, uwzględniając każdy aspekt architektury. Zapewnia płynne zamknięcia i przeszkody i odmianą dynamiczne pogłosu bez konieczności ręcznej pracy rysowania woluminów. Cały czas pozostały światła CPU podczas wykonywania.

## <a name="what-exactly-happens-during-baking"></a>Co to dokładnie będzie się działo podczas "pieczenie"?

Tworzenie składa się z symulacji fale akustyczne cuboid symulacji regionów, w przypadku każdego sondowania odbiornika a ich tematyka.

## <a name="next-steps"></a>Kolejne kroki
* Spróbuj [Unity Akustyka projektu przykładowej zawartości](unity-quickstart.md) lub [Unreal przykładowej zawartości](unreal-quickstart.md)

