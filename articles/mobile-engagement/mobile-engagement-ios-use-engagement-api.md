---
title: Sposób użycia interfejsu API programu zaangażowania w systemie iOS
description: Najnowsze iOS SDK — sposób użycia interfejsu API programu zaangażowania w systemie iOS
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 1fb4509e-3804-46c1-949f-1cf727f91f9f
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: na
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 189a3029449a3161da2a20f940b77a5bb63bd1ef
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="how-to-use-the-engagement-api-on-ios"></a>Sposób użycia interfejsu API programu zaangażowania w systemie iOS
> [!IMPORTANT]
> Usługa Azure Mobile Engagement zostanie wycofana w dniu 31-03-2018. Ta strona zostanie usunięta wkrótce potem.
> 

Ten dokument jest dodatkiem do dokumentu jak zintegrować zaangażowania w systemie iOS: dostarcza w głębokość szczegółowe informacje dotyczące raportu statystyk aplikacji za pomocą interfejsu API usługi Engagement.

Należy pamiętać, że jeśli chcesz tylko zaangażowania do raportów aplikacji sesji, działania, awarii (Crash) i informacje techniczne, następnie Najłatwiejszą metodą jest zapewnienie wszystkie niestandardowe `UIViewController` obiekty dziedziczyć odpowiadającego `EngagementViewController` klasy.

Jeśli chcesz zrobić więcej, na przykład, jeśli zachodzi konieczność raportów aplikacji określonych zdarzeń, błędów i zadań, lub jeśli zajdzie potrzeba raportu działania aplikacji w inny sposób niż jeden zaimplementowana w `EngagementViewController` klasy, a następnie należy użyć interfejsu API usługi Engagement.

Interfejsu API programu zaangażowania jest zapewniana przez `EngagementAgent` klasy. Wystąpienie tej klasy można pobrać przez wywołanie metody `[EngagementAgent shared]` metody statycznej (należy pamiętać, że `EngagementAgent` obiektu zwróconego jest pojedyncza).

Przed wszystkie wywołania interfejsu API `EngagementAgent` obiekt musi zostać zainicjowany przez wywołanie metody `[EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}"];`

## <a name="engagement-concepts"></a>Pojęcia dotyczące usługi Engagement
Następujące części uściślić typowe [Mobile Engagement pojęcia](mobile-engagement-concepts.md) dla platformy iOS.

### <a name="session-and-activity"></a>`Session` i `Activity`
*Działania* są zwykle skojarzone z jednego ekranu aplikacji, to znaczy *działania* uruchamiana, gdy ekran jest wyświetlany i zatrzymuje po zamknięciu ekranu: dotyczy to sytuacji, gdy zestaw SDK Engagement jest zintegrowany przy użyciu `EngagementViewController` klasy.

Ale *działania* mogą również być kontrolowane ręcznie przy użyciu interfejsu API usługi Engagement. Dzięki temu podzielenie danej ekranu w kilku części sub, aby uzyskać więcej informacji na temat użycia tego ekranu (na przykład aby częstotliwość znane i jak długo okna dialogowe są używane wewnątrz tego ekranu).

## <a name="reporting-activities"></a>Działania raportowania
### <a name="user-starts-a-new-activity"></a>Użytkownik uruchamia nowe działanie
            [[EngagementAgent shared] startActivity:@"MyUserActivity" extras:nil];

Należy wywołać `startActivity()` każdej zmianie działania użytkownika. W pierwszym wywołaniu tej funkcji uruchamia nową sesję użytkownika.

### <a name="user-ends-his-current-activity"></a>Użytkownik kończy swoje bieżące działanie
            [[EngagementAgent shared] endActivity];

> [!WARNING]
> Należy **nigdy** wywołanie tej funkcji przez użytkownika, z wyjątkiem, jeśli chcesz podzielić jedno użycie aplikacji na kilka sesji: wywołanie tej funkcji spowodowałoby zakończenie bieżącej sesji, więc, kolejne wywołanie `startActivity()` Czy uruchomić nową sesję. Ta funkcja jest wywoływana przez zestaw SDK automatycznie, gdy aplikacja jest zamknięty.
> 
> 

## <a name="reporting-events"></a>Zdarzenia raportowania
### <a name="session-events"></a>Zdarzenia sesji
Zdarzenia sesji są zwykle używane do zgłaszania akcji wykonywanych przez użytkownika podczas jego sesji.

