---
title: Wprowadzenie do usługi Azure Mobile Engagement na potrzeby platformy Xamarin.Android
description: Dowiedz się, jak używać usługi Azure Mobile Engagement z funkcją analizy i powiadomieniami wypychanymi na potrzeby aplikacji platformy Xamarin.Android.
services: mobile-engagement
documentationcenter: xamarin
author: piyushjo
manager: erikre
editor: ''
ms.assetid: fb68cf98-08a2-41b5-8e59-757469de3fe7
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 06/16/2016
ms.author: piyushjo
ms.openlocfilehash: 4ac6e85a0143fc083c22a92e99503689f463d813
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="get-started-with-azure-mobile-engagement-for-xamarinandroid-apps"></a>Rozpoczynanie pracy z usługą Azure Mobile Engagement na potrzeby aplikacji platformy Xamarin.Android
> [!IMPORTANT]
> Usługa Azure Mobile Engagement zostanie wycofana w dniu 31-03-2018. Ta strona zostanie usunięta wkrótce potem.
> 

[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

W tym temacie pokazano, jak za pomocą usługi Azure Mobile Engagement można określić sposób użycia aplikacji oraz wysyłać powiadomienia wypychane do segmentowanych użytkowników aplikacji platformy Xamarin.Android.
W tym samouczku został omówiony prosty scenariusz emisji przy użyciu usługi Mobile Engagement. W ramach tego samouczka zostanie utworzona pusta aplikacja platformy Xamarin.Android służąca do zbierania danych podstawowych i odbierania powiadomień wypychanych przy użyciu usługi Google Cloud Messaging (GCM).

> [!NOTE]
> Usługa Azure Mobile Engagement zostanie wycofana w marcu 2018 r. i jest obecnie dostępna wyłącznie dla dotychczasowych klientów. Aby uzyskać więcej informacji, zobacz [Mobile Engagement](https://azure.microsoft.com/en-us/services/mobile-engagement/).

Dla tego samouczka wymagane są następujące elementy:

* Program [Xamarin Studio](http://xamarin.com/studio). Można również użyć programu Visual Studio z platformą Xamarin, ale w tym samouczku używany jest program Xamarin Studio. Aby uzyskać instrukcje dotyczące instalowania, zobacz [Instalator i instalacja programu Visual Studio i platformy Xamarin](https://msdn.microsoft.com/library/mt613162.aspx).
* [Zestaw SDK platformy Xamarin usługi Mobile Engagement](https://www.nuget.org/packages/Microsoft.Azure.Engagement.Xamarin/)

> [!NOTE]
> Do wykonania kroków tego samouczka potrzebne jest aktywne konto platformy Azure. Jeśli jej nie masz, możesz utworzyć bezpłatne konto próbne w zaledwie kilka minut. Aby uzyskać szczegółowe informacje, zobacz artykuł [Bezpłatna wersja próbna platformy Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-xamarin-android-get-started).
> 
> 

## <a id="setup-azme"></a>Konfigurowanie usługi Mobile Engagement dla aplikacji systemu Android
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a id="connecting-app"></a>Łączenie aplikacji z zapleczem usługi Mobile Engagement
Ten samouczek przedstawia „podstawową integrację”, tj. minimalny zestaw wymagany do zbierania danych i wysyłania powiadomień wypychanych. 

Aby zademonstrować integrację, utworzona zostanie podstawowa aplikacja za pomocą programu Xamarin Studio.

### <a name="create-a-new-xamarinandroid-project"></a>Tworzenie nowego projektu platformy Xamarin.Android
1. Uruchom program **Xamarin Studio**. Wybierz pozycję **File** (Plik) -> **New** (Nowy) -> **Solution** (Rozwiązanie). 
   
    ![][1]
2. Wybierz pozycję **Android App** (Aplikacja systemu Android), upewnij się, że wybrany język to **C#**, a następnie kliknij przycisk **Next** (Dalej).
   
    ![][2]
3. Wypełnij pole **App Name** (Nazwa aplikacji) i **Organization Identifier** (Identyfikator organizacji). Upewnij się, że zaznaczono pole wyboru **Google Play Services** (Usługi Google Play), a następnie kliknij przycisk **Next** (Dalej). 
   
    ![][3]
4. Jeśli to będzie konieczne, zaktualizuj pola **Project Name** (Nazwa projektu), **Solution Name** (Nazwa rozwiązania) i **Location** (Lokalizacja), a następnie kliknij pozycję **Create** (Utwórz).
   
    ![][4]

Za pomocą platformy Xamarin Studio zostanie utworzona aplikacja, w której zostanie zintegrowana usługa Mobile Engagement. 

### <a name="connect-your-app-to-mobile-engagement-backend"></a>Łączenie aplikacji z zapleczem usługi Mobile Engagement
1. Kliknij prawym przyciskiem myszy folder **Packages** w oknie Solution (Rozwiązanie), a następnie wybierz pozycję **Add Packages** (Dodaj pakiety).
   
    ![][5]
2. Wyszukaj zestaw **Microsoft Azure Mobile Engagement Xamarin SDK** i dodaj go do swojego rozwiązania.  
   
    ![][6]
3. Otwórz plik **MainActivity.cs** i dodaj następującą instrukcję using:
   
        using Microsoft.Azure.Engagement;
        using Microsoft.Azure.Engagement.Activity;
4. W metodzie `OnCreate` dodaj następujący kod, aby zainicjować połączenia z zapleczem usługi Mobile Engagement. Upewnij się, że został dodany element **ConnectionString**. 
   
        EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
        engagementConfiguration.ConnectionString = "YourConnectionStringFromAzurePortal";
        EngagementAgent.Init(engagementConfiguration);

### <a name="add-permissions-and-a-service-declaration"></a>Dodawanie uprawnień i deklaracji usługi
1. Otwórz plik **Manifest.xml** w folderze właściwości. Wybierz kartę Source (Źródło), aby bezpośrednio zaktualizować źródło XML.
2. Te uprawnienia są dodawane do pliku Manifest.xml (można go znaleźć w folderze **Properties**) projektu bezpośrednio przed lub po znaczniku `<application>`:
   
        <uses-permission android:name="android.permission.INTERNET"/>
        <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
        <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
        <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
        <uses-permission android:name="android.permission.VIBRATE" />
        <uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION"/>
3. Dodaj następujący kod między tagami `<application>` i `</application>`, aby zadeklarować usługę agenta:
   
        <service
             android:name="com.microsoft.azure.engagement.service.EngagementService"
             android:exported="false"
             android:label="<Your application name>"
             android:process=":Engagement"/>
4. We wklejonym właśnie kodzie zamień element `"<Your application name>"` w etykiecie. Ta wartość jest wyświetlana w menu **Ustawienia**, w którym użytkownicy mogą zobaczyć usługi uruchomione na urządzeniu. Możesz na przykład dodać wyraz „Usługa” w tej etykiecie.

### <a name="send-a-screen-to-mobile-engagement"></a>Wysyłanie ekranu do usługi Mobile Engagement
Aby rozpocząć wysyłanie danych i sprawdzić, czy użytkownicy są aktywni, konieczne jest wysłanie co najmniej jednego ekranu do zaplecza usługi Mobile Engagement. Aby to zrobić, upewnij się, że klasa `MainActivity` dziedziczy z klasy `EngagementActivity`, a nie z klasy `Activity`.

    public class MainActivity : EngagementActivity

Alternatywnie jeśli nie możesz dziedziczyć z klasy `EngagementActivity`, musisz dodać metody `.StartActivity` i `.EndActivity` odpowiednio w `OnResume` i `OnPause`.  

        protected override void OnResume()
            {
                EngagementAgent.StartActivity(EngagementAgentUtils.BuildEngagementActivityName(Java.Lang.Class.FromType(this.GetType())), null);
                base.OnResume();             
            }

            protected override void OnPause()
            {
                EngagementAgent.EndActivity();
                base.OnPause();            
            }

## <a id="monitor"></a>Łączenie aplikacji z funkcją monitorowania w czasie rzeczywistym
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a id="integrate-push"></a>Włączanie powiadomień wypychanych i funkcji komunikatów w aplikacji
Usługa Mobile Engagement umożliwia interakcję z użytkownikami przy użyciu powiadomień wypychanych i komunikatów w aplikacji w kontekście kampanii. Ten moduł w portalu Mobile Engagement ma nazwę REACH.
W poniższych sekcjach aplikacja zostanie skonfigurowana do ich odbierania.

[!INCLUDE [Enable Google Cloud Messaging](../../includes/mobile-engagement-enable-google-cloud-messaging.md)]

[!INCLUDE [Enable in-app messaging](../../includes/mobile-engagement-android-send-push.md)]

[!INCLUDE [Send notification from portal](../../includes/mobile-engagement-android-send-push-from-portal.md)]

<!-- Images -->
[1]: ./media/mobile-engagement-xamarin-android-get-started/1.png
[2]: ./media/mobile-engagement-xamarin-android-get-started/2.png
[3]: ./media/mobile-engagement-xamarin-android-get-started/3.png
[4]: ./media/mobile-engagement-xamarin-android-get-started/4.png
[5]: ./media/mobile-engagement-xamarin-android-get-started/5.png
[6]: ./media/mobile-engagement-xamarin-android-get-started/6.png
