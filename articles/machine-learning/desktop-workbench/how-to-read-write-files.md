---
title: Odczytuj i zapisuj pliki dużych ilości danych | Dokumentacja firmy Microsoft
description: Odczytu i zapisu dużych plików w usłudze Azure Machine Learning doświadczeń.
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 09/10/2017
ROBOTS: NOINDEX
ms.openlocfilehash: 4a2dff4dd57bdb0b010bbb4568d796f1e197a728
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46971505"
---
# <a name="persisting-changes-and-working-with-large-files"></a>Utrwalanie zmian i Praca z dużymi plikami

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)]

Usługa eksperymentowanie w usłudze Machine Learning Azure można skonfigurować szereg celów wykonania. Niektóre cele są lokalne, np. komputera lokalnego lub kontenerze platformy Docker na komputerze lokalnym. Inne są zdalnej, np. kontenerze platformy Docker na komputerze zdalnym lub klaster usługi HDInsight. Aby uzyskać więcej informacji, zobacz [Omówienie usługi Azure Machine Learning wypróbować usługę wykonywania](experimentation-service-configuration.md). 

Zanim będzie możliwe wykonanie w miejscu docelowym, należy skopiować folder projektu, do obliczeniowego elementu docelowego. Należy wykonać, nawet w przypadku lokalnego wykonywania, który używa lokalnego folderu tymczasowego dla tego celu. 

## <a name="execution-isolation-portability-and-reproducibility"></a>Wykonanie izolacji, przenoszenia i powtarzalności
Celem tego projektu jest zapewnienie izolacji, odtwarzaniem i przenośność wykonywania. Jeśli wykonywane jest dwa razy ten sam skrypt, w tym samym lub innym obliczeniowego elementu docelowego, pojawi się te same wyniki. Zmiany wprowadzone podczas pierwszego wykonania nie powinien wpływać na znajdującymi się na drugiego wykonywania. W tym projekcie można traktować jako zasobów obliczeniowych bezstanowych, każdy o bez koligacji do zadań, które są wykonywane po zakończeniu obliczeniowych elementów docelowych.

## <a name="challenges"></a>Wyzwania
Mimo że ten projekt zapewnia korzyści z przenośności i powtarzalność, stwarza również pewne wyzwania.

### <a name="persisting-state-changes"></a>Utrwalanie zmian stanu
Jeśli skrypt modyfikuje stan kontekstu obliczeniowego, zmiany nie są zachowywane dla następnego wykonywanie i mogą one nie propagowany z powrotem do komputera klienckiego automatycznie. 

Dokładniej mówiąc czy skrypt tworzy podfolder zapisuje pliku, folderu lub pliku nie będzie obecny w katalogu projektu po wykonaniu. Pliki są przechowywane w folderze temp w środowisku docelowym obliczeń. Można ich używać do debugowania, ale nie można polegać na istnienie stałe.

### <a name="working-with-large-files-in-the-project-folder"></a>Praca z dużymi plikami w folderze projektu

Jeśli folder projektu zawiera wszelkie duże pliki, wiąże się z opóźnieniem gdy folder jest kopiowany do docelowej środowisko obliczeniowe na początku wykonywania. Nawet wykonanie ma miejsce lokalnie, czy ruch dyskowy nadal zbędne zapobiegających. Z tego powodu firma Microsoft obecnie limit rozmiaru maksymalnego projektu na 25 MB.

## <a name="option-1-use-the-outputs-folder"></a>Opcja 1: Użycie *generuje* folderu
Ta opcja jest preferowana, jeśli mają zastosowanie następujące warunki:
* Skrypt generuje pliki.
* Oczekujesz, że pliki można zmienić za pomocą każdego eksperymentu.
* Chcesz zachować historię tych plików. 

Typowe przypadki użycia to:
* Uczenie modelu
* Tworzenie zestawu danych
* Kreślenia wykresu jako pliku obrazu, w ramach szkoleń modelowych wykonywanie 

>[!Note]
> Maksymalny rozmiar rejestrowanych pliku w folderze danych wyjściowych po uruchomieniu jest 512 MB. Oznacza to, jeśli skrypt generuje plik przekracza 512 MB w folderze danych wyjściowych, go nie są zbierane istnieje. 

