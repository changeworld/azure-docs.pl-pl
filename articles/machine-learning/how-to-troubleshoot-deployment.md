---
title: Przewodnik rozwiązywania problemów z wdrażaniem
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak obejść, rozwiązać i rozwiązać typowe błędy wdrażania platformy Docker za pomocą usługi Azure Kubernetes i Azure Container Instances przy użyciu Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: chris-lauren
ms.author: clauren
ms.reviewer: jmartens
ms.date: 10/25/2019
ms.custom: seodec18
ms.openlocfilehash: f9361f1ca998d32a998794a7e95220ee5c7ac623
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/10/2020
ms.locfileid: "75834779"
---
# <a name="troubleshooting-azure-machine-learning-azure-kubernetes-service-and-azure-container-instances-deployment"></a>Rozwiązywanie problemów Azure Machine Learning usługi Azure Kubernetes i wdrożenia Azure Container Instances

Dowiedz się, jak obejść typowe błędy wdrażania platformy Docker i rozwiązać je za pomocą usług Azure Container Instances (ACI) i Azure Kubernetes Service (AKS) przy użyciu Azure Machine Learning.

W przypadku wdrażania modelu w Azure Machine Learning system wykonuje wiele zadań. Dostępne są następujące zadania wdrażania:

1. W rejestrze modelu obszaru roboczego, należy zarejestrować model.

2. Zbuduj obraz Docker, w tym:
    1. Pobierz zarejestrowanego modelu z rejestru. 
    2. Utwórz plik dockerfile, za pomocą środowiska Python na podstawie zależności, określonych w pliku yaml środowiska.
    3. Dodawanie plików modelu, a skrypt oceniania, które należy podać w pliku dockerfile.
    4. Twórz nowy obraz platformy Docker przy użyciu pliku dockerfile.
    5. Zarejestruj obrazu platformy Docker za pomocą usługi Azure Container Registry, skojarzone z obszarem roboczym.

    > [!IMPORTANT]
    > W zależności od kodu Tworzenie obrazu odbywa się automatycznie bez wprowadzania danych przez użytkownika.

3. Wdrażanie obrazu platformy Docker do usługi Azure Container wystąpienia (ACI) lub do usługi Azure Kubernetes Service (AKS).

4. Uruchom nowy kontener (lub kontenery) w usłudze ACI i AKS. 

