---
title: 'Szybki start: tworzenie projektu wykrywania obiektów przy użyciu zestawu Custom Vision SDK dla języka Java'
titleSuffix: Azure Cognitive Services
description: Utwórz projekt, dodaj tagi, przekaż obrazy, wyszkol projekt i wykrywaj obiekty przy użyciu zestawu Java SDK.
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 12/05/2019
ms.author: areddish
ms.openlocfilehash: a98c8b5d7c312582cf6644f74bda664c5031468b
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/17/2020
ms.locfileid: "76166153"
---
# <a name="quickstart-create-an-object-detection-project-with-the-custom-vision-sdk-for-java"></a>Szybki start: tworzenie projektu wykrywania obiektów przy użyciu zestawu Custom Vision SDK dla języka Java

W tym artykule pokazano, jak rozpocząć pracę z zestawem SDK Custom Vision przy użyciu języka Java w celu utworzenia modelu wykrywania obiektów. Po jego utworzeniu możesz dodać oznaczone regiony, przesłać obrazy, wyszkolić projekt, uzyskać adres URL domyślnego punktu końcowego przewidywania i użyć tego punktu końcowego do programowego przetestowania obrazu. Użyj tego przykładu jako szablonu do utworzenia własnej aplikacji języka Java.

## <a name="prerequisites"></a>Wymagania wstępne

