---
title: Tworzenie obszaru roboczego Azure Databricks za pomocą usługi Azure Resource Manager
description: W tym przewodniku szybki start pokazano, jak za pomocą szablonu Azure Resource Manager utworzyć Azure Databricks obszar roboczy, utworzyć klaster Apache Spark i uruchomić zadanie Spark.
services: azure-databricks
ms.service: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.workload: big-data
ms.topic: quickstart
ms.custom: mvc
ms.date: 07/12/2019
ms.openlocfilehash: 5f22c9e7b7f040a0e6b957cbc184720918ce9222
ms.sourcegitcommit: f272ba8ecdbc126d22a596863d49e55bc7b22d37
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2019
ms.locfileid: "72274146"
---
# <a name="quickstart-run-a-spark-job-on-azure-databricks-using-the-azure-resource-manager-template"></a>Szybki Start: uruchamianie zadania Spark na Azure Databricks przy użyciu szablonu Azure Resource Manager

W tym przewodniku szybki start użyjesz szablonu Azure Resource Manager, aby utworzyć obszar roboczy Azure Databricks z klastrem Apache Spark. Uruchamiasz zadanie w klastrze i korzystasz z wykresów niestandardowych do tworzenia raportów w czasie rzeczywistym na podstawie bezpłatnych i płatnych zastosowań na mocy demograficznej.

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/)

## <a name="sign-in-to-the-azure-portal"></a>Zaloguj się do Azure Portal