Dowiedz się więcej na temat tego procesu w [zarządzania modelami](concept-model-management-and-deployment.md) wprowadzenie.

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcji platformy Azure**. Jeśli go nie masz, wypróbuj [bezpłatną lub płatną wersję Azure Machine Learning](https://aka.ms/AMLFree).
* [Zestaw SDK Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
* [Wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
* [Rozszerzenie interfejsu wiersza polecenia dla Azure Machine Learning](reference-azure-machine-learning-cli.md).
* Aby debugować lokalnie, musisz mieć działającą instalację platformy Docker w systemie lokalnym.

    Aby zweryfikować instalację platformy Docker, użyj polecenia `docker run hello-world` z terminalu lub wiersza polecenia. Informacje dotyczące instalowania platformy Docker lub rozwiązywania problemów z błędami platformy Docker znajdują się w [dokumentacji platformy Docker](https://docs.docker.com/).

## <a name="before-you-begin"></a>Przed rozpoczęciem

Jeśli napotkasz problem z dowolnym najpierw musisz jest podzielenie zadania wdrożenia (opisany poprzedniego) na poszczególne kroki, aby ustalić przyczynę problemu.

Rozdzielenie wdrożenia na zadania jest przydatne, jeśli używasz interfejsu API usługi [WebService. deploy ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#deploy-workspace--name--model-paths--image-config--deployment-config-none--deployment-target-none--overwrite-false-) lub usługi [WebService. deploy_from_model ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#deploy-from-model-workspace--name--models--image-config--deployment-config-none--deployment-target-none--overwrite-false-) , ponieważ obie te funkcje wykonują wyżej wymienione kroki jako pojedynczą akcję. Zwykle te interfejsy API są wygodne, ale ułatwiają rozbicie czynności podczas rozwiązywania problemów przez zastąpienie ich przy użyciu poniższych wywołań interfejsu API.

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

Jeśli nie można skompilować obrazu platformy Docker, wywołanie [Image. wait_for_creation ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.image(class)?view=azure-ml-py#wait-for-creation-show-output-false-) lub [Service. wait_for_deployment ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#wait-for-deployment-show-output-false-) zakończy się niepowodzeniem z komunikatami o błędach, które mogą oferować pewne wskazówki. Można także znaleźć więcej szczegółowych informacji o błędach w dzienniku kompilacji obrazu. Poniżej przykładowy kod pokazuje sposób odnajdowania identyfikator uri dziennika kompilacji obrazu.

```python
# if you already have the image object handy
print(image.image_build_log_uri)

# if you only know the name of the image (note there might be multiple images with the same name but different version number)
print(ws.images['myimg'].image_build_log_uri)

# list logs for all images in the workspace
for name, img in ws.images.items():
    print(img.name, img.version, img.image_build_log_uri)
```

Identyfikator uri dziennika obrazu jest adres URL sygnatury dostępu Współdzielonego do pliku dziennika przechowywanych w usłudze Azure blob storage. Po prostu skopiuj i wklej identyfikator uri do okna przeglądarki i możesz pobrać i przejrzeć plik dziennika.

### <a name="azure-key-vault-access-policy-and-azure-resource-manager-templates"></a>Azure Key Vault zasad dostępu i szablonów Azure Resource Manager

Kompilacja obrazu może również zakończyć się niepowodzeniem z powodu problemu z zasadami dostępu dla Azure Key Vault. Ta sytuacja może wystąpić, gdy używasz szablonu Azure Resource Manager do tworzenia obszaru roboczego i skojarzonych zasobów (w tym Azure Key Vault), wiele razy. Przykładowo wielokrotne użycie szablonu z tymi samymi parametrami w ramach potoku ciągłej integracji i wdrażania.

Większość operacji tworzenia zasobów za pomocą szablonów to idempotentne, ale Key Vault czyści zasady dostępu za każdym razem, gdy szablon jest używany. Wyczyszczenie zasad dostępu powoduje przerwanie dostępu do Key Vault wszystkich istniejących obszarów roboczych, które go używają. Ten warunek powoduje błędy podczas próby utworzenia nowych obrazów. Poniżej przedstawiono przykłady błędów, które można odbierać:

__Portal__:
```text
Create image "myimage": An internal server error occurred. Please try again. If the problem persists, contact support.
```

__Zestaw SDK__:
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

__Interfejs wiersza polecenia__:
```text
ERROR: {'Azure-cli-ml Version': None, 'Error': WebserviceException('Received bad response from Model Management Service:\nResponse Code: 500\nHeaders: {\'Date\': \'Tue, 26 Feb 2019 17:34:05
GMT\', \'Content-Type\': \'application/json\', \'Transfer-Encoding\': \'chunked\', \'Connection\': \'keep-alive\', \'api-supported-versions\': \'2018-03-01-preview, 2018-11-19\', \'x-ms-client-request-id\':
\'bc89430916164412abe3d82acb1d1109\', \'x-ms-client-session-id\': \'\', \'Strict-Transport-Security\': \'max-age=15724800; includeSubDomains; preload\'}\nContent:
b\'{"code":"InternalServerError","statusCode":500,"message":"An internal server error occurred. Please try again. If the problem persists, contact support"}\'',)}
```

Aby uniknąć tego problemu, zalecamy zastosowanie jednej z następujących metod:

* Nie Wdrażaj szablonu więcej niż raz dla tych samych parametrów. Lub Usuń istniejące zasoby przed użyciem szablonu, aby utworzyć je ponownie.
* Przejrzyj zasady dostępu Key Vault a następnie użyj tych zasad, aby ustawić właściwość `accessPolicies` szablonu.
* Sprawdź, czy zasób Key Vault już istnieje. Jeśli tak, nie należy go ponownie tworzyć za pomocą szablonu. Na przykład Dodaj parametr, który umożliwia wyłączenie tworzenia zasobu Key Vault, jeśli już istnieje.

## <a name="debug-locally"></a>Debuguj lokalnie

Jeśli wystąpią problemy ze wdrożeniem modelu do ACI lub AKS, spróbuj wdrożyć go jako lokalną usługę sieci Web. Korzystanie z lokalnej usługi sieci Web ułatwia rozwiązywanie problemów. Obraz platformy Docker zawierający model zostanie pobrany i uruchomiony w systemie lokalnym.

> [!WARNING]
> Lokalne wdrożenia usługi sieci Web nie są obsługiwane w scenariuszach produkcyjnych.

Aby wdrożyć lokalnie, zmodyfikuj swój kod, aby użyć `LocalWebservice.deploy_configuration()` do utworzenia konfiguracji wdrożenia. Następnie użyj `Model.deploy()`, aby wdrożyć usługę. W poniższym przykładzie jest wdrażany model (zawarty w zmiennej `model`) jako lokalna usługa sieci Web:

```python
from azureml.core.model import InferenceConfig, Model
from azureml.core.environment import Environment
from azureml.core.webservice import LocalWebservice

# Create inference configuration based on the environment definition and the entry script
myenv = Environment.from_conda_specification(name="env", file_path="myenv.yml")
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)

# Create a local deployment, using port 8890 for the web service endpoint
deployment_config = LocalWebservice.deploy_configuration(port=8890)
# Deploy the service
service = Model.deploy(
    ws, "mymodel", [model], inference_config, deployment_config)
# Wait for the deployment to complete
service.wait_for_deployment(True)
# Display the port that the web service is available on
print(service.port)
```

Należy pamiętać, że jeśli definiujesz własną Conda specyfikację YAML, musisz wyświetlić listę opcji "Azure-Defaults" z wersją > = 1.0.45 jako zależność PIP. Ten pakiet zawiera funkcje, które są konieczne do hostowania modelu jako usługi sieci Web.

W tym momencie można korzystać z usługi w zwykły sposób. Na przykład poniższy kod ilustruje wysyłanie danych do usługi:

```python
import json

test_sample = json.dumps({'data': [
    [1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
    [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]
]})

test_sample = bytes(test_sample, encoding='utf8')

prediction = service.run(input_data=test_sample)
print(prediction)
```

Aby uzyskać więcej informacji na temat dostosowywania środowiska języka Python, zobacz [Tworzenie środowisk i zarządzanie nimi na potrzeby szkolenia i wdrażania](how-to-use-environments.md). 

### <a name="update-the-service"></a>Aktualizowanie usługi

Podczas testowania lokalnego może być konieczne zaktualizowanie pliku `score.py` w celu dodania rejestrowania lub podjęcia próby rozwiązania problemów, które zostały wykryte. Aby ponownie załadować zmiany do pliku `score.py`, użyj `reload()`. Na przykład poniższy kod ponownie ładuje skrypt dla usługi, a następnie wysyła do niej dane. Dane są oceniane przy użyciu zaktualizowanego pliku `score.py`:

> [!IMPORTANT]
> Metoda `reload` jest dostępna tylko dla wdrożeń lokalnych. Aby uzyskać informacje na temat aktualizowania wdrożenia do innego obiektu docelowego obliczeń, zobacz sekcję Update ( [Wdrażanie modeli](how-to-deploy-and-where.md#update)).

```python
service.reload()
print(service.run(input_data=test_sample))
```

> [!NOTE]
> Skrypt zostanie ponownie załadowany z lokalizacji określonej przez obiekt `InferenceConfig` używany przez usługę.

Aby zmienić model, Conda zależności lub konfigurację wdrożenia, użyj [Update ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#update--args-). Poniższy przykład aktualizuje model używany przez usługę:

```python
service.update([different_model], inference_config, deployment_config)
```

### <a name="delete-the-service"></a>Usuwanie usługi

Aby usunąć usługę, użyj polecenie [delete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#delete--).

### <a id="dockerlog"></a>Inspekcja dziennika platformy Docker

Można wydrukować szczegółowe komunikaty dziennika aparat platformy Docker z obiektu usługi. Dziennik można wyświetlić w przypadku wdrożeń ACI, AKS i lokalnych. Poniższy przykład ilustruje sposób drukowania dzienników.

```python
# if you already have the service object handy
print(service.get_logs())

# if you only know the name of the service (note there might be multiple services with the same name but different version number)
print(ws.webservices['mysvc'].get_logs())
```

## <a name="service-launch-fails"></a>Uruchomienie usługi nie powiedzie się.

Po pomyślnym skompilowaniu obrazu system podejmie próbę uruchomienia kontenera przy użyciu konfiguracji wdrożenia. Jako część procesu rozruchu kontenera `init()` funkcji oceniania skryptu zostanie wywołany przez system. Jeśli istnieją nieobsłużone wyjątki w `init()` funkcji, można napotkać **CrashLoopBackOff** błąd w komunikacie o błędzie.

Skorzystaj z informacji w sekcji [sprawdzanie dziennika platformy Docker](#dockerlog) , aby sprawdzić dzienniki.

## <a name="function-fails-get_model_path"></a>Funkcja kończy się niepowodzeniem: get_model_path()

Często w funkcji `init()` w skrypcie oceniania funkcja [model. get_model_path ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-) jest wywoływana w celu zlokalizowania pliku modelu lub folderu plików modelu w kontenerze. Jeśli nie można znaleźć pliku lub folderu modelu, funkcja kończy się niepowodzeniem. Najprostszym sposobem, aby debugować ten błąd jest uruchomienie poniższego kodu języka Python w powłoce kontenera:

```python
from azureml.core.model import Model
import logging
logging.basicConfig(level=logging.DEBUG)
print(Model.get_model_path(model_name='my-best-model'))
```

Ten przykład drukuje ścieżkę lokalną (względem `/var/azureml-app`) w kontenerze, w którym skrypt oceniania oczekuje na znalezienie pliku lub folderu modelu. Następnie można sprawdzić, czy plik lub folder jest w rzeczywistości których oczekuje się, można.

Ustawienie poziomu rejestrowania na debugowanie może spowodować zarejestrowanie dodatkowych informacji, co może być przydatne podczas identyfikowania błędu.

## <a name="function-fails-runinput_data"></a>Funkcja kończy się niepowodzeniem: run(input_data)

Jeśli usługa została pomyślnie wdrożona, ale jej ulega awarii, gdy opublikujesz danych do punktu końcowego oceniania, można dodać błąd Przechwytywanie instrukcji w swojej `run(input_data)` funkcji tak, aby zamiast tego zwraca szczegółowy komunikat o błędzie. Przykład:

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

**Uwaga**: zwracanie komunikaty o błędach z `run(input_data)` wywołanie powinno się odbywać tylko do celów debugowania. Ze względów bezpieczeństwa nie należy zwracać komunikatów o błędach w ten sposób w środowisku produkcyjnym.

## <a name="http-status-code-503"></a>Kod stanu HTTP 503

Wdrożenia usługi Kubernetes platformy Azure obsługują Skalowanie automatyczne, co umożliwia dodawanie replik w celu obsługi dodatkowego obciążenia. Jednak Autoskalowanie jest przeznaczone do obsługi **stopniowanych** zmian obciążenia. Jeśli otrzymujesz duże liczby żądań na sekundę, klienci mogą otrzymać kod stanu HTTP 503.

Istnieją dwie rzeczy, które mogą pomóc w zapobieganiu kodów stanu 503:

* Zmień poziom wykorzystania, przy którym automatyczne skalowanie tworzy nowe repliki.
    
    Domyślnie skalowanie użycia obiektów docelowych jest ustawione na 70%, co oznacza, że usługa może obsłużyć liczbę żądań na sekundę (RPS pliku) do 30%. Cel wykorzystania można dostosować, ustawiając `autoscale_target_utilization` na niższą wartość.

    > [!IMPORTANT]
    > Ta zmiana nie powoduje *szybszego*tworzenia replik. Zamiast tego są tworzone przy niższym progu wykorzystania. Zamiast czekać, aż usługa zostanie wykorzystana przez 70%, zmiana wartości na 30% powoduje utworzenie replik w przypadku wystąpienia 30%.
    
    Jeśli usługa sieci Web już korzysta z bieżącej maksymalnej liczby replik i nadal widzisz 503 kodów stanu, zwiększ wartość `autoscale_max_replicas`, aby zwiększyć maksymalną liczbę replik.

* Zmień minimalną liczbę replik. Zwiększenie minimalnych replik zapewnia większą pulę do obsługi przychodzących skoków.

    Aby zwiększyć minimalną liczbę replik, ustaw wartość `autoscale_min_replicas` na wyższą. Wymagane repliki można obliczyć przy użyciu następującego kodu, zastępując wartości wartościami charakterystycznymi dla projektu:

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
    > Jeśli odbierane są żądania większe niż w przypadku nowych replik minimalnych, może dojść do 503s. Na przykład w miarę wzrostu ruchu do usługi może być konieczne zwiększenie minimalnej liczby replik.

Aby uzyskać więcej informacji na temat ustawiania `autoscale_target_utilization`, `autoscale_max_replicas`i `autoscale_min_replicas` dla programu, zobacz odwołanie do modułu [AksWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py) .


## <a name="advanced-debugging"></a>Zaawansowane debugowanie

W niektórych przypadkach może być konieczne interaktywne Debugowanie kodu w języku Python zawartego we wdrożeniu modelu. Na przykład, jeśli skrypt wejścia kończy się niepowodzeniem i powód nie może być określony przez dodatkowe rejestrowanie. Używając Visual Studio Code i Python Tools for Visual Studio (PTVSD), możesz dołączyć do kodu działającego wewnątrz kontenera Docker.

> [!IMPORTANT]
> Ta metoda debugowania nie działa w przypadku używania `Model.deploy()` i `LocalWebservice.deploy_configuration` do lokalnego wdrożenia modelu. Zamiast tego należy utworzyć obraz przy użyciu klasy [ContainerImage](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.containerimage?view=azure-ml-py) . 

Lokalne wdrożenia usługi sieci Web wymagają pracy instalacji platformy Docker w systemie lokalnym. Aby uzyskać więcej informacji na temat korzystania z platformy Docker, zapoznaj się z [dokumentacją platformy Docker](https://docs.docker.com/).

### <a name="configure-development-environment"></a>Konfigurowanie środowiska programowania

1. Aby zainstalować Python Tools for Visual Studio (PTVSD) w lokalnym VS Code środowisku programistycznym, użyj następującego polecenia:

    ```
    python -m pip install --upgrade ptvsd
    ```

    Aby uzyskać więcej informacji na temat używania PTVSD z VS Code, zobacz [debugowanie zdalne](https://code.visualstudio.com/docs/python/debugging#_remote-debugging).

1. Aby skonfigurować VS Code do komunikowania się z obrazem platformy Docker, Utwórz nową konfigurację debugowania:

    1. W obszarze VS Code wybierz menu __Debuguj__ , a następnie wybierz pozycję __Otwórz konfiguracje__. Zostanie otwarty plik o nazwie __Launch. JSON__ .

    1. W pliku __Launch. JSON__ Znajdź wiersz zawierający `"configurations": [`i Wstaw następujący tekst po nim:

        ```json
        {
            "name": "Azure Machine Learning: Docker Debug",
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
        > Jeśli w sekcji konfiguracje znajdują się już inne wpisy, Dodaj przecinek (,) po wstawionym kodzie.

        Ta sekcja dołącza do kontenera Docker przy użyciu portu 5678.

    1. Zapisz plik __Launch. JSON__ .

### <a name="create-an-image-that-includes-ptvsd"></a>Tworzenie obrazu zawierającego PTVSD

1. Zmodyfikuj środowisko Conda dla danego wdrożenia, aby zawierało PTVSD. Poniższy przykład demonstruje dodanie go przy użyciu `pip_packages` parametru:

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

1. Aby rozpocząć PTVSD i poczekać na połączenie, gdy usługa zostanie uruchomiona, Dodaj następujący element na początku pliku `score.py`:

    ```python
    import ptvsd
    # Allows other computers to attach to ptvsd on this IP address and port.
    ptvsd.enable_attach(address=('0.0.0.0', 5678), redirect_output = True)
    # Wait 30 seconds for a debugger to attach. If none attaches, the script continues as normal.
    ptvsd.wait_for_attach(timeout = 30)
    print("Debugger attached...")
    ```

1. Podczas debugowania, możesz chcieć wprowadzić zmiany w plikach w obrazie bez konieczności ponownego tworzenia go. Aby zainstalować Edytor tekstu (vim) w obrazie platformy Docker, Utwórz nowy plik tekstowy o nazwie `Dockerfile.steps` i użyj następujących elementów jako zawartości pliku:

    ```text
    RUN apt-get update && apt-get -y install vim
    ```

    Edytor tekstu umożliwia modyfikowanie plików wewnątrz obrazu platformy Docker w celu przetestowania zmian bez tworzenia nowego obrazu.

1. Aby utworzyć obraz, który używa pliku `Dockerfile.steps`, użyj parametru `docker_file` podczas tworzenia obrazu. W poniższym przykładzie pokazano, jak to zrobić:

    > [!NOTE]
    > W tym przykładzie przyjęto założenie, że `ws` wskazuje obszar roboczy Azure Machine Learning, a `model` jest wdrażanym modelem. Plik `myenv.yml` zawiera zależności Conda utworzone w kroku 1.

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

Po utworzeniu obrazu zostanie wyświetlona lokalizacja obrazu w rejestrze. Lokalizacja jest podobna do następującego tekstu:

```text
myregistry.azurecr.io/myimage:1
```

W tym przykładzie tekstu Nazwa rejestru jest `myregistry` a obraz ma nazwę `myimage`. Wersja obrazu jest `1`.

### <a name="download-the-image"></a>Pobierz obraz

1. Otwórz wiersz polecenia, terminal lub inną powłokę i użyj następującego polecenia [platformy Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) w celu uwierzytelnienia w ramach subskrypcji platformy Azure, która zawiera obszar roboczy Azure Machine Learning:

    ```azurecli
    az login
    ```

1. Aby uwierzytelnić się w Azure Container Registry (ACR), który zawiera obraz, użyj następującego polecenia. Zastąp `myregistry` zwracaną po zarejestrowaniu obrazu:

    ```azurecli
    az acr login --name myregistry
    ```

1. Aby pobrać obraz do lokalnego platformy Docker, użyj następującego polecenia. Zastąp `myimagepath` lokalizacją zwróconą po zarejestrowaniu obrazu:

    ```bash
    docker pull myimagepath
    ```

    Ścieżka obrazu powinna być podobna do `myregistry.azurecr.io/myimage:1`. Gdy `myregistry` jest rejestrem, `myimage` jest obrazem, a `1` to wersja obrazu.

    > [!TIP]
    > Uwierzytelnianie z poprzedniego kroku nie jest ostatnio nieograniczone. Jeśli czekasz na wystarczającą ilość czasu między poleceniem uwierzytelniania a poleceniem ściągania, zostanie wyświetlony komunikat o błędzie uwierzytelniania. W takim przypadku należy ponownie uwierzytelnić.

    Czas potrzebny na ukończenie pobierania zależy od szybkości połączenia internetowego. Podczas procesu jest wyświetlany stan pobierania. Po zakończeniu pobierania można użyć polecenia `docker images`, aby sprawdzić, czy plik został pobrany.

1. Aby ułatwić pracę z obrazem, użyj następującego polecenia, aby dodać tag. Zastąp `myimagepath` wartością lokalizacji z kroku 2.

    ```bash
    docker tag myimagepath debug:1
    ```

    W pozostałej części kroków można odwołać się do lokalnego obrazu jako `debug:1` zamiast pełnej wartości ścieżki obrazu.

### <a name="debug-the-service"></a>Debugowanie usługi

> [!TIP]
> Jeśli ustawisz limit czasu dla połączenia usługi PTVSD w pliku `score.py`, musisz połączyć VS Code z sesją debugowania przed upływem limitu czasu. Rozpocznij VS Code, Otwórz lokalną kopię `score.py`, ustaw punkt przerwania i przygotuj się do użycia przed wykonaniem kroków opisanych w tej sekcji.
>
> Aby uzyskać więcej informacji na temat debugowania i ustawiania punktów przerwania, zobacz [debugowanie](https://code.visualstudio.com/Docs/editor/debugging).

1. Aby uruchomić kontener platformy Docker przy użyciu obrazu, użyj następującego polecenia:

    ```bash
    docker run --rm --name debug -p 8000:5001 -p 5678:5678 debug:1
    ```

1. Aby dołączyć VS Code do PTVSD wewnątrz kontenera, Otwórz VS Code i naciśnij klawisz F5 lub wybierz opcję __Debuguj__. Po wyświetleniu monitu wybierz konfigurację __Azure Machine Learning: Docker Debug__ . Możesz również wybrać ikonę debugowania z paska bocznego, __Azure Machine Learning:__ z menu rozwijanego debugowanie, a następnie użyć zielonej strzałki do dołączenia debugera.

    ![Ikona debugowania, przycisk Rozpocznij debugowanie i selektor konfiguracji](./media/how-to-troubleshoot-deployment/start-debugging.png)

W tym momencie VS Code nawiązuje połączenie z usługą PTVSD wewnątrz kontenera Docker i zostanie zatrzymane na ustawionym wcześniej punkcie przerwania. Teraz można przechodzić przez kod w trakcie jego uruchamiania, wyświetlać zmienne itp.

Aby uzyskać więcej informacji na temat używania VS Code do debugowania języka Python, zobacz [Debugowanie kodu](https://docs.microsoft.com/visualstudio/python/debugging-python-in-visual-studio?view=vs-2019)w języku Python.

<a id="editfiles"></a>
### <a name="modify-the-container-files"></a>Modyfikowanie plików kontenera

Aby wprowadzić zmiany w plikach w obrazie, można dołączyć do uruchomionego kontenera i wykonać powłokę bash. Z tego miejsca możesz użyć usługi VIM do edycji plików:

1. Aby nawiązać połączenie z uruchomionym kontenerem i uruchomić powłokę bash w kontenerze, użyj następującego polecenia:

    ```bash
    docker exec -it debug /bin/bash
    ```

1. Aby znaleźć pliki używane przez usługę, użyj następującego polecenia z powłoki bash w kontenerze, jeśli domyślny katalog jest inny niż `/var/azureml-app`:

    ```bash
    cd /var/azureml-app
    ```

    W tym miejscu możesz użyć usługi VIM do edycji pliku `score.py`. Aby uzyskać więcej informacji na temat korzystania z usługi VIM, zobacz [Korzystanie z edytora vim](https://www.tldp.org/LDP/intro-linux/html/sect_06_02.html).

1. Zmiany w kontenerze nie są zwykle utrwalane. Aby zapisać wszelkie wprowadzone zmiany, użyj poniższego polecenia, przed zamknięciem powłoki uruchomionej w powyższym kroku (czyli w innej powłoce):

    ```bash
    docker commit debug debug:2
    ```

    To polecenie tworzy nowy obraz o nazwie `debug:2`, który zawiera zmiany.

    > [!TIP]
    > Aby zmiany zaczęły obowiązywać, należy zatrzymać bieżący kontener i zacząć korzystać z nowej wersji.

1. Upewnij się, że zmiany wprowadzone w plikach w kontenerze są zsynchronizowane z lokalnymi plikami, których VS Code używa. W przeciwnym razie środowisko debugera nie będzie działało zgodnie z oczekiwaniami.

### <a name="stop-the-container"></a>Zatrzymywanie kontenera

Aby zatrzymać kontener, użyj następującego polecenia:

```bash
docker stop debug
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o wdrażaniu:

* [Jak wdrażać i którym](how-to-deploy-and-where.md)
* [Samouczek: Uczenie i wdrażanie modeli](tutorial-train-models-with-aml.md)
