---
title: Kopiowanie danych z Azure Data Factory na platformę Azure Eksplorator danych
description: W tym temacie omówiono pobieranie (ładowanie) danych do usługi Azure Eksplorator danych przy użyciu narzędzia do kopiowania Azure Data Factory
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: b3bd9b800da4f096639d02c78b718216441621a9
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/08/2019
ms.locfileid: "70803988"
---
# <a name="copy-data-to-azure-data-explorer-using-azure-data-factory"></a>Kopiowanie danych do usługi Azure Eksplorator danych przy użyciu Azure Data Factory 

Azure Eksplorator danych to szybka i w pełni zarządzana usługa analizy danych do analizy w czasie rzeczywistym w przypadku dużych ilości danych przesyłanych strumieniowo z wielu źródeł, takich jak aplikacje, witryny sieci Web i urządzenia IoT. Iteracyjnie Eksplorowanie danych i identyfikowanie wzorców i anomalii w celu ulepszania produktów, ulepszania środowiska klienta, monitorowania urządzeń i zwiększania wydajności. Eksploruj nowe pytania i uzyskuj odpowiedzi w ciągu minut. Azure Data Factory to w pełni zarządzana usługa integracji danych oparta na chmurze. Możesz użyć usługi do wypełniania bazy danych usługi Azure Eksplorator danych bazą danych z istniejącego systemu i zaoszczędzić czas podczas kompilowania rozwiązań analitycznych.

Azure Data Factory oferuje następujące korzyści związane z ładowaniem danych na platformę Azure Eksplorator danych:

* **Łatwa konfiguracja**: Intuicyjny kreator z pięcioma krokami bez konieczności wykonywania skryptów.
* **Obsługa rozbudowanych magazynów danych**: Wbudowana obsługa bogatego zestawu lokalnych i opartych na chmurze magazynów danych. Aby uzyskać szczegółową listę, zobacz tabelę [obsługiwanych magazynów danych](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats).
* **Bezpieczne i zgodne**: Dane są przesyłane za pośrednictwem protokołu HTTPS lub ExpressRoute. Globalna obecność usługi gwarantuje, że dane nigdy nie opuszczą granicy geograficznej.
* **Wysoka wydajność**: Szybkość ładowania danych do 1 GB/s do Eksplorator danych platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [wydajność działania kopiowania](/azure/data-factory/copy-activity-performance).

W tym artykule pokazano, jak za pomocą narzędzia Kopiowanie danych Data Factory załadować dane z usługi Amazon S3 do platformy Azure Eksplorator danych. Możesz wykonać podobne kroki, aby skopiować dane z innych magazynów danych, takich jak [Azure Blob Storage](/azure/data-factory/connector-azure-blob-storage), [Azure SQL Database](/azure/data-factory/connector-azure-sql-database), [Azure SQL Data Warehouse](/azure/data-factory/connector-azure-sql-data-warehouse), [Google BigQuery](/azure/data-factory/connector-google-bigquery),[Oracle](/azure/data-factory/connector-oracle)i [system plików](/azure/data-factory/connector-file-system).

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/).
* [Klaster Eksplorator danych i baza danych platformy Azure](create-cluster-database-portal.md)
* Źródło danych.

## <a name="create-a-data-factory"></a>Tworzenie fabryki danych

1. Wybierz przycisk **Utwórz zasób** (+) w lewym górnym rogu portalu > **Analytics** > **Data Factory**.

   ![Tworzenie nowej fabryki danych](media/data-factory-load-data/create-adf.png)

