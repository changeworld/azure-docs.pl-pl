---
title: Utwórz aplikację funkcji platformy Azure przy użyciu języka Java i środowiska Eclipse | Dokumentacja firmy Microsoft
description: Przewodnik do tworzenia i publikowania prostego HTTP wyzwalane aplikacji bez użycia serwera za pomocą języka Java i Eclipse do usługi Azure Functions.
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
ms.openlocfilehash: 123a24eb13de584d8e3b70d0d8b1173f583867c1
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "58881430"
---
# <a name="create-your-first-function-with-java-and-eclipse"></a>Tworzenie pierwszej funkcji przy użyciu języka Java i Eclipse 

W tym artykule przedstawiono sposób tworzenia [bezserwerowe](https://azure.microsoft.com/solutions/serverless/) projektu funkcji za pomocą środowiska Eclipse IDE i narzędzia Apache Maven testowania i debugowania, a następnie wdrożyć go w usłudze Azure Functions. 

<!-- TODO ![Access a Hello World function from the command line with cURL](media/functions-create-java-maven/hello-azure.png) -->

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-development-environment"></a>Konfigurowanie środowiska projektowego

Aby opracować aplikacje funkcji przy użyciu języka Java i Eclipse, musi mieć zainstalowane następujące oprogramowanie:

-  Zestaw [Java Developer Kit](https://www.azul.com/downloads/zulu/), wersja 8.
-  Narzędzie [Apache Maven](https://maven.apache.org), wersja 3.0 lub nowsza.
-  [Eclipse](https://www.eclipse.org/downloads/packages/), za pomocą języka Java i Maven obsługuje.
-  [Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure)

> [!IMPORTANT] 
> Aby wykonać wszystkie czynności opisane w tym przewodniku Szybki start, dla zmiennej środowiskowej JAVA_HOME należy ustawić lokalizację instalacji zestawu JDK.

Zdecydowanie zalecane jest również instalowanie [funkcji podstawowych narzędzi usługi Azure, wersja 2](functions-run-local.md#v2), które zapewniają środowiska lokalnego do uruchamiania i debugowania usługi Azure Functions. 

## <a name="create-a-functions-project"></a>Tworzenie projektu funkcji

1. W środowisku Eclipse wybierz **pliku** menu, następnie wybierz pozycję **projektu**. 
1. Otwórz **projektu w języku Java** folderu w **nowy projekt** okna, a następnie wybierz pozycję **Projekt narzędzia Maven**, a następnie wybierz **dalej**.
1. Zaakceptuj ustawienia domyślne w **nowy projekt narzędzia Maven** dialog i wybierz **dalej**.
1. Wybierz **Dodaj Archetype** i Dodaj pozycje dla [azure-functions-archetype](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype).
    - Identyfikator grupy archetype: com.microsoft.azure
    - Archetype identyfikator artefaktu: azure-functions-archetype
    - Wersja: Użyj najnowszej wersji z [centralne repozytorium](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype)
    ![tworzenie Maven środowiska Eclipse](media/functions-create-first-java-eclipse/functions-create-eclipse.png)  
1. Kliknij przycisk **OK** i wprowadź szczegóły dla bieżącego projektu i ostatecznie **Zakończ**.

Narzędzie Maven tworzy pliki projektu w nowym folderze o nazwie wartości _artifactId_. Kod wygenerowany w projekcie jest prostą [wyzwalaną przez protokół HTTP](/azure/azure-functions/functions-bindings-http-webhook) funkcja, która zwraca treść wyzwalająca żądania HTTP.

## <a name="run-functions-locally-in-the-ide"></a>Uruchom funkcje lokalnie w środowisku IDE

> [!NOTE]
> [Podstawowe narzędzia usługi Azure Functions, wersja 2](functions-run-local.md#v2) musi być zainstalowany na uruchamianie i debugowanie funkcji lokalnie.

1. Kliknij prawym przyciskiem myszy na wygenerowanego projektu, a następnie wybierz **Uruchom jako** i **kompilacji narzędzia Maven**.
1. W **Edycja konfiguracji** okno dialogowe, wprowadź `package` w **cele** i **nazwa** pól, następnie wybierz pozycję **Uruchom**. To skompiluje oraz pakiet kodu funkcji.
1. Po zakończeniu kompilacji należy utworzyć inny konfiguracji Uruchom jak powyżej, za pomocą `azure-functions:run` jako cel i nazwa. Wybierz **Uruchom** do uruchomienia tej funkcji w środowisku IDE.

Zakończenie środowiska uruchomieniowego w oknie konsoli po zakończeniu testowania funkcji. Tylko jednej funkcji — host może być aktywny i działa lokalnie w danym momencie.

### <a name="debug-the-function-in-eclipse"></a>Debugowanie funkcji w środowisku Eclipse

W swojej **Uruchom jako** w poprzednim kroku, a zmiana konfiguracji `azure-functions:run` do `mvn azure-functions:run -DenableDebug` i uruchomić zaktualizowaną konfiguracją można uruchomić aplikacji funkcji w trybie debugowania.

Wybierz **Uruchom** menu i Otwórz **Debuguj konfiguracje**. Wybierz **zdalna aplikacja Java** i Utwórz nowe. Nadaj nazwę konfiguracji, a następnie wypełnij ustawienia. Port powinien być zgodny z portu debugowania otwierane przez hosta funkcji, która domyślnie jest `5005`. Po zakończeniu instalacji, kliknij pozycję `Debug` można rozpocząć debugowania.

![Debugowanie funkcji w środowisku Eclipse](media/functions-create-first-java-eclipse/debug-configuration-eclipse.PNG)

Ustaw punkty przerwania i zbadaj obiekty w funkcji za pomocą środowiska IDE. Po zakończeniu Zatrzymaj debuger i uruchamianie hosta funkcji. Tylko jednej funkcji — host może być aktywny i działa lokalnie w danym momencie.

## <a name="deploy-the-function-to-azure"></a>Wdrażanie funkcji na platformie Azure

Proces wdrażania w usłudze Azure Functions korzysta z poświadczeń konta z interfejsu wiersza polecenia platformy Azure. [Zaloguj się przy użyciu wiersza polecenia platformy Azure](/cli/azure/authenticate-azure-cli?view=azure-cli-latest) przed kontynuowaniem, przy użyciu wiersza polecenia na komputerze.

```azurecli
az login
```

Wdróż swój kod w nowej funkcji aplikacji, używając `azure-functions:deploy` celem narzędzia Maven w nowym **Uruchom jako** konfiguracji.

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
