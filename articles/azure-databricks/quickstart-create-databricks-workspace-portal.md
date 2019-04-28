---
title: 'Szybki start: uruchamianie zadania Spark w usłudze Azure Databricks przy użyciu witryny Azure Portal'
description: W tym przewodniku Szybki start pokazano, jak za pomocą witryny Azure Portal utworzyć obszar roboczy usługi Azure Databricks oraz klaster Apache Spark i uruchomić zadanie Spark.
services: azure-databricks
ms.service: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.workload: big-data
ms.topic: quickstart
ms.date: 07/23/2018
ms.custom: mvc
ms.openlocfilehash: 3a58a34271562b127735a4682046a7b646d0c085
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60771095"
---
# <a name="quickstart-run-a-spark-job-on-azure-databricks-using-the-azure-portal"></a>Szybki start: uruchamianie zadania Spark w usłudze Azure Databricks przy użyciu witryny Azure Portal

W tym przewodniku Szybki start pokazano, jak utworzyć obszar roboczy usługi Azure Databricks oraz klaster Apache Spark w tym obszarze roboczym. Na koniec wyjaśniono, jak uruchomić zadanie Spark w tym klastrze Databricks. Aby uzyskać więcej informacji o usłudze Azure Databricks, zobacz [Co to jest Azure Databricks](what-is-azure-databricks.md).

W tym przewodniku Szybki start w ramach zadania Spark przeanalizujesz dane subskrypcji kanałów radiowych, aby na podstawie danych demograficznych uzyskać szczegółowe informacje dotyczące korzystania z usług płatnych/bezpłatnych.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="log-in-to-the-azure-portal"></a>Logowanie do witryny Azure Portal

