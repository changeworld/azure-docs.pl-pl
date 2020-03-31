---
title: Tworzenie aplikacji funkcji platformy Azure za pomocą aplikacji Java i Eclipse
description: Instrukcje tworzenia i publikowania prostej aplikacji bezserwerowej wyzwalane HTTP przy użyciu oprogramowania Java i Eclipse do usługi Azure Functions.
author: jeffhollan
ms.topic: conceptual
ms.date: 07/01/2018
ms.author: jehollan
ms.custom: mvc, devcenter
ms.openlocfilehash: c82432a3d76a84eba1ad921d936b2f3ba064e2ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79136837"
---
# <a name="create-your-first-function-with-java-and-eclipse"></a>Stwórz swoją pierwszą funkcję za pomocą oprogramowania Java i Eclipse 

W tym artykule pokazano, jak utworzyć projekt funkcji [bezserwerowych](https://azure.microsoft.com/solutions/serverless/) za pomocą Eclipse IDE i Apache Maven, przetestować i debugować go, a następnie wdrożyć go do usługi Azure Functions. 

<!-- TODO ![Access a Hello World function from the command line with cURL](media/functions-create-java-maven/hello-azure.png) -->

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-development-environment"></a>Konfigurowanie środowiska projektowego

Aby opracować aplikację funkcji z programem Java i Eclipse, musisz mieć zainstalowane następujące elementy:

-  [Zestaw Java Developer Kit](https://www.azul.com/downloads/zulu/), wersja 8.
-  [Apache Maven](https://maven.apache.org), wersja 3.0 lub wyższa.
-  [Eclipse](https://www.eclipse.org/downloads/packages/), z obsługą Java i Maven.
-  [Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure)

> [!IMPORTANT] 
> Aby wykonać wszystkie czynności opisane w tym przewodniku Szybki start, dla zmiennej środowiskowej JAVA_HOME należy ustawić lokalizację instalacji zestawu JDK.

Zdecydowanie zaleca się również zainstalowanie [narzędzia Azure Functions Core Tools w wersji 2,](functions-run-local.md#v2)które zapewniają lokalne środowisko do uruchamiania i debugowania usługi Azure Functions. 

## <a name="create-a-functions-project"></a>Tworzenie projektu funkcji

1. W programie Eclipse wybierz menu **Plik,** a następnie wybierz polecenie **Nowy —&gt; Projekt Maven**. 
1. Zaakceptuj wartości domyślne w oknie dialogowym **Nowy projekt Maven** i wybierz pozycję **Dalej**.
1. Wybierz **pozycję Dodaj archetyp** i dodaj wpisy dla [archetypu azure-functions](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype).
    - Identyfikator grupy archetypów: com.microsoft.azure
    - Identyfikator artefaktu archetypu: azure-functions-archetype
    - Wersja: Sprawdź i użyj najnowszej wersji z [centralnego repozytorium](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype)
    ![Eclipse Maven](media/functions-create-first-java-eclipse/functions-create-eclipse.png)  
1. Kliknij **przycisk OK,** a następnie kliknij przycisk **Dalej**.  Pamiętaj, aby wypełnić wartości dla `resourceGroup`wszystkich `appName`pól, w tym , i `appRegion` (użyj innego appName innego niż **fabrikam-function-20170920120101928**), a ostatecznie **Zakończ**.
    ![Zaćmienie Maven tworzy2](media/functions-create-first-java-eclipse/functions-create-eclipse2.png)  

Narzędzie Maven tworzy pliki projektu w nowym folderze o nazwie wartości _artifactId_. Wygenerowany kod w projekcie jest prostą funkcją [wyzwalaną http,](/azure/azure-functions/functions-bindings-http-webhook) która odzwierciedla treść wyzwalającego żądania HTTP.

## <a name="run-functions-locally-in-the-ide"></a>Uruchamianie funkcji lokalnie w ide

> [!NOTE]
> [Podstawowe narzędzia azure functions, wersja 2](functions-run-local.md#v2) musi być zainstalowana do uruchamiania i debugowania funkcji lokalnie.

1. Kliknij prawym przyciskiem myszy wygenerowany projekt, a następnie wybierz polecenie **Uruchom jako** i **Maven build**.
1. W oknie dialogowym `package` **Edytowanie konfiguracji** wprowadź pola **Cele** i **Nazwa,** a następnie wybierz pozycję **Uruchom**. Spowoduje to skompilowanie i spakowanie kodu funkcji.
1. Po zakończeniu kompilacji należy utworzyć inną konfigurację uruchom jak wyżej, używając `azure-functions:run` jako celu i nazwy. Wybierz **uruchom,** aby uruchomić funkcję w IDE.

Zakończ środowisko wykonawcze w oknie konsoli po zakończeniu testowania funkcji. Tylko jeden host funkcji może być aktywny i działa lokalnie w czasie.

### <a name="debug-the-function-in-eclipse"></a>Debugowanie funkcji w programie Eclipse

W konfiguracji **Uruchom jako** skonfigurowane w `azure-functions:run` poprzednim `azure-functions:run -DenableDebug` kroku, zmień i uruchom zaktualizowaną konfigurację, aby uruchomić aplikację funkcji w trybie debugowania.

Wybierz menu **Uruchom** i otwórz **opcję Konfiguracje debugowania**. Wybierz **pozycję Remote Java Application** i utwórz nową. Nadaj konfiguracji nazwę i wypełnij ustawienia. Port powinien być zgodny z portem debugowania otwartym `5005`przez hosta funkcji, który domyślnie jest . Po skonfigurowaniu `Debug` kliknij, aby rozpocząć debugowanie.

![Funkcje debugowania w programie Eclipse](media/functions-create-first-java-eclipse/debug-configuration-eclipse.PNG)

Ustaw punkty przerwania i sprawdź obiekty w funkcji przy użyciu IDE. Po zakończeniu zatrzymaj debugera i uruchomionego hosta funkcji. Tylko jeden host funkcji może być aktywny i działa lokalnie w czasie.

## <a name="deploy-the-function-to-azure"></a>Wdrażanie funkcji na platformie Azure

Proces wdrażania w usłudze Azure Functions korzysta z poświadczeń konta z interfejsu wiersza polecenia platformy Azure. [Zaloguj się za pomocą interfejsu wiersza polecenia platformy Azure](/cli/azure/authenticate-azure-cli?view=azure-cli-latest) przed kontynuowaniem korzystania z wiersza polecenia komputera.

```azurecli
az login
```

Wdrażanie kodu w nowej aplikacji `azure-functions:deploy` funkcji przy użyciu celu Maven w nowej konfiguracji **Uruchom jako.**

Po zakończeniu wdrażania zostanie wyświetlony adres URL umożliwiający uzyskanie dostępu do aplikacji funkcji platformy Azure:

```output
[INFO] Successfully deployed Function App with package.
[INFO] Deleting deployment package from Azure Storage...
[INFO] Successfully deleted deployment package fabrikam-function-20170920120101928.20170920143621915.zip
[INFO] Successfully deployed Function App at https://fabrikam-function-20170920120101928.azurewebsites.net
[INFO] ------------------------------------------------------------------------
```

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z [Przewodnikiem dewelopera po funkcjach języka Java](functions-reference-java.md), aby uzyskać więcej informacji na temat tworzenia funkcji języka Java.
- Dodaj do swojego projektu kolejne funkcje z różnymi wyzwalaczami, używając elementu docelowego `azure-functions:add` narzędzia Maven.
