---
title: Użyj Geth, aby dołączyć do usługi Azure łańcucha bloków Service
description: Dołącz do wystąpienia Geth w węźle transakcji usługi Azure łańcucha bloków Service
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 11/20/2019
ms.topic: quickstart
ms.service: azure-blockchain
ms.reviewer: janders
manager: femila
ms.openlocfilehash: 1c285a7b5cc04aa330e9f68fdb82cfd099a19e03
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74285278"
---
# <a name="quickstart-use-geth-to-attach-to-an-azure-blockchain-service-transaction-node"></a>Szybki Start: używanie Geth do dołączania do węzła transakcji usługi Azure łańcucha bloków Service

W tym przewodniku szybki start użyjesz klienta Geth do dołączenia do wystąpienia Geth w węźle transakcji usługi Azure łańcucha bloków. Po dołączeniu należy użyć konsoli JavaScript Geth do wywołania web3 JavaScript Dapp API.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Zainstaluj [Geth](https://github.com/ethereum/go-ethereum/wiki/geth)
* Kończenie [szybkiego startu: Tworzenie elementu członkowskiego łańcucha bloków przy użyciu Azure Portal](create-member.md) lub [szybkiego startu: Tworzenie elementu członkowskiego łańcucha bloków usługi Azure łańcucha bloków przy użyciu interfejsu wiersza polecenia platformy Azure](create-member-cli.md)

## <a name="get-geth-connection-string"></a>Pobierz parametry połączenia usługi Geth

Parametry połączenia Geth można uzyskać dla węzła transakcji usługi Azure łańcucha bloków w Azure Portal.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Przejdź do elementu członkowskiego usługi Azure łańcucha bloków. Wybierz pozycję **węzły transakcji** i domyślne łącze węzła transakcji.

    ![Wybierz domyślny węzeł transakcji](./media/connect-geth/transaction-nodes.png)

1. Wybierz **Parametry połączenia**.
1. Skopiuj parametry połączenia z **protokołu HTTPS (dostęp do klucza 1)** . Potrzebujesz ciągu dla następnej sekcji.

    ![Parametry połączenia](./media/connect-geth/connection-string.png)

## <a name="connect-to-geth"></a>Nawiązywanie połączenia z usługą Geth

1. Otwórz wiersz polecenia lub powłokę.
1. Użyj podpolecenia Geth Attach, aby dołączyć do uruchomionego wystąpienia Geth w węźle Transaction. Wklej parametry połączenia jako argument dla podpolecenia Attach. Na przykład:

    ``` bash
    geth attach <connection string>
    ```

1. Po nawiązaniu połączenia z konsolą Ethereum węzła transakcji można wywołać interfejs API usługi web3 JavaScript Dapp lub interfejs API administratora.

    Na przykład użyj poniższego interfejsu API, aby dowiedzieć się chainId.

    ``` bash
    admin.nodeInfo.protocols.istanbul.config.chainId
    ```

    W tym przykładzie chainId jest 661.

    ![Opcja usługi Azure łańcucha bloków](./media/connect-geth/geth-attach.png)

1. Aby rozłączyć się z konsolą programu, wpisz `exit`.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start użyto klienta Geth do dołączenia do wystąpienia Geth w węźle transakcji usługi Azure łańcucha bloków. Wypróbuj następny samouczek, aby użyć usługi Azure łańcucha bloków Development Kit dla Ethereum do tworzenia, kompilowania, wdrażania i wykonywania funkcji kontraktu inteligentnego za pośrednictwem transakcji.

> [!div class="nextstepaction"]
> [Używanie Visual Studio Code do tworzenia, kompilowania i wdrażania kontraktów inteligentnych](send-transaction.md)