- Wybrane środowisko IDE Java
- Zainstalowany zestaw [JDK 7 lub 8](https://aka.ms/azure-jdks).
- Zainstalowane narzędzie Maven
- [!INCLUDE [create-resources](includes/create-resources.md)]

## <a name="get-the-custom-vision-sdk-and-sample-code"></a>Pobieranie zestawu Custom Vision SDK i przykładowego kodu

Do napisania aplikacji języka Java używającej usługi Custom Vision potrzebne są pakiety Maven usługi Custom Vision. Te pakiety są zawarte w przykładowym projekcie, który zostanie pobrany, ale możesz uzyskać do nich dostęp osobno.

Pakiet SDK Custom Vision można zainstalować z centralnego repozytorium Maven:
- [Pakiet SDK do szkolenia](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-training)
- [Pakiet SDK do przewidywania](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-prediction)

Sklonuj lub pobierz projekt [Cognitive Services Java SDK Samples (Przykłady dotyczące zestawu Cognitive Services Java SDK)](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master). Przejdź do folderu **Vision/CustomVision/** .

Ten projekt języka Java tworzy nowy projekt wykrywania obiektów za pomocą usługi Custom Vision, mający nazwę __Sample Java OD Project__ (Przykładowy projekt wykrywania obiektów w języku Java), do którego można uzyskać dostęp za pośrednictwem [witryny internetowej Custom Vision](https://customvision.ai/). Następnie zostaną przesłane obrazy do szkolenia i testowania klasyfikatora. W tym projekcie klasyfikator jest używany do ustalania, czy drzewo jest __choiną__ (Hemlock), czy __wiśnią japońską__ (Japanese Cherry).

[!INCLUDE [get-keys](includes/get-keys.md)]

Program jest skonfigurowany do przechowywania danych kluczy jako zmiennych środowiskowych. Ustaw te zmienne, przechodząc do folderu **Vision/CustomVision** w programie PowerShell. Następnie wprowadź poniższe polecenia:

```powershell
$env:AZURE_CUSTOMVISION_TRAINING_API_KEY ="<your training api key>"
$env:AZURE_CUSTOMVISION_PREDICTION_API_KEY ="<your prediction api key>"
```

## <a name="understand-the-code"></a>Zrozumienie kodu

Załaduj projekt `Vision/CustomVision` w środowisku IDE Java i otwórz plik _CustomVisionSamples.java_. Znajdź metodę **runSample** i Dodaj komentarz do wywołania metody **ImageClassification_Sample** ,&mdash;ta metoda wykonuje scenariusz klasyfikacji obrazu, który nie jest uwzględniony w tym przewodniku. Metoda **ObjectDetection_Sample** implementuje podstawowe funkcje tego przewodnika Szybki start. Przejdź do jej definicji i przejrzyj kod. 

### <a name="create-a-new-custom-vision-service-project"></a>Tworzenie nowego projektu Custom Vision Service

Przejdź do bloku kodu, który tworzy klienta szkolenia i projekt wykrywania obiektów. Utworzony projekt będzie widoczny w odwiedzonej wcześniej [witrynie internetowej Custom Vision](https://customvision.ai/). Zobacz przeciążania metod tworzenia [projektu](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.customvision.training.trainings.createproject?view=azure-java-stable#com_microsoft_azure_cognitiveservices_vision_customvision_training_Trainings_createProject_String_CreateProjectOptionalParameter_) , aby określić inne opcje podczas tworzenia projektu (wyjaśnione w przewodniku po portalu sieci Web dla [czujnika](get-started-build-detector.md) ).

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_create_od)]

### <a name="add-tags-to-your-project"></a>Dodanie tagów do projektu

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_tags_od)]

### <a name="upload-and-tag-images"></a>Przekazywanie i tagowanie obrazów

Oznaczając tagami obrazy w projektach wykrywania obiektów, należy określić region każdego otagowanego obiektu za pomocą znormalizowanych współrzędnych. Przejdź do elementu Map o nazwie `regionMap`. Ten kod kojarzy poszczególne przykładowe obrazy z ich otagowanymi regionami.

> [!NOTE]
> Jeśli nie masz narzędzia do klikania i przeciągania, aby oznaczyć współrzędne regionów, możesz użyć internetowego interfejsu użytkownika w [Customvision.AI](https://www.customvision.ai/). W tym przykładzie współrzędne zostały już podane.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_od_mapping)]

Następnie przejdź do bloku kodu, który dodaje obrazy do projektu. Obrazy są odczytywane z folderu projektu **src/main/resources** oraz są przekazywane do usługi z odpowiednimi tagami i współrzędnymi regionów.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_upload_od)]

W poprzednim fragmencie kodu są używane dwie funkcje pomocnika, które pobierają obrazy jako strumienie zasobów i przekazują je do usługi (w jednej partii można przekazać do 64 obrazów).

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_helpers)]

### <a name="train-the-project-and-publish"></a>Uczenie projektu i publikowanie

Ten kod tworzy pierwszą iterację modelu predykcyjnego, a następnie publikuje tę iterację w punkcie końcowym przewidywania. Nazwa nadana do publikowanej iteracji może służyć do wysyłania żądań przewidywania. Iteracja nie jest dostępna w punkcie końcowym przewidywania do momentu opublikowania.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_train_od)]

### <a name="use-the-prediction-endpoint"></a>Używanie punktu końcowego przewidywania

Punkt końcowy przewidywania, reprezentowany tutaj przez obiekt `predictor`, jest odwołaniem umożliwiającym przesyłanie obrazu do bieżącego modelu i uzyskiwanie przewidywania klasyfikacji. W tym przykładzie element `predictor` jest zdefiniowany w innym miejscu przy użyciu zmiennej środowiskowej klucza przewidywania.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_prediction_od)]

## <a name="run-the-application"></a>Uruchamianie aplikacji

Aby skompilować i uruchomić rozwiązanie przy użyciu narzędzia Maven, uruchom następujące polecenie w katalogu projektu w programie PowerShell:

```powershell
mvn compile exec:java
```

Wyświetl dane wyjściowe konsoli, aby uzyskać wyniki rejestrowania i przewidywania. Możesz następnie sprawdzić, czy obraz testowy został odpowiednio otagowany i czy region wykrywania jest poprawny.

[!INCLUDE [clean-od-project](includes/clean-od-project.md)]

## <a name="next-steps"></a>Następne kroki

Teraz wiesz, jak można zrealizować w kodzie poszczególne kroki procesu wykrywania obiektów. W tym przykładzie jest wykonywana jedna iteracja szkolenia, ale często trzeba szkolić i testować model wiele razy, aby zwiększyć jego dokładność. Następny przewodnik dotyczy klasyfikacji obrazów. Jej zasady są podobne do wykrywania obiektów.

> [!div class="nextstepaction"]
> [Testowanie i ponowne szkolenie modelu](test-your-model.md)
