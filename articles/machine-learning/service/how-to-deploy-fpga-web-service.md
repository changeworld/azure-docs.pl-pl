---
title: Wdrażanie modelu jako usługi sieci web na FPGA za pomocą usługi Azure Machine Learning
description: Dowiedz się, jak wdrożyć usługę sieci web przy użyciu modelu z systemem FPGA za pomocą usługi Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: tedway
author: tedway
ms.date: 10/01/2018
ms.openlocfilehash: df6637f1a52b679ba9ad0a49fb37d4e4b72f35e4
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/03/2018
ms.locfileid: "48237827"
---
# <a name="deploy-a-model-as-a-web-service-on-an-fpga-with-azure-machine-learning"></a>Wdrażanie modelu jako usługi sieci web na FPGA za pomocą usługi Azure Machine Learning

Model można wdrożyć jako usługę sieci web, na [pola Tablice bramek programowane (układów FPGA)](concept-accelerate-with-fpgas.md).  Za pomocą układów FPGA zapewnia wnioskowania niezwykle małych opóźnień, nawet w przypadku rozmiar jednej partii.   

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- Należy zażądać i zatwierdzić FPGA limitu przydziału. Aby zażądać dostępu, wypełnij formularz żądania limitu przydziału: https://aka.ms/aml-real-time-ai

- Obszar roboczy usługi Azure Machine Learning i Azure Machine Learning SDK dla język Python jest zainstalowany. Dowiedz się, jak uzyskać te wymagania wstępne przy użyciu [sposób konfigurowania środowiska deweloperskiego](how-to-configure-environment.md) dokumentu.
 
  - Obszar roboczy musi być zapisana w *wschodnie stany USA 2* regionu.

  - Zainstaluj dodatkowe contrib:

    ```shell
    pip install --upgrade azureml-sdk[contrib]
    ```  

## <a name="create-and-deploy-your-model"></a>Tworzenie i wdrażanie modelu
Tworzenie potoku w celu wstępnego przetworzenia obrazu wejściowego cechowanie go za pomocą siecią ResNet-50 na FPGA, a następnie uruchom funkcji, za pośrednictwem classifer skonfigurowanych pod kątem w zestawie danych sieci ImageNet.

Postępuj zgodnie z instrukcjami, aby:

* Zdefiniuj potoku modelu
* Wdrażanie modelu
* Używanie wdrożonego modelu
* Usuwanie wdrożonych usług

> [!IMPORTANT]
> W celu zoptymalizowania opóźnienia i przepływności, klienta należy w tym samym regionie platformy Azure jako punktu końcowego.  Obecnie interfejsy API są tworzone w regionie wschodnie stany USA Azure.



### <a name="preprocess-image"></a>Wstępne przetwarzanie obrazu
Pierwszy etap potoku jest przetwarzanie wstępne obrazów.

```python
import os
import tensorflow as tf

# Input images as a two-dimensional tensor containing an arbitrary number of images represented a strings
import azureml.contrib.brainwave.models.utils as utils
in_images = tf.placeholder(tf.string)
image_tensors = utils.preprocess_array(in_images)
print(image_tensors.shape)
```

### <a name="add-featurizer"></a>Dodaj Featurized
Inicjowanie modelu i Pobierz punkt kontrolny TensorFlow wykonywanie kwantyzowanych wersji ResNet50 ma być używany jako featurized.

```python
from azureml.contrib.brainwave.models import QuantizedResnet50, Resnet50
model_path = os.path.expanduser('~/models')
model = QuantizedResnet50(model_path, is_frozen = True)
feature_tensor = model.import_graph_def(image_tensors)
print(model.version)
print(feature_tensor.name)
print(feature_tensor.shape)
```

### <a name="add-classifier"></a>Dodaj klasyfikatora
Po zapoznaniu to klasyfikatora na zestawie danych sieci ImageNet.

```python
classifier_input, classifier_output = Resnet50.get_default_classifier(feature_tensor, model_path)
```

