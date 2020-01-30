---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/16/2020
ms.author: glenga
ms.openlocfilehash: c54145cf48912d3911a39e681d85cb6907be8e52
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2020
ms.locfileid: "76842320"
---
## <a name="run-the-function-locally"></a>Lokalne uruchamianie funkcji

Azure Functions Core Tools integruje się z Visual Studio Code, aby umożliwić uruchamianie i debugowanie Azure Functions projektu lokalnie.  

1. Aby debugować funkcję, Wstaw wywołanie polecenia cmdlet [`Wait-Debugger`](/powershell/module/microsoft.powershell.utility/wait-debugger?view=powershell-6) w kodzie funkcji przed dołączeniem debugera, a następnie naciśnij klawisz F5, aby uruchomić projekt aplikacji funkcji i dołączyć debuger. Dane wyjściowe z pakietu Core Tools są wyświetlane na panelu **terminalu**.

1. W panelu **terminalu** skopiuj punkt końcowy adresu URL funkcji wyzwalanej przez protokół HTTP.

    ![Lokalne dane wyjściowe platformy Azure](./media/functions-run-function-test-local-vs-code-ps/functions-vscode-f5.png)

1. Dołącz ciąg zapytania `?name=<yourname>` do tego adresu URL, a następnie użyj `Invoke-RestMethod` w drugim wierszu polecenia programu PowerShell, aby wykonać żądanie w następujący sposób:

    ```powershell
    PS > Invoke-RestMethod -Method Get -Uri http://localhost:7071/api/HttpTrigger?name=PowerShell
    Hello PowerShell
    ```

    Możesz również wykonać żądanie GET z przeglądarki.

    Gdy wywołasz punkt końcowy HttpTrigger bez przekazywania parametru `name` jako parametru zapytania lub w treści, funkcja zwróci błąd [HttpStatusCode]:: nieprawidłowego żądania. Gdy przeglądasz kod w programie Run. ps1, zobaczysz, że ten błąd występuje podczas projektowania.

1. Aby zatrzymać debugowanie, naciśnij klawisze Shift+F5.

Gdy będziesz mieć pewność, że funkcja działa poprawnie na komputerze lokalnym, możesz opublikować projekt na platformie Azure.

> [!NOTE]
> Pamiętaj, aby usunąć wszystkie wywołania do `Wait-Debugger` przed opublikowaniem funkcji na platformie Azure. 