---
title: Kopiowanie danych z usługi Azure Data Factory do Usługi Azure Data Explorer
description: W tym artykule dowiesz się, jak pozyskiwania (ładowania) danych do usługi Azure Data Explorer przy użyciu narzędzia do kopiowania usługi Azure Data Factory.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: 8e17a004ff866f3915000fb72b6770757062cf83
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422910"
---
# <a name="copy-data-to-azure-data-explorer-by-using-azure-data-factory"></a>Kopiowanie danych do Eksploratora danych platformy Azure przy użyciu usługi Azure Data Factory 

Usługa Azure Data Explorer to szybka, w pełni zarządzana usługa analizy danych. Oferuje analizę w czasie rzeczywistym na dużych ilościach danych, które przesyłają strumieniowo z wielu źródeł, takich jak aplikacje, witryny sieci Web i urządzenia IoT. Za pomocą usługi Azure Data Explorer możesz iterować eksplorowanie danych i identyfikować wzorce i anomalie w celu ulepszania produktów, ulepszania doświadczeń klientów, monitorowania urządzeń i zwiększania liczby operacji. Pomaga odkrywać nowe pytania i uzyskać odpowiedzi w ciągu kilku minut. 

Usługa Azure Data Factory to w pełni zarządzana, oparta na chmurze usługa integracji danych. Można go użyć do wypełniania bazy danych Usługi Azure Data Explorer danymi z istniejącego systemu. Może pomóc zaoszczędzić czas podczas tworzenia rozwiązań analitycznych.

Podczas ładowania danych do usługi Azure Data Explorer usługa Data Factory zapewnia następujące korzyści:

* **Łatwa konfiguracja:** Uzyskaj intuicyjny, pięcioetapowy kreator bez obsługi skryptów.
* **Obsługa magazynu danych:** uzyskaj wbudowaną obsługę bogatego zestawu lokalnych i chmurowych magazynów danych. Aby uzyskać szczegółową listę, zobacz tabelę [obsługiwanych magazynów danych](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats).
* **Bezpieczne i zgodne:** Dane są przesyłane za pośrednictwem protokołu HTTPS lub usługi Azure ExpressRoute. Globalna obecność usługi gwarantuje, że dane nigdy nie opuszczają granicy geograficznej.
* **Wysoka wydajność:** szybkość ładowania danych wynosi do 1 gigabajta na sekundę (GBps) do usługi Azure Data Explorer. Aby uzyskać więcej informacji, zobacz [Kopiowanie wydajności działania](/azure/data-factory/copy-activity-performance).

W tym artykule narzędzie Data Copy Data służy do ładowania danych z usługi Amazon Simple Storage Service (S3) do usługi Azure Data Explorer. Podobny proces można wykonać, aby skopiować dane z innych magazynów danych, takich jak:
* [Azure Blob Storage](/azure/data-factory/connector-azure-blob-storage)
* [Azure SQL Database](/azure/data-factory/connector-azure-sql-database)
* [Magazyn danych SQL platformy Azure](/azure/data-factory/connector-azure-sql-data-warehouse)
* [Google BigQuery](/azure/data-factory/connector-google-bigquery)
* [Oracle](/azure/data-factory/connector-oracle)
* [System plików](/azure/data-factory/connector-file-system)

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/) przed rozpoczęciem.
* [Baza danych i klaster usługi Azure Data Explorer](create-cluster-database-portal.md).
* Źródło danych.

## <a name="create-a-data-factory"></a>Tworzenie fabryki danych

