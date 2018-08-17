---
title: Wprowadzenie do projektu Akustyka — Cognitive Services
description: Ten przewodnik Szybki Start będzie pokazują, jak zintegrować wtyczkę w swoim projekcie aparatu Unity, tworzenie sceny i dotyczą Akustyka źródeł dźwięku.
services: cognitive-services
author: kegodin
manager: noelc
ms.service: cognitive-services
ms.component: acoustics
ms.topic: article
ms.date: 08/17/2018
ms.author: kegodin
ms.openlocfilehash: b80543b199d4b766c1a8800d2dff4cf5ed81f8cc
ms.sourcegitcommit: 1aedb52f221fb2a6e7ad0b0930b4c74db354a569
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/17/2018
ms.locfileid: "40181662"
---
# <a name="getting-started-with-project-acoustics"></a>Wprowadzenie do Akustyka projektu
Ten przewodnik Szybki Start będzie pokazują, jak zintegrować wtyczkę w swoim projekcie aparatu Unity, tworzenie sceny i dotyczą Akustyka źródeł dźwięku. W tym przewodniku Szybki Start, musisz najpierw utworzyć [konta usługi Azure batch](create-azure-account.md). W tym przewodniku założono pewną znajomość środowiska Unity.

## <a name="download-the-plugin"></a>Pobierz wtyczkę
Zarejestruj [tutaj](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRwMoAEhDCLJNqtVIPwQN6rpUOFRZREJRR0NIQllDOTQ1U0JMNVc4OFNFSy4u) do dołączenia do projektanta w wersji zapoznawczej.

