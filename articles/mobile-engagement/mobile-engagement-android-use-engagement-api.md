---
title: Jak używać Engagement interfejsu API w systemie Android
description: Najnowszy zestaw SDK systemu Android — jak używać Engagement interfejsu API w systemie Android
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 09b62659-82ae-4a55-8784-fca0b6b22eaf
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: na
ms.topic: article
ms.date: 07/25/2016
ms.author: piyushjo;ricksal
ms.openlocfilehash: 79d0652be227dd6703a35d31409cf8e0d9c59519
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-use-the-engagement-api-on-android"></a>Jak używać Engagement interfejsu API w systemie Android
> [!IMPORTANT]
> Usługa Azure Mobile Engagement wycofaniu na 3/31/2018. Ta strona zostanie usunięta wkrótce po.
> 

Ten dokument jest dodatkiem do dokumentu [opcje zaawansowane raportowanie dla systemu Android zestaw SDK usługi Mobile Engagement](mobile-engagement-android-advanced-reporting.md). Zapewnia on głębokość szczegółowe informacje dotyczące raportu statystyk aplikacji za pomocą interfejsu API usługi Engagement.

Należy pamiętać, że jeśli chcesz tylko zaangażowania do raportów aplikacji sesji, działania, awarii (Crash) i informacje techniczne, następnie Najłatwiejszą metodą jest zapewnienie wszystkie Twoje `Activity` klasy podrzędne dziedziczą odpowiadającego `EngagementActivity` klasy.

Jeśli chcesz zrobić więcej, na przykład, jeśli zachodzi konieczność raportów aplikacji określonych zdarzeń, błędów i zadań, lub jeśli zajdzie potrzeba raportu działania aplikacji w inny sposób niż jeden zaimplementowana w `EngagementActivity` klasy, a następnie należy użyć interfejsu API usługi Engagement.

Interfejsu API programu zaangażowania jest zapewniana przez `EngagementAgent` klasy. Wystąpienie tej klasy można pobrać przez wywołanie metody `EngagementAgent.getInstance(Context)` metody statycznej (należy pamiętać, że `EngagementAgent` obiektu zwróconego jest pojedyncza).

## <a name="engagement-concepts"></a>Pojęcia dotyczące usługi Engagement
Następujące części uściślić typowe [Mobile Engagement pojęcia](mobile-engagement-concepts.md), platformy systemu Android.

### <a name="session-and-activity"></a>`Session` i `Activity`
Jeśli użytkownik pozostaje więcej niż kilka sekund bezczynności między dwiema *działania*, następnie ta sekwencja z *działania* jest podzielony na dwie różne *sesji*. Te kilka sekund, są nazywane "limit czasu sesji".

*Działania* są zwykle skojarzone z jednego ekranu aplikacji, to znaczy *działania* uruchamiana, gdy ekran jest wyświetlany i zatrzymuje po zamknięciu ekranu: dotyczy to sytuacji, gdy zestaw SDK Engagement jest zintegrowany przy użyciu `EngagementActivity` klasy.

Ale *działania* mogą również być kontrolowane ręcznie przy użyciu interfejsu API usługi Engagement. Dzięki temu podzielenie danej ekranu w kilku części sub, aby uzyskać więcej informacji na temat użycia tego ekranu (na przykład aby częstotliwość znane i jak długo okna dialogowe są używane wewnątrz tego ekranu).

## <a name="reporting-activities"></a>Działania raportowania
> [!IMPORTANT]
> Nie trzeba raportów działań, takich jak opisane w tej sekcji, jeśli używasz `EngagementActivity` klasy i jej wariantów zgodnie z objaśnieniem w sposób integracji usługi Engagement Android dokumentu.
> 
> 

### <a name="user-starts-a-new-activity"></a>Użytkownik uruchamia nowe działanie
            EngagementAgent.getInstance(this).startActivity(this, "MyUserActivity", null);
            // Passing the current activity is required for Reach to display in-app notifications, passing null will postpone such announcements and polls.

Należy wywołać `startActivity()` każdej zmianie działania użytkownika. W pierwszym wywołaniu tej funkcji uruchamia nową sesję użytkownika.

Najlepszym miejscem, aby wywołanie tej funkcji znajduje się na każde działanie `onResume` wywołania zwrotnego.

### <a name="user-ends-his-current-activity"></a>Użytkownik kończy swoje bieżące działanie
            EngagementAgent.getInstance(this).endActivity();

Należy wywołać `endActivity()` co najmniej raz, gdy użytkownik kończy swoje ostatnie działanie. Informuje Engagement SDK, że użytkownik jest obecnie w stanie bezczynności i sesji użytkownika muszą być zamknięte raz limit czasu sesji wygaśnie (jeśli wywołujesz `startActivity()` przed upłynięciem limitu czasu sesji, po prostu wznowić sesji).

