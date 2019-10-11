---
title: Uruchamianie zadania Spark na Azure Databricks przy użyciu Azure Portal
description: W tym przewodniku szybki start pokazano, jak za pomocą Azure Portal utworzyć obszar roboczy Azure Databricks, klaster Apache Spark i uruchomić zadanie Spark.
services: azure-databricks
ms.service: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.workload: big-data
ms.topic: quickstart
ms.date: 05/08/2019
ms.custom: mvc
ms.openlocfilehash: 1cc6d6236b940cf898651bdfdd47f4db1ac53851
ms.sourcegitcommit: f272ba8ecdbc126d22a596863d49e55bc7b22d37
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2019
ms.locfileid: "72274172"
---
# <a name="quickstart-run-a-spark-job-on-azure-databricks-using-the-azure-portal"></a>Szybki Start: uruchamianie zadania Spark na Azure Databricks przy użyciu Azure Portal

W tym przewodniku szybki start użyjesz Azure Portal, aby utworzyć obszar roboczy Azure Databricks z klastrem Apache Spark. Uruchamiasz zadanie w klastrze i używasz niestandardowych wykresów do tworzenia raportów w czasie rzeczywistym na podstawie danych o bezpieczeństwie Boston.

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/)

## <a name="sign-in-to-the-azure-portal"></a>Zaloguj się do Azure Portal

