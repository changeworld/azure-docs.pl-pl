---
title: Integracja zestawu SDK systemu Android z usługi Azure Mobile Engagement
description: Najnowsze aktualizacje i procedury dotyczące zestawu SDK systemu Android dla usługi Azure Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: a5487793-1a12-4f6c-a1cf-587c5a671e6b
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: fffff6de996b8295639b3d595c5f778de8a0f74f
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-integrate-engagement-on-android"></a>Integrowanie usługi Engagement w systemie Android
> [!IMPORTANT]
> Usługa Azure Mobile Engagement wycofaniu na 3/31/2018. Ta strona zostanie usunięta wkrótce po.
> 

> [!div class="op_single_selector"]
> * [Aplikacje uniwersalne systemu Windows](mobile-engagement-windows-store-integrate-engagement.md)
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
> * [iOS](mobile-engagement-ios-integrate-engagement.md)
> * [Android](mobile-engagement-android-integrate-engagement.md)
> 
> 

W tej procedurze opisano Najprostszym sposobem, aby aktywować analizy i funkcji w aplikacji systemu Android monitorowania usługi Engagement.

> [!IMPORTANT]
> Minimalny poziom interfejsu API zestawu SDK systemu Android musi być 10 lub nowszej (Android 2.3.3 lub nowszej).
> 
> 

