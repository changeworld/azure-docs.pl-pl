---
title: Często zadawane pytania dotyczące Akustyka - usług Cognitive Services
description: Ta strona zawiera odpowiedzi na pytania często zadawane Akustyka projektu, w tym instrukcje pobierania i tworzenie procesu.
services: cognitive-services
author: kegodin
manager: noelc
ms.service: cognitive-services
ms.component: acoustics
ms.topic: article
ms.date: 08/17/2018
ms.author: kegodin
ms.openlocfilehash: a71e867bd23cf64b2ac7fc8cd1c54c55d92ce924
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/28/2018
ms.locfileid: "47431792"
---
# <a name="frequently-asked-questions"></a>Często zadawane pytania

## <a name="what-is-project-acoustics"></a>Co to jest akustyka projektu?

Wtyczka Unity Akustyka projektu to system Akustyka obliczający zachowanie dźwięku przed środowiska uruchomieniowego, podobnie statyczne oświetlenia. Chmura ma skomplikowanymi fali fizyki obliczeń, więc koszt czasu wykonywania procesora CPU jest niska.  

## <a name="where-can-i-download-the-plugin"></a>Gdzie można pobrać wtyczkę?

Jeśli interesuje Cię ocena wtyczki akustyki, zarejestruj się [tutaj](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRwMoAEhDCLJNqtVIPwQN6rpUOFRZREJRR0NIQllDOTQ1U0JMNVc4OFNFSy4u), aby dołączyć do programu Designer Preview.

## <a name="is-azure-used-at-runtime"></a>Azure jest używana w środowisku uruchomieniowym?

Nie, integracja z chmurą jest używane tylko na etapie precompute jako część instalacji sceny.
 
## <a name="what-is-simulation-input"></a>Co to jest symulacji dane wejściowe? 

Dane wejściowe symulacji są sceny 3D, poziomie grę lub środowisko wirtualne. Projekt Akustyka wykonuje 3D wave pomiarowej symulacje, które modelują fizyki dźwięku ściśle, łącznie z płynnym zamknięcia i rozproszenie.
 
## <a name="what-is-the-runtime-cost"></a>Jaki jest koszt środowiska uruchomieniowego?

Akustyka zajmuje około 0,01% procesora CPU na źródło na klatkę. Użycie pamięci RAM zależy od rozmiaru sceny i należą do zakresu od 10 do 100 MB.
 
## <a name="do-i-need-to-simplify-the-level-geometry-control-triangle-count-make-meshes-watertight"></a>Czy potrzebuję uprościć poziomu geometrii? Liczba trójkąt kontroli? Oczek, że wodoszczelne?

Nie. System będzie pozyskiwać szczegółowe geometrii poziomu bezpośrednio. Będzie ona voxelized dla wewnętrznego przetwarzania.
 
## <a name="whats-in-the-runtime-lookup-table"></a>Co znajduje się w tabeli odnośników czasu wykonywania?

Plik ACE jest tabelą parametrów akustycznych między wiele pary lokalizacji źródłowej i odbiornika.
 
## <a name="can-it-handle-moving-sources"></a>Może on obsługiwać przenoszenia źródeł

Tak, **Acoustics Microsoft** wtyczki spatializer Unity konsultować się z tabeli odnośników na każdy takt audio przetwarzania przy użyciu bieżącej lokalizacji źródłowej i odbiornika. Procesor DSP spatializer aktualizuje sprawnie parametrów akustycznych przetwarzania na każdy takt.
 
## <a name="can-it-handle-dynamic-geometry-closing-doors-walls-blown-away"></a>Może on obsługiwać dynamiczne geometrii Zamyka drzwi? Ściany fascynujące natychmiast?

Nie. Parametrów akustycznych są wstępnie obliczone oparte na stanie statyczne poziomu gier. Firma Microsoft sugeruje, pozostawiając geometrii drzwi poza Akustyka, a następnie zastosować dodatkowe zamknięcia na podstawie stanu obiektów zniszczalnych i ruchomego gier przy użyciu technik ustalonych.
 
## <a name="does-it-handle-materials"></a>Go obsługuje materiałów?

Tak. Fizyczny nazw materiału w Twoim poziomie nawyki absorptivity pobrane materiały.
 
## <a name="what-do-the-probes-represent"></a>Co uwzględnia "sondy"?

Sondy są próbkowania lokalizacji możliwe odtwarzacza. Każdy sondy reprezentuje symulację oddzielne wave sceny pochodzące lokalizacji sondowania. W czasie wykonywania parametrów akustycznych dla lokalizacji odbiornika są interpolowane w pobliżu lokalizacji funkcji badania.
 
## <a name="why-spend-so-much-compute-in-the-cloud-what-does-it-buy-me"></a>Dlaczego warto poświęcić tak dużej ilości zasobów obliczeniowych w chmurze? Co to on kupić mnie?

Projekt Akustyka zapewnia dokładnych i wiarygodnych parametrów akustycznych nawet w przypadku niezwykle złożonych środowisk wirtualnych, uwzględniając każdy aspekt architektury. Zapewnia płynne zamknięcia. / przeszkoda bez ręcznej pracy i dynamiczne pogłosu odmiany bez rysowania woluminów. Cały czas pozostały światła CPU podczas wykonywania.

## <a name="what-exactly-happens-during-baking"></a>Co to dokładnie będzie się działo podczas "pieczenie"?

System uważa potencjalne lokalizacje odtwarzacza, aby wygenerować zestaw równomiernie rozmieszczonych pozycji "sondy" przykładowy. Tworzenie dla poziomu składa się z niezależnych zadań dla każdego sondy: system uzna cuboid "Symulacji Region", a ich tematyka na sondę i wykonuje symulacji wave szczegółowe, w tym regionie, rozdzielczością maksymalnie 25 cm.

## <a name="next-steps"></a>Następne kroki
* Zapoznaj się z [sceny próbki](sample-walkthrough.md)

