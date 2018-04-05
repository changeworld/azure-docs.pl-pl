---
title: Wprowadzenie do usługi Azure Mobile Engagement dla aplikacji systemu Android
description: Dowiedz się, jak używać usługi Azure Mobile Engagement z funkcją analizy i powiadomieniami wypychanymi na potrzeby aplikacji systemu Android.
services: mobile-engagement
documentationcenter: android
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 3c286c6d-cfef-4e3e-9b2c-715429fe82db
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: hero-article
ms.date: 08/10/2016
ms.author: piyushjo;ricksal
ms.openlocfilehash: 4eda8129e03e4880e168f7c154c7236da028e81e
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="get-started-with-azure-mobile-engagement-for-android-apps"></a>Wprowadzenie do usługi Azure Mobile Engagement dla aplikacji systemu Android
> [!IMPORTANT]
> Usługa Azure Mobile Engagement zostanie wycofana w dniu 31-03-2018. Ta strona zostanie usunięta wkrótce potem.
> 


[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

W tym temacie pokazano, jak za pomocą usługi Azure Mobile Engagement można określić sposób użycia aplikacji oraz wysyłać powiadomienia wypychane do określonych segmentów użytkowników aplikacji systemu Android.
W tym samouczku został omówiony prosty scenariusz emisji przy użyciu usługi Mobile Engagement. W tym samouczku zostanie utworzona pusta aplikacja systemu Android służąca do zbierania podstawowych danych i odbierania powiadomień wypychanych przy użyciu usługi Google Cloud Messaging (GCM).

## <a name="prerequisites"></a>Wymagania wstępne
Wykonanie kroków tego samouczka wymaga [narzędzi dla deweloperów systemu Android](https://developer.android.com/sdk/index.html), które obejmują zintegrowane środowisko projektowe Android Studio, oraz najnowszej platformy Android.

Wymagany jest również [zestaw Mobile Engagement Android SDK](https://aka.ms/vq9mfn).

> [!IMPORTANT]
> Do wykonania kroków tego samouczka jest potrzebne aktywne konto platformy Azure. Jeśli jej nie masz, możesz utworzyć bezpłatne konto próbne w zaledwie kilka minut. Aby uzyskać szczegółowe informacje, zobacz artykuł [Bezpłatna wersja próbna platformy Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-android-get-started).
>
>

## <a name="set-up-mobile-engagement-for-your-android-app"></a>Konfigurowanie usługi Mobile Engagement dla aplikacji systemu Android
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a name="connect-your-app-to-the-mobile-engagement-backend"></a>Łączenie aplikacji z zapleczem usługi Mobile Engagement
Ten samouczek przedstawia „podstawową integrację”, tj. minimalny zestaw wymagany do zbierania danych i wysyłania powiadomień wypychanych. Aby zademonstrować integrację, utworzysz podstawową aplikację za pomocą programu Android Studio.

Kompletna dokumentacja integracji znajduje się w sekcji [Mobile Engagement Android SDK integration](mobile-engagement-android-sdk-overview.md) (Integracja z zestawem Mobile Engagement Android SDK).

### <a name="create-an-android-project"></a>Tworzenie projektu systemu Android
1. Uruchom program **Android Studio** i w oknie podręcznym wybierz pozycję **Start a new Android Studio project** (Utwórz nowy projekt programu Android Studio).

    ![][1]
2. Podaj nazwę aplikacji i domenę firmy. Zanotuj te informacje, ponieważ będą one potrzebne później. Kliknij przycisk **Dalej**.

    ![][2]
3. Wybierz docelowy typ urządzeń i poziom interfejsu API, a następnie kliknij przycisk **Next** (Dalej).

   > [!NOTE]
   > Usługa Mobile Engagement wymaga co najmniej interfejsu API na poziomie 10 (Android 2.3.3).
   >
   >

    ![][3]
4. Wybierz tutaj pozycję **Blank Activity** (Puste działanie), które jest jedynym ekranem dla tej aplikacji, a następnie kliknij przycisk **Next** (Dalej).

    ![][4]
5. Pozostaw wartości domyślne, a następnie kliknij pozycję **Finish** (Zakończ).

    ![][5]

W programie Android Studio zostanie utworzona aplikacja demonstracyjna, z którą będzie zintegrowana usługa Mobile Engagement.

### <a name="include-the-sdk-library-in-your-project"></a>Dołączanie biblioteki SDK do projektu
1. Pobierz zestaw [Mobile Engagement Android SDK](https://aka.ms/vq9mfn).
2. Wypakuj plik archiwum do folderu na swoim komputerze.
3. Ustal bibliotekę jar dla bieżącej wersji tego zestawu SDK, a następnie skopiuj ją do Schowka.

      ![][6]
4. Przejdź do sekcji **Project** (Projekt) (1) i wklej plik jar w folderze libs (2).

      ![][7]
5. Aby załadować bibliotekę, zsynchronizuj projekt.

      ![][8]

### <a name="connect-your-app-to-mobile-engagement-backend-with-the-connection-string"></a>Łączenie aplikacji z zapleczem usługi Mobile Engagement za pomocą parametrów połączenia
1. Skopiuj następujące wiersze kodu do lokalizacji tworzenia działania (należy to zrobić tylko w jednym miejscu aplikacji, zwykle w głównym działaniu). Dla tej aplikacji przykładowej otwórz plik MainActivity w folderze src -> main -> java i dodaj następujący kod:

        EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
        engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
        EngagementAgent.getInstance(this).init(engagementConfiguration);
2. Rozpoznaj odwołania, naciskając klawisze Alt + Enter lub dodając następujące instrukcje importu:

        import com.microsoft.azure.engagement.EngagementAgent;
        import com.microsoft.azure.engagement.EngagementConfiguration;
3. Wróć do witryny Azure Portal na stronie **Informacje o połączeniu** swojej aplikacji i skopiuj **parametry połączenia**.

      ![](../../includes/media/mobile-engagement-create-app-in-portal-new/app-connection-info.png)

4. Wklej ją do parametru `setConnectionString`, zastępując cały ciąg pokazany w poniższym kodzie:

        engagementConfiguration.setConnectionString("Endpoint=my-company-name.device.mobileengagement.windows.net;SdkKey=********************;AppId=*********");

### <a name="add-permissions-and-a-service-declaration"></a>Dodawanie uprawnień i deklaracji usługi
1. Dodaj te uprawnienia do pliku Manifest.xml projektu bezpośrednio przed tagiem `<application>` lub po nim:

        <uses-permission android:name="android.permission.INTERNET"/>
        <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
        <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
        <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
        <uses-permission android:name="android.permission.VIBRATE" />
        <uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION"/>
2. Aby zadeklarować usługę agenta, dodaj ten kod między tagami `<application>` i `</application>`:

        <service
             android:name="com.microsoft.azure.engagement.service.EngagementService"
             android:exported="false"
             android:label="<Your application name>"
             android:process=":Engagement"/>
3. We wklejonym kodzie zastąp tag `"<Your application name>"` w etykiecie wyświetlanej w menu **Ustawienia**, w którym można zobaczyć usługi uruchomione na urządzeniu. Możesz na przykład dodać wyraz „Usługa” w tej etykiecie.

### <a name="send-a-screen-to-mobile-engagement"></a>Wysyłanie ekranu do usługi Mobile Engagement
Aby rozpocząć wysyłanie danych i upewnić się, że użytkownicy są aktywni, konieczne jest wysłanie co najmniej jednego ekranu (Działanie) do zaplecza usługi Mobile Engagement.

Przejdź do pliku **MainActivity.java** i dodaj następujący kod, aby zastąpić klasę bazową klasy **MainActivity** klasą **EngagementActivity**:

    public class MainActivity extends EngagementActivity {

> [!NOTE]
> Jeśli klasą bazową nie jest *Activity*, zapoznaj się z artykułem [Advanced Android Reporting](mobile-engagement-android-advanced-reporting.md) (Zaawansowane raportowanie w systemie Android), aby poznać sposób dziedziczenia z różnych klas.
>
>

Oznacz jako komentarz następujący wiersz w tym prostym scenariuszu przykładowym:

    // setSupportActionBar(toolbar);

Jeśli chcesz zachować `ActionBar` w aplikacji, zobacz [Zaawansowane raportowanie w systemie Android](mobile-engagement-android-advanced-reporting.md).

## <a name="connect-app-with-real-time-monitoring"></a>Łączenie aplikacji z funkcją monitorowania w czasie rzeczywistym
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a name="enable-push-notifications-and-in-app-messaging"></a>Włączanie powiadomień wypychanych i funkcji komunikatów w aplikacji
W czasie kampanii usługa Mobile Engagement umożliwia interakcję z użytkownikami przy użyciu powiadomień wypychanych i komunikatów w aplikacji. Ten moduł w portalu Mobile Engagement ma nazwę REACH.
W poniższej sekcji aplikacja zostanie skonfigurowana do ich odbierania.

### <a name="copy-sdk-resources-in-your-project"></a>Kopiowanie zasobów zestawu SDK w projekcie
1. Przejdź wstecz do zawartości pobierania zestawu SDK i skopiuj folder **res**.

    ![][10]
2. Wróć do programu Android Studio, wybierz katalog **main** plików projektu, a następnie wklej go, aby dodać zasoby do projektu.

    ![][11]

[!INCLUDE [Enable Google Cloud Messaging](../../includes/mobile-engagement-enable-google-cloud-messaging.md)]

[!INCLUDE [Enable in-app messaging](../../includes/mobile-engagement-android-send-push.md)]

[!INCLUDE [Send notification from portal](../../includes/mobile-engagement-android-send-push-from-portal.md)]

## <a name="next-steps"></a>Następne kroki
Przejdź do sekcji [Android SDK](mobile-engagement-android-sdk-overview.md) (Zestaw Android SDK), aby uzyskać szczegółową wiedzę na temat integracji zestawu SDK.

<!-- Images. -->
[1]: ./media/mobile-engagement-android-get-started/android-studio-new-project.png
[2]: ./media/mobile-engagement-android-get-started/android-studio-project-props.png
[3]: ./media/mobile-engagement-android-get-started/android-studio-project-props2.png
[4]: ./media/mobile-engagement-android-get-started/android-studio-add-activity.png
[5]: ./media/mobile-engagement-android-get-started/android-studio-activity-name.png
[6]: ./media/mobile-engagement-android-get-started/sdk-content.png
[7]: ./media/mobile-engagement-android-get-started/paste-jar.png
[8]: ./media/mobile-engagement-android-get-started/sync-project.png
[9]: ./media/mobile-engagement-android-get-started/app-connection-info-page.png
[10]: ./media/mobile-engagement-android-get-started/copy-resources.png
[11]: ./media/mobile-engagement-android-get-started/paste-resources.png
