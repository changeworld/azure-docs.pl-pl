---
title: Korzystanie z usługi Azure łańcucha bloków Development Kit dla Ethereum — Azure łańcucha bloków Service
description: Połącz się z siecią konsorcjum usługi Azure łańcucha bloków, korzystając z rozszerzenia Azure łańcucha bloków Development Kit w Visual Studio Code
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 10/14/2019
ms.topic: quickstart
ms.service: azure-blockchain
ms.reviewer: chrisseg
manager: femila
ms.openlocfilehash: 6364e887c699219d80974d592a8ff7c77cca2621
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72329312"
---
# <a name="quickstart-use-visual-studio-code-to-connect-to-an-azure-blockchain-service-consortium-network"></a>Szybki Start: używanie Visual Studio Code do nawiązywania połączenia z siecią Azure łańcucha bloków Service Consortium

W tym przewodniku szybki start zainstalujesz rozszerzenie Azure łańcucha bloków Development Kit dla Ethereum Visual Studio Code, aby dołączyć je do konsorcjum w usłudze Azure łańcucha bloków. Zestaw Azure łańcucha bloków Development Kit upraszcza tworzenie, łączenie, kompilowanie i wdrażanie inteligentnych kontraktów w księgach Ethereum. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Kończenie [szybkiego startu: Tworzenie elementu członkowskiego łańcucha bloków przy użyciu Azure Portal](create-member.md) lub [szybkiego startu: Tworzenie elementu członkowskiego łańcucha bloków usługi Azure łańcucha bloków przy użyciu interfejsu wiersza polecenia platformy Azure](create-member-cli.md)
* [Visual Studio Code](https://code.visualstudio.com/Download)
* [Rozszerzenie Azure łańcucha bloków Development Kit dla rozszerzenia Ethereum](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain)
* [Node.js](https://nodejs.org)
* [Usługa Git](https://git-scm.com)
* [Python](https://www.python.org/downloads/release/python-2715/). Dodaj język Python. exe do ścieżki. Język Python w ścieżce jest wymagany w przypadku usługi Azure łańcucha bloków Development Kit.
* [Truffle](https://www.trufflesuite.com/docs/truffle/getting-started/installation)
* [Interfejs wiersza polecenia ganache](https://github.com/trufflesuite/ganache-cli)

### <a name="verify-azure-blockchain-development-kit-environment"></a>Weryfikowanie środowiska zestawu Azure łańcucha bloków Development Kit

Zestaw Azure łańcucha bloków Development Kit sprawdza wymagania wstępne dotyczące środowiska deweloperskiego. Aby zweryfikować środowisko programistyczne:

Z palety poleceń VS Code wybierz pozycję **Azure łańcucha bloków: Pokaż stronę powitalną**.

Pakiet Azure łańcucha bloków Development Kit uruchamia skrypt walidacji, który trwa około minuty. Możesz wyświetlić dane wyjściowe, wybierając pozycję **terminal > nowym terminalu**. Na pasku menu terminalu wybierz kartę Output ( **dane wyjściowe** ) i pozycję **Azure łańcucha bloków** na liście rozwijanej. Pomyślne sprawdzenie poprawności wygląda tak, jak na poniższym obrazie:

![Prawidłowe środowisko deweloperskie](./media/connect-vscode/valid-environment.png)

 Jeśli brakuje wymaganego narzędzia, Nowa karta o nazwie **Azure łańcucha bloków Development Kit — wersja zapoznawcza** zawiera listę wymaganych aplikacji do zainstalowania i linków do pobrania narzędzi.

![Aplikacje wymagane przez zestaw dev Kit](./media/connect-vscode/required-apps.png)

Przed przejściem do przewodnika Szybki Start Zainstaluj wszystkie brakujące wymagania wstępne.

## <a name="connect-to-consortium-member"></a>Łączenie z członkiem konsorcjum

Można nawiązać połączenie z członkami konsorcjum przy użyciu rozszerzenia Azure łańcucha bloków Development VS Code Kit. Po nawiązaniu połączenia z konsorcjum można skompilować, skompilować i wdrożyć inteligentne kontrakty w składowej konsorcjum usługi Azure łańcucha bloków.

Jeśli nie masz dostępu do elementu członkowskiego konsorcjum usługi Azure łańcucha bloków, Ukończ wymagania wstępne [szybkiego startu: Tworzenie elementu członkowskiego łańcucha bloków przy użyciu Azure Portal](create-member.md) lub [szybkiego startu: Tworzenie elementu członkowskiego łańcucha bloków usługi Azure łańcucha bloków za pomocą interfejsu wiersza polecenia platformy Azure ](create-member-cli.md).

1. W okienku Eksploratora Visual Studio Code (VS Code) rozwiń rozszerzenie **Azure łańcucha bloków** .
1. Wybierz pozycję **Połącz z konsorcjum**.

   ![Nawiązywanie połączenia z konsorcjum](./media/connect-vscode/connect-consortium.png)

    Jeśli zostanie wyświetlony monit o uwierzytelnienie platformy Azure, postępuj zgodnie z monitami, aby przeprowadzić uwierzytelnianie przy użyciu przeglądarki.
1. Wybierz pozycję **Połącz z usługą Azure łańcucha bloków Service Consortium** na liście rozwijanej paleta poleceń.
1. Wybierz subskrypcję i grupę zasobów skojarzoną z elementem członkowskim konsorcjum usługi Azure łańcucha bloków.
1. Wybierz z listy swoją konsorcjum.

Członkowie konsorcjum i łańcucha bloków są wyświetlani na pasku bocznym Eksploratora programu Visual Studio.

![Konsorcjum wyświetlane w Eksploratorze](./media/connect-vscode/consortium-node.png)

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start użyto rozszerzenia łańcucha bloków Development Kit dla Ethereum Visual Studio Code do dołączenia do konsorcjum w usłudze Azure łańcucha bloków. Skorzystaj z następnego samouczka, aby użyć zestawu Azure łańcucha bloków Development Kit dla Ethereum i Truffle do tworzenia, kompilowania, wdrażania i wykonywania funkcji kontraktu inteligentnego za pośrednictwem transakcji.

> [!div class="nextstepaction"]
> [Używanie Visual Studio Code do tworzenia, kompilowania i wdrażania kontraktów inteligentnych](send-transaction.md)