Zaloguj się do [Azure Portal](https://portal.azure.com).

> [!Note]
> Tego samouczka nie można przeprowadzić za pomocą **subskrypcji bezpłatnej wersji próbnej platformy Azure**.
> Jeśli masz bezpłatne konto, przejdź do swojego profilu i Zmień subskrypcję na **płatność zgodnie z rzeczywistym**użyciem. Aby uzyskać więcej informacji, zobacz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/). Następnie [Usuń limit wydatków](https://docs.microsoft.com/azure/billing/billing-spending-limit#remove-the-spending-limit-in-account-center)i [Poproś o zwiększenie limitu przydziału](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) dla procesorów wirtualnych vCPU w Twoim regionie. Podczas tworzenia obszaru roboczego Azure Databricks możesz wybrać warstwę cenową **wersji próbnej (Premium-14-Days Free dBu)** , aby umożliwić dostęp do obszaru roboczego bezpłatnie Azure Databricks DBU przez 14 dni.

## <a name="create-an-azure-databricks-workspace"></a>Tworzenie obszaru roboczego Azure Databricks

W tej sekcji utworzysz obszar roboczy Azure Databricks przy użyciu Azure Portal.

1. W Azure Portal wybierz pozycję **Utwórz zasób** > **Analytics** > **Azure Databricks**.

    Kostki datacegły ![w Azure Portal](./media/quickstart-create-databricks-workspace-portal/azure-databricks-on-portal.png "datakosteks na Azure Portal")

2. W obszarze **usługa Azure Databricks**podaj wartości, aby utworzyć obszar roboczy datakostki.

    ![Utwórz obszar roboczy Azure Databricks](./media/quickstart-create-databricks-workspace-portal/create-databricks-workspace.png "Tworzenie Azure Databricks obszaru roboczego")

    Podaj następujące wartości:
    
    |Właściwość  |Opis  |
    |---------|---------|
    |**Nazwa obszaru roboczego**     | Podaj nazwę obszaru roboczego datakostki        |
    |**Ramach**     | Z listy rozwijanej wybierz subskrypcję platformy Azure.        |
    |**Grupa zasobów**     | Określ, czy chcesz utworzyć nową grupę zasobów, czy użyć istniejącej. Grupa zasobów to kontener, który zawiera powiązane zasoby dla rozwiązania platformy Azure. Aby uzyskać więcej informacji, zobacz [Omówienie grupy zasobów platformy Azure](../azure-resource-manager/resource-group-overview.md). |
    |**Lokalizacja**     | Wybierz pozycję **zachodnie stany USA 2**. W przypadku innych dostępnych regionów zobacz [usługi platformy Azure dostępne według regionów](https://azure.microsoft.com/regions/services/).        |
    |**Warstwa cenowa**     |  Wybierz warstwę **standardowa**, **Premium**lub **wersja próbna**. Aby uzyskać więcej informacji o tych warstwach, zobacz [stronę cennika usługi datacegły](https://azure.microsoft.com/pricing/details/databricks/).       |

    Wybierz pozycję **Przypnij do pulpitu nawigacyjnego** , a następnie kliknij pozycję **Utwórz**.

4. Tworzenie obszaru roboczego trwa kilka minut. Podczas tworzenia obszaru roboczego można wyświetlić stan wdrożenia w obszarze **powiadomienia**.

    ![](./media/quickstart-create-databricks-workspace-portal/databricks-deployment-tile.png "Kafelek") wdrożenia datakosteks Deployment

## <a name="create-a-spark-cluster-in-databricks"></a>Tworzenie klastra Spark w kostkach

> [!NOTE]
> Aby utworzyć klaster Azure Databricks przy użyciu bezpłatnego konta, przed utworzeniem klastra przejdź do swojego profilu i Zmień subskrypcję na **płatność zgodnie z rzeczywistym**użyciem. Aby uzyskać więcej informacji, zobacz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/).

1. W Azure Portal przejdź do utworzonego obszaru roboczego datakosteks, a następnie kliknij pozycję **Uruchom obszar roboczy**.

2. Nastąpi przekierowanie do portalu Azure Databricks. W portalu kliknij pozycję **nowy klaster**.

    ![Datakostki na]platformie Azure —(./media/quickstart-create-databricks-workspace-portal/databricks-on-azure.png "datakostki")

3. Na stronie **nowy klaster** podaj wartości, aby utworzyć klaster.

    ![Tworzenie klastra usługi datakosteks Spark na platformie Azure](./media/quickstart-create-databricks-workspace-portal/create-databricks-spark-cluster.png "Tworzenie klastra datakostek Spark na platformie Azure")

    Zaakceptuj wszystkie pozostałe wartości domyślne inne niż następujące:

   * Wprowadź nazwę klastra.
   * W tym artykule należy utworzyć klaster ze środowiskiem uruchomieniowym **5,2** .
   * Upewnij się, że zaznaczysz pole wyboru **Przerwij po \_ @ no__t-2 minut braku aktywności** . Podaj czas trwania (w minutach) działania klastra, Jeśli klaster nie jest używany.
    
     Wybierz pozycję **Utwórz klaster**. Po uruchomieniu klastra można dołączyć notesy do klastra i uruchamiać zadania platformy Spark.

Aby uzyskać więcej informacji na temat tworzenia klastrów, zobacz [Tworzenie klastra Spark w Azure Databricks](https://docs.azuredatabricks.net/user-guide/clusters/create.html).

## <a name="run-a-spark-sql-job"></a>Uruchamianie zadania Spark SQL

Wykonaj następujące zadania, aby utworzyć Notes w kostkach danych, skonfigurować Notes, aby odczytywać dane z otwartych zestawów danych platformy Azure, a następnie uruchomić zadanie Spark SQL na danych.

1. W lewym okienku wybierz pozycję **Azure Databricks**. Ze **wspólnych zadań**wybierz pozycję **Nowy Notes**.

    ![Tworzenie notesu w kostkach datakosteks](./media/quickstart-create-databricks-workspace-portal/databricks-create-notebook.png "Tworzenie notesu w kostkach")

2. W oknie dialogowym **Tworzenie notesu** wprowadź nazwę, wybierz opcję **Python** jako język i wybierz utworzony wcześniej klaster Spark.

    ![Tworzenie notesu w kostkach datakosteks](./media/quickstart-create-databricks-workspace-portal/databricks-notebook-details.png "Tworzenie notesu w kostkach")

    Wybierz pozycję **Utwórz**.

3. W tym kroku utworzysz ramkę Dataframe o bezpieczeństwie Boston z poziomu [otwartych zestawów danych platformy Azure](https://azure.microsoft.com/services/open-datasets/catalog/boston-safety-data/#AzureDatabricks)i używasz języka SQL do wykonywania zapytań dotyczących danych.

   Następujące polecenie ustawia informacje o dostępie do usługi Azure Storage. Wklej ten kod PySpark w pierwszej komórce i Użyj **klawiszy SHIFT + ENTER** , aby uruchomić kod.

   ```python
   blob_account_name = "azureopendatastorage"
   blob_container_name = "citydatacontainer"
   blob_relative_path = "Safety/Release/city=Boston"
   blob_sas_token = r"?st=2019-02-26T02%3A34%3A32Z&se=2119-02-27T02%3A34%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=XlJVWA7fMXCSxCKqJm8psMOh0W4h7cSYO28coRqF2fs%3D"
   ```

   Następujące polecenie umożliwia platformie Spark odczytywanie danych z magazynu obiektów BLOB zdalnie. Wklej ten kod PySpark do następnej komórki i Użyj **klawiszy SHIFT + ENTER** , aby uruchomić kod.

   ```python
   wasbs_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)
   spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name), blob_sas_token)
   print('Remote blob path: ' + wasbs_path)
   ```

   Następujące polecenie tworzy ramkę danych. Wklej ten kod PySpark do następnej komórki i Użyj **klawiszy SHIFT + ENTER** , aby uruchomić kod.

   ```python
   df = spark.read.parquet(wasbs_path)
   print('Register the DataFrame as a SQL temporary view: source')
   df.createOrReplaceTempView('source')
   ```

4. Uruchom instrukcję SQL zwracającą 10 najważniejszych wierszy danych z widoku tymczasowego o nazwie **Źródło**. Wklej ten kod PySpark do następnej komórki i Użyj **klawiszy SHIFT + ENTER** , aby uruchomić kod.

   ```python
   print('Displaying top 10 rows: ')
   display(spark.sql('SELECT * FROM source LIMIT 10'))
   ```

5. Zobaczysz tabelaryczne dane wyjściowe podobne do pokazanego na poniższym zrzucie ekranu (wyświetlane są tylko niektóre kolumny):

    ![]Przykładowe dane przykładowe danych(./media/quickstart-create-databricks-workspace-portal/databricks-sample-csv-data.png "JSON")

6. Teraz utworzysz wizualną reprezentację tych danych, aby pokazać, ile zdarzeń związanych z bezpieczeństwem jest raportowanych za pomocą aplikacji do łączenia się z aplikacjami i pracownikami, a nie innymi źródłami. W dolnej części danych wyjściowych tabelarycznych wybierz ikonę **wykresu słupkowego** , a następnie kliknij pozycję **Opcje**wykresów.

    ![Utwórz]wykres słupkowy(./media/quickstart-create-databricks-workspace-portal/create-plots-databricks-notebook.png "tworzenia wykresu słupkowego")

8. W obszarze **Dostosowywanie wykresu**, przeciągnij i upuść wartości, jak pokazano na zrzucie ekranu.

    ![Dostosuj wykres kołowy](./media/quickstart-create-databricks-workspace-portal/databricks-notebook-customize-plot.png "Dostosowywanie wykresu słupkowego")

   * Ustaw **klucze** na **Źródło**.
   * Ustaw **wartości** na **< \id >** .
   * Ustaw **agregację** na wartość **Count**.
   * Ustaw **Typ wyświetlania** na **Wykres kołowy**.

     Kliknij przycisk **Zastosuj**.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Po zakończeniu tego artykułu możesz zakończyć działanie klastra. W tym celu w obszarze roboczym Azure Databricks w okienku po lewej stronie wybierz pozycję **klastry**. W przypadku klastra, który chcesz zakończyć, przesuń kursor na wielokropek w kolumnie **Akcje** , a następnie wybierz ikonę **Zakończ** .

![Zatrzymaj klaster datakostki],(./media/quickstart-create-databricks-workspace-portal/terminate-databricks-cluster.png "Zatrzymaj klaster datakostki")

Jeśli klaster nie zostanie ręcznie zakończony, zostanie on automatycznie zatrzymany, pod warunkiem, że podczas tworzenia klastra zaznaczono pole wyboru **Przerwij po \_ @ no__t-2 min nieaktywności** . W takim przypadku klaster zostaje automatycznie zatrzymany, jeśli był nieaktywny przez określony czas.

## <a name="next-steps"></a>Następne kroki

W tym artykule został utworzony klaster Spark w Azure Databricks i uruchomiono zadanie Spark przy użyciu danych z usługi Azure Open DataSets. Możesz również sprawdzić [źródła danych Spark](https://docs.azuredatabricks.net/spark/latest/data-sources/index.html) , aby dowiedzieć się, jak importować dane z innych źródeł danych do Azure Databricks. Przejdź do następnego artykułu, aby dowiedzieć się, jak wykonać operację ETL (wyodrębnianie, przekształcanie i ładowanie danych) przy użyciu Azure Databricks.

> [!div class="nextstepaction"]
>[Wyodrębnianie, przekształcanie i ładowanie danych przy użyciu Azure Databricks](databricks-extract-load-sql-data-warehouse.md)
