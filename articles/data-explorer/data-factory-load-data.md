---
title: Kopiowanie danych z usługi Azure Data Factory do Eksploratora danych platformy Azure
description: W tym temacie nauczysz się pozyskiwania danych (załaduj) do Eksploratora danych Azure przy użyciu narzędzia do kopiowania usługi Azure Data Factory
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: 64856d53168a7676cf279da2d8675ce81e1985f7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60447878"
---
# <a name="copy-data-to-azure-data-explorer-using-azure-data-factory"></a>Kopiowanie danych do Eksploratora danych Azure przy użyciu usługi Azure Data Factory 

Eksplorator usługi Azure Data to szybki, w pełni zarządzana Usługa do analizowania danych w czasie rzeczywistym analizy dużych ilości danych przesyłanych strumieniowo z wielu źródeł, takich jak aplikacje, witryny sieci Web i urządzeń IoT. Iteracyjne eksplorować dane i zidentyfikować wzorce i anomalie, aby ulepszać produkty, chcę udoskonalić środowisko pracy klienta, monitorowanie urządzeń i operacji zwiększania. Eksploruj nowe pytania i uzyskuj odpowiedzi w ciągu minut. Usługa Azure Data Factory to usługa integracji danych w pełni zarządzane w chmurze. Korzystać z niej, aby wypełnić bazę danych Eksplorator danych Azure przy użyciu danych z istniejącego systemu i zaoszczędzić czas podczas tworzenia rozwiązań do analizy.

Usługa Azure Data Factory oferuje następujące korzyści dotyczące ładowania danych do Eksploratora danych usługi Azure:

* **Łatwe do skonfigurowania**: Intuicyjny Kreator w pięciu krokach z bez skryptu wymagane.
* **Szczegółowe dane sklepu obsługują**: Wbudowana obsługa bogaty zestaw w środowisku lokalnym i magazynami danych w chmurze. Aby uzyskać szczegółową listę, zobacz tabelę [obsługiwane magazyny danych](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats).
* **Bezpieczeństwo i zgodność**: Dane są przesyłane za pośrednictwem protokołu HTTPS lub usługi ExpressRoute. Obecność usługi global service gwarantuje, że danych nigdy nie opuszcza granicy geograficznej.
* **Wysoka wydajność**: Maksymalna szybkość ładowania danych do 1-GB/s do Eksploratora danych usługi Azure. Aby uzyskać więcej informacji, zobacz [wydajności działania kopiowania](/azure/data-factory/copy-activity-performance).

W tym artykule przedstawiono sposób użycia narzędzia do kopiowania danych w bazie wiedzy Data Factory do ładowania danych z usługi Amazon S3 do Eksploratora danych usługi Azure. Można wykonać podobne kroki, aby skopiować dane z innych magazynów danych, takich jak [usługi Azure Blob Storage](/azure/data-factory/connector-azure-blob-storage), [usługi Azure SQL Database](/azure/data-factory/connector-azure-sql-database), [Azure SQL Data Warehouse](/azure/data-factory/connector-azure-sql-data-warehouse), [Google BigQuery](/azure/data-factory/connector-google-bigquery),[Oracle](/azure/data-factory/connector-oracle), i [system plików](/azure/data-factory/connector-file-system).

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/).
* [Eksplorator danych usługi Azure klastra i bazy danych](create-cluster-database-portal.md)
* Źródło danych.

## <a name="create-a-data-factory"></a>Tworzenie fabryki danych

1. Wybierz **Utwórz zasób** przycisk (+) w lewym górnym rogu portalu > **Analytics** > **usługi Data Factory**.

   ![Tworzenie nowej fabryki danych](media/data-factory-load-data/create-adf.png)

