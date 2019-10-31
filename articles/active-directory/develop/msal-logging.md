---
title: Rejestrowanie w aplikacjach Microsoft Authentication Library (MSAL)
titleSuffix: Microsoft identity platform
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
ms.date: 09/05/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: e12badd84bd929bdeb7b60ad6e99d6b3169e5022
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73150450"
---
# <a name="logging-in-msal-applications"></a>Logowanie w aplikacjach MSAL

Aplikacje Microsoft Authentication Library (MSAL) generują komunikaty dziennika, które mogą pomóc zdiagnozować problemy. Aplikacja może skonfigurować rejestrowanie z kilkoma wierszami kodu i mieć kontrolę niestandardową na poziomie szczegółowości oraz o tym, czy dane osobiste i organizacyjne są rejestrowane. Zalecamy utworzenie wywołania zwrotnego rejestrowania MSAL i udostępnienie użytkownikom przesyłania dzienników w przypadku problemów z uwierzytelnianiem.

## <a name="logging-levels"></a>Poziomy rejestrowania

MSAL zapewnia kilka poziomów rejestrowania szczegółów:

- Błąd: wskazuje, że wystąpił problem i został wygenerowany błąd. Służy do debugowania i identyfikowania problemów.
- Ostrzeżenie: niekoniecznie Wystąpił błąd lub błąd, ale są one przeznaczone do diagnostyki i lokalizowania problemów.
- Informacja: MSAL będzie rejestrować zdarzenia przeznaczone do celów informacyjnych niekoniecznie do debugowania.
- Verbose: domyślne. MSAL rejestruje szczegółowe informacje o zachowaniu biblioteki.

## <a name="personal-and-organizational-data"></a>Dane osobowe i organizacyjne

Domyślnie Rejestrator MSAL nie przechwytuje żadnych wysoce poufnych danych osobistych lub organizacji. Biblioteka zawiera opcję włączenia rejestrowania danych osobowych i organizacji w przypadku podjęcia decyzji.

## <a name="logging-in-msalnet"></a>Logowanie w MSAL.NET

 > [!NOTE]
 > Zobacz [MSAL.NET wiki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki) , aby poznać przykłady rejestrowania MSAL.NET i nie tylko.

W MSAL 3. x rejestrowanie jest ustawione na aplikację przy tworzeniu aplikacji przy użyciu modyfikatora `.WithLogging` Builder. Ta metoda pobiera parametry opcjonalne:

- `Level` umożliwia podjęcie decyzji o żądanym poziomie rejestrowania. Ustawienie na błędy spowoduje tylko błędy
- `PiiLoggingEnabled` pozwala rejestrować dane osobiste i organizacyjne, jeśli ustawiono wartość true. Domyślnie to ustawienie ma wartość FAŁSZ, aby aplikacja nie rejestrował danych osobowych.
- `LogCallback` jest ustawiona na delegata, który wykonuje rejestrowanie. Jeśli `PiiLoggingEnabled` ma wartość true, ta metoda otrzyma komunikaty dwa razy: jeden raz z parametrem `containsPii` ma wartość false, a komunikat bez danych osobowych i drugi raz z parametrem `containsPii` równą true, a komunikat może zawierać dane osobowe. W niektórych przypadkach (gdy wiadomość nie zawiera danych osobowych), komunikat będzie taki sam.
- `DefaultLoggingEnabled` Włącza rejestrowanie domyślne dla platformy. Domyślnie jest to wartość false. Jeśli ustawisz ją na wartość true, używa ona śledzenia zdarzeń w aplikacjach Desktop/platformy UWP, NSLog w systemach iOS i Logcat w systemie Android.

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

## <a name="logging-in-msal-for-android-using-java"></a>Logowanie w MSAL dla systemu Android przy użyciu języka Java

Włącz logowanie przy tworzeniu aplikacji przez utworzenie wywołania zwrotnego rejestrowania. Wywołanie zwrotne przyjmuje następujące parametry:

