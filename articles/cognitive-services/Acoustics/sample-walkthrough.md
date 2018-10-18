---
title: 'Przykład: akustyka projektu'
titlesuffix: Azure Cognitive Services
description: Ten przewodnik opisuje przykładową scenę Unity na potrzeby akustyki projektu, wraz z wdrożeniem na komputery i VR.
services: cognitive-services
author: kegodin
manager: cgronlun
ms.service: cognitive-services
ms.component: acoustics
ms.topic: sample
ms.date: 08/17/2018
ms.author: kegodin
ms.openlocfilehash: f5ea565e68579dfad601d1037daeb4113e3daa43
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/10/2018
ms.locfileid: "48901162"
---
# <a name="unity-sample-walkthrough"></a>Przewodnik po przykładzie Unity
Jest to przewodnik po przykładzie Akustyka projektu. Aby uzyskać więcej informacji o tym, czym jest akustyka projektu, zobacz [Wprowadzenie do akustyki projektu](what-is-acoustics.md). Aby uzyskać pomoc w dodawaniu pakietu akustyki projektu do już istniejącego projektu środowiska Unity, użyj [przewodnika Wprowadzenie](getting-started.md).

## <a name="requirements-for-running-the-sample-project"></a>Wymagania dotyczące uruchamiania przykładowego projektu
* Unity 2018.2+, z wersją środowiska uruchomieniowego skryptów .NET 4.x
* Unity Editor — wersja 64-bitowa dla systemu Windows
* Przykładowe rozwiązanie obsługuje komputery z systemem Windows, platformę uniwersalną systemu Windows oraz obiekty docelowe z systemem Android, w tym wyświetlacze mocowane na głowie (HMD).
* Subskrypcja Azure Batch jest wymagana na potrzeby procesu tworzenia

## <a name="sample-project-setup"></a>Konfiguracja przykładowego projektu
Pobierz i zaimportuj pakiet **MicrosoftAcoustics.Sample.unitypackage**. Po zaimportowaniu ustawienia projektu obejmujące wtyczkę **Spatializer** oraz opcję **Wersja środowiska uruchomieniowego skryptów** są aktualizowane, aby spełniać wymagania wtyczki. Po zakończeniu operacji zobaczysz błąd w konsoli środowiska Unity pochodzący z pliku **AcousticsGeometry.cs** i dotyczący zmiany wersji środowiska uruchomieniowego skryptów na **odpowiednik .NET 4.x**. Ta zmiana ustawień jest wykonywana w ramach importu pakietu, ale wymaga ponownego uruchomienia środowiska Unity w celu zastosowania. Uruchom ponownie środowisko Unity.

## <a name="running-the-sample"></a>Uruchamianie przykładowej aplikacji
Przykład obejmuje scenę pokazową **Assets/AcousticsDemo/ProjectAcousticsDemo.unity**. Ta scena zawiera trzy źródła dźwięku. Domyślnie tylko jedno źródło dźwięku jest odtwarzane, a dwa pozostałe są wstrzymane. Znajdują się one w obszarze **Źródła dźwięku** w **hierarchii**. Aby ułatwić tworzenie ogólnego skryptu nawigacji, główna kamera jest elementem podrzędnym obiektu CameraHolder. 

![Widok hierarchii](media/SampleHierarchyView.png)

Scena została już utworzona i ma plik ACE skojarzony z obiektem **MicrosoftAcoustics** w **hierarchii**. 

Posłuchaj brzmienia sceny, klikając przycisk odtwarzania w edytorze Unity. Na komputerze użyj klawiszy W, A, S, D oraz myszy, aby się poruszać. Aby porównać, jak brzmi scena z akustyką i bez niej, naciśnij przycisk **R** do momentu, w którym tekst nakładki zmieni się na czerwony i będzie wyświetlany komunikat „Akustyka: wyłączona”. Aby wyświetlić skróty klawiaturowe zapewniające większą liczbę kontrolek, naciśnij klawisz **F1**. Wszystkich kontrolek można również użyć poprzez kliknięcie prawym przyciskiem myszy akcji do wykonania, a następnie kliknięcie lewym przyciskiem myszy w celu wykonania akcji.

## <a name="targeting-other-platforms"></a>Określanie celu na innych platformach
Przykład zawiera ustawienia do uruchamiania na następujących platformach: komputery z systemem Windows, platforma uniwersalna systemu Windows, rzeczywistość mieszana systemu Windows, urządzenia z systemem Android oraz Oculus Go. Domyślnie projekt został skonfigurowany pod kątem komputerów z systemem Windows Desktop. Aby użyć platformy VR, przejdź do ustawień odtwarzacza (**Edytuj > Ustawienia projektu > Odtwarzacz**), znajdź pozycję **Ustawienia XR**, a następnie zaznacz pole wyboru **Obsługiwana rzeczywistość wirtualna**.

![Włączanie VR](media/VRSupport.png)  

Podłącz zestaw VR do komputera. Przejdź do pozycji **Plik > Ustawienia kompilacji**, a następnie kliknij opcję **Skompiluj i uruchom**, aby wdrożyć przykład do zestawu VR. Nawiguj w scenie przy użyciu kontrolerów ruchu dla zestawu lub spróbuj użyć klawiszy W, A, S, D na klawiaturze.    
Aby użyć systemu Android i Oculus Go, wybierz opcję Android w menu **Ustawienia kompilacji**. Kliknij przycisk **Przełącz cel**, a następnie kliknij opcję **Skompiluj i uruchom**. Spowoduje to wdrożenie przykładowej sceny do podłączonego urządzenia z systemem Android. Aby uzyskać więcej informacji o wdrażaniu środowiska Unity dla systemu Android, zobacz [dokumentację aparatu Unity](https://docs.unity3d.com/Manual/android-GettingStarted.html).

![Nakierowywanie na system Android](media/TargetAndroid.png)  

## <a name="next-steps"></a>Następne kroki
* [Utwórz konto platformy Azure](create-azure-account.md) dla własnych przypadków tworzenia
* Eksploruj [proces projektowania](design-process.md)

