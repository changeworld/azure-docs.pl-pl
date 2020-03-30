---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/12/2020
ms.author: glenga
ms.openlocfilehash: f4af3c202d4f00c4ac3041921175c92226f0db7c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "76964157"
---
## <a name="run-the-function-locally"></a>Lokalne uruchamianie funkcji

Program Visual Studio Code integruje się z [podstawowymi narzędziami usługi Azure Functions,](../articles/azure-functions/functions-run-local.md) aby umożliwić uruchomienie tego projektu na lokalnym komputerze deweloperskim przed opublikowaniem na platformie Azure.

1. Aby wywołać funkcję, naciśnij klawisz F5, aby uruchomić projekt aplikacji funkcji. Dane wyjściowe z pakietu Core Tools są wyświetlane na panelu **terminalu**.

1. Jeśli nie zainstalowano jeszcze podstawowych narzędzi usługi Azure Functions, wybierz pozycję **Zainstaluj** w wierszu polecenia. Po zainstalowaniu narzędzi podstawowych aplikacja zostanie uruchomiony w panelu **Terminal.** Punkt końcowy adresu URL funkcji wyzwalanej przez HTTP jest uruchomiony lokalnie. 

    ![Lokalne dane wyjściowe platformy Azure](./media/functions-run-function-test-local-vs-code/functions-vscode-f5.png)

1. Po uruchomieniu narzędzia Core, przejdź do następującego adresu `?name=Functions` URL, aby wykonać żądanie GET, który zawiera ciąg zapytania.

    <http://localhost:7071/api/HttpExample?name=Functions>

1. Zwracana jest odpowiedź, która wygląda następująco w przeglądarce:

    ![Odpowiedź hosta localhost funkcji wyświetlona w przeglądarce](./media/functions-run-function-test-local-vs-code/functions-test-local-browser.png)

1. Informacje o żądaniu są wyświetlane w panelu **Terminal.**

    ![Wykonywanie funkcji w panelu Terminal](./media/functions-run-function-test-local-vs-code/function-execution-terminal.png)

1. Naciśnij klawisze Ctrl +C, aby zatrzymać narzędzia core i odłączyć debuger.
