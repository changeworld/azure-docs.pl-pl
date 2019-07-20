---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 07/05/2019
ms.author: wolfma
ms.openlocfilehash: 308ee2ef121648cb45152948926c5fd7fb934744
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/19/2019
ms.locfileid: "68362577"
---
1. Open Visual Studio 2019.

1. W oknie uruchamiania wybierz pozycję **Utwórz nowy projekt**. 

1. Wybierz pozycję **aplikacja konsoli (.NET Framework)** , a następnie wybierz przycisk **dalej**.

1. W polu **Nazwa projektu**wprowadź `helloworld`, a następnie wybierz pozycję **Utwórz**.

1. Na pasku menu programu Visual Studio wybierz kolejno pozycje **Narzędzia** > **Pobierz narzędzia i funkcje**i sprawdź, czy jest dostępne obciążenie **Programowanie aplikacji klasycznych dla platformy .NET** . Jeśli obciążenie nie zostało zainstalowane, zaznacz pole wyboru, a następnie wybierz pozycję **Modyfikuj** , aby rozpocząć instalację. Pobranie i zainstalowanie tego składnika może potrwać kilka minut.

   Jeśli pole wyboru **Programowanie aplikacji klasycznych dla platformy .NET** jest zaznaczone, możesz teraz zamknąć to okno dialogowe.

   ![Włącz pakiet roboczy Programowanie aplikacji klasycznych dla platformy .NET](../articles/cognitive-services/speech-service/media/sdk/vs-enable-net-desktop-workload.png)

Następnym krokiem jest zainstalowanie [pakietu NuGet zestawu Speech SDK](https://aka.ms/csspeech/nuget), aby można było odwołać się do niego w kodzie.

1. W Eksplorator rozwiązań kliknij prawym przyciskiem `helloworld`myszy, a następnie wybierz pozycję **Zarządzaj pakietami NuGet** , aby wyświetlić Menedżera pakietów NuGet.

   ![Menedżer pakietów NuGet](../articles/cognitive-services/speech-service/media/sdk/vs-nuget-package-manager.png)

1. W prawym górnym rogu Znajdź pole listy rozwijanej **Źródło pakietu** i upewnij się, że wybrano opcję **NuGet.org** .

1. W lewym górnym rogu wybierz pozycję **Przeglądaj**.

1. W polu wyszukiwania wpisz `Microsoft.CognitiveServices.Speech` pakiet i naciśnij klawisz ENTER.

1. Wybierz `Microsoft.CognitiveServices.Speech`pozycję, a następnie wybierz pozycję **Zainstaluj** , aby zainstalować najnowszą stabilną wersję.

   ![Zainstaluj pakiet NuGet Microsoft. CognitiveServices. Speech](../articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-03-nuget-install-1.0.0.png)

1. Zaakceptuj wszystkie umowy i licencje, aby rozpocząć instalację.

   Po zainstalowaniu pakietu w oknie **konsola Menedżera pakietów** zostanie wyświetlone potwierdzenie.

Teraz, aby skompilować i uruchomić aplikację konsolową, Utwórz konfigurację platformy zgodną z architekturą komputera.

1. Na pasku menu wybierz kolejno opcje **Kompiluj** > **Configuration Manager**. **Programu Configuration Manager** pojawi się okno dialogowe.

   ![Okno dialogowe Configuration Manager](../articles/cognitive-services/speech-service/media/sdk/vs-configuration-manager-dialog-box.png)

1. W polu listy rozwijanej **platforma aktywnego rozwiązania** wybierz pozycję **Nowy**. Zostanie wyświetlone okno dialogowe **Nowa platforma rozwiązania** .

1. W polu **Typ lub wybierz nową platformę z** listy rozwijanej:
   - Jeśli korzystasz z 64-bitowego systemu Windows, wybierz pozycję **x64**.
   - Jeśli korzystasz z 32-bitowego systemu Windows, wybierz pozycję **x86**.

1. Wybierz przycisk **OK** , a następnie **Zamknij**.
