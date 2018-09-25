---
title: Konfigurowanie środowiska deweloperskiego dla usługi Azure Machine Learning | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować środowisko programowania, podczas pracy z usługą Azure Machine Learning. W tym dokumencie Dowiedz się, jak używać środowisk Conda, Utwórz pliki konfiguracyjne i skonfiguruj notesów programu Jupyter, notesy platformy Azure, środowiska IDE, edytory kodu i maszyna wirtualna do nauki o danych.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.reviewer: larryfr
manager: cgronlun
ms.topic: conceptual
ms.date: 8/6/2018
ms.openlocfilehash: 7796accffb7041e567c5e18857d09e105b5268ce
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46961573"
---
# <a name="how-to-configure-a-development-environment-for-the-azure-machine-learning-service"></a>Jak skonfigurować środowisko projektowe służące do usługi Azure Machine Learning

Informacje dotyczące konfigurowania środowiska deweloperskiego do pracy z usługą Azure Machine Learning. Dowiesz się jak, utworzyć plik konfiguracji, które kojarzy środowiska z obszaru roboczego usługi Azure Machine Learning. Także przedstawiono sposób konfigurowania następujących środowisk programowania:

* Notesy Jupyter na komputerze lokalnym
* Visual Studio Code
* Ulubionego edytora kodu

