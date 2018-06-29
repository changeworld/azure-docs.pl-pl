---
title: Ładowanie danych do usługi Azure Data Lake magazynu Gen2 (wersja zapoznawcza) z fabryką danych Azure
description: Umożliwia kopiowanie danych do usługi Azure Data Lake magazynu Gen2 fabryki danych Azure (wersja zapoznawcza)
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: jingwang
ms.openlocfilehash: 961c8dea4dbb6b6600d10b75e84a9a84c34c329b
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37036661"
---
# <a name="load-data-into-azure-data-lake-storage-gen2-preview-with-azure-data-factory"></a>Ładowanie danych do usługi Azure Data Lake magazynu Gen2 podglądu z fabryką danych Azure

[Azure Data Lake magazynu Gen2 w wersji zapoznawczej](../storage/data-lake-storage/introduction.md) dodaje protokołu o funkcje systemu plików hierarchicznej przestrzeni nazw i zabezpieczeń do magazynu obiektów Blob Azure, dzięki czemu można łatwo nawiązać analytics platform warstwy magazynu trwałego. W danych Lake magazynu Gen2 (wersja zapoznawcza), wszystkie właściwości obiektu magazynu pozostają podczas dodawania korzyści wynikające z interfejsem systemu plików.

Fabryka danych Azure to usługa integracji pełni zarządzanych danych oparte na chmurze. Korzystasz z usługi do wypełnienia lake danymi z bogaty zestaw lokalnymi i danych opartych na chmurze przechowuje i zaoszczędzić czas podczas tworzenia rozwiązań analizy. Aby uzyskać szczegółową listę obsługiwanych łączników, zobacz tabelę [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).

Fabryki danych Azure oferuje skalowanie w poziomie, rozwiązania przepływu danych zarządzanych w przeciwieństwie do narzędzia AzCopy, danych wiersza polecenia transferu narzędzie. Ze względu na skalowalność architektury ADF jego pozyskiwania danych wysokiej przepływności. Aby uzyskać więcej informacji, zobacz [wydajności działania kopiowania](copy-activity-performance.md).