1. Na stronie **Nowa fabryka danych** podaj wartości dla następujących pól, a następnie wybierz pozycję **Utwórz**.

    ![Strona Nowa fabryka danych](media/data-factory-load-data/my-new-data-factory.png)

    **Ustawienie**  | **Opis pola**
    |---|---|
    | **Nazwa** | Wprowadź globalnie unikatową nazwę fabryki danych. Jeśli zostanie wyświetlony komunikat o błędzie *" \"nazwa fabryki danych LoadADXDemo\" jest niedostępna"* , wprowadź inną nazwę fabryki danych. Aby poznać reguły nazewnictwa Data Factory artefaktów, zobacz [Data Factory reguły nazewnictwa](/azure/data-factory/naming-rules).|
    | **Subskrypcja** | Wybierz swoją subskrypcję platformy Azure, w której chcesz utworzyć fabrykę danych. |
    | **Grupa zasobów** | Wybierz pozycję **Utwórz nową** i wprowadź nazwę nowej grupy zasobów. Wybierz pozycję **Użyj istniejącej**, jeśli masz istniejącą grupę zasobów. |
    | **Wersja** | Wybierz pozycję **v2** |
    | **Location** | Wybierz lokalizację fabryki danych. Na liście rozwijanej są wyświetlane tylko obsługiwane lokalizacje. Magazyny danych używane przez fabrykę danych mogą znajdować się w innych lokalizacjach lub regionach. |
    | | |

1. Wybierz pozycję powiadomienia na pasku narzędzi, aby monitorować proces tworzenia. Po zakończeniu tworzenia przejdź do utworzonej fabryki danych. Zostanie otwarta strona główna **Data Factory** .

   ![Strona główna fabryki danych](media/data-factory-load-data/data-factory-home-page.png)

1. Wybierz kafelek **tworzenie & monitor** , aby uruchomić aplikację na osobnej karcie.

## <a name="load-data-into-azure-data-explorer"></a>Ładowanie danych do usługi Azure Eksplorator danych

Dane można ładować z wielu typów [magazynów danych](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) do usługi Azure Eksplorator danych. Ten temat zawiera szczegółowe informacje dotyczące ładowania danych z usługi Amazon S3.

Istnieją dwa sposoby ładowania danych do usługi Azure Eksplorator danych przy użyciu Azure Data Factory:

* Interfejs użytkownika Azure Data Factory — [karta **autor**](/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory)
* [Narzędzie Azure Data Factory **Kopiowanie danych** ](/azure/data-factory/quickstart-create-data-factory-copy-data-tool) używane w tym artykule.

### <a name="copy-data-from-amazon-s3-source"></a>Kopiowanie danych z usługi Amazon S3 (Źródło)

1. **Na stronie Wprowadzenie** wybierz kafelek **Kopiowanie danych** , aby uruchomić narzędzie kopiowanie danych.

   ![Kafelek Narzędzia do kopiowania danych](media/data-factory-load-data/copy-data-tool-tile.png)

1. Na stronie **Właściwości** Określ **nazwę zadania** i wybierz przycisk **dalej**.

    ![Kopiuj ze źródeł właściwości](media/data-factory-load-data/copy-from-source.png)

1. Na stronie **Magazyn danych źródłowych** kliknij pozycję **+ Utwórz nowe połączenie**.

    ![Tworzenie połączenia z danymi źródłowymi](media/data-factory-load-data/source-create-connection.png)

1. Wybierz pozycję **Amazon S3**, a następnie wybierz pozycję **Kontynuuj** .

    ![Nowa połączona usługa](media/data-factory-load-data/amazons3-select-new-linked-service.png)

1. Na stronie **Nowa połączona usługa (Amazon S3)** wykonaj następujące czynności:

    ![Określ połączoną usługę Amazon S3](media/data-factory-load-data/amazons3-new-linked-service-properties.png)

    * Określ **nazwę** nowej połączonej usługi.
    * Wybierz pozycję **Połącz za pośrednictwem wartości środowiska Integration Runtime** z listy rozwijanej.
    * Określ wartość **identyfikatora klucza dostępu** .
    * Określ wartość **klucza dostępu tajnego** .
    * Wybierz pozycję **Testuj połączenie** , aby przetestować utworzone połączenie połączonej usługi.
    * Wybierz pozycję **Finish** (Zakończ).

1. Na stronie **Magazyn danych źródłowych** zobaczysz nowe połączenie usługi AmazonS31. Wybierz opcję **Dalej**.

   ![Utworzono połączenie z magazynem danych źródłowych](media/data-factory-load-data/source-data-store-created-connection.png)

