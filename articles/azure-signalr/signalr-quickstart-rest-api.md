---
title: Przewodnik Szybki start — interfejs API REST usługi Azure SignalR Service | Microsoft Docs
description: Przewodnik Szybki start dotyczący korzystania z interfejsu API REST usługi Azure SignalR Service.
services: signalr
documentationcenter: ''
author: sffamily
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: signalr
ms.devlang: dotnet
ms.topic: quickstart
ms.tgt_pltfrm: ASP.NET
ms.workload: tbd
ms.date: 06/13/2018
ms.author: zhshang
ms.openlocfilehash: 93c1198ecfba6db809228ed6dcd99c705f53926c
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46972763"
---
# <a name="quickstart-broadcast-real-time-messages-from-console-app"></a>Szybki start: tworzenie przekazów komunikatów w czasie rzeczywistym z poziomu aplikacji konsolowej

Usługa Azure SignalR Service udostępnia [interfejs API REST](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md) umożliwiający obsługę scenariuszy dotyczących komunikacji między klientem a serwerem, takich jak tworzenie przekazów. Możesz wybrać dowolny język programowania, który umożliwia wywoływanie interfejsu API REST. Możesz zamieszczać komunikaty do wszystkich połączonych klientów, konkretnych klientów według ich nazwy lub do grupy klientów.

W tym przewodniku Szybki start dowiesz się, jak wysyłać komunikaty z aplikacji wiesza polecenia do aplikacji połączonych klientów w języku C#.

## <a name="prerequisites"></a>Wymagania wstępne

Czynności przedstawione w tym przewodniku Szybki start można wykonywać w systemie macOS, Windows lub Linux.
* [Zestaw SDK dla platformy .NET Core](https://www.microsoft.com/net/download/core)
* Wybrany edytor tekstu lub edytor kodu.


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem <https://portal.azure.com/> przy użyciu danych konta Azure.


[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

## <a name="clone-the-sample-application"></a>Klonowanie przykładowej aplikacji

W czasie, gdy usługa jest wdrażana, przejdźmy do przygotowania kodu. Sklonuj [przykładową aplikację z witryny GitHub](https://github.com/aspnet/AzureSignalR-samples.git), ustaw parametry połączenia usługi SignalR Service i uruchom aplikację lokalnie.

1. Otwórz okno terminala usługi Git. Przejdź do folderu, w którym chcesz sklonować przykładowy projekt.

1. Uruchom następujące polecenie w celu sklonowania przykładowego repozytorium. To polecenie tworzy kopię przykładowej aplikacji na komputerze.

    ```bash
    git clone https://github.com/aspnet/AzureSignalR-samples.git
    ```

## <a name="build-and-run-the-sample"></a>Kompilowanie i uruchamianie przykładu

W tym przykładzie zastosowano aplikację konsolową pokazującą użycie usługi Azure SignalR Service. Oferuje ona dwa tryby:

- Tryb serwera: wykorzystuje proste polecenia, aby wywołać interfejs API REST usługi Azure SignalR Service.
- Tryb klienta: nawiązuje połączenie z usługą Azure SignalR Service i otrzymuje komunikaty z serwera.

Możesz również dowiedzieć się, w jaki sposób wygenerować tokeny dostępu służące do uwierzytelniania w usłudze Azure SignalR Service.

### <a name="build-the-executable-file"></a>Tworzenie pliku wykonywalnego

Jako przykładu użyjemy systemu macOS osx.10.13-x64. Możesz znaleźć [informacje](https://docs.microsoft.com/dotnet/core/rid-catalog), w jaki sposób utworzyć odpowiednią aplikację na innych platformach.
```bash
cd AzureSignalR-samples/samples/Serverless/

dotnet publish -c Release -r osx.10.13-x64
```

### <a name="start-a-client"></a>Uruchamianie klienta

```bash
cd bin/Release/netcoreapp2.1/osx.10.13-x64/

Serverless client <ClientName> -c "<ConnectionString>" -h <HubName>
```

### <a name="start-a-server"></a>Uruchamianie serwera

```bash
cd bin/Release/netcoreapp2.1/osx.10.13-x64/

Serverless server -c "<ConnectionString>" -h <HubName>
```

## <a name="run-the-sample-without-publishing"></a>Uruchamianie aplikacji przykładowej bez publikowania

Możesz również uruchomić poniższe polecenie, aby uruchomić serwer lub klienta.

```bash
# Start a server
dotnet run -- server -c "<ConnectionString>" -h <HubName>

# Start a client
dotnet run -- client <ClientName> -c "<ConnectionString>" -h <HubName>
```

### <a name="use-user-secrets-to-specify-connection-string"></a>Korzystanie z wpisów tajnych użytkowników w celu określenia parametrów połączenia

Możesz uruchomić polecenie `dotnet user-secrets set Azure:SignalR:ConnectionString "<ConnectionString>"` w katalogu głównym przykładu. Wtedy opcja `-c "<ConnectionString>"` nie jest już potrzebna.

## <a name="usage"></a>Sposób użycia

Po uruchomieniu serwera użyj tego polecenia, aby wysłać komunikat.

```
send user <User Id>
send users <User List>
send group <Group Name>
send groups <Group List>
broadcast
```

Możesz uruchomić wielu klientów o wielu nazwach.


[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]
