---
title: Wprowadzenie do usługi Azure Mobile Engagement na potrzeby platformy Xamarin.iOS
description: Dowiedz się, jak używać usługi Azure Mobile Engagement z funkcją analizy i powiadomieniami wypychanymi na potrzeby aplikacji platformy Xamarin.iOS.
services: mobile-engagement
documentationcenter: xamarin
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 0448209e-fff6-47bd-985c-2cf074bac12f
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 331ae3f77603ee5fc5153c52c5c103813d7029ed
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="get-started-with-azure-mobile-engagement-for-xamarinios-apps"></a>Rozpoczynanie pracy z usługą Azure Mobile Engagement na potrzeby aplikacji platformy Xamarin.iOS
> [!IMPORTANT]
> Usługa Azure Mobile Engagement zostanie wycofana w dniu 31-03-2018. Ta strona zostanie usunięta wkrótce potem.
> 

[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

W tym temacie pokazano, jak za pomocą usługi Azure Mobile Engagement można określić sposób użycia aplikacji oraz wysyłać powiadomienia wypychane do segmentowanych użytkowników aplikacji platformy Xamarin.iOS.
W tym samouczku zostanie utworzona pusta aplikacja platformy Xamarin.iOS służąca do zbierania danych podstawowych i odbierania powiadomień wypychanych przy użyciu systemu Apple Push Notification System (APNS).

> [!NOTE]
> Usługa Azure Mobile Engagement zostanie wycofana w marcu 2018 r. i jest obecnie dostępna wyłącznie dla dotychczasowych klientów. Aby uzyskać więcej informacji, zobacz [Mobile Engagement](https://azure.microsoft.com/services/mobile-engagement/).

Dla tego samouczka wymagane są następujące elementy:

* Program [Xamarin Studio](http://xamarin.com/studio). Można również użyć programu Visual Studio z platformą Xamarin, ale w tym samouczku używany jest program Xamarin Studio. Aby uzyskać instrukcje dotyczące instalowania, zobacz [Instalator i instalacja programu Visual Studio i platformy Xamarin](https://msdn.microsoft.com/library/mt613162.aspx). 
* [Zestaw SDK platformy Xamarin usługi Mobile Engagement](https://www.nuget.org/packages/Microsoft.Azure.Engagement.Xamarin/)

> [!NOTE]
> Do wykonania kroków tego samouczka potrzebne jest aktywne konto platformy Azure. Jeśli jej nie masz, możesz utworzyć bezpłatne konto próbne w zaledwie kilka minut. Aby uzyskać szczegółowe informacje, zobacz artykuł [Bezpłatna wersja próbna platformy Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-xamarin-ios-get-started).
> 
> 

## <a id="setup-azme"></a>Konfigurowanie usługi Mobile Engagement dla aplikacji systemu iOS
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a id="connecting-app"></a>Łączenie aplikacji z zapleczem usługi Mobile Engagement
Ten samouczek przedstawia „podstawową integrację”, tj. minimalny zestaw wymagany do zbierania danych i wysyłania powiadomień wypychanych.

Aby zademonstrować integrację, utworzona zostanie podstawowa aplikacja za pomocą platformy Xamarin:

### <a name="create-a-new-xamarinios-project"></a>Tworzenie nowego projektu platformy Xamarin.iOS
1. Uruchom program Xamarin Studio. Wybierz pozycję **File** (Plik)  -> **New** (Nowy) -> **Solution** (Rozwiązanie). 
   
    ![][1]
2. Wybierz pozycję **Single View App** (Aplikacja z jednym widokiem), upewnij się, że wybrany język to **C#**, a następnie kliknij przycisk **Next** (Dalej).
   
    ![][2]
3. Wypełnij pole **App Name** (Nazwa aplikacji) i **Organization Identifier** (Identyfikator organizacji), a następnie kliknij przycisk **Next** (Dalej). 
   
    ![][3]
   
   > [!IMPORTANT]
   > Upewnij się, że profil publikowania, który ostatecznie zostanie użyty do wdrożenia aplikacji systemu iOS używa identyfikatora aplikacji zgodnego z identyfikatorem pakietu z tej aplikacji. 
   > 
   > 
4. Jeśli to będzie konieczne, zaktualizuj pola **Project Name** (Nazwa projektu), **Solution Name** (Nazwa rozwiązania) i **Location** (Lokalizacja), a następnie kliknij pozycję **Create** (Utwórz).
   
    ![][4]

Za pomocą platformy Xamarin Studio zostanie utworzona aplikacja demonstracyjna, w której zostanie zintegrowana usługa Mobile Engagement. 

### <a name="connect-your-app-to-mobile-engagement-backend"></a>Łączenie aplikacji z zapleczem usługi Mobile Engagement
1. Kliknij prawym przyciskiem myszy folder **Packages** w oknie Solution (Rozwiązanie), a następnie wybierz pozycję **Add Packages** (Dodaj pakiety).
   
    ![][5]
2. Wyszukaj zestaw **Microsoft Azure Mobile Engagement Xamarin SDK** i dodaj go do swojego rozwiązania.  
   
    ![][6]
3. Otwórz plik **AppDelegate.cs** i dodaj następującą instrukcję using:
   
        using Microsoft.Azure.Engagement.Xamarin;
4. W metodzie **FinishedLaunching** dodaj następujący kod, aby zainicjować połączenie z zapleczem usługi Mobile Engagement. Upewnij się, że został dodany element **ConnectionString**. Ten kod zawiera również zastępczy element **NotificationIcon** dodawany przez zestaw SDK usługi Mobile Engagement Mobile, który można zastąpić. 
   
        EngagementConfiguration config = new EngagementConfiguration {
                        ConnectionString = "YourConnectionStringFromAzurePortal",
                        NotificationIcon = UIImage.FromBundle("close")
                    };
        EngagementAgent.Init (config);

## <a id="monitor"></a>Włączanie monitorowania w czasie rzeczywistym
Aby rozpocząć wysyłanie danych i sprawdzić, czy użytkownicy są aktywni, konieczne jest wysłanie co najmniej jednego ekranu do zaplecza usługi Mobile Engagement.

1. Otwórz plik **ViewController.cs** i dodaj następującą instrukcję using:
   
        using Microsoft.Azure.Engagement.Xamarin;
2. Zastąp klasy, w których klasa `ViewController` dziedziczy z klasy `UIViewController`, klasą `EngagementViewController`. 

## <a id="monitor"></a>Łączenie aplikacji z funkcją monitorowania w czasie rzeczywistym
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a id="integrate-push"></a>Włączanie powiadomień wypychanych i funkcji komunikatów w aplikacji
Usługa Mobile Engagement umożliwia wchodzenie w interakcję z użytkownikami i modułem REACH przy użyciu powiadomień wypychanych i komunikatów w aplikacji w kontekście kampanii. Ten moduł w portalu Mobile Engagement ma nazwę REACH.
W poniższych sekcjach aplikacja zostanie skonfigurowana do ich odbierania.

### <a name="modify-your-application-delegate"></a>Modyfikowanie delegata aplikacji
1. Otwórz plik **AppDelegate.cs** i dodaj następującą instrukcję using:
   
        using System; 
2. Teraz wewnątrz metody `FinishedLaunching` dodaj następujący kod służący do rejestrowania wiadomości wypychanych poniżej metody `EngagementAgent.init(...)`
   
        if (UIDevice.CurrentDevice.CheckSystemVersion(8,0))
        {
            var pushSettings = UIUserNotificationSettings.GetSettingsForTypes (
                (UIUserNotificationType.Badge |
                    UIUserNotificationType.Sound |
                    UIUserNotificationType.Alert),
                null);
            UIApplication.SharedApplication.RegisterUserNotificationSettings (pushSettings);
            UIApplication.SharedApplication.RegisterForRemoteNotifications ();
        }
        else
        {
            UIApplication.SharedApplication.RegisterForRemoteNotificationTypes (
                UIRemoteNotificationType.Badge |
                UIRemoteNotificationType.Sound |
                UIRemoteNotificationType.Alert);
        }
3. Na koniec zaktualizuj lub dodaj następujące metody:
   
        public override void DidReceiveRemoteNotification (UIApplication application, NSDictionary userInfo, 
            Action<UIBackgroundFetchResult> completionHandler)
        {
            EngagementAgent.ApplicationDidReceiveRemoteNotification(userInfo, completionHandler);
        }
   
        public override void RegisteredForRemoteNotifications (UIApplication application, NSData deviceToken)
        {
            // Register device token on Engagement
            EngagementAgent.RegisterDeviceToken(deviceToken);
        }
   
        public override void FailedToRegisterForRemoteNotifications(UIApplication application, NSError error)
        {
            Console.WriteLine("Failed to register for remote notifications: Error '{0}'", error);
        }
4. Upewnij się, że na liście właściwości **Info.plist** rozwiązania wartość pozycji **Bundle Identifier** (Identyfikator pakietu) jest zgodna z **identyfikatorem aplikacji** znajdującym się w profilu aprowizacji w Centrum deweloperów firmy Apple. 
   
    ![][7]
5. Upewnij się również, że na tej samej liście właściwości **Info.plist** zaznaczono pole wyboru **Enable Background Modes** (Włącz tryby w tle) i **Remote Notifications** (Powiadomienia zdalne). 
   
     ![][8]
6. Uruchom aplikację na urządzeniu skojarzonym z tym profilem publikowania. 

[!INCLUDE [mobile-engagement-ios-send-push-push](../../includes/mobile-engagement-ios-send-push.md)]

<!-- Images. -->
[1]: ./media/mobile-engagement-xamarin-ios-get-started/new-solution.png
[2]: ./media/mobile-engagement-xamarin-ios-get-started/app-type.png
[3]: ./media/mobile-engagement-xamarin-ios-get-started/configure-project-name.png
[4]: ./media/mobile-engagement-xamarin-ios-get-started/configure-project-confirm.png
[5]: ./media/mobile-engagement-xamarin-ios-get-started/add-nuget.png
[6]: ./media/mobile-engagement-xamarin-ios-get-started/add-nuget-azme.png
[7]: ./media/mobile-engagement-xamarin-ios-get-started/info-plist-confirm-bundle.png
[8]: ./media/mobile-engagement-xamarin-ios-get-started/info-plist-configure-push.png
