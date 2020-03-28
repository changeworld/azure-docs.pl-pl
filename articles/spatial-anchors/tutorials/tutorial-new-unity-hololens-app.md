---
title: 'Samouczek: Tworzenie nowej aplikacji HoloLens Unity'
description: W tym samouczku dowiesz się, jak utworzyć nową aplikację HoloLens Unity przy użyciu zakotwiczeń przestrzennych platformy Azure.
author: julianparismorgan
manager: vriveras
services: azure-spatial-anchors
ms.author: pmorgan
ms.date: 07/05/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: e1abb759c80e770f1e650c232b6b2e21232b7e6f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75457723"
---
# <a name="tutorial-step-by-step-instructions-to-create-a-new-hololens-unity-app-using-azure-spatial-anchors"></a>Samouczek: Instrukcje krok po kroku dotyczące tworzenia nowej aplikacji HoloLens Unity przy użyciu zakotwiczeń przestrzennych platformy Azure

W tym samouczku pokazano, jak utworzyć nową aplikację HoloLens Unity za pomocą funkcji Azure Spatial Anchors.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć kroki tego samouczka, upewnij się, że dysponujesz następującymi elementami:

1. Komputer z systemem Windows z <a href="https://www.visualstudio.com/downloads/" target="_blank">programem Visual Studio 2017+</a> zainstalowany z obciążeniem **deweloperskim platformy systemu** Windows i składnikiem **SDK systemu Windows 10.0.18362.0 lub nowszym oraz** <a href="https://git-scm.com/download/win" target="_blank">Git dla systemu Windows</a>.
2. Powinno być zainstalowane rozszerzenie [C++/WinRT Visual Studio Extension (VSIX)](https://aka.ms/cppwinrt/vsix) dla programu Visual Studio z usługi [Visual Studio Marketplace](https://marketplace.visualstudio.com/).
3. Urządzenie HoloLens z włączonym [trybem dewelopera](https://docs.microsoft.com/windows/mixed-reality/using-visual-studio). Ten artykuł wymaga urządzenia HoloLens z [aktualizacją systemu Windows z 10 października 2018 r.](https://docs.microsoft.com/windows/mixed-reality/release-notes-october-2018 ) (znaną także jako RS5). Aby wykonać aktualizację do najnowszej wersji na urządzeniu HoloLens, otwórz aplikację **Ustawienia**, przejdź do pozycji **Aktualizacja i zabezpieczenia**, a następnie wybierz przycisk **Sprawdź dostępność aktualizacji**.

## <a name="getting-started"></a>Wprowadzenie

Najpierw utworzymy nasz projekt i scenę Unity:
1. Rozpocznij jedność.
2. Wybierz **pozycję Nowy**.
4. Upewnij się, że wybrano **opcję 3D.**
5. Nadaj nazwę projektowi i wprowadź pozycję zapisz **lokalizację**.
6. Kliknij **pozycję Utwórz projekt**.
7. Zapisz pustą scenę domyślną w nowym pliku za pomocą: **Zapisz plik** > **jako**.
8. Nazwij nową scenę **Main** i naciśnij przycisk **Zapisz.**

**Konfigurowanie ustawień projektu**

Teraz ustawimy niektóre ustawienia projektu Unity, które pomogą nam kierować zestaw holograficzny zestawu Windows SDK do programowania.

Najpierw ustawiamy ustawienia jakości dla naszej aplikacji.
1. Wybieranie **opcji Edytuj** > **jakość** **ustawień** > projektu
2. W kolumnie pod logo **Sklepu Windows** kliknij strzałkę w wierszu **Domyślny** i wybierz pozycję **Bardzo niski**. Zobaczysz, że ustawienie jest stosowane poprawnie, gdy pole w kolumnie **Sklep Windows** i wiersz **Bardzo niski** jest zielony.

Musimy poinformować Unity, że aplikacja, którą próbujemy wyeksportować, powinna utworzyć widok wciągający zamiast widoku 2D. Tworzymy wciągający widok, włączając obsługę wirtualnej rzeczywistości w unity kierowania windows 10 SDK.

1. Przejdź do **pozycji Edytuj** > **odtwarzacz****ustawień** > projektu .
2. W **Panelu Inspektora** **ustawień odtwarzacza**wybierz ikonę **Sklepu Windows.**
3. Rozwiń grupę **Ustawienia XR.**
4. W sekcji **Renderowanie** zaznacz pole wyboru **Obsługiwane przez wirtualną rzeczywistość,** aby dodać nową listę **zestawów SDK rzeczywistości wirtualnej.**
5. Sprawdź, czy na liście jest wyświetlana **rzeczywistość mieszana** systemu Windows. Jeśli nie, **+** wybierz przycisk u dołu listy i wybierz pozycję **Windows Mixed Reality**.

> [!NOTE]
> Jeśli nie widzisz ikony Sklepu Windows, przed instalacją sprawdź dokładnie, czy wybrano wybraną wybraną wybraną wybraną wybraną wybraną wybraną wybraną wybraną wybraną wybraną wybraną wybraną wybraną wybraną wybraną wybraną wyewidenc Jeśli nie, może być konieczne ponowne zainstalowanie unity z poprawną instalacją systemu Windows.

**Weryfikowanie konfiguracji wewnętrznej bazy danych skryptów**
1. Przejdź do **pozycji Edytuj** > **odtwarzacz** **ustawień** > projektu (nadal możesz mieć otwarty **odtwarzacz** z poprzedniego kroku).
2. W **Panelu Inspektora** **ustawień odtwarzacza**wybierz ikonę **Sklepu Windows.**
3. W sekcji **Konfiguracja innych ustawień** upewnij się, że **wewnętrznej bazy danych skryptów** jest ustawiona na **IL2CPP**.

**Ustawianie możliwości**
1. Przejdź do **pozycji Edytuj** > **odtwarzacz** **ustawień** > projektu (nadal możesz mieć otwarty **odtwarzacz** z poprzedniego kroku).
2. W **Panelu Inspektora** **ustawień odtwarzacza**wybierz ikonę **Sklepu Windows.**
3. W sekcji Konfiguracja **ustawień publikowania** zaznacz pole wyboru **InternetClientServer** i **SpatialPerception**.

**Konfigurowanie głównej kamery wirtualnej**
1. W **panelu Hierarchia**wybierz opcję **Aparat główny**.
2. W **inspektorze**ustaw swoją pozycję transformacji na **0,0,0**.
3. Znajdź właściwość **Wyczyść flagi** i zmień pozycję rozwijanej ze **Skybox** na **Solid Color**.
4. Kliknij pole **Tło,** aby otworzyć selektor kolorów.
5. Ustaw **R, G, B i A** na **0**.
6. Wybierz **pozycję Dodaj komponent** i wyszukaj i dodaj **zderzacz map przestrzennych**.

**Stwórz nasz skrypt**
1. W **okienku Projekt** utwórz nowy folder **Skrypty**w folderze **Zasoby.**
2. Kliknij prawym przyciskiem myszy folder, a następnie wybierz polecenie **Utwórz >**, **Skrypt C#.** Title it **AzureSpatialAnchorsScript**.
3. Przejdź do **gameobject** -> **Create Empty**.
4. Wybierz go, a w **Inspektorze** zmień jego nazwę z **GameObject** na **MixedRealityCloud**. Wybierz **pozycję Dodaj składnik** i wyszukaj i dodaj kod **AzureSpatialAnchorsScript**.

**Tworzenie prefabrykatu kuli**
1. Przejdź do **gameobject** -> **3D Object** -> **Sphere**.
2. W **Inspektorze**ustaw swoją skalę na **0,25, 0,25, 0,25**.
3. Znajdź obiekt **Sfera** w okienku **Hierarchia.** Kliknij go i przeciągnij do folderu **Zasoby** w okienku **Projekt.**
4. Kliknij prawym przyciskiem myszy i **usuń** oryginalną sferę utworzoną w okienku **Hierarchia.**

Prefabrykaty sfery powinny być teraz prefabrykować kulę w okienku **programu Project.**

## <a name="trying-it-out"></a>Wypróbowanie go
Aby sprawdzić, czy wszystko działa, skompiluj aplikację w **unity** i wdrożyć ją w **programie Visual Studio.** Postępuj zgodnie z rozdziałem 6 z [ **MR Basics 100: Pierwsze kroki z kursem Unity,** ](https://docs.microsoft.com/windows/mixed-reality/holograms-100#chapter-6---build-and-deploy-to-device-from-visual-studio) aby to zrobić. Powinien zostać wyświetlony ekran startowy Unity, a następnie wyraźny ekran.

## <a name="place-an-object-in-the-real-world"></a>Umieść obiekt w świecie rzeczywistym
Utwórzmy & umieść obiekt za pomocą aplikacji. Otwórz rozwiązanie programu Visual Studio, które utworzyliśmy podczas [wdrażania naszej aplikacji.](#trying-it-out)

Najpierw dodaj do swojego: `Assembly-CSharp (Universal Windows)\Scripts\AzureSpatialAnchorsScript.cs`

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=19-24)]

Następnie dodaj do `AzureSpatialAnchorsScript` klasy następujące zmienne członkowskie:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=26-42,48-52,60-79)]

