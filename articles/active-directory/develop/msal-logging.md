---
title: Logowanie do aplikacji MSAL | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się więcej o logowaniu w aplikacjach Biblioteki uwierzytelniania Firmy Microsoft (MSAL).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/11/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 58697cc535357710c6889f05060b5e04e129ae7d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77084891"
---
# <a name="logging-in-msal-applications"></a>Logowanie do aplikacji MSAL

Aplikacje biblioteki uwierzytelniania firmy Microsoft (MSAL) generują komunikaty dziennika, które mogą pomóc w diagnozowaniu problemów. Aplikacja może skonfigurować rejestrowanie za pomocą kilku wierszy kodu i mieć niestandardową kontrolę nad poziomem szczegółowości i tym, czy dane osobiste i organizacyjne są rejestrowane. Zaleca się utworzenie wywołania zwrotnego rejestrowania MSAL i zapewnienie użytkownikom możliwości przesyłania dzienników, gdy mają problemy z uwierzytelnianiem.

## <a name="logging-levels"></a>Poziomy rejestrowania

MSAL zapewnia kilka poziomów szczegółowości rejestrowania:

- Błąd: wskazuje, że coś poszło nie tak i został wygenerowany błąd. Służy do debugowania i identyfikowania problemów.
- Ostrzeżenie: Nie musi być błąd lub błąd, ale są przeznaczone do diagnostyki i wskazywania problemów.
- Informacje: MSAL będzie rejestrować zdarzenia przeznaczone do celów informacyjnych niekoniecznie przeznaczone do debugowania.
- Pełne: Domyślnie. MSAL rejestruje pełne szczegóły zachowania biblioteki.

## <a name="personal-and-organizational-data"></a>Dane osobowe i organizacyjne

Domyślnie rejestrator MSAL nie przechwytuje żadnych bardzo poufnych danych osobistych lub organizacyjnych. Biblioteka udostępnia opcję włączania rejestrowania danych osobistych i organizacyjnych, jeśli zdecydujesz się to zrobić.

Aby uzyskać szczegółowe informacje na temat rejestrowania MSAL w określonym języku, wybierz kartę pasującą do twojego języka:

## <a name="net"></a>[.NET](#tab/dotnet)

## <a name="logging-in-msalnet"></a>Logowanie do MSAL.NET

 > [!NOTE]
 > Zobacz [wiki MSAL.NET, aby](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki) zapoznać się z próbkami rejestrowania MSAL.NET i nie tylko.

W msal 3.x rejestrowanie jest ustawiane dla `.WithLogging` aplikacji podczas tworzenia aplikacji przy użyciu modyfikatora konstruktora. Ta metoda przyjmuje parametry opcjonalne:

- `Level`pozwala zdecydować, który poziom rejestrowania chcesz. Ustawienie go na Błędy spowoduje tylko błędy
- `PiiLoggingEnabled`umożliwia rejestrowanie danych osobistych i organizacyjnych, jeśli jest ustawiona na true. Domyślnie jest to ustawiona na false, dzięki czemu aplikacja nie rejestruje danych osobowych.
- `LogCallback`jest ustawiona na delegata, który wykonuje rejestrowanie. Jeśli `PiiLoggingEnabled` jest to prawda, ta metoda otrzyma `containsPii` wiadomości dwa razy: raz z parametrem jest równy false i wiadomości bez danych osobowych, a po raz drugi z `containsPii` parametrem równa true i wiadomość może zawierać dane osobowe. W niektórych przypadkach (gdy wiadomość nie zawiera danych osobowych), wiadomość będzie taka sama.
- `DefaultLoggingEnabled`umożliwia domyślne rejestrowanie dla platformy. Domyślnie jest false. Jeśli ustawisz go na true używa śledzenia zdarzeń w aplikacjach pulpitu/platformy uniwersalnej systemu Windows, NSLog na iOS i logcat na Androida.

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

## <a name="android"></a>[Android](#tab/android)

## <a name="logging-in-msal-for-android-using-java"></a>Logowanie do msal dla systemu Android przy użyciu oprogramowania Java

Włącz logowanie podczas tworzenia aplikacji, tworząc wywołanie zwrotne rejestrowania. Wywołanie zwrotne przyjmuje następujące parametry:

