---
title: Wprowadzenie do usługi Azure Mobile Engagement na potrzeby aplikacji platformy Silverlight systemu Windows Phone
description: Dowiedz się, jak używać usługi Azure Mobile Engagement z funkcją analizy i powiadomieniami wypychanymi na potrzeby aplikacji platformy Silverlight systemu Windows Phone.
services: mobile-engagement
documentationcenter: windows
author: piyushjo
manager: erikre
editor: ''
ms.assetid: aa34692f-87f7-47c6-a20c-a1972750bc25
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 9fb1426e66df6cd8085342743b7d045c297743e5
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="get-started-with-azure-mobile-engagement-for-windows-phone-silverlight-apps"></a>Wprowadzenie do usługi Azure Mobile Engagement na potrzeby aplikacji platformy Silverlight systemu Windows Phone
> [!IMPORTANT]
> Usługa Azure Mobile Engagement zostanie wycofana w dniu 31-03-2018. Ta strona zostanie usunięta wkrótce potem.
> 

[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

W tym temacie pokazano, jak za pomocą usługi Azure Mobile Engagement można określić sposób użycia aplikacji oraz wysyłać powiadomienia wypychane do segmentowanych użytkowników aplikacji platformy Silverlight systemu Windows Phone.
W tym samouczku został omówiony prosty scenariusz emisji przy użyciu usługi Mobile Engagement. W ramach tego samouczka utworzona zostanie pusta aplikacja platformy Silverlight systemu Windows Phone, za pomocą której będą zbierane podstawowe dane i odbierane powiadomienia wypychane przy użyciu Usługi powiadomień systemu Windows.

> [!NOTE]
> Usługa Azure Mobile Engagement zostanie wycofana w marcu 2018 r. i jest obecnie dostępna wyłącznie dla dotychczasowych klientów. Aby uzyskać więcej informacji, zobacz [Mobile Engagement](https://azure.microsoft.com/en-us/services/mobile-engagement/).

> [!NOTE]
> Projekty systemu Windows Phone 8.1 i wcześniejszych nie są obsługiwane w programie Visual Studio 2017 r.  Aby uzyskać więcej informacji, zobacz [Obsługiwane platformy i zgodność programu Visual Studio 2017](https://www.visualstudio.com/en-us/productinfo/vs2017-compatibility-vs).

> [!NOTE]
> Jeśli aplikacja ma być przeznaczona zarówno dla systemu Windows Phone 8.1 (bez użycia platformy Silverlight), dodatkowe informacje można zawiera [Samouczek aplikacji uniwersalnych systemu Windows](mobile-engagement-windows-store-dotnet-get-started.md).
> 
> 

Dla tego samouczka wymagane są następujące elementy:

* Visual Studio 2013
* Pakiet NuGet [MicrosoftAzure.MobileEngagement]

> [!NOTE]
> Do wykonania kroków tego samouczka potrzebne jest aktywne konto platformy Azure. Jeśli jej nie masz, możesz utworzyć bezpłatne konto próbne w zaledwie kilka minut. Aby uzyskać szczegółowe informacje, zobacz artykuł [Bezpłatna wersja próbna platformy Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-windows-phone-get-started).
> 
> 

## <a id="setup-azme"></a>Konfigurowanie usługi Mobile Engagement dla aplikacji systemu Windows Phone
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a id="connecting-app"></a>Łączenie aplikacji z zapleczem usługi Mobile Engagement
Ten samouczek przedstawia „podstawową integrację”, tj. minimalny zestaw wymagany do zbierania danych i wysyłania powiadomień wypychanych. Kompletna dokumentacja integracji znajduje się w sekcji [Mobile Engagement Windows Phone SDK integration](mobile-engagement-windows-phone-sdk-overview.md) (Integracja zestawu SDK dla systemu Windows Phone usługi Mobile Engagement).

Aby zademonstrować integrację, utworzona zostanie podstawowa aplikacja za pomocą programu Visual Studio.

### <a name="create-a-new-windows-phone-silverlight-project"></a>Tworzenie nowego projektu platformy Silverlight systemu Windows Phone
W następujących krokach założono korzystanie z programu Visual Studio 2015, chociaż kroki są podobne także w przypadku wcześniejszych wersji programu Visual Studio. 

1. Uruchom program Visual Studio i na ekranie **Strona główna** wybierz pozycję **Nowy projekt**.
2. W oknie podręcznym wybierz pozycję **Windows 8** -> **Windows Phone** -> **Pusta aplikacja (Windows Phone Silverlight)**. Wypełnij pola **Nazwa** i **Nazwa rozwiązania**, a następnie kliknij przycisk **OK**.
   
    ![][1]
3. Można wybrać system docelowy: **Windows Phone 8.0** lub **Windows Phone 8.1**.

Została utworzona nowa aplikacja platformy Silverlight systemu Windows Phone, z którą zostanie zintegrowany zestaw SDK usługi Azure Mobile Engagement.

### <a name="connect-your-app-to-the-mobile-engagement-backend"></a>Łączenie aplikacji z zapleczem usługi Mobile Engagement
1. Zainstaluj pakiet NuGet [MicrosoftAzure.MobileEngagement] w projekcie.
2. Otwórz plik `WMAppManifest.xml` (w folderze Właściwości) i upewnij się, że w znaczniku `<Capabilities />` został zadeklarowany następujący kod:
   
        <Capability Name="ID_CAP_NETWORKING" />
        <Capability Name="ID_CAP_IDENTITY_DEVICE" />
   
    ![][2]
3. Teraz wklej parametry połączenia, które wcześniej zostały skopiowane dla aplikacji usługi Mobile Engagement, a następnie wklej je do pliku `Resources\EngagementConfiguration.xml` między znaczniki `<connectionString>` i `</connectionString>`:
   
    ![][3]
4. W pliku `App.xaml.cs`:
   
    a. Dodaj instrukcję `using`:
   
            using Microsoft.Azure.Engagement;
   
    b. Zainicjuj zestaw SDK w metodzie `Application_Launching`:
   
            private void Application_Launching(object sender, LaunchingEventArgs e)
            {
              EngagementAgent.Instance.Init();
            }
   
    c. Wstaw następujący kod w metodzie `Application_Activated`:
   
            private void Application_Activated(object sender, ActivatedEventArgs e)
            {
               EngagementAgent.Instance.OnActivated(e);
            }

## <a id="monitor"></a>Włączanie monitorowania w czasie rzeczywistym
Aby rozpocząć wysyłanie danych i upewnić się, że użytkownicy są aktywni, konieczne jest wysłanie co najmniej jednego ekranu (Działanie) do zaplecza usługi Mobile Engagement.

1. W pliku MainPage.xaml.cs dodaj instrukcję `using`:
   
        using Microsoft.Azure.Engagement;
2. Zastąp klasę bazową **MainPage**, którą wcześniej była **PhoneApplicationPage**, klasą **EngagementPage**.
   
        class MainPage : EngagementPage 
3. W pliku `MainPage.xml`:
   
    a. Dodaj deklaracje przestrzeni nazw:
   
            xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"
   
    b. Zastąp element `phone:PhoneApplicationPage` w nazwie tagu XML elementem `engagement:EngagementPage`.

## <a id="monitor"></a>Łączenie aplikacji z funkcją monitorowania w czasie rzeczywistym
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a id="integrate-push"></a>Włączanie powiadomień wypychanych i funkcji komunikatów w aplikacji
Usługa Mobile Engagement umożliwia wchodzenie w interakcję z użytkownikami przy użyciu powiadomień wypychanych i komunikatów w aplikacji w kontekście kampanii. Ten moduł w portalu Mobile Engagement ma nazwę REACH.
W poniższych sekcjach aplikacja zostanie skonfigurowana do ich odbierania.

### <a name="enable-your-app-to-receive-mpns-push-notifications"></a>Umożliwianie aplikacji otrzymywanie powiadomień wypychanych usługi MPNS
Dodaj nowe funkcje do pliku `WMAppManifest.xml`:

        ID_CAP_PUSH_NOTIFICATION
        ID_CAP_WEBBROWSERCOMPONENT

   ![][5]

### <a name="initialize-the-reach-sdk"></a>Inicjowanie zestawu SDK modułu REACH
1. W pliku `App.xaml.cs` wywołaj funkcję `EngagementReach.Instance.Init();` w funkcji **Application_Launching** bezpośrednio po zainicjowaniu agenta:
   
        private void Application_Launching(object sender, LaunchingEventArgs e)
        {
           EngagementAgent.Instance.Init();
           EngagementReach.Instance.Init();
        }
2. W pliku `App.xaml.cs` wywołaj funkcję `EngagementReach.Instance.OnActivated(e);` w funkcji **Application_Activated** bezpośrednio po zainicjowaniu agenta:
   
        private void Application_Activated(object sender, ActivatedEventArgs e)
        {
           EngagementAgent.Instance.OnActivated(e);
           EngagementReach.Instance.OnActivated(e);
        }

Wszystko jest gotowe. Teraz zostanie zweryfikowane, czy podstawowa integracja została przeprowadzona prawidłowo.

## <a id="send"></a>Wysyłanie powiadomienia do aplikacji
[!INCLUDE [Create Windows Push campaign](../../includes/mobile-engagement-windows-push-campaign.md)]

Na urządzeniu powinno zostać wyświetlone powiadomienie, które będzie widoczne jako powiadomienie w aplikacji, jeśli aplikacja jest otwarta, lub jeśli nie jest, jako powiadomienie wyskakujące mające postać zbliżoną do następującej: 

![][6]

<!-- URLs. -->
[MicrosoftAzure.MobileEngagement]: http://go.microsoft.com/?linkid=9874664
[Mobile Engagement Windows Phone SDK documentation]: ../mobile-engagement-windows-phone-integrate-engagement/

<!-- Images. -->
[1]: ./media/mobile-engagement-windows-phone-get-started/project-properties.png
[2]: ./media/mobile-engagement-windows-phone-get-started/wmappmanifest-capabilities.png
[3]: ./media/mobile-engagement-windows-phone-get-started/add-connection-string.png
[5]: ./media/mobile-engagement-windows-phone-get-started/reach-capabilities.png
[6]: ./media/mobile-engagement-windows-phone-get-started/push-screenshot.png
