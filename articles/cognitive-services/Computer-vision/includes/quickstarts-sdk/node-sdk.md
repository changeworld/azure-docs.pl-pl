---
title: 'Szybki start: biblioteka klienta przetwarzania obrazów dla pliku Node.js'
description: Wprowadzenie do biblioteki klienta przetwarzania obrazów dla aplikacji Node.js
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 01/22/2020
ms.author: pafarley
ms.openlocfilehash: 234e2402287fd76bdfd96cb6884c3fd20e805d06
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80136021"
---
<a name="HOLTop"></a>

[Dokumentacja](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-computervision/?view=azure-node-latest) | [referencyjna Przykłady pakietu (npm) kodu źródłowego biblioteki](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-computervision) | [(npm)](https://www.npmjs.com/package/@azure/cognitiveservices-computervision) | [Samples](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [utwórz bezpłatną subskrypcję platformy Azure](https://azure.microsoft.com/free/)
* Bieżąca wersja pliku [Node.js](https://nodejs.org/)

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-computer-vision-azure-resource"></a>Tworzenie zasobu platformy Computer Vision Azure

Usługi Azure Cognitive Services są reprezentowane przez zasoby platformy Azure, które subskrybujesz. Utwórz zasób dla przetwarzania obrazów komputerowych przy użyciu [witryny Azure portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) lub [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) na komputerze lokalnym. Możesz również wykonać następujące czynności:

* Pobierz [klucz wersji próbnej](https://azure.microsoft.com/try/cognitive-services/#decision) ważny przez siedem dni za darmo. Po zarejestrowaniu się będzie on dostępny w [witrynie sieci Web platformy Azure.](https://azure.microsoft.com/try/cognitive-services/my-apis/)
* Wyświetl swój zasób w [witrynie Azure portal](https://portal.azure.com/).

Po otrzymasz klucz z subskrypcji próbnej lub zasobu, [utwórz zmienne środowiskowe](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) dla klucza i adresu URL punktu końcowego, nazwany `COMPUTER_VISION_SUBSCRIPTION_KEY` i `COMPUTER_VISION_ENDPOINT` odpowiednio.

### <a name="create-a-new-nodejs-application"></a>Tworzenie nowej aplikacji Node.js

W oknie konsoli (takim jak cmd, PowerShell lub Bash) utwórz nowy katalog aplikacji i przejdź do niego.

```console
mkdir myapp && cd myapp
```

Uruchom `npm init` polecenie, aby utworzyć aplikację `package.json` węzła z plikiem.

```console
npm init
```

### <a name="install-the-client-library"></a>Instalowanie biblioteki klienta

Zainstaluj `ms-rest-azure` pakiety i `@azure/cognitiveservices-computervision` NPM:

```console
npm install @azure/cognitiveservices-computervision
```

`package.json` Plik aplikacji zostanie zaktualizowany o zależności.

### <a name="prepare-the-nodejs-script"></a>Przygotowywanie skryptu Node.js

Utwórz nowy plik, *index.js*i otwórz go w edytorze tekstu. Dodaj następujące instrukcje importu.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_imports)]

Następnie zdefiniuj funkcję `computerVision` i deklaruj serię asynchroniową z podstawową funkcją i funkcją wywołania zwrotnego. Dodasz kod szybkiego startu do funkcji `computerVision` podstawowej i wywołasz u dołu skryptu.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_functiondef_begin)]

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_functiondef_end)]

## <a name="object-model"></a>Model obiektu

Następujące klasy i interfejsy obsługują niektóre z głównych funkcji SDK computer vision node.js.

|Nazwa|Opis|
|---|---|
| [ComputerVisionClient (System obsługi komputerów)](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-computervision/computervisionclient?view=azure-node-latest) | Ta klasa jest potrzebna dla wszystkich funkcji przetwarzania. Tworzenie wystąpienia z informacjami o subskrypcji i używać go do wykonywania większości operacji obrazu.|
|[VisualFeatureTytyty](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-computervision/visualfeaturetypes?view=azure-node-latest)| Wyliczenie to definiuje różne typy analizy obrazu, które można wykonać w standardowej operacji analizy. Należy określić zestaw **visualfeatureTypes** wartości w zależności od potrzeb. |

