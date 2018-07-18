---
title: Utwórz aplikację funkcji platformy Azure przy użyciu języka Java i IntelliJ | Dokumentacja firmy Microsoft
description: Przewodnik do tworzenia i publikowania prostego HTTP wyzwalane aplikacji bez użycia serwera za pomocą języka Java i IntelliJ do usługi Azure Functions.
services: functions
documentationcenter: na
author: jeffhollan
manager: jpconnock
keywords: Azure funkcji, funkcje, przetwarzanie zdarzeń, obliczeniowych, architektura bezserwerowa, języka java
ms.service: functions
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 07/01/2018
ms.author: jehollan
ms.custom: mvc, devcenter
ms.openlocfilehash: e926bfb023fe3edfd564aa6389e21f6594bec169
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/18/2018
ms.locfileid: "39117504"
---
# <a name="create-your-first-function-with-java-and-intellij-preview"></a>Tworzenie pierwszej funkcji przy użyciu języka Java i IntelliJ (wersja zapoznawcza)

> [!NOTE] 
> Język Java dla usługi Azure Functions jest obecnie w wersji zapoznawczej.

W tym artykule przedstawiono sposób tworzenia [bezserwerowe](https://azure.microsoft.com/overview/serverless-computing/) projektu funkcji przy użyciu rozwiązania IntelliJ IDEA i narzędzia Apache Maven testowanie i debugowanie na komputerze z poziomu środowiska IDE i wdrożyć ją do usługi Azure Functions. 

<!-- TODO ![Access a Hello World function from the command line with cURL](media/functions-create-java-maven/hello-azure.png) -->

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-development-environment"></a>Konfigurowanie środowiska projektowego

Aby opracować aplikacje funkcji przy użyciu języka Java i IntelliJ, musi mieć zainstalowane następujące oprogramowanie:

-  Zestaw [Java Developer Kit](https://www.azul.com/downloads/zulu/), wersja 8.
-  Narzędzie [Apache Maven](https://maven.apache.org), wersja 3.0 lub nowsza.
-  [IntelliJ IDEA](https://www.jetbrains.com/idea/download), społeczności lub Ultimate przy użyciu narzędzia Maven.
-  [Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure)

> [!IMPORTANT] 
> Aby wykonać wszystkie czynności opisane w tym przewodniku Szybki start, dla zmiennej środowiskowej JAVA_HOME należy ustawić lokalizację instalacji zestawu JDK.

Jest zdecydowanie zalecane także zainstalować [funkcji podstawowych narzędzi usługi Azure, wersja 2](functions-run-local.md#v2), które zapewniają lokalne Środowisko deweloperskie do pisania, uruchamiania i debugowania usługi Azure Functions. 


## <a name="create-a-functions-project"></a>Tworzenie projektu funkcji

1. W IntelliJ IDEA kliknij, aby **Utwórz nowy projekt**.  
1. Zaznacz, aby utworzyć na podstawie **Maven**
1. Zaznacz pole wyboru, aby **Utwórz z archetype** i **Dodaj Archetype** dla [azure-functions-archetype](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype).
    - GroupId: com.microsoft.azure
    - ArtifactId: azure-functions-archetype
    - Wersja: Użyj najnowszej wersji z [centralne repozytorium](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype)
    ![tworzenie IntelliJ Maven](media/functions-create-first-java-intellij/functions-create-intellij.png)  
1. Kliknij przycisk **dalej** i wprowadź szczegóły dla bieżącego projektu i ostatecznie **Zakończ**.

Narzędzie Maven tworzy pliki projektu w nowym folderze o nazwie wartości _artifactId_. Kod wygenerowany w projekcie jest prostą [wyzwalaną przez protokół HTTP](/azure/azure-functions/functions-bindings-http-webhook) funkcja, która zwraca treść wyzwalająca żądania HTTP.

## <a name="run-functions-locally-in-the-ide"></a>Uruchom funkcje lokalnie w środowisku IDE

> [!NOTE]
> [Podstawowe narzędzia usługi Azure Functions, wersja 2](functions-run-local.md#v2) musi być zainstalowany na uruchamianie i debugowanie funkcji lokalnie.

1. Zaznacz, aby zaimportować zmiany lub upewnij się, że [automatyczne importowanie](https://www.jetbrains.com/help/idea/creating-and-optimizing-imports.html) jest włączona.
1. Otwórz **projektami Maven** paska narzędzi
1. W obszarze cykl życia, kliknij dwukrotnie **pakietu** do pakietu oraz rozwiązania tworzenia katalogu docelowego.
1. W obszarze wtyczek -> kliknij dwukrotnie plik z usługi azure functions **azure-functions: Uruchom** można uruchomić lokalne środowisko uruchomieniowe usługi azure functions.  
  ![Narzędzie maven narzędzi dla usługi Azure Functions](media/functions-create-first-java-intellij/functions-intellij-java-maven-toolbar.png)  

Zamknij okno dialogowe uruchamiania, po zakończeniu testowania funkcji. Tylko jednej funkcji — host może być aktywny i działa lokalnie w danym momencie.

### <a name="debug-the-function-in-intellij"></a>Debugowanie funkcji w programie IntelliJ

Można debugować funkcje w programie IntelliJ przez dołączenie do hosta funkcji po uruchomieniu.  Uruchom funkcję Azure lokalnie przy użyciu kroków powyżej, a następnie w **Uruchom** menu wybierz opcję **dołączanie do procesu lokalnego**.  Proces powinien być widoczny na porcie 5005 dostępne.  Po dołączeniu może mieć punkty przerwania trafień i debugowania w aplikacji funkcji.

Po zakończeniu zatrzymanie debugera i uruchomionego procesu. Tylko jednej funkcji — host może być aktywności i działania lokalnie, w czasie.

## <a name="deploy-the-function-to-azure"></a>Wdrażanie funkcji na platformie Azure

Proces wdrażania w usłudze Azure Functions korzysta z poświadczeń konta z interfejsu wiersza polecenia platformy Azure. [Zaloguj się przy użyciu wiersza polecenia platformy Azure](/cli/azure/authenticate-azure-cli?view=azure-cli-latest) przed kontynuowaniem, przy użyciu wiersza polecenia na komputerze.

```azurecli
az login
```

Wdróż swój kod w nowej funkcji aplikacji, używając `azure-functions:deploy` Maven objęcia użytkowników zasadami lub wybierz usługi azure functions: Wdrażanie opcji w oknie projektach narzędzia Maven.

```
mvn azure-functions:deploy
```

Po zakończeniu wdrażania zostanie wyświetlony adres URL umożliwiający uzyskanie dostępu do aplikacji funkcji platformy Azure:

```output
[INFO] Successfully deployed Function App with package.
[INFO] Deleting deployment package from Azure Storage...
[INFO] Successfully deleted deployment package fabrikam-function-20170920120101928.20170920143621915.zip
[INFO] Successfully deployed Function App at https://fabrikam-function-20170920120101928.azurewebsites.net
[INFO] ------------------------------------------------------------------------
```

## <a name="next-steps"></a>Kolejne kroki

- Zapoznaj się z [Przewodnikiem dewelopera po funkcjach języka Java](functions-reference-java.md), aby uzyskać więcej informacji na temat tworzenia funkcji języka Java.
- Dodaj do swojego projektu kolejne funkcje z różnymi wyzwalaczami, używając elementu docelowego `azure-functions:add` narzędzia Maven.