1. W **nowa fabryka danych** strony, podaj wartości dla następujących pól, a następnie wybierz **Utwórz**.

    ![Strona Nowa fabryka danych](media/data-factory-load-data/my-new-data-factory.png)

    **Ustawienie**  | **Opis pola**
    |---|---|
    | **Nazwa** | Wprowadź unikatową w skali globalnej nazwę fabryki danych. Jeśli zostanie wyświetlony błąd *"Nazwa fabryki danych \"LoadADXDemo\" jest niedostępna"*, wprowadź inną nazwę dla fabryki danych. Reguły nazewnictwa artefaktów usługi Data Factory, zobacz [Data Factory — reguły nazewnictwa](/azure/data-factory/naming-rules).|
    | **Subskrypcja** | Wybieranie subskrypcji platformy Azure, w której chcesz utworzyć fabrykę danych. |
    | **Grupa zasobów** | Wybierz **Utwórz nową** i wprowadź nazwę nowej grupy zasobów. Wybierz **Użyj istniejącej**, jeśli masz istniejącą grupę zasobów. |
    | **Wersja** | Wybierz **V2** |
    | **Lokalizacja** | Wybierz lokalizację fabryki danych. Na liście rozwijanej są wyświetlane tylko obsługiwane lokalizacje. Magazyny danych, które są używane przez fabrykę danych może być w inne lokalizacje lub regiony. |
    | | |

1. Wybierz opcję powiadomienia na pasku narzędzi, aby monitorować proces tworzenia. Po utworzeniu, przejdź do usługi data factory, który został utworzony. **Usługi Data Factory** zostanie otwarta strona główna.

   ![Strona główna fabryki danych](media/data-factory-load-data/data-factory-home-page.png)

1. Wybierz **tworzenie i monitorowanie** Kafelek, aby uruchomić aplikację w osobnej karcie.

## <a name="load-data-into-azure-data-explorer"></a>Ładowanie danych do Eksploratora danych platformy Azure

Dane mogą być ładowane z wielu typów [magazyny danych](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) do Eksploratora danych usługi Azure. W tym temacie szczegółowo przedstawiono ładowania danych z usługi Amazon S3.

Istnieją dwa sposoby, aby załadować dane do Eksploratora danych Azure przy użyciu usługi Azure Data Factory:

* Interfejsu użytkownika w usłudze Azure Data Factory — [ **Autor** kartę](/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory)
* [Usługa Azure Data Factory **kopiowania danych** narzędzie](/azure/data-factory/quickstart-create-data-factory-copy-data-tool) używane w tym artykule.

### <a name="copy-data-from-amazon-s3-source"></a>Kopiowanie danych z usługi Amazon S3 (źródło)

1. W **zaczynajmy** wybierz opcję **kopiowania danych** Kafelek, aby uruchomić narzędzie do kopiowania danych.

   ![Skopiuj Kafelek narzędzia danych](media/data-factory-load-data/copy-data-tool-tile.png)

1. W **właściwości** określ **Nazwa zadania** i wybierz **dalej**.

    ![Skopiuj z właściwości źródła](media/data-factory-load-data/copy-from-source.png)

1. W **magazynu danych źródłowych** kliknij **+ Utwórz nowe połączenie**.

    ![Źródło danych, tworzenia połączenia](media/data-factory-load-data/source-create-connection.png)

1. Wybierz **Amazon S3**, a następnie wybierz pozycję **Kontynuuj**

    ![Nowa połączona usługa](media/data-factory-load-data/amazons3-select-new-linked-service.png)

1. W **nowa połączona usługa (Amazon S3)** wykonaj następujące czynności:

    ![Określ usługę Amazon S3 połączone](media/data-factory-load-data/amazons3-new-linked-service-properties.png)

    * Określ **nazwa** nowego połączoną usługę.
    * Wybierz **Połącz za pośrednictwem środowiska integration runtime** wartość z listy rozwijanej.
    * Określ **identyfikator klucza dostępu** wartość.
    * Określ **tajny klucz dostępu** wartość.
    * Wybierz **Testuj połączenie** Aby przetestować połączenie z połączonej usługi został utworzony.
    * Wybierz pozycję **Finish** (Zakończ).