Ponadto które chcesz porównać dane wyjściowe we wszystkich przebiegach aktualizacji, wybierz plik wyjściowy (na przykład model), który został utworzony przez poprzednie uruchomienia, a następnie użyj go dla kolejnych zadań (na przykład oceniania).

Może zapisywać pliki w folderze o nazwie *generuje* , jest określana względem katalogu głównego. Folder odbiera specjalnego traktowania, usługa eksperymentowanie w usłudze. Cokolwiek skrypt utworzy w folderze, podczas wykonywania, takich jak plik modelu, plik danych lub plik obrazu wykreślona (łącznie znane jako _artefaktów_), zostały skopiowane do konta magazynu obiektów Blob platformy Azure, który jest skojarzony z usługi konta eksperymentowania, po zakończeniu przebiegu. Pliki stają się częścią Twojej rekordu historii uruchamiania.

Poniżej przedstawiono krótki przykład kodu do przechowywania modelu w *generuje* folderu:
```python
import os
import pickle

# m is a scikit-learn model. 
# we serialize it into a mode.plk file under the ./outputs folder.
with open(os.path.join('.', 'outputs', 'model.pkl'), 'wb') as f:    
    pickle.dump(m, f)
```
Możesz pobrać wszystkie artefaktu, przechodząc do **pliki wyjściowe** sekcji na stronie szczegółów uruchomienia konkretnej uruchamiane w aplikacji Azure Machine Learning Workbench. Po prostu wybierz działanie, a następnie wybierz **Pobierz** przycisku. Alternatywnie można wprowadzić `az ml asset download` polecenia w oknie interfejsu wiersza polecenia (CLI).

Aby uzyskać pełniejszy przykład, zobacz `iris_sklearn.py` skryptu języka Python w _klasyfikowanie irysów_ przykładowy projekt.

## <a name="option-2-use-the-shared-folder"></a>Opcja 2: Użycie folderu udostępnionego
Jeśli nie potrzebujesz do przechowywania historii plików poszczególnymi uruchomieniami, za pomocą folderu udostępnionego, który można uzyskać dostęp we wszystkich przebiegach aktualizacji niezależnych może być świetnym rozwiązaniem w następujących scenariuszach: 
- Skrypt musi ładowanie danych z plików lokalnych, takich jak pliki CSV lub katalog plików tekstowych lub graficznych, jak w przypadku danych szkoleniowych lub testowym. 
- Skrypt przetwarza dane pierwotne i zapisuje wyniki pośrednie, takich jak dane szkoleniowe neural z plików tekstowych lub graficznych, które są używane w kolejnych szkolenia, uruchom. 
- Skrypt podaje modelu, a kolejne skrypt oceniania musi wybrać model i użyć jej do oceny. 

Należy zauważyć folder udostępniony znajduje się na urządzenie docelowe obliczeniowego wybranego lokalnie. W związku z tym ta opcja działa tylko wtedy, gdy wszystko, czego przebiegów skryptu odwołujące się do tego folderu udostępnionego są wykonywane na tym samym obliczeniowego elementu docelowego i obliczeniowego elementu docelowego nie zostanie odtworzony między działa.

Dzięki wykorzystaniu funkcji folder udostępniony, można odczytać lub zapisać do specjalnego folderu, który jest identyfikowany przez zmienną środowiskową `AZUREML_NATIVE_SHARE_DIRECTORY`. 

### <a name="example"></a>Przykład
Poniżej przedstawiono przykładowy kod języka Python dla korzystać z tego folderu udziału do odczytu i zapisu do pliku tekstowego:
```python
import os

# write to the shared folder
with open(os.environ['AZUREML_NATIVE_SHARE_DIRECTORY'] + 'test.txt', "w") as f1:
    f1.write(“Hello World”)

# read from the shared folder
with open(os.environ['AZUREML_NATIVE_SHARE_DIRECTORY'] + 'test.txt', "r") as f2:
    text = f2.read()
```

