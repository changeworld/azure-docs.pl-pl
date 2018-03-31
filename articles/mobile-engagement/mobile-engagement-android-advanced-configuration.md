---
title: Konfiguracja zaawansowana dla zestawem Azure Mobile Engagement Android SDK
description: W tym artykule opisano opcje konfiguracji zaawansowanej, łącznie z manifestu systemu Android z zestawem Azure Mobile Engagement Android SDK
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 37d2c09a-86fa-473d-8987-c7e35a0eb3e8
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 10/04/2016
ms.author: piyushjo;ricksal
ms.openlocfilehash: 29a7bb7daae59e2034504ce27c9ba66755b11e4b
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="advanced-configuration-for-azure-mobile-engagement-android-sdk"></a>Konfiguracja zaawansowana dla zestawem Azure Mobile Engagement Android SDK
> [!IMPORTANT]
> Usługa Azure Mobile Engagement wycofaniu na 3/31/2018. Ta strona zostanie usunięta wkrótce po.
> 

> [!div class="op_single_selector"]
> * [Platforma uniwersalna systemu Windows](mobile-engagement-windows-store-advanced-configuration.md)
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
> * [iOS](mobile-engagement-ios-integrate-engagement.md)
> * [Android](mobile-engagement-android-advanced-configuration.md)
>
>

W tej procedurze opisano sposób konfigurowania różnych opcji konfiguracji dla usługi Azure Mobile Engagement aplikacji systemu Android.

## <a name="prerequisites"></a>Wymagania wstępne
[!INCLUDE [Prereqs](../../includes/mobile-engagement-android-prereqs.md)]

## <a name="permission-requirements"></a>Wymagania dotyczące uprawnień
Niektóre opcje wymaga określonych uprawnień, które są wymienione wszystkie tutaj odwołania i wiersza w określonej funkcji. Dodaj te uprawnienia do pliku AndroidManifest.xml projektu bezpośrednio przed lub po `<application>` tagu.

Kod uprawnień musi wyglądać podobnie do poniższego, w którym Wypełnij odpowiednie uprawnienia z poniższą tabelą.

    <uses-permission android:name="android.permission.[specific permission]"/>


| Uprawnienie | Gdy jest używany |
| --- | --- |
| INTERNET |Wymagany. Na podstawowym raportowaniem |
| ACCESS_NETWORK_STATE |Wymagany. Na podstawowym raportowaniem |
| RECEIVE_BOOT_COMPLETED |Wymagany. Wyświetlani Centrum powiadomień po ponownym uruchomieniu urządzenia |
| WAKE_LOCK |Zalecane. Umożliwia zbieranie danych przy użyciu sieci Wi-Fi lub gdy ekran jest wyłączony |
| WŁĄCZ WIBRACJĘ |Opcjonalny. Włącza wibrację po otrzymaniu powiadomienia |
| DOWNLOAD_WITHOUT_NOTIFICATION |Opcjonalny. Włącza powiadomień systemu Android duży obraz. |
| WRITE_EXTERNAL_STORAGE |Opcjonalny. Włącza powiadomień systemu Android duży obraz. |
| ACCESS_COARSE_LOCATION |Opcjonalny. Włącza raportowanie lokalizacji w czasie rzeczywistym |
| ACCESS_FINE_LOCATION |Opcjonalny. Włącza raportowanie oparte na GPS lokalizacji |

