---
title: Tworzenie aplikacji platformy uniwersalnej systemu Windows korzystającej z usługi Mobile Apps | Microsoft Docs
description: Wykonaj kroki opisane w tym samouczku, aby rozpocząć używanie zapleczy aplikacji mobilnych Azure na potrzeby tworzenia aplikacji platformy uniwersalnej systemu Windows w języku C#, Visual Basic lub JavaScript.
services: app-service\mobile
documentationcenter: windows
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 47124296-2908-4d92-85e0-05c4aa6db916
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: 508989e0e2daeac7922d61ff692ee4579139cfc8
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38307225"
---
# <a name="create-a-windows-app"></a>Tworzenie aplikacji systemu Windows
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>Omówienie
W tym samouczku przedstawiono sposób dodawania usługi zaplecza opartej na chmurze do aplikacji platformy uniwersalnej systemu Windows. Aby uzyskać więcej informacji, zobacz artykuł [Co to jest usługa Mobile Apps](app-service-mobile-value-prop.md). Poniżej przedstawiono ekrany przechwycone z ukończonej aplikacji:

![Ukończona aplikacja klasyczna](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-completed-desktop.png)   
Uruchamianie na komputerze

![Ukończona aplikacja na telefon](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-completed.png)  
Uruchamianie na telefonie

Wykonanie czynności opisanych w tym samouczku jest wymaganiem wstępnym dla wszystkich innych samouczków Aplikacji mobilnych dotyczących aplikacji platformy uniwersalnej systemu Windows.

## <a name="prerequisites"></a>Wymagania wstępne
Do wykonania kroków tego samouczka niezbędne są następujące elementy:

* Aktywne konto platformy Azure. Jeśli nie masz konta, możesz utworzyć konto wersji próbnej platformy Azure i uzyskać maksymalnie 10 bezpłatnych aplikacji mobilnych, z których możesz korzystać nawet po zakończeniu okresu ważności wersji próbnej. Aby uzyskać szczegółowe informacje, zobacz temat [Azure Free Trial](https://azure.microsoft.com/pricing/free-trial/) (Bezpłatna wersja próbna platformy Azure).
* Program [Visual Studio Community 2015] lub nowszy.

## <a name="create-a-new-azure-mobile-app-backend"></a>Tworzenie zaplecza nowej Aplikacji mobilnej Azure
Wykonaj te kroki, aby utworzyć zaplecze nowej Aplikacji mobilnej.

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

W ten sposób zainicjowano obsługę zaplecza Aplikacji mobilnej Azure, które może być używane przez aplikacje mobilne klientów. Teraz pobierzesz projekt serwera dla prostego zaplecza typu „lista czynności do wykonania” i opublikujesz go na platformie Azure.

## <a name="configure-the-server-project"></a>Konfigurowanie projektu serwera
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-client-project"></a>Pobieranie i uruchamianie projektu klienta
Po skonfigurowaniu zaplecza Aplikacji mobilnej można utworzyć nową aplikację klienta lub zmodyfikować istniejącą aplikację w celu nawiązania połączenia z platformą Azure. W tej sekcji można pobrać projekt szablonu aplikacji platformy uniwersalnej systemu Windows zmodyfikowany pod kątem potrzeb nawiązywania połączenia z zapleczem Aplikacji mobilnej.

1. W bloku **Szybki start** dla zaplecza Aplikacji mobilnej kliknij pozycję **Utwórz nową aplikację** > **Pobieranie**, a następnie wyodrębnij skompresowane pliki projektu na komputerze lokalnym.

    ![Pobieranie projektu Szybki start systemu Windows](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-app-windows-quickstart.png)
2. (Opcjonalnie) Dodaj projekt aplikacji platformy uniwersalnej systemu Windows do tego samego rozwiązania co projekt serwera. Ułatwi to debugowanie oraz testowanie zaplecza i aplikacji w tym samym rozwiązaniu Visual Studio (jeśli chcesz to zrobić). Aby dodać projekt aplikacji platformy uniwersalnej systemu Windows do rozwiązania, musisz korzystać z programu Visual Studio 2015 lub nowszego.
3. Z aplikacją platformy uniwersalnej systemu Windows jako projektem startowym naciśnij klawisz F5, aby wdrożyć i uruchomić aplikację.
4. W aplikacji wpisz znaczący tekst, na przykład *Ukończ samouczek* w polu tekstowym **Wstaw czynność do wykonania**, a następnie kliknij pozycję **Zapisz**.

    ![Kompletna aplikacja systemu Windows typu Szybki start na komputerze](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-startup.png)

    Spowoduje to wysłanie żądania POST do zaplecza nowej aplikacji mobilnej, która jest obsługiwana na platformie Azure.
5. (Opcjonalnie) Zatrzymaj aplikację i uruchom ją ponownie na innym urządzeniu lub w emulatorze aplikacji mobilnych.

    ![Kompletna aplikacja systemu Windows typu Szybki start na telefonie](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-completed.png)

    Zauważ, że dane zapisane w poprzednim kroku są ładowane z platformy Azure po uruchomieniu aplikacji platformy uniwersalnej systemu Windows.

## <a name="next-steps"></a>Następne kroki
* [Dodawanie uwierzytelniania do aplikacji](app-service-mobile-windows-store-dotnet-get-started-users.md)  
  Dowiedz się, jak uwierzytelniać użytkowników aplikacji przy użyciu dostawcy tożsamości.
* [Dodawanie powiadomień wypychanych do aplikacji](app-service-mobile-windows-store-dotnet-get-started-push.md)  
  Dowiedz się, jak dodać obsługę powiadomień wypychanych do aplikacji i skonfigurować zaplecze Aplikacji mobilnej na potrzeby wysyłania powiadomień wypychanych przy użyciu usługi Azure Notification Hubs.
* [Włączanie synchronizacji w trybie offline dla aplikacji](app-service-mobile-windows-store-dotnet-get-started-offline-data.md)  
  Dowiedz się, jak dodać obsługę aplikacji w trybie offline przy użyciu zaplecza aplikacji mobilnej. Synchronizacja w trybie offline umożliwia użytkownikom końcowym interakcję z aplikacją mobilną &mdash; przeglądanie, dodawanie lub modyfikowanie danych &mdash; nawet w przypadku braku połączenia sieciowego.

<!-- Anchors. -->
<!-- Images. -->
<!-- URLs. -->
[Mobile App SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Azure portal]: https://portal.azure.com/
[Visual Studio Community 2015]: https://go.microsoft.com/fwLink/p/?LinkID=534203
