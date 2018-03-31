---
title: Lokalizacja raportowania dla usługi Azure Mobile Engagement Android SDK
description: Opisuje sposób konfigurowania lokalizacji raportowanie dla zestawem Azure Mobile Engagement Android SDK
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 6cab5ed1-b767-46ac-9f0b-48a4e249d88c
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 08/12/2016
ms.author: piyushjo;ricksal
ms.openlocfilehash: 7978010bd92af18e3ab7c8ccab8dc682d7ef18df
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="location-reporting-for-azure-mobile-engagement-android-sdk"></a>Lokalizacja raportowania dla usługi Azure Mobile Engagement Android SDK
> [!IMPORTANT]
> Usługa Azure Mobile Engagement wycofaniu na 3/31/2018. Ta strona zostanie usunięta wkrótce po.
> 

> [!div class="op_single_selector"]
> * [Android](mobile-engagement-android-integrate-engagement.md)
> 
> 

W tym temacie opisano sposób wykonywania lokalizacji raportowanie dla aplikacji systemu Android.

## <a name="prerequisites"></a>Wymagania wstępne
[!INCLUDE [Prereqs](../../includes/mobile-engagement-android-prereqs.md)]

## <a name="location-reporting"></a>Raportowanie lokalizacji
Jeśli chcesz lokalizacje, które mają być zgłaszane należy dodać kilka wierszy konfiguracji (między `<application>` i `</application>` tagów).

### <a name="lazy-area-location-reporting"></a>Raportowanie lokalizacji obszaru z opóźnieniem
Raportowanie lokalizacji obszaru z opóźnieniem umożliwia raportowanie kraju, region i lokalizację skojarzone z urządzeń. Raportowanie lokalizacji tego typu używa tylko lokalizacje sieciowe (na podstawie Identyfikatora komórki lub Wi-Fi). Obszar urządzenia są zgłaszane co najwyżej raz na sesję. GPS nigdy nie jest używany, a w związku z tym tego typu lokalizacji raportu ma niski wpływ na baterii.

Obszary zgłoszone są używane do obliczeniowe geograficzne statystyki dotyczące użytkowników, sesji zdarzenia i błędy. Mogą one również używane jako kryterium w kampanie Zasięgowe.

Możesz włączyć lokalizacji obszaru z opóźnieniem raportowania przy użyciu konfiguracji wymienione wcześniej w tej procedurze:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setLazyAreaLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

Należy również określić uprawnienie lokalizacji. Ten kod zawiera ``COARSE`` uprawnienia:

    <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

Jeśli aplikacja wymaga, możesz użyć ``ACCESS_FINE_LOCATION`` zamiast tego.

### <a name="real-time-location-reporting"></a>Raportowanie lokalizacji w czasie rzeczywistym
Raportowanie lokalizacji w czasie rzeczywistym umożliwia raportowanie współrzędne geograficzne skojarzone z urządzeń. Domyślnie raportowanie lokalizacji tego typu używa tylko lokalizacje sieciowe, na podstawie Identyfikatora komórki lub Wi-Fi. Raportowania jest aktywna tylko po uruchomieniu aplikacji na pierwszym planie (na przykład podczas sesji).

W czasie rzeczywistym lokalizacje są *nie* używany do obliczania statystyk. Ich jedynym celem jest, aby zezwolić na użycie grodzenia w czasie rzeczywistym \<Reach-odbiorców geofencing\> kryterium w kampanie Zasięgowe.

Aby włączyć lokalizacji w czasie rzeczywistym, raportowanie, Dodaj wiersz kodu do których wartość parametrów połączenia usługi Engagement w działaniu uruchamiania. Wynik wygląda następująco:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

        You also need to specify a location permission. This code uses ``COARSE`` permission:

            <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

        If your app requires it, you can use ``ACCESS_FINE_LOCATION`` instead.

#### <a name="gps-based-reporting"></a>GPS na podstawie raportowania
Domyślnie raportowanie lokalizacji w czasie rzeczywistym używa tylko lokalizacje sieciowe. Aby włączyć korzystanie z lokalizacji na podstawie GPS, czyli znacznie bardziej dokładne, użyj obiektu konfiguracji:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    engagementConfiguration.setFineRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

