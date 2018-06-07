---
title: Eksportowanie bazy danych U-SQL przy użyciu usługi Azure Data Lake Tools dla programu Visual Studio
description: Dowiedz się, jak za pomocą narzędzia Azure Data Lake Tools dla programu Visual Studio można wyeksportować bazę danych U-SQL oraz automatycznie zaimportuj go do lokalnego konta.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
manager: kfile
editor: jasonwhowell
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 11/27/2017
ms.openlocfilehash: 1dbcf9b88af7777a1103876a429ccc26450e2f58
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34623414"
---
# <a name="export-a-u-sql-database"></a>Eksportuj U-SQL bazy danych

W tym artykule, Dowiedz się, jak używać [Azure Data Lake Tools dla programu Visual Studio](http://aka.ms/adltoolsvs) eksportowania U-SQL bazy danych jako pojedynczego skryptu U-SQL i pobranych zasobów. Wyeksportowanej bazy danych można zaimportować do konta lokalnego, w tym samym procesie.

Klienci obsługi zwykle wiele środowisk umożliwiające tworzenie, badanie i produkcji. Tych środowisk znajdują się w obu konto lokalne, na komputerze lokalnym deweloperów i konta usługi Azure Data Lake Analytics na platformie Azure. 

Podczas tworzenia i dostrajania zapytań U-SQL w środowiskach programistycznych i testowych, deweloperzy często konieczne ponowne utworzenie pracy w produkcyjnej bazie danych. Kreator eksportu bazy danych pomaga przyspieszyć ten proces. Za pomocą kreatora, deweloperzy mogą sklonować istniejącego środowiska bazy danych i przykładowe dane do innych kont usługi Data Lake Analytics.

## <a name="export-steps"></a>Kroki eksportowania

### <a name="step-1-export-the-database-in-server-explorer"></a>Krok 1: Eksportowanie bazy danych w Eksploratorze serwera

Wszystkie konta usługi Data Lake Analytics, które mają uprawnienia do są wyświetlane w Eksploratorze serwera. Aby wyeksportować bazy danych:

1. W Eksploratorze serwera rozwiń konto, które zawiera bazę danych, która ma zostać wykonane eksportowanie.
2. Kliknij prawym przyciskiem myszy bazę danych, a następnie wybierz **wyeksportować**. 
   
    ![Eksplorator serwera - Export a bazy danych](./media/data-lake-analytics-data-lake-tools-export-database/export-database.png)

     Jeśli **wyeksportować** nie jest dostępna opcja menu, musisz [aktualizacji narzędzie do wersji najnowszą wersję apletu](http://aka.ms/adltoolsvs).

### <a name="step-2-configure-the-objects-that-you-want-to-export"></a>Krok 2: Konfigurowanie obiektów, które mają zostać wyeksportowane

Jeśli potrzebujesz tylko niewielką częścią dużej bazy danych, można skonfigurować podzbiór obiektów, które mają zostać wyeksportowane w Kreatorze eksportu. 

Akcja eksportu jest wykonywana, uruchamiając zadanie U-SQL. W związku z tym eksportowanie z konta platformy Azure generuje pewien koszt związany.

![Kreator eksportu bazy danych — wybierz eksportu obiektów](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard.png)

### <a name="step-3-check-the-objects-list-and-other-configurations"></a>Krok 3: Sprawdź listę obiektów i inne konfiguracje

W tym kroku, można sprawdzić wybranych obiektów w **listy obiektów eksportu** pole. Jeśli wystąpią jakieś błędy, wybierz **Wstecz** przejść wstecz i poprawnie Skonfiguruj obiekty, które ma zostać wykonane eksportowanie.

Można także skonfigurować inne ustawienia dla elementu docelowego eksportu. W poniższej tabeli przedstawiono opisy konfiguracji:

|Konfigurowanie|Opis|
|-------------|-----------|
|Nazwa docelowego|Ta nazwa wskazuje, gdzie chcesz zapisać wyeksportowany bazy danych zasobów. Przykłady są zestawy, dodatkowe pliki i przykładowych danych. W folderze głównym Twojego danych lokalnych jest tworzony folder o tej nazwie.|
|Katalog projektu|Ta ścieżka definiuje, gdzie chcesz zapisać wyeksportowany skryptu U-SQL. Wszystkie definicje obiektów bazy danych są zapisywane w tej lokalizacji.|
|Tylko schemat|Jeśli wybierzesz tę opcję, tylko definicje bazy danych i zasobów (takich jak zestawy i dodatkowe pliki) są eksportowane.|
|Schemat i dane|Wybranie tej opcji, definicje bazy danych, zasobów i dane są eksportowane. Top N wierszy tabel są eksportowane.|
|Automatycznie zaimportuj do lokalnej bazy danych|Jeśli wybierzesz tę opcję, wyeksportowanej bazy danych jest automatycznie importowane z lokalną bazą danych podczas eksportowania jest zakończony.|

![Kreator eksportu bazy danych — eksportu listy obiektów i inne konfiguracje](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard-configuration.png)

### <a name="step-4-check-the-export-results"></a>Krok 4: Sprawdzenie wyników eksportu

Po zakończeniu operacji eksportowania, wyeksportowanych wyników można wyświetlić w oknie dziennika w kreatorze. Poniższy przykład przedstawia sposób znaleźć wyeksportowane zasoby U-SQL skrypt i bazę danych w tym zestawów, dodatkowe pliki i przykładowe dane:

![Kreator eksportu bazy danych — wyników eksportu](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard-completed.png)

## <a name="import-the-exported-database-to-a-local-account"></a>Importowanie wyeksportowanej bazy danych do konta lokalnego

Najbardziej wygodny sposób importowania wyeksportowanego bazy danych jest wybranie **zaimportować do lokalnej bazy danych automatycznie** pole wyboru podczas procesu eksportowanie w kroku 3. Jeśli nie zaznaczysz to pole, najpierw należy znaleźć wyeksportowanego skryptu U-SQL w dzienniku eksportu. Następnie uruchom skrypt U-SQL lokalnie, aby zaimportować bazę danych do konta lokalnego.

## <a name="import-the-exported-database-to-a-data-lake-analytics-account"></a>Importuj wyeksportowanej bazy danych do konta usługi Data Lake Analytics

Aby zaimportować bazę danych do innego konta usługi Data Lake Analytics:

1. Prześlij wyeksportowane zasoby, w tym zestawów, dodatkowe pliki i przykładowe dane, do konta usługi Data Lake Analytics, który chcesz zaimportować do domyślnego konta usługi Azure Data Lake Store. Można znaleźć folderu zasobów wyeksportowanego w folderze głównym danych lokalnych. Przekazywanie całego folderu głównego domyślnego konta usługi Data Lake Store.
2. Po zakończeniu przekazywania przesłać wyeksportowanego skryptu U-SQL do konta usługi Data Lake Analytics, które chcesz zaimportować do bazy danych.

## <a name="known-limitations"></a>Znane ograniczenia

Obecnie w przypadku wybrania **schemat i dane** opcja w kroku 3, narzędzie uruchamia zadania skryptu U-SQL, aby wyeksportować dane przechowywane w tabelach. W związku z tym procesu eksportowania danych może być wolne, i może pociągnąć za sobą kosztów. 

## <a name="next-steps"></a>Kolejne kroki

* [Dowiedz się więcej o języku U-SQL bazy danych](https://msdn.microsoft.com/library/azure/mt621299.aspx) 
* [Testowanie i debugowanie zadań U-SQL przy użyciu uruchamiania lokalnego i zestawu SDK U-SQL usługi Azure Data Lake](data-lake-analytics-data-lake-tools-local-run.md)


