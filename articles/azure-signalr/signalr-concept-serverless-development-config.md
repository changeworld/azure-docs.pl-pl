---
title: Tworzenie & konfigurowanie aplikacji Usługi Azure — Azure SignalR
description: Szczegółowe informacje na temat tworzenia i konfigurowania aplikacji w czasie rzeczywistym bez użycia serwera przy użyciu funkcji Azure Functions i usługi Azure SignalR
author: anthonychu
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: antchu
ms.openlocfilehash: e1157a695d34c75b237391427b37365421366ef8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77523174"
---
# <a name="azure-functions-development-and-configuration-with-azure-signalr-service"></a>Programowanie i konfigurowanie w usłudze Azure Functions za pomocą usługi Azure SignalR Service

Aplikacje usługi Azure Functions mogą korzystać z [powiązań usługi Azure SignalR,](../azure-functions/functions-bindings-signalr-service.md) aby dodać funkcje w czasie rzeczywistym. Aplikacje klienckie używają zestawów SDK klienta dostępnych w kilku językach, aby połączyć się z usługą Azure SignalR i odbierać wiadomości w czasie rzeczywistym.

W tym artykule opisano pojęcia dotyczące tworzenia i konfigurowania aplikacji funkcji platformy Azure, która jest zintegrowana z usługą SignalR.

## <a name="signalr-service-configuration"></a>Konfiguracja usługi SignalR

Usługa Azure SignalR można skonfigurować w różnych trybach. W przypadku użycia z usługą Azure Functions usługa musi być skonfigurowana w trybie *bezserwerowym.*

W witrynie Azure portal znajdź *ustawienia* strony zasobu usługi SignalR. Ustaw *tryb usługi* na *Serverless*.

![Tryb serwisowania sygnału](media/signalr-concept-azure-functions/signalr-service-mode.png)

## <a name="azure-functions-development"></a>Opracowywanie usługi Azure Functions

Aplikacja bezserwerowa działająca w czasie rzeczywistym utworzona przy użyciu usług Azure Functions i Azure SignalR Service zwykle wymaga dwóch funkcji Azure Functions:

* Funkcja „negotiate” (negocjacja) wywoływana przez klienta w celu uzyskania prawidłowego tokenu dostępu do usługi SignalR i adresu URL punktu końcowego usługi
* Co najmniej jedna funkcja, która wysyła komunikaty lub zarządza członkostwem w grupie

### <a name="negotiate-function"></a>funkcja negocjowania

Aplikacja kliencka wymaga prawidłowego tokenu dostępu do łączenia się z usługą Azure SignalR Service. Token dostępu może być anonimowy lub uwierzytelniony do danego identyfikatora użytkownika. Aplikacje usługi SignalR bez serwera wymagają punktu końcowego HTTP o nazwie "negotiate", aby uzyskać token i inne informacje o połączeniu, takie jak adres URL punktu końcowego usługi SignalR.

Użyj funkcji platformy Azure wyzwalanej przez protokół HTTP i powiązania wejściowego *SignalRConnectionInfo* w celu wygenerowania obiektu informacji o połączeniu. Funkcja musi mieć trasę HTTP, `/negotiate`która kończy się na .

Aby uzyskać więcej informacji na temat tworzenia funkcji negocjowania, zobacz [odwołanie do powiązania wejściowego *SignalRConnectionInfo* ](../azure-functions/functions-bindings-signalr-service-input.md).