1. W **magazynu danych źródłowych** stronie zostaną wyświetlone nowe połączenie AmazonS31. Wybierz opcję **Dalej**.

   ![Utworzono połączenie magazynu danych źródłowych](media/data-factory-load-data/source-data-store-created-connection.png)

1. W **Wybieranie pliku lub folderu wejściowego** strony:

    1. Przejdź do folderu/pliku, który chcesz skopiować. Wybieranie folderu/pliku.
    1. Wybierz opcję zachowania dotyczącego kopiowania zgodnie z potrzebami. Zachowaj **kopia binarna** niezaznaczone.
    1. Wybierz opcję **Dalej**.

    ![Wybieranie pliku lub folderu wejściowego](media/data-factory-load-data/source-choose-input-file.png)

1. W **formaty plików ustawień** strony wybierz odpowiednie ustawienia w pliku i kliknij pozycję **dalej**.

   ![Wybieranie pliku lub folderu wejściowego](media/data-factory-load-data/source-file-format-settings.png)

### <a name="copy-data-into-azure-data-explorer-destination"></a>Kopiowanie danych do Eksploratora danych platformy Azure (docelowy)

Azure Eksploratorze danych nowa połączona usługa jest tworzony do kopiowania danych do tabeli docelowej Azure Eksplorator danych (ujścia) wymienionymi poniżej.

1. W **docelowego magazynu danych** strony, możesz użyć istniejących danych przechowywać połączenia lub określić nowy magazyn danych, klikając **+ Utwórz nowe połączenie**.

    ![Strona Docelowy magazyn danych](media/data-factory-load-data/destination-create-connection.png)

1. W **nowa połączona usługa** wybierz opcję **Eksploratora danych usługi Azure**, a następnie wybierz pozycję **Kontynuuj**

    ![Wybierz pozycję Azure Eksplorator danych — nowa połączona usługa](media/data-factory-load-data/adx-select-new-linked-service.png)

