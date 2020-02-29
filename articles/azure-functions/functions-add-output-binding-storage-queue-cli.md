---
title: Łączenie Azure Functions z usługą Azure Storage przy użyciu narzędzi wiersza polecenia
description: Dowiedz się, jak połączyć Azure Functions z kolejką usługi Azure Storage, dodając powiązanie danych wyjściowych do projektu wiersza polecenia.
ms.date: 02/07/2020
ms.topic: quickstart
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: e3c37b368b723cc95302949baa8e85e2a8b621be
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/29/2020
ms.locfileid: "78201927"
---
# <a name="connect-azure-functions-to-azure-storage-using-command-line-tools"></a>Łączenie Azure Functions z usługą Azure Storage przy użyciu narzędzi wiersza polecenia

W tym artykule opisano integrację kolejki usługi Azure Storage z funkcją i kontem magazynu utworzonym w [poprzednim przewodniku szybki start](functions-create-first-azure-function-azure-cli.md). Tę integrację można osiągnąć za pomocą *powiązania danych wyjściowych* , które zapisuje dane z żądania HTTP do wiadomości w kolejce. Wykonanie tego artykułu nie wiąże się z żadnymi dodatkowymi kosztami powyżej kilku centów z poprzedniego przewodnika Szybki Start. Aby dowiedzieć się więcej na temat powiązań, zobacz temat [Azure Functions wyzwalacze i koncepcje powiązań](functions-triggers-bindings.md).

## <a name="configure-your-local-environment"></a>Konfigurowanie środowiska lokalnego

Przed rozpoczęciem należy wykonać czynności opisane w artykule [Szybki Start: Tworzenie projektu Azure Functions z poziomu wiersza polecenia](functions-create-first-azure-function-azure-cli.md). Jeśli przed końcem tego artykułu zostały już wyczyszczone zasoby, wykonaj kroki ponownie, aby utworzyć aplikację funkcji i powiązane zasoby na platformie Azure.

## <a name="retrieve-the-azure-storage-connection-string"></a>Pobieranie parametrów połączenia usługi Azure Storage

Po utworzeniu aplikacji funkcji na platformie Azure w poprzednim przewodniku szybki start utworzono również konto magazynu. Parametry połączenia dla tego konta są bezpiecznie przechowywane w ustawieniach aplikacji na platformie Azure. Pobierając ustawienia do pliku *Local. Settings. JSON* , można użyć zapisu tego połączenia do kolejki magazynu w ramach tego samego konta podczas lokalnego uruchamiania funkcji. 

1. Z poziomu katalogu głównego projektu uruchom następujące polecenie, zastępując `<APP_NAME>` nazwą aplikacji funkcji z poprzedniego przewodnika Szybki Start. To polecenie spowoduje zastąpienie wszelkich istniejących wartości w pliku.

    ```
    func azure functionapp fetch-app-settings <APP_NAME>
    ```
    
1. Otwórz plik *Local. Settings. JSON* i Znajdź wartość o nazwie `AzureWebJobsStorage`, czyli parametry połączenia konta magazynu. Używasz nazwy `AzureWebJobsStorage` i parametrów połączenia w innych sekcjach tego artykułu.

> [!IMPORTANT]
> Ponieważ *Local. Settings. JSON* zawiera wpisy tajne pobrane z platformy Azure, należy zawsze wykluczyć ten plik z kontroli źródła. Plik *. gitignore* utworzony za pomocą projektu funkcji lokalnych domyślnie wyklucza plik.

[!INCLUDE [functions-register-storage-binding-extension-csharp](../../includes/functions-register-storage-binding-extension-csharp.md)]

## <a name="add-an-output-binding-definition-to-the-function"></a>Dodawanie definicji powiązania danych wyjściowych do funkcji

Chociaż funkcja może mieć tylko jeden wyzwalacz, może mieć wiele powiązań wejściowych i wyjściowych, które umożliwiają łączenie się z innymi usługami i zasobami platformy Azure bez konieczności pisania niestandardowego kodu integracji. 

::: zone pivot="programming-language-python,programming-language-javascript,programming-language-powershell,programming-language-typescript"  
Te powiązania deklaruje się w pliku *Function. JSON* w folderze funkcji. Z poprzedniego przewodnika Szybki Start plik *Function. JSON* w folderze *HttpExample* zawiera dwa powiązania w kolekcji `bindings`:  
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript"  
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-JavaScript/function.json" range="2-18":::  
::: zone-end

