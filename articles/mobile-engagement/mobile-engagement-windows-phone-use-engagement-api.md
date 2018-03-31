---
title: Jak zaangażowania interfejsu API w systemie Windows Phone Silverlight
description: Jak zaangażowania interfejsu API w systemie Windows Phone Silverlight
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: ae2ba2e8-f75b-4dee-a164-a7dd65d35a23
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: na
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 03673924ee7734fcf6f1f4f7c744616844fdc87a
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-use-the-engagement-api-on-windows-phone-silverlight"></a>Jak zaangażowania interfejsu API w systemie Windows Phone Silverlight
> [!IMPORTANT]
> Usługa Azure Mobile Engagement wycofaniu na 3/31/2018. Ta strona zostanie usunięta wkrótce po.
> 

Ten dokument jest dodatkiem do dokumentu [jak zintegrowana usługa Mobile Engagement w aplikacji Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md). Zapewnia on głębokość szczegółowe informacje dotyczące raportu statystyk aplikacji za pomocą interfejsu API usługi Engagement.

Jeśli mają zaangażowania do raportów aplikacji sesji, działania, awarii (Crash) i informacje techniczne, a następnie Najłatwiejszą metodą jest zapewnienie wszystkie Twoje `PhoneApplicationPage` klasy podrzędne dziedziczą `EngagementPage` klasy.

Jeśli chcesz zrobić więcej, na przykład, jeśli zachodzi konieczność raportów aplikacji określonych zdarzeń, błędów i zadań, lub jeśli zajdzie potrzeba raportu działania aplikacji w inny sposób niż jeden zaimplementowana w `EngagementPage` klasy, a następnie należy użyć interfejsu API usługi Engagement.

Interfejsu API programu zaangażowania jest zapewniana przez `EngagementAgent` klasy. Można dostęp do tych metod, za pomocą `EngagementAgent.Instance`.

Nawet wtedy, gdy moduł agenta nie została zainicjowana, każde wywołanie interfejsu API jest opóźniona i zostanie wykonana ponownie gdy agent nie jest dostępny.

## <a name="engagement-concepts"></a>Pojęcia dotyczące usługi Engagement
Następujące części uściślić pojęcia Mobile Engagement dla platformy Windows Phone.

### <a name="session-and-activity"></a>`Session` i `Activity`
*Działania* jest zazwyczaj skojarzony z jedną stronę aplikacji, to znaczy *działania* rozpoczyna się, gdy strona jest wyświetlana i zatrzymywana, gdy strona zostanie zamknięty: dotyczy to sytuacji, gdy zestaw SDK Engagement jest zintegrowany przy użyciu `EngagementPage` klasy.

Ale *działania* mogą również być kontrolowane ręcznie przy użyciu interfejsu API usługi Engagement. Dzięki temu można podzielić na daną stronę w kilku części sub, aby uzyskać więcej informacji o korzystaniu z tej strony (na przykład aby częstotliwość znane i jak długo okna dialogowe są używane wewnątrz tej strony).

## <a name="reporting-activities"></a>Działania raportowania
### <a name="user-starts-a-new-activity"></a>Użytkownik uruchamia nowe działanie
#### <a name="reference"></a>Informacje ogólne
            void StartActivity(string name, Dictionary<object, object> extras = null)

Należy wywołać `StartActivity()` każdej zmianie działania użytkownika. W pierwszym wywołaniu tej funkcji uruchamia nową sesję użytkownika.

> [!IMPORTANT]
> Zestaw SDK automatycznie Wywołaj metodę EndActivity po zamknięciu aplikacji. W związku z tym zaleca do wywoływania metody StartActivity zawsze, gdy działanie zmiany użytkowników i nigdy Wywołaj metodę EndActivity, ponieważ wywołanie tej metody wymusza bieżącej sesji, aby zostać zakończona.
> 
> 

#### <a name="example"></a>Przykład
            EngagementAgent.Instance.StartActivity("main", new Dictionary<object, object>() {{"example", "data"}});

