---
title: Za pomocą narzędzi Visual Studio Code dla sztucznej Inteligencji rozszerzenia za pomocą usługi Azure Machine Learning
description: Więcej informacji na temat narzędzia Visual Studio Code dla sztucznej Inteligencji i Rozpocznij szkolenie i wdrażanie usługi machine learning i głębokiego uczenia modeli za pomocą usługi Azure Machine Learning w programie VS Code.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: jmartens
author: j-martens
ms.reviewer: jmartens
ms.date: 10/1/2018
ms.openlocfilehash: 6372aada8c3d380f8d3cefb0479e45bbd7c63898
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2018
ms.locfileid: "49945250"
---
# <a name="vs-code-tools-for-ai-train-and-deploy-ml-models-from-vs-code"></a>Program VS Code Tools for AI: szkolenie i wdrażać modele uczenia Maszynowego z programu VS Code
W tym artykule nauczysz się używać **programu VS Code Tools for AI** rozszerzenia uczenie i wdrażanie usługi machine learning i głębokiego uczenia modeli za pomocą usługi Azure Machine Learning w programie VS Code.

Usługa Azure Machine Learning zapewnia obsługę przeprowadzania eksperymentów, lokalnie i na zdalnym obliczeniowych elementów docelowych. Dla każdego eksperymentu można śledzenie bieżącego wielu uruchomień na potrzeby często iteracyjne spróbuj różnych technik, hiperparametrów i inne. Usługa Azure Machine Learning służy do śledzenia niestandardowe metryki i eksperymentować przebiegów, umożliwiając odtwarzaniem do nauki o danych i sprawdzalność.

I wdrożeniem tych modeli do potrzeb testowania i produkcji.

## <a name="prerequisites"></a>Wymagania wstępne

+ [Masz narzędzi programu VS Code dla sztucznej Inteligencji](how-to-vscode-tools.md) konfigurowania usługi Azure Machine Learning.

+ Masz [Azure Machine Learning SDK for język Python jest zainstalowany](how-to-vscode-tools.md) z programem VS Code.

+ Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-and-manage-compute-targets"></a>Tworzenie i zarządzanie nimi obliczeniowych elementów docelowych

Przy użyciu narzędzia Visual Studio Code dla sztucznej Inteligencji Przygotuj dane, uczyć modele i wdrażania ich zarówno lokalnie, a na zdalnym obliczeniowych elementów docelowych.

To rozszerzenie obsługuje kilka elementów docelowych obliczeniowej zdalnego dla usługi Azure Machine Learning. Zobacz [pełną listę obsługiwanych obliczeniowych elementów docelowych](how-to-set-up-training-targets.md) dla usługi Azure Machine Learning.

### <a name="create-compute-targets-for-azure-machine-learning-in-vs-code"></a>Tworzenie obliczeniowych elementów docelowych dla usługi Azure Machine Learning w programie VS Code

**Aby utworzyć cel obliczenia:**

1. Kliknij ikonę platformy Azure, w pasku działań programu Visual Studio Code. Azure: Pasku bocznym usługi Machine Learning jest wyświetlana.

2. W widoku drzewa rozwiń Twojej subskrypcji platformy Azure i obszar roboczy usługi Azure Machine Learning. W animowany obraz 'OpenMind Studio' jest nazwa subskrypcji i obszar roboczy jest "MyWorkspace". 

3. W węźle obszaru roboczego kliknij prawym przyciskiem myszy **obliczenia** węzeł i wybierz polecenie **Tworzenie obliczeń**.

4. Wybierz typ docelowy obliczeń z listy. 

5. W polu wprowadź unikatową nazwę dla tego celu obliczeń i określ rozmiar maszyny wirtualnej.

6. Określ wszelkie właściwości zaawansowane w pliku konfiguracji JSON, który zostanie otwarty na nowej karcie. 

7. Po zakończeniu konfigurowania obliczeniowego elementu docelowego, kliknij przycisk **Zakończ** w prawym dolnym rogu.

