---
title: Rozwiązanie tworzenie akustyczne projektu
titlesuffix: Azure Cognitive Services
description: W tym przeglądzie koncepcyjnym opisano różnice między poszczególnymi i bardziej precyzyjnymi rozwiązaniami w czasie, gdy jest to akustyczne
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
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2019
ms.locfileid: "68854346"
---
# <a name="project-acoustics-bake-resolution"></a>Rozwiązanie tworzenie akustyczne projektu
W tym przeglądzie koncepcyjnym opisano różnice między poszczególnymi i bardziej precyzyjnymi rozwiązaniami w czasie, gdy jest to akustyczne To ustawienie należy wybrać podczas kroku sondy przepływu pracy pieczenia.

## <a name="Coarse-vs-Fine-Resolution"></a>Grube rozwiązanie vs

Jedyną różnicą między ustawieniem bardzo duże i precyzyjne rozwiązanie jest częstotliwość wykonywania symulacji. Stosuje częstotliwość dwa razy jako duże. Ma to wpływ na symulację akustyczną:

* Długość fali dla znaków bardzo dużych jest dwa razy większa od, w związku z czym voxels są dwa razy duże.
* Czas symulacji jest bezpośrednio związany z rozmiarem Voxel, co sprawia, że duże tworzenie około 16 razy szybciej niż w przypadku tworzenie.
* Portale (na przykład drzwi lub okna) mniejsze niż rozmiar Voxel nie mogą być symulowane. Ustawienie bardzo duże może spowodować, że niektóre z tych mniejszych portali nie będą symulowane; w związku z tym nie przechodzą dźwięku w czasie wykonywania. Możesz sprawdzić, czy tak się dzieje, wyświetlając voxels.
* Niższa częstotliwość symulacji skutkuje mniej diffraction wokół rogów i krawędzi.
* Źródła dźwięku nie mogą znajdować się w obrębie "wypełnione" voxels (tj. voxels zawierających geometrię). Powoduje to brak dźwięku. Umieszczanie źródeł dźwięku jest trudniejsze, więc nie znajdują się w większej voxels niż w przypadku korzystania z ustawienia.
* Im większy voxels, jak pokazano poniżej. Pierwszy obraz został utworzony przy użyciu opcji bardzo duże, a drugi to ten sam bramą, przy użyciu precyzyjnej rozdzielczości. Jak wskazano na czerwono, nastąpi znacznie mniej włamania do bramą przy użyciu ustawienia bardziej szczegółowego. Niebieska linia to bramą zgodnie z definicją geometrii, natomiast czerwona linia jest skutecznym portalem akustycznym zdefiniowanym przez rozmiar Voxel. Sposób, w jaki te wtargnięcie odgrywa w danej sytuacji, zależy całkowicie od tego, jak voxels się z geometrią portalu, która jest określana na podstawie rozmiaru i lokalizacji obiektów w scenie.

![Zrzut ekranu przedstawiający gruby voxels wypełniający bramą w Unreal](media/unreal-coarse-bake.png)

![Zrzut ekranu przedstawiający szczegółowe voxels w bramą w Unreal](media/unreal-fine-bake.png)

## <a name="next-steps"></a>Następne kroki

Wypróbuj bardzo duże i precyzyjne ustawienia rozdzielczości, korzystając z naszych wtyczek [Unreal](unreal-baking.md) lub [Unity](unity-baking.md) .