## <a name="supported-platforms-for-quickstart"></a>Obsługiwane platformy pod kątem szybkiego startu
* [Unity 2018.2 +](http://www.unity3d.com)
  * Wymaga ustawienia projektu **.NET 4.x odpowiednik** skryptów wersji środowiska uruchomieniowego 
  * Wymaga edytora aparatu Unity z systemem Windows

## <a name="import-the-plugin"></a>Importuj wtyczki
Importuj Akustyka UnityPackage do projektu. 
* Na platformie Unity, przejdź do **zasoby > Importuj pakiet > Pakiet niestandardowy...**

![Importowanie pakietu](media/ImportPackage.png)  

* Wybierz **MicrosoftAcoustics.unitypackage**

Jeśli importujesz wtyczki do istniejącego projektu, projekt może już istnieć **mcs.rsp** plik w folderze głównym projektu, który określa opcje kompilatora C#. Należy scalić zawartość tego pliku, plik mcs.rsp, który jest dostarczany za pomocą wtyczki Akustyka projektu.

## <a name="enable-the-plugin"></a>Włączanie wtyczki
Tworzenie części toolkit Akustyka wymaga wersji programu .NET 4.x skryptów środowiska uruchomieniowego. Importowanie pakietu zaktualizuje ustawienia odtwarzacza aparatu Unity. Ponowne uruchomienie aparatu Unity dla tego ustawienia zaczęły obowiązywać.

![Ustawienia odtwarzacza](media/PlayerSettings.png)

![.NET 4.5](media/Net45.png)

## <a name="create-a-navigation-mesh"></a>Tworzenie siatki nawigacji
Użyj standardowych [przepływu pracy środowiska Unity](https://docs.unity3d.com/Manual/nav-BuildingNavMesh.html) utworzyć siatkę nawigacji dla Twojego projektu. Aby uzyskać informacje dotyczące sposobu używania własnego nawigacyjne siatki, zobacz [Tworzenie interfejsu użytkownika, przeprowadzenie](bake-ui-walkthrough.md).

## <a name="mark-static-meshes-for-acoustics"></a>Oznacz siatek statycznych dla Akustyka
Wyświetlenie przy użyciu okna Akustyka **okna > Akustyka** na platformie Unity. To okno może być zadokowane obok Inspektor.

![Otwórz Akustyka okna](media/WindowAcoustics.png)

W oknie hierarchii mechanizmu Unity Usuń zaznaczenie pola wyboru wszystkie wybrane elementy. W Akustyka **obiektu** kartę, kliknij pole wyboru "Akustyka geometrii", aby oznaczyć wszystkie siatki i terenach w sceny jako Akustyka geometrii.

Na **materiałów** kartę, przypisz akustyczny materiałów do materiałów sceny. **Domyślne** materiału jest odpowiednikiem konkretny absorpcji. Aby uzyskać więcej informacji na temat określania właściwości materiałów, zobacz [strony procesu projektowania](design-process.md).

![Karta materiały](media/MaterialsTab.png)

## <a name="preview-the-probes"></a>Sondy w wersji zapoznawczej
Na **sondy** kliknij pozycję **Calculate**. To obliczenie może potrwać kilka minut w zależności od rozmiaru sceny. Po zakończeniu obliczania zobaczysz przestawne obszary, które oznaczania miejsc, w symulacji Akustyka o nazwie "sondy punktów" w widoku sceny. Jeśli pojawi się wystarczająco blisko obiektu w oknie sceny, również spowoduje voxelization sceny. Zielony voxels powinny wyrównać obiekty, które zostały oznaczone jako geometrii. Sonda punktów i wyświetla voxel można przełączać na elementy Gizmo menu w prawym górnym rogu widoku sceny.

![GizmosPreview](media/BakePreviewWithGizmos.png)

## <a name="bake-the-scene"></a>Tworzenie sceny
W **tworzenie** kartę, wprowadź swoje poświadczenia platformy Azure i kliknij przycisk **tworzenie**. Jeśli nie masz konta usługi Azure Batch, zobacz [w tym przewodniku dotyczącym naszych konfiguracja zalecana konta](create-azure-account.md).
Po zakończeniu Tworzenie pliku danych zostaną automatycznie pobrane na **zasobów/AcousticsData** katalog w projekcie.

## <a name="set-up-audio-runtime-dsp"></a>Konfigurowanie obsługi audio DSP
Firma Microsoft osadzić audio środowisko uruchomieniowe DSP dla Akustyka w ramach spatializer mechanizmu Unity i można go zintegrować z systemem HRTF spatialization. Aby włączyć przetwarzanie Akustyka, przełącz się do **Acoustics Microsoft** spatializer, przechodząc do **Edytuj > Ustawienia projektu > Audio**i wybierz **Acoustics Microsoft** jako **wtyczki Spatializer** dla Twojego projektu. Upewnij się również, **rozmiar buforu DSP** jest ustawiona na najlepszej wydajności.

![Ustawienia projektu](media/ProjectSettings.png)  

![Spatializer Akustyka projektu](media/ChooseSpatializer.png)

Otwórz Mixer Audio (**okna > Audio Mixer**). Upewnij się, że masz co najmniej jeden Mixer z jednej grupy. Jeśli nie kliknij przycisk "+", aby po prawej stronie **mieszarki**. Kliknij prawym przyciskiem myszy w dolnej części paska kanału, w sekcji skutki, a następnie dodaj **Mixer Akustyka Microsoft** efekt. Należy zauważyć, że tylko jeden projekt Akustyka Mixer jest obsługiwana w danym momencie.

![Audio Mixer](media/AudioMixer.png)

## <a name="set-up-the-acoustics-lookup-table"></a>Konfigurowanie Akustyka tabeli odnośników
Przeciąganie i upuszczanie **Acoustics Microsoft** prefab z panelu Projekt do sceny:

![Prefab Akustyka](media/AcousticsPrefab.png)

Kliknij pozycję **ProjectAcoustics** obiektu gry, a następnie przejść do jego panelu Inspektor. Określ lokalizację zawartości tworzenie wyników ("". ACE pliku w **zasobów/AcousticsData**) przez przeciąganie i upuszczanie go w skrypcie Menedżera Akustyka lub przez kliknięcie przycisku kółko obok pola tekstowego.

![Menedżer Akustyka](media/AcousticsManager.png)  

## <a name="apply-acoustics-to-sound-sources"></a>Dotyczą Akustyka źródeł dźwięku
Utwórz źródła audio. Kliknij pole wyboru w dolnej części panelu Inspektor AudioSource informujący, że **Spatialize**. Upewnij się, że **przestrzenne programu Blend** jest ustawiona na pełne 3D.  

![Źródło audio](media/AudioSource.png)

## <a name="apply-post-bake-design"></a>Stosowanie po Tworzenie projektu
Możesz dołączyć skrypt **AcousticsSourceCustomization** do źródła dźwięku w sceny w taki sposób, aby włączyć dodatkowe źródła projektu parametrów, klikając **Dodaj składnik** i wybierając pozycję **skryptów > Akustyka źródła dostosowywania**:

![Dostosowywanie źródła](media/SourceCustomization.png)

Dostępne są także parametrów na **Mixer Akustyka Microsoft**. Aby uzyskać więcej informacji na temat projektowania po tworzenie zobacz [projektowania parametry](design-process.md).

## <a name="next-steps"></a>Kolejne kroki
* Spróbuj [sceny próbki](sample-walkthrough.md)
* Więcej informacji na temat pełnego zestawu [Tworzenie funkcji](bake-ui-walkthrough.md)
* Zapoznaj się z bardziej szczegółowe [projektowania parametrów](design-process.md)

