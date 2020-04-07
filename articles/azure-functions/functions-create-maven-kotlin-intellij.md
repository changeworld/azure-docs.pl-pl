---
title: Tworzenie funkcji platformy Azure za pomocą kotlin i intellij
description: Dowiedz się, jak tworzyć i publikować prostą aplikację bezserwerową wyzwalaną protokołem HTTP na platformie Azure za pomocą aplikacji Kotlin i IntelliJ.
author: dglover
ms.service: azure-functions
ms.topic: quickstart
ms.date: 03/25/2020
ms.author: dglover
ms.openlocfilehash: 2eb1a016e04a4150a76112c68683926810f5c66d
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80674097"
---
# <a name="quickstart-create-your-first-http-triggered-function-with-kotlin-and-intellij"></a>Szybki start: Tworzenie pierwszej funkcji wyzwalania HTTP z Kotlin i IntelliJ

W tym artykule pokazano, jak utworzyć projekt funkcji [bezserwerowych](https://azure.microsoft.com/overview/serverless-computing/) z IntelliJ IDEA i Apache Maven. Pokazano również, jak lokalnie debugować kod funkcji w zintegrowanym środowisku programistycznym (IDE), a następnie wdrożyć projekt funkcji na platformie Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-development-environment"></a>Konfigurowanie środowiska projektowego

Aby opracować funkcję z Kotlin i IntelliJ, zainstaluj następujące oprogramowanie:

- [Zestaw Java Developer Kit](https://aka.ms/azure-jdks) (JDK), wersja 8
- [Apache Maven](https://maven.apache.org), wersja 3.0 lub nowsza
- [IntelliJ IDEA](https://www.jetbrains.com/idea/download), Wersje społecznościowe lub ultimate z Maven
- [Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure)
- [Wersja 2.x](functions-run-local.md#v2) podstawowych narzędzi usługi Azure Functions. Zapewnia lokalne środowisko programistyczne do pisania, uruchamiania i debugowania usługi Azure Functions.

> [!IMPORTANT]
> JAVA_HOME zmienna środowiskowa musi być ustawiona na lokalizację instalacji zestawu JDK, aby wykonać kroki opisane w tym artykule.

## <a name="create-a-functions-project"></a>Tworzenie projektu funkcji

1. W programie IntelliJ IDEA wybierz pozycję **Utwórz nowy projekt**.  
1. W oknie **Nowy projekt** wybierz **pozycję Maven** z lewego okienka.
1. Zaznacz pole wyboru **Utwórz z archetypu,** a następnie wybierz pozycję **Dodaj archetyp** dla [typu azure-functions-kotlin-archetype](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-kotlin-archetype).
1. W oknie **Dodaj archetyp** wypełnij pola w następujący sposób:
    - _GrupaId_: com.microsoft.azure
    - _ArtifactId_: azure-functions-kotlin-archetype
    - _Wersja_: Użyj najnowszej wersji z [centralnego repozytorium](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-kotlin-archetype)
    ![Utwórz projekt Maven z archetypu w IntelliJ IDEA](media/functions-create-first-kotlin-intellij/functions-create-intellij.png)  
1. Wybierz **przycisk OK**, a następnie wybierz pozycję **Dalej**.
1. Wprowadź szczegóły bieżącego projektu i wybierz pozycję **Zakończ**.

Maven tworzy pliki projektu w nowym folderze o takiej samej nazwie jak _artifactid_ wartość. Wygenerowany kod projektu jest prostą funkcją [wyzwalaną przez protokół HTTP,](/azure/azure-functions/functions-bindings-http-webhook) która odzwierciedla treść wyzwalającego żądania HTTP.

## <a name="run-functions-locally-in-the-ide"></a>Uruchamianie funkcji lokalnie w ide

> [!NOTE]
> Aby uruchamiać i debugować funkcje lokalnie, upewnij się, że zainstalowano [narzędzia Podstawowe usługi Azure Functions w wersji 2](functions-run-local.md#v2).

1. Importuj zmiany ręcznie lub włącz [automatyczne importowanie](https://www.jetbrains.com/help/idea/creating-and-optimizing-imports.html).
1. Otwórz pasek narzędzi **Maven Projects.**
1. Rozwiń **węzeł życia**, a następnie otwórz **pakiet**. Rozwiązanie jest tworzone i pakowane w nowo utworzony katalog docelowy.
1. Rozwiń **wtyczki** > **azure-functions** i otwórz **azure-functions:run,** aby uruchomić lokalne środowisko uruchomieniowe usługi Azure Functions.  
  ![Pasek narzędzi Maven dla funkcji platformy Azure](media/functions-create-first-kotlin-intellij/functions-intellij-kotlin-maven-toolbar.png)  

1. Zamknij okno dialogowe uruchamiania po zakończeniu testowania funkcji. Tylko jeden host funkcji może być aktywny i działa lokalnie w czasie.

## <a name="debug-the-function-in-intellij"></a>Debugowanie funkcji w intellij

1. Aby uruchomić hosta funkcji w trybie debugowania, dodaj **-DenableDebug** jako argument po uruchomieniu funkcji. Można zmienić konfigurację w [maven cele](https://www.jetbrains.com/help/idea/maven-support.html#run_goal) lub uruchomić następujące polecenie w oknie terminala:  

   ```
   mvn azure-functions:run -DenableDebug
   ```

   To polecenie powoduje, że host funkcji otwiera port debugowania w 5005.

1. W menu **Uruchom** wybierz polecenie **Edytuj konfiguracje**.
1. Wybierz **opcję (+),** aby dodać **pilota**.
1. Wypełnij pola _Nazwa_ i _Ustawienia,_ a następnie wybierz przycisk **OK,** aby zapisać konfigurację.
1. Po skonfigurowaniu wybierz opcję **Debugowanie < nazwa konfiguracji zdalnej >** lub naciśnij klawisze Shift+F9 na klawiaturze, aby rozpocząć debugowanie.

   ![Funkcje debugowania w technologii IntelliJ](media/functions-create-first-kotlin-intellij/debug-configuration-intellij.PNG)

1. Po zakończeniu zatrzymaj debuger i uruchomiony proces. Tylko jeden host funkcji może być aktywny i działa lokalnie w czasie.

## <a name="deploy-the-function-to-azure"></a>Wdrażanie funkcji na platformie Azure

1. Aby można było wdrożyć funkcję na platformie Azure, należy [zalogować się przy użyciu interfejsu wiersza polecenia platformy Azure.](/cli/azure/authenticate-azure-cli?view=azure-cli-latest)

   ``` azurecli
   az login
   ```

1. Wdrożyć kod do nowej funkcji `azure-functions:deploy` przy użyciu obiektu docelowego Maven. Można również wybrać opcję **azure-functions:deploy** w oknie Maven Projects.

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

Teraz, gdy wdrożono pierwszą funkcję Kotlin na platformie Azure, zapoznaj się z [przewodnikiem dla deweloperów funkcji Java, aby](functions-reference-java.md) uzyskać więcej informacji na temat tworzenia funkcji java i kotlin.
- Dodaj dodatkowe funkcje z różnymi wyzwalaczami do projektu przy użyciu obiektu docelowego `azure-functions:add` Maven.