Zaloguj się do witryny [Azure Portal](https://portal.azure.com).

## <a name="create-an-azure-databricks-workspace"></a>Tworzenie obszaru roboczego usługi Azure Databricks

W tej sekcji utworzysz obszar roboczy usługi Azure Databricks przy użyciu witryny Azure Portal.

1. W witrynie Azure Portal wybierz pozycję **Utwórz zasób** > **Dane i analiza** > **Azure Databricks**.

    ![Usługa Databricks w witrynie Azure Portal](./media/quickstart-create-databricks-workspace-portal/azure-databricks-on-portal.png "Usługa Databricks w witrynie Azure Portal")

2. W obszarze **Usługa Azure Databricks** podaj wartości umożliwiające utworzenie obszaru roboczego usługi Databricks.

    ![Tworzenie obszaru roboczego usługi Azure Databricks](./media/quickstart-create-databricks-workspace-portal/create-databricks-workspace.png "Tworzenie obszaru roboczego usługi Azure Databricks")

    Podaj następujące wartości:
    
    |Właściwość  |Opis  |
    |---------|---------|
    |**Nazwa obszaru roboczego**     | Podaj nazwę obszaru roboczego usługi Databricks.        |
    |**Subskrypcja**     | Z listy rozwijanej wybierz subskrypcję platformy Azure.        |
    |**Grupa zasobów**     | Określ, czy chcesz utworzyć nową grupę zasobów, czy użyć istniejącej grupy. Grupa zasobów to kontener, który zawiera powiązane zasoby dla rozwiązania platformy Azure. Aby uzyskać więcej informacji, zobacz [Omówienie usługi Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). |
    |**Lokalizacja**     | Wybierz pozycję **East US 2** (Wschodnie stany USA 2). Inne dostępne regiony podano na stronie [dostępności usług platformy Azure według regionów](https://azure.microsoft.com/regions/services/).        |
    |**Warstwa cenowa**     |  Wybierz warstwę **Standardowa** lub **Premium**. Aby uzyskać więcej informacji o tych warstwach, zobacz [stronę usługi Databricks](https://azure.microsoft.com/pricing/details/databricks/).       |

    Zaznacz opcję **Przypnij do pulpitu nawigacyjnego**, a następnie kliknij pozycję **Utwórz**.

4. Tworzenie obszaru roboczego trwa kilka minut. Podczas tworzenia obszaru roboczego po prawej stronie portalu jest wyświetlany kafelek **Przesyłanie wdrożenia dla usługi Azure Databricks**. Aby go zobaczyć, być może trzeba będzie przesunąć pulpit nawigacyjny w prawo. W górnej części ekranu jest również wyświetlany pasek postępu. Postęp można obserwować w dowolnym z tych obszarów.

    ![Kafelek wdrażania usługi Databricks](./media/quickstart-create-databricks-workspace-portal/databricks-deployment-tile.png "Kafelek wdrażania usługi Databricks")

## <a name="create-a-spark-cluster-in-databricks"></a>Tworzenie klastra Spark w usłudze Databricks

> [!NOTE]
> Aby użyć bezpłatnego konta do utworzenia klastra usługi Azure Databricks, przed utworzeniem klastra przejdź do swojego profilu i zmień swoją subskrypcję na **płatność zgodnie z rzeczywistym użyciem**. Aby uzyskać więcej informacji, zobacz [Bezpłatne konto platformy Azure](https://azure.microsoft.com/free/).

1. W witrynie Azure Portal przejdź do utworzonego obszaru roboczego usługi Databricks, a następnie kliknij pozycję **Uruchom obszar roboczy**.

2. Nastąpi przekierowanie do portalu usługi Azure Databricks. W portalu kliknij pozycję **Klaster**.

    ![Usługa Databricks na platformie Azure](./media/quickstart-create-databricks-workspace-portal/databricks-on-azure.png "Usługa Databricks na platformie Azure")

3. Na stronie **Nowy klaster** podaj wartości, aby utworzyć klaster.

    ![Tworzenie klastra Spark usługi Databricks na platformie Azure](./media/quickstart-create-databricks-workspace-portal/create-databricks-spark-cluster.png "Tworzenie klastra Spark usługi Databricks na platformie Azure")

    Zaakceptuj pozostałe wartości domyślne poza następującymi:

   * Wprowadź nazwę klastra.
   * W tym artykule należy utworzyć klaster ze środowiskiem uruchomieniowym **4.0**.
   * Upewnij się, że jest zaznaczone pole wyboru **Zakończ po \_\_ min nieaktywności**. Podaj czas (w minutach), po jakim działanie klastra ma zostać zakończone, jeśli nie jest używany.
    
     Wybierz pozycję **Utwórz klaster**. Po uruchomieniu klastra możesz dołączyć do niego notesy i uruchamiać zadania Spark.

Aby uzyskać więcej informacji na temat tworzenia klastrów, zobacz [Create a Spark cluster in Azure Databricks](https://docs.azuredatabricks.net/user-guide/clusters/create.html) (Tworzenie klastra Spark w usłudze Azure Databricks).


## <a name="download-a-sample-data-file"></a>Pobieranie przykładowego pliku danych
Pobierz przykładowy plik danych JSON i zapisz go w usłudze Azure Blob Storage.

1. Pobierz ten przykładowy plik danych JSON [z serwisu GitHub](https://raw.githubusercontent.com/Azure/usql/master/Examples/Samples/Data/json/radiowebsite/small_radio_json.json) na komputer lokalny. Kliknij prawym przyciskiem myszy i wybierz polecenie Zapisz jako, aby lokalnie zapisać nieprzetworzony plik.

2. Jeśli jeszcze nie masz konta magazynu, utwórz je.
   - W witrynie Azure Portal wybierz polecenie **Utwórz zasób**. Wybierz kategorię **Storage**, a następnie wybierz zasób **Konta magazynu**.
   - Podaj unikatową nazwę konta magazynu.
   - Wybierz **Rodzaj konta**: **Blob Storage**
   - Wybierz nazwę **grupy zasobów**. Użyj tej samej grupy zasobów, w której utworzono obszar roboczy usługi Databricks.
    
     Aby uzyskać więcej informacji, zobacz [Tworzenie konta usługi Azure Blob Storage](../storage/common/storage-quickstart-create-account.md).

3. W ramach konta usługi Blob Storage utwórz kontener magazynu i przekaż do niego przykładowy plik json. Do przekazania pliku możesz użyć witryny Azure Portal lub [Eksploratora usługi Microsoft Azure Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md).

    - Otwórz konto magazynu w witrynie Azure Portal.
    - Wybierz pozycję **Obiekty blob**.
    - Wybierz pozycję **+ Kontener**, aby utworzyć nowy, pusty kontener.
    - W polu **Nazwa** podaj nazwę kontenera, na przykład `databricks`.
    - Wybierz poziom dostępu **Prywatny (bez anonimowego dostępu)**.
    - Po utworzeniu kontenera wybierz jego nazwę.
    - Wybierz przycisk **Przekaż**.
    - Na stronie **Pliki** wybierz **ikonę folderu**, a następnie znajdź i wybierz przykładowy plik `small_radio_json.json` do przekazania.
    - Wybierz pozycję **Przekaż**, aby przekazać plik.

## <a name="run-a-spark-sql-job"></a>Uruchamianie zadania Spark SQL
Wykonaj poniższe kroki, aby utworzyć notes w usłudze Databricks, skonfigurować odczytywanie za jego pomocą danych z konta usługi Azure Blob Storage, a następnie uruchomić zadanie Spark SQL na tych danych.

1. W okienku po lewej stronie kliknij pozycję **Obszar roboczy**. Na liście rozwijanej **Obszar roboczy** kliknij pozycję **Utwórz**, a następnie kliknij pozycję **Notes**.

    ![Tworzenie notesu w usłudze Databricks](./media/quickstart-create-databricks-workspace-portal/databricks-create-notebook.png "Tworzenie notesu w usłudze Databricks")

2. W oknie dialogowym **Tworzenie notesu** wpisz nazwę, wybierz jako język pozycję **Scala** i wybierz utworzony wcześniej klaster Spark.

    ![Tworzenie notesu w usłudze Databricks](./media/quickstart-create-databricks-workspace-portal/databricks-notebook-details.png "Tworzenie notesu w usłudze Databricks")

    Kliknij pozycję **Utwórz**.

3. W tym kroku skojarz konto usługi Azure Storage z klastrem Spark usługi Databricks. Istnieją dwa sposoby osiągnięcia tego: zainstalowanie konta usługi Azure Storage w systemie plików usługi Databricks (DBFS) lub bezpośredni dostęp do konta usługi Azure Storage z utworzonej aplikacji.

    > [!IMPORTANT]
    >W tym artykule wykorzystano podejście **instalowania magazynu z systemem plików DBFS**. Takie podejście zapewnia, że zainstalowany magazyn zostanie skojarzony z samym systemem plików klastra. W związku z tym każda aplikacja uzyskująca dostęp do klastra jest również w stanie skorzystać ze skojarzonego magazynu. Podejście bezpośredniego dostępu jest ograniczone do aplikacji, z poziomu której został skonfigurowany dostęp.
    >
    > Aby użyć podejścia instalowania, musisz utworzyć klaster Spark z wersją środowiska uruchomieniowego usługi Databricks **4.0**, które zostało wybrane w tym artykule.

    W poniższym fragmencie kodu zastąp `{YOUR CONTAINER NAME}`, `{YOUR STORAGE ACCOUNT NAME}` i `{YOUR STORAGE ACCOUNT ACCESS KEY}` odpowiednimi wartościami dla konta usługi Azure Storage. Wklej ten fragment w pustej komórce w notesie, a następnie naciśnij klawisze SHIFT+ENTER, aby uruchomić komórkę kodu.

   * **Zainstaluj konto magazynu z DBFS (zalecane)**. W tym fragmencie kodu ścieżka konta usługi Azure Storage jest instalowana w `/mnt/mypath`. Dlatego we wszystkich przyszłych wystąpieniach, gdzie uzyskujesz dostęp do konta usługi Azure Storage, nie trzeba podawać pełnej ścieżki. Wystarczy użyć `/mnt/mypath`.

         dbutils.fs.mount(
           source = "wasbs://{YOUR CONTAINER NAME}@{YOUR STORAGE ACCOUNT NAME}.blob.core.windows.net/",
           mountPoint = "/mnt/mypath",
           extraConfigs = Map("fs.azure.account.key.{YOUR STORAGE ACCOUNT NAME}.blob.core.windows.net" -> "{YOUR STORAGE ACCOUNT ACCESS KEY}"))

   * **Bezpośredni dostęp do konta magazynu**

         spark.conf.set("fs.azure.account.key.{YOUR STORAGE ACCOUNT NAME}.blob.core.windows.net", "{YOUR STORAGE ACCOUNT ACCESS KEY}")

     Aby uzyskać instrukcje dotyczące sposobu pobierania klucza konta magazynu, zobacz [Zarządzanie kluczami dostępu do magazynu](../storage/common/storage-account-manage.md#access-keys).

     > [!NOTE]
     > Z klastrem Spark w usłudze Azure Databricks można również używać usługi Azure Data Lake Store. Aby uzyskać instrukcje, zobacz [Use Data Lake Store with Azure Databricks](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html) (Używanie usługi Data Lake Store z usługą Azure Databricks).

4. Uruchom instrukcję SQL, aby utworzyć tabelę tymczasową przy użyciu danych z przykładowego pliku danych JSON, **small_radio_json.json**. W poniższym fragmencie kodu zamień wartości symboli zastępczych na właściwe nazwy kontenera i konta magazynu. Wklej ten fragment w komórce kodu w notesie, a następnie naciśnij klawisze SHIFT+ENTER. W tym fragmencie element `path` wskazuje lokalizację przykładowego pliku JSON przekazanego na konto usługi Azure Storage.

    ```sql
    %sql
    DROP TABLE IF EXISTS radio_sample_data;
    CREATE TABLE radio_sample_data
    USING json
    OPTIONS (
     path "/mnt/mypath/small_radio_json.json"
    )
    ```

    Po pomyślnym wykonaniu polecenia wszystkie dane z pliku JSON znajdą się w tabeli w klastrze usługi Databricks.

    Magiczne polecenie języka `%sql` umożliwia uruchamianie kodu SQL z notesu, nawet jeśli notes jest innego typu. Aby uzyskać więcej informacji, zobacz [Mixing languages in a notebook](https://docs.azuredatabricks.net/user-guide/notebooks/index.html#mixing-languages-in-a-notebook) (Łączenie języków w notesie).

5. Przyjrzyjmy się migawce przykładowych danych JSON, aby lepiej zrozumieć uruchamiane zapytanie. Wklej poniższy fragment kodu w komórce kodu i naciśnij klawisze **SHIFT+ENTER**.

    ```sql
    %sql
    SELECT * from radio_sample_data
    ```

6. Pojawią się tabelaryczne dane wyjściowe, takie jak pokazano na poniższym zrzucie ekranu (przedstawiono tu tylko niektóre kolumny):

    ![Przykładowe dane JSON](./media/quickstart-create-databricks-workspace-portal/databricks-sample-csv-data.png "Przykładowe dane JSON")

    W danych przykładowych jest rejestrowana między innymi płeć odbiorców kanału radiowego (nazwa kolumny **gender** — „płeć”) i informacja o tym, czy subskrypcja jest bezpłatna, czy płatna (nazwa kolumny **level** — „poziom”).

7. Teraz utworzysz reprezentację wizualną tych danych w celu pokazania, ilu użytkowników każdej płci ma konta bezpłatne, a ilu opłaca subskrypcję. U dołu tabelarycznych danych wyjściowych kliknij ikonę **Wykres słupkowy**, a następnie pozycję **Opcje wykresu**.

    ![Tworzenie wykresu słupkowego](./media/quickstart-create-databricks-workspace-portal/create-plots-databricks-notebook.png "Tworzenie wykresu słupkowego")

8. W obszarze **Dostosowywanie wykresu** przeciągnij i upuść wartości, jak pokazano na zrzucie ekranu.

    ![Dostosowywanie wykresu słupkowego](./media/quickstart-create-databricks-workspace-portal/databricks-notebook-customize-plot.png "Dostosowywanie wykresu słupkowego")

   * W obszarze **Klucze** ustaw wartość **gender** (płeć).
   * W obszarze **Grupowania serii** ustaw wartość **level** (poziom).
   * W obszarze **Wartości** ustaw wartość **level** (poziom).
   * W obszarze **Agregacja** ustaw wartość **LICZBA**.

     Kliknij przycisk **Zastosuj**.

9. Wynikiem będzie reprezentacja wizualna przedstawiona na zrzucie ekranu:

    ![Dostosowywanie wykresu słupkowego](./media/quickstart-create-databricks-workspace-portal/databricks-sql-query-output-bar-chart.png "Dostosowywanie wykresu słupkowego")

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy skończysz korzystać z tego artykułu, możesz zakończyć działanie klastra. Aby to zrobić, w obszarze roboczym usługi Azure Databricks wybierz pozycję **Klastry** w lewym okienku. W obszarze klastra, którego działanie chcesz zakończyć, przesuń kursor na wielokropek w kolumnie **Akcje**, a następnie wybierz ikonę **Zakończ**.

![Zatrzymywanie klastra usługi Databricks](./media/quickstart-create-databricks-workspace-portal/terminate-databricks-cluster.png "Zatrzymywanie klastra usługi Databricks")

Jeśli nie zakończysz działania klastra ręcznie, zostanie on automatycznie zatrzymany, o ile podczas tworzenia klastra zaznaczono pole wyboru **Zakończ po \_\_ min nieaktywności**. W takim przypadku nieaktywny klaster automatycznie zatrzymuje się po określonym czasie.

## <a name="next-steps"></a>Kolejne kroki

W tym artykule został utworzony klaster Spark w usłudze Azure Databricks i przy użyciu danych w usłudze Azure Storage zostało uruchomione zadanie Spark. Aby dowiedzieć się, jak zaimportować dane z innych źródeł danych do usługi Azure Databricks, zobacz [Spark data sources](https://docs.azuredatabricks.net/spark/latest/data-sources/index.html) (Źródła danych platformy Spark). Przejdź do następnego artykułu, aby dowiedzieć się, jak wykonać operację ETL (wyodrębnianie, przekształcanie i ładowanie danych) przy użyciu usługi Azure Databricks.

> [!div class="nextstepaction"]
>[Wyodrębnianie, przekształcanie i ładowanie danych przy użyciu usługi Azure Databricks](databricks-extract-load-sql-data-warehouse.md)
