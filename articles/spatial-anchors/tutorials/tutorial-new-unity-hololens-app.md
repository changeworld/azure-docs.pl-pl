---
title: Samouczek — instrukcje krok po kroku do tworzenia nowej aplikacji HoloLens Unity przy użyciu Azure przestrzenne kotwic | Dokumentacja firmy Microsoft
description: W tym samouczku dowiesz się, jak utworzyć nową aplikację HoloLens Unity za pomocą kotwic przestrzenne Azure.
author: julianparismorgan
manager: vriveras
services: azure-spatial-anchors
ms.author: pmorgan
ms.date: 05/14/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: c831e8fdacf5103619374605dd980ab1f6735047
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2019
ms.locfileid: "67135299"
---
# <a name="tutorial-step-by-step-instructions-to-create-a-new-hololens-unity-app-using-azure-spatial-anchors"></a>Samouczek: Szczegółowe instrukcje dotyczące tworzenia nowej aplikacji HoloLens Unity przy użyciu kotwic przestrzenne platformy Azure

Ten samouczek przedstawia sposób tworzenia nowej aplikacji platformy HoloLens Unity kotwic przestrzenne platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć kroki tego samouczka, upewnij się, że dysponujesz następującymi elementami:

1. Maszyna Windows z <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2017 +</a> zainstalowane z **programowania na platformę uniwersalną Windows** obciążenia i **zestawu Windows 10 SDK (10.0.17763.0 lub nowszej)** składnik, a <a href="https://git-scm.com/download/win" target="_blank">Git dla Windows</a>.
2. Powinno być zainstalowane rozszerzenie [C++/WinRT Visual Studio Extension (VSIX)](https://aka.ms/cppwinrt/vsix) dla programu Visual Studio z usługi [Visual Studio Marketplace](https://marketplace.visualstudio.com/).
3. Urządzenie HoloLens z włączonym [trybem dewelopera](https://docs.microsoft.com/windows/mixed-reality/using-visual-studio). Ten artykuł wymaga urządzenia HoloLens z [aktualizacją systemu Windows z 10 października 2018 r.](https://docs.microsoft.com/windows/mixed-reality/release-notes-october-2018 ) (znaną także jako RS5). Aby wykonać aktualizację do najnowszej wersji na urządzeniu HoloLens, otwórz aplikację **Ustawienia**, przejdź do pozycji **Aktualizacja i zabezpieczenia**, a następnie wybierz przycisk **Sprawdź dostępność aktualizacji**.

## <a name="getting-started"></a>Wprowadzenie

Najpierw skonfigurujemy naszych projektów i sceny aparatu Unity:
1. Rozpocznij aparatu Unity.
2. Wybierz pozycję **Nowy**.
4. Upewnij się, **3D** jest zaznaczone.
5. Nazwij swój projekt, a następnie wprowadź zapisywania **lokalizacji**.
6. Kliknij przycisk **Tworzenie projektu**.
7. Zapisz sceny domyślnym pustym nowego pliku za pomocą: **Plik** > **Zapisz jako**.
8. Nazwij nową scenę **Main** i naciśnij klawisz **Zapisz** przycisku.

**Konfigurowanie ustawień projektu**

Teraz ustawimy niektóre ustawienia projektu środowiska Unity, pomagających nam docelowej systemu Windows Holographic zestawu SDK do tworzenia aplikacji. 

Po pierwsze umożliwia konfigurowanie ustawień jakości dla naszej aplikacji. 
1. Wybierz **Edytuj** > **ustawienia projektu** > **jakości**
2. W kolumnie, **Windows Store** logo, kliknąć strzałkę w **domyślne** wiersza, a następnie wybierz **bardzo niski**. Będziesz wiedzieć, ustawienie zostanie zastosowane poprawnie po, w tym polu **Windows Store** kolumny i **bardzo niski** wiersz jest zielony.

Należy umożliwić wiedzą, że aplikację, którą próbujemy eksportu należy utworzyć immersyjnych widoku, zamiast widoku 2D aparatu Unity. Utworzymy immersyjnych widoku, należy włączyć obsługę rzeczywistość wirtualna na przeznaczonych dla zestawu Windows 10 SDK platformy Unity.

1. Przejdź do **Edytuj** > **ustawienia projektu** > **Player**.
2. W **panelu Inspektor** dla **ustawienia odtwarzacza**, wybierz opcję **Windows Store** ikony.
3. Rozwiń **ustawienia XR** grupy.
4. W **renderowania** sekcji wyboru **obsługiwane rzeczywistości wirtualnej** pole wyboru, aby dodać nowy **wirtualnego SDK mogą stać się rzeczywistością** listy.
5. Upewnij się, że **Windows Mixed Reality** pojawia się na liście. Jeśli nie, wybierz **+** znajdujący się w dolnej części listy i wybierz **Windows Mixed Reality**.
 
> [!NOTE]
> Jeśli nie widzisz ikonę Windows Store, sprawdź, aby upewnić się, że wybrano Windows Store zaplecza platformy .NET skryptów przed instalacją. W przeciwnym razie może być konieczne ponowne zainstalowanie środowiska Unity poprawną instalację Windows.

**Weryfikowanie konfiguracji platformy .NET**
1. Przejdź do **Edytuj** > **ustawienia projektu** > **Player** (nadal obowiązują **Player** open od poprzedniego krok).
2. W **panelu Inspektor** dla **ustawienia odtwarzacza**, wybierz opcję **Windows Store** ikony.
3. W **inne ustawienia** konfiguracji sekcji, upewnij się, że **skryptów zaplecza** ustawiono **.NET**.

**zestaw możliwości**
1. Przejdź do **Edytuj** > **ustawienia projektu** > **Player** (nadal obowiązują **Player** open od poprzedniego krok).
2. W **panelu Inspektor** dla **ustawienia odtwarzacza**, wybierz opcję **Windows Store** ikony.
3. W **ustawień publikowania** sekcji konfiguracji, sprawdź **InternetClientServer** i **SpatialPerception**.

**Konfigurowanie głównego wirtualną kamerę**
1. W **panelu hierarchii**, wybierz opcję **aparatu Main**.
2. W **Inspektor**, równa pozycji przekształcenie **0,0,0**.
3. Znajdź **Wyczyść flagi** właściwości i zmienić listę rozwijaną z **Skybox** do **jednolitego koloru**.
4. Kliknij pozycję **tła** pole, aby otworzyć selektor kolorów.
5. Ustaw **języka R, G, B i A** do **0**.
6. Wybierz **Dodaj składnik** i wyszukaj **przestrzenne Collider mapowanie**.

**Utwórz naszego skryptu**
1. W **projektu** okienko, Utwórz nowy folder **skrypty**w obszarze **zasoby** folderu. 
2. Kliknij prawym przyciskiem myszy folder, a następnie wybierz **Utwórz >** ,  **C# skryptu**. Nadaj jej tytuł **AzureSpatialAnchorsScript**. 
3. Przejdź do **elementy GameObject** -> **Utwórz puste**. 
4. Zaznacz go, a następnie w **Inspektor** zmienić go z **elementy GameObject** do **MixedRealityCloud**. Wybierz **Dodaj składnik** i wyszukania i dodania **AzureSpatialAnchorsScript**.

## <a name="trying-it-out"></a>Próba użycia zasobu
Do przetestowania, czy wszystko działa, Utwórz aplikację **Unity** i wdrożyć ją z **programu Visual Studio**. Postępuj zgodnie z rozdział 6 z [ **podstawy MR 100: Wprowadzenie do aparatu Unity** kurs](https://docs.microsoft.com/windows/mixed-reality/holograms-100#chapter-6---build-and-deploy-to-device-from-visual-studio) Aby to zrobić. Powinien zostać wyświetlony Unity start ekranu, a następnie wyczyść ekran.

## <a name="place-an-object-in-the-real-world"></a>Umieść obiekt w rzeczywistych warunkach
Umożliwia tworzenie i umieść obiekt przy użyciu aplikacji. Otwórz rozwiązanie programu Visual Studio, które utworzyliśmy, gdy firma Microsoft [wdrożonych aplikacji w języku](#trying-it-out). 

Najpierw dodaj następujące instrukcje importu do Twojej `Assembly-CSharp (Universal Windows)\Scripts\AzureSpatialAnchorsScript.cs`:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=19-24)]

Następnie dodaj następujące zmienne składowe do Twojej `AzureSpatialAnchorsScript` klasy: 

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=26-37,43-47,55-74)]

