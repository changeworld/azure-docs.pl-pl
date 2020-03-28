---
title: 'Szybki start: biblioteka klienta przetwarzania obrazów w urozumienie komputera dla języka Python'
description: Wprowadzenie do biblioteki klienta przetwarzania obrazów w urozumienie komputera dla języka Python dzięki temu szybkiemustartowi.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: 5f552dfc320459d23e538e65e5f34c085f5b5ad9
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80136007"
---
<a name="HOLTop"></a>

[Dokumentacja](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision) | [referencyjna Przykłady pakietu kodu źródłowego](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-vision-computervision) | [(PiPy)](https://pypi.org/project/azure-cognitiveservices-vision-computervision/) | [biblioteki](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [utwórz bezpłatną subskrypcję platformy Azure](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-computer-vision-azure-resource"></a>Tworzenie zasobu platformy Computer Vision Azure

Usługi Azure Cognitive Services są reprezentowane przez zasoby platformy Azure, które subskrybujesz. Utwórz zasób dla przetwarzania obrazów komputerowych przy użyciu [witryny Azure portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) lub [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) na komputerze lokalnym. Możesz również wykonać następujące czynności:

* Pobierz [klucz wersji próbnej](https://azure.microsoft.com/try/cognitive-services/#decision) ważny przez siedem dni za darmo. Po zarejestrowaniu się będzie on dostępny w [witrynie sieci Web platformy Azure.](https://azure.microsoft.com/try/cognitive-services/my-apis/)  
* Wyświetlanie zasobu w [witrynie Azure portal](https://portal.azure.com/)

Po otrzymasz klucz z subskrypcji próbnej lub zasobu, [utwórz zmienne środowiskowe](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) dla klucza i adresu URL punktu końcowego, odpowiednio `COMPUTER_VISION_SUBSCRIPTION_KEY` i `COMPUTER_VISION_ENDPOINT`, odpowiednio.
 
### <a name="create-a-new-python-application"></a>Tworzenie nowej aplikacji w języku Python

Utwórz nowy&mdash;skrypt Języka Python*quickstart-file.py*, na przykład. Następnie otwórz go w preferowanym edytorze lub IDE i zaimportuj następujące biblioteki.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_imports)]

Następnie utwórz zmienne dla punktu końcowego i klucza platformy Azure zasobu.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_vars)]

> [!NOTE]
> Jeśli po uruchomieniu aplikacji utworzono zmienną środowiskową, należy zamknąć i ponownie otworzyć edytor, IDE lub powłokę uruchamianą ją, aby uzyskać dostęp do zmiennej.

### <a name="install-the-client-library"></a>Instalowanie biblioteki klienta

Bibliotekę klienta można zainstalować za pomocą:

```console
pip install --upgrade azure-cognitiveservices-vision-computervision
```

## <a name="object-model"></a>Model obiektu

Następujące klasy i interfejsy obsługują niektóre z głównych funkcji SDK języka Python firmy Computer Vision.

|Nazwa|Opis|
|---|---|
|[ComputerVisionClientOperationsMiksyna](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.operations.computervisionclientoperationsmixin?view=azure-python)| Ta klasa obsługuje bezpośrednio wszystkie operacje obrazu, takie jak analiza obrazu, wykrywanie tekstu i generowanie miniatur.|
| [ComputerVisionClient (System obsługi komputerów)](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python) | Ta klasa jest potrzebna dla wszystkich funkcji przetwarzania. Tworzenie wystąpienia z informacjami o subskrypcji i używać go do tworzenia wystąpień innych klas. Implementuje **ComputerVisionClientOperationsMixin**.|
|[VisualFeatureTytyty](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-python)| Wyliczenie to definiuje różne typy analizy obrazu, które można wykonać w standardowej operacji analizy. Należy określić zestaw **visualfeatureTypes** wartości w zależności od potrzeb. |

## <a name="code-examples"></a>Przykłady kodu

Te fragmenty kodu pokazują, jak wykonać następujące zadania za pomocą biblioteki klienta przetwarzania dla języka Python:

* [Uwierzytelnij klienta](#authenticate-the-client)
* [Analizowanie obrazu](#analyze-an-image)
* [Czytanie drukowanego i odręcznego tekstu](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>Uwierzytelnij klienta

> [!NOTE]
> Ten przewodnik Szybki start zakłada, że [utworzono zmienną środowiskową](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) dla klucza przetwarzania o nazwie `COMPUTER_VISION_SUBSCRIPTION_KEY`.

Tworzenie wystąpienia klienta z punktem końcowym i kluczem. Utwórz [obiekt CognitiveServicesCredentials](https://docs.microsoft.com/python/api/msrest/msrest.authentication.cognitiveservicescredentials?view=azure-python) za pomocą klucza i użyj go z punktem końcowym do utworzenia obiektu [ComputerVisionClient.](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python)

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_client)]

## <a name="analyze-an-image"></a>Analizowanie obrazu

Zapisz odwołanie do adresu URL obrazu, który chcesz przeanalizować.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_remoteimage)]

### <a name="get-image-description"></a>Pobierz opis obrazu

