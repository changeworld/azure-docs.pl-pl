---
title: Nawiązywanie połączenia z usługą Azure łańcucha bloków przy użyciu programu Geth
description: Nawiązywanie połączenia z siecią usługi Azure łańcucha bloków za pomocą Geth
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: quickstart
ms.service: azure-blockchain
ms.reviewer: jackyhsu
manager: femila
ms.openlocfilehash: a26899e291c4f44d3c9d91032b2ee191ba03133a
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2019
ms.locfileid: "70931773"
---
# <a name="quickstart-use-geth-to-connect-to-a-transaction-node"></a>Szybki start: Użycie programu Geth do nawiązania połączenia z węzłem transakcji

Geth to klient programu go Ethereum, którego można użyć do dołączenia do wystąpienia Geth w węźle transakcji usługi Azure łańcucha bloków.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Zainstaluj [Geth](https://github.com/ethereum/go-ethereum/wiki/geth)
* Pełny [Przewodnik Szybki Start: Utwórz składową łańcucha bloków przy użyciu Azure Portal](create-member.md) lub [szybkiego startu: Tworzenie członka usługi Azure łańcucha bloków Service łańcucha bloków przy użyciu interfejsu wiersza polecenia platformy Azure](create-member-cli.md)

## <a name="get-the-geth-connection-string"></a>Pobierz parametry połączenia usługi Geth

Parametry połączenia Geth można znaleźć w Azure Portal.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Przejdź do elementu członkowskiego usługi Azure łańcucha bloków. Wybierz pozycję **węzły transakcji** i domyślne łącze węzła transakcji.

    ![Wybierz domyślny węzeł transakcji](./media/connect-geth/transaction-nodes.png)

1. Wybierz **Parametry połączenia**.
1. Skopiuj parametry połączenia z **protokołu HTTPS (dostęp do klucza 1)** . Polecenie jest potrzebne do następnej sekcji.

    ![Parametry połączenia](./media/connect-geth/connection-string.png)

## <a name="connect-to-geth"></a>Nawiązywanie połączenia z usługą Geth

1. Otwórz wiersz polecenia lub powłokę.
1. Użyj podpolecenia Geth Attach, aby dołączyć do uruchomionego wystąpienia Geth w węźle Transaction. Wklej parametry połączenia jako argument dla podpolecenia Attach. Na przykład

    ```
    geth attach <connection string>
    ```

1. Po nawiązaniu połączenia z konsolą Ethereum węzła transakcji można wywołać interfejs API usługi web3 JavaScript Dapp lub interfejs API administratora.

    Na przykład użyj poniższego interfejsu API, aby dowiedzieć się chainId.

    ```bash
    admin.nodeInfo.protocols.istanbul.config.chainId
    ```

    W tym przykładzie chainId jest 297.

    ![Opcja usługi Azure łańcucha bloków](./media/connect-geth/geth-attach.png)

1. Aby rozłączyć się z konsolą programu, wpisz `exit`polecenie.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start użyto klienta Geth do dołączenia do wystąpienia Geth w węźle transakcji usługi Azure łańcucha bloków. Skorzystaj z następnego samouczka, aby użyć zestawu Azure łańcucha bloków Development Kit dla Ethereum i Truffle do tworzenia, kompilowania, wdrażania i wykonywania funkcji kontraktu inteligentnego za pośrednictwem transakcji.

> [!div class="nextstepaction"]
> [Używanie Visual Studio Code do tworzenia, kompilowania i wdrażania kontraktów inteligentnych](send-transaction.md)