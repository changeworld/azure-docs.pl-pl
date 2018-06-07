---
title: Analizowanie danych w usłudze Data Lake Store za pomocą usługi Power BI | Dokumentacja firmy Microsoft
description: Użyj usługi Power BI do analizowania danych przechowywanych w usłudze Azure Data Lake Store
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 57d19d27-e135-49d9-a7ea-46c48ef4e3bd
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: 37cde17703a15ba5659814e1b65a54d24b67edad
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34624791"
---
# <a name="analyze-data-in-data-lake-store-by-using-power-bi"></a>Analizowanie danych w usłudze Data Lake Store za pomocą usługi Power BI
W tym artykule dowiesz się, jak używać Power BI Desktop do analizowania i wizualizacja danych przechowywanych w usłudze Azure Data Lake Store.

## <a name="prerequisites"></a>Wymagania wstępne
Przed przystąpieniem do wykonania kroków opisanych w tym samouczku należy dysponować następującymi elementami:

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Konto usługi Azure Data Lake Store**. Postępuj zgodnie z instrukcjami w temacie [Rozpoczynanie pracy z usługą Azure Data Lake Store za pomocą witryny Azure Portal](data-lake-store-get-started-portal.md). W tym artykule przyjęto założenie, że utworzono już konto usługi Data Lake Store o nazwie **mybidatalakestore**i przekazać przykładowy plik danych (**Drivers.txt**) do niego. Ten przykładowy plik jest dostępny do pobrania z [repozytorium Git programu Azure Data Lake](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/Drivers.txt).
* **Power BI Desktop**. Możesz pobrać ten z [Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=45331). 

## <a name="create-a-report-in-power-bi-desktop"></a>Tworzenie raportu w programie Power BI Desktop
1. Uruchom Power BI Desktop na tym komputerze.
2. Z **Home** wstążki, kliknij przycisk **Pobierz dane**, a następnie kliknij przycisk więcej. W **Pobierz dane** okno dialogowe, kliknij przycisk **Azure**, kliknij przycisk **Azure Data Lake Store**, a następnie kliknij przycisk **Connect**.
   
    ![Nawiązać połączenia z usługą Data Lake Store](./media/data-lake-store-power-bi/get-data-lake-store-account.png "nawiązać połączenia z usługą Data Lake Store")
3. Jeśli zostanie wyświetlone okno dialogowe o łączniku jest na etapie programowania, zdecydować, aby kontynuować.
4. W **Microsoft Azure Data Lake Store** okno dialogowe, podaj adres URL do swojego konta usługi Data Lake Store, a następnie kliknij przycisk **OK**.
   
    ![Adres URL dla usługi Data Lake Store](./media/data-lake-store-power-bi/get-data-lake-store-account-url.png "adres URL dla usługi Data Lake Store")
5. W następnym oknie dialogowym kliknij **Zaloguj** do logowania się do konta usługi Data Lake Store. Nastąpi przekierowanie na stronę logowania Twojej organizacji. Postępuj zgodnie z monitami, aby zalogować się do konta.
   
    ![Zaloguj się do usługi Data Lake Store](./media/data-lake-store-power-bi/get-data-lake-store-account-signin.png "Zaloguj się do usługi Data Lake Store")
6. Po pomyślnym zalogowaniu, kliknij przycisk **Connect**.
   
    ![Nawiązać połączenia z usługą Data Lake Store](./media/data-lake-store-power-bi/get-data-lake-store-account-connect.png "nawiązać połączenia z usługą Data Lake Store")
7. Następne okno dialogowe zawiera plik, który został przekazany do konta usługi Data Lake Store. Sprawdź informacje, a następnie kliknij przycisk **obciążenia**.
   
    ![Ładowanie danych z usługi Data Lake Store](./media/data-lake-store-power-bi/get-data-lake-store-account-load.png "ładowanie danych z usługi Data Lake Store")
8. Po danych został pomyślnie załadowany do usługi Power BI, pojawi się następujące pola **pola** kartę.
   
    ![Zaimportowane pola](./media/data-lake-store-power-bi/imported-fields.png "zaimportowane pola")
   
    Jednak do wizualizowania i analizowania danych, firma Microsoft preferowane jest dane, które mają być dostępne dla następujących pól
   
    ![Żądany pola](./media/data-lake-store-power-bi/desired-fields.png "żądanego pola")
   
    W następnych krokach modyfikacjom zapytanie, aby przekonwertować importowanych danych w wybranym formacie.
