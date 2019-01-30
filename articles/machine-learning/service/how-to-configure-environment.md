---
title: Skonfiguruj środowisko programistyczne języka Python
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak skonfigurować środowisko programowania, podczas pracy z usługą Azure Machine Learning. W tym artykule dowiesz się, jak używać środowisk Conda, Utwórz pliki konfiguracyjne i skonfiguruj notesów programu Jupyter, notesy platformy Azure, usługi Azure Databricks, środowiska IDE, edytory kodu i maszyna wirtualna do nauki o danych.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
manager: cgronlun
ms.topic: conceptual
ms.date: 01/18/2019
ms.custom: seodec18
ms.openlocfilehash: 02d0afd18ad966baf8954ae5add180c970fe302e
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/30/2019
ms.locfileid: "55247412"
---
# <a name="configure-a-development-environment-for-azure-machine-learning"></a>Konfigurowanie środowiska deweloperskiego dla usługi Azure Machine Learning

W tym artykule dowiesz się, jak skonfigurować środowisko programistyczne do pracy z usługą Azure Machine Learning. Usługa Machine Learning jest niezależny od platformy. 

Jedynymi wymogami dla swojego środowiska programowania są 3 języka Python, Conda (dla środowiska izolowane) i pliku konfiguracji, który zawiera Twoje informacje o obszarze roboczym usługi Azure Machine Learning.

Ten artykuł koncentruje się na następujących środowisk i narzędzia:

