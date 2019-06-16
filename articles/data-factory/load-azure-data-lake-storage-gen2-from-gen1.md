---
title: Kopiowanie danych z usługi Azure Data Lake Storage Gen1 do Gen2 za pomocą usługi Azure Data Factory
description: Kopiowanie danych z usługi Azure Data Lake Storage Gen1 do Gen2 za pomocą usługi Azure Data Factory
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: jingwang
ms.openlocfilehash: d13dad6e87bd6c821b497138ad75d7ae2b9a052d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67068979"
---
# <a name="copy-data-from-azure-data-lake-storage-gen1-to-gen2-with-azure-data-factory"></a>Kopiowanie danych z usługi Azure Data Lake Storage Gen1 do Gen2 za pomocą usługi Azure Data Factory

Azure Data Lake magazynu Gen2 to zbiór funkcji przeznaczonych do analizy danych big data, która jest wbudowana w [usługi Azure Blob storage](../storage/blobs/storage-blobs-introduction.md). Służy on do połączenia interfejsem z danych za pomocą obu paradygmatów magazynu plików, jak systemu i obiekt.

Jeśli obecnie używasz usługi Azure Data Lake Storage Gen1, będziesz w stanie ocenić usługi Azure Data Lake Storage Gen2 przez skopiowanie danych z programu Data Lake Storage Gen1 Gen2 przy użyciu usługi Azure Data Factory.

Usługa Azure Data Factory to usługa integracji danych w pełni zarządzane w chmurze. Korzystać z niej, aby wypełnić usługi Data lake z danymi z bogatego zestawu w środowisku lokalnym i magazyny danych opartych na chmurze i zaoszczędzić czas podczas kompilowania rozwiązań do analizy. Aby uzyskać listę obsługiwanych łączników, zobacz tabelę [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).

Usługa Azure Data Factory oferuje rozwiązanie do przenoszenia danych skalowalne, zarządzane. Ze względu na architekturę skalowalnego w poziomie usługi Data Factory jego pozyskiwać dane przy wysokiej przepływności. Aby uzyskać więcej informacji, zobacz [wydajności działania kopiowania](copy-activity-performance.md).

W tym artykule pokazano, jak skopiować dane z usługi Azure Data Lake Storage Gen1 do usługi Azure Data Lake Storage Gen2 za pomocą narzędzia do usługi Data Factory kopiowania danych. Możesz wykonać podobne kroki w celu skopiowania danych z innych typów magazynów danych.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).
* Konto usługi Azure Data Lake Storage Gen1 z danymi.
* Konto usługi Azure Storage za pomocą magazynu Gen2 jeziora danych, włączone. Jeśli nie masz konta magazynu, [Tworzenie konta usługi](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM).

## <a name="create-a-data-factory"></a>Tworzenie fabryki danych

