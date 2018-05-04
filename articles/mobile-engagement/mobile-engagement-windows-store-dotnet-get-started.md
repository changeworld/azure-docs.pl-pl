---
title: Wprowadzenie do usługi Azure Mobile Engagement na potrzeby uniwersalnych aplikacji systemu Windows
description: Dowiedz się, jak używać usługi Azure Mobile Engagement z funkcją analizy i powiadomieniami wypychanymi na potrzeby uniwersalnych aplikacji systemu Windows
services: mobile-engagement
documentationcenter: windows
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 48103867-7f64-4646-b019-42bd797d38e2
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 08/12/2016
ms.author: piyushjo;ricksal
ms.openlocfilehash: ed6f3e0a369157bbf4c89982f8e1acbc1ed51340
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="get-started-with-azure-mobile-engagement-for-windows-universal-apps"></a>Wprowadzenie do usługi Azure Mobile Engagement na potrzeby uniwersalnych aplikacji systemu Windows
> [!IMPORTANT]
> Usługa Azure Mobile Engagement zostanie wycofana w dniu 31-03-2018. Ta strona zostanie usunięta wkrótce potem.
> 

[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

W tym temacie pokazano, jak za pomocą usługi Azure Mobile Engagement można określić sposób użycia aplikacji oraz wysyłać powiadomienia wypychane do segmentowanych użytkowników uniwersalnej aplikacji systemu Windows.
W tym samouczku został omówiony prosty scenariusz emisji przy użyciu usługi Mobile Engagement. Utworzysz pustą uniwersalną aplikację systemu Windows, za pomocą której będą zbierane dane dotyczące zużycia i odbierane powiadomienia wypychane przy użyciu Usługi powiadomień systemu Windows.

> [!NOTE]
> Usługa Azure Mobile Engagement zostanie wycofana w marcu 2018 r. i jest obecnie dostępna wyłącznie dla dotychczasowych klientów. Aby uzyskać więcej informacji, zobacz [Mobile Engagement](https://azure.microsoft.com/services/mobile-engagement/).

## <a name="prerequisites"></a>Wymagania wstępne
[!INCLUDE [Prereqs](../../includes/mobile-engagement-windows-store-prereqs.md)]

## <a name="set-up-mobile-engagement-for-your-windows-universal-app"></a>Konfigurowanie usługi Mobile Engagement dla aplikacji uniwersalnej systemu Windows
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a id="connecting-app"></a>Łączenie aplikacji z zapleczem usługi Mobile Engagement
Ten samouczek przedstawia „podstawową integrację”, tj. minimalny zestaw wymagany do zbierania danych i wysyłania powiadomień wypychanych. Kompletna dokumentacja integracji znajduje się w sekcji [Mobile Engagement Windows Universal SDK integration](mobile-engagement-windows-store-sdk-overview.md) (Integracja uniwersalnego zestawu SDK systemu Windows usługi Mobile Engagement).

Aby zademonstrować integrację, utworzysz podstawową aplikację za pomocą programu Visual Studio.

### <a name="create-a-windows-universal-app-project"></a>Tworzenie projektu aplikacji uniwersalnej systemu Windows
W następujących krokach założono korzystanie z programu Visual Studio 2015, chociaż kroki są podobne także w przypadku wcześniejszych wersji programu Visual Studio.

1. Uruchom program Visual Studio i na ekranie **Strona główna** wybierz pozycję **Nowy projekt**.
2. W oknie podręcznym wybierz pozycję **Windows** -> **Aplikacja uniwersalna** -> **Pusta aplikacja (aplikacja uniwersalna systemu Windows)**. Wypełnij pola **Nazwa** i **Nazwa rozwiązania**, a następnie kliknij przycisk **OK**.

    ![][1]

Został utworzony projekt aplikacji uniwersalnej systemu Windows, z którym następnie zintegrujesz zestaw SDK usługi Azure Mobile Engagement.

### <a name="connect-your-app-to-mobile-engagement-backend"></a>Łączenie aplikacji z zapleczem usługi Mobile Engagement
1. Zainstaluj pakiet NuGet [MicrosoftAzure.MobileEngagement] w projekcie. Jeśli aplikacja ma być przeznaczona zarówno dla platformy Windows, jak i Windows Phone, należy to zrobić w obu projektach. W przypadku systemu Windows 8.x i Windows Phone 8.1 ten sam pakiet NuGet umieszcza poprawne pliki binarne specyficzne dla platformy w każdym projekcie.
2. Otwórz plik **Package.appxmanifest** i upewnij się, że dodano w nim następującą funkcję:

        Internet (Client)

    ![][2]
3. Teraz skopiuj parametry połączenia, które wcześniej zostały skopiowane dla aplikacji usługi Mobile Engagement, a następnie wklej je do pliku `Resources\EngagementConfiguration.xml` między znaczniki `<connectionString>` i `</connectionString>`:

    ![][3]

    > [!TIP]
    > Jeśli aplikacja jest przeznaczona zarówno dla platformy Windows, jak i Windows Phone, nadal powinny zostać utworzone dwie aplikacje usługi Mobile Engagement — jedna dla każdej z obsługiwanych platform. Dzięki dwóm aplikacjom możliwe będzie tworzenie poprawnej segmentacji odbiorców i wysyłanie odpowiednio skierowanych powiadomień dla każdej platformy.

    > [!IMPORTANT]
    > Menedżer NuGet nie kopiuje automatycznie zasobów zestawu SDK do aplikacji UWP systemu Windows 10. Należy to zrobić ręcznie, postępując zgodnie z wyświetlonymi instrukcjami (plik readme.txt) podczas instalacji pakietu Nuget.  

1. W pliku `App.xaml.cs`:

    a. Dodaj instrukcję `using`:

            using Microsoft.Azure.Engagement;

    b. Dodaj metodę inicjowania usługi Engagement:

           private void InitEngagement(IActivatedEventArgs e)
           {
             EngagementAgent.Instance.Init(e);

             //... rest of the code
           }

    d. Zainicjuj zestaw SDK w metodzie **OnLaunched**:

            protected override void OnLaunched(LaunchActivatedEventArgs e)
            {
              InitEngagement(e);

              //... rest of the code
            }

    d. Wstaw następujący kod w metodzie **OnActivated**, a następnie dodaj metodę, jeśli została już dodana:

            protected override void OnActivated(IActivatedEventArgs e)
            {
              InitEngagement(e);

              //... rest of the code
            }

## <a id="monitor"></a>Włączanie monitorowania w czasie rzeczywistym
Aby rozpocząć wysyłanie danych i upewnić się, że użytkownicy są aktywni, konieczne jest wysłanie co najmniej jednego ekranu (Działanie) do zaplecza usługi Mobile Engagement.

1. W pliku **MainPage.xaml.cs** dodaj następującą instrukcję `using`:

    using Microsoft.Azure.Engagement.Overlay;
2. Zmień klasę bazową **MainPage** z **Page** na **EngagementPageOverlay**:

        class MainPage : EngagementPageOverlay
3. W pliku `MainPage.xaml`:

    a. Dodaj deklaracje przestrzeni nazw:

        xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay"

    b. Zastąp wartość **Page** w nazwie tagu XML wartością **engagement:EngagementPageOverlay**

> [!IMPORTANT]
> Jeśli strona zastępuje metodę `OnNavigatedTo`, nie zapomnij wywołać metody `base.OnNavigatedTo(e)`. W przeciwnym razie działanie nie zostanie zgłoszone (element `EngagementPage` wywołuje metodę `StartActivity` wewnątrz jego metody `OnNavigatedTo`). Jest to szczególnie ważne w projekcie systemu Windows Phone, w którym szablon domyślny zawiera metodę `OnNavigatedTo`.
>
> W **aplikacjach uniwersalnych systemu Windows 10** użyj metody zalecanej w sekcji „Recommended method: overload your Page classes” (Zalecana metoda: przeciążenie klas Page) tematu [Advanced Reporting with the Windows Universal Apps Engagement SDK](mobile-engagement-windows-store-advanced-reporting.md) (Zaawansowane raportowanie za pomocą zestawu SDK zaangażowania aplikacji uniwersalnych systemu Windows), a nie wymienionej powyżej.

## <a id="monitor"></a>Łączenie aplikacji z funkcją monitorowania w czasie rzeczywistym
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a id="integrate-push"></a>Włączanie powiadomień wypychanych i funkcji komunikatów w aplikacji
Usługa Mobile Engagement umożliwia wchodzenie w interakcję z użytkownikami przy użyciu powiadomień wypychanych i komunikatów w aplikacji w kontekście kampanii. Ten moduł w portalu Mobile Engagement ma nazwę REACH.
W poniższych sekcjach aplikacja zostanie skonfigurowana do ich odbierania.

### <a name="enable-your-app-to-receive-wns-push-notifications"></a>Umożliwianie aplikacji otrzymywanie powiadomień wypychanych Usługi powiadomień systemu Windows
1. W pliku `Package.appxmanifest` na karcie **Aplikacja** w obszarze **Powiadomienia** ustaw pozycję **Możliwe do wyświetlania na pasku przewijania:** na wartość **Tak**

    ![][5]

### <a name="initialize-the-reach-sdk"></a>Inicjowanie zestawu SDK modułu REACH
W pliku `App.xaml.cs` wywołaj metodę **EngagementReach.Instance.Init(e);** w funkcji **InitEngagement** bezpośrednio po zainicjowaniu agenta:

        private void InitEngagement(IActivatedEventArgs e)
        {
           EngagementAgent.Instance.Init(e);
           EngagementReach.Instance.Init(e);
        }

Wszystko jest gotowe do wysłania wyskakującego powiadomienia. Następnie zweryfikujemy, czy podstawowa integracja została przeprowadzona prawidłowo.

### <a name="grant-access-to-mobile-engagement-to-send-notifications"></a>Udzielanie dostępu do usługi Mobile Engagement w celu wysyłania powiadomień
1. Otwórz [Centrum deweloperów Sklepu Windows] w przeglądarce internetowej, a następnie zaloguj się i utwórz konto, jeśli to konieczne.
2. Kliknij pozycję **Pulpit nawigacyjny** w prawym górnym rogu, a następnie kliknij pozycję **Utwórz nową aplikację** z menu lewego panelu.

    ![][9]
3. Utwórz aplikację przez zarezerwowanie jej nazwy.

    ![][10]
4. Po utworzeniu aplikacji przejdź do pozycji **Usługi -> Powiadomienia wypychane** z menu po lewej stronie.

    ![][11]
5. W sekcji Powiadomienia wypychane kliknij link **Witryna usług Live**.

    ![][12]
6. Nastąpi przejście do sekcji poświadczeń wypychania. Upewnij się, że jesteś w sekcji **Ustawienia aplikacji**, a następnie skopiuj swój **identyfikator SID pakietu** i **klucz tajny klienta**.

    ![][13]
7. Przejdź do obszaru **Ustawienia** portalu Mobile Engagement, a następnie kliknij sekcję **Natywne powiadomienia wypychane** po lewej stronie. Następnie kliknij przycisk **Edytuj**, aby wprowadzić swój **identyfikator zabezpieczeń (SID) pakietu** i **klucz tajny** w przedstawiony sposób:

    ![][6]
8. Na końcu upewnij się, że aplikacja programu Visual Studio została skojarzona z tą utworzoną aplikacją w sklepie z aplikacjami. W programie Visual Studio kliknij pozycję **Skojarz aplikację ze sklepem**.

    ![][7]

## <a id="send"></a>Wysyłanie powiadomienia do aplikacji
[!INCLUDE [Create Windows Push campaign](../../includes/mobile-engagement-windows-push-campaign.md)]

Jeśli aplikacja jest uruchomiona, zobaczysz powiadomienie w aplikacji. W przeciwnym razie (jeśli aplikacja jest zamknięta) zobaczysz powiadomienie wyskakujące.
Jeśli widzisz powiadomienie w aplikacji, ale nie powiadomienie wyskakujące, a aplikacja jest uruchomiona w trybie debugowania w programie Visual Studio, spróbuj użyć pozycji **Zdarzenia cyklu życia -> Wstrzymaj** na pasku narzędzi, aby upewnić się, że aplikacja została wstrzymana. Jeśli kliknięto przycisk Strona główna podczas debugowania aplikacji w programie Visual Studio, to aplikacja nie zawsze zostanie wstrzymana, a w związku z tym, że powiadomienie jest wyświetlane w aplikacji, to nie jest wyświetlane powiadomienie wyskakujące.  

![][8]

<!-- URLs. -->
[Mobile Engagement Windows Universal SDK documentation]: ../mobile-engagement-windows-store-integrate-engagement/
[MicrosoftAzure.MobileEngagement]: http://go.microsoft.com/?linkid=9864592
[Centrum deweloperów Sklepu Windows]: https://dev.windows.com
[Windows Universal Apps - Overlay integration]: ../mobile-engagement-windows-store-integrate-engagement-reach/#overlay-integration

<!-- Images. -->
[1]: ./media/mobile-engagement-windows-store-dotnet-get-started/universal-app-creation.png
[2]: ./media/mobile-engagement-windows-store-dotnet-get-started/manifest-capabilities.png
[3]: ./media/mobile-engagement-windows-store-dotnet-get-started/add-connection-info.png
[5]: ./media/mobile-engagement-windows-store-dotnet-get-started/manifest-toast.png
[6]: ./media/mobile-engagement-windows-store-dotnet-get-started/enter-credentials.png
[7]: ./media/mobile-engagement-windows-store-dotnet-get-started/associate-app-store.png
[8]: ./media/mobile-engagement-windows-store-dotnet-get-started/vs-suspend.png
[9]: ./media/mobile-engagement-windows-store-dotnet-get-started/dashboard_create_app.png
[10]: ./media/mobile-engagement-windows-store-dotnet-get-started/dashboard_app_name.png
[11]: ./media/mobile-engagement-windows-store-dotnet-get-started/dashboard_services_push.png
[12]: ./media/mobile-engagement-windows-store-dotnet-get-started/dashboard_services_push_1.png
[13]: ./media/mobile-engagement-windows-store-dotnet-get-started/dashboard_services_push_creds.png
