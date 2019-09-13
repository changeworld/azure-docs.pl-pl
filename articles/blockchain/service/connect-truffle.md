---
title: Łączenie przy użyciu rozwiązania Truffle
description: Nawiązywanie połączenia z siecią usługi Azure łańcucha bloków za pomocą Truffle
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 08/29/2019
ms.topic: quickstart
ms.service: azure-blockchain
ms.reviewer: jackyhsu
manager: femila
ms.openlocfilehash: ea64e3fe4789ac0558463ded81c8179db83469d5
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2019
ms.locfileid: "70932594"
---
# <a name="quickstart-use-truffle-to-connect-to-a-transaction-node"></a>Szybki start: Użycie programu Truffle do nawiązania połączenia z węzłem transakcji

Truffle to środowisko deweloperskie łańcucha bloków, którego można użyć do nawiązania połączenia z węzłem transakcji usługi Azure łańcucha bloków.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Pełny [Przewodnik Szybki Start: Utwórz składową łańcucha bloków przy użyciu Azure Portal](create-member.md) lub [szybkiego startu: Tworzenie członka usługi Azure łańcucha bloków Service łańcucha bloków przy użyciu interfejsu wiersza polecenia platformy Azure](create-member-cli.md)
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

### <a name="transaction-node-endpoint-addresses"></a>Adresy punktów końcowych węzła transakcji

1. W Azure Portal przejdź do domyślnego węzła transakcji i wybierz pozycję **węzły transakcji > parametry połączenia**.
1. Skopiuj i Zapisz adres URL punktu końcowego z **protokołu HTTPS (dostęp do klucza 1)** . W dalszej części tego samouczka potrzebujesz adresów punktu końcowego dla pliku konfiguracji kontraktu inteligentnego.

    ![Adres punktu końcowego transakcji](./media/connect-truffle/endpoint.png)

### <a name="edit-configuration-file"></a>Edytuj plik konfiguracji

Następnie należy zaktualizować plik konfiguracji Truffle za pomocą punktu końcowego węzła transakcji.

1. W folderze projektu **truffledemo** Otwórz plik `truffle-config.js` konfiguracji Truffle w edytorze.
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
1. Zamknij konsolę Truffle.

    ```bash
    .exit
    ```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono projekt Truffle w celu nawiązania połączenia z domyślnym węzłem transakcji usługi Azure łańcucha bloków Service.

Skorzystaj z następnego samouczka, aby użyć zestawu Azure łańcucha bloków Development Kit dla Ethereum i Truffle do tworzenia, kompilowania, wdrażania i wykonywania funkcji kontraktu inteligentnego za pośrednictwem transakcji.

> [!div class="nextstepaction"]
> [Używanie Visual Studio Code do tworzenia, kompilowania i wdrażania kontraktów inteligentnych](send-transaction.md)
