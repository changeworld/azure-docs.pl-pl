---
title: Przewodnik rozwiązywania problemów z wdrażaniem
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak obejść, rozwiązać i rozwiązać typowe błędy wdrażania platformy Docker za pomocą usługi Azure Kubernetes Service i wystąpień kontenerów platformy Azure przy użyciu usługi Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: clauren42
ms.author: clauren
ms.reviewer: jmartens
ms.date: 03/05/2020
ms.custom: seodec18
ms.openlocfilehash: fab46f7d7ae74ad643ce3f122b27b0dc767f5a78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78399689"
---
# <a name="troubleshooting-azure-machine-learning-azure-kubernetes-service-and-azure-container-instances-deployment"></a>Rozwiązywanie problemów z usługą Azure Machine Learning Azure Kubernetes service i wdrożenie wystąpień kontenerów platformy Azure

Dowiedz się, jak obejść lub rozwiązać typowe błędy wdrażania platformy Docker za pomocą wystąpień kontenerów platformy Azure (ACI) i usługi Azure Kubernetes Service (AKS) przy użyciu usługi Azure Machine Learning.

Podczas wdrażania modelu w usłudze Azure Machine Learning system wykonuje szereg zadań.

Zalecane i najbardziej aktualne podejście do wdrażania modelu jest za pośrednictwem interfejsu API [Model.deploy()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) przy użyciu [środowiska](https://docs.microsoft.com/azure/machine-learning/service/how-to-use-environments) obiektu jako parametr wejściowy. W takim przypadku nasza usługa utworzy podstawowy obraz dockera podczas etapu wdrażania i zainstaluje wymagane modele w jednym wywołaniu. Podstawowe zadania wdrażania to:

1. Zarejestruj model w rejestrze modelu obszaru roboczego.

2. Zdefiniuj konfigurację wnioskowania:
    1. Utwórz obiekt [Środowisko](https://docs.microsoft.com/azure/machine-learning/service/how-to-use-environments) na podstawie zależności określonych w pliku yaml środowiska lub użyj jednego z naszych zamówionych środowisk.
    2. Utwórz konfigurację wnioskowania (Obiekt InferenceConfig) na podstawie środowiska i skryptu oceniania.

3. Wdrażanie modelu w usłudze ACI (Azure Container Instance) lub w usłudze Azure Kubernetes Service (AKS).

Dowiedz się więcej o tym procesie we wprowadzeniu do [zarządzania modelami.](concept-model-management-and-deployment.md)

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**. Jeśli go nie masz, wypróbuj [bezpłatną lub płatną wersję usługi Azure Machine Learning](https://aka.ms/AMLFree).
* Zestaw [SDK usługi Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
* [Interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
* [Rozszerzenie interfejsu wiersza polecenia dla usługi Azure Machine Learning](reference-azure-machine-learning-cli.md).
* Aby debugować lokalnie, musisz mieć działającą instalację platformy Docker w systemie lokalnym.

    Aby zweryfikować instalację platformy `docker run hello-world` Docker, użyj polecenia z terminala lub wiersza polecenia. Aby uzyskać informacje dotyczące instalowania platformy Docker lub rozwiązywania problemów z błędami platformy Docker, zobacz [Dokumentację platformy Docker](https://docs.docker.com/).

## <a name="before-you-begin"></a>Przed rozpoczęciem

Jeśli napotkasz jakikolwiek problem, pierwszą rzeczą do zrobienia jest rozbicie zadania wdrażania (poprzednio opisane) na poszczególne kroki, aby wyizolować problem.

Zakładając, że używasz nowej/zalecanej metody wdrażania za pośrednictwem interfejsu API [Model.deploy()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) z obiektem [Środowisko](https://docs.microsoft.com/azure/machine-learning/service/how-to-use-environments) jako parametrem wejściowym, kod można podzielić na trzy główne kroki:

1. Zarejestrowanie modelu. Oto przykładowy kod:

    ```python
    from azureml.core.model import Model


    # register a model out of a run record
    model = best_run.register_model(model_name='my_best_model', model_path='outputs/my_model.pkl')

    # or, you can register a file or a folder of files as a model
    model = Model.register(model_path='my_model.pkl', model_name='my_best_model', workspace=ws)
    ```

2. Zdefiniuj konfigurację wnioskowania dla wdrożenia:

    ```python
    from azureml.core.model import InferenceConfig
    from azureml.core.environment import Environment


    # create inference configuration based on the requirements defined in the YAML
    myenv = Environment.from_conda_specification(name="myenv", file_path="myenv.yml")
    inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
    ```

3. Wdrażanie modelu przy użyciu konfiguracji wnioskowania utworzonej w poprzednim kroku:

    ```python
    from azureml.core.webservice import AciWebservice


    # deploy the model
    aci_config = AciWebservice.deploy_configuration(cpu_cores=1, memory_gb=1)
    aci_service = Model.deploy(workspace=ws,
                           name='my-service',
                           models=[model],
                           inference_config=inference_config,
                           deployment_config=aci_config)
    aci_service.wait_for_deployment(show_output=True)
    ```

Po podziale procesu wdrażania na poszczególne zadania, możemy przyjrzeć się niektóre z najczęstszych błędów.

## <a name="debug-locally"></a>Debugowanie lokalnie

Jeśli wystąpią problemy z wdrażaniem modelu w usłudze ACI lub AKS, spróbuj wdrożyć go jako lokalną usługę sieci web. Korzystanie z lokalnej usługi sieci web ułatwia rozwiązywanie problemów. Obraz platformy Docker zawierający model jest pobierany i uruchamiany w systemie lokalnym.

> [!WARNING]
> Wdrożenia lokalnych usług sieci web nie są obsługiwane w scenariuszach produkcyjnych.

Aby wdrożyć lokalnie, zmodyfikuj kod, aby użyć `LocalWebservice.deploy_configuration()` do utworzenia konfiguracji wdrożenia. Następnie `Model.deploy()` użyj do wdrożenia usługi. Poniższy przykład wdraża model (zawarty w zmiennej modelu) jako lokalną usługę sieci web:

```python
from azureml.core.environment import Environment
from azureml.core.model import InferenceConfig, Model
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

Należy pamiętać, że jeśli definiujesz własną specyfikację Conda YAML, należy wyświetlić listę azureml-defaults z wersją >= 1.0.45 jako zależności pipsa. Ten pakiet zawiera funkcje potrzebne do obsługi modelu jako usługi sieci web.

W tym momencie można pracować z usługą w zwykły sposób. Na przykład poniższy kod demonstruje wysyłanie danych do usługi:

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

Aby uzyskać więcej informacji na temat dostosowywania środowiska języka Python, zobacz [Tworzenie środowisk szkoleniowych i wdrożeniowych oraz zarządzanie nimi.](how-to-use-environments.md) 

### <a name="update-the-service"></a>Aktualizowanie usługi

Podczas testowania lokalnego `score.py` może być konieczne zaktualizowanie pliku, aby dodać rejestrowanie lub spróbować rozwiązać wszelkie wykryte problemy. Aby ponownie załadować `score.py` zmiany `reload()`do pliku, użyj programu . Na przykład poniższy kod ponownie ładuje skrypt dla usługi, a następnie wysyła do niego dane. Dane są oceniane przy `score.py` użyciu zaktualizowanego pliku:

> [!IMPORTANT]
> Metoda `reload` jest dostępna tylko dla wdrożeń lokalnych. Aby uzyskać informacje na temat aktualizowania wdrożenia do innego celu obliczeniowego, zobacz sekcję aktualizacji [modeli wdrażania](how-to-deploy-and-where.md#update).

```python
service.reload()
print(service.run(input_data=test_sample))
```

> [!NOTE]
> Skrypt jest ponownie ładowany z `InferenceConfig` lokalizacji określonej przez obiekt używany przez usługę.

Aby zmienić model, zależności Conda lub konfigurację wdrożenia, użyj [update()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#update--args-). Poniższy przykład aktualizuje model używany przez usługę:

```python
service.update([different_model], inference_config, deployment_config)
```

### <a name="delete-the-service"></a>Usuwanie usługi

Aby usunąć usługę, użyj [pliku delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#delete--).

### <a name="inspect-the-docker-log"></a><a id="dockerlog"></a>Sprawdzanie dziennika platformy Docker

Można wydrukować szczegółowe komunikaty dziennika aparatu platformy Docker z obiektu usługi. Można wyświetlić dziennik dla wdrożeń ACI, AKS i lokalnych. W poniższym przykładzie pokazano, jak wydrukować dzienniki.

```python
# if you already have the service object handy
print(service.get_logs())

# if you only know the name of the service (note there might be multiple services with the same name but different version number)
print(ws.webservices['mysvc'].get_logs())
```

## <a name="service-launch-fails"></a>Uruchomienie usługi kończy się niepowodzeniem

Po pomyślnym skonskoncie obrazu system próbuje uruchomić kontener przy użyciu konfiguracji wdrażania. W ramach procesu uruchamiania kontenera `init()` funkcja w skrypcie oceniania jest wywoływana przez system. Jeśli w `init()` funkcji występują nieprzechodnione wyjątki, w komunikacie o błędzie może zostać wyświetlony błąd **CrashLoopBackOff.**

Użyj informacji w sekcji Inspekcja dziennika platformy [Docker,](#dockerlog) aby sprawdzić dzienniki.

## <a name="function-fails-get_model_path"></a>Funkcja nie powiedzie się: get_model_path()

Często w `init()` funkcji w skrypcie oceniania wywoływana jest funkcja [Model.get_model_path(),](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-) aby zlokalizować plik modelu lub folder plików modelu w kontenerze. Jeśli nie można odnaleźć pliku lub folderu modelu, funkcja nie powiedzie się. Najprostszym sposobem debugowania tego błędu jest uruchomienie poniższego kodu Języka Python w powłoki kontenera:

```python
from azureml.core.model import Model
import logging
logging.basicConfig(level=logging.DEBUG)
print(Model.get_model_path(model_name='my-best-model'))
```

W tym przykładzie odbita `/var/azureml-app`ścieżkę lokalną (względem) w kontenerze, w którym skrypt oceniania oczekuje znalezienia pliku lub folderu modelu. Następnie można sprawdzić, czy plik lub folder jest rzeczywiście, gdzie oczekuje się, że będzie.

Ustawienie poziomu rejestrowania na DEBUG może spowodować dodatkowe informacje, które mają być rejestrowane, które mogą być przydatne w identyfikacji błędu.

## <a name="function-fails-runinput_data"></a>Funkcja kończy się niepowodzeniem: run(input_data)

Jeśli usługa została pomyślnie wdrożona, ale ulega awarii podczas publikowania danych w punkcie końcowym `run(input_data)` oceniania, można dodać instrukcję przechwytywania błędów w funkcji, aby zamiast tego zwracała szczegółowy komunikat o błędzie. Przykład:

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

**Uwaga:** Zwracanie komunikatów `run(input_data)` o błędach z wywołania powinny być wykonywane tylko w celu debugowania. Ze względów bezpieczeństwa nie należy zwracać komunikatów o błędach w ten sposób w środowisku produkcyjnym.

## <a name="http-status-code-502"></a>Kod stanu HTTP 502

Kod stanu 502 wskazuje, że usługa została zdyskwaliona wyjątek lub uległa awarii w `run()` metodzie pliku score.py. Użyj informacji w tym artykule do debugowania pliku.

## <a name="http-status-code-503"></a>Kod stanu HTTP 503

Wdrożenia usługi Azure Kubernetes obsługują skalowanie automatyczne, które umożliwia dodawanie replik w celu obsługi dodatkowego obciążenia. Jednak skalowanie automatyczne jest przeznaczony do obsługi **stopniowych** zmian obciążenia. Jeśli otrzymasz duże skoki żądań na sekundę, klienci mogą otrzymać kod stanu HTTP 503.

Istnieją dwie rzeczy, które mogą pomóc w zapobieganiu kodom stanu 503:

* Zmień poziom wykorzystania, na którym skalowanie automatyczne tworzy nowe repliki.
    
    Domyślnie wykorzystanie docelowe skalowania automatycznego jest ustawione na 70%, co oznacza, że usługa może obsługiwać skoki żądań na sekundę (RPS) do 30%. Cel wykorzystania można dostosować, ustawiając wartość `autoscale_target_utilization` niższą.

    > [!IMPORTANT]
    > Ta zmiana nie powoduje *szybszego*tworzenia replik. Zamiast tego są one tworzone przy niższym progu wykorzystania. Zamiast czekać, aż usługa jest 70% wykorzystywane, zmiana wartości do 30% powoduje, że repliki mają być tworzone, gdy 30% wykorzystania występuje.
    
    Jeśli usługa sieci web korzysta już z bieżących replik max i nadal widzisz `autoscale_max_replicas` 503 kody stanu, zwiększ wartość, aby zwiększyć maksymalną liczbę replik.

* Zmień minimalną liczbę replik. Zwiększenie minimalnych replik zapewnia większą pulę do obsługi przychodzących kolców.

    Aby zwiększyć minimalną liczbę replik, należy ustawić `autoscale_min_replicas` wyższą wartość. Wymagane repliki można obliczyć przy użyciu następującego kodu, zastępując wartości wartościami specyficznymi dla projektu:

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
    > Jeśli otrzymasz skoki żądań większe niż nowe repliki minimalne mogą obsługiwać, może pojawić się 503s ponownie. Na przykład w miarę zwiększania ruchu do usługi może być konieczne zwiększenie minimalnej repliki.

Aby uzyskać więcej `autoscale_target_utilization` `autoscale_max_replicas`informacji `autoscale_min_replicas` na temat ustawiania , i dla, zobacz odwołanie do modułu [AksWebservice.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py)

## <a name="http-status-code-504"></a>Kod stanu HTTP 504

Kod stanu 504 wskazuje, że limit czasu żądania został przesunął. Domyślny limit czasu wynosi 1 minutę.

Można zwiększyć limit czasu lub spróbuj przyspieszyć usługę, modyfikując score.py, aby usunąć niepotrzebne wywołania. Jeśli te akcje nie rozwiążą problemu, użyj informacji zawartych w tym artykule, aby debugować plik score.py. Kod może być w stanie zawieszenia lub nieskończonej pętli.

## <a name="advanced-debugging"></a>Zaawansowane debugowanie

W niektórych przypadkach może być konieczne interaktywne debugowanie kodu języka Python zawartego we wdrożeniu modelu. Na przykład jeśli skrypt wpisu nie powiódł się i przyczyna nie może być określona przez dodatkowe rejestrowanie. Za pomocą programu Visual Studio Code i Narzędzia Python dla programu Visual Studio (PTVSD), można dołączyć do kodu uruchomionego wewnątrz kontenera platformy Docker.

> [!IMPORTANT]
> Ta metoda debugowania nie działa `Model.deploy()` `LocalWebservice.deploy_configuration` podczas korzystania i wdrażania modelu lokalnie. Zamiast tego należy utworzyć obraz przy użyciu [Metody Model.package().](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#package-workspace--models--inference-config-none--generate-dockerfile-false-)

Wdrożenia lokalnych usług sieci web wymagają działającej instalacji platformy Docker w systemie lokalnym. Aby uzyskać więcej informacji na temat korzystania z platformy Docker, zobacz [Dokumentację platformy Docker](https://docs.docker.com/).

### <a name="configure-development-environment"></a>Konfigurowanie środowiska programowania

1. Aby zainstalować narzędzia Języka Python dla programu Visual Studio (PTVSD) w lokalnym środowisku programistycznym VS Code, należy użyć następującego polecenia:

    ```
    python -m pip install --upgrade ptvsd
    ```

    Aby uzyskać więcej informacji na temat korzystania z PTVSD z kodem VS, zobacz [Zdalne debugowanie](https://code.visualstudio.com/docs/python/debugging#_remote-debugging).

1. Aby skonfigurować program VS Code do komunikowania się z obrazem platformy Docker, należy utworzyć nową konfigurację debugowania:

    1. W obszarze Kod programu VS wybierz menu __Debugowanie,__ a następnie wybierz polecenie __Otwórz konfiguracje__. Zostanie otwarty plik o nazwie __launch.json.__

    1. W pliku __launch.json__ znajdź wiersz `"configurations": [`zawierający program i wstaw następujący tekst po nim:

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
        > Jeśli istnieją już inne wpisy w sekcji konfiguracje, dodać przecinek (,) po wstawionym kodzie.

        Ta sekcja jest dołączana do kontenera platformy Docker przy użyciu portu 5678.

    1. Zapisz plik __launch.json.__

### <a name="create-an-image-that-includes-ptvsd"></a>Tworzenie obrazu zawierającego PTVSD

1. Zmodyfikuj środowisko conda dla wdrożenia, tak aby obejmowało PTVSD. Poniższy przykład pokazuje dodanie `pip_packages` go przy użyciu parametru:

    ```python
    from azureml.core.conda_dependencies import CondaDependencies 


    # Usually a good idea to choose specific version numbers
    # so training is made on same packages as scoring
    myenv = CondaDependencies.create(conda_packages=['numpy==1.15.4',            
                                'scikit-learn==0.19.1', 'pandas==0.23.4'],
                                 pip_packages = ['azureml-defaults==1.0.45', 'ptvsd'])

    with open("myenv.yml","w") as f:
        f.write(myenv.serialize_to_string())
    ```

1. Aby uruchomić ptvsd i czekać na połączenie po uruchomieniu usługi, dodaj `score.py` następujące elementy do górnej części pliku:

    ```python
    import ptvsd
    # Allows other computers to attach to ptvsd on this IP address and port.
    ptvsd.enable_attach(address=('0.0.0.0', 5678), redirect_output = True)
    # Wait 30 seconds for a debugger to attach. If none attaches, the script continues as normal.
    ptvsd.wait_for_attach(timeout = 30)
    print("Debugger attached...")
    ```

1. Utwórz obraz na podstawie definicji środowiska i ściągnij obraz do rejestru lokalnego. Podczas debugowania można wprowadzić zmiany w plikach obrazu bez konieczności ponownego tworzenia. Aby zainstalować edytor tekstu (vim) na obrazie `Environment.docker.base_image` `Environment.docker.base_dockerfile` platformy Docker, użyj właściwości i:

    > [!NOTE]
    > W tym przykładzie przyjęto założenie, że `ws` wskazuje `model` obszar roboczy usługi Azure Machine Learning i jest to wdrażany model. Plik `myenv.yml` zawiera zależności conda utworzone w kroku 1.

    ```python
    from azureml.core.conda_dependencies import CondaDependencies
    from azureml.core.model import InferenceConfig
    from azureml.core.environment import Environment


    myenv = Environment.from_conda_specification(name="env", file_path="myenv.yml")
    myenv.docker.base_image = None
    myenv.docker.base_dockerfile = "FROM mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04\nRUN apt-get update && apt-get install vim -y"
    inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
    package = Model.package(ws, [model], inference_config)
    package.wait_for_creation(show_output=True)  # Or show_output=False to hide the Docker build logs.
    package.pull()
    ```

    Po utworzeniu i pobraniu obrazu ścieżka obrazu (zawiera repozytorium, nazwę i znacznik, który w tym przypadku jest również jego skrótem) jest wyświetlana w komunikacie podobnym do następującego:

    ```text
    Status: Downloaded newer image for myregistry.azurecr.io/package@sha256:<image-digest>
    ```

1. Aby ułatwić pracę z obrazem, użyj następującego polecenia, aby dodać znacznik. Zamień `myimagepath` na wartość lokalizacji z poprzedniego kroku.

    ```bash
    docker tag myimagepath debug:1
    ```

    W pozostałych krokach można odwołać się do `debug:1` obrazu lokalnego jako zamiast wartości ścieżki pełnego obrazu.

### <a name="debug-the-service"></a>Debugowanie usługi

> [!TIP]
> Jeśli ustawisz limit czasu dla połączenia `score.py` PTVSD w pliku, należy połączyć vs code do sesji debugowania przed upływem limitu czasu. Uruchom program VS Code, `score.py`otwórz lokalną kopię , ustawić punkt przerwania i mieć go gotowe do pracy przed użyciem kroków w tej sekcji.
>
> Aby uzyskać więcej informacji na temat debugowania i ustawiania punktów przerwania, zobacz [Debugowanie](https://code.visualstudio.com/Docs/editor/debugging).

1. Aby uruchomić kontener platformy Docker przy użyciu obrazu, użyj następującego polecenia:

    ```bash
    docker run --rm --name debug -p 8000:5001 -p 5678:5678 debug:1
    ```

1. Aby dołączyć kod VS do PTVSD wewnątrz kontenera, otwórz vs code i użyj klawisza F5 lub wybierz __debug .__ Po wyświetleniu monitu wybierz konfigurację __usługi Azure Machine Learning: Debug docker.__ Można również wybrać ikonę debugowania z paska bocznego, wpis __Azure Machine Learning: Docker Debug__ z menu rozwijanego Debugowania, a następnie użyć zielonej strzałki, aby dołączyć debuger.

    ![Ikona debugowania, przycisk debugowania i selektor konfiguracji](./media/how-to-troubleshoot-deployment/start-debugging.png)

W tym momencie vs code łączy się z PTVSD wewnątrz kontenera platformy Docker i zatrzymuje się w punkcie przerwania, który został ustawiony wcześniej. Możesz teraz przejść przez kod podczas jego pracy, przeglądać zmienne itp.

Aby uzyskać więcej informacji na temat używania kodu VS do debugowania języka Python, zobacz [Debugowanie kodu języka Python](https://docs.microsoft.com/visualstudio/python/debugging-python-in-visual-studio?view=vs-2019).

<a id="editfiles"></a>
### <a name="modify-the-container-files"></a>Modyfikowanie plików kontenerów

Aby wprowadzić zmiany w plikach obrazu, można dołączyć do uruchomionego kontenera i wykonać powłokę bash. Stamtąd można użyć vim do edycji plików:

1. Aby połączyć się z uruchomionym kontenerem i uruchomić powłokę bash w kontenerze, użyj następującego polecenia:

    ```bash
    docker exec -it debug /bin/bash
    ```

1. Aby znaleźć pliki używane przez usługę, użyj następującego polecenia z powłoki bash w `/var/azureml-app`kontenerze, jeśli domyślny katalog jest inny niż:

    ```bash
    cd /var/azureml-app
    ```

    W tym miejscu można użyć vim do edycji `score.py` pliku. Aby uzyskać więcej informacji na temat korzystania z vim, zobacz [Korzystanie z edytora Vim](https://www.tldp.org/LDP/intro-linux/html/sect_06_02.html).

1. Zmiany w kontenerze zwykle nie są utrwalone. Aby zapisać wprowadzone zmiany, użyj następującego polecenia, zanim zakończysz działanie powłoki rozpoczętej w powyższym kroku (czyli w innej powłoce):

    ```bash
    docker commit debug debug:2
    ```

    To polecenie tworzy nowy `debug:2` obraz o nazwie, który zawiera zmiany.

    > [!TIP]
    > Należy zatrzymać bieżący kontener i rozpocząć korzystanie z nowej wersji, zanim zmiany zaczną obowiązywać.

1. Upewnij się, aby zachować zmiany wprowadzone do plików w kontenerze w synchronizacji z plikami lokalnymi, które używa programu VS Code. W przeciwnym razie środowisko debugera nie będzie działać zgodnie z oczekiwaniami.

### <a name="stop-the-container"></a>Zatrzymywanie kontenera

Aby zatrzymać kontener, użyj następującego polecenia:

```bash
docker stop debug
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o wdrażaniu:

* [Jak wdrożyć i gdzie](how-to-deploy-and-where.md)
* [Samouczek: Szkolenie & wdrażania modeli](tutorial-train-models-with-aml.md)
