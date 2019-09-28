---
title: Omówienie funkcji akustycznych projektu
titlesuffix: Azure Cognitive Services
description: Akustyczny projekt to aparat akustyczny dla interaktywnych środowisk 3W, integrowanie symulacji fizyki rozszerzania Wave z interaktywnymi kontrolkami projektu.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: overview
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: 65678f08399f378b8580eed79e49197dd4d84c64
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71351137"
---
# <a name="what-is-project-acoustics"></a>Co to jest akustyka projektu?
Akustyczny projekt jest aparatem akustycznym Wave dla interaktywnych środowisk 3W. Modele it to efekty Wave, takie jak zamknięcia, przeszkody, portale i efekty reverberation w złożonych scenach, bez konieczności ręcznego stosowania znaczników strefy lub intensywnego raytracing procesora CPU. Obejmuje to także integrację aparatu gier i oprogramowania pośredniczącego. Funkcja wyznaczania wartości akustycznych projektu jest podobna do statycznego oświetlenia: Tworzenie szczegółowej fizyki w trybie offline, aby zapewnić fizyczną linię bazową, i korzystać z uproszczonego środowiska uruchomieniowego z kontrolkami projektowymi, aby zaspokoić cele w zakresie hałasu wirtualnego świata.

![Zrzut ekranu przedstawiający narzędzia War 4 z voxels akustycznymi](media/gears-with-voxels.jpg)

## <a name="using-wave-physics-for-interactive-acoustics"></a>Korzystanie z fizyki Wave dla interaktywnych hałasu
Metody akustyczne oparte na protokole ray mogą sprawdzać zamknięcia za pomocą jednego rzutu Source-Listener lub Reverb przez oszacowanie lokalnego woluminu sceny przy użyciu kilku promieni. Jednak te techniki mogą być niezawodne, ponieważ Pebble occludes tak samo jak w przypadku Boulder. Promienie nie uwzględniają sposobu, w jaki dźwięk zgina się wokół obiektów, zjawisko znanego jako diffraction. Symulacja akustyczna projektu przechwytuje te efekty przy użyciu symulacji opartej na Wave. Wartości akustyczne są bardziej przewidywalne, dokładne i bezproblemowe.

Kluczowe innowacje w projekcie polegają na rozłączeniu symulacji akustycznej opartej na dźwiękowym czasie rzeczywistym za pomocą tradycyjnych koncepcji projektowania dźwięku. Tłumaczy wyniki symulacji na tradycyjne parametry Audio DSP dla zamknięcia, portale i Reverb. Projektant używa formantów dla tego procesu tłumaczenia. Aby uzyskać więcej informacji na temat podstawowych technologii związanych z dźwiękiem, odwiedź [stronę projektu badania](https://www.microsoft.com/en-us/research/project/project-triton/).

![Animacja pokazująca poziomy rozpropagowania fali Wave przez scenę](media/wave-simulation.gif)

## <a name="video-presentation-from-gdc-2019-30-min"></a>Prezentacja wideo z GDC 2019 (~ 30 min)
[Film wideo ![Project akustyczny](https://img.youtube.com/vi/uY4G-GUAQIE/0.jpg)](https://www.youtube.com/watch?v=uY4G-GUAQIE "Kliknij, aby odtworzyć wideo")

## <a name="setup"></a>Konfiguracja
Integracja z mechanizmem [akustycznym w projekcie](unity-integration.md) polega na przeciąganiu i upuszczaniu i włączaniu wtyczki aparatu audio aparatu Unity. Rozszerzanie kontroli źródła audio Unity przez dołączenie składnika C# kontroli akustycznych projektu do każdego obiektu audio.

Integracja z obsługą [Unreali projektu](unreal-integration.md) obejmuje wtyczki edytora i gier dla Unreal oraz wtyczkę Wwise. Niestandardowy składnik audio rozszerza znane funkcje Wwise w programie Unreal z kontrolkami projektu na żywo. Kontrolki projektowania są również uwidocznione w Wwise na wtyczki miksera.

## <a name="workflow"></a>Przepływ pracy
* **Przed tworzenie:** Zacznij od konfigurowania tworzenie, wybierając geometrię reaguje na akustyczne, na przykład ignorując wałki świetlne. Następnie Edytuj automatyczne przypisania materiału i wybierz obszary nawigacji, aby Przewodnik po próbkach odbiornika. Nie ma znaczników ręcznych dla Reverb/Portal/strefy pokojowej.
* **Tworzenie** Krok analizy jest uruchamiany lokalnie, co oznacza, że voxelization i inne analizy geometryczne sceny na podstawie powyższych opcji. Wyniki są wizualizowane w edytorze, aby sprawdzić konfigurację sceny. W przypadku przesyłania tworzenie dane Voxel są wysyłane na platformę Azure i są odbierane przez użytkownika.
* **Środowiska uruchomieniowego** Załaduj zasób do swojego poziomu i wszystko jest gotowe do nasłuchiwania obciążeń akustycznych na poziomie. Zaprojektuj na żywo akustyczne w edytorze, używając szczegółowych kontroli dla poszczególnych źródeł. Formanty mogą być również sterowane na poziomie skryptów.

## <a name="runtime-platforms"></a>Platformy środowiska uruchomieniowego
Wtyczki środowiska uruchomieniowego akustyczne projektu można obecnie wdrożyć na następujących platformach:
* Windows
* MacOS
* Android
* Xbox One

## <a name="editor-platforms"></a>Platformy edytora
Wtyczka edytora akustycznego projektu jest dostępna dla następujących platform:
* Windows
* MacOS (tylko aparat Unity)

## <a name="download"></a>Do pobrania
* [Wtyczka i przykłady w środowisku Unity](https://www.microsoft.com/en-us/download/details.aspx?id=57346)
* [Unreal akustyczny projektu i przykłady & Wwise](https://www.microsoft.com/download/details.aspx?id=58090)
  * W przypadku plików binarnych usługi Xbox i innych pomocy technicznej skontaktuj się z nami za pośrednictwem [forum](https://github.com/microsoft/ProjectAcoustics/issues).

## <a name="contact-us"></a>Skontaktuj się z nami
* [Dyskusje i raporty o problemach dotyczących projektów](https://github.com/microsoft/ProjectAcoustics/issues)

## <a name="next-steps"></a>Następne kroki
* Wypróbuj [Przewodnik Szybki Start dla środowiska Unity](unity-quickstart.md) lub [Unreal](unreal-quickstart.md)
* Poznaj informacje [o projekcji projektu dźwiękowego dotyczącego hałasu](design-process.md)

