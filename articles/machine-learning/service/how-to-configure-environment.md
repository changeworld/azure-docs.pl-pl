---
title: Konfigurowanie środowiska deweloperskiego dla usługi Azure Machine Learning | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować środowisko programowania, podczas pracy z usługą Azure Machine Learning. W tym dokumencie Dowiedz się, jak używać środowisk Conda, Utwórz pliki konfiguracyjne i skonfiguruj notesów programu Jupyter, notesy platformy Azure, środowiska IDE, edytory kodu i maszyna wirtualna do nauki o danych.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.component: core
ms.reviewer: larryfr
manager: cgronlun
ms.topic: conceptual
ms.date: 8/6/2018
ms.openlocfilehash: 657a762874f7c2fb40553552ef6c17d9b5b6da0f
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2018
ms.locfileid: "49958622"
---
# <a name="configure-a-development-environment-for-azure-machine-learning"></a>Konfigurowanie środowiska deweloperskiego dla usługi Azure Machine Learning

Ten artykuł nauczy Cię sposobu konfigurowania środowiska deweloperskiego do pracy z usługą Azure Machine Learning, w tym:

- Jak utworzyć plik konfiguracji, które kojarzy środowiska z obszarem roboczym usługi Azure Machine Learning.
- Instrukcje konfigurowania następujących środowisk programowania:
  - Notesy Jupyter na komputerze
  - Visual Studio Code
  - Edytor kodu niestandardowego
