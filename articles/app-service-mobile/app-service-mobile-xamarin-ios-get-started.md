---
title: Wprowadzenie do aplikacji Xamarin.iOS
description: Wykonaj czynności opisane w tym samouczku, aby rozpocząć używanie usługi Azure Mobile Apps do tworzenia aplikacji platformy Xamarin.iOS.
ms.assetid: 14428794-52ad-4b51-956c-deb296cafa34
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 06/25/2019
ms.openlocfilehash: 6c35189e7c841fa2724f1cfe84afc689d5510676
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77459008"
---
# <a name="create-a-xamarinios-app"></a>Tworzenie aplikacji platformy Xamarin.iOS
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>Omówienie
W tym samouczku przedstawiono sposób dodawania usługi zaplecza opartej na chmurze do aplikacji mobilnej platformy Xamarin.iOS przy użyciu zaplecza Aplikacji mobilnej Azure.  Utworzysz zaplecze nowej aplikacji mobilnej oraz prostą aplikację platformy Xamarin.iOS typu *Lista czynności do wykonania*, która przechowuje dane aplikacji na platformie Azure.

Wykonanie kroków tego samouczka jest wymagane w przypadku wszystkich innych samouczków dla platformy Xamarin.iOS dotyczących używania funkcji Mobile Apps w usłudze Azure App Service.

## <a name="prerequisites"></a>Wymagania wstępne
Do wykonania kroków tego samouczka niezbędne jest spełnienie następujących wymagań wstępnych:

* Aktywne konto platformy Azure. Jeśli nie masz konta, utwórz konto wersji próbnej platformy Azure i uzyskać maksymalnie 10 bezpłatnych aplikacji mobilnych, z których możesz korzystać nawet po zakończeniu okresu ważności wersji próbnej. Aby uzyskać szczegółowe informacje, zobacz [Bezpłatna wersja próbna platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
* Visual Studio dla komputerów Mac. Zobacz [Ustawienia i instalacje programu Visual Studio dla komputerów Mac](https://docs.microsoft.com/visualstudio/mac/installation?view=vsmac-2019)
* Komputer Mac z kodem Xcode 9.0 lub nowszym.
  
## <a name="create-an-azure-mobile-app-backend"></a>Tworzenie zaplecza Aplikacji mobilnej Azure
[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>Tworzenie połączenia z bazą danych i konfigurowanie projektu klienta i serwera
[!INCLUDE [app-service-mobile-configure-new-backend](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-xamarinios-app"></a>Uruchamianie aplikacji Xamarin.iOS
1. Otwórz projekt Xamarin.iOS.

2. Przejdź do [witryny Azure portal](https://portal.azure.com/) i przejdź do utworzonej aplikacji mobilnej. Na `Overview` bloku poszukaj adresu URL, który jest publicznym punktem końcowym aplikacji mobilnej. Przykład - nazwa witryny dla mojej nazwy aplikacji "test123" będzie https://test123.azurewebsites.net.

3. Otwórz plik `QSTodoService.cs` w tym folderze - xamarin.iOS/ZUMOAPPNAME. Nazwa aplikacji `ZUMOAPPNAME`to .

4. W `QSTodoService` klasie `ZUMOAPPURL` zamień zmienną na publiczny punkt końcowy powyżej.

    `const string applicationURL = @"ZUMOAPPURL";`

    Staje się
    
    `const string applicationURL = @"https://test123.azurewebsites.net";`
    
5. Naciśnij klawisz F5, aby wdrożyć i uruchomić aplikację w emulatorze iPhone'a.

6. W aplikacji wpisz sensowny tekst, na przykład *Ukończ samouczek,* a następnie kliknij przycisk +.

    ![][10]

    Dane z żądania zostaną wstawione do tabeli czynności do wykonania. Elementy przechowywane w tabeli są zwracane przez zaplecze aplikacji mobilnej, a dane są wyświetlane na liście.

   > [!NOTE]
   > Możesz przejrzeć kod uzyskujący dostęp do zaplecza aplikacji mobilnej w celu wyszukiwania i wstawiania danych. Znajduje się on w pliku C# ToDoActivity.cs.
   
<!-- Images. -->
[10]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-quickstart-startup-ios.png
