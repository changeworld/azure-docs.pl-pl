---
title: Zainstalować i używać interfejsu wiersza polecenia dla najważniejszych zadań — usługi Azure Machine Learning
description: Informacje o sposobie instalowania i używania interfejsu wiersza polecenia dla najbardziej typowych maszyny uczenia zadania w usłudze Azure Machine Learning.
services: machine-learning
author: haining
ms.author: haining
manager: cgronlun
ms.reviewer: mldocs
ms.service: machine-learning
ms.component: desktop-workbench
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/10/2018
ms.openlocfilehash: 4b6ed5c70201b918a5bfa252719b2673303b38e9
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34830864"
---
# <a name="install-and-use-the-machine-learning-cli-for-top-tasks-in-azure-machine-learning"></a>Zainstalować i używać machine learning interfejsu wiersza polecenia dla najważniejszych zadań w usłudze Azure Machine Learning

Usługi uczenie maszynowe Azure są dane zintegrowane, end-to-end nauki i zaawansowane rozwiązania analizy. Analityków Professional danych można użyć usługi Azure Machine Learning Przygotowanie danych, tworzenie eksperymentów i wdrażanie modeli w skali chmury. 

Usługa Azure Machine Learning zapewnia interfejsu wiersza polecenia (CLI), z którego można:
+ Zarządzanie obszaru roboczego i projektów
+ Konfigurowanie celów obliczeń
+ Uruchom eksperymenty szkolenia
+ Wyświetl historię i metryki dla ostatnich przebiegów
+ Wdrażanie modeli w środowisku produkcyjnym jako usługi sieci web
+ Zarządzanie modelami produkcyjnych i usługi

W tym artykule opisano niektóre z najczęściej używanych poleceń interfejsu wiersza polecenia dla Twojej wygody. 

![Azure Machine Learning interfejsu wiersza polecenia](media/cli-for-azure-machine-learning/flow.png)

## <a name="what-you-need-to-get-started"></a>Wymagane do rozpoczęcia pracy

Należy współautora dostępu do subskrypcji platformy Azure lub grupę zasobów, w którym można wdrożyć do modeli. Ponadto musisz zainstalować usługi Azure Machine Learning Workbench uruchomienie interfejsu wiersza polecenia. 

>[!IMPORTANT]
>Interfejs wiersza polecenia oferowane przez usługi Azure Machine Learning różni się od [wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest), który służy do zarządzania zasobami platformy Azure.

## <a name="get-and-start-cli"></a>Pobierz i uruchom interfejsu wiersza polecenia

Aby uzyskać ten interfejs wiersza polecenia, należy zainstalować Azure Machine Learning Workbench, który można pobrać stąd:
    + Windows — https://aka.ms/azureml-wb-msi 
    + System MacOS — https://aka.ms/azureml-wb-dmg 

Aby uruchomić interfejsu wiersza polecenia:
+ W konsoli usługi Azure Machine Learning Workbench, uruchom interfejsu wiersza polecenia z menu **Plik -> Otwórz okno wiersza polecenia.**

## <a name="get-command-help"></a>Uzyskaj pomoc dotyczącą polecenia 

Możesz uzyskać dodatkowe informacje na temat poleceń interfejsu wiersza polecenia przy użyciu `--debug` lub `--help` po poleceń, takich jak `az ml <xyz> --debug` gdzie `<xyz>` to nazwa polecenia. Na przykład:
```azurecli
az ml computetarget --debug 

az ml experiment --help
```

## <a name="common-cli-tasks-for-azure-machine-learning"></a>Typowe zadania uczenia maszynowego Azure CLI 

