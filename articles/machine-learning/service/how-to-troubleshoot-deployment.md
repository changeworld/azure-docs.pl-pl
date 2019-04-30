---
title: Przewodnik rozwiązywania problemów z wdrażaniem
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak obejść, rozwiązania i rozwiązywanie typowych problemów wdrażania platformy Docker za pomocą usługi AKS i ACI przy użyciu usługi Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: chris-lauren
ms.author: clauren
ms.reviewer: jmartens
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 815be7400e0a0560ace7e07b317aeb25c2feacd5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60817414"
---
# <a name="troubleshooting-azure-machine-learning-service-aks-and-aci-deployments"></a>Rozwiązywanie problemów z wdrożeniami usługi AKS i ACI usługi Azure Machine Learning

W tym artykule dowiesz się, jak obejść lub Rozwiązywanie typowych błędów wdrażania platformy Docker za pomocą usługi Azure Container Instances (ACI) i Azure Kubernetes Service (AKS), za pomocą usługi Azure Machine Learning.

W przypadku wdrażania modelu w usłudze Azure Machine Learning, system wykonuje wiele zadań. Jest to złożonej sekwencji zdarzeń, a czasami pojawiają się problemy. Dostępne są następujące zadania wdrażania:

1. W rejestrze modelu obszaru roboczego, należy zarejestrować model.

2. Zbuduj obraz Docker, w tym:
    1. Pobierz zarejestrowanego modelu z rejestru. 
    2. Utwórz plik dockerfile, za pomocą środowiska Python na podstawie zależności, określonych w pliku yaml środowiska.
    3. Dodawanie plików modelu, a skrypt oceniania, które należy podać w pliku dockerfile.
    4. Twórz nowy obraz platformy Docker przy użyciu pliku dockerfile.
    5. Zarejestruj obrazu platformy Docker za pomocą usługi Azure Container Registry, skojarzone z obszarem roboczym.

3. Wdrażanie obrazu platformy Docker do usługi Azure Container wystąpienia (ACI) lub do usługi Azure Kubernetes Service (AKS).

4. Uruchom nowy kontener (lub kontenery) w usłudze ACI i AKS. 

Dowiedz się więcej na temat tego procesu w [zarządzania modelami](concept-model-management-and-deployment.md) wprowadzenie.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Jeśli napotkasz problem z dowolnym najpierw musisz jest podzielenie zadania wdrożenia (opisany poprzedniego) na poszczególne kroki, aby ustalić przyczynę problemu. 

Jest to przydatne, jeśli używasz `Webservice.deploy` interfejsu API, lub `Webservice.deploy_from_model` interfejsu API, ponieważ te funkcje zgrupować wyżej czynności w ramach jednej akcji. Zazwyczaj te interfejsy API są wygodne, ale warto podzielić kroki podczas rozwiązywania problemów, zastępując je poniżej wywołania interfejsu API.

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
                                                      execution_script="score.py",
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
Jeśli system jest w stanie utworzyć obraz platformy Docker `image.wait_for_creation()` wywołanie zakończy się niektóre komunikaty o błędach, które może zaoferować pewne wskazówki. Można także znaleźć więcej szczegółowych informacji o błędach w dzienniku kompilacji obrazu. Poniżej przykładowy kod pokazuje sposób odnajdowania identyfikator uri dziennika kompilacji obrazu.

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

Większość operacji tworzenia zasobów za pomocą szablonów są idempotentne, ale usługi Key Vault Czyści zasady dostępu w każdym razem, gdy jest używany szablon. Spowoduje to podzielenie dostępu do usługi Key Vault do istniejącego obszaru roboczego, który jest używany. Skutkuje to błędy podczas próby utworzenia nowych obrazów. Poniżej przedstawiono przykłady błędów, które mogą odbierać:

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
* Sprawdź zasady dostępu magazynu kluczy i umożliwia ustawienie `accessPolicies` właściwości szablonu.
* Sprawdź, czy zasobu usługi Key Vault już istnieje. Jeśli tak jest, nie twórz ponownie go za pomocą szablonu. Na przykład dodać parametr, który umożliwia wyłączenie tworzenia zasobu usługi Key Vault, jeśli już istnieje.

## <a name="service-launch-fails"></a>Uruchomienie usługi nie powiedzie się.
Po pomyślnym utworzeniu obrazu, system próbuje uruchomić kontener w usłudze ACI i AKS w zależności od konfiguracji wdrożenia. Zaleca się najpierw spróbuj wdrożenia usługi ACI, ponieważ jest prostsza wdrażanie pojedynczego kontenera. Dzięki temu można następnie wykluczyć problemy specyficzne dla usługi AKS.

Jako część procesu rozruchu kontenera `init()` funkcji oceniania skryptu zostanie wywołany przez system. Jeśli istnieją nieobsłużone wyjątki w `init()` funkcji, można napotkać **CrashLoopBackOff** błąd w komunikacie o błędzie. Poniżej przedstawiono kilka wskazówek, aby rozwiązać ten problem.

### <a name="inspect-the-docker-log"></a>Sprawdź dziennik platformy Docker
Można wydrukować szczegółowe komunikaty dziennika aparat platformy Docker z obiektu usługi.

```python
# if you already have the service object handy
print(service.get_logs())

# if you only know the name of the service (note there might be multiple services with the same name but different version number)
print(ws.webservices['mysvc'].get_logs())
```

