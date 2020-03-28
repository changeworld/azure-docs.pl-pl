---
title: 'Szybki start: biblioteka klienta przetwarzania obrazów w urozumienie komputera dla języka Java'
description: W tym przewodniku Szybki start rozpocznij pracę z biblioteką klienta usługi Computer Vision dla języka Java.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/19/2019
ms.author: pafarley
ms.openlocfilehash: dbe986145a223f1958f1945abfa189de90952f4a
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80272793"
---
<a name="HOLTop"></a>

[Przykłady artefaktu](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/computervision?view=azure-java-stable) | [(Maven)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision/) | dokumentacji[referencyjnej](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [utwórz bezpłatną subskrypcję platformy Azure](https://azure.microsoft.com/free/)
* Aktualna wersja [zestawu Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Narzędzie kompilacji Gradle](https://gradle.org/install/)lub inny menedżer zależności.

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-computer-vision-azure-resource"></a>Tworzenie zasobu platformy Computer Vision Azure

Usługi Azure Cognitive Services są reprezentowane przez zasoby platformy Azure, które subskrybujesz. Utwórz zasób dla przetwarzania obrazów komputerowych przy użyciu [witryny Azure portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) lub [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) na komputerze lokalnym. Możesz również wykonać następujące czynności:

* Pobierz [klucz wersji próbnej](https://azure.microsoft.com/try/cognitive-services/#decision) ważny przez siedem dni za darmo. Po zarejestrowaniu się będzie on dostępny w [witrynie sieci Web platformy Azure.](https://azure.microsoft.com/try/cognitive-services/my-apis/)  
* Wyświetl swój zasób w [witrynie Azure portal](https://portal.azure.com/).

Następnie [należy utworzyć zmienne środowiskowe](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) dla ciągu `COMPUTER_VISION_SUBSCRIPTION_KEY` punktu `COMPUTER_VISION_ENDPOINT`końcowego klucza i usługi, odpowiednio o nazwie i ,.

### <a name="create-a-new-gradle-project"></a>Tworzenie nowego projektu Gradle

W oknie konsoli (takim jak cmd, PowerShell lub Bash) utwórz nowy katalog aplikacji i przejdź do niego. 

```console
mkdir myapp && cd myapp
```

Uruchom `gradle init` polecenie z katalogu roboczego. To polecenie spowoduje utworzenie podstawowych plików kompilacji dla gradle, w tym *build.gradle.kts*, który jest używany w czasie wykonywania do tworzenia i konfigurowania aplikacji.

```console
gradle init --type basic
```

Po wyświetleniu monitu wybierz pozycję **Język DSL**, a następnie **Kotlin**.

Znajdź *build.gradle.kts* i otwórz go za pomocą preferowanego edytora IDE lub tekstu. Następnie skopiuj w następującej konfiguracji kompilacji. Ta konfiguracja definiuje projekt jako aplikację Java, której punktem wejścia jest klasa **ComputerVisionQuickstarts**. Importuje bibliotekę wizji komputerowej.

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

W katalogu roboczym uruchom następujące polecenie, aby utworzyć folder źródłowy projektu:

```console
mkdir -p src/main/java
```

Przejdź do nowego folderu i utwórz plik o nazwie *ComputerVisionQuickstarts.java*. Otwórz go w preferowanym edytorze `import` lub IDE i dodaj następujące instrukcje:

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_imports)]

Następnie dodaj definicję klasy dla **ComputerVisionQuickstarts**.

### <a name="install-the-client-library"></a>Instalowanie biblioteki klienta

Ten przewodnik Szybki start używa menedżera zależności Gradle. Bibliotekę klienta i informacje dla innych menedżerów zależności można znaleźć w [repozytorium Centralnym Maven](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-textanalytics/).

W pliku *build.gradle.kts* projektu uwzględnij bibliotekę klienta przetwarzania jako zależność.

```kotlin
dependencies {
    compile(group = "com.microsoft.azure.cognitiveservices", name = "azure-cognitiveservices-computervision", version = "1.0.2-beta")
}
```

## <a name="object-model"></a>Model obiektu

Następujące klasy i interfejsy obsługują niektóre z głównych funkcji SDK Java usługi Przetwarzania Obrazów.

|Nazwa|Opis|
|---|---|
| [ComputerVisionClient (System obsługi komputerów)](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-java-stable) | Ta klasa jest potrzebna dla wszystkich funkcji przetwarzania. Tworzenie wystąpienia z informacjami o subskrypcji i używać go do tworzenia wystąpień innych klas.|
|[Technologia ComputerVision](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervision?view=azure-java-stable)| Ta klasa pochodzi z obiektu klienta i bezpośrednio obsługuje wszystkie operacje obrazu, takie jak analiza obrazu, wykrywanie tekstu i generowanie miniatur.|
|[VisualFeatureTytyty](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-java-stable)| Wyliczenie to definiuje różne typy analizy obrazu, które można wykonać w standardowej operacji analizy. Należy określić zestaw visualfeatureTypes wartości w zależności od potrzeb. |

## <a name="code-examples"></a>Przykłady kodu

Te fragmenty kodu pokazują, jak wykonać następujące zadania za pomocą biblioteki klienta usługi Computer Vision dla języka Java:

* [Uwierzytelnij klienta](#authenticate-the-client)
* [Analizowanie obrazu](#analyze-an-image)
* [Czytanie drukowanego i odręcznego tekstu](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>Uwierzytelnij klienta

> [!NOTE]
> Ten przewodnik Szybki start zakłada, że [utworzono zmienną środowiskową](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) dla klucza przetwarzania o nazwie `COMPUTER_VISION_SUBSCRIPTION_KEY`.

Poniższy kod `main` dodaje metodę do klasy i tworzy zmienne dla punktu końcowego i klucza platformy Azure zasobu. Musisz wprowadzić własny ciąg punktu końcowego, który można znaleźć, sprawdzając sekcję **Przegląd** witryny Azure portal. 

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_mainvars)]

Następnie dodaj następujący kod, aby utworzyć [obiekt ComputerVisionClient](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-java-stable) i przekazuje go do innych metod, które zdefiniujesz później.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_client)]

> [!NOTE]
> Jeśli po uruchomieniu aplikacji utworzono zmienną środowiskową, należy zamknąć i ponownie otworzyć edytor, IDE lub powłokę uruchamianą ją, aby uzyskać dostęp do zmiennej.

## <a name="analyze-an-image"></a>Analizowanie obrazu

Poniższy kod definiuje metodę `AnalyzeLocalImage`, która używa obiektu klienta do analizowania obrazu lokalnego i drukowania wyników. Metoda zwraca opis tekstowy, kategoryzację, listę znaczników, wykryte twarze, flagi zawartości dla dorosłych, główne kolory i typ obrazu.

### <a name="set-up-test-image"></a>Konfigurowanie obrazu testowego

Najpierw utwórz **zasoby/** folder w folderze **src/main/** projektu i dodaj obraz, który chcesz przeanalizować. Następnie dodaj następującą definicję metody do klasy **ComputerVisionQuickstarts.** W razie potrzeby zmień `pathToLocalImage` wartość pliku obrazu. 

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_refs)]

> [!NOTE]
> Można również analizować obraz zdalny przy użyciu jego adresu URL. Zobacz przykładowy kod w [usłudze GitHub, aby](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java) uzyskać scenariusze dotyczące obrazów zdalnych.

### <a name="specify-visual-features"></a>Określanie obiektów wizualnych

Następnie określ, które funkcje wizualne chcesz wyodrębnić w analizie. Zobacz [VisualFeatureTypes wyliczenia](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-java-stable) dla pełnej listy.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_features)]

### <a name="analyze"></a>Analiza
Ta metoda drukuje szczegółowe wyniki do konsoli dla każdego zakresu analizy obrazu. Zalecamy otoczyć to wywołanie metody w Try/Catch bloku. Metoda **analyzeImageInStream** zwraca obiekt **ImageAnalysis,** który zawiera wszystkie wyodrębnione informacje.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_analyze)]

W poniższych sekcjach pokazano, jak szczegółowo przeanalizować te informacje.

### <a name="get-image-description"></a>Pobierz opis obrazu

Poniższy kod pobiera listę wygenerowanych podpisów dla obrazu. Aby uzyskać więcej informacji, zobacz [Opis obrazów](../../concept-describing-images.md).

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_captions)]

### <a name="get-image-category"></a>Pobierz kategorię obrazu

Poniższy kod pobiera wykrytą kategorię obrazu. Aby uzyskać więcej informacji, zobacz [Kategoryzowanie obrazów](../../concept-categorizing-images.md).

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_category)]

### <a name="get-image-tags"></a>Pobierz znaczniki obrazów

Poniższy kod pobiera zestaw wykrytych tagów na obrazie. Aby uzyskać więcej informacji, zobacz [Tagi zawartości](../../concept-tagging-images.md).

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_tags)]

### <a name="detect-faces"></a>Wykrywanie twarzy

Poniższy kod zwraca wykryte twarze na obrazie z ich współrzędne prostokąta i wybiera atrybuty twarzy. Aby uzyskać więcej informacji, zobacz [Wykrywanie twarzy](../../concept-detecting-faces.md).

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_faces)]

### <a name="detect-adult-racy-or-gory-content"></a>Wykrywanie treści dla dorosłych, rasistowskich lub gory

Poniższy kod drukuje wykrytą obecność zawartości dla dorosłych na obrazie. Aby uzyskać więcej informacji, zobacz [Adult, racy, gory treści](../../concept-detecting-adult-content.md).

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_adult)]

### <a name="get-image-color-scheme"></a>Pobierz schemat kolorów obrazu

Poniższy kod drukuje wykryte atrybuty kolorów obrazu, takie jak kolory dominujące i kolor akcentu. Aby uzyskać więcej informacji, zobacz [Schematy kolorów](../../concept-detecting-color-schemes.md).

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_colors)]

### <a name="get-domain-specific-content"></a>Pobierz zawartość specyficzną dla domeny

Usługa Computer Vision może używać specjalistycznego modelu do dalszej analizy obrazów. Aby uzyskać więcej informacji, zobacz [Zawartość specyficzna dla domeny](../../concept-detecting-domain-content.md). 

Poniższy kod analizuje dane dotyczące wykrytych gwiazd na obrazie.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_celebrities)]

Poniższy kod analizuje dane dotyczące wykrytych punktów orientacyjnych na obrazie.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_landmarks)]

### <a name="get-the-image-type"></a>Pobierz typ obrazu

Poniższy kod drukuje informacje o&mdash;typie obrazu, niezależnie od tego, czy jest to obiekt clipart, czy rysunek liniowy.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_imagetype)]

## <a name="read-printed-and-handwritten-text"></a>Czytanie drukowanego i odręcznego tekstu

Funkcja Wizja komputerowa może odczytywać widoczny tekst na obrazie i konwertować go na strumień znaków.

> [!NOTE]
> Tekst można również odczytać na obrazie zdalnym przy użyciu jego adresu URL. Zobacz przykładowy kod w [usłudze GitHub, aby](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java) uzyskać scenariusze dotyczące obrazów zdalnych.

### <a name="call-the-recognize-api"></a>Wywołanie interfejsu API rozpoznawania

Najpierw użyj następującego kodu, aby wywołać **metodę recognizePrintedTextInStream** dla danego obrazu. Po dodaniu tego kodu do projektu, należy `localTextImagePath` zastąpić wartość ścieżką do obrazu lokalnego. 

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_call)]

### <a name="print-recognize-results"></a>Wyniki rozpoznawania wydruku

Poniższy blok kodu przetwarza zwrócony tekst i analizuje go, aby wydrukować pierwsze słowo w każdym wierszu. Można użyć tego kodu, aby szybko zrozumieć strukturę **wystąpienia OcrResult.**

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_print)]

Na koniec zamknij blok try/catch i definicję metody.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_catch)]

## <a name="run-the-application"></a>Uruchamianie aplikacji

Możesz utworzyć aplikację za pomocą:

```console
gradle build
```

Uruchom aplikację za `gradle run` pomocą polecenia:

```console
gradle run
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli chcesz wyczyścić i usunąć subskrypcję usług Cognitive Services, możesz usunąć zasób lub grupę zasobów. Usunięcie grupy zasobów powoduje również usunięcie innych skojarzonych z nią zasobów.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfejs wiersza polecenia platformy Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start opisano, jak używać biblioteki Java do wykonywania zadań bazowych za pomocą biblioteki Java usługi Computer Vision. Następnie zapoznaj się z dokumentacją referencyjną, aby dowiedzieć się więcej o bibliotece.

> [!div class="nextstepaction"]
>[Numer referencyjny computer vision (Java)](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/computervision?view=azure-java-stable)

* [Czym jest przetwarzanie obrazów?](../../Home.md)
* Kod źródłowy tego przykładu można znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java).