## <a name="code-examples"></a>Przykłady kodu

Te fragmenty kodu pokazują, jak wykonać następujące zadania za pomocą biblioteki klienta przetwarzania dla aplikacji Node.js:

* [Uwierzytelnij klienta](#authenticate-the-client)
* [Analizowanie obrazu](#analyze-an-image)
* [Czytanie drukowanego i odręcznego tekstu](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>Uwierzytelnij klienta

Tworzenie zmiennych dla punktu końcowego i klucza platformy Azure zasobu. Jeśli po uruchomieniu aplikacji utworzono zmienną środowiskową, należy zamknąć i ponownie otworzyć edytor, IDE lub powłokę uruchamianą ją, aby uzyskać dostęp do zmiennej.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_vars)]

Tworzenie wystąpienia klienta z punktem końcowym i kluczem. Utwórz [obiekt ApiKeyCredentials](https://docs.microsoft.com/python/api/msrest/msrest.authentication.apikeycredentials?view=azure-python) za pomocą klucza i punktu końcowego i użyj go do utworzenia obiektu [ComputerVisionClient.](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-computervision/computervisionclient?view=azure-node-latest)

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_client)]

## <a name="analyze-an-image"></a>Analizowanie obrazu

Kod w tej sekcji analizuje zdalne obrazy, aby wyodrębnić różne funkcje wizualne. Można wykonać te operacje w ramach **analyzeImage** metody obiektu klienta lub można wywołać je przy użyciu poszczególnych metod. Szczegółowe informacje można znaleźć w [dokumentacji referencyjnej.](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-computervision/?view=azure-node-latest)

> [!NOTE]
> Można również analizować obraz lokalny. Zobacz przykładowy kod w [usłudze GitHub, aby](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js) uzyskać scenariusze dotyczące obrazów lokalnych.

### <a name="get-image-description"></a>Pobierz opis obrazu

Poniższy kod pobiera listę wygenerowanych podpisów dla obrazu. Zobacz [Opisz obrazy,](../../concept-describing-images.md) aby uzyskać więcej informacji.

Najpierw zdefiniuj adres URL obrazu do analizy:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_describe_image)]

Następnie dodaj następujący kod, aby uzyskać opis obrazu i wydrukować go na konsoli.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_describe)]

### <a name="get-image-category"></a>Pobierz kategorię obrazu

Poniższy kod pobiera wykrytą kategorię obrazu. Aby uzyskać więcej informacji, [zobacz Kategoryzowanie obrazów.](../../concept-categorizing-images.md)

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_categories)]

Zdefiniuj `formatCategories`funkcję pomocnika:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_categories_format)]

### <a name="get-image-tags"></a>Pobierz znaczniki obrazów

Poniższy kod pobiera zestaw wykrytych tagów na obrazie. Aby uzyskać więcej informacji, zobacz [Tagi zawartości.](../../concept-tagging-images.md)

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_tags)]

Zdefiniuj `formatTags`funkcję pomocnika:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_tagsformat)]

### <a name="detect-objects"></a>Wykrywanie obiektów

Poniższy kod wykrywa typowe obiekty na obrazie i drukuje je na konsoli. Zobacz [Wykrywanie obiektów,](../../concept-object-detection.md) aby uzyskać więcej informacji.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_objects)]

Zdefiniuj `formatRectObjects`funkcję pomocnika:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_objectformat)]

### <a name="detect-brands"></a>Wykrywanie marek

Poniższy kod wykrywa marki firmowe i logo na obrazie i drukuje je na konsoli. Aby uzyskać więcej informacji, zobacz [Wykrywanie marki.](../../concept-brand-detection.md)

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_brands)]

### <a name="detect-faces"></a>Wykrywanie twarzy

Poniższy kod zwraca wykryte twarze na obrazie z ich współrzędne prostokąta i wybierz atrybuty twarzy. Zobacz [Wykrywanie twarzy, aby](../../concept-detecting-faces.md) uzyskać więcej informacji.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_faces)]

Zdefiniuj `formatRectFaces`funkcję pomocnika:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_formatfaces)]