### <a name="user-ends-his-current-activity"></a>Użytkownik kończy swoje bieżące działanie
#### <a name="reference"></a>Informacje ogólne
            void EndActivity()

Należy wywołać `EndActivity()` co najmniej raz, gdy użytkownik kończy swoje ostatnie działanie. Informuje Engagement SDK, że użytkownik jest obecnie w stanie bezczynności i sesji użytkownika muszą być zamknięte raz limit czasu sesji wygaśnie (jeśli wywołujesz `StartActivity()` przed upłynięciem limitu czasu sesji, sesja jest kontynuowane po prostu).

#### <a name="example"></a>Przykład
            EngagementAgent.Instance.EndActivity();

## <a name="reporting-jobs"></a>Zadania raportowania
### <a name="start-a-job"></a>Uruchom zadanie
#### <a name="reference"></a>Informacje ogólne
            void StartJob(string name, Dictionary<object, object> extras = null)

To zadanie służy do śledzenia zadań podaje w danym okresie czasu.

#### <a name="example"></a>Przykład
            // An upload begins...

            // Set the extras
            var extras = new Dictionary<object, object>();
            extras.Add("title", "avatar");
            extras.Add("type", "image");

            EngagementAgent.Instance.StartJob("uploadData", extras);

### <a name="end-a-job"></a>Zakończenia zadania
#### <a name="reference"></a>Informacje ogólne
            void EndJob(string name)

Jak zadanie śledzone przez zadanie zostało zakończone, należy wywołać metodę EndJob dla tego zadania, podając nazwę zadania.

#### <a name="example"></a>Przykład
            // In the previous section, we started an upload tracking with a job
            // Then, the upload ends

            EngagementAgent.Instance.EndJob("uploadData");

## <a name="reporting-events"></a>Zdarzenia raportowania
Brak trzy typy zdarzeń:

* Autonomiczny zdarzenia
* Zdarzenia sesji
* Zdarzenia zadań

### <a name="standalone-events"></a>Autonomiczny zdarzenia
#### <a name="reference"></a>Informacje ogólne
            void SendEvent(string name, Dictionary<object, object> extras = null)

Autonomiczny zdarzeń może wystąpić poza kontekstem sesji.

#### <a name="example"></a>Przykład
            EngagementAgent.Instance.SendEvent("event", extra);

### <a name="session-events"></a>Zdarzenia sesji
#### <a name="reference"></a>Informacje ogólne
            void SendSessionEvent(string name, Dictionary<object, object> extras = null)

Zdarzenia sesji są zwykle używane do zgłaszania akcji wykonywanych przez użytkownika podczas jego sesji.

#### <a name="example"></a>Przykład
**Bez danych:**

            EngagementAgent.Instance.SendSessionEvent("sessionEvent");

            // or

            EngagementAgent.Instance.SendSessionEvent("sessionEvent", null);

**Z danymi:**

            Dictionary<object, object> extras = new Dictionary<object,object>();
            extras.Add("name", "data");
            EngagementAgent.Instance.SendSessionEvent("sessionEvent", extras);

### <a name="job-events"></a>Zdarzenia zadań
#### <a name="reference"></a>Informacje ogólne
            void SendJobEvent(string eventName, string jobName, Dictionary<object, object> extras = null)

Zdarzenia zadania są zazwyczaj używane do zgłaszania akcji wykonywanych przez użytkownika podczas wykonywania zadania.

#### <a name="example"></a>Przykład
            EngagementAgent.Instance.SendJobEvent("eventName", "jobName", extras);

## <a name="reporting-errors"></a>Raportowanie błędów
Brak trzy typy błędów:

* Błędy autonomiczny
* Błędy sesji
* Błędy zadań

### <a name="standalone-errors"></a>Błędy autonomiczny
#### <a name="reference"></a>Informacje ogólne
            void SendError(string name, Dictionary<object, object> extras = null)

Sprzecznie błędy sesji mogą wystąpić błędy autonomiczny poza kontekstem sesji.

