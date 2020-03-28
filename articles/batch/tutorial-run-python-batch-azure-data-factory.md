---
title: Uruchamianie skryptów języka Python za pośrednictwem fabryki danych — azure batch Python
description: Samouczek — dowiedz się, jak uruchamiać skrypty języka Python w ramach potoku za pośrednictwem usługi Azure Data Factory przy użyciu usługi Azure Batch.
services: batch
author: mammask
manager: jeconnoc
ms.service: batch
ms.devlang: python
ms.topic: tutorial
ms.date: 12/11/2019
ms.author: komammas
ms.custom: mvc
ms.openlocfilehash: 2995c5da4491f14471d9ed03022a144a02beab5a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78201836"
---
# <a name="tutorial-run-python-scripts-through-azure-data-factory-using-azure-batch"></a>Samouczek: Uruchamianie skryptów języka Python za pośrednictwem usługi Azure Data Factory przy użyciu usługi Azure Batch

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Uwierzytelnianie przy użyciu kont usług Batch i Storage
> * Tworzenie i uruchamianie skryptu w języku Python
> * Tworzenie puli węzłów obliczeniowych w celu uruchomienia aplikacji
> * Planowanie obciążeń języka Python
> * Monitorowanie potoku analizy
> * Dostęp do plików dziennika

W poniższym przykładzie uruchamia skrypt języka Python, który odbiera dane wejściowe CSV z kontenera magazynu obiektów blob, wykonuje proces manipulowania danymi i zapisuje dane wyjściowe w oddzielnym kontenerze magazynu obiektów blob.

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

