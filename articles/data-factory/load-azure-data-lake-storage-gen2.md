---
title: Ładowanie danych do usługi Azure Data Lake Storage Gen2 za pomocą usługi Azure Data Factory
description: Kopiowanie danych do usługi Azure Data Lake Storage Gen2 za pomocą usługi Azure Data Factory
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/15/2019
ms.author: jingwang
ms.openlocfilehash: 2c04c13eb6399bebf908337d8f71bd03adf4d06a
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58447223"
---
# <a name="load-data-into-azure-data-lake-storage-gen2-with-azure-data-factory"></a>Ładowanie danych do usługi Azure Data Lake Storage Gen2 za pomocą usługi Azure Data Factory

Platformy Azure Data Lake Storage Gen2 to zbiór funkcji przeznaczonych do analizy danych big data, wbudowane [usługi Azure Blob storage](../storage/blobs/storage-blobs-introduction.md). Umożliwia łączenie się z danych za pomocą obu paradygmatów magazynu plików, jak systemu i obiekt.

Usługa Azure Data Factory to usługa integracji danych w pełni zarządzane w chmurze. Korzystać z niej, aby wypełnić usługi Data lake z danymi z bogatego zestawu w środowisku lokalnym i magazyny danych opartych na chmurze i zaoszczędzić czas podczas tworzenia rozwiązań do analizy. Aby uzyskać szczegółową listę obsługiwanych łączników, zobacz tabelę [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).

Usługa Azure Data Factory oferuje rozwiązanie do przenoszenia danych skalowalne, zarządzane. Z uwagi na architekturę skalowalnego w poziomie ADF jego pozyskiwać dane przy wysokiej przepływności. Aby uzyskać więcej informacji, zobacz [wydajności działania kopiowania](copy-activity-performance.md).

W tym artykule dowiesz się, jak używać narzędzia do kopiowania danych w bazie wiedzy Data Factory do ładowania danych z _usługi Amazon Web Services S3_ do _usługi Azure Data Lake Storage Gen2_. Możesz wykonać podobne kroki w celu skopiowania danych z innych typów magazynów danych.

>[!TIP]
>Kopiowanie danych z usługi Azure Data Lake Storage Gen1 do Gen2, można znaleźć [instruktażu określonych](load-azure-data-lake-storage-gen2-from-gen1.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure: Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).
* Konto usługi Azure Storage za pomocą magazynu Gen2 jeziora danych, włączone: Jeśli nie masz konta magazynu, kliknij przycisk [tutaj](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM) ją utworzyć.
* Konta usług AWS z przedział S3, która zawiera dane: W tym artykule pokazano, jak skopiować dane z usługi Amazon S3. Wykonując podobne kroki, można użyć innych magazynów danych.

## <a name="create-a-data-factory"></a>Tworzenie fabryki danych

