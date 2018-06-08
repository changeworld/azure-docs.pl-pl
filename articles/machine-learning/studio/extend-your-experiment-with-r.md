---
title: Rozszerzanie eksperymentu z R | Dokumentacja firmy Microsoft
description: Jak rozszerzyć funkcjonalność usługi Azure Machine Learning Studio za pomocą języka R przy użyciu modułu wykonania skryptu języka R.
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
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
ms.openlocfilehash: 1f05119d94611df2e75afc3a56d9682d1149326c
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34834478"
---
# <a name="extend-your-experiment-with-r"></a>Rozszerzanie eksperymentu przy użyciu języka R
Funkcje usługi Azure Machine Learning Studio za pomocą języka R można rozszerzyć za pomocą [wykonanie skryptu języka R] [ execute-r-script] modułu.

Ten moduł akceptuje wiele zestawów danych wejściowych i daje jednego zestawu danych jako dane wyjściowe. Możesz wpisać skrypt języka R w **skrypt języka R** parametr [wykonanie skryptu języka R] [ execute-r-script] modułu.

Aby dostęp do każdego portu wejściowego modułu przy użyciu kodu podobne do następujących:

    dataset1 <- maml.mapInputPort(1)

## <a name="listing-all-currently-installed-packages"></a>Wyświetlanie listy wszystkich aktualnie zainstalowane pakiety
Lista zainstalowanych pakietów można zmienić. Lista obecnie zainstalowanych pakietów można znaleźć w [R pakiety obsługiwanych przez usługi Azure Machine Learning](https://msdn.microsoft.com/library/azure/mt741980.aspx).

Możesz również można uzyskać pełną, bieżącą listę zainstalowanych pakietów wprowadzając następujący kod do [wykonanie skryptu języka R] [ execute-r-script] modułu:

    out <- data.frame(installed.packages(,,,fields="Description"))
    maml.mapOutputPort("out")

To wysyła listę pakietów do portem wyjściowym [wykonanie skryptu języka R] [ execute-r-script] modułu.
W celu wyświetlenia listy pakietów połączenia takich jak moduł konwersji [Konwertuj do formatu CSV] [ convert-to-csv] po lewej stronie danymi wyjściowymi [wykonanie skryptu języka R] [ execute-r-script] modułu Uruchom eksperyment, a następnie kliknij dane wyjściowe moduł konwersji i wybierz **Pobierz**. 

![Pobieranie danych wyjściowych modułu "Konwertuj do CSV"](./media/extend-your-experiment-with-r/download-package-list.png)


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
