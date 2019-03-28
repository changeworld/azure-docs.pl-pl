---
title: 'Szybki start: Zestaw SDK dla języka Python'
titleSuffix: Azure Cognitive Services
description: Z tego przewodnika Szybki start dowiesz się, jak wykonywać typowe zadania przy użyciu zestawu SDK dla języka Python.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 02/28/2019
ms.author: pafarley
ms.openlocfilehash: 23db6f889e2ca4266b7e3566c18cf9a85d4062a8
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2019
ms.locfileid: "58517559"
---
# <a name="azure-cognitive-services-computer-vision-sdk-for-python"></a>Zestaw SDK przetwarzania obrazów usług Azure Cognitive Services dla języka Python

Usługa przetwarzania obrazów oferuje deweloperom dostęp do zaawansowanych algorytmów przetwarzania obrazów i zwracania informacji. Algorytmy przetwarzania obrazów pozwalają analizować zawartość obrazu na różne sposoby, w zależności od interesujących Cię funkcji wizualnych. 

* [Analizowanie obrazu](#analyze-an-image)
* [Pobieranie listy domen tematycznych](#get-subject-domain-list)
* [Analizowanie obrazu według domeny](#analyze-an-image-by-domain)
* [Pobieranie tekstu opisu obrazu](#get-text-description-of-an-image)
* [Pobieranie tekstu odręcznego z obrazu](#get-text-from-image)
* [Generowanie miniatury](#generate-thumbnail)

Aby uzyskać więcej informacji na temat tej usługi, zobacz [Czym jest przetwarzanie obrazów?][computervision_docs].

Szukasz dodatkowej dokumentacji?

* [Dokumentacja referencyjna zestawu SDK](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision)
* [Dokumentacja przetwarzania obrazów w usługach Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/computer-vision/)

## <a name="prerequisites"></a>Wymagania wstępne

* [Python 3.6+][python]
* Bezpłatny [klucz interfejsu API przetwarzania obrazów][computervision_resource] i skojarzony region. Te wartości są potrzebne do utworzenia wystąpienia obiektu klienta [ComputerVisionAPI][ref_computervisionclient]. Aby uzyskać te wartości, użyj jednej z następujących metod. 

### <a name="if-you-dont-have-an-azure-subscription"></a>Jeśli nie masz subskrypcji platformy Azure

Utwórz bezpłatne klucz prawidłowe przez 7 dni w ramach **[wypróbuj] [ computervision_resource]** środowisko usługi przetwarzania obrazów. Po utworzeniu klucza skopiuj klucz i nazwę regionu. Będą one potrzebne do [utworzenia klienta](#create-client).

Po utworzeniu klucza zachowaj następujące wartości:

* Wartość klucza: ciąg 32 znaków w formacie `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` 
* Region klucza: poddomena adresu URL punktu końcowego — https://**westcentralus**.api.cognitive.microsoft.com

### <a name="if-you-have-an-azure-subscription"></a>Jeśli masz subskrypcję platformy Azure

Najprostszą do utworzenia zasobu w ramach subskrypcji jest korzystających z następujących [wiersza polecenia platformy Azure] [ azure_cli] polecenia. Spowoduje to utworzenie klucz usługi cognitive Services, który można stosować w przypadku wielu usług cognitive services. Musisz wybrać _istniejących_ nazwę grupy zasobów, na przykład, "group Moje cogserv" i nowego zasobu przetwarzania komputera nazwę, takich jak "Mój komputer wizji — zasób". 

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

Zainstaluj [pakiet][pypi_computervision] zestawu SDK przetwarzania obrazów usług Azure Cognitive Services dla języka Python za pomocą programu [pip][pip]:

```Bash
pip install azure-cognitiveservices-vision-computervision
```

## <a name="authentication"></a>Authentication

Po utworzeniu zasobu przetwarzania obrazów należy utworzyć wystąpienie obiektu klienta przy użyciu **regionu** i jednego z **kluczy konta** tego zasobu.

Wartości te umożliwiają utworzenie wystąpienia obiektu klienta [ComputerVisionAPI][ref_computervisionclient]. 

Na przykład użyć terminala powłoki Bash, aby ustawić zmienne środowiskowe:

```Bash
ACCOUNT_REGION=<resourcegroup-name>
ACCT_NAME=<computervision-account-name>
```

### <a name="for-azure-subscription-users-get-credentials-for-key-and-region"></a>Dla użytkowników do subskrypcji platformy Azure należy uzyskać poświadczenia dla klucza i regionu

Jeśli nie pamiętasz swój region i klucz, można użyć następującą metodę, aby je znaleźć. Jeśli potrzebujesz do tworzenia klucza i region, można użyć metody [posiadacze subskrypcji Azure](#if-you-have-an-azure-subscription) lub [użytkownicy bez subskrypcji platformy Azure](#if-you-dont-have-an-azure-subscription).

Poniższy fragment kodu [interfejsu wiersza polecenia platformy Azure][cloud_shell] umożliwia wypełnienie dwóch zmiennych środowiskowych wartościami **regionu** i jednego z **kluczy** konta przetwarzania obrazów (wartości te można również znaleźć w witrynie [Azure Portal][azure_portal]). Ten fragment kodu został sformatowany dla powłoki Bash.

```Bash
RES_GROUP=<resourcegroup-name>
ACCT_NAME=<computervision-account-name>

export ACCOUNT_REGION=$(az cognitiveservices account show \
    --resource-group $RES_GROUP \
    --name $ACCT_NAME \
    --query location \
    --output tsv)

export ACCOUNT_KEY=$(az cognitiveservices account keys list \
    --resource-group $RES_GROUP \
    --name $ACCT_NAME \
    --query key1 \
    --output tsv)
```


### <a name="create-client"></a>Tworzenie klienta

Pobierz region i klucz ze zmiennych środowiskowych, a następnie utwórz [ComputerVisionAPI] [ ref_computervisionclient] obiektu klienta.  

```Python
from azure.cognitiveservices.vision.computervision import ComputerVisionAPI
from azure.cognitiveservices.vision.computervision.models import VisualFeatureTypes
from msrest.authentication import CognitiveServicesCredentials

# Get region and key from environment variables
import os
region = os.environ['ACCOUNT_REGION']
key = os.environ['ACCOUNT_KEY']

# Set credentials
credentials = CognitiveServicesCredentials(key)

# Create client
client = ComputerVisionAPI(region, credentials)
```

## <a name="examples"></a>Przykłady

Aby wykonać dowolne z poniższych zadań, potrzebujesz obiektu klienta [ComputerVisionAPI][ref_computervisionclient].

### <a name="analyze-an-image"></a>Analizowanie obrazu

Obraz można analizować pod kątem określonych cech za pomocą operacji [`analyze_image`][ref_computervisionclient_analyze_image]. Właściwość [`visual_features`][ref_computervision_model_visualfeatures] umożliwia ustawienie typów analizy do wykonania na obrazie. Typowe wartości to `VisualFeatureTypes.tags` i `VisualFeatureTypes.description`.

```Python
url = "https://upload.wikimedia.org/wikipedia/commons/thumb/1/12/Broadway_and_Times_Square_by_night.jpg/450px-Broadway_and_Times_Square_by_night.jpg"

image_analysis = client.analyze_image(url,visual_features=[VisualFeatureTypes.tags])

for tag in image_analysis.tags:
    print(tag)
```

### <a name="get-subject-domain-list"></a>Pobieranie listy domen tematycznych

Za pomocą operacji [`list_models`][ref_computervisionclient_list_models] można przejrzeć domeny tematyczne używane do analizy obrazu. Te nazwy domen są używane podczas [analizowania obrazu według domeny](#analyze-an-image-by-domain). Przykładowa domena to `landmarks`.

```Python
models = client.list_models()

for x in models.models_property:
    print(x)
```

### <a name="analyze-an-image-by-domain"></a>Analizowanie obrazu według domeny

Za pomocą operacji [`analyze_image_by_domain`][ref_computervisionclient_analyze_image_by_domain] można analizować obraz według domeny tematycznej. Prawidłowe nazwy domen można znaleźć na [liście obsługiwanych domen tematycznych](#get-subject-domain-list).  

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

Za pomocą operacji [`describe_image`][ref_computervisionclient_describe_image] można uzyskać opis językowy obrazu. Przy użyciu właściwości `max_description` można pobrać kilka opisów w przypadku przeprowadzania analizy tekstu pod kątem słów kluczowych skojarzonych z obrazem. Przykładowe opisy tekstowe dla następującego obrazu to `a train crossing a bridge over a body of water`, `a large bridge over a body of water` i `a train crossing a bridge over a large body of water`.

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

Można wyodrębnić dowolny odręczny lub drukowany tekst znajdujący się na obrazie. Wymaga to dwóch wywołań zestawu SDK: [`recognize_text`][ref_computervisionclient_recognize_text] i [`get_text_operation_result`][ref_computervisionclient_get_text_operation_result]. Wywołanie operacji recognize_text jest asynchroniczne. Przed wyodrębnieniem danych tekstowych musisz sprawdzić w wynikach wywołania operacji get_text_operation_result, czy pierwsze wywołanie zostało ukończone z wyliczeniem [`TextOperationStatusCodes`][ref_computervision_model_textoperationstatuscodes]. Wyniki obejmują tekst oraz współrzędne pola ograniczenia tekstu. 

```Python
# import models
from azure.cognitiveservices.vision.computervision.models import TextRecognitionMode
from azure.cognitiveservices.vision.computervision.models import TextOperationStatusCodes

url = "https://azurecomcdn.azureedge.net/cvt-1979217d3d0d31c5c87cbd991bccfee2d184b55eeb4081200012bdaf6a65601a/images/shared/cognitive-services-demos/read-text/read-1-thumbnail.png"
mode = TextRecognitionMode.handwritten
raw = True
custom_headers = None
numberOfCharsInOperationId = 36

# Async SDK call
rawHttpResponse = client.recognize_text(url, mode, custom_headers,  raw)

# Get ID from returned headers
operationLocation = rawHttpResponse.headers["Operation-Location"]
idLocation = len(operationLocation) - numberOfCharsInOperationId
operationId = operationLocation[idLocation:]

# SDK call
while result.status in ['NotStarted', 'Running']:
    time.sleep(1)
    result = client.get_text_operation_result(operationId)

# Get data
if result.status == TextOperationStatusCodes.succeeded:

    for line in result.recognition_result.lines:
        print(line.text)
        print(line.bounding_box)
```

### <a name="generate-thumbnail"></a>Generowanie miniatury

Za pomocą operacji [`generate_thumbnail`][ref_computervisionclient_generate_thumbnail] można wygenerować miniaturę obrazu w formacie JPG. Miniatura nie musi mieć takich samych proporcji co oryginalny obraz. 

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

W przypadku korzystania z obiektu klienta [ComputerVisionAPI][ref_computervisionclient] przy użyciu zestawu SDK dla języka Python do zwracania błędów służy klasa [`ComputerVisionErrorException`][ref_computervision_computervisionerrorexception]. Błędy zwracane przez tę usługę odpowiadają kodom stanu HTTP zwracanym dla żądań interfejsu API REST.

Na przykład jeśli spróbujesz przeprowadzić analizę obrazu za pomocą nieprawidłowego klucza, zostanie zwrócony błąd `401`. W poniższym fragmencie kodu przedstawiono bezpieczną obsługę [błędu][ref_httpfailure] opartą na przechwyceniu wyjątku i wyświetleniu dodatkowych informacji o błędzie.

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
        print("Error unauthorized. Make sure your key and region are correct.")
    else:
        raise
```

### <a name="handle-transient-errors-with-retries"></a>Obsługa błędów przejściowych z logiką ponawiania próby

Podczas korzystania z klienta [ComputerVisionAPI][ref_computervisionclient] mogą wystąpić błędy przejściowe, związane z [ograniczeniami liczby wywołań][computervision_request_units] wymuszonymi przez usługę, lub inne przejściowe problemy, takie jak awarie sieci. Aby uzyskać informacje na temat obsługi takich błędów, zobacz temat [Wzorzec ponawiania][azure_pattern_retry] w przewodniku po wzorcach projektowych opartych na chmurze i powiązany temat [Wzorzec wyłącznika][azure_pattern_circuit_breaker].

### <a name="more-sample-code"></a>Więcej przykładów kodu

Kilka przykładów dotyczących zestawu SDK przetwarzania obrazów dla języka Python jest dostępnych w repozytorium GitHub zestawu SDK. Zawierają one przykładowy kod dla innych scenariuszy, często występujących podczas korzystania z przetwarzania obrazów:

* [recognize_text][recognize-text]

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Applying content tags to images (Stosowanie tagów zawartości do obrazów)](../concept-tagging-images.md)

<!-- LINKS -->
[pip]: https://pypi.org/project/pip/
[python]: https://www.python.org/downloads/

[azure_cli]: https://docs.microsoft.com/en-us/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-create
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


[computervision_resource]: https://azure.microsoft.com/en-us/try/cognitive-services/?

[computervision_docs]: https://docs.microsoft.com/azure/cognitive-services/computer-vision/home

[ref_computervisionclient]: https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python

[ref_computervisionclient_analyze_image]: https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python

[ref_computervisionclient_list_models]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python

[ref_computervisionclient_analyze_image_by_domain]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python

[ref_computervisionclient_describe_image]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python

[ref_computervisionclient_recognize_text]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python

[ref_computervisionclient_get_text_operation_result]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python

[ref_computervisionclient_generate_thumbnail]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python


[ref_computervision_model_visualfeatures]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-python

[ref_computervision_model_textoperationstatuscodes]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.models.textoperationstatuscodes?view=azure-python

[computervision_request_units]:https://azure.microsoft.com/pricing/details/cognitive-services/computer-vision/

[recognize-text]:https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/vision/computer_vision_samples.py

