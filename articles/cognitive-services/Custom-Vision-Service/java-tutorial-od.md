---
title: 'Szybki start: Tworzenie projektu wykrywania obiektów przy użyciu zestawu Custom Vision SDK dla języka Java'
titlesuffix: Azure Cognitive Services
description: Utwórz projekt, dodaj tagi, przekaż obrazy, wyszkol projekt i wykrywaj obiekty przy użyciu zestawu Java SDK.
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 03/21/2019
ms.author: areddish
ms.openlocfilehash: 7e401fe099a4e18573ccfe2aacda6068c37abfb7
ms.sourcegitcommit: 87bd7bf35c469f84d6ca6599ac3f5ea5545159c9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2019
ms.locfileid: "58352055"
---
# <a name="quickstart-create-an-object-detection-project-with-the-custom-vision-sdk-for-java"></a>Szybki start: Tworzenie projektu wykrywania obiektów przy użyciu zestawu Custom Vision SDK dla języka Java

Ten artykuł zawiera informacje i przykładowy kod, dzięki którym można łatwiej rozpocząć tworzenie modeli wykrywania obiektów za pomocą zestawu Custom Vision SDK i języka Java. Po jego utworzeniu możesz dodać oznaczone regiony, przesłać obrazy, wyszkolić projekt, uzyskać adres URL domyślnego punktu końcowego przewidywania i użyć tego punktu końcowego do programowego przetestowania obrazu. Użyj tego przykładu jako szablonu do utworzenia własnej aplikacji języka Java.

## <a name="prerequisites"></a>Wymagania wstępne

- Wybrane środowisko IDE Java
- Zainstalowany zestaw [JDK 7 lub 8](https://aka.ms/azure-jdks).
- Zainstalowane narzędzie Maven

## <a name="get-the-custom-vision-sdk-and-sample-code"></a>Pobieranie zestawu Custom Vision SDK i przykładowego kodu

Do napisania aplikacji języka Java używającej usługi Custom Vision potrzebne są pakiety Maven usługi Custom Vision. Są one zawarte w przykładowym projekcie, który pobierzesz, ale możesz osobno uzyskać do nich dostęp w tym miejscu.

Pakiet SDK Custom Vision można zainstalować z centralnego repozytorium Maven:
- [Pakiet SDK do szkolenia](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-training)
- [Pakiet SDK do przewidywania](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-prediction)

Sklonuj lub pobierz projekt [Cognitive Services Java SDK Samples (Przykłady dotyczące zestawu Cognitive Services Java SDK)](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master). Przejdź do folderu **Vision/CustomVision/**.

Ten projekt języka Java tworzy nowy projekt wykrywania obiektów za pomocą usługi Custom Vision, mający nazwę __Sample Java OD Project__ (Przykładowy projekt wykrywania obiektów w języku Java), do którego można uzyskać dostęp za pośrednictwem [witryny internetowej Custom Vision](https://customvision.ai/). Następnie zostaną przesłane obrazy do szkolenia i testowania klasyfikatora. W tym projekcie klasyfikator jest używany do ustalania, czy drzewo jest __choiną__ (Hemlock), czy __wiśnią japońską__ (Japanese Cherry).

[!INCLUDE [get-keys](includes/get-keys.md)]

Program jest skonfigurowany do przechowywania danych kluczy jako zmiennych środowiskowych. Ustaw te zmienne, przechodząc do folderu **Vision/CustomVision** w programie PowerShell. Następnie wprowadź poniższe polecenia:

```PowerShell
$env:AZURE_CUSTOMVISION_TRAINING_API_KEY ="<your training api key>"
$env:AZURE_CUSTOMVISION_PREDICTION_API_KEY ="<your prediction api key>"
```

## <a name="understand-the-code"></a>Zrozumienie kodu

Załaduj projekt `Vision/CustomVision` w środowisku IDE Java i otwórz plik _CustomVisionSamples.java_. Znajdź metodę **runSample** i oznacz wywołanie metody **ImageClassification_Sample** jako komentarz &mdash; uruchamia ono scenariusz klasyfikacji obrazów, którego nie omówiono w tym przewodniku. Metoda **ObjectDetection_Sample** implementuje podstawowe funkcje tego przewodnika Szybki start. Przejdź do jej definicji i przejrzyj kod. 

### <a name="create-a-new-custom-vision-service-project"></a>Tworzenie nowego projektu Custom Vision Service

Przejdź do bloku kodu, który tworzy klienta szkolenia i projekt wykrywania obiektów. Utworzony projekt będzie widoczny w odwiedzonej wcześniej [witrynie internetowej Custom Vision](https://customvision.ai/). 

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?range=181-206)]

### <a name="add-tags-to-your-project"></a>Dodanie tagów do projektu

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?range=208-218)]

### <a name="upload-and-tag-images"></a>Przekazywanie i tagowanie obrazów

Oznaczając tagami obrazy w projektach wykrywania obiektów, należy określić region każdego otagowanego obiektu za pomocą znormalizowanych współrzędnych. Przejdź do elementu Map o nazwie `regionMap`. Ten kod kojarzy poszczególne przykładowe obrazy z ich otagowanymi regionami.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?range=130-179)]

Następnie przejdź do bloku kodu, który dodaje obrazy do projektu. Obrazy są odczytywane z folderu projektu **src/main/resources** oraz są przekazywane do usługi z odpowiednimi tagami i współrzędnymi regionów.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?range=220-231)]

W poprzednim fragmencie kodu były używane dwie funkcje pomocnicze pobierające obrazy jako strumienie zasobów i przekazujące je do usługi.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?range=277-314)]

### <a name="train-the-project"></a>Szkolenie projektu

Ten kod tworzy pierwszą iterację w projekcie i oznacza ją jako domyślną. Iteracja domyślna odzwierciedla wersję modelu, która będzie odpowiadać na żądania przewidywania. Należy ją zaktualizować przy każdym ponownym szkoleniu modelu.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?range=233-242)]

### <a name="use-the-prediction-endpoint"></a>Używanie punktu końcowego przewidywania

Punkt końcowy przewidywania, reprezentowany tutaj przez obiekt `predictor`, jest odwołaniem umożliwiającym przesyłanie obrazu do bieżącego modelu i uzyskiwanie przewidywania klasyfikacji. W tym przykładzie element `predictor` jest zdefiniowany w innym miejscu przy użyciu zmiennej środowiskowej klucza przewidywania.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?range=244-270)]

## <a name="run-the-application"></a>Uruchamianie aplikacji

Aby skompilować i uruchomić rozwiązanie przy użyciu narzędzia Maven, uruchom następujące polecenie w katalogu projektu w programie PowerShell:

```PowerShell
mvn compile exec:java
```

Wyświetl dane wyjściowe konsoli, aby uzyskać wyniki rejestrowania i przewidywania. Możesz następnie sprawdzić, czy obraz testowy został odpowiednio otagowany i czy region wykrywania jest poprawny.

[!INCLUDE [clean-od-project](includes/clean-od-project.md)]

## <a name="next-steps"></a>Kolejne kroki

Teraz wiesz, jak można zrealizować w kodzie poszczególne kroki procesu wykrywania obiektów. W tym przykładzie jest wykonywana jedna iteracja szkolenia, ale często trzeba szkolić i testować model wiele razy, aby zwiększyć jego dokładność. Następny przewodnik dotyczy klasyfikacji obrazów. Jej zasady są podobne do wykrywania obiektów.

> [!div class="nextstepaction"]
> [Testowanie i ponowne szkolenie modelu](test-your-model.md)
