---
title: Wizualizacja danych usługi SQL Data Warehouse przy użyciu usługi Power BI | Microsoft Azure
description: Wizualizacja danych usługi SQL Data Warehouse przy użyciu usługi Power BI
services: sql-data-warehouse
author: kavithaj
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: consume
ms.date: 04/17/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: 52581a87caac419a79caab647cc9c5a4ee7453ba
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
ms.locfileid: "31601581"
---
# <a name="visualize-data-with-power-bi"></a>Wizualizacja danych przy użyciu usługi Power BI
Ten samouczek przedstawia sposób nawiązania połączenia z usługą SQL Data Warehouse przy użyciu usługi Power BI oraz tworzenia podstawowych wizualizacji.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Data-Warehouse-Sample-Data-and-PowerBI/player]
> 
> 

## <a name="prerequisites"></a>Wymagania wstępne
Do wykonania kroków opisanych w tym samouczku potrzebne są:

* Baza danych usługi SQL Data Warehouse ze wstępnie załadowaną bazą danych AdventureWorksDW. Aby udostępnić magazyn danych, zobacz [Tworzenie usługi SQL Data Warehouse](create-data-warehouse-portal.md) i wybierz opcję ładowanie przykładowych danych. Jeśli już masz magazynu danych, ale bez przykładowych danych, możesz [załadować WideWorldImportersDW](load-data-wideworldimportersdw.md).

## <a name="1-connect-to-your-database"></a>1. Nawiązywanie połączenia z bazą danych
Aby otworzyć usługę Power BI nawiązać połączenie się z bazą danych AdventureWorksDW:

1. Zaloguj się do [Azure Portal](https://portal.azure.com/).
2. Kliknij pozycję **Bazy danych SQL** i wybierz bazę danych AdventureWorks usługi SQL Data Warehouse.
   
    ![Znajdowanie bazy danych](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-find-database.png)
3. Kliknij przycisk „Otwórz w usłudze Power BI”.
   
    ![Przycisk usługi Power BI](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-button.png)
4. Powinna zostać wyświetlona strona połączenia z usługą SQL Data Warehouse przedstawiająca adres sieci Web bazy danych. Kliknij przycisk Dalej.
   
    ![Połączenie z usługą Power BI](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-connect-to-azure.png)
5. Wprowadź nazwę użytkownika serwera Azure SQL i hasło.
   
    ![Power BI logowania](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-sign-in.png)
6. Aby otworzyć bazy danych, kliknij zestaw danych AdventureWorksDW w lewym bloku.
   
    ![Otwieranie bazy danych AdventureWorksDW w usłudze Power BI](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-open-adventureworks.png)

## <a name="2-create-a-report"></a>2. Tworzenie raportu
Teraz możesz przystąpić do analizowania przykładowych danych bazy AdventureWorksDW przy użyciu usługi Power BI. Do wykonywania analizy służy widok bazy danych AdventureWorksDW o nazwie AggregateSales (Łączna sprzedaż). Ten widok zawiera kilka kluczowych metryk do analizowania sprzedaży firmy.

1. Aby utworzyć mapę kwot sprzedaży na podstawie kodów pocztowych, w okienku pól po prawej stronie kliknij widok AggregateSales (Łączna sprzedaż), aby go rozwinąć. Kliknij kolumny PostalCode (Kod pocztowy) and SalesAmount (Kwota sprzedaży), aby je zaznaczyć.
   
    ![Usługa Power BI wybiera widoku AggregateSales](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-aggregatesales.png)
   
    Usługa Power BI automatycznie rozpoznany dane geograficzne i umieszcza je na mapie dla Ciebie.
   
    ![Mapa w usłudze Power BI](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-map.png)

2. W tym kroku zostanie utworzony wykres słupkowy przedstawiający kwotę sprzedaży w odniesieniu do dochodu klienta. Aby utworzyć wykres słupkowy, przejdź do widoku rozszerzonego widoku AggregateSales. Kliknij pole SalesAmount (Kwota sprzedaży). Przeciągnij pole Customer Income (Dochód klienta) w lewo i upuść go w sekcji Axis (Oś).
   
    ![Usługa Power BI wybiera osi](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-chooseaxis.png)
   
    Wykres słupkowy na lewą stronę.
   
    ![Wykres słupkowy w usłudze Power BI](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-bar.png)
3. W tym kroku utworzymy wykres liniowy, który przedstawia kwoty sprzedaży według dat zamówienia. Aby utworzyć wykres liniowy, przejdź do widoku rozszerzonego widoku AggregateSales. Kliknij pola SalesAmount (Kwota sprzedaży) i OrderDate (Data zamówienia). W kolumnie wizualizacje kliknij ikonę wykres liniowy, która jest to pierwsza ikona w drugim wierszu w obszarze wizualizacji.
   
    ![Wykres liniowy wybiera usługi Power BI](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-prepare-line.png)
   
    Masz teraz raport wyświetlający trzy różne wizualizacje danych.
   
    ![Wykres liniowy w usłudze Power BI](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-line.png)

W dowolnym momencie możesz kliknąć menu **Plik** i wybrać polecenie **Zapisz**, aby zapisać postęp.

## <a name="using-direct-connnect"></a>Za pomocą bezpośrednich Konstruktor
Jako z bazy danych SQL Azure SQL danych magazynu bezpośrednie połączenie umożliwia przekazywanie logicznej obok możliwości analityczne usługi Power BI. Za pomocą bezpośrednich Connect zapytania są wysyłane do usługi Azure SQL Data Warehouse w w czasie rzeczywistym Ci poznać platformę danych.  Ta funkcja połączone z skali usługi SQL Data Warehouse umożliwia tworzenie dynamicznych raportów w minutach przed terabajtów danych. Ponadto wprowadzenie przycisku Otwórz w usłudze Power BI umożliwia użytkownikom bezpośrednio z usługi Power BI bez zbierania informacji z innych części Azure ich SQL Data Warehouse.

Podczas używania połączenie bezpośrednie:

* Określ nazwę FQDN serwera podczas nawiązywania połączenia.
* Upewnij się, że reguły zapory dla bazy danych są skonfigurowane do Zezwalaj na dostęp do usług platformy Azure.
* Każda akcja, np. wybierając kolumnę lub Dodawanie filtru, wysyła zapytanie bezpośrednio w magazynie danych.
* Kafelki są odświeżane i automatycznie co 15 minut.
* Funkcja pytania i odpowiedzi nie jest dostępna dla bezpośrednie łączenie zestawów danych.
* Zmiany schematu są włączone automatycznie.
* Limit czasu wszystkie zapytania bezpośredniego połączenia będzie 2 minuty.

Te ograniczenia i informacje mogą ulec zmianie w ulepszaniu środowiska.

## <a name="next-steps"></a>Kolejne kroki
Po przećwiczeniu podstawowych zadań z użyciem przykładowych danych zapoznaj się ze sposobami wykonywania takich czynności, jak [opracowywanie](sql-data-warehouse-overview-develop.md), [ładowanie](design-elt-data-loading.md) lub [migrowanie](sql-data-warehouse-overview-migrate.md) danych. Możesz też zapoznać się z [witryną sieci Web usługi Power BI](http://www.powerbi.com/).
