---
title: Publikowanie funkcji na platformie Azure za pomocą języka Java i Maven/Gradle
description: Tworzenie i publikowanie funkcji wyzwalanej przez protokół HTTP na platformie Azure za pomocą języka Java i Maven lub Gradle.
author: KarlErickson
ms.author: karler
ms.topic: quickstart
ms.date: 08/10/2018
ms.custom: mvc, devcenter, seo-java-july2019, seo-java-august2019, seo-java-september2019
zone_pivot_groups: java-build-tools-set
ms.openlocfilehash: d0f30272a4e605449c946ca402db4f3ba00735bc
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80520537"
---
# <a name="quickstart-use-java-and-mavengradle-to-create-and-publish-a-function-to-azure"></a>Szybki start: tworzenie i publikowanie funkcji na platformie Azure za pomocą funkcji Java i Maven/Gradle

W tym artykule pokazano, jak skompilować i opublikować funkcję Java w usłudze Azure Functions za pomocą narzędzia wiersza polecenia Maven/Gradle. Po zakończeniu kodu funkcji jest uruchamiany na platformie Azure w [planie hostingu bezserwerowym](functions-scale.md#consumption-plan) i jest wyzwalany przez żądanie HTTP.

<!--
> [!NOTE] 
> You can also create a Kotlin-based Azure Functions project by using the azure-functions-kotlin-archetype instead. Visit the [GitHub repository](https://github.com/microsoft/azure-maven-archetypes/tree/develop/azure-functions-kotlin-archetype) for more information.
-->

## <a name="prerequisites"></a>Wymagania wstępne

Aby opracowywać funkcje przy użyciu języka Java, musisz mieć zainstalowane następujące składniki:

- Zestaw [Java Developer Kit](https://aka.ms/azure-jdks), wersja 8
- [Interfejs wiersza polecenia platformy Azure]
- [Narzędzia podstawowe usług Azure Functions w](./functions-run-local.md#v2) wersji 2.6.666 lub wyższej
::: zone pivot="java-build-tools-maven" 
- Narzędzie [Apache Maven](https://maven.apache.org), wersja 3.0 lub nowsza
::: zone-end

::: zone pivot="java-build-tools-gradle"  
- [Gradle](https://gradle.org/), wersja 4.10 i powyżej
::: zone-end 

Potrzebna jest również aktywna subskrypcja platformy Azure. [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


> [!IMPORTANT]
> Aby wykonać wszystkie czynności opisane w tym przewodniku Szybki start, dla zmiennej środowiskowej JAVA_HOME należy ustawić lokalizację instalacji zestawu JDK.

## <a name="prepare-a-functions-project"></a>Przygotowywanie projektu funkcji

::: zone pivot="java-build-tools-maven" 
W pustym folderze uruchom następujące polecenie, aby wygenerować projekt usługi Functions z [archetypu narzędzia Maven](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html).

```bash
mvn archetype:generate -DarchetypeGroupId=com.microsoft.azure -DarchetypeArtifactId=azure-functions-archetype 
```

> [!NOTE]
> Jeśli używasz programu Powershell, pamiętaj, aby dodać "" wokół parametrów.

> [!NOTE]
> Jeśli występują problemy z uruchomieniem polecenia, zapoznaj się `maven-archetype-plugin` z używaną wersją. Ponieważ polecenie jest uruchomione w pustym `.pom` katalogu bez pliku, może to być próba użycia `~/.m2/repository/org/apache/maven/plugins/maven-archetype-plugin` wtyczki starszej wersji, jeśli uaktualniono maven ze starszej wersji. Jeśli tak, spróbuj usunąć `maven-archetype-plugin` katalog i ponownie uruchomić polecenie.

Maven prosi o wartości potrzebne do zakończenia generowania projektu podczas wdrażania. Po wyświetleniu monitu podaj następujące wartości:

| Wartość | Opis |
| ----- | ----------- |
| **groupId** | Wartość, która jednoznacznie identyfikuje projekt we wszystkich projektach, zgodnie z [regułami nazewnictwa pakietów](https://docs.oracle.com/javase/specs/jls/se6/html/packages.html#7.7) dla języka Java. Przykłady w tym użyczynaniu przewodnika `com.fabrikam.functions`Szybki start . |
| **artifactId (ida artefaktu)** | Wartość, która jest nazwą słoika, bez numeru wersji. Przykłady w tym użyczynaniu przewodnika `fabrikam-functions`Szybki start . |
| **Wersja** | Wybierz wartość domyślną . `1.0-SNAPSHOT` |
| **Pakiet** | Wartość, która jest pakietem Java dla wygenerowanego kodu funkcji. Użyj wartości domyślnej. Przykłady w tym użyczynaniu przewodnika `com.fabrikam.functions`Szybki start . |

Wpisz `Y` lub naciśnij klawisz Enter, aby potwierdzić.

Maven tworzy pliki projektu w nowym folderze o nazwie **artifactId**, który w tym przykładzie jest `fabrikam-functions`. Uruchom następujące polecenie, aby zmienić katalog na utworzony folder projektu.
```bash
cd fabrikam-function
```

::: zone-end 
::: zone pivot="java-build-tools-gradle"
Użyj następującego polecenia, aby sklonować przykładowy projekt:

```bash
git clone https://github.com/Azure-Samples/azure-functions-samples-java.git
cd azure-functions-samples-java/
```

Otwórz `build.gradle` i `appName` zmień w poniższej sekcji na unikatową nazwę, aby uniknąć konfliktu nazw domen podczas wdrażania na platformie Azure. 

```gradle
azurefunctions {
    resourceGroup = 'java-functions-group'
    appName = 'azure-functions-sample-demo'
    pricingTier = 'Consumption'
    region = 'westus'
    runtime {
      os = 'windows'
    }
    localDebug = "transport=dt_socket,server=y,suspend=n,address=5005"
}
```
::: zone-end

Otwórz nowy plik Function.java ze ścieżki *src/main/java* w edytorze tekstu i przejrzyj wygenerowany kod. Ten kod jest funkcją [wyzwalaną HTTP,](functions-bindings-http-webhook.md) która odzwierciedla treść żądania. 

> [!div class="nextstepaction"]
> [Wystąpił problem](https://www.research.net/r/javae2e?tutorial=functions-maven-quickstart&step=generate-project)

## <a name="run-the-function-locally"></a>Lokalne uruchamianie funkcji

Uruchom następujące polecenie do kompilacji, a następnie uruchom projekt funkcji:

::: zone pivot="java-build-tools-maven" 
```bash
mvn clean package 
mvn azure-functions:run
```
::: zone-end 

::: zone pivot="java-build-tools-gradle"  
```bash
gradle jar --info
gradle azureFunctionsRun
```
::: zone-end 

Po uruchomieniu projektu lokalnie zostaną wyświetlone następujące dane wyjściowe:

```output
...

Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

Http Functions:

    HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
...
```

Wyzwolić funkcję z wiersza polecenia za pomocą cURL w nowym oknie terminala:

```bash
curl -w "\n" http://localhost:7071/api/HttpExample --data AzureFunctions
```

```output
Hello AzureFunctions!
```
[Klucz funkcyjny](functions-bindings-http-webhook-trigger.md#authorization-keys) nie jest wymagany podczas uruchamiania lokalnego. Aby zatrzymać wykonywanie kodu funkcji, użyj polecenia `Ctrl+C` w oknie terminala.

> [!div class="nextstepaction"]
> [Wystąpił problem](https://www.research.net/r/javae2e?tutorial=functions-maven-quickstart&step=local-run)

## <a name="deploy-the-function-to-azure"></a>Wdrażanie funkcji na platformie Azure

Aplikacja funkcji i powiązane zasoby są tworzone na platformie Azure podczas pierwszego wdrażania aplikacji funkcji. Przed wdrożeniem należy użyć polecenia interfejsu wiersza polecenia interfejsu wiersza polecenia platformy Azure [az,](/cli/azure/authenticate-azure-cli) aby zalogować się do subskrypcji platformy Azure. 

```azurecli
az login
```

> [!TIP]
> Jeśli Twoje konto może uzyskać dostęp do wielu subskrypcji, użyj [konta AZ ustawionego,](/cli/azure/account#az-account-set) aby ustawić domyślną subskrypcję dla tej sesji. 

Użyj następującego polecenia, aby wdrożyć projekt w nowej aplikacji funkcji. 


::: zone pivot="java-build-tools-maven" 
```bash
mvn azure-functions:deploy
```
::: zone-end 

::: zone pivot="java-build-tools-gradle"  
```bash
gradle azureFunctionsDeploy
```
::: zone-end

Spowoduje to utworzenie następujących zasobów na platformie Azure:

+ Grupa zasobów. Nazwany z _resourceGroup,_ które podano.
+ Konto magazynu. Wymagane przez funkcje. Nazwa jest generowana losowo na podstawie wymagań dotyczących nazwy konta magazynu.
+ Plan usługi aplikacji. Hosting bezserwerowy dla aplikacji funkcji w określonej _aplikacjiRegion_. Nazwa jest generowana losowo.
+ Aplikacja funkcyjna. Aplikacja funkcji jest jednostką wdrażania i wykonywania dla funkcji. Nazwa jest _appName_, dołączane z losowo generowanym numerem. 

Wdrożenie również pakiety plików projektu i wdraża je do nowej aplikacji funkcji za pomocą [wdrożenia zip](functions-deployment-technologies.md#zip-deploy), z włączonym trybem uruchamiania z pakietu.

Po zakończeniu wdrażania zostanie wyświetlony adres URL, którego można użyć do uzyskania dostępu do punktów końcowych aplikacji funkcji. Ponieważ wyzwalacz HTTP, który `authLevel = AuthorizationLevel.FUNCTION`opublikowaliśmy używa, musisz uzyskać klawisz funkcyjny, aby wywołać punkt końcowy funkcji za pośrednictwem protokołu HTTP. Najprostszym sposobem uzyskania klucza funkcji jest [portal Azure.]

> [!div class="nextstepaction"]
> [Wystąpił problem](https://www.research.net/r/javae2e?tutorial=functions-maven-quickstart&step=deploy)

## <a name="get-the-http-trigger-url"></a>Pobierz adres URL wyzwalacza HTTP

<!--- We can updates this to remove portal dependency after the Maven archetype returns the full URLs with keys on publish (https://github.com/microsoft/azure-maven-plugins/issues/571). -->

Możesz uzyskać adres URL wymagany do wyzwolenia funkcji za pomocą klucza funkcyjnego z witryny Azure portal. 

1. Przejdź do [witryny Azure portal], zaloguj się, wpisz _appName_ aplikacji funkcji w **search** u góry strony i naciśnij enter.
 
1. W aplikacji funkcji rozwiń **pozycję Funkcje (Tylko do odczytu)**, wybierz funkcję, a następnie wybierz **</> Pobierz adres URL funkcji** w prawym górnym rogu. 

    ![Kopiowanie adresu URL funkcji z witryny Azure Portal](./media/functions-create-java-maven/get-function-url-portal.png)

1. Wybierz **pozycję domyślną (klawisz funkcyjny)** i wybierz pozycję **Kopiuj**. 

Możesz teraz użyć skopiowanego adresu URL, aby uzyskać dostęp do swojej funkcji.

## <a name="verify-the-function-in-azure"></a>Weryfikowanie funkcji na platformie Azure

Aby zweryfikować aplikację funkcji `cURL`działającą na platformie Azure przy użyciu programu , zastąp adres URL z poniższego przykładu adresem URL skopiowanym z portalu.

```console
curl -w "\n" https://fabrikam-functions-20190929094703749.azurewebsites.net/api/HttpExample?code=zYRohsTwBlZ68YF.... --data AzureFunctions
```

Spowoduje to wysłanie żądania POST `AzureFunctions` do punktu końcowego funkcji z treścią żądania. Pojawi się następująca odpowiedź.

```output
Hello AzureFunctions!
```

> [!div class="nextstepaction"]
> [Wystąpił problem](https://www.research.net/r/javae2e?tutorial=functions-maven-quickstart&step=verify-deployment)

## <a name="next-steps"></a>Następne kroki

Utworzono projekt funkcji Java z funkcją wyzwalaną http, uruchom go na komputerze lokalnym i wdrożono go na platformie Azure. Teraz rozszerz swoją funkcję o...

> [!div class="nextstepaction"]
> [Dodawanie powiązania wyjściowego kolejki usługi Azure Storage](functions-add-output-binding-storage-queue-java.md)


[Interfejs wiersza polecenia platformy Azure]: /cli/azure
[Azure Portal]: https://portal.azure.com