- `tag`jest ciągiem przekazanym do wywołania zwrotnego przez bibliotekę. Jest skojarzony z wpisem dziennika i może służyć do sortowania wiadomości rejestrowania.
- `logLevel`pozwala zdecydować, który poziom rejestrowania chcesz. Obsługiwane poziomy dziennika `Error`to: `Warning` `Info`, `Verbose`, , i .
- `message`jest zawartością wpisu dziennika.
- `containsPII`określa, czy rejestrowane są wiadomości zawierające dane osobowe, czy dane organizacyjne. Domyślnie jest to ustawione na false, dzięki czemu aplikacja nie rejestruje danych osobowych. Jeśli `containsPII` `true`tak, ta metoda otrzyma wiadomości dwa `containsPII` razy: `false` raz `message` z parametrem ustawionym na `containsPii` i `true` bez danych osobowych, a po raz drugi z parametrem ustawionym na i wiadomość może zawierać dane osobowe. W niektórych przypadkach (gdy wiadomość nie zawiera danych osobowych), wiadomość będzie taka sama.

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

Domyślnie rejestrator MSAL nie przechwytuje żadnych osobistych informacji umożliwiających identyfikację ani informacji umożliwiających identyfikację organizacji.
Aby umożliwić rejestrowanie danych osobowych lub informacji umożliwiających identyfikację organizacji:

```java
Logger.getInstance().setEnablePII(true);
```

Aby wyłączyć rejestrowanie danych osobowych i danych organizacji:

```java
Logger.getInstance().setEnablePII(false);
```

Domyślnie rejestrowanie do logcat jest wyłączone. Aby włączyć:

```java
Logger.getInstance().setEnableLogcatLog(true);
```

## <a name="javascript"></a>[Javascript](#tab/javascript)

 Włącz rejestrowanie w pliku MSAL.js (JavaScript), przekazując obiekt rejestratora podczas konfiguracji tworzenia `UserAgentApplication` wystąpienia. Ten obiekt rejestratora ma następujące właściwości:

- `localCallback`: wywołanie zwrotne wystąpienie, które mogą być dostarczone przez dewelopera do korzystania i publikowania dzienników w sposób niestandardowy. Zaimplementuj localCallback metody w zależności od tego, jak chcesz przekierować dzienniki.
- `level`(opcjonalnie): konfigurowalny poziom dziennika. Obsługiwane poziomy dziennika `Error`to: `Warning` `Info`, `Verbose`, , i . Wartość domyślna to `Info`.
- `piiLoggingEnabled`(opcjonalnie): jeśli ustawiona jest wartość true, rejestruje dane osobiste i organizacyjne. Domyślnie jest to false, dzięki czemu aplikacja nie rejestruje danych osobowych. Dzienniki danych osobistych nigdy nie są zapisywane w domyślnych wyjściach, takich jak Console, Logcat lub NSLog.
- `correlationId`(opcjonalnie): unikatowy identyfikator, używany do mapowania żądania z odpowiedzią do celów debugowania. Domyślnie identyfikator GUID W WERSJI 4 RFC4122 (128 bitów).

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

## <a name="objective-c"></a>[Cel C](#tab/objc)

## <a name="msal-for-ios-and-macos-logging-objc"></a>MSAL dla iOS i macOS rejestrowania ObjC

Ustaw wywołanie zwrotne, aby przechwycić rejestrowanie MSAL i włączyć go do rejestrowania własnej aplikacji. Podpis wywołania zwrotnego wygląda następująco:

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

Przykład:

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

### <a name="personal-data"></a>Dane osobowe

Domyślnie msal nie przechwytuje ani nie rejestruje żadnych danych osobowych.Default, MSAL doesn't capture or log any personal data (PII). Biblioteka umożliwia deweloperom aplikacji, aby włączyć to za pośrednictwem właściwości w MSALLogger klasy. Po włączeniu `pii.Enabled`aplikacja ponosi odpowiedzialność za bezpieczne obchodzenie się z bardzo wrażliwymi danymi i przestrzeganie wymogów regulacyjnych.

```objc
// By default, the `MSALLogger` doesn't capture any PII

// PII will be logged
MSALGlobalConfig.loggerConfig.piiEnabled = YES;

// PII will NOT be logged
MSALGlobalConfig.loggerConfig.piiEnabled = NO;
```

### <a name="logging-levels"></a>Poziomy rejestrowania

Aby ustawić poziom rejestrowania podczas logowania przy użyciu usługi MSAL dla systemu iOS i macOS, należy użyć jednej z następujących wartości:

