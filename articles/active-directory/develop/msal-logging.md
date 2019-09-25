---
title: Logowanie w aplikacjach MSAL | Platforma tożsamości firmy Microsoft
description: Dowiedz się więcej o rejestrowaniu w aplikacjach Microsoft Authentication Library (MSAL).
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/28/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4dad8a276cd40b1ff04bbced833b5d70cec4fc87
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71268580"
---
# <a name="logging-in-msal-applications"></a>Logowanie w aplikacjach MSAL

Aplikacje Microsoft Authentication Library (MSAL) generują komunikaty dziennika, które mogą pomóc zdiagnozować problemy. Aplikacja może skonfigurować rejestrowanie z kilkoma wierszami kodu i mieć kontrolę niestandardową na poziomie szczegółowości oraz o tym, czy dane osobiste i organizacyjne są rejestrowane. Zalecamy utworzenie wywołania zwrotnego rejestrowania MSAL i udostępnienie użytkownikom przesyłania dzienników w przypadku problemów z uwierzytelnianiem.

## <a name="logging-levels"></a>Poziomy rejestrowania

MSAL zapewnia kilka poziomów rejestrowania szczegółów:

- Błąd: Wskazuje, że wystąpił problem i został wygenerowany błąd. Służy do debugowania i identyfikowania problemów.
- Ostrzeżenie: Niekoniecznie Wystąpił błąd lub błąd, ale są one przeznaczone do diagnostyki i lokalizowania problemów.
- Informacje MSAL będzie rejestrować zdarzenia przeznaczone do celów informacyjnych niekoniecznie do debugowania.
- pełne Domyślne. MSAL rejestruje szczegółowe informacje o zachowaniu biblioteki.

## <a name="personal-and-organizational-data"></a>Dane osobowe i organizacyjne

Domyślnie Rejestrator MSAL nie przechwytuje żadnych wysoce poufnych danych osobistych lub organizacji. Biblioteka zawiera opcję włączenia rejestrowania danych osobowych i organizacji w przypadku podjęcia decyzji.

## <a name="logging-in-msalnet"></a>Logowanie w MSAL.NET

 > [!NOTE]
 > Aby uzyskać więcej informacji na temat MSAL.NET, zapoznaj się z witryną [typu wiki usługi MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki). Uzyskaj przykłady rejestrowania MSAL.NET i nie tylko.
 
W MSAL 3. x rejestrowanie jest ustawione na aplikację przy tworzeniu aplikacji przy użyciu `.WithLogging` modyfikatora konstruktora. Ta metoda pobiera parametry opcjonalne:

- *Poziom* pozwala określić żądany poziom rejestrowania. Ustawienie na błędy spowoduje tylko błędy
- *PiiLoggingEnabled* umożliwia rejestrowanie danych osobistych i organizacji, jeśli ustawiono wartość true. Domyślnie to ustawienie ma wartość FAŁSZ, aby aplikacja nie rejestrował danych osobowych.
- *LogCallback* jest ustawiona na delegata, który wykonuje rejestrowanie. Jeśli *PiiLoggingEnabled* ma wartość true, ta metoda otrzyma komunikaty dwa razy: jeden raz z parametrem *containsPii* jest równa false, a komunikat bez danych osobowych, a drugi — parametr *containsPii* ma wartość true, a komunikat może zawierać dane osobowe. W niektórych przypadkach (gdy wiadomość nie zawiera danych osobowych), komunikat będzie taki sam.
- *DefaultLoggingEnabled* włącza domyślne rejestrowanie dla platformy. Domyślnie jest to wartość false. Jeśli ustawisz ją na wartość true, używa ona śledzenia zdarzeń w aplikacjach Desktop/platformy UWP, NSLog w systemach iOS i Logcat w systemie Android.

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
                                             .ExecuteAsync().Result;
  }
 }
 ```

 ## <a name="logging-in-msaljs"></a>Rejestrowanie w MSAL. js

 Możesz włączyć rejestrowanie w MSAL. js, przekazując obiekt rejestratora podczas konfiguracji w celu utworzenia `UserAgentApplication` wystąpienia. Ten obiekt rejestratora ma następujące właściwości:

- `localCallback`: wystąpienie wywołania zwrotnego, które może zostać dostarczone przez dewelopera do korzystania z dzienników i publikowania ich w niestandardowy sposób. Zaimplementuj metodę localCallback w zależności od tego, jak chcesz przekierować dzienniki.

- `level`(opcjonalnie): konfigurowalny poziom rejestrowania. Obsługiwane poziomy dzienników są następujące: Błąd, ostrzeżenie, informacje, pełne. Wartość domyślna to info.

- `piiLoggingEnabled`(opcjonalnie): umożliwia rejestrowanie danych osobistych i organizacji, jeśli ustawiono wartość true. Domyślnie to ustawienie ma wartość FAŁSZ, aby aplikacja nie rejestrował danych osobowych. Osobiste dzienniki danych nigdy nie są zapisywane w domyślnych danych wyjściowych, takich jak Console, Logcat lub NSLog. Wartość domyślna to false.

- `correlationId`(opcjonalnie): unikatowy identyfikator używany do mapowania żądania z odpowiedzią na potrzeby debugowania. Wartość domyślna to RFC4122 w wersji 4 GUID (128 bitów).

```javascript
function loggerCallback(logLevel, message, containsPii) {
   console.log(message);
}

