---
title: Konfigurowanie środowiska deweloperskiego
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak skonfigurować środowisko programowania, podczas pracy z usługą Azure Machine Learning. W tym dokumencie Dowiedz się, jak używać środowisk Conda, Utwórz pliki konfiguracyjne i skonfiguruj notesów programu Jupyter, notesy platformy Azure, środowiska IDE, edytory kodu i maszyna wirtualna do nauki o danych.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.component: core
ms.reviewer: larryfr
manager: cgronlun
ms.topic: conceptual
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 6e2222d56ea37983b1efafedaac8e01058cb44fa
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2018
ms.locfileid: "53098053"
---
# <a name="configure-a-development-environment-for-azure-machine-learning"></a>Konfigurowanie środowiska deweloperskiego dla usługi Azure Machine Learning

W tym dokumencie opisano konfigurowanie środowiska deweloperskiego do pracy z usługą Azure Machine Learning. Usługa Azure Machine Learning jest niezależne od platformy. Tylko wymagania dotyczące środowiska deweloperskiego __Python 3__, __Conda__ (dla środowiska izolowane) i pliku konfiguracji, który zawiera Twoje informacje o obszarze roboczym usługi Azure Machine Learning.

Ten dokument koncentruje się na następujących konkretnych środowisk i narzędzia:

