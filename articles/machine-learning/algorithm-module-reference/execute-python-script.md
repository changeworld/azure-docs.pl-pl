---
title: 'Wykonaj skrypt języka Python: odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak uruchomić kod w języku Python przy użyciu modułu uruchamiania skryptów języka Python w Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/10/2020
ms.openlocfilehash: 3370c7ebb8e0253543e6b9cb6ce7614811fb5bd0
ms.sourcegitcommit: d322d0a9d9479dbd473eae239c43707ac2c77a77
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/12/2020
ms.locfileid: "79140794"
---
# <a name="execute-python-script-module"></a>Wykonaj moduł skryptu języka Python

W tym artykule opisano moduł w programie Azure Machine Learning Designer (wersja zapoznawcza).

Użyj tego modułu, aby uruchomić kod języka Python. Aby uzyskać więcej informacji na temat architektury i zasad projektowania języka Python, zobacz [następujący artykuł](https://docs.microsoft.com/azure/machine-learning/machine-learning-execute-python-scripts).

W języku Python można wykonywać zadania, które nie są obecnie obsługiwane przez istniejące moduły, takie jak:

+ Wizualizacja danych przy użyciu `matplotlib`
+ Wyliczanie zestawów danych i modeli w obszarze roboczym przy użyciu bibliotek języka Python
+ Odczytywanie, ładowanie i manipulowanie danymi ze źródeł nieobsługiwanych przez moduł [Import danych](./import-data.md)
+ Uruchom własny kod uczenia głębokiego 


Azure Machine Learning używa dystrybucji Anaconda języka Python, która obejmuje wiele typowych narzędzi do przetwarzania danych. Zostanie automatycznie zaktualizowana wersja Anaconda. Bieżąca wersja:
 -  Anaconda 4.5 + Distribution for Python 3,6 

Wstępnie zainstalowane pakiety są następujące:
-  asn1crypto==0.24.0
- attrs==19.1.0
- azure-common==1.1.18
- azure-storage-blob==1.5.0
- azure-storage-common==1.4.0
- certifi==2019.3.9
- cffi==1.12.2
- chardet==3.0.4
- cryptography==2.6.1
- distro==1.4.0
- idna==2.8
- jsonschema==3.0.1
- lightgbm = = 2.2.3
- more-itertools==6.0.0
- numpy = = 1.16.2
- pandas==0.24.2
- Pillow==6.0.0
- pip==19.0.3
- pyarrow==0.12.1
- pycparser==2.19
- pycryptodomex==3.7.3
- pyrsistent==0.14.11
- python-dateutil==2.8.0
- pytz==2018.9
- requests==2.21.0
- scikit-learn==0.20.3
- scipy = = 1.2.1
- setuptools==40.8.0
- six==1.12.0
- torch==1.0.1.post2
- torchvision = = 0.2.2. Post3
- urllib3 = = 1.24.1
- koło = = 0.33.1 

 Aby zainstalować inne pakiety spoza wstępnie zainstalowanej listy, na przykład *scikit-misc*, Dodaj następujący kod do skryptu: 

 ```python
import os
os.system(f"pip install scikit-misc")
```

## <a name="upload-files"></a>Przekazywanie plików
**Skrypt Execute Python** obsługuje przekazywanie plików przy użyciu [Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py#upload-file-name--path-or-stream-).

Poniższy przykład pokazuje, jak przekazać plik obrazu w module **wykonywania skryptu języka Python** :

```Python

# The script MUST contain a function named azureml_main
# which is the entry point for this module.

# imports up here can be used to
import pandas as pd

# The entry point function can contain up to two input arguments:
#   Param<dataframe1>: a pandas.DataFrame
#   Param<dataframe2>: a pandas.DataFrame
def azureml_main(dataframe1 = None, dataframe2 = None):

    # Execution logic goes here
    print(f'Input pandas.DataFrame #1: {dataframe1}')

    from matplotlib import pyplot as plt
    plt.plot([1, 2, 3, 4])
    plt.ylabel('some numbers')
    img_file = "line.png"
    plt.savefig(img_file)

    from azureml.core import Run
    run = Run.get_context(allow_offline=True)
    run.upload_file(f"graphics/{img_file}", img_file)

    # Return value must be of a sequence of pandas.DataFrame
    # E.g.
    #   -  Single return value: return dataframe1,
    #   -  Two return values: return dataframe1, dataframe2
    return dataframe1,
}
```

Po pomyślnym przesłaniu potoku można wyświetlić podgląd obrazu w prawym panelu modułu

[!div class="mx-imgBorder"]
![przekazane —](media/module/upload-image-in-python-script.png) obrazu

## <a name="how-to-configure-execute-python-script"></a>Jak skonfigurować skrypt wykonywania skryptu języka Python

Moduł **wykonywania skryptu języka Python** zawiera przykładowy kod w języku Python, którego można użyć jako punktu wyjścia. Aby skonfigurować moduł **wykonywania skryptu języka Python** , należy podać zestaw danych wejściowych i kod języka Python do wykonania w polu tekstowym **skrypt języka Python** .

1. Dodaj moduł **wykonywania skryptu języka Python** do potoku.

2. Dodaj i Połącz **pozycję DataSet1** dowolnych zestawów danych z projektanta, który ma być używany na potrzeby danych wejściowych. Odwołuje się do tego zestawu danych w skrypcie języka Python jako **DataFrame1**.

    Użycie zestawu danych jest opcjonalne, jeśli chcesz wygenerować dane przy użyciu języka Python, lub użyj kodu Python, aby zaimportować dane bezpośrednio do modułu.

    Ten moduł obsługuje Dodawanie drugiego zestawu danych w **Dataset2**. Odwołuje się do drugiego zestawu danych w skrypcie języka Python jako DataFrame2.

    Zestawy danych przechowywane w Azure Machine Learning są automatycznie konwertowane na dane **Pandas** . ramki po załadowaniu tego modułu.

    ![Wykonaj mapę wejściową języka Python](media/module/python-module.png)

4. Aby uwzględnić nowe pakiety lub kod w języku Python, Dodaj plik spakowane zawierający te zasoby niestandardowe w **pakiecie skryptu**. Dane wejściowe do **pakietu skryptu** muszą być plikiem skompresowanym przekazanym do obszaru roboczego jako zestaw danych typu plików. Można przekazać zestaw danych na stronie zasobów **zestawy** danych, a następnie przeciągnąć i upuścić moduł DataSet z listy **moje zbiory** w lewym drzewie modułów na stronie Tworzenie projektanta. 

    Podczas wykonywania potoku można użyć dowolnego pliku zawartego w przekazanym skompresowanym archiwum. Jeśli archiwum zawiera strukturę katalogów, struktura jest zachowywana, ale należy dołączać katalog o nazwie **src** do ścieżki.

5. W polu tekstowym **skrypt języka Python** wpisz lub wklej prawidłowy skrypt w języku Python.

    Pole tekstowe **skrypt języka Python** jest wstępnie wypełnione kilkoma instrukcjami w komentarzach i przykładowym kodzie na potrzeby dostępu do danych i wyjścia. Należy edytować lub zamienić ten kod. Pamiętaj, aby przestrzegać Konwencji języka Python dotyczących wcięć i wielkości liter.

    + Skrypt musi zawierać funkcję o nazwie `azureml_main` jako punkt wejścia dla tego modułu.
    + Funkcja punktu wejścia może zawierać maksymalnie dwa argumenty wejściowe: `Param<dataframe1>` i `Param<dataframe2>`
    + Pliki spakowane połączone z trzecim portem wejściowym są rozpakowane i przechowywane w katalogu, `.\Script Bundle`, który jest również dodawany do `sys.path`języka Python. 

    W związku z tym, jeśli plik zip zawiera `mymodule.py`, zaimportuj go przy użyciu `import mymodule`.

    + Do projektanta można zwrócić dwa zestawy danych, które muszą być sekwencją typu `pandas.DataFrame`. Możesz tworzyć inne dane wyjściowe w kodzie języka Python i zapisywać je bezpośrednio w usłudze Azure Storage.

6. Uruchom potok lub wybierz moduł, a następnie kliknij pozycję **Uruchom wybrane** , aby uruchomić tylko skrypt języka Python.

    Wszystkie dane i kod są ładowane do maszyny wirtualnej i uruchamiane przy użyciu określonego środowiska języka Python.

## <a name="results"></a>Wyniki

Wyniki wszelkich obliczeń wykonanych przez osadzony kod języka Python muszą zostać dostarczone jako Pandas. Ramka Dataframe, która jest automatycznie konwertowana do formatu zestawu danych Azure Machine Learning, dzięki czemu można używać wyników z innymi modułami w potoku.

Moduł zwraca dwa zestawy danych:  
  
+ **Zestaw danych wyników 1**, zdefiniowany przez pierwszą zwróconą ramkę datapandas w skrypcie języka Python

+ **Zestaw danych wynikowych 2**, zdefiniowany przez drugą zwróconą ramkę datapandas w skrypcie języka Python


## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning. 