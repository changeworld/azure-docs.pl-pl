---
title: Rejestrowanie w aplikacjach MSAL | Azure
description: Więcej informacji na temat rejestrowania się w aplikacjach firmy Microsoft Authentication Library (MSAL).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/22/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 58f18995d46ca61ae68a7b226bbfc9a286e73a0b
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2019
ms.locfileid: "65544092"
---
# <a name="logging"></a>Rejestrowanie
Aplikacje Microsoft Authentication Library (MSAL), aby wygenerować komunikaty dziennika, które mogą pomóc zdiagnozować problemy i podaj szczegóły. Aplikację można skonfigurować rejestrowanie przy użyciu kilku wierszy kodu i niestandardowe kontrolować poziom szczegółowości oraz czy dane osobiste i organizacyjne są rejestrowane. Zalecane jest, ustaw wywołanie zwrotne rejestrowania biblioteki MSAL i umożliwiają użytkownikom na przesłanie dzienników, gdy występują problemy z uwierzytelnianiem.

## <a name="logging-levels"></a>Poziomy rejestrowania

Rejestrator MSAL firmy umożliwia kilka poziomów szczegółowości można przechwytywania:

- Błąd: Wskazuje Niestety, wystąpił problem i wystąpił błąd. Służy do debugowania i identyfikowania problemów.
- Ostrzeżenie: Zdarzenia, które są pytanie i aplikacja potrzebuje więcej informacji na. Istnieje nie zawsze zostały komunikat o błędzie lub awaria, ale przeznaczone do diagnostyki i precyzyjne określenie problemów.
- Informacje: Biblioteka MSAL będzie rejestrować zdarzenia przeznaczone do celów informacyjnych, niekoniecznie przeznaczonych do debugowania.
- verbose: Domyślne. Biblioteka MSAL będzie Zaloguj się dużymi ilościami informacji i podaje pełnych szczegółów, w jakie zachowanie biblioteki.

## <a name="personal-and-organizational-data"></a>Danych osobistych i organizacyjnych
Domyślnie rejestratora MSAL nie przechwytuje wysoce poufnych danych osobistych lub organizacyjnych. Biblioteka udostępnia opcję Włącz rejestrowanie danych osobistych i organizacyjnych, jeśli zdecydujesz się to zrobić.

## <a name="logging-in-msalnet"></a>Logowanie do platformy MSAL.NET
W MSAL 3.x, rejestrowanie jest ustawiana dla poszczególnych aplikacji na tworzenie aplikacji za pomocą `.WithLogging` modyfikator konstruktora. Ta metoda przyjmuje następujące parametry opcjonalne:

- *Poziom* umożliwia zdecydować, której poziom rejestrowania. Ustawienie błędy tylko występują błędy
- *PiiLoggingEnabled* pozwala na rejestrowanie osobistych i organizacyjnych danych, jeśli ustawiono wartość true. Domyślnie to jest wartość false, dzięki czemu aplikacja nie rejestruje danych osobowych.
- *LogCallback* jest ustawiona na obiekt delegowany, który wykonuje rejestrowanie. Jeśli *PiiLoggingEnabled* ma wartość true, ta metoda będzie odbierać komunikaty dwa razy: jeden raz z *containsPii* parametr ma wartość FAŁSZ i wiadomość bez danych osobowych i drugi raz z *containsPii* parametr ma wartość true, a komunikat może zawierać dane osobowe. W niektórych przypadkach (kiedy komunikat nie zawiera danych osobowych) wiadomości będą takie same.
- *DefaultLoggingEnabled* umożliwia domyślne rejestrowanie dla platformy. Domyślnie ma wartość false. Jeśli ustawisz wartość "true", jej używa śledzenia zdarzeń w aplikacjach pulpitu/platformy uniwersalnej systemu Windows, NSLog w systemie iOS i logcat w systemie Android.

```csharp
class Program
 {
  private static void Log(LogLevel level, string message, bool containsPii)
  {
     if (containsPii)
     {
        Console.ForegroundColor = ConsoleColor.Red;
     }
     Console.WriteLine($"{level} {message}");
     Console.ResetColor();
  }

  static void Main(string[] args)
  {
    var scopes = new string[] { "User.Read" };

    var application = PublicClientApplicationBuilder.Create("<clientID>")
                      .WithLogging(Log, LogLevel.Info, true)
                      .Build();

    AuthenticationResult result = application.AcquireTokenInteractive(scopes)
                                             .ExecuteAsnc();
  }
 }
 ```

 ## <a name="logging-in-msaljs"></a>Rejestrowanie w MSAL.js

 Można włączyć rejestrowanie w MSAL.js przez przekazanie obiektu rejestratora podczas konfiguracji do tworzenia `UserAgentApplication` wystąpienia. Ten obiekt Rejestrator ma następujące właściwości:

- *localCallback*: wystąpienie wywołania zwrotnego, podanym przez dewelopera w celu umożliwienia użycia i publikowanie dzienników w sposób niestandardowy. Implementuje metody localCallback w zależności od tego, jaki sposób przekierowywania dzienników.

- *poziom* (opcjonalnie): poziom dziennika można konfigurować. Dostępne są następujące poziomy dziennika obsługiwane: Błąd, ostrzeżenie, informacje o pełne. Wartość domyślna to informacje.

- *piiLoggingEnabled* (opcjonalnie): umożliwia rejestrowanie osobistych i organizacyjnych danych, jeśli ustawiono wartość true. Domyślnie ta ma wartość false, aby aplikacja nie rejestruje danych osobowych. Domyślne dane wyjściowe, takie jak konsola, Logcat lub NSLog nigdy nie są zapisywane dzienniki danych osobowych. Domyślnym ustawieniem jest wartość false.

- *Identyfikator korelacji* (opcjonalnie): Unikatowy identyfikator używany do mapowania żądania z odpowiedzią na potrzeby debugowania. Wartość domyślna to identyfikator guid wersji 4 RFC4122 (128-bitowy).

```javascript

function loggerCallback(logLevel, message, containsPii) {
   console.log(message);
}

var msalConfig = {
    auth: {
        clientId: “abcd-ef12-gh34-ikkl-ashdjhlhsdg”,
    },
    system: {
        logger: {
            localCallback: loggerCallback,
            level: Msal.LogLevel.Verbose,
            piiLoggingEnabled: false,
            correlationId: '1234'
        }
    }
}

var UserAgentApplication = new Msal.UserAgentApplication(msalConfig);
```