|Poziom  |Opis |
|---------|---------|
| `MSALLogLevelNothing`| Wyłączanie wszystkich rejestrowania |
| `MSALLogLevelError` | Poziom domyślny, drukuje informacje tylko wtedy, gdy wystąpią błędy |
| `MSALLogLevelWarning` | Ostrzeżenia |
| `MSALLogLevelInfo` |  Punkty wejścia biblioteki z parametrami i różnymi operacjami pęku kluczy |
|`MSALLogLevelVerbose`     |  Śledzenie interfejsu API |

Przykład:

```objc
MSALGlobalConfig.loggerConfig.logLevel = MSALLogLevelVerbose;
 ```

 ### <a name="log-message-format"></a>Format komunikatu dziennika

Część komunikatów msal dziennik jest w formacie`TID = <thread_id> MSAL <sdk_ver> <OS> <OS_ver> [timestamp - correlation_id] message`

Przykład:

`TID = 551563 MSAL 0.2.0 iOS Sim 12.0 [2018-09-24 00:36:38 - 36764181-EF53-4E4E-B3E5-16FE362CFC44] acquireToken returning with error: (MSALErrorDomain, -42400) User cancelled the authorization session.`

Podanie identyfikatorów korelacji i sygnatur czasowych są przydatne do śledzenia problemów. Informacje o identyfikatorze sygnatury czasowej i identyfikatorze korelacji są dostępne w komunikacie dziennika. Jedynym niezawodnym miejscem do ich pobrania jest z msal rejestrowania wiadomości.

## <a name="swift"></a>[Swift](#tab/swift)

## <a name="msal-for-ios-and-macos-logging-swift"></a>MSAL dla iOS i macOS rejestrowania Swift

Ustaw wywołanie zwrotne, aby przechwycić rejestrowanie MSAL i włączyć go do rejestrowania własnej aplikacji. Podpis (reprezentowany w języku Objective-C) dla wywołania zwrotnego wygląda następująco:

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

Przykład:

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

### <a name="personal-data"></a>Dane osobowe

Domyślnie msal nie przechwytuje ani nie rejestruje żadnych danych osobowych.Default, MSAL doesn't capture or log any personal data (PII). Biblioteka umożliwia deweloperom aplikacji, aby włączyć to za pośrednictwem właściwości w MSALLogger klasy. Po włączeniu `pii.Enabled`aplikacja ponosi odpowiedzialność za bezpieczne obchodzenie się z bardzo wrażliwymi danymi i przestrzeganie wymogów regulacyjnych.

```swift
// By default, the `MSALLogger` doesn't capture any PII

// PII will be logged
MSALGlobalConfig.loggerConfig.piiEnabled = true

// PII will NOT be logged
MSALGlobalConfig.loggerConfig.piiEnabled = false
```

### <a name="logging-levels"></a>Poziomy rejestrowania

Aby ustawić poziom rejestrowania podczas logowania przy użyciu usługi MSAL dla systemu iOS i macOS, należy użyć jednej z następujących wartości:

|Poziom  |Opis |
|---------|---------|
| `MSALLogLevelNothing`| Wyłączanie wszystkich rejestrowania |
| `MSALLogLevelError` | Poziom domyślny, drukuje informacje tylko wtedy, gdy wystąpią błędy |
| `MSALLogLevelWarning` | Ostrzeżenia |
| `MSALLogLevelInfo` |  Punkty wejścia biblioteki z parametrami i różnymi operacjami pęku kluczy |
|`MSALLogLevelVerbose`     |  Śledzenie interfejsu API |

Przykład:

```swift
MSALGlobalConfig.loggerConfig.logLevel = .verbose
 ```

### <a name="log-message-format"></a>Format komunikatu dziennika

Część komunikatów msal dziennik jest w formacie`TID = <thread_id> MSAL <sdk_ver> <OS> <OS_ver> [timestamp - correlation_id] message`

Przykład:

`TID = 551563 MSAL 0.2.0 iOS Sim 12.0 [2018-09-24 00:36:38 - 36764181-EF53-4E4E-B3E5-16FE362CFC44] acquireToken returning with error: (MSALErrorDomain, -42400) User cancelled the authorization session.`

Podanie identyfikatorów korelacji i sygnatur czasowych są przydatne do śledzenia problemów. Informacje o identyfikatorze sygnatury czasowej i identyfikatorze korelacji są dostępne w komunikacie dziennika. Jedynym niezawodnym miejscem do ich pobrania jest z msal rejestrowania wiadomości.

## <a name="java"></a>[Java](#tab/java)

## <a name="msal-for-java-logging"></a>MSAL do rejestrowania języka Java