Aby uzyskać pełniejszy przykład, zobacz *iris_sklearn_shared_folder.py* w pliku _klasyfikowanie irysów_ przykładowy projekt.

Przed użyciem tej funkcji, musisz ustawić *.compute* pliku prostego różne konfiguracje, które reprezentują kontekstu wykonania docelowych w *aml_config* folderu. Rzeczywiste ścieżkę do folderu, zależy od docelowej obliczeń, wybór i wartości, które można skonfigurować.

### <a name="configure-local-compute-context"></a>Konfigurowanie w lokalnym kontekście obliczeniowym

Aby włączyć tę funkcję w lokalnym kontekście obliczeniowym, po prostu dodać do swojej *.compute* pliku następujący wiersz, który reprezentuje _lokalnego_ środowiska (zwykle o nazwie *local.compute*).
```
# local.runconfig
...
nativeSharedDirectory: ~/.azureml/share
...
```

~/.Azureml/share ścieżki jest domyślna ścieżka folderu podstawowego. Możesz zmienić ją na lokalną ścieżkę bezwzględną, który jest dostępny dla uruchomienia skryptu. Nazwa konta eksperymentowania, nazwa obszaru roboczego i nazwę projektu są automatycznie dołączane do nazwy podstawowego katalogu, która staje się pełna ścieżka udostępnionego katalogu. Na przykład pliki można być zapisywane (i pobierane z) wartość domyślna w następującej ścieżce, jeśli używasz kroku:

```
# on Windows
C:\users\<username>\.azureml\share\<exp_acct_name>\<workspace_name>\<proj_name>\

# on macOS
/Users/<username>/.azureml/share/<exp_acct_name>/<workspace_name>/<proj_name>/
```

### <a name="configure-the-docker-compute-context-local-or-remote"></a>Konfigurowanie kontekstu obliczeniowego platformy Docker (lokalnym lub zdalnym)
Aby włączyć tę funkcję w kontekście obliczeniowym aparatu Docker, należy dodać następujące dwa wiersze do platformy Docker lokalnym lub zdalnym *.compute* pliku.

```
# docker.compute
...
sharedVolumes: true
nativeSharedDirectory: ~/.azureml/share
...
```
>[!IMPORTANT]
>**SharedVolumes** właściwość musi być równa *true* zastosowania `AZUREML_NATIVE_SHARE_DIRECTORY` zmiennej środowiskowej, aby uzyskać dostęp do udostępnionego folderu. W przeciwnym razie wykonanie nie powiedzie się.

