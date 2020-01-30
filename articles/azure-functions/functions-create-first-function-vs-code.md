---
title: Tworzenie pierwszej funkcji na platformie Azure przy użyciu programu Visual Studio Code
description: Tworzenie prostej funkcji wyzwalanej przez protokół HTTP i publikowanie jej przy użyciu rozszerzenia usługi Azure Functions w programie Visual Studio Code.
ms.topic: quickstart
ms.date: 01/10/2020
ms.custom: mvc, devcenter
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: 0540c7b01d693975f34515c7d13f0477ac74d4a1
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2020
ms.locfileid: "76842260"
---
# <a name="quickstart-create-an-azure-functions-project-using-visual-studio-code"></a>Szybki Start: Tworzenie projektu Azure Functions przy użyciu Visual Studio Code

W tym artykule opisano tworzenie funkcji reagującej na żądania HTTP przy użyciu Visual Studio Code. Po przetestowaniu kodu lokalnie należy wdrożyć go w środowisku bezserwerowym Azure Functions. W ramach tego przewodnika Szybki Start powiąże się niewielką opłatą za kilka centów USD lub mniej na koncie platformy Azure. 

Istnieje również wersja tego artykułu [oparta na interfejsie wiersza polecenia](functions-create-first-azure-function-azure-cli.md) .

## <a name="prerequisites"></a>Wymagania wstępne

