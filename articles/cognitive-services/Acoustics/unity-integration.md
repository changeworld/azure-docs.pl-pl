---
title: Integracja i wdrażanie projektu Akustyka Jedność
titlesuffix: Azure Cognitive Services
description: W tym artykule opisano, jak zintegrować wtyczkę Project Acoustics Unity z projektem Unity.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "72242996"
---
# <a name="project-acoustics-unity-integration"></a>Integracja projektu Akustyka Jedność
W tym artykule opisano, jak zintegrować wtyczkę Project Acoustics Unity z projektem Unity.

Wymagania dotyczące oprogramowania:
* [Unity 2018.2+](https://unity3d.com) dla systemu Windows
* [Pakiet Project Acoustics Unity](https://www.microsoft.com/download/details.aspx?id=57346)

## <a name="import-the-plug-in"></a>Importowanie wtyczki
1. Zaimportuj akustykę UnityPackage do projektu. 
 W unity przejdź do **pakietu** > **niestandardowego** > **Custom Package**importu zasobów .

    ![Menu Pakiet importu Unity](media/import-package.png)  

1. Wybierz **polecenie ProjectAcoustics.unitypackage**.

1. Wybierz przycisk **Importuj,** aby zintegrować pakiet Unity z projektem.

    ![Okno dialogowe Pakiet importu jedności](media/import-dialog.png)  

Jeśli importujesz wtyczkę do istniejącego projektu, projekt może już mieć plik *mcs.rsp* w katalogu głównym projektu. Ten plik określa opcje kompilatora języka C#. Scal zawartość tego pliku z plikiem mcs.rsp dostarczanym z wtyczką Project Acoustics.

## <a name="enable-the-plug-in"></a>Włącz wtyczkę
Część pieca zestawu narzędzi akustyki wymaga .NET 4. *x* wersja środowiska wykonawczego skryptów. Importowanie pakietów aktualizuje ustawienia odtwarzacza Unity. Uruchom ponownie unity dla tego ustawienia, aby wejść w życie.

![Panel Ustawienia odtwarzacza Unity](media/player-settings.png)

![Panel Ustawienia odtwarzacza Unity z wybraną pozycją .NET 4.5](media/net45.png)

## <a name="set-up-audio-dsp"></a>Konfigurowanie audio DSP
Projekt Akustyka zawiera audio runtime DSP, który integruje się z unity audio engine spatializer framework. Obejmuje zarówno hrtf oparte i panoramowania spatialization. Aby włączyć program DSP Akustyki projektu, przejdź do **sekcji Edytuj** > **ustawienia** > **projektu Audio,** aby otworzyć ustawienia dźwięku Unity. Wybierz **akustykę projektu** jako **wtyczkę Spatializer** dla swojego projektu. Upewnij się, że **rozmiar buforu DSP** jest ustawiony na *najlepszą wydajność*.

![Menu Ustawienia projektu Unity](media/project-settings.png)  

![Panel Ustawień Unity Spatializer z wybranym spatializerem Akustyka projektu](media/choose-spatializer.png)

Następnie otwórz mikser audio **(Window** > **Audio Mixer).** Upewnij się, że masz co najmniej jeden mikser z jedną grupą. Jeśli go nie masz, wybierz **+** przycisk po prawej stronie **mikserów**. Kliknij prawym przyciskiem myszy dolną część paska kanału w sekcji efekty i dodaj efekt **Mikser akustyki firmy Microsoft.** W ciągu jednego czasu obsługiwany jest tylko jeden mikser Akustyki Projektu.

![Mikser audio Unity, w skład który hostuje mikser Project Acoustics](media/audio-mixer.png)

## <a name="enable-acoustics-on-sound-sources"></a>Włącz akustykę w źródłach dźwięku
Tworzenie źródła dźwięku: zaznacz pole wyboru **Spatialize** u dołu panelu Inspektor audioSource. Upewnij się, że **opcja Mieszanie przestrzenne** jest ustawione na pełne *3D*.  

![Panel Źródło dźwięku Unity](media/audio-source.png)

## <a name="enable-acoustic-design"></a>Włącz projektowanie akustyczne
Dołącz skrypt *AcousticsAdjust* do źródła dźwięku w scenie, aby włączyć dodatkowe parametry projektu źródła: Wybierz **dodaj komponent** i wybierz opcję**Koryguj akustyka** **skryptów** > .

![Skrypt Unity AcousticsAdjust](media/acoustics-adjust.png)

## <a name="next-steps"></a>Następne kroki
* [Upiec swoją scenę z Projekt Akustyka dla Unity](unity-baking.md).
* [Utwórz konto usługi Azure Batch,](create-azure-account.md) aby upiec scenę w chmurze.
* Zapoznaj się z [procesem projektowania akustyka projektu](unity-workflow.md).