Zanim będziemy kontynuować, musimy ustawić prefabrykat kuli utworzyliśmy na naszej sferzePrefab zmiennej członkowskiej. Wróć do **Unity**.
1. W **obszarze Jedność**wybierz obiekt **MixedRealityCloud** w okienku **Hierarchia.**
2. Kliknij prefabrykat **kuli** zapisany w okienku **Projekt.** Przeciągnij **sferę** klikniętą do obszaru **Prefabrykowania sfery** w obszarze **Skrypt zakotwiczenia przestrzenne (Skrypt) platformy Azure** w okienku **Inspektor.**

Teraz powinieneś mieć ustawiony **sferę** jako prefabryk w skrypcie. Skompiluj z **Unity,** a następnie otwórz wynikowe rozwiązanie **programu Visual Studio** ponownie, tak jak to miało miejsce w trying it [out.](#trying-it-out)

W **programie Visual** `AzureSpatialAnchorsScript.cs` Studio otwórz się ponownie. Dodaj następujący kod `Start()` do metody. Ten kod zostanie `GestureRecognizer`podłączony, który wykryje, `HandleTap`kiedy jest dotknij powietrza i połączenia .

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=81-90,93&highlight=4-10)]

Teraz musimy dodać poniższą `HandleTap()` `Update()`metodę. Zrobi rzut promienia i uzyskać punkt trafienia, w którym można umieścić kulę.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=267-277,299-300,304-312)]

