---
title: Rozwiązywanie problemów z projektem Akustyka tworzenie
titlesuffix: Azure Cognitive Services
description: To omówienie pojęć dotyczących opisano różnicę między rozdzielczość zgrubnym i poprawnie podczas pieczenie Akustyka.
services: cognitive-services
author: KyleStorck
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: KyleStorck
ms.openlocfilehash: 8684cf16e41c763db01c064242cc5466b1c83f01
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/07/2019
ms.locfileid: "67621410"
---
# <a name="project-acoustics-bake-resolution"></a>Rozwiązywanie problemów z projektem Akustyka tworzenie
To omówienie pojęć dotyczących opisano różnicę między rozdzielczość zgrubnym i poprawnie podczas pieczenie Akustyka. Możesz wybrać to ustawienie podczas wykonywania kroku sondy wypieku przepływu pracy.

## <a name="Coarse-vs-Fine-Resolution"></a>Rozpoznawanie poprawnie zdalnego programu vs

Jedyną różnicą między ustawień rozpoznawania zgrubnym i dobrym rozwiązaniem jest częstotliwość, w którym odbywa się symulacji. Szczegółowe wykorzystuje dwa razy możliwie jak zgrubnym częstotliwości. Ma to liczba skutki akustyczny symulacji:

* Długość fali grubych jest dwa razy tak długo, jak poprawnie i dlatego voxels dwukrotnie są tak duże.
* Rozmiar voxel, dzięki czemu tworzenie zgrubnym około 16 razy szybciej niż tworzenie dobrym rozwiązaniem jest bezpośrednio związana czasu symulacji.
* Nie może być symulowana portali (na przykład drzwi lub systemu windows) mniejszy niż rozmiar voxel. Gruba ustawienie może spowodować, że niektóre z tych portali mniejszych, aby nie być symulowana; w związku z tym nie będzie przekazują dźwięku za pomocą w czasie wykonywania. Aby zobaczyć, jeśli to się dzieje, wyświetlając voxels.
* Mniejszą częstotliwością symulacji powoduje mniej diffraction wokół rogi i krawędzie.
* Nie można zlokalizować źródła dźwięku wewnątrz voxels "wypełniony" (czyli voxels zawierające geometrii). Skutkuje to Brak dźwięku. Jest trudniejsze do umieszczenia źródeł dźwięku, dzięki czemu nie wewnątrz większych voxels z grubą, niż gdyby poprawnie ustawienie.
* Większe voxels będzie mającym więcej do portali, jak pokazano poniżej. Pierwszy obraz został utworzony przy użyciu zdalnego, podczas gdy druga jest tego samego pola, za pomocą cienkiej rozpoznawania. Wskazane przez czerwony oznaczenia, jest znacznie mniej nieautoryzowanego dostępu do pola przy użyciu ustawień w dobrym stanie. Niebieska linia jest bramą, zgodnie z definicją geometrii, podczas gdy czerwona linia jest skuteczne portal akustyczne, zależy od rozmiaru voxel. Jak ta włamań odgrywa w danej sytuacji zależy od całkowicie jak voxels wiersz w górę przy użyciu geometrii portalu, który zależy od rozmiaru i lokalizacje obiektów w scenie.

![Zrzut ekranu przedstawiający zgrubnym voxels, wypełniając pola w Unreal](media/unreal-coarse-bake.png)

![Zrzut ekranu przedstawiający voxels dobrym rozwiązaniem w pola w Unreal](media/unreal-fine-bake.png)

## <a name="next-steps"></a>Kolejne kroki

Wypróbuj ustawień rozpoznawania zgrubnym i poprawnie samodzielnie za pomocą naszych [Unreal](unreal-baking.md) lub [Unity](unity-baking.md) wtyczek.