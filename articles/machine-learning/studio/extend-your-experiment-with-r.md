---
title: Rozszerzanie eksperymentu przy użyciu języka R
titleSuffix: ML Studio (classic) - Azure
description: Jak rozszerzyć funkcjonalność usługi Azure Machine Learning Studio (klasyczny) za pośrednictwem języka Języka Języka R przy użyciu modułu Wykonywanie skryptu języka R.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/20/2017
ms.openlocfilehash: 7b4b869695eb2073121a889cd81d99c4fc06d4b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79218031"
---
# <a name="azure-machine-learning-studio-classic-extend-your-experiment-with-r"></a>Usługa Azure Machine Learning Studio (klasyczna): rozszerzenie eksperymentu za pomocą języka R 

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]
Można rozszerzyć funkcjonalność usługi Azure Machine Learning Studio (klasyczny) za pomocą języka Języka R przy użyciu modułu [Wykonywanie skryptu języka R.][execute-r-script]

Ten moduł akceptuje wiele wejściowych zestawów danych i daje pojedynczy zestaw danych jako dane wyjściowe. Skrypt języka R można wpisać w **parametrze Skrypt języka R** modułu [Wykonywanie skryptu R.][execute-r-script]

Dostęp do każdego portu wejściowego modułu można uzyskać przy użyciu kodu podobnego do następującego:

    dataset1 <- maml.mapInputPort(1)

## <a name="listing-all-currently-installed-packages"></a>Wyświetlanie listy wszystkich aktualnie zainstalowanych pakietów
Lista zainstalowanych pakietów może ulec zmianie. Listę aktualnie zainstalowanych pakietów można znaleźć w [pakietach języka R obsługiwanych przez usługę Azure Machine Learning Studio (klasyczna).](https://msdn.microsoft.com/library/azure/mt741980.aspx)

Możesz również uzyskać pełną, aktualną listę zainstalowanych pakietów, wprowadzając następujący kod do modułu [Wykonaj skrypt R:][execute-r-script]

    out <- data.frame(installed.packages(,,,fields="Description"))
    maml.mapOutputPort("out")

Spowoduje to wysłanie listy pakietów do portu wyjściowego modułu [Wykonywanie skryptu R.][execute-r-script]
Aby wyświetlić listę pakietów, podłącz moduł konwersji, taki jak [Konwertuj na CSV,][convert-to-csv] na lewe wyjście [modułu Wykonaj skrypt R,][execute-r-script] uruchom eksperyment, a następnie kliknij dane wyjściowe modułu konwersji i wybierz **pobierz**. 

![Pobierz dane wyjściowe modułu "Konwersja na CSV"](./media/extend-your-experiment-with-r/download-package-list.png)


<!--
For convenience, here is the [current full list with version numbers in Excel format](https://az754797.vo.msecnd.net/docs/RPackages.xlsx).
-->

## <a name="importing-packages"></a>Importowanie pakietów
Pakiety, które nie zostały jeszcze zainstalowane, można importować za pomocą następujących poleceń w module [Wykonywanie skryptu języka R:][execute-r-script]

    install.packages("src/my_favorite_package.zip", lib = ".", repos = NULL, verbose = TRUE)
    success <- library("my_favorite_package", lib.loc = ".", logical.return = TRUE, verbose = TRUE)

gdzie `my_favorite_package.zip` plik zawiera pakiet.




<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[convert-to-csv]: https://msdn.microsoft.com/library/azure/faa6ba63-383c-4086-ba58-7abf26b85814/