Kod uruchomiony w kontenerze platformy Docker, zawsze będzie widział ten folder udostępniony jako */azureml-share /*. Ścieżka folderu widziany przez kontener platformy Docker nie konfiguruje się. Nie należy używać tej nazwy folderu w kodzie. Zamiast tego należy zawsze używać nazwa zmiennej środowiskowej `AZUREML_NATIVE_SHARE_DIRECTORY` do odwoływania się do tego folderu. Jest mapowany na folder lokalny na hoście platformy Docker machine lub kontekstu obliczeniowego. Katalog podstawowy tego folderu lokalnego jest wartość możliwa do skonfigurowania `nativeSharedDirectory` w *.compute* pliku. Ścieżka lokalna folderu udostępnionego na maszynie hosta, korzystając z wartością domyślną jest następująca:
```
# Windows
C:\users\<username>\.azureml\share\<exp_acct_name>\<workspace_name>\<proj_name>\

# macOS
/Users/<username>/.azureml/share/<exp_acct_name>/<workspace_name>/<proj_name>/

# Ubuntu Linux
/home/<username>/.azureml/share/<exp_acct_name>/<workspace_name>/<proj_name>/
```

>[!NOTE]
>Ścieżka folderu udostępnionego na dysku lokalnym jest taki sam, czy jest ono w lokalnym kontekście obliczeniowym lub lokalnego kontekstu obliczeniowego platformy Docker. Oznacza to, że możesz nawet udostępniać pliki między lokalne uruchomienie i lokalne uruchomienie platformy Docker.

Można umieścić dane wejściowe bezpośrednio w tych folderach i oczekiwane, czy uruchomienia aparatu Docker na komputerze lokalnym lub w każdej chwili można pobrać go. Można również zapisywać pliki do tego folderu z lokalnych lub platformy Docker działa i spodziewać się, że pliki Pobierz utrwalone w tym folderze pozostałych wykonanie cyklu życia.

Aby uzyskać więcej informacji, zobacz [pliki konfiguracji usługi Azure Machine Learning Workbench wykonywania](experimentation-service-configuration-reference.md).

>[!NOTE]
>`AZUREML_NATIVE_SHARE_DIRECTORY` Zmienna środowiskowa nie jest obsługiwana w kontekście obliczeniowym HDInsight. Jednak to łatwo osiągnąć ten sam wynik w sposób jawny przy użyciu ścieżki bezwzględnej magazynu obiektów Blob platformy Azure do odczytu i zapisu związane z magazynem obiektów blob dołączonych.

## <a name="option-3-use-external-durable-storage"></a>Opcja 3: Użyj zewnętrznego magazynu trwałego

Zewnętrzne trwałego magazynu umożliwia utrwalanie stanu w czasie wykonywania. Ta opcja jest przydatna w następujących scenariuszach:
- Dane wejściowe już są przechowywane w magazynie trwałym, który jest dostępny z docelowego środowiska obliczeniowego.
- Pliki nie muszą być częścią rekordy historii uruchamiania.
- Pliki muszą być udostępnione przez wykonań w różnych środowiskach obliczeniowych.
- Pliki musi mieć możliwość przetrwać kontekstu obliczeniowego.

Jedno z podejść jest usługa Azure Blob storage w kodzie języka Python lub PySpark. Poniżej przedstawiono krótki przykład:

```python
from azure.storage.blob import BlockBlobService
from azure.storage.blob.models import PublicAccess
import glob
import os

ACCOUNT_NAME = "<your blob storage account name>"
ACCOUNT_KEY = "<account key>"
CONTAINER_NAME = "<container name>"

blob_service = BlockBlobService(account_name=ACCOUNT_NAME, account_key=ACCOUNT_KEY)

## Create a new container if necessary, or use an existing one
blob_service.create_container(CONTAINER_NAME, fail_on_exist=False, public_access=PublicAccess.Container)

# df is a pandas DataFrame
df.to_csv('mydata.csv', sep='\t', index=False)

# Export the mydata.csv file to Blob storage.
for name in glob.iglob('mydata.csv'):
    blob_service.create_blob_from_path(CONTAINER_NAME, 'single_file.csv', name)
```

Poniżej przedstawiono krótki przykład dołączanie dowolnego magazynu obiektów Blob platformy Azure do środowiska uruchomieniowego usługi HDI Spark:
```python
def attach_storage_container(spark, account, key):
    config = spark._sc._jsc.hadoopConfiguration()
    setting = "fs.azure.account.key." + account + ".blob.core.windows.net"
    if not config.get(setting):
        config.set(setting, key)
 
attach_storage_container(spark, "<storage account name>", "<storage key>”)
```

## <a name="conclusion"></a>Podsumowanie
Ponieważ usługa Azure Machine Learning wykonuje skrypty przez skopiowanie całego folderu projektu do kontekstu obliczeniowego docelowej, należy zwrócić szczególną uwagę przy użyciu dużych danych wejściowych, wyjściowych i pośrednie pliki. W przypadku plików o dużym rozmiarze transakcji można użyć folderu specjalne dane wyjściowe, folderu udostępnionego, który jest dostępny za pośrednictwem `AZUREML_NATIVE_SHARE_DIRECTORY` zmiennej środowiskowej lub zewnętrznego magazynu trwałego. 

## <a name="next-steps"></a>Kolejne kroki
- Przegląd [pliki konfiguracji usługi Azure Machine Learning Workbench wykonywania](experimentation-service-configuration-reference.md) artykułu.
- Zobacz jak [klasyfikowanie irysów](tutorial-classifying-iris-part-1.md) samouczek projekt korzysta z folderu danych wyjściowych do utrwalenia uczonego modelu.
