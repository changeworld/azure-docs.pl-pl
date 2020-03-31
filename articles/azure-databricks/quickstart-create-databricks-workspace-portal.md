---
title: Szybki start — uruchamianie zadania platformy Spark na platformie Azure Databricks przy użyciu witryny Azure portal
description: Ten przewodnik Szybki start pokazuje, jak używać witryny Azure Portal do tworzenia obszaru roboczego usługi Azure Databricks, klastra Platformy Spark Apache i uruchamiania zadania platformy Spark.
services: azure-databricks
ms.service: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.workload: big-data
ms.topic: quickstart
ms.date: 03/23/2020
ms.custom: mvc
ms.openlocfilehash: d6af521238a034bc22612335119f08284b87eb4b
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80132707"
---
# <a name="quickstart-run-a-spark-job-on-azure-databricks-using-the-azure-portal"></a>Szybki start: uruchamianie zadania Spark w usłudze Azure Databricks przy użyciu witryny Azure Portal

W tym przewodniku Szybki start można użyć witryny Azure portal do utworzenia obszaru roboczego usługi Azure Databricks z klastrem Platformy Spark Apache. Uruchom zadanie w klastrze i używasz niestandardowych wykresów do tworzenia raportów w czasie rzeczywistym z danych bezpieczeństwa Bostonu.

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure — [utwórz bezpłatną subskrypcję](https://azure.microsoft.com/free/)

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się do [Portalu Azure](https://portal.azure.com).

> [!Note]
> Tego samouczka nie można przeprowadzić przy użyciu **bezpłatnej subskrypcji próbnej platformy Azure.**
> Jeśli masz darmowe konto, przejdź do swojego profilu i zmień subskrypcję na **płatność zgodnie z rzeczywistymami.** Aby uzyskać więcej informacji, zobacz [Bezpłatne konto platformy Azure](https://azure.microsoft.com/free/). Następnie [usuń limit wydatków](https://docs.microsoft.com/azure/billing/billing-spending-limit#why-you-might-want-to-remove-the-spending-limit)i [poproś o zwiększenie przydziału](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) dla procesorów wirtualnych w twoim regionie. Podczas tworzenia obszaru roboczego usługi Azure Databricks, można wybrać **warstwę cenową trial (Premium — 14-dniowe bezpłatne dbu),** aby dać obszarowi roboczemu dostęp do bezpłatnych witryn dbu usługi Premium Azure Databricks przez 14 dni.

## <a name="create-an-azure-databricks-workspace"></a>Tworzenie obszaru roboczego usługi Azure Databricks

W tej sekcji utworzysz obszar roboczy usługi Azure Databricks przy użyciu witryny Azure Portal.

1. W witrynie Azure portal wybierz pozycję **Utwórz zasób** > **Analytics** > **Azure Databricks**.

    ![Databricks w witrynie Azure portal](./media/quickstart-create-databricks-workspace-portal/azure-databricks-on-portal.png "Databricks w witrynie Azure portal")

2. W obszarze **Usługa Azure Databricks** podaj wartości umożliwiające utworzenie obszaru roboczego usługi Databricks.

    ![Tworzenie obszaru roboczego usługi Azure Databricks](./media/quickstart-create-databricks-workspace-portal/create-databricks-workspace.png "Tworzenie obszaru roboczego usługi Azure Databricks")

    Podaj następujące wartości:
    
    |Właściwość  |Opis  |
    |---------|---------|
    |**Nazwa obszaru roboczego**     | Podaj nazwę obszaru roboczego usługi Databricks.        |
    |**Subskrypcja**     | Z listy rozwijanej wybierz subskrypcję platformy Azure.        |
    |**Grupa zasobów**     | Określ, czy chcesz utworzyć nową grupę zasobów, czy użyć istniejącej grupy. Grupa zasobów to kontener, który zawiera powiązane zasoby dla rozwiązania platformy Azure. Aby uzyskać więcej informacji, zobacz [Omówienie usługi Azure Resource Manager](../azure-resource-manager/management/overview.md). |
    |**Lokalizacja**     | Wybierz pozycję **Zachodnie stany USA 2**. Inne dostępne regiony podano na stronie [dostępności usług platformy Azure według regionów](https://azure.microsoft.com/regions/services/).        |
    |**Warstwa cenowa**     |  Wybierz między **Standard**, **Premium**lub **Trial**. Aby uzyskać więcej informacji o tych warstwach, zobacz [stronę usługi Databricks](https://azure.microsoft.com/pricing/details/databricks/).       |

3. Wybierz **pozycję Recenzja + Utwórz**, a następnie **utwórz**. Tworzenie obszaru roboczego trwa kilka minut. Podczas tworzenia obszaru roboczego można wyświetlić stan wdrożenia w **obszarze Powiadomienia**. Po zakończeniu tego procesu konto użytkownika jest automatycznie dodawane jako użytkownik administratora w obszarze roboczym.

    ![Kafelek wdrażania databricks](./media/quickstart-create-databricks-workspace-portal/databricks-deployment-tile.png "Kafelek wdrażania databricks")

    Gdy wdrożenie obszaru roboczego nie powiedzie się, obszar roboczy jest nadal tworzony w stanie awarii. Usuń nieudany obszar roboczy i utwórz nowy obszar roboczy, który rozwiązuje błędy wdrażania. Po usunięciu nieudanego obszaru roboczego grupa zarządzanych zasobów i wszystkie pomyślnie wdrożone zasoby są również usuwane.

## <a name="create-a-spark-cluster-in-databricks"></a>Tworzenie klastra Spark w usłudze Databricks

> [!NOTE]
> Aby użyć bezpłatnego konta do utworzenia klastra usługi Azure Databricks, przed utworzeniem klastra przejdź do swojego profilu i zmień swoją subskrypcję na **płatność zgodnie z rzeczywistym użyciem**. Aby uzyskać więcej informacji, zobacz [Bezpłatne konto platformy Azure](https://azure.microsoft.com/free/).

1. W witrynie Azure Portal przejdź do utworzonego obszaru roboczego usługi Databricks, a następnie kliknij pozycję **Uruchom obszar roboczy**.

2. Nastąpi przekierowanie do portalu usługi Azure Databricks. W portalu kliknij pozycję **Nowy klaster**.

    ![Databricks na platformie Azure](./media/quickstart-create-databricks-workspace-portal/databricks-on-azure.png "Databricks na platformie Azure")

3. Na stronie **Nowy klaster** podaj wartości, aby utworzyć klaster.

    ![Tworzenie klastra Databricks Spark na platformie Azure](./media/quickstart-create-databricks-workspace-portal/create-databricks-spark-cluster.png "Tworzenie klastra Databricks Spark na platformie Azure")

    Zaakceptuj pozostałe wartości domyślne poza następującymi:

   * Wprowadź nazwę klastra.
   * W tym artykule utwórz klaster z czasem wykonywania **5.3.**
   * Upewnij się, że zaznaczysz pole wyboru **Zakończ po \_ \_ minutach braku aktywności.** Podaj czas (w minutach), po jakim działanie klastra ma zostać zakończone, jeśli nie jest używany.
    
     Wybierz pozycję **Utwórz klaster**. Po uruchomieniu klastra możesz dołączyć do niego notesy i uruchamiać zadania Spark.

Aby uzyskać więcej informacji na temat tworzenia klastrów, zobacz [Create a Spark cluster in Azure Databricks](/azure/databricks/clusters/create) (Tworzenie klastra Spark w usłudze Azure Databricks).

## <a name="run-a-spark-sql-job"></a>Uruchamianie zadania Spark SQL

Wykonaj następujące zadania, aby utworzyć notes w Databricks, skonfiguruj notes do odczytu danych z otwartych zestawów danych platformy Azure, a następnie uruchom zadanie programu Spark SQL na danych.

1. W lewym okienku wybierz pozycję **Azure Databricks**. W obszarze **Typowe zadania**wybierz pozycję **Nowy notes**.

    ![Tworzenie notesu w umiań danych](./media/quickstart-create-databricks-workspace-portal/databricks-create-notebook.png "Tworzenie notesu w umiań danych")

2. W oknie dialogowym **Tworzenie notesu** wprowadź nazwę, wybierz **pozycję Python** jako język i wybierz utworzony wcześniej klaster Platformy Spark.

    ![Tworzenie notesu w umiań danych](./media/quickstart-create-databricks-workspace-portal/databricks-notebook-details.png "Tworzenie notesu w umiań danych")

    Wybierz **pozycję Utwórz**.

3. W tym kroku utwórz ramę Spark DataFrame z boston safety data z [otwartych zestawów danych platformy Azure](https://azure.microsoft.com/services/open-datasets/catalog/boston-safety-data/#AzureDatabricks)i użyj języka SQL do wykonywania zapytań o dane.

   Następujące polecenie ustawia informacje o dostępie do magazynu platformy Azure. Wklej ten kod PySpark do pierwszej komórki i użyj **klawiszy Shift+Enter,** aby uruchomić kod.

   ```python
   blob_account_name = "azureopendatastorage"
   blob_container_name = "citydatacontainer"
   blob_relative_path = "Safety/Release/city=Boston"
   blob_sas_token = r"?st=2019-02-26T02%3A34%3A32Z&se=2119-02-27T02%3A34%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=XlJVWA7fMXCSxCKqJm8psMOh0W4h7cSYO28coRqF2fs%3D"
   ```

   Następujące polecenie umożliwia programowi Spark zdalne odczytywanie z magazynu obiektów Blob. Wklej ten kod PySpark do następnej komórki i użyj **klawiszy Shift+Enter,** aby uruchomić kod.

   ```python
   wasbs_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)
   spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name), blob_sas_token)
   print('Remote blob path: ' + wasbs_path)
   ```

   Następujące polecenie tworzy DataFrame. Wklej ten kod PySpark do następnej komórki i użyj **klawiszy Shift+Enter,** aby uruchomić kod.

   ```python
   df = spark.read.parquet(wasbs_path)
   print('Register the DataFrame as a SQL temporary view: source')
   df.createOrReplaceTempView('source')
   ```

4. Uruchom instrukcję SQL zwraca 10 pierwszych wierszy danych z widoku tymczasowego o nazwie **źródło**. Wklej ten kod PySpark do następnej komórki i użyj **klawiszy Shift+Enter,** aby uruchomić kod.

   ```python
   print('Displaying top 10 rows: ')
   display(spark.sql('SELECT * FROM source LIMIT 10'))
   ```

5. Pojawią się tabelaryczne dane wyjściowe, takie jak pokazano na poniższym zrzucie ekranu (przedstawiono tu tylko niektóre kolumny):

    ![Przykładowe dane](./media/quickstart-create-databricks-workspace-portal/databricks-sample-csv-data.png "Przykładowe dane JSON")

6. Teraz tworzysz wizualną reprezentację tych danych, aby pokazać, ile zdarzeń bezpieczeństwa jest zgłaszanych przy użyciu aplikacji Citizens Connect i aplikacji City Worker zamiast z innych źródeł. U dołu danych wyjściowych tabelaryczne wybierz ikonę **wykresu słupkowego,** a następnie kliknij pozycję **Opcje wydruku**.

    ![Tworzenie wykresu słupkowego](./media/quickstart-create-databricks-workspace-portal/create-plots-databricks-notebook.png "Tworzenie wykresu słupkowego")

8. W obszarze **Dostosowywanie wykresu** przeciągnij i upuść wartości, jak pokazano na zrzucie ekranu.

    ![Dostosowywanie wykresu kołowego](./media/quickstart-create-databricks-workspace-portal/databricks-notebook-customize-plot.png "Dostosowywanie wykresu słupkowego")

   * Ustaw **klawisze** do **źródła**.
   * Ustaw **wartości** na **<\id>**.
   * W obszarze **Agregacja** ustaw wartość **LICZBA**.
   * Ustaw **typ wyświetlania** na **wykres kołowy**.

     Kliknij przycisk **Zastosuj**.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy skończysz korzystać z tego artykułu, możesz zakończyć działanie klastra. Aby to zrobić, w obszarze roboczym usługi Azure Databricks wybierz pozycję **Klastry** w lewym okienku. W obszarze klastra, którego działanie chcesz zakończyć, przesuń kursor na wielokropek w kolumnie **Akcje**, a następnie wybierz ikonę **Zakończ**.

![Zatrzymywać klaster Databricks](./media/quickstart-create-databricks-workspace-portal/terminate-databricks-cluster.png "Zatrzymywać klaster Databricks")

Jeśli klaster nie zostanie ręcznie zakończony, zostanie on automatycznie wstrzymany, pod warunkiem, że podczas tworzenia klastra zostanie zaznaczone pole wyboru **Zakończ po \_ \_ minutach braku aktywności.** W takim przypadku nieaktywny klaster automatycznie zatrzymuje się po określonym czasie.

## <a name="next-steps"></a>Następne kroki

W tym artykule utworzono klaster platformy Spark w usłudze Azure Databricks i uruchomiono zadanie platformy Spark przy użyciu danych z zestawów danych azure open. Aby dowiedzieć się, jak zaimportować dane z innych źródeł danych do usługi Azure Databricks, zobacz [Spark data sources](/azure/databricks/data/data-sources/index) (Źródła danych platformy Spark). Przejdź do następnego artykułu, aby dowiedzieć się, jak wykonać operację ETL (wyodrębnianie, przekształcanie i ładowanie danych) przy użyciu usługi Azure Databricks.

> [!div class="nextstepaction"]
>[Wyodrębnianie, przekształcanie i ładowanie danych przy użyciu usługi Azure Databricks](databricks-extract-load-sql-data-warehouse.md)
