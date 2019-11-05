---
title: Rozszerzanie eksperymentu przy użyciu języka R
titleSuffix: Azure Machine Learning Studio (classic)
description: Jak zwiększyć funkcjonalność Azure Machine Learning Studio (klasyczny) za pośrednictwem języka R przy użyciu modułu skryptu Execute R.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/20/2017
ms.openlocfilehash: a1a3eca380240d624da3e2f086749756aabccbe2
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73492951"
---
# <a name="azure-machine-learning-studio-classic-extend-your-experiment-with-r"></a>Azure Machine Learning Studio (klasyczny): poszerzanie eksperymentu przy użyciu języka R 
Funkcjonalność Azure Machine Learning Studio (klasyczny) można zwiększyć za pośrednictwem języka R przy użyciu modułu [skryptu wykonywania języka r][execute-r-script] .

Ten moduł akceptuje wiele zestawów danych wejściowych i daje pojedynczy zestaw danych jako dane wyjściowe. Skrypt języka R można wpisać do parametru **skryptu języka r** modułu [wykonywania skryptu języka][execute-r-script] r.

Dostęp do każdego portu wejściowego modułu można uzyskać przy użyciu kodu podobnego do poniższego:

    dataset1 <- maml.mapInputPort(1)

## <a name="listing-all-currently-installed-packages"></a>Wyświetlanie wszystkich aktualnie zainstalowanych pakietów
Można zmienić listę zainstalowanych pakietów. Listę aktualnie zainstalowanych pakietów można znaleźć w [pakietach języka R obsługiwanych przez Azure Machine Learning Studio (klasyczny)](https://msdn.microsoft.com/library/azure/mt741980.aspx).

Możesz również uzyskać kompletną, bieżącą listę zainstalowanych pakietów, wprowadzając następujący kod do modułu [wykonywania skryptu języka R][execute-r-script] :

    out <- data.frame(installed.packages(,,,fields="Description"))
    maml.mapOutputPort("out")

Spowoduje to wysłanie listy pakietów do portu wyjściowego modułu [wykonywania skryptu języka R][execute-r-script] .
Aby wyświetlić listę pakietów, Podłącz moduł konwersji, taki jak [konwertowanie do formatu CSV][convert-to-csv] , na lewo od lewej strony modułu [wykonywania skryptu języka R][execute-r-script] , uruchom eksperyment, a następnie kliknij dane wyjściowe modułu konwersji i wybierz pozycję **Pobierz**. 

![Pobierz dane wyjściowe modułu "Konwertuj do pliku CSV"](./media/extend-your-experiment-with-r/download-package-list.png)


<!--
For convenience, here is the [current full list with version numbers in Excel format](https://az754797.vo.msecnd.net/docs/RPackages.xlsx).
-->

## <a name="importing-packages"></a>Importowanie pakietów
Pakiety, które nie zostały jeszcze zainstalowane, można zaimportować przy użyciu następujących poleceń w module [wykonywania skryptu języka R][execute-r-script] :

    install.packages("src/my_favorite_package.zip", lib = ".", repos = NULL, verbose = TRUE)
    success <- library("my_favorite_package", lib.loc = ".", logical.return = TRUE, verbose = TRUE)

miejsce, w którym plik `my_favorite_package.zip` zawiera pakiet.




<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[convert-to-csv]: https://msdn.microsoft.com/library/azure/faa6ba63-383c-4086-ba58-7abf26b85814/
