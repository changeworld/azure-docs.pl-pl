---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 09/13/2018
ms.author: wolfma
ms.openlocfilehash: afe6f1493c7fa8272c67f23d6708ad6e4eea9381
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66145503"
---
1. Uruchom program Visual Studio 2017.

1. Na pasku menu w programie Visual Studio wybierz **Narzędzia > Pobierz narzędzia** i upewnij się, że **programowanie aplikacji klasycznych dla platformy .NET** obciążenie jest dostępne. Jeśli to obciążenie nie zostało zainstalowane, zaznacz to pole wyboru, a następnie kliknij przycisk **Modyfikuj**, aby rozpocząć instalację. Pobranie i zainstalowanie tego składnika może potrwać kilka minut.

   Jeśli pole wyboru **Programowanie aplikacji klasycznych dla platformy .NET** jest zaznaczone, możesz teraz zamknąć to okno dialogowe.

   ![Włącz pakiet roboczy Programowanie aplikacji klasycznych dla platformy .NET](~/articles/cognitive-services/speech-service/media/sdk/vs-enable-net-desktop-workload.png)

1. Następnie utwórzmy projekt. Na pasku menu wybierz pozycję **Plik > Nowy > Projekt**. W wyświetlonym oknie dialogowym w lewym okienku rozwiń sekcje **Zainstalowane > Visual C# > Pulpit Windows**, a następnie wybierz pozycję **Aplikacja konsoli (.NET Framework)**. Nazwij ten projekt *helloworld*.

    ![Utwórz aplikację konsoli w języku Visual C# (.NET Framework)](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-01-new-console-app.png "Utwórz aplikację konsoli w języku Visual C# (.NET Framework)")

1. Projekt jest już skonfigurowany, czas więc zainstalować [pakiet NuGet zestawu SDK rozpoznawania mowy](https://aka.ms/csspeech/nuget) i dodać odwołanie do niego w kodzie. Znajdź Eksploratora rozwiązań, a następnie kliknij prawym przyciskiem myszy pozycję helloworld. W menu wybierz pozycję **Zarządzaj pakietami NuGet**.

   ![Kliknij prawym przyciskiem myszy pozycję Zarządzaj pakietami NuGet dla rozwiązania](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-02-manage-nuget-packages.png "Zarządzaj pakietami NuGet dla rozwiązania")

1. W prawym górnym rogu Menedżera pakietów NuGet odszukaj listę rozwijaną **Źródło pakietów** i upewnij się, że jest wybrana pozycja **nuget.org**. Następnie wybierz przycisk **Przeglądaj**, wyszukaj pakiet `Microsoft.CognitiveServices.Speech` i zainstaluj jego najnowszą stabilną wersję.

   ![Zainstaluj pakiet NuGet Microsoft.CognitiveServices.Speech](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-03-nuget-install-1.0.0.png "Instalowanie pakietu NuGet")

1. Zaakceptuj wszystkie umowy i licencje, aby rozpocząć instalację.

   ![Zaakceptuj licencję](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-04-nuget-license.png "Zaakceptuj licencję")

    Po zainstalowaniu pakietu w konsoli Menedżera pakietów zostanie wyświetlone potwierdzenie.

1. Następnym krokiem jest utworzenie konfiguracji platformy zgodnej z architekturą komputera, za pomocą którego będzie kompilowana i uruchamiana aplikacja konsoli. Na pasku menu wybierz pozycję **Kompilacja** > **Menedżer konfiguracji**.

    ![Uruchom Menedżera konfiguracji](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-05-cfg-manager-click.png "Uruchom Menedżera konfiguracji")

1. W oknie dialogowym **Menedżer konfiguracji** odszukaj listę rozwijaną **Aktywne platformy rozwiązania** i wybierz pozycję **Nowa**.

    ![Dodaj nową platformę w oknie Menedżera konfiguracji](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-06-cfg-manager-new.png "Dodaj nową platformę w oknie Menedżera konfiguracji")

1. Jeśli używasz 64-bitowego systemu Windows, po wyświetleniu monitu **Wpisz lub wybierz nową platformę** wybierz opcję `x64`. Jeśli używasz 32-bitowego systemu Windows, wybierz opcję `x86`. Po zakończeniu kliknij pozycję **OK**.

    ![W 64-bitowym systemie Windows dodaj nową platformę o nazwie „x64”](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-07-cfg-manager-add-x64.png "Dodawanie platformy x64")
