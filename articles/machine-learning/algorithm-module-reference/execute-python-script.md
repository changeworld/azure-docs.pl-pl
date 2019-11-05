---
title: 'Wykonaj skrypt języka Python: odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak uruchomić kod w języku Python przy użyciu modułu uruchamiania skryptów języka Python w Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: 1ba10bf682d900a45f345f2ebe2707ba1275e94e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73497861"
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
-  asn1crypto = = 0.24.0
- attri = = 19.1.0
- Azure — wspólne = = 1.1.18
- Azure-Storage-BLOB = = 1.5.0
- Azure-Storage-Common = = 1.4.0
- poświadcza = = 2019.3.9
- cffi = = 1.12.2
- chardet = = 3.0.4
- Kryptografia = =
- dystrybucji = = 1.4.0
- IDNA = = 2.8
- jsonschema = = 3.0.1
- lightgbm = = 2.2.3
- Więcej — itertools = = 6.0.0
- numpy = = 1.16.2
- Pandas = = 0.24.2
- Pillow = = 6.0.0
- PIP = = 19.0.3
- pyarrow = = 0.12.1
- pycparser = = 2.19
- pycryptodomex = = 3.7.3
- pyrsistent = = 0.14.11
- Python-dateutil = = 2.8.0
- pytz = = 2018.9
- żądania = = 2.21.0
- scikit-Dowiedz się = = 0.20.3
- scipy = = 1.2.1
- setuptools = = 40.8.0
- sześć = = 1.12.0
- Torch = = 1.0.1. post2
- torchvision = = 0.2.2. Post3
- urllib3 = = 1.24.1
- koło = = 0.33.1 

 Aby zainstalować inne pakiety spoza wstępnie zainstalowanej listy, na przykład *scikit-misc*, Dodaj następujący kod do skryptu: 

 ```python
import os
os.system(f"pip install scikit-misc")
```

## <a name="how-to-use"></a>Jak stosować

Moduł **wykonywania skryptu języka Python** zawiera przykładowy kod w języku Python, którego można użyć jako punktu wyjścia. Aby skonfigurować moduł **wykonywania skryptu języka Python** , należy podać zestaw danych wejściowych i kod języka Python do wykonania w polu tekstowym **skrypt języka Python** .

1. Dodaj moduł **wykonywania skryptu języka Python** do potoku.

2. Dodaj i Połącz **pozycję DataSet1** dowolnych zestawów danych z projektanta, który ma być używany na potrzeby danych wejściowych. Odwołuje się do tego zestawu danych w skrypcie języka Python jako **DataFrame1**.

    Użycie zestawu danych jest opcjonalne, jeśli chcesz wygenerować dane przy użyciu języka Python, lub użyj kodu Python, aby zaimportować dane bezpośrednio do modułu.

    Ten moduł obsługuje Dodawanie drugiego zestawu danych w **Dataset2**. Odwołuje się do drugiego zestawu danych w skrypcie języka Python jako DataFrame2.

    Zestawy danych przechowywane w Azure Machine Learning są automatycznie konwertowane na dane **Pandas** . ramki po załadowaniu tego modułu.

    ![Wykonaj mapę wejściową języka Python](media/module/python-module.png)

4. Aby uwzględnić nowe pakiety lub kod w języku Python, Dodaj plik spakowane zawierający te zasoby niestandardowe w **pakiecie skryptu**. Dane wejściowe do **pakietu skryptu** muszą być plikiem skompresowanym już przekazanym do obszaru roboczego. 

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