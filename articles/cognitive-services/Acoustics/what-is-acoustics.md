---
title: Omówienie akustyki projektu
titlesuffix: Azure Cognitive Services
description: Project Acoustics to silnik akustyki do interaktywnych doświadczeń 3D, integrujący symulację fizyki pieczonej fali z interaktywnymi elementami sterującymi projektem.
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
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71351137"
---
# <a name="what-is-project-acoustics"></a>Co to jest akustyka projektu?
Project Acoustics to silnik akustyki falowej do interaktywnych doświadczeń 3D. Modeluje efekty fal, takie jak okluzja, przeszkoda, portaling i efekty pogłosu w złożonych scenach bez konieczności ręcznego oznaczania stref lub intensywnego raytracingu procesora. Obejmuje również silnik gry i integrację oprogramowania pośredniczącego audio. Filozofia Projektu Akustyka jest podobna do oświetlenia statycznego: upiec szczegółową fizykę w trybie offline, aby zapewnić fizyczną linię bazową, i użyj lekkiego środowiska uruchomieniowego z ekspresyjnymi elementami sterującymi projektowymi, aby osiągnąć swoje cele artystyczne w zakresie akustyki wirtualnego świata.

![Zrzut ekranu z Gears of War 4 przedstawiający voxels akustyki](media/gears-with-voxels.jpg)

## <a name="using-wave-physics-for-interactive-acoustics"></a>Wykorzystanie fizyki fal do interaktywnej akustyki
Metody akustyki oparte na promieniach mogą sprawdzać niedrożność za pomocą pojedynczego źródła do odbiornika, lub prowadzić pogłos, oceniając lokalną objętość sceny za pomocą kilku promieni. Ale te techniki mogą być zawodne, ponieważ kamyk zakuwa tyle, co głaz. Promienie nie uwzględniają sposobu, w jaki dźwięk wygina się wokół obiektów, co jest zjawiskiem znanym jako dyfrakcja. Symulacja akustyki projektu rejestruje te efekty za pomocą symulacji opartej na falach. Akustyka jest bardziej przewidywalna, dokładna i płynna.

Kluczową innowacją projektu Akustyka jest para prawdziwej symulacji akustycznej opartej na falach dźwiękowych z tradycyjnymi koncepcjami projektowania dźwięku. Przekłada wyniki symulacji na tradycyjne parametry DSP audio dla okluzji, portaling i pogłosu. Projektant używa formantów nad tym procesem tłumaczenia. Aby uzyskać więcej informacji na temat podstawowych technologii projektu Akustyka, odwiedź [stronę projektu badawczego](https://www.microsoft.com/en-us/research/project/project-triton/).

![Animacja przedstawiająca poziomy fragment fali 2D przez scenę](media/wave-simulation.gif)

## <a name="video-presentation-from-gdc-2019-30-min"></a>Prezentacja wideo z GDC 2019 (~30 min)
[![Wideo akustyki projektu](https://img.youtube.com/vi/uY4G-GUAQIE/0.jpg)](https://www.youtube.com/watch?v=uY4G-GUAQIE "Kliknij, aby odtworzyć film")

## <a name="setup"></a>Konfiguracja
[Integracja Project Acoustics Unity](unity-integration.md) jest przeciągana i upuszczana i zawiera wtyczkę silnika audio Unity. Powiększ formanty źródła dźwięku Unity, dołączając składnik formantu Akustyka projektu C# do każdego obiektu audio.

[Integracja Project Acoustics Unreal](unreal-integration.md) obejmuje edytor i wtyczki do gier dla Unreal oraz wtyczkę miksera Wwise. Niestandardowy komponent audio rozszerza znane funkcje Wwise w ramach Unreal dzięki kontrolom projektowania akustyki na żywo. Elementy sterujące projektami są również widoczne w Wwise na wtyczce miksera.

## <a name="workflow"></a>Przepływ pracy
* **Pieczenie wstępne:** Zacznij od ustawienia pieca, wybierając geometrię, która reaguje na akustykę, na przykład ignorując wały świetlne. Następnie edytuj automatyczne przypisywanie materiałów i wybieraj obszary nawigacji, aby poprowadzić próbkowanie odbiornika. Nie ma ręcznego znaczników dla stref pogłosu/ portalu/pokoju.
* **Piec:** Krok analizy jest uruchamiany lokalnie, co powoduje woxelizację i inną analizę geometryczną na scenie na podstawie powyższych wyborów. Wyniki są wizualizowane w edytorze w celu weryfikacji konfiguracji sceny. Po przesłaniu pieczenia dane voxel są wysyłane na platformę Azure i otrzymujesz z powrotem zasób gry akustyki.
* **Środowisko wykonawcze:** Załaduj zasób na swój poziom, a będziesz gotowy słuchać akustyki na swoim poziomie. Projektowanie akustyki na żywo w edytorze przy użyciu elementów sterujących granulowany na źródło. Formanty mogą być również napędzane z poziomu skryptów.

## <a name="runtime-platforms"></a>Platformy runtime
Wtyczki środowiska wykonawczego Project Acoustics można obecnie wdrożyć na następujących platformach:
* Windows
* MacOS
* Android
* Xbox One

## <a name="editor-platforms"></a>Platformy edytora
Wtyczka edytora Project Acoustics jest dostępna dla następujących platform:
* Windows
* MacOS (tylko Jedność)

## <a name="download"></a>Pobierz
* [Wtyczka i próbki Projektu Akustyka Unity](https://www.microsoft.com/en-us/download/details.aspx?id=57346)
* [Project Acoustics Unreal & wtyczek i próbek Wwise](https://www.microsoft.com/download/details.aspx?id=58090)
  * Aby uzyskać pliki binarne konsoli Xbox i inne wsparcie, skontaktuj się z nami za pośrednictwem [forum](https://github.com/microsoft/ProjectAcoustics/issues).

## <a name="contact-us"></a>Skontaktuj się z nami
* [Dyskusja na temat akustyki projektu i raportowanie problemów](https://github.com/microsoft/ProjectAcoustics/issues)

## <a name="next-steps"></a>Następne kroki
* Wypróbuj [szybki start akustyki projektu dla unity](unity-quickstart.md) lub [dla unreal](unreal-quickstart.md)
* Poznaj [filozofię projektowania dźwięku projektu Akustyka](design-process.md)

