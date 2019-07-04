---
title: Łączenie funkcji do usługi Azure Storage przy użyciu programu Visual Studio Code
description: Dowiedz się, jak dodać powiązanie danych wyjściowych, aby połączyć Twoje funkcje do kolejki usługi Azure Storage przy użyciu programu Visual Studio Code.
author: ggailey777
ms.author: glenga
ms.date: 06/25/2019
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc
manager: jeconnoc
ms.openlocfilehash: b207064f691391af2c180c7a6ab03e42ed79fcb6
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67450996"
---
# <a name="connect-functions-to-azure-storage-using-visual-studio-code"></a>Łączenie funkcji do usługi Azure Storage przy użyciu programu Visual Studio Code

Usługa Azure Functions umożliwia łączenie funkcji do usług platformy Azure i innych zasobów, bez konieczności pisania kodu integracji. Te *powiązania*, które reprezentują dane wejściowe i dane wyjściowe są zadeklarowane w ramach definicji funkcji. Dane z powiązań podano funkcji jako parametrów. Wyzwalacz jest specjalny typ powiązania danych wejściowych. Gdy funkcja ma tylko jeden wyzwalacz, ona mają wielu danych wejściowych i wyjściowych powiązania. Aby dowiedzieć się więcej, zobacz [pojęcia powiązania i Wyzwalacze usługi Azure Functions](functions-triggers-bindings.md).

W tym artykule pokazano, jak używać programu Visual Studio Code do łączenia funkcji został utworzony w [poprzednim artykule Szybki Start](functions-create-first-function-vs-code.md) do usługi Azure Storage. Powiązania danych wyjściowych, które możesz dodać do tej funkcji zapisuje dane z żądania HTTP do wiadomości w kolejce usługi Azure Queue storage. 

Większość powiązania wymaga parametrów połączenia przechowywanych korzystającą z funkcji w celu uzyskania dostępu do powiązanej usługi. Aby ułatwić, należy użyć konta magazynu, który został utworzony za pomocą aplikacji funkcji. Połączenie z tym kontem jest już przechowywany w aplikacji, ustawienie o nazwie `AzureWebJobsStorage`.  

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego artykułu, musi spełniać następujące wymagania:

