---
title: 'Szybki start: przetwarzanie obrazów Biblioteka kliencka dla języka Java'
titleSuffix: Azure Cognitive Services
description: Wprowadzenie do biblioteki klienta przetwarzanie obrazów dla środowiska Java.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: ''
ms.topic: quickstart
ms.date: 07/25/2019
ms.author: pafarley
ms.openlocfilehash: daea1415c42960970d097753bc657392d4e1a1f4
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/29/2019
ms.locfileid: "70137274"
---
# <a name="quickstart-computer-vision-client-library-for-java"></a>Szybki start: przetwarzanie obrazów Biblioteka kliencka dla języka Java

Wprowadzenie do biblioteki klienta przetwarzanie obrazów dla środowiska Java. Wykonaj następujące kroki, aby zainstalować pakiet i wypróbować przykładowy kod dla podstawowych zadań. Przetwarzanie obrazów zapewnia dostęp do zaawansowanych algorytmów przetwarzania obrazów i zwracania informacji.

Użyj biblioteki klienta przetwarzanie obrazów dla języka Java, aby:

* Analizuj obraz pod kątem tagów, opisu tekstu, twarzy, treści dla dorosłych itd.

[](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/computervision?view=azure-java-stable) | [Przykłady](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0) artefaktów dokumentacji referencyjnej[(Maven)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision/) | 

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/)
* Bieżąca wersja [zestawu Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Narzędzie kompilacji Gradle](https://gradle.org/install/)lub inny Menedżer zależności.

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-computer-vision-azure-resource"></a>Tworzenie zasobu przetwarzanie obrazów platformy Azure

Usługa Azure Cognitive Services jest reprezentowana przez zasoby platformy Azure, które subskrybujesz. Utwórz zasób dla przetwarzanie obrazów przy użyciu [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) lub [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) na komputerze lokalnym. Możesz również wykonać następujące czynności:

* Uzyskaj [klucz wersji próbnej](https://azure.microsoft.com/try/cognitive-services/#decision) ważny przez siedem dni bezpłatnie. Po utworzeniu konta będzie ono dostępne w [witrynie sieci Web systemu Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Wyświetl zasób na [Azure Portal](https://portal.azure.com/).

Następnie [Utwórz zmienne środowiskowe](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) dla ciągu punktu końcowego klucza i usługi, odpowiednio `COMPUTER_VISION_SUBSCRIPTION_KEY` nazwane `COMPUTER_VISION_ENDPOINT`i.

### <a name="create-a-new-gradle-project"></a>Utwórz nowy projekt Gradle

W oknie konsoli (na przykład cmd, PowerShell lub bash) Utwórz nowy katalog dla aplikacji i przejdź do niego. 

```console
mkdir myapp && cd myapp
```

`gradle init` Uruchom polecenie z katalogu roboczego. To polecenie spowoduje utworzenie podstawowych plików kompilacji dla Gradle, w tym *Build. Gradle. KTS*, który jest używany w środowisku uruchomieniowym do tworzenia i konfigurowania aplikacji.

```console
gradle init --type basic
```

Po wyświetleniu monitu wybierz pozycję **Język DSL**, a następnie **Kotlin**.

Znajdź element *Build. Gradle. KTS* i otwórz go za pomocą PREFEROWANEGO środowiska IDE lub edytora tekstu. Następnie skopiuj w poniższej konfiguracji kompilacji. Ta konfiguracja definiuje projekt jako aplikację Java, której punkt wejścia to Klasa **ComputerVisionQuickstarts**. Importuje bibliotekę przetwarzanie obrazów.

```kotlin
plugins {
    java
    application
}
application { 
    mainClassName = "ComputerVisionQuickstarts"
}
repositories {
    mavenCentral()
}
```

W katalogu roboczym Uruchom następujące polecenie, aby utworzyć folder źródłowy projektu:

```console
mkdir -p src/main/java
```

Przejdź do nowego folderu i Utwórz plik o nazwie *ComputerVisionQuickstarts. Java*. Otwórz go w preferowanym edytorze lub środowisku IDE i Dodaj następujące `import` instrukcje:

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/ComputerVisionQuickstart.java?name=snippet_imports)]

Następnie Dodaj definicję klasy dla **ComputerVisionQuickstarts**.

### <a name="install-the-client-library"></a>Zainstaluj bibliotekę kliencką

Ten przewodnik Szybki Start używa Menedżera zależności Gradle. Bibliotekę i informacje o kliencie można znaleźć dla innych menedżerów zależności w [repozytorium centralnym Maven](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-textanalytics/).

W pliku *Build. Gradle. KTS* projektu jako zależność należy uwzględnić bibliotekę klienta przetwarzanie obrazów.

```kotlin
dependencies {
    compile(group = "com.microsoft.azure.cognitiveservices", name = "azure-cognitiveservices-computervision", version = "1.0.2-beta")
}
```

## <a name="object-model"></a>Model obiektów

Poniższe klasy i interfejsy obsługują niektóre główne funkcje zestawu SDK języka Java przetwarzanie obrazów.

