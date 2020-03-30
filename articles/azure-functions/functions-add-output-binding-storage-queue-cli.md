---
title: Łączenie funkcji platformy Azure z usługą Azure Storage przy użyciu narzędzi wiersza polecenia
description: Dowiedz się, jak połączyć usługę Azure Functions z kolejką usługi Azure Storage, dodając powiązanie danych wyjściowych do projektu wiersza polecenia.
ms.date: 02/07/2020
ms.topic: quickstart
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: 9181caf516d5c2003cfe99b125d2921732cbbb9d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79473391"
---
# <a name="connect-azure-functions-to-azure-storage-using-command-line-tools"></a>Łączenie funkcji platformy Azure z usługą Azure Storage przy użyciu narzędzi wiersza polecenia

W tym artykule można zintegrować kolejkę usługi Azure Storage z funkcją i kontem magazynu utworzonym w [poprzednim przewodniku Szybki start](functions-create-first-azure-function-azure-cli.md). Tej integracji można osiągnąć przy użyciu *powiązania danych wyjściowych,* który zapisuje dane z żądania HTTP do wiadomości w kolejce. Ukończenie tego artykułu nie wiąże się z dodatkowymi kosztami przekraczającymi kilka centów USD z poprzedniego szybkiego startu. Aby dowiedzieć się więcej o powiązaniach, zobacz [Koncepcje wyzwalaczy i powiązań usługi Azure Functions.](functions-triggers-bindings.md)

## <a name="configure-your-local-environment"></a>Konfigurowanie środowiska lokalnego

Przed rozpoczęciem należy ukończyć artykuł [Szybki start: Tworzenie projektu usługi Azure Functions z wiersza polecenia](functions-create-first-azure-function-azure-cli.md). Jeśli już oczyszczone zasoby na końcu tego artykułu, przejdź przez kroki ponownie, aby ponownie utworzyć aplikację funkcji i powiązanych zasobów na platformie Azure.

## <a name="retrieve-the-azure-storage-connection-string"></a>Pobieranie ciągu połączenia usługi Azure Storage

Podczas tworzenia aplikacji funkcji na platformie Azure w poprzednim przewodniku Szybki start utworzono również konto magazynu. Parametry połączenia dla tego konta są bezpiecznie przechowywane w ustawieniach aplikacji na platformie Azure. Pobierając to ustawienie do pliku *local.settings.json,* można użyć tego zapisu połączenia do kolejki magazynu na tym samym koncie podczas lokalnego uruchamiania funkcji. 

1. W katalogu głównym projektu uruchom następujące polecenie, zastępując `<APP_NAME>` nazwą aplikacji funkcji z poprzedniego przewodnika Szybki start. To polecenie zastąpi wszystkie istniejące wartości w pliku.

    ```
    func azure functionapp fetch-app-settings <APP_NAME>
    ```
    
1. Otwórz *local.settings.json* i znajdź `AzureWebJobsStorage`wartość o nazwie , która jest ciągiem połączenia konta magazynu. Nazwy `AzureWebJobsStorage` i ciągu połączenia należy użyć w innych sekcjach tego artykułu.

> [!IMPORTANT]
> Ponieważ *local.settings.json* zawiera wpisy tajne pobrane z platformy Azure, zawsze wyklucz ten plik z kontroli źródła. Plik *.gitignore* utworzony za pomocą projektu funkcji lokalnych domyślnie wyklucza plik.

[!INCLUDE [functions-register-storage-binding-extension-csharp](../../includes/functions-register-storage-binding-extension-csharp.md)]

## <a name="add-an-output-binding-definition-to-the-function"></a>Dodawanie definicji wiązania wyjściowego do funkcji

Chociaż funkcja może mieć tylko jeden wyzwalacz, może mieć wiele powiązań danych wejściowych i wyjściowych, które umożliwiają łączenie się z innymi usługami i zasobami platformy Azure bez pisania niestandardowego kodu integracji. 