* [Notesy platformy Azure](#aznotebooks): notesów programu Jupyter usługi hostowanej w chmurze platformy Azure. Jest __najprostsza__ sposobem na rozpoczęcie pracy, ponieważ zestaw SDK usługi Azure Machine Learning jest już zainstalowany.
* [Maszyna wirtualna do nauki o danych](#dsvm): A __środowiska wstępnie skonfigurowane programowanie/eksperymentowania__ na platformie Azure w chmurze oznacza to __przeznaczony do pracy nauki o danych__ i którą można wdrożyć do jednej Tylko wystąpienia maszyn wirtualnych procesora CPU lub GPU wystąpienia w pamięci masowej. Python 3, Conda, notesy Jupyter i zestawu SDK usługi Azure Machine Learning są już zainstalowane. Maszyna wirtualna jest dostarczany z popularnych ML / głębokiego uczenia struktur, narzędzi i edytory związane z opracowywaniem rozwiązań uczenia Maszynowego. Jest to prawdopodobnie __najbardziej kompletne__ środowisko programistyczne przeznaczone do uczenia Maszynowego na platformie Azure.
* [Program Jupyter Notebooks](#jupyter): Jeśli już używasz notesów programu Jupyter, zestaw SDK zawiera niektóre dodatki, które należy zainstalować.
* [Visual Studio Code](#vscode): Jeśli używasz programu Visual Studio Code, istnieją pewne przydatne rozszerzenia, które można zainstalować.
* [Usługa Azure Databricks](#aml-databricks): platformy do analizy danych popularnych oparte na platformie Apache Spark. Dowiedz się, jak uzyskać Machine Learning zestawu SDK usługi Azure do klastra, dzięki czemu można wdrażać modele.

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

Maszyna wirtualna do nauki o danych (DSVM) jest dostosowany obraz maszyny wirtualnej (VM) **przeznaczony do pracy nauki o danych** to wstępnie skonfigurowane przy użyciu:

  - Pakietów, takich jak Tensorflow, Pytorch, scikit-dowiedzieć się, Xgboost i zestawu SDK usługi Azure ML
  - Narzędzi nauki o danych popularne, takich jak Spark autonomicznej testowania odzyskiwania po awarii
  - Narzędzia platformy Azure, takich jak Eksplorator interfejsu wiersza polecenia narzędzia Azcopy i magazynu
  - Zintegrowanych środowisk projektowych (IDE) takich jak Visual Studio Code, platformy PyCharm i programu RStudio
  - Serwer notesu programu Jupyter 

Zestaw SDK usługi Azure Machine Learning działa w wersji Ubuntu albo Windows DSVM. Aby używać maszyna wirtualna do nauki o danych jako środowisko projektowe, użyj następujące czynności:

1. Aby utworzyć maszynę wirtualną do nauki o danych, użyj jednej z następujących metod:

    * W witrynie Azure Portal:

        * [Tworzenie __Ubuntu__ maszyny wirtualnej do nauki o danych](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro)

        * [Tworzenie __Windows__ maszyny wirtualnej do nauki o danych](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/provision-vm)

    * Przy użyciu wiersza polecenia platformy Azure:

        > [!IMPORTANT]
        > Korzystając z wiersza polecenia platformy Azure, należy najpierw zalogować do subskrypcji platformy Azure przy użyciu `az login` polecenia.
        >
        > Korzystając z polecenia w tym kroku, należy podać nazwę grupy zasobów, nazwę maszyny Wirtualnej, nazwę użytkownika i hasła.

        * Aby utworzyć __Ubuntu__ maszyny wirtualnej do nauki o danych, użyj następującego polecenia:

            ```azurecli
            # create a Ubuntu DSVM in your resource group
            # note you need to be at least a contributor to the resource group in order to execute this command successfully
            # If you need to create a new resource group use: "az group create --name YOUR-RESOURCE-GROUP-NAME --location YOUR-REGION (For example: westus2)"
            az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:linux-data-science-vm-ubuntu:linuxdsvmubuntu:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --generate-ssh-keys --authentication-type password
            ```

        * Aby utworzyć __Windows__ maszyny wirtualnej do nauki o danych, użyj następującego polecenia:

            ```azurecli
            # create a Windows Server 2016 DSVM in your resource group
            # note you need to be at least a contributor to the resource group in order to execute this command successfully
            az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:dsvm-windows:server-2016:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --authentication-type password
            ```    

2. Zestaw SDK usługi Azure Machine Learning jest **zainstalowane** na maszyny DSVM. Aby użyć środowiska Conda, który zawiera zestaw SDK, użyj jednej z następujących poleceń:

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

1. Aby zainstalować serwer notesu programu Jupyter obsługujących conda przy użyciu następującego polecenia:

    ```shell
    # install Jupyter
    conda install nb_conda
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

1. Aby zainstalować rozszerzenia usługi Azure Machine Learning dla programu Visual Studio Code, zobacz [Tools for AI](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai) strony.

    Aby uzyskać więcej informacji, zobacz [przy użyciu usługi Azure Machine Learning dla programu Visual Studio Code](how-to-vscode-tools.md).

<a name="aml-databricks"></a>

## <a name="azure-databricks"></a>Azure Databricks

Niestandardową wersję Machine Learning zestawu SDK usługi Azure dla usługi Azure Databricks służy do end-to-end niestandardowego uczenia maszynowego. Lub uczenie modelu w usłudze Databricks i użyj [programu Visual Studio Code](how-to-vscode-train-deploy.md#deploy-your-service-from-vs-code) wdrażanie modelu

Aby przygotować usługi Databricks w klastrze i uzyskać notesów próbki:

1. Tworzenie [klastra usługi Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal) z wersją środowiska uruchomieniowego usługi Databricks 4.x (wysoka współbieżność preferowana) za pomocą **Python 3**. 

1. Utworzyć bibliotekę do [zainstalować i dołączyć](https://docs.databricks.com/user-guide/libraries.html#create-a-library) Azure Machine Learning zestaw SDK for Python `azureml-sdk[databricks]` PyPi pakietu do klastra. Gdy wszystko będzie gotowe, zobaczysz biblioteki dołączona, jak pokazano na tej ilustracji. Należy pamiętać o tych [typowych problemów w usłudze Databricks](resource-known-issues.md#databricks).

   ![Zestaw SDK w usłudze Databricks ](./media/how-to-azure-machine-learning-on-databricks/sdk-installed-on-databricks.jpg)

   Jeśli ten krok nie powiedzie się, uruchom ponownie klaster:
   1. Wybierz `Clusters`w okienku po lewej stronie. Wybierz nazwę klastra w tabeli. 
   1. Na `Libraries` zaznacz `Restart`.

1. Pobierz [usługi Azure Databricks / Notes zestawu SDK usługi Azure Machine Learning archiwum pliku](https://github.com/Azure/MachineLearningNotebooks/blob/master/databricks/Databricks_AMLSDK_github.dbc).

   >[!Warning]
   > Wiele notesów próbki są dostępne do użycia z usługą Azure Machine Learning. Te przykładowe notesy pracy z usługą Azure Databricks: https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks

1.  [Zaimportować ten plik archiwum](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-manage.html#import-an-archive) do Twojej usługi Databricks klastra i zacznij przeglądać jako [opisane w tym miejscu](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks).


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