#### <a name="example"></a>Przykład
            EngagementAgent.Instance.SendError("errorName", extras);

### <a name="session-errors"></a>Błędy sesji
#### <a name="reference"></a>Informacje ogólne
            void SendSessionError(string name, Dictionary<object, object> extras = null)

Błędy sesji są zwykle używane do raportów o błędach podczas sesji jego wpływu na użytkownika.

#### <a name="example"></a>Przykład
            EngagementAgent.Instance.SendSessionError("errorName", extra);

### <a name="job-errors"></a>Błędy zadań
#### <a name="reference"></a>Informacje ogólne
            void SendJobError(string errorName, string jobName, Dictionary<object, object> extras = null)

Błędy może być powiązane z uruchomionym zadaniem zamiast związany z bieżącą sesją użytkownika.

#### <a name="example"></a>Przykład
            EngagementAgent.Instance.SendJobError("errorName", "jobname", extra);

## <a name="reporting-crashes"></a>Raportowanie awarii (Crash)
Agent udostępnia dwie metody na wypadek awarii.

### <a name="send-an-exception"></a>Wyślij Wystąpił wyjątek
#### <a name="reference"></a>Informacje ogólne
            void SendCrash(Exception e, bool terminateSession = false)

#### <a name="example"></a>Przykład
Wystąpił wyjątek w dowolnym momencie możesz wysłać przez wywołanie metody:

            EngagementAgent.Instance.SendCrash(aCatchedException);

Opcjonalny parametr umożliwia również zakończyć sesję zaangażowania w tym samym czasie niż przy użyciu tej awarii. Aby to zrobić, należy wywołać:

            EngagementAgent.Instance.SendCrash(new Exception("example"), terminateSession: true);

Jeśli możesz to zrobić, zadania i sesji zostanie zamknięte zaraz po awarii wysyłania.

### <a name="send-an-unhandled-exception"></a>Wyślij nieobsługiwany wyjątek
#### <a name="reference"></a>Informacje ogólne
            void SendCrash(ApplicationUnhandledExceptionEventArgs e)

Engagement udostępnia również metody do wysyłania nieobsługiwanych wyjątków. Jest to szczególnie przydatne, gdy jest używany wewnątrz obsługi zdarzeń UnhandledException silverlight.

Ta metoda będzie **zawsze** przerwanie zaangażowania sesji i zadania po wywołaniu.

#### <a name="example"></a>Przykład
Służy on do implementowania obsługi własnych UnhandledException (zwłaszcza, jeśli wyłączono automatyczne awarii, funkcję zaangażowania raportowania). Na przykład w `Application_UnhandledException` metody `App.xaml.cs` pliku:

            // In your App.xaml.cs file

            // Code to execute on Unhandled Exceptions
            private void Application_UnhandledException(object sender, ApplicationUnhandledExceptionEventArgs e)
            {
              // your own code

              EngagementAgent.Instance.SendCrash(e);
            }

## <a name="onactivated"></a>OnActivated
### <a name="reference"></a>Informacje ogólne
            void OnActivated(ActivatedEventArgs e)

Gdy użytkownik przechodzi do przodu, od aplikacji, po wywołaniu zdarzenia dezaktywowane, system operacyjny spróbuje ponownie przełączyć aplikację do stanu nieaktywni. Następnie aplikacja jest chowanie. W tym procesie aplikacja zostanie zakończona, ale niektóre dane o stanie aplikacji i poszczególnych stron w aplikacji zostaną zachowane.

Należy wstawić `EngagementAgent.Instance.OnActivated(e)` w `Application_Activated` metody plik App.xaml.cs, aby zresetować agenta zaangażowania, gdy aplikacja została schowany.

### <a name="example"></a>Przykład
            // Inside your App.xaml.cs file

            // Code to execute when the application is activated (brought to foreground)
            // This code will not execute when the application is first launched
            private void Application_Activated(object sender, ActivatedEventArgs e)
            {
              EngagementAgent.Instance.OnActivated(e);
            }

## <a name="device-id"></a>Identyfikator urządzenia
            String GetDeviceId()