1. Na stronie **Wybieranie pliku lub folderu wejściowego** :

    1. Przejdź do folderu/pliku, który chcesz skopiować. Wybierz folder/plik.
    1. W razie potrzeby wybierz zachowanie kopiowania. Nie sprawdzaj **kopii binarnej** .
    1. Wybierz opcję **Dalej**.

    ![Wybieranie pliku lub folderu wejściowego](media/data-factory-load-data/source-choose-input-file.png)

1. Na stronie **Ustawienia formatów plików** wybierz odpowiednie ustawienia dla pliku, a następnie kliknij przycisk **dalej**.

   ![Wybieranie pliku lub folderu wejściowego](media/data-factory-load-data/source-file-format-settings.png)

### <a name="copy-data-into-azure-data-explorer-destination"></a>Kopiowanie danych do usługi Azure Eksplorator danych (miejsce docelowe)

Utworzono nową połączoną usługę Azure Eksplorator danych w celu skopiowania danych do tabeli docelowej Eksplorator danych platformy Azure określonej poniżej.

#### <a name="create-the-azure-data-explorer-linked-service"></a>Tworzenie połączonej usługi Azure Eksplorator danych

1. Na stronie **docelowy magazyn danych** możesz użyć istniejącego połączenia z magazynem danych lub określić nowy magazyn danych, klikając pozycję **+ Utwórz nowe połączenie**.

    ![Strona Docelowy magazyn danych](media/data-factory-load-data/destination-create-connection.png)

1. Na stronie **Nowa połączona usługa** wybierz pozycję **Azure Eksplorator danych**, a następnie wybierz pozycję **Kontynuuj** .

    ![Wybierz pozycję Azure Eksplorator danych — Nowa połączona usługa](media/data-factory-load-data/adx-select-new-linked-service.png)

