---
title: Nawiązywanie połączenia z usługą Azure łańcucha bloków przy użyciu programu Truffle
description: Nawiązywanie połączenia z siecią usługi Azure łańcucha bloków za pomocą Truffle
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 11/20/2019
ms.topic: quickstart
ms.service: azure-blockchain
ms.reviewer: janders
manager: femila
ms.openlocfilehash: f5d752c99331d454e7f9f98c06b9ba0209de8cc7
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74285363"
---
# <a name="quickstart-use-truffle-to-connect-to-azure-blockchain-service"></a>Szybki Start: używanie Truffle do nawiązywania połączenia z usługą Azure łańcucha bloków

W tym przewodniku szybki start użyjesz połączenia Truffle z węzłem transakcji usługi Azure łańcucha bloków. Następnie należy użyć konsoli interaktywnej Truffle do wywołania metod **web3** w celu interakcji z siecią łańcucha bloków.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Kończenie [szybkiego startu: Tworzenie elementu członkowskiego łańcucha bloków przy użyciu Azure Portal](create-member.md) lub [szybkiego startu: Tworzenie elementu członkowskiego łańcucha bloków usługi Azure łańcucha bloków przy użyciu interfejsu wiersza polecenia platformy Azure](create-member-cli.md)
* Zainstaluj [Truffle](https://github.com/trufflesuite/truffle). Truffle wymaga zainstalowania kilku narzędzi, w tym [Node. js](https://nodejs.org), [git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
* Zainstaluj środowisko [Python 2.7.15](https://www.python.org/downloads/release/python-2715/). Środowisko Python jest niezbędne dla Web3.

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

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Przejdź do elementu członkowskiego usługi Azure łańcucha bloków. Wybierz pozycję **węzły transakcji** i domyślne łącze węzła transakcji.

    ![Wybierz domyślny węzeł transakcji](./media/connect-truffle/transaction-nodes.png)

1. Wybierz **Parametry połączenia**.
1. Skopiuj parametry połączenia z **protokołu HTTPS (dostęp do klucza 1)** . Potrzebujesz ciągu dla następnej sekcji.

    ![Parametry połączenia](./media/connect-truffle/connection-string.png)

### <a name="edit-configuration-file"></a>Edytuj plik konfiguracji

Następnie należy zaktualizować plik konfiguracji Truffle za pomocą punktu końcowego węzła transakcji.

1. W folderze projektu **truffledemo** Otwórz plik konfiguracji Truffle `truffle-config.js` w edytorze.
1. Zastąp zawartość pliku następującymi informacjami o konfiguracji. Dodaj zmienną zawierającą adres punktu końcowego. Zamień nawias ostry na wartości zebrane z poprzedniej sekcji.

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

1. Użyj konsoli Truffle, aby nawiązać połączenie z domyślnym węzłem transakcji. W wierszu polecenia lub powłoce Uruchom następujące polecenie:

    ``` bash
    truffle console --network defaultnode
    ```

    Truffle nawiązuje połączenie z domyślnym węzłem transakcji i udostępnia konsolę interaktywną.

    Możesz wywoływać metody w obiekcie **web3** , aby móc korzystać z sieci łańcucha bloków.

1. Wywołaj metodę **getBlockNumber** , aby zwrócić bieżący numer bloku.

    ```bash
    web3.eth.getBlockNumber();
    ```

    Przykładowe dane wyjściowe:

    ```bash
    truffle(defaultnode)> web3.eth.getBlockNumber();
    18567
    ```
1. Zamknij konsolę Truffle.

    ```bash
    .exit
    ```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start użyto Truffle do nawiązania połączenia z domyślnym węzłem transakcji usługi Azure łańcucha bloków i użycia konsoli interaktywnej do zwrócenia bieżącego numeru bloku łańcucha bloków.

Wypróbuj następny samouczek, aby użyć usługi Azure łańcucha bloków Development Kit dla Ethereum do tworzenia, kompilowania, wdrażania i wykonywania funkcji kontraktu inteligentnego za pośrednictwem transakcji.

> [!div class="nextstepaction"]
> [Używanie Visual Studio Code do tworzenia, kompilowania i wdrażania kontraktów inteligentnych](send-transaction.md)