Dowiedz się więcej o najbardziej typowych zadań, które można wykonać za pomocą interfejsu wiersza polecenia w tej sekcji, w tym:
+ [Konfigurowanie celów obliczeń](#target)
+ [Przesyłanie zadań do wykonania zdalnego](#jobs)
+ [Praca z notesów Jupyter](#jupyter)
+ [Interakcja z historii wykonywania](#history)
+ [Konfigurowanie środowiska, który umożliwia operacjonalizację](#o16n)

<a name="target"></a>

### <a name="set-up-a-compute-target"></a>Konfigurowanie docelowej obliczeń

Trwa uczenie modelu w różnych celów, w tym komputerze można obliczyć:
+ w trybie lokalnym
+ w nauce danych maszyny Wirtualnej (DSVM)
+ w klastrze usługi HDInsight

Aby dołączyć docelowej maszyny Wirtualnej nauki danych:
```azurecli
az ml computetarget attach remotedocker -n <target name> -a <ip address or FQDN> -u <username> -w <password>
``` 

Aby dołączyć obiekt docelowy HDInsight:
```azurecli
az ml computetarget attach cluster -n <target name> -a <cluster name, e.g. myhdicluster-ssh.azurehdinsight.net> -u <ssh username> -w <ssh password>
```

W ramach **aml_config** folderu, można zmienić conda zależności. 

Ponadto można operować PySpark, Python lub Python w DSVM procesora GPU. 

Aby zdefiniować Python tryb działania:
+ Dla języka Python, Dodaj `Framework:Python` w `<target name>.runconfig` 

+ PySpark, można dodać `Framework:PySpark` w `<target name>.runconfig` 

+ Dla języka Python w DSVM procesora GPU
    1. Dodaj `Framework:Python` w `<target name>.runconfig` 

    1. Ponadto Dodaj `baseDockerImage: microsoft/mmlspark:plus-gpu-0.9.9 and nvidiaDocker:true` w `<target name>.compute`

Aby przygotować docelowy obliczeniowych:
```azurecli
az ml experiment prepare -c <target name>
```

>[!TIP]
>Aby wyświetlić Twojej subskrypcji:<br/>
>`az account show`<br/>
>
>Aby ustawić subskrypcję:<br/>
>`az account set –s "my subscription name" `

<a name="jobs"></a>

### <a name="submit-remote-jobs"></a>Przesyłanie zadań zdalnego

Aby przesłać zadanie dla zdalnego miejsca docelowego:
```azurecli
az ml experiment submit -c <target name> myscript.py
```

<a name="jupyter"></a>

### <a name="work-with-jupyter-notebooks"></a>Praca z notesów Jupyter

Aby uruchomić notesu Jupyter:
```azurecli
az ml notebook start
```

To polecenie uruchamia notesu Jupyter w localhost. Pracy lokalnego wybierając jądra Python 3 lub działać w zdalnej maszyny Wirtualnej, wybierając jądra `<target name>`.

<a name="history"></a>

### <a name="interact-with-and-explore-the-run-history"></a>Interakcyjnie i badanie historii wykonywania

Aby wyświetlić listę historii uruchamiania:
```azurecli
az ml history list -o table
```

Aby wyświetlić listę wszystkich ukończone działa:
```azurecli
az ml history list --query "[?status=='Completed']" -o table
```

Aby znaleźć działa z najlepszym dokładność:
```azurecli
az ml history list --query "@[?Accuracy != null] | max_by(@, &Accuracy).Accuracy"
```

Możesz również pobrać pliki generowane przy każdym uruchomieniu. 

Aby promować modelu, który jest zapisywany w folderze dane wyjściowe:
```azurecli
az ml history promote -r <run id> -ap outputs/model.pkl -n <model name>
```

Aby pobrać modelu:
```azurecli
az ml asset download -l assets/model.pkl.link -d <model folder path>
```

<a name="o16n"></a>

### <a name="configure-your-environment-to-operationalize"></a>Skonfiguruj środowisko do operacjonalizacji

Aby skonfigurować środowisko operationalization, należy utworzyć:

> [!div class="checklist"]
> * Grupy zasobów 
> * Konto magazynu
> * Rejestru kontenera platformy Azure (ACR)
> * Konto szczegółowe informacje o aplikacji
> * Wdrożenie Kubernetes w klastrze usługi kontenera platformy Azure (ACS)


Aby zdefiniować wdrożenia lokalnego przy testowaniu w kontenerze Docker
```azurecli
az ml env setup -l <region, e.g. eastus2> -n <env name> -g <resource group name>
```

Aby skonfigurować klaster usług ACS z Kubernetes:
```azurecli
az ml env setup -l <region, e.g. eastus2> -n <env name> -g <resource group name> --cluster
```

Aby monitorować stan wdrożenia:
```azurecli
az ml env show -n <environment name> -g <resource group name>
```

Aby ustawić środowiska, który ma być używany:
```azurecli
az ml env set -n <environment name> -g <resource group name>
```

## <a name="next-steps"></a>Kolejne kroki

Rozpoczynanie pracy z jednym z tych artykułów: 
+ [Zainstaluj i uruchom przy użyciu usługi Azure Machine Learning](../service/quickstart-installation.md)
+ [Samouczek danych Iris klasyfikującego: Część 1](tutorial-classifying-iris-part-1.md)

Szczegółowej analizy jeden z tych artykułach:
+ [Polecenia interfejsu wiersza polecenia do zarządzania modelami](model-management-cli-reference.md)
