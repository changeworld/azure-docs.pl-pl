---
title: 'Szybki start: Tworzenie projektu klasyfikacji obrazów przy użyciu zestawu Custom Vision SDK dla języka Java'
titlesuffix: Azure Cognitive Services
description: Utwórz projekt, dodaj tagi, przekaż obrazy, naucz projekt i wykonaj przewidywanie przy użyciu zestawu Java SDK.
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 03/21/2019
ms.author: areddish
ms.openlocfilehash: 01052e218d2c7017ca6f29126f7232aeee16705a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60608146"
---
# <a name="quickstart-create-an-image-classification-project-with-the-custom-vision-sdk-for-java"></a>Szybki start: Tworzenie projektu klasyfikacji obrazów przy użyciu zestawu Custom Vision SDK dla języka Java

Ten artykuł zawiera informacje i przykładowy kod, dzięki którym można łatwiej rozpocząć tworzenie modelu klasyfikacji obrazów za pomocą zestawu Custom Vision Java SDK. Po jej utworzeniu możesz dodać tagi, przesłać obrazy, wyszkolić projekt, uzyskać adres URL punktu końcowego domyślnego przewidywania projektu i użyć punktu końcowego do programowego przetestowania obrazu. Użyj tego przykładu jako szablonu do utworzenia własnej aplikacji języka Java. Jeśli chcesz przejść przez proces tworzenia i używania modelu klasyfikacji _bez_ kodu, zobacz zamiast tego [wskazówki dotyczące przeglądarki](getting-started-build-a-classifier.md).

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

Ten projekt Java za pomocą usługi Custom Vision tworzy nowy projekt klasyfikacji obrazów o nazwie __Sample Java Project__, do którego można uzyskać dostęp za pośrednictwem [witryny internetowej Custom Vision](https://customvision.ai/). Następnie zostaną przesłane obrazy do szkolenia i testowania klasyfikatora. W tym projekcie klasyfikator jest używany do ustalania, czy drzewo jest __choiną__ (Hemlock), czy __wiśnią japońską__ (Japanese Cherry).

[!INCLUDE [get-keys](includes/get-keys.md)]

Program jest skonfigurowany do przechowywania danych kluczy jako zmiennych środowiskowych. Ustaw te zmienne, przechodząc do folderu **Vision/CustomVision** w programie PowerShell. Następnie wprowadź poniższe polecenia:

```powershell
$env:AZURE_CUSTOMVISION_TRAINING_API_KEY ="<your training api key>"
$env:AZURE_CUSTOMVISION_PREDICTION_API_KEY ="<your prediction api key>"
```

## <a name="understand-the-code"></a>Zrozumienie kodu

Załaduj projekt `Vision/CustomVision` w środowisku IDE Java i otwórz plik _CustomVisionSamples.java_. Znajdź metodę **runSample** i oznacz wywołanie metody **ObjectDetection_Sample** jako komentarz &mdash; uruchamia ona scenariusz wykrywania obiektów, którego nie omówiono w tym przewodniku. Metoda **ImageClassification_Sample** implementuje podstawowe funkcje tego przykładu. Przejdź do jej definicji i przejrzyj kod.

### <a name="create-a-custom-vision-service-project"></a>Utworzenie projektu Custom Vision Service

Ten pierwszy fragment kodu tworzy projekt klasyfikacji obrazów. Utworzony projekt będzie widoczny w odwiedzonej wcześniej [witrynie internetowej Custom Vision](https://customvision.ai/). 

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?range=57-63)]

### <a name="create-tags-in-the-project"></a>Tworzenie tagów w projekcie

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?range=65-74)]

### <a name="upload-and-tag-images"></a>Przekazywanie i tagowanie obrazów

Przykładowe obrazy znajdują się w folderze **src/main/resources** projektu. Są one odczytywane z tego miejsca i przekazywane do usługi wraz z odpowiednimi tagami.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?range=76-87)]

W poprzednim fragmencie kodu były używane dwie funkcje pomocnicze pobierające obrazy jako strumienie zasobów i przekazujące je do usługi.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?range=277-314)]

### <a name="train-the-classifier-and-publish"></a>Klasyfikator uczenie i publikowanie

Ten kod tworzy pierwszą iteracją w projekcie, a następnie publikuje tej iteracji do endpoint prognoz. Nazwa nadana opublikowanych iteracji może służyć do wysyłania żądań do prognozowania. Iteracji nie jest dostępna w punkcie końcowym prognozowania, dopóki zostanie opublikowany.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?range=89-99)]

### <a name="use-the-prediction-endpoint"></a>Używanie punktu końcowego przewidywania

Punkt końcowy przewidywania, reprezentowany tutaj przez obiekt `predictor`, jest odwołaniem umożliwiającym przesyłanie obrazu do bieżącego modelu i uzyskiwanie przewidywania klasyfikacji. W tym przykładzie element `predictor` jest zdefiniowany w innym miejscu przy użyciu zmiennej środowiskowej klucza przewidywania.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?range=108-120)]

## <a name="run-the-application"></a>Uruchamianie aplikacji

Aby skompilować i uruchomić rozwiązanie przy użyciu narzędzia Maven, uruchom następujące polecenie w katalogu projektu w programie PowerShell:

```powershell
mvn compile exec:java
```

Dane wyjściowe aplikacji w konsoli powinny być podobne do poniższego tekstu:

```console
Creating project...
Adding images...
Adding image: hemlock_1.jpg
Adding image: hemlock_2.jpg
Adding image: hemlock_3.jpg
Adding image: hemlock_4.jpg
Adding image: hemlock_5.jpg
Adding image: hemlock_6.jpg
Adding image: hemlock_7.jpg
Adding image: hemlock_8.jpg
Adding image: hemlock_9.jpg
Adding image: hemlock_10.jpg
Adding image: japanese_cherry_1.jpg
Adding image: japanese_cherry_2.jpg
Adding image: japanese_cherry_3.jpg
Adding image: japanese_cherry_4.jpg
Adding image: japanese_cherry_5.jpg
Adding image: japanese_cherry_6.jpg
Adding image: japanese_cherry_7.jpg
Adding image: japanese_cherry_8.jpg
Adding image: japanese_cherry_9.jpg
Adding image: japanese_cherry_10.jpg
Training...
Training status: Training
Training status: Training
Training status: Completed
Done!
        Hemlock: 93.53%
        Japanese Cherry: 0.01%
```

Następnie możesz sprawdzić, czy przewidywanie dla obrazu testowego (kilka ostatnich wierszy danych wyjściowych) jest poprawne.

[!INCLUDE [clean-ic-project](includes/clean-ic-project.md)]

## <a name="next-steps"></a>Kolejne kroki

Teraz wiesz już, jak wykonać każdy krok procesu klasyfikacji obrazów przy użyciu kodu. W tym przykładzie jest wykonywana jedna iteracja szkolenia, ale często trzeba szkolić i testować model wiele razy, aby zwiększyć jego dokładność.

> [!div class="nextstepaction"]
> [Testowanie i ponowne szkolenie modelu](test-your-model.md)
