---
title: Wizualizacja danych usługi SQL Data Warehouse przy użyciu usługi Power BI | Microsoft Azure
description: Wizualizacja danych usługi SQL Data Warehouse przy użyciu usługi Power BI
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: integration
ms.date: 04/17/2018
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 4650e171c180653e94d78451d66bba1bcc71b136
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66241285"
---
# <a name="visualize-data-with-power-bi"></a>Wizualizacja danych przy użyciu usługi Power BI
Ten samouczek przedstawia sposób nawiązania połączenia z usługą SQL Data Warehouse przy użyciu usługi Power BI oraz tworzenia podstawowych wizualizacji.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Data-Warehouse-Sample-Data-and-PowerBI/player]
> 
> 

## <a name="prerequisites"></a>Wymagania wstępne
Do wykonania kroków opisanych w tym samouczku potrzebne są:

* Baza danych usługi SQL Data Warehouse ze wstępnie załadowaną bazą danych AdventureWorksDW. Aby udostępnić magazyn danych, zobacz [utworzyć SQL Data Warehouse](create-data-warehouse-portal.md) , jak załadować dane przykładowe. Jeśli już masz magazyn danych, ale bez przykładowych danych, możesz to zrobić [obciążenia WideWorldImportersDW](load-data-wideworldimportersdw.md).

## <a name="1-connect-to-your-database"></a>1. Nawiązywanie połączenia z bazą danych
Aby otworzyć usługę Power BI nawiązać połączenie się z bazą danych AdventureWorksDW:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com/).
2. Kliknij pozycję **Bazy danych SQL** i wybierz bazę danych AdventureWorks usługi SQL Data Warehouse.
   
    ![Znajdowanie bazy danych](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-find-database.png)
3. Kliknij przycisk „Otwórz w usłudze Power BI”.
   
    ![Przycisk usługi Power BI](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-button.png)
4. Powinna zostać wyświetlona strona połączenia z usługą SQL Data Warehouse przedstawiająca adres sieci Web bazy danych. Kliknij przycisk Dalej.
   
    ![Połączenie z usługą Power BI](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-connect-to-azure.png)
5. Wprowadź nazwę użytkownika serwera usługi Azure SQL i hasło.
   
    ![Usługa Power BI Zaloguj się](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-sign-in.png)
6. Aby otworzyć bazy danych, kliknij zestaw danych AdventureWorksDW w lewym bloku.
   
    ![Otwieranie bazy danych AdventureWorksDW w usłudze Power BI](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-open-adventureworks.png)

## <a name="2-create-a-report"></a>2. Tworzenie raportu
Teraz możesz przystąpić do analizowania przykładowych danych bazy AdventureWorksDW przy użyciu usługi Power BI. Do wykonywania analizy służy widok bazy danych AdventureWorksDW o nazwie AggregateSales (Łączna sprzedaż). Ten widok zawiera kilka kluczowych metryk do analizowania sprzedaży firmy.

1. Aby utworzyć mapę kwot sprzedaży na podstawie kodów pocztowych, w okienku pól po prawej stronie kliknij widok AggregateSales (Łączna sprzedaż), aby go rozwinąć. Kliknij kolumny PostalCode (Kod pocztowy) and SalesAmount (Kwota sprzedaży), aby je zaznaczyć.
   
    ![Usługa Power BI wybiera widoku AggregateSales](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-aggregatesales.png)
   
    Usługa Power BI automatycznie rozpoznana, dane geograficzne i umieścić go w mapie dla Ciebie.
   
    ![Mapa w usłudze Power BI](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-map.png)

2. W tym kroku zostanie utworzony wykres słupkowy przedstawiający kwotę sprzedaży w odniesieniu do dochodu klienta. Aby utworzyć wykres słupkowy, przejdź do widoku rozszerzonego widoku AggregateSales. Kliknij pole SalesAmount (Kwota sprzedaży). Przeciągnij pole Customer Income (Dochód klienta) w lewo i upuść go w sekcji Axis (Oś).
   
    ![Usługa Power BI wybiera osi](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-chooseaxis.png)
   
    Wykres słupkowy, za pośrednictwem po lewej stronie.
   
    ![Wykres słupkowy w usłudze Power BI](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-bar.png)
3. W tym kroku utworzymy wykres liniowy, który przedstawia kwoty sprzedaży według dat zamówienia. Aby utworzyć wykres liniowy, przejdź do widoku rozszerzonego widoku AggregateSales. Kliknij pola SalesAmount (Kwota sprzedaży) i OrderDate (Data zamówienia). W kolumnie wizualizacje kliknij ikonę wykres liniowy, który jest to pierwsza ikona w drugim wierszu w obszarze wizualizacji.
   
    ![Usługa Power BI wybiera wykres liniowy](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-prepare-line.png)
   
    Masz teraz raport wyświetlający trzy różne wizualizacje danych.
   
    ![Wykres liniowy w usłudze Power BI](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-line.png)

W dowolnym momencie możesz kliknąć menu **Plik** i wybrać polecenie **Zapisz**, aby zapisać postęp.

## <a name="using-direct-connect"></a>Połącz z przy użyciu bezpośrednich
Jako za pomocą usługi Azure SQL Database, SQL dane magazynu bezpośrednie łączenie umożliwia przekazywanie logicznego wraz z możliwości analitycznych usługi Power BI. Za pomocą bezpośredniego Connect zapytania są wysyłane do usługi Azure SQL Data Warehouse w w czasie rzeczywistym gdy eksplorujesz dane.  Ta funkcja w połączeniu ze skalą usługi SQL Data Warehouse, umożliwia tworzenie dynamicznych raportów w ciągu kilku minut w obliczu terabajtów danych. Ponadto wprowadzenie przycisku Otwórz w usłudze Power BI umożliwia użytkownikom bezpośrednie łączenie usługi Power BI do usługi SQL Data Warehouse bez zbierania informacji z innymi częściami systemu Azure.

Korzystając z połączenia bezpośredniego:

* Określ w pełni kwalifikowaną nazwę serwera podczas łączenia.
* Upewnij się, że reguły zapory dla bazy danych są skonfigurowane do Zezwalaj na dostęp do usług platformy Azure.
* Wszystkie akcje, takie jak wybór kolumny lub Dodawanie filtru, wysyła zapytanie bezpośrednio w magazynie danych.
* Kafelki są odświeżane automatycznie, a co około 15 minut.
* Pytania i odpowiedzi nie jest dostępna dla bezpośrednie łączenie zestawów danych.
* Zmiany schematu są włączane automatycznie.
* Wszystkie zapytania bezpośrednie łączenie przekroczy limit czasu po 2 minuty.

Te ograniczenia i uwagi mogą ulec zmianie, jak poprawić doświadczenia.

## <a name="next-steps"></a>Kolejne kroki
Teraz, gdy firma Microsoft udzielono trochę czasu na przećwiczeniu podstawowych zadań z przykładowymi danymi, zobacz instrukcje [opracowywanie](sql-data-warehouse-overview-develop.md) lub [obciążenia](design-elt-data-loading.md). Możesz też zapoznać się z [witryną sieci Web usługi Power BI](https://www.powerbi.com/).
