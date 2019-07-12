---
title: Przewodnik rozwiązywania problemów z wdrażaniem
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak obejść, rozwiązania i rozwiązywanie typowych problemów wdrażania platformy Docker za pomocą usługi Azure Kubernetes Service i usługi Azure Container Instances za pomocą usługi Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: chris-lauren
ms.author: clauren
ms.reviewer: jmartens
ms.date: 07/09/2018
ms.custom: seodec18
ms.openlocfilehash: e0f4b024d717c08df3514df057abf89d55be1dc9
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707033"
---
# <a name="troubleshooting-azure-machine-learning-service-azure-kubernetes-service-and-azure-container-instances-deployment"></a>Rozwiązywanie problemów z wdrażaniem usługi Azure Kubernetes Service i usługi Azure Container Instances usługi Azure Machine Learning

Dowiedz się, jak obejść lub Rozwiązywanie typowych problemów wdrażania platformy Docker z usługi Azure Container Instances (ACI) i Azure Kubernetes Service (AKS), za pomocą usługi Azure Machine Learning.

W przypadku wdrażania modelu w usłudze Azure Machine Learning, system wykonuje wiele zadań. Dostępne są następujące zadania wdrażania:

1. W rejestrze modelu obszaru roboczego, należy zarejestrować model.

2. Zbuduj obraz Docker, w tym:
    1. Pobierz zarejestrowanego modelu z rejestru. 
    2. Utwórz plik dockerfile, za pomocą środowiska Python na podstawie zależności, określonych w pliku yaml środowiska.
    3. Dodawanie plików modelu, a skrypt oceniania, które należy podać w pliku dockerfile.
    4. Twórz nowy obraz platformy Docker przy użyciu pliku dockerfile.
    5. Zarejestruj obrazu platformy Docker za pomocą usługi Azure Container Registry, skojarzone z obszarem roboczym.

    > [!IMPORTANT]
    > W zależności od kodu Tworzenie obrazu realizowane automatycznie bez interwencji użytkownika.

3. Wdrażanie obrazu platformy Docker do usługi Azure Container wystąpienia (ACI) lub do usługi Azure Kubernetes Service (AKS).

4. Uruchom nowy kontener (lub kontenery) w usłudze ACI i AKS. 

Dowiedz się więcej na temat tego procesu w [zarządzania modelami](concept-model-management-and-deployment.md) wprowadzenie.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Jeśli napotkasz problem z dowolnym najpierw musisz jest podzielenie zadania wdrożenia (opisany poprzedniego) na poszczególne kroki, aby ustalić przyczynę problemu.

