---
title: Przewodnik Szybki start pokazujący, jak korzystać z usługi Azure SignalR Service
description: Przewodnik Szybki start pokazujący, jak za pomocą usługi Azure SignalR Service utworzyć pokój rozmów przy użyciu aplikacji ASP.NET Core MVC.
author: sffamily
ms.service: signalr
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 03/01/2019
ms.author: zhshang
ms.openlocfilehash: 3dc893ea10e47e867110f674a458498a6bd24a4f
ms.sourcegitcommit: 179918af242d52664d3274370c6fdaec6c783eb6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/13/2019
ms.locfileid: "65560716"
---
# <a name="quickstart-create-a-chat-room-by-using-signalr-service"></a>Szybki start: Tworzenie pokoju rozmów przy użyciu usługi SignalR


Usługa Azure SignalR Service to usługa platformy Azure, która ułatwia deweloperom tworzenie aplikacji internetowych z funkcjami działającymi w czasie rzeczywistym. Ta usługa bazuje na bibliotece [SignalR dla platformy ASP.NET Core 2.0](https://docs.microsoft.com/aspnet/core/signalr/introduction).

W tym artykule pokazano, jak rozpocząć pracę z usługą Azure SignalR Service. W tym przewodniku Szybki Start utworzysz aplikację do obsługi rozmów za pomocą aplikacji sieci web platformy ASP.NET Core MVC. Ta aplikacja nawiąże połączenie z zasobem usługi Azure SignalR Service, aby umożliwić aktualizacje zawartości w czasie rzeczywistym. Będziesz hostowanie aplikacji sieci web w środowisku lokalnym i połącz się z wielu klientów w przeglądarkach. Każdy klient będzie mógł wypychać aktualizacje zawartości do innych klientów. 

Do wykonania kroków tego przewodnika Szybki start możesz użyć dowolnego edytora kodu. Jedną z opcji jest [programu Visual Studio Code](https://code.visualstudio.com/), który jest dostępny w Windows, macOS i platformy Linux.

Kod dla tego samouczka jest dostępny do pobrania w [repozytorium GitHub o nazwie AzureSignalR-samples](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/ChatRoom). Ponadto można utworzyć zasobów platformy Azure używane w tym przewodniku Szybki Start, postępując zgodnie z [utworzyć skrypt SignalR Service](scripts/signalr-cli-create-service.md).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Wymagania wstępne

* Zainstaluj [platformy .NET Core SDK](https://www.microsoft.com/net/download/windows).
* Pobierz lub sklonuj repozytorium GitHub [AzureSignalR-sample](https://github.com/aspnet/AzureSignalR-samples). 

## <a name="create-an-azure-signalr-resource"></a>Tworzenie zasobu usługi Azure SignalR

[!INCLUDE [azure-signalr-create](../../includes/signalr-create.md)]

## <a name="create-an-aspnet-core-web-app"></a>Tworzenie aplikacji internetowej ASP.NET Core

W tej sekcji użyjesz [platformy .NET Core interfejsu wiersza polecenia (CLI)](https://docs.microsoft.com/dotnet/core/tools/) do utworzenia projektu aplikacji sieci web programu ASP.NET Core MVC. Zaletą używania interfejsu wiersza polecenia platformy .NET Core w programie Visual Studio jest, że jest ona dostępna w Windows, macOS i Linux platformy. 

1. Utwórz folder dla projektu. Ten przewodnik Szybki Start używa *E:\Testing\chattest* folderu.

2. W nowym folderze uruchom następujące polecenie, aby utworzyć projekt:

        dotnet new mvc


## <a name="add-secret-manager-to-the-project"></a>Dodawanie narzędzia Secret Manager do projektu

W tej sekcji dodasz [narzędzie Menedżer klucz tajny](https://docs.microsoft.com/aspnet/core/security/app-secrets) do projektu. Narzędzie Menedżer klucz tajny przechowuje danymi poufnymi w przypadku tworzonym spoza Twojej drzewa projektu. Takie podejście pomaga uniemożliwiają przypadkowe udostępnianie kluczy tajnych aplikacji w kodzie źródłowym.

1. Otwórz plik *csproj*. Dodaj element `DotNetCliToolReference`, aby uwzględnić narzędzia *Microsoft.Extensions.SecretManager.Tools*. Również dodać `UserSecretsId` elementu, jak pokazano w poniższym kodzie dla *chattest.csproj*, a następnie zapisz plik.

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

1. Dodaj odwołanie do `Microsoft.Azure.SignalR` pakietu NuGet, uruchamiając następujące polecenie:

        dotnet add package Microsoft.Azure.SignalR

2. Uruchom następujące polecenie, aby przywrócić pakiety dla projektu:

        dotnet restore

3. Dodaj wpis tajny o nazwie *Azure:SignalR:ConnectionString* do narzędzia Secret Manager. 

    Ten wpis tajny będzie zawierać parametry połączenia umożliwiające dostęp do zasobu usługi SignalR Service. *Azure: SignalR:ConnectionString* to domyślny klucz konfiguracji SignalR szuka nawiązać połączenie. Zastąp wartość w poniższym poleceniu ciąg połączenia dla zasobu usługi SignalR.

    Należy uruchomić to polecenie w tym samym katalogu co *.csproj* pliku.

    ```
    dotnet user-secrets set Azure:SignalR:ConnectionString "<Your connection string>"    
    ```

    Menedżer klucz tajny będą używane tylko w przypadku testowania aplikacji sieci web, gdy jest on hostowany lokalnie. Później w samouczku wdrożysz aplikacji rozmów w sieci web na platformie Azure. Po wdrożeniu aplikacji sieci web na platformie Azure, korzystając z ustawienia aplikacji zamiast przechowywać parametry połączenia z menedżerem klucz tajny.

    Ten klucz tajny jest dostępny za pomocą interfejsu API konfiguracji. Dwukropek (:) działanie jest nazwa konfiguracji za pomocą interfejsu API konfiguracji na wszystkich obsługiwanych platformach. Zobacz [konfiguracji przez środowisko](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/index?tabs=basicconfiguration&view=aspnetcore-2.0). 


4. Otwórz plik *Startup.cs* i zaktualizuj metodę `ConfigureServices` tak, aby używała usługi SignalR Service przez wywołanie metody `services.AddSignalR().AddAzureSignalR()`:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddMvc();
        services.AddSignalR().AddAzureSignalR();
    }
    ```

    Nie, przekazując parametr `AddAzureSignalR()`, ten kod używa domyślnego klucza konfiguracji dla parametrów połączenia zasobu usługi SignalR. Domyślny klucz konfiguracji jest *platformy Azure: SignalR:ConnectionString*.

5. Oprócz tego w pliku *Startup.cs* zaktualizuj metodę `Configure`, zastępując wywołanie metody `app.UseStaticFiles()` następującym kodem, i zapisz plik.

    ```csharp
    app.UseFileServer();
    app.UseAzureSignalR(routes =>
    {
        routes.MapHub<Chat>("/chat");
    });
    ```            

### <a name="add-a-hub-class"></a>Dodawanie klasy centrum

W bibliotece SignalR Centrum to podstawowy składnik, który udostępnia zestaw metod, które mogą być wywoływane przez klienta. W tej sekcji klasa centrum jest definiowania za pomocą dwóch metod: 

* `Broadcast`: ta metoda rozgłasza komunikat do wszystkich klientów.
* `Echo`: ta metoda wysyła komunikat z powrotem do elementu wywołującego.

Użyj obu metod `Clients` interfejsu, który zawiera zestaw SDK z SignalR platformy ASP.NET Core. Ten interfejs zapewnia dostęp do wszyscy połączeni klienci, dzięki czemu będzie można wypchnąć zawartości do klientów.

1. W katalogu projektu dodaj nowy folder o nazwie *Hub*. Dodaj nowy plik kodu centrum o nazwie *Chat.cs* do nowego folderu.

2. Dodaj następujący kod do *Chat.cs* można zdefiniować klasy koncentratora, a następnie zapisz plik. 

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

### <a name="add-the-client-interface-for-the-web-app"></a>Dodawanie interfejsu klienta dla aplikacji sieci web

Interfejs użytkownika klienta dla tej aplikacji pokoju rozmów będzie składać się z kodu HTML i JavaScript w pliku o nazwie *index.html* w *wwwroot* katalogu.

Kopia *index.html* pliku *css* folderu i *skrypty* folderu z *wwwroot* folderu [przykłady repozytorium](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/ChatRoom/wwwroot). Wklej je do projektu *wwwroot* folderu.

Oto główne kod *index.html*: 

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

Kod w *index.html* wywołania `HubConnectionBuilder.build()` do nawiązania połączenia HTTP do zasobu biblioteki SignalR platformy Azure.

Jeśli połączenie zostanie nawiązane pomyślnie, zostanie przekazane do metody `bindConnectionMessage`, która dodaje procedury obsługi zdarzeń dla przychodzących wypchnięć zawartości do klienta. 

Metoda `HubConnection.start()` uruchamia komunikację z centrum. Następnie `onConnected()` dodaje procedury obsługi zdarzeń przycisku. Te procedury obsługi używają połączenia w celu umożliwienia temu klientowi wpychania aktualizacji zawartości do wszystkich połączonych klientów.

## <a name="add-a-development-runtime-profile"></a>Dodawanie profilu środowiska uruchomieniowego programowania

W tej sekcji dodasz środowiska uruchomieniowego programowania dla platformy ASP.NET Core. Aby uzyskać więcej informacji, zobacz [Praca z wieloma środowiskami w programie ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/environments).

1. Utwórz folder o nazwie *właściwości* w projekcie.

2. Dodaj nowy plik o nazwie *launchSettings.json* w folderze o następującej zawartości i Zapisz plik.

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

1. Aby skompilować aplikację przy użyciu interfejsu wiersza polecenia platformy .NET Core, uruchom następujące polecenie w powłoce poleceń:

        dotnet build

2. Po kompilacja zakończy się pomyślnie, należy uruchomić następujące polecenie, aby lokalnie uruchomić aplikację sieci web:

        dotnet run

    Aplikacja będzie obsługiwana lokalnie na porcie 5000, zgodnie z konfiguracją w naszym rozwoju środowiska uruchomieniowego profilu:

        E:\Testing\chattest>dotnet run
        Hosting environment: Development
        Content root path: E:\Testing\chattest
        Now listening on: http://localhost:5000
        Application started. Press Ctrl+C to shut down.    

3. Otwórz dwa okna przeglądarki. W każdej przeglądarce przejdź do `http://localhost:5000`. Zostanie wyświetlony monit wpisz swoje imię. Wprowadź nazwę klienta dla klientów i wypychania zawartości wiadomości między klientami, zarówno za pomocą testu **wysyłania** przycisku.

    ![Przykład rozmowy grupy usługi Azure SignalR](media/signalr-quickstart-dotnet-core/signalr-quickstart-complete-local.png)



## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nastąpi przejście do następnego samouczka, możesz zachować zasoby utworzone w tym przewodniku Szybki Start i użyć ich ponownie.

Jeśli zakończeniu przewodnika Szybki Start Przykładowa aplikacja można usunąć zasobów platformy Azure utworzone w tym przewodniku Szybki Start, aby uniknąć naliczania opłat. 

> [!IMPORTANT]
> Usunięcie grupy zasobów jest nieodwracalne i obejmuje wszystkie zasoby w tej grupie. Uważaj, aby nie usunąć przypadkowo niewłaściwych zasobów lub niewłaściwej grupy zasobów. Jeśli utworzono zasoby do hostowania w tym przykładzie w istniejącej grupy zasobów, która zawiera zasoby, które chcesz zachować, możesz usuwać poszczególne zasoby pojedynczo z jego bloku zamiast usuwania grupy zasobów.
> 
> 

Zaloguj się do witryny [Azure Portal](https://portal.azure.com) i wybierz pozycję **Grupy zasobów**.

W **Filtruj według nazwy** tekstu wpisz nazwę grupy zasobów. W instrukcjach dla tego przewodnika Szybki start używana była grupa zasobów o nazwie *SignalRTestResources*. W grupie zasobów na liście wyników, wybierz przycisk wielokropka (**...** ) > **Usuń grupę zasobów**.

   
![Opcje usuwania grupy zasobów](./media/signalr-quickstart-dotnet-core/signalr-delete-resource-group.png)


Zobaczysz prośbę o potwierdzenie usunięcia grupy zasobów. Wprowadź nazwę grupy zasobów w celu potwierdzenia, a następnie wybierz **Usuń**.
   
Po krótkim czasie grupa zasobów i wszystkie jej zasoby zostaną usunięte.



## <a name="next-steps"></a>Kolejne kroki

W tym przewodniku Szybki Start utworzono nowy zasób usługi Azure SignalR Service. Następnie używana z aplikacją sieci web platformy ASP.NET Core do wypychania aktualizacji zawartości w czasie rzeczywistym do wielu klientów połączonych. Aby dowiedzieć się więcej o korzystaniu z usługi Azure SignalR Service, przejdź do samouczka, który demonstruje uwierzytelniania.

> [!div class="nextstepaction"]
> [Uwierzytelnianie w usłudze Azure SignalR Service](./signalr-concept-authenticate-oauth.md)


