---
title: Rozdzielczość pieca akustyki projektu
titlesuffix: Azure Cognitive Services
description: Ten przegląd koncepcyjny opisuje różnicę między grubymi i drobnymi rozdzielczościami podczas pieczenia akustyki.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: d8eb3b2cbaf7b4e842d8338eefde756f6d381111
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "68854346"
---
# <a name="project-acoustics-bake-resolution"></a>Rozdzielczość pieca akustyki projektu
Ten przegląd koncepcyjny opisuje różnicę między grubymi i drobnymi rozdzielczościami podczas pieczenia akustyki. To ustawienie należy wybrać podczas etapu Sondy przepływu pracy pieczenia.

## <a name="coarse-vs-fine-resolution"></a><a name="Coarse-vs-Fine-Resolution"></a>Gruba a dobra rozdzielczość

Jedyną różnicą między ustawieniami rozdzielczości grubo i precyzyjnej jest częstotliwość, z jaką przeprowadzana jest symulacja. Fine wykorzystuje częstotliwość dwukrotnie większą niż gruba. Ma to szereg konsekwencji dla symulacji akustycznej:

* Długość fali dla grubej jest dwa razy dłuższa niż drobna, a zatem woki są dwa razy większe.
* Czas symulacji jest bezpośrednio związany z rozmiarem woxelu, dzięki czemu gruboisty piec jest około 16 razy szybszy niż drobny piec.
* Portale (na przykład drzwi lub okna) mniejsze niż rozmiar voxel nie mogą być symulowane. Ustawienie gruboiste może spowodować, że niektóre z tych mniejszych portali nie będą symulowane; w związku z tym nie będą przekazywać dźwięk w czasie wykonywania. Możesz sprawdzić, czy tak się dzieje, przeglądając voxels.
* Niższa częstotliwość symulacji powoduje mniejszą dyfrakcję wokół narożników i krawędzi.
* Źródła dźwięku nie mogą znajdować się wewnątrz "wypełnionych" woxels (tj. voxels, które zawierają geometrię). Powoduje to brak dźwięku. Trudniej jest umieścić źródła dźwięku, więc nie znajdują się one wewnątrz większych woki grube niż podczas korzystania z grzywny ustawienie.
* Większe woki wtrącają się bardziej do portali, jak pokazano poniżej. Pierwszy obraz został utworzony przy użyciu grubo, podczas gdy drugi jest tym samym drzwiem przy użyciu doskonałej rozdzielczości. Jak wskazują czerwone oznaczenia, jest znacznie mniej włamań do drzwi za pomocą drobnego ustawienia. Niebieska linia jest bramą zdefiniowaną przez geometrię, podczas gdy czerwona linia jest skutecznym portalem akustycznym zdefiniowanym przez rozmiar woxelu. Sposób, w jaki to wtargnięcie rozgrywa się w danej sytuacji, zależy całkowicie od tego, jak woki są zgodne z geometrią portalu, która zależy od wielkości i lokalizacji obiektów w scenie.

![Zrzut ekranu przedstawiający grube woki wypełniających drzwi w Unreal](media/unreal-coarse-bake.png)

![Zrzut ekranu z drobnymi voxels w drzwiach w Unreal](media/unreal-fine-bake.png)

## <a name="next-steps"></a>Następne kroki

Wypróbuj grube i precyzyjne ustawienia rozdzielczości samodzielnie, korzystając z naszych wtyczek [Unreal](unreal-baking.md) lub [Unity.](unity-baking.md)
