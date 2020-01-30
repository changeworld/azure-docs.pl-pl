---
title: Łączenie funkcji z usługą Azure Storage przy użyciu Visual Studio Code
description: Dowiedz się, jak dodać powiązanie danych wyjściowych, aby połączyć funkcje z kolejką usługi Azure Storage przy użyciu Visual Studio Code.
ms.date: 06/25/2019
ms.topic: quickstart
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: 5b7d7be7854a216b7cb7b610ea6d51fdc496a93f
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2020
ms.locfileid: "76845661"
---
# <a name="connect-functions-to-azure-storage-using-visual-studio-code"></a>Łączenie funkcji z usługą Azure Storage przy użyciu Visual Studio Code

[!INCLUDE [functions-add-storage-binding-intro](../../includes/functions-add-storage-binding-intro.md)]

W tym artykule pokazano, jak za pomocą Visual Studio Code połączyć funkcję utworzoną w [poprzednim artykule szybki start](functions-create-first-function-vs-code.md) z usługą Azure Storage. Powiązanie danych wyjściowych dodawane do tej funkcji zapisuje dane z żądania HTTP do wiadomości w kolejce usługi Azure queue storage. 

Większość powiązań wymaga przechowywanych parametrów połączenia używanych przez funkcje do uzyskiwania dostępu do usługi powiązanej. Aby ułatwić sobie korzystanie z konta magazynu utworzonego za pomocą aplikacji funkcji programu. Połączenie z tym kontem jest już przechowywane w ustawieniu aplikacji o nazwie `AzureWebJobsStorage`.  

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem pracy z tym artykułem należy spełnić następujące wymagania:

* Zainstaluj [rozszerzenie usługi Azure Storage dla Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage).