1. W menu po lewej stronie wybierz **Utwórz zasób** > **dane + analiza** > **usługi Data Factory**:
   
   ![Wybór usługi Data Factory w okienku „Nowy”](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. W **nowa fabryka danych** Podaj wartości dla pól, które są wyświetlane na poniższej ilustracji: 
      
   ![Strona Nowa fabryka danych](./media/load-azure-data-lake-storage-gen2//new-azure-data-factory.png)
 
    * **Nazwa**: Wprowadź unikatową w skali globalnej nazwę fabryki danych platformy Azure. Jeśli zostanie wyświetlony błąd "Nazwa fabryki danych \"LoadADLSDemo\" jest niedostępny," Wprowadź inną nazwę dla fabryki danych. Na przykład można użyć nazwy  _**twojanazwa**_**ADFTutorialDataFactory**. Spróbuj ponownie utworzyć fabrykę danych. Artykuł [Data Factory naming rules (Zasady nazewnictwa fabryki danych)](naming-rules.md) zawiera zasady nazewnictwa artefaktów usługi Data Factory.
    * **Subskrypcja**: Wybieranie subskrypcji platformy Azure, w której chcesz utworzyć fabrykę danych. 
    * **Grupa zasobów**: Wybierz istniejącą grupę zasobów z listy rozwijanej lub **Utwórz nową** opcji, a następnie wprowadź nazwę grupy zasobów. Informacje na temat grup zasobów znajdują się w artykule [Using resource groups to manage your Azure resources](../azure-resource-manager/resource-group-overview.md) (Używanie grup zasobów do zarządzania zasobami platformy Azure).  
    * **Wersja**: Wybierz **V2**.
    * **Lokalizacja**: Wybierz lokalizację fabryki danych. Na liście rozwijanej są wyświetlane tylko obsługiwane lokalizacje. Magazyny danych, które są używane przez fabrykę danych mogą być w innych lokalizacjach i regionach. 

3. Wybierz pozycję **Utwórz**.
4. Po zakończeniu tworzenia przejdź z fabryką danych. Zostanie wyświetlony **usługi Data Factory** strony głównej, jak pokazano na poniższej ilustracji: 
   
   ![Strona główna fabryki danych](./media/load-azure-data-lake-storage-gen2/data-factory-home-page.png)

   Wybierz **tworzenie i monitorowanie** Kafelek, aby uruchomić aplikację integracji danych w osobnej karcie.

## <a name="load-data-into-azure-data-lake-storage-gen2"></a>Ładowanie danych do usługi Azure Data Lake Storage Gen2

1. W **wprowadzenie** wybierz opcję **kopiowania danych** Kafelek, aby uruchomić narzędzie do kopiowania danych: 

   ![Kafelek narzędzia do kopiowania danych](./media/load-azure-data-lake-storage-gen2/copy-data-tool-tile.png)
2. W **właściwości** określ **CopyFromAmazonS3ToADLS** dla **Nazwa zadania** i wybierz przycisk **dalej**:

    ![Strona właściwości](./media/load-azure-data-lake-storage-gen2/copy-data-tool-properties-page.png)
3. W **magazynu danych źródłowych** kliknij **+ Utwórz nowe połączenie**:

    ![Strona Źródłowy magazyn danych](./media/load-azure-data-lake-storage-gen2/source-data-store-page.png)
    
    Wybierz **Amazon S3** z galerii łączników, a następnie wybierz pozycję **Kontynuuj**
    
    ![Strona s3 źródłowy magazyn danych](./media/load-azure-data-lake-storage-gen2/source-data-store-page-s3.png)
    
4. W **połączenia Określ Amazon S3** wykonaj następujące czynności:

   1. Określ **identyfikator klucza dostępu** wartość.
   2. Określ **tajny klucz dostępu** wartość.
   3. Kliknij przycisk **Testuj połączenie** Aby zweryfikować ustawienia, zaznacz opcję **Zakończ**.
   4. Zobaczysz, że nowe połączenie zostanie utworzona. Wybierz opcję **Dalej**.
   
      ![Określanie konta usługi Amazon S3](./media/load-azure-data-lake-storage-gen2/specify-amazon-s3-account.png)
      
5. W **Wybieranie pliku lub folderu wejściowego** strony, przejdź do folderu i pliku, który chcesz skopiować. Wybierz folderów i plików, wybierz pozycję **wybierz**:

    ![Wybieranie pliku lub folderu wejściowego](./media/load-azure-data-lake-storage-gen2/choose-input-folder.png)

6. Określanie zachowania dotyczącego kopiowania, sprawdzając **kopiowania plików rekursywnie** i **kopia binarna** opcje. Wybierz **dalej**:

    ![Określ folder wyjściowy](./media/load-azure-data-lake-storage-gen2/specify-binary-copy.png)
    
7. W **docelowego magazynu danych** kliknij **+ Utwórz nowe połączenie**, a następnie wybierz pozycję **usługi Azure Data Lake Storage Gen2**i wybierz **Kontynuuj**:

    ![Strona Docelowy magazyn danych](./media/load-azure-data-lake-storage-gen2/destination-data-storage-page.png)

8. W **połączenia Określ magazyn usługi Azure Data Lake** wykonaj następujące czynności:

   1. Wybierz usługi Data Lake magazynu Gen2 — listy rozwijanej w stanie Zmiana warstwy konta z "Nazwa konta magazynu".
   2. Wybierz **Zakończ** do utworzenia połączenia. Następnie wybierz przycisk **Dalej**.
   
   ![Określ konto usługi Azure Data Lake Storage Gen2](./media/load-azure-data-lake-storage-gen2/specify-adls.png)

9. W **Wybieranie wyjściowego pliku lub folderu** wpisz **copyfroms3** jako nazwa folderu danych wyjściowych, a następnie wybierz **dalej**: 

    ![Określ folder wyjściowy](./media/load-azure-data-lake-storage-gen2/specify-adls-path.png)

10. W **ustawienia** wybierz opcję **dalej** można użyć wartości domyślnych:

    ![Strona Ustawienia](./media/load-azure-data-lake-storage-gen2/copy-settings.png)
11. W **Podsumowanie** strony, przejrzyj ustawienia i wybierz **dalej**:

    ![Strona podsumowania](./media/load-azure-data-lake-storage-gen2/copy-summary.png)
12. W **strony wdrażania**, wybierz opcję **Monitor** Aby monitorować potok:

    ![Strona Wdrażanie](./media/load-azure-data-lake-storage-gen2/deployment-page.png)
13. Zwróć uwagę, że karta **Monitor** po lewej stronie jest automatycznie wybrana. **Akcje** kolumna zawiera linki, aby wyświetlić szczegóły uruchamiania działania i ponowne uruchamianie potoku:

    ![Monitorowanie uruchomień potoku](./media/load-azure-data-lake-storage-gen2/monitor-pipeline-runs.png)

14. Aby wyświetlić uruchomienia działań, które są skojarzone z uruchomieniem potoku, wybierz pozycję **Wyświetl uruchomienia działań** łącze w **akcje** kolumny. W potoku jest tylko jedno działanie (działanie kopiowania), dlatego na liście jest wyświetlana tylko jedna pozycja. Aby wrócić do widoku uruchomienia potoku, wybierz **potoki** link u góry. Wybierz pozycję **Odśwież**, aby odświeżyć listę. 

    ![Monitorowanie uruchomień działania](./media/load-azure-data-lake-storage-gen2/monitor-activity-runs.png)

15. Aby monitorować szczegóły wykonania dla każdego działania kopiowania, wybierz **szczegóły** link (obraz okularów) w obszarze **akcje** w działaniu, w widoku monitorowania. Możesz monitorować szczegółowe informacje, takie jak ilość danych skopiowanych ze źródła do ujścia, przepływność danych, wykonywania kroków za pomocą odpowiedni czas i konfiguracje używane:

    ![Szczegóły uruchamiania działania monitora](./media/load-azure-data-lake-storage-gen2/monitor-activity-run-details.png)

16. Upewnij się, że dane zostały skopiowane na konto usługi Data Lake Storage Gen2.

## <a name="best-practices"></a>Najlepsze praktyki

Podczas kopiowania dużych woluminów danych z magazynu danych oparte na plikach, sugerowane do:

- Podzielić pliki na 10TB do użycia pojemności 30TB każdy.
- Nie wyzwalają zbyt wiele przebiegów współbieżnych kopiowania, aby uniknąć ograniczania z magazynów danych źródła lub ujścia. Możesz uruchomić za pomocą jednej kopii, uruchom i monitor przepływności, a następnie stopniowo dodawać więcej zgodnie z potrzebami.

## <a name="next-steps"></a>Kolejne kroki

* [Omówienie działania kopiowania](copy-activity-overview.md)
* [Łącznik usługi Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)