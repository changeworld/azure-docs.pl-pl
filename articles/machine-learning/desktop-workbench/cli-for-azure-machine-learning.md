---
title: Instalowanie i używanie interfejsu wiersza polecenia dla najważniejszych zadań — Azure Machine Learning
description: Dowiedz się, jak zainstalować i używać interfejsu wiersza polecenia dla maszyny najbardziej typowe zadania w usłudze Azure Machine Learning uczenia.
services: machine-learning
author: haining
ms.author: haining
manager: cgronlun
ms.reviewer: mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/10/2018
ROBOTS: NOINDEX
ms.openlocfilehash: 06e85845d41b240638a5b5b4d75d64fd460a99bf
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46953331"
---
# <a name="install-and-use-the-machine-learning-cli-for-top-tasks-in-azure-machine-learning"></a>Instalowanie i używanie machine learning interfejs wiersza polecenia dla najważniejszych zadań w usłudze Azure Machine Learning

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)]

Usługa Azure Machine Learning to zintegrowane, end-to-end danych do analizy i zaawansowanego rozwiązania do analizy. Profesjonalnych analityków, można użyć usługi Azure Machine Learning do przygotowywania danych, opracowywania eksperymentów i wdrażania modeli na skalę chmury. 

Usługa Azure Machine Learning udostępnia interfejs wiersza polecenia (CLI) za pomocą którego można:
+ Zarządzaj obszaru roboczego i projektów
+ Konfigurowanie celów obliczeń
+ Uruchamiaj eksperymenty szkolenia
+ Wyświetl historię i metryki dla ostatnich przebiegów
+ Wdrażać modele do produkcji jako usług sieci web
+ Zarządzanie modelami w środowisku produkcyjnym i usług

W tym artykule opisano niektóre z najczęściej używanych poleceń interfejsu wiersza polecenia dla Twojej wygody. 

![Usługi Azure Machine Learning interfejs wiersza polecenia](media/cli-for-azure-machine-learning/flow.png)

## <a name="what-you-need-to-get-started"></a>Wymagane do rozpoczęcia pracy

Potrzebny jest dostęp współpracowników do subskrypcji platformy Azure lub grupę zasobów, w którym można wdrożyć swoje modele. Ponadto należy zainstalować aplikację Azure Machine Learning Workbench, aby uruchomić interfejs wiersza polecenia. 

>[!IMPORTANT]
>Interfejs wiersza polecenia są dostarczane z usługą Azure Machine Learning, usługi różni się od [wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest), który jest używany do zarządzania zasobami platformy Azure.

## <a name="get-and-start-cli"></a>Pobierz i uruchom interfejs wiersza polecenia

Aby uzyskać ten interfejs wiersza polecenia, należy zainstalować program Azure Machine Learning Workbench, który można pobrać w tym miejscu:
    + Windows — https://aka.ms/azureml-wb-msi 
    + MacOS — https://aka.ms/azureml-wb-dmg 

Aby uruchomić interfejs wiersza polecenia:
+ W aplikacji Azure Machine Learning Workbench Uruchom interfejs wiersza polecenia z menu **Plik -> Otwórz wiersz polecenia.**

## <a name="get-command-help"></a>Uzyskaj pomoc dla polecenia 

Możesz uzyskać dodatkowe informacje o przy użyciu interfejsu wiersza polecenia `--debug` lub `--help` po poleceniach, takie jak `az ml <xyz> --debug` gdzie `<xyz>` to nazwa polecenia. Na przykład:
```azurecli
az ml computetarget --debug 

az ml experiment --help
```

## <a name="common-cli-tasks-for-azure-machine-learning"></a>Typowe zadania interfejsu wiersza polecenia platformy Azure Machine Learning 