Począwszy od systemu Android M [niektóre uprawnienia są zarządzane w czasie wykonywania](mobile-engagement-android-location-reporting.md#android-m-permissions).

Jeśli korzystasz już z ``ACCESS_FINE_LOCATION``, a następnie nie należy również użyć ``ACCESS_COARSE_LOCATION``.

## <a name="android-manifest-configuration-options"></a>Opcje konfiguracji manifestu systemu android
### <a name="crash-report"></a>Raport o awarii
Aby wyłączyć raporty awarii, Dodaj ten kod między `<application>` i `</application>` tagów:

    <meta-data android:name="engagement:reportCrash" android:value="false"/>

### <a name="burst-threshold"></a>Próg serii
Domyślnie raporty usługi Engagement rejestruje się w czasie rzeczywistym. Jeśli dzienniki raportu Twojej aplikacji różnią się często, lepiej jest buforu dzienniki i raportuj je w całości na regularne podstawy czasu (nazywanych "Tryb szybki"). Aby to zrobić, Dodaj ten kod między `<application>` i `</application>` tagów:

    <meta-data android:name="engagement:burstThreshold" android:value="{interval between too bursts (in milliseconds)}"/>

Tryb serii nieco zwiększa czas pracy baterii, ale ma wpływ na monitorowanie usługi Engagement: wszystkie czas trwania sesji i zadań są zaokrąglane do serii wartość progową (w związku z tym sesji i zadania krótsze niż próg serii może nie być widoczna). Próg z serii powinna być dłuższa niż 30000 (30s).

### <a name="session-timeout"></a>Limit czasu sesji
 Działanie może być zakończona, naciskając klawisz **Home** lub **ponownie** klucza przez ustawienie bezczynności telefonu lub przejście do innej aplikacji. Domyślnie jest zakończenie sesji dziesięć sekund po zakończeniu jego ostatniej aktywności. Pozwala to uniknąć podziału sesji zawsze użytkownik opuszcza i zwraca do aplikacji szybkie, która może wystąpić, gdy użytkownik wybiera obrazu, sprawdza powiadomienia itp. Można modyfikować tego parametru. Aby to zrobić, Dodaj ten kod między `<application>` i `</application>` tagów:

    <meta-data android:name="engagement:sessionTimeout" android:value="{session timeout (in milliseconds)}"/>

## <a name="disable-log-reporting"></a>Wyłączanie dziennika raportowania
### <a name="using-a-method-call"></a>Przy użyciu wywołania metody
Jeśli chcesz zaangażowania, aby zatrzymać wysyłanie dzienników można wywołać:

    EngagementAgent.getInstance(context).setEnabled(false);

To wywołanie jest trwały: używa plików udostępnionych preferencji.

Jeśli zaangażowania jest aktywny, gdy wywołanie tej funkcji, może zająć jedną minutę na zatrzymanie usługi. Jednak go nie Uruchom usługę na wszystkich przy następnym uruchomieniu aplikacji.

Możesz włączyć ponownie raportowania przez wywołanie tej samej funkcji z dziennika `true`.

### <a name="integration-in-your-own-preferenceactivity"></a>Integracja z własną `PreferenceActivity`
Zamiast wywoływania tej funkcji, to ustawienie można również zintegrować bezpośrednio w istniejącą `PreferenceActivity`.

Zaangażowania, aby użyć pliku preferencji (z odpowiednią tryb) można skonfigurować w `AndroidManifest.xml` pliku z `application meta-data`:

* `engagement:agent:settings:name` Klucz służy do definiowania nazwę pliku udostępnionych preferencji.
* `engagement:agent:settings:mode` Klucz służy do definiowania trybu plików udostępnionych preferencji. Użyj tego samego trybu jako w Twojej `PreferenceActivity`. Tryb muszą być przekazywane w postaci liczby: Jeśli korzystasz z kombinacją flag stałej w kodzie, sprawdź wartość całkowita.

Zaangażowania zawsze używa `engagement:key` logiczna klucz w pliku preferencji zarządzania to ustawienie.

Poniższy przykład przedstawia `AndroidManifest.xml` przedstawiono wartości domyślne:

    <application>
        [...]
        <meta-data
          android:name="engagement:agent:settings:name"
          android:value="engagement.agent" />
        <meta-data
          android:name="engagement:agent:settings:mode"
          android:value="0" />

Następnie można dodać `CheckBoxPreference` w układzie preferencji podobny do następującego:

    <CheckBoxPreference
      android:key="engagement:enabled"
      android:defaultValue="true"
      android:title="Use Engagement"
      android:summaryOn="Engagement is enabled."
      android:summaryOff="Engagement is disabled." />
