---
title: Importuj z Azure SQL Database
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak używać modułu Importuj dane w Azure Machine Learning Visual Interface, aby pobrać dane z Azure SQL Database.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/09/2019
ms.openlocfilehash: 374776088d93813f39122f2018b00466c55d2b6e
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2019
ms.locfileid: "72694612"
---
# <a name="import-from-azure-sql-database"></a>Importuj z Azure SQL Database

W tym artykule opisano, jak używać modułu [Importuj dane](import-data.md) w Azure Machine Learning Visual Interface, aby pobrać dane z Azure SQL Database.  

Aby zaimportować dane z bazy danych, należy określić zarówno nazwę serwera, jak i nazwę bazy danych, a także instrukcję SQL, która definiuje tabelę, widok lub zapytanie.  

Ogólnie rzecz biorąc przechowywanie danych w bazach danych platformy Azure jest droższe niż używanie tabel lub obiektów BLOB na platformie Azure. Mogą to być również limity ilości danych, które można przechowywać w bazie danych, w zależności od typu subskrypcji. Nie istnieją jednak opłaty za transakcje dotyczące usługi SQL Azure Database, dzięki czemu opcja jest idealna, aby uzyskać szybki dostęp do mniejszych ilości często używanych informacji, takich jak tabele odnośników danych lub słowniki danych.

Przechowywanie danych w bazie danych platformy Azure jest również preferowane, jeśli musisz mieć możliwość filtrowania danych przed ich przeczytaniem lub jeśli chcesz zapisać prognozy lub metryki z powrotem w bazie danych na potrzeby raportowania.

## <a name="how-to-import-data-from-azure-sql-database"></a>Jak zaimportować dane z Azure SQL Database

1. Dodaj moduł [Import danych](import-data.md) do potoku. Ten moduł można znaleźć w interfejsie wizualizacji, w kategorii dane wejściowe i wyjściowe.

1. W obszarze **Źródło danych**wybierz pozycję **Azure SQL Database**.

1. Ustaw następujące opcje charakterystyczne dla Azure SQL Database.

    **Nazwa serwera bazy danych**: wpisz nazwę serwera, która jest generowana przez platformę Azure. Zazwyczaj ma on postać `<generated_identifier>.database.windows.net`.

    **Nazwa bazy danych**: wpisz nazwę istniejącej bazy danych na określonym serwerze.

    **Nazwa konta użytkownika serwera**: wpisz nazwę użytkownika konta, które ma uprawnienia dostępu do bazy danych.

    **Hasło konta użytkownika serwera**: Podaj hasło do określonego konta użytkownika.

    **Zapytanie bazy danych**: wpisz lub wklej instrukcję SQL opisującą dane, które mają zostać odczytane. Zawsze sprawdzaj poprawność instrukcji SQL i ponownie Weryfikuj wyniki zapytania przy użyciu narzędzia, takiego jak Visual Studio Eksplorator serwera lub SQL Server Data Tools.

1. Jeśli zestaw danych, który można odczytać do Azure Machine Learning nie powinien zmieniać się między uruchomieniami potoku, wybierz opcję **Użyj buforowanych wyników** .

    Gdy jest zaznaczone, jeśli nie ma żadnych innych zmian parametrów modułu, potok ładuje dane przy pierwszym uruchomieniu modułu, a następnie używa buforowanej wersji zestawu danych.

    Jeśli chcesz ponownie załadować zestaw danych dla każdej iteracji potoku, usuń zaznaczenie tej opcji. Zestaw danych jest ponownie ładowany ze źródła za każdym razem, gdy wszystkie parametry zostaną zmienione w [importowanych danych](import-data.md).

1. Uruchamianie potoku.

    Ponieważ [Import danych](import-data.md) ładuje dane do interfejsu wizualizacji, można również wykonać niejawną konwersję typów, w zależności od typów danych używanych w źródłowej bazie danych.

## <a name="results"></a>Wyniki

Po zakończeniu importowania kliknij wyjściowy zestaw danych i wybierz polecenie **Wizualizuj** , aby sprawdzić, czy dane zostały zaimportowane pomyślnie.

Opcjonalnie można zmienić zestaw danych i jego metadane przy użyciu narzędzi w interfejsie wizualizacji:

- Użyj opcji [Edytuj metadane](edit-metadata.md) , aby zmienić nazwy kolumn, przekonwertować kolumnę na inny typ danych lub wskazać kolumny, które są etykietami lub funkcjami.

- Użyj [opcji wybierz kolumny w zestawie danych](select-columns-in-dataset.md) , aby wybrać podzbiór kolumn.

- Użyj [partycji i przykładu](partition-and-sample.md) , aby oddzielić zestaw danych według kryteriów lub pobrać n pierwszych wierszy.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning usługi. 
