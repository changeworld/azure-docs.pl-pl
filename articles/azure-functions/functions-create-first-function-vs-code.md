---
title: Tworzenie pierwszej funkcji na platformie Azure przy użyciu programu Visual Studio Code
description: Tworzenie prostej funkcji wyzwalanej przez protokół HTTP i publikowanie jej przy użyciu rozszerzenia usługi Azure Functions w programie Visual Studio Code.
ms.topic: quickstart
ms.date: 01/10/2020
ms.custom: mvc, devcenter
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: 26313c68305f4d7e6411d31fa12366442ce4bd38
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/02/2020
ms.locfileid: "76964181"
---
# <a name="quickstart-create-an-azure-functions-project-using-visual-studio-code"></a>Szybki Start: Tworzenie projektu Azure Functions przy użyciu Visual Studio Code

W tym artykule opisano tworzenie funkcji reagującej na żądania HTTP przy użyciu Visual Studio Code. Po przetestowaniu kodu lokalnie należy wdrożyć go w środowisku bezserwerowym Azure Functions. W ramach tego przewodnika Szybki Start powiąże się niewielką opłatą za kilka centów USD lub mniej na koncie platformy Azure. 

Istnieje również wersja tego artykułu [oparta na interfejsie wiersza polecenia](functions-create-first-azure-function-azure-cli.md) .

## <a name="configure-your-environment"></a>Konfigurowanie środowiska

Przed rozpoczęciem upewnij się, że zostały spełnione następujące wymagania:

+ Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