Poniższe kroki są wystarczająca liczba na aktywuje raport dzienniki wymagane do obliczenia wszystkich statystyki dotyczące użytkowników, sesji, działania, awarii (Crash) i Technicals. Raport dzienniki wymagane do obliczenia innych danych statystycznych, takich jak zdarzenia i błędy zadań musi zostać wykonane ręcznie przy użyciu interfejsu API usługi Engagement (zobacz [sposobu korzystania z zaawansowanych Mobile Engagement znakowanie interfejsu API w dla systemu Android](mobile-engagement-android-use-engagement-api.md) ponieważ te statystyki są aplikacji zależnej.

## <a name="embed-the-engagement-sdk-and-service-into-your-android-project"></a>Osadź Engagement SDK i usługi do projektu systemu Android
Pobierz zestaw SDK systemu Android z [tutaj](https://aka.ms/vq9mfn) uzyskać `mobile-engagement-VERSION.jar` i umieść je w `libs` folderu projektu systemu Android (Utwórz folder biblioteki, jeśli jeszcze nie istnieje).

> [!IMPORTANT]
> W przypadku tworzenia pakietu aplikacji z narzędzia ProGuard, należy zachować niektóre klasy. Możesz użyć następującego fragmentu kodu konfiguracji:
> 
> -keep public class * extends android.os.IInterface -keep class com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity$EngagementReachContentJS {
> 
> <methods>; }
> 
> 

Określ ciąg połączenia zaangażowania przez wywołanie następującej metody w działaniu uruchamiania:

            EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
            engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
            EngagementAgent.getInstance(this).init(engagementConfiguration);

Ciąg połączenia dla aplikacji jest wyświetlana w portalu Azure.

* Jeśli brakuje, należy dodać następujące uprawnienia dla systemu Android (przed `<application>` tagu):
  
          <uses-permission android:name="android.permission.INTERNET"/>
          <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
* Dodaj poniższą sekcję (między `<application>` i `</application>` tagów):
  
          <service
            android:name="com.microsoft.azure.engagement.service.EngagementService"
            android:exported="false"
            android:label="<Your application name>Service"
            android:process=":Engagement"/>
* Zmień `<Your application name>` według nazwy aplikacji.

> [!TIP]
> `android:label` Atrybutu umożliwia wybór nazwy usługi Engagement, jak będzie wyświetlany użytkownikom końcowym na ekranie telefon "Uruchamianie usługi". Zalecane jest aby ustawić wartość tego atrybutu `"<Your application name>Service"` (np. `"AcmeFunGameService"`).
> 
> 

Określanie `android:process` atrybutu zapewnia, że usługi Engagement będą uruchamiane w swoim własnym procesie (uruchamianie zaangażowania w ramach tego samego procesu, zgodnie z aplikacji spowoduje, że Twoje main/wątku interfejsu użytkownika będzie potencjalnie mniej dynamiczne).

> [!NOTE]
> Kod umieszczony w `Application.onCreate()` i innych wywołań zwrotnych aplikacji zostanie uruchomione dla procesów wszystkich aplikacji, w tym usługi Engagement. Może mieć niepożądane skutki uboczne (takich jak przydziału pamięci niepotrzebnych i wątków w procesie stopnia zaangażowania, zduplikowany odbiorniki emisji lub usługi).
> 
> 

Jeśli można zastąpić `Application.onCreate()`, zaleca się Dodaj poniższy fragment kodu na początku Twojej `Application.onCreate()` funkcji:

             public void onCreate()
             {
               if (EngagementAgentUtils.isInDedicatedEngagementProcess(this))
                 return;

               ... Your code...
             }

Tak samo postąpić w `Application.onTerminate()`, `Application.onLowMemory()` i `Application.onConfigurationChanged(...)`.

Można rozszerzać `EngagementApplication` zamiast rozszerzanie `Application`: wywołanie zwrotne `Application.onCreate()` jest wyboru procesu i wywołania `Application.onApplicationProcessCreate()` tylko jeśli bieżący proces nie jest obsługującym usługę zaangażowania, te same zasady poprosić o innych wywołań zwrotnych.

## <a name="basic-reporting"></a>Podstawowe raportowanie
### <a name="recommended-method-overload-your-activity-classes"></a>Zalecana metoda: przeciążenia sieci `Activity` klas
Aby aktywować raportu wszystkie dzienniki wymagane przez zaangażowania można obliczyć użytkowników, sesji, działania, awarii (Crash) i statystyki technicznych, po prostu należy wszystkie Twoje `*Activity` klasy podrzędne dziedziczą odpowiadającego `Engagement*Activity` klasy (np. Jeśli zwiększa działanie starszej wersji `ListActivity`, rozszerza upewnij `EngagementListActivity`).

**Bez usługi Engagement:**

            package com.company.myapp;

            import android.app.Activity;
            import android.os.Bundle;

            public class MyApp extends Activity
            {
              @Override
              public void onCreate(Bundle savedInstanceState)
              {
                super.onCreate(savedInstanceState);
                setContentView(R.layout.main);
              }
            }

**Z usługi Engagement:**

            package com.company.myapp;

            import com.microsoft.azure.engagement.activity.EngagementActivity;
            import android.os.Bundle;

            public class MyApp extends EngagementActivity
            {
              @Override
              public void onCreate(Bundle savedInstanceState)
              {
                super.onCreate(savedInstanceState);
                setContentView(R.layout.main);
              }
            }

> [!IMPORTANT]
> Korzystając z `EngagementListActivity` lub `EngagementExpandableListActivity`, upewnij się, że wszelkie wywołanie `requestWindowFeature(...);` staje się przed wywołaniem do `super.onCreate(...);`, w przeciwnym razie wystąpi awaria.
> 
> 

Te klasy w można znaleźć `src` folderu i skopiować je do projektu. Klasy są również w **JavaDoc**.

### <a name="alternate-method-call-startactivity-and-endactivity-manually"></a>Alternatywna metoda: wywołanie `startActivity()` i `endActivity()` ręcznie
Jeśli nie możesz lub nie było przeciążyć Twojej `Activity` klas, można zamiast tego rozpoczęcia i zakończenia działań przez wywołanie metody `EngagementAgent`przez metody bezpośrednio.

> [!IMPORTANT]
> Zestaw SDK systemu Android nigdy nie wywołuje `endActivity()` metody, nawet wtedy, gdy aplikacja zostanie zamknięta (w systemie Android, aplikacje są faktycznie nigdy nie zamknięte). W związku z tym jest *wysokiej* zaleca, aby wywołać `startActivity()` metody w `onResume` wywołania zwrotnego z *wszystkie* działaniami i `endActivity()` metody w `onPause()` wywołania zwrotnego z *wszystkie* działań. Jest to jedyny sposób należy upewnić się, że sesje nie zostaną ujawnione. Jeśli przeciek sesji usługi Engagement nigdy nie spowoduje rozłączenie z zapleczem usługi Engagement (ponieważ usługa połączono tak długo, jak długo trwa oczekiwanie na sesji).
> 
> 

Oto przykład:

            public class MyActivity extends Some3rdPartyActivity
            {
              @Override
              protected void onResume()
              {
                super.onResume();
                String activityNameOnEngagement = EngagementAgentUtils.buildEngagementActivityName(getClass()); // Uses short class name and removes "Activity" at the end.
                EngagementAgent.getInstance(this).startActivity(this, activityNameOnEngagement, null);
              }

              @Override
              protected void onPause()
              {
                super.onPause();
                EngagementAgent.getInstance(this).endActivity();
              }
            }

W tym przykładzie są bardzo podobne do `EngagementActivity` klasy i jej wariantów, którego kod źródłowy jest udostępniany w `src` folderu.

## <a name="test"></a>Testowanie
Teraz Sprawdź, czy integracją przez uruchomienie aplikacji mobilnej w emulator lub urządzenie i weryfikowanie rejestruje sesji na karcie Monitor.

Następne sekcje są opcjonalne.

## <a name="location-reporting"></a>Raportowanie lokalizacji
Jeśli chcesz lokalizacje, które mają być zgłaszane należy dodać kilka wierszy konfiguracji (między `<application>` i `</application>` tagów).

### <a name="lazy-area-location-reporting"></a>Raportowanie lokalizacji obszaru z opóźnieniem
Umożliwia raportowanie lokalizacji obszaru z opóźnieniem zgłoszenia kraj, region i lokalizację skojarzone z urządzeniami. Raportowanie lokalizacji tego typu używa tylko lokalizacje sieciowe (na podstawie Identyfikatora komórki lub Wi-Fi). Obszar urządzenia są zgłaszane co najwyżej raz na sesję. GPS nigdy nie jest używana, a w związku z tym ten typ lokalizacji raportu ma bardzo nieliczne (nie musi wypowiedzieć nie) wpływ na baterii.

Obszary zgłoszone są używane do obliczeniowe geograficzne statystyki dotyczące użytkowników, sesji, zdarzenia i błędy. Mogą one również używane jako kryterium w kampanie Zasięgowe.

Aby włączyć raportowanie lokalizacji obszaru z opóźnieniem, należy go za pomocą konfiguracji wymienione wcześniej w tej procedurze:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setLazyAreaLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

Należy również dodać następujące uprawnienia, jeśli brakuje:

            <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

Lub możesz korzystać ``ACCESS_FINE_LOCATION`` Jeśli już używać w aplikacji.

### <a name="real-time-location-reporting"></a>Raportowanie lokalizacji w czasie rzeczywistym
Raportowanie lokalizacji w czasie rzeczywistym umożliwia zgłoszenia współrzędne geograficzne skojarzonego z urządzeń. Domyślnie raportowanie lokalizacji tego typu używa tylko lokalizacje sieciowe (na podstawie Identyfikatora komórki lub Wi-Fi) i raportowania jest aktywne po uruchomieniu aplikacji na pierwszym planie (np. podczas sesji).

Lokalizacje w czasie rzeczywistym *nie* używany do obliczania statystyk. Ich jedynym celem jest, aby zezwolić na użycie grodzenia czasu rzeczywistego \<Reach-odbiorców geofencing\> kryterium w kampanie Zasięgowe.

Aby włączyć raportowanie miejsca w czasie rzeczywistym, należy go za pomocą konfiguracji wymienione wcześniej w tej procedurze:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

Należy również dodać następujące uprawnienia, jeśli brakuje:

            <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

Lub możesz korzystać ``ACCESS_FINE_LOCATION`` Jeśli już używać w aplikacji.

#### <a name="gps-based-reporting"></a>GPS na podstawie raportowania
Domyślnie raportowanie lokalizacji w czasie rzeczywistym używa tylko lokalizacje sieciowe. Aby włączyć korzystanie z GPS na podstawie lokalizacji (które są znacznie bardziej precyzyjne), użyj obiektu konfiguracji:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    engagementConfiguration.setFineRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

Należy również dodać następujące uprawnienia, jeśli brakuje:

            <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>

#### <a name="background-reporting"></a>Raportowanie w tle
Domyślnie raportowanie lokalizacji w czasie rzeczywistym jest aktywne po uruchomieniu aplikacji na pierwszym planie (np. podczas sesji). Aby włączyć raportowanie również, w tle, użyj obiektu konfiguracji:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    engagementConfiguration.setBackgroundRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

> [!NOTE]
> Gdy aplikacja zostanie uruchomiona w tle, tylko do sieci na podstawie lokalizacji są raportowane, nawet jeśli włączono GPS.
> 
> 

Raport lokalizacji tła zostanie zatrzymana, jeśli użytkownik ponownym uruchomieniu urządzenia, można dodać, to aby stał się automatycznie uruchomiony ponownie w czasie rozruchu:

            <receiver android:name="com.microsoft.azure.engagement.EngagementLocationBootReceiver"
               android:exported="false">
               <intent-filter>
                  <action android:name="android.intent.action.BOOT_COMPLETED" />
               </intent-filter>
            </receiver>

Należy również dodać następujące uprawnienia, jeśli brakuje:

            <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />

### <a name="android-m-permissions"></a>Android M uprawnień
Począwszy od systemu Android M, niektóre uprawnienia są zarządzane w czasie wykonywania i wymaga zatwierdzenia użytkownika.

Uprawnienia środowiska uruchomieniowego zostaną wyłączone domyślnie do nowych instalacji aplikacji, jeśli docelowy poziom interfejsu API systemu Android 23. W przeciwnym razie ona zostanie włączona domyślnie.

Użytkownik może Włącz/wyłącz te uprawnienia z menu ustawień urządzenia. Wyłączenie uprawnień z menu systemowe kasuje procesów w tle aplikacji, jest zachowanie systemu które nie ma wpływu na możliwość odbierania wypychania w tle.

W kontekście usługi Mobile Engagement dostępne są następujące uprawnienia, które wymagają zatwierdzenia w czasie wykonywania:

* `ACCESS_COARSE_LOCATION`
* `ACCESS_FINE_LOCATION`
* `WRITE_EXTERNAL_STORAGE` (tylko wtedy, gdy przeznaczonych dla interfejsu API systemu Android na poziomie 23 tej)

Zewnętrzna pamięć masowa jest używany tylko dla funkcji szerszej Reach. Jeśli okaże się, prosząc użytkowników o uprawnienie to można problem, można go usunąć Jeśli używane tylko w przypadku usługi Mobile Engagement, ale kosztem wyłączenie funkcji Duży obraz.

W przypadku funkcji lokalizacji należy zażądać uprawnień użytkownika za pomocą okna dialogowego w standardowym systemie. Jeśli użytkownik zaakceptuje, należy przekazać do ``EngagementAgent`` podjęcie zmiana pod uwagę w czasie rzeczywistym (w przeciwnym razie zmiany zostaną przetworzone przy następnym użytkownik uruchamia aplikację).

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
         * Request location permission, but this won't explain why it is needed to the user.
         * The standard Android documentation explains with more details how to display a rationale activity to explain the user why the permission is needed in your application.
         * Putting COARSE vs FINE has no impact here, they are part of the same group for runtime permission management.
         */
        if (checkSelfPermission(android.Manifest.permission.ACCESS_FINE_LOCATION) != PackageManager.PERMISSION_GRANTED)
          requestPermissions(new String[] { android.Manifest.permission.ACCESS_FINE_LOCATION }, 0);

        /* Only if you want to keep features using external storage */
        if (checkSelfPermission(android.Manifest.permission.WRITE_EXTERNAL_STORAGE) != PackageManager.PERMISSION_GRANTED)
          requestPermissions(new String[] { android.Manifest.permission.WRITE_EXTERNAL_STORAGE }, 1);
      }
    }

    @Override
    public void onRequestPermissionsResult(int requestCode, String[] permissions, int[] grantResults)
    {
      /* Only a positive location permission update requires engagement agent refresh, hence the request code matching from above function */
      if (requestCode == 0 && grantResults[0] == PackageManager.PERMISSION_GRANTED)
        getEngagementAgent().refreshPermissions();
    }