|Name|Opis|
|---|---|
| [ComputerVisionClient](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-java-stable) | Ta klasa jest wymagana dla wszystkich funkcji przetwarzanie obrazów. Tworzysz wystąpienie z informacjami o subskrypcji i używasz ich do tworzenia wystąpień innych klas.|
|[ComputerVision](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervision?view=azure-java-stable)| Ta klasa pochodzi z obiektu klienta i bezpośrednio obsługuje wszystkie operacje obrazu, takie jak analiza obrazu, wykrywanie tekstu i generowanie miniatury.
|[VisualFeatureTypes](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-java-stable)| To Wyliczenie definiuje różne typy analizy obrazów, które można wykonać przy użyciu standardowej operacji analizy. Należy określić zestaw wartości VisualFeatureTypes w zależności od potrzeb. |

## <a name="code-examples"></a>Przykłady kodu

Te fragmenty kodu przedstawiają sposób wykonywania następujących zadań za pomocą biblioteki klienta przetwarzanie obrazów dla języka Java:

* [Uwierzytelnianie klienta](#authenticate-the-client)
* [Analizowanie obrazu](#analyze-an-image)

## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

> [!NOTE]
> W tym przewodniku szybki start przyjęto założenie, że dla klucza przetwarzanie obrazów `COMPUTER_VISION_SUBSCRIPTION_KEY`została [utworzona zmienna środowiskowa](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) o nazwie.

Poniższy kod dodaje `main` metodę do klasy i tworzy zmienne dla punktu końcowego i klucza usługi Azure Resource. Musisz wprowadzić własny ciąg punktu końcowego, który można znaleźć, sprawdzając sekcję **omówienie** Azure Portal. 

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/ComputerVisionQuickstart.java?name=snippet_mainvars)]

Następnie Dodaj następujący kod, aby utworzyć obiekt [ComputerVisionClient](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-java-stable) i przekazywać go do innych metod, które zostaną później zdefiniowane.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/ComputerVisionQuickstart.java?name=snippet_client)]

> [!NOTE]
> Jeśli zmienna środowiskowa została utworzona po uruchomieniu aplikacji, należy zamknąć i ponownie otworzyć Edytor, środowisko IDE lub powłokę, na których jest uruchomiona, aby uzyskać dostęp do zmiennej.

## <a name="analyze-an-image"></a>Analizowanie obrazu

Poniższy kod definiuje metodę, `AnalyzeLocalImage`która używa obiektu klienta do analizowania obrazu lokalnego i drukowania wyników. Metoda zwraca opis tekstowy, kategoryzację, listę tagów, wykrytych twarzy, flag zawartości dla dorosłych, kolory główne i typ obrazu.

### <a name="set-up-test-image"></a>Konfigurowanie obrazu testu

Najpierw Utwórz **zasoby/** folder w folderze **src/Main/** folder projektu, a następnie Dodaj obraz, który chcesz przeanalizować. Następnie Dodaj następującą definicję metody do klasy **ComputerVisionQuickstarts** . W razie potrzeby zmień wartość `pathToLocalImage` w celu dopasowania do pliku obrazu. 

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/ComputerVisionQuickstart.java?name=snippet_analyzelocal_refs)]

### <a name="specify-visual-features"></a>Określ funkcje wizualne

Następnie określ, które funkcje wizualne mają zostać wyodrębnione w analizie. Aby uzyskać pełną listę, zobacz Wyliczenie [VisualFeatureTypes](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-java-stable) .

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/ComputerVisionQuickstart.java?name=snippet_analyzelocal_features)]

### <a name="analyze"></a>Analiza
Ta metoda drukuje szczegółowe wyniki do konsoli dla każdego zakresu analizy obrazu. Zalecamy obchwycenie tego wywołania metody w bloku try/catch

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/ComputerVisionQuickstart.java?name=snippet_analyzelocal_analyze)]

### <a name="display-results"></a>Wyświetlanie wyników

Powyższe wywołanie metody zwróci obiekt ImageAnalysis, który zawiera wszystkie wyodrębnione informacje. Aby wydrukować szczegóły danej funkcji wizualizacji, można użyć bloku kodu, takiego jak poniższy:

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/ComputerVisionQuickstart.java?name=snippet_analyzelocal_display)]

Zapoznaj się z przykładowym kodem w witrynie [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/ComputerVisionQuickstart.java) , aby uzyskać pełny zestaw opcji wyświetlania.

## <a name="run-the-application"></a>Uruchamianie aplikacji

Aplikację można skompilować przy użyciu:

```console
gradle build
```

Uruchom aplikację za pomocą `gradle run` polecenia:

```console
gradle run
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli chcesz wyczyścić i usunąć subskrypcję Cognitive Services, możesz usunąć zasób lub grupę zasobów. Usunięcie grupy zasobów spowoduje również usunięcie wszystkich skojarzonych z nią zasobów.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfejs wiersza polecenia platformy Azure](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono, jak wykonywać podstawowe zadania przy użyciu biblioteki języka Java przetwarzanie obrazów. Następnie zapoznaj się z dokumentacją referencyjną, aby dowiedzieć się więcej o bibliotece.

> [!div class="nextstepaction"]
>[Odwołanie przetwarzanie obrazów (Java)](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/computervision?view=azure-java-stable)

* [Co to jest przetwarzanie obrazów?](../Home.md)
* Kod źródłowy tego przykładu można znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/ComputerVisionQuickstart.java).