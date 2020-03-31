---
title: Dołącz do usługi Azure Blockchain Service za pomocą funkcji Geth
description: Dołącz do wystąpienia Geth w węźle transakcji usługi Azure Blockchain Service
ms.date: 11/20/2019
ms.topic: quickstart
ms.reviewer: janders
ms.openlocfilehash: 9da78eac1dc429bcc0ad52bb9cb2f1fb743a90d4
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "74455833"
---
# <a name="quickstart-use-geth-to-attach-to-an-azure-blockchain-service-transaction-node"></a>Szybki start: Dołączanie do węzła transakcji usługi Azure Blockchain Service za pomocą funkcji Geth

W tym przewodniku Szybki start można dołączyć do wystąpienia Geth w węźle transakcji usługi Azure Blockchain Service. Po podłączeniu używasz konsoli Geth JavaScript, aby wywołać interfejs API Dapp JavaScript w języku Web3.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Zainstaluj [Geth](https://github.com/ethereum/go-ethereum/wiki/geth)
* Ukończ [szybki start: Tworzenie członka łańcucha bloków przy użyciu portalu Azure](create-member.md) lub [przewodnika Szybki start: tworzenie członka łańcucha bloków usługi Azure Blockchain przy użyciu interfejsu wiersza polecenia platformy Azure](create-member-cli.md)

## <a name="get-geth-connection-string"></a>Pobierz ciąg połączenia Geth

Możesz uzyskać ciąg połączenia Geth dla węzła transakcji usługi Azure Blockchain w portalu Azure.

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
1. Przejdź do swojego członka usługi Azure Blockchain Service. Wybierz węzły transakcji i **domyślne łącze** węzła transakcji.

    ![Wybierz domyślny węzeł transakcji](./media/connect-geth/transaction-nodes.png)

1. Wybierz **pozycję Parametry połączenia**.
1. Skopiuj ciąg połączenia z **protokołu HTTPS (klucz programu Access 1)**. Potrzebujesz ciągu dla następnej sekcji.

    ![Parametry połączenia](./media/connect-geth/connection-string.png)

## <a name="connect-to-geth"></a>Połącz się z Gethem

1. Otwórz wiersz polecenia lub powłokę.
1. Użyj podpokazyju Geth attach, aby dołączyć do uruchomionego wystąpienia Geth w węźle transakcji. Wklej ciąg połączenia jako argument podpokazyju dołączania. Przykład:

    ``` bash
    geth attach <connection string>
    ```

1. Po połączeniu z konsolą Ethereum węzła transakcji można wywołać interfejs API Dapp JavaScript w języku web3 lub interfejs API administratora.

    Na przykład użyj następującego interfejsu API, aby dowiedzieć się chainId.

    ``` bash
    admin.nodeInfo.protocols.istanbul.config.chainId
    ```

    W tym przykładzie chainId jest 661.

    ![Opcja usługi Azure Blockchain](./media/connect-geth/geth-attach.png)

1. Aby odłączyć `exit`się od konsoli, wpisz .

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start użyto klienta Geth do dołączenia do wystąpienia Geth w węźle transakcji usługi Azure Blockchain Service. Wypróbuj następny samouczek, aby użyć zestawu Azure Blockchain Development Kit for Ethereum do tworzenia, tworzenia, wdrażania i wykonywania funkcji inteligentnego kontraktu za pośrednictwem transakcji.

> [!div class="nextstepaction"]
> [Tworzenie, tworzenie i wdrażanie inteligentnych kontraktów w usłudze Azure Blockchain Service](send-transaction.md)