## <a name="advanced-reporting"></a>Zaawansowane raportowanie
Opcjonalnie, jeśli chcesz zgłosić aplikacji określonych zdarzeń, błędów i zadań, należy użyć interfejsu API usługi Engagement za pomocą metody `EngagementAgent` klasy. Obiekt tej klasy może być pobranych przez wywołanie metody `EngagementAgent.getInstance()` metody statycznej.

Interfejsu API usługi Engagement pozwala korzystać ze wszystkich zaawansowanych możliwości usługi Engagement i została szczegółowo opisana w sposób używania interfejsu API programu zaangażowania w systemie Android (jak również w dokumentacji technicznej `EngagementAgent` klasy).

## <a name="advanced-configuration-in-androidmanifestxml"></a>Konfiguracja zaawansowana (w pliku AndroidManifest.xml)
### <a name="wake-locks"></a>Wznawianie pracy blokad
Należy upewnić się, że dane statystyczne są wysyłane w czasie rzeczywistym za pomocą sieci Wi-Fi lub ekranu jest wyłączona, należy dodać następujące uprawnienia opcjonalne:

            <uses-permission android:name="android.permission.WAKE_LOCK"/>

### <a name="crash-report"></a>Raport o awarii
Jeśli chcesz wyłączyć raporty awarii, dodaj ją (między `<application>` i `</application>` tagów):

            <meta-data android:name="engagement:reportCrash" android:value="false"/>