* Zainstalowana dystrybucja [języka Python](https://www.python.org/downloads/) do testowania lokalnego.
* Pakiet [platformy Azure.](https://pypi.org/project/azure/) `pip`
* Konto usługi Azure Batch i połączone konto usługi Azure Storage. Aby uzyskać więcej informacji na temat tworzenia i łączenia kont usługi Batch z kontami magazynu, zobacz [Tworzenie konta usługi Batch.](quick-create-portal.md#create-a-batch-account)
* Konto usługi Azure Data Factory. Zobacz [Tworzenie fabryki danych,](../data-factory/quickstart-create-data-factory-portal.md#create-a-data-factory) aby uzyskać więcej informacji na temat tworzenia fabryki danych za pośrednictwem witryny Azure portal.
* [Eksplorator partii](https://azure.github.io/BatchExplorer/).
* [Eksplorator usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure portal w [https://portal.azure.com](https://portal.azure.com).

[!INCLUDE [batch-common-credentials](../../includes/batch-common-credentials.md)]

## <a name="create-a-batch-pool-using-batch-explorer"></a>Tworzenie puli partii przy użyciu Eksploratora wsadowego

W tej sekcji użyjesz Eksploratora wsadowego do utworzenia puli partii, która będzie używana przez potok fabryki danych platformy Azure. 

1. Zaloguj się do Eksploratora wsadowego przy użyciu poświadczeń platformy Azure.
1. Wybierz swoje konto usługi Batch
1. Utwórz pulę, wybierając **pozycję Pule** na pasku po lewej stronie, a następnie przycisk **Dodaj** nad formularzem wyszukiwania. 
    1. Wybierz identyfikator i nazwę wyświetlaną. Użyjemy `custom-activity-pool` w tym przykładzie.
    1. Ustaw typ skali na **Stały rozmiar**i ustaw liczbę dedykowanych węzłów na 2.
    1. W obszarze **Nauki o danych**wybierz pozycję **Dsvm Windows** jako system operacyjny.
    1. Wybierz `Standard_f2s_v2` jako rozmiar maszyny wirtualnej.
    1. Włącz zadanie startowe i `cmd /c "pip install pandas"`dodaj polecenie . Tożsamość użytkownika może pozostać domyślnym **użytkownikiem puli**.
    1. Kliknij przycisk **OK**.

## <a name="create-blob-containers"></a>Tworzenie kontenerów obiektów blob

W tym miejscu utworzysz kontenery obiektów blob, które będą przechowywać pliki wejściowe i wyjściowe dla zadania Wsadowego OCR.

1. Zaloguj się do Eksploratora magazynu przy użyciu poświadczeń platformy Azure.
1. Korzystając z konta magazynu połączonego z kontem usługi Batch, utwórz dwa kontenery obiektów blob (jeden dla plików wejściowych, jeden dla plików wyjściowych), wykonując kroki opisane w [witrynie Utwórz kontener obiektów blob](../vs-azure-tools-storage-explorer-blobs.md#create-a-blob-container).
    * W tym przykładzie zadzwonimy do `input`naszego kontenera wejściowego i naszego kontenera wyjściowego. `output`
1. `main.py` Przekazywanie `iris.csv` i do `input` kontenera wejściowego przy użyciu Eksploratora magazynu, wykonując kroki opisane w [witrynie Zarządzanie obiektami blob w kontenerze obiektów blob](../vs-azure-tools-storage-explorer-blobs.md#managing-blobs-in-a-blob-container)


## <a name="develop-a-script-in-python"></a>Tworzenie skryptu w języku Python

Poniższy skrypt języka `iris.csv` Python ładuje `input` zestaw danych z kontenera, wykonuje proces manipulowania `output` danymi i zapisuje wyniki z powrotem do kontenera.

``` python
# Load libraries
from azure.storage.blob import BlockBlobService
import pandas as pd

# Define parameters
storageAccountName = "<storage-account-name>"
storageKey         = "<storage-account-key>"
containerName      = "output"

# Establish connection with the blob storage account
blobService = BlockBlobService(account_name=storageAccountName,
                               account_key=storageKey
                               )

# Load iris dataset from the task node
df = pd.read_csv("iris.csv")

# Subset records
df = df[df['Species'] == "setosa"]

# Save the subset of the iris dataframe locally in task node
df.to_csv("iris_setosa.csv", index = False)

# Upload iris dataset
blobService.create_blob_from_text(containerName, "iris_setosa.csv", "iris_setosa.csv")
```

Zapisz skrypt `main.py` jako i przekaż go do kontenera **usługi Azure Storage.** Przed przesłaniem go do kontenera obiektów blob należy przetestować i zweryfikować jego funkcjonalność lokalnie:

``` bash
python main.py
```

## <a name="set-up-an-azure-data-factory-pipeline"></a>Konfigurowanie potoku usługi Azure Data Factory

W tej sekcji utworzysz i sprawdź poprawność potoku przy użyciu skryptu Języka Python.

1. Wykonaj kroki, aby utworzyć fabrykę danych w sekcji "Tworzenie fabryki danych" [w tym artykule](../data-factory/quickstart-create-data-factory-portal.md#create-a-data-factory).
1. W polu **Zasoby fabryczne** wybierz przycisk + (plus), a następnie wybierz pozycję **Potok**
1. Na karcie **Ogólne** ustaw nazwę potoku jako "Uruchom Pythona"

    ![](./media/run-python-batch-azure-data-factory/create-pipeline.png)

1. W polu **Działania** rozwiń węzeł **Usługa wsadowa**. Przeciągnij działanie niestandardowe z przybornika **Działania** na powierzchnię projektanta potoku.
1. Na karcie **Ogólne** określ **testPipeline** dla name

    ![](./media/run-python-batch-azure-data-factory/create-custom-task.png)
1. Na karcie **Usługa Azure Batch** dodaj konto **wsadowe** utworzone w poprzednich krokach i **połączenie testuj,** aby upewnić się, że zakończyło się pomyślnie

    ![](./media/run-python-batch-azure-data-factory/integrate-pipeline-with-azure-batch.png)

1. Na karcie **Ustawienia** wprowadź `python main.py`polecenie .
1. W przypadku **usługi połączonej z zasobami**dodaj konto magazynu utworzone w poprzednich krokach. Przetestuj połączenie, aby upewnić się, że jest pomyślne.
1. W **ścieżce folderów**wybierz nazwę kontenera **usługi Azure Blob Storage** zawierającego skrypt języka Python i skojarzone dane wejściowe. Spowoduje to pobranie wybranych plików z kontenera do wystąpień węzła puli przed wykonaniem skryptu Języka Python.

    ![](./media/run-python-batch-azure-data-factory/create-custom-task-py-script-command.png)
1. Aby sprawdzić poprawność ustawień potoku, kliknij pozycję **Weryfikuj** na pasku narzędzi potoku powyżej kanwy. Sprawdź, czy potok został pomyślnie zweryfikowany. Wybierz przycisk &gt;&gt; (strzałka w prawo), aby zamknąć dane wyjściowe weryfikacji.
1. Kliknij **przycisk Debugowanie,** aby przetestować potok i upewnić się, że działa dokładnie.
1. Kliknij **przycisk Publikuj,** aby opublikować potok.
1. Kliknij **przycisk Wyzwalacz,** aby uruchomić skrypt języka Python w ramach procesu wsadowego.

    ![](./media/run-python-batch-azure-data-factory/create-custom-task-py-success-run.png)

### <a name="monitor-the-log-files"></a>Monitorowanie plików dziennika

W przypadku, gdy ostrzeżenia lub błędy są generowane przez `stdout.txt` wykonanie `stderr.txt` skryptu, można wyewidencjonować lub uzyskać więcej informacji na temat danych wyjściowych, który został zarejestrowany.

1. Wybierz **zadania** z lewej strony Eksploratora wsadowego.
1. Wybierz zadanie utworzone przez fabrykę danych. Zakładając, że `custom-activity-pool`nazwano `adfv2-custom-activity-pool`twoją pulę , wybierz opcję .
1. Kliknij zadanie, które miało kod zakończenia awarii.
1. Wyświetlanie `stdout.txt` `stderr.txt` i badanie i diagnozowanie problemu.

## <a name="next-steps"></a>Następne kroki

W tym samouczku zbadano przykład, który nauczył cię, jak uruchamiać skrypty języka Python w ramach potoku za pośrednictwem usługi Azure Data Factory przy użyciu usługi Azure Batch.

Aby dowiedzieć się więcej o usłudze Azure Data Factory, zobacz:

> [!div class="nextstepaction"]
> [Potoki fabryki](../data-factory/introduction.md)
> danych platformy Azure[i działania niestandardowe](../data-factory/concepts-pipelines-activities.md)
> [Custom activities](../data-factory/transform-data-using-dotnet-custom-activity.md)