var msalConfig = {
    auth: {
        clientId: “abcd-ef12-gh34-ikkl-ashdjhlhsdg”,
    },
     system: {
             logger: new Msal.Logger(
                                loggerCallback ,{
                                     level: Msal.LogLevel.Verbose,
                                     piiLoggingEnabled: false,
                                     correlationId: '1234'
                                }
                        )
     }
}

var UserAgentApplication = new Msal.UserAgentApplication(msalConfig);
```

## <a name="logging-in-msal-for-ios-and-macos"></a>Logowanie w usłudze MSAL dla systemów iOS i macOS

Ustaw wywołanie zwrotne, aby przechwycić MSAL rejestrowanie i dołączyć je do rejestrowania własnych aplikacji. Podpis dla wywołania zwrotnego wygląda następująco:

```objc
/*!
    The LogCallback block for the MSAL logger
 
    @param  level           The level of the log message
    @param  message         The message being logged
    @param  containsPII     If the message might contain Personally Identifiable Information (PII)
                            this will be true. Log messages possibly containing PII will not be
                            sent to the callback unless PIllLoggingEnabled is set to YES on the
                            logger.

 */
typedef void (^MSALLogCallback)(MSALLogLevel level, NSString *message, BOOL containsPII);
```

Na przykład:

Objective-C
```objc
[MSALGlobalConfig.loggerConfig setLogCallback:^(MSALLogLevel level, NSString *message, BOOL containsPII)
    {
        if (!containsPII)
        {
#if DEBUG
            // IMPORTANT: MSAL logs may contain sensitive information. Never output MSAL logs with NSLog, or print, directly unless you're running your application in debug mode. If you're writing MSAL logs to file, you must store the file securely.
            NSLog(@"MSAL log: %@", message);
#endif
        }
    }];
```

Swift
```swift
MSALGlobalConfig.loggerConfig.setLogCallback { (level, message, containsPII) in
    if let message = message, !containsPII
    {
#if DEBUG
    // IMPORTANT: MSAL logs may contain sensitive information. Never output MSAL logs with NSLog, or print, directly unless you're running your application in debug mode. If you're writing MSAL logs to file, you must store the file securely.
    print("MSAL log: \(message)")
#endif
    }
}
```

### <a name="personal-identifiable-information-pii"></a>Identyfikowalne dane osobowe

Domyślnie MSAL nie przechwytuje ani nie rejestruje żadnych dane OSOBowe. Biblioteka umożliwia deweloperom aplikacji włączenie tej właściwości w klasie MSALLogger. Włączając dane OSOBowe, aplikacja jest odpowiedzialna za bezpieczne obsługiwanie bardzo poufnych danych i spełnianie wymagań prawnych.

Objective-C
```objc
// By default, the `MSALLogger` doesn't capture any PII

// PII will be logged
MSALGlobalConfig.loggerConfig.piiEnabled = YES;

// PII will NOT be logged
MSALGlobalConfig.loggerConfig.piiEnabled = NO;
```

Swift
```swift
// By default, the `MSALLogger` doesn't capture any PII

// PII will be logged
MSALGlobalConfig.loggerConfig.piiEnabled = true

// PII will NOT be logged
MSALGlobalConfig.loggerConfig.piiEnabled = false
```

### <a name="logging-levels"></a>Poziomy rejestrowania

Aby ustawić poziom rejestrowania podczas rejestrowania przy użyciu MSAL dla systemów iOS i macOS, użyj jednej z następujących wartości:

|Poziom  |Opis |
|---------|---------|
| `MSALLogLevelNothing`| Wyłącz wszystkie rejestrowanie |
| `MSALLogLevelError` | Poziom domyślny, drukuje informacje tylko w przypadku wystąpienia błędów |
| `MSALLogLevelWarning` | Ostrzeżenia |
| `MSALLogLevelInfo` |  Punkty wejścia biblioteki, z parametrami i różnymi operacjami łańcucha kluczy |
|`MSALLogLevelVerbose`     |  Śledzenie interfejsu API       |

Na przykład:

Objective-C
```objc
MSALGlobalConfig.loggerConfig.logLevel = MSALLogLevelVerbose;
 ```
 
 Swift
```swift
MSALGlobalConfig.loggerConfig.logLevel = .verbose
 ```

### <a name="log-message-format"></a>Format komunikatu dziennika

Część komunikatów dziennika MSAL jest w formacie`TID = <thread_id> MSAL <sdk_ver> <OS> <OS_ver> [timestamp - correlation_id] message`

Na przykład:

`TID = 551563 MSAL 0.2.0 iOS Sim 12.0 [2018-09-24 00:36:38 - 36764181-EF53-4E4E-B3E5-16FE362CFC44] acquireToken returning with error: (MSALErrorDomain, -42400) User cancelled the authorization session.`

Przekazywanie identyfikatorów korelacji i sygnatur czasowych jest przydatne w przypadku śledzenia problemów. W komunikacie dziennika są dostępne informacje o sygnaturze czasowej i IDENTYFIKATORze korelacji. Jedyną niezawodnym miejscem, w którym można je pobrać, jest MSAL komunikaty rejestrowania.