Teraz musimy stworzyć sferę. Kula będzie początkowo biała, ale ta wartość zostanie później dostosowana. Dodaj następującą `CreateAndSaveSphere()` metodę:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=314-325,390)]

Uruchom aplikację z **programu Visual Studio,** aby ponownie ją zweryfikować. Tym razem dotknij ekranu, aby utworzyć & umieścić białą kulę na wybranej powierzchni.

## <a name="set-up-the-dispatcher-pattern"></a>Konfigurowanie wzorca dyspozytora

Podczas pracy z Unity, wszystkie interfejsy API Unity, na przykład interfejsy API, których używasz do aktualizacji interfejsu użytkownika, muszą się zdarzyć w wątku głównym. W kodzie napiszemy jednak otrzymujemy wywołania zwrotne w innych wątkach. Chcemy zaktualizować interfejs użytkownika w tych wywołaniach zwrotnych, więc musimy sposób, aby przejść z wątku bocznego do wątku głównego. Aby wykonać kod w wątku głównym z wątku bocznego, użyjemy wzorca dyspozytora.

Dodajmy zmienną członkowską, dispatchQueue, która jest kolejką akcji. Będziemy wypychać akcje do kolejki, a następnie odkuwają i uruchamiają akcje w wątku głównym.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=38-51&highlight=6-9)]

Następnie dodajmy sposób dodawania akcji do kolejki. Dodaj `QueueOnUpdate()` zaraz `Update()` po:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=107-117)]

Teraz użyjmy update() pętli, aby sprawdzić, czy istnieje akcja w kolejce. Jeśli tak, będziemy dequeue działania i uruchomić go.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=95-105&highlight=4-10)]

## <a name="get-the-azure-spatial-anchors-sdk"></a>Pobierz zestaw SDK zakotwiczeń przestrzennych platformy Azure

## <a name="via-unity-package"></a>[Pakiet Via Unity](#tab/UnityPackage)