Następnie dodaj następujący kod do Twojego `Start()` metody. Ten kod będzie obsługiwać `GestureRecognizer`, który wykryje po naciśnięcie w powietrzu i wywołanie `HandleTap`.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=76-85,88&highlight=4-10)]

W efekcie powstał Dodaj następujący kod `HandleTap()` poniższej metody `Update()`. Wówczas wykonaj rzutowanie ray i Uzyskaj trafień punktu, w którym można umieścić kuli. 

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=264-274,295-297,301-309)]

Teraz musimy utworzyć kuli. Kula będzie początkowo białe, ale ta wartość zostanie zmieniona później. Dodaj następujący kod `CreateAndSaveSphere()` metody:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=311-324,389)]

Uruchom aplikację z **programu Visual Studio** do sprawdzania poprawności jeszcze raz. Tym razem wybierz ekran, aby umieścić swoje kuli białe na powierzchnię wybranego & Utwórz.

## <a name="set-up-the-dispatcher-pattern"></a>Konfigurowanie wzorzec dyspozytora

Podczas pracy z użyciem aparatu Unity, wszystkie Unity interfejsy API, na przykład interfejsów API służy do aktualizacji interfejsu użytkownika muszą nastąpić w wątku głównym. W kodzie, który będziemy pisać jednak uzyskujemy wywołań zwrotnych na inny wątek. Chcemy aktualizacji interfejsu użytkownika w te wywołania zwrotne, więc potrzebujemy sposób można przejść od wątków strony, do głównego wątku. Aby wykonać kod w głównym wątku z wątków strony, użyjemy wzorca dyspozytora. 

