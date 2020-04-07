---
title: Łączenie funkcji platformy Azure z usługą Azure Storage przy użyciu narzędzi wiersza polecenia
description: Dowiedz się, jak połączyć usługę Azure Functions z kolejką usługi Azure Storage, dodając powiązanie danych wyjściowych do projektu wiersza polecenia.
ms.date: 02/07/2020
ms.topic: quickstart
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: f9d9573523083b6355f423b7b3db94b795d8657f
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673329"
---
# <a name="connect-azure-functions-to-azure-storage-using-command-line-tools"></a>Łączenie funkcji platformy Azure z usługą Azure Storage przy użyciu narzędzi wiersza polecenia

W tym artykule można zintegrować kolejkę usługi Azure Storage z funkcją i kontem magazynu utworzonym w [poprzednim przewodniku Szybki start](functions-create-first-azure-function-azure-cli.md). Tej integracji można osiągnąć przy użyciu *powiązania danych wyjściowych,* który zapisuje dane z żądania HTTP do wiadomości w kolejce. Ukończenie tego artykułu nie wiąże się z dodatkowymi kosztami przekraczającymi kilka centów USD z poprzedniego szybkiego startu. Aby dowiedzieć się więcej o powiązaniach, zobacz [Koncepcje wyzwalaczy i powiązań usługi Azure Functions.](functions-triggers-bindings.md)

## <a name="configure-your-local-environment"></a>Konfigurowanie środowiska lokalnego

Przed rozpoczęciem należy ukończyć artykuł [Szybki start: Tworzenie projektu usługi Azure Functions z wiersza polecenia](functions-create-first-azure-function-azure-cli.md). Jeśli już oczyszczone zasoby na końcu tego artykułu, przejdź przez kroki ponownie, aby ponownie utworzyć aplikację funkcji i powiązanych zasobów na platformie Azure.

[!INCLUDE [functions-cli-get-storage-connection](../../includes/functions-cli-get-storage-connection.md)]

[!INCLUDE [functions-register-storage-binding-extension-csharp](../../includes/functions-register-storage-binding-extension-csharp.md)]

[!INCLUDE [functions-add-output-binding-cli](../../includes/functions-add-output-binding-cli.md)]

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]  
::: zone-end  
::: zone pivot="programming-language-java" 
[!INCLUDE [functions-add-output-binding-java-cli](../../includes/functions-add-output-binding-java-cli.md)]
::: zone-end   

Aby uzyskać więcej informacji na temat szczegółów powiązań, zobacz [koncepcje wyzwalaczy i powiązań usługi Azure Functions](functions-triggers-bindings.md) oraz [konfiguracja danych wyjściowych kolejki.](functions-bindings-storage-queue-output.md#configuration)

## <a name="add-code-to-use-the-output-binding"></a>Dodaj kod, aby użyć powiązania danych wyjściowych

Po zdefiniowaniu powiązania kolejki można teraz zaktualizować `msg` funkcję, aby odbierać parametr wyjściowy i zapisywać komunikaty do kolejki.

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

::: zone pivot="programming-language-java"
[!INCLUDE [functions-add-output-binding-java-code](../../includes/functions-add-output-binding-java-code.md)]

[!INCLUDE [functions-add-output-binding-java-test-cli](../../includes/functions-add-output-binding-java-test-cli.md)]
::: zone-end

Należy zauważyć, że *nie* trzeba pisać żadnego kodu do uwierzytelniania, uzyskiwania odwołania do kolejki lub zapisywania danych. Wszystkie te zadania integracji są wygodnie obsługiwane w usłudze Azure Functions powiązania środowiska wykonawczego i danych wyjściowych kolejki.

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

[!INCLUDE [functions-extension-bundles-info](../../includes/functions-extension-bundles-info.md)]

## <a name="view-the-message-in-the-azure-storage-queue"></a>Wyświetlanie wiadomości w kolejce usługi Azure Storage

[!INCLUDE [functions-add-output-binding-view-queue-cli](../../includes/functions-add-output-binding-view-queue-cli.md)]

## <a name="redeploy-the-project-to-azure"></a>Ponowne wdrożenie projektu na platformie Azure

Teraz, gdy zostały zweryfikowane lokalnie, że funkcja napisał komunikat do kolejki usługi Azure Storage, można ponownie wdrożyć projekt, aby zaktualizować punkt końcowy uruchomiony na platformie Azure.

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell,programming-language-csharp" 
W *localfunctionsProj* folderu [`func azure functionapp publish`](functions-run-local.md#project-file-deployment) użyj polecenia, aby ponownie wdrożyć projekt, zastępując`<APP_NAME>` nazwą aplikacji.

```
func azure functionapp publish <APP_NAME>
```
::: zone-end  

::: zone pivot="programming-language-java" 

W lokalnym folderze projektu użyj następującego polecenia Maven, aby ponownie opublikować projekt:
```
mvn azure-functions:deploy
```
::: zone-end

## <a name="verify-in-azure"></a>Weryfikuj na platformie Azure

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
