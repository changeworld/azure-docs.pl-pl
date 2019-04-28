---
title: Tworzenie funkcji platformy Azure przy użyciu języka Java i IntelliJ | Dokumentacja firmy Microsoft
description: Dowiedz się, jak tworzyć i publikować to prosta aplikacja wyzwalanej przez HTTP, bez użycia serwera na platformie Azure przy użyciu języka Java i IntelliJ.
services: functions
documentationcenter: na
author: jeffhollan
manager: jpconnock
keywords: Azure funkcji, funkcje, przetwarzanie zdarzeń, obliczeniowych, architektura bezserwerowa, języka java
ms.service: azure-functions
ms.devlang: java
ms.topic: conceptual
ms.date: 07/01/2018
ms.author: jehollan
ms.custom: mvc, devcenter
ms.openlocfilehash: da93c60b52edf509900adf89fb688a0596d9763b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61342226"
---
# <a name="create-your-first-azure-function-with-java-and-intellij"></a>Tworzenie pierwszej funkcji platformy Azure przy użyciu języka Java i IntelliJ

W tym artykule przedstawiono:
- Jak utworzyć [bezserwerowe](https://azure.microsoft.com/overview/serverless-computing/) projektu funkcji przy użyciu rozwiązania IntelliJ IDEA i narzędzia Apache Maven
- Kroki, testowanie i debugowanie funkcji w zintegrowanym środowisku programistycznym (IDE) na komputerze lokalnym
- Instrukcje dotyczące wdrażania projektu funkcji do usługi Azure Functions

<!-- TODO ![Access a Hello World function from the command line with cURL](media/functions-create-java-maven/hello-azure.png) -->

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-development-environment"></a>Konfigurowanie środowiska projektowego

Tworzenie funkcji przy użyciu języka Java i IntelliJ, należy zainstalować następujące oprogramowanie:

- [Zestaw Java Developer Kit](https://www.azul.com/downloads/zulu/) (JDK) w wersji 8
- [Narzędzia Apache Maven](https://maven.apache.org), wersja 3.0 lub nowsza
- [IntelliJ IDEA](https://www.jetbrains.com/idea/download), społeczności lub Ultimate wersji za pomocą narzędzia Maven
- [Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure)

> [!IMPORTANT]
> Zmienna środowiskowa JAVA_HOME należy ustawić do lokalizacji instalacji zestawu JDK, wykonanie czynności opisanych w tym artykule.

 Zaleca się zainstalowanie [funkcji podstawowych narzędzi usługi Azure, wersja 2](functions-run-local.md#v2). Zapewnia lokalne Środowisko deweloperskie do pisania, uruchamiania i debugowania usługi Azure Functions.

## <a name="create-a-functions-project"></a>Tworzenie projektu funkcji

1. Z rozwiązaniem IntelliJ IDEA wybierz **Utwórz nowy projekt**.  
1. W **nowy projekt** wybierz **Maven** z okienka po lewej stronie.
1. Wybierz **Utwórz z archetype** pole wyboru, a następnie wybierz pozycję **Dodaj Archetype** dla [azure-functions-archetype](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype).
1. W **Dodaj Archetype** okna, wypełnij pola w następujący sposób:
    - _GroupId_: com.microsoft.azure
    - _ArtifactId_: azure-functions-archetype
    - _Wersja_: Użyj najnowszej wersji z [centralne repozytorium](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype)
    ![Utwórz projekt narzędzia Maven z archetype w IntelliJ IDEA](media/functions-create-first-java-intellij/functions-create-intellij.png)  
1. Wybierz **OK**, a następnie wybierz pozycję **dalej**.
1. Wprowadź szczegóły dla bieżącego projektu, a następnie wybierz pozycję **Zakończ**.

Narzędzie maven utworzy pliki projektu w nowym folderze o nazwie identycznej z nazwą _ArtifactId_ wartości. Wygenerowany kod w projekcie jest prostą [wyzwalanej przez HTTP](/azure/azure-functions/functions-bindings-http-webhook) funkcja, która zwraca treść wyzwalająca żądania HTTP.

## <a name="run-functions-locally-in-the-ide"></a>Uruchom funkcje lokalnie w środowisku IDE

> [!NOTE]
> Aby uruchomić i debugowanie funkcji lokalnie, upewnij się, po zainstalowaniu [funkcji podstawowych narzędzi usługi Azure, wersja 2](functions-run-local.md#v2).

1. Ręczne importowanie zmiany lub Włącz [automatyczne importowanie](https://www.jetbrains.com/help/idea/creating-and-optimizing-imports.html).
1. Otwórz **projektami Maven** paska narzędzi.
1. Rozwiń **cyklu życia**, a następnie otwórz **pakietu**. Rozwiązanie to skompilowania i spakowania w katalogu docelowym nowo utworzony.
1. Rozwiń **wtyczek** > **usługi azure functions** , a następnie otwórz **azure-functions: Uruchom** można uruchomić lokalne środowisko uruchomieniowe usługi Azure Functions.  
  ![Narzędzie maven narzędzi dla usługi Azure Functions](media/functions-create-first-java-intellij/functions-intellij-java-maven-toolbar.png)  

1. Zamknij okno dialogowe uruchamiania po zakończeniu testowania funkcji. Tylko jednej funkcji — host może być aktywny i działa lokalnie w danym momencie.

## <a name="debug-the-function-in-intellij"></a>Debugowanie funkcji w programie IntelliJ

1. Aby rozpocząć hosta funkcji w trybie debugowania, Dodaj **- DenableDebug** jako argument po uruchomieniu funkcji. Można albo zmień konfigurację w [cele maven](https://www.jetbrains.com/help/idea/maven-support.html#run_goal) lub uruchom następujące polecenie w oknie terminalu:  

   ```
   mvn azure-functions:run -DenableDebug
   ```

   To polecenie powoduje hosta funkcji otworzyć port debugowania w 5005.

1. Na **Uruchom** menu, wybierz opcję **Edytuj konfiguracje**.
1. Wybierz **(+)** dodać **zdalnego**.
1. Wykonaj _nazwa_ i _ustawienia_ pola, a następnie wybierz **OK** Aby zapisać konfigurację.
1. Po zakończeniu pracy Instalatora wybierz **debugowania < nazwa konfiguracji zdalnego >** lub naciśnij klawisze Shift + F9 na klawiaturze, aby rozpocząć debugowanie.

   ![Debugowanie funkcji w programie IntelliJ](media/functions-create-first-java-intellij/debug-configuration-intellij.PNG)

1. Gdy skończysz, Zatrzymaj debuger i uruchomionego procesu. Tylko jednej funkcji — host może być aktywny i działa lokalnie w danym momencie.

## <a name="deploy-the-function-to-azure"></a>Wdrażanie funkcji na platformie Azure

1. Przed wdrożeniem funkcji na platformie Azure, należy najpierw [Zaloguj się przy użyciu wiersza polecenia platformy Azure](/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

   ``` azurecli
   az login
   ```

1. Wdróż swój kod w nowej funkcji przy użyciu `azure-functions:deploy` docelowej narzędzia Maven. Możesz również wybrać **usługi azure functions: Wdrażanie** opcji w oknie projektach narzędzia Maven.

   ```
   mvn azure-functions:deploy
   ```

1. Adres URL można znaleźć funkcji w danych wyjściowych wiersza polecenia platformy Azure, po pomyślnym wdrożeniu funkcji.

   ``` output
   [INFO] Successfully deployed Function App with package.
   [INFO] Deleting deployment package from Azure Storage...
   [INFO] Successfully deleted deployment package fabrikam-function-20170920120101928.20170920143621915.zip
   [INFO] Successfully deployed Function App at https://fabrikam-function-20170920120101928.azurewebsites.net
   [INFO] ------------------------------------------------------------------------
   ```

## <a name="next-steps"></a>Kolejne kroki

- Zapoznaj się z [Przewodnikiem dewelopera po funkcjach języka Java](functions-reference-java.md), aby uzyskać więcej informacji na temat tworzenia funkcji języka Java.
- Dodaj dodatkowe funkcje z różnymi wyzwalaczami do projektu przy użyciu `azure-functions:add` docelowej narzędzia Maven.
