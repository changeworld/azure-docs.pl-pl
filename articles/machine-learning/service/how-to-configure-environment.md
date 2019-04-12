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
ms.topic: conceptual
ms.date: 02/24/2019
ms.custom: seodec18
ms.openlocfilehash: d4866a6863143d2228c556a64c8e75c9f273076e
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/11/2019
ms.locfileid: "59489505"
---
# <a name="configure-a-development-environment-for-azure-machine-learning"></a>Konfigurowanie środowiska deweloperskiego dla usługi Azure Machine Learning

W tym artykule dowiesz się, jak skonfigurować środowisko programistyczne do pracy z usługą Azure Machine Learning. Usługa Machine Learning jest niezależny od platformy.

Jedynymi wymogami dla swojego środowiska programowania są 3 języka Python, Anaconda (dla środowiska izolowane) i pliku konfiguracji, który zawiera Twoje informacje o obszarze roboczym usługi Azure Machine Learning.

Ten artykuł koncentruje się na następujących środowisk i narzędzia:

* Azure Notebooks: Usługa notesów programu Jupyter, która jest hostowana w chmurze platformy Azure. Jest najprostszym sposobem na rozpoczęcie pracy, ponieważ zestaw SDK usługi Azure Machine Learning jest już zainstalowany.

* [Maszyna wirtualna do nauki o danych (DSVM)](#dsvm): Wstępnie skonfigurowane rozwoju lub eksperymentowania środowisku w chmurze platformy Azure, który jest przeznaczony do pracy nauki o danych i którą można wdrożyć tylko wystąpienia maszyn wirtualnych procesora CPU lub wystąpienia oparte na procesorze GPU. Python 3, Conda, notesy Jupyter i zestawu SDK usługi Azure Machine Learning są już zainstalowane. Maszyna wirtualna jest powiązana z popularnych uczenia maszynowego i głębokiego uczenia struktur, narzędzi i edytory do tworzenia rozwiązania uczenia maszynowego. Prawdopodobnie jest najbardziej zaawansowane środowiska programistycznego, Machine learning na platformie Azure.

* [Notes Jupyter](#jupyter): Jeśli już używasz notesu programu Jupyter, zestaw SDK zawiera niektóre dodatki, które należy zainstalować.

* [Visual Studio Code](#vscode): Jeśli używasz programu Visual Studio Code, ma pewne przydatne rozszerzenia, które można zainstalować.

* [Usługa Azure Databricks](#aml-databricks): Popularne platformy do analizy danych oparty na platformie Apache Spark. Dowiedz się, jak uzyskać Machine Learning zestawu SDK usługi Azure do klastra, dzięki czemu można wdrażać modele.

* [Notesy platformy Azure](#aznotebooks): Usługa notesów programu Jupyter, która jest hostowana w chmurze platformy Azure. Również prosty sposób rozpocząć pracę, ponieważ zestaw SDK usługi Azure Machine Learning jest już zainstalowany.  

Jeśli już masz środowisko Python 3 lub po prostu ma podstawowe kroki dotyczące instalowania zestawu SDK, zobacz [komputera lokalnego](#local) sekcji.

## <a name="prerequisites"></a>Wymagania wstępne

- Obszar roboczy usługi Azure Machine Learning. Aby utworzyć obszar roboczy, zobacz [Utwórz obszar roboczy usługi Azure Machine Learning](setup-create-workspace.md).

Obszar roboczy to wszystko, czego potrzebujesz, aby zacząć korzystać z usługi [notesów usługi Azure](#aznotebooks), [DSVM](#dsvm), lub [usługi Azure Databricks](#aml-databricks).

Aby zainstalować zestaw SDK środowiska usługi [komputera lokalnego](#local), [serwer notesu programu Jupyter](#jupyter) lub [programu Visual Studio Code](#vscode) należy również:

- Albo [Anaconda](https://www.anaconda.com/download/) lub [Miniconda](https://conda.io/miniconda.html) Menedżera pakietów.

- W systemie Linux lub macOS konieczne powłoki bash.

    > [!TIP]
    > Jeśli jesteś w systemie Linux lub macOS i korzystanie z powłoki niż powłoki bash (na przykład zsh) może być wystąpią błędy, podczas uruchamiania niektórych poleceń. Aby obejść ten problem, należy użyć `bash` polecenie, aby rozpocząć nową powłokę bash, a następnie uruchom polecenia istnieje.

- W Windows należy wiersz polecenia lub wiersz Anaconda (zainstalowanych w ramach pakietu Anaconda i Miniconda).

## <a id="aznotebooks"></a>Notesy platformy Azure

[Notesy platformy Azure](https://notebooks.azure.com) (wersja zapoznawcza) to opracowywanie interakcyjne środowisko w chmurze platformy Azure. To łatwe Rozpoczynanie pracy z usługą Azure Machine Learning rozwoju.

* Zestaw SDK usługi Azure Machine Learning jest już zainstalowana.
* Po utworzeniu obszaru roboczego usługi Azure Machine Learning w witrynie Azure portal, możesz kliknąć przycisk, aby automatycznie skonfigurować środowisko notesu platformy Azure do pracy z obszarem roboczym.

Użyj [witryny Azure portal](https://portal.azure.com) wprowadzenie notesy platformy Azure.  Otwórz obszar roboczy z **Przegląd** zaznacz **wprowadzenie do platformy Azure, notesy**.

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

Podczas korzystania z komputera lokalnego, (które mogą być także zdalnego maszyny wirtualnej), Utwórz środowisko Anaconda i zainstaluj zestaw SDK, wykonując następujące czynności:

1. Pobierz i zainstaluj [Anaconda](https://www.anaconda.com/distribution/#download-section) (w wersji Python 3.7) Jeśli nie jeszcze.

1. Otwórz wiersz Anaconda i Utwórz środowisko przy użyciu następujących poleceń:

    Uruchom następujące polecenie, aby utworzyć środowiska.

    ```shell
    conda create -n myenv python=3.6.5
    ```

    Następnie można uaktywnić środowiska.

    ```shell
    conda activate myenv
    ```

    W tym przykładzie tworzy środowisko przy użyciu języka python 3.6.5, ale można wybrać żadnych szczególnych subversions. Zgodność z zestawu SDK nie może być gwarantowane działanie z niektórych wersji głównych (3.5 + jest zalecane) i zaleca się spróbuj różnych wersji/subversion w środowisku pakietu Anaconda, jeśli wystąpią błędy. Utworzenie środowiska, podczas gdy składniki i pakiety będą pobierane, potrwa kilka minut.

1. Uruchom następujące polecenia w nowe środowisko umożliwiające specyficznymi dla środowiska ipython jądra. To pewność, że oczekiwane jądra i pakietu zaimportować zachowanie podczas pracy z notesów programu Jupyter w środowiskach Anaconda:

    ```shell
    conda install notebook ipykernel
    ```

    Następnie uruchom następujące polecenie, aby utworzyć Jądro:

    ```shell
    ipython kernel install --user
    ```

1. Użyj następujących poleceń, aby zainstalować pakiety:

    To polecenie instaluje podstawowy zestaw SDK usługi Azure Machine Learning, za pomocą notesu i automl dodatki. `automl` Bardzo dużych instalacji jest i może zostać usunięty z nawiasów, jeśli nie zamierzasz uruchomić eksperymenty uczenia maszynowego automatycznych. `automl` Dodatkowe usługi Azure Machine Learning Prep zestawu SDK usługi Data zawiera także domyślnie jako zależność.

     ```shell
    pip install azureml-sdk[notebooks,automl]
    ```

    Użyj tego polecenia, aby zainstalować usługi Azure Machine Learning Prep zestawu SDK usługi Data samodzielnie:

    ```shell
    pip install azureml-dataprep
    ```

   > [!NOTE]
   > Jeśli zostanie wyświetlony komunikat, że nie można odinstalować PyYAML, należy użyć następującego polecenia:
   >
   > `pip install --upgrade azureml-sdk[notebooks,automl] azureml-dataprep --ignore-installed PyYAML`

   Potrwa kilka minut, aby zainstalować zestaw SDK. Zobacz [Przewodnik instalacji](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) Aby uzyskać więcej informacji na temat opcji instalacji.

1. Zainstaluj inne pakiety dla eksperymentów uczenia maszynowego.

    Użyj jednej z poniższych poleceń i Zastąp  *\<nowy pakiet >* przy użyciu pakietu, którą chcesz zainstalować. Instalowanie pakietów za pomocą `conda install` wymaga, że pakiet jest częścią bieżącego kanały (nowych kanałów możesz dodać w chmurze Anaconda).

    ```shell
    conda install <new package>
    ```

    Alternatywnie możesz zainstalować pakiety za pomocą `pip`.

    ```shell
    pip install <new package>
    ```

### <a id="jupyter"></a>Program Jupyter Notebooks

Program Jupyter Notebooks są częścią [projektu Jupyter](https://jupyter.org/). Zapewniają one interaktywne środowisko kodowania, w której utworzono dokumenty, które mieszać kodu na żywo z tekstu opisowego i grafiki. Notesów programu Jupyter są również doskonały sposób, aby udostępniać wyniki innym użytkownikom, ponieważ można zapisać danych wyjściowych sekcje kodu w dokumencie. Notesy Jupyter notebook można zainstalować na wielu różnych platformach.

Procedury w [komputera lokalnego](#local) sekcji instaluje składniki niezbędne do uruchamiania aplikacji Jupyter Notebooks w środowisku pakietu Anaconda. Aby włączyć te składniki w danym środowisku notesu programu Jupyter, wykonaj następujące czynności:

1. Otwórz wiersz Anaconda i aktywacja środowiska.

    ```shell
    conda activate myenv
    ```

1. Uruchom serwer notesu programu Jupyter, za pomocą następującego polecenia:

    ```shell
    jupyter notebook
    ```

1. Aby sprawdzić, czy notesu programu Jupyter, można użyć zestawu SDK, należy utworzyć **New** Notes, wybierz opcję **Python 3** jako jądra, a następnie uruchom następujące polecenie w komórce Notes:

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

1. Jeśli wystąpią problemy, importowanie modułów i otrzymywać `ModuleNotFoundError`, upewnij się, Twoje jądra programu Jupyter jest podłączony do prawidłowej ścieżki dla danego środowiska, uruchamiając następujący kod w komórce w notesie.

    ```python
    import sys
    sys.path
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
Usługa Azure Databricks to oparta na platformie Apache Spark środowisko w chmurze platformy Azure. Zapewnia środowisko notesu na podstawie współpracy przy użyciu klastra obliczeniowego na podstawie procesora CPU lub GPU.

Jak usługa Azure Databricks działa z usługą Azure Machine Learning:
+ Można wytrenuj model przy użyciu MLlib platformy Spark i wdrażanie modelu w usłudze ACI/AKS z usługi Azure databricks. 
+ Można również użyć [automatyczne usługi machine learning](concept-automated-ml.md) możliwości w specjalnych SDK uczenie Maszynowe Azure z usługą Azure Databricks.
+ Możesz użyć usługi Azure Databricks, jako cel obliczenia z [potoku usługi Azure Machine Learning](concept-ml-pipelines.md). 

### <a name="set-up-your-databricks-cluster"></a>Konfigurowanie klastra usługi Databricks

Tworzenie [klastra usługi Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal). Niektóre ustawienia mają zastosowanie tylko w przypadku instalowania zestawu SDK dla zautomatyzowanych uczenia maszynowego w usłudze Databricks.
**Potrwa kilka minut na utworzenie klastra.**

Użyj następujących ustawień:

| Ustawienie |Dotyczy| Wartość |
|----|---|---|
| Nazwa klastra |zawsze| yourclustername |
| Środowisko uruchomieniowe usługi Databricks |zawsze| Środowisko uruchomieniowe żadnych innych ML (innego niż ML 4.x, 5.x) |
| Wersja języka Python |zawsze| 3 |
| Procesy robocze |zawsze| 2 lub nowszy |
| Typy maszyn wirtualnych na węzeł procesu roboczego <br>(określa maksymalna liczba współbieżnych iteracji) |Zautomatyzowane uczenie maszynowe<br>Tylko| Preferowane maszyny Wirtualnej zoptymalizowane pod kątem pamięci |
| Włączanie skalowania automatycznego |Zautomatyzowane uczenie maszynowe<br>Tylko| Usuń zaznaczenie pola wyboru |

Zaczekaj, aż klaster ma zainstalowany przed kontynuowaniem.

### <a name="install-the-correct-sdk-into-a-databricks-library"></a>Zainstaluj poprawny zestaw SDK do biblioteki usługi Databricks
Gdy klaster działa, [utworzyć bibliotekę](https://docs.databricks.com/user-guide/libraries.html#create-a-library) Dołącz odpowiedni pakiet zestawu SDK usługi Azure Machine Learning do klastra. 

1. Wybierz **tylko jeden** opcji (nie instalacji zestawu SDK są obsługiwane)

   |SDK&nbsp;package&nbsp;extras|Element źródłowy|PyPi&nbsp;Name&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
   |----|---|---|
   |Dla usługi Databricks| Przekaż Python Egg lub PyPI | azureml-sdk[databricks]|
   |Dla usługi Databricks - with-<br> automatyczne możliwości usług ML| Przekaż Python Egg lub PyPI | azureml-sdk[automl_databricks]|

   > [!Warning]
   > Można zainstalować inne dodatki zestawu SDK. Wybierz tylko jedną z tych opcji [databricks] lub [automl_databricks].

   * Nie należy wybierać **automatycznie dołączyć się do wszystkich klastrów**.
   * Wybierz **Dołącz** obok swojej nazwy klastra.

1. Monitorowanie błędów, aż stan zmieni się na **dołączone**, co może potrwać kilka minut.  Jeśli ta czynność zakończy się niepowodzeniem, sprawdź następujące informacje: 

   Ponowne uruchomienie klastra przez:
   1. W okienku po lewej stronie wybierz **klastrów**.
   1. W tabeli wybierz nazwę klastra.
   1. Na **bibliotek** zaznacz **ponowne uruchomienie**.
      
   Ponadto należy wziąć pod uwagę:
   + W pliku konfiguracyjnym Automl, korzystając z usługi Azure Databricks, Dodaj następujące parametry:
        1. ```max_concurrent_iterations``` opiera się na liczbę węzłów procesu roboczego w klastrze. 
        2. ```spark_context=sc``` opiera się na domyślny kontekst aparatu spark. 
   + Lub, jeśli masz starszą wersję zestawu SDK, usuń zaznaczenie opcji z zainstalowanych libs klastra i przenosić do Kosza. Instalowanie nowej wersji zestawu SDK i uruchom ponownie klaster. Jeśli występuje problem, po to, odłącz i ponownie podłącz klastra.

Jeśli instalacja się powiodła, zaimportowanej biblioteki powinien wyglądać podobnie jeden z nich:
   
Zestaw SDK dla usługi Databricks **_bez_** automatyczne usługi machine learning ![Machine Learning zestawu SDK usługi Azure dla usługi Databricks](./media/how-to-configure-environment/amlsdk-withoutautoml.jpg)

Zestaw SDK dla usługi Databricks **WITH** automatyczne usługi machine learning ![zestawu SDK za pomocą zautomatyzowanego uczenia maszynowego zainstalowana w usłudze Databricks](./media/how-to-configure-environment/automlonadb.jpg)

### <a name="start-exploring"></a>Rozpocznij eksplorację

Wypróbuj to:
+ Pobierz [pliku archiwum notesu](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks/Databricks_AMLSDK_1-4_6.dbc) dla zestawu SDK usługi Azure Databricks/Azure Machine Learning i [import pliku archiwum](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-manage.html#import-an-archive) do klastra usługi Databricks.  
  Wiele notesów próbki są dostępne, **tylko [notesów te przykładowe](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks) pracy z usługą Azure Databricks.**
  
+ Dowiedz się, jak [tworzenie potoku za pomocą usługi Databricks jako obliczeniowe szkolenia](how-to-create-your-first-pipeline.md).

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

* **Postępuj zgodnie z instrukcjami w [Utwórz obszar roboczy usługi Azure Machine Learning](setup-create-workspace.md#sdk)**: A *config.json* plik zostanie utworzony w bibliotece notesy platformy Azure. Plik zawiera informacje o konfiguracji dla obszaru roboczego. Można pobrać lub skopiuj *config.json* do innych środowisk programowania.

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

- [Uczenie modelu](tutorial-train-models-with-aml.md) w usłudze Azure Machine Learning z zestawem danych mnist ręcznie ZAPISANYCH
- Widok [Azure Machine Learning SDK dla języka Python](https://aka.ms/aml-sdk) odwołania
- Dowiedz się więcej o [SDK przeznaczonego do przygotowania usługi Azure Machine Learning danych](https://aka.ms/data-prep-sdk)
