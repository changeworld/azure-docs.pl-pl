---
title: 'Dane eksportu: odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak używać modułu Eksportuj dane w usłudze Azure Machine Learning do zapisywania wyników, danych pośrednich i danych roboczych z potoków do miejsc docelowych magazynu w chmurze poza usługą Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 807771fd4018c9666f059c965370ebc36d0105df
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456305"
---
# <a name="export-data-module"></a>Moduł Eksportuj dane

W tym artykule opisano moduł w projektancie usługi Azure Machine Learning (wersja zapoznawcza).

Ten moduł służy do zapisywania wyników, danych pośrednich i danych roboczych z potoków w miejscach docelowych magazynu w chmurze. 

Ten moduł obsługuje eksportowanie danych do następujących usług danych w chmurze:

- Kontener obiektów blob platformy Azure
- Udział plików platformy Azure
- Azure Data Lake
- Azure Data Lake Gen2

Przed wyeksportowanie danych należy najpierw zarejestrować magazyn danych w obszarze roboczym usługi Azure Machine Learning. Aby uzyskać więcej informacji, zobacz [Dostęp do danych w usługach magazynu platformy Azure](../how-to-access-data.md).

## <a name="how-to-configure-export-data"></a>Jak skonfigurować dane eksportu

1. Dodaj moduł **Eksportuj dane** do potoku w projektancie. Ten moduł można znaleźć w kategorii **Wejście i wyjście.**

1. Połącz **eksportuj dane** z modułem zawierającym dane, które chcesz wyeksportować.

1. Wybierz **pozycję Eksportuj dane,** aby otworzyć okienko **Właściwości.**

1. W przypadku **magazynu danych**wybierz istniejący magazyn danych z listy rozwijanej. Można również utworzyć nowy magazyn danych. Sprawdź, jak odwiedzić [dane programu Access w usługach magazynu platformy Azure.](../how-to-access-data.md)

1. Pole wyboru **Regeneruj dane wyjściowe,** decyduje, czy wykonać moduł do ponownego generowania danych wyjściowych w czasie wykonywania. 

    Domyślnie jest niezaznaczony, co oznacza, że jeśli moduł został wcześniej wykonany z tymi samymi parametrami, system ponownie użyczy danych wyjściowych z ostatniego uruchomienia, aby skrócić czas wykonywania. 

    Jeśli jest zaznaczona, system wykona moduł ponownie, aby ponownie wygenerować dane wyjściowe.

1. Zdefiniuj ścieżkę w magazynie danych, w którym znajdują się dane. Ścieżka jest ścieżką względną. Puste ścieżki lub ścieżki adresu URL nie są dozwolone.


1. W polu **Format pliku**wybierz format, w którym dane mają być przechowywane.
 
1. Prześlij potok.

## <a name="next-steps"></a>Następne kroki

Zobacz [zestaw modułów dostępnych dla](module-reference.md) usługi Azure Machine Learning. 