* [Notesy platformy Azure](#aznotebooks): Usługa notesów programu Jupyter, która jest hostowana w chmurze platformy Azure. Jest najprostszym sposobem na rozpoczęcie pracy, ponieważ zestaw SDK usługi Azure Machine Learning jest już zainstalowany.

* [Maszyna wirtualna do nauki o danych (DSVM)](#dsvm): Wstępnie skonfigurowane rozwoju lub eksperymentowania środowisku w chmurze platformy Azure, który jest przeznaczony do pracy nauki o danych i którą można wdrożyć tylko wystąpienia maszyn wirtualnych procesora CPU lub wystąpienia oparte na procesorze GPU. Python 3, Conda, notesy Jupyter i zestawu SDK usługi Azure Machine Learning są już zainstalowane. Maszyna wirtualna jest powiązana z popularnych uczenia maszynowego i głębokiego uczenia struktur, narzędzi i edytory do tworzenia rozwiązania uczenia maszynowego. Prawdopodobnie jest najbardziej zaawansowane środowiska programistycznego, Machine learning na platformie Azure.

* [Notes Jupyter](#jupyter): Jeśli już używasz notesu programu Jupyter, zestaw SDK zawiera niektóre dodatki, które należy zainstalować.

* [Visual Studio Code](#vscode): Jeśli używasz programu Visual Studio Code, ma pewne przydatne rozszerzenia, które można zainstalować.

* [Usługa Azure Databricks](#aml-databricks): Popularne platformy do analizy danych oparty na platformie Apache Spark. Dowiedz się, jak uzyskać Machine Learning zestawu SDK usługi Azure do klastra, dzięki czemu można wdrażać modele.

Jeśli już masz środowisko Python 3 lub po prostu ma podstawowe kroki dotyczące instalowania zestawu SDK, zobacz [komputera lokalnego](#local) sekcji.

## <a name="prerequisites"></a>Wymagania wstępne

- Obszar roboczy usługi Azure Machine Learning. Aby utworzyć obszar roboczy, zobacz [wprowadzenie do usługi Azure Machine Learning](quickstart-get-started.md).

- Albo [Anaconda firmy Continuum](https://www.anaconda.com/download/) lub [Miniconda](https://conda.io/miniconda.html) Menedżera pakietów.

    > [!IMPORTANT]
    > Anaconda i Miniconda nie są wymagane, gdy używasz notesy platformy Azure.

- W systemie Linux lub macOS konieczne powłoki bash.

    > [!TIP]
    > Jeśli jesteś w systemie Linux lub macOS i korzystanie z powłoki niż powłoki bash (na przykład zsh) może być wystąpią błędy, podczas uruchamiania niektórych poleceń. Aby obejść ten problem, należy użyć `bash` polecenie, aby rozpocząć nową powłokę bash, a następnie uruchom polecenia istnieje.

- W Windows należy wiersz polecenia lub wiersz Anaconda (zainstalowanych w ramach pakietu Anaconda i Miniconda).

## <a id="aznotebooks"></a>Notesy platformy Azure

[Notesy platformy Azure](https://notebooks.azure.com) (wersja zapoznawcza) to opracowywanie interakcyjne środowisko w chmurze platformy Azure. Jest najprostszym sposobem rozpoczęcia pracy za pomocą usługi Azure Machine Learning.

* Zestaw SDK usługi Azure Machine Learning jest już zainstalowana.
* Po utworzeniu obszaru roboczego usługi Azure Machine Learning w witrynie Azure portal, możesz kliknąć przycisk, aby automatycznie skonfigurować środowisko notesu platformy Azure do pracy z obszarem roboczym.

Aby rozpocząć tworzenie aplikacji za pomocą notesów usługi Azure, zobacz [wprowadzenie do usługi Azure Machine Learning](quickstart-get-started.md).

Domyślnie notesów usługi Azure korzysta z warstwy bezpłatna usługa, która jest ograniczony do 4GB pamięci i 1GB danych. Można jednak usunąć te limity, dołączając wystąpienia maszyny wirtualnej do nauki o danych do projektu notesy platformy Azure. Aby uzyskać więcej informacji, zobacz [zarządzanie i konfigurować projekty notesy platformy Azure — warstwa wystąpień obliczeniowych](/azure/notebooks/configure-manage-azure-notebooks-projects#compute-tier).

## <a id="dsvm"></a>Maszyna wirtualna do nauki o danych

Maszyny DSVM jest dostosowany obraz maszyny wirtualnej (VM). Jest przeznaczony do pracy nauki o danych, która jest wstępnie skonfigurowana za pomocą:

  - Pakietów, takich jak TensorFlow, PyTorch, Scikit-learn, XGBoost i Azure Machine Learning zestawu SDK
  - Narzędzi nauki o danych popularne, takich jak Spark, jak i testowania odzyskiwania po awarii
  - Narzędzia platformy Azure, takich jak wiersza polecenia platformy Azure, narzędzia AzCopy i Eksploratora usługi Storage
  - Zintegrowanych środowisk projektowych (IDE), takie jak Visual Studio Code i platformy PyCharm
  - Serwer notesu programu Jupyter

Zestaw SDK usługi Azure Machine Learning działa w wersji Ubuntu albo Windows maszyny DSVM. Ale jeśli planujesz używać maszyny DSVM jako obiekt docelowy obliczeń jest obsługiwana tylko w systemie Ubuntu.

Aby używać maszyny DSVM jako środowiska deweloperskiego, wykonaj następujące czynności:

1. Tworzenie maszyny wirtualnej DSVM w jednym z następujących środowiskach:

    * Witryna Azure portal:

        * [Tworzenie maszyny wirtualnej do nauki o danych Ubuntu](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro)

        * [Utwórz maszynę wirtualną do nauki o danych Windows](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/provision-vm)

    * Interfejs wiersza polecenia platformy Azure:

        > [!IMPORTANT]
        > * Użycie opcji wiersza polecenia platformy Azure, należy najpierw zalogować do subskrypcji platformy Azure przy użyciu `az login` polecenia.
        >
        > * Gdy używasz polecenia w tym kroku musisz podać nazwę grupy zasobów, nazwę maszyny Wirtualnej, nazwę użytkownika i hasła.

        * Aby utworzyć maszynę wirtualną do nauki o danych Ubuntu, użyj następującego polecenia:

            ```azurecli
            # create a Ubuntu DSVM in your resource group
            # note you need to be at least a contributor to the resource group in order to execute this command successfully
            # If you need to create a new resource group use: "az group create --name YOUR-RESOURCE-GROUP-NAME --location YOUR-REGION (For example: westus2)"
            az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:linux-data-science-vm-ubuntu:linuxdsvmubuntu:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --generate-ssh-keys --authentication-type password
            ```

        * Aby utworzyć maszynę wirtualną do nauki o danych Windows, użyj następującego polecenia:

            ```azurecli
            # create a Windows Server 2016 DSVM in your resource group
            # note you need to be at least a contributor to the resource group in order to execute this command successfully
            az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:dsvm-windows:server-2016:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --authentication-type password
            ```    

2. Zestaw SDK usługi Azure Machine Learning jest już zainstalowana na maszyny DSVM. Aby użyć środowiska Conda, który zawiera zestaw SDK, użyj jednej z następujących poleceń:

    * Dla maszyny wirtualnej DSVM z systemem Ubuntu:

        ```shell
        conda activate py36
        ```

    * Dla maszyny wirtualnej DSVM Windows:

        ```shell
        conda activate AzureML
        ```

1. Aby sprawdzić, czy można dostępu do zestawu SDK i sprawdź wersję, użyj następującego kodu języka Python:

    ```python
    import azureml.core
    print(azureml.core.VERSION)
    ```

1. Aby skonfigurować maszyny wirtualnej DSVM do używania Twojego obszaru roboczego usługi Azure Machine Learning, zobacz [utworzyć plik konfiguracji obszaru roboczego](#workspace) sekcji.

Aby uzyskać więcej informacji, zobacz [maszyn wirtualnych do nauki o danych](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/).

## <a id="local"></a>Komputer lokalny

Podczas korzystania z komputera lokalnego, (które mogą być także zdalnego maszyny wirtualnej), tworzenie środowiska Conda i zainstaluj zestaw SDK, wykonując następujące czynności:

1. Otwórz wiersz polecenia lub powłokę.

1. Tworzenie środowiska Conda przy użyciu następujących poleceń:

    ```shell
    # create a new Conda environment with Python 3.6, NumPy, and Cython
    conda create -n myenv Python=3.6 cython numpy

    # activate the Conda environment
    conda activate myenv

    # On macOS run
    source activate myenv
    ```

    Może upłynąć kilka minut, aby utworzyć środowisko, jeśli środowisko Python 3.6 i inne składniki usługi muszą zostać pobrane.

1. Zainstaluj SDK Azure maszyny Learning dodatki Notes i zestawu SDK przygotowywania danych przy użyciu następującego polecenia:

     ```shell
    pip install --upgrade azureml-sdk[notebooks,automl] azureml-dataprep
    ```

   > [!NOTE]
   > Jeśli zostanie wyświetlony komunikat, że nie można odinstalować PyYAML, należy użyć następującego polecenia:
   >
   > `pip install --upgrade azureml-sdk[notebooks,automl] azureml-dataprep --ignore-installed PyYAML`

   Może upłynąć kilka minut, aby zainstalować zestaw SDK.

1. Zainstaluj pakiety do eksperymentów uczenia maszynowego. Następujące polecenie i Zastąp  *\<nowy pakiet >* przy użyciu pakietu, którą chcesz zainstalować:

    ```shell
    conda install <new package>
    ```

1. Aby sprawdzić, czy zainstalowano zestaw SDK, użyj następującego kodu języka Python:

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

### <a id="jupyter"></a>Program Jupyter Notebooks

Program Jupyter Notebooks są częścią [projektu Jupyter](https://jupyter.org/). Zapewniają one interaktywne środowisko kodowania, w której utworzono dokumenty, które mieszać kodu na żywo z tekstu opisowego i grafiki. Notesów programu Jupyter są również doskonały sposób, aby udostępniać wyniki innym użytkownikom, ponieważ można zapisać danych wyjściowych sekcje kodu w dokumencie. Notesy Jupyter notebook można zainstalować na wielu różnych platformach.

Procedury w [komputera lokalnego](#local) sekcji instaluje składniki opcjonalne dla notesów programu Jupyter. Aby włączyć te składniki w danym środowisku notesu programu Jupyter, wykonaj następujące czynności:

1. Otwórz wiersz polecenia lub powłokę.

1. Aby zainstalować narzędzia Conda-aware serwer notesu Jupyter, użyj następującego polecenia:

    ```shell
    # install Jupyter
    conda install nb_conda
    ```

1. Otwieranie notesu Jupyter, za pomocą następującego polecenia:

    ```shell
    jupyter notebook
    ```

1. Aby sprawdzić, czy notesu programu Jupyter można używać zestawu SDK, otwórz nowy notes, wybierz **myenv** jako jądra, a następnie uruchom następujące polecenie w komórce Notes:

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

1. Aby skonfigurować notesu Jupyter do używania Twojego obszaru roboczego usługi Azure Machine Learning, przejdź do [utworzyć plik konfiguracji obszaru roboczego](#workspace) sekcji.

### <a id="vscode"></a>Program Visual Studio Code

Visual Studio Code to edytor kodu dla wielu platform. Opiera się na lokalnej instalacji języka Python 3 i Conda obsługi języka Python, ale udostępnia dodatkowe narzędzia do pracy ze sztuczną Inteligencją. Zapewnia również obsługę służąca do wybierania środowiska Conda w edytorze kodu.

Aby użyć programu Visual Studio Code do tworzenia aplikacji, wykonaj następujące czynności:

1. Aby dowiedzieć się, jak używać programu Visual Studio Code dla programowania w języku Python, zobacz [Rozpoczynanie pracy z językiem Python w VSCode](https://code.visualstudio.com/docs/python/python-tutorial).

1. Aby wybrać środowiska Conda, Otwórz program VS Code, a następnie wybierz klawisze Ctrl + Shift + P (z systemem Linux i Windows) lub polecenie + Shift + P (Mac).  
    __Paleta polecenia__ zostanie otwarty. 

1. Wprowadź __Python: Wybierz Interpreter__, a następnie wybierz pozycję środowiska Conda.

1. Aby sprawdzić, czy można użyć zestawu SDK, Utwórz, a następnie uruchom nowy plik języka Python (PY), który zawiera następujący kod:

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

1. Aby zainstalować rozszerzenia usługi Azure Machine Learning dla programu Visual Studio Code, zobacz [Tools for AI](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai).

    Aby uzyskać więcej informacji, zobacz [użycia usługi Azure Machine Learning dla programu Visual Studio Code](how-to-vscode-tools.md).

<a name="aml-databricks"></a>

## <a name="azure-databricks"></a>Azure Databricks

Niestandardową wersję Machine Learning zestawu SDK usługi Azure dla usługi Azure Databricks służy do end-to-end niestandardowego uczenia maszynowego. Możesz również uczenie modelu w usłudze Databricks i wdrożyć ją za pomocą [programu Visual Studio Code](how-to-vscode-train-deploy.md#deploy-your-service-from-vs-code).

Aby przygotować usługi Databricks w klastrze i uzyskać notesów próbki:

1. Tworzenie [klastra usługi Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal) z następującymi ustawieniami:

    | Ustawienie | Wartość |
    |----|---|
    | Nazwa klastra | yourclustername |
    | Środowisko uruchomieniowe usługi Databricks | Środowisko uruchomieniowe żadnych innych ML (innego niż ML 4.x, 5.x) |
    | Wersja języka Python | 3 |
    | Procesy robocze | 2 lub nowszy |

    Użyj tych ustawień, tylko wtedy, gdy będziesz używać uczenia maszynowego automatyczne w usłudze Databricks:
    
    |   Ustawienie | Wartość |
    |----|---|
    | Typy maszyn wirtualnych na węzeł procesu roboczego | Preferowane maszyny Wirtualnej zoptymalizowane pod kątem pamięci |
    | Włączanie skalowania automatycznego | Usuń zaznaczenie pola wyboru |
    
    Liczba węzłów procesu roboczego w klastrze usługi Databricks określa maksymalną liczbę równoczesnych iteracji w ustawieniach zautomatyzowane uczenia Maszynowego.  

    Potrwa kilka minut na utworzenie klastra. Zaczekaj, aż klaster ma zainstalowany przed kontynuowaniem.

1. Instalowanie i dołączanie pakietu zestawu SDK usługi Azure Machine Learning do klastra.  

    * [Utworzyć bibliotekę](https://docs.databricks.com/user-guide/libraries.html#create-a-library) przy użyciu jednego z tych ustawień (_wybierz tylko jeden z tych opcji_):
    
        * Aby zainstalować zestaw SDK usługi Azure Machine Learning _bez_ zautomatyzowane machine learning możliwości:
            | Ustawienie | Wartość |
            |----|---|
            |Element źródłowy | Przekaż Python Egg lub PyPI
            |Nazwa PyPi | azureml-sdk[databricks]
    
        * Aby zainstalować zestaw SDK usługi Azure Machine Learning _z_ automatyczne usługi machine learning:
            | Ustawienie | Wartość |
            |----|---|
            |Element źródłowy | Przekaż Python Egg lub PyPI
            |Nazwa PyPi | azureml-sdk[automl_databricks]
    
    * Nie należy wybierać **automatycznie dołączyć się do wszystkich klastrów**

    * Wybierz **Dołącz** obok swojej nazwy klastra

    * Upewnij się, że nie ma żadnych błędów, aż stan zmieni się na **dołączone**. Może upłynąć kilka minut.

    Jeśli masz starszą wersję zestawu SDK, usuń zaznaczenie opcji z zainstalowanych libs klastra i Przenieś do Kosza. Instalowanie nowej wersji zestawu SDK i uruchom ponownie klaster. Jeśli występuje problem, po to, odłącz i ponownie podłącz klastra.

    Gdy wszystko będzie gotowe, biblioteka jest dołączony, jak pokazano na poniższych ilustracjach. Należy pamiętać o tych [typowych problemów w usłudze Databricks](resource-known-issues.md#databricks).

    * Po zainstalowaniu zestawu SDK usługi Azure Machine Learning _bez_ automatyczne usługi machine learning ![SDK bez automatycznego uczenia maszynowego zainstalowana w usłudze Databricks ](./media/how-to-configure-environment/amlsdk-withoutautoml.jpg)

    * Po zainstalowaniu zestawu SDK usługi Azure Machine Learning _z_ automatyczne usługi machine learning ![zestawu SDK za pomocą zautomatyzowanego uczenia maszynowego zainstalowana w usłudze Databricks ](./media/how-to-configure-environment/automlonadb.jpg)

   Jeśli ta czynność zakończy się niepowodzeniem, należy ponownie uruchomić klastra, wykonując następujące czynności:

   a. W okienku po lewej stronie wybierz **klastrów**. 
   
   b. W tabeli wybierz nazwę klastra. 

   c. Na **bibliotek** zaznacz **ponowne uruchomienie**.

1. Pobierz [pliku archiwum notesu zestawu SDK usługi Azure Databricks/Azure Machine Learning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks/Databricks_AMLSDK_1-4_6.dbc).

   >[!Warning]
   > Wiele notesów próbki są dostępne do użycia z usługą Azure Machine Learning. Tylko [notesów te przykładowe](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks) pracy z usługą Azure Databricks.

1.  [Importowanie pliku archiwum](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-manage.html#import-an-archive) do Twojej usługi Databricks klastra i zacznij przeglądać, zgodnie z opisem na [Machine Learning notesów](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks) strony.


## <a id="workspace"></a>Utwórz plik konfiguracji obszaru roboczego

Plik konfiguracji obszaru roboczego jest pliku JSON, który informuje zestawu SDK, jak komunikować się z obszarem roboczym usługi Azure Machine Learning. Plik *config.json*, i ma następujący format:

```json
{
    "subscription_id": "<subscription-id>",
    "resource_group": "<resource-group>",
    "workspace_name": "<workspace-name>"
}
```

Ten plik JSON musi być w strukturze katalogu, który zawiera Twoje skrypty języka Python i notesy Jupyter. Może być w tym samym katalogu, w podkatalogu nazwanym *aml_config*, lub w katalogu nadrzędnym.

Aby użyć tego pliku w kodzie, należy użyć `ws=Workspace.from_config()`. Ten kod ładuje dane z pliku i nawiązuje połączenie z obszarem roboczym.

Można utworzyć pliku konfiguracji na trzy sposoby:

* **Postępuj zgodnie z [szybkiego startu usługi Azure Machine Learning](quickstart-get-started.md)**: A *config.json* plik zostanie utworzony w bibliotece notesy platformy Azure. Plik zawiera informacje o konfiguracji dla obszaru roboczego. Można pobrać lub skopiuj *config.json* do innych środowisk programowania.

* **Ręcznie utworzyć plik**: Przy użyciu tej metody korzystanie z edytora tekstu. Można znaleźć wartości, które przejdź do pliku konfiguracyjnego, przechodząc do obszaru roboczego w [witryny Azure portal](https://portal.azure.com). Skopiuj nazwę obszaru roboczego, grupy zasobów i wartości Identyfikatora subskrypcji i używać ich w pliku konfiguracji.

     ![Azure Portal](./media/how-to-configure-environment/configure.png)

* **Programistyczne tworzenie pliku**: W poniższym fragmencie kodu Połącz się z obszarem roboczym, podając identyfikator subskrypcji, grupy zasobów i nazwę obszaru roboczego. Następnie zapisuje Konfiguracja obszaru roboczego do pliku:

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

    Ten kod, zapisuje plik konfiguracyjny *aml_config/config.json* pliku.

## <a name="next-steps"></a>Kolejne kroki

- [Uczenie modelu](tutorial-train-models-with-aml.md) w usłudze Azure Machine Learning z zestawem danych mnist ręcznie ZAPISANYCH]
- Widok [Azure Machine Learning SDK dla języka Python](https://aka.ms/aml-sdk) odwołania
- Dowiedz się więcej o [SDK przeznaczonego do przygotowania usługi Azure Machine Learning danych](https://aka.ms/data-prep-sdk)
