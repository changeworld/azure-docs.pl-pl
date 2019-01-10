---
title: Usługa Azure Analysis Services samouczek Lekcja 6 Tworzenie miar | Dokumentacja firmy Microsoft
description: Opisuje sposób tworzenia miar w projekcie samouczka usług Azure Analysis Services.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 0e9f798917b557d99259ece4773cce0853b15237
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/10/2019
ms.locfileid: "54191687"
---
# <a name="create-measures"></a>Tworzenie miar

W tej lekcji utworzysz miary, które zostaną uwzględnione w modelu. Podobnie jak w przypadku utworzonych kolumn obliczeniowych, miara jest obliczeniem tworzonym przy użyciu formuły języka DAX. Jednak w odróżnieniu od kolumn obliczeniowych miary są szacowane na podstawie *filtru* wybranego przez użytkownika. Może nim być na przykład określona kolumna lub fragmentator dodany do pola Etykiety wierszy w tabeli przestawnej. Wartość każdej komórki w filtrze jest następnie obliczana z użyciem zastosowanej miary. Miary są wydajnymi i elastycznymi metodami wykonywania obliczeń, które możesz dołączyć do niemal wszystkich modeli tabelarycznych w celu przeprowadzania dynamicznych obliczeń na danych liczbowych. Aby dowiedzieć się więcej, zobacz temat [Miary](https://docs.microsoft.com/sql/analysis-services/tabular-models/measures-ssas-tabular).
  
Do tworzenia miar służy *siatka miar*. Każda tabela domyślnie zawiera pustą siatkę miar. Zwykle jednak nie tworzy się miar dla każdej tabeli. Siatka miar jest widoczna poniżej tabeli w projektancie modeli w widoku danych. Aby ukryć lub wyświetlić siatkę miar, kliknij menu **Tabela**, a następnie kliknij przycisk **Pokaż siatkę miar**.  
  
Możesz utworzyć miarę, klikając pustą komórkę w siatce miar, a następnie wpisując formułę języka DAX na pasku formuły. Po zakończeniu tworzenia formuły i naciśnięciu klawisza ENTER w komórce pojawi się miara. Można również tworzyć miary za pomocą standardowej funkcji agregacji, klikając kolumnę, a następnie klikając przycisk Autosumowanie (**∑**) na pasku narzędzi. Miary utworzone za pomocą funkcji Autosumowanie są wyświetlane w komórce siatki miar bezpośrednio pod kolumną, ale można je przenieść.  
  
W tej lekcji utworzysz miary zarówno przez wprowadzenie formuły języka DAX na pasku formuły, jak i za pomocą funkcji Autosumowanie.  
  
Szacowany czas trwania lekcji: **30 minut**  
  
## <a name="prerequisites"></a>Wymagania wstępne  
Ten temat stanowi część samouczka modelowania tabelarycznego, który należy wykonać w podanej kolejności. Przed przystąpieniem do wykonywania zadań w tej lekcji, należy ukończyć lekcję poprzednią: [Lekcja 5 Tworzenie kolumn obliczeniowych](../tutorials/aas-lesson-5-create-calculated-columns.md).  
  
## <a name="create-measures"></a>Tworzenie miar  
  
#### <a name="to-create-a-dayscurrentquartertodate-measure-in-the-dimdate-table"></a>Tworzenie miary DaysCurrentQuarterToDate w tabeli DimDate  
  
1.  W projektancie modeli kliknij tabelę **DimDate**.  
  
2.  W siatce miar kliknij pustą komórkę w lewym górnym rogu.  
  
3.  Na pasku formuły wpisz następującą formułę:  
  
    ```
    DaysCurrentQuarterToDate:=COUNTROWS( DATESQTD( 'DimDate'[Date])) 
    ```
  
    Zauważ, że teraz lewa górna komórka zawiera nazwę miary **DaysCurrentQuarterToDate**, po której następuje wynik **92**. Wynik nie jest w tym momencie ważny, ponieważ nie został zastosowany żaden filtr użytkownika.
    
      ![aas-lesson6-newmeasure](../tutorials/media/aas-lesson6-newmeasure.png) 
    
    W odróżnieniu od kolumn obliczeniowych w formułach miar można wpisać nazwę miary, po której następują dwukropek i wyrażenie formuły.

  
#### <a name="to-create-a-daysincurrentquarter-measure-in-the-dimdate-table"></a>Tworzenie miary DaysInCurrentQuarter w tabeli DimDate  
  
1.  W nadal aktywnej tabeli **DimDate** w projektancie modeli kliknij w siatce miar pustą komórkę poniżej utworzonej miary.  
  
2.  Na pasku formuły wpisz następującą formułę:  
  
    ```
    DaysInCurrentQuarter:=COUNTROWS( DATESBETWEEN( 'DimDate'[Date], STARTOFQUARTER( LASTDATE('DimDate'[Date])), ENDOFQUARTER('DimDate'[Date])))
    ```
  
    W przypadku tworzenia stosunku porównania pomiędzy niezakończonym okresem i okresem poprzednim formuła musi obliczyć, jaka część okresu już upłynęła i dokonać porównania z proporcjonalnym fragmentem okresu poprzedniego. W tym przypadku formuła [DaysCurrentQuarterToDate]/[DaysInCurrentQuarter] daje w wyniku proporcjonalną wartość czasu, który upłynął w bieżącym okresie.  
  
#### <a name="to-create-an-internetdistinctcountsalesorder-measure-in-the-factinternetsales-table"></a>Tworzenie miary InternetDistinctCountSalesOrder w tabeli FactInternetSales  
  
1.  Kliknij tabelę **FactInternetSales**.   
  
2.  Kliknij nagłówek kolumny **SalesOrderNumber**.  
  
3.  Na pasku narzędzi kliknij strzałkę w dół znajdującą się obok przycisku Autosumowanie (**∑**), a następnie wybierz pozycję **DistinctCount**.  
  
    Funkcja Autosumowanie automatycznie tworzy miarę dla wybranej kolumny przy użyciu standardowej formuły agregacji DistinctCount.  
    
       ![aas-lesson6-newmeasure2](../tutorials/media/aas-lesson6-newmeasure2.png)
  
4.  W siatce miar kliknij nową miarę, a następnie w oknie **Właściwości** w polu **Nazwa miary** zmień nazwę miary na **InternetDistinctCountSalesOrder**. 
 
  
#### <a name="to-create-additional-measures-in-the-factinternetsales-table"></a>Tworzenie dodatkowych miar w tabeli FactInternetSales  
  
1.  Za pomocą funkcji Autosumowanie utwórz i nazwij następujące miary:  

    |Kolumna|Nazwa miary|Autosumowanie (∑)|Formuła|  
    |----------------|----------|-----------------|-----------|  
    |SalesOrderLineNumber|InternetOrderLinesCount|Licznik|=COUNTA([SalesOrderLineNumber])|  
    |OrderQuantity|InternetTotalUnits|Suma|=SUM([OrderQuantity])|  
    |DiscountAmount|InternetTotalDiscountAmount|Suma|=SUM([DiscountAmount])|  
    |TotalProductCost|InternetTotalProductCost|Suma|=SUM([TotalProductCost])|  
    |SalesAmount|InternetTotalSales|Suma|=SUM([SalesAmount])|  
    |Margin|InternetTotalMargin|Suma|=SUM([Margin])|  
    |TaxAmt|InternetTotalTaxAmt|Suma|=SUM([TaxAmt])|  
    |Freight|InternetTotalFreight|Suma|=SUM([Freight])|  
  
2.  Klikając pustą komórkę w siatce miar i używając paska formuły, utwórz kolejno następujące niestandardowe miary:  
  
      ```
      InternetPreviousQuarterMargin:=CALCULATE([InternetTotalMargin],PREVIOUSQUARTER('DimDate'[Date]))
      ```
      
      ```
      InternetCurrentQuarterMargin:=TOTALQTD([InternetTotalMargin],'DimDate'[Date])
      ```
  
      ```
      InternetPreviousQuarterMarginProportionToQTD:=[InternetPreviousQuarterMargin]*([DaysCurrentQuarterToDate]/[DaysInCurrentQuarter])
      ```
  
      ```
      InternetPreviousQuarterSales:=CALCULATE([InternetTotalSales],PREVIOUSQUARTER('DimDate'[Date]))
      ```
  
      ```
      InternetCurrentQuarterSales:=TOTALQTD([InternetTotalSales],'DimDate'[Date])
      ```
      
      ```
      InternetPreviousQuarterSalesProportionToQTD:=[InternetPreviousQuarterSales]*([DaysCurrentQuarterToDate]/[DaysInCurrentQuarter])
      ```
  
Miary utworzone dla tabeli FactInternetSales umożliwiają analizowanie kluczowych danych finansowych, takich jak sprzedaż, koszty i marża, dotyczących elementów zdefiniowanych przez filtr wybrany przez użytkownika.  
  
## <a name="whats-next"></a>Co dalej?
[Lekcja 7: Tworzenie kluczowych wskaźników wydajności](../tutorials/aas-lesson-7-create-key-performance-indicators.md).  

  
