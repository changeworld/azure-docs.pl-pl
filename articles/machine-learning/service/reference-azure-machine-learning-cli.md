---
title: Rozszerzenie interfejsu wiersza polecenia usługi Azure Machine Learning — informacje
description: Więcej informacji na temat machine learning rozszerzenie interfejsu wiersza polecenia dla usługi Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: reference
ms.reviewer: jmartens
ms.author: jordane
author: jpe316
ms.date: 09/24/2018
ms.openlocfilehash: 45ed1867d6d151250340bb21450b4b0d9b00e993
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2018
ms.locfileid: "51243151"
---
# <a name="what-is-the-azure-machine-learning-cli"></a>Co to jest interfejsu wiersza polecenia usługi Azure Machine Learning?

Rozszerzenie usługi Azure Machine Learning interfejs wiersza polecenia (CLI) jest dla deweloperów korzystających z usługi Azure Machine Learning i analityków danych. Umożliwia ona szybko Automatyzuj przepływy pracy programu machine learning i umieść je w środowisku produkcyjnym, takich jak:
+ Uruchamianie eksperymentów w celu tworzenia modeli uczenia maszynowego

+ Zarejestruj modele uczenia maszynowego do użycia przez klientów

+ Pakowanie, wdrażanie i śledzenia w cyklu życia modeli usługi machine learning

Tym interfejsu wiersza polecenia platformy uczenia maszynowego jest rozszerzeniem [wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) i został zbudowany na podstawie oparta na środowisku Python <a href="https://aka.ms/aml-sdk" target="_blank">SDK</a> dla usługi Azure Machine Learning.

> [!NOTE]
> Interfejs wiersza polecenia jest obecnie dostępna w wczesną wersję zapoznawczą i zostaną zaktualizowane.

## <a name="installing-and-uninstalling"></a>Instalowanie i odinstalowywanie

Możesz zainstalować interfejs wiersza polecenia za pomocą tego polecenia w naszym indeksie PyPi w wersji zapoznawczej:
```AzureCLI
az extension add -s https://azuremlsdktestpypi.blob.core.windows.net/wheels/sdk-release/Preview/E7501C02541B433786111FE8E140CAA1/azure_cli_ml-0.1.68-py2.py3-none-any.whl --pip-extra-index-urls  https://azuremlsdktestpypi.azureedge.net/sdk-release/Preview/E7501C02541B433786111FE8E140CAA1
```

Możesz usunąć za pomocą tego polecenia interfejsu wiersza polecenia:
```AzureCLI
az extension remove -n azure-cli-ml
```

Można zaktualizować wiersza polecenia przy użyciu **Usuń** i **Dodaj** powyższych kroków.

## <a name="using-the-cli-vs-the-sdk"></a>Przy użyciu interfejsu wiersza polecenia lub zestawu SDK
Interfejs wiersza polecenia jest lepiej nadaje się do usługi automation przez osobę operacji deweloperskich lub jako część potoku ciągłej integracji i dostarczania. Jest zoptymalizowany do obsługi zadań rzadkich i wysoce sparametryzowanych. 

Przykłady:
- obliczenia, inicjowanie obsługi administracyjnej
- przesyłanie eksperymentu sparametryzowane
- Rejestracja modelu, tworzenie obrazu
- Wdrażanie usługi

Analitycy danych, zaleca się używania zestawu SDK usługi Azure ML.

## <a name="common-machine-learning-cli-commands"></a>Typowe usługi machine learning poleceń interfejsu wiersza polecenia
> [!NOTE]
> Przykładowe pliki, można użyć, aby pomyślnie wykonać poniższych poleceń można znaleźć [tutaj.](https://github.com/Azure/MachineLearningNotebooks/tree/cli/cli)

Bogaty zestaw `az ml` polecenia, aby korzystać z usługi w dowolnym środowisku wiersza polecenia, w tym Azure portal usługa cloud shell.

Poniżej przedstawiono przykładowe Typowe polecenia:

### <a name="workspace-creation--compute-setup"></a>Konfiguracja tworzenia & obliczeń obszaru roboczego

+ Tworzenie usługi Azure Machine Learning service obszaru roboczego, zasób najwyższego poziomu dla usługi machine learning.
   ```AzureCLI
   az ml workspace create -n myworkspace -g myresourcegroup
   ```

+ Ustaw interfejs wiersza polecenia i domyślnie używają tego obszaru roboczego.
   ```AzureCLI
   az configure --defaults aml_workspace=myworkspace group=myresourcegroup
   ```

+ Tworzenie maszyny wirtualnej DSVM (maszyny Wirtualnej analizy danych). Można również utworzyć klastrów BatchAI dla rozproszonego szkolenia lub klastry usługi AKS na potrzeby wdrożenia.
  ```AzureCLI
  az ml computetarget setup dsvm -n mydsvm
  ```

### <a name="experiment-submission"></a>Przesyłanie eksperymentu
+ Dołącz do projektu (konfiguracji uruchamiania) przesyłania eksperymentu. Służy do śledzenia przebiegów eksperymentu.
  ```AzureCLI
  az ml project attach --experiment-name myhistory
  ```

+ Przysyłanie eksperymentu korzystająca z usługi Azure Machine Learning w elemencie docelowym obliczeniowych wybranych przez użytkownika. W tym przykładzie będą wykonywane względem środowisku obliczeniowym lokalnego. Upewnij się, że plik środowiska conda przechwytuje zależności języka python.

  ```AzureCLI
  az ml run submit -c local train.py
  ```

+ Wyświetlanie listy przesłanych eksperymentów.
```AzureCLI
az ml history list
```

### <a name="model-registration-image-ceation--deployment"></a>Rejestracja modelu, ceation obrazu i wdrożenie

+ Zarejestruj model za pomocą usługi Azure Machine Learning.
  ```AzureCLI
  az ml model register -n mymodel -m sklearn_regression_model.pkl
  ```

+ Utwórz obraz zawiera Twoje modelu uczenia maszynowego i zależności. 
  ```AzureCLI
  az ml image create container -n myimage -r python -m mymodel:1 -f score.py -c myenv.yml
  ```

+ Spakowane model jest wdrażany elementów docelowych, łącznie z usługami ACI i AKS.
  ```AzureCLI
  az ml service create aci -n myaciservice --image-id myimage:1
  ```
    
## <a name="full-command-list"></a>Pełne polecenie listy
Rozszerzenie interfejsu wiersza polecenia (i ich obsługiwanych parametrów) można znaleźć pełną listę poleceń, uruchamiając ```az ml COMMANDNAME -h```. 
