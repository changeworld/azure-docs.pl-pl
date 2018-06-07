---
title: 'Samouczek Azure Analysis Services: lekcja 10 — tworzenie partycji | Microsoft Docs'
description: Opisuje sposób tworzenia partycji w projekcie samouczka usług Azure Analysis Services.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 51ba9b80bd5572be451107dee13453e4d5037ff9
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34596053"
---
# <a name="create-partitions"></a>Tworzenie partycji

W tej lekcji utworzysz partycje dzielące tabelę FactInternetSales na mniejsze części logiczne, które mogą być przetwarzane (odświeżane) niezależnie od innych partycji. Domyślnie każda tabela uwzględniona w modelu ma jedną partycję, która obejmuje wszystkie kolumny i wiersze tej tabeli. W przypadku tabeli FactInternetSales dane mają być dzielone według lat i dla każdego okresu pięcioletniego w tabeli ma być utworzona jedna partycja. Każda partycja będzie mogła być przetwarzana niezależnie. Aby dowiedzieć się więcej, zobacz temat [Partycje](https://docs.microsoft.com/sql/analysis-services/tabular-models/partitions-ssas-tabular). 
  
Szacowany czas trwania lekcji: **15 minut**  
  
## <a name="prerequisites"></a>Wymagania wstępne  
Ten temat stanowi część samouczka modelowania tabelarycznego, który należy wykonać w podanej kolejności. Przed przystąpieniem do wykonywania zadań w tej lekcji należy ukończyć lekcję poprzednią: [Lekcja 9. Tworzenie hierarchii](../tutorials/aas-lesson-9-create-hierarchies.md).  
  
## <a name="create-partitions"></a>Tworzenie partycji  
  
#### <a name="to-create-partitions-in-the-factinternetsales-table"></a>Tworzenie partycji w tabeli FactInternetSales  
  
1.  W Eksploratorze modeli tabelarycznych rozwiń węzeł **Tabele**, a następnie kliknij prawym przyciskiem myszy pozycje **FactInternetSales** > **Partycje**.  
  
2.  W Menedżerze partycji kliknij opcję **Kopiuj**, a następnie zmień nazwę pliku na **FactInternetSales2010**.
  
    Ponieważ partycja ma obejmować tylko wiersze z określonego przedziału w roku 2010, należy zmodyfikować wyrażenie zapytania.
  
4.  Kliknij opcję **Projekt**, aby otworzyć Edytor zapytań, a następnie kliknij zapytanie **FactInternetSales2010**.

5.  W wersji zapoznawczej kliknij strzałkę w dół w nagłówku kolumny **OrderDate**, a następnie kliknij opcje **Filtry dat/godzin** > **Pomiędzy**.

    ![aas-lesson10-query-editor](../tutorials/media/aas-lesson10-query-editor.png)

6.  W oknie dialogowym Filtrowanie wierszy, w obszarze **Pokaż wiersze, w których: OrderDate** pozostaw zaznaczoną opcję **wypada po lub w dniu**, a następnie w polu daty wpisz **1/1/2010**. Pozostaw zaznaczony operator **oraz**, wybierz opcję **jest przed**, a następnie w polu daty wpisz **1/1/2011** i kliknij przycisk **OK**.

    ![aas-lesson10-filter-rows](../tutorials/media/aas-lesson10-filter-rows.png)
    
    Zwróć uwagę, że w części ZASTOSOWANE KROKI w Edytorze zapytań widać kolejny krok o nazwie Przefiltrowano wiersze. Ten filtr służy do wybierania dat zamówień tylko z 2010 roku.

8.  Kliknij przycisk **Importuj**.

    W Menedżerze partycji zwróć uwagę, że wyrażenie zapytania zawiera teraz dodatkową klauzulę Przefiltrowano wiersze.

    ![aas-lesson10-query](../tutorials/media/aas-lesson10-query.png)
  
    Ta instrukcja określa, że dana partycja powinna zawierać tylko dane z tych wierszy, w których data OrderDate przypada w roku kalendarzowym 2010, zgodnie z treścią klauzuli Przefiltrowano wiersze.  
  
  
#### <a name="to-create-a-partition-for-the-2011-year"></a>Tworzenie partycji dla 2011 roku  
  
1.  Na liście partycji kliknij utworzoną przez siebie partycję **FactInternetSales2010**, a następnie kliknij przycisk **Kopiuj**.  Zmień nazwę partycji na **FactInternetSales2011**. 

    Nie musisz używać Edytora zapytań do utworzenia nowej klauzuli Przefiltrowano wiersze. Ponieważ utworzono kopię zapytania dotyczącego roku 2010, wystarczy wprowadzić niewielką zmianę w zapytaniu dotyczącym roku 2011.
  
2.  Aby ta partycja zawierała tylko wiersze z 2011 roku, w **wyrażeniu zapytania** zamień lata w klauzuli Przefiltrowano wiersze odpowiednio na **2011** i **2012**, tj.:  
  
    ```  
    let
        Source = #"SQL/localhost;AdventureWorksDW2014",
        dbo_FactInternetSales = Source{[Schema="dbo",Item="FactInternetSales"]}[Data],
        #"Removed Columns" = Table.RemoveColumns(dbo_FactInternetSales,{"OrderDateKey", "DueDateKey", "ShipDateKey"}),
        #"Filtered Rows" = Table.SelectRows(#"Removed Columns", each [OrderDate] >= #datetime(2011, 1, 1, 0, 0, 0) and [OrderDate] < #datetime(2012, 1, 1, 0, 0, 0))
    in
        #"Filtered Rows"
   
    ```  
  
#### <a name="to-create-partitions-for-2012-2013-and-2014"></a>Tworzenie partycji dla lat 2012, 2013 i 2014  
  
- Wykonaj poprzednie kroki, tworząc partycje dla lat 2012, 2013 i 2014 przez zmianę roku w klauzuli Przefiltrowano wiersze, tak by uwzględniane były tylko wiersze z danego roku. 
  

## <a name="delete-the-factinternetsales-partition"></a>Usuwanie partycji FactInternetSales
Po utworzeniu partycji dla każdego roku można usunąć partycję FactInternetSales, aby uniknąć nakładania się partycji po wybraniu przetwarzania wszystkich partycji.

#### <a name="to-delete-the-factinternetsales-partition"></a>Usuwanie partycji FactInternetSales
-  Kliknij partycję FactInternetSales, a następnie kliknij przycisk **Usuń**.



## <a name="process-partitions"></a>Przetwarzanie partycji  
Zwróć uwagę, że w Menedżerze partycji kolumna **Ostatnio przetwarzane** dla każdej nowo utworzonej partycji pokazuje informację, że te partycje nie były jeszcze przetwarzane. Po utworzeniu partycji należy uruchomić operację przetwarzania partycji lub przetwarzania tabeli, aby odświeżyć dane w tych partycjach.  
  
#### <a name="to-process-the-factinternetsales-partitions"></a>Przetwarzanie partycji FactInternetSales  
  
1.  Kliknij przycisk **OK**, aby zamknąć Menedżera partycji.  
  
2.  Kliknij tabelę **FactInternetSales**, a następnie kliknij menu **Model** > **Przetwarzanie** > **Przetwarzanie partycji**.  
  
3.  W oknie dialogowym Przetwarzanie partycji sprawdź, czy dla opcji **Tryb** wybrano ustawienie **Przetwórz dane domyślne**.  
  
4.  Zaznacz pole wyboru w kolumnie **Przetwarzanie** dla każdej z pięciu utworzonych partycji, a następnie kliknij przycisk **OK**.  

    ![aas-lesson10-process-partitions](../tutorials/media/aas-lesson10-process-partitions.png)
  
    Jeśli zostanie wyświetlony monit o poświadczenia personifikacji, wprowadź nazwę użytkownika systemu Windows i hasło określone w lekcji 2.  
  
    Zostanie wyświetlone okno dialogowe **Przetwarzanie danych** ze szczegółami przetwarzania poszczególnych partycji. Zwróć uwagę, że dla każdej partycji przenoszona jest inna liczba wierszy. Każda partycja zawiera tylko wiersze z roku określonego w klauzuli WHERE w instrukcji SQL. Po zakończeniu przetwarzania możesz zamknąć okno dialogowe Przetwarzanie danych.  
  
    ![aas-lesson10-process-complete](../tutorials/media/aas-lesson10-process-complete.png)
  
 ## <a name="whats-next"></a>Co dalej?
Przejdź do następnej lekcji: [Lekcja 11. Tworzenie ról](../tutorials/aas-lesson-11-create-roles.md). 