1. W **nowa połączona usługa (Eksplorator danych platformy Azure)** wykonaj następujące czynności:

    ![Nowa połączona usługa ADX](media/data-factory-load-data/adx-new-linked-service.png)

   * Wybierz **nazwa** Eksplorator danych platformy Azure połączonej usługi.
   * W **Metoda wyboru konta**: 
        * Wybierz **subskrypcji platformy Azure z** przycisk radiowy, a następnie wybierz swoje **subskrypcji platformy Azure** konta. Następnie wybierz swoje **klastra**. Uwaga na liście rozwijanej będą tylko wyświetlanie listy klastrów, które należą do użytkownika.
        * Można także wybrać **ręcznie wprowadzić** przycisk radiowy, a następnie wprowadź swoje **punktu końcowego**.
    * Określ **dzierżawy**.
    * Wprowadź **identyfikator podmiotu zabezpieczeń usługi**.
    * Wybierz **klucz_jednostki_usługi** przycisk, a następnie wprowadź **klucz jednostki usługi**.
    * Wybierz swoje **bazy danych** z menu rozwijanego. Można także wybrać **Edytuj** pole wyboru i wprowadź nazwę bazy danych.
    * Wybierz **Testuj połączenie** Aby przetestować połączenie z połączonej usługi został utworzony. Jeśli możesz połączyć z konfiguracją zielony znacznik wyboru **Łączenie przebiegło pomyślnie** będą wyświetlane.
    * Wybierz **Zakończ** wymagana do ukończenia tworzenia połączonej usługi.

    > [!NOTE]
    > Nazwa główna usługi jest używana przez usługę Azure Data Factory dostęp do usługi Azure Eksploratora danych. Dla jednostki usługi [Tworzenie jednostki usługi Azure Active Directory (Azure AD)](/azure/azure-stack/azure-stack-create-service-principals#manage-service-principal-for-azure-ad). Nie używaj **usługi Azure Key Vault** metody.

1. **Docelowego magazynu danych** zostanie otwarty. Utworzone połączenie danych Eksploratora danych usługi Azure jest dostępna do użycia. Wybierz **dalej** do skonfigurowania połączenia.

    ![ADX docelowego magazynu danych](media/data-factory-load-data/destination-data-store.png)

1. W **Mapowanie tabeli**, ustaw nazwę tabeli docelowej i wybierz **dalej**.

    ![Docelowy zestaw danych tabeli mapowania](media/data-factory-load-data/destination-dataset-table-mapping.png)

1. W **mapowania kolumn** strony:
    * Pierwszy mapowanie jest wykonywane przez usługę ADF zgodnie z opisem w [ADF mapowanie schematu](/azure/data-factory/copy-activity-schema-and-type-mapping)
        * Ustaw **mapowania kolumn** dla tabeli docelowej usługi Azure Data Factory. Domyślne mapowanie jest wyświetlany ze źródła do tabeli docelowej ADF.
        * Usuń zaznaczenie kolumn, których nie trzeba zdefiniować mapowanie kolumny.
    * Drugi mapowanie występuje, gdy dane tabelaryczne są pozyskiwane do Eksploratora danych usługi Azure. Mapowanie odbywa się zgodnie z opisem w [reguły mapowania CSV](/azure/kusto/management/mappings#csv-mapping). Należy pamiętać, że nawet wtedy, gdy źródło danych nie jest w formacie CSV, ADF ma przekształcone dane w formacie tabelarycznym w związku z tym, mapowanie CSV jest tylko odpowiednie mapowanie na tym etapie.
        * W obszarze **właściwości ujścia Azure Eksplorator danych (Kusto)** dodać odpowiedni **Nazwa mapowania pozyskiwania** (opcjonalnie) to mapowanie kolumny może służyć.
        * Jeśli **Nazwa mapowania pozyskiwania** nie jest określona, kolejność mapowania "by-name" zdefiniowana w **mapowania kolumn** sekcji zostanie przeprowadzona. W przypadku niepowodzenia "by-name" mapowanie Eksploratora danych usługi Azure podejmie próbę pozyskiwać dane w kolejności "według kolumny pozycji" (mapy według pozycji jako domyślną).
    * Wybierz **dalej**

    ![Mapowanie kolumny zestawu danych docelowego](media/data-factory-load-data/destination-dataset-column-mapping.png)

1. Na stronie **Ustawienia**:
    * Ustaw odpowiednią **odporność na uszkodzenia ustawienia**.
    * **Ustawienia wydajności**: Włącz przemieszczania nie ma zastosowania. **Zaawansowane ustawienia** zagadnienia kosztów. Pozostaw jako jest, jeśli nie specyficzne potrzeby.
    * Wybierz opcję **Dalej**.

    ![Kopiowanie ustawień danych](media/data-factory-load-data/copy-data-settings.png)

1. W **Podsumowanie**, przejrzyj ustawienia i wybierz **dalej**.

    ![Kopiowanie danych podsumowania](media/data-factory-load-data/copy-data-summary.png)

1. W **strony wdrażania**:
    * Wybierz **Monitor** Aby przełączyć się do **Monitor** karty i wyświetlany jest stan potoku (przepływ postępu, błędy i dane).
    * Wybierz **Edytuj potoku** edytować połączone usługi, zestawy danych i potoki.
    * Wybierz **Zakończ** do pełnej kopii danych zadania

    ![Strona Wdrażanie](media/data-factory-load-data/deployment.png)

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o [Eksploratora usługi Azure Data connector](/azure/data-factory/connector-azure-data-explorer) w usłudze Azure Data Factory.

* Dowiedz się więcej o edytowaniu połączone usługi, zestawy danych i potoki w [interfejs użytkownika usługi Data Factory](/azure/data-factory/quickstart-create-data-factory-portal).

* Dowiedz się więcej o [zapytań Eksploratora danych usługi Azure](/azure/data-explorer/web-query-data) danych podczas wykonywania zapytań.
