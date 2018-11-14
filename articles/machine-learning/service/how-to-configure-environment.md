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
ms.date: 11/6/2018
ms.openlocfilehash: 2fd2d35bde95a3e268f46b398f2163f9d40ab1ee
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2018
ms.locfileid: "51613957"
---
# <a name="configure-a-development-environment-for-azure-machine-learning"></a>Konfigurowanie środowiska deweloperskiego dla usługi Azure Machine Learning

W tym dokumencie opisano konfigurowanie środowiska deweloperskiego do pracy z usługą Azure Machine Learning. Usługa Azure Machine Learning jest niezależne od platformy. Tylko wymagania dotyczące środowiska deweloperskiego __Python 3__, __Conda__ (dla środowiska izolowane) i pliku konfiguracji, który zawiera Twoje informacje o obszarze roboczym usługi Azure Machine Learning.

Ten dokument koncentruje się na następujących konkretnych środowisk i narzędzia:

* [Notesy platformy Azure](#aznotebooks): notesów programu Jupyter usługi hostowanej w chmurze platformy Azure. Jest __najprostsza__ sposobem na rozpoczęcie pracy, ponieważ zestaw SDK usługi Azure Machine Learning jest już zainstalowany.
* [Maszyna wirtualna do nauki o danych](#dsvm): maszynę wirtualną w chmurze platformy Azure, która jest __przeznaczony do pracy nauki o danych__. Python 3, Conda, notesy Jupyter i zestawu SDK usługi Azure Machine Learning są już zainstalowane. Maszyna wirtualna jest dostarczany z popularnych struktur, narzędzi i edytory do opracowywania rozwiązań uczenia Maszynowego uczenia Maszynowego. Jest to prawdopodobnie __najbardziej kompletne__ środowisko programistyczne przeznaczone do uczenia Maszynowego na platformie Azure.
* [Program Jupyter Notebooks](#jupyter): Jeśli już używasz notesów programu Jupyter, zestaw SDK zawiera niektóre dodatki, które należy zainstalować.
* [Visual Studio Code](#vscode): Jeśli używasz programu Visual Studio Code, istnieją pewne przydatne rozszerzenia, które można zainstalować.

Jeśli już masz środowisko Python 3 lub po prostu ma podstawowe kroki dotyczące instalowania zestawu SDK, zobacz [komputera lokalnego](#local) sekcji.

## <a name="prerequisites"></a>Wymagania wstępne

- Obszar roboczy usługi Azure Machine Learning. Postępuj zgodnie z instrukcjami w [wprowadzenie do usługi Azure Machine Learning](quickstart-get-started.md) ją utworzyć.

- Albo [Anaconda firmy Continuum](https://www.anaconda.com/download/) lub [Miniconda](https://conda.io/miniconda.html) Menedżera pakietów.

    > [!IMPORTANT]
    > Anaconda i Miniconda nie są wymagane, gdy za pomocą notesów usługi Azure.

- W systemie Linux lub Mac OS należy powłoki bash.

    > [!TIP]
    > Jeśli jesteś w systemie Linux lub Mac OS i użyć powłoki niż powłoki bash (na przykład zsh) mogą występować błędy, podczas uruchamiania niektórych poleceń. Aby obejść ten problem, należy użyć `bash` polecenie, aby rozpocząć nową powłokę bash, a następnie uruchom polecenia istnieje.

- W Windows należy wiersz polecenia lub wiersz Anaconda (zainstalowanych w ramach pakietu Anaconda i Miniconda).

## <a id="anotebooks"></a>Notesy platformy Azure

[Notesy platformy Azure](https://notebooks.azure.com) (wersja zapoznawcza) to opracowywanie interakcyjne środowisko w chmurze platformy Azure. Jest __najprostsza__ sposobem na rozpoczęcie pracy za pomocą usługi Azure Machine Learning.

* Zestaw SDK usługi Azure Machine Learning jest __zainstalowane__.
* Po utworzeniu obszaru roboczego usługi Azure Machine Learning w witrynie Azure portal, możesz kliknąć przycisk, aby automatycznie skonfigurować środowisko notesu platformy Azure do pracy z obszarem roboczym.

Aby rozpocząć tworzenie aplikacji za pomocą notesów usługi Azure, postępuj zgodnie z [wprowadzenie do usługi Azure Machine Learning](quickstart-get-started.md) dokumentu.

## <a id="dsvm"></a>Maszyna wirtualna do nauki o danych

Maszyna wirtualna do nauki o danych (DSVM) jest dostosowany obraz maszyny wirtualnej (VM) **przeznaczony do pracy nauki o danych**. Zawiera ona:

  - Narzędzia do analizy danych popularnych
  - Zintegrowanych środowisk projektowych (IDE), takich jak platformy PyCharm i programu RStudio
  - Pakietów, takich jak notesów Jupyter i Tensorflow

Zestaw SDK usługi Azure Machine Learning działa w wersji Ubuntu albo Windows DSVM. Aby użyć maszyny wirtualnej DSVM jako środowiska deweloperskiego, wykonaj następujące kroki:

1. Aby utworzyć maszynę wirtualną do nauki o danych, wykonaj czynności opisane w jednej z następujących dokumentów:

    * [Tworzenie maszyny wirtualnej do nauki o danych Ubuntu](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro)
    * [Utwórz maszynę wirtualną do nauki o danych Windows](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/provision-vm)

1. Zestaw SDK usługi Azure Machine Learning jest **zainstalowane** na maszyny DSVM. Aby użyć środowiska Conda, który zawiera zestaw SDK, użyj jednej z następujących poleceń:

    * Na __Ubuntu__ nauki, użyj tego polecenia:

        ```shell
        conda activate py36
        ```

    * Na __Windows__ nauki, użyj tego polecenia:

        ```shell
        conda activate AzureML
        ```

1. Aby sprawdzić, czy można dostępu do zestawu SDK i sprawdź wersję, użyj następującego kodu języka Python:

    ```python
    import azureml.core
    print(azureml.core.VERSION)
    ```

1. Aby skonfigurować maszyny wirtualnej DSVM do używania Twojego obszaru roboczego usługi Azure Machine Learning, zobacz [Konfiguruj obszar roboczy](#workspace) sekcji.

Aby uzyskać więcej informacji na maszynach wirtualnych do nauki o danych, zobacz [maszyn wirtualnych do nauki o danych](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/).

## <a id="local"></a>Komputer lokalny

Korzystając z komputera lokalnego, (które mogą być także zdalnego maszyny wirtualnej), umożliwia następujące kroki tworzenia środowiska conda i zainstaluj zestaw SDK:

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

1. Aby sprawdzić, czy zestaw SDK jest zainstalowany, poniższy kod języka Python:

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

### <a id="jupyter"></a>Program Jupyter Notebooks

Program Jupyter Notebooks są częścią [projektu Jupyter](https://jupyter.org/). Zapewniają one interaktywne środowisko kodowania, w której utworzono dokumenty, które mieszać kodu na żywo z tekstu opisowego i grafiki. Notesy Jupyter są również doskonały sposób, aby udostępniać wyniki innym użytkownikom, jak można zapisać danych wyjściowych sekcje kodu w dokumencie. Notesy Jupyter notebook można zainstalować na wielu różnych platformach.

Kroki opisane w [komputera lokalnego](#local) sekcji zainstalować składniki opcjonalne dla notesów programu Jupyter. Aby włączyć te składniki w danym środowisku notesu programu Jupyter, należy użyć następujące czynności:

1. Otwórz wiersz polecenia lub powłokę.

1. Aby zainstalować serwer notesu programu Jupyter obsługujących conda i włączyć widżetów eksperymentu, użyj następujących poleceń:

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

1. Aby sprawdzić, czy notesu programu Jupyter, można użyć zestawu SDK, otwórz nowy notes i wybierz "myenv" jako swojej jądra. Następnie uruchom następujące polecenie w komórce Notes:

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

1. Aby skonfigurować notesu Jupyter do używania Twojego obszaru roboczego usługi Azure Machine Learning, zobacz [Konfiguruj obszar roboczy](#workspace) sekcji.

### <a id="vscode"></a>Program Visual Studio Code

Visual Studio Code to edytor kodu dla wielu platform. Opiera się na lokalnej instalacji języka Python 3 i Conda obsługi języka Python, ale udostępnia dodatkowe narzędzia do pracy ze sztuczną Inteligencją. Zapewnia również obsługę służąca do wybierania środowiska Conda w edytorze kodu.

Aby użyć programu Visual Studio Code do tworzenia aplikacji, użyj następujących kroków:

1. Aby dowiedzieć się, jak używać programu Visual Studio Code dla programowania w języku Python, zobacz [Rozpoczynanie pracy z językiem Python w VSCode](https://code.visualstudio.com/docs/python/python-tutorial) dokumentu.

1. Aby wybrać środowiska Conda, Otwórz program VS Code, a następnie użyj __Ctrl-Shift-P__ (Linux i Windows) lub __polecenia-Shift-P__ (Mac), aby uzyskać __paleta polecenia__. Wprowadź __Python: Wybierz Interpreter__ , a następnie wybierz środowiska conda.

1. Aby sprawdzić, czy można użyć zestawu SDK, Utwórz nowy plik w języku Python (PY) zawierający poniższy kod. Następnie uruchom go:

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

1. Aby zainstalować narzędzia Visual Studio code dla sztucznej Inteligencji rozszerzenia, zobacz [Tools for AI](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai) strony.

    Aby uzyskać więcej informacji, zobacz [Użyj programu VS Code Tools dla sztucznej Inteligencji za pomocą usługi Azure Machine Learning](how-to-vscode-tools.md) dokumentu.

## <a id="workspace"></a>Utwórz plik konfiguracji obszaru roboczego

Plik konfiguracji obszaru roboczego jest dokument JSON, który informuje zestawu SDK, jak komunikować się z obszarem roboczym usługi Azure Machine Learning. Plik `config.json` i ma następujący format:

```json
{
    "subscription_id": "<subscription-id>",
    "resource_group": "<resource-group>",
    "workspace_name": "<workspace-name>"
}
```

Ten plik musi być w strukturze katalogu, który zawiera Twoje skrypty języka Python i notesy Jupyter. Może to być w tym samym katalogu, w podkatalogu o nazwie `aml_config`, lub w katalogu nadrzędnym.

Aby użyć tego pliku w kodzie, należy użyć `ws=Workspace.from_config()`. Ten kod ładuje dane z pliku i nawiązuje połączenie z obszarem roboczym.

Istnieją trzy sposoby tworzenia pliku konfiguracji:

* Jeśli stosujesz [szybkiego startu usługi Azure Machine Learning](quickstart-get-started.md), `config.json` plik zostanie utworzony w bibliotece notesy platformy Azure. Ten plik zawiera informacje o konfiguracji dla obszaru roboczego. Można pobrać lub skopiuj to `config.json` do innych środowisk programowania.

* Możesz **ręcznie utworzyć plik** za pomocą edytora tekstów. Można znaleźć wartości, przejść do pliku konfiguracji, przechodząc do obszaru roboczego w [witryny Azure portal](https://portal.azure.com). Kopiuj __nazwa obszaru roboczego__, __grupy zasobów__, i __identyfikator subskrypcji__ wartości i używać ich w pliku konfiguracji.
        ![Azure Portal](./media/how-to-configure-environment/configure.png)

* Możesz **programowo utworzyć plik**. Poniższy fragment kodu przedstawia sposób nawiązywania połączenia z obszarem roboczym, podając identyfikator subskrypcji, grupy zasobów i nazwę obszaru roboczego. Następnie zapisuje Konfiguracja obszaru roboczego do pliku:

    ```python
    from azureml.core import Workspace

    subscription_id = '<subscription-id>'
    resource_group  = '<resource-group>'
    workspace_name  = '<workspace-name>'

    try:
        ws = Workspace(subscription_id = subscription_id, resource_group = resource_group, workspace_name = workspace_name)
        ws.write_config()
        print('Library configuration succeeded')
    except:
        print('Workspace not found')
    ```

    Ten kod, zapisuje plik konfiguracyjny `aml_config/config.json` pliku.

## <a name="next-steps"></a>Kolejne kroki

- [Uczenie modelu w usłudze Azure Machine Learning z zestawem danych mnist ręcznie ZAPISANYCH](tutorial-train-models-with-aml.md)
- [Usługi Azure Machine Learning zestawu SDK dla języka Python](https://aka.ms/aml-sdk)
- [Zestaw SDK przygotowywania danych usługi Azure Machine Learning](https://aka.ms/data-prep-sdk)