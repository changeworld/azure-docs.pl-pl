---
title: Tworzenie pierwszej funkcji programu PowerShell z usługą Azure Functions
description: Dowiedz się, jak utworzyć pierwszą funkcję programu PowerShell w systemie Azure za pomocą programu Visual Studio Code.
services: functions
keywords: ''
author: joeyaiello
manager: jeconnoc
ms.author: jaiello, glenga
ms.date: 04/25/2019
ms.topic: quickstart
ms.service: azure-functions
ms.devlang: powershell
ms.openlocfilehash: 1fc541f1236d17e1c2ffbf64ddb0340dcf5c0b9a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67063483"
---
# <a name="create-your-first-powershell-function-in-azure-preview"></a>Tworzenie pierwszej funkcji programu PowerShell w systemie Azure (wersja zapoznawcza)

[!INCLUDE [functions-powershell-preview-note](../../includes/functions-powershell-preview-note.md)]

W tym artykule Szybki Start przedstawiono szczegółowe instrukcje dotyczące tworzenia pierwszej [bezserwerowe](https://azure.com/serverless) funkcji programu PowerShell przy użyciu programu Visual Studio Code.

![Kod usługi Azure Functions w projekcie programu Visual Studio Code](./media/functions-create-first-function-powershell/powershell-project-first-function.png)

Możesz użyć [Rozszerzenie usługi Azure Functions dla programu Visual Studio Code] Tworzenie funkcji programu PowerShell lokalnie, a następnie wdrożony nowej aplikacji funkcji na platformie Azure. Rozszerzenie jest aktualnie dostępne w wersji zapoznawczej. Aby dowiedzieć się więcej, zobacz stronę [Rozszerzenie usługi Azure Functions dla programu Visual Studio Code].

> [!NOTE]  
> Obsługa programu PowerShell dla [rozszerzenia usługi Azure Functions][rozszerzenie usługi azure functions dla programu visual studio code] aktualnie jest domyślnie wyłączona. Włączanie obsługi programu PowerShell jest jednym z kroków opisanych w tym artykule.

Poniższe kroki są obsługiwane w systemach macOS, Windows i systemy operacyjne oparte na systemie Linux.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki start:

* Zainstaluj [programu PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-windows)

* Zainstaluj [program Visual Studio Code](https://code.visualstudio.com/) na jednej z [obsługiwanych platform](https://code.visualstudio.com/docs/supporting/requirements#_platforms). 

* Zainstaluj [rozszerzenia programu PowerShell dla programu Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell).

* Zainstaluj [zestawu .NET Core SDK 2.2 +](https://www.microsoft.com/net/download) (dostępne na wszystkich obsługiwanych platformach i wymaganiami podstawowych narzędzi usługi Azure Functions).

* Instalowanie wersji 2.x [podstawowych narzędzi usługi Azure Functions](functions-run-local.md#v2).

* Należy również aktywną subskrypcją platformy Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [functions-install-vs-code-extension](../../includes/functions-install-vs-code-extension.md)] 

## <a name="create-a-function-app-project"></a>Tworzenie projektu aplikacji funkcji

Szablon projektu usługi Azure Functions w programie Visual Studio Code umożliwia utworzenie projektu, który można opublikować w aplikacji funkcji na platformie Azure. Aplikacja funkcji umożliwia grupowanie funkcji w jednostki logiczne, co ułatwia wdrażanie i udostępnianie zasobów oraz zarządzanie nimi. 

1. W programie Visual Studio Code Wybierz logo platformy Azure, aby wyświetlić **platformy Azure: Funkcje** obszaru, a następnie wybierz ikonę Utwórz nowy projekt.

    ![Tworzenie projektu aplikacji funkcji](./media/functions-create-first-function-powershell/create-function-app-project.png)

1. Wybierz lokalizację dla obszaru roboczego projektu funkcji i wybierz **wybierz**.

    > [!NOTE]
    > Ten artykuł został zaprojektowany pod kątem wykonania poza obszarem roboczym. W takim przypadku nie wybieraj folderu projektu, który jest częścią obszaru roboczego.

1. Wybierz **programu Powershell (wersja zapoznawcza)** jako język dla projektu aplikacji funkcji i następnie **usługi Azure Functions w wersji 2**.

1. Wybierz **wyzwalacza HTTP** jako szablon dla pierwszej funkcji, należy użyć `HTTPTrigger` jako funkcja nazwę, a następnie wybierz poziom autoryzacji **funkcja**.

    > [!NOTE]
    > **Funkcja** poziom autoryzacji wymaga [funkcyjne](functions-bindings-http-webhook.md#authorization-keys) wartości podczas wywoływania punktu końcowego funkcji na platformie Azure. To na celu utrudnienie wystarczy zadzwonić do funkcji.

1. Po wyświetleniu monitu wybierz pozycję **Add to workspace** (Dodaj do obszaru roboczego).

Visual Studio Code tworzy projekt aplikacji funkcji programu PowerShell w nowy obszar roboczy. Ten projekt zawiera [host.json](functions-host-json.md) i [local.settings.json](functions-run-local.md#local-settings-file) pliki konfiguracyjne, które mają zastosowanie do wszystkich funkcji w projekcie. To [projektu programu PowerShell](functions-reference-powershell.md#folder-structure) jest taka sama jak aplikacji funkcji działających na platformie Azure.

## <a name="run-the-function-locally"></a>Lokalne uruchamianie funkcji

Podstawowe narzędzia usługi Azure Functions integruje się z usługą Visual Studio Code, aby umożliwić uruchamianie i debugowanie projektu usługi Azure Functions lokalnie.  

1. Aby debugować swoją funkcję, należy wstawić wywołanie do [ `Wait-Debugger` ] polecenia cmdlet w kodzie funkcji przed chcesz dołączyć debuger, naciśnij klawisz F5, aby uruchomić projekt aplikacji funkcji i dołączyć debuger. Dane wyjściowe z pakietu Core Tools są wyświetlane na panelu **terminalu**.

1. W panelu **terminalu** skopiuj punkt końcowy adresu URL funkcji wyzwalanej przez protokół HTTP.

    ![Lokalne dane wyjściowe platformy Azure](./media/functions-create-first-function-powershell/functions-vscode-f5.png)

1. Dołącz ciąg zapytania `?name=<yourname>` do tego adresu URL, a następnie użyj `Invoke-RestMethod` można wykonać żądania, w następujący sposób:

    ```powershell
    PS > Invoke-RestMethod -Method Get -Uri http://localhost:7071/api/HttpTrigger?name=PowerShell
    Hello PowerShell
    ```

    Można również wykonać żądanie GET w przeglądarce.

    Podczas wywoływania punktu końcowego HttpTrigger bez przekazywania `name` parametru jako parametr zapytania lub w treści, funkcja zwraca błąd 500. Podczas przeglądania kodu w run.ps1 zobaczysz, że ten błąd jest celowe.

1. Aby zatrzymać debugowanie, naciśnij klawisze Shift+F5.

Gdy będziesz mieć pewność, że funkcja działa poprawnie na komputerze lokalnym, możesz opublikować projekt na platformie Azure.

> [!NOTE]
> Pamiętaj, aby usunąć wszelkie wywołania `Wait-Debugger` przed opublikowaniem funkcji na platformie Azure. 

> [!NOTE]
> Tworzenie aplikacji funkcji na platformie Azure tylko wyświetli monit o nazwę aplikacji funkcji. Ustaw azureFunctions.advancedCreation wartość true, aby się monit o wszystkich pozostałych wartości.

[!INCLUDE [functions-publish-project-vscode](../../includes/functions-publish-project-vscode.md)]

## <a name="test"></a>Uruchom funkcję na platformie Azure

Aby zweryfikować, że uruchomieniu opublikowanej funkcji na platformie Azure, wykonaj następujące polecenie programu PowerShell, zastępując `Uri` parametru za pomocą adresu URL funkcji HTTPTrigger z poprzedniego kroku. Tak jak poprzednio Dołącz ciąg zapytania `&name=<yourname>` do adresu URL, jak w poniższym przykładzie:

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

## <a name="next-steps"></a>Kolejne kroki

Używasz programu Visual Studio Code do tworzenia aplikacji funkcji programu PowerShell przy użyciu prostej funkcji wyzwalanej przez HTTP. Możesz także dowiedzieć się więcej o [debugowania środowiska PowerShell działają lokalnie](functions-debug-powershell-local.md) przy użyciu podstawowych narzędzi usługi Azure Functions. Zapoznaj się z [— przewodnik dewelopera usługi Azure Functions w programie PowerShell](functions-reference-powershell.md).

> [!div class="nextstepaction"]
> [Włączanie integracji usługi Application Insights](functions-monitoring.md#manually-connect-an-app-insights-resource)

[Azure portal]: https://portal.azure.com
[Azure Functions Core Tools]: functions-run-local.md
[Rozszerzenie usługi Azure Functions dla programu Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
[`Wait-Debugger`]: /powershell/module/microsoft.powershell.utility/wait-debugger?view=powershell-6