1. Zaloguj się do [Portalu Azure](https://ms.portal.azure.com).

1. W lewym okienku wybierz pozycję **Utwórz fabrykę** > **danych****analizy** > zasobów .

   ![Tworzenie fabryki danych w witrynie Azure portal](media/data-factory-load-data/create-adf.png)

1. W okienku **Nowa fabryka danych** podaj wartości pól w poniższej tabeli:

   ![Okienko "Nowa fabryka danych"](media/data-factory-load-data/my-new-data-factory.png)  

   | Ustawienie  | Wartość do wniesienia  |
   |---|---|
   | **Nazwa** | W polu wprowadź globalnie unikatową nazwę fabryki danych. Jeśli zostanie wyświetlony błąd, * \"nazwa fabryki danych LoadADXDemo\" nie jest dostępna*, wprowadź inną nazwę dla fabryki danych. Aby uzyskać reguły dotyczące nazywania artefaktów fabryki danych, zobacz [Reguły nazewnictwa fabryki danych](/azure/data-factory/naming-rules).|
   | **Subskrypcja** | Na liście rozwijanej wybierz subskrypcję platformy Azure, w której chcesz utworzyć fabrykę danych. |
   | **Grupa zasobów** | Wybierz **pozycję Utwórz nowy**, a następnie wprowadź nazwę nowej grupy zasobów. Jeśli masz już grupę zasobów, wybierz pozycję **Użyj istniejącego**. |
   | **Wersja** | Z listy rozwijanej wybierz pozycję **V2**. |    
   | **Lokalizacja** | Z listy rozwijanej wybierz lokalizację fabryki danych. Na liście są wyświetlane tylko obsługiwane lokalizacje. Magazyny danych, które są używane przez fabrykę danych może istnieć w innych lokalizacjach lub regionach. |

1. Wybierz **pozycję Utwórz**.

1. Aby monitorować proces tworzenia, wybierz **pozycję Powiadomienia** na pasku narzędzi. Po utworzeniu fabryki danych wybierz ją.
   
   Zostanie otwarte **okienko Fabryka danych.**

   ![Okienko Fabryka danych](media/data-factory-load-data/data-factory-home-page.png)

1. Aby otworzyć aplikację w osobnym okienku, wybierz kafelek **Autor & Monitor.**

## <a name="load-data-into-azure-data-explorer"></a>Ładowanie danych do Eksploratora danych platformy Azure

Można załadować dane z wielu typów [magazynów danych](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) do Usługi Azure Data Explorer. W tym artykule omówiono sposób ładowania danych z Amazon S3.

Dane można załadować w jeden z następujących sposobów:

* W interfejsie użytkownika usługi Azure Data Factory w lewym okienku wybierz ikonę **Autor.** Jest to pokazane w sekcji "Tworzenie fabryki danych" [w obszarze Tworzenie fabryki danych przy użyciu interfejsu użytkownika usługi Azure Data Factory](/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory).
* W narzędziu Kopiowanie danych fabrycznych platformy Azure, jak pokazano w [narzędziu Kopiuj dane, aby skopiować dane](/azure/data-factory/quickstart-create-data-factory-copy-data-tool).

### <a name="copy-data-from-amazon-s3-source"></a>Kopiowanie danych z Amazon S3 (źródło)

1. W okienku **Rozpocznijmy pracę** otwórz narzędzie Kopiuj dane, wybierając pozycję **Kopiuj dane**.

   ![Przycisk Narzędzia Kopiuj dane](media/data-factory-load-data/copy-data-tool-tile.png)

1. W okienku **Właściwości** w polu **Nazwa zadania** wprowadź nazwę, a następnie wybierz pozycję **Dalej**.

    ![Okienko Kopiowanie właściwości danych](media/data-factory-load-data/copy-from-source.png)

1. W okienku **Magazyn danych źródłowych** wybierz pozycję **Utwórz nowe połączenie**.

    ![Okienko Kopiowanie danych "Magazyn danych źródłowych"](media/data-factory-load-data/source-create-connection.png)

1. Wybierz **amazon S3**, a następnie wybierz pozycję **Kontynuuj**.

    ![Okienko Nowa usługa połączona](media/data-factory-load-data/amazons3-select-new-linked-service.png)

1. W okienku **Nowa usługa połączona (Amazon S3)** wykonaj następujące czynności:

    ![Określ usługę połączony Amazon S3](media/data-factory-load-data/amazons3-new-linked-service-properties.png)

    a. W polu **Nazwa** wprowadź nazwę nowej połączonej usługi.

    b. Na liście rozwijanej **Połącz za pośrednictwem integracji** wybierz wartość.

    d. W polu **Identyfikator klucza dostępu** wprowadź wartość.
    
    > [!NOTE]
    > W Amazon S3, aby zlokalizować klucz dostępu, wybierz nazwę użytkownika Amazon na pasku nawigacyjnym, a następnie wybierz **pozycję Moje poświadczenia zabezpieczeń**.
    
    d. W polu **Klucz tajnego dostępu** wprowadź wartość.

    e. Aby przetestować utworzone połączenie połączonej usługi, wybierz opcję **Testuj połączenie**.

    f. Wybierz **pozycję Zakończ**.
    
      W okienku **Magazyn danych źródłowych** jest wyświetlane nowe połączenie AmazonS31. 

1. Wybierz **pozycję Dalej**.

   ![Połączenie utworzone w magazynie danych źródłowych](media/data-factory-load-data/source-data-store-created-connection.png)

1. W **okienku Wybieranie pliku wejściowego lub folderu** wykonaj następujące czynności:

    a. Przejdź do pliku lub folderu, który chcesz skopiować, a następnie zaznacz go.

    b. Wybierz odpowiednie zachowanie kopiowania. Upewnij się, że pole wyboru **Kopia binarna** jest wyczyszczone.

    d. Wybierz **pozycję Dalej**.

    ![Wybieranie pliku lub folderu wejściowego](media/data-factory-load-data/source-choose-input-file.png)

1. W okienku **Ustawienia formatu pliku** wybierz odpowiednie ustawienia pliku. a następnie wybierz przycisk **Dalej**.

   ![Okienko "Ustawienia formatu pliku"](media/data-factory-load-data/source-file-format-settings.png)

### <a name="copy-data-into-azure-data-explorer-destination"></a>Kopiowanie danych do Eksploratora danych platformy Azure (miejsce docelowe)

Nowa usługa połączona usługi Azure Data Explorer jest tworzona w celu skopiowania danych do tabeli docelowej (ujścia) usługi Azure Data Explorer, która jest określona w tej sekcji.

> [!NOTE]
> Użyj [działania polecenia usługi Azure Data Factory, aby uruchamiać polecenia sterowania usługi Azure Data Explorer](data-factory-command-activity.md) i używać dowolnego z [poleceń pozyskiwania z poleceń kwerendy,](/azure/kusto/management/data-ingestion/ingest-from-query)takich jak `.set-or-replace`.

#### <a name="create-the-azure-data-explorer-linked-service"></a>Tworzenie połączonej usługi Azure Data Explorer

Aby utworzyć usługę połączony z Eksploratorem danych platformy Azure, wykonaj następujące czynności:

1. Aby użyć istniejącego połączenia magazynu danych lub określić nowy magazyn danych, w okienku **Docelowy magazyn danych** wybierz pozycję **Utwórz nowe połączenie**.

    ![Okienko docelowego magazynu danych](media/data-factory-load-data/destination-create-connection.png)

1. W okienku **Nowa usługa połączona** wybierz pozycję **Azure Data Explorer**, a następnie wybierz pozycję **Kontynuuj**.

    ![Okienko Nowa usługa połączona](media/data-factory-load-data/adx-select-new-linked-service.png)

1. W okienku **Nowa usługa połączona (Azure Data Explorer)** wykonaj następujące czynności:

    ![Okienko Nowa usługa połączona usługi Azure Data Explorer](media/data-factory-load-data/adx-new-linked-service.png)

   a. W polu **Nazwa** wprowadź nazwę połączonej usługi Usługi Azure Data Explorer.

   b. W **obszarze Metoda wyboru konta**wybierz jedną z następujących opcji: 

    * Wybierz **opcję Z subskrypcji platformy Azure,** a następnie na listach rozwijanych wybierz **subskrypcję platformy Azure** i **klaster**. 

        > [!NOTE]
        > Kontrola rozwijana **klastra** zawiera listę tylko klastrów skojarzonych z subskrypcją.

    * Wybierz **pozycję Wprowadź ręcznie**, a następnie wprowadź punkt **końcowy**.

   d. W polu **Dzierżawa** wprowadź nazwę dzierżawy.

   d. W polu **Identyfikator jednostki usługi** wprowadź identyfikator jednostki usługi.

   e. Wybierz **polecenie Klucz jednostkowy usługi,** a następnie w polu **Klucz główny usługi** wprowadź wartość klucza.

   f. Z listy rozwijanej **Baza danych** wybierz nazwę bazy danych. Alternatywnie zaznacz pole wyboru **Edytuj,** a następnie wprowadź nazwę bazy danych.

   g. Aby przetestować utworzone połączenie połączonej usługi, wybierz opcję **Testuj połączenie**. Jeśli można połączyć się z usługą połączona, w okienku zostanie wyświetlony zielony znacznik wyboru i **komunikat Pomyślny połączenie.**

   h. Wybierz **zakończ,** aby zakończyć tworzenie połączonej usługi.

    > [!NOTE]
    > Podmiot usługi jest używany przez usługę Azure Data Factory w celu uzyskania dostępu do usługi Azure Data Explorer. Aby utworzyć jednostkę usługi, przejdź do [tworzenia jednostki usługi Azure Active Directory (Azure AD).](/azure-stack/operator/azure-stack-create-service-principals#manage-an-azure-ad-service-principal) Nie należy używać metody usługi Azure Key Vault.

#### <a name="configure-the-azure-data-explorer-data-connection"></a>Konfigurowanie połączenia danych usługi Azure Data Explorer

Po utworzeniu połączonego połączenia usługi zostanie otwarte okienko **Magazyn danych miejsce docelowe,** a utworzone połączenie będzie dostępne do użycia. Aby skonfigurować połączenie, wykonaj następujące czynności:

1. Wybierz **pozycję Dalej**.

    ![Okienko "Docelowy magazyn danych" eksploratora danych platformy Azure](media/data-factory-load-data/destination-data-store.png)

1. W okienku **mapowania tabeli** ustaw nazwę tabeli docelowej, a następnie wybierz pozycję **Dalej**.

    ![Okienko docelowego zestawu danych "Mapowanie tabel"](media/data-factory-load-data/destination-dataset-table-mapping.png)

1. W okienku **mapowania kolumn** odbywają się następujące mapowania:

    a. Pierwsze mapowanie jest wykonywane przez usługę Azure Data Factory zgodnie z [mapowaniem schematu usługi Azure Data Factory.](/azure/data-factory/copy-activity-schema-and-type-mapping) Wykonaj następujące czynności:

    * Ustaw **mapowania kolumn** dla tabeli docelowej usługi Azure Data Factory. Domyślne mapowanie jest wyświetlane ze źródła do tabeli docelowej usługi Azure Data Factory.

    * Anuluj zaznaczenie kolumn, których nie potrzebujesz do definiowania mapowania kolumn.

    b. Drugie mapowanie występuje, gdy te dane tabelaryczne są pozyskiwania do Usługi Azure Data Explorer. Mapowanie odbywa się zgodnie z [regułami mapowania CSV](/azure/kusto/management/mappings#csv-mapping). Nawet jeśli dane źródłowe nie są w formacie CSV, usługa Azure Data Factory konwertuje dane na format tabelaryczny. W związku z tym mapowanie CSV jest jedynym odpowiednim mapowaniem na tym etapie. Wykonaj następujące czynności:

    * (Opcjonalnie) W obszarze **Azure Data Explorer (Kusto) sink właściwości**, dodać odpowiednią nazwę **mapowania pozyskiwania,** dzięki czemu można użyć mapowania kolumn.

    * Jeśli **nazwa mapowania pozyskiwania** nie zostanie określona, zostanie użyta kolejność mapowania nazw według *nazwy* zdefiniowana w sekcji **Mapowania kolumn.** Jeśli mapowanie *nazw według nazwy* nie powiedzie się, Usługa Azure Data Explorer próbuje pozyskiwania danych w kolejności pozycji według *kolumny* (oznacza to, że mapuje według pozycji jako domyślnej).

    * Wybierz **pozycję Dalej**.

    ![Okienko docelowego zestawu danych "Mapowanie kolumn"](media/data-factory-load-data/destination-dataset-column-mapping.png)

1. W okienku **Ustawienia** wykonaj następujące czynności:

    a. W obszarze **Ustawienia tolerancji na uszkodzenia**wprowadź odpowiednie ustawienia.

    b. W obszarze **Ustawienia wydajności** **włącz przemieszczanie** nie ma zastosowania, a **ustawienia zaawansowane** uwzględnić kwestie związane z kosztami. Jeśli nie masz żadnych określonych wymagań, pozostaw te ustawienia w stanie, w jakim jest.

    d. Wybierz **pozycję Dalej**.

    ![Okienko kopiowania danych "Ustawienia"](media/data-factory-load-data/copy-data-settings.png)

1. W okienku **Podsumowanie** przejrzyj ustawienia, a następnie wybierz pozycję **Dalej**.

    ![Okienko "Podsumowanie" danych kopiowania](media/data-factory-load-data/copy-data-summary.png)

1. W okienku **Zakończenie wdrażania** wykonaj następujące czynności:

    a. Aby przełączyć się na kartę **Monitor** i wyświetlić stan potoku (czyli postęp, błędy i przepływ danych), wybierz opcję **Monitoruj**.

    b. Aby edytować połączone usługi, zestawy danych i rurociągi, wybierz pozycję **Edytuj potok**.

    d. Wybierz **pozycję Zakończ,** aby wykonać zadanie kopiowania danych.

    ![Okienko "Wdrożenie zakończone"](media/data-factory-load-data/deployment.png)

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [łączniku usługi Azure Data Explorer](/azure/data-factory/connector-azure-data-explorer) w usłudze Azure Data Factory.
* Dowiedz się więcej o edytowaniu połączonych usług, zestawów danych i potoków w [interfejsie użytkownika fabryki danych](/azure/data-factory/quickstart-create-data-factory-portal).
* Dowiedz się więcej o [kwerendach usługi Azure Data Explorer](/azure/data-explorer/web-query-data) dotyczących zapytań dotyczących danych.
