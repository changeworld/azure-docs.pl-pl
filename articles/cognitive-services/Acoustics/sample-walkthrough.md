---
title: Przewodnik przykładowej Akustyka projektu — Cognitive Services
description: Ten przewodnik opisuje sceny przykładowe Unity Akustyka projektu, łącznie z wdrożenia pulpitu i VR.
services: cognitive-services
author: kegodin
manager: noelc
ms.service: cognitive-services
ms.component: acoustics
ms.topic: article
ms.date: 08/17/2018
ms.author: kegodin
ms.openlocfilehash: eaf7ff9f7f791fd6d04e6b76d256b4987c50cd13
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/28/2018
ms.locfileid: "47434096"
---
# <a name="unity-sample-walkthrough"></a>Przewodnik po przykładzie aparatu Unity
Jest to przewodnik po przykładowej Akustyka projektu. Aby dowiedzieć się więcej o jakie Akustyka projektu jest, zapoznaj się z [wprowadzenie do projektu Akustyka](what-is-acoustics.md). Aby uzyskać pomoc podczas dodawania pakietu Akustyka projektu do istniejącego projektu środowiska Unity, użyj [Wprowadzenie — przewodnik](getting-started.md).

## <a name="requirements-for-running-the-sample-project"></a>Wymagania dotyczące uruchamiania przykładowego projektu
* Unity 2018.2 +, za pomocą skryptów wersji środowiska uruchomieniowego .NET 4.x
* Unity Editor — wersja 64-bitowa dla systemu Windows
* Przykład obsługuje pulpitu Windows, platformy uniwersalnej systemu Windows i Android obiekty docelowe, łącznie z zainstalowanym head Wyświetla (HMDs)
* Wymagane dla procesu Tworzenie subskrypcji platformy Azure Batch

## <a name="sample-project-setup"></a>Przykładowe ustawienia projektu
Pobierz i zaimportuj **MicrosoftAcoustics.Sample.unitypackage**. Przy imporcie, ustawienia, w tym projektu **Spatializer** i **skryptów wersji środowiska uruchomieniowego** zostały zaktualizowane pod kątem wymagań wtyczki. Po zakończeniu tej operacji zostanie wyświetlony błąd w konsoli programu Unity z **AcousticsGeometry.cs** o zmienianiu wersji środowiska uruchomieniowego skryptów, aby **.NET 4.x odpowiednik**. Zmiany tego ustawienia jest wykonywane w ramach importowania pakietu, ale wymaga ponownego uruchomienia aparatu Unity zaczęły obowiązywać. Ponowne uruchomienie aparatu Unity.

## <a name="running-the-sample"></a>Uruchamianie przykładowej aplikacji
Przykład obejmuje scenę pokaz **Assets/AcousticsDemo/ProjectAcousticsDemo.unity**. Ta sceny ma trzy źródła dźwięku. Domyślnie tylko jedno źródło dźwięk jest odtwarzany, a pozostałe dwa są wstrzymane. Te znajdują się w obszarze **źródła dźwięku** w **hierarchii**. Aby skrypt rodzajowy nawigacji, kamera główny jest element podrzędny obiektu CameraHolder. 

![Widok hierarchii](media/SampleHierarchyView.png)

Sceny ma już rozszerzania i plik ACE skojarzone z **MicrosoftAcoustics** prefab w **hierarchii**. 

Posłuchaj, jak brzmi sceny, klikając przycisk odtwarzania w programie Unity editor. Na komputerze stacjonarnym, należy użyć W, A, S, D i myszy, aby poruszać się. Aby porównać, jak sceny brzmi z lub bez Akustyka, naciśnij klawisz **R** przycisk aż do tekstu nakładki zmieni kolor na czerwony i jest wyświetlany komunikat "Akustyka: wyłączone." Aby wyświetlić skróty klawiaturowe dla większej liczby kontrolek, naciśnij klawisz **F1**. Wszystkie kontrolki są również użyteczny przez kliknięcie prawym przyciskiem myszy, aby wybrać akcję do wykonania, pozostanie kliknięcie do wykonania akcji.

## <a name="targeting-other-platforms"></a>Inne platformy
Przykład zawiera ustawienia do uruchomienia na pulpit Windows, platformy uniwersalnej systemu Windows, Windows Mixed Reality, Android i Oculus z rzeczywistym użyciem. Domyślnie projekt został skonfigurowany dla Windows Desktop. Pod kątem platformy VR, przejdź do ustawienia odtwarzacza (**Edytuj > Ustawienia projektu > Player**), Znajdź **ustawienia XR**i sprawdź **obsługiwane rzeczywistości wirtualnej** pole wyboru.

![Włącz VR](media/VRSupport.png)  

Połącz zestaw słuchawkowy VR do Twojego komputera. Przejdź do **Plik > Ustawienia kompilacji**i kliknij przycisk **kompilowanie i uruchamianie** na wdrażanie przykładu w usłudze usługi zestaw nagłowny rzeczywistości Wirtualnej. Nawigowanie po sceny przy użyciu kontrolerów ruchu dla Twojego słuchawkowy, lub spróbuj użyć W, A, S, D na klawiaturze.    
Przeznaczony dla systemu Android oraz Oculus z rzeczywistym użyciem, wybierz systemu Android z **ustawieniach kompilacji** menu. Kliknij przycisk **Przełącz docelowej**, następnie **kompilowanie i uruchamianie**. Połączone urządzenia z systemem Android spowoduje to wdrożenie sceny próbki. Aby uzyskać informacje o programowaniu dla platformy Unity dla systemu Android, zobacz [dokumentacja aparatu Unity](https://docs.unity3d.com/Manual/android-GettingStarted.html).

![Docelowym systemem Android](media/TargetAndroid.png)  

## <a name="next-steps"></a>Kolejne kroki
* [Utwórz konto platformy Azure](create-azure-account.md) dla własnego tworzony
* Zapoznaj się z [Projektowanie procesu](design-process.md)