Oto przykład dla usługi Azure Batch AI: [ ![Tworzenie usługi Azure Batch AI obliczeń w programie VS Code](./media/vscode-tools-for-ai/createcompute.gif)](./media/vscode-tools-for-ai/createcompute.gif#lightbox)

### <a name="use-remote-computes-for-experiments-in-vs-code"></a>Użyj zdalnego obliczeń dla eksperymentów w programie VS Code

Aby używać zdalnego obliczeniowego elementu docelowego podczas szkolenia, musisz utworzyć plik konfiguracji przebiegu. Ten plik nakazuje usługi Azure Machine Learning nie tylko miejsca uruchomić eksperyment, ale również, jak przygotować środowisko.

#### <a name="the-run-configuration-file"></a>Plik "konfiguracji uruchamiania"

Rozszerzenie programu VS Code automatycznie utworzy konfiguracji uruchamiania dla Twojego **lokalnego** i **docker** środowisk na komputerze lokalnym.

Jest to fragment, domyślny plik konfiguracji przebiegu.

Jeśli chcesz zainstalować wszystkie biblioteki/zależności samodzielnie ustawić `userManagedDependencies: True` a następnie przebiegów eksperymentu lokalny Użyj domyślnym środowiskiem Python określonych przez rozszerzenie programu VS Code w języku Python.

```yaml
# user_managed_dependencies=True indicates that the environment will be user managed. False indicates that AzureML will manage the user environment.
    userManagedDependencies: False
# The python interpreter path
    interpreterPath: python
# Path to the conda dependencies file to use for this run. If a project
# contains multiple programs with different sets of dependencies, it may be
# convenient to manage those environments with separate files.
    condaDependenciesFile: aml_config/conda_dependencies.yml
# Docker details
    docker:
# Set True to perform this run inside a Docker container.
    enabled: false
```

#### <a name="the-conda-dependencies-file"></a>Pliku zależności conda

Domyślnie zostanie utworzony nowy środowiska conda i zależności instalacji są zarządzane. Jednakże, należy określić zależności w `aml_config/conda_dependencies.yml` pliku.

Jest to fragment z domyślną aml_config/conda_dependencies.yml.
Możesz dodać dodatkowe zależności w pliku konfiguracji.

```yaml
# The dependencies defined in this file will be automatically provisioned for runs with userManagedDependencies=False.

name: project_environment
dependencies:
  # The python interpreter version.

  # Currently Azure ML only supports 3.5.2 and later.

- python=3.6.2

- pip:
    # Required packages for AzureML execution, history, and data preparation.

  - --index-url https://azuremlsdktestpypi.azureedge.net/sdk-release/Preview/E7501C02541B433786111FE8E140CAA1
  - --extra-index-url https://pypi.python.org/simple
  - azureml-defaults

```

## <a name="train-and-tune-models"></a>Szkolenie i dostrajanie modeli

Usługa Azure Machine Learning z programu VS Code umożliwia szybkie powtarzanie czynności w kodzie, krok po kroku i debugowania i użyć rozwiązania kontroli kodu źródłowego wyboru. 

**Aby uruchomić eksperyment w usłudze Azure Machine Learning:**

1. Kliknij ikonę platformy Azure, w pasku działań programu Visual Studio Code. Azure: Pasku bocznym usługi Machine Learning jest wyświetlana.

1. W widoku drzewa rozwiń Twojej subskrypcji platformy Azure i obszar roboczy usługi Azure Machine Learning. W animowany obraz 'OpenMind Studio' jest nazwa subskrypcji i obszar roboczy jest "MyWorkspace". 

1. W węźle obszaru roboczego rozwiń **obliczenia** węzła i kliknij prawym przyciskiem myszy **konfiguracji uruchamiania** obliczeń, który chcesz użyć. 

1. Wybierz **uruchamiania eksperymentu**.

1. Kliknij przycisk **uruchamiania eksperymentu widoku** się zintegrowane portalu usługi Azure Machine Learning w celu monitorowania przebiegów i zobacz wytrenowane modele.

   [![Uruchamianie eksperymentu usługi machine learning z programu VS Code](./media/vscode-tools-for-ai/runexperiment.gif)](./media/vscode-tools-for-ai/runexperiment.gif#lightbox)

## <a name="deploy-and-manage-models"></a>Wdrażanie modeli i zarządzania nimi
Usługę Azure Machine Learning umożliwia wdrażanie i zarządzanie nimi modeli w chmurze i na urządzeniach brzegowych usługi machine learning. 

### <a name="register-your-model-to-azure-machine-learning-from-vs-code"></a>Zarejestruj swój model usługi Azure Machine Learning z programu VS Code

Teraz, gdy masz uczony model, należy zarejestrować go w obszarze roboczym.
Zarejestrowane modele można śledzić i wdrożony.

**Aby zarejestrować model:**

1. Kliknij ikonę platformy Azure, w pasku działań programu Visual Studio Code. Azure: Pasku bocznym usługi Machine Learning jest wyświetlana.

1. W widoku drzewa rozwiń Twojej subskrypcji platformy Azure i obszar roboczy usługi Azure Machine Learning.

1. W węźle obszaru roboczego kliknij prawym przyciskiem myszy **modeli** i wybierz polecenie **zarejestrować Model**.

1. Z listy wybierz, czy chcesz przekazać **pliku modelu** (dla pojedynczego modeli) **folder modelu** (w przypadku modeli z wielu plików, takich jak Tensorflow). 

1. Użyj okna wyboru pliku, aby wybrać plik lub folder.

   [![Obliczenia](./media/vscode-tools-for-ai/registermodel.gif)](./media/vscode-tools-for-ai/registermodel.gif#lightbox)

> [!Warning]
> Teraz Usuń tagi z pliku json wygenerowany.

### <a name="deploy-your-service-from-vs-code"></a>Wdrażanie usługi z programu VS Code

Program VS Code można wdrożyć usługę sieci web w taki sposób, aby:
+ Wystąpienia kontenerów platformy Azure (ACI): do testowania
+ Usługa Azure Kubernetes Service (AKS): w środowisku produkcyjnym 

Nie trzeba utworzyć kontener usługi ACI, aby przetestować z wyprzedzeniem, ponieważ są one tworzone na bieżąco. Jednak klastrów AKS należy wcześniej skonfigurować. 

Dowiedz się więcej o [wdrażanie za pomocą usługi Azure Machine Learning](how-to-deploy-and-where.md) ogólnie rzecz biorąc.

**Wdrażanie usługi sieci web:**

1. Kliknij ikonę platformy Azure, w pasku działań programu Visual Studio Code. Azure: Pasku bocznym usługi Machine Learning jest wyświetlana.

1. W widoku drzewa rozwiń subskrypcji platformy Azure i obszaru roboczego usługi Azure Machine Learning.

1. W obszarze węzła obszaru roboczego, należy rozwinąć **modeli** węzła.

1. Kliknij prawym przyciskiem myszy modelu, który chcesz wdrożyć, a następnie wybierz **wdrażanie usługi z modelu zarejestrowany** polecenia z menu kontekstowego.

1. W palecie poleceń programu VS Code wybierz obliczeniowego elementu docelowego, do której ma zostać wdrożony na liście. 

1. W polu wprowadź nazwę dla tej usługi. 

1. W oknie dialogowym w prawym dolnym rogu kliknij **Przeglądaj** i wybierz skrypt oceniania. Zamyka okno dialogowe.

1. Jeśli masz lokalnego pliku platformy Docker, kliknij przycisk **Przeglądaj** w zostanie wyświetlone drugie okno dialogowe. 

   Jeśli okno dialogowe anulowania, nie określaj lokalnego pliku platformy Docker "Azure Machine Learning" jeden jest używany domyślnie.

1. W trzecim zostanie wyświetlone okno dialogowe, kliknij przycisk **Przeglądaj** i wybierz ścieżkę pliku lokalnego conda, lub wprowadź ścieżkę pliku w edytorze json później.

1. Jeśli masz plik schema.json chcesz użyć, kliknij przycisk **Przeglądaj** w oknie dialogowym czwarty, które zostanie wyświetlone, a następnie wybierz plik.

Teraz jest wdrożona usługa sieci web.

Oto przykład dla wystąpienia kontenera platformy Azure: [ ![wystąpienia kontenera platformy Azure z programu VS Code](./media/vscode-tools-for-ai/deploy.gif)](./media/vscode-tools-for-ai/deploy.gif#lightbox)

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać omówienie szkolenie przy użyciu usługi Machine Learning spoza programu VS Code, przeczytaj [samouczek: uczenia modeli za pomocą usługi Azure Machine Learning](tutorial-train-models-with-aml.md).

Omówienie edytowania, uruchamiania i debugowania kodu lokalnie, zobacz [samouczek języka Python Hello World](https://code.visualstudio.com/docs/python/python-tutorial)
