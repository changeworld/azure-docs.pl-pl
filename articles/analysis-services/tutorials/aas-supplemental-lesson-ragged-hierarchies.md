---
title: 'Samouczek Azure Analysis Services: lekcja uzupełniająca — niewyrównane hierarchie | Microsoft Docs'
description: Opisuje sposób naprawiania niewyrównanych hierarchii w samouczku usług Azure Analysis Services.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/03/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 9fdf332727c4d66af2d5394fb26e84f6ea9d963f
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2018
ms.locfileid: "37440769"
---
# <a name="supplemental-lesson---ragged-hierarchies"></a>Lekcja uzupełniająca — niewyrównane hierarchie

W tej lekcji uzupełniającej przedstawione zostanie rozwiązanie często występującego problemu polegającego na tworzeniu tabeli przestawnej dla hierarchii zawierających puste wartości (członków) na różnych poziomach. Przykładem może być organizacja, w której menedżer wysokiego szczebla jako bezpośrednich podwładnych ma menedżerów działów oraz osoby niebędące menedżerami. Innym przykładem mogą być hierarchie geograficzne składające się z kraju, regionu i miasta, gdzie niektóre miasta nie mają nadrzędnego kraju lub regionu, jak Waszyngton czy Watykan. Hierarchie z pustymi członkami często osiągają różne, niewyrównane poziomy.

![aas-lesson-detail-ragged-hierarchies-table](../tutorials/media/aas-lesson-detail-ragged-hierarchies-table.png)

Modele tabelaryczne o poziomie zgodności 1400 zapewniają dodatkową właściwość hierarchii o nazwie **Ukryj członków**. Ustawienie **Domyślne** zakłada, że na żadnym poziomie nie występują puści członkowskie. Ustawienie **Ukryj pustych członków** wyklucza pustych członków z hierarchii w przypadku ich dodania do tabeli przestawnej lub raportu.  
  
Szacowany czas trwania lekcji: **20 minut**  
  
## <a name="prerequisites"></a>Wymagania wstępne  
Ta lekcja uzupełniająca stanowi część samouczka modelowania tabelarycznego. Przed wykonaniem zadań w tej lekcji uzupełniającej należy ukończyć wszystkie poprzednie lekcje lub wykonać przykładowy projekt modelu Adventure Works Internet Sales. 

Jeśli projekt AW Internet Sales został utworzony w ramach tego samouczka, model nie zawiera jeszcze żadnych danych ani hierarchii niewyrównanych. W celu ukończenia tej lekcji uzupełniającej należy najpierw utworzyć problem, dodając pewne dodatkowe tabele oraz tworząc relacje, kolumny obliczeniowe, miarę i nową hierarchię organizacji. Zajmie to ok. 15 minut. Następnie można rozwiązać problem w ciągu kilku minut.  

## <a name="add-tables-and-objects"></a>Dodawanie tabel i obiektów
  
### <a name="to-add-new-tables-to-your-model"></a>Dodawanie nowych tabel do modelu
  
1.  W Eksploratorze modeli tabelarycznych rozwiń pozycję **Źródła danych**, a następnie prawym przyciskiem myszy kliknij połączenie i kliknij pozycję **Importuj nowe tabele**.
  
2.  W oknie Nawigator wybierz tabele **DimEmployee** oraz **FactResellerSales**, a następnie kliknij przycisk **OK**.

3.  W edytorze zapytań kliknij opcję **Importuj**

4.  Utwórz następującą [relację](../tutorials/aas-lesson-4-create-relationships.md):

    | Tabela 1           | Kolumna       | Kierunek filtru   | Tabela 2     | Kolumna      | Aktywne |
    |-------------------|--------------|--------------------|-------------|-------------|--------|
    | FactResellerSales | OrderDateKey | Domyślne            | DimDate     | Date        | Yes    |
    | FactResellerSales | DueDate      | Domyślne            | DimDate     | Date        | Nie     |
    | FactResellerSales | ShipDateKey  | Domyślne            | DimDate     | Date        | Nie     |
    | FactResellerSales | ProductKey   | Domyślne            | DimProduct  | ProductKey  | Yes    |
    | FactResellerSales | EmployeeKey  | Do obu tabel | DimEmployee | EmployeeKey | Yes    |

