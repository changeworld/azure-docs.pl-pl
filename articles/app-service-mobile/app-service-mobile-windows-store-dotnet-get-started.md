---
title: Tworzenie aplikacji platformy UWP
description: Wykonaj kroki opisane w tym samouczku, aby rozpocząć używanie zapleczy aplikacji mobilnych Azure na potrzeby tworzenia aplikacji platformy uniwersalnej systemu Windows w języku C#, Visual Basic lub JavaScript.
ms.assetid: 47124296-2908-4d92-85e0-05c4aa6db916
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 06/25/2019
ms.openlocfilehash: a0f78239406567513c1eb94b48bf1090165d9185
ms.sourcegitcommit: 3d4917ed58603ab59d1902c5d8388b954147fe50
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2019
ms.locfileid: "74668697"
---
# <a name="create-a-windows-app-with-an-azure-backend"></a>Tworzenie aplikacji systemu Windows z zapleczem na platformie Azure

[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

> [!NOTE]
> Usługa Visual Studio App Center obsługuje kompleksowe i zintegrowane usługi mające kluczowe znaczenie podczas tworzenia aplikacji mobilnych. Deweloperzy mogą używać usług do **tworzenia**, **testowania** i **dystrybuowania** w celu konfigurowania potoku ciągłej integracji i ciągłego wdrażania. Po wdrożeniu aplikacji deweloperzy mogą monitorować stan i użycie aplikacji za pomocą usług do **analizy** i **diagnostyki**, a także współpracować z użytkownikami za pomocą usługi do **wypychania**. Deweloperzy mogą również korzystać z usługi **uwierzytelniania** do uwierzytelniania użytkowników oraz usługi **danych** do utrwalania i synchronizowania danych aplikacji w chmurze.
>
> Jeśli chcesz zintegrować usługi w chmurze w aplikacji mobilnej, zarejestruj się w usłudze [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) już dziś.

## <a name="overview"></a>Przegląd

W tym samouczku przedstawiono sposób dodawania usługi zaplecza opartej na chmurze do aplikacji platformy uniwersalnej systemu Windows. Aby uzyskać więcej informacji, zobacz artykuł [Co to jest usługa Mobile Apps](app-service-mobile-value-prop.md). Poniżej przedstawiono ekrany przechwycone z ukończonej aplikacji:

![Ukończona aplikacja klasyczna](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-completed-desktop.png)

Wykonanie czynności opisanych w tym samouczku jest wymaganiem wstępnym dla wszystkich innych samouczków Aplikacji mobilnych dotyczących aplikacji platformy uniwersalnej systemu Windows.

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków tego samouczka niezbędne są następujące elementy:

* Aktywne konto platformy Azure. Jeśli nie masz konta, możesz utworzyć konto wersji próbnej platformy Azure i uzyskać maksymalnie 10 bezpłatnych aplikacji mobilnych, z których możesz korzystać nawet po zakończeniu okresu ważności wersji próbnej. Aby uzyskać szczegółowe informacje, zobacz artykuł [Bezpłatna wersja próbna platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
* Windows 10.
* Visual Studio Community 2017.
* Wiedza dotycząca opracowywania aplikacji platformy UWP. Odwiedź stronę [dokumentacji platformy UWP](https://docs.microsoft.com/windows/uwp/), aby dowiedzieć się, jak [skonfigurować środowisko](https://docs.microsoft.com/windows/uwp/get-started/get-set-up) tworzenia aplikacji platformy UWP.

## <a name="create-a-new-azure-mobile-app-backend"></a>Tworzenie zaplecza nowej Aplikacji mobilnej Azure

Wykonaj te kroki, aby utworzyć zaplecze nowej Aplikacji mobilnej.

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>Tworzenie połączenia z bazą danych i Konfigurowanie projektu klienta i serwera
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-client-project"></a>Uruchom projekt klienta

1. Otwórz projekt platformy UWP.

2. Przejdź do [Azure Portal](https://portal.azure.com/) i przejdź do utworzonej aplikacji mobilnej. W bloku `Overview` Wyszukaj adres URL, który jest publicznym punktem końcowym aplikacji mobilnej. Przykład — nazwa sitename dla mojej aplikacji "test123" zostanie https://test123.azurewebsites.net.

3. Otwórz plik `App.xaml.cs` w tym folderze — Windows-platformy UWP-CS/ZUMOAPPNAME/. Nazwa aplikacji jest `ZUMOAPPNAME`.

4. W klasie `App` Zastąp parametr `ZUMOAPPURL` z publicznym punktem końcowym powyżej.

    `public static MobileServiceClient MobileService = new MobileServiceClient("ZUMOAPPURL");`

    stanowi
    
    `public static MobileServiceClient MobileService = new MobileServiceClient("https://test123.azurewebsites.net");`

5. Naciśnij klawisz F5, aby wdrożyć i uruchomić aplikację.

6. W aplikacji wpisz znaczący tekst, na przykład *Ukończ samouczek* w polu tekstowym **Wstaw czynność do wykonania**, a następnie kliknij pozycję **Zapisz**.

    ![Kompletna aplikacja systemu Windows typu Szybki start na komputerze](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-startup.png)

    Spowoduje to wysłanie żądania POST do zaplecza nowej aplikacji mobilnej, która jest obsługiwana na platformie Azure.