Należy również dodać następujące uprawnienia, jeśli brakuje:

    <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>

#### <a name="background-reporting"></a>Raportowanie w tle
Domyślnie raportowanie lokalizacji w czasie rzeczywistym jest aktywne po uruchomieniu aplikacji na pierwszym planie (na przykład podczas sesji). Aby włączyć raportowanie również, w tle, użyj tego obiektu konfiguracji:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    engagementConfiguration.setBackgroundRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

> [!NOTE]
> Po uruchomieniu aplikacji w tle, tylko lokalizacje sieciowe są raportowane, nawet jeśli włączono GPS.
> 
> 

Jeśli użytkownik uruchamia swojego urządzenia, lokalizacji raportu tła jest zatrzymana. Aby umożliwić automatyczne ponowne uruchomienie przy rozruchu, Dodaj ten kod.

    <receiver android:name="com.microsoft.azure.engagement.EngagementLocationBootReceiver"
           android:exported="false">
        <intent-filter>
            <action android:name="android.intent.action.BOOT_COMPLETED" />
        </intent-filter>
    </receiver>

Należy również dodać następujące uprawnienia, jeśli brakuje:

    <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />

## <a name="android-m-permissions"></a>Android M uprawnień
Począwszy od systemu Android M, niektóre uprawnienia są zarządzane w czasie wykonywania i wymagają zatwierdzenia użytkownika.

Interfejs API systemu Android na poziomie 23 w przypadku skierowania, uprawnienia środowiska uruchomieniowego są domyślnie wyłączona w przypadku nowych instalacji aplikacji. W przeciwnym razie są włączone domyślnie.

Użytkownik może Włączanie/wyłączanie te uprawnienia z menu ustawień urządzenia. Wyłączenie uprawnień z menu systemowego kasuje procesów w tle aplikacji, która jest zachowanie systemowe i nie ma wpływu na możliwość odbierania wypychania w tle.

W kontekście lokalizacji usługi Mobile Engagement raportowania są uprawnienia, które wymagają zatwierdzenia w czasie wykonywania:

* `ACCESS_COARSE_LOCATION`
* `ACCESS_FINE_LOCATION`

Zażądaj uprawnień od użytkownika, korzystając z okna dialogowego standardowy system. Jeśli użytkownik zaakceptuje, poinformuj ``EngagementAgent`` podjęcie zmiana pod uwagę w czasie rzeczywistym. W przeciwnym razie zmiany są przetwarzane przy następnym użytkownik uruchamia aplikację.

Oto przykład kodu do użycia w działaniu aplikacji, aby zażądać uprawnień i przekazuje wynik w przypadku wartości dodatniej do ``EngagementAgent``:

    @Override
    public void onCreate(Bundle savedInstanceState)
    {
      /* Other code... */

      /* Request permissions */
      requestPermissions();
    }

    @TargetApi(Build.VERSION_CODES.M)
    private void requestPermissions()
    {
      /* Avoid crashing if not on Android M */
      if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M)
      {
        /*
         * Request location permission, but this doesn't explain why it is needed to the user.
         * The standard Android documentation explains with more details how to display a rationale activity to explain the user why the permission is needed in your application.
         * Putting COARSE vs FINE has no impact here, they are part of the same group for runtime permission management.
         */
        if (checkSelfPermission(android.Manifest.permission.ACCESS_FINE_LOCATION) != PackageManager.PERMISSION_GRANTED)
          requestPermissions(new String[] { android.Manifest.permission.ACCESS_FINE_LOCATION }, 0);

      }
    }

    @Override
    public void onRequestPermissionsResult(int requestCode, String[] permissions, int[] grantResults)
    {
      /* Only a positive location permission update requires engagement agent refresh, hence the request code matching from above function */
      if (requestCode == 0 && grantResults[0] == PackageManager.PERMISSION_GRANTED)
        getEngagementAgent().refreshPermissions();
    }