Dowiedz się więcej o najbardziej typowych zadań, które można wykonywać za pomocą interfejsu wiersza polecenia w tej sekcji, w tym:
+ [Konfigurowanie celów obliczeń](#target)
+ [Przesyłanie zadań do wykonania zdalnego](#jobs)
+ [Praca z notesami Jupyter](#jupyter)
+ [Interakcja z uruchomień](#history)
+ [Konfigurowanie środowiska do obsługi operacji](#o16n)

<a name="target"></a>

### <a name="set-up-a-compute-target"></a>Konfigurowanie obliczeniowego elementu docelowego

Można obliczyć w modelu w różnych docelowych, w tym uczenia maszynowego:
+ w trybie lokalnym
+ w zakresie analizy danych maszyny Wirtualnej (DSVM)
+ w klastrze usługi HDInsight

Aby dołączyć docelowej maszyny Wirtualnej do nauki o danych:
```azurecli
az ml computetarget attach remotedocker -n <target name> -a <ip address or FQDN> -u <username> -w <password>
``` 

Aby dołączyć obiekt docelowy HDInsight:
```azurecli
az ml computetarget attach cluster -n <target name> -a <cluster name, e.g. myhdicluster-ssh.azurehdinsight.net> -u <ssh username> -w <ssh password>
```

W ramach **aml_config** folderu, możesz zmienić zależności conda. 

Ponadto mogą działać z PySpark, Python lub język Python w procesorze GPU maszyna DSVM. 

Aby zdefiniować tryb działania języka Python:
+ Dla języka Python, Dodaj `Framework:Python` w `<target name>.runconfig` 

+ PySpark, można dodać `Framework:PySpark` w `<target name>.runconfig` 

+ Dla języka Python w procesorze GPU maszyna DSVM
    1. Dodaj `Framework:Python` w `<target name>.runconfig` 

    1. Ponadto Dodaj `baseDockerImage: microsoft/mmlspark:plus-gpu-0.9.9 and nvidiaDocker:true` w `<target name>.compute`

Aby przygotować obliczeniowego elementu docelowego:
```azurecli
az ml experiment prepare -c <target name>
```

>[!TIP]
>Aby pokazać Twojej subskrypcji:<br/>
>`az account show`<br/>
>
>Aby ustawić subskrypcję:<br/>
>`az account set –s "my subscription name" `

<a name="jobs"></a>

### <a name="submit-remote-jobs"></a>Przesyłanie zadań zdalnego

Aby przesłać zadanie do zdalnego obiektu docelowego:
```azurecli
az ml experiment submit -c <target name> myscript.py
```

<a name="jupyter"></a>

### <a name="work-with-jupyter-notebooks"></a>Praca z notesami Jupyter

Aby uruchomić Notes Jupyter:
```azurecli
az ml notebook start
```

To polecenie uruchamia notesu programu Jupyter w localhost. Pracowanie z lokalnego, wybierając jądra Python 3 lub pracować na zdalnej maszynie wirtualnej, wybierając jądra `<target name>`.

<a name="history"></a>

### <a name="interact-with-and-explore-the-run-history"></a>Korzystaj z i zapoznaj się z historii uruchamiania

Aby wyświetlić historię uruchamiania:
```azurecli
az ml history list -o table
```

Aby wyświetlić listę wszystkich zakończone przebiegi:
```azurecli
az ml history list --query "[?status=='Completed']" -o table
```

Aby znaleźć działa z najlepszą dokładnością:
```azurecli
az ml history list --query "@[?Accuracy != null] | max_by(@, &Accuracy).Accuracy"
```

Można również pobrać pliki generowane przez każdego uruchomienia. 

Aby promować modelu, który jest zapisywany w folderze danych wyjściowych:
```azurecli
az ml history promote -r <run id> -ap outputs/model.pkl -n <model name>
```

Aby pobrać tego modelu:
```azurecli
az ml asset download -l assets/model.pkl.link -d <model folder path>
```

<a name="o16n"></a>

### <a name="configure-your-environment-to-operationalize"></a>Skonfiguruj środowisko do obsługi operacji

Aby skonfigurować swoje środowisko obsługi operacji, należy utworzyć:

> [!div class="checklist"]
> * Grupa zasobów 
> * Konto magazynu
> * Rejestr Azure Container Registry (ACR)
> * Konto szczegółowe informacje o aplikacji
> * Wdrożenie rozwiązania Kubernetes w klastrze usługi Azure Container Service (ACS)


Aby skonfigurować lokalne wdrożenie do testowania w kontenerze platformy Docker:
```azurecli
az ml env setup -l <region, e.g. eastus2> -n <env name> -g <resource group name>
```

Aby skonfigurować klaster usługi ACS z rozwiązaniem Kubernetes:
```azurecli
az ml env setup -l <region, e.g. eastus2> -n <env name> -g <resource group name> --cluster
```

Aby monitorować stan wdrożenia:
```azurecli
az ml env show -n <environment name> -g <resource group name>
```

Aby ustawić środowisko, które mają być używane:
```azurecli
az ml env set -n <environment name> -g <resource group name>
```

## <a name="next-steps"></a>Kolejne kroki

Rozpoczynanie pracy z jednym z następujących artykułów: 
+ [Instalowanie i rozpocząć korzystanie z usługi Azure Machine Learning](quickstart-installation.md)
+ [Klasyfikowanie irysów danych samouczek: Część 1](tutorial-classifying-iris-part-1.md)

Szczegółowe informacje z jednym z następujących artykułów:
+ [Polecenia interfejsu wiersza polecenia do zarządzania modelami](model-management-cli-reference.md)
