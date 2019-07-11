---
title: 'Szybki start: Zestaw SDK dla języka Python'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku Szybki Start dowiesz się, jak używać zestawu SDK języka Python do wykonywania typowych zadań, takich jak analizy obrazu, opis pobierania, rozpoznawanie tekstu i generowanie miniatur.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 04/17/2019
ms.author: pafarley
ms.openlocfilehash: c03568ece97bdaad86f4564debf9f3b2fa14c6ed
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2019
ms.locfileid: "67786643"
---
# <a name="azure-cognitive-services-computer-vision-sdk-for-python"></a>Zestaw SDK przetwarzania obrazów usług Azure Cognitive Services dla języka Python

Usługa przetwarzania obrazów oferuje deweloperom dostęp do zaawansowanych algorytmów przetwarzania obrazów i zwracania informacji. Algorytmy przetwarzania obrazów pozwalają analizować zawartość obrazu na różne sposoby, w zależności od interesujących Cię funkcji wizualnych.

* [Analizowanie obrazu](#analyze-an-image)
* [Pobieranie listy domen tematycznych](#get-subject-domain-list)
* [Analizowanie obrazu według domeny](#analyze-an-image-by-domain)
* [Pobieranie tekstu opisu obrazu](#get-text-description-of-an-image)
* [Pobieranie tekstu odręcznego z obrazu](#get-text-from-image)
* [Generowanie miniatury](#generate-thumbnail)

Aby uzyskać więcej informacji na temat tej usługi, zobacz [co to jest przetwarzanie obrazów?][computervision_docs].

Szukasz dodatkowej dokumentacji?

* [Dokumentacja referencyjna zestawu SDK](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision)
* [Dokumentacja przetwarzania obrazów w usługach Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/computer-vision/)

## <a name="prerequisites"></a>Wymagania wstępne

* [Python 3.6 +][python]
* Bezpłatne [klucz komputerowej][computervision_resource] and associated endpoint. You need these values when you create the instance of the [ComputerVisionClient][ref_computervisionclient] obiektu klienta. Aby uzyskać te wartości, użyj jednej z następujących metod.

### <a name="if-you-dont-have-an-azure-subscription"></a>Jeśli nie masz subskrypcji platformy Azure

Utwórz bezpłatne klucz prawidłowe przez 7 dni w ramach **[wypróbuj][computervision_resource]** środowisko usługi przetwarzania obrazów. Po utworzeniu klucz, skopiuj nazwę klucza i punktu końcowego. Będą one potrzebne do [utworzenia klienta](#create-client).

Po utworzeniu klucza zachowaj następujące wartości:

* Wartość klucza: ciąg 32 znaków w formacie `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`
* Klucza punktu końcowego: adres URL podstawowego punktu końcowego https\://westcentralus.api.cognitive.microsoft.com

### <a name="if-you-have-an-azure-subscription"></a>Jeśli masz subskrypcję platformy Azure

Najprostszą do utworzenia zasobu w ramach subskrypcji jest korzystających z następujących [wiersza polecenia platformy Azure][azure_cli] polecenia. Spowoduje to utworzenie klucz usługi cognitive Services, który można stosować w przypadku wielu usług cognitive services. Musisz wybrać _istniejących_ nazwę grupy zasobów, na przykład, "group Moje cogserv" i nowego zasobu przetwarzania komputera nazwę, takich jak "Mój komputer wizji — zasób".

```Bash
RES_REGION=westeurope
RES_GROUP=<resourcegroup-name>
ACCT_NAME=<computervision-account-name>

az cognitiveservices account create \
    --resource-group $RES_GROUP \
    --name $ACCT_NAME \
    --location $RES_REGION \
    --kind CognitiveServices \
    --sku S0 \
    --yes
```

<!--
## Installation

Install the Azure Cognitive Services Computer Vision SDK with [pip][pip], optionally within a [virtual environment][venv].

### Configure a virtual environment (optional)

Although not required, you can keep your base system and Azure SDK environments isolated from one another if you use a [virtual environment][virtualenv]. Execute the following commands to configure and then enter a virtual environment with [venv][venv], such as `cogsrv-vision-env`:

```Bash
python3 -m venv cogsrv-vision-env
source cogsrv-vision-env/bin/activate
```
-->

### <a name="install-the-sdk"></a>Instalacja zestawu SDK

Zainstaluj zestaw SDK przetwarzania komputera usług Cognitive platformy Azure dla języka Python [pakietu][pypi_computervision] with [pip][pip]:

```Bash
pip install azure-cognitiveservices-vision-computervision
```

## <a name="authentication"></a>Authentication

Po utworzeniu zasobu przetwarzania obrazów, należy jej **punktu końcowego**, a jeden z jego **kluczy kont** do utworzenia wystąpienia obiektu klienta.

Te wartości są używane podczas tworzenia wystąpienia [ComputerVisionClient][ref_computervisionclient] obiektu klienta.

Na przykład użyć terminala powłoki Bash, aby ustawić zmienne środowiskowe:

```Bash
ACCOUNT_ENDPOINT=<resourcegroup-name>
ACCT_NAME=<computervision-account-name>
```

### <a name="for-azure-subscription-users-get-credentials-for-key-and-endpoint"></a>Dla użytkowników do subskrypcji platformy Azure uzyskiwać poświadczenia na potrzeby key i punktu końcowego

Jeśli nie pamiętasz klucz i punkt końcowy, można użyć następującą metodę, aby je znaleźć. Jeśli musisz utworzyć klucz i punkt końcowy, można użyć metody [posiadacze subskrypcji Azure](#if-you-have-an-azure-subscription) lub [użytkownicy bez subskrypcji platformy Azure](#if-you-dont-have-an-azure-subscription).

Użyj [wiersza polecenia platformy Azure][cloud_shell] fragment kodu poniżej, aby wypełnić dwóch zmiennych środowiskowych za pomocą konta komputerowej **punktu końcowego** i jeden z jego **klucze** (możesz również znaleźć te wartości w [witryny Azure portal][azure_portal]). Ten fragment kodu został sformatowany dla powłoki Bash.

```Bash
RES_GROUP=<resourcegroup-name>
ACCT_NAME=<computervision-account-name>

export ACCOUNT_ENDPOINT=$(az cognitiveservices account show \
    --resource-group $RES_GROUP \
    --name $ACCT_NAME \
    --query endpoint \
    --output tsv)

export ACCOUNT_KEY=$(az cognitiveservices account keys list \
    --resource-group $RES_GROUP \
    --name $ACCT_NAME \
    --query key1 \
    --output tsv)
```


### <a name="create-client"></a>Tworzenie klienta

Pobierz klucz i punkt końcowy ze zmiennych środowiskowych, a następnie utwórz [ComputerVisionClient][ref_computervisionclient] obiektu klienta.

```Python
from azure.cognitiveservices.vision.computervision import ComputerVisionClient
from azure.cognitiveservices.vision.computervision.models import VisualFeatureTypes
from msrest.authentication import CognitiveServicesCredentials

# Get endpoint and key from environment variables
import os
endpoint = os.environ['ACCOUNT_ENDPOINT']
key = os.environ['ACCOUNT_KEY']

# Set credentials
credentials = CognitiveServicesCredentials(key)

# Create client
client = ComputerVisionClient(endpoint, credentials)
```

## <a name="examples"></a>Przykłady

Potrzebujesz [ComputerVisionClient][ref_computervisionclient] obiektu klienta przed przy użyciu dowolnej z poniższych zadań.

### <a name="analyze-an-image"></a>Analizowanie obrazu

Możesz analizować obraz dla niektórych funkcji [ `analyze_image` ][ref_computervisionclient_analyze_image] . Use the [`visual_features`][ref_computervision_model_visualfeatures] właściwość umożliwiająca ustawienie typów analizy do wykonania na obrazie. Typowe wartości to `VisualFeatureTypes.tags` i `VisualFeatureTypes.description`.

```Python
url = "https://upload.wikimedia.org/wikipedia/commons/thumb/1/12/Broadway_and_Times_Square_by_night.jpg/450px-Broadway_and_Times_Square_by_night.jpg"

image_analysis = client.analyze_image(url,visual_features=[VisualFeatureTypes.tags])

for tag in image_analysis.tags:
    print(tag)
```

### <a name="get-subject-domain-list"></a>Pobieranie listy domen tematycznych

Przeglądanie domen podmiotu, używany do analizowania obrazu za pomocą [ `list_models` ][ref_computervisionclient_list_models]. Te nazwy domen są używane podczas [analizowania obrazu według domeny](#analyze-an-image-by-domain). Przykładowa domena to `landmarks`.

```Python
models = client.list_models()

for x in models.models_property:
    print(x)
```

### <a name="analyze-an-image-by-domain"></a>Analizowanie obrazu według domeny

Możesz analizować obrazu według domeny podmiotu z [ `analyze_image_by_domain` ][ref_computervisionclient_analyze_image_by_domain]. Prawidłowe nazwy domen można znaleźć na [liście obsługiwanych domen tematycznych](#get-subject-domain-list).

```Python
# type of prediction
domain = "landmarks"

# Public domain image of Eiffel tower
url = "https://images.pexels.com/photos/338515/pexels-photo-338515.jpeg"

# English language response
language = "en"

analysis = client.analyze_image_by_domain(domain, url, language)

for landmark in analysis.result["landmarks"]:
    print(landmark["name"])
    print(landmark["confidence"])
```

### <a name="get-text-description-of-an-image"></a>Pobieranie tekstu opisu obrazu

Zawiera opis tekstowy opartych na języku obrazu [ `describe_image` ][ref_computervisionclient_describe_image]. Przy użyciu właściwości `max_description` można pobrać kilka opisów w przypadku przeprowadzania analizy tekstu pod kątem słów kluczowych skojarzonych z obrazem. Przykładowe opisy tekstowe dla następującego obrazu to `a train crossing a bridge over a body of water`, `a large bridge over a body of water` i `a train crossing a bridge over a large body of water`.

```Python
domain = "landmarks"
url = "http://www.public-domain-photos.com/free-stock-photos-4/travel/san-francisco/golden-gate-bridge-in-san-francisco.jpg"
language = "en"
max_descriptions = 3

analysis = client.describe_image(url, max_descriptions, language)

for caption in analysis.captions:
    print(caption.text)
    print(caption.confidence)
```

### <a name="get-text-from-image"></a>Pobieranie tekstu z obrazu

Można wyodrębnić dowolny odręczny lub drukowany tekst znajdujący się na obrazie. Wymaga to dwóch wywołań do zestawu SDK: [ `batch_read_file` ](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python) i [ `get_read_operation_result` ](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python). Wywołanie `batch_read_file` jest asynchroniczna. W wynikach `get_read_operation_result` wywołanie, należy sprawdzić, jeśli pierwsze wywołanie została ukończona z [ `TextOperationStatusCodes` ][ref_computervision_model_textoperationstatuscodes] przed wyodrębniania danych z pliku tekstowego. Wyniki obejmują tekst oraz współrzędne pola ograniczenia tekstu.

```Python
# import models
from azure.cognitiveservices.vision.computervision.models import TextOperationStatusCodes
import time

url = "https://azurecomcdn.azureedge.net/cvt-1979217d3d0d31c5c87cbd991bccfee2d184b55eeb4081200012bdaf6a65601a/images/shared/cognitive-services-demos/read-text/read-1-thumbnail.png"
raw = True
custom_headers = None
numberOfCharsInOperationId = 36

# Async SDK call
rawHttpResponse = client.batch_read_file(url, custom_headers,  raw)

# Get ID from returned headers
operationLocation = rawHttpResponse.headers["Operation-Location"]
idLocation = len(operationLocation) - numberOfCharsInOperationId
operationId = operationLocation[idLocation:]

# SDK call
while True:
    result = client.get_read_operation_result(operationId)
    if result.status not in ['NotStarted', 'Running']:
        break
    time.sleep(1)

# Get data
if result.status == TextOperationStatusCodes.succeeded:
    for textResult in result.recognition_results:
        for line in textResult.lines:
            print(line.text)
            print(line.bounding_box)
```

### <a name="generate-thumbnail"></a>Generowanie miniatury

Można wygenerować się miniaturę obrazu (JPG) [ `generate_thumbnail` ][ref_computervisionclient_generate_thumbnail]. Miniatura nie musi mieć takich samych proporcji co oryginalny obraz.

Aby skorzystać z tego przykładu, zainstaluj pakiet **Pillow**:

```bash
pip install Pillow
```

Po zainstalowaniu pakietu Pillow możesz użyć go w poniższym przykładowym kodzie w celu wygenerowania miniatury.

```Python
# Pillow package
from PIL import Image

# IO package to create local image
import io

width = 50
height = 50
url = "http://www.public-domain-photos.com/free-stock-photos-4/travel/san-francisco/golden-gate-bridge-in-san-francisco.jpg"

thumbnail = client.generate_thumbnail(width, height, url)

for x in thumbnail:
    image = Image.open(io.BytesIO(x))

image.save('thumbnail.jpg')
```

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="general"></a>Ogólne

Kiedy wchodzisz w interakcję z [ComputerVisionClient][ref_computervisionclient] client object using the Python SDK, the [`ComputerVisionErrorException`][ref_computervision_computervisionerrorexception] klasa służy do zwracania błędów. Błędy zwracane przez tę usługę odpowiadają kodom stanu HTTP zwracanym dla żądań interfejsu API REST.

Na przykład jeśli spróbujesz przeprowadzić analizę obrazu za pomocą nieprawidłowego klucza, zostanie zwrócony błąd `401`. W poniższym fragmencie kodu [błąd][ref_httpfailure] odbywa się bez problemu zmieniała przechwytywanie wyjątku i wyświetlając dodatkowe informacje o tym błędzie.

```Python

domain = "landmarks"
url = "http://www.public-domain-photos.com/free-stock-photos-4/travel/san-francisco/golden-gate-bridge-in-san-francisco.jpg"
language = "en"
max_descriptions = 3

try:
    analysis = client.describe_image(url, max_descriptions, language)

    for caption in analysis.captions:
        print(caption.text)
        print(caption.confidence)
except HTTPFailure as e:
    if e.status_code == 401:
        print("Error unauthorized. Make sure your key and endpoint are correct.")
    else:
        raise
```

### <a name="handle-transient-errors-with-retries"></a>Obsługa błędów przejściowych z logiką ponawiania próby

Podczas pracy z [ComputerVisionClient][ref_computervisionclient] client, you might encounter transient failures caused by [rate limits][computervision_request_units] wymuszane przez usługę lub inne problemy przejściowe, takie jak awarie sieci. Aby uzyskać informacje na temat obsługi tego rodzaju błędów, zobacz [wzorca ponawiania][azure_pattern_retry] w przewodniku wzorce projektowe w chmurze i powiązane [wzorzec wyłącznika][azure_pattern_circuit_breaker].

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Applying content tags to images (Stosowanie tagów zawartości do obrazów)](../concept-tagging-images.md)

<!-- LINKS -->
[pip]: https://pypi.org/project/pip/
[python]: https://www.python.org/downloads/

[azure_cli]: https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-create
[azure_pattern_circuit_breaker]: https://docs.microsoft.com/azure/architecture/patterns/circuit-breaker
[azure_pattern_retry]: https://docs.microsoft.com/azure/architecture/patterns/retry
[azure_portal]: https://portal.azure.com
[azure_sub]: https://azure.microsoft.com/free/

[cloud_shell]: https://docs.microsoft.com/azure/cloud-shell/overview

[venv]: https://docs.python.org/3/library/venv.html
[virtualenv]: https://virtualenv.pypa.io

[source_code]: https://github.com/Azure/azure-sdk-for-python/tree/master/azure-cognitiveservices-vision-computervision

[pypi_computervision]:https://pypi.org/project/azure-cognitiveservices-vision-computervision/
[pypi_pillow]:https://pypi.org/project/Pillow/

[ref_computervision_sdk]: https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision?view=azure-python
[ref_computervision_computervisionerrorexception]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.models.computervisionerrorexception?view=azure-python
[ref_httpfailure]: https://docs.microsoft.com/python/api/msrest/msrest.exceptions.httpoperationerror?view=azure-python


[computervision_resource]: https://azure.microsoft.com/try/cognitive-services/?

[computervision_docs]: https://docs.microsoft.com/azure/cognitive-services/computer-vision/home

[ref_computervisionclient]: https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python

[ref_computervisionclient_analyze_image]: https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python

[ref_computervisionclient_list_models]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python

[ref_computervisionclient_analyze_image_by_domain]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python

[ref_computervisionclient_describe_image]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python

[ref_computervisionclient_get_text_operation_result]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python

[ref_computervisionclient_generate_thumbnail]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python


[ref_computervision_model_visualfeatures]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-python

[ref_computervision_model_textoperationstatuscodes]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.models.textoperationstatuscodes?view=azure-python

[computervision_request_units]:https://azure.microsoft.com/pricing/details/cognitive-services/computer-vision/
