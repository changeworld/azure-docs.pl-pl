---
title: Szybki start biblioteki klienta Java wyszukiwania niestandardowego usługi Bing
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/27/2020
ms.author: aahi
ms.openlocfilehash: 32e02d3a7c1af6d15e7f381807d80f19b94da38f
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80587079"
---
Wprowadzenie do biblioteki klienta wyszukiwania niestandardowego usługi Bing dla języka Java. Wykonaj następujące kroki, aby zainstalować pakiet i wypróbować przykładowy kod dla podstawowych zadań. Interfejs API wyszukiwania niestandardowego Bing umożliwia tworzenie dostosowanych do potrzeb wyszukiwarek bez reklam dla tematów, na których Ci zależy. Kod źródłowy tego przykładu można znaleźć w [usłudze GitHub](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingCustomSearch)

Użyj biblioteki klienta wyszukiwania niestandardowego Bing dla oprogramowania Java, aby:

* Znajdź wyniki wyszukiwania w internecie z wystąpienia wyszukiwania niestandardowego Bing. 

[Przykłady](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/bingcustomsearch?view=azure-java-stable) | [kodu źródłowego biblioteki](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Search.BingCustomSearch) | referencyjnej Artifact[(Maven)](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customsearch/) | [Samples](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [utwórz bezpłatną subskrypcję.](https://azure.microsoft.com/free/)
* Aktualna wersja [zestawu Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html).
* [Narzędzie kompilacji Gradle](https://gradle.org/install/)lub inny menedżer zależności.
* Wystąpienie wyszukiwania niestandardowego Bing. Zobacz [Szybki start: tworzenie pierwszego wystąpienia wyszukiwania niestandardowego Bing, aby](../../quick-start.md) uzyskać więcej informacji.

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](~/includes/cognitive-services-bing-custom-search-signup-requirements.md)]

Po dostaniu klucza z subskrypcji próbnej lub zasobu `AZURE_BING_CUSTOM_SEARCH_API_KEY` [utwórz zmienną środowiskową](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) dla klucza o nazwie .

### <a name="create-a-new-gradle-project"></a>Tworzenie nowego projektu Gradle

> [!TIP]
> Jeśli nie używasz Gradle, możesz znaleźć szczegóły biblioteki klienta dla innych menedżerów zależności w [repozytorium Centralnym Maven](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-textanalytics/).

W oknie konsoli (takim jak cmd, PowerShell lub Bash) utwórz nowy katalog aplikacji i przejdź do niego. 

```console
mkdir myapp && cd myapp
```

Uruchom `gradle init` polecenie z katalogu roboczego. To polecenie tworzy niezbędne pliki kompilacji dla Gradle, w tym plik *build.gradle.kts,* który jest używany w czasie wykonywania do konfigurowania aplikacji.

```console
gradle init --type basic
```

Po wyświetleniu monitu wybierz pozycję **Język DSL**, a następnie **Kotlin**.

## <a name="install-the-client-library"></a>Instalowanie biblioteki klienta 

Znajdź *build.gradle.kts* i otwórz go za pomocą preferowanego edytora IDE lub tekstu. Następnie skopiuj w tej konfiguracji kompilacji. Pamiętaj, aby dołączyć bibliotekę klienta w: `dependencies`

```kotlin
plugins {
    java
    application
}
application {
    mainClassName = "main.java.BingCustomSearchSample"
}
repositories {
    mavenCentral()
}
dependencies {
    compile("org.slf4j:slf4j-simple:1.7.25")
    compile("com.microsoft.azure.cognitiveservices:azure-cognitiveservices-customsearch:1.0.2")
}
```

Utwórz folder dla przykładowej aplikacji. W katalogu roboczym uruchom następujące polecenie:

```console
mkdir src/main/java
```

Przejdź do nowego folderu i utwórz plik o nazwie *BingCustomSearchSample.java*. Otwórz go i `import` dodaj następujące instrukcje:


[!code-java[import statements](~/cognitive-services-java-sdk-samples/Search/BingCustomSearch/src/main/java/BingCustomSearchSample.java?name=imports)]

Tworzenie klasy o nazwie`BingCustomSearchSample`

```java
public class BingCustomSearchSample {
}
```

W klasie utwórz `main` metodę i zmienną dla klucza zasobu. Jeśli po uruchomieniu aplikacji utworzono zmienną środowiskową, zamknij i ponownie otwórz edytor, IDE lub powłokę uruchamiając ją, aby uzyskać dostęp do zmiennej. Metody zostaną zdefiniowane później.

[!code-java[main method](~/cognitive-services-java-sdk-samples/Search/BingCustomSearch/src/main/java/BingCustomSearchSample.java?name=main)]

## <a name="object-model"></a>Model obiektu

Klient wyszukiwania niestandardowego Bing jest obiektem [BingCustomSearchAPI](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchapi?view=azure-java-stable) utworzonym na podstawie metody [uwierzytelniania()](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchmanager.authenticate) obiektu [BingCustomSearchManager.](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchmanager?view=azure-java-stable) Żądanie wyszukiwania można wysłać przy użyciu metody [BingCustomInstances.search().](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustominstances.search?view=azure-java-stable#com_microsoft_azure_cognitiveservices_search_customsearch_BingCustomInstances_search__)

Odpowiedź interfejsu API jest [obiektem SearchResponse](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.models.searchresponse?view=azure-java-stable) zawierającym informacje o zapytaniu wyszukiwania i wynikach wyszukiwania.

## <a name="code-examples"></a>Przykłady kodu

Te fragmenty kodu pokazują, jak wykonać następujące zadania za pomocą biblioteki klienta wyszukiwania niestandardowego Bing dla języka Java:

* [Uwierzytelnij klienta](#authenticate-the-client)
* [Uzyskaj wyniki wyszukiwania z niestandardowego wystąpienia wyszukiwania](#get-search-results-from-your-custom-search-instance)

## <a name="authenticate-the-client"></a>Uwierzytelnij klienta

Główna metoda powinna zawierać obiekt [BingCustomSearchManager,](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchapi?view=azure-java-stable) który przyjmuje `authenticate()`klucz i wywołuje jego .

```java
BingCustomSearchAPI client = BingCustomSearchManager.authenticate(subscriptionKey);
```

## <a name="get-search-results-from-your-custom-search-instance"></a>Uzyskaj wyniki wyszukiwania z niestandardowego wystąpienia wyszukiwania

Użyj funkcji [BingCustomInstances.search()](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustominstances.search?view=azure-java-stable#com_microsoft_azure_cognitiveservices_search_customsearch_BingCustomInstances_search__) klienta, aby wysłać zapytanie wyszukiwania do wystąpienia niestandardowego. Ustaw `withCustomConfig` niestandardowy identyfikator konfiguracji lub domyślnie na `1`. Po otrzymaniu odpowiedzi z interfejsu API sprawdź, czy znaleziono wyniki wyszukiwania. Jeśli tak, uzyskaj pierwszy wynik wyszukiwania, `webPages().value().get()` wywołując funkcję odpowiedzi i wydrukuj nazwę i adres URL wyniku. 

[!code-java[call the custom search API](~/cognitive-services-java-sdk-samples/Search/BingCustomSearch/src/main/java/BingCustomSearchSample.java?name=runSample)]

## <a name="run-the-application"></a>Uruchamianie aplikacji

Tworzenie aplikacji za pomocą następującego polecenia z głównego katalogu projektu:

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
> [Tworzenie aplikacji internetowej z funkcją wyszukiwania niestandardowego](../../tutorials/custom-search-web-page.md)
