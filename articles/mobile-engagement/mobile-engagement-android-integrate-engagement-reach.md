---
title: Integracja zestawu SDK systemu Android z usługi Azure Mobile Engagement
description: Najnowsze aktualizacje i procedury dotyczące zestawu SDK systemu Android dla usługi Azure Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 9ec3fab3-35ec-458e-bf41-6cdd69e3fa44
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 06/27/2016
ms.author: piyushjo
ms.openlocfilehash: 15e71d8aeb8c8060161ade4262e0be0a093b5650
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-integrate-engagement-reach-on-android"></a>Integrowanie Reach usługi Engagement w systemie Android
> [!IMPORTANT]
> Usługa Azure Mobile Engagement wycofaniu na 3/31/2018. Ta strona zostanie usunięta wkrótce po.
> 

> [!IMPORTANT]
> Musi występować po integracji procedury opisane w sekcji sposobu integracji usługi Engagement Android dokumentu przed wykonaniem tego przewodnika.
> 
> 

## <a name="standard-integration"></a>Standardowa integracji

Skopiuj pliki zasobów Reach z zestawu SDK w projekcie:

* Skopiuj pliki z `res/layout` folderu dostarczane przy użyciu zestawu SDK do `res/layout` folderu aplikacji.
* Skopiuj pliki z `res/drawable` folderu dostarczane przy użyciu zestawu SDK do `res/drawable` folderu aplikacji.

Edytowanie użytkownika `AndroidManifest.xml` pliku:

* Dodaj poniższą sekcję (między `<application>` i `</application>` tagów):
  
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
          <receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver" android:exported="false">
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
* Należy to uprawnienie do powtarzania powiadomień systemowych, które nie zostały kliknięty przy rozruchu (w przeciwnym razie będą znajdować się na dysku, ale nie będą już wyświetlane, które trzeba uwzględniać to).
  
          <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
* Określanie ikony używany do powiadomień, (zarówno w aplikacji oraz systemowe z nich) przez kopiowanie i edytowania poniższej sekcji (między `<application>` i `</application>` tagów):
  
          <meta-data android:name="engagement:reach:notification:icon" android:value="<name_of_icon_WITHOUT_file_extension_and_WITHOUT_'@drawable/'>" />

> [!IMPORTANT]
> Ta sekcja ma **obowiązkowe** Jeśli planujesz używanie powiadomień systemowych, tworząc kampanie Zasięgowe. Android uniemożliwia powiadomień systemowych bez ikony wyświetlane. Dlatego w przypadku pominięcia tej sekcji, użytkownicy końcowi uniemożliwi do ich odbierania.
> 
> 