- `tag` jest ciągiem przesłanym do wywołania zwrotnego przez bibliotekę. Jest ona skojarzona z wpisem dziennika i może służyć do sortowania komunikatów rejestrowania.
- `logLevel` umożliwia podjęcie decyzji o żądanym poziomie rejestrowania. Obsługiwane poziomy dzienników to: `Error`, `Warning`, `Info`i `Verbose`.
- `message` to zawartość wpisu dziennika.
- `containsPII` określa, czy komunikaty zawierające dane osobowe lub dane organizacji są rejestrowane. Domyślnie to ustawienie ma wartość FAŁSZ, aby aplikacja nie rejestrował danych osobowych. Jeśli `containsPII` jest `true`, ta metoda otrzyma komunikaty dwa razy: jeden raz z parametrem `containsPII` ustawionym na `false` i `message` bez danych osobowych i po raz drugi z parametrem `containsPii` ustawionym na `true`, a komunikat może zawierać dane osobowe. W niektórych przypadkach (gdy wiadomość nie zawiera danych osobowych), komunikat będzie taki sam.

```java
private StringBuilder mLogs;

mLogs = new StringBuilder();
Logger.getInstance().setExternalLogger(new ILoggerCallback()
{
   @Override
   public void log(String tag, Logger.LogLevel logLevel, String message, boolean containsPII)
   {
      mLogs.append(message).append('\n');
   }
});
```

Domyślnie Rejestrator MSAL nie będzie przechwytywać informacji osobistych ani informacji o organizacji.
Aby włączyć rejestrowanie informacji osobistych lub informacji o organizacji:

```java
Logger.getInstance().setEnablePII(true);
```

Aby wyłączyć rejestrowanie danych osobowych i danych organizacji:

```java
Logger.getInstance().setEnablePII(false);
```

Domyślnie logowanie do Logcat jest wyłączone. Aby włączyć: 
```java
Logger.getInstance().setEnableLogcatLog(true);
```

## <a name="logging-in-msaljs"></a>Rejestrowanie w MSAL. js

 Włącz rejestrowanie w MSAL. js przez przekazanie obiektu rejestratora podczas konfiguracji w celu utworzenia wystąpienia `UserAgentApplication`. Ten obiekt rejestratora ma następujące właściwości:

- `localCallback`: wystąpienie wywołania zwrotnego, które może zostać dostarczone przez dewelopera do korzystania z dzienników i publikowania ich w niestandardowy sposób. Zaimplementuj metodę localCallback w zależności od tego, jak chcesz przekierować dzienniki.
- `level` (opcjonalnie): konfigurowalny poziom rejestrowania. Obsługiwane poziomy dzienników to: `Error`, `Warning`, `Info`i `Verbose`. Wartość domyślna to `Info`.
- `piiLoggingEnabled` (opcjonalnie): w przypadku ustawienia wartości true program rejestruje dane osobiste i organizacyjne. Domyślnie jest to wartość false, aby aplikacja nie rejestrował danych osobowych. Osobiste dzienniki danych nigdy nie są zapisywane w domyślnych danych wyjściowych, takich jak Console, Logcat lub NSLog.
- `correlationId` (opcjonalnie): unikatowy identyfikator używany do mapowania żądania z odpowiedzią na potrzeby debugowania. Wartość domyślna to RFC4122 w wersji 4 GUID (128 bitów).

```javascript
function loggerCallback(logLevel, message, containsPii) {
   console.log(message);
}

var msalConfig = {
    auth: {
        clientId: "<Enter your client id>",
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

Obiektowy C
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

Obiektowy C
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
| `MSALLogLevelWarning` | Ostrzeżeni |
| `MSALLogLevelInfo` |  Punkty wejścia biblioteki, z parametrami i różnymi operacjami łańcucha kluczy |
|`MSALLogLevelVerbose`     |  Śledzenie interfejsu API       |

Na przykład:

Obiektowy C
```objc
MSALGlobalConfig.loggerConfig.logLevel = MSALLogLevelVerbose;
 ```
 
 Swift
```swift
MSALGlobalConfig.loggerConfig.logLevel = .verbose
 ```

### <a name="log-message-format"></a>Format komunikatu dziennika

Część komunikatów dziennika MSAL jest w formacie `TID = <thread_id> MSAL <sdk_ver> <OS> <OS_ver> [timestamp - correlation_id] message`

Na przykład:

`TID = 551563 MSAL 0.2.0 iOS Sim 12.0 [2018-09-24 00:36:38 - 36764181-EF53-4E4E-B3E5-16FE362CFC44] acquireToken returning with error: (MSALErrorDomain, -42400) User cancelled the authorization session.`

Przekazywanie identyfikatorów korelacji i sygnatur czasowych jest przydatne w przypadku śledzenia problemów. W komunikacie dziennika są dostępne informacje o sygnaturze czasowej i IDENTYFIKATORze korelacji. Jedyną niezawodnym miejscem, w którym można je pobrać, jest MSAL komunikaty rejestrowania.
