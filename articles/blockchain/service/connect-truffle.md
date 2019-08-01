---
title: Łączenie przy użyciu rozwiązania Truffle
description: Nawiązywanie połączenia z siecią usługi Azure łańcucha bloków za pomocą Truffle
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/29/2019
ms.topic: quickstart
ms.service: azure-blockchain
ms.reviewer: jackyhsu
manager: femila
ms.openlocfilehash: 9154bc749f7db337de67f501d5e5049dfd466156
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/31/2019
ms.locfileid: "68698470"
---
# <a name="quickstart-use-truffle-to-connect-to-an-azure-blockchain-service-network"></a>Szybki start: Użyj Truffle, aby nawiązać połączenie z siecią usługi Azure łańcucha bloków Service

Truffle to środowisko deweloperskie łańcucha bloków, którego można użyć do nawiązania połączenia z węzłem usługi Azure łańcucha bloków.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* [Tworzenie elementu członkowskiego usługi Azure łańcucha bloków](create-member.md)
* Zainstaluj [Truffle](https://github.com/trufflesuite/truffle). Truffle wymaga zainstalowania kilku narzędzi, w tym [Node. js](https://nodejs.org), [git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
* Zainstaluj środowisko [Python 2.7.15](https://www.python.org/downloads/release/python-2715/). Środowisko Python jest niezbędne dla Web3.
* Zainstaluj narzędzie [Visual Studio Code](https://code.visualstudio.com/download).
* Zainstaluj [rozszerzenie Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=JuanBlanco.solidity).

## <a name="create-truffle-project"></a>Utwórz projekt Truffle

1. Otwórz wiersz polecenia środowiska Node. js lub powłokę.
1. Zmień katalog na miejsce, w którym chcesz utworzyć katalog projektu Truffle.
1. Utwórz katalog dla projektu i zmień ścieżkę do nowego katalogu. Na przykład

    ``` bash
    mkdir truffledemo
    cd truffledemo
    ```

1. Zainicjuj projekt Truffle.

    ``` bash
    truffle init
    ```

1. Zainstaluj interfejs API Ethereum JavaScript web3 w folderze projektu. Obecnie jest wymagana wersja web3 wersja 1.0.0-beta. 37.

    ``` bash
    npm install web3@1.0.0-beta.37
    ```

    Podczas instalacji mogą pojawić się ostrzeżenia npm.
    
## <a name="configure-truffle-project"></a>Konfigurowanie projektu Truffle

Aby skonfigurować projekt Truffle, potrzebne są pewne informacje o węźle transakcji z Azure Portal.

### <a name="transaction-node-endpoint-addresses"></a>Adresy punktów końcowych węzła transakcji

1. W Azure Portal przejdź do każdego węzła transakcji i wybierz pozycję **węzły transakcji > parametry połączenia**.
1. Skopiuj i Zapisz adres URL punktu końcowego z **protokołu HTTPS (dostęp do klucza 1)** dla każdego węzła transakcji. W dalszej części tego samouczka potrzebujesz adresów punktu końcowego dla pliku konfiguracji kontraktu inteligentnego.

    ![Adres punktu końcowego transakcji](./media/send-transaction/endpoint.png)

### <a name="edit-configuration-file"></a>Edytuj plik konfiguracji

1. Uruchom Visual Studio Code i Otwórz folder katalog projektu Truffle, korzystając z menu **plik > Otwórz folder** .
1. Otwórz plik `truffle-config.js`konfiguracji Truffle.
1. Zastąp zawartość pliku następującymi informacjami o konfiguracji. Dodaj zmienną zawierającą adres punktu końcowego. Zamień nawias ostry na wartości zebrane z poprzednich sekcji.

    ``` javascript
    var defaultnode = "<default transaction node connection string>";   
    var Web3 = require("web3");
    
    module.exports = {
      networks: {
        defaultnode: {
          provider: new Web3.providers.HttpProvider(defaultnode),
          network_id: "*"
        }
      }
    }
    ```

1. Zapisz zmiany w `truffle-config.js`.

## <a name="connect-to-transaction-node"></a>Łączenie z węzłem transakcji

Użyj *Web3* , aby nawiązać połączenie z węzłem transakcji.

1. Użyj konsoli Truffle, aby nawiązać połączenie z domyślnym węzłem transakcji.

    ``` bash
    truffle console --network defaultnode
    ```

    Truffle nawiązuje połączenie z domyślnym węzłem transakcji i udostępnia konsolę interaktywną.

    Można wywołać metody obiektu **web3** , aby móc korzystać z węzła transakcji.

1. Wywołaj metodę **getBlockNumber** , aby zwrócić bieżący numer bloku.

    ```bash
    web3.eth.getBlockNumber();
    ```

    Przykładowe dane wyjściowe:

    ```bash
    truffle(defaultnode)> web3.eth.getBlockNumber();
    18567
    ```
1. Zamknij konsolę programowanie Truffle.

    ```bash
    .exit
    ```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono projekt Truffle w celu nawiązania połączenia z domyślnym węzłem transakcji usługi Azure łańcucha bloków Service.

Wypróbuj następny samouczek, aby użyć Truffle do wysłania transakcji do sieci łańcucha bloków z konsorcjum.

> [!div class="nextstepaction"]
> [Wyślij transakcję](send-transaction.md)
