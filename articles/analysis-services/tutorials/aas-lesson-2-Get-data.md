---
title: 'Samouczek Azure Analysis Services: lekcja 2 — pobieranie danych | Microsoft Docs'
description: Opisuje sposób pobierania i importowania danych w projekcie samouczka usług Azure Analysis Services.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: b7aaadb6237a3b16d166d3bc931fddc70fb4a792
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/19/2018
ms.locfileid: "49427624"
---
# <a name="get-data"></a>Pobieranie danych

W tej lekcji skorzystasz z funkcji pobierania danych programu SSDT w celu połączenia się z przykładową bazą danych Adventure Works, wybrania danych, wyświetlenia ich podglądu i odfiltrowania, a następnie zaimportowania danych do obszaru roboczego modelu.  
  
Funkcja pobierania danych umożliwia importowanie danych z wielu różnych źródeł, takich jak: baza danych SQL Azure, Oracle, Sybase, kanał informacyjny OData, Teradata, pliki itp. Można również wykonywać kwerendy danych przy użyciu wyrażeń formuły Power Query M.

> [!NOTE]
> Zadania i obrazy w tym samouczku pokazują łączenie z bazą danych AdventureWorksDW2014 na serwerze lokalnym. W niektórych przypadkach baza danych Adventure Works na platformie Azure może być inna.
  
Szacowany czas trwania lekcji: **10 minut**  
  
## <a name="prerequisites"></a>Wymagania wstępne  
Ten temat stanowi część samouczka modelowania tabelarycznego, który należy wykonać w podanej kolejności. Przed przystąpieniem do wykonywania zadań w tej lekcji należy ukończyć lekcję poprzednią: [Lekcja 1: Tworzenie nowego projektu modelu tabelarycznego](../tutorials/aas-lesson-1-create-a-new-tabular-model-project.md).  
  
## <a name="create-a-connection"></a>Tworzenie połączenia  
  
#### <a name="to-create-a-connection-to-the-adventureworksdw2014-database"></a>Tworzenie połączenia z bazą danych AdventureWorksDW2014  
  
1.  W Eksploratorze modeli tabelarycznych kliknij prawym przyciskiem myszy pozycje **Źródła danych** > **Importuj ze źródła danych**.  
  
    Spowoduje to uruchomienie funkcji pobierania danych, który prowadzi użytkownika przez proces łączenia się ze źródłem danych. Jeśli nie widzisz Eksploratora modeli tabelarycznych, w **Eksploratorze rozwiązań** kliknij dwukrotnie pozycję **Model.bim** w celu otwarcia modelu w projektancie. 
    
    ![aas-lesson2-getdata](../tutorials/media/aas-lesson2-getdata.png)
  
2.  W oknie Pobierz dane kliknij kolejno opcje **Baza danych** > **Baza danych SQL Server** > **Połącz**.  
  
3.  W oknie dialogowym **Baza danych SQL Server** w polu **Serwer** wpisz nazwę serwera, na którym zainstalowano bazę danych AdventureWorksDW2014, a następnie kliknij przycisk **Połącz**.  

4.  Po wyświetleniu monitu o podanie poświadczeń należy podać poświadczenia używane przez usługi Analysis Services do nawiązywania połączenia ze źródłem danych podczas importowania i przetwarzania danych. W obszarze **Tryb personifikacji** wybierz pozycję **Personifikuj konto**, a następnie wprowadź poświadczenia i kliknij przycisk **Połącz**. Zaleca się korzystanie z konta, do którego hasło nie wygasa.

    ![aas-lesson2-account](../tutorials/media/aas-lesson2-account.png)
  
    > [!NOTE]  
    > Najbezpieczniejszą metodą łączenia się ze źródłem danych jest używanie konta użytkownika i hasła systemu Windows.
  
5.  W oknie Nawigator wybierz bazę danych **AdventureWorksDW2014**, a następnie kliknij przycisk **OK**. Spowoduje to utworzenie połączenia z bazą danych. 
  
6.  W oknie Nawigator zaznacz pole wyboru dla następujących tabel: **DimCustomer**, **DimDate**, **DimGeography**, **DimProduct**,  **DimProductCategory**, **DimProductSubcategory**, i **FactInternetSales**, a następnie kliknij przycisk **Edytuj**.

    ![aas-lesson2-select-tables](../tutorials/media/aas-lesson2-select-tables.png)
  
    Zostanie otwarty Edytor zapytań. W następnej sekcji należy wybrać jedynie te dane, które mają zostać zaimportowane.

  