### <a name="create-service-definition"></a>Tworzenie definicji usługi
Teraz, gdy masz zdefiniowanego wstępnego przetwarzania obrazu, featurized i klasyfikatora, który jest uruchamiany w usłudze, można utworzyć definicji usługi. Definicja usługi to zbiór plików wygenerowany na podstawie modelu, który jest wdrożony w usłudze FPGA. Definicja usługi składa się z potoku. Potok jest serię etapów, które są uruchamiane w kolejności.  Etapy TensorFlow, Keras etapów i etapy BrainWave są obsługiwane.  Etapy są uruchamiane w kolejności od usługi, z danymi wyjściowymi wprowadzania danych wejściowych każdego etapu w kolejnym etapie.

Aby utworzyć etapu TensorFlow, określ sesję zawierający wykres (w tym przypadku wykres domyślne są używane) i dane wejściowe i wyjściowe tensors na tym etapie.  Te informacje jest używany, aby zapisać wykres, dzięki czemu mogą być uruchamiane w usłudze.

```python
from azureml.contrib.brainwave.pipeline import ModelDefinition, TensorflowStage, BrainWaveStage

save_path = os.path.expanduser('~/models/save')
model_def_path = os.path.join(save_path, 'service_def.zip')

model_def = ModelDefinition()
with tf.Session() as sess:
    model_def.pipeline.append(TensorflowStage(sess, in_images, image_tensors))
    model_def.pipeline.append(BrainWaveStage(sess, model))
    model_def.pipeline.append(TensorflowStage(sess, classifier_input, classifier_output))
    model_def.save(model_def_path)
    print(model_def_path)
```

### <a name="deploy-model"></a>Wdrażanie modelu
Tworzenie usługi na podstawie definicji usługi.  Obszar roboczy musi znajdować się w lokalizacji wschodnie stany USA 2.

```python
from azureml.core import Workspace

ws = Workspace.from_config()
print(ws.name, ws.resource_group, ws.location, ws.subscription_id, sep = '\n')

from azureml.core.model import Model
model_name = "resnet-50-rtai"
registered_model = Model.register(ws, model_def_path, model_name)

from azureml.core.webservice import Webservice
from azureml.exceptions import WebserviceException
from azureml.contrib.brainwave import BrainwaveWebservice, BrainwaveImage
service_name = "imagenet-infer"
service = None
try:
    service = Webservice(ws, service_name)
except WebserviceException:
    image_config = BrainwaveImage.image_configuration()
    deployment_config = BrainwaveWebservice.deploy_configuration()
    service = Webservice.deploy_from_model(ws, service_name, [registered_model], image_config, deployment_config)
    service.wait_for_deployment(true)
```

### <a name="test-the-service"></a>Testowanie usługi
Wysyłanie obrazu do interfejsu API i testowanie odpowiedzi, należy dodać mapowanie z Identyfikatorem klasy danych wyjściowych do sieci ImageNet nazwy klasy.

```python
import requests
classes_entries = requests.get("https://raw.githubusercontent.com/Lasagne/Recipes/master/examples/resnet50/imagenet_classes.txt").text.splitlines()
```

Wywołanie usługi i zastąp nazwę pliku "your image.jpg" poniżej obrazu z Twojego komputera. 

```python
with open('your-image.jpg') as f:
    results = service.run(f)
# map results [class_id] => [confidence]
results = enumerate(results)
# sort results by confidence
sorted_results = sorted(results, key=lambda x: x[1], reverse=True)
# print top 5 results
for top in sorted_results[:5]:
    print(classes_entries[top[0]], 'confidence:', top[1])
``` 

### <a name="clean-up-service"></a>Czyszczenie usługi
Usuń usługę.

```python
service.delete()
    
registered_model.delete()
```

## <a name="secure-fpga-web-services"></a>Zabezpieczania usług sieci web FPGA

Azure modeli usługi Machine Learning systemem układów FPGA zapewniają obsługę protokołu SSL i uwierzytelniania opartego na kluczu. Dzięki temu można ograniczyć dostęp do swojej usługi i zabezpieczanie danych przesyłanych przez klientów.

> [!IMPORTANT]
> Uwierzytelnianie jest włączona tylko dla usług, które zostały podane certyfikat i klucz. 
>
> Jeśli protokół SSL nie jest włączona, każdy użytkownik w Internecie będzie do nawiązywania połączeń z usługą.
>
> Po włączeniu protokołu SSL i klucz uwierzytelniania jest wymagany podczas uzyskiwania dostępu do usługi.