* Zainstaluj [Eksplorator usługi Azure Storage](https://storageexplorer.com/). Eksplorator usługi Storage jest narzędziem używanym do badania komunikatów w kolejce generowanych przez powiązanie danych wyjściowych. Eksplorator usługi Storage jest obsługiwana w systemach operacyjnych macOS, Windows i Linux.

::: zone pivot="programming-language-csharp"
* Zainstaluj [narzędzia interfejs wiersza polecenia platformy .NET Core](https://docs.microsoft.com/dotnet/core/tools/?tabs=netcore2x).
::: zone-end

* Wykonaj kroki opisane w [części 1 Visual Studio Code szybki start](functions-create-first-function-vs-code.md). 

W tym artykule przyjęto założenie, że użytkownik jest już zalogowany do subskrypcji platformy Azure z Visual Studio Code. Możesz się zalogować, uruchamiając `Azure: Sign In` z palety poleceń. 

## <a name="download-the-function-app-settings"></a>Pobierz ustawienia aplikacji funkcji

W [poprzednim artykule szybki start](functions-create-first-function-vs-code.md)utworzono aplikację funkcji na platformie Azure wraz z wymaganym kontem magazynu. Parametry połączenia dla tego konta są bezpiecznie przechowywane w ustawieniach aplikacji na platformie Azure. W tym artykule opisano pisanie komunikatów w kolejce magazynu w ramach tego samego konta. Aby nawiązać połączenie z kontem magazynu podczas lokalnego uruchamiania funkcji, musisz pobrać ustawienia aplikacji do pliku Local. Settings. JSON. 

1. Naciśnij klawisz F1, aby otworzyć paletę poleceń, a następnie wyszukaj i uruchom polecenie `Azure Functions: Download Remote Settings....`. 

1. Wybierz aplikację funkcji utworzoną w poprzednim artykule. Wybierz opcję **tak, aby** zastąpić istniejące ustawienia lokalne. 

    > [!IMPORTANT]  
    > Ponieważ zawiera wpisy tajne, plik Local. Settings. JSON nigdy nie jest publikowany i jest wykluczony z kontroli źródła.

1. Skopiuj wartość `AzureWebJobsStorage`, która jest kluczem dla wartości parametrów połączenia z kontem magazynu. To połączenie służy do sprawdzania, czy powiązanie danych wyjściowych działa zgodnie z oczekiwaniami.

## <a name="register-binding-extensions"></a>Rejestrowanie rozszerzeń do wiązania

Ze względu na to, że korzystasz z powiązania danych wyjściowych usługi queue storage, przed uruchomieniem projektu musisz mieć zainstalowane rozszerzenie powiązania magazynu. 

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell"

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

::: zone-end

::: zone pivot="programming-language-csharp"

Z wyjątkiem wyzwalaczy HTTP i Timer, powiązania są implementowane jako pakiety rozszerzeń. Uruchom następujące polecenie [dotnet Add Package](/dotnet/core/tools/dotnet-add-package) w oknie terminalu, aby dodać pakiet rozszerzenia magazynu do projektu.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
```

::: zone-end

Teraz można dodać powiązanie danych wyjściowych magazynu do projektu.

## <a name="add-an-output-binding"></a>Dodawanie powiązania danych wyjściowych

W funkcjach każdy typ powiązania wymaga `direction`, `type`i unikatowy `name` do zdefiniowania w pliku Function. JSON. Sposób definiowania tych atrybutów zależy od języka aplikacji funkcji.

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell"

[!INCLUDE [functions-add-output-binding-json](../../includes/functions-add-output-binding-json.md)]

::: zone-end

::: zone pivot="programming-language-csharp"

[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]

::: zone-end

## <a name="add-code-that-uses-the-output-binding"></a>Dodawanie kodu korzystającego z powiązania danych wyjściowych

Po zdefiniowaniu powiązania można użyć `name` powiązania, aby uzyskać do niego dostęp jako atrybut w sygnaturze funkcji. Za pomocą powiązania danych wyjściowych nie trzeba używać kodu zestawu SDK usługi Azure Storage do uwierzytelniania, uzyskiwania odwołania do kolejki lub zapisywania danych. Te zadania są wykonywane za pomocą środowiska uruchomieniowego usługi Functions i powiązania danych wyjściowych kolejki.

::: zone pivot="programming-language-javascript"

[!INCLUDE [functions-add-output-binding-js](../../includes/functions-add-output-binding-js.md)]

::: zone-end

::: zone pivot="programming-language-typescript"

Dodaj kod, który używa `msg` obiektu powiązania danych wyjściowych na `context.bindings`, aby utworzyć komunikat w kolejce. Dodaj ten kod przed instrukcją `context.res`.

```typescript
// Add a message to the Storage queue.
context.bindings.msg = "Name passed to the function: " + name;
```

W tym momencie funkcja powinna wyglądać w następujący sposób:

```javascript
import { AzureFunction, Context, HttpRequest } from "@azure/functions"

const httpTrigger: AzureFunction = async function (context: Context, req: HttpRequest): Promise<void> {
    context.log('HTTP trigger function processed a request.');
    const name = (req.query.name || (req.body && req.body.name));

    if (name) {
        // Add a message to the Storage queue.
        context.bindings.msg = "Name passed to the function: " + name; 
        // Send a "hello" response.
        context.res = {
            // status: 200, /* Defaults to 200 */
            body: "Hello " + (req.query.name || req.body.name)
        };
    }
    else {
        context.res = {
            status: 400,
            body: "Please pass a name on the query string or in the request body"
        };
    }
};

export default httpTrigger;
```

::: zone-end

::: zone pivot="programming-language-powershell"

Dodaj kod, który używa polecenia cmdlet `Push-OutputBinding`, aby napisać tekst do kolejki przy użyciu powiązania danych wyjściowych `msg`. Dodaj ten kod przed ustawieniem stanu OK w instrukcji `if`.

```powershell
# Write the $name value to the queue.
$outputMsg = "Name passed to the function: $name"
Push-OutputBinding -name msg -Value $outputMsg
```

W tym momencie funkcja powinna wyglądać w następujący sposób:

```powershell
using namespace System.Net

# Input bindings are passed in via param block.
param($Request, $TriggerMetadata)

# Write to the Azure Functions log stream.
Write-Host "PowerShell HTTP trigger function processed a request."

# Interact with query parameters or the body of the request.
$name = $Request.Query.Name
if (-not $name) {
    $name = $Request.Body.Name
}

if ($name) {
    # Write the $name value to the queue.
    $outputMsg = "Name passed to the function: $name"
    Push-OutputBinding -name msg -Value $outputMsg

    $status = [HttpStatusCode]::OK
    $body = "Hello $name"
}
else {
    $status = [HttpStatusCode]::BadRequest
    $body = "Please pass a name on the query string or in the request body."
}

# Associate values to output bindings by calling 'Push-OutputBinding'.
Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = $status
    Body = $body
})
```

::: zone-end

::: zone pivot="programming-language-python"

[!INCLUDE [functions-add-output-binding-python](../../includes/functions-add-output-binding-python.md)]

::: zone-end

::: zone pivot="programming-language-csharp"

[!INCLUDE [functions-add-storage-binding-csharp-library-code](../../includes/functions-add-storage-binding-csharp-library-code.md)]

::: zone-end

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-python"

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

::: zone-end

::: zone pivot="programming-language-powershell"

[!INCLUDE [functions-run-function-test-local-vs-code-ps](../../includes/functions-run-function-test-local-vs-code-ps.md)]

::: zone-end

Nowa kolejka o nazwie **requeue** została utworzona na koncie magazynu przez środowisko uruchomieniowe funkcji, gdy zostanie użyte powiązanie danych wyjściowych. Użyjesz Eksplorator usługi Storage, aby sprawdzić, czy kolejka została utworzona wraz z nową wiadomością.

### <a name="connect-storage-explorer-to-your-account"></a>Łączenie Eksploratora usługi Storage z kontem

Pomiń tę sekcję, jeśli już zainstalowano Eksplorator usługi Azure Storage i połączono ją z kontem platformy Azure.

1. Uruchom narzędzie [Azure Storage Explorer] , wybierz ikonę Połącz po lewej stronie, a następnie wybierz pozycję **Dodaj konto**.

    ![Dodaj konto platformy Azure do Eksplorator usługi Microsoft Azure Storage](./media/functions-add-output-binding-storage-queue-vs-code/storage-explorer-add-account.png)

1. W oknie dialogowym **Połącz** wybierz pozycję **Dodaj konto platformy**Azure, wybierz **środowisko platformy Azure**, a następnie wybierz pozycję **Zaloguj się...** . 

    ![Zaloguj się do swojego konta platformy Azure](./media/functions-add-output-binding-storage-queue-vs-code/storage-explorer-connect-azure-account.png)

Po pomyślnym zalogowaniu się do konta zobaczysz wszystkie subskrypcje platformy Azure skojarzone z Twoim kontem.

### <a name="examine-the-output-queue"></a>Sprawdzanie kolejki wyjściowej

1. W Visual Studio Code naciśnij klawisz F1, aby otworzyć paletę poleceń, a następnie wyszukaj i uruchom polecenie `Azure Storage: Open in Storage Explorer` a następnie wybierz nazwę konta magazynu. Twoje konto magazynu zostanie otwarte w Eksplorator usługi Azure Storage.  

1. Rozwiń węzeł **Kolejki**, a następnie wybierz kolejkę o nazwie **outqueue**. 

   Kolejka zawiera komunikat utworzony za pomocą powiązania danych wyjściowych kolejki po uruchomieniu funkcji wyzwalanej przez protokół HTTP. Jeśli funkcja została wywołana przy użyciu domyślnego elementu `name` o wartości *Azure*, komunikat w kolejce to *Nazwa przekazana do funkcji: Azure*.

    ![Komunikat w kolejce wyświetlany w Eksplorator usługi Azure Storage](./media/functions-add-output-binding-storage-queue-vs-code/function-queue-storage-output-view-queue.png)

1. Uruchom ponownie funkcję, Wyślij kolejne żądanie, a w kolejce zobaczysz nowy komunikat.  

Teraz można ponownie opublikować zaktualizowaną aplikację funkcji na platformie Azure.

## <a name="redeploy-and-verify-the-updated-app"></a>Wdróż ponownie i sprawdź zaktualizowaną aplikację

1. W Visual Studio Code naciśnij klawisz F1, aby otworzyć paletę poleceń. W palecie poleceń Wyszukaj i wybierz `Azure Functions: Deploy to function app...`.

1. Wybierz aplikację funkcji utworzoną w pierwszym artykule. Ponieważ wdrażasz ponownie projekt w tej samej aplikacji, wybierz pozycję **Wdróż** , aby odrzucić ostrzeżenie o zastępowaniu plików.

1. Po zakończeniu wdrażania możesz ponownie użyć zapełnienia lub przeglądarki, aby przetestować ponownie wdrożoną funkcję. Tak jak wcześniej, należy dołączyć ciąg zapytania `&name=<yourname>` do adresu URL, jak w poniższym przykładzie:

    ```bash
    curl https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....&name=<yourname>
    ```

1. Ponownie [Wyświetl komunikat w kolejce magazynu](#examine-the-output-queue) , aby sprawdzić, czy powiązanie danych wyjściowych ponownie generuje nowy komunikat w kolejce.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

*Zasoby* na platformie Azure dotyczą aplikacji funkcji, funkcji, kont magazynu i tak dalej. Zasoby są grupowane w ramach *grup zasobów*, a wszystkie elementy w grupie możesz usunąć, usuwając tę grupę.

Aby ukończyć te przewodniki Szybki start, zostały utworzone zasoby. Za te zasoby może zostać naliczona opłata — zależy to od Twojego [stanu konta](https://azure.microsoft.com/account/) i [cennika usług](https://azure.microsoft.com/pricing/). Jeśli nie potrzebujesz już tych zasobów, oto jak możesz je usunąć:

[!INCLUDE [functions-cleanup-resources-vs-code.md](../../includes/functions-cleanup-resources-vs-code.md)]

## <a name="next-steps"></a>Następne kroki

Została zaktualizowana funkcja wyzwalana przez protokół HTTP w celu zapisania danych w kolejce magazynu. Następnie można dowiedzieć się więcej na temat opracowywania funkcji przy użyciu Visual Studio Code:

> [!div class="nextstepaction"]
> [Opracowywanie Azure Functions przy użyciu Visual Studio Code](functions-develop-vs-code.md)

[Azure Storage Explorer]: https://storageexplorer.com/
