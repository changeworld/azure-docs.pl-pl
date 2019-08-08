---
title: Integracja i wdrażanie w środowisku Unity
titlesuffix: Azure Cognitive Services
description: W tym instruktażu wyjaśniono integrację wtyczki aparatu Unity z dźwiękiem w projekcie aparatu Unity.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: dffef5062e89ef725abcfda187f5e6159c676b9c
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2019
ms.locfileid: "68854323"
---
# <a name="project-acoustics-unity-integration"></a>Integracja środowiska Unity w projekcie
W tym instruktażu wyjaśniono integrację wtyczki aparatu Unity z dźwiękiem w projekcie aparatu Unity.

Wymagania dotyczące oprogramowania:
* [Unity 2018.2 +](https://unity3d.com) dla systemu Windows
* [Pakiet Unity akustycznych projektu](https://www.microsoft.com/download/details.aspx?id=57346)

## <a name="import-the-plugin"></a>Zaimportuj wtyczkę
Zaimportuj UnityPackage akustyczne do projektu. 
* W środowisku Unity przejdź do pozycji **zasoby > Importuj pakiet > pakietu niestandardowego...**

    ![Zrzut ekranu przedstawiający menu pakiet importowania aparatu Unity](media/import-package.png)  

* Wybierz **ProjectAcoustics. UNITYPACKAGE**

Jeśli importujesz wtyczkę do istniejącego projektu, projekt może już mieć plik **MCS. rsp** w katalogu głównym projektu, który określa opcje C# kompilatora. Należy scalić zawartość tego pliku z plikiem MCS. rsp, który jest dostarczany z wtyczką akustyczną projektu.

## <a name="enable-the-plugin"></a>Włącz wtyczkę
Część Tworzenie zestawu narzędzi akustycznych wymaga wersji środowiska uruchomieniowego obsługi skryptów .NET 4. x. Importowanie pakietu zaktualizuje ustawienia odtwarzacza Unity. Uruchom ponownie środowisko Unity, aby to ustawienie zaczęło obowiązywać.

![Zrzut ekranu przedstawiający panel ustawień odtwarzacza Unity](media/player-settings.png)

![Zrzut ekranu przedstawiający panel ustawień odtwarzacza Unity z wybranym programem .NET 4,5](media/net45.png)

## <a name="set-up-audio-dsp"></a>Konfigurowanie Audio DSP
Akustyczne projektu zawiera procesor DSP środowiska uruchomieniowego audio, który integruje się z platformą spatializer aparatu audio Unity. Obejmuje zarówno HRTF, jak i panoramowanie spatialization. Włącz akustyczny dla projektu, otwierając ustawienia audio Unity przy użyciu opcji **edytuj > Project ustawienia > audio**, a następnie wybierając pozycję **akustyczne projektu** jako **wtyczkę spatializer** dla projektu. Upewnij się, że **rozmiar buforu DSP** jest ustawiony na najlepszą wydajność.

![Zrzut ekranu przedstawiający panel ustawień projektu środowiska Unity](media/project-settings.png)  

![Zrzut ekranu przedstawiający panel ustawień spatializer środowiska Unity z wybranymi dźwiękami spatializer](media/choose-spatializer.png)

Następnie otwórz mikser audio (**okno > mikser audio**). Upewnij się, że masz co najmniej jeden mikser, z jedną grupą. Jeśli nie, kliknij przycisk "+" znajdujący się po prawej stronie **mikserów**. Kliknij prawym przyciskiem myszy dolny pasek kanału w sekcji efekty i Dodaj efekt miksera **hałasu projektu** . Należy zauważyć, że tylko jeden mikser akustyczny w projekcie jest obsługiwany jednocześnie.

![Zrzut ekranu przedstawiający mikser audio aparatu Unity obsługujący dźwięk z mikserem](media/audio-mixer.png)

## <a name="enable-acoustics-on-sound-sources"></a>Włącz akustyczne w źródłach dźwięku
Utwórz źródło audio. Kliknij pole wyboru u dołu panelu Inspektora AudioSource, który wskazuje **Spatialize**. Upewnij się, że dla **mieszania przestrzennego** ustawiono pełny 3W.  

![Zrzut ekranu przedstawiający panel źródła audio Unity](media/audio-source.png)

## <a name="enable-acoustic-design"></a>Włącz układ akustyczny
Dołącz skrypt **AcousticsAdjust** do źródła dźwięku w scenie, aby włączyć dodatkowe parametry projektu źródłowego, klikając pozycję **Dodaj składnik** i wybierając pozycję **Skrypty > Ustawienia akustyczne dostosowują**się:

![Zrzut ekranu przedstawiający skrypt AcousticsAdjust środowiska Unity](media/acoustics-adjust.png)

## <a name="next-steps"></a>Kolejne kroki
* [Tworzenie scenę przy użyciu akustycznych projektu dla aparatu Unity](unity-baking.md)
* [Utwórz konto Azure Batch](create-azure-account.md) , aby tworzenie swoją scenę w chmurze
* Zapoznaj się z [procesem projektowania aparatu Unity](unity-workflow.md)w środowisku projektowym.