### <a name="burst-threshold"></a>Próg serii
Domyślnie raporty usługi Engagement rejestruje się w czasie rzeczywistym. Jeśli aplikacja zgłasza dzienniki bardzo często, lepiej jest buforu dzienniki i raportuj je w całości na regularne podstawy czasu (jest to nazywane "tryb serii"). Aby to zrobić, Dodaj (między `<application>` i `</application>` tagów):

            <meta-data android:name="engagement:burstThreshold" android:value="{interval between too bursts (in milliseconds)}"/>

Tryb serii nieco zwiększyć czas pracy baterii, ale ma wpływ na monitorowanie usługi Engagement: wszystkie czas trwania sesji i zadania zostanie zaokrąglony próg serii (w związku z tym sesji i zadania krótsze niż próg serii może nie być widoczna). Zaleca się przy użyciu progu w serii się niż 30000 (30s).

### <a name="session-timeout"></a>Limit czasu sesji
Domyślnie sesja jest 10s zakończone po zakończeniu jego ostatniej aktywności (która zazwyczaj występuje po naciśnięciu klawisza Home lub Wstecz, przez ustawienie bezczynności telefonu lub przejście do innej aplikacji). Pozwoli to uniknąć podziału sesji każdy czas zakończenia użytkownika i bardzo szybko powrócić do aplikacji (które mogą wystąpić podczas odebrania on obrazu, sprawdź powiadomienia itp.). Można modyfikować tego parametru. Aby to zrobić, Dodaj (między `<application>` i `</application>` tagów):

            <meta-data android:name="engagement:sessionTimeout" android:value="{session timeout (in milliseconds)}"/>