::: zone pivot="programming-language-python,programming-language-javascript,programming-language-powershell,programming-language-typescript"  
Deklarujesz te powiązania w pliku *function.json* w folderze funkcji. Z poprzedniego przewodnika Szybki start plik *function.json* w folderze *HttpExample* zawiera dwa powiązania w `bindings` kolekcji:  
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
Każde powiązanie ma co najmniej typ, kierunek i nazwę. W powyższym przykładzie pierwsze powiązanie jest typu `httpTrigger` z kierunkiem `in`. Dla `in` kierunku `name` określa nazwę parametru wejściowego, który jest wysyłany do funkcji, gdy wywoływane przez wyzwalacz.  
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript"  
Drugie powiązanie w kolekcji nosi nazwę `res`. To `http` powiązanie jest`out`powiązaniem danych wyjściowych ( ), który jest używany do zapisu odpowiedzi HTTP. 

Aby zapisać do kolejki usługi Azure Storage `out` z `queue` tej funkcji, należy dodać powiązanie typu o nazwie `msg`, jak pokazano w poniższym kodzie:

:::code language="json" source="~/functions-docs-javascript/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-python"  
Drugie powiązanie w kolekcji `http` jest `out`typu z direction `name` , `$return` w którym to przypadku specjalne wskazuje, że to powiązanie używa funkcji zwraca wartość, a nie dostarczanie parametru wejściowego.

Aby zapisać do kolejki usługi Azure Storage `out` z `queue` tej funkcji, należy dodać powiązanie typu o nazwie `msg`, jak pokazano w poniższym kodzie:

:::code language="json" source="~/functions-docs-python/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-powershell"  
Drugie powiązanie w kolekcji nosi nazwę `res`. To `http` powiązanie jest`out`powiązaniem danych wyjściowych ( ), który jest używany do zapisu odpowiedzi HTTP. 

Aby zapisać do kolejki usługi Azure Storage `out` z `queue` tej funkcji, należy dodać powiązanie typu o nazwie `msg`, jak pokazano w poniższym kodzie:

:::code language="json" source="~/functions-docs-powershell/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-python,programming-language-javascript,programming-language-powershell,programming-language-typescript"  
W takim `msg` przypadku jest nadana funkcji jako argument wyjściowy. W `queue` przypadku typu należy również określić nazwę `queueName` kolejki i podać *nazwę* połączenia usługi Azure Storage (z `connection` *local.settings.json*) w pliku . 
::: zone-end  

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]  
::: zone-end  

