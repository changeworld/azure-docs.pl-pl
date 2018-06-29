---
title: Ładowanie danych do usługi Azure Data Lake Store przy użyciu fabryki danych Azure | Dokumentacja firmy Microsoft
description: Kopiowanie danych do usługi Azure Data Lake Store za pomocą fabryki danych Azure
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
ms.openlocfilehash: 25df96664f6b5fe9da26bee43bc726e05504e5b8
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37058989"
---
# <a name="load-data-into-azure-data-lake-store-by-using-azure-data-factory"></a>Ładowanie danych do usługi Azure Data Lake Store przy użyciu fabryki danych Azure

[Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md) jest całego przedsiębiorstwa hiperskali repozytorium na potrzeby obciążeń analizy dużych danych big data. Usługa Azure Data Lake umożliwia przechwytywanie danych dowolnego rozmiar, typ i szybkość wprowadzanie. Dane są przechwytywane w jednym miejscu na potrzeby analiz operacyjnych i poznawczych.

Fabryka danych Azure to usługa integracji pełni zarządzanych danych oparte na chmurze. Usługa służy do wypełniania lake z danych z istniejącego systemu i zaoszczędzić czas podczas tworzenia rozwiązań analizy.

Fabryka danych Azure oferuje następujące korzyści dla ładowania danych do usługi Azure Data Lake Store:

