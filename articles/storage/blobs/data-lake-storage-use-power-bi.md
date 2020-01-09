---
title: Analizowanie danych w Azure Data Lake Storage Gen2 przy użyciu Power BI | Microsoft Docs
description: Używanie Power BI do analizowania danych przechowywanych w Azure Data Lake Storage Gen2
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: conceptual
ms.date: 07/18/2019
ms.author: normesta
ms.reviewer: bensack
ms.openlocfilehash: 156999c6b6d8451516ac1c0f095e1a864420d0b2
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75354828"
---
# <a name="analyze-data-in-azure-data-lake-storage-gen2-by-using-power-bi"></a>Analizowanie danych w Azure Data Lake Storage Gen2 przy użyciu Power BI

W tym artykule dowiesz się, jak za pomocą Power BI Desktop analizować i wizualizować dane przechowywane na koncie magazynu, które ma hierarchiczną przestrzeń nazw (Azure Data Lake Storage Gen2).

## <a name="prerequisites"></a>Wymagania wstępne

Przed przystąpieniem do wykonania kroków opisanych w tym samouczku należy dysponować następującymi elementami:

> [!div class="checklist"]
> * Subskrypcja platformy Azure. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Konto magazynu, które ma hierarchiczną przestrzeń nazw. Postępuj zgodnie z [tymi](data-lake-storage-quickstart-create-account.md) instrukcjami, aby je utworzyć.
> W tym artykule przyjęto założenie, że utworzono konto o nazwie `myadlsg2`.
> * Otrzymujesz jedną z następujących ról dla konta magazynu: **czytnik danych obiektów BLOB**, **współautor danych obiektów BLOB**lub **właściciel danych obiektów BLOB**.
> * Przykładowy plik danych o nazwie `Drivers.txt` znajduje się na koncie magazynu.
> Możesz pobrać ten przykład z [repozytorium Azure Data Lake git](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/Drivers.txt), a następnie przekazać ten plik na konto magazynu.
> * **Power BI Desktop**. Możesz pobrać ten program z [Centrum pobierania Microsoft](https://www.microsoft.com/download/details.aspx?id=45331). 

## <a name="create-a-report-in-power-bi-desktop"></a>Tworzenie raportu w programie Power BI Desktop

1. Uruchom Power BI Desktop na komputerze.
2. Na karcie **Narzędzia główne** wstążki kliknij pozycję **Pobierz dane**, a następnie kliknij pozycję **więcej**.
3. W oknie dialogowym **pobieranie danych** kliknij pozycję **Azure**, kliknij pozycję **Azure Data Lake Store Gen2 (beta)** , a następnie kliknij pozycję **Połącz**.

    ![Pobierz stronę danych](media/data-lake-storage-use-power-bi/get-data-page.png)

4. W oknie dialogowym **Azure Data Lake Storage Gen2** możesz podać adres URL konta Azure Data Lake Storage Gen2, systemu plików lub podfolderu przy użyciu formatu punktu końcowego kontenera. Adresy URL Data Lake Storage Gen2 mają następujący wzorzec `https://<accountname>.dfs.core.windows.net/<filesystemname>/<subfolder>` a następnie kliknij przycisk **OK**.

    ![Adres URL](media/data-lake-storage-use-power-bi/adls-url.png)

5. W następnym oknie dialogowym kliknij przycisk **Zaloguj** się, aby zalogować się do konta magazynu. Nastąpi przekierowanie do strony logowania w organizacji. Postępuj zgodnie z monitami, aby zalogować się do konta.

    ![Strona logowania](media/data-lake-storage-use-power-bi/sign-in.png)

6. Po pomyślnym zalogowaniu kliknij pozycję **Połącz**.

    ![Strona zalogowana](media/data-lake-storage-use-power-bi/signed-in.png)

7. Następne okno dialogowe przedstawia wszystkie pliki pod adresem URL podanym w kroku 4 powyżej, w tym plik przekazany do konta magazynu. Sprawdź informacje, a następnie kliknij przycisk **Załaduj**.

    ![Systemy plików](media/data-lake-storage-use-power-bi/file-systems.png)

8. Po pomyślnym załadowaniu danych do Power BI na karcie **pola** zostaną wyświetlone następujące pola.

    ![Karta pola](media/data-lake-storage-use-power-bi/fields.png)

    Jednak aby wizualizować i analizować dane, wolimy, aby dane były dostępne według następujących pól.

    ![Pola](media/data-lake-storage-use-power-bi/preferred-fields.png)

    W następnych krokach zaktualizujemy zapytanie w celu przekonwertowania zaimportowanych danych w żądanym formacie.

9. Na karcie **Narzędzia główne** na wstążce kliknij przycisk **Edytuj zapytania**.

    ![Zapytania](media/data-lake-storage-use-power-bi/queries.png)

10. W **Edytorze zapytań**w kolumnie **zawartość** kliknij pozycję dane **binarne**. Plik zostanie automatycznie wykryty jako wolumin CSV i powinny zostać wyświetlone dane wyjściowe, jak pokazano poniżej. Twoje dane są teraz dostępne w formacie, którego można użyć do tworzenia wizualizacji.

    ![Dane wyjściowe](media/data-lake-storage-use-power-bi/binary.png)

11. Na karcie **Narzędzia główne** na wstążce kliknij przycisk **Zamknij** i **Zastosuj**, a następnie kliknij przycisk **Zamknij** i **Zastosuj**.

    ![Zamknięcie i zastosowanie](media/data-lake-storage-use-power-bi/close-apply.png)

12. Po zaktualizowaniu zapytania na karcie **pola** zostaną wyświetlone nowe pola dostępne dla wizualizacji.

    ![Nowe pola](media/data-lake-storage-use-power-bi/new-fields.png)

13. Pozwól nam utworzyć wykres kołowy reprezentujący sterowniki w poszczególnych miastoch dla danego kraju. W tym celu należy wybrać następujące opcje.

    Na karcie **wizualizacje** kliknij symbol wykresu kołowego.

    ![Wizualizacje](media/data-lake-storage-use-power-bi/visualizations.png)

    Kolumny, które będą używane, to kolumna 4 (nazwa miasta) i kolumna 7 (nazwa kraju). Przeciągnij te kolumny z karty **pola** na kartę **wizualizacje** , jak pokazano poniżej.

    ![Przeciągnij pola](media/data-lake-storage-use-power-bi/visualizations-drag-fields.png)

    Wykres kołowy powinien teraz wyglądać podobnie do przedstawionego poniżej.

    ![Wykres kołowy](media/data-lake-storage-use-power-bi/pie-chart.png)

14. Wybranie określonego kraju z filtrów na poziomie strony pozwala teraz zobaczyć liczbę sterowników w każdym mieście wybranego kraju. Na przykład na karcie **wizualizacje** w obszarze filtry na **poziomie strony**wybierz pozycję **Brazylia**.

    ![Filtry stron](media/data-lake-storage-use-power-bi/page-filters.png)

15. Wykres kołowy jest automatycznie aktualizowany w celu wyświetlenia sterowników w miejscowościach Brazylii.

    ![Brazylia](media/data-lake-storage-use-power-bi/pie-chart-updated.png)

16. W menu **plik** kliknij polecenie **Zapisz** , aby zapisać wizualizację jako plik Power BI Desktop.

## <a name="publish-report-to-power-bi-service"></a>Publikuj raport w usługa Power BI

Po utworzeniu wizualizacji w Power BI Desktop można udostępnić ją innym osobom, publikując ją w usługa Power BI. Aby uzyskać instrukcje, jak to zrobić, zobacz temat [Publikowanie z Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-upload-desktop-files/).