Najlepszym miejscem, aby wywołanie tej funkcji znajduje się na każde działanie `onPause` wywołania zwrotnego.

## <a name="reporting-events"></a>Zdarzenia raportowania
### <a name="session-events"></a>Zdarzenia sesji
Zdarzenia sesji są zwykle używane do zgłaszania akcji wykonywanych przez użytkownika podczas jego sesji.

**Przykład bez dodatkowe dane:**

            public MyActivity extends EngagementActivity {
               [...]
               @Override
               public boolean onPrepareOptionsMenu(Menu menu) {
                  getEngagementAgent().sendSessionEvent("menu_shown", null);
               }
               [...]
            }

**Przykład: dodatkowe dane:**

            public MyActivity extends EngagementActivity {
              [...]
              @Override
              public boolean onMenuItemSelected(int featureId, MenuItem item) {
                Bundle extras = new Bundle();
                extras.putInt("id", item.getItemId());
                getEngagementAgent().sendSessionEvent("menu_selected", extras);
              }
              [...]
            }

### <a name="standalone-events"></a>Autonomiczny zdarzenia
Sprzecznie zdarzenia sesji zdarzeń autonomicznej może wystąpić poza kontekstem sesji.

**Przykład:**

Załóżmy, że chcesz zgłaszania zdarzeń występujących po wyzwoleniu emisji odbiornik:

            /** Triggered by Intent.ACTION_BATTERY_LOW */
            public BatteryLowReceiver extends BroadcastReceiver {
              [...]
              @Override
              public void onReceive(Context context, Intent intent) {
                EngagementAgent.getInstance(context).sendEvent("battery_low", null);
              }
              [...]
            }

## <a name="reporting-errors"></a>Raportowanie błędów
### <a name="session-errors"></a>Błędy sesji
Błędy sesji są zwykle używane do raportów o błędach podczas sesji jego wpływu na użytkownika.

**Przykład:**

            /** The user has entered invalid data in a form */
            public MyActivity extends EngagementActivity {
              [...]
              public void onMyFormSubmitted(MyForm form) {
                [...]
                /* The user has entered an invalid email address */
                getEngagementAgent().sendSessionError("sign_up_email", null);
                [...]
              }
              [...]
            }

### <a name="standalone-errors"></a>Błędy autonomiczny
Sprzecznie błędy sesji mogą wystąpić błędy autonomiczny poza kontekstem sesji.

**Przykład:**

Poniższy przykład pokazuje, jak zgłaszać błąd, gdy pamięć staje się niskim na klawiaturze telefonu w trakcie procesu aplikacji.

            public MyApplication extends EngagementApplication {

              @Override
              protected void onApplicationProcessLowMemory() {
                EngagementAgent.getInstance(this).sendError("low_memory", null);
              }
            }

## <a name="reporting-jobs"></a>Zadania raportowania
### <a name="example"></a>Przykład
Załóżmy, że chcesz zgłosić w czasie trwania procesu logowania:

            [...]
            public void signIn(Context context, ...) {

              /* We need an Android context to call the Engagement API, if you are extending Activity, Service, you can pass "this" */
              EngagementAgent engagementAgent = EngagementAgent.getInstance(context);

              /* Report sign in job has started */
              engagementAgent.startJob("sign_in", null);

              [... sign in ...]

              /* Report sign in job is now ended */
              engagementAgent.endJob("sign_in");
            }
            [...]

### <a name="report-errors-during-a-job"></a>Zgłoś błędy podczas wykonywania zadania
Błędy może być powiązane z uruchomionym zadaniem zamiast związany z bieżącą sesją użytkownika.

**Przykład:**

Załóżmy, że chcesz zgłosić błąd w trakcie możesz zalogować się proces:

[...] publiczny rejestrowanie void (kontekstu kontekstu,...) {

              /* We need an Android context to call the Engagement API, if you are extending Activity, Service, you can pass "this" */
              EngagementAgent engagementAgent = EngagementAgent.getInstance(context);

              /* Report sign in job has been started */
              engagementAgent.startJob("sign_in", null);

              /* Try to sign in */
              while(true)
                try {
                  trySignin();
                  break;
                }
                catch(Exception e) {
                  /* Report the error to Engagement */
                  engagementAgent.sendJobError("sign_in_error", "sign_in", null);

                  /* Retry after a moment */
                  sleep(2000);
                }
              [...]
              /* Report sign in job is now ended */
              engagementAgent.endJob("sign_in");
            }
            [...]

### <a name="reporting-events-during-a-job"></a>Zdarzenia raportowania podczas wykonywania zadania
Zdarzenia może być powiązane z uruchomionym zadaniem zamiast związany z bieżącą sesją użytkownika.

