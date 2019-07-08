---
title: Projekt Akustyka Unity integracji i ciągłego wdrażania
titlesuffix: Azure Cognitive Services
description: Niniejszy instruktaż wyjaśnia integracja wtyczki projektu Akustyka Unity w swoim projekcie aparatu Unity.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: kegodin
ms.openlocfilehash: 2deddfd4e6c03b53306d8fbab3340dce464158b0
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/07/2019
ms.locfileid: "67612739"
---
# <a name="project-acoustics-unity-integration"></a>Integracja aparatu Unity Akustyka projektu
Niniejszy instruktaż wyjaśnia integracja wtyczki projektu Akustyka Unity w swoim projekcie aparatu Unity.

Wymagania dotyczące oprogramowania:
* [Unity 2018.2 +](https://unity3d.com) dla Windows
* [Pakiet Unity Akustyka projektu](https://www.microsoft.com/download/details.aspx?id=57346)

## <a name="import-the-plugin"></a>Importuj wtyczki
Importuj Akustyka UnityPackage do projektu. 
* Na platformie Unity, przejdź do **zasoby > Importuj pakiet > Pakiet niestandardowy...**

    ![Zrzut ekranu z importowanie pakietu Unity menu](media/import-package.png)  

* Wybierz **ProjectAcoustics.unitypackage**

Jeśli importujesz wtyczki do istniejącego projektu, projekt może już istnieć **mcs.rsp** plik w folderze głównym projektu, który określa opcje kompilatora C#. Należy scalić zawartość tego pliku, plik mcs.rsp, który jest dostarczany za pomocą wtyczki Akustyka projektu.

## <a name="enable-the-plugin"></a>Włączanie wtyczki
Tworzenie części toolkit Akustyka wymaga wersji programu .NET 4.x skryptów środowiska uruchomieniowego. Importowanie pakietu zaktualizuje ustawienia odtwarzacza aparatu Unity. Ponowne uruchomienie aparatu Unity dla tego ustawienia zaczęły obowiązywać.

![Zrzut ekranu Unity Player ustawienia panelu](media/player-settings.png)

![Zrzut ekranu Unity ustawienia odtwarzacza panel .NET 4.5 wybrane](media/net45.png)

## <a name="set-up-audio-dsp"></a>Konfigurowanie audio DSP
Akustyka projektu obejmuje audio środowiska uruchomieniowego DSP, która integruje się w ramach spatializer audio aparatu Unity. Obejmuje ona zarówno na podstawie HRTF i przesuwania spatialization. Włącz DSP Akustyka projektu, otwierając ustawienia audio Unity za pomocą **Edytuj > Ustawienia projektu > Audio**, a następnie wybierając pozycję **Akustyka projektu** jako **wtyczki Spatializer** dla Twojego projektu. Upewnij się, że **rozmiar buforu DSP** jest ustawiona na najlepszej wydajności.

![Zrzut ekranu Unity ustawienia projektu panelu](media/project-settings.png)  

![Zrzut ekranu Spatializer Unity panel ustawień za pomocą projektu Akustyka spatializer wybrane](media/choose-spatializer.png)

Następnie otwórz Mixer Audio (**okna > Mixer Audio**). Upewnij się, że masz co najmniej jeden Mixer z jednej grupy. Jeśli nie kliknij przycisk "+", aby po prawej stronie **mieszarki**. Kliknij prawym przyciskiem myszy w dolnej części paska kanału, w sekcji skutki, a następnie dodaj **Mixer Akustyka projektu** efekt. Należy zauważyć, że tylko jeden projekt Akustyka Mixer jest obsługiwana w danym momencie.

![Zrzut ekranu z aparatu Unity Audio Mixer hostingu mixer Akustyka projektu](media/audio-mixer.png)

## <a name="enable-acoustics-on-sound-sources"></a>Włącz Akustyka na źródeł dźwięku
Utwórz źródła audio. Kliknij pole wyboru w dolnej części panelu Inspektor AudioSource informujący, że **Spatialize**. Upewnij się, że **przestrzenne programu Blend** jest ustawiona na pełne 3D.  

![Zrzut ekranu Unity źródła Audio panelu](media/audio-source.png)

## <a name="enable-acoustic-design"></a>Włącz akustyczny projekt
Dołącz skrypt **AcousticsAdjust** do źródła dźwięku w sceny w taki sposób, aby włączyć dodatkowe źródła projektu parametrów, klikając **Dodaj składnik** i wybierając pozycję **Skrypty > Dostosuj Akustyka** :

![Zrzut ekranu AcousticsAdjust Unity skryptu](media/acoustics-adjust.png)

## <a name="next-steps"></a>Kolejne kroki
* [Tworzenie sceny z Akustyka projektu dla aparatu Unity](unity-baking.md)
* [Tworzenie konta usługi Azure Batch](create-azure-account.md) wprowadzić sceny w chmurze
* Zapoznaj się z [procesu projektowania Unity Akustyka projektu](unity-workflow.md).