## <a name="filter-the-table-data"></a>Filtrowanie danych tabeli  
Tabele w przykładowej bazie AdventureWorksDW2014 zawierają dane, których nie trzeba uwzględniać w modelu. O ile jest to możliwe, należy odfiltrować zbędne dane w celu zaoszczędzenia miejsca w pamięci używanego przez model. Możesz odfiltrować kolumny tabel, tak aby dane w nich zawarte nie były importowane do bazy danych obszaru roboczego, lub też odfiltrować bazę danych modelu po jej wdrożeniu. 
  
#### <a name="to-filter-the-table-data-before-importing"></a>Filtrowanie danych tabeli przed zaimportowaniem  
  
1.  W edytorze zapytań wybierz tabelę **DimCustomer**. Zostanie wyświetlony widok tabeli DimCustomer w źródle danych (przykładowej bazie danych AdventureWorksDW2014). 
  
2.  Korzystając z funkcji wyboru wielokrotnego (Ctrl + kliknięcie), wybierz pozycje **SpanishEducation**, **FrenchEducation**, **SpanishOccupation**, **FrenchOccupation**, kliknij je prawym przyciskiem myszy, a następnie kliknij pozycję **Usuń kolumny**. 

    ![aas-lesson2-remove-columns](../tutorials/media/aas-lesson2-remove-columns.png)
  
    Importowanie tych kolumn nie jest wymagane, ponieważ zawarte w nich wartości nie mają znaczenia dla analizy sprzedaży w Internecie. Dzięki wyeliminowaniu zbędnych kolumn model jest mniejszy i wydajniejszy.  

    > [!TIP]
    > Jeśli popełnisz błąd, możesz utworzyć kopię zapasową, usuwając krok w pozycji **ZASTOSOWANE KROKI**.   
    
    ![aas-lesson2-remove-columns](../tutorials/media/aas-lesson2-remove-step.png)

  
4.  Odfiltruj pozostałe tabele, usuwając następujące kolumny w każdej tabeli:  
    
    **DimDate**
    
      |Kolumna|  
      |--------|  
      |**DateKey**|  
      |**SpanishDayNameOfWeek**|  
      |**FrenchDayNameOfWeek**|  
      |**SpanishMonthName**|  
      |**FrenchMonthName**|  
  
    **DimGeography**
  
      |Kolumna|  
      |-------------|  
      |**SpanishCountryRegionName**|  
      |**FrenchCountryRegionName**|  
      |**IpAddressLocator**|  
  
    **DimProduct**
  
      |Kolumna|  
      |-----------|  
      |**SpanishProductName**|  
      |**FrenchProductName**|  
      |**FrenchDescription**|  
      |**ChineseDescription**|  
      |**ArabicDescription**|  
      |**HebrewDescription**|  
      |**ThaiDescription**|  
      |**GermanDescription**|  
      |**JapaneseDescription**|  
      |**TurkishDescription**|  
  
    **DimProductCategory**
  
      |Kolumna|  
      |--------------------|  
      |**SpanishProductCategoryName**|  
      |**FrenchProductCategoryName**|  
  
    **DimProductSubcategory**
  
      |Kolumna|  
      |-----------------------|  
      |**SpanishProductSubcategoryName**|  
      |**FrenchProductSubcategoryName**|  
  
    **FactInternetSales**
  
      Brak usuniętych kolumn.
  
## <a name="Import"></a>Importowanie wybranych tabel i kolumn danych  
Po wyświetleniu podglądu i odfiltrowaniu zbędnych danych możesz zaimportować pozostałe dane. Kreator importuje dane tabeli oraz relacje między tabelami. W modelu zostają utworzone nowe tabele i kolumny, natomiast odfiltrowane dane nie są importowane.  
  
#### <a name="to-import-the-selected-tables-and-column-data"></a>Importowanie wybranych tabel i kolumn danych  
  
1.  Przejrzyj wybrane pozycje. Jeśli wszystko wygląda poprawnie, kliknij przycisk **Importuj**. W oknie dialogowym Przetwarzanie danych wyświetlany jest stan danych importowanych ze źródła danych do bazy danych obszaru roboczego.
  
    ![aas-lesson2-success](../tutorials/media/aas-lesson2-success.png) 
  
2.  Kliknij przycisk **Zamknij**.  

  
## <a name="save-your-model-project"></a>Zapisanie projektu modelu  
Projekt modelu należy regularnie zapisywać.  
  
#### <a name="to-save-the-model-project"></a>Zapisywanie projektu modelu  
  
-   Kliknij kolejno opcje **Plik** > **Zapisz wszystko**.  
  
## <a name="whats-next"></a>Co dalej?
[Lekcja 3. Oznaczanie jako tabeli dat](../tutorials/aas-lesson-3-mark-as-date-table.md).

  
  