W tym artykule przedstawiono sposób użycia narzędzia kopii fabryki danych do ładowania danych z _usługi Amazon S3 usług sieci Web_ do _Azure Data Lake magazynu Gen2_. Można wykonać podobne kroki można skopiować danych z innych typów magazynów danych.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure: Jeśli nie masz subskrypcji platformy Azure, Utwórz [bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.
* Konto magazynu platformy Azure z Gen2 magazynu Lake danych włączone: Jeśli nie masz konta magazynu, kliknij przycisk [tutaj](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM) go utworzyć.
* Konta usług AWS z przedział S3, który zawiera dane: w tym artykule pokazano, jak skopiować dane z usługi Amazon S3. Wykonując kroki podobne, można użyć innych magazynów danych.

## <a name="create-a-data-factory"></a>Tworzenie fabryki danych

1. W menu po lewej stronie wybierz **nowy** > **dane i analiza** > **fabryki danych**:
   
   ![Tworzenie nowej fabryki danych](./media/load-azure-data-lake-storage-gen2/new-azure-data-factory-menu.png)
2. W **nowa fabryka danych** Podaj wartości dla pól, które są wyświetlane na poniższej ilustracji: 
      
   ![Strona Nowa fabryka danych](./media/load-azure-data-lake-storage-gen2//new-azure-data-factory.png)
 
    * **Nazwa**: Wprowadź globalnie unikatowej nazwy dla fabryką danych Azure. Jeśli zostanie wyświetlony błąd "Nazwa fabryki danych \"LoadADLSDemo\" jest niedostępny," Wprowadź inną nazwę dla fabryki danych. Na przykład można użyć nazwy  _**twojanazwa**_**ADFTutorialDataFactory**. Spróbuj ponownie utworzyć fabryki danych. Artykuł [Data Factory naming rules (Zasady nazewnictwa fabryki danych)](naming-rules.md) zawiera zasady nazewnictwa artefaktów usługi Data Factory.
    * **Subskrypcja**: Wybierz subskrypcję platformy Azure, w którym można utworzyć fabryki danych. 
    * **Grupa zasobów**: Wybierz istniejącą grupę zasobów z listy rozwijanej lub wybierz **Utwórz nowy** opcję i wprowadź nazwę grupy zasobów. Informacje na temat grup zasobów znajdują się w artykule [Using resource groups to manage your Azure resources](../azure-resource-manager/resource-group-overview.md) (Używanie grup zasobów do zarządzania zasobami platformy Azure).  
    * **Wersja**: Wybierz **V2(Preview)**.
    * **Lokalizacja**: Wybierz lokalizację dla fabryki danych. Na liście rozwijanej są wyświetlane tylko obsługiwane lokalizacje. Magazyny danych, które są używane przez fabrykę danych może być w innych lokalizacjach i regionach. 

3. Wybierz pozycję **Utwórz**.
4. Po zakończeniu tworzenia go z fabryką danych. Zostanie wyświetlony **fabryki danych** strony głównej, jak pokazano na poniższej ilustracji: 
   
   ![Strona główna fabryki danych](./media/load-azure-data-lake-storage-gen2/data-factory-home-page.png)

   Wybierz **autora & Monitor** Kafelek, aby uruchomić aplikację integracji danych w osobnej karcie.

## <a name="load-data-into-azure-data-lake-storage-gen2"></a>Ładowanie danych do usługi Azure Data Lake magazynu Gen2

1. W **wprowadzenie** wybierz pozycję **kopiowania danych** Kafelek, aby uruchomić narzędzie kopii danych: 

   ![Kafelek narzędzia do kopiowania danych](./media/load-azure-data-lake-storage-gen2/copy-data-tool-tile.png)
2. W **właściwości** określ **CopyFromAmazonS3ToADLS** dla **Nazwa zadania** pola i wybierz pozycję **dalej**:

    ![Strona właściwości](./media/load-azure-data-lake-storage-gen2/copy-data-tool-properties-page.png)
3. W **magazynu danych źródła** kliknij przycisk **+ Utwórz nowe połączenie**:

    ![Strona Źródłowy magazyn danych](./media/load-azure-data-lake-storage-gen2/source-data-store-page.png)
    
    Wybierz **Amazon S3** z galerii łączników, a następnie wybierz **Kontynuuj**
    
    ![Źródło danych magazynu s3 strony](./media/load-azure-data-lake-storage-gen2/source-data-store-page-s3.png)
    
4. W **połączenia Określ Amazon S3** wykonaj następujące czynności:
   1. Określ **identyfikator klucza dostępu** wartość.
   2. Określ **klucz tajny klucz dostępu** wartość.
   3. Kliknij przycisk **połączenie testowe** sprawdzania poprawności ustawień, następnie wybierz **Zakończ**.
   
   ![Określ konto Amazon S3](./media/load-azure-data-lake-storage-gen2/specify-amazon-s3-account.png)
   
   4. Zostanie wyświetlone pobiera tworzone jest nowe połączenie. Wybierz opcję **Dalej**.
   
5. W **wybierz wejściowy plik lub folder** strony, przejdź do folderu i pliku, który chcesz skopiować. Wybierz folderów i plików, wybierz pozycję **wybierz**:

    ![Wybieranie pliku lub folderu wejściowego](./media/load-azure-data-lake-storage-gen2/choose-input-folder.png)

6. Określ zachowanie kopii sprawdzając **kopiowania plików rekursywnie** i **binarne kopiowania** opcje. Wybierz **dalej**:

    ![Określ folder wyjściowy](./media/load-azure-data-lake-storage-gen2/specify-binary-copy.png)
    
7. W **magazyn danych docelowy** kliknij przycisk **+ Utwórz nowe połączenie**, a następnie wybierz **Gen2 magazynu systemu Azure Data Lake (wersja zapoznawcza)** i wybierz **Kontynuuj** :

    ![Strona Docelowy magazyn danych](./media/load-azure-data-lake-storage-gen2/destination-data-storage-page.png)

8. W **połączenia Określ usługi Azure Data Lake Storage** wykonaj następujące czynności:

   1. Wybierz z magazynu Gen2 Lake danych listy rozwijanej konto stanie z "Nazwa konta magazynu".
   2. Wybierz opcję **Dalej**.
   
   ![Określ konto usługi Azure Data Lake Store](./media/load-azure-data-lake-storage-gen2/specify-adls.png)

9. W **wybierz dane wyjściowe pliku lub folderu** wprowadź **copyfroms3** jako nazwa folderu wyjściowego, a następnie wybierz **dalej**: 

    ![Określ folder wyjściowy](./media/load-azure-data-lake-storage-gen2/specify-adls-path.png)

10. W **ustawienia** wybierz pozycję **dalej** można użyć wartości domyślnych:

    ![Strona Ustawienia](./media/load-azure-data-lake-storage-gen2/copy-settings.png)
11. W **Podsumowanie** strony, przejrzyj ustawienia, a następnie wybierz **dalej**:

    ![Strona podsumowania](./media/load-azure-data-lake-storage-gen2/copy-summary.png)
12. W **stronę wdrożenia**, wybierz pozycję **monitora** do monitorowania potoku:

    ![Strona Wdrażanie](./media/load-azure-data-lake-storage-gen2/deployment-page.png)
13. Zwróć uwagę, że karta **Monitor** po lewej stronie jest automatycznie wybrana. **Akcje** kolumna zawiera łącza, aby wyświetlić szczegóły uruchomienia działania i ponownie uruchomić potoku:

    ![Monitorowanie uruchomień potoku](./media/load-azure-data-lake-storage-gen2/monitor-pipeline-runs.png)

14. Zaznacz, aby wyświetlić uruchomień działania, które są skojarzone z potoku Uruchom **odbywa się działanie widoku** łącze w **akcje** kolumny. W potoku jest tylko jedno działanie (działanie kopiowania), dlatego na liście jest wyświetlana tylko jedna pozycja. Wybierz, aby wrócić do potoku uruchamia widok **potoki** łącze u góry. Wybierz pozycję **Odśwież**, aby odświeżyć listę. 

    ![Monitorowanie uruchomień działania](./media/load-azure-data-lake-storage-gen2/monitor-activity-runs.png)

15. Aby monitorować szczegóły wykonywania dla każdego działania kopiowania, wybierz **szczegóły** łącza (obraz okularów) w obszarze **akcje** w działaniu widok monitorowania. Możliwość monitorowania szczegółów, takich jak ilość danych skopiowana ze źródła do zbiornika, przepływność danych, wykonywanie czynności z odpowiedni czas trwania używane konfiguracje:

    ![Monitorowanie aktywności szczegóły uruchomienia](./media/load-azure-data-lake-storage-gen2/monitor-activity-run-details.png)

16. Upewnij się, że dane są kopiowane do konta Data Lake magazynu Gen2.

## <a name="best-practice"></a>Najlepsze praktyki

Podczas kopiowania dużych woluminów danych z magazynu danych opartych na plikach, są zalecane do:

- Dzielenia plików na 10TB do 20TB użycia.
- Nie wyzwalają zbyt wiele współbieżnych kopii działa w celu uniknięcia ograniczania z źródło i ujście magazynów danych. Możesz rozpocząć od jednej kopii Uruchom i monitor przepływność, a następnie stopniowego dodawania jednego zgodnie z potrzebami.

## <a name="next-steps"></a>Kolejne kroki

* [Omówienie działania kopiowania](copy-activity-overview.md)
* [Łącznik usługi Azure Data Lake magazynu Gen2](connector-azure-data-lake-storage.md)