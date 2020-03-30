---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 02/19/2020
ms.author: glenga
ms.openlocfilehash: edf5fc33ec14d41630462cca1a4ace0663473196
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78191063"
---
## <a name="run-the-function-locally"></a>Lokalne uruchamianie funkcji

Podstawowe narzędzia azure functions integruje się z programem Visual Studio Code, aby umożliwić lokalne uruchamianie i debugowanie projektu usługi Azure Functions. Aby uzyskać szczegółowe informacje na temat debugowania w programie Visual Studio Code, zobacz [debugowanie programu PowerShell Azure Functions lokalnie.](../articles/azure-functions/functions-debug-powershell-local.md) 

1. Naciśnij klawisz F5, aby uruchomić projekt aplikacji funkcji. Dane wyjściowe z pakietu Core Tools są wyświetlane na panelu **terminalu**.

1. W panelu **terminalu** skopiuj punkt końcowy adresu URL funkcji wyzwalanej przez protokół HTTP.

    ![Lokalne dane wyjściowe platformy Azure](./media/functions-run-function-test-local-vs-code-ps/functions-vscode-f5.png)

1. Dołącz ciąg `?name=<yourname>` zapytania do tego adresu `Invoke-RestMethod` URL, a następnie użyj w drugim wierszu polecenia programu PowerShell, aby wykonać żądanie w następujący sposób:

    ```powershell
    PS > Invoke-RestMethod -Method Get -Uri http://localhost:7071/api/HttpTrigger?name=PowerShell
    Hello PowerShell
    ```

    Żądanie GET można również wykonać z przeglądarki z następującego adresu URL:

    <http://localhost:7071/api/HttpExample?name=PowerShell>

    Po wywołaniu punktu końcowego HttpTrigger bez przekazywania parametru `name` jako parametr kwerendy `BadRequest` lub w treści, funkcja zwraca błąd. Podczas przeglądania kodu w run.ps1, widać, że ten błąd występuje zgodnie z projektem.

1. Informacje o żądaniu są wyświetlane w panelu **Terminal.**

    ![Wykonywanie funkcji w panelu Terminal](./media/functions-run-function-test-local-vs-code-ps/function-execution-terminal.png)

1. Po zakończeniu naciśnij **klawisze Ctrl +C,** aby zatrzymać narzędzia podstawowe.

Gdy będziesz mieć pewność, że funkcja działa poprawnie na komputerze lokalnym, możesz opublikować projekt na platformie Azure.