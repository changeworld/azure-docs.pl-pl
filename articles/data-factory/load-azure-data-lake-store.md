---
title: Ładowanie danych do usługi Azure Data Lake Storage Gen1 przy użyciu usługi Azure Data Factory | Dokumentacja firmy Microsoft
description: Kopiowanie danych do usługi Azure Data Lake Storage Gen1 za pomocą usługi Azure Data Factory
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/17/2018
ms.author: jingwang
ms.openlocfilehash: 522b9743af28dedb2aec5682a1ae95b9d52ad2d9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60549119"
---
# <a name="load-data-into-azure-data-lake-storage-gen1-by-using-azure-data-factory"></a>Ładowanie danych do usługi Azure Data Lake Storage Gen1 przy użyciu usługi Azure Data Factory

[Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md) (wcześniej znane jako usługi Azure Data Lake Store) to repozytorium ogromnej skali całego przedsiębiorstwa dla obciążeń wynikających z analizy danych big data. Data Lake Storage Gen1 umożliwia przechwytywanie danych dowolnego rozmiaru, typie i szybkości wprowadzania. Dane są przechwytywane w jednym miejscu na potrzeby analizy operacyjnej i rozpoznawczej.

Usługa Azure Data Factory to usługa integracji danych w pełni zarządzane w chmurze. Usługi można użyć do wypełniania usługi Data lake za pomocą danych z istniejącego systemu i zaoszczędzić czas podczas tworzenia rozwiązań do analizy.

Usługa Azure Data Factory oferuje następujące korzyści dla ładowania danych do usługi Data Lake Storage Gen1:

* **Łatwe do skonfigurowania**: Intuicyjny Kreator krok 5 z bez skryptu wymagane.
* **Szczegółowe dane sklepu obsługują**: Wbudowana obsługa bogaty zestaw w środowisku lokalnym i magazynami danych w chmurze. Aby uzyskać szczegółową listę, zobacz tabelę [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
* **Bezpieczeństwo i zgodność**: Dane są przesyłane za pośrednictwem protokołu HTTPS lub usługi ExpressRoute. Obecność usługi global service gwarantuje, że danych nigdy nie opuszcza granicy geograficznej.
* **Wysoka wydajność**: Maksymalna szybkość ładowania danych do 1-GB/s do Data Lake Storage Gen1. Aby uzyskać więcej informacji, zobacz [wydajności działania kopiowania](copy-activity-performance.md).

W tym artykule pokazano, jak za pomocą narzędzia Kopia fabryki danych do _ładowanie danych z usługi Amazon S3 do Data Lake Storage Gen1_. Możesz wykonać podobne kroki w celu skopiowania danych z innych typów magazynów danych.

> [!NOTE]
> Aby uzyskać więcej informacji, zobacz [kopiowanie danych do lub z Data Lake Storage Gen1 przy użyciu usługi Azure Data Factory](connector-azure-data-lake-store.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure: Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).
* Konta usługi Data Lake Storage Gen1: Jeśli nie masz konta Data Lake Storage Gen1, zapoznaj się z instrukcjami w [Tworzenie konta Data Lake Storage Gen1](../data-lake-store/data-lake-store-get-started-portal.md#create-a-data-lake-storage-gen1-account).
* Amazon S3: W tym artykule pokazano, jak skopiować dane z usługi Amazon S3. Wykonując podobne kroki, można użyć innych magazynów danych.

## <a name="create-a-data-factory"></a>Tworzenie fabryki danych

1. W menu po lewej stronie wybierz **Utwórz zasób** > **Analytics** > **usługi Data Factory**:
   
   ![Wybór usługi Data Factory w okienku „Nowy”](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. W **nowa fabryka danych** Podaj wartości dla pól, które są wyświetlane na poniższej ilustracji: 
      
   ![Strona Nowa fabryka danych](./media/load-data-into-azure-data-lake-store//new-azure-data-factory.png)
 
    * **Nazwa**: Wprowadź unikatową w skali globalnej nazwę fabryki danych platformy Azure. Jeśli zostanie wyświetlony błąd "Nazwa fabryki danych \"LoadADLSG1Demo\" jest niedostępny," Wprowadź inną nazwę dla fabryki danych. Na przykład można użyć nazwy  _**twojanazwa**_**ADFTutorialDataFactory**. Spróbuj ponownie utworzyć fabrykę danych. Artykuł [Data Factory naming rules (Zasady nazewnictwa fabryki danych)](naming-rules.md) zawiera zasady nazewnictwa artefaktów usługi Data Factory.
    * **Subskrypcja**: Wybieranie subskrypcji platformy Azure, w której chcesz utworzyć fabrykę danych. 
    * **Grupa zasobów**: Wybierz istniejącą grupę zasobów z listy rozwijanej lub **Utwórz nową** opcji, a następnie wprowadź nazwę grupy zasobów. Informacje na temat grup zasobów znajdują się w artykule [Using resource groups to manage your Azure resources](../azure-resource-manager/resource-group-overview.md) (Używanie grup zasobów do zarządzania zasobami platformy Azure).  
    * **Wersja**: Wybierz **V2**.
    * **Lokalizacja**: Wybierz lokalizację fabryki danych. Na liście rozwijanej są wyświetlane tylko obsługiwane lokalizacje. Magazyny danych, które są używane przez fabrykę danych mogą być w innych lokalizacjach i regionach. Te magazyny danych obejmują usługi Azure Data Lake Storage Gen1, usługa Azure Storage, Azure SQL Database i tak dalej.

3. Wybierz pozycję **Utwórz**.
4. Po zakończeniu tworzenia przejdź z fabryką danych. Zostanie wyświetlony **usługi Data Factory** strony głównej, jak pokazano na poniższej ilustracji: 
   
   ![Strona główna fabryki danych](./media/load-data-into-azure-data-lake-store/data-factory-home-page.png)

   Wybierz **tworzenie i monitorowanie** Kafelek, aby uruchomić aplikację integracji danych w osobnej karcie.

## <a name="load-data-into-data-lake-storage-gen1"></a>Ładowanie danych do usługi Data Lake Storage Gen1

1. W **wprowadzenie** wybierz opcję **kopiowania danych** Kafelek, aby uruchomić narzędzie do kopiowania danych: 

   ![Kafelek narzędzia do kopiowania danych](./media/load-data-into-azure-data-lake-store/copy-data-tool-tile.png)
2. W **właściwości** określ **CopyFromAmazonS3ToADLS** dla **Nazwa zadania** i wybierz przycisk **dalej**:

    ![Strona właściwości](./media/load-data-into-azure-data-lake-store/copy-data-tool-properties-page.png)
3. W **magazynu danych źródłowych** kliknij **+ Utwórz nowe połączenie**:

    ![Strona Źródłowy magazyn danych](./media/load-data-into-azure-data-lake-store/source-data-store-page.png)
    
    Wybierz **Amazon S3**i wybierz **Kontynuuj**
    
    ![Strona s3 źródłowy magazyn danych](./media/load-data-into-azure-data-lake-store/source-data-store-page-s3.png)
    
4. W **połączenia Określ Amazon S3** wykonaj następujące czynności: 
   1. Określ **identyfikator klucza dostępu** wartość.
   2. Określ **tajny klucz dostępu** wartość.
   3. Wybierz pozycję **Finish** (Zakończ).
   
      ![Określanie konta usługi Amazon S3](./media/load-data-into-azure-data-lake-store/specify-amazon-s3-account.png)
   
   4. Zostaną wyświetlone nowe połączenie. Wybierz opcję **Dalej**.
   
   ![Określanie konta usługi Amazon S3](./media/load-data-into-azure-data-lake-store/specify-amazon-s3-account-created.png)
   
5. W **Wybieranie pliku lub folderu wejściowego** strony, przejdź do folderu i pliku, który chcesz skopiować. Wybierz folderów i plików, wybierz pozycję **wybierz**, a następnie wybierz pozycję **dalej**:

    ![Wybieranie pliku lub folderu wejściowego](./media/load-data-into-azure-data-lake-store/choose-input-folder.png)

6. Wybierz opcję zachowania dotyczącego kopiowania, wybierając **kopiowania plików rekursywnie** i **kopia binarna** (kopiowanie plików jako — jest) opcji. Wybierz **dalej**:

    ![Określ folder wyjściowy](./media/load-data-into-azure-data-lake-store/specify-binary-copy.png)
    
7. W **docelowego magazynu danych** kliknij **+ Utwórz nowe połączenie**, a następnie wybierz pozycję **usługi Azure Data Lake Storage Gen1**i wybierz **Kontynuuj**:

    ![Strona Docelowy magazyn danych](./media/load-data-into-azure-data-lake-store/destination-data-storage-page.png)

8. W **nowej połączonej usługi (Azure Data Lake Storage Gen1)** wykonaj następujące czynności: 

   1. Wybierz konto usługi Data Lake Storage Gen1 **nazwa konta Data Lake Store**.
   2. Określ **dzierżawy**i wybierz przycisk Zakończ.
   3. Wybierz opcję **Dalej**.
   
   > [!IMPORTANT]
   > W tym przewodniku użyjesz tożsamość zarządzaną dla zasobów platformy Azure do uwierzytelnienia konta Data Lake Storage Gen1. Pamiętaj przyznać odpowiednie uprawnienia w Data Lake Storage Gen1 postępując zgodnie z pliku MSI [w instrukcjach](connector-azure-data-lake-store.md#managed-identity).
   
   ![Określanie konta usługi Data Lake Storage Gen1](./media/load-data-into-azure-data-lake-store/specify-adls.png)
9. W **Wybieranie wyjściowego pliku lub folderu** wpisz **copyfroms3** jako nazwa folderu danych wyjściowych, a następnie wybierz **dalej**: 

    ![Określ folder wyjściowy](./media/load-data-into-azure-data-lake-store/specify-adls-path.png)

10. W **ustawienia** wybierz opcję **dalej**:

    ![Strona Ustawienia](./media/load-data-into-azure-data-lake-store/copy-settings.png)
11. W **Podsumowanie** strony, przejrzyj ustawienia i wybierz **dalej**:

    ![Strona podsumowania](./media/load-data-into-azure-data-lake-store/copy-summary.png)
12. W **strony wdrażania**, wybierz opcję **Monitor** monitorować potok (zadanie):

    ![Strona Wdrażanie](./media/load-data-into-azure-data-lake-store/deployment-page.png)
13. Zwróć uwagę, że karta **Monitor** po lewej stronie jest automatycznie wybrana. **Akcje** kolumna zawiera linki, aby wyświetlić szczegóły uruchamiania działania i ponowne uruchamianie potoku:

    ![Monitorowanie uruchomień potoku](./media/load-data-into-azure-data-lake-store/monitor-pipeline-runs.png)
14. Aby wyświetlić uruchomienia działań, które są skojarzone z uruchomieniem potoku, wybierz pozycję **Wyświetl uruchomienia działań** łącze w **akcje** kolumny. W potoku jest tylko jedno działanie (działanie kopiowania), dlatego na liście jest wyświetlana tylko jedna pozycja. Aby wrócić do widoku uruchomienia potoku, wybierz **potoki** link u góry. Wybierz pozycję **Odśwież**, aby odświeżyć listę. 

    ![Monitorowanie uruchomień działania](./media/load-data-into-azure-data-lake-store/monitor-activity-runs.png)

15. Aby monitorować szczegóły wykonania dla każdego działania kopiowania, wybierz **szczegóły** łącze w obszarze **akcje** w działaniu, w widoku monitorowania. Możesz monitorować szczegółowe informacje, takie jak ilość danych skopiowanych ze źródła do ujścia, przepływność danych, wykonywania kroków za pomocą odpowiedni czas i konfiguracje używane:

    ![Szczegóły uruchamiania działania monitora](./media/load-data-into-azure-data-lake-store/monitor-activity-run-details.png)

16. Upewnij się, że dane zostały skopiowane na konto usługi Data Lake Storage Gen1: 

    ![Weryfikacja danych wyjściowych Data Lake Storage Gen1](./media/load-data-into-azure-data-lake-store/adls-copy-result.png)

## <a name="next-steps"></a>Kolejne kroki

Przejdź z następującym artykułem, aby dowiedzieć się więcej o obsłudze Data Lake Storage Gen1: 

> [!div class="nextstepaction"]
>[Łącznik usługi Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)
