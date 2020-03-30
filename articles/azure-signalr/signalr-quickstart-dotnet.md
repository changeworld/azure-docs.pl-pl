---
title: Rozwijaj z ASP.NET — usługa Azure SignalR
description: Szybki start do korzystania z usługi Azure SignalR, aby utworzyć pokój rozmów z ASP.NET framework.
author: sffamily
ms.service: signalr
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 04/20/2019
ms.author: zhshang
ms.openlocfilehash: ec5b7a75bced4b7cd81a120925558b8c1be57818
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "74158177"
---
# <a name="quickstart-create-a-chat-room-with-aspnet-and-signalr-service"></a>Szybki start: Tworzenie pokoju rozmów z usługą ASP.NET i SignalR

Usługa Azure SignalR jest oparta na [SignalR dla ASP.NET Core 2.0](https://docs.microsoft.com/aspnet/core/signalr/introduction), który **nie** jest w 100% zgodny z ASP.NET SignalR. Usługa Azure SignalR service ponownie zaimplementowana ASP.NET protokołu danych SignalR w oparciu o najnowsze technologie ASP.NET Core. Podczas korzystania z usługi Azure SignalR dla ASP.NET SignalR, niektóre funkcje ASP.NET SignalR nie są już obsługiwane, na przykład usługa Azure SignalR nie odtwarza komunikatów, gdy klient łączy się ponownie. Ponadto transport Forever Frame i JSONP nie są obsługiwane. Niektóre zmiany kodu i właściwa wersja bibliotek zależnych są potrzebne, aby ASP.NET aplikacji SignalR pracy z signalr service. 

Pełną listę porównawczych funkcji między ASP.NET SignalR i ASP.NET Core SignalR można znaleźć w [dok.](https://docs.microsoft.com/aspnet/core/signalr/version-differences?view=aspnetcore-2.2)

W tym przewodniku Szybki start dowiesz się, jak rozpocząć korzystanie z usługi ASP.NET i usługi Azure SignalR dla [podobnej aplikacji Chat Room.](./signalr-quickstart-dotnet-core.md)


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
## <a name="prerequisites"></a>Wymagania wstępne

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
* [.NET 4.6.1](https://www.microsoft.com/net/download/windows)
* [ASP.NET SignalR 2.4.1](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/)

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do [witryny Azure portal](https://portal.azure.com/) za pomocą konta platformy Azure.

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

*Tryb bezserwerowy* nie jest obsługiwany w przypadku aplikacji ASP.NET SignalR. Zawsze używaj *domyślnego* lub *klasycznego* dla wystąpienia usługi Azure SignalR.

Można również utworzyć zasoby platformy Azure używane w tym przewodniku Szybki start za pomocą [skryptu Utwórz usługę SignalR](scripts/signalr-cli-create-service.md).

## <a name="clone-the-sample-application"></a>Klonowanie przykładowej aplikacji

W czasie, gdy usługa jest wdrażana, przejdźmy do pracy z kodem. Sklonuj [przykładową aplikację z witryny GitHub](https://github.com/aspnet/AzureSignalR-samples/tree/master/aspnet-samples/ChatRoom), ustaw parametry połączenia usługi SignalR Service i uruchom aplikację lokalnie.

1. Otwórz okno terminala usługi Git. Przejdź do folderu, w którym chcesz sklonować przykładowy projekt.

1. Uruchom następujące polecenie w celu sklonowania przykładowego repozytorium. To polecenie tworzy kopię aplikacji przykładowej na komputerze.

    ```bash
    git clone https://github.com/aspnet/AzureSignalR-samples.git
    ```

## <a name="configure-and-run-chat-room-web-app"></a>Konfigurowanie i uruchamianie aplikacji sieci Web Pokój rozmów

1. Uruchom program Visual Studio i otwórz rozwiązanie w folderze *aspnet-samples/ChatRoom/* sklonowanego repozytorium.

1. W przeglądarce, w której jest otwierany portal Azure, znajdź i wybierz utworzone wystąpienie.

1. Wybierz pozycję **Klucze**, aby wyświetlić parametry połączenia dla wystąpienia usługi SignalR Service.

1. Zaznacz i skopiuj podstawowe parametry połączenia.

1. Teraz ustaw ciąg połączenia w pliku web.config.

    ```xml
    <configuration>
    <connectionStrings>
        <add name="Azure:SignalR:ConnectionString" connectionString="<Replace By Your Connection String>"/>
    </connectionStrings>
    ...
    </configuration>
    ```

1. W *Startup.cs*, zamiast wywoływania, `MapSignalR()`musisz `MapAzureSignalR({your_applicationName})` wywołać i przekazać w ciągu połączenia, aby aplikacja połączyć się z usługą zamiast hostingu SignalR przez siebie. Zastąp `{YourApplicationName}` nazwę aplikacji. Ta nazwa jest unikatową nazwą odróżnić tę aplikację od innych aplikacji. Jako wartość `this.GetType().FullName` można użyć.

    ```cs
    public void Configuration(IAppBuilder app)
    {
        // Any connection or hub wire up and configuration should go here
        app.MapAzureSignalR(this.GetType().FullName);
    }
    ```

    Należy również odwołać się do sdk usługi przed użyciem tych interfejsów API. Otwieranie **narzędzi | Menedżer pakietów NuGet | Konsola Menedżera pakietów** i uruchom polecenie:

    ```powershell
    Install-Package Microsoft.Azure.SignalR.AspNet
    ```

    Poza tymi zmianami wszystko inne pozostaje takie samo, nadal można użyć interfejsu koncentratora, który już znasz, aby napisać logikę biznesową.

    > [!NOTE]
    > W implementacji punkt `/signalr/negotiate` końcowy jest narażony na negocjacje przez zestaw SDK usługi Azure SignalR. Zwróci specjalną odpowiedź negocjacji, gdy klienci próbują połączyć i przekierować klientów do punktu końcowego usługi zdefiniowanego w ciągu połączenia.

1. Naciśnij **klawisz F5,** aby uruchomić projekt w trybie debugowania. Możesz zobaczyć, że aplikacja działa lokalnie. Zamiast hostowania środowiska uruchomieniowego SignalR przez samą aplikację, teraz łączy się z usługą Azure SignalR.

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]



> [!IMPORTANT]
> Usunięcie grupy zasobów jest nieodwracalne i grupa zasobów oraz wszystkie zawarte w niej zasoby zostaną trwale usunięte. Uważaj, aby nie usunąć przypadkowo niewłaściwych zasobów lub grupy zasobów. Jeśli zasoby do hostowania tego przykładu zostały utworzone wewnątrz istniejącej grupy zasobów zawierającej zasoby, które chcesz zachować, możesz usunąć każdy zasób oddzielnie z odpowiadającego mu bloku zamiast usuwać całą grupę zasobów.
> 
> 

Zaloguj się do witryny [Azure Portal](https://portal.azure.com) i kliknij pozycję **Grupy zasobów**.

W polu tekstowym **Filtruj według nazwy...** wpisz nazwę grupy zasobów. W instrukcjach dla tego przewodnika Szybki start używana była grupa zasobów o nazwie *SignalRTestResources*. Dla grupy zasobów na liście wyników kliknij pozycję **...**, a następnie kliknij pozycję **Usuń grupę zasobów**.

   
![Usuń](./media/signalr-quickstart-dotnet-core/signalr-delete-resource-group.png)

Po krótkim czasie grupa zasobów i wszystkie zawarte w niej zasoby zostaną usunięte.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono nowy zasób usługi Azure SignalR i użyto go z ASP.NET aplikacji sieci web. Następnie dowiedz się, jak tworzyć aplikacje w czasie rzeczywistym przy użyciu usługi Azure SignalR z ASP.NET Core.

> [!div class="nextstepaction"]
> [Usługa Azure SignalR z ASP.NET Core](./signalr-quickstart-dotnet-core.md)
