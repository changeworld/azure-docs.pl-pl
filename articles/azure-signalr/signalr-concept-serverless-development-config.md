---
title: Tworzenie i konfigurowanie aplikacji usługi funkcje Azure SignalR Service
description: Szczegółowe informacje na temat sposobu tworzenia i konfigurowania aplikacji w czasie rzeczywistym bez użycia serwera za pomocą usługi Azure Functions i Azure SignalR Service
author: anthonychu
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: antchu
ms.openlocfilehash: 9b68b9d0bbac984c29759cf4b7b026a559a9d819
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60808997"
---
# <a name="azure-functions-development-and-configuration-with-azure-signalr-service"></a>Azure Functions rozwoju i konfiguracji za pomocą usługi Azure SignalR Service

Usługa Azure Functions aplikacje mogą korzystać z [powiązań usługi Azure SignalR Service](../azure-functions/functions-bindings-signalr-service.md) umożliwiające dodanie funkcji w czasie rzeczywistym. Aplikacje klienckie użyć zestawów SDK klienta dostępna w kilku językach nawiązać połączenie z usługi Azure SignalR Service i odbieranie komunikatów w czasie rzeczywistym.

W tym artykule opisano pojęcia dotyczące tworzenia i konfigurowania aplikacji funkcji platformy Azure, która jest zintegrowana z usługą SignalR.

## <a name="signalr-service-configuration"></a>Konfiguracja usługi SignalR

Usługi Azure SignalR Service można skonfigurować w różnych trybach. W przypadku użycia z usługą Azure Functions, usługa musi być skonfigurowana jako *aplikacje niewymagające użycia serwera* trybu.

W witrynie Azure portal zlokalizuj *ustawienia* strony zasobu usługi SignalR. Ustaw *tryb usługi* do *aplikacje niewymagające użycia serwera*.

![Tryb usługi SignalR](media/signalr-concept-azure-functions/signalr-service-mode.png)

## <a name="azure-functions-development"></a>Programowanie na platformie Azure Functions

Bez użycia serwera aplikacji w czasie rzeczywistym, tworzone za pomocą usługi Azure Functions i Azure SignalR Service zwykle wymaga dwóch funkcji platformy Azure:

* Funkcja "negocjowania", gdy klient wywołuje uzyskać prawidłowy SignalR Service token dostępu i adres URL punktu końcowego usługi
* Jedną lub więcej funkcji, które wysyłanie wiadomości lub zarządzanie członkostwem w grupie

### <a name="negotiate-function"></a>negocjowania — funkcja

Aplikacja kliencka wymaga prawidłowym tokenem dostępu nawiązać połączenia z usługi Azure SignalR Service. Token dostępu może być anonimowi lub uwierzytelniony identyfikator danego użytkownika. Aplikacje bezserwerowe SignalR Service wymaga punktu końcowego HTTP o nazwie "negocjowania" w celu uzyskania tokenu i inne informacje o połączeniu, takich jak adres URL punktu końcowego usługi SignalR.

Azure funkcja wyzwalana przez używają protokołu HTTP i *SignalRConnectionInfo* powiązania do generowania informacji obiekt połączenia danych wejściowych. Funkcja musi mieć trasę w protokole HTTP, które kończy się `/negotiate`.