::: zone pivot="programming-language-python"  
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json" range="2-18":::  
::: zone-end

::: zone pivot="programming-language-powershell"  
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-PowerShell/function.json" range="2-18":::
::: zone-end  

::: zone pivot="programming-language-python,programming-language-javascript, programming-language-powershell, programming-language-typescript"  
Każde powiązanie ma co najmniej typ, kierunek i nazwę. W powyższym przykładzie pierwsze powiązanie jest typu `httpTrigger` z kierunekem `in`. Dla `in` kierunek `name` określa nazwę parametru wejściowego, który jest wysyłany do funkcji po wywołaniu przez wyzwalacz.  
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript"  
Drugie powiązanie w kolekcji ma nazwę `res`. To `http` powiązania jest powiązaniem wyjściowym (`out`), które jest używane do zapisywania odpowiedzi HTTP. 

Aby zapisać do kolejki usługi Azure Storage przy użyciu tej funkcji, należy dodać powiązanie `out` typu `queue` z nazwą `msg`, jak pokazano w poniższym kodzie:

:::code language="json" source="~/functions-docs-javascript/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-python"  
Drugie powiązanie w kolekcji jest typu `http` z kierunkiem `out`, a w takim przypadku specjalna `name` `$return` wskazuje, że to powiązanie używa wartości zwracanej funkcji zamiast podawania parametru wejściowego.

Aby zapisać do kolejki usługi Azure Storage przy użyciu tej funkcji, należy dodać powiązanie `out` typu `queue` z nazwą `msg`, jak pokazano w poniższym kodzie:

:::code language="json" source="~/functions-docs-python/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-powershell"  
Drugie powiązanie w kolekcji ma nazwę `res`. To `http` powiązania jest powiązaniem wyjściowym (`out`), które jest używane do zapisywania odpowiedzi HTTP. 

Aby zapisać do kolejki usługi Azure Storage przy użyciu tej funkcji, należy dodać powiązanie `out` typu `queue` z nazwą `msg`, jak pokazano w poniższym kodzie:

:::code language="json" source="~/functions-docs-powershell/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-python,programming-language-javascript,programming-language-powershell,programming-language-typescript"  
W tym przypadku `msg` jest przyznany do funkcji jako argument wyjściowy. W przypadku typu `queue` należy również określić nazwę kolejki w `queueName` i podać *nazwę* połączenia usługi Azure Storage (z pliku *Local. Settings. json*) w `connection`. 
::: zone-end  

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]  
::: zone-end  

Aby uzyskać więcej informacji na temat szczegółów powiązań, zobacz [Azure Functions wyzwalacze i koncepcje powiązań](functions-triggers-bindings.md) oraz [konfigurację wyjściową kolejki](functions-bindings-storage-queue-output.md#configuration).

## <a name="add-code-to-use-the-output-binding"></a>Dodaj kod, aby użyć powiązania danych wyjściowych

Za pomocą powiązania kolejki określonego w *funkcji Function. JSON*można teraz zaktualizować funkcję w taki sposób, aby otrzymywać `msg` parametr wyjściowy i pisać komunikaty do kolejki.

::: zone pivot="programming-language-python"     
[!INCLUDE [functions-add-output-binding-python](../../includes/functions-add-output-binding-python.md)]
::: zone-end  

::: zone pivot="programming-language-javascript"  
[!INCLUDE [functions-add-output-binding-js](../../includes/functions-add-output-binding-js.md)]
::: zone-end  

::: zone pivot="programming-language-typescript"  
[!INCLUDE [functions-add-output-binding-ts](../../includes/functions-add-output-binding-ts.md)]
::: zone-end  

::: zone pivot="programming-language-powershell"  
[!INCLUDE [functions-add-output-binding-powershell](../../includes/functions-add-output-binding-powershell.md)]  
::: zone-end

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library-code](../../includes/functions-add-storage-binding-csharp-library-code.md)]
::: zone-end 