Teraz pobierzemy zestaw SDK zakotwiczenia przestrzenne platformy Azure. Przejdź do [strony Wydania aplikacji Usługi Azure Spatial Anchors GitHub](https://github.com/Azure/azure-spatial-anchors-samples/releases). W obszarze Zasoby pobierz **pakiet AzureSpatialAnchors.unitypackage**. W ujamienie przejdź do **strony Zasoby**, kliknij przycisk **Importuj pakiet** > **niestandardowy...**. Przejdź do pakietu i wybierz pozycję **Otwórz**.

W nowym oknie **Pakietu importu unity,** które się pojawia, usuń zaznaczenie **wtyczek,** a następnie kliknij przycisk **Importuj** w prawym dolnym rogu.

Teraz musimy przywrócić pakiety Nuget, aby uzyskać zestaw SDK zakotwiczenia przestrzenne platformy Azure. Skompiluj z **Unity,** a następnie otwórz i skompiluj wynikowe rozwiązanie **programu Visual Studio** ponownie, jak opisano w [wypróbować.](#trying-it-out)

## <a name="via-nugetforunity"></a>[Przez NuGetForUnity](#tab/NuGetForUnity)

Najpierw musimy zainstalować NuGetForUnity. Przejdź do [strony Wydania NuGetForUnity GitHub](https://github.com/GlitchEnzo/NuGetForUnity/releases). W obszarze Zasoby pobierz pakiet **Lastest NuGetForUnity.unitypackage**. W ujamienie przejdź do **strony Zasoby**, kliknij przycisk **Importuj pakiet** > **niestandardowy...**. Przejdź do pakietu i wybierz pozycję **Otwórz**. Unity zainstaluje teraz NugetForUnity. Jeśli nie widzisz nowej listy rozwijanej **NuGet** w unity, może być konieczne kliknięcie prawym przyciskiem myszy w obszarze**Zasoby** **projektów** > . Następnie wybierz pozycję **Ponownie zaimportuj wszystkie**.

Po zainstalowaniu nugetforunity wybierz **nuget** > **zarządzaj pakietami NuGet**. Następnie wyszukaj pozycję Microsoft.Azure.SpatialAnchors.Unity i wybierz pozycję **Zainstaluj**.

Teraz musimy utworzyć, aby uzyskać rzeczywiste Azure Spatial Anchors SDK, jak pakiet NuGet właśnie pobrane tylko zawiera skrypty pomocnicze. Skompiluj z **Unity,** a następnie otwórz i skompiluj wynikowe rozwiązanie **programu Visual Studio** ponownie, jak opisano w [wypróbować.](#trying-it-out)

---

W rozwiązaniu **programu Visual Studio** dodaj `<ProjectName>\Assets\Scripts\AzureSpatialAnchorsScript.cs`do swojego rozwiązania:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=18-21&highlight=1)]

Następnie dodaj do `AzureSpatialAnchorsScript` swojej klasy następujące zmienne członkowskie:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=48-63&highlight=6-11)]

## <a name="attach-a-local-azure-spatial-anchor-to-the-local-anchor"></a>Dołączanie lokalnej kotwicy przestrzennej platformy Azure do lokalnej kotwicy

Skonfigurujmy usługę Azure Spatial Anchor's CloudSpatialAnchorSession. Zaczniemy od dodania `InitializeSession()` następującej `AzureSpatialAnchorsScript` metody wewnątrz klasy. Po wywołaniu zapewni sesji Azure Spatial Anchors jest tworzony i poprawnie zainicjowane podczas uruchamiania aplikacji.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=174-202,205-209)]

Teraz musimy napisać kod do obsługi wywołań delegata. Dodamy do nich więcej w miarę kontynuowania.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=211-226)]

Teraz podłączmy metodę `initializeSession()` do metody. `Start()`

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=81-93&highlight=12)]

Na koniec dodaj następujący kod `CreateAndSaveSphere()` do metody. Dołączy lokalną kotwicę przestrzenną platformy Azure do sfery, którą umieszczamy w świecie rzeczywistym.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=314-338,390&highlight=14-25)]

Przed kontynuowaniem dalszych, należy utworzyć identyfikator konta zakotwiczenia przestrzennego platformy Azure i klucz, jeśli jeszcze ich nie masz. Postępuj zgodnie z poniższą sekcją, aby je uzyskać.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="upload-your-local-anchor-into-the-cloud"></a>Przesyłanie lokalnej kotwicy do chmury