Aby dowiedzieć się, jak utworzyć uwierzytelniony token, zobacz [Korzystanie z uwierzytelniania usługi app service](#using-app-service-authentication).

### <a name="sending-messages-and-managing-group-membership"></a>Wysyłanie wiadomości i zarządzanie członkostwem w grupie

Użyj powiązania danych wyjściowych *SignalR* do wysyłania wiadomości do klientów połączonych z usługą Azure SignalR. Wiadomości można emitować do wszystkich klientów lub do podzbioru klientów uwierzytelnionych przy użyciu określonego identyfikatora użytkownika lub dodanych do określonej grupy.

Użytkownicy mogą być dodawane do jednej lub więcej grup. Można również użyć powiązania wyjściowego *SignalR,* aby dodać lub usunąć użytkowników do/z grup.

Aby uzyskać więcej informacji, zobacz odwołanie do wiązania wyjścia [ *SignalR* ](../azure-functions/functions-bindings-signalr-service-output.md).

### <a name="signalr-hubs"></a>Koncentratory signalr

SignalR ma koncepcję "koncentratorów". Każde połączenie klienta i każda wiadomość wysłana z usługi Azure Functions jest ograniczone do określonego centrum. Koncentratorów służy do oddzielania połączeń i komunikatów w logiczne przestrzenie nazw.

## <a name="client-development"></a>Rozwój klienta

Aplikacje klienckie SignalR można wykorzystać SDK klienta SignalR w jednym z kilku języków, aby łatwo połączyć się z i odbierać wiadomości z usługi Azure SignalR.

### <a name="configuring-a-client-connection"></a>Konfigurowanie połączenia klienta

Aby połączyć się z usługą SignalR, klient musi zakończyć pomyślną negocjacja połączenia, która składa się z następujących kroków:

1. Złożyć żądanie do punktu końcowego *negocjuj negocjujów* HTTP omówione powyżej, aby uzyskać prawidłowe informacje o połączeniu
1. Łączenie się z usługą SignalR przy użyciu adresu URL punktu końcowego usługi i tokenu dostępu uzyskanego z punktu końcowego *negocjacji*

Zestaw SDK klienta signalr zawiera już logikę wymaganą do wykonania uzgadniania negocjacji. Przekaż adres URL punktu końcowego `negotiate` negocjacji, minus segment, do `HubConnectionBuilder`sdk . Oto przykład w Języku JavaScript:

```javascript
const connection = new signalR.HubConnectionBuilder()
  .withUrl('https://my-signalr-function-app.azurewebsites.net/api')
  .build()
```

Zgodnie z konwencją SDK automatycznie `/negotiate` dołącza do adresu URL i używa go do rozpoczęcia negocjacji.

> [!NOTE]
> Jeśli używasz SDK JavaScript/TypeScript w przeglądarce, musisz [włączyć udostępnianie zasobów między źródłami (CORS)](#enabling-cors) w aplikacji funkcji.

Aby uzyskać więcej informacji na temat korzystania z SDK klienta SignalR, zapoznaj się z dokumentacją dla twojego języka:

* [.NET Standard](https://docs.microsoft.com/aspnet/core/signalr/dotnet-client)
* [Javascript](https://docs.microsoft.com/aspnet/core/signalr/javascript-client)
* [Java](https://docs.microsoft.com/aspnet/core/signalr/java-client)

### <a name="sending-messages-from-a-client-to-the-service"></a>Wysyłanie wiadomości od klienta do usługi

Mimo że SDK SignalR umożliwia aplikacjom klienckim wywoływanie logiki wewnętrznej bazy danych w centrum SignalR, ta funkcja nie jest jeszcze obsługiwana podczas korzystania z usługi SignalR z usługą Azure Functions. Użyj żądań HTTP do wywoływania funkcji platformy Azure.

## <a name="azure-functions-configuration"></a>Konfiguracja usługi Azure Functions

Aplikacje funkcji platformy Azure, które integrują się z usługą Azure SignalR Service, można wdrożyć jak każdą typową aplikację funkcji platformy Azure, przy użyciu technik, takich jak [ciągłe wdrażanie, wdrażanie](../azure-functions/functions-continuous-deployment.md) [zip](../azure-functions/deployment-zip-push.md)i [uruchamianie z pakietu.](../azure-functions/run-functions-from-deployment-package.md)

Istnieje jednak kilka specjalnych zagadnień dotyczących aplikacji korzystających z powiązań usługi SignalR. Jeśli klient działa w przeglądarce, cors musi być włączona. A jeśli aplikacja wymaga uwierzytelniania, można zintegrować punkt końcowy negocjacji z uwierzytelnianiem usługi App Service.

### <a name="enabling-cors"></a>Włączanie cors

Klient JavaScript/TypeScript żąda http do funkcji negocjowania w celu zainicjowania negocjacji połączeń. Gdy aplikacja kliencka jest hostowana w innej domenie niż aplikacja Funkcji platformy Azure, udostępnianie zasobów między źródłami (CORS) musi być włączone w aplikacji Function lub przeglądarka zablokuje żądania.

#### <a name="localhost"></a>Localhost

Podczas uruchamiania aplikacji Funkcja na komputerze lokalnym `Host` można dodać sekcję do *local.settings.json,* aby włączyć usługę CORS. W `Host` sekcji dodaj dwie właściwości:

* `CORS`- wprowadź podstawowy adres URL, który jest źródłem aplikacji klienckiej
* `CORSCredentials`- ustawić `true` go, aby umożliwić "withCredentials" wniosków

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

#### <a name="cloud---azure-functions-cors"></a>Chmura — usługi Azure Functions CORS

Aby włączyć cors w aplikacji funkcji platformy Azure, przejdź do ekranu konfiguracji CORS w obszarze *funkcji platformy* kartę aplikacji funkcji w witrynie Azure portal.

> [!NOTE]
> Konfiguracja cors nie jest jeszcze dostępna w planie użycia systemu Azure Functions Linux. Użyj [usługi Azure API Management,](#cloud---azure-api-management) aby włączyć usługę CORS.

Mechanizm CORS z właściwościami dostępu-Control-Allow-Credentials musi być włączona, aby klient SignalR miał wywołać funkcję negotiate. Zaznacz to pole wyboru, aby je włączyć.

W sekcji *Dozwolone źródła* dodaj wpis z podstawowym adresem URL pochodzenia aplikacji sieci web.

![Konfigurowanie cors](media/signalr-concept-serverless-development-config/cors-settings.png)

#### <a name="cloud---azure-api-management"></a>Chmura — usługa Azure API Management

Usługa Azure API Management udostępnia bramę interfejsu API, która dodaje możliwości do istniejących usług zaplecza. Można go użyć, aby dodać CORS do aplikacji funkcji. Oferuje warstwę zużycia z cenami płatności za akcję i miesięczną bezpłatną dotacją.

Informacje na temat [importowania aplikacji Funkcji platformy Azure](../api-management/import-function-app-as-api.md)można znaleźć w dokumentacji zarządzania interfejsami API. Po zaimportowaniu można dodać zasady przychodzące, aby włączyć usługę CORS z obsługą dostępu do kontroli dostępu-zezwoleniu na poświadczenia.

```xml
<cors allow-credentials="true">
  <allowed-origins>
    <origin>https://azure-samples.github.io</origin>
  </allowed-origins>
  <allowed-methods>
    <method>GET</method>
    <method>POST</method>
  </allowed-methods>
  <allowed-headers>
    <header>*</header>
  </allowed-headers>
  <expose-headers>
    <header>*</header>
  </expose-headers>
</cors>
```

Skonfiguruj klientów SignalR do używania adresu URL zarządzania interfejsami API.

### <a name="using-app-service-authentication"></a>Korzystanie z uwierzytelniania usługi aplikacji

Usługa Azure Functions ma wbudowane uwierzytelnianie, obsługujące popularnych dostawców, takich jak Facebook, Twitter, Microsoft Account, Google i Azure Active Directory. Ta funkcja może być zintegrowana z powiązaniem *SignalRConnectionInfo* w celu utworzenia połączeń z usługą Azure SignalR Service, które zostały uwierzytelnione do identyfikatora użytkownika. Aplikacja może wysyłać wiadomości przy użyciu powiązania danych wyjściowych *SignalR,* które są przeznaczone do tego identyfikatora użytkownika.

W witrynie Azure portal na karcie *Funkcje platformy* aplikacji Funkcji otwórz okno Ustawienia *uwierzytelniania/autoryzacji.* Postępuj zgodnie z dokumentacją [uwierzytelniania usługi app service,](../app-service/overview-authentication-authorization.md) aby skonfigurować uwierzytelnianie przy użyciu wybranego dostawcy tożsamości.

Po skonfigurowaniu uwierzytelnione żądania `x-ms-client-principal-name` HTTP `x-ms-client-principal-id` będą zawierać i nagłówki zawierające odpowiednio nazwę użytkownika i identyfikator użytkownika uwierzytelnionej tożsamości.

Tych nagłówków można użyć w konfiguracji powiązania *SignalRConnectionInfo* do tworzenia uwierzytelnionych połączeń. Oto przykład C# negocjuje `x-ms-client-principal-id` funkcję, która używa nagłówka.

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

Następnie można wysyłać wiadomości do `UserId` tego użytkownika, ustawiając właściwość komunikatu SignalR.

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

Aby uzyskać informacje na temat innych języków, zobacz [powiązania usługi Azure SignalR dla](../azure-functions/functions-bindings-signalr-service.md) usługi Azure Functions odwołania.

## <a name="next-steps"></a>Następne kroki

W tym artykule dowiesz się, jak tworzyć i konfigurować bezserwerowe aplikacje usługi SignalR przy użyciu funkcji platformy Azure. Spróbuj samodzielnie utworzyć aplikację, korzystając z jednego z szybkich uruchomień lub samouczków na [stronie przegląd usługi SignalR](index.yml).