Aby uzyskać więcej informacji na temat tworzenia funkcji negotiate, zobacz [ *SignalRConnectionInfo* wejściowych odwołanie powiązania](../azure-functions/functions-bindings-signalr-service.md#signalr-connection-info-input-binding).

Aby dowiedzieć się więcej o sposobie tworzenia tokenu uwierzytelniony, dotyczą [przy użyciu uwierzytelnianie usługi App Service](#using-app-service-authentication).

### <a name="sending-messages-and-managing-group-membership"></a>Wysyłanie wiadomości i zarządzania członkostwa w grupie

Użyj *SignalR* powiązania danych wyjściowych, do wysyłania wiadomości do klientów podłączone do usługi Azure SignalR Service. Komunikaty można rozgłaszać do wszystkich klientów, lub wysłać je do podzbioru klientów, którzy są uwierzytelniane przy użyciu Identyfikatora użytkownika lub został dodany do określonej grupy.

Użytkownicy mogą zostać dodani do co najmniej jednej grupy. Można również użyć *SignalR* powiązanie do dodawania lub usuwania użytkowników do i z grupy danych wyjściowych.

Aby uzyskać więcej informacji, zobacz [ *SignalR* odwołania powiązania wyjściowego](../azure-functions/functions-bindings-signalr-service.md#signalr-output-binding).

### <a name="signalr-hubs"></a>SignalR Hubs

SignalR utworzono według koncepcji "centra". Każde połączenie klienta, a każdy komunikat wysyłany z usługi Azure Functions jest ograniczony do określonego koncentratora. Koncentratory służy jako sposób na dzielenie logiczne przestrzeni nazw usługi połączenia i wiadomości.

## <a name="client-development"></a>Programowanie klienta

SignalR aplikacje klienckie mogą korzystać z zestawu SDK w jednym z kilku języków łatwe nawiązywanie i odbieranie komunikatów z usługi Azure SignalR Service klienta SignalR.

### <a name="configuring-a-client-connection"></a>Konfigurowanie połączenia klienta

Aby połączyć usługi SignalR, klienta należy wykonać negocjacji pomyślnym nawiązaniu połączenia, który składa się z następujących czynności:

1. Zgłosić wniosek o *negocjowania* punkt końcowy HTTP omówione powyżej, aby uzyskać informacje o połączeniu prawidłowe
1. Nawiązywanie połączenia SignalR Service przy użyciu adresu URL punktu końcowego usługi i token dostępu uzyskany z *negocjowania* punktu końcowego

Zestawy SDK klienta SignalR już zawiera logiki wymaganej do wykonania uzgadniania negocjacji. Przekazywanie adresu URL punktu końcowego negotiate, minus `negotiate` segmentu w zestawie SDK `HubConnectionBuilder`. Oto przykład w języku JavaScript:

```javascript
const connection = new signalR.HubConnectionBuilder()
  .withUrl('https://my-signalr-function-app.azurewebsites.net/api')
  .build()
```

Zgodnie z Konwencją, zestaw SDK automatycznie dołącza `/negotiate` do adresu URL i używa go w celu rozpoczęcia negocjacji.

> [!NOTE]
> Jeśli używasz zestawu SDK języka JavaScript/TypeScript w przeglądarce, musisz [Włącz współużytkowanie zasobów między źródłami (cors)](#enabling-cors) na aplikację funkcji.

Aby uzyskać więcej informacji na temat korzystania z zestawu SDK klienta SignalR można znaleźć w dokumentacji dotyczącej języka:

* [.NET Standard](https://docs.microsoft.com/aspnet/core/signalr/dotnet-client)
* [JavaScript](https://docs.microsoft.com/aspnet/core/signalr/javascript-client)
* [Java](https://docs.microsoft.com/aspnet/core/signalr/java-client)

### <a name="sending-messages-from-a-client-to-the-service"></a>Wysyłanie komunikatów z klienta do usługi

Mimo że zestaw SDK SignalR pozwala aplikacjom do wywołania logiki zaplecza w Centrum SignalR klientów, ta funkcja nie jest jeszcze obsługiwana w przypadku użycia usługi SignalR z usługi Azure Functions. Użyj protokołu HTTP żądania do wywołania usługi Azure Functions.

## <a name="azure-functions-configuration"></a>Konfiguracja usługi Azure Functions

Podobnie jak wszelkie typowych aplikacji funkcji platformy Azure przy użyciu technik, takich jak można wdrożyć aplikacje funkcji platformy Azure, które integrują się z usługi Azure SignalR Service [wdrażanie ciągłe](../azure-functions/functions-continuous-deployment.md), [zip wdrożenia](../azure-functions/deployment-zip-push.md)i [uruchomienia z pakietu](../azure-functions/run-functions-from-deployment-package.md).

Istnieją jednak kilka specjalnych zagadnień dotyczących aplikacji, które używają powiązania usługi SignalR. Jeśli klient jest uruchomiony w przeglądarce, należy włączyć mechanizm CORS. A jeśli aplikacja wymaga uwierzytelniania, negotiate punktu końcowego można zintegrować z uwierzytelnianie usługi App Service.

### <a name="enabling-cors"></a>Włączanie funkcji CORS

Klient JavaScript/TypeScript wysyła żądania HTTP funkcji negotiate, aby zainicjować negocjowania połączenia. Gdy aplikacja klienta znajduje się w innej domenie niż aplikacji funkcji platformy Azure, współużytkowanie zasobów między źródłami (cors) musi być włączona na aplikację funkcji lub przeglądarki będzie blokować żądania.

#### <a name="localhost"></a>Localhost

Podczas uruchamiania aplikacji funkcji na komputerze lokalnym, możesz dodać `Host` sekcji *local.settings.json* Włączanie mechanizmu CORS. W `Host` sekcji, należy dodać dwie właściwości:

* `CORS` -Wprowadź podstawowy adres URL, z którego pochodzi aplikacja kliencka
* `CORSCredentials` -Ustaw ją na `true` Aby zezwolić na żądania "withCredentials"

Przykład:

```json
{
  "IsEncrypted": false,
  "Values": {
    // values
  },
  "Host": {
    "CORS": "http://localhost:8080",
    "CORSCredentials": true
  }
}
```

#### <a name="azure"></a>Azure

Aby włączyć mechanizm CORS w aplikacji funkcji platformy Azure, przejdź do ekranu konfiguracji mechanizmu CORS w obszarze *funkcje platformy* kartę aplikacji funkcji w witrynie Azure portal.

Mechanizm CORS za pomocą dostępu — kontrola-Allow-Credentials musi być włączona dla klienta SignalR wywołać funkcję negotiate. Zaznacz pole wyboru, aby go włączyć.

W *dozwolone źródła* Dodaj wpis o pochodzeniu podstawowy adres URL aplikacji sieci web.

![Konfigurowanie mechanizmu CORS](media/signalr-concept-serverless-development-config/cors-settings.png)

### <a name="using-app-service-authentication"></a>Przy użyciu uwierzytelniania usługi App Service

Usługa Azure Functions oferuje wbudowane uwierzytelnianie, obsługa popularnych dostawców, takich jak Facebook, Twitter, Account Microsoft, Google i usługi Azure Active Directory. Ta funkcja może zostać zintegrowany z *SignalRConnectionInfo* powiązania do tworzenia połączeń z usługi Azure SignalR Service, uwierzytelnione na identyfikator użytkownika. Aplikację można wysyłać komunikatów za pomocą *SignalR* danych wyjściowych powiązania, które są stosowane dla tego identyfikatora użytkownika.

W witrynie Azure portal w aplikacji funkcji *funkcje platformy* otwartą kartą *uwierzytelniania/autoryzacji* okno Ustawienia. Postępuj zgodnie z dokumentacją [uwierzytelnianie usługi App Service](../app-service/overview-authentication-authorization.md) do konfigurowania uwierzytelniania z użyciem wybranego dostawcy tożsamości.

Po skonfigurowaniu uwierzytelnionego żądania HTTP będzie zawierać `x-ms-client-principal-name` i `x-ms-client-principal-id` nagłówków zawierających uwierzytelnionej tożsamości użytkownika i identyfikator użytkownika, odpowiednio.

Możesz użyć tych nagłówków w swojej *SignalRConnectionInfo* konfigurację powiązania w celu utworzenia uwierzytelnionych połączeń. Oto przykład C# negocjowania funkcję, która używa `x-ms-client-principal-id` nagłówka.

```csharp
[FunctionName("negotiate")]
public static SignalRConnectionInfo Negotiate(
    [HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req,
    [SignalRConnectionInfo
        (HubName = "chat", UserId = "{headers.x-ms-client-principal-id}")]
        SignalRConnectionInfo connectionInfo)
{
    // connectionInfo contains an access key token with a name identifier claim set to the authenticated user
    return connectionInfo;
}
```

Następnie możesz wysłać komunikaty do tego użytkownika, ustawiając `UserId` właściwości komunikatu SignalR.

```csharp
[FunctionName("SendMessage")]
public static Task SendMessage(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]object message,
    [SignalR(HubName = "chat")]IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage
        {
            // the message will only be sent to these user IDs
            UserId = "userId1",
            Target = "newMessage",
            Arguments = new [] { message }
        });
}
```

Aby uzyskać informacji na temat innych języków, zobacz [powiązań usługi Azure SignalR Service](../azure-functions/functions-bindings-signalr-service.md) dla odwoływać się do usługi Azure Functions.

## <a name="next-steps"></a>Kolejne kroki

W tym artykule mają przedstawiono sposób tworzenia i konfigurowania aplikacji SignalR Service bez użycia serwera za pomocą usługi Azure Functions. Spróbuj utworzyć aplikację samodzielnie, korzystając z przewodników Szybki Start lub samouczki na [stronę przeglądu usługi SignalR](index.yml).