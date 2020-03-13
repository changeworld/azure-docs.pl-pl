---
title: Tworzenie funkcji platformy Azure przy użyciu języka Java i IntelliJ
description: Dowiedz się, jak utworzyć i opublikować prostą bezserwerową aplikację wywoływaną przez protokół HTTP na platformie Azure przy użyciu języka Java i IntelliJ.
author: jeffhollan
ms.topic: conceptual
ms.date: 07/01/2018
ms.author: jehollan
ms.custom: mvc, devcenter
ms.openlocfilehash: 7003dc19a7bfc405809de91534028aba8e0416c5
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/12/2020
ms.locfileid: "79136854"
---
# <a name="create-your-first-azure-function-with-java-and-intellij"></a>Tworzenie pierwszej funkcji platformy Azure przy użyciu języka Java i IntelliJ

W tym artykule opisano:
- Jak utworzyć projekt funkcji bez użycia [serwera](https://azure.microsoft.com/overview/serverless-computing/) z pomysłem IntelliJ i Apache Maven
- Kroki testowania i debugowania funkcji w zintegrowanym środowisku programistycznym (IDE) na własnym komputerze
- Instrukcje dotyczące wdrażania projektu funkcji w Azure Functions

<!-- TODO ![Access a Hello World function from the command line with cURL](media/functions-create-java-maven/hello-azure.png) -->

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-development-environment"></a>Konfigurowanie środowiska projektowego

Aby opracować funkcję przy użyciu języka Java i IntelliJ, Zainstaluj następujące oprogramowanie:

- [Java developer Kit](https://www.azul.com/downloads/zulu/) (JDK), wersja 8
- [Apache Maven](https://maven.apache.org), wersja 3,0 lub nowsza
- [INTELLIJ pomysł](https://www.jetbrains.com/idea/download), wersja Community lub Ultimate z Maven
- [Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure)

> [!IMPORTANT]
> Aby wykonać kroki opisane w tym artykule, zmienna środowiskowa JAVA_HOME musi być ustawiona na lokalizację instalacji JDK.

 Zalecamy zainstalowanie [Azure Functions Core Tools w wersji 2](functions-run-local.md#v2). Zapewnia lokalne środowisko programistyczne do pisania, uruchamiania i debugowania Azure Functions.

## <a name="create-a-functions-project"></a>Tworzenie projektu funkcji

1. W IntelliJ pomysł wybierz pozycję **Utwórz nowy projekt**.  
1. W oknie **Nowy projekt** w okienku po lewej stronie wybierz pozycję **Maven** .
1. Zaznacz pole wyboru **Utwórz z Archetype** , a następnie wybierz pozycję **Dodaj Archetype** dla [usługi Azure-Functions-Archetype](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype).
1. W oknie **Dodawanie Archetype** Wypełnij pola w następujący sposób:
    - _GroupID_: com. Microsoft. Azure
    - _ArtifactId_: Azure-Functions-Archetype
    - _Wersja_: Sprawdź i użyj najnowszej wersji z [centralnego repozytorium](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype)
    ![Utwórz projekt Maven z archetype w IntelliJ pomysł](media/functions-create-first-java-intellij/functions-create-intellij.png)  
1. Wybierz przycisk **OK**, a następnie wybierz przycisk **dalej**.
1. Wprowadź szczegóły dla bieżącego projektu i wybierz pozycję **Zakończ**.

Maven tworzy pliki projektu w nowym folderze o tej samej nazwie, co wartość _ArtifactId_ . Wygenerowany kod projektu jest prostą funkcją [wyzwalaną przez protokół http](/azure/azure-functions/functions-bindings-http-webhook) , która umożliwia echo treści wyzwalanego żądania HTTP.

## <a name="run-functions-locally-in-the-ide"></a>Uruchamianie funkcji lokalnie w środowisku IDE

> [!NOTE]
> Aby uruchamiać i debugować funkcje lokalnie, upewnij się, że zainstalowano [Azure Functions Core Tools w wersji 2](functions-run-local.md#v2).

1. Importuj zmiany ręcznie lub Włącz [autoimport](https://www.jetbrains.com/help/idea/creating-and-optimizing-imports.html).
1. Otwórz pasek narzędzi **projektów Maven** .
1. Rozwiń węzeł **cykl życia**, a następnie otwórz **pakiet**. Rozwiązanie jest skompilowane i spakowane w nowo utworzonym katalogu docelowym.
1. Rozwiń węzeł **wtyczki** > **Azure-Functions** i Otwórz pozycję **Azure-Functions: Run** , aby rozpocząć Azure Functions lokalnego środowiska uruchomieniowego.  
  ![pasek narzędzi Maven dla Azure Functions](media/functions-create-first-java-intellij/functions-intellij-java-maven-toolbar.png)  

1. Zamknij okno dialogowe uruchamiania po zakończeniu testowania funkcji. Tylko jeden host funkcji może być aktywny i uruchamiany lokalnie.

## <a name="debug-the-function-in-intellij"></a>Debuguj funkcję w IntelliJ

1. Aby uruchomić hosta funkcji w trybie debugowania, należy dodać **-DenableDebug** jako argument podczas uruchamiania funkcji. Konfigurację można zmienić w [celach Maven](https://www.jetbrains.com/help/idea/maven-support.html#run_goal) lub uruchomić następujące polecenie w oknie terminalu:  

   ```
   mvn azure-functions:run -DenableDebug
   ```

   To polecenie powoduje, że host funkcji otwiera port debugowania w 5005.

1. W menu **Uruchom** wybierz polecenie **Edytuj konfiguracje**.
1. Wybierz pozycję **(+)** , aby dodać element **zdalny**.
1. Wypełnij pola _Nazwa_ i _Ustawienia_ , a następnie wybierz przycisk **OK** , aby zapisać konfigurację.
1. Po zakończeniu instalacji wybierz kolejno opcje **debuguj < nazwa konfiguracji zdalnej >** lub naciśnij klawisze SHIFT + F9 na klawiaturze, aby rozpocząć debugowanie.

1. Po zakończeniu Zatrzymaj debuger i uruchomiony proces. Tylko jeden host funkcji może być aktywny i uruchamiany lokalnie.

## <a name="deploy-the-function-to-azure"></a>Wdrażanie funkcji na platformie Azure

1. Aby można było wdrożyć funkcję na platformie Azure, musisz [zalogować się przy użyciu interfejsu wiersza polecenia platformy Azure](/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

   ``` azurecli
   az login
   ```

1. Wdróż swój kod w nowej funkcji przy użyciu `azure-functions:deploy` celu Maven. Możesz również wybrać opcję **Azure-Functions: Deploy** w oknie projekty Maven.

   ```
   mvn azure-functions:deploy
   ```

1. Znajdź adres URL funkcji w danych wyjściowych interfejsu wiersza polecenia platformy Azure po pomyślnym wdrożeniu funkcji.

   ``` output
   [INFO] Successfully deployed Function App with package.
   [INFO] Deleting deployment package from Azure Storage...
   [INFO] Successfully deleted deployment package fabrikam-function-20170920120101928.20170920143621915.zip
   [INFO] Successfully deployed Function App at https://fabrikam-function-20170920120101928.azurewebsites.net
   [INFO] ------------------------------------------------------------------------
   ```

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z [Przewodnikiem dewelopera po funkcjach języka Java](functions-reference-java.md), aby uzyskać więcej informacji na temat tworzenia funkcji języka Java.
- Dodaj do projektu dodatkowe funkcje z różnymi wyzwalaczami przy użyciu elementu docelowego `azure-functions:add` Maven.