Aby uzyskać więcej informacji na temat szczegółów powiązań, zobacz [koncepcje wyzwalaczy i powiązań usługi Azure Functions](functions-triggers-bindings.md) oraz [konfiguracja danych wyjściowych kolejki.](functions-bindings-storage-queue-output.md#configuration)

## <a name="add-code-to-use-the-output-binding"></a>Dodaj kod, aby użyć powiązania danych wyjściowych

Za pomocą powiązania kolejki określonego w *function.json*można teraz `msg` zaktualizować funkcję, aby odbierać parametr wyjściowy i zapisywać wiadomości do kolejki.

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

Należy zauważyć, że *nie* trzeba pisać żadnego kodu do uwierzytelniania, uzyskiwania odwołania do kolejki lub zapisywania danych. Wszystkie te zadania integracji są wygodnie obsługiwane w usłudze Azure Functions powiązania środowiska wykonawczego i danych wyjściowych kolejki.

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

[!INCLUDE [functions-extension-bundles-info](../../includes/functions-extension-bundles-info.md)]

## <a name="view-the-message-in-the-azure-storage-queue"></a>Wyświetlanie wiadomości w kolejce usługi Azure Storage

Kolejkę można wyświetlić w [witrynie Azure portal](../storage/queues/storage-quickstart-queues-portal.md) lub w [Eksploratorze magazynu platformy Microsoft Azure](https://storageexplorer.com/). Kolejkę można również wyświetlić w wierszu polecenia platformy Azure, zgodnie z opisem w następujących krokach:

1. Otwórz plik *local.setting.json* projektu funkcji i skopiuj wartość ciągu połączenia. W oknie terminala lub polecenia uruchom następujące polecenie, `AZURE_STORAGE_CONNECTION_STRING`aby utworzyć zmienną środowiskową `<MY_CONNECTION_STRING>`o nazwie , wklejając określony ciąg połączenia zamiast pliku . (Ta zmienna środowiskowa oznacza, że nie trzeba podawać ciągu `--connection-string` połączenia do każdego kolejnego polecenia przy użyciu argumentu).

    # <a name="bash"></a>[Bash](#tab/bash)
    
    ```bash
    AZURE_STORAGE_CONNECTION_STRING="<MY_CONNECTION_STRING>"
    ```
    
    # <a name="powershell"></a>[Powershell](#tab/powershell)
    
    ```powershell
    $env:AZURE_STORAGE_CONNECTION_STRING = "<MY_CONNECTION_STRING>"
    ```
    
    # <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/cmd)
    
    ```azurecli
    set AZURE_STORAGE_CONNECTION_STRING="<MY_CONNECTION_STRING>"
    ```
    
    ---
    
1. (Opcjonalnie) Użyj [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) polecenia, aby wyświetlić kolejki magazynu na koncie. Dane wyjściowe z tego polecenia `outqueue`powinny zawierać kolejkę o nazwie , która została utworzona, gdy funkcja napisała swój pierwszy komunikat do tej kolejki.
    
    ```azurecli
    az storage queue list --output tsv
    ```

1. Użyj [`az storage message get`](/cli/azure/storage/message#az-storage-message-get) polecenia, aby odczytać wiadomość z tej kolejki, która powinna być pierwszą nazwą używaną podczas testowania funkcji wcześniej. Polecenie odczytuje i usuwa pierwszą wiadomość z kolejki. 

    # <a name="bash"></a>[Bash](#tab/bash)
    
    ```bash
    echo `echo $(az storage message get --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
    ```
    
    # <a name="powershell"></a>[Powershell](#tab/powershell)
    
    ```powershell
    [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($(az storage message get --queue-name outqueue -o tsv --query '[].{Message:content}')))
    ```
    
    # <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/cmd)
    
    ```azurecli
    az storage message get --queue-name outqueue -o tsv --query [].{Message:content} > %TEMP%out.b64 && certutil -decode -f %TEMP%out.b64 %TEMP%out.txt > NUL && type %TEMP%out.txt && del %TEMP%out.b64 %TEMP%out.txt /q
    ```

    Ten skrypt używa certutil do dekodowania kolekcji wiadomości zakodowanych base64 z lokalnego pliku tymczasowego. Jeśli nie ma żadnych danych `> NUL` wyjściowych, spróbuj usunąć ze skryptu, aby zatrzymać pomijanie danych wyjściowych certutil, w przypadku wystąpienia błędu. 
    
    ---
    
    Ponieważ treść wiadomości jest przechowywana [base64 zakodowana,](functions-bindings-storage-queue-trigger.md#encoding)wiadomość musi zostać zdekodowana przed jej wyświetleniem. Po wykonaniu `az storage message get`komunikat zostanie usunięty z kolejki. Jeśli w `outqueue`pliku znajdowała się tylko jedna wiadomość, nie zostanie pobrana wiadomość po uruchomieniu tego polecenia po raz drugi i zamiast tego pojawi się błąd.

## <a name="redeploy-the-project-to-azure"></a>Ponowne wdrożenie projektu na platformie Azure

Teraz, gdy zostały zweryfikowane lokalnie, że funkcja napisał komunikat do kolejki usługi Azure Storage, można ponownie wdrożyć projekt, aby zaktualizować punkt końcowy uruchomiony na platformie Azure.

1. W *localfunctionsProj* folderu [`func azure functionapp publish`](functions-run-local.md#project-file-deployment) użyj polecenia, aby ponownie wdrożyć projekt, zastępując`<APP_NAME>` nazwą aplikacji.

    ```
    func azure functionapp publish <APP_NAME>
    ```
    
1. Podobnie jak w poprzednim przewodniku Szybki start, użyj przeglądarki lub CURL, aby przetestować ponownie wepchaną funkcję.

    # <a name="browser"></a>[Przeglądarka](#tab/browser)
    
    Skopiuj pełny **adres URL invoke** wyświetlany w danych wyjściowych polecenia `&name=Functions`publikowania na pasku adresu przeglądarki, dołączając parametr kwerendy . Przeglądarka powinna wyświetlać podobne dane wyjściowe, jak po uruchomieniu funkcji lokalnie.

    ![Dane wyjściowe funkcji są uruchamiane na platformie Azure w przeglądarce](./media/functions-add-output-binding-storage-queue-cli/function-test-cloud-browser.png)

    # <a name="curl"></a>[Curl](#tab/curl)
    
    Uruchom [`curl`](https://curl.haxx.se/) z **adresem URL invoke**, `&name=Functions`dołączając parametr . Dane wyjściowe polecenia powinny być tekst "Hello Functions".
    
    ![Dane wyjściowe funkcji są uruchamiane na platformie Azure przy użyciu funkcji CURL](./media/functions-add-output-binding-storage-queue-cli/function-test-cloud-curl.png)

    --- 

1. Sprawdź kolejkę magazynu ponownie, zgodnie z opisem w poprzedniej sekcji, aby sprawdzić, czy zawiera nową wiadomość zapisaną w kolejce.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po zakończeniu użyj następującego polecenia, aby usunąć grupę zasobów i wszystkie jej zawarte zasoby, aby uniknąć ponoszenia dalszych kosztów.

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```

## <a name="next-steps"></a>Następne kroki

Zaktualizowano funkcję wyzwalaną http, aby zapisywać dane w kolejce magazynu. Teraz możesz dowiedzieć się więcej o tworzeniu funkcji z wiersza polecenia przy użyciu narzędzi Core Tools i interfejsu wiersza polecenia platformy Azure:

+ [Praca z podstawowymi narzędziami usług Azure Functions](functions-run-local.md)  

::: zone pivot="programming-language-csharp"  
+ [Przykłady kompletnych projektów funkcji w języku C#](/samples/browse/?products=azure-functions&languages=csharp).

+ [Odwołanie do dewelopera usług Azure Functions C#](functions-dotnet-class-library.md)  
::: zone-end 
::: zone pivot="programming-language-javascript"  
+ [Przykłady kompletnych projektów funkcji w języku JavaScript](/samples/browse/?products=azure-functions&languages=javascript).

+ [Przewodnik dla deweloperów języka JavaScript w usłudze Azure Functions](functions-reference-node.md)  
::: zone-end  
::: zone pivot="programming-language-typescript"  
+ [Przykłady kompletnych projektów funkcji w języku TypeScript](/samples/browse/?products=azure-functions&languages=typescript).

+ [Przewodnik dla deweloperów usługi Azure Functions TypeScript](functions-reference-node.md#typescript)  
::: zone-end  
::: zone pivot="programming-language-python"  
+ [Przykłady kompletnych projektów funkcji w pythonie](/samples/browse/?products=azure-functions&languages=python).

+ [Przewodnik dla deweloperów usługi Azure Functions Python](functions-reference-python.md)  
::: zone-end  
::: zone pivot="programming-language-powershell"  
+ [Przykłady kompletnych projektów funkcji w programie PowerShell](/samples/browse/?products=azure-functions&languages=azurepowershell).

+ [Przewodnik dla deweloperów programu Azure Functions PowerShell](functions-reference-powershell.md) 
::: zone-end
+ [Wyzwalacze i powiązania usługi Azure Functions](functions-triggers-bindings.md)

+ [Strona z cennikiem funkcji](https://azure.microsoft.com/pricing/details/functions/)

+ [Szacowanie kosztów planu zużycia](functions-consumption-costs.md) 
