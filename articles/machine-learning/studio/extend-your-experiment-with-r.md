---
title: Rozszerzanie eksperymentu przy użyciu języka R - Azure Machine Learning Studio | Dokumentacja firmy Microsoft
description: Jak rozszerzyć funkcjonalność usługi Azure Machine Learning Studio za pomocą języka R przy użyciu modułu wykonywania skryptu języka R.
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: (previous ms.author=hshapiro, author=heatherbshapiro)
ms.author: amlstudiodocs
manager: hjerez
editor: cgronlun
ms.assetid: 2c038a45-ba4d-42ea-9a88-e67391ef8c0a
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.openlocfilehash: df6453f1cf6e8b372239d528067a7258d645518d
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2018
ms.locfileid: "52311012"
---
# <a name="azure-machine-learning-studio-extend-your-experiment-with-r"></a>Usługa Azure Machine Learning Studio: Rozszerzanie eksperymentu przy użyciu języka R 
Funkcje usługi Azure Machine Learning Studio za pośrednictwem języka R można rozszerzyć za pomocą [wykonanie skryptu języka R] [ execute-r-script] modułu.

Ten moduł przyjmuje wiele zestawów danych wejściowych i daje jednego zestawu danych jako dane wyjściowe. Możesz wpisać skrypt języka R do **skrypt języka R** parametru [wykonanie skryptu języka R] [ execute-r-script] modułu.

Każdy z portem wejściowym modułu jest dostęp za pomocą kodu podobne do następujących:

    dataset1 <- maml.mapInputPort(1)

## <a name="listing-all-currently-installed-packages"></a>Wyświetlanie listy wszystkich obecnie zainstalowanych pakietów
Lista zainstalowanych pakietów można zmienić. Lista obecnie zainstalowanych pakietów można znaleźć w [R pakiety obsługiwanych przez usługi Azure Machine Learning Studio](https://msdn.microsoft.com/library/azure/mt741980.aspx).

Możesz także uzyskać pełne, bieżąca lista zainstalowanych pakietów, wprowadzając następujący kod do [wykonanie skryptu języka R] [ execute-r-script] modułu:

    out <- data.frame(installed.packages(,,,fields="Description"))
    maml.mapOutputPort("out")

To wysyła listę pakietów do portu wyjściowego [wykonanie skryptu języka R] [ execute-r-script] modułu.
Aby wyświetlić listę pakietów, połączyć moduł konwersji takich jak [Konwertuj do formatu CSV] [ convert-to-csv] do lewej wyjście [wykonanie skryptu języka R] [ execute-r-script] modułu Uruchom eksperyment, a następnie kliknij wyjście modułu konwersji i wybierz **Pobierz**. 

![Pobierz dane wyjściowe modułu "Konwertuj do pliku CSV"](./media/extend-your-experiment-with-r/download-package-list.png)


<!--
For convenience, here is the [current full list with version numbers in Excel format](http://az754797.vo.msecnd.net/docs/RPackages.xlsx).
-->

## <a name="importing-packages"></a>Importowanie pakietów
Można importować pakiety, które nie są już zainstalowane za pomocą następujących poleceń w [wykonanie skryptu języka R] [ execute-r-script] modułu:

    install.packages("src/my_favorite_package.zip", lib = ".", repos = NULL, verbose = TRUE)
    success <- library("my_favorite_package", lib.loc = ".", logical.return = TRUE, verbose = TRUE)

gdzie `my_favorite_package.zip` plik zawiera pakiet.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[convert-to-csv]: https://msdn.microsoft.com/library/azure/faa6ba63-383c-4086-ba58-7abf26b85814/
