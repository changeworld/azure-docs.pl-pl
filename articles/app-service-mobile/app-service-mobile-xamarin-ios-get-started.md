---
title: Wprowadzenie do funkcji Azure App Service Mobile Apps dla aplikacji Xamarin.iOS | Microsoft Docs
description: Wykonaj czynności opisane w tym samouczku, aby rozpocząć używanie usługi Azure Mobile Apps do tworzenia aplikacji platformy Xamarin.iOS.
services: app-service\mobile
documentationcenter: xamarin
author: elamalani
manager: crdun
editor: ''
ms.assetid: 14428794-52ad-4b51-956c-deb296cafa34
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 57867eeca9f29cfc3983cbdca94c830aa7a20500
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446259"
---
# <a name="create-a-xamarinios-app"></a>Tworzenie aplikacji platformy Xamarin.iOS
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

> [!NOTE]
> Visual Studio App Center jest inwestujemy w nowe i zintegrowane usługi decydujące znaczenie dla aplikacji mobilnych. Deweloperzy mogą używać **kompilacji**, **testu** i **dystrybucji** usług do konfigurowania potoku ciągłej integracji i ciągłego dostarczania. Gdy aplikacja jest wdrażana, deweloperzy mogą monitorować stan i użycie ich przy użyciu aplikacji **Analytics** i **diagnostyki** usług i angażuj użytkowników za pomocą **wypychania** Usługa. Deweloperzy mogą również wykorzystać **uwierzytelniania** do uwierzytelniania użytkowników i **danych** usługę, aby utrwalić i synchronizowanie danych aplikacji w chmurze. Zapoznaj się z [platformy App Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=app-service-mobile-xamarin-ios-get-started) już dziś.
>

## <a name="overview"></a>Przegląd
W tym samouczku przedstawiono sposób dodawania usługi zaplecza opartej na chmurze do aplikacji mobilnej platformy Xamarin.iOS przy użyciu zaplecza Aplikacji mobilnej Azure.  Utworzysz zaplecze nowej aplikacji mobilnej oraz prostą aplikację platformy Xamarin.iOS typu *Lista czynności do wykonania*, która przechowuje dane aplikacji na platformie Azure.

Wykonanie kroków tego samouczka jest wymagane w przypadku wszystkich innych samouczków dla platformy Xamarin.iOS dotyczących używania funkcji Mobile Apps w usłudze Azure App Service.

## <a name="prerequisites"></a>Wymagania wstępne
Do wykonania kroków tego samouczka niezbędne jest spełnienie następujących wymagań wstępnych:

* Aktywne konto platformy Azure. Jeśli nie masz konta, utwórz konto wersji próbnej platformy Azure i uzyskać maksymalnie 10 bezpłatnych aplikacji mobilnych, z których możesz korzystać nawet po zakończeniu okresu ważności wersji próbnej. Aby uzyskać więcej informacji, zobacz [bezpłatnej wersji próbnej Azure](https://azure.microsoft.com/pricing/free-trial/).
* Visual Studio for Mac. Zobacz [Instalator i instalacja programu Visual Studio dla komputerów Mac](https://docs.microsoft.com/visualstudio/mac/installation?view=vsmac-2019)
* Komputer Mac z Xcode 9.0 lub nowszym.
  
## <a name="create-an-azure-mobile-app-backend"></a>Tworzenie zaplecza Aplikacji mobilnej Azure
[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>Tworzenie połączenia z bazą danych i konfigurowanie projektu klienta i serwera
[!INCLUDE [app-service-mobile-configure-new-backend](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-xamarinios-app"></a>Uruchamianie aplikacji platformy Xamarin.iOS
1. Otwórz projekt rozszerzenia Xamarin.iOS.

2. Przejdź do [witryny Azure portal](https://portal.azure.com/) i przejdź do aplikacji mobilnej, który został utworzony. Na `Overview` bloku, zwróć uwagę na adres URL, który jest publiczny punkt końcowy dla twojej aplikacji mobilnej. Przykład — nazwa witryny, dla mojej nazwy aplikacji "test123" będzie https://test123.azurewebsites.net.

3. Otwórz plik `QSTodoService.cs` w tym folderze - xamarin.iOS/ZUMOAPPNAME. Nazwa aplikacji jest `ZUMOAPPNAME`.

4. W `QSTodoService` klasy, Zastąp `ZUMOAPPURL` zmiennej z publicznym punktem końcowym powyżej.

    `const string applicationURL = @"ZUMOAPPURL";`

    staje się
    
    `const string applicationURL = @"https://test123.azurewebsites.net";`
    
5. Naciśnij klawisz F5, aby wdrożyć i uruchomić aplikację w emulatorze urządzenia iPhone.

6. W aplikacji wpisz znaczący tekst, na przykład *ukończenia tego samouczka* a następnie kliknij przycisk +.

    ![][10]

    Dane z żądania zostaną wstawione do tabeli TodoItem. Elementy przechowywane w tabeli są zwracane przez zaplecze aplikacji mobilnej, a dane są wyświetlane na liście.

   > [!NOTE]
   > Możesz przejrzeć kod uzyskujący dostęp do zaplecza aplikacji mobilnej w celu wyszukiwania i wstawiania danych. Znajduje się on w pliku C# ToDoActivity.cs.
   
<!-- Images. -->
[10]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-quickstart-startup-ios.png