### <a name="debug-the-docker-image-locally"></a>Debuguj lokalnie za pomocą obrazu platformy Docker
Sytuacje dziennik platformy Docker nie emituje wystarczającej ilości informacji o co będzie problem. Można wykonaj krok dalej i ściągnąć utworzony obraz platformy Docker, uruchom lokalny kontener i interakcyjnie debugować bezpośrednio w kontenerze na żywo. Aby uruchomić lokalny kontener, konieczne jest posiadanie aparat platformy Docker działa lokalnie, a będzie o wiele prostsze, jeśli masz też [wiersza polecenia platformy azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) zainstalowane.

Najpierw musimy sprawdzić lokalizację obrazu:

```python
# print image location
print(image.image_location)
```

Lokalizacja obrazu ma następujący format: `<acr-name>.azurecr.io/<image-name>:<version-number>`, takich jak `myworkpaceacr.azurecr.io/myimage:3`. 

Teraz przejdź do okna wiersza polecenia. Jeśli masz zainstalowany azure-cli, możesz wpisać następujące polecenia, aby zalogować się do usługi ACR (Azure Container Registry) skojarzone z obszarem roboczym, w którym jest przechowywany obraz. 

```sh
# log on to Azure first if you haven't done so before
$ az login

# make sure you set the right subscription in case you have access to multiple subscriptions
$ az account set -s <subscription_name_or_id>

# now let's log in to the workspace ACR
# note the acr-name is the domain name WITHOUT the ".azurecr.io" postfix
# e.g.: az acr login -n myworkpaceacr
$ az acr login -n <acr-name>
```
Jeśli nie masz azure-cli zainstalowana, możesz użyć `docker login` polecenie, aby zalogować się do usługi ACR. Jednak musisz najpierw pobrać nazwę użytkownika i hasło usługi ACR z witryny Azure portal.

Po zalogowaniu się do usługi ACR, można ściągnąć obraz platformy Docker i uruchomić kontenera w środowisku lokalnym, a następnie uruchom sesję powłoki bash debugowanie przy użyciu `docker run` polecenia:

```sh
# note the image_id is <acr-name>.azurecr.io/<image-name>:<version-number>
# for example: myworkpaceacr.azurecr.io/myimage:3
$ docker run -it <image_id> /bin/bash
```

Po uruchomieniu sesji programu bash działający kontener, można znaleźć oceniania skrypty w `/var/azureml-app` folderu. Następnie można uruchomić sesję języka Python, aby debugować skrypty oceniania. 

```sh
# enter the directory where scoring scripts live
cd /var/azureml-app

# find what Python packages are installed in the python environment
pip freeze

# sanity-check on score.py
# you might want to edit the score.py to trigger init().
# as most of the errors happen in init() when you are trying to load the model.
python score.py
```
W przypadku, gdy potrzebujesz edytora tekstów, aby zmodyfikować skrypty, można zainstalować vim, nano, Emacs lub innych ulubionego edytora.

```sh
# update package index
apt-get update

# install a text editor of your choice
apt-get install vim
apt-get install nano
apt-get install emacs

# launch emacs (for example) to edit score.py
emacs score.py

# exit the container bash shell
exit
```

Można również uruchomić lokalnie usługi sieci web i wysyłać ruch HTTP. Serwer Flask, w kontenerze platformy Docker jest uruchomiony na porcie 5001. Można mapować do dowolnych portów dostępnych na komputerze hosta.
```sh
# you can find the scoring API at: http://localhost:8000/score
$ docker run -p 8000:5001 <image_id>
```

## <a name="function-fails-getmodelpath"></a>Funkcja kończy się niepowodzeniem: get_model_path()
Często w `init()` funkcji w skrypt oceniania `Model.get_model_path()` funkcja jest wywoływana, aby zlokalizować plik modelu lub folder z plikami modelu w kontenerze. Jest to często źródło błędu, jeśli nie można odnaleźć modelu pliku lub folderu. Najprostszym sposobem, aby debugować ten błąd jest uruchomienie poniższego kodu języka Python w powłoce kontenera:

```python
import logging
logging.basicConfig(level=logging.DEBUG)
from azureml.core.model import Model
print(Model.get_model_path(model_name='my-best-model'))
```

Wypisuje to ścieżka lokalna (względem `/var/azureml-app`) w kontenerze, gdzie skrypt oceniania oczekuje, aby znaleźć model pliku lub folderu. Następnie można sprawdzić, czy plik lub folder jest w rzeczywistości których oczekuje się, można.

Ustawienie poziomu rejestrowania debugowania może zapewnia Przyczyna dodatkowe informacje mają być rejestrowane, które mogą okazać się przydatna podczas identyfikacji awarii.

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
**Uwaga**: Zwracanie komunikaty o błędach z `run(input_data)` wywołanie powinno się odbywać tylko do celów debugowania. Może nie być dobry pomysł, aby to zrobić w środowisku produkcyjnym ze względów bezpieczeństwa.

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

Aby uzyskać więcej informacji na temat ustawień dotyczących `autoscale_target_utilization`, `autoscale_max_replicas`, i `autoscale_min_replicas` , zobacz [AksWebservice](https://docs.microsoft.com/en-us/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py) odwołania do modułu.


## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o wdrażaniu: 
* [Jak wdrażać i którym](how-to-deploy-and-where.md)

* [Samouczek: Uczenie i wdrażanie modeli](tutorial-train-models-with-aml.md)
