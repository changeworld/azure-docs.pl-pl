---
title: Nawiązywanie połączenia przy użyciu Truffle
description: Łączenie z siecią Azure Blockchain Service przy użyciu Truffle
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: quickstart
ms.service: azure-blockchain
ms.reviewer: jackyhsu
manager: femila
ms.openlocfilehash: 037f37d6a8e1c41579403dbf7c9dd265efbb5d10
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/02/2019
ms.locfileid: "65026956"
---
# <a name="quickstart-use-truffle-to-connect-to-a-an-azure-blockchain-service-network"></a>Szybki start: Użyj Truffle, aby nawiązać połączenie z siecią Azure Blockchain Service

Truffle to środowisko programistyczne łańcucha bloków, używanego do łączenia się z węzłem Azure Blockchain Service.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* [Tworzenie elementu członkowskiego temat aplikacji Azure Blockchain](create-member.md)
* Truffle wymaga zainstalowania narzędzi kilka tym [Node.js](https://nodejs.org), [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git), i [Truffle](https://github.com/trufflesuite/truffle).

    Aby szybko skonfigurować w systemie Windows 10, należy zainstalować [Ubuntu on Windows](https://www.microsoft.com/p/ubuntu/9nblggh4msv6) terminala powłoki Bash w systemie Unix Zainstaluj [Truffle](https://github.com/trufflesuite/truffle). Ubuntu on Windows dystrybucji obejmuje środowisko Node.js i usługi Git.

## <a name="create-truffle-project"></a>Utwórz projekt Truffle

1. Otwórz terminal powłoki Bash.
1. Zmień katalog, do której chcesz utworzyć Truffle katalogu projektu. Na przykład:

    ``` bash
    cd /mnt/c
    ```

1. Utwórz katalog dla projektu i zmień ścieżki do nowego katalogu. Na przykład:

    ``` bash
    mkdir truffledemo
    cd truffledemo
    ```

1. Zainstaluj sieci Web w 3 interfejsu API języka JavaScript Ethereum w folderze projektu. Obecnie wersja sieci Web 3 wersji 1.0.0-beta.37 jest wymagana.

    ``` bash
    npm install web3@1.0.0-beta.37
    ```

    Ostrzeżenia npm może pojawić się podczas instalacji.

1. Inicjowanie projektu Truffle.

    ``` bash
    truffle init
    ```

1. Uruchom konsolę opracowywanie interakcyjne Truffle firmy.

    ``` bash
    truffle develop
    ```

    Truffle tworzy łańcuch bloków rozwoju lokalnego i dostarcza interakcyjną konsolę.

## <a name="connect-to-transaction-node"></a>Łączenie z węzłem transakcji

Użyjemy sieci Web 3 Połącz się z węzłem transakcji. Parametry połączenia w sieci Web 3 może pobrać z witryny Azure portal.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Przejdź do elementu członkowskiego usługi Azure Service łańcucha bloków. Wybierz **węzłów transakcji** i domyślne łącze węzła transakcji.

    ![Wybierz węzeł transakcji domyślne](./media/connect-truffle/transaction-nodes.png)

1. Wybierz **przykładowego kodu > sieci Web 3**.
1. Skopiuj kod JavaScript z **HTTPS (klucz dostępu 1)**. Kod jest wymagany dla firmy Truffle opracowywanie interakcyjne konsoli.

    ![Kod sieci Web 3](./media/connect-truffle/web3-code.png)

1. Wklej kod JavaScript z poprzedniego kroku do konsoli opracowywanie interakcyjne Truffle. Kod tworzy obiekt sieci Web 3, który jest połączony Twój węzeł transakcji usługi Azure Service łańcucha bloków.

    Przykładowe dane wyjściowe:

    ```bash
    truffle(develop)> var Web3 = require("Web3");
    truffle(develop)> var provider = new Web3.providers.HttpProvider("https://myblockchainmember.blockchain.azure.com:3200/hy5FMu5TaPR0Zg8GxiPwned");
    truffle(develop)> var web3 = new Web3(provider);
    truffle(develop)>
     ```

    Można wywoływać metody dla **sieci Web 3** na interakcję z Twój węzeł transakcji.

1. Wywołaj **getBlockNumber** metodę, aby zwrócić bieżący numer bloku.

    ```bash
    web3.eth.getBlockNumber();
    ```

    Przykładowe dane wyjściowe:

    ```bash
    truffle(develop)> web3.eth.getBlockNumber();
    18567
    ```
1. Zakończ działanie konsoli deweloperskiej Truffle.

    ```bash
    .exit
    ```

## <a name="next-steps"></a>Kolejne kroki

W tym przewodniku Szybki Start utworzono projekt Truffle, połącz się z usługi Azure Blockchain usługi domyślne transakcji węzłem.

Spróbuj następnego samouczka, aby wysyłać za pomocą Truffle do transakcji sieci konsorcjum łańcucha bloków.

> [!div class="nextstepaction"]
> [Wyślij transakcji](send-transaction.md)