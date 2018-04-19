---
title: "Odczyt i zapis plików dużej ilości danych | Dokumentacja firmy Microsoft"
description: "Odczytywanie i zapisywanie dużych plików w eksperymentów w usłudze Azure Machine Learning."
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/10/2017
ms.openlocfilehash: 099ff69b396c35730471d684b59115f03ccf67d9
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/08/2018
---
# <a name="persisting-changes-and-working-with-large-files"></a>Utrwalanie zmian i Praca z dużymi plikami
W usłudze Azure Machine Learning eksperymenty można skonfigurować różne realizacji celów. Niektóre elementy docelowe są lokalne, na przykład komputer lokalny lub kontener Docker na komputerze lokalnym. Inne są zdalnego, takich jak kontener Docker na komputerze zdalnym lub klastra usługi HDInsight. Aby uzyskać więcej informacji, zobacz [Omówienie usługi Azure Machine Learning eksperymentu usługi wykonywania](experimentation-service-configuration.md). 

Zanim można wykonywać w celu, należy skopiować folder projektu do docelowego obliczeń. Należy więc nawet w przypadku lokalnego wykonywania, który używa lokalnego folderu tymczasowego w tym celu. 

## <a name="execution-isolation-portability-and-reproducibility"></a>Wykonanie izolacji, przenośność i powtarzalności
Celem tego projektu jest zapewnienie izolacji, powtarzalności i przenośność wykonywania. Jeśli dwa razy ten sam skrypt zostanie wykonana na tym samym lub innym obliczeniowe docelowej, pojawi się takie same wyniki. Zmiany wprowadzone podczas wykonywania pierwszej nie powinna wpłynąć na drugi wykonywania. Ten projekt można traktować obliczeniowe elementy docelowe jako zasobów obliczeniowych bezstanowych, mając bez koligacji do zadań, które są wykonywane po zakończeniu.

## <a name="challenges"></a>Wyzwania
Mimo że ten projekt zalety przenośności i powtarzalności, stwarza również niektórych wyjątkowe wyzwanie.

### <a name="persisting-state-changes"></a>Trwałych zmian stanu
Jeśli skrypt modyfikuje stan kontekstu obliczeń, zmiany nie są zachowywane wykonywania dalej i ich jest nie propagowane do komputera klienckiego automatycznie. 

W szczególności jeśli skrypt tworzy podfolder lub zapisuje plik, folderu lub pliku nie będzie obecne w katalogu projektu po wykonaniu. Pliki są przechowywane w folder tymczasowy w środowisku docelowym obliczeń. Można ich używać do debugowania, ale nie może polegać na istnienie trwałych.

### <a name="working-with-large-files-in-the-project-folder"></a>Praca z dużymi plikami w folderze projektu

Jeśli folder projektu zawiera wszystkie duże pliki, ponosisz opóźnienia, gdy folder jest kopiowany do środowiska obliczeniowego docelowego na początku wykonywania. Nawet jeśli wykonanie występuje lokalnie, jest nadal niepotrzebnych dysku ruch do uniknięcia. Z tego powodu możemy obecnie cap rozmiar maksymalny projektu na 25 MB.

## <a name="option-1-use-the-outputs-folder"></a>Opcja 1: Użycie *generuje* folderu
Ta opcja jest preferowana, jeśli mają zastosowanie następujące warunki:
* Skrypt tworzy pliki.
* Spodziewasz się pliki, aby zmienić z każdym eksperymentu.
* Chcesz zachować historię tych plików. 

Typowe przypadki użycia są:
* Uczenie modelu
* Tworzenie zestawu danych
* Kreślenia wykres jako plik obrazu, w ramach Twojej wykonywania uczenia modelu 

Ponadto chcesz porównać dane wyjściowe w działa, wybierz plik wyjściowy (na przykład model), który został utworzony przez poprzednie Uruchom, a następnie użyć go do kolejnych zadań (na przykład oceniania).

Można zapisać pliki w folderze o nazwie *generuje* to względem katalogu głównego. Folder odbiera szczególnego traktowania przez usługę eksperymenty. Niczego skrypt tworzy w folderze, podczas wykonywania, takie jak plik modelu, pliku danych lub pliku obrazu kreślony (zbiorczo znane jako _artefakty_), jest kopiowany do konta magazynu obiektów Blob platformy Azure, który jest skojarzony z sieci Konto eksperymenty po zakończeniu uruchom. Pliki stają się częścią rekordu historii wykonywania.

Oto krótkie przykład kodu do przechowywania modelu w *generuje* folderu:
```python
import os
import pickle

# m is a scikit-learn model. 
# we serialize it into a mode.plk file under the ./outputs folder.
with open(os.path.join('.', 'outputs', 'model.pkl'), 'wb') as f:    
    pickle.dump(m, f)
```
Dowolnym artefakcie można pobrać, przechodząc do **pliki wyjściowe** sekcji strony szczegółów wykonywania konkretnej Uruchom w konsoli usługi Azure Machine Learning Workbench. Po prostu wybierz Uruchom, a następnie wybierz **Pobierz** przycisku. Alternatywnie można wprowadzić `az ml asset download` polecenie w oknie wiersza polecenia (CLI).