* **Można skonfigurować**: bez skryptu wymagane intuicyjne kreatora krok 5.
* **Obsługa magazynu danych sformatowanego**: wbudowaną obsługę bogaty zestaw lokalnymi i magazyny danych oparte na chmurze. Aby uzyskać szczegółową listę, zobacz tabelę [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
* **Bezpieczne i zgodne**: dane są przesyłane za pośrednictwem protokołu HTTPS lub ExpressRoute. Obecność usługi global service zapewnia, że dane nigdy nie przekracza granic geograficznych.
* **Wysoka wydajność**: maksymalnie 1-GB/s danych ładowania szybkości do usługi Azure Data Lake Store. Aby uzyskać więcej informacji, zobacz [wydajności działania kopiowania](copy-activity-performance.md).

W tym artykule przedstawiono sposób użycia narzędzia kopii fabryki danych do _ładowanie danych z usługi Amazon S3 do usługi Azure Data Lake Store_. Można wykonać podobne kroki można skopiować danych z innych typów magazynów danych.

> [!NOTE]
> Aby uzyskać więcej informacji, zobacz [kopiowanie danych do i z usługi Azure Data Lake Store przy użyciu fabryki danych Azure](connector-azure-data-lake-store.md).
## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure: Jeśli nie masz subskrypcji platformy Azure, Utwórz [bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.
* Azure Data Lake Store: Jeśli nie masz konta usługi Data Lake Store, zobacz instrukcje w [Utwórz konto usługi Data Lake Store](../data-lake-store/data-lake-store-get-started-portal.md#create-an-azure-data-lake-store-account).
* Amazon S3: W tym artykule pokazano, jak skopiować dane z usługi Amazon S3. Wykonując kroki podobne, można użyć innych magazynów danych.

## <a name="create-a-data-factory"></a>Tworzenie fabryki danych

1. W menu po lewej stronie wybierz **nowy** > **dane i analiza** > **fabryki danych**:
   
   ![Tworzenie nowej fabryki danych](./media/load-data-into-azure-data-lake-store/new-azure-data-factory-menu.png)
2. W **nowa fabryka danych** Podaj wartości dla pól, które są wyświetlane na poniższej ilustracji: 
      
   ![Strona Nowa fabryka danych](./media/load-data-into-azure-data-lake-store//new-azure-data-factory.png)
 
    * **Nazwa**: Wprowadź globalnie unikatowej nazwy dla fabryką danych Azure. Jeśli zostanie wyświetlony błąd "Nazwa fabryki danych \"LoadADLSDemo\" jest niedostępny," Wprowadź inną nazwę dla fabryki danych. Na przykład można użyć nazwy  _**twojanazwa**_**ADFTutorialDataFactory**. Spróbuj ponownie utworzyć fabryki danych. Artykuł [Data Factory naming rules (Zasady nazewnictwa fabryki danych)](naming-rules.md) zawiera zasady nazewnictwa artefaktów usługi Data Factory.
    * **Subskrypcja**: Wybierz subskrypcję platformy Azure, w którym można utworzyć fabryki danych. 
    * **Grupa zasobów**: Wybierz istniejącą grupę zasobów z listy rozwijanej lub wybierz **Utwórz nowy** opcję i wprowadź nazwę grupy zasobów. Informacje na temat grup zasobów znajdują się w artykule [Using resource groups to manage your Azure resources](../azure-resource-manager/resource-group-overview.md) (Używanie grup zasobów do zarządzania zasobami platformy Azure).  
    * **Wersja**: Wybierz **V2**.
    * **Lokalizacja**: Wybierz lokalizację dla fabryki danych. Na liście rozwijanej są wyświetlane tylko obsługiwane lokalizacje. Magazyny danych, które są używane przez fabrykę danych może być w innych lokalizacjach i regionach. Te magazyny danych obejmują usługi Azure Data Lake Store, usługi Azure Storage, baza danych SQL Azure i tak dalej.

3. Wybierz pozycję **Utwórz**.
4. Po zakończeniu tworzenia go z fabryką danych. Zostanie wyświetlony **fabryki danych** strony głównej, jak pokazano na poniższej ilustracji: 
   
   ![Strona główna fabryki danych](./media/load-data-into-azure-data-lake-store/data-factory-home-page.png)

   Wybierz **autora & Monitor** Kafelek, aby uruchomić aplikację integracji danych w osobnej karcie.

## <a name="load-data-into-azure-data-lake-store"></a>Ładowanie danych do usługi Azure Data Lake Store

1. W **wprowadzenie** wybierz pozycję **kopiowania danych** Kafelek, aby uruchomić narzędzie kopii danych: 

   ![Kafelek narzędzia do kopiowania danych](./media/load-data-into-azure-data-lake-store/copy-data-tool-tile.png)
2. W **właściwości** określ **CopyFromAmazonS3ToADLS** dla **Nazwa zadania** pola i wybierz pozycję **dalej**:

    ![Strona właściwości](./media/load-data-into-azure-data-lake-store/copy-data-tool-properties-page.png)
3. W **magazynu danych źródła** kliknij przycisk **+ Utwórz nowe połączenie**:

    ![Strona Źródłowy magazyn danych](./media/load-data-into-azure-data-lake-store/source-data-store-page.png)
    
    Wybierz **Amazon S3**i wybierz **Kontynuuj**
    
    ![Źródło danych magazynu s3 strony](./media/load-data-into-azure-data-lake-store/source-data-store-page-s3.png)
    
4. W **połączenia Określ Amazon S3** wykonaj następujące czynności: 
   1. Określ **identyfikator klucza dostępu** wartość.
   2. Określ **klucz tajny klucz dostępu** wartość.
   3. Wybierz pozycję **Finish** (Zakończ).
   
   ![Określ konto Amazon S3](./media/load-data-into-azure-data-lake-store/specify-amazon-s3-account.png)
   
   4. Zostanie wyświetlone nowe połączenie. Wybierz opcję **Dalej**.
   
   ![Określ konto Amazon S3](./media/load-data-into-azure-data-lake-store/specify-amazon-s3-account-created.png)
   
5. W **wybierz wejściowy plik lub folder** strony, przejdź do folderu i pliku, który chcesz skopiować. Wybierz folderów i plików, wybierz pozycję **wybierz**, a następnie wybierz **dalej**:

    ![Wybieranie pliku lub folderu wejściowego](./media/load-data-into-azure-data-lake-store/choose-input-folder.png)

6. Wybierz zachowanie kopiowania, wybierając **kopiowania plików rekursywnie** i **binarne kopiowania** (kopiowanie plików jako — jest) opcje. Wybierz **dalej**:

    ![Określ folder wyjściowy](./media/load-data-into-azure-data-lake-store/specify-binary-copy.png)
    
7. W **magazyn danych docelowy** kliknij przycisk **+ Utwórz nowe połączenie**, a następnie wybierz **Azure Data Lake Store**i wybierz **Kontynuuj**:

    ![Strona Docelowy magazyn danych](./media/load-data-into-azure-data-lake-store/destination-data-storage-page.png)

8. W **połączenia Określ Data Lake Store** wykonaj następujące czynności: 

   1. Wybierz usługi Data Lake Store dla **nazwa konta usługi Data Lake Store**.
   2. Określ **dzierżawy**i wybierz przycisk Zakończ.
   3. Wybierz opcję **Dalej**.
   
   > [!IMPORTANT]
   > W tym przewodniku, należy użyć _tożsamość usługi zarządzanej_ do uwierzytelniania usługi Data Lake Store. Pamiętaj umożliwić nazwy głównej usługi odpowiednie uprawnienia w usłudze Azure Data Lake Store, wykonując następujące [tych instrukcji](connector-azure-data-lake-store.md#using-managed-service-identity-authentication).
   
   ![Określ konto usługi Azure Data Lake Store](./media/load-data-into-azure-data-lake-store/specify-adls.png)
9. W **wybierz dane wyjściowe pliku lub folderu** wprowadź **copyfroms3** jako nazwa folderu wyjściowego, a następnie wybierz **dalej**: 

    ![Określ folder wyjściowy](./media/load-data-into-azure-data-lake-store/specify-adls-path.png)

10. W **ustawienia** wybierz pozycję **dalej**:

    ![Strona Ustawienia](./media/load-data-into-azure-data-lake-store/copy-settings.png)
11. W **Podsumowanie** strony, przejrzyj ustawienia, a następnie wybierz **dalej**:

    ![Strona podsumowania](./media/load-data-into-azure-data-lake-store/copy-summary.png)
12. W **stronę wdrożenia**, wybierz pozycję **monitora** do monitorowania procesu (zadania):

    ![Strona Wdrażanie](./media/load-data-into-azure-data-lake-store/deployment-page.png)
13. Zwróć uwagę, że karta **Monitor** po lewej stronie jest automatycznie wybrana. **Akcje** kolumna zawiera łącza, aby wyświetlić szczegóły uruchomienia działania i ponownie uruchomić potoku:

    ![Monitorowanie uruchomień potoku](./media/load-data-into-azure-data-lake-store/monitor-pipeline-runs.png)
14. Zaznacz, aby wyświetlić uruchomień działania, które są skojarzone z potoku Uruchom **odbywa się działanie widoku** łącze w **akcje** kolumny. W potoku jest tylko jedno działanie (działanie kopiowania), dlatego na liście jest wyświetlana tylko jedna pozycja. Wybierz, aby wrócić do potoku uruchamia widok **potoki** łącze u góry. Wybierz pozycję **Odśwież**, aby odświeżyć listę. 

    ![Monitorowanie uruchomień działania](./media/load-data-into-azure-data-lake-store/monitor-activity-runs.png)

15. Aby monitorować szczegóły wykonywania dla każdego działania kopiowania, wybierz **szczegóły** łącze w obszarze **akcje** w działaniu widok monitorowania. Możliwość monitorowania szczegółów, takich jak ilość danych skopiowana ze źródła do zbiornika, przepływność danych, wykonywanie czynności z odpowiedni czas trwania używane konfiguracje:

    ![Monitorowanie aktywności szczegóły uruchomienia](./media/load-data-into-azure-data-lake-store/monitor-activity-run-details.png)

16. Sprawdź, czy dane są kopiowane do usługi Azure Data Lake Store: 

    ![Sprawdź dane wyjściowe usługi Data Lake Store](./media/load-data-into-azure-data-lake-store/adls-copy-result.png)

## <a name="next-steps"></a>Kolejne kroki

Przejdź do następujących artykuł, aby dowiedzieć się więcej na temat obsługi usługi Azure Data Lake Store: 

> [!div class="nextstepaction"]
>[Łącznik usługi Azure Data Lake Store](connector-azure-data-lake-store.md)