Zwróć uwagę, że *nie* musisz pisać żadnego kodu do uwierzytelniania, pobieranie odwołania do kolejki lub zapisywanie danych. Wszystkie te zadania integracji są wygodnie obsługiwane w środowisku uruchomieniowym Azure Functions i powiązania danych wyjściowych kolejki.

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

[!INCLUDE [functions-extension-bundles-info](../../includes/functions-extension-bundles-info.md)]

## <a name="view-the-message-in-the-azure-storage-queue"></a>Wyświetlanie komunikatu w kolejce usługi Azure Storage

Kolejkę można wyświetlić w [Azure Portal](../storage/queues/storage-quickstart-queues-portal.md) lub [Eksplorator usługi Microsoft Azure Storage](https://storageexplorer.com/). Możesz również wyświetlić kolejkę w interfejsie wiersza polecenia platformy Azure, zgodnie z opisem w następujących krokach:

1. Otwórz plik *Local. JSON* projektu funkcji i skopiuj wartość parametrów połączenia. W terminalu lub w oknie polecenia Uruchom następujące polecenie, aby utworzyć zmienną środowiskową o nazwie `AZURE_STORAGE_CONNECTION_STRING`, wklejając określone parametry połączenia zamiast `<MY_CONNECTION_STRING>`. (Ta zmienna środowiskowa oznacza, że nie trzeba podawać parametrów połączenia dla każdego kolejnego polecenia przy użyciu argumentu `--connection-string`).

    # <a name="bash"></a>[bash](#tab/bash)
    
    ```bash
    AZURE_STORAGE_CONNECTION_STRING="<MY_CONNECTION_STRING>"
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    $env:AZURE_STORAGE_CONNECTION_STRING = "<MY_CONNECTION_STRING>"
    ```
    
    # <a name="cmd"></a>[Cmd](#tab/cmd)
    
    ```cmd
    set AZURE_STORAGE_CONNECTION_STRING="<MY_CONNECTION_STRING>"
    ```
    
    ---
    
1. Obowiązkowe Użyj [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) polecenia, aby wyświetlić kolejki magazynu na Twoim koncie. Dane wyjściowe tego polecenia powinny zawierać kolejkę o nazwie `outqueue`, która została utworzona, gdy funkcja zapisała swój pierwszy komunikat do tej kolejki.
    
    ```azure-cli
    az storage queue list --output tsv
    ```

1. Użyj [`az storage message get`](/cli/azure/storage/message#az-storage-message-get) polecenie, aby odczytać komunikat z tej kolejki, która powinna być pierwszą nazwą użytą podczas testowania funkcji wcześniej. Polecenie odczytuje i usuwa pierwszy komunikat z kolejki. 

    # <a name="bash"></a>[bash](#tab/bash)
    
    ```bash
    echo `echo $(az storage message get --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($(az storage message get --queue-name outqueue -o tsv --query '[].{Message:content}')))
    ```
    
    # <a name="cmd"></a>[Cmd](#tab/cmd)
    
    ```cmd
    az storage message get --queue-name outqueue -o tsv --query [].{Message:content} > %TEMP%out.b64 && certutil -decode -f %TEMP%out.b64 %TEMP%out.txt > NUL && type %TEMP%out.txt && del %TEMP%out.b64 %TEMP%out.txt /q
    ```

    Ten skrypt używa narzędzia Certutil do zdekodowania kolekcji komunikatów kodowanej algorytmem Base64 z lokalnego pliku tymczasowego. Jeśli nie ma żadnych danych wyjściowych, spróbuj usunąć `> NUL` ze skryptu, aby zatrzymać pomijanie danych wyjściowych narzędzia Certutil w przypadku wystąpienia błędu. 
    
    ---
    
    Ponieważ treść komunikatu jest przechowywana [zakodowana w formacie base64](functions-bindings-storage-queue-trigger.md#encoding), komunikat musi zostać zdekodowany przed wyświetleniem. Po wykonaniu `az storage message get`wiadomość zostanie usunięta z kolejki. Jeśli w `outqueue`wystąpił tylko jeden komunikat, nie będzie można pobrać komunikatu po drugim uruchomieniu tego polecenia, a zamiast tego wystąpi błąd.

## <a name="redeploy-the-project-to-azure"></a>Ponowne wdrażanie projektu na platformie Azure

Po sprawdzeniu, czy funkcja zapisała komunikat do kolejki usługi Azure Storage, można ponownie wdrożyć projekt w celu zaktualizowania punktu końcowego uruchomionego na platformie Azure.

1. W folderze *LocalFunctionsProj* użyj polecenia [`func azure functionapp publish`](functions-run-local.md#project-file-deployment) , aby ponownie wdrożyć projekt, zastępując`<APP_NAME>` nazwą aplikacji.

    ```
    func azure functionapp publish <APP_NAME>
    ```
    
1. Tak jak w poprzednim przewodniku Szybki Start, użyj przeglądarki lub zapełnienia, aby przetestować ponownie wdrożoną funkcję.

    # <a name="browser"></a>[Przeglądarka](#tab/browser)
    
    Skopiuj pełny **adres URL Wywołaj** pokazany w danych wyjściowych polecenia Publikuj na pasku adresu przeglądarki, dołączając parametr zapytania `&name=Functions`. Przeglądarka powinna wyświetlać podobne dane wyjściowe, jak w przypadku lokalnego uruchomienia funkcji.

    ![Dane wyjściowe funkcji są uruchamiane na platformie Azure w przeglądarce](./media/functions-add-output-binding-storage-queue-cli/function-test-cloud-browser.png)

    # <a name="curl"></a>[odsłon](#tab/curl)
    
    Uruchom [`curl`](https://curl.haxx.se/) przy użyciu **adresu URL wywołania**, dołączając `&name=Functions`parametru. Danymi wyjściowymi polecenia powinien być tekst "Hello Functions".
    
    ![Dane wyjściowe funkcji są uruchamiane na platformie Azure przy użyciu zwinięcia](./media/functions-add-output-binding-storage-queue-cli/function-test-cloud-curl.png)

    --- 

1. Sprawdź ponownie kolejkę magazynu, zgodnie z opisem w poprzedniej sekcji, aby sprawdzić, czy zawiera ona nowy komunikat zapisany w kolejce.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po zakończeniu Użyj następującego polecenia, aby usunąć grupę zasobów i wszystkie zawarte w niej zasoby, aby uniknąć ponoszenia dalszych kosztów.

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```

## <a name="next-steps"></a>Następne kroki

Została zaktualizowana funkcja wyzwalana przez protokół HTTP w celu zapisania danych w kolejce magazynu. Teraz można dowiedzieć się więcej na temat opracowywania funkcji z poziomu wiersza polecenia przy użyciu narzędzi podstawowych i interfejsu CLI platformy Azure:

+ [Pracuj z Azure Functions Core Tools](functions-run-local.md)  

::: zone pivot="programming-language-csharp"  
+ [Przykłady kompletnych projektów funkcji w C# ](/samples/browse/?products=azure-functions&languages=csharp).

+ [Dokumentacja C# dla deweloperów Azure Functions](functions-dotnet-class-library.md)  
::: zone-end 
::: zone pivot="programming-language-javascript"  
+ [Przykłady kompletnych projektów funkcji w języku JavaScript](/samples/browse/?products=azure-functions&languages=javascript).

+ [Przewodnik dla deweloperów Azure Functions JavaScript](functions-reference-node.md)  
::: zone-end  
::: zone pivot="programming-language-typescript"  
+ [Przykłady całych projektów funkcji w języku TypeScript](/samples/browse/?products=azure-functions&languages=typescript).

+ [Azure Functions przewodnik dewelopera języka TypeScript](functions-reference-node.md#typescript)  
::: zone-end  
::: zone pivot="programming-language-python"  
+ [Przykłady kompletnych projektów funkcji w języku Python](/samples/browse/?products=azure-functions&languages=python).

+ [Przewodnik dewelopera w języku Python Azure Functions](functions-reference-python.md)  
::: zone-end  
::: zone pivot="programming-language-powershell"  
+ [Przykłady kompletnych projektów funkcji w programie PowerShell](/samples/browse/?products=azure-functions&languages=azurepowershell).

+ [Przewodnik dewelopera programu Azure Functions PowerShell](functions-reference-powershell.md) 
::: zone-end
+ [Azure Functions wyzwalacze i powiązania](functions-triggers-bindings.md)

+ [Strona cennika funkcji](https://azure.microsoft.com/pricing/details/functions/)

+ [Szacowanie kosztów planu zużycia](functions-consumption-costs.md) 