### <a name="detect-adult-racy-or-gory-content"></a>Wykrywanie treści dla dorosłych, rasistowskich lub gory

Poniższy kod drukuje wykrytą obecność zawartości dla dorosłych na obrazie. Zobacz [Adult, racy, gory treści](../../concept-detecting-adult-content.md) więcej szczegółów.

Zdefiniuj adres URL obrazu, który ma być używany:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_adult_image)]

Następnie dodaj następujący kod, aby wykryć zawartość dla dorosłych i wydrukować wyniki na konsoli.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_adult)]

### <a name="get-image-color-scheme"></a>Pobierz schemat kolorów obrazu

Poniższy kod drukuje wykryte atrybuty kolorów obrazu, takie jak kolory dominujące i kolor akcentu. Zobacz [schematy kolorów, aby](../../concept-detecting-color-schemes.md) uzyskać więcej informacji.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_colors)]

Zdefiniuj `printColorScheme` funkcję pomocnika, aby wydrukować szczegóły schematu kolorów na konsoli.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_colors_print)]

### <a name="get-domain-specific-content"></a>Pobierz zawartość specyficzną dla domeny

Usługa Computer Vision może używać specjalistycznego modelu do dalszej analizy obrazów. Aby uzyskać więcej informacji, zobacz [Zawartość specyficzna](../../concept-detecting-domain-content.md) dla domeny.

Najpierw zdefiniuj adres URL obrazu do analizy:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_domain_image)]

Poniższy kod analizuje dane dotyczące wykrytych punktów orientacyjnych na obrazie.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_landmarks)]

Zdefiniuj `formatRectDomain` funkcję pomocnika, aby przeanalizować dane lokalizacji dotyczące wykrytych punktów orientacyjnych.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_landmarks_rect)]

### <a name="get-the-image-type"></a>Pobierz typ obrazu

Poniższy kod drukuje informacje o&mdash;typie obrazu, niezależnie od tego, czy jest to obiekt clipart, czy rysunek liniowy.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_imagetype)]

Zdefiniuj `describeType`funkcję pomocnika:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_imagetype_describe)]

## <a name="read-printed-and-handwritten-text"></a>Czytanie drukowanego i odręcznego tekstu

Funkcja Wizja komputerowa może odczytywać widoczny tekst na obrazie i konwertować go na strumień znaków.

> [!NOTE]
> Można również odczytać tekst z obrazu lokalnego. Zobacz przykładowy kod w [usłudze GitHub, aby](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js) uzyskać scenariusze dotyczące obrazów lokalnych.

### <a name="set-up-test-images"></a>Konfigurowanie obrazów testowych

Zapisz odwołanie do adresu URL obrazów, z których chcesz wyodrębnić tekst.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_images)]

### <a name="call-the-recognize-api"></a>Wywołanie interfejsu API rozpoznawania

Dodaj poniższy kod, `recognizeText` który wywołuje funkcję dla danego obrazów.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_call)]

Zdefiniuj `recognizeText` funkcję. Wywołuje to **metodę recognizeText** w obiekcie klienta, który zwraca identyfikator operacji i uruchamia proces asynchroniczne, aby odczytać zawartość obrazu. Następnie używa identyfikatora operacji, aby sprawdzić operację w odstępach jednosekundowych, aż wyniki zostaną zwrócone. Następnie zwraca wyodrębnione wyniki.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_helper)]

Następnie zdefiniuj `printRecText`funkcję pomocnika , która drukuje wyniki operacji rozpoznawania na konsoli.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_print)]

## <a name="run-the-application"></a>Uruchamianie aplikacji

Uruchom aplikację za `node` pomocą polecenia w pliku szybkiego startu.

```console
node index.js
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli chcesz wyczyścić i usunąć subskrypcję usług Cognitive Services, możesz usunąć zasób lub grupę zasobów. Usunięcie grupy zasobów powoduje również usunięcie innych skojarzonych z nią zasobów.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfejs wiersza polecenia platformy Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
>[Odwołanie do interfejsu API przetwarzania obrazów komputerowych (node.js)](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-computervision/?view=azure-node-latest)

* [Czym jest przetwarzanie obrazów?](../../Home.md)
* Kod źródłowy tego przykładu można znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js).
