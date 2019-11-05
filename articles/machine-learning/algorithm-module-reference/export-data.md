---
title: 'Eksportowanie danych: odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak używać modułu eksport danych w Azure Machine Learning, aby zapisywać wyniki, dane pośrednie i dane robocze z potoków do miejsc docelowych magazynu w chmurze poza Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: 5badcbe2a76c41bdc1431abae617e35b7555eb88
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73497898"
---
# <a name="export-data-module"></a>Eksportuj moduł danych

W tym artykule opisano moduł w programie Azure Machine Learning Designer (wersja zapoznawcza).

Ten moduł służy do zapisywania wyników, danych pośrednich i danych roboczych z potoków do miejsc docelowych magazynu w chmurze spoza Azure Machine Learning. 

Ten moduł obsługuje eksportowanie danych do następujących usług danych w chmurze:

- Kontener obiektów blob platformy Azure
- Udział plików platformy Azure
- Azure Data Lake
- Azure Data Lake Gen2

Przed wyeksportowaniem danych najpierw należy najpierw zarejestrować magazyn danych w obszarze roboczym Azure Machine Learning. Aby uzyskać więcej informacji, zobacz [jak uzyskać dostęp do danych](../service/how-to-access-data.md).

## <a name="how-to-configure-export-data"></a>Jak skonfigurować dane eksportu

1. Dodaj moduł **eksportu danych** do potoku w projektancie. Ten moduł można znaleźć w kategorii **dane wejściowe i wyjściowe** .

1. Połącz **eksport danych** z modułem zawierającym dane, które chcesz wyeksportować.

1. Wybierz pozycję **Eksportuj dane** , aby otworzyć okienko **Właściwości** .

1. W obszarze **Magazyn**danych wybierz istniejący magazyn danych z listy rozwijanej. Możesz również utworzyć nowy magazyn danych. Zapoznaj się z tematem jak [uzyskać dostęp do danych](../service/how-to-access-data.md)

1. Zdefiniuj ścieżkę w magazynie danych, w której mają zostać zapisane dane. 


1. W polu **Format pliku**wybierz format, w którym mają być przechowywane dane.
 
1. Uruchamianie potoku.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning. 