---
title: Analizowanie danych w usłudze Azure Data Lake Storage Gen1 przy użyciu usługi Power BI | Dokumenty firmy Microsoft
description: Analizowanie danych przechowywanych w usłudze Azure Data Lake Storage Gen1 za pomocą usługi Power BI
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: 57d19d27-e135-49d9-a7ea-46c48ef4e3bd
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: d8717b8f365e692b5f27bf8a04d65c5147b8f31b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "65603208"
---
# <a name="analyze-data-in-azure-data-lake-storage-gen1-by-using-power-bi"></a>Analizowanie danych w usłudze Azure Data Lake Storage Gen1 przy użyciu usługi Power BI
W tym artykule dowiesz się, jak analizować i wizualizować dane przechowywane w usłudze Azure Data Lake Storage Gen1 za pomocą programu Power BI Desktop.

## <a name="prerequisites"></a>Wymagania wstępne
Przed przystąpieniem do wykonania kroków opisanych w tym samouczku należy dysponować następującymi elementami:

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Konto Data Lake Storage Gen1**. Postępuj zgodnie z instrukcjami w [usłudze Wprowadzenie do usługi Azure Data Lake Storage Gen1 przy użyciu witryny Azure portal.](data-lake-store-get-started-portal.md) W tym artykule założono, że utworzono już konto Data Lake Storage Gen1 o nazwie **myadlsg1**i przesłano do niego przykładowy plik danych (**Drivers.txt).** Ten przykładowy plik jest dostępny do pobrania z [repozytorium Git usługi Azure Data Lake.](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/Drivers.txt)
* **Program Power BI Desktop**. Można go pobrać z [Centrum pobierania Microsoft](https://www.microsoft.com/en-us/download/details.aspx?id=45331). 

## <a name="create-a-report-in-power-bi-desktop"></a>Tworzenie raportu w programie Power BI Desktop
1. Uruchom program Power BI Desktop na komputerze.
2. Na wstążce **Narzędzia domowe** kliknij pozycję **Pobierz dane**, a następnie kliknij pozycję Więcej. W oknie dialogowym **Pobierz dane** kliknij pozycję **Azure**, kliknij pozycję Usługa Azure Data **Lake Store**, a następnie kliknij pozycję **Połącz**.
   
    ![Łączenie się z gen1 magazynu usługi Data Lake](./media/data-lake-store-power-bi/get-data-lake-store-account.png "Łączenie się z gen1 magazynu usługi Data Lake")
3. Jeśli zostanie wyświetlone okno dialogowe dotyczące łącznika w fazie rozwoju, zdecyduj się kontynuować.
4. W oknie dialogowym **Usługa Azure Data Lake Store** podaj adres URL konta Usługi Data Lake Storage Gen1, a następnie kliknij przycisk **OK**.
   
    ![Adres URL usługi Data Lake Storage Gen1](./media/data-lake-store-power-bi/get-data-lake-store-account-url.png "Adres URL usługi Data Lake Storage Gen1")
5. W następnym oknie dialogowym kliknij pozycję **Zaloguj się,** aby zalogować się do konta Data Lake Storage Gen1. Nastąpi przekierowanie do strony logowania organizacji. Postępuj zgodnie z instrukcjami, aby zalogować się na konto.
   
    ![Zaloguj się do usługi Data Lake Storage Gen1](./media/data-lake-store-power-bi/get-data-lake-store-account-signin.png "Zaloguj się do usługi Data Lake Storage Gen1")
6. Po pomyślnym zalogowaniu kliknij przycisk **Połącz**.
   
    ![Łączenie się z gen1 magazynu usługi Data Lake](./media/data-lake-store-power-bi/get-data-lake-store-account-connect.png "Łączenie się z gen1 magazynu usługi Data Lake")
7. Następne okno dialogowe zawiera plik przekazany na konto Usługi Data Lake Storage Gen1. Sprawdź informacje, a następnie kliknij przycisk **Załaduj**.
   
    ![Ładowanie danych z usługi Data Lake Storage Gen1](./media/data-lake-store-power-bi/get-data-lake-store-account-load.png "Ładowanie danych z usługi Data Lake Storage Gen1")
8. Po pomyślnym załadowaniu danych do usługi Power BI zostaną wyświetlone następujące pola na karcie **Pola.**
   
    ![Importowane pola](./media/data-lake-store-power-bi/imported-fields.png "Importowane pola")
   
    Jednak, aby wizualizować i analizować dane, wolimy, aby dane były dostępne w następujących polach
   
    ![Żądane pola](./media/data-lake-store-power-bi/desired-fields.png "Żądane pola")
   
    W następnych krokach zaktualizujemy kwerendę, aby przekonwertować zaimportowane dane w żądanym formacie.
9. Na wstążce **Narzędzia domowe** kliknij pozycję **Edytuj kwerendy**.
   
    ![Edytuj zapytania](./media/data-lake-store-power-bi/edit-queries.png "Edytuj zapytania")
10. W Edytorze zapytań w kolumnie **Zawartość** kliknij pozycję **Plik binarny**.
    
    ![Edytuj zapytania](./media/data-lake-store-power-bi/convert-query1.png "Edytuj zapytania")
11. Zostanie wyświetlona ikona pliku reprezentująca przekazany plik **Drivers.txt.** Kliknij prawym przyciskiem myszy plik, a następnie kliknij polecenie **CSV**.    
    
    ![Edytuj zapytania](./media/data-lake-store-power-bi/convert-query2.png "Edytuj zapytania")
12. Powinieneś zobaczyć dane wyjściowe, jak pokazano poniżej. Dane są teraz dostępne w formacie, którego można użyć do tworzenia wizualizacji.
    
    ![Edytuj zapytania](./media/data-lake-store-power-bi/convert-query3.png "Edytuj zapytania")
13. Na wstążce **Narzędzia domowe** kliknij pozycję Zamknij **i zastosuj**, a następnie kliknij pozycję Zamknij **i zastosuj**.
    
    ![Edytuj zapytania](./media/data-lake-store-power-bi/load-edited-query.png "Edytuj zapytania")
14. Po zaktualizowaniu **kwerendy** na karcie Pola zostaną wyświetlone nowe pola dostępne do wizualizacji.
    
    ![Zaktualizowane pola](./media/data-lake-store-power-bi/updated-query-fields.png "Zaktualizowane pola")
15. Utwórzmy wykres kołowy reprezentujący kierowców w każdym mieście dla danego kraju/regionu. Aby to zrobić, dokonaj następujących wyborów.
    
    1. Na karcie Wizualizacje kliknij symbol wykresu kołowego.
       
        ![Tworzenie wykresu kołowego](./media/data-lake-store-power-bi/create-pie-chart.png "Tworzenie wykresu kołowego")
    2. Kolumny, których będziemy używać, to **Kolumna 4** (nazwa miasta) i **Kolumna 7** (nazwa kraju/regionu). Przeciągnij te kolumny z karty **Pola** na kartę **Wizualizacje,** jak pokazano poniżej.
       
        ![Tworzenie wizualizacji](./media/data-lake-store-power-bi/create-visualizations.png "Tworzenie wizualizacji")
    3. Wykres kołowy powinien teraz przypominać wykres pokazany poniżej.
       
        ![Wykres kołowy](./media/data-lake-store-power-bi/pie-chart.png "Tworzenie wizualizacji")
16. Wybierając określony kraj/region z filtrów poziomu strony, możesz teraz zobaczyć liczbę sterowników w każdym mieście wybranego kraju/regionu. Na przykład na karcie **Wizualizacje** w obszarze **Filtry poziomu strony**wybierz pozycję **Brazylia**.
    
    ![Wybierz kraj](./media/data-lake-store-power-bi/select-country.png "Wybierz kraj/region")
17. Wykres kołowy jest automatycznie aktualizowany w celu wyświetlenia sterowników w miastach Brazylii.
    
    ![Kierowcy w kraju](./media/data-lake-store-power-bi/driver-per-country.png "Kierowcy na kraj/region")
18. Z menu **Plik** kliknij polecenie **Zapisz,** aby zapisać wizualizację jako plik programu Power BI Desktop.

## <a name="publish-report-to-power-bi-service"></a>Publikowanie raportu w usłudze Power BI
Po utworzeniu wizualizacji w programie Power BI Desktop można je udostępnić innym osobom, publikując je w usłudze Power BI. Aby uzyskać instrukcje dotyczące tego, jak to zrobić, zobacz [Publikowanie z programu Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-upload-desktop-files/).

## <a name="see-also"></a>Zobacz też
* [Analizowanie danych w usłudze Data Lake Storage Gen1 przy użyciu usługi Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md)

