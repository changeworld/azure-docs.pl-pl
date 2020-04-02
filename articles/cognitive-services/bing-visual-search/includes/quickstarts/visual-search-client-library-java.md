---
title: Szybki start biblioteki klienta Java wyszukiwania wizualnego Bing
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/26/2020
ms.author: aahi
ms.openlocfilehash: 40432d4bad0070f9de21ec61020d976f0014e00c
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80550016"
---
Użyj tego przewodnika Szybki start, aby rozpocząć uzyskiwanie szczegółowych informacji o obrazie z usługi wyszukiwania wizualnego Bing przy użyciu biblioteki klienta Java. Usługa Bing Visual Search ma interfejs API REST zgodny z większością języków programowania, ale biblioteka klienta zapewnia łatwy sposób integracji usługi z aplikacjami. Kod źródłowy tego przewodnika Szybki start można znaleźć na [GitHub](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingVisualSearch).

Użyj biblioteki klienta wyszukiwania wizualnego Bing dla oprogramowania Java, aby:

* Przekaż obraz, aby wysłać żądanie wyszukiwania wizualnego.
* Pobierz token wglądu w obraz i wizualne tagi wyszukiwania.

[Przykłady](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/bingvisualsearch?view=azure-java-stable) | [kodu źródłowego biblioteki](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Search.BingVisualSearch) | referencyjnej Artifact[(Maven)](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-visualsearch/) | [Samples](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [utwórz bezpłatną subskrypcję platformy Azure](https://azure.microsoft.com/free/)
* Aktualna wersja [zestawu Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Narzędzie do budowania Gradle](https://gradle.org/install/)lub inny menedżer zależności

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](~/includes/cognitive-services-bing-visual-search-signup-requirements.md)]

Po dostaniu klucza z subskrypcji próbnej lub zasobu `BING_SEARCH_V7_SUBSCRIPTION_KEY` [utwórz zmienną środowiskową](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) dla klucza o nazwie .

### <a name="create-a-new-gradle-project"></a>Tworzenie nowego projektu Gradle

W oknie konsoli (takim jak cmd, PowerShell lub Bash) utwórz nowy katalog aplikacji i przejdź do niego. 

```console
mkdir myapp && cd myapp
```

Uruchom `gradle init` polecenie z katalogu roboczego. To polecenie spowoduje utworzenie podstawowych plików kompilacji dla gradle, w tym *build.gradle.kts,* który jest używany w czasie wykonywania do tworzenia i konfigurowania aplikacji.

```console
gradle init --type basic
```

Po wyświetleniu monitu wybierz pozycję **Język DSL**, a następnie **Kotlin**.

Znajdź *build.gradle.kts* i otwórz go za pomocą preferowanego edytora IDE lub tekstu. Następnie skopiuj go do tej konfiguracji kompilacji:

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

Utwórz folder dla przykładowej aplikacji. W katalogu roboczym uruchom następujące polecenie:

```console
mkdir -p src/main/java
```

Utwórz folder dla obrazu, który chcesz przekazać do interfejsu API. Umieść obraz w folderze **zasobów.**

```console
mkdir -p src/main/resources
``` 

Przejdź do nowego folderu i utwórz plik o nazwie *BingVisualSearchSample.java*. Otwórz go w preferowanym edytorze `import` lub IDE i dodaj następujące instrukcje:

[!code-java[Import statements](~/cognitive-services-java-sdk-samples/Search/BingVisualSearch/src/main/java/BingVisualSearchSample.java?name=imports)]

Następnie utwórz nową klasę.

```java
public class BingVisualSearchSample {
}
```

W `main` metodzie aplikacji utwórz zmienne dla punktu końcowego i klucza platformy Azure zasobu. Jeśli po uruchomieniu aplikacji utworzono zmienną środowiskową, należy zamknąć i ponownie otworzyć edytor, IDE lub powłokę uruchamianą ją, aby uzyskać dostęp do zmiennej. Następnie utwórz `byte[]` dla obrazu, który będziesz przesyłać. Utwórz `try` blok dla metod, które zdefiniujesz później, a następnie `toByteArray()`załaduj obraz i przekonwertuj go na bajty za pomocą programu .

[!code-java[Main method](~/cognitive-services-java-sdk-samples/Search/BingVisualSearch/src/main/java/BingVisualSearchSample.java?name=main)]


### <a name="install-the-client-library"></a>Instalowanie biblioteki klienta

Ten przewodnik Szybki start używa menedżera zależności Gradle. Bibliotekę klienta i informacje dla innych menedżerów zależności można znaleźć w [repozytorium Centralnym Maven](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-textanalytics/).

W pliku *build.gradle.kts* projektu należy dołączyć bibliotekę klienta `implementation` jako instrukcję. 

```kotlin
dependencies {
    compile("org.slf4j:slf4j-simple:1.7.25")
    compile("com.microsoft.azure.cognitiveservices:azure-cognitiveservices-visualsearch:1.0.2-beta")
    compile("com.google.code.gson:gson:2.8.5")
}
```

## <a name="code-examples"></a>Przykłady kodu

Te fragmenty kodu pokazują, jak wykonać następujące zadania za pomocą biblioteki klienta wyszukiwania wizualnego Bing i języka Java:

* [Uwierzytelnij klienta](#authenticate-the-client)
* [Wysyłanie wizualnego żądania wyszukiwania](#send-a-visual-search-request)
* [Drukowanie tokenu wglądu w obraz i wizualnych znaczników wyszukiwania](#print-the-image-insight-token-and-visual-search-tags)

## <a name="authenticate-the-client"></a>Uwierzytelnij klienta

> [!NOTE]
> Ten przewodnik Szybki start zakłada utworzenie [zmiennej środowiskowej](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) dla klucza wyszukiwania wizualnego Bing o nazwie `BING_SEARCH_V7_SUBSCRIPTION_KEY`.


W głównej metodzie należy użyć klucza subskrypcji do wystąpienia obiektu [BingVisualSearchAPI.](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.visualsearch.bingvisualsearchapi?view=azure-java-stable)

```csharp
BingVisualSearchAPI client = BingVisualSearchManager.authenticate(subscriptionKey);
```

## <a name="send-a-visual-search-request"></a>Wysyłanie wizualnego żądania wyszukiwania

W nowej metodzie wyślij tablicę bajtów obrazu `main()` (która została utworzona w metodzie) przy użyciu metody [bingImages().visualSearch().](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.visualsearch.bingimages.visualsearch?view=azure-java-stable#com_microsoft_azure_cognitiveservices_search_visualsearch_BingImages_visualSearch__) 

[!code-java[visualSearch() method](~/cognitive-services-java-sdk-samples/Search/BingVisualSearch/src/main/java/BingVisualSearchSample.java?name=visualSearch)]

## <a name="print-the-image-insight-token-and-visual-search-tags"></a>Drukowanie tokenu wglądu w obraz i wizualnych znaczników wyszukiwania

Sprawdź, czy [obiekt ImageKnowledge](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.visualsearch.models.imageknowledge?view=azure-java-stable) ma wartość null. Jeśli nie, wydrukuj token wglądu w obrazy, liczbę znaczników, liczbę akcji i pierwszy typ akcji.

[!code-java[Print token and tags](~/cognitive-services-java-sdk-samples/Search/BingVisualSearch/src/main/java/BingVisualSearchSample.java?name=printVisualSearchResults)]

## <a name="run-the-application"></a>Uruchamianie aplikacji

Możesz utworzyć aplikację za pomocą:

```console
gradle build
```

Uruchom aplikację z `run` celem:

```console
gradle run
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli chcesz wyczyścić i usunąć subskrypcję usług Cognitive Services, możesz usunąć zasób lub grupę zasobów. Usunięcie grupy zasobów powoduje również usunięcie innych skojarzonych z nią zasobów.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfejs wiersza polecenia platformy Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie jednostronicowej aplikacji internetowej](../../tutorial-bing-visual-search-single-page-app.md)
