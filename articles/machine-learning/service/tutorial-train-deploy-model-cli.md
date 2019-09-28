---
title: Uczenie i wdrażanie modeli z interfejsu wiersza polecenia
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak używać rozszerzenia Uczenie maszynowe dla interfejsu wiersza polecenia platformy Azure, aby uczenie, zarejestrować i wdrożyć model z wiersza poleceń.
ms.author: larryfr
author: Blackmist
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 09/12/2019
ms.openlocfilehash: fb46aaf04535c1b44cdd80810fbb6382dc727a67
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71350424"
---
# <a name="tutorial-train-and-deploy-a-model-from-the-cli"></a>Samouczek: Uczenie i wdrażanie modelu z poziomu interfejsu wiersza polecenia

W tym samouczku użyjesz rozszerzenia Machine Learning dla interfejsu wiersza polecenia platformy Azure, aby nauczyć, zarejestrować i wdrożyć model.

Skrypty szkoleniowe dotyczące języka Python w tym samouczku wykorzystują [scikit — Dowiedz się, jak](https://scikit-learn.org/) nauczyć model podstawowy. Fokus tego samouczka nie znajduje się na skryptach lub modelu, ale proces używania interfejsu wiersza polecenia do pracy z Azure Machine Learning.

Dowiedz się, jak wykonać następujące czynności:

> [!div class="checklist"]
> * Zainstaluj rozszerzenie uczenia maszynowego
> * Tworzenie obszaru roboczego usługi Azure Machine Learning
> * Utwórz zasób obliczeniowy służący do uczenia modelu
> * Rozpocznij pracę szkoleniową
> * Rejestrowanie i pobieranie modelu
> * Wdróż model jako usługę sieci Web
> * Dane oceny przy użyciu usługi sieci Web

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję Azure Machine Learning](https://aka.ms/AMLFree) dzisiaj.

* Aby korzystać z poleceń interfejsu wiersza polecenia w tym dokumencie ze **środowiska lokalnego**, wymagany jest [interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

    Jeśli używasz [Azure Cloud Shell](https://azure.microsoft.com//features/cloud-shell/), interfejs wiersza polecenia jest dostępny za pomocą przeglądarki i przebywa w chmurze.

## <a name="download-the-example-project"></a>Pobierz przykładowy projekt

Na potrzeby tego samouczka Pobierz projekt [https://github.com/microsoft/MLOps](https://github.com/microsoft/MLOps) . Pliki w katalogach `model-training` i `model-deployment` są używane przez kroki opisane w tym samouczku.

Aby uzyskać kopię lokalną plików, [Pobierz archiwum zip](https://github.com/microsoft/MLOps/archive/master.zip)lub użyj następującego polecenia git, aby sklonować repozytorium:

```azurecli-interactive
git clone https://github.com/microsoft/MLOps.git
```

### <a name="training-files"></a>Pliki szkoleniowe

Katalog `model-training` zawiera następujące pliki, które są używane podczas uczenia modelu:

* `.azureml\sklearn.runconfig`: Plik __konfiguracji uruchomieniowej__ . Ten plik definiuje środowisko uruchomieniowe potrzebne do uczenia modelu.
* `train-sklearn.py`: Skrypt szkoleniowy. Ten plik pociąga za model.
* `mylib.py`: Moduł pomocnika używany przez `train-sklearn.py`.
* `training-env.yml`: Definiuje zależności oprogramowania wymagające do uruchomienia skryptu szkoleniowego.

Skrypt szkoleniowy używa zestawu danych cukrzycą z scikit — Dowiedz się, jak szkolić model.

### <a name="deployment-files"></a>Pliki wdrożenia

Katalog `model-deployment` zawiera następujące pliki, które są używane do wdrożenia przeszkolonego modelu jako usługi sieci Web:

* `aciDeploymentConfig.yml`: Plik __konfiguracji wdrożenia__ . Ten plik definiuje środowisko hostingu, które jest niezbędne dla modelu.
* `inferenceConfig.yml`: Plik configuration__ wnioskowania. Ten plik definiuje środowisko oprogramowania używane przez usługę do oceny danych w modelu.
* `score.py`: Skrypt języka Python, który akceptuje dane przychodzące, ocenia je przy użyciu modelu, a następnie zwraca odpowiedź.
* `scoring-env.yml`: Zależności Conda, które są konieczne do uruchomienia modelu i skryptu `score.py`.

## <a name="connect-to-your-azure-subscription"></a>Nawiązywanie połączenia z subskrypcją platformy Azure

Istnieje kilka sposobów uwierzytelniania w ramach subskrypcji platformy Azure z poziomu interfejsu wiersza polecenia. Najbardziej podstawowa jest możliwość interakcyjnego uwierzytelniania przy użyciu przeglądarki. Aby uwierzytelnić interaktywnie, Otwórz wiersz polecenia lub terminal i użyj następującego polecenia:

```azurecli-interactive
az login
```

Jeśli interfejs wiersza polecenia może otworzyć Twoją domyślną przeglądarkę, zrobi to i załaduje stronę logowania. W przeciwnym razie musisz otworzyć przeglądarkę i postępować zgodnie z instrukcjami w wierszu polecenia. Instrukcje obejmują przeglądanie [https://aka.ms/devicelogin](https://aka.ms/devicelogin) i wprowadzanie kodu autoryzacji.

## <a name="install-the-machine-learning-extension"></a>Zainstaluj rozszerzenie uczenia maszynowego

Aby zainstalować rozszerzenie Uczenie maszynowe, użyj następującego polecenia:

```azurecli-interactive
az extension add -n azure-cli-ml
```

Jeśli zostanie wyświetlony komunikat o tym, że rozszerzenie jest już zainstalowane, użyj następującego polecenia, aby zaktualizować najnowszą wersję:

```azurecli-interactive
az extension update -n azure-cli-ml
```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów to podstawowy kontener zasobów na platformie Azure. Podczas pracy z usługą Azure Machine Learning Grupa zasobów będzie zawierać obszar roboczy usługi Azure Machine Learning. Będzie również zawierać inne usługi platformy Azure używane przez obszar roboczy. Na przykład jeśli nauczysz model przy użyciu zasobów obliczeniowych opartych na chmurze, ten zasób zostanie utworzony w grupie zasobów.

Aby __utworzyć nową grupę zasobów__, użyj następującego polecenia. Zamień `<resource-group-name>` na nazwę, która ma być używana dla tej grupy zasobów. Zamień `<location>` na region platformy Azure, który ma być używany dla tej grupy zasobów:

> [!TIP]
> Należy wybrać region, w którym usługa Azure Machine Learning jest dostępna. Aby uzyskać więcej informacji, zobacz [dostępne produkty według regionów](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service).

```azurecli-interactive
az group create --name <resource-group-name> --location <location>
```

Odpowiedź z tego polecenia jest podobna do następującej:

```json
{
  "id": "/subscriptions/<subscription-GUID>/resourceGroups/<resourcegroupname>",
  "location": "<location>",
  "managedBy": null,
  "name": "<resource-group-name>",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null,
  "type": null
}
```

Aby uzyskać więcej informacji na temat pracy z grupami zasobów, zobacz [AZ Group](https://docs.microsoft.com//cli/azure/group?view=azure-cli-latest).

## <a name="create-a-workspace"></a>Tworzenie obszaru roboczego

Aby utworzyć nowy obszar roboczy, użyj następującego polecenia. Zastąp `<workspace-name>` nazwą, której chcesz użyć dla tego obszaru roboczego. Zastąp `<resource-group-name>` nazwą grupy zasobów:

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name>
```

Dane wyjściowe tego polecenia są podobne do następujących:

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>",
  "containerRegistry": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.containerregistry/registries/<acr-name>",
  "creationTime": "2019-08-30T20:24:19.6984254+00:00",
  "description": "",
  "friendlyName": "<workspace-name>",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

## <a name="connect-local-project-to-workspace"></a>Połącz projekt lokalny z obszarem roboczym

Z poziomu terminalu lub wiersza polecenia Użyj następujących poleceń zmień katalogi na katalog `mlops`, a następnie połącz się z obszarem roboczym:

```azurecli-interactive
cd ~/mlops
az ml folder attach -w <workspace-name> -g <resource-group-name>
```

Dane wyjściowe tego polecenia są podobne do następujących:

```json
{
  "Experiment name": "model-training",
  "Project path": "/Code/MLOps/model-training",
  "Resource group": "<resource-group-name>",
  "Subscription id": "<subscription-id>",
  "Workspace name": "<workspace-name>"
}
```

To polecenie tworzy plik `.azureml/config.json`, który zawiera informacje, które są konieczne do nawiązania połączenia z obszarem roboczym. Pozostałe polecenia `az ml` użyte w tym samouczku będą używać tego pliku, więc nie trzeba dodawać obszaru roboczego i grupy zasobów do wszystkich poleceń.

## <a name="create-the-compute-target-for-training"></a>Utwórz element docelowy obliczeń na potrzeby szkolenia

Ten przykład używa wystąpienia obliczeniowego Azure Machine Learning do uczenia modelu. Aby utworzyć nowe wystąpienie obliczeniowe, użyj następującego polecenia:

```azurecli-interactive
az ml computetarget create amlcompute -n cpu --max-nodes 4 --vm-size Standard_D2_V2
```

Dane wyjściowe tego polecenia są podobne do następujących:

```json
{
  "location": "<location>",
  "name": "cpu",
  "provisioningErrors": null,
  "provisioningState": "Succeeded"
}
```

To polecenie tworzy nowy obiekt docelowy obliczeń o nazwie `cpu` z maksymalnie czterema węzłami. Wybrany rozmiar maszyny wirtualnej zapewnia maszynę wirtualną z zasobem procesora GPU. Aby uzyskać informacje na temat rozmiaru maszyny wirtualnej, zobacz [typy maszyn wirtualnych i rozmiary].

> [!IMPORTANT]
> Nazwa elementu docelowego obliczeń (`cpu` w tym przypadku) jest ważna; odwołuje się do niego plik `.azureml/sklearn.runconfig` używany w następnej sekcji.

## <a name="submit-the-training-run"></a>Prześlij przebieg szkolenia

Aby rozpocząć szkolenie w miejscu docelowym obliczeń `cpu`, należy zmienić katalogi na katalog `model-training`, a następnie użyć następującego polecenia:

```azurecli-interactive
cd ~/mlops/model-training
az ml run submit-script -e myexperiment -c sklearn -d training-env.yml -t runoutput.json train-sklearn.py
```

To polecenie określa nazwę eksperymentu (`myexperiment`). Eksperyment zapisuje informacje o tym przebiegu w obszarze roboczym.

Parametr `-c sklearn` Określa plik `.azureml/sklearn.runconfig`. Jak wspomniano wcześniej, ten plik zawiera informacje służące do konfigurowania środowiska używanego w ramach przebiegu szkoleniowego. Po sprawdzeniu tego pliku zobaczysz, że odwołuje się do utworzonego wcześniej elementu docelowego obliczeń `cpu`. Zawiera również listę liczby węzłów, które mają być używane podczas uczenia (`"nodeCount": "4"`) i zawiera sekcję `"condaDependenciees"`, która zawiera listę pakietów języka Python wymaganych do uruchomienia skryptu szkoleniowego.

Aby uzyskać więcej informacji na temat uruchamiania plików konfiguracji, zobacz [Konfigurowanie elementów docelowych obliczeń i używanie ich do uczenia modelu](how-to-set-up-training-targets.md#create-run-configuration-and-submit-run-using-azure-machine-learning-cli).

Parametr `-t` przechowuje odwołanie do tego przebiegu w pliku JSON i zostanie użyte w następnych krokach w celu zarejestrowania i pobrania modelu.

W miarę przebiegu szkolenia program przesyła strumieniowo informacje z sesji szkoleniowej do zdalnego zasobu obliczeniowego. Część informacji jest podobna do poniższego tekstu:

```text
alpha is 0.80, and mse is 3340.02
alpha is 0.85, and mse is 3349.36
alpha is 0.90, and mse is 3359.09
alpha is 0.95, and mse is 3369.13


The experiment completed successfully. Finalizing run...
Cleaning up all outstanding Run operations, waiting 300.0 seconds
```

Ten tekst jest rejestrowany ze skryptu szkoleniowego (`train-sklearn.py`) i wyświetla dwie metryki wydajności dla tego modelu. W tym przypadku chcemy, aby model miał najwyższą wartość alfa. Metryki wydajności są specyficzne dla modelu, który jest szkoleniowy. Inne modele będą mieć różne metryki wydajności.

W przypadku inspekcji `train-sklearn.py` Zauważ, że używa również wartości alfa, gdy przechowuje przeszkolone modele do pliku. W tym przypadku pociąga za niego kilka modeli. Jeden z najwyższą wartością alfa powinna być najlepszym. Patrząc na dane wyjściowe powyżej, a kod, model z alfa 0,95 został zapisany jako `./outputs/ridge_0.95.pkl`

Model został zapisany w katalogu `./outputs` w miejscu docelowym obliczeń, w którym został przeszkolony. W tym przypadku wystąpienie obliczeniowe Azure Machine Learning w chmurze platformy Azure. Proces uczenia automatycznie przekazuje zawartość katalogu `./outputs` z elementu docelowego obliczeń, w którym szkolenia odbywają się w obszarze roboczym Azure Machine Learning. Jest ona przechowywana w ramach eksperymentu (`myexperiment` w tym przykładzie).

## <a name="register-the-model"></a>Zarejestruj model

Aby zarejestrować model bezpośrednio z wersji zapisanej w eksperymentie, użyj następującego polecenia:

```azurecli-interactive
az ml model register -n mymodel -f runoutput.json --asset-path "outputs/ridge_0.95.pkl" -t registeredmodel.json
```

To polecenie rejestruje plik `outputs/ridge_0.95.pkl` utworzony w ramach szkolenia przebiegu jako nowy model rejestracji o nazwie `mymodel`. @No__t-0 odwołuje się do ścieżki w eksperymentie. W takim przypadku eksperyment i informacje o przebiegu są ładowane z pliku `runoutput.json` utworzonego przez polecenie szkolenia. @No__t-0 tworzy plik JSON, który odwołuje się do nowego zarejestrowanego modelu utworzonego przez to polecenie, i jest używany przez inne polecenia CLI, które działają z zarejestrowanymi modelami.

Dane wyjściowe tego polecenia są podobne do następujących:

```json
{
  "createdTime": "2019-09-19T15:25:32.411572+00:00",
  "description": "",
  "experimentName": "myexperiment",
  "framework": "Custom",
  "frameworkVersion": null,
  "id": "mymodel:1",
  "name": "mymodel",
  "properties": "",
  "runId": "myexperiment_1568906070_5874522d",
  "tags": "",
  "version": 1
}
```

### <a name="model-versioning"></a>Przechowywanie wersji modelu

Zwróć uwagę na numer wersji zwrócony dla modelu. Ta wersja jest zwiększana za każdym razem, gdy rejestrujesz nowy model o tej nazwie. Na przykład możesz pobrać model i zarejestrować go z pliku lokalnego przy użyciu następujących poleceń:

```azurecli-interactive
az ml model download -i "mymodel:1" -t .
az ml model register -n mymodel -p "ridge_0.95.pkl"
```

Pierwsze polecenie pobiera zarejestrowany model do bieżącego katalogu. Nazwa pliku to `ridge_0.95.pkl`, który jest plikiem, do którego odwołuje się po zarejestrowaniu modelu. Drugie polecenie rejestruje model lokalny (`-p "ridge_0.95.pkl"`) o takiej samej nazwie jak w poprzedniej rejestracji (`mymodel`). Tym razem dane JSON zwracają listę wersji jako 2.

## <a name="deploy-the-model"></a>Wdrażanie modelu

Aby wdrożyć model, należy zmienić katalogi na katalog `model-deployment`, a następnie użyć następującego polecenia:

```azurecli-interactive
cd ~/mlops/model-deployment
az ml model deploy -n myservice -m "mymodel:1" --ic inferenceConfig.yml --dc aciDeploymentConfig.yml
```

Może zostać wyświetlony komunikat "nie można utworzyć klienta platformy Docker". Możesz zignorować ten komunikat. Interfejs wiersza polecenia umożliwia wdrożenie usługi sieci Web do lokalnego kontenera platformy Docker i sprawdzenie, czy platforma Docker. W takim przypadku nie korzystamy z lokalnego wdrożenia.

To polecenie służy do wdrażania nowej usługi o nazwie `myservice` przy użyciu wersji 1 modelu, który został wcześniej zarejestrowany.

Plik `inferenceConfig.yml` zawiera informacje dotyczące sposobu wykonywania wnioskowania, takie jak skrypt wejścia (`score.py`) i zależności oprogramowania. Aby uzyskać więcej informacji na temat struktury tego pliku, zobacz [Schemat konfiguracji wnioskowania](reference-azure-machine-learning-cli.md#inference-configuration-schema). Aby uzyskać więcej informacji na temat skryptów wprowadzania, zobacz [Wdrażanie modeli za pomocą usługi Azure Machine Learning](how-to-deploy-and-where.md#prepare-to-deploy).

@No__t-0 opisuje środowisko wdrożenia używane do hostowania usługi. Konfiguracja wdrożenia jest specyficzna dla typu obliczenia używanego na potrzeby wdrożenia. W takim przypadku używane jest wystąpienie kontenera platformy Azure. Aby uzyskać więcej informacji, zobacz [Schemat konfiguracji wdrożenia](reference-azure-machine-learning-cli.md#deployment-configuration-schema).

Ukończenie procesu wdrażania potrwa kilka minut.

> [!TIP]
> W tym przykładzie użyto Azure Container Instances. Wdrożenia do ACI automatycznie tworzą wymagany zasób ACI. Jeśli zamiast tego chcesz przeprowadzić wdrożenie w usłudze Azure Kubernetes Service, należy najpierw utworzyć klaster AKS i określić go jako część polecenia `az ml model deploy`. Aby zapoznać się z przykładem wdrażania do AKS, zobacz [Wdrażanie modelu w klastrze usługi Azure Kubernetes](how-to-deploy-azure-kubernetes-service.md).

Po kilku minutach zwracane są informacje podobne do następującego kodu JSON:

```json
ACI service creation operation finished, operation "Succeeded"
{
  "computeType": "ACI",
  {...ommitted for space...}
  "runtimeType": null,
  "scoringUri": "http://6c061467-4e44-4f05-9db5-9f9a22ef7a5d.eastus2.azurecontainer.io/score",
  "state": "Healthy",
  "tags": "",
  "updatedAt": "2019-09-19T18:22:32.227401+00:00"
}
```

### <a name="the-scoring-uri"></a>Identyfikator URI oceniania

@No__t-0 zwrócony ze wdrożenia to punkt końcowy REST dla modelu wdrożonego jako usługa sieci Web. Ten identyfikator URI można także uzyskać za pomocą następującego polecenia:

```azurecli-interactive
az ml service show -n myservice
```

To polecenie zwraca ten sam dokument JSON, w tym `scoringUri`.

Punkt końcowy REST może służyć do wysyłania danych do usługi. Aby uzyskać informacje na temat tworzenia aplikacji klienckiej, która wysyła dane do usługi, zobacz temat [Korzystanie z modelu Azure Machine Learning wdrożonego jako usługa sieci Web](how-to-consume-web-service.md) .

### <a name="send-data-to-the-service"></a>Wyślij dane do usługi

Chociaż można utworzyć aplikację kliencką do wywoływania punktu końcowego, interfejs wiersza polecenia Machine Learning udostępnia narzędzie, które może działać jako klient testowy. Użyj następującego polecenia, aby wysłać dane testowe do usługi:

```azurecli-interactive
az ml service run -n myservice -d '{"data":[[1,2,3,4,5,6,7,8,9,10]]}'
```

Odpowiedź polecenia jest podobna do `[4684.920839774082]`.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

> [!IMPORTANT]
> Utworzone zasoby mogą być używane jako wstępnie wymagane składniki w innych samouczkach usługi Azure Machine Learning i artykułach z instrukcjami.

### <a name="delete-deployed-service"></a>Usuń wdrożoną usługę

Jeśli planujesz kontynuować korzystanie z obszaru roboczego Azure Machine Learning, ale chcesz pozbyć się wdrożonej usługi w celu obniżenia kosztów, użyj następującego polecenia:

```azurecli-interactive
az ml service delete -n myservice
```

To polecenie zwraca dokument JSON zawierający nazwę usuniętej usługi. Usunięcie usługi może potrwać kilka minut.

### <a name="delete-the-training-compute"></a>Usuń obliczenia szkoleniowe

Jeśli planujesz kontynuowanie korzystania z obszaru roboczego Azure Machine Learning, ale chcesz usunąć element docelowy obliczeń `cpu` utworzonego na potrzeby szkolenia, użyj następującego polecenia:

```azurecli-interactive
az ml computetarget delete -n cpu
```

To polecenie zwraca dokument JSON zawierający identyfikator usuniętego elementu docelowego obliczeń. Usunięcie elementu docelowego obliczeń może potrwać kilka minut.

### <a name="delete-everything"></a>Usuń wszystko

Jeśli nie planujesz używania utworzonych zasobów, usuń je, aby nie nawiązać dodatkowych opłat.

Aby usunąć grupę zasobów i wszystkie zasoby platformy Azure utworzone w tym dokumencie, użyj następującego polecenia. Zastąp `<resource-group-name>` nazwą utworzonej wcześniej grupy zasobów:

```azurecli-interactive
az group delete -g <resource-group-name> -y
```

## <a name="next-steps"></a>Następne kroki

W tym samouczku Azure Machine Learning użyto interfejsu wiersza polecenia usługi Machine Learning do wykonywania następujących zadań:

> [!div class="checklist"]
> * Zainstaluj rozszerzenie uczenia maszynowego
> * Tworzenie obszaru roboczego usługi Azure Machine Learning
> * Utwórz zasób obliczeniowy służący do uczenia modelu
> * Rozpocznij pracę szkoleniową
> * Rejestrowanie i pobieranie modelu
> * Wdróż model jako usługę sieci Web
> * Dane oceny przy użyciu usługi sieci Web

Aby uzyskać więcej informacji na temat korzystania z interfejsu wiersza polecenia, zobacz [Korzystanie z rozszerzenia interfejsu wiersza polecenia dla usługi Azure Machine Learning](reference-azure-machine-learning-cli.md).