**Przykład bez dodatkowe dane:**

    @implementation MyViewController {
       [...]
       - (void)willRotateToInterfaceOrientation:(UIInterfaceOrientation)toInterfaceOrientation duration:(NSTimeInterval)duration
       {
        [super willRotateToInterfaceOrientation:toInterfaceOrientation duration:duration];
            ...
        [[EngagementAgent shared] sendSessionEvent:@"will_rotate" extras:nil];
            ...
       }
       [...]
    }

**Przykład: dodatkowe dane:**

    @implementation MyViewController {
       [...]
       - (void)willRotateToInterfaceOrientation:(UIInterfaceOrientation)toInterfaceOrientation duration:(NSTimeInterval)duration
       {
        [super willRotateToInterfaceOrientation:toInterfaceOrientation duration:duration];
            ...
        NSMutableDictionary* extras = [NSMutableDictionary dictionary];
        [extras setObject:[NSNumber numberWithInt:toInterfaceOrientation] forKey:@"to_orientation_id"];
        [extras setObject:[NSNumber numberWithDouble:duration] forKey:@"duration"];
        [[EngagementAgent shared] sendSessionEvent:@"will_rotate" extras:extras];
            ...
       }
       [...]
    }

### <a name="standalone-events"></a>Autonomiczny zdarzenia
Sprzecznie zdarzenia sesji zdarzeń autonomicznego może służyć poza kontekstem sesji.

**Przykład:**

    [[EngagementAgent shared] sendEvent:@"received_notification" extras:nil];

## <a name="reporting-errors"></a>Raportowanie błędów
### <a name="session-errors"></a>Błędy sesji
Błędy sesji są zwykle używane do raportów o błędach podczas sesji jego wpływu na użytkownika.

**Przykład:**

    /** The user has entered invalid data in a form */
    @implementation MyViewController {
      [...]
      -(void)onMyFormSubmitted:(MyForm*)form {
        [...]
        /* The user has entered an invalid email address */
        [[EngagementAgent shared] sendSessionError:@"sign_up_email" extras:nil]
        [...]
      }
      [...]
    }

### <a name="standalone-errors"></a>Błędy autonomiczny
Sprzeczności z sesji błędy błędy autonomiczny może służyć poza kontekstem sesji.

**Przykład:**

    [[EngagementAgent shared] sendError:@"something_failed" extras:nil];

## <a name="reporting-jobs"></a>Zadania raportowania
**Przykład:**

Załóżmy, że chcesz zgłosić w czasie trwania procesu logowania:

    [...]
    -(void)signIn
    {
      /* Start job */
      [[EngagementAgent shared] startJob:@"sign_in" extras:nil];

      [... sign in ...]

      /* End job */
      [[EngagementAgent shared] endJob:@"sign_in"];
    }
    [...]

### <a name="report-errors-during-a-job"></a>Zgłoś błędy podczas wykonywania zadania
Błędy może być powiązane z uruchomionym zadaniem zamiast związany z bieżącą sesją użytkownika.

**Przykład:**

Załóżmy, że chcesz zgłosić błąd podczas procesu logowania:

    [...]
    -(void)signin
    {
      /* Start job */
      [[EngagementAgent shared] startJob:@"sign_in" extras:nil];

      BOOL success = NO;
      while (!success) {
        /* Try to sign in */
        NSError* error = nil;
        [self trySigin:&error];
        success = error == nil;

        /* If an error occured report it */
        if(!success)
        {
          [[EngagementAgent shared] sendJobError:@"sign_in_error"
                         jobName:@"sign_in"
                          extras:[NSDictionary dictionaryWithObject:[error localizedDescription] forKey:@"error"]];

          /* Retry after a moment */
          [NSThread sleepForTimeInterval:20];
        }
      }

      /* End job */
      [[EngagementAgent shared] endJob:@"sign_in"];
    };
    [...]

### <a name="events-during-a-job"></a>Zdarzenia podczas wykonywania zadania
Zdarzenia może być powiązane z uruchomionym zadaniem zamiast związany z bieżącą sesją użytkownika.

**Przykład:**