**Przykład:**

Załóżmy, że mamy sieci społecznościowych i używamy zadania do raportu całkowity czas, w którym użytkownik jest połączony z serwerem. Użytkownik może łączność w tle, nawet jeśli osoba korzysta z innej aplikacji lub gdy telefon jest uśpiony, więc nie ma żadnej sesji.

Użytkownik może odbierać komunikaty z jego znajomych, to zdarzenia zadania.

            [...]
            public void signin(Context context, ...) {
              [...Sign in code...]
              EngagementAgent.getInstance(context).startJob("connection", null);
            }
            [...]
            public void signout(Context context) {
              [...Sign out code...]
              EngagementAgent.getInstance(context).endJob("connection");
            }
            [...]
            public void onMessageReceived(Context context) {
              [...Notify in status bar...]
              EngagementAgent.getInstance(context).sendJobEvent("message_received", "connection", null);
            }
            [...]

## <a name="extra-parameters"></a>Dodatkowe parametry
Dowolne dane, może zostać dołączona do zdarzeń, błędów, działań i zadań.

Te dane mogą być elementami struktury, używa klasy pakietu dla systemu Android (w rzeczywistości działa jak dodatkowe parametry w lokalizacji docelowych z systemem Android). Należy pamiętać, że pakiet może zawierać tablic lub innego wystąpienia pakietu.

> [!IMPORTANT]
> Jeśli umieścisz w parcelable lub które można serializować parametrów, upewnij się, ich `toString()` zaimplementowano metoda zwraca ciąg zrozumiałą dla użytkownika. Klas możliwych do serializacji, zawierające pola z systemem innym niż przejściowy, które nie są serializacji spowoduje awarii systemu Android, gdy będzie wywoływać `bundle.putSerializable("key",value);`
> 
> [!WARNING]
> Tablice rozrzedzonych w dodatkowe parametry nie są obsługiwane, oznacza to, że nie można serializować jako tablica. Należy przekonwertować je na standardowe tablice przed jego użyciem w dodatkowe parametry.
> 
> 

### <a name="example"></a>Przykład
            Bundle extras = new Bundle();
            extras.putString("video_id", 123);
            extras.putString("ref_click", "http://foobar.com/blog");
            EngagementAgent.getInstance(context).sendEvent("video_clicked", extras);

### <a name="limits"></a>Limity
#### <a name="keys"></a>Klucze
Każdy klucz w `Bundle` musi być zgodna z następującym wyrażeniem regularnym:

`^[a-zA-Z][a-zA-Z_0-9]*`

Oznacza to, że klucze musi rozpoczynać się od co najmniej jedną literą, następują litery, cyfry i znaki podkreślenia (\_).

#### <a name="size"></a>Rozmiar
Dodatki są ograniczone do **1024** znaków na wywołanie (po zakodowaniu w formacie JSON przez usługę Engagement).

W poprzednim przykładzie JSON na serwer wysyłane jest 58 znaków:

            {"ref_click":"http:\/\/foobar.com\/blog","video_id":"123"}

## <a name="reporting-application-information"></a>Raportowanie informacji o aplikacji
Można ręcznie raportu śledzenia informacji (lub innych aplikacji szczegółowych informacji) przy użyciu `sendAppInfo()` funkcji.

Należy pamiętać, że te informacje mogą zostać przesłane przyrostowo: tylko najnowszą wartość dla danego klucza zostaną zachowane dla danego urządzenia.

Jak dodatkowe zdarzenia Klasa pakietu jest używana do abstrakcyjnej informacje o aplikacji, należy pamiętać, że tablice lub podrzędne pakietów będzie traktowany jako płaska ciągów (za pomocą serializacji JSON).

### <a name="example"></a>Przykład
Oto przykładowy kod do wysyłania płci użytkownika i Data urodzenia:

            Bundle appInfo = new Bundle();
            appInfo.putString("status", "premium");
            appInfo.putString("expiration", "2016-12-07"); // December 7th 2016
            EngagementAgent.getInstance(context).sendAppInfo(appInfo);

### <a name="limits"></a>Limity
#### <a name="keys"></a>Klucze
Każdy klucz w `Bundle` musi być zgodna z następującym wyrażeniem regularnym:

`^[a-zA-Z][a-zA-Z_0-9]*`

Oznacza to, że klucze musi rozpoczynać się od co najmniej jedną literą, następują litery, cyfry i znaki podkreślenia (\_).

#### <a name="size"></a>Rozmiar
Informacje o aplikacji są ograniczone do **1024** znaków na wywołanie (po zakodowaniu w formacie JSON przez usługę Engagement).

W poprzednim przykładzie JSON na serwer wysyłane jest 44 znaków:

            {"expiration":"2016-12-07","status":"premium"}
