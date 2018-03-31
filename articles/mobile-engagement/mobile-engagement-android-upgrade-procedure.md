---
title: Integracja zestawu SDK systemu Android z usługi Azure Mobile Engagement
description: Najnowsze aktualizacje i procedury dotyczące zestawu SDK systemu Android dla usługi Azure Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 11618586-c709-49ca-bcd8-745323ff1af6
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 77047cb1dc39fa3c05f58550ceea74e78396157f
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="upgrade-procedures"></a>Procedury uaktualniania
> [!IMPORTANT]
> Usługa Azure Mobile Engagement wycofaniu na 3/31/2018. Ta strona zostanie usunięta wkrótce po.
> 

Jeśli już jest zintegrowany starszej wersji naszego zestawu SDK do aplikacji, należy wziąć pod uwagę następujące kwestie, podczas uaktualniania zestawu SDK.

Należy wykonać kilka procedur, jeśli pominięte kilka wersji zestawu SDK. Na przykład w przypadku migrowania z 1.4.0 do 1.6.0, należy najpierw wykonać procedurę "od 1.4.0 do 1.5.0" następnie procedury "od 1.5.0 do 1.6.0".

Niezależnie od wersji uaktualnienia, należy zastąpić `mobile-engagement-VERSION.jar` nowym.

## <a name="from-420-to-421"></a>Z 4.2.0 do 4.2.1
Ten krok faktycznie można zrobić w dowolnej wersji zestawu SDK, jest do poprawy zabezpieczeń po zintegrowaniu Reach działań.

Teraz należy dodać `exported="false"` dla wszystkich działań Reach.

Działania reach powinna wyglądać tak jak to Twojej `AndroidManifest.xml`:

            <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementTextAnnouncementActivity" android:theme="@android:style/Theme.Light" android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="android.intent.category.DEFAULT" />
                <data android:mimeType="text/plain" />
              </intent-filter>
            </activity>
            <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity" android:theme="@android:style/Theme.Light" android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="android.intent.category.DEFAULT" />
                <data android:mimeType="text/html" />
              </intent-filter>
            </activity>
            <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementPollActivity" android:theme="@android:style/Theme.Light" android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.POLL"/>
                <category android:name="android.intent.category.DEFAULT" />
              </intent-filter>
            </activity>
            <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementLoadingActivity" android:theme="@android:style/Theme.Dialog" android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.LOADING"/>
                <category android:name="android.intent.category.DEFAULT"/>
              </intent-filter>
            </activity>

## <a name="from-400-to-410"></a>Z 4.0.0 do 4.1.0
Zestaw SDK teraz dojścia nowe uprawnienia modelu z systemem Android M.