1. W menu po lewej stronie wybierz **Utwórz zasób** > **dane + analiza** > **usługi Data Factory**.
   
   ![Wybór usługi Data Factory w okienku](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. Na **nowa fabryka danych** Podaj wartości dla pól, które są wyświetlane na poniższej ilustracji: 
      
   ![Strona Nowa fabryka danych](./media/load-azure-data-lake-storage-gen2-from-gen1/new-azure-data-factory.png)
 
    * **Nazwa**: Wprowadź unikatową w skali globalnej nazwę fabryki danych platformy Azure. Jeśli zostanie wyświetlony błąd "Nazwa fabryki danych \"LoadADLSDemo\" jest niedostępny," Wprowadź inną nazwę dla fabryki danych. Na przykład użyj nazwy _**twojanazwa**_ **ADFTutorialDataFactory**. Utwórz ponownie fabrykę danych. Artykuł [Data Factory naming rules (Zasady nazewnictwa fabryki danych)](naming-rules.md) zawiera zasady nazewnictwa artefaktów usługi Data Factory.
    * **Subskrypcja**: Wybieranie subskrypcji platformy Azure, w której chcesz utworzyć fabrykę danych. 
    * **Grupa zasobów**: Wybierz istniejącą grupę zasobów z listy rozwijanej. Możesz również wybrać **Utwórz nową** opcji, a następnie wprowadź nazwę grupy zasobów. Informacje na temat grup zasobów znajdują się w artykule [Using resource groups to manage your Azure resources (Używanie grup zasobów do zarządzania zasobami platformy Azure)](../azure-resource-manager/resource-group-overview.md). 
    * **Wersja**: Wybierz **V2**.
    * **Lokalizacja**: Wybierz lokalizację fabryki danych. Na liście rozwijanej są wyświetlane tylko obsługiwane lokalizacje. Magazyny danych, które są używane przez fabrykę danych mogą być w innych lokalizacjach i regionach. 

3. Wybierz pozycję **Utwórz**.
4. Po zakończeniu tworzenia przejść z fabryką danych. Zostanie wyświetlony **usługi Data Factory** strony głównej, jak pokazano na poniższej ilustracji: 
   
   ![Strona główna fabryki danych](./media/load-azure-data-lake-storage-gen2-from-gen1/data-factory-home-page.png)

5. Wybierz **tworzenie i monitorowanie** Kafelek, aby uruchomić aplikację integracji danych w osobnej karcie.

## <a name="load-data-into-azure-data-lake-storage-gen2"></a>Ładowanie danych do usługi Azure Data Lake Storage Gen2

1. Na **wprowadzenie** wybierz opcję **kopiowania danych** Kafelek, aby uruchomić narzędzie do kopiowania danych. 

   ![Skopiuj Kafelek narzędzia danych](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-data-tool-tile.png)
2. Na **właściwości** określ **CopyFromADLSGen1ToGen2** dla **Nazwa zadania** pola. Wybierz opcję **Dalej**.

    ![Strona właściwości](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-data-tool-properties-page.png)
3. Na **magazynu danych źródłowych** wybierz opcję **+ Utwórz nowe połączenie**.

    ![Strona Źródłowy magazyn danych](./media/load-azure-data-lake-storage-gen2-from-gen1/source-data-store-page.png)
    
4. Wybierz **usługi Azure Data Lake Storage Gen1** z galerii łączników, a następnie wybierz pozycję **Kontynuuj**.
    
    ![Strona usługi Azure Data Lake Storage Gen1 magazynu danych źródłowych](./media/load-azure-data-lake-storage-gen2-from-gen1/source-data-store-page-adls-gen1.png)
    
5. Na **połączenia Określ Azure Data Lake Storage Gen1** strony, wykonaj następujące kroki:

   a. Wybierz usługi Data Lake magazynu Gen1 dla nazwy konta i określić lub zweryfikować **dzierżawy**.
  
   b. Wybierz **Testuj połączenie** sprawdzania poprawności ustawień. Następnie wybierz pozycję **Zakończ**.
  
   c. Zobaczysz, że utworzono nowe połączenie. Wybierz opcję **Dalej**.
   
   > [!IMPORTANT]
   > W tym przewodniku używasz tożsamość zarządzaną dla zasobów platformy Azure do uwierzytelniania usługi Azure Data Lake magazynu Gen1. Aby przyznać tożsamości zarządzanej odpowiednie uprawnienia w usłudze Azure Data Lake magazynu Gen1, wykonaj [w instrukcjach](connector-azure-data-lake-store.md#managed-identity).
   
   ![Określ konto usługi Azure Data Lake Storage Gen1](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen1-account.png)
      
6. Na **Wybieranie pliku lub folderu wejściowego** strony, przejdź do folderu i pliku, który chcesz skopiować. Wybierz folder lub plik, a następnie wybierz pozycję **wybierz**.

    ![Wybieranie pliku lub folderu wejściowego](./media/load-azure-data-lake-storage-gen2-from-gen1/choose-input-folder.png)

7. Określ zachowanie kopiowania, wybierając **kopiowania plików rekursywnie** i **kopia binarna** opcje. Wybierz opcję **Dalej**.

    ![Określ folder wyjściowy](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-binary-copy.png)
    
8. Na **docelowego magazynu danych** wybierz opcję **+ Utwórz nowe połączenie** > **usługi Azure Data Lake Storage Gen2**  >   **Kontynuuj**.

    ![Strona Docelowy magazyn danych](./media/load-azure-data-lake-storage-gen2-from-gen1/destination-data-storage-page.png)

9. Na **połączenia Określ usługi Azure Data Lake magazynu Gen2** strony, wykonaj następujące kroki:

   a. Wybierz konto usługi Data Lake Storage Gen2 możliwością z **nazwa konta magazynu** listy rozwijanej.
   
   b. Wybierz **Zakończ** do utworzenia połączenia. Następnie wybierz przycisk **Dalej**.
   
   ![Określ konto usługi Azure Data Lake Storage Gen2](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen2-account.png)

10. Na **Wybieranie wyjściowego pliku lub folderu** wpisz **copyfromadlsgen1** jako nazwa folderu danych wyjściowych, a następnie wybierz **dalej**. Data Factory tworzy odpowiedni system plików usługi Azure Data Lake Storage Gen2 i podfoldery podczas kopiowania, jeśli nie istnieją.

    ![Określ folder wyjściowy](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen2-path.png)

11. Na **ustawienia** wybierz opcję **dalej** można użyć ustawień domyślnych.

12. Na **Podsumowanie** strony, przejrzyj ustawienia i wybierz **dalej**.

    ![Strona podsumowania](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-summary.png)
13. Na **strony wdrażania**, wybierz opcję **Monitor** Aby monitorować potok.

    ![Strona Wdrażanie](./media/load-azure-data-lake-storage-gen2-from-gen1/deployment-page.png)
14. Zwróć uwagę, że karta **Monitor** po lewej stronie jest automatycznie wybrana. Kolumna **Akcje** zawiera linki służące do wyświetlania szczegółów działań i ponownego uruchamiania potoku.

    ![Monitorowanie uruchomień potoku](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-pipeline-runs.png)

15. Aby wyświetlić uruchomienia działań, które są skojarzone z uruchomieniem potoku, wybierz pozycję **Wyświetl uruchomienia działań** łącze w **akcje** kolumny. W potoku jest tylko jedno działanie (działanie kopiowania), dlatego na liście jest wyświetlana tylko jedna pozycja. Aby wrócić do widoku uruchomienia potoku, wybierz **potoki** link u góry. Wybierz pozycję **Odśwież**, aby odświeżyć listę. 

    ![Monitorowanie uruchomień działania](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-activity-runs.png)

16. Aby monitorować szczegóły wykonania dla każdego działania kopiowania, wybierz **szczegóły** link (obraz okularów) w obszarze **akcje** w działaniu, w widoku monitorowania. Można monitorować szczegółowe informacje, takie jak ilość danych skopiowanych ze źródła do ujścia, przepływność danych, wykonywania kroków za pomocą odpowiedni czas i konfiguracje używane.

    ![Szczegóły uruchamiania działania monitora](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-activity-run-details.png)

17. Upewnij się, że dane zostały skopiowane do swojego konta usługi Azure Data Lake Storage Gen2.

## <a name="best-practices"></a>Najlepsze praktyki

Aby ocenić, uaktualniania z usługi Azure Data Lake Storage Gen1 do usługi Azure Data Lake Storage Gen2 ogólnie rzecz biorąc, zobacz [uaktualniania rozwiązań do analizy danych big data z usługi Azure Data Lake Storage Gen1 do usługi Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-upgrade.md). Poniższe sekcje wprowadzenie najlepsze rozwiązania dotyczące używania usługi Data Factory w celu uaktualnienia dane z Data Lake Storage Gen1 Data Lake Storage Gen2.

### <a name="data-partition-for-historical-data-copy"></a>Partycja danych związanym z kopiowaniem danych historycznych

- Jeśli Twoje całkowity rozmiar danych w Data Lake Storage Gen1 jest mniejszy niż 30 TB, a liczba plików jest mniejsza niż 1 mln, działanie kopiowania pojedynczego można skopiować wszystkie dane.
- Jeśli masz większej ilości danych, aby skopiować lub potrzebna jest elastyczność zarządzania migracją danych w plikach wsadowych i każdy z nich ukończone w określonym przedziale czasu, podzielenia danych. Partycjonowania zmniejsza ryzyko związane z dowolnym nieoczekiwany problem.

Sprawdź rozwiązania end-to-end i przetestować przepływność kopiowania w danym środowisku, należy użyć weryfikacji koncepcji. Główne kroki weryfikacji koncepcji: 

1. Utwórz jeden potok usługi Data Factory z działaniem kopiowania pojedynczej do skopiowania do Data Lake magazynu Gen2 uzyskać punkt odniesienia wydajności kopiowania Data Lake Storage Gen1 kilka TB danych. Rozpoczynać [jednostek integracji danych (DIUs)](copy-activity-performance.md#data-integration-units) jako 128. 
2. Na podstawie kopii przepływności pobranego w kroku 1, Oblicz szacowany czas, które są wymagane do migracji danych. 
3. (Opcjonalnie) Utwórz tabelę kontroli i zdefiniuj filtr pliku do partycjonowania plików przeznaczonych do migracji. Jest to sposób na partycji pliki: 

    - Rozkład według nazwy folderu lub nazwa folderu z filtr z symbolami wieloznacznymi. Firma Microsoft zaleca tej metody.
    - Partycja w pliku przez Data ostatniej modyfikacji.

### <a name="network-bandwidth-and-storage-io"></a>Przepustowości i magazynu we/wy sieci 

Można kontrolować współbieżności zadań kopiowania usługi Data Factory, które dane z Data Lake Storage Gen1 do odczytu i zapisu danych Data Lake Storage Gen2. W ten sposób można zarządzać użyciem na tej operacji We/Wy, aby uniknąć wpływu na zwykłą działalnością pracę na Data Lake Storage Gen1 podczas migracji magazynu.

### <a name="permissions"></a>Uprawnienia 

W usłudze Data Factory [łącznika Data Lake Storage Gen1](connector-azure-data-lake-store.md) obsługuje usługi tożsamości podmiotu zabezpieczeń i zarządzanie nimi dla uwierzytelnień zasobów platformy Azure. [Łącznika Data Lake Storage Gen2](connector-azure-data-lake-storage.md) obsługuje konta klucz jednostki usługi i tożsamości zarządzanej dla usługi Azure resource uwierzytelnień. Aby fabryka danych mogła przejść i skopiuj wszystkie pliki lub listy kontroli dostępu (ACL można przyznać wysokiego wystarczających uprawnień dla konta, które zapewniają dostęp do odczytu, lub zapisać wszystkie pliki i ustawić list ACL, jeśli zdecydujesz się). Należy przyznać jej rolę administratora lub właściciela w okresie migracji. 

### <a name="preserve-acls-from-data-lake-storage-gen1"></a>Zachowaj listy ACL z Gen1 Lake magazynu danych

Jeśli chcesz replikować list ACL wraz z plikami danych podczas uaktualniania z programu Data Lake Storage Gen1 Data Lake Storage Gen2, zobacz [zachować listy ACL z Data Lake Storage Gen1](connector-azure-data-lake-storage.md#preserve-acls-from-data-lake-storage-gen1). 

### <a name="incremental-copy"></a>Przyrostowa kopia 

Można załadować tylko nowe lub zaktualizowane pliki z Data Lake Storage Gen1, można użyć kilku metod:

- Załaduj nowe lub zaktualizowane pliki, czas podzielonym na partycje katalogu ani nazwie pliku. Przykładem jest/2019 r/05/13 / *.
- Ładowanie nowych lub zaktualizowanych plików, Data ostatniej modyfikacji.
- Identyfikowanie nowych lub zaktualizowanych plików przez wszystkie narzędzia innych firm lub rozwiązania. Następnie przekaż nazwę pliku lub folderu do potoku usługi fabryka danych za pośrednictwem parametru lub tabeli lub pliku. 

Odpowiednie częstotliwość celu przyrostowego ładowania zależy od tego, całkowita liczba plików w usłudze Azure Data Lake magazynu Gen1 i liczby nowe lub zaktualizowane pliki, które mają zostać załadowane, każdym razem. 

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Omówienie działania kopiowania](copy-activity-overview.md)
> [łącznika usługi Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)
> [łącznika usługi Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)