Zalecanym podejściem jest użycie pakietu Anaconda firmy Continuum [wirtualnych środowisk conda](https://conda.io/docs/user-guide/tasks/manage-environments.html) do izolowania środowiska pracy w celu uniknięcia konfliktów zależności między pakietami. W tym artykule przedstawiono kroki konfigurowania środowiska conda i używać go do usługi Azure Machine Learning.


## <a name="prerequisites"></a>Wymagania wstępne

* Obszar roboczy usługi Azure Machine Learning. Aby go utworzyć, użyj kroków w [wprowadzenie do usługi Azure Machine Learning](quickstart-get-started.md) dokumentu.

* [Anaconda firmy continuum](https://www.anaconda.com/download/) lub [Miniconda](https://conda.io/miniconda.html) Menedżera pakietów.

 * W środowisku Visual Studio Code [rozszerzenie języka Python](https://code.visualstudio.com/docs/python/python-tutorial).

## <a name="create-workspace-configuration-file"></a>Utwórz plik konfiguracji obszaru roboczego

Plik konfiguracji obszaru roboczego jest używana przez zestaw SDK do komunikowania się z obszarem roboczym usługi Azure Machine Learning.  Istnieją dwa sposoby uzyskania tego pliku:

* Po zakończeniu [Szybki Start](quickstart-get-started.md), plik `config.json` zostanie utworzony w notesach platformy Azure.  Ten plik zawiera informacje o konfiguracji dla obszaru roboczego.  Pobierz ją w tym samym katalogu co skryptów lub notesów, które ją przywołują.

* Utwórz plik konfiguracyjny samodzielnie z następujących czynności:

    1. Otwieranie obszaru roboczego w [witryny Azure portal](https://portal.azure.com). Kopiuj __nazwa obszaru roboczego__, __grupy zasobów__, i __identyfikator subskrypcji__. Te wartości są używane do tworzenia pliku konfiguracji.

       Pulpit nawigacyjny z obszaru roboczego portalu jest obsługiwana na tylko w przeglądarkach Edge, Chrome i Firefox.
    
        ![Azure Portal](./media/how-to-configure-environment/configure.png) 
    
    3. W edytorze tekstów Utwórz plik o nazwie **config.json**.  Dodaj następującą zawartość do tego pliku, wstawianie wartości z portalu:
    
        ```json
        {
        "subscription_id": "<subscription-id>",
        "resource_group": "<resource-group>",
        "workspace_name": "<workspace-name>"
        }
        ```
    
        >[!NOTE] 
        >W dalszej części kodu możesz przeczytać ten plik przy użyciu:  `ws = Workspace.from_config()`
    
    4. Pamiętaj zapisać **config.json** w tym samym katalogu co skryptów lub notesów, które ją przywołują.
    
## <a name="azure-notebooks-and-data-science-virtual-machine"></a>Notesy platformy Azure oraz maszynę wirtualną do nauki o danych

Azure notesów i maszyn wirtualnych usługi Azure Data Science dsvm (dystrybucji) są wstępnie skonfigurowane do pracy z usługą Azure Machine Learning. Wymagane składniki, takie jak Azure Machine Learning zestawu SDK są wstępnie zainstalowane w tych środowiskach.

Notesy platformy Azure to usługa notesu Jupyter w chmurze platformy Azure. Maszyna wirtualna do nauki o danych jest obraz maszyny Wirtualnej, który jest wstępnie skonfigurowana do pracy do analizy danych. Maszyna wirtualna zawiera popularne narzędzia, środowisk IDE i pakietów, takich jak notesów programu Jupyter, platformy PyCharm i Tensorflow.

Nadal obowiązują plik konfiguracji obszaru roboczego, aby użyć tych środowisk.

Aby uzyskać więcej informacji na maszynach wirtualnych do nauki o danych, zobacz [maszyn wirtualnych do nauki o danych](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/) dokumentacji.

Przykład za pomocą notesów usługi Azure za pomocą usługi Azure Machine Learning, zobacz [wprowadzenie do usługi Azure Machine Learning](quickstart-get-started.md) dokumentu.

## <a name="configure-jupyter-notebooks-on-your-own-computer"></a>Konfigurowanie aplikacji Jupyter Notebooks na swoim komputerze

1. Otwórz wiersz polecenia lub powłokę.

2. Aby utworzyć środowiska conda, użyj następujących poleceń:

    ```shell
    # create a new conda environment with Python 3.6, numpy and cython
    conda create -n myenv Python=3.6 cython numpy

    # activate the conda environment
    conda activate myenv

    # If you are running Mac OS you should run
    source activate myenv
    ```

    Może potrwać kilka minut, aby utworzyć środowisko, ponieważ środowisko Python 3.6 i inne składniki mogą muszą zostać pobrane.

3. Aby zainstalować zestaw SDK usługi Azure Machine Learning za pomocą notesu dodatki, użyj następującego polecenia:

     ```shell
    pip install --upgrade azureml-sdk[notebooks,automl,contrib]
    ```

    Może potrwać kilka minut, aby zainstalować zestaw SDK.

4. Aby zainstalować pakiety dla eksperymentów uczenia maszynowego, użyj następującego polecenia i Zastąp `<new package>` przy użyciu pakietu, którą chcesz zainstalować:

    ```shell
    conda install <new package>
    ```

5. Aby zainstalować serwer notesu programu Jupyter obsługujących conda i włączyć widżetów eksperymentu (w celu wyświetlania informacji o uruchomieniu), użyj następujących poleceń:

    ```shell
    # install Jupyter 
    conda install nb_conda

    # install experiment widget
    jupyter nbextension install --py --user azureml.train.widgets

    # enable experiment widget
    jupyter nbextension enable --py --user azureml.train.widgets
    ```

6. Aby uruchomić Notes Jupyter, użyj następującego polecenia:

    ```shell
    jupyter notebook
    ```

7. Otwórz nowy notes i wybierz "myenv" jako swojej jądra. Następnie sprawdź, czy masz Machine Learning zestawu SDK usługi Azure instalowane przez uruchomione następujące polecenie w komórce w notesie:

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

## <a name="configure-visual-studio-code"></a>Konfigurowanie programu Visual Studio Code

1. Otwórz wiersz polecenia lub powłokę.

2. Aby utworzyć środowiska conda, użyj następujących poleceń:

    ```shell
    # create a new conda environment with Python 3.6, numpy and cython
    conda create -n myenv Python=3.6 cython numpy

    # activate the conda environment
    conda activate myenv

    # If you are running Mac OS you should run
    source activate myenv
    ```

2. Aby zainstalować zestaw SDK usługi Azure Machine Learning, użyj następującego polecenia:
 
    ```shell
    pip install --upgrade azureml-sdk[automl,contrib]
    ```

4. Aby zainstalować narzędzia Visual Studio code dla sztucznej Inteligencji, zobacz wpis witryny marketplace programu Visual Studio dla [Tools for AI](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai). 

5. Aby zainstalować pakiety dla eksperymentów uczenia maszynowego, użyj następującego polecenia i Zastąp `<new package>` przy użyciu pakietu, którą chcesz zainstalować:

    ```shell
    conda install <new package>
    ```

6. Uruchamianie programu Visual Studio Code, a następnie użyj __CTRL-SHIFT-P__ można pobrać __paletę poleceń__. Wprowadź *Python: Wybierz Interpreter*i wybierz utworzonego środowiska conda.

    > [!NOTE]
    > Visual Studio Code jest automatycznie świadomość środowisk conda na tym komputerze. Aby uzyskać więcej informacji, zobacz [dokumentację kodu programu Visual Studio](https://code.visualstudio.com/docs/python/environments#_conda-environments).

7. Aby zweryfikować konfigurację, należy użyć programu Visual Studio Code można utworzyć nowy plik skryptu języka Python za pomocą następującego kodu, a następnie uruchom go:

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

## <a name="configure-code-editor-of-your-choice"></a>Konfigurowanie ulubionego edytora kodu

Edytor kodu niestandardowego za pomocą zestawu SDK usługi Azure Machine Learning, najpierw utwórz środowiska conda, zgodnie z powyższym opisem. Następnie postępuj zgodnie z instrukcjami dotyczącymi każdego edytora, aby użyć środowiska conda. Na przykład znajdują się w instrukcji dotyczących platformy PyCharm [ https://www.jetbrains.com/help/pycharm/2018.2/conda-support-creating-conda-virtual-environment.html ](https://www.jetbrains.com/help/pycharm/2018.2/conda-support-creating-conda-virtual-environment.html).
 
## <a name="next-steps"></a>Kolejne kroki

* [Uczenie modelu w usłudze Azure Machine Learning z zestawem danych mnist ręcznie ZAPISANYCH](tutorial-train-models-with-aml.md)
