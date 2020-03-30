---
title: 'Import danych: Odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak korzystać z modułu Importuj dane w usłudze Azure Machine Learning, aby załadować dane do potoku uczenia maszynowego z istniejących usług danych w chmurze.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: d124fdc15bd34743b237985a66cc35625f5d9a4b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456203"
---
# <a name="import-data-module"></a>Moduł Importuj dane

W tym artykule opisano moduł w projektancie usługi Azure Machine Learning (wersja zapoznawcza).

Ten moduł służy do ładowania danych do potoku uczenia maszynowego z istniejących usług danych w chmurze. 

> [!Note]
> Wszystkie funkcje zapewniane przez ten moduł mogą być wykonywane przez **magazyn danych** i **zestawy danych** na stronie docelowej worksapce. Zalecamy korzystanie z **magazynu danych** i **zestawu danych,** który zawiera dodatkowe funkcje, takie jak monitorowanie danych. Aby dowiedzieć się więcej, zobacz [Jak uzyskać dostęp do danych](../how-to-access-data.md) i jak [zarejestrować zestawy danych](../how-to-create-register-datasets.md) artykułu.
> Po zarejestrowaniu zestawu danych można go znaleźć w kategorii **Zestawy** -> danych**Moje zestawy danych** w interfejsie projektanta. Ten moduł jest zarezerwowany dla użytkowników Studio (classic) dla znanego doświadczenia. 
>

Moduł **Importuj dane** obsługuje odczytywanie danych z następujących źródeł:

- Adres URL za pośrednictwem protokołu HTTP
- Magazyny w chmurze platformy Azure za pośrednictwem [**magazynów danych**](../how-to-access-data.md))
    - Kontener obiektów blob platformy Azure
    - Udział plików platformy Azure
    - Azure Data Lake
    - Azure Data Lake Gen2
    - Azure SQL Database
    - Azure PostgreSQL    

Przed użyciem magazynu w chmurze należy najpierw zarejestrować magazyn danych w obszarze roboczym usługi Azure Machine Learning. Aby uzyskać więcej informacji, zobacz [Jak uzyskać dostęp do danych](../how-to-access-data.md). 

Po zdefiniowaniu danych, które chcesz i połączyć się ze źródłem, **[Import danych](./import-data.md)** wnioskuje typ danych każdej kolumny na podstawie wartości, które zawiera i ładuje dane do potoku projektanta. Dane wyjściowe **importu danych** jest zestaw danych, które mogą być używane z dowolnego potoku projektanta.

Jeśli dane źródłowe ulegnie zmianie, można odświeżyć zestaw danych i dodać nowe dane, ponownie korzystając z [funkcji Importuj dane](./import-data.md).

## <a name="how-to-configure-import-data"></a>Jak skonfigurować importowane dane

1. Dodaj moduł **Importuj dane** do potoku. Ten moduł można znaleźć w kategorii **Dane wejściowe i wyjściowe** w projektancie.

1. Wybierz moduł, aby otworzyć prawe okienko.

1. Wybierz **pozycję Źródło danych**i wybierz typ źródła danych. Może to być HTTP lub magazyn danych.

    Jeśli wybierzesz magazyn danych, możesz wybrać istniejące magazyny danych, które już zarejestrowały się w obszarze roboczym usługi Azure Machine Learning lub utworzyć nowy magazyn danych. Następnie zdefiniuj ścieżkę danych do zaimportowania w magazynie danych. Możesz łatwo przeglądać ścieżkę, klikając **przycisk Przeglądaj ścieżkę** ![importu-ścieżki danych-ścieżka](media/module/import-data-path.png)

1. Wybierz schemat podglądu, aby filtrować kolumny, które chcesz uwzględnić. Można również zdefiniować ustawienia zaawansowane, takie jak Delimiter w opcjach analizowania.

    ![import-data-preview](media/module/import-data.png)

1. Pole wyboru **Regeneruj dane wyjściowe,** decyduje, czy wykonać moduł do ponownego generowania danych wyjściowych w czasie wykonywania. 

    Domyślnie jest niezaznaczony, co oznacza, że jeśli moduł został wcześniej wykonany z tymi samymi parametrami, system ponownie użyczy danych wyjściowych z ostatniego uruchomienia, aby skrócić czas wykonywania. 

    Jeśli jest zaznaczona, system wykona moduł ponownie, aby ponownie wygenerować dane wyjściowe. Więc wybierz tę opcję, gdy dane bazowe w magazynie jest aktualizowana, może pomóc w uzyskanie najnowszych danych.


1. Prześlij potok.

    Gdy importuj dane ładuje dane do projektanta, wnioskuje typ danych każdej kolumny na podstawie wartości, które zawiera, numeryczne lub kategoryczne.

    Jeśli nagłówek jest obecny, nagłówek jest używany do nazwy kolumn wyjściowego zestawu danych.

    Jeśli w danych nie ma żadnych istniejących nagłówków kolumn, nowe nazwy kolumn są generowane przy użyciu formatu col1, col2,... , coln*.

## <a name="results"></a>Wyniki

Po zakończeniu importowania kliknij wyjściowy zestaw danych i wybierz pozycję **Wizualizuj,** aby sprawdzić, czy dane zostały pomyślnie zaimportowane.

Jeśli chcesz zapisać dane do ponownego użycia, zamiast importować nowy zestaw danych za każdym razem, gdy potok jest uruchamiany, wybierz ikonę **Zarejestruj zestaw danych** na karcie **Wyjścia** w prawym panelu modułu. Wybierz nazwę zestawu danych. Zapisany zestaw danych zachowuje dane w momencie zapisywania, zestaw danych nie jest aktualizowany po ponownym uruchomieniu potoku, nawet jeśli zmieni się zestaw danych w potoku. Może to być przydatne do robienia migawek danych.

Po zaimportowaniu danych może być konieczne dodatkowe przygotowania do modelowania i analizy:

- Użyj [funkcji Edytuj metadane,](./edit-metadata.md) aby zmienić nazwy kolumn, obsłużyć kolumnę jako inny typ danych lub wskazać, że niektóre kolumny są etykietami lub funkcjami.

- [Użyj opcji Wybierz kolumny w zestawie danych,](./select-columns-in-dataset.md) aby wybrać podzbiór kolumn do przekształcenia lub użycia w modelowaniu. Przekształcone lub usunięte kolumny można łatwo ponownie dołączyć do oryginalnego zestawu danych przy użyciu [dodaj kolumny](./add-columns.md) modułu.  

- Użyj [partycji i próbki,](./partition-and-sample.md) aby podzielić zestaw danych, przeprowadzić próbkowanie lub uzyskać górne n wierszy.

## <a name="next-steps"></a>Następne kroki

Zobacz [zestaw modułów dostępnych dla](module-reference.md) usługi Azure Machine Learning. 
