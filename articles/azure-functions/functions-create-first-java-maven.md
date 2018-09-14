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
ms.author: routlaw, glenga
ms.custom: mvc, devcenter
ms.openlocfilehash: 16d6dd6a589256ad98a37465e64e847778d0cc7e
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/07/2018
ms.locfileid: "44092597"
---
# <a name="create-your-first-function-with-java-and-maven-preview"></a>Tworzenie pierwszej funkcji przy użyciu języka Java i narzędzia Maven (wersja zapoznawcza)

[!INCLUDE [functions-java-preview-note](../../includes/functions-java-preview-note.md)]

Ten przewodnik Szybki start przeprowadzi Cię przez tworzenie projektu funkcji [bezserwerowej](https://azure.microsoft.com/overview/serverless-computing/) za pomocą narzędzia Maven, testowanie jej lokalnie i wdrażanie w usłudze Azure Functions. Po zakończeniu będziesz mieć działającą na platformie Azure funkcję wyzwalaną przez protokół HTTP.

![Uzyskiwanie dostępu do funkcji Hello world z poziomu wiersza polecenia za pomocą programu cURL](media/functions-create-java-maven/hello-azure.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne
Aby opracowywać aplikacje funkcji przy użyciu języka Java, trzeba mieć zainstalowane następujące składniki:

-  Zestaw [Java Developer Kit](https://www.azul.com/downloads/zulu/), wersja 8.
-  Narzędzie [Apache Maven](https://maven.apache.org), wersja 3.0 lub nowsza.
-  [Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure)

> [!IMPORTANT] 
> Aby wykonać wszystkie czynności opisane w tym przewodniku Szybki start, dla zmiennej środowiskowej JAVA_HOME należy ustawić lokalizację instalacji zestawu JDK.

## <a name="install-the-azure-functions-core-tools"></a>Instalowanie podstawowych narzędzi usługi Azure Functions

Podstawowe narzędzia usługi Azure Functions zapewniają lokalne środowisko deweloperskie do programowania, uruchamiania i debugowania w usłudze Azure Functions z terminalu lub wiersza polecenia. 

Zainstaluj [podstawowe narzędzia w wersji 2](functions-run-local.md#v2) na komputerze lokalnym, aby móc kontynuować.

## <a name="generate-a-new-functions-project"></a>Generowanie nowego projektu usługi Functions

W pustym folderze uruchom następujące polecenie, aby wygenerować projekt usługi Functions z [archetypu narzędzia Maven](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html).

### <a name="linuxmacos"></a>Linux/MacOS

```bash
mvn archetype:generate \
    -DarchetypeGroupId=com.microsoft.azure \
    -DarchetypeArtifactId=azure-functions-archetype 
```

### <a name="windows-cmd"></a>Windows (CMD)
```cmd
mvn archetype:generate ^
    -DarchetypeGroupId=com.microsoft.azure ^
    -DarchetypeArtifactId=azure-functions-archetype
```

Narzędzie Maven poprosi o podanie wartości wymaganych do ukończenia generowania projektu. Aby uzyskać informacje o wartościach _groupId_, _artifactId_ i _version_, zobacz materiały referencyjne [Konwencja nazewnictwa narzędzia Maven](https://maven.apache.org/guides/mini/guide-naming-conventions.html). Wartość _appName_ musi być unikatowa w obrębie platformy Azure, dlatego narzędzie Maven generuje domyślną nazwę aplikacji na podstawie wprowadzonej wcześniej wartości _artifactId_. Wartość _packageName_ określa pakiet Java dla generowanego kodu funkcji.

Wartość `appRegion` określa [region platformy Azure](https://azure.microsoft.com/global-infrastructure/regions/), w którym ma zostać uruchomiona wdrożona aplikacja funkcji. Listę wartości dla nazw regionów można uzyskać za pomocą polecenia `az account list-locations` w interfejsie wiersza polecenia platformy Azure. Wartość `resourceGroup` określa grupę zasobów platformy Azure, w której zostanie utworzona aplikacja funkcji.

Poniższe identyfikatory `com.fabrikam.functions` i `fabrikam-functions` są używane jako przykład i poprawiają czytelność dalszych kroków w tym przewodniku Szybki start. Zachęcamy do podania własnych wartości w narzędziu Maven w tym kroku.

```Output
Define value for property 'groupId': com.fabrikam.functions
Define value for property 'artifactId' : fabrikam-functions
Define value for property 'version' 1.0-SNAPSHOT : 
Define value for property 'package': com.fabrikam.functions
Define value for property 'appName' fabrikam-functions-20170927220323382:
Define value for property 'appRegion' westus : 
Define value for property 'resourceGroup' java-functions-group: 
Confirm properties configuration: Y
```

Narzędzie Maven utworzy pliki projektu w nowym folderze o nazwie podanej we właściwości _artifactId_. W tym przykładzie jest to `fabrikam-functions`. Gotowy do uruchomienia kod wygenerowany w projekcie jest prostą funkcją [wyzwalaną przez protokół HTTP](/azure/azure-functions/functions-bindings-http-webhook), która zwraca treść żądania za ciągiem „Hello, ”.

```java
public class Function {
    @FunctionName("HttpTrigger-Java")
    public HttpResponseMessage HttpTriggerJava(
    @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> request,final ExecutionContext context) {
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

## <a name="run-the-function-locally"></a>Lokalne uruchamianie funkcji

Zmień katalog na nowo utworzony folder projektu, a następnie skompiluj i uruchom funkcję za pomocą narzędzia Maven:

```
cd fabrikam-functions
mvn clean package 
mvn azure-functions:run
```

> [!NOTE]
> Jeśli wystąpi wyjątek `javax.xml.bind.JAXBException` w przypadku używania platformy Java 9, zobacz obejście w serwisie [GitHub](https://github.com/jOOQ/jOOQ/issues/6477).

Te dane wyjściowe są wyświetlane, gdy funkcja jest uruchomiona lokalnie w systemie i jest gotowa do odpowiadania na żądania HTTP:

```Output
Listening on http://0.0.0.0:7071/
Hit CTRL-C to exit...

Http Functions:

        HttpTrigger-Java: http://localhost:7071/api/HttpTrigger-Java
```

Wyzwól funkcję z poziomu wiersza polecenia, używając programu curl w nowym oknie terminalu:

```
curl -w '\n' -d LocalFunctionTest http://localhost:7071/api/HttpTrigger-Java
```

```Output
Hello, LocalFunctionTest
```

Aby zatrzymać wykonywanie kodu funkcji, użyj polecenia `Ctrl-C` w oknie terminala.

## <a name="deploy-the-function-to-azure"></a>Wdrażanie funkcji na platformie Azure

Proces wdrażania w usłudze Azure Functions korzysta z poświadczeń konta z interfejsu wiersza polecenia platformy Azure. Aby kontynuować, [zaloguj się przy użyciu interfejsu wiersza polecenia platformy Azure](/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

```azurecli
az login
```

Wdróż swój kod w nowej aplikacji funkcji, używając elementu docelowego `azure-functions:deploy` narzędzia Maven.

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

```
curl -w '\n' -d AzureFunctionsTest https://fabrikam-functions-20170920120101928.azurewebsites.net/api/HttpTrigger-Java
```

```Output
Hello, AzureFunctionsTest
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

Zapisz zmiany i wdróż ponownie, uruchamiając polecenie `azure-functions:deploy` z poziomu terminalu, tak jak poprzednio. Aplikacja funkcji zostanie zaktualizowana, a żądanie:

```bash
curl -w '\n' -d AzureFunctionsTest https://fabrikam-functions-20170920120101928.azurewebsites.net/api/HttpTrigger-Java
```

będzie miało zaktualizowane dane wyjściowe:

```Output
Hi, AzureFunctionsTest
```

## <a name="next-steps"></a>Następne kroki

Aplikacja funkcji języka Java z prostym wyzwalaczem HTTP została utworzona i wdrożona w usłudze Azure Functions.

- Zapoznaj się z [Przewodnikiem dewelopera po funkcjach języka Java](functions-reference-java.md), aby uzyskać więcej informacji na temat tworzenia funkcji języka Java.
- Dodaj do swojego projektu kolejne funkcje z różnymi wyzwalaczami, używając elementu docelowego `azure-functions:add` narzędzia Maven.
- Pisanie i debugowanie funkcji lokalnie za pomocą oprogramowania [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions), [IntelliJ](functions-create-maven-intellij.md) i [Eclipse](functions-create-maven-eclipse.md). 
- Debuguj funkcje wdrożone na platformie Azure przy użyciu programu Visual Studio Code. Zobacz dokumentację [bezserwerowych aplikacji Java](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud) programu Visual Studio Code, aby uzyskać instrukcje.
