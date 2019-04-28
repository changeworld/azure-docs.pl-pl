---
title: Rozpoczynanie pracy z funkcją Mobile Apps za pomocą platformy Xamarin.Forms
description: Wykonaj czynności opisane w tym samouczku, aby rozpocząć używanie funkcji Mobile Apps do tworzenia aplikacji platformy Xamarin.Forms
services: app-service\mobile
documentationcenter: xamarin
author: conceptdev
manager: crdun
ms.assetid: 5e692220-cc89-4548-96c8-35259722acf5
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: crdun
ms.openlocfilehash: 2e0a4b51e9140b9b3c0032980b7c6cd5a4d55638
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62128944"
---
# <a name="create-a-xamarinforms-app-with-azure"></a>Tworzenie aplikacji platformy Xamarin.Forms na platformie Azure

[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

W tym samouczku przedstawiono sposób dodawania usługi zaplecza opartej na chmurze do aplikacji mobilnej platformy Xamarin.Forms przy użyciu funkcji Mobile Apps usługi Azure App Service jako zaplecza. Tworzysz nowe zaplecze funkcji Mobile Apps oraz prostą aplikację platformy Xamarin.Forms typu Lista czynności do wykonania, która przechowuje dane aplikacji na platformie Azure.

Wykonanie czynności opisanych w tym samouczku jest wymaganiem wstępnym dla wszystkich innych samouczków usługi Mobile Apps dotyczących aplikacji platformy Xamarin.Forms.

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków tego samouczka niezbędne są następujące elementy:

* Aktywne konto platformy Azure. Jeśli nie masz konta, możesz utworzyć konto wersji próbnej platformy Azure i uzyskać maksymalnie 10 bezpłatnych aplikacji mobilnych, z których możesz korzystać nawet po zakończeniu okresu ważności wersji próbnej. Aby uzyskać więcej informacji, zobacz [Bezpłatna wersja próbna platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

* Narzędzia Visual Studio Tools for Xamarin w programie Visual Studio 2017 lub Visual Studio dla komputerów Mac. Instrukcje znajdują się na [stronie z opisem instalacji platformy Xamarin][Install Xamarin].

* (opcjonalnie) Aby móc tworzyć aplikacje dla systemu iOS, wymagany jest komputer Mac z programem Xcode 9.0 lub nowszym. Do opracowywania aplikacji dla systemu iOS można używać programu Visual Studio dla komputerów Mac lub programu Visual Studio 2017, jeśli komputer Mac jest dostępny w sieci.

## <a name="create-a-new-mobile-apps-back-end"></a>Tworzenie nowego zaplecza funkcji Mobile Apps

Aby utworzyć nowe zaplecze funkcji Mobile Apps, wykonaj następujące czynności:

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

Masz teraz zdefiniowane zaplecze funkcji Mobile Apps, którego mogą używać mobilne aplikacje. Teraz pobierasz projekt serwera dla prostego zaplecza typu „lista czynności do wykonania” i publikujesz go na platformie Azure.

## <a name="configure-the-server-project"></a>Konfigurowanie projektu serwera

Aby skonfigurować projekt serwera do użycia zaplecza Node.js lub .NET, wykonaj następujące czynności:

[!INCLUDE [app-service-mobile-configure-new-backend](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-xamarinforms-solution"></a>Pobieranie i uruchamianie platformy Xamarin.Forms

Do otwierania rozwiązania wymagane są narzędzia Visual Studio Tools for Xamarin. Zobacz [instrukcje dotyczące instalacji platformy Xamarin][Install Xamarin]. Jeśli te narzędzia zostały już zainstalowane, wykonaj następujące kroki, aby pobrać i otwórz rozwiązanie:

### <a name="visual-studio"></a>Visual Studio

1. Przejdź do witryny [Azure Portal].

2. W bloku ustawień aplikacji mobilnej kliknij pozycję **Szybki start** (w obszarze Wdrożenie > **Xamarin.Forms**). W kroku 3 kliknij pozycję **Utwórz nową aplikację**, jeśli nie została jeszcze wybrana.  Następnie kliknij przycisk **Pobierz**.

   Ta akcja spowoduje pobranie projektu zawierającego aplikację klienta połączoną z aplikacją mobilną. Zapisz skompresowany plik projektu na komputerze lokalnym i zapamiętaj, gdzie został on zapisany.

3. Wyodrębnij pobrany projekt, a następnie otwórz go w programie Visual Studio 2017.

   ![Projekt wyodrębniony w programie Visual Studio][8]

4. Wykonaj poniższe instrukcje, aby uruchomić projekty systemu Android lub Windows, a jeśli w sieci jest dostępny komputer Mac, również projekty systemu iOS.

### <a name="visual-studio-for-mac"></a>Visual Studio dla komputerów Mac

1. Przejdź do witryny [Azure Portal].

2. W bloku ustawień aplikacji mobilnej kliknij pozycję **Szybki start** (w obszarze Wdrożenie > **Xamarin.Forms**). W kroku 3 kliknij pozycję **Utwórz nową aplikację**, jeśli nie została jeszcze wybrana.  Następnie kliknij przycisk **Pobierz**.

   Ta akcja spowoduje pobranie projektu zawierającego aplikację klienta połączoną z aplikacją mobilną. Zapisz skompresowany plik projektu na komputerze lokalnym i zapamiętaj, gdzie został on zapisany.

3. Wyodrębnij pobrany projekt, a następnie otwórz go w programie Visual Studio dla komputerów Mac.

   ![Projekt wyodrębniony w programie Visual Studio dla komputerów Mac][9]

4. Wykonaj poniższe instrukcje, aby uruchomić projekty systemu Android lub iOS.



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

Ta akcja spowoduje wysłanie żądania POST do nowego zaplecza funkcji Mobile Apps, które jest hostowane na platformie Azure. Dane z żądania zostaną wstawione do tabeli TodoItem. Elementy przechowywane w tabeli są zwracane przez zaplecze funkcji Mobile Apps, a dane są wyświetlane na liście.

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

1. Kliknij prawym przyciskiem myszy projekt iOS, a następnie wybierz pozycję **Ustaw jako projekt startowy**.

2. W menu **Uruchom** wybierz pozycję **Rozpocznij debugowanie** w celu skompilowania projektu i uruchomienia aplikacji w emulatorze urządzenia iPhone.



W aplikacji wpisz znaczący tekst, na przykład *Poznaj program Xamarin*, a wybierz znak plusa (**+**).

![Tworzona aplikacja dla systemu iOS][10]

Ta akcja spowoduje wysłanie żądania POST do nowego zaplecza funkcji Mobile Apps, które jest hostowane na platformie Azure. Dane z żądania zostaną wstawione do tabeli TodoItem. Elementy przechowywane w tabeli są zwracane przez zaplecze funkcji Mobile Apps, a dane są wyświetlane na liście.

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

Ta akcja spowoduje wysłanie żądania POST do nowego zaplecza funkcji Mobile Apps, które jest hostowane na platformie Azure. Dane z żądania zostaną wstawione do tabeli TodoItem. Elementy przechowywane w tabeli są zwracane przez zaplecze funkcji Mobile Apps, a dane są wyświetlane na liście.

![Tworzona aplikacja dla platformy uniwersalnej systemu Windows][12]

> [!NOTE]
> Kod uzyskujący dostęp do zaplecza funkcji Mobile Apps można znaleźć w pliku C# **TodoItemManager.cs** projektu biblioteki klas przenośnych rozwiązania.
>

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli masz problemy z kompilowaniem rozwiązania, uruchom menedżera pakietów NuGet i przeprowadź aktualizację rozszerzenia `Xamarin.Forms` do najnowszej wersji, a w projekcie systemu Android zaktualizuj pakiety obsługi rozszerzenia `Xamarin.Android`. Projekty typu Szybki start nie zawsze zawierają najnowsze wersje.

Pamiętaj, że wszystkie pakiety dla pomocy technicznej, do których odwołuje się projekt systemu Android, muszą mieć tę samą wersję. [Pakiet NuGet usługi Azure Mobile Apps](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/) ma zależność `Xamarin.Android.Support.CustomTabs` dla platformy Android, więc jeśli projekt używa nowszych pakietów dla pomocy technicznej, musisz bezpośrednio zainstalować ten pakiet w wymaganej wersji w celu uniknięcia konfliktów.

## <a name="next-steps"></a>Kolejne kroki

* [Dodawanie uwierzytelniania do aplikacji](app-service-mobile-xamarin-forms-get-started-users.md) Dowiedz się, jak uwierzytelniać użytkowników aplikacji przy użyciu dostawcy tożsamości.

* [Dodawanie powiadomień wypychanych do aplikacji](app-service-mobile-xamarin-forms-get-started-push.md) Dowiedz się, jak dodać obsługę powiadomień wypychanych do aplikacji i skonfigurować zaplecze funkcji Mobile Apps na potrzeby wysyłania powiadomień wypychanych przy użyciu usługi Azure Notification Hubs.

* [Włączanie synchronizacji w trybie offline dla aplikacji](app-service-mobile-xamarin-forms-get-started-offline-data.md) Dowiedz się, jak dodać obsługę aplikacji w trybie offline przy użyciu zaplecza funkcji Mobile Apps. Synchronizacja w trybie offline umożliwia wyświetlanie, dodawanie lub modyfikowanie danych aplikacji mobilnej, nawet w przypadku braku połączenia sieciowego.

* [Używanie zarządzanego klienta funkcji Mobile Apps](app-service-mobile-dotnet-how-to-use-client-library.md) Dowiedz się, jak pracować z zestawem SDK zarządzanego klienta w aplikacji platformy Xamarin.

* [Korzystanie z innych usług platformy Azure za pomocą rozszerzenia Xamarin.Forms](https://docs.microsoft.com/xamarin/xamarin-forms/data-cloud/) Dodawanie innych funkcji platformy Azure, takich jak wyszukiwanie, magazyn i usługi poznawcze, do aplikacji platformy Xamarin.Forms.

<!-- Anchors. -->
[Get started with Mobile Apps back ends]:#getting-started
[Create a new Mobile Apps back end]:#create-new-service
[Next steps]:#next-steps

<!-- Images. -->
[6]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart.png
[8]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart-vs.png
[9]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart-xs.png
[10]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-android.png
[12]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-windows.png

<!-- URLs. -->
[Install Xamarin]: https://docs.microsoft.com/xamarin/cross-platform/get-started/installation/
[Mobile app SDK]: https://go.microsoft.com/fwlink/?LinkId=257545
[Azure Portal]: https://portal.azure.com/
