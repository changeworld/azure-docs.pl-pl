---
title: Tworzenie pierwszej funkcji programu PowerShell na platformie Azure
description: Dowiedz się, jak utworzyć pierwszą funkcję programu PowerShell na platformie Azure przy użyciu Visual Studio Code.
author: joeyaiello
ms.author: jaiello
ms.reviewer: glenga
ms.date: 04/25/2019
ms.topic: quickstart
ms.openlocfilehash: 8f77083ae0107ed9f9c5e3943cfcec25846ff087
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74227200"
---
# <a name="create-your-first-powershell-function-in-azure"></a>Tworzenie pierwszej funkcji programu PowerShell na platformie Azure

Ten artykuł Szybki Start przeprowadzi Cię przez proces tworzenia pierwszej funkcji programu PowerShell [bezserwerowej](https://azure.com/serverless) przy użyciu Visual Studio Code.

![Azure Functions kod w projekcie Visual Studio Code](./media/functions-create-first-function-powershell/powershell-project-first-function.png)

Użyj [Rozszerzenie usługi Azure Functions dla programu Visual Studio Code] , aby utworzyć funkcję programu PowerShell lokalnie, a następnie wdrożyć ją w nowej aplikacji funkcji na platformie Azure. Rozszerzenie jest aktualnie dostępne w wersji zapoznawczej. Aby dowiedzieć się więcej, zobacz stronę [Rozszerzenie usługi Azure Functions dla programu Visual Studio Code].

> [!NOTE]  
> Obsługa programu PowerShell dla rozszerzenia [Azure Functions extension][rozszerzenie usługi azure functions dla programu visual studio code] jest obecnie domyślnie wyłączona. Włączenie obsługi programu PowerShell to jeden z kroków opisanych w tym artykule.

Poniższe kroki są obsługiwane w systemach operacyjnych macOS, Windows i Linux.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki start:

* Instalowanie [programu PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-windows)

* Zainstaluj [program Visual Studio Code](https://code.visualstudio.com/) na jednej z [obsługiwanych platform](https://code.visualstudio.com/docs/supporting/requirements#_platforms). 

* Zainstaluj [rozszerzenie programu PowerShell dla Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell).

* Zainstaluj [zestaw .NET Core SDK 2.2 +](https://www.microsoft.com/net/download) (wymagane przez program Azure Functions Core Tools i dostępne na wszystkich obsługiwanych platformach).

* Zainstaluj wersję 2. x [Azure Functions Core Tools](functions-run-local.md#v2).

* Potrzebna jest również aktywna subskrypcja platformy Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [functions-install-vs-code-extension](../../includes/functions-install-vs-code-extension.md)] 

## <a name="create-a-function-app-project"></a>Tworzenie projektu aplikacji funkcji

Szablon projektu usługi Azure Functions w programie Visual Studio Code umożliwia utworzenie projektu, który można opublikować w aplikacji funkcji na platformie Azure. Aplikacja funkcji umożliwia grupowanie funkcji w jednostki logiczne, co ułatwia wdrażanie i udostępnianie zasobów oraz zarządzanie nimi. 

1. W programie Visual Studio Code wybierz logo platformy Azure, aby wyświetlić obszar**Azure: Functions**, a następnie wybierz ikonę tworzenia nowego projektu.

    ![Tworzenie projektu aplikacji funkcji](./media/functions-create-first-function-powershell/create-function-app-project.png)

1. Wybierz lokalizację obszaru roboczego projektu usługi Functions i wybierz **pozycję Wybierz**.

    > [!NOTE]
    > Ten artykuł został zaprojektowany pod kątem wykonania poza obszarem roboczym. W takim przypadku nie wybieraj folderu projektu, który jest częścią obszaru roboczego.

1. Wybierz program **PowerShell** jako język projektu aplikacji funkcji, a następnie **Azure Functions v2**.

1. Wybierz **wyzwalacz http** jako szablon pierwszej funkcji, użyj `HTTPTrigger` jako nazwy funkcji i wybierz poziom autoryzacji **funkcji**.

    > [!NOTE]
    > Poziom autoryzacji **funkcji** wymaga wartości [klucza funkcji](functions-bindings-http-webhook.md#authorization-keys) podczas wywoływania punktu końcowego funkcji na platformie Azure. Dzięki temu tylko każdy użytkownik może wywołać swoją funkcję.

1. Po wyświetleniu monitu wybierz pozycję **Add to workspace** (Dodaj do obszaru roboczego).

Visual Studio Code tworzy projekt aplikacji funkcji programu PowerShell w nowym obszarze roboczym. Ten projekt zawiera pliki konfiguracji pliku [host. JSON](functions-host-json.md) oraz [Local. Settings. JSON](functions-run-local.md#local-settings-file) , które mają zastosowanie do całej funkcji w projekcie. Ten [projekt programu PowerShell](functions-reference-powershell.md#folder-structure) jest taki sam jak aplikacja funkcji działająca na platformie Azure.

## <a name="run-the-function-locally"></a>Lokalne uruchamianie funkcji

Azure Functions Core Tools integruje się z Visual Studio Code, aby umożliwić uruchamianie i debugowanie Azure Functions projektu lokalnie.  

1. Aby debugować funkcję, Wstaw wywołanie polecenia cmdlet [`Wait-Debugger`] w kodzie funkcji przed dołączeniem debugera, a następnie naciśnij klawisz F5, aby uruchomić projekt aplikacji funkcji i dołączyć debuger. Dane wyjściowe z pakietu Core Tools są wyświetlane na panelu **terminalu**.

1. W panelu **terminalu** skopiuj punkt końcowy adresu URL funkcji wyzwalanej przez protokół HTTP.

    ![Lokalne dane wyjściowe platformy Azure](./media/functions-create-first-function-powershell/functions-vscode-f5.png)

1. Dołącz ciąg zapytania `?name=<yourname>` do tego adresu URL, a następnie użyj `Invoke-RestMethod`, aby wykonać żądanie w następujący sposób:

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
>
> Tworzenie aplikacji funkcji na platformie Azure tylko pyta o nazwę aplikacji funkcji. Inne wartości są zdefiniowane dla Ciebie.
> Ustaw `azureFunctions.advancedCreation`, aby `true` monitować o wszystkie inne wartości.

[!INCLUDE [functions-publish-project-vscode](../../includes/functions-publish-project-vscode.md)]

## <a name="test"></a>Uruchamianie funkcji na platformie Azure

Aby sprawdzić, czy opublikowana funkcja działa na platformie Azure, wykonaj następujące polecenie programu PowerShell, zastępując parametr `Uri` adresem URL funkcji HTTPTrigger z poprzedniego kroku. Tak jak wcześniej, należy dołączyć ciąg zapytania `&name=<yourname>` do adresu URL, jak w poniższym przykładzie:

```powershell
PS > Invoke-WebRequest -Method Get -Uri "https://glengatest-vscode-powershell.azurewebsites.net/api/HttpTrigger?code=nrY05eZutfPqLo0som...&name=PowerShell"

StatusCode        : 200
StatusDescription : OK
Content           : Hello PowerShell
RawContent        : HTTP/1.1 200 OK
                    Content-Length: 16
                    Content-Type: text/plain; charset=utf-8
                    Date: Thu, 25 Apr 2019 16:01:22 GMT

                    Hello PowerShell
Forms             : {}
Headers           : {[Content-Length, 16], [Content-Type, text/plain; charset=utf-8], [Date, Thu, 25 Apr 2019 16:01:22 GMT]}
Images            : {}
InputFields       : {}
Links             : {}
ParsedHtml        : mshtml.HTMLDocumentClass
RawContentLength  : 16
```

## <a name="next-steps"></a>Następne kroki

Użyto Visual Studio Code do utworzenia aplikacji funkcji programu PowerShell z prostą funkcją wyzwalaną przez protokół HTTP. Warto również dowiedzieć się więcej na temat [debugowania funkcji programu PowerShell lokalnie](functions-debug-powershell-local.md) przy użyciu Azure Functions Core Tools. Zapoznaj się z [przewodnikiem dewelopera programu Azure Functions PowerShell](functions-reference-powershell.md).

> [!div class="nextstepaction"]
> [Enable Application Insights integration (Włączanie integracji z usługą Application Insights)](functions-monitoring.md#manually-connect-an-app-insights-resource)

[Azure portal]: https://portal.azure.com
[Azure Functions Core Tools]: functions-run-local.md
[Rozszerzenie usługi Azure Functions dla programu Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
["Czekaj-Debugger"]: /powershell/module/microsoft.powershell.utility/wait-debugger?view=powershell-6