Identyfikator urządzenia zaangażowania można uzyskać przez wywołanie tej metody.

## <a name="extras-parameters"></a>Dodatkowe parametry
Dowolne dane można dołączyć do zdarzenia, błąd, działania lub zadania. Dane te mogą być elementami struktury, za pomocą słownika. Klucze i wartości mogą być dowolnego typu.

Dodatkowe dane są serializowane, tak aby wstawić własne typu dodatki należy dodać kontraktu danych dla tego typu.

### <a name="example"></a>Przykład
Utworzymy nową klasę "Osoby".

            using System.Runtime.Serialization;

            namespace Engagement.Agent
            {
              [DataContract]
              public class Person
              {
                public Person(string name, int age)
                {
                  Age = age;
                  Name = name;
                }

                // Properties

                [DataMember]
                public int Age
                {
                  get;
                  set;
                }

                [DataMember]
                public string Name
                {
                  get;
                  set; 
                }
              }
            }

Następnie dodamy `Person` wystąpienia dodatkową.

            Person person = new Person("Engagement Haddock", 51);
            var extras = new Dictionary<object, object>();
            extras.Add("people", person);

            EngagementAgent.Instance.SendEvent("Event", extras);

> [!WARNING]
> Jeśli inne typy obiektów, upewnij się, że ich metodę ToString() jest zaimplementowana do zwrócenia człowieka czytelnych ciągów.
> 
> 

### <a name="limits"></a>Limity
#### <a name="keys"></a>Klucze
Każdy klucz w obiekcie musi odpowiadać następującym wyrażeniem regularnym:

`^[a-zA-Z][a-zA-Z_0-9]*$`

Oznacza to, że klucze musi rozpoczynać się od co najmniej jedną literą, następują litery, cyfry i znaki podkreślenia (\_).

#### <a name="size"></a>Rozmiar
Dodatki są ograniczone do **1024** znaków w wywołaniu.

## <a name="reporting-application-information"></a>Raportowanie informacji o aplikacji
### <a name="reference"></a>Informacje ogólne
            void SendAppInfo(Dictionary<object, object> appInfos)

Funkcja śledzenia informacji (lub innych aplikacji szczegółowych informacji) przy użyciu SendAppInfo() ręcznie może raportować.

Należy pamiętać, że te informacje mogą zostać przesłane przyrostowo: tylko najnowszą wartość dla danego klucza zostaną zachowane dla danego urządzenia. Podobnie jak dodatkowe zdarzenia użycie słownika\<obiektu, obiekt\> dołączyć informacje.

### <a name="example"></a>Przykład
            Dictionary<object, object> appInfo = new Dictionary<object, object>()
            {
               {"subscription", "2013-12-07"},
               {"premium", "true"}
            };

            EngagementAgent.Instance.SendAppInfo(appInfo);

### <a name="limits"></a>Limity
#### <a name="keys"></a>Klucze
Każdy klucz w obiekcie musi odpowiadać następującym wyrażeniem regularnym:

`^[a-zA-Z][a-zA-Z_0-9]*$`

Oznacza to, że klucze musi rozpoczynać się od co najmniej jedną literą, następują litery, cyfry i znaki podkreślenia (\_).

#### <a name="size"></a>Rozmiar
Informacje o aplikacji są ograniczone do **1024** znaków w wywołaniu.

W poprzednim przykładzie JSON na serwer wysyłane jest 44 znaków:

            {"subscription":"2013-12-07","premium":"true"}

## <a name="logging"></a>Rejestrowanie
### <a name="enable-logging"></a>Włączanie rejestrowania
Można skonfigurować zestaw SDK do tworzenia dzienników testu w konsoli środowiska IDE.
Dzienniki te nie są uaktywnione domyślnie. Aby dostosować to, zaktualizuj właściwość `EngagementAgent.Instance.TestLogEnabled` do jednej z dostępnych wartości `EngagementTestLogLevel` wyliczenia, na przykład:

            EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
            EngagementAgent.Instance.Init();