Jeśli lokalizacja funkcji lub powiadomienia szerszej przeczytaj [w tej sekcji](mobile-engagement-android-integrate-engagement.md#android-m-permissions).

Oprócz nowego modelu uprawnień obsługujemy są teraz skonfigurować funkcje lokalizacji w czasie wykonywania.
Firma Microsoft są nadal zgodne z parametrami manifestu dla lokalizacji, ale jest już przestarzały. Aby użyć konfiguracji środowiska uruchomieniowego, Usuń z następującymi sekcjami w Twojej ``AndroidManifest.xml``:

    <meta-data
      android:name="engagement:locationReport:lazyArea"
      android:value="true"/>
    <meta-data
      android:name="engagement:locationReport:realTime"
      android:value="true"/>
    <meta-data
      android:name="engagement:locationReport:realTime:background"
      android:value="true"/>
    <meta-data
      android:name="engagement:locationReport:realTime:fine"
      android:value="true"/>

i odczytu [to zaktualizowane procedury](mobile-engagement-android-integrate-engagement.md#location-reporting) zamiast tego użyć konfiguracji środowiska wykonawczego.

## <a name="from-300-to-400"></a>Z 3.0.0 do 4.0.0
### <a name="native-push"></a>Natywnych powiadomień wypychanych
Natywnych powiadomień wypychanych (GCM/ADM) teraz służy także do w powiadomieniach wewnętrznych aplikacji, musisz skonfigurować poświadczenia natywnych powiadomień wypychanych dla dowolnego typu wypychania kampanii.

Jeśli nie jest już wykonaj [tej procedury](mobile-engagement-android-integrate-engagement-reach.md#native-push).

### <a name="androidmanifestxml"></a>AndroidManifest.xml
Integracja reach został zmodyfikowany w ``AndroidManifest.xml``.

Zastąp to:

    <receiver
      android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver"
      android:exported="false">
      <intent-filter>
        <action android:name="android.intent.action.BOOT_COMPLETED"/>
        <action android:name="com.microsoft.azure.engagement.intent.action.AGENT_CREATED"/>
        <action android:name="com.microsoft.azure.engagement.intent.action.MESSAGE"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.ACTION_NOTIFICATION"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.EXIT_NOTIFICATION"/>
        <action android:name="android.intent.action.DOWNLOAD_COMPLETE"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.DOWNLOAD_TIMEOUT"/>
      </intent-filter>
    </receiver>

Od

    <receiver
      android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver"
      android:exported="false">
      <intent-filter>
        <action android:name="android.intent.action.BOOT_COMPLETED"/>
        <action android:name="com.microsoft.azure.engagement.intent.action.AGENT_CREATED"/>
        <action android:name="com.microsoft.azure.engagement.intent.action.MESSAGE"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.ACTION_NOTIFICATION"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.EXIT_NOTIFICATION"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.DOWNLOAD_TIMEOUT"/>
      </intent-filter>
    </receiver>
    <receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachDownloadReceiver">
      <intent-filter>
        <action android:name="android.intent.action.DOWNLOAD_COMPLETE"/>
      </intent-filter>
    </receiver>

Brak prawdopodobnie ekranu ładowania teraz kliknięcie powiadomienia (tekstu/sieci web z zawartością) lub sonda.
Należy dodać to dla tych kampanii do pracy w 4.0.0:

    <activity
      android:name="com.microsoft.azure.engagement.reach.activity.EngagementLoadingActivity"
      android:theme="@android:style/Theme.Dialog">
      <intent-filter>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.LOADING"/>
        <category android:name="android.intent.category.DEFAULT"/>
      </intent-filter>
    </activity>

### <a name="resources"></a>Zasoby
Osadzić nowy `res/layout/engagement_loading.xml` plik do projektu.

## <a name="from-240-to-300"></a>Z 2.4.0 do 3.0.0
Poniżej opisano sposób migracji integracji zestawu SDK usługi Capptain oferowane przez Capptain SAS w aplikacji obsługiwane przez usługę Azure Mobile Engagement. W przypadku migracji z wcześniejszej wersji, przejrzyj witrynę sieci web Capptain, aby przeprowadzić migrację do 2.4.0, a następnie zastosować następującą procedurę.

> [!IMPORTANT]
> Capptain i usługi Mobile Engagement nie są takie same usługi i procedury przedstawionej poniżej tylko prezentuje sposób migracji aplikacji klienckiej. Migrowanie zestawu SDK w aplikacji nie będą migrowane dane z serwerów Capptain do serwerów usługi Mobile Engagement.
> 
> 

### <a name="jar-file"></a>Plik JAR
Zastąp `capptain.jar` przez `mobile-engagement-VERSION.jar` w Twojej `libs` folderu.

### <a name="resource-files"></a>Pliki zasobów
Każdy plik zasobu podaliśmy (poprzedzony `capptain_`) musi zostać zastąpione przez nowe (prefiksem `engagement_`).

Po dostosowaniu tych plików, należy ponownie zastosować dostosowanie na nowe pliki **również zmieniono identyfikatorów plików zasobów**.

### <a name="application-id"></a>Identyfikator aplikacji
Teraz zaangażowania używa parametrów połączenia do konfigurowania identyfikatory zestawu SDK, takich jak identyfikator aplikacji.

Należy użyć `EngagementAgent.init` metody w działanie uruchamiania następująco:

            EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
            engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
            EngagementAgent.getInstance(this).init(engagementConfiguration);

Ciąg połączenia dla aplikacji jest wyświetlana w portalu Azure.

Usuń wszystkie wywołania `CapptainAgent.configure` jako `EngagementAgent.init` zastępuje tę metodę.

`appId` Nie będzie można skonfigurować przy użyciu `AndroidManifest.xml`.

Usuń w tej sekcji z Twojej `AndroidManifest.xml` Jeśli została ona:

            <meta-data android:name="capptain:appId" android:value="<YOUR_APPID>"/>

### <a name="java-api"></a>Interfejs API języka Java
Każdego wywołania dla każdej klasy Java naszego zestawu SDK ma zostać zmienione; na przykład `CapptainAgent.getInstance(this)` można zmienić nazwy `EngagementAgent.getInstance(this)`, `extends CapptainActivity` można zmienić nazwy `extends EngagementActivity` itp...

Jeśli zostały zintegrowany z domyślnych plików preferencji agenta, domyślna nazwa pliku jest teraz `engagement.agent` , jak klucz `engagement:agent`.

Podczas tworzenia sieci web anonsów, integratora Javascript jest teraz `engagementReachContent`.

### <a name="androidmanifestxml"></a>AndroidManifest.xml
Wiele zmian się stało, usługa nie jest już udostępniony i wiele odbiorników nie są już możliwy do eksportu.

Deklaracja usługa jest teraz prostsze; Usuń filtr konwersji i wszystkie metadane w nim, a następnie dodaj `exportable=false`.

Dodatkowo wszystko, co jest zmieniana za pomocą engagement.

Teraz wygląda następująco:

            <service
              android:name="com.microsoft.azure.engagement.service.EngagementService"
              android:exported="false"
              android:label="<Your application name>Service"
              android:process=":Engagement"/>

Jeśli chcesz włączyć dzienników testu meta-data teraz został przeniesiony do tagu aplikacji i została zmieniona:

            <application>

              <meta-data android:name="engagement:log:test" android:value="true" />

              <service/>

            </application>

Zmieniono tylko wszystkie inne metadane, poniżej przedstawiono pełną listę (zmiana nazwy kursu tylko te, które są używane):

            <meta-data
              android:name="engagement:reportCrash"
              android:value="true"/>
            <meta-data
              android:name="engagement:sessionTimeout"
              android:value="10000"/>
            <meta-data
              android:name="engagement:burstThreshold"
              android:value="0"/>
            <meta-data
              android:name="engagement:connection:delay"
              android:value="0"/>
            <meta-data
              android:name="engagement:locationReport:lazyArea"
              android:value="false"/>
            <meta-data
              android:name="engagement:locationReport:realTime"
              android:value="false"/>
            <meta-data
              android:name="engagement:locationReport:realTime:background"
              android:value="false"/>
            <meta-data
              android:name="engagement:locationReport:realTime:fine"
              android:value="false"/>
            <meta-data
              android:name="engagement:agent:settings:name"
              android:value="engagement.agent"/>
            <meta-data
              android:name="engagement:agent:settings:mode"
              android:value="0"/>
            <meta-data
              android:name="engagement:gcm:sender"
              android:value="<YOUR_PROJECT_NUMBER>\n"/>
            <meta-data
              android:name="engagement:adm:register"
              android:value="true"/>
            <meta-data
              android:name="engagement:reach:notification:icon"
              android:value="<DRAWABLE_NAME_WITHOUT_EXTENSION>"/>

            <activity android:name="SomeActivityWithoutReachOverlay">
              <meta-data
                android:name="engagement:notification:overlay"
                android:value="false"/>
            </activity>

Google Play i SmartAd śledzenia został usunięty z zestawu SDK, należy usunąć to bez zastępowania:

            <meta-data 
                android:name="capptain:track:installReferrerForwardList"
                android:value="com.class1,com.class2"/>
            <meta-data
                android:name="capptain:track:adservers"
                android:value="smartad" />

Działania Reach są obecnie deklarowane następująco:

            <activity
              android:name="com.microsoft.azure.engagement.reach.activity.EngagementTextAnnouncementActivity"
              android:theme="@android:style/Theme.Light">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="android.intent.category.DEFAULT"/>
                <data android:mimeType="text/plain"/>
              </intent-filter>
            </activity>
            <activity
              android:name="com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity"
              android:theme="@android:style/Theme.Light">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="android.intent.category.DEFAULT"/>
                <data android:mimeType="text/html"/>
              </intent-filter>
            </activity>
            <activity
              android:name="com.microsoft.azure.engagement.reach.activity.EngagementPollActivity"
              android:theme="@android:style/Theme.Light">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.POLL"/>
                <category android:name="android.intent.category.DEFAULT"/>
              </intent-filter>
            </activity>

Jeśli masz działań niestandardowych Reach musisz tylko zmienić akcje konwersji do pasuje do żadnej `com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT` lub `com.microsoft.azure.engagement.reach.intent.action.POLL`.

Zmieniono emisji odbiornikami, a także teraz dodać `exported=false`. Poniżej przedstawiono pełną listę odbiorcy z nowego specyfikacji, (zmiana nazwy kursu tylko te, które są używane):

            <receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver"
              android:exported="false">
              <intent-filter>
                <action android:name="android.intent.action.BOOT_COMPLETED"/>
                <action android:name="com.microsoft.azure.engagement.intent.action.AGENT_CREATED"/>
                <action android:name="com.microsoft.azure.engagement.intent.action.MESSAGE"/>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ACTION_NOTIFICATION"/>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.EXIT_NOTIFICATION"/>
                <action android:name="android.intent.action.DOWNLOAD_COMPLETE"/>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.DOWNLOAD_TIMEOUT"/>
              </intent-filter>
            </receiver>

            <receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMEnabler"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT" />
              </intent-filter>
            </receiver>

            <receiver
              android:name="com.microsoft.azure.engagement.gcm.EngagementGCMReceiver"
              android:permission="com.google.android.c2dm.permission.SEND">
              <intent-filter>
                <action android:name="com.google.android.c2dm.intent.REGISTRATION"/>
                <action android:name="com.google.android.c2dm.intent.RECEIVE"/>
                <category android:name="<your_package_name>"/>
              </intent-filter>
            </receiver>

            <receiver android:name="com.microsoft.azure.engagement.adm.EngagementADMEnabler"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT"/>
              </intent-filter>
            </receiver>

            <receiver
              android:name="com.microsoft.azure.engagement.adm.EngagementADMReceiver"
              android:permission="com.amazon.device.messaging.permission.SEND">
              <intent-filter>
                <action android:name="com.amazon.device.messaging.intent.REGISTRATION"/>
                <action android:name="com.amazon.device.messaging.intent.RECEIVE"/>
                <category android:name="<your_package_name>"/>
              </intent-filter>
            </receiver>

            <receiver android:name="<your_sub_class_of_com.microsoft.azure.engagement.reach.EngagementReachDataPushReceiver>"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.DATA_PUSH" />
              </intent-filter>
            </receiver>

            <receiver android:name="com.microsoft.azure.engagement.EngagementLocationBootReceiver"
               android:exported="false">
               <intent-filter>
                  <action android:name="android.intent.action.BOOT_COMPLETED" />
               </intent-filter>
            </receiver>

            <receiver android:name="<your_sub_class_of_com.microsoft.azure.engagement.EngagementConnectionReceiver.java>"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.intent.action.CONNECTED"/>
                <action android:name="com.microsoft.azure.engagement.intent.action.DISCONNECTED"/>
              </intent-filter>
            </receiver>

            <receiver
              android:name="<your_sub_class_of_com.microsoft.azure.engagement.EngagementMessageReceiver.java>"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.MESSAGE"/>
              </intent-filter>
            </receiver>

Odbiornik śledzenia zostały usunięte, więc musisz usunąć w tej sekcji:

          <receiver android:name="com.ubikod.capptain.android.sdk.track.CapptainTrackReceiver">
            <intent-filter>
              <action android:name="com.ubikod.capptain.intent.action.APPID_GOT" />
              <!-- possibly <action android:name="com.android.vending.INSTALL_REFERRER" /> -->
            </intent-filter>
          </receiver>

Należy pamiętać, że deklaracji w implementacji emisji odbiornika **EngagementMessageReceiver** została zmieniona w `AndroidManifest.xml`. Jest to spowodowane usunięto interfejs API do wysyłania i usunąć dowolnego XMPP wiadomości z dowolnego XMPP jednostek i interfejsu API do wysyłania i odbierania wiadomości między urządzeniami. W związku z tym należy również usunąć następujące wywołania zwrotne z Twojej **EngagementMessageReceiver** implementacji:

            protected void onDeviceMessageReceived(android.content.Context context, java.lang.String deviceId, java.lang.String payload)

i

            protected void onXMPPMessageReceived(android.content.Context context, android.os.Bundle message)

Usuń wszystkie wywołania na **EngagementAgent** dla:

            sendMessageToDevice(java.lang.String deviceId, java.lang.String payload, java.lang.String packageName)

i

            sendXMPPMessage(android.os.Bundle msg)

### <a name="proguard"></a>Proguard
Konfiguracja proguard może mieć wpływ rebranding, reguły teraz chce się dowiedzieć, jak:

            -dontwarn android.**
            -keep class android.support.v4.** { *; }

            -keep public class * extends android.os.IInterface
            -keep class com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity$EngagementReachContentJS {
              <methods>;
            }