9. Z **Home** wstążki, kliknij przycisk **edytowanie zapytań**.
   
    ![Edytuj zapytania](./media/data-lake-store-power-bi/edit-queries.png "edytowanie zapytań")
10. W edytorze zapytań w obszarze **zawartości** kolumny, kliknij przycisk **binarne**.
    
    ![Edytuj zapytania](./media/data-lake-store-power-bi/convert-query1.png "edytowanie zapytań")
11. Zostanie wyświetlona ikona pliku, który reprezentuje **Drivers.txt** pliku, który został przekazany. Kliknij prawym przyciskiem myszy plik, a następnie kliknij przycisk **CSV**.    
    
    ![Edytuj zapytania](./media/data-lake-store-power-bi/convert-query2.png "edytowanie zapytań")
12. Dane wyjściowe powinny być widoczne, jak pokazano poniżej. Twoje dane są teraz dostępne w formacie, który umożliwia tworzenie wizualizacji.
    
    ![Edytuj zapytania](./media/data-lake-store-power-bi/convert-query3.png "edytowanie zapytań")
13. Z **Home** wstążki, kliknij przycisk **zamknąć i zastosować**, a następnie kliknij przycisk **zamknąć i zastosować**.
    
    ![Edytuj zapytania](./media/data-lake-store-power-bi/load-edited-query.png "edytowanie zapytań")
14. Po zaktualizowaniu zapytanie **pola** kartę zostaną wyświetlone nowe pola dostępne dla wizualizacji.
    
    ![Zaktualizowano pola](./media/data-lake-store-power-bi/updated-query-fields.png "zaktualizowane pól")
15. Możemy utworzyć wykres kołowy do reprezentowania sterowników w każdemu miastu dla danego kraju. Aby to zrobić, należy wybrać następujące opcje.
    
    1. Na karcie wizualizacje kliknij symbol wykresu kołowego.
       
        ![Utwórz wykres kołowy](./media/data-lake-store-power-bi/create-pie-chart.png "utworzyć wykres kołowy")
    2. Kolumny, które firma Microsoft ma być używane są **4 kolumny** (nazwa miasta) i **7 kolumn** (nazwa kraju). Przeciągnij te kolumny z **pola** karty, aby **wizualizacje** karcie, jak pokazano poniżej.
       
        ![Tworzenie wizualizacji](./media/data-lake-store-power-bi/create-visualizations.png "tworzenie wizualizacji")
    3. Wykres kołowy powinien teraz wyglądać tak jak pokazano poniżej.
       
        ![Wykres kołowy](./media/data-lake-store-power-bi/pie-chart.png "tworzenie wizualizacji")
16. Po wybraniu określonego kraju z poziomu filtry strony, będą teraz widoczne liczbę sterowników w każdemu miastu wybranego kraju. Na przykład w obszarze **wizualizacje** , w obszarze **strony poziomu filtry**, wybierz pozycję **Brazylia**.
    
    ![Wybierz kraj](./media/data-lake-store-power-bi/select-country.png "wybierz kraj")
17. Wykres kołowy jest automatycznie aktualizowany, aby wyświetlić sterowniki miast Brazylia.
    
    ![Sterowniki w danym kraju](./media/data-lake-store-power-bi/driver-per-country.png "sterowników dla każdego kraju")
18. Z **pliku** menu, kliknij przycisk **zapisać** można zapisać jako plik Power BI Desktop wizualizacji.

## <a name="publish-report-to-power-bi-service"></a>Publikowanie raportu usługi Power BI
Po utworzeniu wizualizacjach w programie Power BI Desktop, możesz go udostępnić innym publikując go do usługi Power BI. Aby uzyskać instrukcje, jak to zrobić, zobacz [publikowania z Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-upload-desktop-files/).

## <a name="see-also"></a>Zobacz także
* [Analizowanie danych w usłudze Data Lake Store za pomocą usługi Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md)