Podzielenie wdrożenia na zadania jest przydatne, jeśli używasz [Webservice.deploy()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#deploy-workspace--name--model-paths--image-config--deployment-config-none--deployment-target-none-) interfejsu API, lub [Webservice.deploy_from_model()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#deploy-from-model-workspace--name--models--image-config--deployment-config-none--deployment-target-none-) interfejsu API, oba te funkcje wykonaj kroki wymienione wyżej co jednej akcji. Zazwyczaj te interfejsy API są wygodne, ale warto podzielić kroki podczas rozwiązywania problemów, zastępując je poniżej wywołania interfejsu API.

1. Należy zarejestrować model. Poniżej przedstawiono przykładowy kod:

    ```python
    # register a model out of a run record
    model = best_run.register_model(model_name='my_best_model', model_path='outputs/my_model.pkl')

    # or, you can register a file or a folder of files as a model
    model = Model.register(model_path='my_model.pkl', model_name='my_best_model', workspace=ws)
    ```

2. Tworzenie obrazu. Poniżej przedstawiono przykładowy kod:

    ```python
    # configure the image
    image_config = ContainerImage.image_configuration(runtime="python",
                                                      entry_script="score.py",
                                                      conda_file="myenv.yml")

    # create the image
    image = Image.create(name='myimg', models=[model], image_config=image_config, workspace=ws)

    # wait for image creation to finish
    image.wait_for_creation(show_output=True)
    ```

3. Obraz, który można wdrożyć jako usługę. Poniżej przedstawiono przykładowy kod:

    ```python
    # configure an ACI-based deployment
    aci_config = AciWebservice.deploy_configuration(cpu_cores=1, memory_gb=1)

    aci_service = Webservice.deploy_from_image(deployment_config=aci_config, 
                                               image=image, 
                                               name='mysvc', 
                                               workspace=ws)
    aci_service.wait_for_deployment(show_output=True)    
    ```

Gdy zostały podzielone procesu wdrażania na poszczególne zadania, można przyjrzymy się niektóre z najbardziej typowych błędów.

## <a name="image-building-fails"></a>Obraz tworzenia kończy się niepowodzeniem

Jeśli nie można utworzyć obraz platformy Docker, [image.wait_for_creation()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.image(class)?view=azure-ml-py#wait-for-creation-show-output-false-) lub [service.wait_for_deployment()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#wait-for-deployment-show-output-false-) wywołanie zakończy się niektóre komunikaty o błędach, które może zaoferować pewne wskazówki. Można także znaleźć więcej szczegółowych informacji o błędach w dzienniku kompilacji obrazu. Poniżej przykładowy kod pokazuje sposób odnajdowania identyfikator uri dziennika kompilacji obrazu.

```python
# if you already have the image object handy
print(image.image_build_log_uri)

# if you only know the name of the image (note there might be multiple images with the same name but different version number)
print(ws.images['myimg'].image_build_log_uri)

# list logs for all images in the workspace
for name, img in ws.images.items():
    print (img.name, img.version, img.image_build_log_uri)
```

Identyfikator uri dziennika obrazu jest adres URL sygnatury dostępu Współdzielonego do pliku dziennika przechowywanych w usłudze Azure blob storage. Po prostu skopiuj i wklej identyfikator uri do okna przeglądarki i możesz pobrać i przejrzeć plik dziennika.

### <a name="azure-key-vault-access-policy-and-azure-resource-manager-templates"></a>Zasady dostępu w usłudze Azure Key Vault i szablonów usługi Azure Resource Manager

Tworzenie obrazu może również zakończyć się niepowodzeniem ze względu na problem z zasad dostępu w usłudze Azure Key Vault. Taka sytuacja może wystąpić, gdy używasz szablonu usługi Azure Resource Manager utworzyć obszar roboczy i skojarzonych zasobów (w tym usługi Azure Key Vault), a wiele razy. Na przykład przy użyciu szablonu wiele razy z tymi samymi parametrami w ramach ciągłej integracji i potoku wdrożenia.

Większość operacji tworzenia zasobów za pomocą szablonów są idempotentne, ale usługi Key Vault Czyści zasady dostępu w każdym razem, gdy jest używany szablon. Czyszczenie zasad podziały dostępu dostępu do usługi Key Vault do istniejącego obszaru roboczego, który jest używany przez. Ten problem powoduje błędy podczas próby utworzenia nowych obrazów. Poniżej przedstawiono przykłady błędów, które mogą odbierać:

__Portal__:
```text
Create image "myimage": An internal server error occurred. Please try again. If the problem persists, contact support.
```

__ZESTAW SDK__:
```python
image = ContainerImage.create(name = "myimage", models = [model], image_config = image_config, workspace = ws)
Creating image
Traceback (most recent call last):
  File "C:\Python37\lib\site-packages\azureml\core\image\image.py", line 341, in create
    resp.raise_for_status()
  File "C:\Python37\lib\site-packages\requests\models.py", line 940, in raise_for_status
    raise HTTPError(http_error_msg, response=self)
requests.exceptions.HTTPError: 500 Server Error: Internal Server Error for url: https://eastus.modelmanagement.azureml.net/api/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>/images?api-version=2018-11-19

Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "C:\Python37\lib\site-packages\azureml\core\image\image.py", line 346, in create
    'Content: {}'.format(resp.status_code, resp.headers, resp.content))
azureml.exceptions._azureml_exception.WebserviceException: Received bad response from Model Management Service:
Response Code: 500
Headers: {'Date': 'Tue, 26 Feb 2019 17:47:53 GMT', 'Content-Type': 'application/json', 'Transfer-Encoding': 'chunked', 'Connection': 'keep-alive', 'api-supported-versions': '2018-03-01-preview, 2018-11-19', 'x-ms-client-request-id': '3cdcf791f1214b9cbac93076ebfb5167', 'x-ms-client-session-id': '', 'Strict-Transport-Security': 'max-age=15724800; includeSubDomains; preload'}
Content: b'{"code":"InternalServerError","statusCode":500,"message":"An internal server error occurred. Please try again. If the problem persists, contact support"}'
```

__INTERFEJS WIERSZA POLECENIA__:
```text
ERROR: {'Azure-cli-ml Version': None, 'Error': WebserviceException('Received bad response from Model Management Service:\nResponse Code: 500\nHeaders: {\'Date\': \'Tue, 26 Feb 2019 17:34:05
GMT\', \'Content-Type\': \'application/json\', \'Transfer-Encoding\': \'chunked\', \'Connection\': \'keep-alive\', \'api-supported-versions\': \'2018-03-01-preview, 2018-11-19\', \'x-ms-client-request-id\':
\'bc89430916164412abe3d82acb1d1109\', \'x-ms-client-session-id\': \'\', \'Strict-Transport-Security\': \'max-age=15724800; includeSubDomains; preload\'}\nContent:
b\'{"code":"InternalServerError","statusCode":500,"message":"An internal server error occurred. Please try again. If the problem persists, contact support"}\'',)}
```

Aby uniknąć tego problemu, zaleca się jedną z następujących metod:

* Nie należy wdrażać szablonu więcej niż jeden raz dla tego samego parametrów. Przed lub usunąć istniejące zasoby przy użyciu szablonu, aby je ponownie utworzyć.
* Przejrzyj zasady dostępu magazynu kluczy, a następnie użyć tych zasad można ustawić `accessPolicies` właściwości szablonu.
* Sprawdź, czy zasobu usługi Key Vault już istnieje. Jeśli tak jest, nie twórz ponownie go za pomocą szablonu. Na przykład dodać parametr, który umożliwia wyłączenie tworzenia zasobu usługi Key Vault, jeśli już istnieje.

## <a name="debug-locally"></a>Debuguj lokalnie

Jeśli wystąpią problemy z modelem wdrażania usługi ACI i AKS, spróbuj wdrożyć go jako usługę internetową lokalnego. Przy użyciu usługi sieci web w lokalnych ułatwia rozwiązywanie problemów. Obraz platformy Docker zawierający model jest pobieranie i uruchamianie w systemie lokalnym.

> [!IMPORTANT]
> Wdrożeń usług internetowych lokalne wymagają działającą instalację platformy Docker w systemie lokalnym. Platformy docker musi być uruchomiony, przed wdrożeniem usługi sieci web w lokalnych. Aby uzyskać informacje na temat instalowania i za pomocą platformy Docker, zobacz [ https://www.docker.com/ ](https://www.docker.com/).

> [!WARNING]
> Wdrożeń usług internetowych lokalnych nie są obsługiwane na potrzeby scenariuszy produkcyjnych.

Aby wdrożyć lokalnie, należy zmodyfikować kodu, aby użyć `LocalWebservice.deploy_configuration()` umożliwia utworzenie konfiguracji wdrożenia. Następnie użyj `Model.deploy()` do wdrożenia usługi. Poniższy przykład służy do wdrażania modelu (zawarty w `model` zmiennej) jako usługę internetową lokalnego:

```python
from azureml.core.model import InferenceConfig,Model
from azureml.core.webservice import LocalWebservice

# Create inference configuration. This creates a docker image that contains the model.
inference_config = InferenceConfig(runtime= "python", 
                                   entry_script="score.py",
                                   conda_file="myenv.yml")

# Create a local deployment, using port 8890 for the web service endpoint
deployment_config = LocalWebservice.deploy_configuration(port=8890)
# Deploy the service
service = Model.deploy(ws, "mymodel", [model], inference_config, deployment_config)
# Wait for the deployment to complete
service.wait_for_deployment(True)
# Display the port that the web service is available on
print(service.port)
```

W tym momencie możesz pracować z usługą, jak zwykle. Na przykład poniższy kod ilustruje wysyłania danych do usługi:

```python
import json

test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10], 
    [10,9,8,7,6,5,4,3,2,1]
]})

test_sample = bytes(test_sample,encoding = 'utf8')

prediction = service.run(input_data=test_sample)
print(prediction)
```

### <a name="update-the-service"></a>Aktualizacja usługi

Podczas testowania lokalnym, użytkownik może być konieczne zaktualizowanie `score.py` plik, aby dodać rejestrowania lub spróbować rozwiązać problemy, które już znasz. Aby ponownie załadować zmiany `score.py` pliku, użyj `reload()`. Na przykład poniższy kod ponownie ładuje skryptu dla usługi, a następnie wysyła dane do niego. Dane są oceniane przy użyciu zaktualizowanego `score.py` pliku:

```python
service.reload()
print(service.run(input_data=test_sample))
```

> [!NOTE]
> Skrypt zostanie ponownie załadowana w lokalizacji określonej przez `InferenceConfig` obiekt używany przez usługę.

Aby zmienić model, zależności Conda lub konfiguracji wdrożenia, należy użyć [update()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#update--args-). Poniższy przykład aktualizuje model używany przez usługę:

```python
service.update([different_model], inference_config, deployment_config)
```

### <a name="delete-the-service"></a>Usuń usługę

Aby usunąć usługę, należy użyć [delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#delete--).

### <a id="dockerlog"></a> Sprawdź dziennik platformy Docker

Można wydrukować szczegółowe komunikaty dziennika aparat platformy Docker z obiektu usługi. Możesz wyświetlić dziennik usługi ACI, AKS i lokalnych wdrożeń. Poniższy przykład pokazuje, jak drukowanie w dziennikach.

```python
# if you already have the service object handy
print(service.get_logs())

# if you only know the name of the service (note there might be multiple services with the same name but different version number)
print(ws.webservices['mysvc'].get_logs())
```

## <a name="service-launch-fails"></a>Uruchomienie usługi nie powiedzie się.

Po pomyślnym utworzeniu obrazu, system próbuje uruchomić kontenera za pomocą konfiguracji wdrożenia. Jako część procesu rozruchu kontenera `init()` funkcji oceniania skryptu zostanie wywołany przez system. Jeśli istnieją nieobsłużone wyjątki w `init()` funkcji, można napotkać **CrashLoopBackOff** błąd w komunikacie o błędzie.

Użyj informacji w [Sprawdź w dzienniku Docker](#dockerlog) sekcji, aby zapoznać się z dziennikami.

## <a name="function-fails-getmodelpath"></a>Funkcja kończy się niepowodzeniem: get_model_path()

Często w `init()` funkcji w skrypt oceniania [Model.get_model_path()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-) funkcja jest wywoływana, aby zlokalizować plik modelu lub folder z plikami modelu w kontenerze. Nie można odnaleźć modelu pliku lub folderu, funkcja kończy się niepowodzeniem. Najprostszym sposobem, aby debugować ten błąd jest uruchomienie poniższego kodu języka Python w powłoce kontenera:

```python
import logging
logging.basicConfig(level=logging.DEBUG)
from azureml.core.model import Model
print(Model.get_model_path(model_name='my-best-model'))
```

W tym przykładzie drukuje się ścieżkę lokalną (względem `/var/azureml-app`) w kontenerze, gdzie skrypt oceniania oczekuje, aby znaleźć model pliku lub folderu. Następnie można sprawdzić, czy plik lub folder jest w rzeczywistości których oczekuje się, można.

Ustawienie poziomu rejestrowania debugowania może spowodować dodatkowe informacje mają być rejestrowane, które mogą okazać się przydatna podczas identyfikacji awarii.

## <a name="function-fails-runinputdata"></a>Funkcja kończy się niepowodzeniem: run(input_data)

Jeśli usługa została pomyślnie wdrożona, ale jej ulega awarii, gdy opublikujesz danych do punktu końcowego oceniania, można dodać błąd Przechwytywanie instrukcji w swojej `run(input_data)` funkcji tak, aby zamiast tego zwraca szczegółowy komunikat o błędzie. Na przykład:

```python
def run(input_data):
    try:
        data = json.loads(input_data)['data']
        data = np.array(data)
        result = model.predict(data)
        return json.dumps({"result": result.tolist()})
    except Exception as e:
        result = str(e)
        # return error message back to the client
        return json.dumps({"error": result})
```

**Uwaga**: Zwracanie komunikaty o błędach z `run(input_data)` wywołanie powinno się odbywać tylko do celów debugowania. Ze względów bezpieczeństwa należy nie powinny zwracać komunikaty o błędach w ten sposób w środowisku produkcyjnym.

## <a name="http-status-code-503"></a>Kod stanu HTTP 503

Wdrożeń usługi Kubernetes na platformie Azure obsługuje Skalowanie automatyczne, co pozwala repliki, aby dodać, aby obsługiwać dodatkowe obciążenie. Jednak skalowania automatycznego jest przeznaczona do obsługi **stopniowego** zmian obciążenia. Jeśli zostanie wyświetlony duże wartości szczytowe w żądaniach na sekundę, klienci mogą otrzymać kod stanu HTTP 503.

Istnieją dwie rzeczy, które może pomóc w uniknięciu 503 kodów stanu:

* Zmień poziom użycia, w których Skalowanie automatyczne utworzenie nowej repliki.
    
    Wykorzystanie docelowe skalowania automatycznego jest domyślnie do 70%, co oznacza, że usługa może obsługiwać skoków żądań na sekundę (RPS) do 30%. Można dostosować docelowej wykorzystania przez ustawienie `autoscale_target_utilization` niższą wartość.

    > [!IMPORTANT]
    > Ta zmiana nie powoduje, że repliki ma zostać utworzony *szybciej*. Zamiast tego są one tworzone zastosowaniem niższego progu wykorzystania. Zamiast czekać, aż do 70% wykorzystania usługi, zmieniając wartość do 30% powoduje, że repliki ma zostać utworzony, gdy wystąpi 30% wykorzystania.
    
    Jeśli usługa sieci web już używa bieżącej repliki max i jest nadal wyświetlany 503 kodów stanu, należy zwiększyć `autoscale_max_replicas` wartość, aby zwiększyć maksymalną liczbę replik.

* Zmień minimalną liczbę replik. Zwiększenie minimalne repliki zapewnia większą pulę do obsługi przychodzących skokom.

    Aby zwiększyć minimalną liczbę replik, ustaw `autoscale_min_replicas` wyższa wartość. Wymagane repliki można obliczyć przy użyciu następującego kodu, zastąpienie wartości określonej wartości do projektu:

    ```python
    from math import ceil
    # target requests per second
    targetRps = 20
    # time to process the request (in seconds)
    reqTime = 10
    # Maximum requests per container
    maxReqPerContainer = 1
    # target_utilization. 70% in this example
    targetUtilization = .7

    concurrentRequests = targetRps * reqTime / targetUtilization

    # Number of container replicas
    replicas = ceil(concurrentRequests / maxReqPerContainer)
    ```

    > [!NOTE]
    > Jeśli pojawi się żądanie wzrostów większych niż nowych minimalne replik może obsługiwać, możesz otrzymać kod 503 ponownie. Na przykład jako ruch do zwiększenia Twojej usługi, konieczne może być zwiększenia minimalna replik.

Aby uzyskać więcej informacji na temat ustawień dotyczących `autoscale_target_utilization`, `autoscale_max_replicas`, i `autoscale_min_replicas` , zobacz [AksWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py) odwołania do modułu.


## <a name="advanced-debugging"></a>Zaawansowane debugowanie

W niektórych przypadkach może być konieczne interaktywne debugowanie kodu języka Python, zawarte we wdrożeniu modelu. Na przykład, jeśli skrypt wejścia, kończy się niepowodzeniem i nie można ustalić przyczyny, logując się dodatkowe. Za pomocą programu Visual Studio Code i narzędzi Python Tools for Visual Studio (PTVSD), można dołączyć do kodu uruchomionej w kontenerze platformy Docker.

> [!IMPORTANT]
> Ta metoda debugowania nie działa, gdy za pomocą `Model.deploy()` i `LocalWebservice.deploy_configuration` wdrożyć model lokalnie. Zamiast tego należy utworzyć obraz przy użyciu [ContainerImage](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.containerimage?view=azure-ml-py) klasy. 
>
> Wdrożeń usług internetowych lokalne wymagają działającą instalację platformy Docker w systemie lokalnym. Platformy docker musi być uruchomiony, przed wdrożeniem usługi sieci web w lokalnych. Aby uzyskać informacje na temat instalowania i za pomocą platformy Docker, zobacz [ https://www.docker.com/ ](https://www.docker.com/).

### <a name="configure-development-environment"></a>Konfigurowanie środowiska programowania

1. Do na swojego lokalnego środowiska deweloperskiego programu VS Code, należy zainstalować narzędzia Python Tools for Visual Studio (PTVSD), użyj następującego polecenia:

    ```
    python -m pip install --upgrade ptvsd
    ```

    Aby uzyskać więcej informacji na temat używania PTVSD z programem VS Code, zobacz [zdalne debugowanie](https://code.visualstudio.com/docs/python/debugging#_remote-debugging).

1. Aby skonfigurować program VS Code do komunikowania się z obrazu platformy Docker, Utwórz nową konfigurację debugowania:

    1. W programie VS Code wybierz __debugowania__ menu, a następnie wybierz __Otwórz konfiguracje__. Plik o nazwie __launch.json__ zostanie otwarty.

    1. W __launch.json__ plików, znajdź wiersz, który zawiera `"configurations": [`i Wstaw następujący tekst po nim:

        ```json
        {
            "name": "Azure Machine Learning service: Docker Debug",
            "type": "python",
            "request": "attach",
            "port": 5678,
            "host": "localhost",
            "pathMappings": [
                {
                    "localRoot": "${workspaceFolder}",
                    "remoteRoot": "/var/azureml-app"
                }
            ]
        }
        ```

        > [!IMPORTANT]
        > Jeśli istnieją już inne wpisy w sekcji konfiguracji, należy dodać przecinek (,) po kodzie, który zostanie wstawiony.

        W tej sekcji dołącza do kontenera platformy Docker przy użyciu portu 5678.

    1. Zapisz __launch.json__ pliku.

### <a name="create-an-image-that-includes-ptvsd"></a>Tworzenie obrazu zawierającego PTVSD

1. Modyfikowanie środowiska conda dla danego wdrożenia, aby obejmowała PTVSD. W poniższym przykładzie pokazano, dodając go przy użyciu `pip_packages` parametru:

    ```python
    from azureml.core.conda_dependencies import CondaDependencies 
    
    # Usually a good idea to choose specific version numbers
    # so training is made on same packages as scoring
    myenv = CondaDependencies.create(conda_packages=['numpy==1.15.4',            
                                'scikit-learn==0.19.1', 'pandas==0.23.4'],
                                 pip_packages = ['azureml-defaults==1.0.17', 'ptvsd'])
    
    with open("myenv.yml","w") as f:
        f.write(myenv.serialize_to_string())
    ```

1. Aby rozpocząć PTVSD i oczekiwania na połączenie, podczas uruchamiania usługi, Dodaj następujący element do góry swoje `score.py` pliku:

    ```python
    import ptvsd
    # Allows other computers to attach to ptvsd on this IP address and port.
    ptvsd.enable_attach(address=('0.0.0.0', 5678), redirect_output = True)
    # Wait 30 seconds for a debugger to attach. If none attaches, the script continues as normal.
    ptvsd.wait_for_attach(timeout = 30)
    print("Debugger attached...")
    ```

1. Podczas debugowania, można wprowadzić zmiany do plików na obrazie bez konieczności ponownego tworzenia go. Aby zainstalować Edytor tekstu (vim) na obrazie platformy Docker, Utwórz nowy plik tekstowy o nazwie `Dockerfile.steps` i zawartość pliku należy zastosować następujące:

    ```text
    RUN apt-get update && apt-get -y install vim
    ```

    Edytor tekstu pozwala modyfikować pliki wewnątrz obrazu platformy docker, aby przetestować zmiany bez tworzenia nowego obrazu.

1. Aby utworzyć obraz, który używa `Dockerfile.steps` pliku, użyj `docker_file` parametru podczas tworzenia obrazu. Poniższy przykład pokazuje, jak to zrobić:

    > [!NOTE]
    > W tym przykładzie założono, że `ws` wskazuje obszaru roboczego usługi Azure Machine Learning, a `model` jest modelem wdrażania. `myenv.yml` Plik zawiera zależności conda, utworzony w kroku 1.

    ```python
    from azureml.core.image import Image, ContainerImage
    image_config = ContainerImage.image_configuration(runtime= "python",
                                 execution_script="score.py",
                                 conda_file="myenv.yml",
                                 docker_file="Dockerfile.steps")

    image = Image.create(name = "myimage",
                     models = [model],
                     image_config = image_config, 
                     workspace = ws)
    # Print the location of the image in the repository
    print(image.image_location)
    ```

Po utworzeniu obrazu jest wyświetlana Lokalizacja obrazu w rejestrze. Lokalizacja jest podobny do następującego tekstu:

```text
myregistry.azurecr.io/myimage:1
```

W tym przykładzie tekstu nazwa rejestru to `myregistry` i nosi nazwę obrazu `myimage`. Wersja obrazu jest `1`.

### <a name="download-the-image"></a>Pobranie obrazu

1. Otwórz wiersz polecenia, program terminal lub innego powłoki i należy użyć następującego [wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) polecenie w celu uwierzytelniania z subskrypcją platformy Azure, która zawiera obszar roboczy usługi Azure Machine Learning:

    ```azurecli
    az login
    ```

1. Aby uwierzytelniać się do usługi Azure Container Registry (ACR), który zawiera obraz, użyj następującego polecenia. Zastąp `myregistry` zwracane, jeśli jeden zarejestrowany obrazu:

    ```azurecli
    az acr login --name myregistry
    ```

1. Pobranie obrazu do usługi lokalnej platformy Docker, użyj następującego polecenia. Zastąp `myimagepath` z lokalizacji, zwracane, jeśli jest zarejestrowany obrazu:

    ```bash
    docker pull myimagepath
    ```

    Ścieżka obrazu powinny być podobne do `myregistry.azurecr.io/myimage:1`. Gdzie `myregistry` jest rejestru, `myimage` jest obraz, i `1` jest wersję obrazu.

    > [!TIP]
    > Uwierzytelnianie w poprzednim kroku nie wystarczą nieskończoność. Jeśli czekasz wystarczająco długo między polecenia uwierzytelniania i ściągnięcia, zostanie wyświetlony wystąpił błąd uwierzytelniania. W takim przypadku ponownie uwierzytelniać.

    Czas potrzebny na ukończenie pobierania, zależy od prędkości połączenia internetowego. Stan pobierania jest wyświetlany podczas procesu. Po zakończeniu pobierania, możesz użyć `docker images` polecenie, aby sprawdzić, czy została pobrana.

1. Aby ułatwić pracy przy użyciu obrazu, użyj następującego polecenia, aby dodać tag. Zastąp `myimagepath` wartością lokalizacji w kroku 2.

    ```bash
    docker tag myimagepath debug:1
    ```

    Pozostałe kroki mogą odwoływać się do lokalnego obrazu jako `debug:1` zamiast wartość ścieżki pełnego obrazu.

### <a name="debug-the-service"></a>Debugowanie usługi

> [!TIP]
> Jeśli musisz ustawić limit czasu połączenia PTVSD w `score.py` pliku, należy połączyć program VS Code sesji debugowania, zanim upłynie limit czasu. Uruchom program VS Code, otwórz lokalną kopię `score.py`, ustaw punkt przerwania i jest gotowa do użytku przed rozpoczęciem korzystania z instrukcjami w tej sekcji.
>
> Aby uzyskać więcej informacji na temat debugowania i ustawiania punktów przerwania, zobacz [debugowanie](https://code.visualstudio.com/Docs/editor/debugging).

1. Aby rozpocząć korzystając z obrazu kontenera platformy Docker, użyj następującego polecenia:

    ```bash
    docker run --rm --name debug -p 8000:5001 -p 5678:5678 debug:1
    ```

1. Aby dołączyć program VS Code PTVSD wewnątrz kontenera, Otwórz program VS Code i Użyj F5 klucza lub wybierz __debugowania__. Po wyświetleniu monitu wybierz __usługi Azure Machine Learning: Debugowanie platformy docker__ konfiguracji. Możesz również wybrać ikonę debugowania, w pasku bocznym __usługi Azure Machine Learning: Debugowanie platformy docker__ wpis z menu rozwijanego debugowania, a następnie użyj zieloną strzałkę, aby dołączyć debuger.

    ![Ikona Debuguj debugowania przycisk start i selektor konfiguracji](media/how-to-troubleshoot-deployment/start-debugging.png)

W tym momencie program VS Code łączy się PTVSD w kontenerze platformy Docker i zatrzymuje się w punkcie przerwania, które wcześniej określono. Możesz teraz przejść przez kod po jej uruchomieniu wyświetlać, zmienne itp.

Aby uzyskać więcej informacji na temat korzystania z programu VS Code w celu debugowania języka Python, zobacz [debugowanie kodu w języku Python](https://docs.microsoft.com/visualstudio/python/debugging-python-in-visual-studio?view=vs-2019).

<a id="editfiles"></a>
### <a name="modify-the-container-files"></a>Modyfikowanie plików kontenera

Aby wprowadzić zmiany do plików na ilustracji, można dołączyć do uruchomionego kontenera i wykonaj powłokę bash. Z tego miejsca vim służy do edytowania plików:

1. Aby nawiązać połączenie z działającym kontenerem i uruchom powłokę bash w kontenerze, użyj następującego polecenia:

    ```bash
    docker exec -it debug /bin/bash
    ```

1. Aby znaleźć pliki używane przez usługę, użyj następującego polecenia z poziomu powłoki bash w kontenerze:

    ```bash
    cd /var/azureml-app
    ```

    W tym miejscu możesz użyć do edycji vim `score.py` pliku. Aby uzyskać więcej informacji na temat korzystania z vim, zobacz [za pomocą edytora Vim](https://www.tldp.org/LDP/intro-linux/html/sect_06_02.html).

1. Zmiany do kontenera nie są zwykle zachowywane. Aby zapisać wprowadzonych zmian, użyj następującego polecenia, zanim Wyjdź z powłoki pracę w poprzednim kroku (oznacza to, w powłoce innego):

    ```bash
    docker commit debug debug:2
    ```

    To polecenie umożliwia utworzenie nowego obrazu o nazwie `debug:2` zawierający edycji.

    > [!TIP]
    > Należy zatrzymać bieżącego kontenera i uruchomić przy użyciu nowej wersji, aby zmiany zaczęły obowiązywać.

1. Upewnij się zachować zmiany wprowadzone do plików w kontenerze w synchronizacji z lokalnymi plikami, które korzysta z programu VS Code. W przeciwnym razie środowisko debugera nie będzie działać zgodnie z oczekiwaniami.

### <a name="stop-the-container"></a>Zastavit kontejner

Aby zatrzymać kontener, użyj następującego polecenia:

```bash
docker stop debug
```

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o wdrażaniu:

* [Jak wdrażać i którym](how-to-deploy-and-where.md)
* [Samouczek: Uczenie i wdrażanie modeli](tutorial-train-models-with-aml.md)
