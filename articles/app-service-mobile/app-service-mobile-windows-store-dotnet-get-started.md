---
title: tworzenie aplikacji platformy uniwersalnej systemu Windows
description: Wykonaj kroki opisane w tym samouczku, aby rozpocząć używanie zapleczy aplikacji mobilnych Azure na potrzeby tworzenia aplikacji platformy uniwersalnej systemu Windows w języku C#, Visual Basic lub JavaScript.
ms.assetid: 47124296-2908-4d92-85e0-05c4aa6db916
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 06/25/2019
ms.openlocfilehash: 9188db19adab9bd46d65fc97f1c62b39141cee90
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77461389"
---
# <a name="create-a-windows-app-with-an-azure-backend"></a>Tworzenie aplikacji systemu Windows z zapleczem na platformie Azure

[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>Omówienie

W tym samouczku przedstawiono sposób dodawania usługi zaplecza opartej na chmurze do aplikacji platformy uniwersalnej systemu Windows. Aby uzyskać więcej informacji, zobacz artykuł [Co to jest usługa Mobile Apps](app-service-mobile-value-prop.md). Poniżej przedstawiono ekrany przechwycone z ukończonej aplikacji:

![Ukończona aplikacja klasyczna](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-completed-desktop.png)

Wykonanie czynności opisanych w tym samouczku jest wymaganiem wstępnym dla wszystkich innych samouczków Aplikacji mobilnych dotyczących aplikacji platformy uniwersalnej systemu Windows.

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków tego samouczka niezbędne są następujące elementy:

* Aktywne konto platformy Azure. Jeśli nie masz konta, możesz utworzyć konto wersji próbnej platformy Azure i uzyskać maksymalnie 10 bezpłatnych aplikacji mobilnych, z których możesz korzystać nawet po zakończeniu okresu ważności wersji próbnej. Aby uzyskać szczegółowe informacje, zobacz [Bezpłatna wersja próbna platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
* Windows 10.
* Visual Studio Community 2017.
* Wiedza dotycząca opracowywania aplikacji platformy UWP. Odwiedź stronę [dokumentacji platformy UWP](https://docs.microsoft.com/windows/uwp/), aby dowiedzieć się, jak [skonfigurować środowisko](https://docs.microsoft.com/windows/uwp/get-started/get-set-up) tworzenia aplikacji platformy UWP.

## <a name="create-a-new-azure-mobile-app-backend"></a>Tworzenie zaplecza nowej Aplikacji mobilnej Azure

Wykonaj te kroki, aby utworzyć zaplecze nowej Aplikacji mobilnej.

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>Tworzenie połączenia z bazą danych i konfigurowanie projektu klienta i serwera
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-client-project"></a>Uruchamianie projektu klienta

1. Otwórz projekt platformy uniwersalnej systemu i platformy uniwersalnej systemu.

2. Przejdź do [witryny Azure portal](https://portal.azure.com/) i przejdź do utworzonej aplikacji mobilnej. Na `Overview` bloku poszukaj adresu URL, który jest publicznym punktem końcowym aplikacji mobilnej. Przykład - nazwa witryny dla mojej nazwy aplikacji "test123" będzie https://test123.azurewebsites.net.

3. Otwórz plik `App.xaml.cs` w tym folderze - windows-uwp-cs/ZUMOAPPNAME/. Nazwa aplikacji `ZUMOAPPNAME`to .

4. W `App` klasie `ZUMOAPPURL` zamień parametr na publiczny punkt końcowy powyżej.

    `public static MobileServiceClient MobileService = new MobileServiceClient("ZUMOAPPURL");`

    Staje się
    
    `public static MobileServiceClient MobileService = new MobileServiceClient("https://test123.azurewebsites.net");`

5. Naciśnij klawisz F5, aby wdrożyć i uruchomić aplikację.

6. W aplikacji wpisz znaczący tekst, na przykład *Ukończ samouczek* w polu tekstowym **Wstaw czynność do wykonania**, a następnie kliknij pozycję **Zapisz**.

    ![Kompletna aplikacja systemu Windows typu Szybki start na komputerze](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-startup.png)

    Spowoduje to wysłanie żądania POST do zaplecza nowej aplikacji mobilnej, która jest obsługiwana na platformie Azure.
