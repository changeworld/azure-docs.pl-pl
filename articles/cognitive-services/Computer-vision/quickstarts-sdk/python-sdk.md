---
title: 'Szybki start: Zestaw SDK dla języka Python'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku szybki start dowiesz się, jak używać zestawu SDK języka Python dla typowych zadań, takich jak analizowanie obrazów, uzyskiwanie opisu, rozpoznawanie tekstu i generowanie miniatury.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 04/17/2019
ms.author: pafarley
ms.openlocfilehash: c03568ece97bdaad86f4564debf9f3b2fa14c6ed
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/12/2019
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
* Bezpłatny [klucz przetwarzanie obrazów][computervision_resource] i skojarzony punkt końcowy. Te wartości są potrzebne podczas tworzenia wystąpienia obiektu klienta [ComputerVisionClient][ref_computervisionclient] . Aby uzyskać te wartości, użyj jednej z następujących metod.

### <a name="if-you-dont-have-an-azure-subscription"></a>Jeśli nie masz subskrypcji platformy Azure

Dla usługi przetwarzanie obrazów Utwórz bezpłatny klucz ważny przez 7 dni **[][computervision_resource]** . Po utworzeniu klucza skopiuj klucz i nazwę punktu końcowego. Będą one potrzebne do [utworzenia klienta](#create-client).

Po utworzeniu klucza zachowaj następujące wartości:

* Wartość klucza: ciąg 32 znaków w formacie `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`
* Kluczowy punkt końcowy: podstawowy adres URL punktu\:końcowego, https//westcentralus.API.Cognitive.Microsoft.com

### <a name="if-you-have-an-azure-subscription"></a>Jeśli masz subskrypcję platformy Azure

Najprostszą metodą utworzenia zasobu w ramach subskrypcji jest użycie następującego polecenia [interfejsu CLI platformy Azure][azure_cli] . Spowoduje to utworzenie klucza usługi poznawczej, który może być używany w wielu usługach poznawczych. Należy wybrać nazwę _istniejącej_ grupy zasobów, na przykład "My-cogserv-Group" i nową nazwę zasobu, na przykład "My-Computer-Vision-Resource".

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

Zainstaluj [pakiet][pypi_computervision] Azure COGNITIVE Services przetwarzanie obrazów SDK dla języka Python za pomocą narzędzia [PIP][pip]:

```Bash
pip install azure-cognitiveservices-vision-computervision
```

## <a name="authentication"></a>Authentication

Po utworzeniu zasobu przetwarzanie obrazów potrzebujesz **punktu końcowego**oraz jednego z **kluczy konta** , aby utworzyć wystąpienie obiektu klienta.

Użyj tych wartości podczas tworzenia wystąpienia obiektu klienta [ComputerVisionClient][ref_computervisionclient] .

Na przykład użyj terminalu bash, aby ustawić zmienne środowiskowe:

```Bash
ACCOUNT_ENDPOINT=<resourcegroup-name>
ACCT_NAME=<computervision-account-name>
```

### <a name="for-azure-subscription-users-get-credentials-for-key-and-endpoint"></a>W przypadku użytkowników subskrypcji platformy Azure Uzyskaj poświadczenia dla klucza i punktu końcowego

Jeśli nie pamiętasz swojego punktu końcowego i klucza, możesz użyć następującej metody, aby je znaleźć. Jeśli musisz utworzyć klucz i punkt końcowy, możesz użyć metody dla [posiadaczy subskrypcji platformy Azure](#if-you-have-an-azure-subscription) lub dla [użytkowników bez subskrypcji platformy Azure](#if-you-dont-have-an-azure-subscription).

Użyj poniższego fragmentu [interfejsu wiersza polecenia platformy Azure][cloud_shell] , aby wypełnić dwie zmienne środowiskowe za pomocą **punktu końcowego** konta przetwarzanie obrazów i jednego z jego **kluczy** (można również znaleźć te wartości w [Azure Portal][azure_portal]). Ten fragment kodu został sformatowany dla powłoki Bash.

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

Pobierz punkt końcowy i klucz ze zmiennych środowiskowych, a następnie Utwórz obiekt klienta [ComputerVisionClient][ref_computervisionclient] .

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

Musisz mieć obiekt klienta [ComputerVisionClient][ref_computervisionclient] przed użyciem któregoś z poniższych zadań.

### <a name="analyze-an-image"></a>Analizowanie obrazu

Obraz niektórych funkcji można analizować za pomocą [`analyze_image`][ref_computervisionclient_analyze_image]programu. [`visual_features`][ref_computervision_model_visualfeatures] Użyj właściwości, aby ustawić typy analiz do wykonania na obrazie. Typowe wartości to `VisualFeatureTypes.tags` i `VisualFeatureTypes.description`.

```Python
url = "https://upload.wikimedia.org/wikipedia/commons/thumb/1/12/Broadway_and_Times_Square_by_night.jpg/450px-Broadway_and_Times_Square_by_night.jpg"

image_analysis = client.analyze_image(url,visual_features=[VisualFeatureTypes.tags])

for tag in image_analysis.tags:
    print(tag)
```

### <a name="get-subject-domain-list"></a>Pobieranie listy domen tematycznych

Przejrzyj domeny tematu używane do analizowania obrazu za pomocą [`list_models`][ref_computervisionclient_list_models]programu. Te nazwy domen są używane podczas [analizowania obrazu według domeny](#analyze-an-image-by-domain). Przykładowa domena to `landmarks`.

```Python
models = client.list_models()

for x in models.models_property:
    print(x)
```

### <a name="analyze-an-image-by-domain"></a>Analizowanie obrazu według domeny

Obraz można analizować według domeny [`analyze_image_by_domain`][ref_computervisionclient_analyze_image_by_domain]podmiotu. Prawidłowe nazwy domen można znaleźć na [liście obsługiwanych domen tematycznych](#get-subject-domain-list).

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

Możesz uzyskać opis tekstowy obrazu z [`describe_image`][ref_computervisionclient_describe_image]. Przy użyciu właściwości `max_description` można pobrać kilka opisów w przypadku przeprowadzania analizy tekstu pod kątem słów kluczowych skojarzonych z obrazem. Przykładowe opisy tekstowe dla następującego obrazu to `a train crossing a bridge over a body of water`, `a large bridge over a body of water` i `a train crossing a bridge over a large body of water`.

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

Można wyodrębnić dowolny odręczny lub drukowany tekst znajdujący się na obrazie. Wymaga to dwóch wywołań zestawu SDK: [`batch_read_file`](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python) i. [`get_read_operation_result`](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python) Wywołanie `batch_read_file` jest asynchroniczne. W wynikach `get_read_operation_result` wywołania należy sprawdzić, czy pierwsze wywołanie zostało zakończone z [`TextOperationStatusCodes`][ref_computervision_model_textoperationstatuscodes] przed wyodrębnieniem danych tekstowych. Wyniki obejmują tekst oraz współrzędne pola ograniczenia tekstu.

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

Można wygenerować miniaturę (JPG) obrazu z [`generate_thumbnail`][ref_computervisionclient_generate_thumbnail]. Miniatura nie musi mieć takich samych proporcji co oryginalny obraz.

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

Podczas współdziałania z obiektem klienta [ComputerVisionClient][ref_computervisionclient] za pomocą zestawu SDK [`ComputerVisionErrorException`][ref_computervision_computervisionerrorexception] języka Python Klasa jest używana do zwracania błędów. Błędy zwracane przez tę usługę odpowiadają kodom stanu HTTP zwracanym dla żądań interfejsu API REST.

Na przykład jeśli spróbujesz przeprowadzić analizę obrazu za pomocą nieprawidłowego klucza, zostanie zwrócony błąd `401`. W poniższym fragmencie kodu [błąd][ref_httpfailure] jest obsługiwany bezpiecznie przez przechwycenie wyjątku i wyświetlenie dodatkowych informacji o błędzie.

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

Podczas pracy z klientem [ComputerVisionClient][ref_computervisionclient] mogą wystąpić błędy przejściowe wynikające z limitów [szybkości][computervision_request_units] wymuszonych przez usługę lub innych problemów przejściowych, takich jak awaria sieci. Aby uzyskać informacje na temat obsługi tych typów błędów, zobacz [wzorzec ponawiania][azure_pattern_retry] wzorców w przewodniku po wzorcach projektowania i pokrewny [wzorzec][azure_pattern_circuit_breaker]wyłącznika.

## <a name="next-steps"></a>Następne kroki

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
