---
title: Tworzenie pierwszej funkcji na platformie Azure przy użyciu języka Java i narzędzia Maven | Microsoft Docs
description: Tworzenie prostej funkcji wyzwalanej przez protokół HTTP i publikowanie jej na platformie Azure przy użyciu języka Java i narzędzia Maven.
services: functions
documentationcenter: na
author: rloutlaw
manager: justhe
keywords: azure functions, funkcje, przetwarzanie zdarzeń, obliczenia, architektura bez serwera
ms.service: azure-functions
ms.devlang: java
ms.topic: quickstart
ms.date: 08/10/2018
ms.author: routlaw
ms.reviewer: glenga
ms.custom: mvc, devcenter
ms.openlocfilehash: fcbf181601230493dc52bde06e4f35db062f9a32
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807169"
---
# <a name="create-your-first-function-with-java-and-maven"></a>Tworzenie pierwszej funkcji przy użyciu języka Java i narzędzia Maven

Ten artykuł przeprowadzi Cię przez przy użyciu narzędzia wiersza polecenia narzędzia Maven, aby tworzyć i publikować funkcji języka Java usługi Azure Functions. Gdy wszystko będzie gotowe, Twój kod funkcji zostanie uruchomiony w [planie zużycia](functions-scale.md#consumption-plan) na platformie Azure i może być wyzwalany za pomocą żądania HTTP.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby opracowywać funkcje przy użyciu języka Java, musisz mieć zainstalowane następujące składniki:

- [Zestaw Java Developer Kit](https://aka.ms/azure-jdks), wersja 8
- [Narzędzia Apache Maven](https://maven.apache.org), wersja 3.0 lub nowszej
- [Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure)
- [Podstawowe narzędzia usługi Azure Functions](./functions-run-local.md#v2) wersji 2.6.666 lub nowszy

> [!IMPORTANT]
> Aby wykonać wszystkie czynności opisane w tym przewodniku Szybki start, dla zmiennej środowiskowej JAVA_HOME należy ustawić lokalizację instalacji zestawu JDK.

## <a name="generate-a-new-functions-project"></a>Generowanie nowego projektu usługi Functions

W pustym folderze uruchom następujące polecenie, aby wygenerować projekt usługi Functions z [archetypu narzędzia Maven](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html).

### <a name="linuxmacos"></a>Linux/macOS

```bash
mvn archetype:generate \
    -DarchetypeGroupId=com.microsoft.azure \
    -DarchetypeArtifactId=azure-functions-archetype 
```

> [!NOTE]
> Jeśli masz problemy z uruchamianiem polecenia, zapoznaj się z tego, co `maven-archetype-plugin` wersja jest używana. Ponieważ polecenia są uruchomione w pustym katalogu bez `.pom` pliku, jego może próbować użyć wtyczki starszej wersji z `~/.m2/repository/org/apache/maven/plugins/maven-archetype-plugin` w przypadku uaktualnienia ze starszej wersji usługi Maven. Jeśli tak, spróbuj usunąć `maven-archetype-plugin` katalogu i uruchom ponownie polecenie.

### <a name="windows"></a>Windows

```powershell
mvn archetype:generate `
    "-DarchetypeGroupId=com.microsoft.azure" `
    "-DarchetypeArtifactId=azure-functions-archetype"
```

```cmd
mvn archetype:generate ^
    "-DarchetypeGroupId=com.microsoft.azure" ^
    "-DarchetypeArtifactId=azure-functions-archetype"
```

Narzędzie Maven poprosi o podanie wartości wymaganych do ukończenia generowania projektu. Aby uzyskać informacje o wartościach _groupId_, _artifactId_ i _version_, zobacz materiały referencyjne [Konwencja nazewnictwa narzędzia Maven](https://maven.apache.org/guides/mini/guide-naming-conventions.html). Wartość _appName_ musi być unikatowa w obrębie platformy Azure, dlatego narzędzie Maven generuje domyślną nazwę aplikacji na podstawie wprowadzonej wcześniej wartości _artifactId_. Wartość _packageName_ określa pakiet Java dla generowanego kodu funkcji.

Poniższe identyfikatory `com.fabrikam.functions` i `fabrikam-functions` są używane jako przykład i poprawiają czytelność dalszych kroków w tym przewodniku Szybki start. Zachęcamy do podania własnych wartości w narzędziu Maven w tym kroku.

```Output
Define value for property 'groupId' (should match expression '[A-Za-z0-9_\-\.]+'): com.fabrikam.functions
Define value for property 'artifactId' (should match expression '[A-Za-z0-9_\-\.]+'): fabrikam-functions
Define value for property 'version' 1.0-SNAPSHOT : 
Define value for property 'package': com.fabrikam.functions
Define value for property 'appName' fabrikam-functions-20170927220323382:
Define value for property 'appRegion' westus: :
Define value for property 'resourceGroup' java-functions-group: :
Confirm properties configuration: Y
```

Narzędzie Maven utworzy pliki projektu w nowym folderze o nazwie podanej we właściwości _artifactId_. W tym przykładzie jest to `fabrikam-functions`. Jest gotowy do uruchomienia kod wygenerowany w projekcie [wyzwalaną przez protokół HTTP](/azure/azure-functions/functions-bindings-http-webhook) funkcja, która zwraca treść żądania. Zastąp *src/main/java/com/fabrikam/functions/Function.java* następującym kodem: 

```java
package com.fabrikam.functions;

import java.util.*;
import com.microsoft.azure.functions.annotation.*;
import com.microsoft.azure.functions.*;

public class Function {
    /**
     * This function listens at endpoint "/api/hello". Two ways to invoke it using "curl" command in bash:
     * 1. curl -d "HTTP Body" {your host}/api/hello
     * 2. curl {your host}/api/hello?name=HTTP%20Query
     */
    @FunctionName("hello")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", methods = { HttpMethod.GET, HttpMethod.POST }, authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> request,
            final ExecutionContext context) {
        context.getLogger().info("Java HTTP trigger processed a request.");

        // Parse query parameter
        String query = request.getQueryParameters().get("name");
        String name = request.getBody().orElse(query);

        if (name == null) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST).body("Please pass a name on the query string or in the request body").build();
        } else {
            return request.createResponseBuilder(HttpStatus.OK).body("Hello, " + name).build();
        }
    }
}

```

## <a name="enable-extension-bundles"></a>Włącz rozszerzenie pakiety

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

## <a name="run-the-function-locally"></a>Lokalne uruchamianie funkcji

Zmień katalog na nowo utworzony folder projektu, a następnie skompiluj i uruchom funkcję za pomocą narzędzia Maven:

```
cd fabrikam-function
mvn clean package 
mvn azure-functions:run
```

> [!NOTE]
> Jeśli wystąpi wyjątek `javax.xml.bind.JAXBException` w przypadku używania platformy Java 9, zobacz obejście w serwisie [GitHub](https://github.com/jOOQ/jOOQ/issues/6477).

Te dane wyjściowe są wyświetlane, gdy funkcja jest uruchomiona lokalnie w systemie i jest gotowa do odpowiadania na żądania HTTP:

```Output
Listening on http://localhost:7071
Hit CTRL-C to exit...

Http Functions:

   hello: http://localhost:7071/api/hello
```

Wyzwól funkcję z poziomu wiersza polecenia, używając programu curl w nowym oknie terminalu:

```
curl -w "\n" http://localhost:7071/api/hello -d LocalFunction
```

```Output
Hello LocalFunction!
```

Aby zatrzymać wykonywanie kodu funkcji, użyj polecenia `Ctrl-C` w oknie terminala.

## <a name="deploy-the-function-to-azure"></a>Wdrażanie funkcji na platformie Azure

Proces wdrażania w usłudze Azure Functions korzysta z poświadczeń konta z interfejsu wiersza polecenia platformy Azure. [Zaloguj się przy użyciu wiersza polecenia platformy Azure](/cli/azure/authenticate-azure-cli?view=azure-cli-latest) przed kontynuowaniem.

```azurecli
az login
```

Wdróż swój kod w nowej aplikacji funkcji, używając elementu docelowego `azure-functions:deploy` narzędzia Maven. Spowoduje to wykonanie [Zip wdrażania z uruchamiania z pakietem](functions-deployment-technologies.md#zip-deploy) włączony tryb.

> [!NOTE]
> Korzystając z programu Visual Studio Code do wdrażania aplikacji funkcji, pamiętaj, aby wybrać subskrypcję — bezpłatne lub otrzymają komunikat o błędzie. Możesz obejrzeć subskrypcji po lewej stronie IDE.

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

Przetestuj działanie aplikacji funkcji na platformie Azure, używając programu `cURL`. Musisz zmienić adres URL z poniższego przykładu tak, aby był zgodny z adresem URL Twojej wdrożonej aplikacji funkcji uzyskanym w poprzednim kroku.

> [!NOTE]
> Upewnij się, możesz ustawić **prawa dostępu** do `Anonymous`. Po wybraniu domyślnego poziomu `Function`, są wymagane, aby przedstawić [funkcyjne](../azure-functions/functions-bindings-http-webhook.md#authorization-keys) w żądaniach wysyłanych do uzyskania dostępu do punktu końcowego funkcji.

```
curl -w "\n" https://fabrikam-function-20170920120101928.azurewebsites.net/api/hello -d AzureFunctions
```

```Output
Hello AzureFunctions!
```

## <a name="make-changes-and-redeploy"></a>Wprowadzanie zmian i ponowne wdrażanie

Otwórz do edycji plik źródłowy `src/main.../Function.java` w wygenerowanym projekcie, aby zmienić tekst zwracany przez aplikację funkcji. Zmień ten wiersz:

```java
return request.createResponse(200, "Hello, " + name);
```

Na następujący:

```java
return request.createResponse(200, "Hi, " + name);
```

Zapisz zmiany. Pakiet czyste mvn wykonywania i ponowne wdrażanie, uruchamiając `azure-functions:deploy` z poziomu terminalu, tak jak poprzednio. Aplikacja funkcji zostanie zaktualizowana, a żądanie:

```bash
curl -w '\n' -d AzureFunctionsTest https://fabrikam-functions-20170920120101928.azurewebsites.net/api/hello
```

będzie miało zaktualizowane dane wyjściowe:

```Output
Hi, AzureFunctionsTest
```

## <a name="next-steps"></a>Następne kroki

Utworzyliśmy aplikację funkcji języka Java z prostym wyzwalaczem HTTP i wdrożyliśmy ją w usłudze Azure Functions.

- Zapoznaj się z [Przewodnikiem dewelopera po funkcjach języka Java](functions-reference-java.md), aby uzyskać więcej informacji na temat tworzenia funkcji języka Java.
- Dodaj do swojego projektu kolejne funkcje z różnymi wyzwalaczami, używając elementu docelowego `azure-functions:add` narzędzia Maven.
- Pisanie i debugowanie funkcji lokalnie za pomocą oprogramowania [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions), [IntelliJ](functions-create-maven-intellij.md) i [Eclipse](functions-create-maven-eclipse.md). 
- Debuguj funkcje wdrożone na platformie Azure przy użyciu programu Visual Studio Code. Zobacz dokumentację [bezserwerowych aplikacji Java](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud) programu Visual Studio Code, aby uzyskać instrukcje.
