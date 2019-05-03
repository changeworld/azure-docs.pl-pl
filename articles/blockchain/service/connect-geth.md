---
title: Użyj Geth, aby nawiązać połączenie z usługą Azure do łańcucha bloków
description: Łączenie z siecią Azure Blockchain Service przy użyciu Geth
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: quickstart
ms.service: azure-blockchain
ms.reviewer: jackyhsu
manager: femila
ms.openlocfilehash: 0716a9326a54ae31d4f355fe5f4c88488339b390
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/02/2019
ms.locfileid: "65026921"
---
# <a name="quickstart-use-geth-to-connect-to-a-transaction-node"></a>Szybki start: Umożliwia łączenie z węzłem transakcji Geth

Geth to klient Przejdź Ethereum służących do dołączenia do wystąpienia Geth na węźle transakcji usługi Azure Service łańcucha bloków.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Zainstaluj [Geth](https://github.com/ethereum/go-ethereum/wiki/geth)
* [Tworzenie elementu członkowskiego temat aplikacji Azure Blockchain](create-member.md)

## <a name="get-the-geth-connection-string"></a>Pobieranie parametrów połączenia Geth

Parametry połączenia Geth można znaleźć w witrynie Azure portal.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Przejdź do elementu członkowskiego usługi Azure Service łańcucha bloków. Wybierz **węzłów transakcji** i domyślne łącze węzła transakcji.

    ![Wybierz węzeł transakcji domyślne](./media/connect-geth/transaction-nodes.png)

1. Wybierz **parametry połączenia**.
1. Skopiuj parametry połączenia z **HTTPS (klucz dostępu 1)**. Polecenie jest konieczne do następnej sekcji.

    ![Parametry połączenia](./media/connect-geth/connection-string.png)

## <a name="connect-to-geth"></a>Nawiązać połączenie z Geth

1. Otwórz wiersz polecenia lub powłokę.
1. Użyj Geth dołączyć podpolecenia można dołączyć do uruchomionego wystąpienia Geth w węźle transakcji. Wklej parametry połączenia jako argument polecenia attach. Na przykład:

    ```
    geth attach <connection string>
    ```

1. Po połączeniu się console Ethereum węzła transakcji, można wywołać interfejsu API sieci Web 3 języka JavaScript Dapp lub interfejs API administratora.

    Na przykład użyć następujący interfejs API, aby dowiedzieć się, chainId.

    ```bash
    admin.nodeInfo.protocols.istanbul.config.chainId
    ```

    W tym przykładzie chainId jest 297.

    ![Opcja Azure Blockchain usługi](./media/connect-geth/geth-attach.png)

1. Aby odłączyć z konsoli, wpisz `exit`.

## <a name="next-steps"></a>Kolejne kroki

W tym przewodniku Szybki Start używasz klienta Geth można dołączyć do wystąpienia Geth na węźle transakcji usługi Azure Service łańcucha bloków. Spróbuj następnego samouczka, aby wdrożyć i wysłać transakcji za pomocą Truffle.

> [!div class="nextstepaction"]
> [Wyślij transakcji](send-transaction.md)