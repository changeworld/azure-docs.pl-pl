---
title: Łączenie funkcji z usługą Azure Storage przy użyciu Visual Studio Code
description: Dowiedz się, jak dodać powiązanie danych wyjściowych, aby połączyć funkcje z kolejką usługi Azure Storage przy użyciu Visual Studio Code.
author: ggailey777
ms.author: glenga
ms.date: 06/25/2019
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc
manager: jeconnoc
ms.openlocfilehash: dfb4abaf3868b76e17fb35f952c4db6bcdf30634
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/03/2019
ms.locfileid: "71838937"
---
# <a name="connect-functions-to-azure-storage-using-visual-studio-code"></a>Łączenie funkcji z usługą Azure Storage przy użyciu Visual Studio Code

Azure Functions umożliwia łączenie funkcji z usługami platformy Azure i innymi zasobami bez konieczności pisania kodu integracji. Te *powiązania*, które reprezentują zarówno dane wejściowe, jak i wyjściowe, są zadeklarowane w definicji funkcji. Dane z powiązań są przekazywane do funkcji jako parametry. Wyzwalacz jest specjalnym typem powiązania danych wejściowych. Chociaż funkcja ma tylko jeden wyzwalacz, może mieć wiele powiązań wejściowych i wyjściowych. Aby dowiedzieć się więcej, zobacz temat [Azure Functions wyzwalacze i koncepcje powiązań](functions-triggers-bindings.md).

W tym artykule pokazano, jak za pomocą Visual Studio Code połączyć funkcję utworzoną w [poprzednim artykule szybki start](functions-create-first-function-vs-code.md) z usługą Azure Storage. Powiązanie danych wyjściowych dodawane do tej funkcji zapisuje dane z żądania HTTP do wiadomości w kolejce usługi Azure queue storage. 

Większość powiązań wymaga przechowywanych parametrów połączenia używanych przez funkcje do uzyskiwania dostępu do usługi powiązanej. Aby ułatwić sobie korzystanie z konta magazynu utworzonego za pomocą aplikacji funkcji programu. Połączenie z tym kontem jest już przechowywane w ustawieniu aplikacji o nazwie `AzureWebJobsStorage`.  

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem pracy z tym artykułem należy spełnić następujące wymagania:

* Zainstaluj [rozszerzenie usługi Azure Storage dla Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage).
* Zainstaluj [Eksplorator usługi Azure Storage](https://storageexplorer.com/). Eksplorator usługi Storage jest narzędziem używanym do badania komunikatów w kolejce generowanych przez powiązanie danych wyjściowych. Eksplorator usługi Storage jest obsługiwana w systemach operacyjnych macOS, Windows i Linux.
* Zainstaluj [Narzędzia interfejs wiersza polecenia platformy .NET Core](https://docs.microsoft.com/dotnet/core/tools/?tabs=netcore2x) (C# tylko projekty).
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

# <a name="javascripttabnodejs"></a>[JavaScript](#tab/nodejs)

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

# <a name="ctabcsharp"></a>[C\#](#tab/csharp)

Z wyjątkiem wyzwalaczy HTTP i Timer, powiązania są implementowane jako pakiety rozszerzeń. Uruchom następujące polecenie [dotnet Add Package](/dotnet/core/tools/dotnet-add-package) w oknie terminalu, aby dodać pakiet rozszerzenia magazynu do projektu.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
```
---
Teraz można dodać powiązanie danych wyjściowych magazynu do projektu.

## <a name="add-an-output-binding"></a>Dodawanie powiązania danych wyjściowych

W funkcjach każdy typ powiązania wymaga `direction`, `type` i unikatowych `name` do zdefiniowania w pliku Function. JSON. Sposób definiowania tych atrybutów zależy od języka aplikacji funkcji.

# <a name="javascripttabnodejs"></a>[JavaScript](#tab/nodejs)

[!INCLUDE [functions-add-output-binding-json](../../includes/functions-add-output-binding-json.md)]

# <a name="ctabcsharp"></a>[C\#](#tab/csharp)

[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]

---

## <a name="add-code-that-uses-the-output-binding"></a>Dodawanie kodu korzystającego z powiązania danych wyjściowych

Po zdefiniowaniu powiązania można użyć `name` powiązania, aby uzyskać do niego dostęp jako atrybut w sygnaturze funkcji. Za pomocą powiązania danych wyjściowych nie trzeba używać kodu zestawu SDK usługi Azure Storage do uwierzytelniania, uzyskiwania odwołania do kolejki lub zapisywania danych. Te zadania są wykonywane za pomocą środowiska uruchomieniowego usługi Functions i powiązania danych wyjściowych kolejki.

# <a name="javascripttabnodejs"></a>[JavaScript](#tab/nodejs)

[!INCLUDE [functions-add-output-binding-js](../../includes/functions-add-output-binding-js.md)]

# <a name="ctabcsharp"></a>[C\#](#tab/csharp)

[!INCLUDE [functions-add-storage-binding-csharp-library-code](../../includes/functions-add-storage-binding-csharp-library-code.md)]

---

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

Nowa kolejka o nazwie **requeue** została utworzona na koncie magazynu przez środowisko uruchomieniowe funkcji, gdy zostanie użyte powiązanie danych wyjściowych. Użyjesz Eksplorator usługi Storage, aby sprawdzić, czy kolejka została utworzona wraz z nową wiadomością.

### <a name="connect-storage-explorer-to-your-account"></a>Łączenie Eksploratora usługi Storage z kontem

Pomiń tę sekcję, jeśli już zainstalowano Eksplorator usługi Azure Storage i połączono ją z kontem platformy Azure.

1. Uruchom narzędzie [Azure Storage Explorer] , wybierz ikonę Połącz po lewej stronie, a następnie wybierz pozycję **Dodaj konto**.

    ![Dodaj konto platformy Azure do Eksplorator usługi Microsoft Azure Storage](./media/functions-add-output-binding-storage-queue-vs-code/storage-explorer-add-account.png)

1. W oknie dialogowym **Połącz** wybierz pozycję **Dodaj konto platformy**Azure, wybierz **środowisko platformy Azure**, a następnie wybierz pozycję **Zaloguj się...** . 

    ![Zaloguj się do swojego konta platformy Azure](./media/functions-add-output-binding-storage-queue-vs-code/storage-explorer-connect-azure-account.png)

Po pomyślnym zalogowaniu się do konta zobaczysz wszystkie subskrypcje platformy Azure skojarzone z Twoim kontem.

### <a name="examine-the-output-queue"></a>Sprawdzanie kolejki wyjściowej

1. W Visual Studio Code naciśnij klawisz F1, aby otworzyć paletę poleceń, a następnie wyszukaj i uruchom polecenie `Azure Storage: Open in Storage Explorer` i wybierz nazwę konta magazynu. Twoje konto magazynu zostanie otwarte w Eksplorator usługi Azure Storage.  

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

1. W Visual Studio Code naciśnij klawisz F1, aby otworzyć paletę poleceń. W palecie poleceń Wyszukaj i wybierz `Azure Functions: Open in portal`.

1. Wybierz aplikację funkcji i naciśnij klawisz ENTER. Strona aplikacji funkcji zostanie otwarta w [Azure Portal](https://portal.azure.com).

1. Na karcie **Przegląd** wybierz nazwany link w obszarze **Grupa zasobów**.

    ![Wybierz grupę zasobów do usunięcia ze strony aplikacji funkcji.](./media/functions-add-output-binding-storage-queue-vs-code/functions-app-delete-resource-group.png)

1. Na stronie **Grupa zasobów** zapoznaj się z listą dołączonych zasobów i sprawdź, czy chcesz je usunąć.
 
1. Wybierz pozycję **Usuń grupę zasobów**, a następnie postępuj zgodnie z instrukcjami.

   Usuwanie może potrwać kilka minut. Po jego zakończeniu przez kilka sekund będzie widoczne powiadomienie. Możesz również wybrać ikonę dzwonka w górnej części strony, aby wyświetlić powiadomienie.

## <a name="next-steps"></a>Następne kroki

Została zaktualizowana funkcja wyzwalana przez protokół HTTP w celu zapisania danych w kolejce magazynu. Aby dowiedzieć się więcej na temat opracowywania funkcji, zobacz [opracowywanie Azure Functions przy użyciu Visual Studio Code](functions-develop-vs-code.md).

Następnie należy włączyć monitorowanie Application Insights dla aplikacji funkcji:

> [!div class="nextstepaction"]
> [Enable Application Insights integration (Włączanie integracji z usługą Application Insights)](functions-monitoring.md#manually-connect-an-app-insights-resource)

[Azure Storage Explorer]: https://storageexplorer.com/