## <a name="disable-log-reporting"></a>Wyłączanie dziennika raportowania
### <a name="using-a-method-call"></a>Przy użyciu wywołania metody
Jeśli chcesz zaangażowania, aby zatrzymać wysyłanie dzienników można wywołać:

            EngagementAgent.getInstance(context).setEnabled(false);

To wywołanie jest trwały: używa plików udostępnionych preferencji.

Jeśli zaangażowania jest aktywny, gdy wywołanie tej funkcji, może potrwać minutę do zatrzymania usługi. Jednak go nie Uruchom usługę na wszystkich przy następnym uruchomieniu aplikacji.

Możesz włączyć ponownie raportowania przez wywołanie tej samej funkcji z dziennika `true`.

### <a name="integration-in-your-own-preferenceactivity"></a>Integracja z własną `PreferenceActivity`
Zamiast wywoływania tej funkcji, to ustawienie można również zintegrować bezpośrednio w istniejącą `PreferenceActivity`.

Zaangażowania, aby użyć pliku preferencji (z odpowiednią tryb) można skonfigurować w `AndroidManifest.xml` pliku z `application meta-data`:

* `engagement:agent:settings:name` Klucz służy do definiowania nazwę pliku udostępnionych preferencji.
* `engagement:agent:settings:mode` Klucz służy do definiowania trybu plików udostępnionych preferencji, należy użyć tego samego trybu jako w Twojej `PreferenceActivity`. Tryb muszą być przekazywane w postaci liczby: Jeśli korzystasz z kombinacją flag stałej w kodzie, sprawdź wartość całkowita.

Użyj zaangażowania zawsze `engagement:key` logiczna klucz w pliku preferencji zarządzania to ustawienie.

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

<!-- URLs. -->
[Device API]: http://go.microsoft.com/?linkid=9876094
