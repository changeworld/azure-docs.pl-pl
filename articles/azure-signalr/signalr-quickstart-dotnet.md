---
title: Przewodnik Szybki Start dowiesz się, jak używać usługi Azure SignalR Service za pomocą platformy ASP.NET
description: 'Szybki Start: tworzenie pokoju rozmów za pomocą środowiska ASP.NET framework za pomocą usługi Azure SignalR Service.'
author: sffamily
ms.service: signalr
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 04/20/2019
ms.author: zhshang
ms.openlocfilehash: 2020ee02d236ca13431adb736d9f48171d33b4f3
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/14/2019
ms.locfileid: "67147414"
---
# <a name="quickstart-create-a-chat-room-with-aspnet-and-signalr-service"></a>Szybki start: Tworzenie pokoju rozmów z platformą ASP.NET i usługi SignalR

Usługi Azure SignalR Service opiera się na [SignalR dla programu ASP.NET Core 2.0](https://docs.microsoft.com/aspnet/core/signalr/introduction), czyli **nie** 100% zgodny z biblioteki SignalR platformy ASP.NET. Usługi Azure SignalR Service dotąd zaimplementowana protokół danych biblioteki SignalR platformy ASP.NET przy użyciu najnowszych technologii ASP.NET Core. Korzystając z usługi Azure SignalR Service dla biblioteki SignalR platformy ASP.NET, niektóre funkcje biblioteki SignalR platformy ASP.NET nie są już obsługiwane, na przykład SignalR platformy Azure nie powtarzania wiadomości, gdy klient ponownie nawiąże połączenie. Ponadto transportu zawsze ramki i JSONP nie są obsługiwane. Niektóre zmiany w kodzie i odpowiednia wersja zależne biblioteki są wymagane do aplikacji ASP.NET SignalR pracy z usługą SignalR. 

Zapoznaj się [doc różnice wersji](https://docs.microsoft.com/aspnet/core/signalr/version-differences?view=aspnetcore-2.2) pełną listę funkcji porównania biblioteki SignalR platformy ASP.NET z biblioteki SignalR platformy ASP.NET Core.

W tym przewodniku Szybki Start będzie dowiesz się, jak rozpocząć pracę z platformą ASP.NET i usługi Azure SignalR Service dla podobnych [aplikację pokoju rozmów](./signalr-quickstart-dotnet-core.md).


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
## <a name="prerequisites"></a>Wymagania wstępne

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
* [.NET 4.6.1](https://www.microsoft.com/net/download/windows)
* [ASP.NET SignalR 2.4.1](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/)

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do [witryny Azure Portal](https://portal.azure.com/) przy użyciu danych konta Azure.

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

*Bez użycia serwera* tryb nie jest obsługiwany w przypadku aplikacji ASP.NET SignalR. Zawsze używaj *domyślne* lub *klasycznego* dla wystąpienia usługi Azure SignalR Service.

Można również tworzyć zasoby platformy Azure używane w tym przewodniku Szybki Start z [utworzyć skrypt SignalR Service](scripts/signalr-cli-create-service.md).

## <a name="clone-the-sample-application"></a>Klonowanie przykładowej aplikacji

W czasie, gdy usługa jest wdrażana, przejdźmy do pracy z kodem. Sklonuj [przykładową aplikację z witryny GitHub](https://github.com/aspnet/AzureSignalR-samples/tree/master/aspnet-samples/ChatRoom), ustaw parametry połączenia usługi SignalR Service i uruchom aplikację lokalnie.

1. Otwórz okno terminala usługi Git. Przejdź do folderu, w którym chcesz sklonować przykładowy projekt.

1. Uruchom następujące polecenie w celu sklonowania przykładowego repozytorium. To polecenie tworzy kopię przykładowej aplikacji na komputerze.

    ```bash
    git clone https://github.com/aspnet/AzureSignalR-samples.git
    ```

## <a name="configure-and-run-chat-room-web-app"></a>Konfigurowanie i uruchamianie aplikacji sieci web pokoju rozmów

1. Uruchom program Visual Studio i otwórz rozwiązanie w *aspnet-samples/pokoju/* folderu sklonowanego repozytorium.

1. W przeglądarce, na której witryny Azure portal jest otwarty Znajdź i wybierz utworzonego wystąpienia.

1. Wybierz pozycję **Klucze**, aby wyświetlić parametry połączenia dla wystąpienia usługi SignalR Service.

1. Zaznacz i skopiuj podstawowe parametry połączenia.

1. Teraz ustawimy parametry połączenia w pliku web.config.

    ```xml
    <configuration>
    <connectionStrings>
        <add name="Azure:SignalR:ConnectionString" connectionString="<Replace By Your Connection String>"/>
    </connectionStrings>
    ...
    </configuration>
    ```

1. W *Startup.cs*, zamiast wywoływać metodę `MapSignalR()`, należy wywołać `MapAzureSignalR({your_applicationName})` i przekazać w ciągu połączenia, aby aplikacja nawiązać połączenie z usługą zamiast hostingu SignalR samodzielnie. Zastąp `{YourApplicationName}` na nazwę aplikacji. Ta nazwa jest unikatowa nazwa w celu odróżnienia tej aplikacji z innych aplikacji. Możesz użyć `this.GetType().FullName` jako wartość.

    ```cs
    public void Configuration(IAppBuilder app)
    {
        // Any connection or hub wire up and configuration should go here
        app.MapAzureSignalR(this.GetType().FullName);
    }
    ```

    Musisz także odwoływać się do zestawu SDK usługi przed rozpoczęciem korzystania z tych interfejsów API. Otwórz **narzędzia | Menedżer pakietów NuGet | Konsola Menedżera pakietów** i uruchom polecenie:

    ```powershell
    Install-Package Microsoft.Azure.SignalR.AspNet
    ```

    Inne niż te zmiany wszystkie inne elementy pozostają takie same, można nadal używać interfejs koncentratora, który znasz już na zapisanie logiki biznesowej.

    > [!NOTE]
    > W implementacji punktu końcowego `/signalr/negotiate` jest uwidaczniany dla negocjacji przez zestaw SDK usługi Azure SignalR Service. Gdy klienci spróbują połączyć i przekierowywać klientów na punkt końcowy usługi zdefiniowane w parametrach połączenia zwróci odpowiedź negocjacji specjalne.

1. Naciśnij klawisz **F5** uruchomić projekt w trybie debugowania. Możesz zobaczyć aplikacji jest uruchamiany lokalnie. Zamiast hostingu środowiska uruchomieniowego SignalR przez samą aplikację, teraz nawiąże połączenie z usługi Azure SignalR Service.

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]



> [!IMPORTANT]
> Usunięcie grupy zasobów jest nieodwracalne i grupa zasobów oraz wszystkie zawarte w niej zasoby zostaną trwale usunięte. Uważaj, aby nie usunąć przypadkowo niewłaściwych zasobów lub grupy zasobów. Jeśli zasoby do hostowania tego przykładu zostały utworzone wewnątrz istniejącej grupy zasobów zawierającej zasoby, które chcesz zachować, możesz usunąć każdy zasób oddzielnie z odpowiadającego mu bloku zamiast usuwać całą grupę zasobów.
> 
> 

Zaloguj się do witryny [Azure Portal](https://portal.azure.com) i kliknij pozycję **Grupy zasobów**.

W polu tekstowym **Filtruj według nazwy...** wpisz nazwę grupy zasobów. W instrukcjach dla tego przewodnika Szybki start używana była grupa zasobów o nazwie *SignalRTestResources*. Dla grupy zasobów na liście wyników kliknij pozycję **...** , a następnie kliknij pozycję **Usuń grupę zasobów**.

   
![Usuwanie](./media/signalr-quickstart-dotnet-core/signalr-delete-resource-group.png)

Po krótkim czasie grupa zasobów i wszystkie zawarte w niej zasoby zostaną usunięte.

## <a name="next-steps"></a>Kolejne kroki

W tym przewodniku Szybki Start utworzono nowy zasób usługi Azure SignalR Service i używać go z aplikacji sieci web platformy ASP.NET. Dowiedz się, jak opracowywanie aplikacji w czasie rzeczywistym przy użyciu usługi Azure SignalR Service za pomocą programu ASP.NET Core.

> [!div class="nextstepaction"]
> [Azure SignalR Service za pomocą programu ASP.NET Core](./signalr-quickstart-dotnet-core.md)
