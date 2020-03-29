---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 08/30/2019
ms.author: wolfma
ms.openlocfilehash: c7332177795a45331749225e0f3dc3856e28a6a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "71327051"
---
Aby utworzyć projekt programu Visual Studio dla rozwoju systemu Windows, należy utworzyć projekt, skonfigurować program Visual Studio dla tworzenia pulpitu platformy .NET, zainstalować zestaw SDK mowy i wybrać architekturę docelową.

### <a name="create-the-project-and-add-the-workload"></a>Tworzenie projektu i dodawanie obciążenia

Aby rozpocząć, utwórz projekt w programie Visual Studio i upewnij się, że program Visual Studio jest skonfigurowany dla tworzenia pulpitu platformy .NET:

1. Otwórz program Visual Studio 2019.

1. W oknie Start wybierz pozycję **Utwórz nowy projekt**. 

1. W oknie **Utwórz nowy projekt** wybierz pozycję **Aplikacja konsoli (.NET Framework),** a następnie wybierz pozycję **Dalej**.

1. W oknie **Konfigurowanie nowego projektu** wprowadź *helloworld* w **programie Project name**, wybierz lub utwórz ścieżkę katalogu w aplikacji **Lokalizacja**, a następnie wybierz pozycję **Utwórz**.

1. Na pasku menu programu Visual Studio wybierz pozycję **Narzędzia** > **Pobierz narzędzia i funkcje**, który otwiera Instalator programu Visual Studio i wyświetla okno dialogowe **Modyfikowanie.**

1. Sprawdź, czy dostępne jest obciążenie **programistyczne dla komputerów stacjonarnych .NET.** Jeśli obciążenie nie zostało zainstalowane, zaznacz pole wyboru obok niego, a następnie wybierz pozycję **Modyfikuj,** aby rozpocząć instalację. Pobranie i zainstalowanie tego składnika może potrwać kilka minut.

   Jeśli pole wyboru obok pozycji **Programowanie pulpitu .NET** jest już zaznaczone, wybierz pozycję **Zamknij,** aby zakończyć okno dialogowe.

   ![Włącz pakiet roboczy Programowanie aplikacji klasycznych dla platformy .NET](../articles/cognitive-services/speech-service/media/sdk/vs-enable-net-desktop-workload.png)

1. Zamknij Instalator programu Visual Studio.

### <a name="install-the-speech-sdk"></a>Instalowanie zestawu SDK usługi Mowa

Następnym krokiem jest zainstalowanie [pakietu NuGet zestawu SDK mowy,](https://aka.ms/csspeech/nuget)dzięki czemu można odwoływać się do niego w kodzie.

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy projekt **helloworld,** a następnie wybierz pozycję **Zarządzaj pakietami NuGet,** aby wyświetlić Menedżera pakietów NuGet.

   ![Menedżer pakietów NuGet](../articles/cognitive-services/speech-service/media/sdk/vs-nuget-package-manager.png)

1. W prawym górnym rogu znajdź pole rozwijane **Źródło pakietu** i upewnij się, że **nuget.org** jest zaznaczona.

1. W lewym górnym rogu wybierz pozycję **Przeglądaj**.

1. W polu wyszukiwania wpisz *microsoft.CognitiveServices.Speech* i wybierz pozycję **Wprowadź**.

1. W wynikach wyszukiwania wybierz pakiet **Microsoft.CognitiveServices.Speech,** a następnie wybierz pozycję **Zainstaluj,** aby zainstalować najnowszą stabilną wersję.

   ![Zainstaluj pakiet Microsoft.CognitiveServices.Speech NuGet](../articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-03-nuget-install-1.0.0.png)

1. Zaakceptuj wszystkie umowy i licencje, aby rozpocząć instalację.

   Po zainstalowaniu pakietu w oknie **Konsoli Menedżera pakietów** pojawi się potwierdzenie.

### <a name="choose-the-target-architecture"></a>Wybierz architekturę docelową

Teraz, aby utworzyć i uruchomić aplikację konsoli, należy utworzyć konfigurację platformy odpowiadającą architekturze komputera.

1. Na pasku menu wybierz pozycję **Build** > **Configuration Manager**. Zostanie wyświetlone okno dialogowe **Menedżer konfiguracji.**

   ![Okno dialogowe Menedżer konfiguracji](../articles/cognitive-services/speech-service/media/sdk/vs-configuration-manager-dialog-box.png)

1. Z listy rozwijanej **Platformy aktywnego rozwiązania** wybierz pozycję **Nowy**. Zostanie wyświetlone okno dialogowe **Nowa platforma rozwiązania.**

1. W polu rozwijany **Typ lub wybierz nową platformę:**
   - Jeśli korzystasz z 64-bitowego systemu Windows, wybierz **x64**.
   - Jeśli korzystasz z 32-bitowego systemu Windows, wybierz **x86**.

1. Wybierz **przycisk OK,** a następnie **zamknij**.
