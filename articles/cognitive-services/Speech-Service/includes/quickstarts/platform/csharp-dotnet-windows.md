---
title: 'Szybki start: panel SDK mowy dla platformy .NET Framework (Windows) — usługa mowy'
titleSuffix: Azure Cognitive Services
description: Ten przewodnik służy do konfigurowania platformy dla języka C# w obszarze .NET Framework for Windows za pomocą zestawu SDK usługi mowy.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/10/2019
ms.author: erhopf
ms.openlocfilehash: a09b969ee3e11aeb04f338cf035b21b5da9bd952
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "78925398"
---
W tym przewodniku pokazano, jak zainstalować pakiet [SDK mowy](~/articles/cognitive-services/speech-service/speech-sdk.md) dla platformy .NET Framework (Windows). Jeśli chcesz tylko nazwa pakietu, aby rozpocząć `Install-Package Microsoft.CognitiveServices.Speech` na własną rękę, uruchom w konsoli NuGet.

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Ten przewodnik Szybki start wymaga następujących elementów:

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)

## <a name="create-a-visual-studio-project-and-install-the-speech-sdk"></a>Tworzenie projektu programu Visual Studio i instalowanie pakietu SDK mowy

Należy zainstalować [pakiet NuGet zestawu SDK mowy,](https://aka.ms/csspeech/nuget) dzięki czemu można odwoływać się do niego w kodzie. Aby to zrobić, może być najpierw konieczne utworzenie projektu **helloworld.** Jeśli masz już projekt z dostępnym obciążeniem **deweloperskim pulpitu .NET,** możesz użyć tego projektu i przejść do [programu Korzystanie z Menedżera pakietów NuGet w celu zainstalowania zestawu SDK mowy.](#use-nuget-package-manager-to-install-the-speech-sdk)

### <a name="create-helloworld-project"></a>Tworzenie projektu helloworld

1. Otwórz program Visual Studio 2019.

1. W oknie Start wybierz pozycję **Utwórz nowy projekt**. 

1. W oknie **Utwórz nowy projekt** wybierz pozycję **Aplikacja konsoli (.NET Framework),** a następnie wybierz pozycję **Dalej**.

1. W oknie **Konfigurowanie nowego projektu** wprowadź *helloworld* w **programie Project name**, wybierz lub utwórz ścieżkę katalogu w aplikacji **Lokalizacja**, a następnie wybierz pozycję **Utwórz**.

1. Na pasku menu programu Visual Studio wybierz pozycję **Narzędzia** > **Pobierz narzędzia i funkcje**, który otwiera Instalator programu Visual Studio i wyświetla okno dialogowe **Modyfikowanie.**

1. Sprawdź, czy dostępne jest obciążenie **programistyczne dla komputerów stacjonarnych .NET.** Jeśli obciążenie nie zostało zainstalowane, zaznacz pole wyboru obok niego, a następnie wybierz pozycję **Modyfikuj,** aby rozpocząć instalację. Pobranie i zainstalowanie tego składnika może potrwać kilka minut.

   Jeśli pole wyboru obok pozycji **Programowanie pulpitu .NET** jest już zaznaczone, wybierz pozycję **Zamknij,** aby zakończyć okno dialogowe.

   ![Włącz pakiet roboczy Programowanie aplikacji klasycznych dla platformy .NET](~/articles/cognitive-services/speech-service/media/sdk/vs-enable-net-desktop-workload.png)

1. Zamknij Instalator programu Visual Studio.

### <a name="use-nuget-package-manager-to-install-the-speech-sdk"></a>Instalowanie zestawu SDK mowy za pomocą Menedżera pakietów NuGet

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy projekt **helloworld,** a następnie wybierz pozycję **Zarządzaj pakietami NuGet,** aby wyświetlić Menedżera pakietów NuGet.

   ![Menedżer pakietów NuGet](~/articles/cognitive-services/speech-service/media/sdk/vs-nuget-package-manager.png)

1. W prawym górnym rogu znajdź pole rozwijane **Źródło pakietu** i **`nuget.org`** upewnij się, że jest zaznaczone.

1. W lewym górnym rogu wybierz pozycję **Przeglądaj**.

1. W polu wyszukiwania wpisz *microsoft.CognitiveServices.Speech* i wybierz pozycję **Wprowadź**.

1. W wynikach wyszukiwania wybierz pakiet **Microsoft.CognitiveServices.Speech,** a następnie wybierz pozycję **Zainstaluj,** aby zainstalować najnowszą stabilną wersję.

   ![Zainstaluj pakiet Microsoft.CognitiveServices.Speech NuGet](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-03-nuget-install-1.0.0.png)

1. Zaakceptuj wszystkie umowy i licencje, aby rozpocząć instalację.

   Po zainstalowaniu pakietu w oknie **Konsoli Menedżera pakietów** pojawi się potwierdzenie.

### <a name="choose-target-architecture"></a>Wybieranie architektury docelowej

Aby utworzyć i uruchomić aplikację konsoli, należy utworzyć konfigurację platformy odpowiadającą architekturze komputera.

1. Na pasku menu wybierz pozycję **Build** > **Configuration Manager**. Zostanie wyświetlone okno dialogowe **Menedżer konfiguracji.**

   ![Okno dialogowe Menedżer konfiguracji](~/articles/cognitive-services/speech-service/media/sdk/vs-configuration-manager-dialog-box.png)

1. Z listy rozwijanej **Platformy aktywnego rozwiązania** wybierz pozycję **Nowy**. Zostanie wyświetlone okno dialogowe **Nowa platforma rozwiązania.**

1. W polu rozwijany **Typ lub wybierz nową platformę:**
   - Jeśli korzystasz z 64-bitowego systemu Windows, wybierz **x64**.
   - Jeśli korzystasz z 32-bitowego systemu Windows, wybierz **x86**.

1. Wybierz **przycisk OK,** a następnie **zamknij**.

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [windows](../quickstart-list.md)]
