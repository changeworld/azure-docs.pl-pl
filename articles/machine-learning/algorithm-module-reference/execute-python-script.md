---
title: 'Uruchom skrypt języka Python: Odwołania do modułu'
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak użyć modułu programu wykonanie skryptu Python w usłudze Azure Machine Learning w celu uruchomienia kodu w języku Python.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 6e61ed5a18e69b107b78bf2042de21d14cd6beb5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65029133"
---
# <a name="execute-python-script-module"></a>Wykonanie modułu skrypt w języku Python

W tym artykule opisano moduł interfejs graficzny (wersja zapoznawcza) dla usługi Azure Machine Learning.

Ten moduł umożliwia uruchamianie kodu języka Python. Aby uzyskać więcej informacji na temat architektury i projektowania zasad Python zobacz [następującego artykułu.](https://docs.microsoft.com/azure/machine-learning/machine-learning-execute-python-scripts)

Za pomocą języka Python należy wykonać zadania, które nie są obecnie obsługiwane przez istniejące moduły takich jak:

+ Wizualizowanie danych przy użyciu `matplotlib`
+ Wyliczanie zestawów danych i modeli, w obszarze roboczym przy użyciu biblioteki języka Python
+ Odczytywanie, ładowania i manipulowania danymi ze źródeł, które nie są obsługiwane przez [importu danych](./import-data.md) modułu
+ Uruchamianie własnego kodu do uczenia głębokiego 


Usługa Azure Machine Learning korzysta z dystrybucji pakietu Anaconda Python, który zawiera wiele typowych narzędzi służących do przetwarzania danych. Środowisko Anaconda w wersji będą aktualizowane automatycznie. Bieżąca wersja to:
 -  Dystrybucja pakietu anaconda 4.5 + dla środowiska Python 3.6 

Wstępnie zainstalowane pakiety są:
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
- lightgbm == 2.2.3
- more-itertools==6.0.0
- numpy==1.16.2
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
- scipy==1.2.1
- setuptools==40.8.0
- six==1.12.0
- torch==1.0.1.post2
- torchvision==0.2.2.post3
- urllib3==1.24.1
- wheel==0.33.1 

 Aby zainstalować inne pakiety nie na liście wstępnie zainstalowane, na przykład *scikit różne*, Dodaj następujący kod do skryptu: 

 ```python
import os
os.system(f"pip install scikit-misc")
```

## <a name="how-to-use"></a>Jak stosować

**Wykonanie skryptu Python** moduł zawiera przykładowy kod języka Python, który można użyć jako punktu wyjścia. Aby skonfigurować **wykonanie skryptu Python** modułu, udostępniają zestaw danych wejściowych i kodu w języku Python do wykonywania w **skrypt w języku Python** pola tekstowego.

1. Dodaj **wykonanie skryptu Python** modułu do eksperymentu.

2. Dodawanie i tworzenie połączenia na **Dataset1** żadnych zestawów danych z interfejsu, z którego chcesz użyć dla danych wejściowych. Odwoływać się do tego zestawu danych w skrypcie języka Python, jako **DataFrame1**.

    Korzystanie z zestawu danych jest opcjonalne, jeśli mają być Generowanie danych przy użyciu języka Python, lub użyj kodu w języku Python, aby zaimportować dane bezpośrednio do modułu.

    Ten moduł obsługuje dodawanie drugiego zestawu danych na **Dataset2**. Odwołanie do drugiego zestawu danych jako DataFrame2 za pomocą skryptu języka Python.

    Zestawy danych przechowywanych w usłudze Azure Machine Learning, są automatycznie konwertowane na **pandas** data.frames po załadowaniu za pomocą tego modułu.

    ![Wykonaj mapy danych wejściowych języka Python](media/module/python-module.png)

4. Aby dołączyć nowe pakiety języka Python lub kod, należy dodać plik zip zawierający te zasoby niestandardowe w **pakietu skryptu**. Dane wejściowe **pakietu skryptu** muszą być plik z rozszerzeniem zip już przesłane do obszaru roboczego. 

    Każdy plik zawarte w archiwum zip przekazanych mogą służyć podczas wykonywania eksperymentów. Jeśli archiwum zawiera strukturę katalogów, struktura zostaną zachowane, ale należy poprzedzić katalog o nazwie **src** do ścieżki.

5. W **skrypt w języku Python** pole tekstowe, wpisz lub Wklej prawidłowy skrypt języka Python.

    **Skrypt w języku Python** pole tekstowe jest wstępnie wypełniony niektóre instrukcje w komentarzach i przykładowy kod, aby uzyskać dostęp do danych i danych wyjściowych. **Należy edytować lub zamienić ten kod.** Pamiętaj można było zgodne z konwencjami języka Python o wcięcia i wielkość liter w wyrazie.

    + Skrypt musi zawierać funkcję o nazwie `azureml_main` jako punkt wejścia dla tego modułu.
    + Funkcja punktu wejścia może zawierać maksymalnie dwa argumenty wejściowe: `Param<dataframe1>` i `Param<dataframe2>`
    + Pliki zip podłączony do portu wejściowego trzeci są rozpakowano i przechowywane w katalogu `.\Script Bundle`, która jest także dodawane do języka Python `sys.path`. 

    W związku z tym jeśli plik zip zawiera `mymodule.py`, importować go za pomocą `import mymodule`.

    + Dwa zestawy danych mogą być zwrócone do interfejsu, która musi być sekwencją typu `pandas.DataFrame`. Można utworzyć inne dane wyjściowe w kodzie języka Python i zapisanie ich bezpośrednio do usługi Azure storage.

6. Uruchom eksperyment, lub wybierz moduł i kliknij przycisk **Uruchom wybrane** do uruchamiania tylko skrypt w języku Python.

    Wszystkie dane i kod jest ładowany do maszyny wirtualnej i uruchamiane przy użyciu określonego środowiska Python.

## <a name="results"></a>Wyniki

Wyniki wszelkie obliczenia wykonywane przez osadzony kod Python musi zostać podana jako pandas. Ramka danych, jest automatycznie konwertowany na format zestawu danych usługi Azure Machine Learning, aby wyniki można użyć z innymi modułami w eksperymencie.

Moduł zwraca dwa zestawy danych:  
  
+ **Wyniki 1 zestaw danych**, zdefiniowany przez pierwszy dataframe pandas zwrócone w skrypcie języka Python

+ **Wynik Dataset 2**, zdefiniowany przez drugi dataframe pandas zwrócone w skrypcie języka Python


## <a name="next-steps"></a>Kolejne kroki

Zobacz [zestaw dostępnych modułów](module-reference.md) do usługi Azure Machine Learning. 