Zaloguj się do [Azure Portal](https://portal.azure.com). 

> [!Note]
> Tego samouczka nie można przeprowadzić za pomocą **subskrypcji bezpłatnej wersji próbnej platformy Azure**.
> Jeśli masz bezpłatne konto, przejdź do swojego profilu i Zmień subskrypcję na **płatność zgodnie z rzeczywistym**użyciem. Aby uzyskać więcej informacji, zobacz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/). Następnie [Usuń limit wydatków](https://docs.microsoft.com/azure/billing/billing-spending-limit#remove-the-spending-limit-in-account-center)i [Poproś o zwiększenie limitu przydziału](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) dla procesorów wirtualnych vCPU w Twoim regionie. Podczas tworzenia obszaru roboczego Azure Databricks możesz wybrać warstwę cenową **wersji próbnej (Premium-14-Days Free dBu)** , aby umożliwić dostęp do obszaru roboczego bezpłatnie Azure Databricks DBU przez 14 dni.

## <a name="create-an-azure-databricks-workspace"></a>Tworzenie obszaru roboczego Azure Databricks

W tej sekcji utworzysz obszar roboczy Azure Databricks przy użyciu szablonu Azure Resource Manager.

1. Kliknij poniższy obraz, aby otworzyć szablon w Azure Portal.

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-databricks-workspace%2Fazuredeploy.json" target="_blank"><img src="./media/quickstart-create-databricks-workspace-resource-manager-template/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Podaj wymagane wartości, aby utworzyć obszar roboczy Azure Databricks

   ![Tworzenie obszaru roboczego Azure Databricks przy użyciu szablonu Azure Resource Manager](./media/quickstart-create-databricks-workspace-resource-manager-template/create-databricks-workspace-using-resource-manager-template.png "tworzenie obszaru roboczego Azure Databricks przy użyciu szablonu Azure Resource Manager")

   Podaj następujące wartości:

   |Właściwość  |Opis  |
   |---------|---------|
   |**Ramach**     | Z listy rozwijanej wybierz subskrypcję platformy Azure.        |
   |**Grupa zasobów**     | Określ, czy chcesz utworzyć nową grupę zasobów, czy użyć istniejącej. Grupa zasobów to kontener, który zawiera powiązane zasoby dla rozwiązania platformy Azure. Aby uzyskać więcej informacji, zobacz [Omówienie grupy zasobów platformy Azure](../azure-resource-manager/resource-group-overview.md). |
   |**Lokalizacja**     | Wybierz pozycję **Wschodnie stany USA 2**. W przypadku innych dostępnych regionów zobacz [usługi platformy Azure dostępne według regionów](https://azure.microsoft.com/regions/services/).        |
   |**Nazwa obszaru roboczego**     | Podaj nazwę obszaru roboczego datakostki        |
   |**Warstwa cenowa**     |  Wybierz warstwę **standardowa** lub **Premium**. Aby uzyskać więcej informacji o tych warstwach, zobacz [stronę cennika usługi datacegły](https://azure.microsoft.com/pricing/details/databricks/).       |

3. Wybierz pozycję **Zgadzam się na powyższe warunki i**postanowienia, wybierz pozycję **Przypnij do pulpitu nawigacyjnego**, a następnie kliknij przycisk **Kup**.

4. Tworzenie obszaru roboczego trwa kilka minut. Podczas tworzenia obszaru roboczego w portalu jest wyświetlany kafelek **przesyłanie wdrożenia dla Azure Databricks** po prawej stronie. Może być konieczne przewinięcie w prawo na pulpicie nawigacyjnym, aby zobaczyć kafelek. W górnej części ekranu jest również wyświetlany pasek postępu. Możesz obejrzeć każdy obszar do postępu.

   ![](./media/quickstart-create-databricks-workspace-resource-manager-template/databricks-deployment-tile.png "Kafelek") wdrożenia datakosteks Deployment

## <a name="create-a-spark-cluster-in-databricks"></a>Tworzenie klastra Spark w kostkach

1. W Azure Portal przejdź do utworzonego obszaru roboczego datakosteks, a następnie kliknij pozycję **Uruchom obszar roboczy**.

2. Nastąpi przekierowanie do portalu Azure Databricks. W portalu kliknij pozycję **klaster**.

   ![Datakostki na]platformie Azure —(./media/quickstart-create-databricks-workspace-resource-manager-template/databricks-on-azure.png "datakostki")

3. Na stronie **nowy klaster** podaj wartości, aby utworzyć klaster.

   ![Tworzenie klastra usługi datakosteks Spark na platformie Azure](./media/quickstart-create-databricks-workspace-resource-manager-template/create-databricks-spark-cluster.png "Tworzenie klastra datakostek Spark na platformie Azure")

   Zaakceptuj wszystkie pozostałe wartości domyślne inne niż następujące:

   * Wprowadź nazwę klastra.
   * W tym artykule należy utworzyć klaster ze środowiskiem uruchomieniowym **4,0** .
   * Upewnij się, że zaznaczysz pole wyboru **Przerwij po \_ @ no__t-2 minut braku aktywności** . Podaj czas trwania (w minutach) działania klastra, Jeśli klaster nie jest używany.

   Wybierz pozycję **Utwórz klaster**. Po uruchomieniu klastra można dołączyć notesy do klastra i uruchamiać zadania platformy Spark.

Aby uzyskać więcej informacji na temat tworzenia klastrów, zobacz [Tworzenie klastra Spark w Azure Databricks](https://docs.azuredatabricks.net/user-guide/clusters/create.html).

## <a name="run-a-spark-sql-job"></a>Uruchamianie zadania Spark SQL

Przed rozpoczęciem pracy z tą sekcją należy spełnić następujące wymagania wstępne:

* [Utwórz konto usługi Azure Blob Storage](../storage/common/storage-quickstart-create-account.md).
* Pobierz przykładowy plik JSON [z usługi GitHub](https://github.com/Azure/usql/blob/master/Examples/Samples/Data/json/radiowebsite/small_radio_json.json).
* Przekaż przykładowy plik JSON na utworzone konto usługi Azure Blob Storage. Aby przekazać pliki, można użyć [Eksplorator usługi Microsoft Azure Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md) .

Wykonaj następujące zadania, aby utworzyć Notes w kostkach danych, skonfigurować Notes tak, aby odczytywać dane z konta usługi Azure Blob Storage, a następnie uruchomić zadanie Spark SQL na danych.

1. W lewym okienku kliknij pozycję **obszar roboczy**. Z listy rozwijanej **obszar roboczy** kliknij pozycję **Utwórz**, a następnie kliknij pozycję **Notes**.

   ![Tworzenie notesu w kostkach datakosteks](./media/quickstart-create-databricks-workspace-resource-manager-template/databricks-create-notebook.png "Tworzenie notesu w kostkach")

2. W oknie dialogowym **Tworzenie notesu** wprowadź nazwę, wybierz pozycję **Scala** jako język i wybierz utworzony wcześniej klaster Spark.

   ![Tworzenie notesu w kostkach datakosteks](./media/quickstart-create-databricks-workspace-resource-manager-template/databricks-notebook-details.png "Tworzenie notesu w kostkach")

   Kliknij przycisk **Utwórz**.

3. W tym kroku należy skojarzyć konto usługi Azure Storage z klastrem Spark. Istnieją dwa sposoby osiągnięcia tego skojarzenia. Konto usługi Azure Storage można zainstalować w systemie plików datakostki (DBFS) lub bezpośrednio uzyskać dostęp do konta usługi Azure Storage z utworzonej aplikacji.

   > [!IMPORTANT]
   >W tym artykule opisano sposób **instalowania magazynu za pomocą DBFS**. Takie podejście zapewnia, że zainstalowany magazyn jest skojarzony z systemem plików klastra. W związku z tym wszystkie aplikacje uzyskujący dostęp do klastra mogą również używać skojarzonego magazynu. Podejście bezpośredniego dostępu jest ograniczone do aplikacji, z której można skonfigurować dostęp.
   >
   > Aby użyć podejścia montażowego, należy utworzyć klaster Spark z środowiskiem uruchomieniowym datakostks w wersji **4,0**, która jest wybrana w tym artykule.

   W poniższym fragmencie kodu Zastąp `{YOUR CONTAINER NAME}`, `{YOUR STORAGE ACCOUNT NAME}` i `{YOUR STORAGE ACCOUNT ACCESS KEY}` odpowiednimi wartościami dla konta usługi Azure Storage. Wklej fragment kodu w pustej komórce w notesie, a następnie naciśnij klawisze SHIFT + ENTER, aby uruchomić komórkę kodu.

   * **Zainstaluj konto magazynu za pomocą DBFS (zalecane)** . W tym fragmencie kodu ścieżka konta usługi Azure Storage jest instalowana do `/mnt/mypath`. Tak więc we wszystkich przyszłych wystąpieniach, w których masz dostęp do konta usługi Azure Storage, nie musisz podawać pełnej ścieżki. Możesz użyć tylko `/mnt/mypath`.

          dbutils.fs.mount(
            source = "wasbs://{YOUR CONTAINER NAME}@{YOUR STORAGE ACCOUNT NAME}.blob.core.windows.net/",
            mountPoint = "/mnt/mypath",
            extraConfigs = Map("fs.azure.account.key.{YOUR STORAGE ACCOUNT NAME}.blob.core.windows.net" -> "{YOUR STORAGE ACCOUNT ACCESS KEY}"))

   * **Bezpośredni dostęp do konta magazynu**

          spark.conf.set("fs.azure.account.key.{YOUR STORAGE ACCOUNT NAME}.blob.core.windows.net", "{YOUR STORAGE ACCOUNT ACCESS KEY}")

     Aby uzyskać instrukcje dotyczące pobierania klucza konta magazynu, zobacz [Zarządzanie kluczami dostępu do magazynu](../storage/common/storage-account-manage.md#access-keys).

   > [!NOTE]
   > Można również użyć Azure Data Lake Store z klastrem Spark w Azure Databricks. Aby uzyskać instrukcje, zobacz [używanie Data Lake Store z Azure Databricks](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html).

4. Uruchom instrukcję SQL, aby utworzyć tabelę tymczasową przy użyciu danych z przykładowego pliku danych JSON, **small_radio_json. JSON**. W poniższym fragmencie kodu Zastąp wartości symboli zastępczych nazwą kontenera i nazwą konta magazynu. Wklej ten fragment w komórce kodu w notesie, a następnie naciśnij klawisze SHIFT + ENTER. W tym fragmencie kodu `path` oznacza lokalizację przykładowego pliku JSON, który został przekazany do konta usługi Azure Storage.

   ```sql
   %sql
   DROP TABLE IF EXISTS radio_sample_data;
   CREATE TABLE radio_sample_data
   USING json
   OPTIONS (
    path "/mnt/mypath/small_radio_json.json"
   )
   ```

   Po pomyślnym zakończeniu wykonywania polecenia wszystkie dane z pliku JSON są dostępne jako tabela w klastrze datakostków.

   Polecenie Magic języka `%sql` umożliwia uruchamianie kodu SQL z notesu, nawet jeśli Notes jest innego typu. Aby uzyskać więcej informacji, zobacz [mieszanie języków w notesie](https://docs.azuredatabricks.net/user-guide/notebooks/index.html#mixing-languages-in-a-notebook).

5. Przyjrzyjmy się migawce przykładowych danych JSON, aby lepiej zrozumieć uruchomione zapytanie. Wklej poniższy fragment kodu w komórce Code i naciśnij klawisze **SHIFT + ENTER**.

   ```sql
   %sql
   SELECT * from radio_sample_data
   ```

6. Zobaczysz tabelaryczne dane wyjściowe podobne do pokazanego na poniższym zrzucie ekranu (wyświetlane są tylko niektóre kolumny):

   Przykładowe dane JSON(./media/quickstart-create-databricks-workspace-resource-manager-template/databricks-sample-csv-data.png "przykładowe") ![dane JSON]

   Wśród innych szczegółów przykładowe dane przechwytuje płeć odbiorców kanału radiowego (nazwa kolumny, **płeć**) i czy ich subskrypcja jest bezpłatna lub płatna (nazwa kolumny, **poziom**).

7. Teraz utworzysz wizualną reprezentację tych danych w celu wyświetlenia dla każdej płci, ilu użytkowników ma bezpłatne konta i ile jest płatnych subskrybentów. W dolnej części danych wyjściowych tabelarycznych kliknij ikonę **Wykres słupkowy** , a następnie kliknij pozycję **Opcje**wykresu.

   ![Utwórz]wykres słupkowy(./media/quickstart-create-databricks-workspace-resource-manager-template/create-plots-databricks-notebook.png "tworzenia wykresu słupkowego")

8. W obszarze **Dostosowywanie wykresu**, przeciągnij i upuść wartości, jak pokazano na zrzucie ekranu.

   ![Dostosowywanie wykresu słupkowego](./media/quickstart-create-databricks-workspace-resource-manager-template/databricks-notebook-customize-plot.png "Dostosowywanie wykresu słupkowego")

   * Ustaw **klucze** na **płeć**.
   * Ustaw **grupowanie serii** na **poziom**.
   * Ustaw **wartości** na **poziom**.
   * Ustaw **agregację** na wartość **Count**.

   Kliknij przycisk **Zastosuj**.

9. Dane wyjściowe przedstawiają reprezentację wizualną, jak przedstawiono na poniższym zrzucie ekranu:

   ![Dostosowywanie wykresu słupkowego](./media/quickstart-create-databricks-workspace-resource-manager-template/databricks-sql-query-output-bar-chart.png "Dostosowywanie wykresu słupkowego")

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Po zakończeniu tego artykułu możesz zakończyć działanie klastra. W tym celu w obszarze roboczym Azure Databricks w okienku po lewej stronie wybierz pozycję **klastry**. W przypadku klastra, który chcesz zakończyć, przesuń kursor na wielokropek w kolumnie **Akcje** , a następnie wybierz ikonę **Zakończ** .

![Zatrzymaj klaster datakostki],(./media/quickstart-create-databricks-workspace-resource-manager-template/terminate-databricks-cluster.png "Zatrzymaj klaster datakostki")

Jeśli klaster nie zostanie ręcznie zakończony, zostanie on automatycznie zatrzymany, pod warunkiem, że podczas tworzenia klastra zaznaczono pole wyboru **Przerwij po \_ @ no__t-2 min nieaktywności** . W takim przypadku klaster zostaje automatycznie zatrzymany, jeśli był nieaktywny przez określony czas.

## <a name="next-steps"></a>Następne kroki

W tym artykule opisano tworzenie klastra Spark w Azure Databricks i uruchamianie zadania Spark przy użyciu danych w usłudze Azure Storage. Możesz również sprawdzić [źródła danych Spark](https://docs.azuredatabricks.net/spark/latest/data-sources/index.html) , aby dowiedzieć się, jak importować dane z innych źródeł danych do Azure Databricks. Możesz również przyjrzeć się szablonowi Menedżer zasobów, aby [utworzyć obszar roboczy Azure Databricks z niestandardowym adresem sieci wirtualnej](https://github.com/Azure/azure-quickstart-templates/tree/master/101-databricks-workspace-with-custom-vnet-address). Aby uzyskać informacje o składni i właściwościach JSON do użycia w szablonie, zobacz artykuł [Microsoft. datacegły/obszary robocze](/azure/templates/microsoft.databricks/workspaces) .

Przejdź do następnego artykułu, aby dowiedzieć się, jak wykonać operację ETL (wyodrębnianie, przekształcanie i ładowanie danych) przy użyciu Azure Databricks.

> [!div class="nextstepaction"]
> [Wyodrębnianie, przekształcanie i ładowanie danych przy użyciu Azure Databricks](databricks-extract-load-sql-data-warehouse.md)