Dodajmy zmienną członkowską dispatchQueue, czyli kolejki akcji. Firma Microsoft będzie przekazania akcji kolejki, a następnie usuwania z kolejki i uruchamiania działań w wątku głównym. 

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=33-46&highlight=6-9)]

Następnie Dodajmy sposób, aby dodać akcję do kolejki. Dodaj `QueueOnUpdate()` od razu po `Update()` :

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=102-112)]

Przejdźmy teraz Użyj pętli Update(), aby sprawdzić, czy ma akcji w kolejce. Jeśli tak, mamy kolejki akcji i uruchom go.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=90-100&highlight=4-10)]

## <a name="get-the-azure-spatial-anchors-sdk"></a>Pobierz Azure kotwic przestrzennego zestawu SDK

Firma Microsoft będzie teraz pobrać przestrzenne kotwic zestawu SDK usługi Azure. Przejdź do [Azure przestrzenne kotwice w witrynie GitHub strony z wersjami](https://github.com/Azure/azure-spatial-anchors-samples/releases). W obszarze zasobów, należy pobrać **AzureSpatialAnchors.unitypackage** pliku. 

Na platformie Unity, przejdź do **zasoby**, kliknij przycisk **Importuj pakiet** > **pakiet niestandardowy...** . Przejdź do pakietu, a następnie wybierz **Otwórz**.

W nowym **Importowanie pakietu Unity** POP, wybierz okno **Brak** w lewym dolnym rogu. Następnie w obszarze **AzureSpatialAnchorsPlugin** > **wtyczek**, wybierz opcję **typowe**, **edytora**, i  **HoloLens**. Kliknij przycisk **importu** w prawym dolnym rogu.

Musimy teraz Przywracanie pakietów Nuget w celu pobrania zestawu SDK usługi Azure przestrzenne zakotwiczenia. Opracowano na podstawie **Unity** a następnie otwórz i kompilacji, wynikowy **programu Visual Studio** rozwiązanie ponownie, jak wyjaśniono w [eksperymentom](#trying-it-out). 

W swojej **programu Visual Studio** rozwiązania, Dodaj następujący import do usługi `<ProjectName>\Assets\Scripts\AzureSpatialAnchorsScript.cs`:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=18-21&highlight=1)]

Następnie dodaj następujące zmienne Członkowskie w swojej `AzureSpatialAnchorsScript` klasy:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=43-58&highlight=6-11)]

## <a name="attach-a-local-azure-spatial-anchor-to-the-local-anchor"></a>Dołącz lokalnego zakotwiczenia przestrzenne Azure do lokalnych zakotwiczenia

Skonfiguruj CloudSpatialAnchorSession Azure przestrzenne zakotwiczenia. Rozpoczniemy pracę przez dodanie poniższego `InitializeSession()` metody w ramach Twojej `AzureSpatialAnchorsScript` klasy. Po wywołaniu zapewni sesję kotwic przestrzenne Azure zostanie utworzony i został poprawnie zainicjowany podczas uruchamiania aplikacji.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=169-197,200-204)]

Teraz musisz napisać kod obsługujący wywołania delegata. Dodamy więcej do nich miarę.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=206-221)]

Teraz umożliwia utworzenie punktu zaczepienia usługi `initializeSession()` metody do Twojej `Start()` metody.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=76-88&highlight=12)]

Na koniec Dodaj następujący kod do Twojego `CreateAndSaveSphere()` metody. Wstawi kotwica przestrzenne lokalnej platformy Azure do kuli, który możemy umieszcza się w świecie rzeczywistym.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=311-337,390&highlight=16-31)]

