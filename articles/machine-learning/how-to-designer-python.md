---
title: Python
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak za pomocą języka Python w projektancie usługi Azure Machine Learning do przekształcania danych.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
author: peterclu
ms.author: peterlu
ms.date: 02/28/2020
ms.openlocfilehash: a2bd9845cd29c7d139e2042f39b4697847639207
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79455795"
---
# <a name="execute-python-code-in-azure-machine-learning-designer"></a>Wykonywanie kodu języka Python w projektancie usługi Azure Machine Learning

W tym artykule dowiesz się, jak użyć modułu [Wykonywanie skryptu języka Python,](algorithm-module-reference/execute-python-script.md) aby dodać niestandardową logikę do projektanta usługi Azure Machine Learning. W poniższych instrukcjach, należy użyć biblioteki Pandas do prostego inżynierii funkcji.

Edytor kodu wbudowanego można użyć, aby szybko dodać prostą logikę języka Python. Jeśli chcesz dodać bardziej złożony kod lub przekazać dodatkowe biblioteki Języka Python, należy użyć metody pliku zip.

Domyślne środowisko wykonywania używa dystrybucji Anacondas języka Python. Aby uzyskać pełną listę wstępnie zainstalowanych pakietów, zobacz stronę [odwołania do modułu Wykonywanie skryptu języka Python.](algorithm-module-reference/execute-python-script.md)

![Wykonywanie mapy wprowadzania języka Python](media/how-to-designer-python/execute-python-map.png)

## <a name="execute-python-written-in-the-designer"></a>Wykonywanie języka Python napisanego w projektancie

### <a name="add-the-execute-python-script-module"></a>Dodawanie modułu Wykonywanie skryptu języka Python

1. Znajdź moduł **Wykonywanie skryptu języka Python** w palecie projektanta. Można go znaleźć w sekcji **Język Języka Pythona.**

1. Przeciągnij i upuść moduł na kanwę potoku.

### <a name="connect-input-datasets"></a>Łączenie wejściowych zestawów danych

W tym artykule użyto przykładowego zestawu danych **Dane o cenach samochodów (raw)**. 

1. Przeciągnij i upuść zestaw danych na kanwę potoku.

1. Połącz port wyjściowy zestawu danych z lewym górnym portem wejściowym modułu **Wykonywanie skryptu języka Python.** Projektant udostępnia dane wejściowe jako parametr do skryptu punktu wejścia.
    
    Prawy port wejściowy jest zarezerwowany dla spakowanych bibliotek python.

    ![Łączenie zestawów danych](media/how-to-designer-python/connect-dataset.png)
        

1. Zanotuj, którego portu wejściowego używasz. Projektant przypisuje lewy port wejściowy `dataset1` do zmiennej `dataset2`i środkowy port wejściowy do . 

Moduły wejściowe są opcjonalne, ponieważ można generować lub importować dane bezpośrednio w module **Wykonywanie skryptu Pythona.**

### <a name="write-your-python-code"></a>Napisz swój kod Pythona

Projektant udostępnia początkowy skrypt punktu wejścia, aby edytować i wprowadzić własny kod Pythona. 

W tym przykładzie można użyć Pandas połączyć dwie kolumny znalezione w zestawie danych samochodowych, **Cena** i **moc,** aby utworzyć nową kolumnę, **Dolary na koniu.** Ta kolumna reprezentuje, ile płacisz za każdą moc, co może być przydatną funkcją, aby zdecydować, czy samochód jest dobrą ofertą dla pieniędzy. 

1. Wybierz moduł **Wykonywanie skryptu języka Python.**

1. W okienku wyświetlanym po prawej stronie kanwy zaznacz pole tekstowe **skryptu Języka Python.**

1. Skopiuj i wklej następujący kod do pola tekstowego.

    ```python
    import pandas as pd
    
    def azureml_main(dataframe1 = None, dataframe2 = None):
        dataframe1['Dollar/HP'] = dataframe1.price / dataframe1.horsepower
        return dataframe1
    ```
    Potok powinien wyglądać następująco:
    
    ![Wykonywanie potoku języka Python](media/how-to-designer-python/execute-python-pipeline.png)

    Skrypt punktu wejścia musi `azureml_main`zawierać funkcję . Istnieją dwa parametry funkcji, które są mapowane do dwóch portów wejściowych dla modułu **Wykonywanie skryptu Pythona.**

    Zwracana wartość musi być Pandas Dataframe. Można zwrócić maksymalnie dwie klatki danych jako wyjścia modułu.
    
1. Prześlij potok.

Teraz masz zestaw danych z nową funkcją **Dolary / HP**, które mogą być przydatne w szkoleniu rekomendatora samochodu. Jest to przykład wyodrębniania operacji i redukcji wymiarowości. 

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [zaimportować własne dane](how-to-designer-import-data.md) w projektancie usługi Azure Machine Learning.