Po uzyskaniu identyfikatora i klucza konta zakotwiczenia `Account Id` przestrzennego `SpatialAnchorsAccountId` `Account Key` platformy `SpatialAnchorsAccountKey`Azure, wklej je do i do .

Na koniec połączmy wszystko. W `SpawnNewAnchoredObject()` metodzie dodaj następujący kod. Wywoła metodę, `CreateAnchorAsync()` gdy tylko zostanie utworzona twoja kula. Po powrocie metody poniższy kod wykona jedną ostateczną aktualizację kuli, zmieniając jej kolor na niebieski.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=314-391&highlight=26-77)]

Uruchom aplikację z **programu Visual Studio** jeszcze raz. Poruszaj się po głowie, a następnie dotknij powietrzem, aby umieścić kulę. Gdy mamy wystarczająco dużo klatek, kula zmieni się na żółtą, a rozpocznie się przesyłanie w chmurze. Po zakończeniu przesyłania kula zmieni kolor na niebieski. Opcjonalnie można również użyć okna dane wyjściowe wewnątrz **programu Visual Studio** do monitorowania komunikatów dziennika, które wysyła aplikacja. Po zakończeniu przekazywania będzie można obserwować zalecane do tworzenia postępu, a także identyfikator zakotwiczenia, który chmura zwraca.

> [!NOTE]
> Jeśli pojawi się "DllNotFoundException: Nie można załadować biblioteki DLL "AzureSpatialAnchors": Nie można odnaleźć określonego modułu.", należy **ponownie wyczyścić** i **skompilować** rozwiązanie.

## <a name="locate-your-cloud-spatial-anchor"></a>Lokalizowanie kotwicy przestrzennej chmury

Jedna kotwica jest przesyłana do chmury, jesteśmy gotowi, aby spróbować zlokalizować go ponownie. Dodajmy następujący kod do `HandleTap()` metody. Ten kod będzie:

* Zadzwoń `ResetSession()`, który `CloudSpatialAnchorSession` zatrzyma i usunąć naszą istniejącą niebieską kulę z ekranu.
* Inicjuj `CloudSpatialAnchorSession` ponownie. Robimy to, więc jesteśmy pewni, że kotwica, którą zamierzamy zlokalizować, pochodzi z chmury, a nie z lokalnej kotwicy, którą stworzyliśmy.
* Utwórz **czujkę,** która będzie szukać kotwicy, którą przekażemy do zakotwiczeń przestrzennych platformy Azure.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=267-305&highlight=13-31,35-36)]

Dodajmy nasze `ResetSession()` i `CleanupObjects()` metody. Możesz umieścić je poniżej`QueueOnUpdate()`

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=119-172)]

Teraz musimy podłączyć kod, który zostanie wywołany, gdy znajduje się kotwica, dla której prowadzi zapytanie. Wewnątrz `InitializeSession()`, dodaj następujące wywołania zwrotne:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=200-206&highlight=4-5)]


Teraz pozwala dodać kod, który utworzy & umieścić zieloną kulę po CloudSpatialAnchor znajduje. Umożliwi to również ponowne stuknięcie ekranu, dzięki czemu możesz powtórzyć cały scenariusz jeszcze raz: utworzyć kolejną lokalną kotwicę, przesłać ją i zlokalizować ponownie.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=228-265)]

Gotowe. Uruchom aplikację z **programu Visual Studio** po raz ostatni, aby wypróbować cały scenariusz od końca do końca. Poruszaj się po urządzeniu i umieść białą kulę. Następnie poruszaj głową, aby rejestrować dane otoczenia, aż kula zmieni kolor na żółty. Twoja lokalna kotwica zostanie przesłana, a kula zmieni kolor na niebieski. Na koniec ponownie stuknij ekran, aby twoja lokalna kotwica została usunięta, a następnie zapytamy o jej odpowiednik w chmurze. Kontynuuj przenoszenie urządzenia, aż do zlokalizowania kotwicy przestrzennej chmury. Zielona kula powinna pojawić się we właściwym miejscu i można spłukać & powtórzyć cały scenariusz ponownie.

[!INCLUDE [AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md)]