* Zainstaluj [rozszerzenia Azure Storage dla programu Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage).
* Zainstaluj [Eksplorator usługi Azure Storage](https://storageexplorer.com/). Eksplorator usługi Storage to narzędzie, którego będziesz używać do sprawdzania komunikatów w kolejce wygenerowane za pomocą powiązania danych wyjściowych. Eksplorator usługi Storage jest obsługiwana w systemach macOS, Windows i systemy operacyjne oparte na systemie Linux.
* Zainstaluj [narzędzi interfejsu wiersza polecenia platformy .NET Core](https://docs.microsoft.com/dotnet/core/tools/?tabs=netcore2x) (C# wyłącznie dla projektów).
* Wykonaj kroki [części 1 tego przewodnika Szybki Start z programu Visual Studio Code](functions-create-first-function-vs-code.md). 

W tym artykule założono, że już zalogowano Cię do subskrypcji platformy Azure z programu Visual Studio Code. Możesz zalogować się, uruchamiając `Azure: Sign In` z palety poleceń. 

## <a name="download-the-function-app-settings"></a>Pobieranie ustawień aplikacji funkcji

W [poprzednim artykule Szybki Start](functions-create-first-function-vs-code.md), utworzyć aplikację funkcji na platformie Azure oraz wymagane konta magazynu. Parametry połączenia dla tego konta są bezpiecznie przechowywane w ustawieniach aplikacji na platformie Azure. W tym artykule Zapisz komunikaty do kolejki na tym samym koncie magazynu. Aby połączyć się z kontem magazynu podczas uruchamiania funkcji lokalnie, możesz pobrać ustawień aplikacji do pliku local.settings.json. 

1. Naciśnij klawisz F1, aby otworzyć paletę poleceń, a następnie wyszukaj i uruchom polecenie `Azure Functions: Download Remote Settings....`. 

1. Wybierz aplikację funkcji, utworzony w poprzednim artykule. Wybierz **tak na wszystko** zastąpienie istniejących ustawień lokalnych. 

    > [!IMPORTANT]  
    > Ponieważ zawiera ona wpisów tajnych oraz ich w pliku local.settings.json nigdy nie zostanie opublikowany i jest wykluczony z kontroli źródła.

1. Skopiuj wartość `AzureWebJobsStorage`, czyli na klucz wartość parametrów połączenia konta magazynu. To połączenie umożliwia Sprawdź, czy powiązania danych wyjściowych działa zgodnie z oczekiwaniami.

## <a name="register-binding-extensions"></a>Rejestrowanie rozszerzeń do wiązania

Ponieważ używasz danych wyjściowych kolejki magazynu powiązania musi mieć rozszerzenie powiązania magazynu, które są zainstalowane przed rozpoczęciem projektu. 

### <a name="javascript"></a>JavaScript

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

### <a name="c-class-library"></a>C\# biblioteki klas

Z wyjątkiem wyzwalaczy HTTP oraz czasomierzem powiązania są implementowane jako pakiety rozszerzeń. Uruchom następujące polecenie [dotnet Dodaj pakiet](/dotnet/core/tools/dotnet-add-package) polecenia w oknie terminalu, aby dodać pakiet rozszerzenia magazynu do projektu.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
```

Teraz możesz dodać magazyn powiązania danych wyjściowych usługi do projektu.

## <a name="add-an-output-binding"></a>Dodawanie powiązania danych wyjściowych

W przypadku funkcji wymaga każdego typu powiązania `direction`, `type`oraz unikatową `name` zdefiniowanych w pliku function.json. Sposób definiowania tych atrybutów, zależy od języka aplikacji funkcji.

### <a name="javascript"></a>JavaScript

Atrybutów powiązania są definiowane bezpośrednio w pliku function.json. W zależności od typu powiązania dodatkowe właściwości mogą być wymagane. [Konfiguracja danych wyjściowych kolejki](functions-bindings-storage-queue.md#output---configuration) opisuje pól wymaganych dla powiązania kolejki usługi Azure Storage. Rozszerzenie można łatwo dodać powiązania do pliku function.json. 

Aby utworzyć powiązanie, kliknij prawym przyciskiem myszy (Ctrl + kliknięcie w systemie macOS) `function.json` plików w folderze HttpTrigger, a następnie wybierz **Dodaj powiązanie...** . Postępuj zgodnie z monitami, aby zdefiniować następujące właściwości powiązania dla nowego powiązania:

| Monit | Wartość | Opis |
| -------- | ----- | ----------- |
| **Wybierz kierunek powiązania** | `out` | Powiązanie to powiązanie danych wyjściowych. |
| **Wybierz powiązanie kierunku...** | `Azure Queue Storage` | Powiązanie to powiązanie kolejki usługi Azure Storage. |
| **Nazwa służąca do identyfikacji tego powiązania w kodzie** | `msg` | Nazwa identyfikująca parametr wiązania, do którego odwołuje się kod. |
| **Kolejka, do którego będą wysyłane wiadomości** | `outqueue` | Nazwa kolejki, która zapisuje powiązania. Gdy *queueName* nie istnieje, tworzy go przy pierwszym użyciu powiązania. |
| **Wybierz ustawienie z "local.setting.json"** | `AzureWebJobsStorage` | Nazwa ustawienia aplikacji zawierającego parametry połączenia dla konta magazynu. `AzureWebJobsStorage` Zawiera parametry połączenia dla konta magazynu, które utworzono za pomocą aplikacji funkcji. |

Powiązanie jest dodawany do `bindings` tablicy w pliku function.json powinien teraz wyglądać podobnie jak w poniższym przykładzie:

```json
{
   ...

  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "msg",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

### <a name="c-class-library"></a>C\# biblioteki klas

W C# projekt biblioteki klas, powiązania są definiowane jako atrybutów powiązania dla metody funkcji. Plik function.json jest następnie automatycznie wygenerowany na podstawie tych atrybutów.

Otwórz plik projektu HttpTrigger.cs i dodaj następującą `using` instrukcji:

```cs
using Microsoft.Azure.WebJobs.Extensions.Storage;
```

Dodaj następujący parametr, aby `Run` definicję metody:

```cs
[Queue("outqueue"),StorageAccount("AzureWebJobsStorage")] ICollector<string> msg
```

`msg` Parametr `ICollector<T>` typ, który reprezentuje kolekcję wiadomości, które są zapisywane do pliku wyjściowego powiązania, kiedy funkcja kończy. W tym przypadku dane wyjściowe to kolejki magazynu o nazwie `outqueue`. Parametry połączenia dla konta magazynu jest ustawiana przez `StorageAccountAttribute`. Ten atrybut wskazuje ustawienie, które zawiera parametry połączenia konta magazynu i można zastosować na poziomie klasy, metody lub parametru. W takim przypadku można pominąć `StorageAccountAttribute` ponieważ domyślne konto magazynu jest już używany.

Uruchom definicję metody powinna teraz wyglądać podobnie do poniższych:  

```cs
[FunctionName("HttpTrigger")]
public static async Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req, 
    [Queue("outqueue"),StorageAccount("AzureWebJobsStorage")] ICollector<string> msg, ILogger log)
```

## <a name="add-code-that-uses-the-output-binding"></a>Dodawanie kodu korzystającego z powiązania danych wyjściowych

Po zdefiniowaniu powiązania, można użyć `name` powiązania dostępu do niego jako atrybut w sygnaturze funkcji. Za pomocą powiązania danych wyjściowych, nie trzeba użyć kodu zestawu SDK usługi Azure Storage do uwierzytelniania, pobieranie odwołania do kolejki lub zapisywania danych. Funkcje środowiska uruchomieniowego i kolejki danych wyjściowych powiązania wykonywanie tych zadań za Ciebie.

### <a name="javascript"></a>JavaScript

Dodaj kod używający `msg` obiekt powiązania danych wyjściowych na `context.bindings` do utworzenia komunikatu w kolejce. Dodaj ten kod przed instrukcją `context.res`.

```javascript
// Add a message to the Storage queue.
context.bindings.msg = "Name passed to the function: " + 
(req.query.name || req.body.name);
```

W tym momencie funkcja powinna wyglądać następująco:

```javascript
module.exports = async function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    if (req.query.name || (req.body && req.body.name)) {
        // Add a message to the Storage queue.
        context.bindings.msg = "Name passed to the function: " + 
        (req.query.name || req.body.name);
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
```

### <a name="c"></a>C\#

Dodaj kod używający `msg` obiekt powiązania danych wyjściowych do utworzenia komunikatu w kolejce. Dodaj ten kod przed powrotem z metody.

```cs
if (!string.IsNullOrEmpty(name))
{
    // Add a message to the output collection.
    msg.Add(string.Format("Name passed to the function: {0}", name));
}
```

W tym momencie funkcja powinna wyglądać następująco:

```cs
[FunctionName("HttpTrigger")]
public static async Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req, 
    [Queue("outqueue"),StorageAccount("AzureWebJobsStorage")] ICollector<string> msg, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    string name = req.Query["name"];

    string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
    dynamic data = JsonConvert.DeserializeObject(requestBody);
    name = name ?? data?.name;

    if (!string.IsNullOrEmpty(name))
    {
        // Add a message to the output collection.
        msg.Add(string.Format("Name passed to the function: {0}", name));
    }
    return name != null
        ? (ActionResult)new OkObjectResult($"Hello, {name}")
        : new BadRequestObjectResult("Please pass a name on the query string or in the request body");
}
```

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

Środowisko uruchomieniowe funkcji utworzy nową kolejkę o nazwie **outqueue** w koncie magazynu przy pierwszym użyciu powiązania danych wyjściowych. Eksplorator usługi Storage umożliwiającym zweryfikowanie, czy kolejka została utworzona wraz z nową wiadomość.

### <a name="connect-storage-explorer-to-your-account"></a>Łączenie Eksploratora usługi Storage z kontem

Pomiń tę sekcję, jeśli już zainstalowano program Azure Storage Explorer i połączono go z kontem platformy Azure.

1. Uruchom [Azure Storage Explorer] narzędzi, wybierz ikonę połączenia po lewej stronie i wybierz **Dodaj konto**.

    ![Dodaj platformę Azure konto Microsoft Azure Storage Explorer](./media/functions-add-output-binding-storage-queue-vs-code/storage-explorer-add-account.png)

1. W **Connect** okno dialogowe, wybierz **Dodaj konto platformy Azure**, wybierz swoje **środowiska platformy Azure**i wybierz **Zaloguj się...** . 

    ![Zaloguj się do swojego konta platformy Azure](./media/functions-add-output-binding-storage-queue-vs-code/storage-explorer-connect-azure-account.png)

Po pomyślnym zalogowaniu do swojego konta, możesz zobaczyć wszystkich subskrypcji platformy Azure skojarzony z Twoim kontem.

### <a name="examine-the-output-queue"></a>Sprawdzanie kolejki wyjściowej

1. W programie Visual Studio Code, naciśnij klawisz F1, aby otworzyć paletę poleceń, a następnie wyszukaj i uruchom polecenie `Azure Storage: Open in Storage Explorer` i wybierz nazwę konta magazynu. Konto magazynu zostanie otwarty w Eksploratorze usługi Azure Storage.  

1. Rozwiń węzeł **Kolejki**, a następnie wybierz kolejkę o nazwie **outqueue**. 

   Kolejka zawiera komunikat utworzony za pomocą powiązania danych wyjściowych kolejki po uruchomieniu funkcji wyzwalanej przez protokół HTTP. Jeśli funkcja została wywołana przy użyciu domyślnego elementu `name` o wartości *Azure*, komunikat w kolejce to *Nazwa przekazana do funkcji: Azure*.

    ![Komunikaty w kolejce wyświetlane w Eksploratorze usługi Azure Storage](./media/functions-add-output-binding-storage-queue-vs-code/function-queue-storage-output-view-queue.png)

1. Uruchom funkcję ponownie wysłać następne żądanie i zostanie wyświetlony nowy komunikat w kolejce pojawi.  

Teraz nadszedł czas na ponownym opublikowaniu aplikacji zaktualizowanych funkcji na platformie Azure.

## <a name="redeploy-and-test-the-updated-app"></a>Ponowne wdrażanie i testowanie zaktualizowanej aplikacji

1. W programie Visual Studio Code naciśnij klawisz F1, aby otworzyć paletę poleceń. W palecie poleceń, wyszukiwanie i wybieranie `Azure Functions: Deploy to function app...`.

1. Wybierz aplikację funkcji, utworzonego w pierwszym artykułu. Ponieważ w przypadku ponownego wdrażania projektu do tej samej aplikacji, wybierz **Wdróż** można zignorować to ostrzeżenie o zastąpienie plików.

1. Po zakończeniu wdrażania możesz ponownie użyć Aby przetestować tę funkcję ponownie wdrożonym cURL albo w przeglądarce. Tak jak poprzednio Dołącz ciąg zapytania `&name=<yourname>` do adresu URL, jak w poniższym przykładzie:

    ```bash
    curl https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....&name=<yourname>
    ```

1. Ponownie [wyświetlić wiadomość w kolejce magazynu](#examine-the-output-queue) Aby zweryfikować, że powiązanie danych wyjściowych ponownie generuje nowy komunikat w kolejce.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

*Zasoby* na platformie Azure dotyczą aplikacji funkcji, funkcji, kont magazynu i tak dalej. Zasoby są grupowane w ramach *grup zasobów*, a wszystkie elementy w grupie możesz usunąć, usuwając tę grupę.

Aby ukończyć te przewodniki Szybki start, zostały utworzone zasoby. Za te zasoby może zostać naliczona opłata — zależy to od Twojego [stanu konta](https://azure.microsoft.com/account/) i [cennika usług](https://azure.microsoft.com/pricing/). Jeśli nie potrzebujesz już tych zasobów, oto jak możesz je usunąć:

1. W programie Visual Studio Code naciśnij klawisz F1, aby otworzyć paletę poleceń. W palecie poleceń, wyszukiwanie i wybieranie `Azure Functions: Open in portal`.

1. Wybierz aplikację funkcji, a następnie naciśnij klawisz Enter. Na stronie aplikacji funkcji jest otwarty w [witryny Azure portal](https://portal.azure.com).

1. W **Przegląd** , a następnie wybierz łącze o nazwie w **grupy zasobów**.

    ![Wybierz grupę zasobów do usunięcia ze strony aplikacji funkcji.](./media/functions-add-output-binding-storage-queue-vs-code/functions-app-delete-resource-group.png)

1. Na stronie **Grupa zasobów** zapoznaj się z listą dołączonych zasobów i sprawdź, czy chcesz je usunąć.
 
1. Wybierz pozycję **Usuń grupę zasobów**, a następnie postępuj zgodnie z instrukcjami.

   Usuwanie może potrwać kilka minut. Po jego zakończeniu przez kilka sekund będzie widoczne powiadomienie. Możesz również wybrać ikonę dzwonka w górnej części strony, aby wyświetlić powiadomienie.

## <a name="next-steps"></a>Kolejne kroki

Użytkownik zaktualizował funkcję wyzwalaną przez protokół HTTP, aby zapisać dane w kolejce magazynu. Aby dowiedzieć się więcej na temat tworzenia funkcji, zobacz [Tworzenie usługi Azure Functions przy użyciu programu Visual Studio Code](functions-develop-vs-code.md).

Następnie należy włączyć monitorowanie usługi Application Insights dla aplikacji funkcji:

> [!div class="nextstepaction"]
> [Włączanie integracji usługi Application Insights](functions-monitoring.md#manually-connect-an-app-insights-resource)

[Azure Storage Explorer]: https://storageexplorer.com/
