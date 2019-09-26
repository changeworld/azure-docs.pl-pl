---
title: Kopiowanie danych z Azure Data Factory na platformę Azure Eksplorator danych
description: W tym artykule dowiesz się, jak pozyskiwanie (ładować) danych do usługi Azure Eksplorator danych przy użyciu narzędzia do kopiowania Azure Data Factory.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: 860b1a579d9c8cee6c6e80ae4c4e7fdd7949d5c7
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300602"
---
# <a name="copy-data-to-azure-data-explorer-by-using-azure-data-factory"></a>Kopiowanie danych do usługi Azure Eksplorator danych przy użyciu Azure Data Factory 

Azure Eksplorator danych to szybka i w pełni zarządzana usługa analizy danych. Oferuje ona analizę w czasie rzeczywistym w przypadku dużych ilości danych przesyłanych strumieniowo z wielu źródeł, takich jak aplikacje, witryny sieci Web i urządzenia IoT. Za pomocą usługi Azure Eksplorator danych można iteracyjnie eksplorować dane oraz identyfikować wzorce i anomalie w celu ulepszania produktów, ulepszania środowiska klienta, monitorowania urządzeń i zwiększania wydajności. Ułatwia to eksplorowanie nowych pytań i otrzymywanie odpowiedzi w ciągu kilku minut. 

Azure Data Factory to w pełni zarządzana, oparta na chmurze usługa integracji danych. Służy do wypełniania bazy danych usługi Azure Eksplorator danych bazą danych z istniejącego systemu. Może pomóc zaoszczędzić czas podczas kompilowania rozwiązań analitycznych.

Podczas ładowania danych do usługi Azure Eksplorator danych Data Factory zapewnia następujące korzyści:

* **Łatwa konfiguracja**: Uzyskaj intuicyjny, 5-etapowy kreator bez konieczności wykonywania skryptów.
* **Obsługa rozbudowanych magazynów danych**: Uzyskaj wbudowaną obsługę bogatego zestawu lokalnych i opartych na chmurze magazynów danych. Aby uzyskać szczegółową listę, zobacz tabelę [obsługiwanych magazynów danych](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats).
* **Bezpieczne i zgodne**: Dane są przesyłane za pośrednictwem protokołu HTTPS lub Azure ExpressRoute. Globalna obecność usługi gwarantuje, że dane nigdy nie opuszczą granicy geograficznej.
* **Wysoka wydajność**: Szybkość ładowania danych to 1 gigabajt na sekundę (GB/s) do Eksplorator danych platformy Azure. Aby uzyskać więcej informacji, zobacz [wydajność działania kopiowania](/azure/data-factory/copy-activity-performance).

W tym artykule opisano używanie narzędzia Data Factory Kopiowanie danych do ładowania danych z usługi Amazon Simple Storage Service (S3) do platformy Azure Eksplorator danych. Podobny proces można wykonać, aby skopiować dane z innych magazynów danych, na przykład:
* [Azure Blob Storage](/azure/data-factory/connector-azure-blob-storage)
* [Azure SQL Database](/azure/data-factory/connector-azure-sql-database)
* [Azure SQL Data Warehouse](/azure/data-factory/connector-azure-sql-data-warehouse)
* [Google BigQuery](/azure/data-factory/connector-google-bigquery)
* [Oracle](/azure/data-factory/connector-oracle)
* [System plików](/azure/data-factory/connector-file-system)

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/).
* [Baza danych i klaster usługi Azure Data Explorer](create-cluster-database-portal.md).
* Źródło danych.

## <a name="create-a-data-factory"></a>Tworzenie fabryki danych