Przed kontynuowaniem pracy, musisz tworzyć kotwic przestrzenne Azure konta identyfikator i klucz, jeśli nie masz jeszcze je. Postępuj zgodnie z następującą sekcję, aby je uzyskać.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="upload-your-local-anchor-into-the-cloud"></a>Przekaż zakotwiczenia lokalnych do chmury

Po utworzeniu swoje konto Azure przestrzenne kotwic identyfikator i klucz go i Wklej `Account Id` do `SpatialAnchorsAccountId` i `Account Key` do `SpatialAnchorsAccountKey`.

Na koniec spróbujmy dołączyć wszystko ze sobą. W swojej `SpawnNewAnchoredObject()` metody, Dodaj następujący kod. Będzie ona wywoływać `CreateAnchorAsync()` metody, jak Twoje Kula jest tworzony. Gdy metoda zwraca kod poniżej wykona jedną finalna wersja aktualizacji update do Twojej Kula, zmieniając jego kolor na niebieski.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=311-389&highlight=28-78)]

Uruchom aplikację z **programu Visual Studio** jeszcze raz. Poruszanie się głową, a następnie powietrzu, aby umieścić w swojej dziedzinie. Gdy będziemy już mieć wystarczającej liczby klatek, kuli zmieni się na żółty, a rozpocznie się przekazywanie chmury. Po zakończeniu przekazywania Twojego kuli spowoduje wyłączenie niebieski. Opcjonalnie można także użyć w oknie danych wyjściowych wewnątrz **programu Visual Studio** do monitorowania Twoja aplikacja wysyła komunikaty dziennika. Będzie można poszukaj zalecanym postępu tworzenia, jak również identyfikatorem kotwicy, zwracające chmury, po zakończeniu przekazywania.

> [!NOTE]
> Jeśli otrzymasz "dllnotfoundexception —: Nie można załadować biblioteki DLL "AzureSpatialAnchors": Nie można odnaleźć określonego modułu. ", należy **czysty** i **kompilacji** ponownie rozwiązanie.

## <a name="locate-your-cloud-spatial-anchor"></a>Znajdź zakotwiczenia przestrzenne chmury

Jeden zakotwiczenia zostanie przekazany do chmury, jesteśmy gotowi próby lokalizowania go ponownie. Możemy dodać następujący kod do Twojego `HandleTap()` metody. Ten kod wykonują następujące czynności:

* Wywołanie `ResetSession()`, które spowoduje zatrzymanie `CloudSpatialAnchorSession` i Usuń naszych istniejących kuli niebieski na ekranie.
* Inicjowanie `CloudSpatialAnchorSession` ponownie. Możemy to zrobić, dzięki czemu możemy się, że zakotwiczenia, którą Spróbujemy znaleźć pochodzi z chmury, zamiast lokalnego zakotwiczenia, którą utworzyliśmy.
* Tworzenie **obserwatora** , będzie szukał zakotwiczenia przekazany do kotwic przestrzenne platformy Azure.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=264-302&highlight=13-31,34-36)]

Teraz Dodajmy naszych `ResetSession()` i `CleanupObjects()` metody. Można je umieścić poniżej `QueueOnUpdate()`

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=114-167)]

Teraz należy dołączyć kod, który zostanie wywołany, gdy znajduje się kontrolnych, które firma Microsoft jest wykonywanie zapytań dotyczących. Wewnątrz `InitializeSession()`, Dodaj następujące wywołania zwrotne:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=195-201&highlight=4-5)]

 
Umożliwia teraz, Dodaj kod, który będzie tworzenie i umieścić zielony kuli po CloudSpatialAnchor znajduje się. Jego można również włączyć ponownie, naciskając pozycję ekranu, dzięki czemu można powtórzyć raz całego scenariusza: tworzenie innego lokalnego zakotwiczenia, przekaż go i ponownie go zlokalizować.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=223-262)]

To wszystko! Uruchom aplikację z **programu Visual Studio** jeszcze jeden raz na wypróbowanie całego scenariusza typu end to end. Poruszanie się urządzenia i umieścić swoje białe kuli. Następnie Zachowaj przenoszenia głową do przechwytywania danych środowiska do momentu kuli zmieni kolor na żółty. Zakotwiczenia lokalnych zostaną przekazane i swojej dziedzinie spowoduje wyłączenie niebieski. Na koniec ponownie naciśnij ekranu tak, aby lokalne zakotwiczenia jest usuwany, a następnie firma Microsoft będzie szukać jego odpowiednika w chmurze. Kontynuuj poruszanie się w urządzeniu aż do zlokalizowania zakotwiczenia przestrzenne chmury. Zielony kuli powinien pojawić się we właściwym miejscu i może przepłukać i powtórz cały scenariusz, ponownie.

[!INCLUDE [AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md)]