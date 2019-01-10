---
title: Usługa Azure Analysis Services samouczek lekcja 4 Tworzenie relacji | Dokumentacja firmy Microsoft
description: Opisuje sposób tworzenia relacji w projekcie samouczka usług Azure Analysis Services.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 4a595e4c955b08bda6fcf49b9ca6a050e8440621
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/10/2019
ms.locfileid: "54187505"
---
# <a name="create-relationships"></a>Tworzenie relacji

W tej lekcji sprawdzisz relacje, które zostały automatycznie utworzone po zaimportowaniu danych, i dodasz nowe relacje między różnymi tabelami. Relacja to połączenie między dwiema tabelami, które określa sposób korelowania danych w tych tabelach. Na przykład: między tabelą DimProduct a tabelą DimProductSubcategory istnieje relacja opierająca się na fakcie, że każdy z produktów należy do podkategorii. Aby dowiedzieć się więcej, zobacz [Relacje](https://docs.microsoft.com/sql/analysis-services/tabular-models/relationships-ssas-tabular).
  
Szacowany czas trwania lekcji: **10 minut**  
  
## <a name="prerequisites"></a>Wymagania wstępne  
Ten temat stanowi część samouczka modelowania tabelarycznego, który należy wykonać w podanej kolejności. Przed przystąpieniem do wykonywania zadań w tej lekcji, należy ukończyć lekcję poprzednią: [Lekcja 3: Oznacz jako tabelę dat](../tutorials/aas-lesson-3-mark-as-date-table.md). 
  
## <a name="review-existing-relationships-and-add-new-relationships"></a>Przegląd istniejących relacji i dodawanie nowych  
Podczas importowania danych przy użyciu narzędzia Pobierz dane uzyskano siedem tabel z bazy danych AdventureWorksDW2014. Ogólnie rzecz biorąc, podczas importowania danych ze źródła relacyjnego istniejące relacje są importowane automatycznie wraz z danymi. Aby uzyskać dane automatycznie utworzyć relacje w modelu danych musi istnieć relacje między tabelami w źródle danych.

Przed przystąpieniem do tworzenia modelu należy sprawdzić, czy relacje między tabelami zostały utworzone prawidłowo. W tym samouczku zostaną również dodane trzy nowe relacje.  

  
#### <a name="to-review-existing-relationships"></a>Aby przejrzeć istniejące relacje:  
  
1.  W menu **Model** kliknij **Widok modelu** > **Widok diagramu**.  

    Projektant modeli zostanie wyświetlony w widoku diagramu, który w postaci graficznej przedstawia wszystkie zaimportowane tabele oraz łączące je linie. Linie między tabelami wskazują relacje, które zostały automatycznie utworzone po zaimportowaniu danych.
    
    ![aas-lesson4-diagram](../tutorials/media/aas-lesson4-diagram.png)
  
    > [!NOTE]
    > Jeśli nie widzisz żadnych relacji między tabelami, prawdopodobnie oznacza to, że nie ma żadnych relacji między tymi tabelami w źródle danych.

    Dołącz jak najwięcej tabel, używając formantów minimapy w prawym dolnym rogu projektanta modeli. Można również kliknąć i przeciągnąć tabele do innych lokalizacji, zbliżając je do siebie lub umieszczając w określonej kolejności. Przenoszenie tabel nie ma wpływu na relacje między tabelami. Jeśli chcesz wyświetlić wszystkie kolumny w określonej tabeli, kliknij i przeciągnij krawędź tabeli, aby powiększyć ją lub zmniejszyć.  
  
2.  Kliknij ciągłą linię między tabelami **DimCustomer** a **DimGeography**. Linia ciągła między tymi dwiema tabelami pokazuje, że relacja jest aktywna, co oznacza, że jest używana domyślnie podczas obliczania formuł języka DAX.  
  
    Należy zauważyć, że w polu pojawiła się zarówno kolumna **GeographyKey** w tabeli **DimCustomer**, jak i kolumna **GeographyKey** w tabeli **DimGeography**. Te kolumny są używane w relacji. Właściwości relacji również pojawiają się w oknie **Właściwości**.  
  
    > [!TIP]  
    > Oprócz projektanta modeli w widoku diagramu do wyświetlania relacji między wszystkimi tabelami w formacie tabeli można również użyć okna dialogowego Zarządzanie relacjami. W Eksploratorze modeli tabelarycznych kliknij prawym przyciskiem myszy pozycje **Relacje** > **Zarządzanie relacjami**.
  
3.  Sprawdź, czy podczas importowania wszystkich tabel z bazy danych AdventureWorksDW zostały utworzone następujące relacje:  
  
    |Aktywne|Tabela|Pokrewna tabela odnośników|  
    |----------|---------|------------------------|  
    |Yes|**DimCustomer [GeographyKey]**|**DimGeography [GeographyKey]**|  
    |Yes|**DimProduct [ProductSubcategoryKey]**|**DimProductSubcategory [ProductSubcategoryKey]**|  
    |Yes|**DimProductSubcategory [ProductCategoryKey]**|**DimProductCategory [ProductCategoryKey]**|  
    |Yes|**FactInternetSales [CustomerKey]**|**DimCustomer [CustomerKey]**|  
    |Yes|**FactInternetSales [ProductKey]**|**DimProduct [ProductKey]**|  
  
    Jeśli brakuje któregokolwiek z tych relacji, sprawdź, czy model zawiera następujące tabele: DimCustomer, DimDate, DimGeography, DimProduct, DimProductCategory, DimProductSubcategory i FactInternetSales. W przypadku tabel pochodzących z tego samego połączenia źródła danych, które zostały zaimportowane w różnym czasie, relacje między takimi tabelami nie są tworzone i należy utworzyć je ręcznie. Jeśli relacje nie są wyświetlane, oznacza to, że nie ma żadnych relacji w źródle danych. Możesz je utworzyć ręcznie w modelu danych.

### <a name="take-a-closer-look"></a>Przyjrzyjmy się temu bliżej
W widoku diagramu zwróć uwagę na strzałkę, gwiazdkę i liczbę w wierszach, które wskazują relacje między tabelami.

![aas-lesson4-line](../tutorials/media/aas-lesson4-line.png)

Strzałka wskazuje kierunek filtrowania. Gwiazdka wskazuje, że dana tabela znajduje się po stronie wielu w kardynalności relacji, a jedynka wskazuje, że dana tabela jest po stronie jednego w kardynalności relacji. W przypadku konieczności edytowania relacji, na przykład w celu zmiany kierunku filtrowania relacji lub jej kardynalności, kliknij dwukrotnie wiersz relacji, aby otworzyć okno dialogowe Edytowanie relacji.

![aas-lesson4-edit](../tutorials/media/aas-lesson4-edit.png)

Te funkcje są przeznaczone do zaawansowanego modelowania danych i wykraczają poza zakres tego samouczka. Aby dowiedzieć się więcej, zobacz [Dwukierunkowe filtry krzyżowe dla modeli tabelarycznych w usługach Analysis Services](https://docs.microsoft.com/sql/analysis-services/tabular-models/bi-directional-cross-filters-tabular-models-analysis-services).

W niektórych przypadkach może być konieczne utworzenie dodatkowych relacji między tabelami w modelu służącym do obsługi określonej logiki biznesowej. W tym samouczku zostaną utworzone trzy dodatkowe relacje między tabelami FactInternetSales a DimDate.  
  
#### <a name="to-add-new-relationships-between-tables"></a>Aby dodać nowe relacje między tabelami  
  
1.  W Projektancie modeli w tabeli **FactInternetSales** kliknij i przytrzymaj kolumnę **OrderDate**, a następnie przeciągnij kursor do kolumny **Date** w tabeli **DimDate**, po czym zwolnij kursor.  

    Zostanie wyświetlona linia ciągła wskazująca utworzenie aktywnej relacji między kolumną **OrderDate** w tabeli **Internet Sales** tabeli a kolumną **Date** w tabeli **Date**. 
  
      ![aas-lesson4-new](../tutorials/media/aas-lesson4-new.png) 
  
    > [!NOTE]  
    > Podczas tworzenia relacji jest automatycznie wybierana kardynalność i kierunek filtrowania pomiędzy tabelą podstawową a pokrewną tabelą odnośników.  
  
2.  W tabeli **FactInternetSales** kliknij i przytrzymaj kolumnę **DueDate**, a następnie przeciągnij kursor do kolumny **Date** w tabeli **DimDate**, po czym zwolnij kursor.  
  
    Zostanie wyświetlona linia przerywana wskazująca utworzenie nieaktywnej relacji między kolumną **DueDate** w tabeli **FactInternet Sales** a kolumną **Date** w tabeli **DimDate**. Między tabelami może istnieć wiele relacji, ale tylko jedna z nich może być aktywna w danym momencie. Nieaktywne relacje można uaktywnić, wykonując specjalne agregacje za pomocą niestandardowych wyrażeń języka DAX.  
  
3.  Na koniec utwórz jeszcze jedną relację. W tabeli **FactInternetSales** kliknij i przytrzymaj kolumnę **ShipDate**, a następnie przeciągnij kursor do kolumny **Date** w tabeli **DimDate**, po czym zwolnij kursor.  
    
     ![aas-lesson4-newinactive](../tutorials/media/aas-lesson4-newinactive.png)
  
## <a name="whats-next"></a>Co dalej?
[Lekcja 5 Tworzenie kolumn obliczeniowych](../tutorials/aas-lesson-5-create-calculated-columns.md).
  
  
  
