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
ms.openlocfilehash: 14fb925896fc94def59b753711eb74f155b2e139
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388353"
---
# <a name="create-a-xamarinios-app"></a>Tworzenie aplikacji platformy Xamarin.iOS
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

> [!NOTE]
> Usługa Visual Studio App Center obsługuje kompleksowe i zintegrowane usługi mające kluczowe znaczenie podczas tworzenia aplikacji mobilnych. Deweloperzy mogą używać usług do **tworzenia**, **testowania** i **dystrybuowania** w celu konfigurowania potoku ciągłej integracji i ciągłego wdrażania. Po wdrożeniu aplikacji deweloperzy mogą monitorować stan i użycie aplikacji za pomocą usług do **analizy** i **diagnostyki**, a także współpracować z użytkownikami za pomocą usługi do **wypychania**. Deweloperzy mogą również korzystać z usługi **uwierzytelniania** do uwierzytelniania użytkowników oraz usługi **danych** do utrwalania i synchronizowania danych aplikacji w chmurze.
>
> Jeśli chcesz zintegrować usługi w chmurze w swojej aplikacji mobilnej, zarejestruj się w usłudze [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) już dziś.

## <a name="overview"></a>Przegląd
W tym samouczku przedstawiono sposób dodawania usługi zaplecza opartej na chmurze do aplikacji mobilnej platformy Xamarin.iOS przy użyciu zaplecza Aplikacji mobilnej Azure.  Utworzysz zaplecze nowej aplikacji mobilnej oraz prostą aplikację platformy Xamarin.iOS typu *Lista czynności do wykonania*, która przechowuje dane aplikacji na platformie Azure.

Wykonanie kroków tego samouczka jest wymagane w przypadku wszystkich innych samouczków dla platformy Xamarin.iOS dotyczących używania funkcji Mobile Apps w usłudze Azure App Service.

## <a name="prerequisites"></a>Wymagania wstępne
Do wykonania kroków tego samouczka niezbędne jest spełnienie następujących wymagań wstępnych:

* Aktywne konto platformy Azure. Jeśli nie masz konta, utwórz konto wersji próbnej platformy Azure i uzyskać maksymalnie 10 bezpłatnych aplikacji mobilnych, z których możesz korzystać nawet po zakończeniu okresu ważności wersji próbnej. Aby uzyskać szczegółowe informacje, zobacz artykuł [Bezpłatna wersja próbna platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
* Visual Studio dla komputerów Mac. Zobacz [Konfigurowanie i Instalowanie programu Visual Studio dla komputerów Mac](https://docs.microsoft.com/visualstudio/mac/installation?view=vsmac-2019)
* Komputer Mac z systemem Xcode 9,0 lub nowszym.
  
## <a name="create-an-azure-mobile-app-backend"></a>Tworzenie zaplecza Aplikacji mobilnej Azure
[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>Tworzenie połączenia z bazą danych i Konfigurowanie projektu klienta i serwera
[!INCLUDE [app-service-mobile-configure-new-backend](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-xamarinios-app"></a>Uruchamianie aplikacji platformy Xamarin. iOS
1. Otwórz projekt Xamarin. iOS.

2. Przejdź do [Azure Portal](https://portal.azure.com/) i przejdź do utworzonej aplikacji mobilnej. W bloku `Overview` poszukaj adresu URL, który jest publicznym punktem końcowym aplikacji mobilnej. Przykład — nazwa witryny dla mojej aplikacji "test123" będzie https://test123.azurewebsites.net.

3. Otwórz plik `QSTodoService.cs` w tym folderze — Xamarin. iOS/ZUMOAPPNAME. Nazwa aplikacji jest `ZUMOAPPNAME`.

4. W klasie `QSTodoService` Zastąp zmienną `ZUMOAPPURL` z publicznym punktem końcowym powyżej.

    `const string applicationURL = @"ZUMOAPPURL";`

    stanowi
    
    `const string applicationURL = @"https://test123.azurewebsites.net";`
    
5. Naciśnij klawisz F5, aby wdrożyć i uruchomić aplikację w emulatorze urządzenia iPhone.

6. W aplikacji wpisz znaczący tekst, na przykład *Ukończ samouczek* , a następnie kliknij przycisk +.

    ![][10]

    Dane z żądania zostaną wstawione do tabeli TodoItem. Elementy przechowywane w tabeli są zwracane przez zaplecze aplikacji mobilnej, a dane są wyświetlane na liście.

   > [!NOTE]
   > Możesz przejrzeć kod uzyskujący dostęp do zaplecza aplikacji mobilnej w celu wyszukiwania i wstawiania danych. Znajduje się on w pliku C# ToDoActivity.cs.
   
<!-- Images. -->
[10]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-quickstart-startup-ios.png