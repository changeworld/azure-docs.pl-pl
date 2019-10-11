---
title: Integracja i wdrażanie w środowisku Unity
titlesuffix: Azure Cognitive Services
description: W tym artykule opisano sposób integrowania wtyczki Unity w projekcie w projekcie Unity.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: a8ddb0e4ca2ee4396a25a70c8b60b653aebb72d8
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/10/2019
ms.locfileid: "72242996"
---
# <a name="project-acoustics-unity-integration"></a>Integracja środowiska Unity w projekcie
W tym artykule opisano sposób integrowania wtyczki Unity w projekcie w projekcie Unity.

Wymagania dotyczące oprogramowania:
* [Unity 2018.2 +](https://unity3d.com) dla systemu Windows
* [Pakiet Unity akustycznych projektu](https://www.microsoft.com/download/details.aspx?id=57346)

## <a name="import-the-plug-in"></a>Zaimportuj wtyczkę
1. Zaimportuj UnityPackage akustyczne do projektu. 
 W środowisku Unity przejdź do pozycji **zasoby** > **Importuj pakiet** > **niestandardowy pakiet**.

    ![Menu pakiet importowania aparatu Unity](media/import-package.png)  

1. Wybierz **ProjectAcoustics. UNITYPACKAGE**.

1. Wybierz przycisk **Importuj** , aby zintegrować pakiet Unity z projektem.

    ![Okno dialogowe pakiet importowania aparatu Unity](media/import-dialog.png)  

Jeśli importujesz wtyczkę do istniejącego projektu, projekt może już mieć plik *MCS. rsp* w katalogu głównym projektu. Ten plik określa opcje C# kompilatora. Scal zawartość tego pliku z plikiem MCS. rsp, który jest dostarczany wraz z wtyczką dźwiękową projektu.

## <a name="enable-the-plug-in"></a>Włącz wtyczkę
Część Tworzenie zestawu narzędzi akustycznych wymaga programu .NET 4. wersja środowiska uruchomieniowego tworzenia skryptów *x* . Import pakietu aktualizuje ustawienia odtwarzacza Unity. Uruchom ponownie środowisko Unity, aby to ustawienie zaczęło obowiązywać.

![Panel ustawień odtwarzacza Unity](media/player-settings.png)

![Panel ustawień odtwarzacza Unity z wybranym programem .NET 4,5](media/net45.png)

## <a name="set-up-audio-dsp"></a>Konfigurowanie Audio DSP
Akustyczne projektu zawiera procesor DSP środowiska uruchomieniowego audio, który integruje się z platformą spatializer aparatu audio Unity. Obejmuje zarówno HRTF, jak i panoramowanie spatialization. Aby włączyć ustawienia akustyczne dla projektu, przejdź do **Edytuj** **Ustawienia projektu** >   > **audio** , aby otworzyć ustawień audio aparatu Unity. Wybierz pozycję **akustyczne projektu** jako **wtyczkę spatializer** dla projektu. Upewnij się, że **rozmiar buforu DSP** jest ustawiony na *najlepszą wydajność*.

![Menu Ustawienia projektu aparatu Unity](media/project-settings.png)  

![Panel ustawień spatializer środowiska Unity z wybranymi dźwiękami spatializer](media/choose-spatializer.png)

Następnie otwórz mikser audio (**okno** > **mikser audio**). Upewnij się, że masz co najmniej jeden mikser, z jedną grupą. Jeśli go nie masz, zaznacz przycisk **+** z prawej strony **mikserów**. Kliknij prawym przyciskiem myszy dolny pasek kanału w sekcji efekty i Dodaj efekt **miksera firmy Microsoft** . Tylko jeden mikser akustyczny w projekcie jest obsługiwany w danym momencie.

![Mikser audio Unity obsługujący mieszarkę akustyczną projektu](media/audio-mixer.png)

## <a name="enable-acoustics-on-sound-sources"></a>Włącz akustyczne w źródłach dźwięku
Utwórz źródło audio: zaznacz pole wyboru **Spatialize** w dolnej części panelu Inspektora AudioSource. Upewnij się, że dla **mieszania przestrzennego** ustawiono pełny *3W*.  

![Panel źródła audio Unity](media/audio-source.png)

## <a name="enable-acoustic-design"></a>Włącz układ akustyczny
Dołącz skrypt *AcousticsAdjust* do źródła dźwięku w scenie, aby włączyć dodatkowe parametry projektowania źródła: wybierz pozycję **Dodaj składnik** i wybierz pozycję **skrypty** > **Ustawienia akustyczne**.

![Skrypt AcousticsAdjust środowiska Unity](media/acoustics-adjust.png)

## <a name="next-steps"></a>Następne kroki
* [Tworzenie swoją scenę przy użyciu akustycznych projektów dla aparatu Unity](unity-baking.md).
* [Utwórz konto Azure Batch](create-azure-account.md) , aby tworzenie swoją scenę w chmurze.
* Zapoznaj się z [procesem projektowania aparatu Unity w środowisku projektowym](unity-workflow.md).
