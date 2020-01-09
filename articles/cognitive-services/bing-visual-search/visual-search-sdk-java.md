---
title: 'Szybki Start: wyszukiwanie wizualne Binga Biblioteka kliencka dla języka Java | Microsoft Docs'
description: Przekazywanie obrazu przy użyciu zestawu SDK wyszukiwania wizualnego Bing oraz uzyskiwanie szczegółowych informacji na jego temat.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ''
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: aahi
ms.openlocfilehash: 5a6d39fa6e10ad0ad102f9d25ffd252ec9e0fa8a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75379502"
---
# <a name="quickstart-bing-visual-search-client-library-for-java"></a>Szybki Start: wyszukiwanie wizualne Binga Biblioteka kliencka dla języka Java

Wprowadzenie do biblioteki klienta wyszukiwanie wizualne Bing dla środowiska Java. Wykonaj następujące kroki, aby zainstalować pakiet i wypróbować przykładowy kod dla podstawowych zadań. 

Użyj biblioteki klienta wyszukiwanie wizualne Bing dla języka Java, aby:

* Przekaż obraz, aby wysłać żądanie wyszukiwania wizualnego.
* Pobierz token Insights i Tagi wyszukiwania wizualnego.

[Dokumentacja referencyjna](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/bingvisualsearch?view=azure-java-stable) | [kod źródłowy biblioteki](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Search.BingVisualSearch) | [artefakt (Maven)](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-visualsearch/) | [przykłady](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/)
* Bieżąca wersja [zestawu Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Narzędzie kompilacji Gradle](https://gradle.org/install/)lub inny Menedżer zależności

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

Po otrzymaniu klucza z subskrypcji próbnej lub zasobu [Utwórz zmienną środowiskową](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) dla klucza o nazwie `BING_SEARCH_V7_SUBSCRIPTION_KEY`.

### <a name="create-a-new-gradle-project"></a>Utwórz nowy projekt Gradle

W oknie konsoli (na przykład cmd, PowerShell lub bash) Utwórz nowy katalog dla aplikacji i przejdź do niego. 

```console
mkdir myapp && cd myapp
```

Uruchom `gradle init` polecenie w katalogu roboczym. To polecenie spowoduje utworzenie podstawowych plików kompilacji dla Gradle, w tym *Build. Gradle. KTS* , który jest używany w środowisku uruchomieniowym do tworzenia i konfigurowania aplikacji.

```console
gradle init --type basic
```

Po wyświetleniu monitu wybierz pozycję **Język DSL**, a następnie **Kotlin**.

Znajdź element *Build. Gradle. KTS* i otwórz go za pomocą PREFEROWANEGO środowiska IDE lub edytora tekstu. Następnie skopiuj go do tej konfiguracji kompilacji:

```kotlin
plugins {
    java
    application
}
application {
    mainClassName = "main.java.BingVisualSearchSample"
}
repositories {
    mavenCentral()
}
dependencies {
    compile("org.slf4j:slf4j-simple:1.7.25")
    compile("com.microsoft.azure.cognitiveservices:azure-cognitiveservices-visualsearch:1.0.2-beta")
    compile("com.google.code.gson:gson:2.8.5")
}
```

Utwórz folder dla przykładowej aplikacji. W katalogu roboczym Uruchom następujące polecenie:

```console
mkdir -p src/main/java
```

Utwórz folder dla obrazu, który ma zostać przekazany do interfejsu API. Umieść obraz wewnątrz folderu **resources** .

```console
mkdir -p src/main/resources
``` 

Przejdź do nowego folderu i Utwórz plik o nazwie *BingVisualSearchSample. Java*. Otwórz go w preferowanym edytorze lub w środowisku IDE i Dodaj następujące instrukcje `import`:

[!code-java[Import statements](~/cognitive-services-java-sdk-samples/Search/BingVisualSearch/src/main/java/BingVisualSearchSample.java?name=imports)]

Następnie utwórz nową klasę.

```java
public class BingVisualSearchSample {
}
```

W metodzie `main` aplikacji Utwórz zmienne dla punktu końcowego i klucza usługi Azure Resource. Jeśli zmienna środowiskowa została utworzona po uruchomieniu aplikacji, należy zamknąć i ponownie otworzyć Edytor, środowisko IDE lub powłokę, na których jest uruchomiona, aby uzyskać dostęp do zmiennej. Następnie utwórz `byte[]` obrazu, który będzie przekazywany. Utwórz blok `try` dla metod, które zostaną zdefiniowane później, i Załaduj obraz i przekonwertuj go na bajty przy użyciu `toByteArray()`.

[!code-java[Main method](~/cognitive-services-java-sdk-samples/Search/BingVisualSearch/src/main/java/BingVisualSearchSample.java?name=main)]


### <a name="install-the-client-library"></a>Zainstaluj bibliotekę kliencką

Ten przewodnik Szybki Start używa Menedżera zależności Gradle. Bibliotekę i informacje o kliencie można znaleźć dla innych menedżerów zależności w [repozytorium centralnym Maven](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-textanalytics/).

Upewnij się, że w pliku *Build. Gradle. KTS* projektu została uwzględniona Biblioteka kliencka jako instrukcja `implementation`. 

```kotlin
dependencies {
    compile("org.slf4j:slf4j-simple:1.7.25")
    compile("com.microsoft.azure.cognitiveservices:azure-cognitiveservices-visualsearch:1.0.2-beta")
    compile("com.google.code.gson:gson:2.8.5")
}
```

## <a name="code-examples"></a>Przykłady kodu

Te fragmenty kodu przedstawiają sposób wykonywania następujących zadań przy użyciu biblioteki klienta wyszukiwanie wizualne Bing i środowiska Java:

* [Uwierzytelnianie klienta](#authenticate-the-client)
* [Wyślij żądanie wyszukiwania wizualnego](#send-a-visual-search-request)
* [Drukuj token wglądu w obraz i Tagi wyszukiwania wizualnego](#print-the-image-insight-token-and-visual-search-tags)

## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

> [!NOTE]
> W tym przewodniku szybki start przyjęto założenie, że dla klucza wyszukiwanie wizualne Bing została [utworzona zmienna środowiskowa](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) o nazwie `BING_SEARCH_V7_SUBSCRIPTION_KEY`.


W metodzie Main upewnij się, że używasz klucza subskrypcji, aby utworzyć wystąpienie obiektu [BingVisualSearchAPI](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.visualsearch.bingvisualsearchapi?view=azure-java-stable) .

```csharp
BingVisualSearchAPI client = BingVisualSearchManager.authenticate(subscriptionKey);
```

## <a name="send-a-visual-search-request"></a>Wyślij żądanie wyszukiwania wizualnego

W nowej metodzie należy wysłać tablicę bajtową obrazu (która została utworzona w metodzie `main()`) przy użyciu metody [bingImages (). visualSearch (](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.visualsearch.bingimages.visualsearch?view=azure-java-stable#com_microsoft_azure_cognitiveservices_search_visualsearch_BingImages_visualSearch__) ) klienta. 

[!code-java[visualSearch() method](~/cognitive-services-java-sdk-samples/Search/BingVisualSearch/src/main/java/BingVisualSearchSample.java?name=visualSearch)]

## <a name="print-the-image-insight-token-and-visual-search-tags"></a>Drukuj token wglądu w obraz i Tagi wyszukiwania wizualnego

Sprawdź, czy obiekt [ImageKnowledge](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.visualsearch.models.imageknowledge?view=azure-java-stable) ma wartość null. W przeciwnym razie należy wydrukować token Insights, liczbę tagów, liczbę akcji i typ pierwszego działania.

[!code-java[Print token and tags](~/cognitive-services-java-sdk-samples/Search/BingVisualSearch/src/main/java/BingVisualSearchSample.java?name=printVisualSearchResults)]

## <a name="run-the-application"></a>Uruchamianie aplikacji

Aplikację można skompilować przy użyciu:

```console
gradle build
```

Uruchom aplikację z celem `run`:

```console
gradle run
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli chcesz wyczyścić i usunąć subskrypcję Cognitive Services, możesz usunąć zasób lub grupę zasobów. Usunięcie grupy zasobów spowoduje również usunięcie wszystkich skojarzonych z nią zasobów.

* [Portal](../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfejs wiersza polecenia platformy Azure](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie jednostronicowej aplikacji internetowej](tutorial-bing-visual-search-single-page-app.md)
