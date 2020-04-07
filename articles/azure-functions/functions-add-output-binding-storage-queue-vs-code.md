---
title: Łączenie funkcji platformy Azure z usługą Azure Storage przy użyciu kodu programu Visual Studio
description: Dowiedz się, jak połączyć usługi Azure Functions z kolejką usługi Azure Storage, dodając powiązanie danych wyjściowych do projektu programu Visual Studio Code.
ms.date: 02/07/2020
ms.topic: quickstart
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: c32f98fc1b3de98592f8e7ceb43c17aa8a9049f7
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673445"
---
# <a name="connect-azure-functions-to-azure-storage-using-visual-studio-code"></a>Łączenie funkcji platformy Azure z usługą Azure Storage przy użyciu kodu programu Visual Studio

[!INCLUDE [functions-add-storage-binding-intro](../../includes/functions-add-storage-binding-intro.md)]

W tym artykule pokazano, jak używać programu Visual Studio Code do łączenia funkcji utworzonej w [poprzednim artykule szybki start](functions-create-first-function-vs-code.md) z usługą Azure Storage. Powiązanie danych wyjściowych, które można dodać do tej funkcji zapisuje dane z żądania HTTP do wiadomości w kolejce magazynu kolejki platformy Azure. 

Większość powiązań wymaga przechowywanego ciągu połączenia, którego funkcja używa do uzyskiwania dostępu do usługi powiązanej. Aby ułatwić, należy użyć konta magazynu utworzonego za pomocą aplikacji funkcji. Połączenie z tym kontem jest już `AzureWebJobsStorage`przechowywane w ustawieniach aplikacji o nazwie .  

## <a name="configure-your-local-environment"></a>Konfigurowanie środowiska lokalnego

Przed rozpoczęciem tego artykułu należy spełnić następujące wymagania:

* Zainstaluj [rozszerzenie usługi Azure Storage dla programu Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage).

