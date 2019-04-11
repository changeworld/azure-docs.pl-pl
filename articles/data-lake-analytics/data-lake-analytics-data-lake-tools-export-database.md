---
title: Eksportowanie bazy danych U-SQL przy użyciu narzędzi Azure Data Lake Tools for Visual Studio
description: Dowiedz się, jak wyeksportować bazę danych U-SQL i automatycznie zaimportować go do konta lokalnego za pomocą usługi Azure Data Lake Tools for Visual Studio.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 11/27/2017
ms.openlocfilehash: fe28aa8b88f557d4bbcdabf1de1c4bc6491743ce
ms.sourcegitcommit: 6e32f493eb32f93f71d425497752e84763070fad
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/10/2019
ms.locfileid: "59471413"
---
# <a name="export-a-u-sql-database"></a>Eksportowanie bazy danych U-SQL

Ten artykuł zawiera informacje o sposobie użycia [Azure Data Lake Tools for Visual Studio](https://aka.ms/adltoolsvs) eksportowania bazy danych U-SQL jako pojedynczy skrypt U-SQL i pobrane zasoby. Możesz zaimportować wyeksportowanej bazy danych do konta lokalnego, w tym samym procesie.

Klienci zwykle Obsługa wielu środowisk na potrzeby programowania, testowania i produkcji. Te środowiska znajdują się w obu kontem lokalnym, na komputerze lokalnym dla deweloperów, a na koncie usługi Azure Data Lake Analytics na platformie Azure. 

Podczas tworzenia i dostosować zapytania U-SQL w środowiskach deweloperskich i testowych, deweloperzy często muszą ponownie utworzyć swoją pracą w produkcyjnej bazie danych. Kreator eksportowania bazy danych pomaga przyspieszyć ten proces. Za pomocą kreatora, deweloperzy można sklonować istniejące środowisko bazy danych i przykładowe dane do innych kont usługi Data Lake Analytics.

## <a name="export-steps"></a>Kroki eksportowania

### <a name="step-1-export-the-database-in-server-explorer"></a>Krok 1: Eksportowanie bazy danych w Eksploratorze serwera

Wszystkie konta usługi Data Lake Analytics, do których masz uprawnienia są wyświetlane w Eksploratorze serwera. Aby wyeksportować bazy danych:

1. W Eksploratorze serwera rozwiń konto które zawiera bazę danych, który chcesz wyeksportować.
2. Kliknij prawym przyciskiem myszy bazę danych, a następnie wybierz **wyeksportować**. 
   
    ![Eksplorator serwera - Export a bazy danych](./media/data-lake-analytics-data-lake-tools-export-database/export-database.png)

     Jeśli **wyeksportować** nie jest dostępna opcja menu, musisz [narzędzie aktualizacji do wersji z najnowszą wersję apletu](https://aka.ms/adltoolsvs).

### <a name="step-2-configure-the-objects-that-you-want-to-export"></a>Krok 2: Skonfiguruj obiekty, które mają zostać wyeksportowane

Jeśli potrzebujesz tylko niewielką część dużych baz danych, można skonfigurować podzbiór obiektów, które mają zostać wyeksportowane w Kreatorze eksportu. 

Akcja eksportu jest wykonywana przez uruchomienie zadania U-SQL. W związku z tym eksportowanie z konta platformy Azure generuje pewien koszt.

![Kreator eksportowania bazy danych — wybierz opcję Eksportuj obiekty](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard.png)

### <a name="step-3-check-the-objects-list-and-other-configurations"></a>Krok 3: Sprawdź listę obiektów i innych konfiguracji

W tym kroku możesz sprawdzić wybranych obiektów w **lista obiektów eksportu** pole. Jeśli występują błędy, wybierz **Wstecz** przejść wstecz i poprawnie Skonfiguruj obiekty, które chcesz wyeksportować.

Można także skonfigurować inne ustawienia dla elementu docelowego eksportu. W poniższej tabeli przedstawiono opisy konfiguracji:

|Konfigurowanie|Opis|
|-------------|-----------|
|Nazwa miejsca docelowego|Ta nazwa wskazuje, gdzie chcesz zapisać zasoby wyeksportowanej bazy danych. Przykładami są zestawy, dodatkowe pliki i przykładowych danych. Folder o tej nazwie jest tworzony w folderze głównym danych lokalnych.|
|Katalog projektu|Ta ścieżka Określa, gdzie chcesz zapisać wyeksportowany skrypt U-SQL. Wszystkich definicji obiektu bazy danych są zapisywane w tej lokalizacji.|
|Tylko schemat|Jeśli wybierzesz tę opcję, tylko definicje bazy danych i zasobów (takich jak zestawy i dodatkowe pliki) są eksportowane.|
|Schemat i dane|Jeśli wybierzesz tę opcję, definicje bazy danych, zasoby i dane zostaną wyeksportowane. Pierwsze wiersze N tabel są eksportowane.|
|Automatycznie zaimportuj do lokalnej bazy danych|Jeśli wybierzesz tę opcję, wyeksportowanej bazy danych jest automatycznie importowane do swojej lokalnej bazy danych podczas eksportowania jest zakończony.|

![Kreator eksportowania bazy danych — Eksportuj listę obiektów i innych konfiguracji](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard-configuration.png)

### <a name="step-4-check-the-export-results"></a>Krok 4: Sprawdzanie wyników eksportu

Po zakończeniu operacji eksportowania, można wyświetlić wyeksportowane wyniki w oknie dziennika w kreatorze. Poniższy przykład pokazuje, jak znaleźć wyeksportowanego U-SQL skrypt i zasobami baz danych, m.in. zestawy, dodatkowe pliki i przykładowe dane:

![Kreator eksportowania bazy danych — wyników eksportu](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard-completed.png)

## <a name="import-the-exported-database-to-a-local-account"></a>Importowanie wyeksportowanej bazy danych do konta lokalnego

Najwygodniejszym sposobem zaimportować wyeksportowanej bazy danych jest wybranie **zaimportuj do lokalnej bazy danych automatycznie** pole wyboru podczas procesu eksportowania w kroku 3. Jeśli nie zaznaczysz to pole, najpierw należy znaleźć wyeksportowanego skryptu U-SQL w Eksportuj Dziennik. Następnie uruchom skrypt U-SQL lokalnie, aby zaimportować bazę danych do konta lokalnego.

## <a name="import-the-exported-database-to-a-data-lake-analytics-account"></a>Importuj wyeksportowanej bazy danych do konta usługi Data Lake Analytics

Aby zaimportować bazę danych do innego konta usługi Data Lake Analytics:

1. Prześlij wyeksportowane zasoby, m.in. zestawy, dodatkowe pliki i przykładowe dane do domyślnego konta usługi Azure Data Lake Store, którą chcesz zaimportować do konta usługi Data Lake Analytics. Można znaleźć folderu zasobów wyeksportowany w folderze głównym danych lokalnych. Przekazać cały folder, do katalogu głównego domyślnego konta Data Lake Store.
2. Po zakończeniu przekazywania Prześlij wyeksportowane skrypt U-SQL do konta usługi Data Lake Analytics, które chcesz zaimportować bazę danych do.

## <a name="known-limitations"></a>Znane ograniczenia

Obecnie Jeśli wybierzesz **schemat i dane** opcja w kroku 3, to narzędzie uruchamia zadania U-SQL, aby wyeksportować dane przechowywane w tabelach. W związku z tym danych, eksportowania proces może być wolne, i mogą zostać naliczone koszty. 

## <a name="next-steps"></a>Kolejne kroki

* [Dowiedz się więcej o języku U-SQL w bazach danych](/u-sql/data-definition-language-ddl-statements) 
* [Testowanie i debugowanie zadań U-SQL przy użyciu uruchamiania lokalnego i zestawu SDK usługi Azure Data Lake U-SQL](data-lake-analytics-data-lake-tools-local-run.md)


