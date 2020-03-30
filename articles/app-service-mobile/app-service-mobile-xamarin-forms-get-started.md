---
title: Rozpocznij pracę z platformą Xamarin.Forms
description: Wykonaj czynności opisane w tym samouczku, aby rozpocząć używanie funkcji Mobile Apps do tworzenia aplikacji platformy Xamarin.Forms
ms.assetid: 5e692220-cc89-4548-96c8-35259722acf5
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 06/25/2019
ms.openlocfilehash: 9ae97ccbcc358a150e914a6e950579a972fef0bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77461338"
---
# <a name="create-a-xamarinforms-app-with-azure"></a>Tworzenie aplikacji platformy Xamarin.Forms na platformie Azure

[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>Omówienie
W tym samouczku przedstawiono sposób dodawania usługi zaplecza opartej na chmurze do aplikacji mobilnej platformy Xamarin.Forms przy użyciu funkcji Mobile Apps usługi Azure App Service jako zaplecza. Tworzysz nowe zaplecze funkcji Mobile Apps oraz prostą aplikację platformy Xamarin.Forms typu Lista czynności do wykonania, która przechowuje dane aplikacji na platformie Azure.

Wykonanie czynności opisanych w tym samouczku jest wymaganiem wstępnym dla wszystkich innych samouczków usługi Mobile Apps dotyczących aplikacji platformy Xamarin.Forms.

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków tego samouczka niezbędne są następujące elementy:

* Aktywne konto platformy Azure. Jeśli nie masz konta, możesz utworzyć konto wersji próbnej platformy Azure i uzyskać maksymalnie 10 bezpłatnych aplikacji mobilnych, z których możesz korzystać nawet po zakończeniu okresu ważności wersji próbnej. Aby uzyskać więcej informacji, zobacz [Bezpłatna wersja próbna platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

* Narzędzia programu Visual Studio dla platformy Xamarin w programie Visual Studio 2017 lub nowszym lub Visual Studio dla komputerów Mac. Instrukcje znajdują się na [stronie z opisem instalacji platformy Xamarin][Install Xamarin].

* (opcjonalnie) Aby móc tworzyć aplikacje dla systemu iOS, wymagany jest komputer Mac z programem Xcode 9.0 lub nowszym. Program Visual Studio dla komputerów Mac może służyć do tworzenia aplikacji systemu iOS lub visual studio 2017 lub nowsze mogą być używane (tak długo, jak mac jest dostępny w sieci).

## <a name="create-a-new-mobile-apps-back-end"></a>Tworzenie nowego zaplecza funkcji Mobile Apps
[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>Tworzenie połączenia z bazą danych i konfigurowanie projektu klienta i serwera
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-xamarinforms-solution"></a>Uruchamianie rozwiązania Xamarin.Forms

Do otwierania rozwiązania wymagane są narzędzia Visual Studio Tools for Xamarin. Zobacz [instrukcje dotyczące instalacji platformy Xamarin][Install Xamarin]. Jeśli te narzędzia zostały już zainstalowane, wykonaj następujące kroki, aby pobrać i otwórz rozwiązanie:

### <a name="visual-studio-windows-and-mac"></a>Visual Studio (Windows i Mac)

1. Przejdź do [witryny Azure portal](https://portal.azure.com/) i przejdź do utworzonej aplikacji mobilnej. Na `Overview` bloku poszukaj adresu URL, który jest publicznym punktem końcowym aplikacji mobilnej. Przykład - nazwa witryny dla mojej nazwy aplikacji "test123" będzie https://test123.azurewebsites.net.

2. Otwórz plik `Constants.cs` w tym folderze - xamarin.forms/ZUMOAPPNAME. Nazwa aplikacji `ZUMOAPPNAME`to .

3. W `Constants.cs` klasie `ZUMOAPPURL` zamień zmienną na publiczny punkt końcowy powyżej.

    `public static string ApplicationURL = @"ZUMOAPPURL";`

    Staje się

    `public static string ApplicationURL = @"https://test123.azurewebsites.net";`
    
4. Wykonaj poniższe instrukcje, aby uruchomić projekty systemu Android lub Windows, a jeśli w sieci jest dostępny komputer Mac, również projekty systemu iOS.

## <a name="optional-run-the-android-project"></a>(Opcjonalnie) Uruchamianie projektu systemu Android

W tej sekcji uruchamiasz projekt platformy Xamarin.Android. Jeśli nie pracujesz z urządzeniami z systemem Android, możesz pominąć tę sekcję.

### <a name="visual-studio"></a>Visual Studio

1. Kliknij prawym przyciskiem myszy projekt Android (Droid), a następnie wybierz pozycję **Ustaw jako projekt startowy**.

2. W menu **Kompilacja** wybierz pozycję **Configuration Manager**.

3. W oknie dialogowym **Configuration Manager** zaznacz pola wyboru **Kompiluj** i **Wdróż** obok projektu systemu Android oraz upewnij się, że zaznaczono pole **Kompiluj** obok projektu współużytkowanego kodu.

4. Aby skompilować projekt i uruchomić aplikację w emulatorze systemu Android, naciśnij klawisz **F5** lub kliknij przycisk **Uruchom**.

### <a name="visual-studio-for-mac"></a>Visual Studio dla komputerów Mac

1. Kliknij prawym przyciskiem myszy projekt Android, a następnie wybierz pozycję **Ustaw jako projekt startowy**.

2. W celu skompilowania projektu i uruchomienia aplikacji w emulatorze systemu Android wybierz menu **Uruchom**, a następnie pozycję **Rozpocznij debugowanie**.

W aplikacji wpisz znaczący tekst, na przykład *Poznaj program Xamarin*, a wybierz znak plusa (**+**).

![Tworzona aplikacja dla systemu Android][11]

Ta akcja spowoduje wysłanie żądania POST do nowego zaplecza funkcji Mobile Apps, które jest hostowane na platformie Azure. Dane z żądania zostaną wstawione do tabeli czynności do wykonania. Elementy przechowywane w tabeli są zwracane przez zaplecze funkcji Mobile Apps, a dane są wyświetlane na liście.

> [!NOTE]
> Kod uzyskujący dostęp do zaplecza funkcji Mobile Apps znajduje się w pliku C# **TodoItemManager.cs** projektu współużytkowanego kodu w rozwiązaniu.
>

## <a name="optional-run-the-ios-project"></a>(Opcjonalnie) Uruchamianie projektu iOS

W tej sekcji uruchamiasz projekt Xamarin.iOS dla urządzeń z systemem iOS. Jeśli nie pracujesz z urządzeniami z systemem iOS, możesz pominąć tę sekcję.

### <a name="visual-studio"></a>Visual Studio

1. Kliknij prawym przyciskiem myszy projekt iOS, a następnie wybierz pozycję **Ustaw jako projekt startowy**.

2. W menu **Kompilacja** wybierz pozycję **Configuration Manager**.

3. W oknie dialogowym **Configuration Manager** zaznacz pola wyboru **Kompiluj** i **Wdróż** obok projektu systemu iOS oraz upewnij się, że zaznaczono pole **Kompiluj** obok projektu współużytkowanego kodu.

4. Aby skompilować projekt i uruchomić aplikację w emulatorze urządzenia iPhone, naciśnij klawisz **F5**.

### <a name="visual-studio-for-mac"></a>Visual Studio dla komputerów Mac

1. Kliknij prawym przyciskiem myszy projekt systemu iOS, a następnie wybierz polecenie **Ustaw jako projekt startowy**.

2. W menu **Uruchom** wybierz pozycję **Rozpocznij debugowanie** w celu skompilowania projektu i uruchomienia aplikacji w emulatorze urządzenia iPhone.

W aplikacji wpisz znaczący tekst, na przykład *Poznaj program Xamarin*, a wybierz znak plusa (**+**).

![Tworzona aplikacja dla systemu iOS][10]

Ta akcja spowoduje wysłanie żądania POST do nowego zaplecza funkcji Mobile Apps, które jest hostowane na platformie Azure. Dane z żądania zostaną wstawione do tabeli czynności do wykonania. Elementy przechowywane w tabeli są zwracane przez zaplecze funkcji Mobile Apps, a dane są wyświetlane na liście.

> [!NOTE]
> Kod uzyskujący dostęp do zaplecza funkcji Mobile Apps można znaleźć w pliku C# **TodoItemManager.cs** projektu współużytkowanego kodu w rozwiązaniu.
>

## <a name="optional-run-the-windows-project"></a>(Opcjonalnie) Uruchamianie projektu Windows

W tej sekcji uruchomisz projekt platformy uniwersalnej systemu Windows Xamarin.Forms dla urządzeń z systemem Windows. Jeśli nie pracujesz z urządzeniami z systemem Windows, możesz pominąć tę sekcję.

### <a name="visual-studio"></a>Visual Studio

1. Kliknij prawym przyciskiem myszy dowolny projekt platformy uniwersalnej systemu Windows, a następnie wybierz pozycję **Ustaw jako projekt startowy**.

2. W menu **Kompilacja** wybierz pozycję **Configuration Manager**.

3. W oknie dialogowym **Configuration Manager** zaznacz pola wyboru **Kompiluj** i **Wdróż** obok wybranego projektu systemu Windows oraz upewnij się, że zaznaczono pole **Kompiluj** obok projektu współużytkowanego kodu.

4. Aby skompilować projekt i uruchomić aplikację w emulatorze systemu Windows, naciśnij klawisz **F5** lub kliknij przycisk **Uruchom** (z napisem **Komputer lokalny**).

> [!NOTE]
> Projektów dla systemu Windows nie można uruchamiać w systemie macOS.

W aplikacji wpisz znaczący tekst, na przykład *Poznaj program Xamarin*, a wybierz znak plusa (**+**).

Ta akcja spowoduje wysłanie żądania POST do nowego zaplecza funkcji Mobile Apps, które jest hostowane na platformie Azure. Dane z żądania zostaną wstawione do tabeli czynności do wykonania. Elementy przechowywane w tabeli są zwracane przez zaplecze funkcji Mobile Apps, a dane są wyświetlane na liście.

![Tworzona aplikacja dla platformy uniwersalnej systemu Windows][12]

> [!NOTE]
> Kod, który uzyskuje dostęp do aplikacji mobilnych zaplecza w **pliku TodoItemManager.cs** C# projektu biblioteki klas przenośnych rozwiązania.
>

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli masz problemy z kompilowaniem rozwiązania, uruchom menedżera pakietów NuGet i przeprowadź aktualizację rozszerzenia `Xamarin.Forms` do najnowszej wersji, a w projekcie systemu Android zaktualizuj pakiety obsługi rozszerzenia `Xamarin.Android`. Projekty typu Szybki start nie zawsze zawierają najnowsze wersje.

Pamiętaj, że wszystkie pakiety dla pomocy technicznej, do których odwołuje się projekt systemu Android, muszą mieć tę samą wersję. [Pakiet NuGet usługi Azure Mobile Apps](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/) ma zależność `Xamarin.Android.Support.CustomTabs` dla platformy Android, więc jeśli projekt używa nowszych pakietów dla pomocy technicznej, musisz bezpośrednio zainstalować ten pakiet w wymaganej wersji w celu uniknięcia konfliktów.

<!-- Images. -->
[10]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-android.png
[12]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-windows.png

<!-- URLs. -->
[Install Xamarin]: https://docs.microsoft.com/xamarin/cross-platform/get-started/installation/