Na przykład bardziej szczegółowy, zobacz `iris_sklearn.py` skryptu języka Python w _klasyfikowania Iris_ przykładowy projekt.

## <a name="option-2-use-the-shared-folder"></a>Opcja 2: Użycie folderu udostępnionego
Jeśli nie ma potrzeby przechowywania historii plików każdym uruchomieniu, za pomocą folderu udostępnionego, który jest dostępny w działa niezależnie od można doskonałe rozwiązanie w następujących scenariuszach: 
- Skrypt wymaga do ładowania danych z lokalnych plików, takich jak pliki CSV lub w katalogu plików tekstowych lub graficznych, jako dane szkoleniowe lub testu. 
- Skrypt przetwarza dane pierwotne i zapisuje wyniki pośrednie, takich jak dane szkoleniowe featurized z plików tekstowych lub graficznych, które są używane w kolejnych szkolenia, uruchom. 
- Skrypt podaje modelu, a kolejne skryptu oceniania musi Podnieś modelu i użyć go do oceny. 

Należy pamiętać lokalnie przebywa folder udostępniony na docelowym wybranym obliczeń. W związku z tym ta opcja działa tylko wtedy, gdy wszystkie Twoje skrypt będzie uruchamiany odwołujące się do tego folderu udostępnionego są wykonywane na tym samym obliczeniowe docelowych i cel obliczeniowych nie jest odtworzenie między działa.

Dzięki wykorzystaniu funkcji folderu udostępnionego, może odczytać lub zapisać do specjalnego folderu, który jest identyfikowany przez zmienną środowiskową `AZUREML_NATIVE_SHARE_DIRECTORY`. 

### <a name="example"></a>Przykład
Oto niektóre przykładowy kod języka Python do odczytu i zapisu do pliku tekstowego przy użyciu ten folder udziału:
```python
import os

# write to the shared folder
with open(os.environ['AZUREML_NATIVE_SHARE_DIRECTORY'] + 'test.txt', 'wb') as f:
    f.write(“Hello World”)

# read from the shared folder
with open(os.environ['AZUREML_NATIVE_SHARE_DIRECTORY'] + 'test.txt', 'r') as f:
    text = file.read()
```

Na przykład bardziej szczegółowy, zobacz *iris_sklearn_shared_folder.py* w pliku _klasyfikowania Iris_ przykładowy projekt.

Przed użyciem tej funkcji, należy ustawić w *.compute* pewne proste konfiguracje, które reprezentują kontekstu wykonywania docelowe w pliku *aml_config* folderu. Rzeczywiste ścieżkę do folderu zależy od docelowego obliczeń, wybrane i wartości, które można skonfigurować.

### <a name="configure-local-compute-context"></a>Skonfiguruj kontekst lokalnym

Aby włączyć tę funkcję w kontekście lokalnym, należy dodać do Twojego *.compute* pliku następujący wiersz, który reprezentuje _lokalnego_ środowiska (zwykle o nazwie *local.compute*).
```
# local.runconfig
...
nativeSharedDirectory: ~/.azureml/share
...
```

~/.Azureml/share ścieżka jest domyślna ścieżka folderu podstawowego. Można zmienić ją na lokalną ścieżkę bezwzględną, który jest dostępny przez uruchomienie skryptu. Nazwa konta eksperymenty, nazwa obszaru roboczego i nazwę projektu są automatycznie dodawane do nazwy katalogu podstawowego, która staje się pełną ścieżkę udostępnionego katalogu. Na przykład pliki można zapisane (i pobierane z) następującej ścieżce, korzystając z poprzednim wartość domyślna:

```
# on Windows
C:\users\<username>\.azureml\share\<exp_acct_name>\<workspace_name>\<proj_name>\

# on macOS
/Users/<username>/.azureml/share/<exp_acct_name>/<workspace_name>/<proj_name>/
```

### <a name="configure-the-docker-compute-context-local-or-remote"></a>Skonfiguruj kontekst obliczeń Docker (lokalnym lub zdalnym)
Aby włączyć tę funkcję w kontekście obliczeń Docker, należy dodać następujące dwa wiersze do rozwiązania Docker lokalnego lub zdalnego *.compute* pliku.

```
# docker.compute
...
sharedVolumes: true
nativeSharedDirectory: ~/.azureml/share
...
```
>[!IMPORTANT]
>**SharedVolumes** musi mieć ustawioną właściwość *true* korzystając `AZUREML_NATIVE_SHARE_DIRECTORY` zmiennej środowiskowej dostępu do folderu udostępnionego. W przeciwnym razie wykonanie nie powiedzie się.

