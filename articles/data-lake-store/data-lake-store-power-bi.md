---
title: Analizowanie danych w usłudze Azure Data Lake magazynu Gen1 przy użyciu usługi Power BI | Dokumentacja firmy Microsoft
description: Usługa Power BI umożliwia analizowanie danych przechowywanych w usłudze Azure Data Lake magazynu Gen1
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
ms.openlocfilehash: b873d90dbc33aff27cd2fc3b5099b08eacf929b6
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "58880070"
---
# <a name="analyze-data-in-azure-data-lake-storage-gen1-by-using-power-bi"></a>Analizowanie danych w usłudze Azure Data Lake magazynu Gen1 przy użyciu usługi Power BI
W tym artykule dowiesz się, jak analizować i wizualizować dane przechowywane w usłudze Azure Data Lake magazynu Gen1 za pomocą programu Power BI Desktop.

## <a name="prerequisites"></a>Wymagania wstępne
Przed przystąpieniem do wykonania kroków opisanych w tym samouczku należy dysponować następującymi elementami:

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Konta Data Lake Storage Gen1**. Postępuj zgodnie z instrukcjami w artykule [Rozpoczynanie pracy z usługą Azure Data Lake Storage Gen1 przy użyciu witryny Azure portal](data-lake-store-get-started-portal.md). W tym artykule założono, że już utworzono konto Data Lake Storage Gen1 o nazwie **myadlsg1**i przekazano przykładowy plik danych (**Drivers.txt**) do niego. Ten przykładowy plik jest dostępny do pobrania [repozytorium Git usługi Azure Data Lake](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/Drivers.txt).
* **Power BI Desktop**. Możesz pobrać ten program z [Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=45331). 

## <a name="create-a-report-in-power-bi-desktop"></a>Tworzenie raportu w programie Power BI Desktop
1. Uruchom program Power BI Desktop na tym komputerze.
2. Z **Home** wstążki, kliknij przycisk **Pobierz dane**, a następnie kliknij przycisk więcej. W **Pobierz dane** okno dialogowe, kliknij przycisk **Azure**, kliknij przycisk **usługi Azure Data Lake Store**, a następnie kliknij przycisk **Connect**.
   
    ![Nawiązać połączenie z Data Lake Storage Gen1](./media/data-lake-store-power-bi/get-data-lake-store-account.png "nawiązać połączenie z Gen1 Lake magazynu danych")
3. Jeśli pojawi się okno dialogowe o łączniku jest w fazie projektowania zdecydować, aby kontynuować.
4. W **usługi Azure Data Lake Store** okno dialogowe, podaj adres URL do konta usługi Data Lake Storage Gen1, a następnie kliknij przycisk **OK**.
   
    ![Adres URL dla programu Data Lake Storage Gen1](./media/data-lake-store-power-bi/get-data-lake-store-account-url.png "adres URL dla Gen1 Lake magazynu danych")
5. W następnym oknie dialogowym kliknij **Zaloguj** do logowania się do konta Data Lake Storage Gen1. Nastąpi przekierowanie na stronę logowania Twojej organizacji. Postępuj zgodnie z monitami, aby zalogować się do konta.
   
    ![Zaloguj się do programu Data Lake Storage Gen1](./media/data-lake-store-power-bi/get-data-lake-store-account-signin.png "Zaloguj się do programu Data Lake Storage Gen1")
6. Po pomyślnym zalogowaniu, kliknij przycisk **Connect**.
   
    ![Nawiązać połączenie z Data Lake Storage Gen1](./media/data-lake-store-power-bi/get-data-lake-store-account-connect.png "nawiązać połączenie z Gen1 Lake magazynu danych")
7. Następne okno dialogowe zawiera plik, który został przekazany do konta usługi Data Lake Storage Gen1. Sprawdź informacje, a następnie kliknij przycisk **obciążenia**.
   
    ![Ładowanie danych z Data Lake Storage Gen1](./media/data-lake-store-power-bi/get-data-lake-store-account-load.png "ładowanie danych z Data Lake Storage Gen1")
