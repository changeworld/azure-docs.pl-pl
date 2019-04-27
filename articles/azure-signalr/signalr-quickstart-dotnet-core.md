---
title: Przewodnik Szybki start pokazujący, jak korzystać z usługi Azure SignalR Service
description: Przewodnik Szybki start pokazujący, jak za pomocą usługi Azure SignalR Service utworzyć pokój rozmów przy użyciu aplikacji ASP.NET Core MVC.
author: sffamily
ms.service: signalr
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 03/01/2019
ms.author: zhshang
ms.openlocfilehash: 248861848aa905f9cbff01ab60affd7cf21aae78
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60690331"
---
# <a name="quickstart-create-a-chat-room-with-signalr-service"></a>Szybki start: tworzenie pokoju rozmów za pomocą usługi SignalR Service


Usługa Azure SignalR Service to usługa platformy Azure, która ułatwia deweloperom tworzenie aplikacji internetowych z funkcjami działającymi w czasie rzeczywistym. Ta usługa bazuje na bibliotece [SignalR dla platformy ASP.NET Core 2.0](https://docs.microsoft.com/aspnet/core/signalr/introduction).

W tym artykule pokazano, jak rozpocząć pracę z usługą Azure SignalR Service. W tym przewodniku Szybki start utworzysz aplikację do rozmów przy użyciu aplikacji typu Aplikacja internetowa platformy ASP.NET Core MVC. Ta aplikacja nawiąże połączenie z zasobem usługi Azure SignalR Service, aby umożliwić aktualizacje zawartości w czasie rzeczywistym. Aplikację internetową będziesz hostować lokalnie i łączyć z wieloma klientami przeglądarkowymi. Każdy klient będzie mógł wypychać aktualizacje zawartości do innych klientów. 


Do wykonania kroków tego przewodnika Szybki start możesz użyć dowolnego edytora kodu. Doskonałym wyborem jest program [Visual Studio Code](https://code.visualstudio.com/), dostępny na platformach Windows, macOS i Linux.

Kod dla tego samouczka jest dostępny do pobrania w [repozytorium GitHub o nazwie AzureSignalR-samples](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/ChatRoom).  Ponadto zasoby platformy Azure używane w tym przewodniku Szybki start można utworzyć za pomocą [skryptu do tworzenia usługi SignalR Service](scripts/signalr-cli-create-service.md).

![Przewodnik Szybki start ukończony, wersja lokalna](media/signalr-quickstart-dotnet-core/signalr-quickstart-complete-local.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Wymagania wstępne

* Zainstalowany [zestaw .NET Core SDK](https://www.microsoft.com/net/download/windows)
* Pobierz lub sklonuj repozytorium GitHub [AzureSignalR-sample](https://github.com/aspnet/AzureSignalR-samples). 

## <a name="create-an-azure-signalr-resource"></a>Tworzenie zasobu usługi Azure SignalR

[!INCLUDE [azure-signalr-create](../../includes/signalr-create.md)]

## <a name="create-an-aspnet-core-web-app"></a>Tworzenie aplikacji internetowej ASP.NET Core

W tej sekcji użyjesz [interfejsu wiersza polecenia platformy .NET Core ](https://docs.microsoft.com/dotnet/core/tools/) do utworzenia nowego projektu Aplikacja internetowa platformy ASP.NET Core MVC. Przewagą używania interfejsu wiersza polecenia platformy .NET Core zamiast programu Visual Studio jest to, że jest dostępny na platformach Windows, macOS i Linux. 

1. Utwórz nowy folder dla projektu. W tym przewodniku Szybki start używany jest folder *E:\Testing\chattest*.

2. W nowym folderze wykonaj następujące polecenie, aby utworzyć nowy projekt Aplikacja internetowa platformy ASP.NET Core MVC:

        dotnet new mvc


## <a name="add-secret-manager-to-the-project"></a>Dodawanie narzędzia Secret Manager do projektu

W tej sekcji dodasz narzędzie [Secret Manager](https://docs.microsoft.com/aspnet/core/security/app-secrets) do projektu. Narzędzie Secret manager przechowuje poufne dane potrzebne w pracy deweloperskiej poza Twoim drzewem projektu. Takie podejście zapobiega przypadkowemu ujawnieniu wpisów tajnych aplikacji w kodzie źródłowym.

1. Otwórz plik *csproj*. Dodaj element `DotNetCliToolReference`, aby uwzględnić narzędzia *Microsoft.Extensions.SecretManager.Tools*. Dodaj również element `UserSecretsId` w sposób pokazany poniżej, a następnie zapisz plik.

    *chattest.csproj:*

    ```xml
    <Project Sdk="Microsoft.NET.Sdk.Web">
    <PropertyGroup>
        <TargetFramework>netcoreapp2.0</TargetFramework>
        <UserSecretsId>SignalRChatRoomEx</UserSecretsId>
    </PropertyGroup>
    <ItemGroup>
        <PackageReference Include="Microsoft.AspNetCore.All" Version="2.0.0" />
    </ItemGroup>
    <ItemGroup>
        <DotNetCliToolReference Include="Microsoft.VisualStudio.Web.CodeGeneration.Tools" Version="2.0.0" />
        <DotNetCliToolReference Include="Microsoft.Extensions.SecretManager.Tools" Version="2.0.0" />
    </ItemGroup>
    </Project>    
    ```

## <a name="add-azure-signalr-to-the-web-app"></a>Dodawanie usługi Azure SignalR do aplikacji internetowej

1. Dodaj odwołanie do pakietu NuGet `Microsoft.Azure.SignalR`, wykonując następujące polecenie:

        dotnet add package Microsoft.Azure.SignalR

2. Uruchom następujące polecenie, aby przywrócić pakiety dla swojego projektu.

        dotnet restore

3. Dodaj wpis tajny o nazwie *Azure:SignalR:ConnectionString* do narzędzia Secret Manager. 

    Ten wpis tajny będzie zawierać parametry połączenia umożliwiające dostęp do zasobu usługi SignalR Service. *Azure:SignalR:ConnectionString* to domyślny klucz konfiguracji, którego usługa SignalR szuka w celu nawiązania połączenia. Zastąp wartość w poleceniu poniżej parametrami połączenia dla Twojego zasobu usługi SignalR Service.

    To polecenie należy wykonać w tym samym katalogu, w którym znajduje się plik *csproj*.

    ```
    dotnet user-secrets set Azure:SignalR:ConnectionString "<Your connection string>"    
    ```

    Narzędzie Secret Manager będzie służyć do testowania aplikacji internetowej, tylko gdy będzie hostowana lokalnie. W późniejszym samouczku wdrożysz aplikację internetową do rozmów na platformie Azure. Po wdrożeniu aplikacji internetowej na platformie Azure użyjesz ustawienia aplikacji zamiast zapisywania parametrów połączenia za pomocą narzędzia Secret Manager.

    Ten klucz tajny jest dostępny za pomocą konfiguracji interfejsu API. Dwukropek (:) działa w nazwie konfiguracji z interfejsem API konfiguracji na wszystkich obsługiwanych platformach. Zobacz [Konfiguracja przez środowisko](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/index?tabs=basicconfiguration&view=aspnetcore-2.0). 


4. Otwórz plik *Startup.cs* i zaktualizuj metodę `ConfigureServices` tak, aby używała usługi SignalR Service przez wywołanie metody `services.AddSignalR().AddAzureSignalR()`:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddMvc();
        services.AddSignalR().AddAzureSignalR();
    }
    ```

    Gdy parametr nie zostanie przekazany do metody `AddAzureSignalR()`, ten kod użyje domyślnego klucza konfiguracji *Azure:SignalR:ConnectionString* dla parametrów połączenia z zasobem usługi SignalR Service.

5. Oprócz tego w pliku *Startup.cs* zaktualizuj metodę `Configure`, zastępując wywołanie metody `app.UseStaticFiles()` następującym kodem, i zapisz plik.

    ```csharp
    app.UseFileServer();
    app.UseAzureSignalR(routes =>
    {
        routes.MapHub<Chat>("/chat");
    });
    ```            

### <a name="add-a-hub-class"></a>Dodawanie klasy centrum

W usłudze SignalR centrum jest podstawowym składnikiem, który ujawnia zestaw metod możliwych do wywoływania z klienta. W tej sekcji klasa centrum jest definiowania za pomocą dwóch metod: 

* `Broadcast`: ta metoda rozgłasza komunikat do wszystkich klientów.
* `Echo`: ta metoda wysyła komunikat z powrotem do elementu wywołującego.

Obie metody używają interfejsu `Clients` dostarczonego przez zestaw ASP.NET Core SignalR SDK. Ten interfejs daje dostęp do wszystkich połączonych klientów, umożliwiając wypychanie do nich zawartości.

1. W katalogu projektu dodaj nowy folder o nazwie *Hub*. Dodaj nowy plik kodu centrum o nazwie *Chat.cs* do nowego folderu.

2. Dodaj następujący kod do pliku *Chat.cs* w celu zdefiniowania klasy centrum i zapisz plik. 

    Zaktualizuj przestrzeń nazw dla tej klasy, jeśli została użyta nazwa projektu inna niż *chattest*.

    ```csharp
    using Microsoft.AspNetCore.SignalR;

    namespace chattest
    {

        public class Chat : Hub
        {
            public void BroadcastMessage(string name, string message)
            {
                Clients.All.SendAsync("broadcastMessage", name, message);
            }

            public void Echo(string name, string message)
            {
                Clients.Client(Context.ConnectionId).SendAsync("echo", name, message + " (echo from server)");
            }
        }
    }
    ```

### <a name="add-the-web-app-client-interface"></a>Dodawanie interfejsu klienta aplikacji internetowej

Interfejs użytkownika klienta dla tej aplikacji pokoju rozmów będzie składać się z kodu HTML i JavaScript w pliku o nazwie *index.html* w katalogu *wwwroot*.

Skopiuj plik *index.html* oraz foldery *css* i *scripts* z folderu *wwwroot* [repozytorium z przykładami](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/ChatRoom/wwwroot) do folderu *wwwroot* w Twoim projekcie.

Główny kod pliku *index.html*: 

```javascript
var connection = new signalR.HubConnectionBuilder()
                            .withUrl('/chat')
                            .build();
bindConnectionMessage(connection);
connection.start()
    .then(function () {
        onConnected(connection);
    })
    .catch(function (error) {
        console.error(error.message);
    });
```    

Kod w pliku *index.html* wywołuje metodę `HubConnectionBuilder.build()` w celu nawiązania połączenia HTTP z zasobem usługi Azure SignalR.

Jeśli połączenie zostanie nawiązane pomyślnie, zostanie przekazane do metody `bindConnectionMessage`, która dodaje procedury obsługi zdarzeń dla przychodzących wypchnięć zawartości do klienta. 

Metoda `HubConnection.start()` uruchamia komunikację z centrum. Po uruchomieniu komunikacji metoda `onConnected()` dodaje procedury obsługi zdarzeń przycisku. Te procedury obsługi używają połączenia w celu umożliwienia temu klientowi wpychania aktualizacji zawartości do wszystkich połączonych klientów.

## <a name="add-a-development-runtime-profile"></a>Dodawanie profilu środowiska uruchomieniowego programowania

W tej sekcji dodasz środowisko uruchomieniowe programowania dla platformy ASP.NET Core. Aby uzyskać więcej informacji na temat środowiska uruchomieniowego dla platformy ASP.NET Core, zobacz [Praca z wieloma środowiskami w programie ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/environments).

1. Utwórz w swoim projekcie nowy folder o nazwie *Properties*.

2. Dodaj do tego folderu nowy plik o nazwie *launchSettings.json* i następującej zawartości, a następnie zapisz plik.

    ```json
    {
        "profiles" : 
        {
            "ChatRoom": 
            {
                "commandName": "Project",
                "launchBrowser": true,
                "environmentVariables": 
                {
                    "ASPNETCORE_ENVIRONMENT": "Development"
                },
                "applicationUrl": "http://localhost:5000/"
            }
        }
    }
    ```


## <a name="build-and-run-the-app-locally"></a>Lokalne kompilowanie i uruchamianie aplikacji

1. Aby skompilować aplikację przy użyciu interfejsu wiersza polecenia platformy .NET Core, wykonaj następujące polecenie w powłoce poleceń:

        dotnet build

2. Po pomyślnym zakończeniu kompilacji wykonaj następujące polecenie w celu lokalnego uruchomienia aplikacji internetowej:

        dotnet run

    Aplikacja będzie hostowana lokalnie na porcie 5000 zgodnie z konfiguracją w naszym profilu środowiska uruchomieniowego programowania:

        E:\Testing\chattest>dotnet run
        Hosting environment: Development
        Content root path: E:\Testing\chattest
        Now listening on: http://localhost:5000
        Application started. Press Ctrl+C to shut down.    

3. Uruchom dwa okna przeglądarki i przejdź w każdym z nich pod adres `http://localhost:5000`. Pojawi się monit o wprowadzenie Twojej nazwy. Wprowadź nazwę dla obu klientów i przetestuj wypychanie zawartości komunikatów między oboma klientami przy użyciu przycisku **Send** (Wyślij).

    ![Przewodnik Szybki start ukończony, wersja lokalna](media/signalr-quickstart-dotnet-core/signalr-quickstart-complete-local.png)



## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli zamierzasz przejść do kolejnego samouczka, możesz zachować zasoby utworzone w tym przewodniku Szybki start i użyć ich ponownie w następnym samouczku.

W przeciwnym razie po zakończeniu pracy z przykładową aplikacją poradnika Szybki start możesz usunąć zasoby platformy Azure utworzone w tym poradniku Szybki start, aby uniknąć naliczania opłat. 

> [!IMPORTANT]
> Usunięcie grupy zasobów jest nieodwracalne i grupa zasobów oraz wszystkie zawarte w niej zasoby zostaną trwale usunięte. Uważaj, aby nie usunąć przypadkowo niewłaściwych zasobów lub grupy zasobów. Jeśli zasoby do hostowania tego przykładu zostały utworzone wewnątrz istniejącej grupy zasobów zawierającej zasoby, które chcesz zachować, możesz usunąć każdy zasób oddzielnie z odpowiadającego mu bloku zamiast usuwać całą grupę zasobów.
> 
> 

Zaloguj się do witryny [Azure Portal](https://portal.azure.com) i kliknij pozycję **Grupy zasobów**.

W polu tekstowym **Filtruj według nazwy...** wpisz nazwę grupy zasobów. W instrukcjach dla tego przewodnika Szybki start używana była grupa zasobów o nazwie *SignalRTestResources*. Dla grupy zasobów na liście wyników kliknij pozycję **...**, a następnie kliknij pozycję **Usuń grupę zasobów**.

   
![Usuwanie](./media/signalr-quickstart-dotnet-core/signalr-delete-resource-group.png)


Zobaczysz prośbę o potwierdzenie usunięcia grupy zasobów. Wpisz nazwę grupy zasobów w celu potwierdzenia, a następnie kliknij pozycję **Usuń**.
   
Po krótkim czasie grupa zasobów i wszystkie zawarte w niej zasoby zostaną usunięte.



## <a name="next-steps"></a>Kolejne kroki

W tym przewodniku Szybki start utworzony został nowy zasób usługi Azure SignalR Service, którego użyto z aplikacją internetową platformy ASP.NET Core do wypychania aktualizacji zawartości w czasie rzeczywistym do wielu połączonych klientów. Aby dowiedzieć się więcej o korzystaniu z usługi Azure SignalR Service, przejdź do następnego samouczka, w którym zaprezentowano uwierzytelnianie.

> [!div class="nextstepaction"]
> [Uwierzytelnianie w usłudze Azure SignalR Service](./signalr-concept-authenticate-oauth.md)