1. Zaloguj się w witrynie [Azure Portal](https://ms.portal.azure.com).

1. W lewym okienku wybierz pozycję **Utwórz zasób** > **Analytics** > **Data Factory**.

   ![Utwórz fabrykę danych w Azure Portal](media/data-factory-load-data/create-adf.png)

1. W okienku **Nowa fabryka danych** podaj wartości pól w poniższej tabeli:

   ![Okienko "Nowa fabryka danych"](media/data-factory-load-data/my-new-data-factory.png)  

   | Ustawienie  | Wartość do wprowadzenia  |
   |---|---|
   | **Nazwa** | W polu Wprowadź globalnie unikatową nazwę fabryki danych. Jeśli wystąpi błąd,  *\"nazwa fabryki danych LoadADXDemo\" jest niedostępna*, wprowadź inną nazwę fabryki danych. Aby uzyskać reguły dotyczące nazewnictwa Data Factory artefaktów, zobacz [Data Factory reguł nazewnictwa](/azure/data-factory/naming-rules).|
   | **Subskrypcja** | Z listy rozwijanej wybierz subskrypcję platformy Azure, w której chcesz utworzyć fabrykę danych. |
   | **Grupa zasobów** | Wybierz pozycję **Utwórz nowy**, a następnie wprowadź nazwę nowej grupy zasobów. Jeśli masz już grupę zasobów, wybierz pozycję **Użyj istniejącej**. |
   | **Wersja** | Z listy rozwijanej wybierz pozycję **v2**. |  
   | **Location** | Z listy rozwijanej wybierz lokalizację fabryki danych. Na liście są wyświetlane tylko obsługiwane lokalizacje. Magazyny danych używane przez fabrykę danych mogą znajdować się w innych lokalizacjach lub regionach. |

1. Wybierz pozycję **Utwórz**.

1. Aby monitorować proces tworzenia, wybierz pozycję **powiadomienia** na pasku narzędzi. Po utworzeniu fabryki danych wybierz ją.
   
   Zostanie otwarte okienko **Data Factory** .

   ![Okienko Data Factory](media/data-factory-load-data/data-factory-home-page.png)

1. Aby otworzyć aplikację w osobnym okienku, wybierz kafelek **tworzenie & monitor** .

## <a name="load-data-into-azure-data-explorer"></a>Ładowanie danych do usługi Azure Eksplorator danych

Dane z wielu typów [magazynów danych](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) można ładować do Eksplorator danych platformy Azure. W tym artykule omówiono sposób ładowania danych z usługi Amazon S3.

Dane można ładować w jeden z następujących sposobów:

* W Azure Data Factory interfejsie użytkownika w lewym okienku wybierz ikonę **autor** , jak pokazano w sekcji "Tworzenie fabryki danych" w temacie [Tworzenie fabryki danych przy użyciu interfejsu użytkownika Azure Data Factory](/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory).
* W narzędziu Azure Data Factory Kopiowanie danych, jak pokazano w [narzędziu kopiowanie danych do kopiowania danych](/azure/data-factory/quickstart-create-data-factory-copy-data-tool).

### <a name="copy-data-from-amazon-s3-source"></a>Kopiowanie danych z usługi Amazon S3 (Źródło)

1. W okienku **wprowadzenie** otwórz Narzędzie kopiowanie danych, wybierając pozycję **Kopiowanie danych**.

   ![Przycisk narzędzia Kopiowanie danych](media/data-factory-load-data/copy-data-tool-tile.png)

1. W okienku **Właściwości** w polu **Nazwa zadania** wprowadź nazwę, a następnie wybierz przycisk **dalej**.

    ![Okienko właściwości Kopiowanie danych](media/data-factory-load-data/copy-from-source.png)

1. W okienku **źródłowy magazyn danych** wybierz pozycję **Utwórz nowe połączenie**.

    ![Okienko Kopiowanie danych "źródłowy magazyn danych"](media/data-factory-load-data/source-create-connection.png)

1. Wybierz pozycję **Amazon S3**, a następnie wybierz pozycję **Kontynuuj**.

    ![Okienko Nowa połączona usługa](media/data-factory-load-data/amazons3-select-new-linked-service.png)

1. W okienku **Nowa połączona usługa (Amazon S3)** wykonaj następujące czynności:

    ![Określ połączoną usługę Amazon S3](media/data-factory-load-data/amazons3-new-linked-service-properties.png)

    a. W polu **Nazwa** wprowadź nazwę nowej połączonej usługi.

    b. Na liście rozwijanej **Połącz za pośrednictwem środowiska Integration Runtime** wybierz wartość.

    c. W polu **Identyfikator klucza dostępu** wprowadź wartość.
    
    > [!NOTE]
    > Aby znaleźć klucz dostępu w usłudze Amazon S3, wybierz nazwę użytkownika Amazon na pasku nawigacyjnym, a następnie wybierz pozycję **moje poświadczenia zabezpieczeń**.
    
    d. W polu **klucz dostępu tajnego** wprowadź wartość.

    e. Aby przetestować utworzone połączenie połączonej usługi, wybierz pozycję **Testuj połączenie**.

    f. Wybierz pozycję **Finish** (Zakończ).
    
      W okienku **źródłowy magazyn danych** zostanie wyświetlone nowe połączenie usługi AmazonS31. 

1. Wybierz opcję **Dalej**.

   ![Utworzono połączenie z magazynem danych źródłowych](media/data-factory-load-data/source-data-store-created-connection.png)

1. W okienku **Wybieranie pliku lub folderu wejściowego** wykonaj następujące czynności:

    a. Przejdź do pliku lub folderu, który chcesz skopiować, a następnie wybierz go.

    b. Wybierz odpowiednie zachowanie kopiowania. Upewnij się, że pole wyboru **Kopiowanie binarne** jest wyczyszczone.

    c. Wybierz opcję **Dalej**.

    ![Wybieranie pliku lub folderu wejściowego](media/data-factory-load-data/source-choose-input-file.png)

1. W okienku **Ustawienia formatu pliku** wybierz odpowiednie ustawienia dla danego pliku. a następnie wybierz przycisk **dalej**.

   ![Okienko "Ustawienia formatu plików"](media/data-factory-load-data/source-file-format-settings.png)

### <a name="copy-data-into-azure-data-explorer-destination"></a>Kopiowanie danych do usługi Azure Eksplorator danych (miejsce docelowe)

Nowa połączona usługa Azure Eksplorator danych zostanie utworzona w celu skopiowania danych do tabeli docelowej Eksplorator danych platformy Azure, która jest określona w tej sekcji.

#### <a name="create-the-azure-data-explorer-linked-service"></a>Tworzenie połączonej usługi Azure Eksplorator danych

Aby utworzyć połączoną usługę Azure Eksplorator danych, wykonaj następujące czynności:

1. Aby użyć istniejącego połączenia z magazynem danych lub określić nowy magazyn danych, w okienku **docelowy magazyn danych** wybierz pozycję **Utwórz nowe połączenie**.

    ![Okienko docelowego magazynu danych](media/data-factory-load-data/destination-create-connection.png)

1. W okienku **Nowa połączona usługa** wybierz pozycję **Azure Eksplorator danych**, a następnie wybierz pozycję **Kontynuuj**.

    ![Okienko Nowa połączona usługa](media/data-factory-load-data/adx-select-new-linked-service.png)

1. W okienku **Nowa połączona usługa (Azure Eksplorator danych)** wykonaj następujące czynności:

    ![Okienko Nowa połączona usługa Eksplorator danych Azure](media/data-factory-load-data/adx-new-linked-service.png)

   a. W polu **Nazwa** wprowadź nazwę połączonej usługi Azure Eksplorator danych.

   b. W obszarze **Metoda wyboru konta**wykonaj jedną z następujących czynności: 

    * Wybierz pozycję **z subskrypcji platformy Azure** , a następnie na liście rozwijanej wybierz swoją **subskrypcję platformy Azure** i **klaster**. 

        > [!NOTE]
        > Kontrolka lista rozwijana **klastra** zawiera tylko te klastry, które są skojarzone z Twoją subskrypcją.

    * Wybierz pozycję **wprowadź ręcznie**, a następnie wprowadź **punkt końcowy**.

   c. W polu **dzierżawca** wprowadź nazwę dzierżawy.

   d. W polu **Identyfikator jednostki usługi** wprowadź identyfikator jednostki usługi.

   e. Wybierz pozycję **klucz jednostki usługi** , a następnie w polu klucz jednostki **usługi** wprowadź wartość klucza.

   f. Z listy rozwijanej **baza danych** wybierz nazwę bazy danych. Możesz też zaznaczyć pole wyboru **Edytuj** , a następnie wprowadzić nazwę bazy danych.

   g. Aby przetestować utworzone połączenie połączonej usługi, wybierz pozycję **Testuj połączenie**. Jeśli można nawiązać połączenie z połączoną usługą, w okienku zostanie wyświetlony zielony znacznik wyboru i komunikat o **pomyślnym nawiązaniu połączenia** .

   h. Wybierz pozycję **Zakończ** , aby ukończyć tworzenie połączonej usługi.

    > [!NOTE]
    > Nazwa główna usługi jest używana przez Azure Data Factory, aby uzyskać dostęp do usługi Azure Eksplorator danych. Aby utworzyć nazwę główną usługi, przejdź do obszaru [Tworzenie jednostki usługi Azure Active Directory (Azure AD)](/azure-stack/operator/azure-stack-create-service-principals#manage-an-azure-ad-service-principal). Nie należy używać metody Azure Key Vault.

#### <a name="configure-the-azure-data-explorer-data-connection"></a>Konfigurowanie połączenia danych Eksplorator danych platformy Azure

Po utworzeniu połączenia połączonej usługi zostanie otwarte okienko **docelowy magazyn danych** , a utworzone połączenie jest dostępne do użycia. Aby skonfigurować połączenie, wykonaj następujące czynności:

1. Wybierz opcję **Dalej**.

    ![Okienko "docelowy magazyn danych" platformy Azure Eksplorator danych](media/data-factory-load-data/destination-data-store.png)

1. W okienku **Mapowanie tabeli** Ustaw nazwę tabeli docelowej, a następnie wybierz przycisk **dalej**.

    ![Okienko "Mapowanie tabeli" docelowego zestawu danych](media/data-factory-load-data/destination-dataset-table-mapping.png)

1. W okienku **Mapowanie kolumn** następujące mapowania zostały wykonane:

    a. Pierwsze mapowanie jest wykonywane przez Azure Data Factory zgodnie z [mapowaniem schematu Azure Data Factory](/azure/data-factory/copy-activity-schema-and-type-mapping). Wykonaj następujące czynności:

    * Ustaw **mapowania kolumn** dla Azure Data Factory tabeli docelowej. Domyślne mapowanie jest wyświetlane z lokalizacji źródłowej w Azure Data Factory tabeli docelowej.

    * Anuluj wybór kolumn, które nie muszą definiować mapowania kolumn.

    b. Drugie mapowanie występuje, gdy te dane tabelaryczne zostaną pozyskane do Eksplorator danych platformy Azure. Mapowanie jest wykonywane zgodnie z [regułami mapowania CSV](/azure/kusto/management/mappings#csv-mapping). Nawet jeśli dane źródłowe nie są w formacie CSV, Azure Data Factory konwertuje dane w formacie tabelarycznym. W związku z tym mapowanie CSV jest jedynym odpowiednim mapowaniem na tym etapie. Wykonaj następujące czynności:

    * Obowiązkowe W obszarze **Właściwości ujścia usługi Azure Eksplorator danych (Kusto)** Dodaj odpowiednią **nazwę mapowania** pozyskiwania, aby można było użyć mapowania kolumn.

    * Jeśli **Nazwa mapowania** pozyskiwania nie jest określona, zostanie użyta kolejność mapowania *według nazwy* , która jest zdefiniowana w sekcji **mapowania kolumn** . Jeśli mapowanie *według nazwy* nie powiedzie się, usługa Azure Eksplorator danych próbuje pozyskać dane w kolejności *położenia według kolumny* (czyli mapuje według pozycji jako domyślnej).

    * Wybierz opcję **Dalej**.

    ![Okienko "Mapowanie kolumn" docelowego zestawu danych](media/data-factory-load-data/destination-dataset-column-mapping.png)

1. W okienku **Ustawienia** wykonaj następujące czynności:

    a. W obszarze **Ustawienia odporności na uszkodzenia**wprowadź odpowiednie ustawienia.

    b. W obszarze **Ustawienia wydajności** **Włączanie przemieszczania** nie ma zastosowania, a **Ustawienia zaawansowane** zawierają zagadnienia dotyczące kosztów. Jeśli nie masz określonych wymagań, pozostaw te ustawienia zgodnie z oczekiwaniami.

    c. Wybierz opcję **Dalej**.

    ![Okienko ustawienia kopiowania danych](media/data-factory-load-data/copy-data-settings.png)

1. W okienku **Podsumowanie** przejrzyj ustawienia, a następnie wybierz przycisk **dalej**.

    ![Okienko "Podsumowanie kopiowania danych"](media/data-factory-load-data/copy-data-summary.png)

1. W okienku **ukończenie wdrożenia** wykonaj następujące czynności:

    a. Aby przełączyć się na kartę **monitor** i wyświetlić stan potoku (czyli postępu, błędów i przepływu danych), wybierz pozycję **Monitoruj**.

    b. Aby edytować połączone usługi, zestawy danych i potoki, wybierz pozycję **Edytuj potok**.

    c. Wybierz pozycję **Zakończ** , aby ukończyć zadanie kopiowania danych.

    ![Okienko "ukończenie wdrożenia"](media/data-factory-load-data/deployment.png)

## <a name="next-steps"></a>Następne kroki

* Więcej informacji na temat [łącznika usługi Azure Eksplorator danych](/azure/data-factory/connector-azure-data-explorer) w programie Azure Data Factory.
* Dowiedz się więcej o edytowaniu połączonych usług, zestawów danych i potoków w [interfejsie użytkownika Data Factory](/azure/data-factory/quickstart-create-data-factory-portal).
* Dowiedz się więcej na temat [zapytań dotyczących usługi Azure Eksplorator danych](/azure/data-explorer/web-query-data) na potrzeby zapytań dotyczących danych.