Poniższy kod pobiera listę wygenerowanych podpisów dla obrazu. Zobacz [Opisz obrazy,](../../concept-describing-images.md) aby uzyskać więcej informacji.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_describe)]

### <a name="get-image-category"></a>Pobierz kategorię obrazu

Poniższy kod pobiera wykrytą kategorię obrazu. Aby uzyskać więcej informacji, [zobacz Kategoryzowanie obrazów.](../../concept-categorizing-images.md)

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_categorize)]

### <a name="get-image-tags"></a>Pobierz znaczniki obrazów

Poniższy kod pobiera zestaw wykrytych tagów na obrazie. Aby uzyskać więcej informacji, zobacz [Tagi zawartości.](../../concept-tagging-images.md)

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_tags)]

### <a name="detect-objects"></a>Wykrywanie obiektów

Poniższy kod wykrywa typowe obiekty na obrazie i drukuje je na konsoli. Zobacz [Wykrywanie obiektów,](../../concept-object-detection.md) aby uzyskać więcej informacji.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_objects)]

### <a name="detect-brands"></a>Wykrywanie marek

Poniższy kod wykrywa marki firmowe i logo na obrazie i drukuje je na konsoli. Aby uzyskać więcej informacji, zobacz [Wykrywanie marki.](../../concept-brand-detection.md)

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_brands)]

### <a name="detect-faces"></a>Wykrywanie twarzy

Poniższy kod zwraca wykryte twarze na obrazie z ich współrzędne prostokąta i wybierz atrybuty twarzy. Zobacz [Wykrywanie twarzy, aby](../../concept-detecting-faces.md) uzyskać więcej informacji.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_faces)]

### <a name="detect-adult-racy-or-gory-content"></a>Wykrywanie treści dla dorosłych, rasistowskich lub gory

Poniższy kod drukuje wykrytą obecność zawartości dla dorosłych na obrazie. Zobacz [Adult, racy, gory treści](../../concept-detecting-adult-content.md) więcej szczegółów.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_adult)]

### <a name="get-image-color-scheme"></a>Pobierz schemat kolorów obrazu

Poniższy kod drukuje wykryte atrybuty kolorów obrazu, takie jak kolory dominujące i kolor akcentu. Zobacz [schematy kolorów, aby](../../concept-detecting-color-schemes.md) uzyskać więcej informacji.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_color)]

### <a name="get-domain-specific-content"></a>Pobierz zawartość specyficzną dla domeny

Usługa Computer Vision może używać specjalistycznego modelu do dalszej analizy obrazów. Aby uzyskać więcej informacji, zobacz [Zawartość specyficzna](../../concept-detecting-domain-content.md) dla domeny. 

Poniższy kod analizuje dane dotyczące wykrytych gwiazd na obrazie.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_celebs)]

Poniższy kod analizuje dane dotyczące wykrytych punktów orientacyjnych na obrazie.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_landmarks)]

### <a name="get-the-image-type"></a>Pobierz typ obrazu

Poniższy kod drukuje informacje o&mdash;typie obrazu, niezależnie od tego, czy jest to obiekt clipart, czy rysunek liniowy.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_type)]

## <a name="read-printed-and-handwritten-text"></a>Czytanie drukowanego i odręcznego tekstu

Funkcja Wizja komputerowa może odczytywać widoczny tekst na obrazie i konwertować go na strumień znaków. Robisz to w dwóch częściach.

### <a name="call-the-read-api"></a>Wywołanie interfejsu API odczytu

Najpierw użyj następującego kodu, aby wywołać **metodę batch_read_file** dla danego obrazu. Zwraca identyfikator operacji i rozpoczyna proces asynchroniczne, aby odczytać zawartość obrazu.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_read_call)]

### <a name="get-read-results"></a>Uzyskaj wyniki odczytu

Następnie pobierz identyfikator operacji zwrócony z **wywołania batch_read_file** i użyj go do kwerendy usługi dla wyników operacji. Poniższy kod sprawdza operację w odstępach jednosekundowych, dopóki wyniki nie zostaną zwrócone. Następnie drukuje wyodrębnione dane tekstowe na konsoli.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_read_response)]

## <a name="run-the-application"></a>Uruchamianie aplikacji

Uruchom aplikację za `python` pomocą polecenia w pliku szybkiego startu.

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli chcesz wyczyścić i usunąć subskrypcję usług Cognitive Services, możesz usunąć zasób lub grupę zasobów. Usunięcie grupy zasobów powoduje również usunięcie innych skojarzonych z nią zasobów.

* [Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#clean-up-resources)
* [Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli#clean-up-resources)


## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start dowiesz się, jak używać biblioteki przetwarzania obrazów dla języka Python do wykonywania zadań bazowych. Następnie zapoznaj się z dokumentacją referencyjną, aby dowiedzieć się więcej o bibliotece.


> [!div class="nextstepaction"]
>[Odwołanie do interfejsu API przetwarzania obrazów komputerowych (Python)](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision)

* [Czym jest przetwarzanie obrazów?](../../Home.md)
* Kod źródłowy tego przykładu można znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/ComputerVision/ComputerVisionQuickstart.py).