::: zone pivot="programming-language-csharp,programming-language-powershell,programming-language-python"  
+ [Node. js](https://nodejs.org/)wymagany przez system Windows for npm. Tylko [aktywne wersje LTS LTS i Maintenance ](https://nodejs.org/about/releases/). Aby sprawdzić swoją wersję, użyj polecenia `npm --version`.
    Nie jest wymagana do lokalnego programowania w systemach MacOS i Linux.   
::: zone-end  
::: zone pivot="programming-language-javascript,programming-language-typescript"  
+ [Node. js](https://nodejs.org/), Active LTS i Maintenance — wersje LTS (zalecane 10.14.1). Aby sprawdzić swoją wersję, użyj polecenia `npm --version`.
::: zone-end 
::: zone pivot="programming-language-python"
+ [Python 3,7](https://www.python.org/downloads/release/python-375/) lub [Python 3,6](https://www.python.org/downloads/release/python-368/), który jest obsługiwany przez Azure Functions. Środowisko Python 3,8 nie jest jeszcze obsługiwane. 
::: zone-end   
::: zone pivot="programming-language-powershell"
+ [Program PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-windows)

+ [Zestaw .NET Core SDK 2.2 +](https://www.microsoft.com/net/download)  
::: zone-end  
+ [Visual Studio Code](https://code.visualstudio.com/) na jednej z [obsługiwanych platform](https://code.visualstudio.com/docs/supporting/requirements#_platforms).  
::: zone pivot="programming-language-csharp"  
+ Rozszerzenie dla Visual Studio Code. [ C# ](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)  
::: zone-end  
::: zone pivot="programming-language-python"
+ [Rozszerzenie Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python) dla Visual Studio Code.  
::: zone-end  
::: zone pivot="programming-language-powershell"
+ [Rozszerzenie programu PowerShell dla Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell).  
::: zone-end  

+ [Azure Functions rozszerzenie](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) Visual Studio Code. 

## <a name="create-an-azure-functions-project"></a>Utwórz projekt lokalny 

W tej sekcji użyjesz Visual Studio Code, aby utworzyć projekt lokalnego Azure Functions w wybranym języku. W dalszej części tego artykułu opublikujesz kod funkcji na platformie Azure. 

1. Wybierz ikonę platformy Azure na pasku działania, a następnie w obszarze **Azure: Functions** wybierz ikonę **Utwórz nowy projekt...** .

    ![Wybierz pozycję Utwórz nowy projekt](media/functions-create-first-function-vs-code/create-new-project.png)

1. Wybierz lokalizację katalogu dla obszaru roboczego projektu, a następnie wybierz **pozycję Wybierz**.

    > [!NOTE]
    > Te kroki zostały zaprojektowane do ukończenia poza obszarem roboczym. W takim przypadku nie wybieraj folderu projektu, który jest częścią obszaru roboczego.

1. Podaj następujące informacje na ekranie:

    ::: zone pivot="programming-language-csharp"
    + **Wybierz język projektu funkcji**: Wybierz `C#`.
    ::: zone-end
    ::: zone pivot="programming-language-javascript"
    + **Wybierz język projektu funkcji**: Wybierz `JavaScript`.
    ::: zone-end
    ::: zone pivot="programming-language-typescript"
    + **Wybierz język projektu funkcji**: Wybierz `TypeScript`.
    ::: zone-end
    ::: zone pivot="programming-language-powershell"
    + **Wybierz język projektu funkcji**: Wybierz `PowerShell`.
    ::: zone-end
    ::: zone pivot="programming-language-python"
    + **Wybierz język projektu funkcji**: Wybierz `Python`.

    + **Wybierz alias języka Python, aby utworzyć środowisko wirtualne**: Wybierz lokalizację interpretera języka Python. Jeśli lokalizacja nie jest wyświetlana, wpisz pełną ścieżkę do pliku binarnego języka Python.  
    ::: zone-end

    + **Wybierz szablon dla pierwszej funkcji projektu**: Wybierz `HTTP trigger`.
    
    + **Podaj nazwę funkcji**: wpisz `HttpExample`.
    
    ::: zone pivot="programming-language-csharp"
    + **Podaj przestrzeń nazw**: wpisz `My.Functions`. 
    ::: zone-end

    + **Poziom autoryzacji**: Wybierz `Anonymous`, co umożliwia wszystkim użytkownikom wywoływanie punktu końcowego funkcji. Aby dowiedzieć się więcej o poziomie autoryzacji, zobacz [klucze autoryzacji](functions-bindings-http-webhook.md#authorization-keys).

    + **Wybierz, w jaki sposób chcesz otworzyć projekt**: Wybierz `Add to workspace`.

1. Korzystając z tych informacji, Visual Studio Code generuje projekt Azure Functions z wyzwalaczem HTTP. Pliki projektu lokalnego można wyświetlić w Eksploratorze. Aby dowiedzieć się więcej na temat tworzonych plików, zobacz [pliki wygenerowanego projektu](functions-develop-vs-code.md#generated-project-files). 

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-python"

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

::: zone-end

::: zone pivot="programming-language-powershell"

[!INCLUDE [functions-run-function-test-local-vs-code-ps](../../includes/functions-run-function-test-local-vs-code-ps.md)]

::: zone-end

Po sprawdzeniu, że funkcja działa poprawnie na komputerze lokalnym, można użyć Visual Studio Code do opublikowania projektu bezpośrednio na platformie Azure. 

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../includes/functions-publish-project-vscode.md)]

## <a name="run-the-function-in-azure"></a>Uruchamianie funkcji na platformie Azure

1. Wróć do obszaru **Azure: Functions** na pasku bocznym, a następnie rozwiń nową aplikację funkcji w ramach subskrypcji. Rozwiń węzeł **funkcje**, kliknij prawym przyciskiem myszy (Windows) lub Ctrl + kliknięcie (MacOS) w **HttpExample**, a następnie wybierz polecenie **Kopiuj adres URL funkcji**.

    ![Skopiuj adres URL funkcji dla nowego wyzwalacza HTTP](./media/functions-create-first-function-vs-code/function-copy-endpoint-url.png)

1. Wklej ten adres URL żądania HTTP na pasku adresu przeglądarki, Dodaj `name` ciąg zapytania jako `?name=Functions` na końcu tego adresu URL, a następnie wykonaj żądanie. Adres URL, który wywołuje funkcję wyzwalaną przez protokół HTTP, powinien mieć następujący format:

        http://<functionappname>.azurewebsites.net/api/httpexample?name=Functions 
        
    Poniższy przykład przedstawia odpowiedź w przeglądarce do zdalnego żądania GET zwróconego przez funkcję: 

    ![Odpowiedź funkcji wyświetlona w przeglądarce](./media/functions-create-first-function-vs-code/functions-test-remote-browser.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po przeniesieniu do następnego kroku [Dodaj powiązanie kolejki usługi Azure Storage do funkcji](functions-add-output-binding-storage-queue-vs-code.md), musisz zachować wszystkie Twoje zasoby, aby skompilować, co już zostało zrobione.

W przeciwnym razie można wykonać poniższe kroki, aby usunąć aplikację funkcji i powiązane z nią zasoby, aby uniknąć ponoszenia dalszych kosztów.

[!INCLUDE [functions-cleanup-resources-vs-code.md](../../includes/functions-cleanup-resources-vs-code.md)]

Aby dowiedzieć się więcej o kosztach funkcji, zobacz [szacowanie kosztów planu zużycia](functions-consumption-costs.md).

## <a name="next-steps"></a>Następne kroki

W programie Visual Studio Code utworzono aplikację funkcji z prostą funkcją wyzwalaną przez protokół HTTP. W następnym artykule można rozszerzyć tę funkcję, dodając powiązanie danych wyjściowych. To powiązanie zapisuje ciąg z żądania HTTP do wiadomości w kolejce usługi Azure Queue Storage. 

> [!div class="nextstepaction"]
> [Dodawanie do funkcji powiązania kolejki usługi Azure Storage](functions-add-output-binding-storage-queue-vs-code.md)

[Azure Functions Core Tools]: functions-run-local.md
[Azure Functions extension for Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