8. Po pomyślnym załadowaniu danych do usługi Power BI, zobaczysz następujące pola **pola** kartę.
   
    ![Zaimportowane pola](./media/data-lake-store-power-bi/imported-fields.png "zaimportowane pola")
   
    Wizualizowanie i analizowanie danych, wolimy jednak dane, które mają być dostępne dla następujących pól
   
    ![Żądane pola](./media/data-lake-store-power-bi/desired-fields.png "żądanego pola")
   
    W następnych krokach będą aktualizowane zapytanie, aby przekonwertować zaimportowanych danych w wybranym formacie.
9. Z **Home** wstążki, kliknij przycisk **Edytuj zapytania**.
   
    ![Edytuj zapytania](./media/data-lake-store-power-bi/edit-queries.png "edytowanie zapytań")
10. W edytorze zapytań w ramach **zawartości** kolumny, kliknij przycisk **binarne**.
    
    ![Edytuj zapytania](./media/data-lake-store-power-bi/convert-query1.png "edytowanie zapytań")
11. Zostanie wyświetlona ikona pliku, który reprezentuje **Drivers.txt** przekazanego pliku. Kliknij prawym przyciskiem myszy plik, a następnie kliknij przycisk **CSV**.    
    
    ![Edytuj zapytania](./media/data-lake-store-power-bi/convert-query2.png "edytowanie zapytań")
12. Powinny pojawić się dane wyjściowe, jak pokazano poniżej. Dane są teraz dostępne w formacie, który służy do tworzenia wizualizacji.
    
    ![Edytuj zapytania](./media/data-lake-store-power-bi/convert-query3.png "edytowanie zapytań")
13. Z **Home** wstążki, kliknij przycisk **Zamknij i Zastosuj**, a następnie kliknij przycisk **Zamknij i Zastosuj**.
    
    ![Edytuj zapytania](./media/data-lake-store-power-bi/load-edited-query.png "edytowanie zapytań")
14. Po zaktualizowaniu zapytanie **pola** kartę wyświetli nowe pola, dostępny dla wizualizacji.
    
    ![Zaktualizowane pola](./media/data-lake-store-power-bi/updated-query-fields.png "zaktualizowane pola")
15. Daj nam utworzyć wykres kołowy do reprezentowania sterowników w każde Miasto dla danego kraju. Aby to zrobić, należy wybrać następujące opcje.
    
    1. Na karcie wizualizacje kliknij symbol dla wykresu kołowego.
       
        ![Utwórz wykres kołowy](./media/data-lake-store-power-bi/create-pie-chart.png "utworzyć wykres kołowy")
    2. Kolumny, których będziemy używać są **kolumny 4** (nazwa miasta) i **7 kolumn** (nazwa kraju). Przeciągnij te kolumny z **pola** kartę **wizualizacje** karty, jak pokazano poniżej.
       
        ![Tworzenie wizualizacji](./media/data-lake-store-power-bi/create-visualizations.png "tworzenie wizualizacji")
    3. Wykres kołowy powinien teraz przypominać podobny do przedstawionego poniżej.
       
        ![Wykres kołowy](./media/data-lake-store-power-bi/pie-chart.png "tworzenie wizualizacji")
16. Wybierając określonego kraju z filtrów na poziomie strony, będą teraz widoczne liczbę sterowników w każde Miasto wybranego kraju. Na przykład w obszarze **wizualizacje** , w obszarze **filtry na poziomie strony**, wybierz opcję **Brazylii**.
    
    ![Wybierz kraj](./media/data-lake-store-power-bi/select-country.png "wybierz kraj")
17. Wykres kołowy zostanie automatycznie zaktualizowany do wyświetlenia sterowniki w miastach Brazylii.
    
    ![Sterowniki w kraju](./media/data-lake-store-power-bi/driver-per-country.png "sterowniki dla każdego kraju")
18. Z **pliku** menu, kliknij przycisk **Zapisz** Aby zapisać wizualizację jako plik programu Power BI Desktop.

## <a name="publish-report-to-power-bi-service"></a>Opublikuj raport w usłudze Power BI
Po utworzeniu wizualizacji w programie Power BI Desktop mogą udostępniać je innym osobom, publikując go do usługi Power BI. Aby uzyskać instrukcje, jak to zrobić, zobacz [publikowanie z programu Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-upload-desktop-files/).

## <a name="see-also"></a>Zobacz także
* [Analizowanie danych w Data Lake Storage Gen1 przy użyciu usługi Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md)