- Jak skonfigurować [wirtualnego środowiska conda](https://conda.io/docs/user-guide/tasks/manage-environments.html) i użyć jej do usługi Azure Machine Learning. Firma Microsoft zaleca stosowanie Anaconda firmy Continuum do izolowania środowiska pracy w celu uniknięcia konfliktów zależności między pakietami.

## <a name="prerequisites"></a>Wymagania wstępne

- Skonfiguruj obszar roboczy usługi Azure Machine Learning. Postępuj zgodnie z instrukcjami w [wprowadzenie do usługi Azure Machine Learning](quickstart-get-started.md).
- Należy zainstalować jedną [Anaconda firmy Continuum](https://www.anaconda.com/download/) lub [Miniconda](https://conda.io/miniconda.html) Menedżera pakietów.
- Jeśli używasz programu Visual Studio Code, [rozszerzenie języka Python](https://code.visualstudio.com/docs/python/python-tutorial).

> [!NOTE]
> Możesz przetestować poleceń powłoki, przedstawione w tym artykule, korzystając z powłoki bash (w systemie Linux i Mac OS) lub wiersza polecenia (Windows).

## <a name="create-a-workspace-configuration-file"></a>Utwórz plik konfiguracji obszaru roboczego

Zestaw SDK usługi Azure Machine Learning korzysta z pliku konfiguracji obszaru roboczego do komunikowania się z obszarem roboczym usługi Azure Machine Learning.

- Aby utworzyć plik konfiguracji, wykonaj [szybkiego startu usługi Azure Machine Learning](quickstart-get-started.md).
  - W procesie szybkiego startu jest tworzona `config.json` pliku w notesach platformy Azure. Ten plik zawiera informacje o konfiguracji dla obszaru roboczego.
  - Pobierz lub skopiuj `config.json` w tym samym katalogu co skryptów lub notesów, które ją przywołują.

- Możesz też ręcznie utworzyć plik, wykonaj następujące czynności:

    1. Otwieranie obszaru roboczego w [witryny Azure portal](https://portal.azure.com). Kopiuj __nazwa obszaru roboczego__, __grupy zasobów__, i __identyfikator subskrypcji__. Te wartości są używane do tworzenia pliku konfiguracji.
        ![Azure Portal](./media/how-to-configure-environment/configure.png)

    1. Tworzenie pliku następującym kodem języka Python i upewnij się uruchomić kod w tym samym katalogu co skryptów lub notesów odwołujące się do obszaru roboczego:

        ```python
        from azureml.core import Workspace

        subscription_id ='<subscription-id>'
        resource_group ='<resource-group>'
        workspace_name = '<workspace-name>'

        try:
           ws = Workspace(subscription_id = subscription_id, resource_group = resource_group, workspace_name = workspace_name)
           ws.write_config()
           print('Library configuration succeeded')
        except:
           print('Workspace not found')
        ```
        Kod zapisuje następujące `aml_config/config.json` pliku:

        ```json
        {
        "subscription_id": "<subscription-id>",
        "resource_group": "<resource-group>",
        "workspace_name": "<workspace-name>"
        }
        ```
        Możesz skopiować `aml_config` katalogu lub po prostu `config.json` pliku do innego katalogu, który odwołuje się do obszaru roboczego.

       > [!NOTE]
       > Inne skrypty lub notesów, w tym samym katalogu lub niższy Załaduj obszar roboczy z `ws=Workspace.from_config()`.

## <a name="azure-notebooks-and-data-science-virtual-machines"></a>Notesy platformy Azure i maszyn wirtualnych do nauki o danych

Azure notesów i maszyn wirtualnych do nauki o danych platformy Azure (maszyny) są skonfigurowane do pracy z usługą Azure Machine Learning. Te środowiska zawierają wymagane składniki, takie jak zestaw SDK usługi Azure Machine Learning.

- Notesy platformy Azure to usługa notesu Jupyter w chmurze platformy Azure.
- Maszyna wirtualna do nauki o danych to dostosowany obraz maszyny wirtualnej (VM) przeznaczony do pracy do analizy danych. Zawiera ona:
  - Popularne narzędzia
  - Zintegrowanych środowisk projektowych (IDE)
  - Pakietów, takich jak notesów programu Jupyter, platformy PyCharm i Tensorflow
- Nadal należy plik konfiguracji obszaru roboczego, aby użyć tych środowisk.

Przykład za pomocą notesów usługi Azure za pomocą usługi Azure Machine Learning, zobacz [wprowadzenie do usługi Azure Machine Learning](quickstart-get-started.md).

Aby uzyskać więcej informacji na maszynach wirtualnych do nauki o danych, zobacz [maszyn wirtualnych do nauki o danych](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/).

## <a name="configure-jupyter-notebooks-on-your-computer"></a>Konfigurowanie notesów programu Jupyter na komputerze

1. Otwórz wiersz polecenia lub powłokę.

1. Tworzenie środowiska conda przy użyciu następujących poleceń:

    ```shell
    # create a new conda environment with Python 3.6, numpy, and cython
    conda create -n myenv Python=3.6 cython numpy

    # activate the conda environment
    conda activate myenv

    # On Mac OS run
    source activate myenv
    ```

    Może upłynąć kilka minut, aby utworzyć środowisko, jeśli środowisko Python 3.6 i inne składniki usługi muszą zostać pobrane.

1. Zainstaluj SDK Azure maszyny Learning dodatki Notes i zestawu SDK przygotowywania danych przy użyciu następującego polecenia:

     ```shell
    pip install --upgrade azureml-sdk[notebooks,automl] azureml-dataprep
    ```

    > [!NOTE]
    > Jeśli pojawi się komunikat `PyYAML` nie może być odinstalowany, użyj następującego polecenia:
    >
    > `pip install --upgrade azureml-sdk[notebooks,automl] azureml-dataprep --ignore-installed PyYAML`

    Może upłynąć kilka minut, aby zainstalować zestaw SDK.

1. Zainstaluj pakiety do eksperymentów uczenia maszynowego. Następujące polecenie i Zastąp `<new package>` przy użyciu pakietu, którą chcesz zainstalować:

    ```shell
    conda install <new package>
    ```

1. Zainstaluj obsługujących conda serwer notesu Jupyter i Włącz widżetów eksperymentu (w celu wyświetlania informacji o uruchomieniu). Użyj następujących poleceń:

    ```shell
    # install Jupyter
    conda install nb_conda

    # install experiment widget
    jupyter nbextension install --py --user azureml.train.widgets

    # enable experiment widget
    jupyter nbextension enable --py --user azureml.train.widgets
    ```

1. Otwieranie notesu Jupyter, za pomocą następującego polecenia:

    ```shell
    jupyter notebook
    ```

1. Otwórz nowy notes, wybierz "myenv" jako swojej jądra, a następnie sprawdź, czy masz Machine Learning zestawu SDK usługi Azure zainstalowany. W komórce w notesie, uruchom następujące polecenie:

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

## <a name="configure-visual-studio-code"></a>Konfigurowanie programu Visual Studio Code

1. Otwórz wiersz polecenia lub powłokę.

1. Tworzenie środowiska conda przy użyciu następujących poleceń:

    ```shell
    # create a new conda environment with Python 3.6, numpy and cython
    conda create -n myenv Python=3.6 cython numpy

    # activate the conda environment
    conda activate myenv

    # If you are running Mac OS you should run
    source activate myenv
    ```

1. Zainstaluj zestaw SDK usługi Azure Machine Learning i zestawu SDK przygotowywania danych za pomocą następującego polecenia:

    ```shell
    pip install --upgrade azureml-sdk[automl] azureml-dataprep
    ```

1. Instalowanie narzędzia Visual Studio code dla sztucznej Inteligencji rozszerzenia. Zobacz [Tools for AI](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai).

1. Zainstaluj pakiety do eksperymentów uczenia maszynowego. Należy użyć następującego polecenia, zastępując `<new package>` przy użyciu pakietu, którą chcesz zainstalować:

    ```shell
    conda install <new package>
    ```

1. Otwórz program Visual Studio Code, a następnie użyj **CTRL-SHIFT-P** (w Windows) lub **polecenia-SHIFT-P** (w systemie Mac OS) można pobrać **paletę poleceń**. Wprowadź _Python: Wybierz Interpreter_ i wybierz utworzonego środowiska conda.

   > [!NOTE]
   > Visual Studio Code jest automatycznie świadomość środowisk conda na tym komputerze. Aby uzyskać więcej informacji, zobacz [dokumentację kodu programu Visual Studio](https://code.visualstudio.com/docs/python/environments#_conda-environments).

1. Zweryfikuj konfigurację przy użyciu programu Visual Studio Code, aby utworzyć nowy plik skryptu języka Python z następującym kodem, a następnie uruchom go:

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

## <a name="configure-a-custom-code-editor"></a>Konfigurowanie Edytora kodu niestandardowego

Za pomocą dowolnego edytora kodu za pomocą zestawu SDK usługi Azure Machine Learning.

1. Utwórz środowiska conda, zgodnie z opisem w kroku 2 [Konfigurowanie programu Visual Studio Code](#configure-visual-studio-code) powyżej.
1. Postępuj zgodnie z instrukcjami dotyczącymi każdego edytora, aby użyć środowiska conda. Na przykład, możesz wykonać [instrukcje platformy PyCharm](https://www.jetbrains.com/help/pycharm/2018.2/conda-support-creating-conda-virtual-environment.html).

## <a name="next-steps"></a>Kolejne kroki

- [Uczenie modelu w usłudze Azure Machine Learning z zestawem danych mnist ręcznie ZAPISANYCH](tutorial-train-models-with-aml.md)