+ [Visual Studio Code](https://code.visualstudio.com/) na jednej z [obsługiwanych platform](https://code.visualstudio.com/docs/supporting/requirements#_platforms). 
::: zone pivot="programming-language-csharp"
+ Rozszerzenie dla Visual Studio Code. [ C# ](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
::: zone-end
::: zone pivot="programming-language-javascript,programming-language-typescript"
+ [Node. js](https://nodejs.org/), Active LTS i Maintenance LTS wersje (zalecane 8.11.1 i 10.14.1).
::: zone-end
::: zone pivot="programming-language-python"
+ [Python 3,7](https://www.python.org/downloads/release/python-375/) lub [Python 3,6](https://www.python.org/downloads/release/python-368/), który jest obsługiwany przez Azure Functions. Środowisko Python 3,8 nie jest jeszcze obsługiwane. 

+ [Rozszerzenie Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python) dla Visual Studio Code.
::: zone-end
::: zone pivot="programming-language-powershell"
+ [Program PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-windows)

+ [Zestaw .NET Core SDK 2.2 +](https://www.microsoft.com/net/download)

+ [Rozszerzenie programu PowerShell dla Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell). 
::: zone-end

+ [Azure Functions rozszerzenie](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) Visual Studio Code. 

+ [Konto platformy Azure](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing) z aktywną subskrypcją. Jeśli nie masz subskrypcji, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

## <a name="create-an-azure-functions-project"></a>Utwórz projekt lokalny 

W tej sekcji użyjesz Visual Studio Code, aby utworzyć projekt lokalnego Azure Functions w wybranym języku. W dalszej części tego artykułu opublikujesz kod funkcji na platformie Azure. 

1. Wybierz ikonę platformy Azure na pasku działania, a następnie w obszarze **Azure: Functions** wybierz ikonę **Utwórz nowy projekt...** .

    ![Wybierz pozycję Utwórz nowy projekt](media/functions-create-first-function-vs-code/create-new-project.png)

1. Wybierz lokalizację katalogu dla obszaru roboczego projektu, a następnie wybierz **pozycję Wybierz**.

    > [!NOTE]
    > Te kroki zostały zaprojektowane do ukończenia poza obszarem roboczym. W takim przypadku nie wybieraj folderu projektu, który jest częścią obszaru roboczego.

1. Podaj następujące informacje na ekranie:

    ::: zone pivot="programming-language-csharp"

    | Monit | Wartość | 
    | ------ | ----- | 
    | Wybierz język dla projektu funkcji | C# |
    | Wybierz wersję | Azure Functions v2 | 
    | Wybierz szablon dla pierwszej funkcji projektu | Wyzwalacz HTTP | 
    | Podaj nazwę funkcji | HttpExample | 
    | Podaj przestrzeń nazw | My. Functions | 
    | Poziom autoryzacji | Anonimowe | 
    | Wybierz, w jaki sposób chcesz otworzyć projekt | Dodaj do obszaru roboczego |

    ::: zone-end

    ::: zone pivot="programming-language-javascript"

    | Monit | Wartość | 
    | ------ | ----- | 
    | Wybierz język dla projektu funkcji | JavaScript | 
    | Wybierz wersję | Azure Functions v2 | 
    | Wybierz szablon dla pierwszej funkcji projektu | Wyzwalacz HTTP | 
    | Podaj nazwę funkcji | HttpExample | 
    | Poziom autoryzacji | Anonimowe | 
    | Wybierz, w jaki sposób chcesz otworzyć projekt | Dodaj do obszaru roboczego |

    ::: zone-end

    ::: zone pivot="programming-language-typescript"

    | Monit | Wartość | 
    | ------ | ----- | 
    | Wybierz język dla projektu funkcji | TypeScript | 
    | Wybierz wersję | Azure Functions v2 | 
    | Wybierz szablon dla pierwszej funkcji projektu | Wyzwalacz HTTP | 
    | Podaj nazwę funkcji | HttpExample | 
    | Poziom autoryzacji | Anonimowe | 
    | Wybierz, w jaki sposób chcesz otworzyć projekt | Dodaj do obszaru roboczego |

    ::: zone-end

    ::: zone pivot="programming-language-powershell"

    | Monit | Wartość | 
    | ------ | ----- | 
    | Wybierz język dla projektu funkcji | PowerShell | 
    | Wybierz wersję | Azure Functions v2 | 
    | Wybierz szablon dla pierwszej funkcji projektu | Wyzwalacz HTTP | 
    | Podaj nazwę funkcji | HttpExample | 
    | Poziom autoryzacji | Anonimowe | 
    | Wybierz, w jaki sposób chcesz otworzyć projekt | Dodaj do obszaru roboczego |

    ::: zone-end

    ::: zone pivot="programming-language-python"

    | Monit | Wartość | 
    | ------ | ----- | 
    | Wybierz język dla projektu funkcji | Python | 
    | Wybierz wersję | Azure Functions v2 | 
    | Wybierz alias języka Python, aby utworzyć środowisko wirtualne | Alias języka Python | 
    | Wybierz szablon dla pierwszej funkcji projektu | Wyzwalacz HTTP | 
    | Podaj nazwę funkcji | HttpExample | 
    | Poziom autoryzacji | Anonimowe | 
    | Wybierz, w jaki sposób chcesz otworzyć projekt | Dodaj do obszaru roboczego | 

    ::: zone-end

1. Visual Studio Code wykonuje następujące czynności:

    + Tworzy projekt Azure Functions w nowym obszarze roboczym, który zawiera pliki konfiguracyjne [host. JSON](functions-host-json.md) i [Local. Settings. JSON](functions-run-local.md#local-settings-file) . 

    ::: zone pivot="programming-language-csharp"

    + Tworzy [plik biblioteki klas HttpExample.cs](functions-dotnet-class-library.md#functions-class-library-project) , który implementuje funkcję.

    ::: zone-end
        
    ::: zone pivot="programming-language-javascript"
    
    + Tworzy plik Package. JSON w folderze głównym.

    + Tworzy folder HttpExample zawierający [plik definicji Function. JSON](functions-reference-node.md#folder-structure) oraz [plik index. js](functions-reference-node.md#exporting-a-function), plik Node. js, który zawiera kod funkcji.
    
    ::: zone-end
    
    ::: zone pivot="programming-language-typescript"
    
    + Tworzy plik Package. JSON i plik tsconfig. JSON w folderze głównym.

    + Tworzy folder HttpExample, który zawiera [plik definicji Function. JSON](functions-reference-node.md#folder-structure) oraz [plik index. TS](functions-reference-node.md#typescript), plik TypeScript zawierający kod funkcji.
    
    ::: zone-end
    
    ::: zone pivot="programming-language-powershell"
    
    + Tworzy folder HttpExample zawierający [plik definicji Function. JSON](functions-reference-python.md#programming-model) oraz plik Run. ps1, który zawiera kod funkcji.
    
    ::: zone-end
    
    ::: zone pivot="programming-language-python"
    
    + Tworzy plik. txt wymagań na poziomie projektu, który zawiera listę pakietów wymaganych przez funkcje.
    
    + Tworzy folder HttpExample zawierający [plik definicji Function. JSON](functions-reference-python.md#programming-model) oraz \_\_init\_\_. PR, który zawiera kod funkcji.
    
    ::: zone-end

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

1. Skopiuj adres URL wyzwalacza HTTP z panelu **Dane wyjściowe**. Ponownie Dodaj `name` ciąg zapytania jako `?name=<yourname>` na końcu tego adresu URL i wykonaj żądanie.

    Adres URL, który wywołuje funkcję wyzwalaną przez protokół HTTP, powinien mieć następujący format:

        http://<functionappname>.azurewebsites.net/api/httpexample?name=<yourname> 

1. Wklej nowy adres URL żądania HTTP na pasku adresu przeglądarki. Poniższy przykład przedstawia odpowiedź w przeglądarce do zdalnego żądania GET zwróconego przez funkcję: 

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