5. W tabeli **DimEmployee** utwórz następujące [kolumny obliczeniowe](../tutorials/aas-lesson-5-create-calculated-columns.md): 

    **Ścieżka** 
    ```
    =PATH([EmployeeKey],[ParentEmployeeKey])
    ```

    **FullName** 
    ```
    =[FirstName] & " " & [MiddleName] & " " & [LastName]
    ```

    **Level1** 
    ```
    =LOOKUPVALUE(DimEmployee[FullName],DimEmployee[EmployeeKey],PATHITEM([Path],1,1)) 
    ```

    **Level2** 
    ```
    =LOOKUPVALUE(DimEmployee[FullName],DimEmployee[EmployeeKey],PATHITEM([Path],2,1)) 
    ```

    **Level3** 
    ```
    =LOOKUPVALUE(DimEmployee[FullName],DimEmployee[EmployeeKey],PATHITEM([Path],3,1)) 
    ```

    **Level4** 
    ```
    =LOOKUPVALUE(DimEmployee[FullName],DimEmployee[EmployeeKey],PATHITEM([Path],4,1)) 
    ```

    **Level5** 
    ```
    =LOOKUPVALUE(DimEmployee[FullName],DimEmployee[EmployeeKey],PATHITEM([Path],5,1)) 
    ```

6.  W tabeli **DimEmployee** utwórz [hierarchię](../tutorials/aas-lesson-9-create-hierarchies.md) o nazwie **Organization** (Organizacja). Dodaj następujące kolumny w podanej kolejności: **Level1**, **Level2**, **Level3**, **Level4**, **Level5**.

7.  W tabeli **FactResellerSales** utwórz następującą [miarę](../tutorials/aas-lesson-6-create-measures.md):

    ```
    ResellerTotalSales:=SUM([SalesAmount])
    ```

8.  Użyj funkcji [Analiza w programie Excel](../tutorials/aas-lesson-12-analyze-in-excel.md), aby otworzyć program Excel i automatycznie utworzyć tabelę przestawną.

9.  W obszarze **Pola tabeli przestawnej** dodaj hierarchię **Organization** z tabeli **DimEmployee** do pola **Wiersze** i miarę **ResellerTotalSales** z tabeli **FactResellerSales** do pola **Wartości**.

    ![aas-lesson-detail-ragged-hierarchies-pivottable](../tutorials/media/aas-lesson-detail-ragged-hierarchies-pivottable.png)

    Jak widać w tabeli przestawnej, w hierarchii wyświetlane są wiersze niewyrównane. Istnieje wiele wierszy, w których są widoczni puści członkowie.

## <a name="to-fix-the-ragged-hierarchy-by-setting-the-hide-members-property"></a>Rozwiązywanie niewyrównanej hierarchii przez ustawienie właściwości Ukryj członków

1.  W **Eksploratorze modeli tabelarycznych** rozwiń węzeł **Tabele** > **DimEmployee** > **Hierarchie** > **Organization**.

2.  W obszarze **Właściwości** > **Ukryj członków** wybierz pozycję **Ukryj członków**. 

    ![aas-lesson-detail-ragged-hierarchies-hidemembers](../tutorials/media/aas-lesson-detail-ragged-hierarchies-hidemembers.png)

3.  Po powrocie do programu Excel odśwież tabelę przestawną. 

    ![aas-lesson-detail-ragged-hierarchies-pivottable-refresh](../tutorials/media/aas-lesson-detail-ragged-hierarchies-pivottable-refresh.png)

    Teraz tabela wygląda o wiele lepiej.

## <a name="see-also"></a>Zobacz też   
[Lekcja 9. Tworzenie hierarchii](../tutorials/aas-lesson-9-create-hierarchies.md)  
[Lekcja uzupełniająca — zabezpieczenia dynamiczne](../tutorials/aas-supplemental-lesson-dynamic-security.md)  
[Lekcja uzupełniająca — wiersze szczegółów](../tutorials/aas-supplemental-lesson-detail-rows.md)  
