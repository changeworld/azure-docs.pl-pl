---
title: Tworzenie pierwszej funkcji na platformie Azure przy użyciu programu Visual Studio Code
description: Tworzenie prostej funkcji wyzwalanej przez protokół HTTP i publikowanie jej przy użyciu rozszerzenia usługi Azure Functions w programie Visual Studio Code.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: azure functions, funkcje, przetwarzanie zdarzeń, obliczenia, architektura bez serwera
ms.service: azure-functions
ms.topic: quickstart
ms.date: 06/25/2019
ms.author: glenga
ms.custom: mvc, devcenter
ms.openlocfilehash: fbde3c939a23e4023c0403c27433717a11762b08
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2019
ms.locfileid: "73622054"
---
# <a name="create-your-first-function-using-visual-studio-code"></a>Tworzenie pierwszej funkcji przy użyciu programu Visual Studio Code

Usługa Azure Functions umożliwia wykonywanie kodu w środowisku [bezserwerowym](https://azure.microsoft.com/solutions/serverless/) bez konieczności uprzedniego tworzenia maszyny wirtualnej lub publikowania aplikacji internetowej.

W tym artykule dowiesz się, jak użyć [Rozszerzenie usługi Azure Functions dla programu Visual Studio Code] do utworzenia i przetestowania funkcji „hello world” na komputerze lokalnym przy użyciu programu Microsoft Visual Studio Code. Następnie opublikujesz kod funkcji na platformie Azure z programu Visual Studio Code.

![Kod usługi Azure Functions w projekcie programu Visual Studio](./media/functions-create-first-function-vs-code/functions-vscode-intro.png)

Rozszerzenie obsługuje C#obecnie funkcje, JavaScript, Java i Python. Kroki opisane w tym artykule i artykule, które są zgodne z obsługą C# języka JavaScript i funkcji. Aby dowiedzieć się, jak używać Visual Studio Code do tworzenia i publikowania funkcji języka Python, zobacz [Tworzenie i wdrażanie bezserwerowego Azure Functions w języku Python z Visual Studio Code](/azure/python/tutorial-vs-code-serverless-python-01). Aby dowiedzieć się, jak używać Visual Studio Code do tworzenia i publikowania funkcji programu PowerShell, zobacz [Tworzenie pierwszej funkcji programu PowerShell na platformie Azure](functions-create-first-function-powershell.md). 

Rozszerzenie jest aktualnie dostępne w wersji zapoznawczej. Aby dowiedzieć się więcej, zobacz stronę [Rozszerzenie usługi Azure Functions dla programu Visual Studio Code].

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki start:

* Zainstaluj [program Visual Studio Code](https://code.visualstudio.com/) na jednej z [obsługiwanych platform](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

* Zainstaluj wersję 2. x [Azure Functions Core Tools](functions-run-local.md#v2).

* Zainstaluj określone wymagania dla wybranego języka:

    | Język | Wymaganie |
    | -------- | --------- |
    | **C#** | [C#rozszerzenia](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)  |
    | **JavaScript** | <sup>*</sup> [Node. js](https://nodejs.org/) | 
 
    <sup>*</sup> Aktywne wersje LTS LTS i Maintenance (zalecane 8.11.1 i 10.14.1).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [functions-install-vs-code-extension](../../includes/functions-install-vs-code-extension.md)]

[!INCLUDE [functions-create-function-app-vs-code](../../includes/functions-create-function-app-vs-code.md)]

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

Gdy będziesz mieć pewność, że funkcja działa poprawnie na komputerze lokalnym, możesz opublikować projekt na platformie Azure.

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../includes/functions-publish-project-vscode.md)]

## <a name="run-the-function-in-azure"></a>Uruchamianie funkcji na platformie Azure

1. Skopiuj adres URL wyzwalacza HTTP z panelu **Dane wyjściowe**. Ten adres URL zawiera klucz funkcji, który jest przesyłany do `code` parametru zapytania. Tak jak poprzednio dołącz ciąg zapytania `?name=<yourname>` na końcu tego adresu URL i wykonaj żądanie.

    Adres URL, który wywołuje funkcję wyzwalaną przez protokół HTTP, powinien mieć następujący format:

        http://<functionappname>.azurewebsites.net/api/<functionname>?code=<function_key>&name=<yourname> 

1. Wklej nowy adres URL żądania HTTP na pasku adresu przeglądarki. Na poniższym obrazie przedstawiono wyświetloną w przeglądarce odpowiedź na zdalne żądanie GET zwróconą przez funkcję: 

    ![Odpowiedź funkcji wyświetlona w przeglądarce](./media/functions-create-first-function-vs-code/functions-test-remote-browser.png)

## <a name="next-steps"></a>Następne kroki

W programie Visual Studio Code utworzono aplikację funkcji z prostą funkcją wyzwalaną przez protokół HTTP. W następnym artykule można rozszerzyć tę funkcję, dodając powiązanie danych wyjściowych. To powiązanie zapisuje ciąg z żądania HTTP do wiadomości w kolejce usługi Azure Queue Storage. W następnym artykule pokazano również, jak wyczyścić te nowe zasoby platformy Azure, usuwając utworzoną grupę zasobów.

> [!div class="nextstepaction"]
> [Dodawanie do funkcji powiązania kolejki usługi Azure Storage](functions-add-output-binding-storage-queue-vs-code.md)

[Azure Functions Core Tools]: functions-run-local.md
[Rozszerzenie usługi Azure Functions dla programu Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