* Jeśli tworzysz kampanie z powiadomień systemowych przy użyciu duży obraz, należy dodać następujące uprawnienia (po `</application>` tagu) Jeśli brak:
  
          <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
          <uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION"/>
  
  * W systemie Android M i czy aplikacja korzysta poziom interfejsu API systemu Android 23 lub większą ``WRITE_EXTERNAL_STORAGE`` uprawnienia wymaga zatwierdzenia przez użytkownika. Przeczytaj [w tej sekcji](mobile-engagement-android-integrate-engagement.md#android-m-permissions).
* Dla powiadomień systemowych można również określić w kampanii Reach Jeśli urządzenie powinno pierścienia i/lub też. Dla tej funkcji, musisz upewnij się, że zadeklarowany następujących uprawnień (po `</application>` tagu):
  
          <uses-permission android:name="android.permission.VIBRATE" />
  
  Bez tego uprawnienia Android uniemożliwia powiadomień systemowych jest wyświetlany, jeśli zaznaczono pierścienia lub opcji vibrate w Menedżerze osiągnąć kampanii.

## <a name="native-push"></a>Wypychanie natywne
Teraz, możesz skonfigurować moduł Reach, należy skonfigurować natywnych powiadomień wypychanych, aby móc odbierać kampanii na urządzeniu.

W systemie Android firma Microsoft obsługuje dwie usługi:

* Urządzenia ze sklepu Google Play: Użyj [Google Cloud Messaging] wykonując [jak GCM zintegrować z przewodnika zaangażowania](mobile-engagement-android-gcm-integrate.md) przewodnik.
* Urządzenia firmy Amazon: Użyj [usługi Amazon Device Messaging] wykonując [jak zintegrować ADM prowadnicy zaangażowania](mobile-engagement-android-adm-integrate.md) przewodnik.

Jeśli chcesz przeanalizować urządzeń zarówno Amazon, jak i Google Play, można mieć wszystkie elementy wewnątrz 1 AndroidManifest.xml/APK do tworzenia aplikacji. Jednak podczas przesyłania do Amazon, mogą one odrzucić aplikacji, jeśli znajduje się kod usługi GCM.

W takim przypadku należy używać wielu APKs.

**Aplikacja jest teraz gotowa do odbierania i wyświetlić kampanie zasięgowe!**

## <a name="how-to-handle-data-push"></a>Sposób obsługi wypychanie danych
### <a name="integration"></a>Integracja
Jeśli chcesz otrzymywać Reach wypychania danych aplikacji, należy utworzyć podklasą klasy `com.microsoft.azure.engagement.reach.EngagementReachDataPushReceiver` i odwołuje się on w `AndroidManifest.xml` pliku (między `<application>` i/lub `</application>` tagów):

            <receiver android:name="<your_sub_class_of_com.microsoft.azure.engagement.reach.EngagementReachDataPushReceiver>"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.DATA_PUSH" />
              </intent-filter>
            </receiver>

Następnie można zastąpić `onDataPushStringReceived` i `onDataPushBase64Received` wywołań zwrotnych. Oto przykład:

            public class MyDataPushReceiver extends EngagementReachDataPushReceiver
            {
              @Override
              protected Boolean onDataPushStringReceived(Context context, String category, String body)
              {
                Log.d("tmp", "String data push message received: " + body);
                return true;
              }

              @Override
              protected Boolean onDataPushBase64Received(Context context, String category, byte[] decodedBody, String encodedBody)
              {
                Log.d("tmp", "Base64 data push message received: " + encodedBody);
                // Do something useful with decodedBody like updating an image view
                return true;
              }
            }

### <a name="category"></a>Kategoria
Parametr kategorii jest opcjonalny, podczas tworzenia kampanii wypychania danych i umożliwia Wypychanie do filtrowania danych. Jest to przydatne, jeśli masz wiele odbiorników emisji obsługi różnych typów wypychania danych, lub jeśli chcesz dystrybuować różnych rodzajów z `Base64` danych, aby zidentyfikować ich typu przed ich analizowania.

### <a name="callbacks-return-parameter"></a>Parametr zwrotnego wywołania zwrotne
Poniżej przedstawiono kilka wskazówek, aby poprawnie obsługiwać parametr zwrotny `onDataPushStringReceived` i `onDataPushBase64Received`:

* Odbiornik emisji powinien zwrócić `null` podczas wywołania zwrotnego, jeśli nie może określić sposób obsługi wypychanie danych. Aby ustalić, czy odbiornik emisji powinna obsługiwać wypychanie danych lub nie należy używać tej kategorii.
* Jeden odbiornik emisji powinien zwrócić `true` podczas wywołania zwrotnego, jeśli akceptuje wypychanie danych.
* Jeden odbiornik emisji powinien zwrócić `false` podczas wywołania zwrotnego, jeśli rozpoznaje wypychania danych, ale odrzuca je niezależnie od przyczyn. Na przykład `false` po otrzymaniu danych jest nieprawidłowa.
* Jeśli emituje jeden odbiornik zwraca `true` podczas drugiego zwraca jedną `false` do tego samego wypychania danych, jest niezdefiniowane zachowanie, nigdy nie należy to zrobić.

Zwracany typ jest używany tylko dla statystyki zasięgu:

* `Replied` jest zwiększany, jeśli jeden z odbiorców emisji albo zwrócony `true` lub `false`.
* `Actioned` zwiększany jest tylko wtedy, gdy jeden z odbiorców emisji zwrócił `true`.

## <a name="how-to-customize-campaigns"></a>Dostosowywanie kampanii
Aby dostosować kampanii, można zmodyfikować układy określone w zestawie SDK Reach.

Należy zachować wszystkie identyfikatory, które są używane w układów i Zachowaj typy widoków, korzystających z identyfikatorem, szczególnie w przypadku widoków tekstu i obrazów. Niektóre widoki właśnie są używane do ukrywania lub pokazywania obszarów, aby ich typ może być zmieniony. Sprawdź kod źródłowy, jeśli chcesz zmienić typ widoku w podanych układów.

### <a name="notifications"></a>Powiadomienia
Istnieją dwa typy powiadomień: powiadomień systemu i w aplikacji, które pliki inny układ.

#### <a name="system-notifications"></a>System powiadomień
Aby dostosować powiadomień systemowych, należy użyć **kategorii**. Można przejść do [kategorii](#categories).

#### <a name="in-app-notifications"></a>Powiadomienia w aplikacji
Domyślnie powiadomienia w aplikacji jest widok dynamicznie dodawane do bieżącego działania interfejsu użytkownika dzięki użyciu Android — metoda `addContentView()`. Jest to nakładce powiadomienia. Nakładki powiadomienia są bardzo szybkiego integracji, ponieważ nie wymagają modyfikacji dowolny układ w aplikacji.

Aby zmodyfikować wygląd sieci nakładki powiadomień, wystarczy zmodyfikować plik `engagement_notification_area.xml` do własnych potrzeb.

> [!NOTE]
> Plik `engagement_notification_overlay.xml` jest ten, który jest używany do tworzenia w nakładce powiadomienia, w tym plik `engagement_notification_area.xml`. Można również dostosować do własnych potrzeb (np. dla obszaru powiadomień w nakładce rozmieszczania).
> 
> 

##### <a name="include-notification-layout-as-part-of-an-activity-layout"></a>Obejmują układu powiadomienia w ramach działania układu
Nakładki są bardzo szybkiego integracji, ale można niewygodne lub mieć skutki uboczne w szczególnych przypadkach. Można dostosować system nakładki na poziomie działania, co ułatwia zapobieganie efekty uboczne specjalnych działań.

Można wybrać obejmują naszych układu powiadomień w układzie istniejących dzięki użyciu Android **obejmują** instrukcji. Poniżej przedstawiono przykład zmodyfikowanych `ListActivity` układu zawierający tylko `ListView`.

**Przed integracji usługi Engagement:**

            <?xml version="1.0" encoding="utf-8"?>
            <ListView
              xmlns:android="http://schemas.android.com/apk/res/android"
              android:id="@android:id/list"
              android:layout_width="fill_parent"
              android:layout_height="fill_parent" />

**Po integracji usługi Engagement:**

            <?xml version="1.0" encoding="utf-8"?>
            <LinearLayout
              xmlns:android="http://schemas.android.com/apk/res/android"
              android:orientation="vertical"
              android:layout_width="fill_parent"
              android:layout_height="fill_parent">

              <ListView
                android:id="@android:id/list"
                android:layout_width="fill_parent"
                android:layout_height="fill_parent"
                android:layout_weight="1" />

              <include layout="@layout/engagement_notification_area" />

            </LinearLayout>

W tym przykładzie dodano kontenera nadrzędnego ponieważ układ oryginalny używany widok listy jako elementu najwyższego poziomu. Dodaliśmy również `android:layout_weight="1"` aby można było dodać widok poniżej skonfigurowany z widoku listy `android:layout_height="fill_parent"`.

Zestaw SDK Reach usługi Engagement automatycznie wykrywa, czy układ powiadomień jest dołączony do tego działania, a nie dodają nakładki dla tego działania.

> [!TIP]
> Jeśli używasz ListActivity w aplikacji widoczne nakładki Reach uniemożliwi reagowanie na już kliknięte elementy w widoku listy. Jest to znany problem. Aby obejść ten problem Sugerujemy można osadzić układu powiadomień w własne działania układ listy podobnie jak w poprzednim przykładzie.
> 
> 

##### <a name="disabling-application-notification-per-activity"></a>Wyłączanie aplikacji powiadomienia na działanie
Jeśli nie chcesz, aby nakładki ma zostać dodany do Twoich działaniach i układu powiadomienia nie zawiera własny układ, można wyłączyć nakładki dla tego działania w `AndroidManifest.xml` przez dodanie `meta-data` sekcji, takich jak w poniższym przykładzie:

            <activity android:name="SplashScreenActivity">
              <meta-data android:name="engagement:notification:overlay" android:value="false"/>
            </activity>

#### <a name="categories"></a> Kategorie
Po zmodyfikowaniu podana układów możesz zmodyfikować wygląd wszystkich powiadomień. Kategorie umożliwiają definiowanie różnych wygląda docelowej (prawdopodobnie zachowania) dla powiadomień. Po utworzeniu kampanii Reach można określić kategorię. Należy pamiętać, że kategorie pozwalają również dostosować anonsów i sond, opisane w dalszej części tego dokumentu.

Aby zarejestrować kategorii Obsługa powiadomienia, należy dodać wywołanie po zainicjowaniu aplikacji.

> [!IMPORTANT]
> Przeczytaj ostrzeżenie o atrybucie android: proces \<android-sdk zaangażowania — proces\> w sposób integracji zaangażowania na temat Android przed kontynuowaniem.
> 
> 

W poniższym przykładzie założono potwierdzonych poprzedniego ostrzeżenie i użyj klasy `EngagementApplication`:

            public class MyApplication extends EngagementApplication
            {
              @Override
              protected void onApplicationProcessCreate()
              {
                // [...] other init
                EngagementReachAgent reachAgent = EngagementReachAgent.getInstance(this);
                reachAgent.registerNotifier(new MyNotifier(this), "myCategory");
              }
            }

`MyNotifier` Obiektu jest implementacją obsługi kategorii powiadomień. Jest implementacja klasy `EngagementNotifier` interfejsu lub klasy podrzędnej implementacji domyślnej: `EngagementDefaultNotifier`.

Należy pamiętać, że tego samego zgłaszający może obsługiwać kilka kategorii można je zarejestrować następująco:

            reachAgent.registerNotifier(new MyNotifier(this), "myCategory", "myAnotherCategory");

Aby zastąpić domyślną implementację kategorii, możesz zarejestrować implementacji takich jak w poniższym przykładzie:

            public class MyApplication extends EngagementApplication
            {
              @Override
              protected void onApplicationProcessCreate()
              {
                // [...] other init
                EngagementReachAgent reachAgent = EngagementReachAgent.getInstance(this);
                reachAgent.registerNotifier(new MyNotifier(this), Intent.CATEGORY_DEFAULT); // "android.intent.category.DEFAULT"
              }
            }

Bieżącej kategorii używany w procedurze obsługi jest przekazywana jako parametr w większości metod można zastąpić w `EngagementDefaultNotifier`.

Jest przekazywany jako `String` parametru lub pośrednio w `EngagementReachContent` obiektu, który ma `getCategory()` metody.

Można zmienić większość procesu tworzenia powiadomień na ponowne definiowanie metod `EngagementDefaultNotifier`, do bardziej zaawansowanych dostosowania swobodnie zapoznaj się z dokumentacją techniczną i kod źródłowy.

##### <a name="in-app-notifications"></a>Powiadomienia w aplikacji
Jeśli chcesz użyć alternatywnych układów dla określonej kategorii, można zaimplementować to jak w poniższym przykładzie:

            public class MyNotifier extends EngagementDefaultNotifier
            {
              public MyNotifier(Context context)
              {
                super(context);
              }

              @Override
              protected int getOverlayLayoutId(String category)
              {
                return R.layout.my_notification_overlay;
              }


              @Override
              public Integer getOverlayViewId(String category)
              {
                return R.id.my_notification_overlay;
              }

              @Override
              public Integer getInAppAreaId(String category)
              {
                return R.id.my_notification_area;
              }
            }

**Przykład `my_notification_overlay.xml` :**

            <?xml version="1.0" encoding="utf-8"?>
            <RelativeLayout
              xmlns:android="http://schemas.android.com/apk/res/android"
              android:id="@+id/my_notification_overlay"
              android:layout_width="fill_parent"
              android:layout_height="fill_parent">

              <include layout="@layout/my_notification_area" />

            </RelativeLayout>

Jak widać, identyfikator widoku nakładki jest inne niż standardowe. Należy pamiętać, że każdego układu dla nakładki używany unikatowy identyfikator.

**Przykład `my_notification_area.xml` :**

            <?xml version="1.0" encoding="utf-8"?>
            <merge
              xmlns:android="http://schemas.android.com/apk/res/android"
              android:layout_width="fill_parent"
              android:layout_height="fill_parent">

              <RelativeLayout
                android:id="@+id/my_notification_area"
                android:layout_width="fill_parent"
                android:layout_height="64dp"
                android:layout_alignParentTop="true"
                android:background="#B000">

                <LinearLayout
                  android:orientation="horizontal"
                  android:layout_width="fill_parent"
                  android:layout_height="fill_parent"
                  android:gravity="center_vertical">

                  <ImageView
                    android:id="@+id/engagement_notification_icon"
                    android:layout_width="48dp"
                    android:layout_height="48dp" />

                  <LinearLayout
                    android:id="@+id/engagement_notification_text"
                    android:orientation="vertical"
                    android:layout_width="fill_parent"
                    android:layout_height="fill_parent"
                    android:layout_weight="1"
                    android:gravity="center_vertical">

                    <TextView
                      android:id="@+id/engagement_notification_title"
                      android:layout_width="fill_parent"
                      android:layout_height="wrap_content"
                      android:singleLine="true"
                      android:ellipsize="end"
                      android:textAppearance="@android:style/TextAppearance.Medium" />

                    <TextView
                      android:id="@+id/engagement_notification_message"
                      android:layout_width="fill_parent"
                      android:layout_height="wrap_content"
                      android:maxLines="2"
                      android:ellipsize="end"
                      android:textAppearance="@android:style/TextAppearance.Small" />

                  </LinearLayout>

                  <ImageView
                    android:id="@+id/engagement_notification_image"
                    android:layout_width="wrap_content"
                    android:layout_height="fill_parent"
                    android:adjustViewBounds="true" />

                  <ImageButton
                    android:id="@+id/engagement_notification_close_area"
                    android:visibility="invisible"
                    android:layout_width="wrap_content"
                    android:layout_height="fill_parent"
                    android:src="@android:drawable/btn_dialog"
                    android:background="#0F00" />

                </LinearLayout>

                <ImageButton
                  android:id="@+id/engagement_notification_close"
                  android:layout_width="wrap_content"
                  android:layout_height="fill_parent"
                  android:layout_alignParentRight="true"
                  android:src="@android:drawable/btn_dialog"
                  android:background="#0F00" />

              </RelativeLayout>

            </merge>

Jak widać, innej niż standardowe jest identyfikator widoku obszaru powiadomień. Należy pamiętać, że każdy układ używa unikatowego identyfikatora obszarów powiadomień.

Ten prosty przykład kategorii sprawia, że aplikacja (lub w aplikacji) powiadomienia wyświetlane w górnej części ekranu. Nie został zmieniony standardowych identyfikatorów używane w obszarze powiadomień, się.

Jeśli chcesz zmienić, konieczne będzie ponownie zdefiniować `EngagementDefaultNotifier.prepareInAppArea` metody. Zaleca się znaleźć dokumentację techniczną i kod źródłowy `EngagementNotifier` i `EngagementDefaultNotifier` Jeśli chcesz to poziomu zaawansowanego dostosowania.

##### <a name="system-notifications"></a>System powiadomień
Rozszerzając `EngagementDefaultNotifier`, można zastąpić `onNotificationPrepared` do zmiany powiadomienia, który został przygotowany przez domyślną implementację.

Na przykład:

            @Override
            protected boolean onNotificationPrepared(Notification notification, EngagementReachInteractiveContent content)
              throws RuntimeException
            {
              if ("ongoing".equals(content.getCategory()))
                notification.flags |= Notification.FLAG_ONGOING_EVENT;
              return true;
            }

W tym przykładzie powoduje, że powiadomienie systemowe dla zawartości będzie wyświetlany jako zdarzenie uruchomione, gdy kategoria "stałe" jest używana.

Jeśli chcesz utworzyć `Notification` obiekt od początku, można powrócić `false` — metoda i wywołanie `notify` samodzielnie na `NotificationManager`. W takim przypadku należy pamiętać o jest `contentIntent`, `deleteIntent` i identyfikator powiadomienia, używany przez `EngagementReachReceiver`.

Oto przykład poprawnego takie implementacji:

            @Override
            protected boolean onNotificationPrepared(Notification notification, EngagementReachInteractiveContent content) throws RuntimeException
            {
              /* Required fields */
              NotificationCompat.Builder builder = new NotificationCompat.Builder(mContext)
                .setSmallIcon(notification.icon)              // icon is mandatory
                .setContentIntent(notification.contentIntent) // keep content intent
                .setDeleteIntent(notification.deleteIntent);  // keep delete intent

              /* Your customization */
              // builder.set...

              /* Dismiss option can be managed only after build */
              Notification myNotification = builder.build();
              if (!content.isNotificationCloseable())
                myNotification.flags |= Notification.FLAG_NO_CLEAR;

              /* Notify here instead of super class */
              NotificationManager manager = (NotificationManager) mContext.getSystemService(Context.NOTIFICATION_SERVICE);
              manager.notify(getNotificationId(content), myNotification); // notice the call to get the right identifier

              /* Return false, we notify ourselves */
              return false;
            }

##### <a name="notification-only-announcements"></a>Anonsów zawierających tylko powiadomienia
Zarządzanie kliknięcie powiadomienia tylko anonsu można dostosowywać przez zastąpienie `EngagementDefaultNotifier.onNotifAnnouncementIntentPrepared` do modyfikowania przygotowanej `Intent`. Za pomocą tej metody pozwala łatwo dostosować flag.

Na przykład aby dodać `SINGLE_TOP` flagi:

            @Override
            protected Intent onNotifAnnouncementIntentPrepared(EngagementNotifAnnouncement notifAnnouncement,
              Intent intent)
            {
              intent.addFlags(Intent.FLAG_ACTIVITY_SINGLE_TOP);
              return intent;
            }

W przypadku starszych zaangażowania użytkowników należy pamiętać, że powiadomień systemowych bez akcji URL teraz uruchamia aplikację jeśli było w tle, więc ta metoda może być wywołany z anonsu bez adres URL akcji. Które należy rozważyć w przypadku dostosowywania celem.

Można też wdrożyć `EngagementNotifier.executeNotifAnnouncementAction` od początku.

##### <a name="notification-life-cycle"></a>Cykl życia powiadomień
Podczas korzystania z domyślnej kategorii, w przypadku niektórych metod cyklu życia są nazywane na `EngagementReachInteractiveContent` obiektu do raportu statystyk i zaktualizować stan kampanii:

* Jeśli powiadomienia są wyświetlane w aplikacji lub umieść w pasku stanu `displayNotification` metoda jest wywoływana (która statystyki) przez `EngagementReachAgent` Jeśli `handleNotification` zwraca `true`.
* Jeśli powiadomienie jest odrzucane, `exitNotification` metoda jest wywoływana, statystyka jest zgłaszany i dalej kampanii teraz mogą być przetwarzane.
* Po kliknięciu powiadomienia `actionNotification` jest wywoływana, statystyka zostaje zgłoszone i skojarzone celem jest uruchamiana.

Jeśli implementacji `EngagementNotifier` pomija domyślne zachowanie, należy wywołać metody te cyklu życia samodzielnie. Poniższe przykłady przedstawiają w niektórych przypadkach, gdy pomijane jest domyślne zachowanie:

* Nie można rozszerzyć `EngagementDefaultNotifier`, np. zaimplementowano obsługi kategorii od początku.
* Dla powiadomień systemowych overrode `onNotificationPrepared` i możesz zmodyfikować `contentIntent` lub `deleteIntent` w `Notification` obiektu.
* Aby powiadomienia w aplikacji, overrode `prepareInAppArea`, należy zamapować co najmniej `actionNotification` do jednego z U.I kontrolki.

> [!NOTE]
> Jeśli `handleNotification` zgłasza wyjątek, zawartość zostanie usunięta i `dropContent` jest wywoływana. Jest to raportowane w statystykach i dalej kampanii teraz mogą być przetwarzane.
> 
> 

### <a name="announcements-and-polls"></a>Anonsów i sond
#### <a name="layouts"></a>Układy
Można zmodyfikować `engagement_text_announcement.xml`, `engagement_web_announcement.xml` i `engagement_poll.xml` pliki, aby dostosować tekst anonsów, web anonsów i sond.

Te pliki mają dwóch typowych układów obszar tytułu i obszar przycisku. Układ tytułu jest `engagement_content_title.xml` i używa gromadzący pliku obiektów drawable tła. Układ przycisków akcji i wyjścia jest `engagement_button_bar.xml` i używa gromadzący pliku obiektów drawable tła.

W sondowania, układ pytanie i ich możliwości są dynamicznie zwiększony przy użyciu kilka razy `engagement_question.xml` pliku układu dla pytań i `engagement_choice.xml` pliku dla opcji.

#### <a name="categories"></a>Kategorie
##### <a name="alternate-layouts"></a>Układy alternatywnej
Takich jak powiadomienia Kategoria kampanii można mieć alternatywne układy anonsów i sond.

Na przykład, aby utworzyć kategorii ogłoszenie tekstu, można rozszerzyć `EngagementTextAnnouncementActivity` i odwołaj `AndroidManifest.xml` pliku:

            <activity android:name="com.your_company.MyCustomTextAnnouncementActivity">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="my_category" />
                <data android:mimeType="text/plain" />
              </intent-filter>
            </activity>

Należy pamiętać, że kategorii w filtrze konwersji umożliwia różnica w stosunku do domyślne działanie anonsu.

SDK osiągnąć używa konwersji system do rozpoznania odpowiednim działaniu dla określonej kategorii i go powraca na domyślnej kategorii Jeśli niepowodzenie rozpoznawania.

Musisz zaimplementować `MyCustomTextAnnouncementActivity`, jeśli chcesz zmienić układ (, zachowując tego samego identyfikatory widok), wystarczy zdefiniować klasy, jak w poniższym przykładzie:

            public class MyCustomTextAnnouncementActivity extends EngagementTextAnnouncementActivity
            {
              @Override
              protected String getLayoutName()
              {
                return "my_text_announcement";  // tell super class to use R.layout.my_text_announcement
              }
            }

Aby zastąpić kategorii domyślny tekst anonsów, po prostu zastąpić `android:name="com.microsoft.azure.engagement.reach.activity.EngagementTextAnnouncementActivity"` przy implementacji.

W podobny sposób można dostosować Web anonsów i sond.

W przypadku anonsów zawierających sieci web można rozszerzyć `EngagementWebAnnouncementActivity` ani deklarować Twoich działaniach w `AndroidManifest.xml` , takich jak w poniższym przykładzie:

            <activity android:name="com.your_company.MyCustomWebAnnouncementActivity">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="my_category" />
                <data android:mimeType="text/html" />    <!-- only difference with text announcements in the intent is the data mime type -->
              </intent-filter>
            </activity>

Ankiety można rozszerzyć `EngagementPollActivity` ani deklarować sieci w `AndroidManifest.xml` , takich jak w poniższym przykładzie:

            <activity android:name="com.your_company.MyCustomPollActivity">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.POLL"/>
                <category android:name="my_category" />
              </intent-filter>
            </activity>

##### <a name="implementation-from-scratch"></a>Implementacja od podstaw
Można zaimplementować kategorie działań anonsu (i sondowania) bez rozszerzenie jednej z `Engagement*Activity` klasy udostępniane przez zestaw SDK Reach. Jest to przydatne na przykład jeśli chcesz zdefiniować układu, który nie używa tego samego widoków jako standardowych układów.

Podobnie jak dostosowywania powiadomień zaawansowane zalecane jest aby przyjrzeć się kodu źródłowego standardowej implementacji.

Poniżej przedstawiono niektóre czynności, które należy wziąć pod uwagę: Reach spowoduje uruchomienie działania o określonych zamiar (odpowiadający filtru konwersji) oraz dodatkowy parametr, który jest identyfikatorem zawartości.

Można pobrać zawartości obiektu, który zawiera pola, które można określić podczas tworzenia kampanii w witrynie sieci web można to zrobić:

            public class MyCustomTextAnnouncement extends EngagementActivity
            {
              private EngagementAnnouncement mContent;

              @Override
              protected void onCreate(Bundle savedInstanceState)
              {
                super.onCreate(savedInstanceState);

                /* Get content */
                mContent = EngagementReachAgent.getInstance(this).getContent(getIntent());
                if (mContent == null)
                {
                  /* If problem with content, exit */
                  finish();
                  return;
                }

                setContentView(R.layout.my_text_announcement);

                /* Configure views by querying fields on mContent */
                // ...
              }
            }

Statystyki, zgłoś zawartość jest wyświetlana w `onResume` zdarzeń:

            @Override
            protected void onResume()
            {
             /* Mark the content displayed */
             mContent.displayContent(this);
             super.onResume();
            }

Następnie, nie zapomnij wywołań albo `actionContent(this)` lub `exitContent(this)` zawartości obiektu przed działanie przechodzi w tle.

Nie można wywołać albo `actionContent` lub `exitContent`, statystyki nie zostaną wysłane (tzn. nie analytics kampanii) i co ważniejsze, dalej kampanii nie będzie powiadamiany, aż do ponownego uruchomienia procesu aplikacji.

Orientacja lub innych zmian konfiguracji może wykonać kod trudnych do określenia, czy działanie przechodzi w tle lub nie, standardowej implementacji upewnia się, zawartość jest zgłaszana jako zakończony, gdy użytkownik opuści działania (albo przez naciśnięcie przycisku `HOME`lub `BACK`), ale nie w przypadku zmiany orientacji.

Oto interesujące część implementacji:

            @Override
            protected void onUserLeaveHint()
            {
              finish();
            }

            @Override
            protected void onPause()
            {
              if (isFinishing() && mContent != null)
              {
                /*
                 * Exit content on exit, this is has no effect if another process method has already been
                 * called so we don't have to check anything here.
                 */
                mContent.exitContent(this);
              }
              super.onPause();
            }

Jak widać, jeśli wywołujesz `actionContent(this)` zakończyła działanie, a następnie `exitContent(this)` można bezpiecznie wywołać bez żadnego efektu.

[here]:http://developer.android.com/tools/extras/support-library.html#Downloading
[Google Cloud Messaging]:http://developer.android.com/guide/google/gcm/index.html
[usługi Amazon Device Messaging]:https://developer.amazon.com/sdk/adm.html