* Zainstaluj [Eksploratora usługi Azure Storage](https://storageexplorer.com/). Eksplorator magazynu to narzędzie, którego użyjesz do sprawdzania komunikatów kolejki generowanych przez powiązanie danych wyjściowych. Eksplorator magazynu jest obsługiwany w systemach operacyjnych macOS, Windows i Linux.

::: zone pivot="programming-language-csharp"
* Zainstaluj [narzędzia .NET Core CLI](https://docs.microsoft.com/dotnet/core/tools/?tabs=netcore2x).
::: zone-end

* Wykonaj kroki opisane w [części 1 przewodnika Szybki start kodu programu Visual Studio](functions-create-first-function-vs-code.md). 

W tym artykule przyjęto założenie, że użytkownik jest już zalogowany do subskrypcji platformy Azure z programu Visual Studio Code. Możesz się zalogować, uruchamiając `Azure: Sign In` z palety poleceń. 

## <a name="download-the-function-app-settings"></a>Pobierz ustawienia aplikacji funkcji

W [poprzednim artykule szybki start](functions-create-first-function-vs-code.md)utworzono aplikację funkcji na platformie Azure wraz z wymaganym kontem magazynu. Parametry połączenia dla tego konta są bezpiecznie przechowywane w ustawieniach aplikacji na platformie Azure. W tym artykule piszesz wiadomości do kolejki magazynu na tym samym koncie. Aby połączyć się z kontem magazynu podczas lokalnego uruchamiania funkcji, należy pobrać ustawienia aplikacji do pliku local.settings.json. 

1. Naciśnij klawisz F1, aby otworzyć paletę poleceń, `Azure Functions: Download Remote Settings....`a następnie wyszukaj i uruchom polecenie . 

1. Wybierz aplikację funkcji utworzoną w poprzednim artykule. Wybierz **opcję Tak dla wszystkich,** aby zastąpić istniejące ustawienia lokalne. 

    > [!IMPORTANT]  
    > Ponieważ zawiera wpisy tajne, local.settings.json plik nigdy nie zostanie opublikowany i jest wykluczony z kontroli źródła.

1. Skopiuj wartość `AzureWebJobsStorage`, która jest kluczem dla wartości ciągu połączenia konta magazynu. To połączenie służy do sprawdzenia, czy powiązanie danych wyjściowych działa zgodnie z oczekiwaniami.

## <a name="register-binding-extensions"></a>Rejestrowanie rozszerzeń do wiązania

Ponieważ używasz powiązania danych wyjściowych magazynu kolejki, przed uruchomieniem projektu musi być zainstalowane rozszerzenie powiązania magazynu. 

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell,programming-language-java"

Projekt został skonfigurowany do używania [pakietów rozszerzeń,](functions-bindings-register.md#extension-bundles)które automatycznie instalują wstępnie zdefiniowany zestaw pakietów rozszerzeń. 

Pakiety rozszerzeń są włączone w pliku host.json w katalogu głównym projektu, który wygląda następująco:

:::code language="json" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/host.json":::

::: zone-end

::: zone pivot="programming-language-csharp"

Z wyjątkiem wyzwalaczy HTTP i czasomierza powiązania są implementowane jako pakiety rozszerzeń. Uruchom następujące polecenie [dotnet dodaj pakiet](/dotnet/core/tools/dotnet-add-package) w oknie terminala, aby dodać pakiet rozszerzenia magazynu do projektu.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
```

::: zone-end

Teraz można dodać powiązanie danych wyjściowych magazynu do projektu.

## <a name="add-an-output-binding"></a>Dodawanie powiązania danych wyjściowych

W funkcji, każdy typ `direction`powiązania `type`wymaga , `name` i unikatowy do zdefiniowania w pliku function.json. Sposób definiowania tych atrybutów zależy od języka aplikacji funkcji.

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell,programming-language-java"

[!INCLUDE [functions-add-output-binding-json](../../includes/functions-add-output-binding-json.md)]

::: zone-end

::: zone pivot="programming-language-csharp"

[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]

::: zone-end

::: zone pivot="programming-language-java"

[!INCLUDE [functions-add-output-binding-java](../../includes/functions-add-output-binding-java.md)]

::: zone-end

## <a name="add-code-that-uses-the-output-binding"></a>Dodawanie kodu korzystającego z powiązania danych wyjściowych

Po zdefiniowaniu powiązania można użyć `name` powiązania, aby uzyskać do niego dostęp jako atrybut w podpisie funkcji. Przy użyciu powiązania danych wyjściowych, nie trzeba używać kodu SDK usługi Azure Storage do uwierzytelniania, pobierania odwołania do kolejki lub zapisywania danych. Powiązania środowiska wykonawczego i danych wyjściowych kolejki funkcji wykonaj te zadania za Ciebie.

::: zone pivot="programming-language-javascript"  
[!INCLUDE [functions-add-output-binding-js](../../includes/functions-add-output-binding-js.md)]
::: zone-end  

::: zone pivot="programming-language-typescript"  
[!INCLUDE [functions-add-output-binding-ts](../../includes/functions-add-output-binding-ts.md)]
::: zone-end  

::: zone pivot="programming-language-powershell"

[!INCLUDE [functions-add-output-binding-powershell](../../includes/functions-add-output-binding-powershell.md)]

::: zone-end

::: zone pivot="programming-language-python"

[!INCLUDE [functions-add-output-binding-python](../../includes/functions-add-output-binding-python.md)]

::: zone-end

::: zone pivot="programming-language-csharp"  

[!INCLUDE [functions-add-storage-binding-csharp-library-code](../../includes/functions-add-storage-binding-csharp-library-code.md)]

::: zone-end  

::: zone pivot="programming-language-java"  

[!INCLUDE [functions-add-storage-binding-java-code](../../includes/functions-add-storage-binding-java-code.md)]

[!INCLUDE [functions-add-output-binding-java-test](../../includes/functions-add-output-binding-java-test.md)]

::: zone-end  

<!--- Local testing section --->

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-python"

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

::: zone-end

::: zone pivot="programming-language-powershell"

[!INCLUDE [functions-run-function-test-local-vs-code-ps](../../includes/functions-run-function-test-local-vs-code-ps.md)]

::: zone-end

Nowa kolejka o nazwie **outqueue** jest tworzony na koncie magazynu przez środowisko uruchomieniowe functions, gdy po raz pierwszy używane jest powiązanie danych wyjściowych. Użyjesz Eksploratora magazynu, aby sprawdzić, czy kolejka została utworzona wraz z nową wiadomością.

::: zone pivot="programming-language-java"  

[!INCLUDE [functions-add-output-binding-java-test](../../includes/functions-add-output-binding-java-test.md)]

::: zone-end

### <a name="connect-storage-explorer-to-your-account"></a>Łączenie Eksploratora usługi Storage z kontem

Pomiń tę sekcję, jeśli został już zainstalowany Eksplorator usługi Azure Storage i połączyłeś go z kontem platformy Azure.

1. Uruchom narzędzie [Azure Storage Explorer], wybierz ikonę połącz po lewej stronie i wybierz pozycję **Dodaj konto**.

    ![Dodawanie konta platformy Azure do Eksploratora usługi Microsoft Azure Storage](./media/functions-add-output-binding-storage-queue-vs-code/storage-explorer-add-account.png)

1. W oknie dialogowym **Łączenie** wybierz pozycję **Dodaj konto platformy Azure**, wybierz środowisko platformy **Azure**i wybierz pozycję **Zaloguj się...**. 

    ![Zaloguj się do swojego konta platformy Azure](./media/functions-add-output-binding-storage-queue-vs-code/storage-explorer-connect-azure-account.png)

Po pomyślnym zalogowaniu się na konto zobaczysz wszystkie subskrypcje platformy Azure skojarzone z Twoim kontem.

### <a name="examine-the-output-queue"></a>Sprawdzanie kolejki wyjściowej

1. W programie Visual Studio Code naciśnij klawisz F1, aby otworzyć paletę `Azure Storage: Open in Storage Explorer` poleceń, a następnie wyszukaj i uruchom polecenie oraz wybierz nazwę konta magazynu. Twoje konto magazynu zostanie otwarte w Eksploratorze usługi Azure Storage.  

1. Rozwiń węzeł **Kolejki**, a następnie wybierz kolejkę o nazwie **outqueue**. 

   Kolejka zawiera komunikat utworzony za pomocą powiązania danych wyjściowych kolejki po uruchomieniu funkcji wyzwalanej przez protokół HTTP. Jeśli funkcja została wywołana przy użyciu domyślnego elementu `name` o wartości *Azure*, komunikat w kolejce to *Nazwa przekazana do funkcji: Azure*.

    ![Komunikat kolejki wyświetlany w Eksploratorze usługi Azure Storage](./media/functions-add-output-binding-storage-queue-vs-code/function-queue-storage-output-view-queue.png)

1. Uruchom tę funkcję ponownie, wyślij kolejne żądanie, a w kolejce pojawi się nowa wiadomość.  

Teraz nadszedł czas, aby ponownie opublikować zaktualizowaną aplikację funkcji na platformie Azure.

## <a name="redeploy-and-verify-the-updated-app"></a>Ponowne wdrożenie i zweryfikowanie zaktualizowanej aplikacji

1. W programie Visual Studio Code naciśnij klawisz F1, aby otworzyć paletę poleceń. W palecie poleceń wyszukaj i wybierz opcję `Azure Functions: Deploy to function app...`.

1. Wybierz aplikację funkcji utworzoną w pierwszym artykule. Ponieważ projekt jest ponownie wdrażany do tej samej aplikacji, wybierz **pozycję Deploy,** aby odrzucić ostrzeżenie dotyczące zastępowania plików.

1. Po zakończeniu wdrażania można ponownie użyć cURL lub przeglądarki, aby przetestować ponownie wdrożeniową funkcję. Jak poprzednio, dołącz `&name=<yourname>` ciąg zapytania do adresu URL, jak w poniższym przykładzie:

    ```bash
    curl https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....&name=<yourname>
    ```

1. Ponownie [wyświetl komunikat w kolejce magazynu,](#examine-the-output-queue) aby sprawdzić, czy powiązanie danych wyjściowych ponownie generuje nową wiadomość w kolejce.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

*Zasoby* na platformie Azure dotyczą aplikacji funkcji, funkcji, kont magazynu i tak dalej. Zasoby są grupowane w ramach *grup zasobów*, a wszystkie elementy w grupie możesz usunąć, usuwając tę grupę.

Aby ukończyć te przewodniki Szybki start, zostały utworzone zasoby. Za te zasoby może zostać naliczona opłata — zależy to od Twojego [stanu konta](https://azure.microsoft.com/account/) i [cennika usług](https://azure.microsoft.com/pricing/). Jeśli nie potrzebujesz już tych zasobów, oto jak możesz je usunąć:

[!INCLUDE [functions-cleanup-resources-vs-code.md](../../includes/functions-cleanup-resources-vs-code.md)]

## <a name="next-steps"></a>Następne kroki

Zaktualizowano funkcję wyzwalaną http, aby zapisywać dane w kolejce magazynu. Teraz możesz dowiedzieć się więcej o tworzeniu funkcji przy użyciu programu Visual Studio Code:

+ [Tworzenie funkcji platformy Azure przy użyciu kodu programu Visual Studio](functions-develop-vs-code.md)
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
+ [Wyzwalacze i powiązania usługi Azure Functions](functions-triggers-bindings.md).
+ [Strona z cennikiem funkcji](https://azure.microsoft.com/pricing/details/functions/)
+ [Szacowanie kosztów planu zużycia](functions-consumption-costs.md) artykuł.