1. Na stronie **Nowa połączona usługa (Azure Eksplorator danych)** wykonaj następujące czynności:

    ![ADX nową połączoną usługę](media/data-factory-load-data/adx-new-linked-service.png)

   * Wybierz **nazwę** połączonej usługi Azure Eksplorator danych.
   * W **metodzie wyboru konta**: 
        * Wybierz przycisk radiowy **z subskrypcji platformy Azure** i wybierz konto **subskrypcji platformy Azure** . Następnie wybierz **klaster**. Należy zauważyć, że lista rozwijana będzie dotyczyć tylko klastrów należących do użytkownika.
        * Alternatywnie możesz wybrać przycisk radiowy **Enter ręcznie** i wprowadzić **punkt końcowy**.
    * Określ **dzierżawcę**.
    * Wprowadź **Identyfikator jednostki usługi**.
    * Wybierz przycisk **klucz jednostki usługi** i wprowadź **klucz jednostki usługi**.
    * Wybierz **bazę danych** z menu rozwijanego. Alternatywnie wybierz opcję **Edytuj** pole wyboru i wprowadź nazwę bazy danych.
    * Wybierz pozycję **Testuj połączenie** , aby przetestować utworzone połączenie połączonej usługi. Jeśli można nawiązać połączenie z instalatorem, zostanie wyświetlone zielonego **znacznika wyboru.**
    * Wybierz pozycję **Zakończ** , aby ukończyć tworzenie połączonej usługi.

    > [!NOTE]
    > Nazwa główna usługi jest używana przez Azure Data Factory, aby uzyskać dostęp do usługi Azure Eksplorator danych. W przypadku jednostki usługi [Utwórz nazwę główną usługi Azure Active Directory (Azure AD)](/azure-stack/operator/azure-stack-create-service-principals#manage-an-azure-ad-service-principal). Nie należy używać metody **Azure Key Vault** .

#### <a name="configure-the-azure-data-explorer-data-connection"></a>Konfigurowanie połączenia danych Eksplorator danych platformy Azure

1. Zostanie otwarty **docelowy magazyn danych** . Utworzone połączenie danych Eksplorator danych platformy Azure jest dostępne do użycia. Wybierz pozycję **dalej** , aby skonfigurować połączenie.

    ![Docelowy magazyn danych ADX](media/data-factory-load-data/destination-data-store.png)

1. W polu **Mapowanie tabeli**Ustaw nazwę tabeli docelowej i wybierz pozycję **dalej**.

    ![Mapowanie tabeli docelowego zestawu danych](media/data-factory-load-data/destination-dataset-table-mapping.png)

1. Na stronie **Mapowanie kolumn** :
    * Pierwsze mapowanie jest wykonywane przez ADF zgodnie z [mapowaniem schematu ADF](/azure/data-factory/copy-activity-schema-and-type-mapping)
        * Ustaw **mapowania kolumn** dla Azure Data Factory tabeli docelowej. Domyślne mapowanie jest wyświetlane z tabeli źródłowej w celu przestawiania ADF.
        * Usuń zaznaczenie kolumn, które nie muszą definiować mapowania kolumn.
    * Drugie mapowanie występuje, gdy te dane tabelaryczne zostaną pozyskane do Eksplorator danych platformy Azure. Mapowanie jest wykonywane zgodnie z [regułami mapowania CSV](/azure/kusto/management/mappings#csv-mapping). Należy pamiętać, że nawet jeśli dane źródłowe nie były w formacie CSV, funkcja ADF przeprzekonwertowała dane w formacie tabelarycznym, dlatego mapowanie CSV jest jedynym odpowiednim mapowaniem na tym etapie.
        * W obszarze **Właściwości ujścia usługi Azure Eksplorator danych (Kusto)** Dodaj odpowiednią **nazwę mapowania** pozyskiwania (opcjonalnie), aby można było użyć mapowania kolumn.
        * Jeśli **Nazwa mapowania** pozyskiwania nie jest określona, kolejność mapowania "według nazwy" zdefiniowana w sekcji **mapowania kolumn** zostanie wykonana. Jeśli mapowanie "według nazwy" nie powiedzie się, usługa Azure Eksplorator danych podejmie próbę pozyskania danych w kolejności "według pozycji kolumny" (mapowanie według pozycji jako domyślna).
    * Wybierz pozycję **Dalej**

    ![Mapowanie kolumn docelowego zestawu danych](media/data-factory-load-data/destination-dataset-column-mapping.png)

1. Na stronie **Ustawienia**:
    * Ustaw odpowiednie **Ustawienia odporności na uszkodzenia**.
    * **Ustawienia wydajności**: Włączanie przemieszczania nie ma zastosowania. **Ustawienia zaawansowane** obejmują zagadnienia dotyczące kosztów. Pozostaw tak, jakby nie konkretnymi potrzebami.
    * Wybierz opcję **Dalej**.

    ![Kopiuj ustawienia danych](media/data-factory-load-data/copy-data-settings.png)

1. W obszarze **Podsumowanie**przejrzyj ustawienia, a następnie wybierz przycisk **dalej**.

    ![Podsumowanie kopiowania danych](media/data-factory-load-data/copy-data-summary.png)

1. Na **stronie wdrażanie**:
    * Wybierz pozycję **monitor** , aby przełączyć się na kartę **monitor** i sprawdzić stan potoku (postęp, błędy i przepływ danych).
    * Wybierz pozycję **Edytuj potok** , aby edytować połączone usługi, zestawy danych i potoki.
    * Wybierz pozycję **Zakończ** , aby ukończyć zadanie kopiowania danych

    ![Strona Wdrażanie](media/data-factory-load-data/deployment.png)

## <a name="next-steps"></a>Następne kroki

* Więcej informacji na temat [łącznika usługi Azure Eksplorator danych](/azure/data-factory/connector-azure-data-explorer) w programie Azure Data Factory.

* Dowiedz się więcej o edytowaniu połączonych usług, zestawów danych i potoków w [interfejsie użytkownika Data Factory](/azure/data-factory/quickstart-create-data-factory-portal).

* Dowiedz się więcej na temat [zapytań dotyczących usługi Azure Eksplorator danych](/azure/data-explorer/web-query-data) na potrzeby zapytań dotyczących danych.