Kod zawsze uruchomione w kontenerze Docker widzi tego folderu udostępnionego jako */azureml-share /*. Ścieżka folderu widziany przez kontener Docker nie konfiguruje się. Nie należy używać tej nazwy folderu w kodzie. Zamiast tego należy zawsze używać zmienna środowiskowa `AZUREML_NATIVE_SHARE_DIRECTORY` do odwoływania się do tego folderu. Jest on zamapowany na folder lokalny na hoście Docker komputera lub obliczeniowe kontekstu. Katalog podstawowy ten folder lokalny jest konfigurowalną wartością `nativeSharedDirectory` w *.compute* pliku. Ścieżka lokalna folderu udostępnionego na komputerze-hoście, korzystając z wartością domyślną jest następujący:
```
# Windows
C:\users\<username>\.azureml\share\<exp_acct_name>\<workspace_name>\<proj_name>\

# macOS
/Users/<username>/.azureml/share/<exp_acct_name>/<workspace_name>/<proj_name>/

# Ubuntu Linux
/home/<username>/.azureml/share/<exp_acct_name>/<workspace_name>/<proj_name>/
```

>[!NOTE]
>Ścieżka folderu udostępnionego na lokalnym dysku jest taki sam, czy w kontekście lokalnym lub lokalny kontekst obliczeń Docker. Oznacza to, że można nawet udostępniać plików między przebiegu lokalnego i lokalnego uruchomienia Docker.

Można umieścić dane wejściowe bezpośrednio w tych folderach i oczekują, że działa Docker na komputerze lokalnym lub w mogą odbierać go. Można również zapisywać pliki w tym folderze z lokalnym lub uruchamia Docker i oczekuje się, że pliki uzyskać utrwalane w tym folderze pozostałych cyklu wykonywania.

Aby uzyskać więcej informacji, zobacz [pliki konfiguracji usługi Azure Machine Learning Workbench wykonywania](experimentation-service-configuration-reference.md).

>[!NOTE]
>`AZUREML_NATIVE_SHARE_DIRECTORY` Zmiennej środowiskowej nie jest obsługiwane w kontekście obliczeń HDInsight. Jednak jest łatwo osiągnąć ten sam rezultat za jawnie odczytywane i zapisywane w magazynie dołączonych obiektów blob za pomocą bezwzględnej ścieżki do magazynu obiektów Blob platformy Azure.

## <a name="option-3-use-external-durable-storage"></a>Opcja 3: Użycie trwałego magazynu zewnętrznego

Trwałe magazynu zewnętrznego umożliwia utrwalić stanu wykonywania. Ta opcja jest przydatna w następujących scenariuszach:
- Dane wejściowe jest już zapisana w magazynie trwałe, który jest dostępny z docelowego środowiska obliczeniowego.
- Pliki nie muszą być częścią rekordy historii wykonywania.
- Pliki muszą być współużytkowane przez wykonaniami w różnych środowiskach obliczeniowych.
- Pliki muszą mieć możliwość przetrwać kontekstu obliczeń.

Jednym z podejść jest używać magazynu obiektów Blob platformy Azure w kodzie języka Python lub PySpark. Oto przykład krótka:

```python
from azure.storage.blob import BlockBlobService
import glob
import os

ACCOUNT_NAME = "<your blob storage account name>"
ACCOUNT_KEY = "<account key>"
CONTAINER_NAME = "<container name>"

blob_service = BlockBlobService(account_name=ACCOUNT_NAME, account_key=ACCOUNT_KEY)

## Create a new container if necessary, or use an existing one
my_service.create_container(CONTAINER_NAME, fail_on_exist=False, public_access=PublicAccess.Container)

# df is a pandas DataFrame
df.to_csv('mydata.csv', sep='\t', index=False)

# Export the mydata.csv file to Blob storage.
for name in glob.iglob('mydata.csv'):
    blob_service.create_blob_from_path(CONTAINER_NAME, 'single_file.csv', name)
```

Oto krótkie przykład do dołączania dowolny dowolnych magazyn obiektów Blob platformy Azure do środowiska wykonawczego HDI Spark:
```python
def attach_storage_container(spark, account, key):
    config = spark._sc._jsc.hadoopConfiguration()
    setting = "fs.azure.account.key." + account + ".blob.core.windows.net"
    if not config.get(setting):
        config.set(setting, key)
 
attach_storage_container(spark, "<storage account name>", "<storage key>”)
```

## <a name="conclusion"></a>Podsumowanie
Ponieważ usługi Azure Machine Learning wykonuje skryptów przez skopiowanie folderu całego projektu do kontekstu obliczeń docelowej, należy zwrócić szczególną uwagę o dużych danych wejściowych, wyjściowych i pośredniczących plików. W przypadku dużych plików transakcji można użyć folderze specjalne wyjścia folderu udostępnionego, który jest dostępny za pośrednictwem `AZUREML_NATIVE_SHARE_DIRECTORY` zmiennej środowiskowej lub trwałego magazynu zewnętrznego. 

## <a name="next-steps"></a>Kolejne kroki
- Przegląd [pliki konfiguracji usługi Azure Machine Learning Workbench wykonywania](experimentation-service-configuration-reference.md) artykułu.
- Zobacz jak [klasyfikowania Iris](tutorial-classifying-iris-part-1.md) projekt samouczka używa folderu danych wyjściowych do utrwalenia uczonego modelu.
