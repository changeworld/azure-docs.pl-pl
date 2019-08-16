---
title: Logowanie w aplikacjach MSAL | Azure
description: Dowiedz się więcej o rejestrowaniu w aplikacjach Microsoft Authentication Library (MSAL).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/22/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 475b692a29edf5cdd05552e7b5c3dc5fde210275
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/15/2019
ms.locfileid: "69512524"
---
# <a name="logging"></a>Rejestrowanie
Aplikacje Microsoft Authentication Library (MSAL) do generowania komunikatów dziennika, które mogą pomóc zdiagnozować problemy i podać szczegóły. Aplikacja może skonfigurować rejestrowanie z kilkoma wierszami kodu i mieć kontrolę niestandardową na poziomie szczegółowości oraz o tym, czy dane osobiste i organizacyjne są rejestrowane. Zaleca się ustawienie wywołania zwrotnego rejestrowania MSAL i umożliwia użytkownikom przesyłanie dzienników w przypadku problemów z uwierzytelnianiem.

## <a name="logging-levels"></a>Poziomy rejestrowania

Rejestrator MSAL umożliwia przechwytywanie kilku poziomów szczegółowości:

- Błąd: Wskazuje, że wystąpił problem i został wygenerowany błąd. Służy do debugowania i identyfikowania problemów.
- Ostrzeżenie: Zdarzenia, które są pytaniami i aplikacja wymaga więcej informacji na temat. Niekoniecznie Wystąpił błąd lub błąd, ale jest on przeznaczony do diagnozowania i lokalizowania problemów.
- Informacje MSAL będzie rejestrować zdarzenia przeznaczone do celów informacyjnych niekoniecznie do debugowania.
- Pełne Domyślne. MSAL będzie rejestrował dużą ilość informacji i przekaże pełne informacje o zachowaniu biblioteki.

## <a name="personal-and-organizational-data"></a>Dane osobowe i organizacyjne
Domyślnie Rejestrator MSAL nie przechwytuje żadnych wysoce poufnych danych osobistych lub organizacji. Biblioteka udostępnia opcję włączenia rejestrowania danych osobowych i organizacji w przypadku podjęcia decyzji.

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

- *localCallback*: wystąpienie wywołania zwrotnego, które może zostać dostarczone przez dewelopera do korzystania z dzienników i publikowania ich w niestandardowy sposób. Zaimplementuj metodę localCallback w zależności od tego, jak chcesz przekierować dzienniki.

- *poziom* (opcjonalnie): konfigurowalny poziom rejestrowania. Obsługiwane poziomy dzienników są następujące: Błąd, ostrzeżenie, informacje, pełne. Wartość domyślna to info.

- *piiLoggingEnabled* (opcjonalnie): umożliwia rejestrowanie danych osobistych i organizacji, jeśli ustawiono wartość true. Domyślnie to ustawienie ma wartość FAŁSZ, aby aplikacja nie rejestrował danych osobowych. Osobiste dzienniki danych nigdy nie są zapisywane w domyślnych danych wyjściowych, takich jak Console, Logcat lub NSLog. Wartość domyślna to false.

- *Identyfikator korelacji* (opcjonalnie): unikatowy identyfikator używany do mapowania żądania z odpowiedzią na potrzeby debugowania. Wartość domyślna to RFC4122 w wersji 4 GUID (128 bitów).

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
