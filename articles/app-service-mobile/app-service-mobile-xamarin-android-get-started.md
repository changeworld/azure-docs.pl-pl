---
title: Wprowadzenie do aplikacji Xamarin.Android
description: Wykonaj ten samouczek, aby rozpocząć korzystanie z usługi Azure Mobile Apps dla tworzenia systemu Android platformy Xamarin.
ms.assetid: 81649dd3-544f-40ff-b9b7-60c66d683e60
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 06/25/2019
ms.openlocfilehash: b42205436c88f9075423bfcaf9e5a9fd931ee4f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77461372"
---
# <a name="create-a-xamarinandroid-app"></a>Tworzenie aplikacji platformy Xamarin.Android
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>Omówienie
W tym samouczku przedstawiono sposób dodawania usługi zaplecza opartej na chmurze do aplikacji platformy Xamarin.Android. Aby uzyskać więcej informacji, zobacz artykuł [Co to jest usługa Mobile Apps](app-service-mobile-value-prop.md).

Zrzut ekranu gotowej aplikacji znajduje się poniżej:

![][0]

Wykonanie czynności opisanych w tym samouczku jest wymaganiem wstępnym dla wszystkich innych samouczków usługi Mobile Apps dotyczących aplikacji platformy Xamarin.Android.

## <a name="prerequisites"></a>Wymagania wstępne
Do wykonania kroków tego samouczka niezbędne jest spełnienie następujących wymagań wstępnych:

* Aktywne konto platformy Azure. Jeśli nie masz konta, skorzystaj z bezpłatnej wersji próbnej platformy Azure i uzyskaj maksymalnie 10 bezpłatnych aplikacji mobilnych. Aby uzyskać szczegółowe informacje, zobacz [Bezpłatna wersja próbna platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
* Visual Studio z programem Xamarin. Instrukcje można znaleźć w temacie [Setup and install for Visual Studio and Xamarin](/visualstudio/cross-platform/setup-and-install) (Konfigurowanie i instalowanie dla programów Visual Studio i Xamarin).

## <a name="create-an-azure-mobile-app-backend"></a>Tworzenie zaplecza Aplikacji mobilnej Azure
Wykonaj te kroki, aby utworzyć zaplecze aplikacji mobilnej.

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

W ten sposób zainicjowano obsługę zaplecza Aplikacji mobilnej Azure, które może być używane przez aplikacje mobilne klientów. Następnie należy pobrać projekt serwera dla prostego zaplecza typu „lista czynności do wykonania” i opublikować go na platformie Azure.

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>Tworzenie połączenia z bazą danych i konfigurowanie projektu klienta i serwera
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-xamarinandroid-app"></a>Uruchamianie aplikacji Xamarin.Android
1. Otwórz projekt Xamarin.Android.

2. Przejdź do [witryny Azure portal](https://portal.azure.com/) i przejdź do utworzonej aplikacji mobilnej. Na `Overview` bloku poszukaj adresu URL, który jest publicznym punktem końcowym aplikacji mobilnej. Przykład - nazwa witryny dla mojej nazwy aplikacji "test123" będzie https://test123.azurewebsites.net.

3. Otwórz plik `ToDoActivity.cs` w tym folderze - xamarin.android/ZUMOAPPNAME/ToDoActivity.cs. Nazwa aplikacji `ZUMOAPPNAME`to .

4. W `ToDoActivity` klasie `ZUMOAPPURL` zamień zmienną na publiczny punkt końcowy powyżej.

    `const string applicationURL = @"ZUMOAPPURL";`

    Staje się
    
    `const string applicationURL = @"https://test123.azurewebsites.net";`
    
5. Naciśnij klawisz F5, aby wdrożyć i uruchomić aplikację.

6. W aplikacji wpisz znaczący tekst, na przykład *Ukończ samouczek*, a następnie kliknij przycisk **Dodaj**.

    ![][10]

    Dane z żądania zostaną wstawione do tabeli czynności do wykonania. Elementy przechowywane w tabeli są zwracane przez zaplecze aplikacji mobilnej, a dane są wyświetlane na liście.

   > [!NOTE]
   > Możesz przejrzeć kod uzyskujący dostęp do zaplecza aplikacji mobilnej w celu wyszukiwania i wstawiania danych. Znajduje się on w pliku C# ToDoActivity.cs.
   
## <a name="troubleshooting"></a>Rozwiązywanie problemów
Jeśli masz problemy z kompilowaniem rozwiązania, uruchom menedżera pakietów NuGet i zaktualizuj pakiety dla pomocy technicznej `Xamarin.Android`. Projekty typu Szybki start nie zawsze zawierają najnowsze wersje.

Pamiętaj, że wszystkie pakiety dla pomocy technicznej, do których odwołuje się projekt, muszą mieć tę samą wersję. [Pakiet NuGet usługi Azure Mobile Apps](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/) ma zależność `Xamarin.Android.Support.CustomTabs` dla platformy Android, więc jeśli projekt używa nowszych pakietów dla pomocy technicznej, musisz bezpośrednio zainstalować ten pakiet w wymaganej wersji w celu uniknięcia konfliktów.

<!-- Images. -->
[0]: ./media/app-service-mobile-xamarin-android-get-started/mobile-quickstart-completed-android.png
[10]: ./media/app-service-mobile-xamarin-android-get-started/mobile-quickstart-startup-android.png
<!-- URLs. -->
[Visual Studio]: https://go.microsoft.com/fwLink/p/?LinkID=534203