Msal for Java umożliwia korzystanie z biblioteki rejestrowania, z której już korzystasz z aplikacji, o ile jest zgodna z SLF4J. MSAL for Java używa [prostego rejestrowania fasady dla java](http://www.slf4j.org/) (SLF4J) jako prostej fasady lub abstrakcji dla różnych struktur rejestrowania, takich jak [java.util.logging](https://docs.oracle.com/javase/7/docs/api/java/util/logging/package-summary.html), [Logback](http://logback.qos.ch/) i [Log4j](https://logging.apache.org/log4j/2.x/). SLF4J umożliwia użytkownikowi podłączenie żądanej struktury rejestrowania w czasie wdrażania.

Na przykład, aby użyć logback jako struktury rejestrowania w aplikacji, należy dodać zależność Logback do pliku Maven pom dla aplikacji:

```xml
<dependency>
    <groupId>ch.qos.logback</groupId>
    <artifactId>logback-classic</artifactId>
    <version>1.2.3</version>
</dependency>
```

Następnie dodaj plik konfiguracyjny Logback:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration debug="true">

</configuration>
```

SLF4J automatycznie wiąże się z logback w czasie wdrażania. Dzienniki MSAL zostaną zapisane na konsoli.

Instrukcje dotyczące wiązania z innymi strukturami rejestrowania można znaleźć w [instrukcji obsługi SLF4J.](http://www.slf4j.org/manual.html)

### <a name="personal-and-organization-information"></a>Informacje osobiste i organizacyjne

Domyślnie rejestrowanie MSAL nie przechwytuje ani nie rejestruje żadnych danych osobistych ani organizacyjnych. W poniższym przykładzie rejestrowanie danych osobistych lub organizacyjnych jest domyślnie wyłączone:

```java
    PublicClientApplication app2 = PublicClientApplication.builder(PUBLIC_CLIENT_ID)
            .authority(AUTHORITY)
            .build();
```

Włącz rejestrowanie danych osobistych i `logPii()` organizacyjnych, ustawiając na konstruktorze aplikacji klienckich. Jeśli włączysz rejestrowanie danych osobistych lub organizacyjnych, aplikacja musi ponosić odpowiedzialność za bezpieczne przetwarzanie wysoce poufnych danych i spełnianie wszelkich wymogów prawnych.

W poniższym przykładzie jest włączone rejestrowanie danych osobistych lub organizacyjnych:

```java
PublicClientApplication app2 = PublicClientApplication.builder(PUBLIC_CLIENT_ID)
        .authority(AUTHORITY)
        .logPii(true)
        .build();
```

## <a name="python"></a>[Python](#tab/python)

## <a name="msal-for-python-logging"></a>MSAL dla rejestrowania języka Python

Logowanie w programie MSAL Python korzysta ze standardowego mechanizmu rejestrowania języka Python, na przykład `logging.info("msg")` rejestrowanie MSAL można skonfigurować w następujący sposób (i zobaczyć je w akcji w [username_password_sample):](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/1.0.0/sample/username_password_sample.py#L31L32)

### <a name="enable-debug-logging-for-all-modules"></a>Włącz rejestrowanie debugowania dla wszystkich modułów

Domyślnie rejestrowanie w dowolnym skrypcie języka Python jest wyłączone. Jeśli chcesz włączyć rejestrowanie debugowania dla wszystkich modułów w całym skrypcie Języka Python, użyj:

```python
logging.basicConfig(level=logging.DEBUG)
```

### <a name="silence-only-msal-logging"></a>Wyciszenie tylko rejestrowania MSAL

Aby wyciszyć tylko rejestrowanie biblioteki MSAL, umożliwiając rejestrowanie debugowania we wszystkich innych modułach w skrypcie języka Python, wyłącz rejestrator używany przez msal python:

```Python
logging.getLogger("msal").setLevel(logging.WARN)
```

### <a name="personal-and-organizational-data-in-python"></a>Dane osobiste i organizacyjne w języku Python

Usługa MSAL dla języka Python nie rejestruje danych osobowych ani danych organizacji. Nie ma właściwości, aby włączyć lub wyłączyć rejestrowanie danych osobowych lub organizacji.

Możesz użyć standardowego rejestrowania języka Python, aby rejestrować, co chcesz, ale jesteś odpowiedzialny za bezpieczne przetwarzanie poufnych danych i przestrzeganie wymagań regulacyjnych.

Aby uzyskać więcej informacji na temat logowania w Pythonie, zapoznaj się z Owaniem Pythona [HOWTO](https://docs.python.org/3/howto/logging.html#logging-basic-tutorial).

---