Protokół SSL, są szyfrowane dane przesyłane między klientem a usługą. On również używany przez klienta, aby zweryfikować tożsamość serwera.

Wdrażanie usługi z włączonym protokołem SSL lub zaktualizować już wdrożonej usługi, aby ją włączyć. Kroki są takie same:

1. Uzyskuje nazwę domeny.

2. Uzyskaj certyfikat protokołu SSL.

3. Wdrażanie lub zaktualizuj usługę z włączonym protokołem SSL.

4. Zaktualizuj serwer DNS, aby wskazać usługę.

### <a name="acquire-a-domain-name"></a>Uzyskiwanie nazwy domeny

Jeśli nie jesteś już właścicielem nazwy domeny, możesz kupić jeden z __rejestratora nazw domen__. Ten proces różni się między rejestratorów, tak jak koszt. Rejestrator również udostępnia narzędzia do zarządzania nazwy domeny. Te narzędzia są używane do mapowania w pełni kwalifikowaną nazwę domeny (np. www.contoso.com) do usługi jest hostowana na adres IP.

### <a name="acquire-an-ssl-certificate"></a>Uzyskaj certyfikat protokołu SSL

Istnieje wiele sposobów, aby uzyskać certyfikat SSL. Najczęściej jest nabyć jeden z __urzędu certyfikacji__ (CA). Niezależnie od tego, gdzie można uzyskać certyfikat potrzebne są następujące pliki:

* A __certyfikatu__. Certyfikat musi zawierać łańcucha certyfikatów pełnego i musi być zakodowany w formacie PEM.
* A __klucz__. Klucz musi być zakodowany w formacie PEM.

