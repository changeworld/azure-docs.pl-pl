---
title: Eksportowanie bazy danych U-SQL — narzędzia usługi Azure Data Lake tools dla programu Visual Studio
description: Dowiedz się, jak wyeksportować bazę danych U-SQL i automatycznie zaimportować ją na konto lokalne za pomocą narzędzia usługi Azure Data Lake Tools dla programu Visual Studio.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 11/27/2017
ms.openlocfilehash: e5a52de0342e864cb108d8d590583fe64f72e3b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71315797"
---
# <a name="export-a-u-sql-database"></a>Eksportowanie bazy danych U-SQL

W tym artykule dowiesz się, jak używać [narzędzi usługi Azure Data Lake Tools for Visual Studio](https://aka.ms/adltoolsvs) do eksportowania bazy danych U-SQL jako pojedynczego skryptu U-SQL i pobranych zasobów. Wyeksportowana baza danych można zaimportować do konta lokalnego w tym samym procesie.

Klienci zwykle utrzymują wiele środowisk do programowania, testowania i produkcji. Te środowiska są hostowane zarówno na koncie lokalnym, na komputerze lokalnym dewelopera, jak i na koncie usługi Azure Data Lake Analytics na platformie Azure. 

Podczas opracowywania i dostrajania zapytań U-SQL w środowiskach deweloperskich i testowych deweloperzy często muszą ponownie utworzyć swoją pracę w produkcyjnej bazie danych. Kreator eksportu bazy danych pomaga przyspieszyć ten proces. Za pomocą kreatora deweloperzy mogą klonować istniejące środowisko bazy danych i przykładowe dane do innych kont usługi Data Lake Analytics.

## <a name="export-steps"></a>Kroki eksportu

### <a name="step-1-export-the-database-in-server-explorer"></a>Krok 1: Eksportowanie bazy danych w Eksploratorze serwera

Wszystkie konta usługi Data Lake Analytics, do których masz uprawnienia, są wymienione w Eksploratorze serwera. Aby wyeksportować bazę danych:

1. W Eksploratorze serwera rozwiń konto zawierające bazę danych, którą chcesz wyeksportować.
2. Kliknij prawym przyciskiem myszy bazę danych, a następnie wybierz polecenie **Eksportuj**. 
   
    ![Eksplorator serwera — eksportowanie bazy danych](./media/data-lake-analytics-data-lake-tools-export-database/export-database.png)

     Jeśli opcja **Menu Eksportuj** nie jest dostępna, należy [zaktualizować narzędzie do wersji trwałej](https://aka.ms/adltoolsvs).

### <a name="step-2-configure-the-objects-that-you-want-to-export"></a>Krok 2: Skonfiguruj obiekty, które chcesz wyeksportować

Jeśli potrzebna jest tylko niewielka część dużej bazy danych, można skonfigurować podzbiór obiektów, które mają zostać wyeksportowane w kreatorze eksportu. 

Akcja eksportu jest zakończona przez uruchomienie zadania U-SQL. W związku z tym eksportowanie z konta platformy Azure wiąże się z pewnymi kosztami.

![Kreator eksportu bazy danych — wybieranie obiektów eksportu](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard.png)

### <a name="step-3-check-the-objects-list-and-other-configurations"></a>Krok 3: Sprawdź listę obiektów i inne konfiguracje

W tym kroku można zweryfikować zaznaczone obiekty w polu **listy Eksportuj obiekt.** Jeśli występują błędy, wybierz **opcję Poprzedni,** aby wrócić i poprawnie skonfigurować obiekty, które chcesz wyeksportować.

Można również skonfigurować inne ustawienia dla obiektu docelowego eksportu. Opisy konfiguracji są wymienione w poniższej tabeli:

|Konfigurowanie|Opis|
|-------------|-----------|
|Nazwa miejsca docelowego|Ta nazwa wskazuje miejsce, w którym chcesz zapisać eksportowane zasoby bazy danych. Przykładami są zestawy, dodatkowe pliki i przykładowe dane. Folder o tej nazwie jest tworzony w lokalnym folderze głównym danych.|
|Katalog projektów|Ta ścieżka określa, gdzie chcesz zapisać eksportowany skrypt U-SQL. Wszystkie definicje obiektów bazy danych są zapisywane w tej lokalizacji.|
|Tylko schemat|Jeśli wybierzesz tę opcję, eksportowane są tylko definicje i zasoby bazy danych (takie jak zestawy i dodatkowe pliki).|
|Schemat i dane|Jeśli wybierzesz tę opcję, definicje baz danych, zasoby i dane zostaną wyeksportowane. Wyeksportowane są górne wiersze N tabel.|
|Automatyczne importowanie do lokalnej bazy danych|Jeśli wybierzesz tę opcję, wyeksportowana baza danych zostanie automatycznie zaimportowana do lokalnej bazy danych po zakończeniu eksportowania.|

![Kreator eksportu bazy danych — eksportowanie listy obiektów i innych konfiguracji](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard-configuration.png)

### <a name="step-4-check-the-export-results"></a>Krok 4: Sprawdź wyniki eksportu

Po zakończeniu eksportowania można wyświetlić wyeksportowane wyniki w oknie dziennika w kreatorze. W poniższym przykładzie pokazano, jak znaleźć eksportowane zasoby skryptu I bazy danych U-SQL, w tym zestawy, dodatkowe pliki i przykładowe dane:

![Kreator eksportu bazy danych — wyniki eksportu](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard-completed.png)

## <a name="import-the-exported-database-to-a-local-account"></a>Importowanie wyeksportowanej bazy danych do konta lokalnego

Najwygodniejszym sposobem importowania eksportowanych danych jest zaznaczenie pola wyboru **Automatycznie importuj do lokalnej bazy danych** podczas procesu eksportowania w kroku 3. Jeśli to pole nie zostało zaznaczone, najpierw znajdź wyeksportowany skrypt U-SQL w dzienniku eksportu. Następnie uruchom skrypt U-SQL lokalnie, aby zaimportować bazę danych do konta lokalnego.

## <a name="import-the-exported-database-to-a-data-lake-analytics-account"></a>Importowanie eksportowanych baz danych do konta usługi Data Lake Analytics

Aby zaimportować bazę danych do innego konta usługi Data Lake Analytics:

1. Przekaż wyeksportowane zasoby, w tym zestawy, dodatkowe pliki i przykładowe dane, do domyślnego konta usługi Azure Data Lake Store konta usługi Data Lake Analytics, do którego chcesz zaimportować. Wyeksportowanego folderu zasobów można znaleźć w folderze głównym danych lokalnych. Przekaż cały folder do katalogu głównego domyślnego konta Data Lake Store.
2. Po zakończeniu przekazywania prześlij wyeksportowany skrypt U-SQL do konta usługi Data Lake Analytics, do którego chcesz zaimportować bazę danych.

## <a name="known-limitations"></a>Znane ograniczenia

Obecnie, jeśli wybierzesz opcję **Schemat i Dane** w kroku 3, narzędzie uruchomi zadanie U-SQL w celu wyeksportowania danych przechowywanych w tabelach. Z tego powodu proces eksportowania danych może być powolny i może ponieść koszty. 

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się więcej o bazach danych U-SQL](/u-sql/data-definition-language-ddl-statements) 
* [Testowanie i debugowanie zadań U-SQL przy użyciu uruchamiania lokalnego i zestawu SDK U-SQL usługi Azure Data Lake](data-lake-analytics-data-lake-tools-local-run.md)


