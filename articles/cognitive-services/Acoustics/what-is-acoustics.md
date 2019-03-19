---
title: Omówienie Akustyka projektu
titlesuffix: Azure Cognitive Services
description: Akustyka projektu jest Akustyka aparat 3D interaktywnych środowisk, integrowania wbudowanymi wave fizyki symulacji za pomocą kontrolek interaktywności.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: overview
ms.date: 08/17/2018
ms.author: kegodin
ms.openlocfilehash: 3d99ea5767c7b2e62f7228440201b4a9b6593b02
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/18/2019
ms.locfileid: "58136604"
---
# <a name="what-is-project-acoustics"></a>Co to jest akustyka projektu?
Akustyka projektu jest aparatem Akustyka wave 3D środowiska interaktywnego. Modeluje ona efektów wave, jak diffraction, portaling i pogłosu efekty w złożonych scen, bez konieczności ręcznego strefy znaczników. Zawiera także aparatu do tworzenia gier i integracji audio oprogramowania pośredniczącego. Filozofia Akustyka projektu jest podobna do statycznego oświetlenia: wprowadzić w trybie offline szczegółowe fizyki zapewnienie fizycznych linii bazowej, a za pomocą uproszczonego środowiska uruchomieniowego ekspresyjny projektowania formanty do zrealizowania określonych celów sztuki.

![Widok projektu](media/gears-with-voxels.jpg)

## <a name="using-wave-physics-for-interactive-acoustics"></a>Przy użyciu fizyki wave Akustyka interaktywne
Metody oparte na ray Akustyka można sprawdzić za pomocą rzutowania pojedynczej ray źródła do odbiornika zamknięcia lub dysku pogłosu poprzez oszacowanie woluminu lokalnego sceny przy użyciu kilku promieniach. Ale techniki te mogą być zawodne, ponieważ pebble occludes boulder w miarę. Promieniach nie uwzględniać sposób dźwięku zakrętów wokół obiektów, zjawiskiem, znane jako diffraction. Symulacja Akustyka projektu przechwytuje te skutki, przy użyciu symulacji na podstawie wave. Wynikiem jest bardziej przewidywalne i niezawodne.

Innowacje klucza Akustyka projektu jest kilka symulacji akustyczny tradycyjnych zaprojektowaniu pojęcia związane z. Tłumaczy je wyniki symulacji na tradycyjnych audio DSP parametrów zamknięcia., portaling i pogłosu. Projektant są używane kontrolki za pośrednictwem tego procesu tłumaczenia. Aby uzyskać szczegółowe informacje na temat podstawowych technologii za Akustyka projektu, odwiedź stronę [strony projektu badawczego](https://www.microsoft.com/en-us/research/project/project-triton/).

![Widok projektu](media/wave-simulation.gif)

## <a name="setup"></a>Konfigurowanie
[Integracja aparatu Unity Akustyka projektu](unity-integration.md) jest przeciągnij i upuść i uwzględnia wtyczki audio aparatu Unity. Rozszerzaj kontroli źródła audio Unity, dołączając Akustyka projektu C# formantów składników do każdego obiektu audio.

[Projekt integracji Akustyka Unreal](unreal-integration.md) zawiera wtyczki edytora i gier, Unreal i wtyczki mixer Wwise. Niestandardowe składnika audio rozszerza dobrze znanych funkcjonalności Wwise w ramach Unreal za pomocą na żywo Akustyka projektu kontrolki. Projektowanie kontrolki również są widoczne w Wwise na wtyczkę mixer.

## <a name="workflow"></a>Przepływ pracy
* **Wstępne tworzenie:** Rozpoczyna się od konfigurowania tworzenie, wybierając geometrię, która reaguje na Akustyka, na przykład ignorując szyby światła. Następnie edytuj automatycznego przypisania materiału i zaznaczanie obszarów nawigacji do odbiornika przewodnik próbkowania. Nie ma żadnych znaczników ręczne dla stref pogłosu/portalu/miejsca.
* **Tworzenie:** Etap analizy jest uruchamiany lokalnie, który wykonuje voxelization i pozostałych funkcji analizy geometrycznych na scenie, w zależności od wyborów powyżej. Wyniki są wizualizowane w edytorze, aby sprawdzić ustawienia sceny. Po przesłaniu tworzenie voxel przesyłane na platformie Azure, a następnie wrócić Akustyka zasobu gier.
* **Środowisko uruchomieniowe:** Załaduj zasób do poziomu, a wszystko jest gotowe do nasłuchiwania Akustyka w Twoim poziomie. Projekt Akustyka na żywo w edytorze za pomocą kontrolki źródła szczegółowych. Formanty również mogą być napędzane z poziomu skryptów.

## <a name="platforms"></a>Platformy
Obecnie można wdrożyć projektu Akustyka wtyczki środowiska uruchomieniowego na następujących platformach:
* Windows
* Android
* Xbox One

## <a name="download"></a>Do pobrania
* [Wtyczki Akustyka projektu i przykłady](https://www.microsoft.com/en-us/download/details.aspx?id=57346)
  * Pliki binarne konsoli Xbox i pomocy technicznej skontaktuj się z nami za pośrednictwem rejestracji poniższy formularz
* [Fora Akustyka projektu](https://social.msdn.microsoft.com/Forums/en-US/home?forum=projectacoustics)
* [Utwórz konto, aby otrzymywać aktualizacje na Akustyka projektu](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRwMoAEhDCLJNqtVIPwQN6rpUOFRZREJRR0NIQllDOTQ1U0JMNVc4OFNFSy4u)

## <a name="next-steps"></a>Kolejne kroki
* Spróbuj [Akustyka projektu Przewodnik Szybki start dotyczący Unity](unity-quickstart.md) lub [Unreal](unreal-quickstart.md)
* Zapoznaj się z [dźwiękowych zasady projektowania klas z Akustyka projektu](design-process.md)