> [!TIP]
> Jeśli urząd certyfikacji nie może dostarczyć certyfikat i klucz jako pliki zakodowane w formacie PEM, możesz użyć narzędzia takie jak [OpenSSL](https://www.openssl.org/) zmian w formacie.

> [!IMPORTANT]
> Certyfikaty z podpisem własnym należy używać tylko w celach deweloperskich. Nie powinny one być używane w środowisku produkcyjnym.
>
> Jeśli używasz certyfikatu z podpisem własnym, zobacz [korzystanie z usług z certyfikatami z podpisem własnym](#self-signed) sekcji, aby uzyskać szczegółowe instrukcje.

> [!WARNING]
> Podczas żądania certyfikatu, podaj w pełni kwalifikowana nazwa domeny (FQDN) adresu, który ma być używany dla usługi. Na przykład www.contoso.com. Adres, który został oznaczony do certyfikatu i adres używany przez klientów, są porównywane podczas weryfikowania tożsamości usługi.
>
> Jeśli adresy różnią się od siebie, klienci spowoduje wystąpienie błędu. 

### <a name="deploy-or-update-the-service-with-ssl-enabled"></a>Wdrożeniem lub aktualizację usługi z włączonym protokołem SSL

Aby wdrożyć usługę z włączonym protokołem SSL, ustaw `ssl_enabled` parametr `True`. Ustaw `ssl_certificate` parametru na wartość __certyfikatu__ pliku i `ssl_key` wartość __klucz__ pliku. W poniższym przykładzie pokazano wdrażanie usługi z włączonym protokołem SSL:

```python
from amlrealtimeai import DeploymentClient

subscription_id = "<Your Azure Subscription ID>"
resource_group = "<Your Azure Resource Group Name>"
model_management_account = "<Your AzureML Model Management Account Name>"
location = "eastus2"

model_name = "resnet50-model"
service_name = "quickstart-service"

deployment_client = DeploymentClient(subscription_id, resource_group, model_management_account, location)

with open('cert.pem','r') as cert_file:
    with open('key.pem','r') as key_file:
        cert = cert_file.read()
        key = key_file.read()
        service = deployment_client.create_service(service_name, model_id, ssl_enabled=True, ssl_certificate=cert, ssl_key=key)
```

Odpowiedź `create_service` operacja zawiera adres IP usługi. Adres IP jest używany, gdy mapowanie nazwy DNS na adres IP usługi.

Odpowiedź zawiera również __klucza podstawowego__ i __klucz pomocniczy__ służące do korzystania z usługi.

### <a name="update-your-dns-to-point-to-the-service"></a>Zaktualizuj serwer DNS, aby wskazać usługę

Użyj narzędzi dostarczanych przez rejestratora nazw domen, aby zaktualizować rekord DNS dla nazwy domeny. Rekord musi wskazywać na adres IP usługi.

> [!NOTE]
> W zależności od Rejestrator i czas wygaśnięcia (TTL) skonfigurowany dla nazwy domeny, może upłynąć kilka minut do kilku godzin, zanim klienci mogą rozpoznać nazwę domeny.

### <a name="consume-authenticated-services"></a>Używanie usług uwierzytelnionego

W poniższych przykładach pokazano, jak używać usługi uwierzytelnionego przy użyciu języka Python i C#:

> [!NOTE]
> Zastąp `authkey` kluczem podstawowym lub pomocniczym zwrócony podczas tworzenia usługi.

```python
from amlrealtimeai import PredictionClient
client = PredictionClient(service.ipAddress, service.port, use_ssl=True, access_token="authKey")
image_file = R'C:\path_to_file\image.jpg'
results = client.score_image(image_file)
```

```csharp
var client = new ScoringClient(host, 50051, useSSL, "authKey");
float[,] result;
using (var content = File.OpenRead(image))
    {
        IScoringRequest request = new ImageRequest(content);
        result = client.Score<float[,]>(request);
    }
```

Inni klienci gRPC można uwierzytelnić żądania, ustawiając nagłówka autoryzacji. Ogólne podejście jest utworzenie `ChannelCredentials` obiekt, który łączy `SslCredentials` z `CallCredentials`. To jest dodawany do nagłówka autoryzacji żądania. Aby uzyskać więcej informacji dotyczących implementowania pomocy technicznej dla Twojego określonych nagłówków, zobacz [ https://grpc.io/docs/guides/auth.html ](https://grpc.io/docs/guides/auth.html).

W poniższych przykładach pokazano, jak ustawić nagłówek w języku C# i przejdź do sekcji:

```csharp
creds = ChannelCredentials.Create(baseCreds, CallCredentials.FromInterceptor(
                      async (context, metadata) =>
                      {
                          metadata.Add(new Metadata.Entry("authorization", "authKey"));
                          await Task.CompletedTask;
                      }));

```

```go
conn, err := grpc.Dial(serverAddr, 
    grpc.WithTransportCredentials(credentials.NewClientTLSFromCert(nil, "")),
    grpc.WithPerRPCCredentials(&authCreds{
    Key: "authKey"}))

type authCreds struct {
    Key string
}

func (c *authCreds) GetRequestMetadata(context.Context, uri ...string) (map[string]string, error) {
    return map[string]string{
        "authorization": c.Key,
    }, nil
}

func (c *authCreds) RequireTransportSecurity() bool {
    return true
}
```

### <a id="self-signed"></a>Korzystanie z usług z certyfikatami z podpisem własnym

Istnieją dwa sposoby, aby umożliwić klientowi uwierzytelniać się na serwerze, zabezpieczonego przy użyciu certyfikatu z podpisem własnym:

* W systemie klienta ustaw `GRPC_DEFAULT_SSL_ROOTS_FILE_PATH` zmiennej środowiskowej, aby wskazać plik certyfikatu w systemie klienta.

* Podczas tworzenia `SslCredentials` obiektów, Przekaż zawartość pliku certyfikatu do konstruktora.

Przy użyciu jednej z metod powoduje, że gRPC użyć certyfikatu jako certyfikatu głównego.

> [!IMPORTANT]
> gRPC Akceptuj niezaufane certyfikaty. Za pomocą niezaufanego certyfikatu zakończy się niepowodzeniem z `Unavailable` kod stanu. Szczegóły dotyczące błędu zawierają `Connection Failed`.

## <a name="sample-notebook"></a>Przykładowy notes

Koncepcji w tym artykule przedstawiono w `project-brainwave/project-brainwave-quickstart.ipynb` notesu.

Pobierz ten notes:

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]