Załóżmy, że mamy sieci społecznościowych i używamy zadania do raportu całkowity czas, w którym użytkownik jest połączony z serwerem. Użytkownik może odbierać komunikaty z jego znajomych, to zdarzenia zadania.

    [...]
    - (void) signin
    {
      [...Sign in code...]
      [[EngagementAgent shared] startJob:@"connection" extras:nil];
    }
    [...]
    - (void) signout
    {
      [...Sign out code...]
      [[EngagementAgent shared] endJob:@"connection"];
    }
    [...]
    - (void) onMessageReceived
    {
      [...Notify user...]
      [[EngagementAgent shared] sendJobEvent:@"connection" jobName:@"message_received" extras:nil];
    }
    [...]

## <a name="extra-parameters"></a>Dodatkowe parametry
Dowolne dane, może zostać dołączona do zdarzeń, błędów, działań i zadań.

Te dane mogą być elementami struktury, używa klasy NSDictionary dla systemu iOS.

Należy pamiętać, że może zawierać dodatkowe `arrays(NSArray, NSMutableArray)`, `numbers(NSNumber class)`, `strings(NSString, NSMutableString)`, `urls(NSURL)`, `data(NSData, NSMutableData)` lub innych `NSDictionary` wystąpień.

> [!NOTE]
> Dodatkowy parametr jest serializowany w formacie JSON. Jeśli chcesz przekazać różnych obiektów niż opisane powyżej, musisz zaimplementować następującą metodę w klasie:
> 
> -(NSString*)JSONRepresentation;
> 
> Metoda powinna zwracać reprezentację obiektu w formacie JSON.
> 
> 

### <a name="example"></a>Przykład
    NSMutableDictionary* extras = [NSMutableDictionary dictionaryWithCapacity:2];
    [extras setObject:[NSNumber numberWithInt:123] forKey:@"video_id"];
    [extras setObject:@"http://foobar.com/blog" forKey:@"ref_click"];
    [[EngagementAgent shared] sendEvent:@"video_clicked" extras:extras];

### <a name="limits"></a>Limity
#### <a name="keys"></a>Klucze
Każdy klucz w `NSDictionary` musi być zgodna z następującym wyrażeniem regularnym:

`^[a-zA-Z][a-zA-Z_0-9]*`

Oznacza to, że klucze musi rozpoczynać się od co najmniej jedną literą, następują litery, cyfry i znaki podkreślenia (\_).

#### <a name="size"></a>Rozmiar
Dodatki są ograniczone do **1024** znaków na wywołanie (po zakodowaniu w formacie JSON przez agenta Engagement).

W poprzednim przykładzie JSON na serwer wysyłane jest 58 znaków:

    {"ref_click":"http:\/\/foobar.com\/blog","video_id":"123"}

## <a name="reporting-application-information"></a>Raportowanie informacji o aplikacji
Można ręcznie raportu śledzenia informacji (lub innych aplikacji szczegółowych informacji) przy użyciu `sendAppInfo:` funkcji.

Należy pamiętać, że te informacje mogą zostać przesłane przyrostowo: tylko najnowszą wartość dla danego klucza zostaną zachowane dla danego urządzenia.

Dodatki zdarzeń, takich jak `NSDictionary` klasa jest używana do abstrakcyjnej informacje o aplikacji, należy pamiętać, że tablice lub słowników podrzędne będzie traktowany jako płaska ciągów (za pomocą serializacji JSON).

**Przykład:**

    NSMutableDictionary* appInfo = [NSMutableDictionary dictionaryWithCapacity:2];
    [appInfo setObject:@"female" forKey:@"gender"];
    [appInfo setObject:@"1983-12-07" forKey:@"birthdate"]; // December 7th 1983
    [[EngagementAgent shared] sendAppInfo:appInfo];

### <a name="limits"></a>Limity
#### <a name="keys"></a>Klucze
Każdy klucz w `NSDictionary` musi być zgodna z następującym wyrażeniem regularnym:

`^[a-zA-Z][a-zA-Z_0-9]*`

Oznacza to, że klucze musi rozpoczynać się od co najmniej jedną literą, następują litery, cyfry i znaki podkreślenia (\_).

#### <a name="size"></a>Rozmiar
Informacje o aplikacji są ograniczone do **1024** znaków na wywołanie (po zakodowaniu w formacie JSON przez agenta Engagement).

W poprzednim przykładzie JSON na serwer wysyłane jest 44 znaków:

    {"birthdate":"1983-12-07","gender":"female"}
