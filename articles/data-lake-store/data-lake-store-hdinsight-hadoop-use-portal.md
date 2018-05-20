---
title: Tworzenie klastrów usługi HDInsight Azure z usługą Data Lake Store za pomocą portalu Azure | Dokumentacja firmy Microsoft
description: Użyj portalu Azure, aby utworzyć i klastry usługi HDInsight za pomocą usługi Azure Data Lake Store
services: data-lake-store,hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: a8c45a83-a8e3-4227-8b02-1bc1e1de6767
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: nitinme
ms.openlocfilehash: ff62ef24a2076605d759e22fcee86947fc0b9274
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="create-hdinsight-clusters-with-data-lake-store-by-using-the-azure-portal"></a>Tworzenie klastrów usługi HDInsight z usługą Data Lake Store za pomocą portalu Azure
> [!div class="op_single_selector"]
> * [Korzystanie z witryny Azure Portal](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Przy użyciu programu PowerShell (do magazynu domyślnego)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Przy użyciu programu PowerShell (dla dodatkowego magazynu)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Użyj Menedżera zasobów](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Dowiedz się, jak utworzyć klaster usługi HDInsight przy użyciu konta usługi Azure Data Lake Store jako domyślnego magazynu lub dodatkowego magazynu za pomocą portalu Azure. Mimo że dodatkowe miejsce do magazynowania jest opcjonalne dla klastra usługi HDInsight, zalecane jest przechowywanie danych biznesowych w ramach kont dodatkowego miejsca do magazynowania.

## <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem tego samouczka, upewnij się, że zostały spełnione następujące wymagania:

* **Subskrypcja platformy Azure**. Przejdź do [Azure Pobierz bezpłatną wersję próbną](https://azure.microsoft.com/pricing/free-trial/).
* **Konto usługi Azure Data Lake Store**. Postępuj zgodnie z instrukcjami z [wprowadzenie do usługi Azure Data Lake Store za pomocą portalu Azure](data-lake-store-get-started-portal.md). Należy także utworzyć folderem głównym na koncie.  W tym samouczku folderu głównego o nazwie __/klastrów__ jest używany.
* **Nazwy głównej usługi Azure Active Directory**. Ten samouczek zawiera instrukcje dotyczące sposobu tworzenia usługę podmiotu zabezpieczeń w usłudze Azure Active Directory (Azure AD). Jednak można utworzyć nazwy głównej usługi, musi być administratorem usługi Azure AD. Jeśli jesteś administratorem, możesz pominąć te wymagania wstępne i kontynuować samouczka.

    >[!NOTE]
    >Usługi można utworzyć podmiot, tylko wtedy, gdy administrator usługi Azure AD. Administrator usługi Azure AD należy utworzyć usługę podmiotu zabezpieczeń przed utworzeniem klastra usługi HDInsight z usługą Data Lake Store. Także nazwy głównej usługi muszą zostać utworzone przy użyciu certyfikatu zgodnie z opisem w [Tworzenie nazwy głównej usługi o certyfikat](../azure-resource-manager/resource-group-authenticate-service-principal.md#create-service-principal-with-self-signed-certificate).
    >

## <a name="create-an-hdinsight-cluster"></a>Tworzenie klastra usługi HDInsight

W tej sekcji utworzysz klaster usługi HDInsight przy użyciu kont usługi Data Lake Store jako domyślną lub dodatkowe miejsce do magazynowania. Ten artykuł dotyczy tylko część konfigurowania kont usługi Data Lake Store.  Aby informacji o tworzeniu klastra ogólne i procedury, zobacz [klastrów utworzyć Hadoop w HDInsight](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md).

### <a name="create-a-cluster-with-data-lake-store-as-default-storage"></a>Tworzenie klastra z usługą Data Lake Store jako domyślnego magazynu

**Aby utworzyć klaster usługi HDInsight z usługi Data Lake Store jako domyślne konto magazynu**

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Postępuj zgodnie z [Tworzenie klastrów](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md#create-clusters) ogólne informacje dotyczące tworzenia klastrów usługi HDInsight.
3. Na **magazynu** bloku, w obszarze **typu podstawowego magazynu**, wybierz pozycję **usługi Data Lake Store**, a następnie wprowadź następujące informacje:

    ![Dodaj nazwy głównej usługi w klastrze usługi HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.adls.storage.png "Dodaj nazwy głównej usługi w klastrze usługi HDInsight")

    - **Konto usługi Data Lake Store wybierz**: Wybierz istniejące konto usługi Data Lake Store. Istniejące konto usługi Data Lake Store jest wymagany.  Zobacz [wymagania wstępne](#prerequisites).
    - **Ścieżka katalogu głównego**: wprowadź ścieżkę, w którym mają być przechowywane pliki specyficznych dla klastra. Zrzut ekranu, to __/klastrów/myhdiadlcluster/__, w którym __/klastrów__ musi istnieć folder, a Portal tworzy *myhdicluster* folderu.  *Myhdicluster* jest nazwą klastra.
    - **Dostęp do usługi Data Lake Store**: Konfigurowanie dostępu między konta usługi Data Lake Store i klaster usługi HDInsight. Aby uzyskać instrukcje, zobacz [dostępu Konfiguruj Data Lake Store](#configure-data-lake-store-access).
    - **Dodatkowe konta magazynu**: kont Dodawanie konta magazynu Azure jako dodatkowy magazyn dla klastra. Aby dodać dodatkowe magazyny Lake danych odbywa się przez nadanie uprawnień klastra na danych więcej kont usługi Data Lake Store podczas konfigurowania konta usługi Data Lake Store jako typu podstawowego magazynu. Zobacz [Konfigurowanie dostępu do usługi Data Lake Store](#configure-data-lake-store-access).

4. Na **dostępu do usługi Data Lake Store**, kliknij przycisk **wybierz**, a następnie kontynuuj tworzenia klastra, zgodnie z opisem w [klastrów utworzyć Hadoop w HDInsight](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md).


### <a name="create-a-cluster-with-data-lake-store-as-additional-storage"></a>Tworzenie klastra z usługą Data Lake Store jako dodatkowego miejsca do magazynowania

Poniższe instrukcje tworzenia klastra usługi HDInsight z konta usługi Azure Storage jako domyślny magazyn i konto usługi Data Lake Store jako dodatkowego magazynu.
**Aby utworzyć klaster usługi HDInsight z usługi Data Lake Store jako domyślne konto magazynu**

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Postępuj zgodnie z [Tworzenie klastrów](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md#create-clusters) ogólne informacje dotyczące tworzenia klastrów usługi HDInsight.
3. Na **magazynu** bloku, w obszarze **typu podstawowego magazynu**, wybierz pozycję **usługi Azure Storage**, a następnie wprowadź następujące informacje:

    ![Dodaj nazwy głównej usługi w klastrze usługi HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.png "Dodaj nazwy głównej usługi w klastrze usługi HDInsight")

    - **Wybór metody**: użyj jednej z następujących opcji:

        * Aby określić konto magazynu, który jest częścią subskrypcji platformy Azure, wybierz **Moje subskrypcje**, a następnie wybierz konto magazynu.
        * Aby określić konto magazynu, który znajduje się poza subskrypcją platformy Azure, wybierz **klucz dostępu**, a następnie podaj informacje dotyczące konta magazynu zewnętrznego.

    - **Domyślny kontener**: Użyj wartości domyślnej lub określ własną nazwę.

    - Dodatkowych kont magazynu: dodać więcej kont magazynu Azure jako dodatkowe miejsce do magazynowania.
    - Dostęp do usługi Data Lake Store: Konfigurowanie dostępu między konta usługi Data Lake Store i klaster usługi HDInsight. Instrukcje można znaleźć [dostępu Konfiguruj Data Lake Store](#configure-data-lake-store-access).

## <a name="configure-data-lake-store-access"></a>Konfigurowanie dostępu do usługi Data Lake Store 

W tej sekcji skonfigurujesz dostęp do usługi Data Lake Store z klastrów usługi HDInsight przy użyciu nazwy głównej usługi Azure Active Directory. 

### <a name="specify-a-service-principal"></a>Określ nazwy głównej usługi

W portalu Azure możesz użyć istniejącej nazwy głównej usługi lub Utwórz nową.

**Aby utworzyć nazwy głównej usługi z portalu Azure**

1. Kliknij przycisk **dostępu do usługi Data Lake Store** z bloku magazynu.
2. Na **dostępu do usługi Data Lake Store** bloku, kliknij przycisk **Utwórz nowy**.
3. Kliknij przycisk **nazwy głównej usługi**, a następnie postępuj zgodnie z instrukcjami, aby utworzyć nazwy głównej usługi.
4. Pobierz certyfikat, jeśli zdecydujesz się używać go w przyszłości. Pobieranie certyfikatu jest przydatne, jeśli chcesz użyć tej samej nazwy głównej usługi podczas tworzenia dodatkowych klastrów usługi HDInsight.

    ![Dodaj nazwy głównej usługi w klastrze usługi HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.2.png "Dodaj nazwy głównej usługi w klastrze usługi HDInsight")

4. Kliknij przycisk **dostępu** Aby skonfigurować dostęp do folderu.  Zobacz [skonfigurować uprawnienia do pliku](#configure-file-permissions).


**Aby użyć istniejącej nazwy głównej usługi z portalu Azure**

1. Kliknij przycisk **dostępu do usługi Data Lake Store**.
1. Na **dostępu do usługi Data Lake Store** bloku, kliknij przycisk **Użyj istniejącego**.
2. Kliknij przycisk **nazwy głównej usługi**, a następnie wybierz nazwę główną usługi. 
3. Przekaż certyfikat (pfx) jest skojarzona z Twojej nazwy głównej usługi wybrany, a następnie wprowadź hasło certyfikatu.

    ![Dodaj nazwy głównej usługi w klastrze usługi HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.5.png "Dodaj nazwy głównej usługi w klastrze usługi HDInsight")

4. Kliknij przycisk **dostępu** Aby skonfigurować dostęp do folderu.  Zobacz [skonfigurować uprawnienia do pliku](#configure-file-permissions).


### <a name="configure-file-permissions"></a>Skonfiguruj uprawnienia do pliku

Konfiguruje są różne w zależności od tego, czy konto jest używane jako magazyn domyślne konto magazynu dodatkowe:

- Używane jako domyślnego magazynu

    - uprawnienia na poziomie głównym konta usługi Data Lake Store
    - uprawnienia na poziomie głównym magazynu klastra usługi HDInsight. Na przykład __/klastrów__ folderu używanego wcześniej w samouczku.
- Użyj jako dodatkowego magazynu

    - Uprawnienia w foldery, w której należy plik dostępu.

**Aby przypisać uprawnienia na poziomie głównym konta usługi Data Lake Store**

1. Na **dostępu do usługi Data Lake Store** bloku, kliknij przycisk **dostępu**. **Wybierz uprawnienia do pliku** bloku jest otwarty. Wyświetlane są wszystkie konta usługi Data Lake Store w ramach subskrypcji.
2. Umieść kursor (nie klikaj) myszy nad nazwą konta usługi Data Lake Store, aby uwidocznić pole wyboru, zaznacz pole wyboru.

    ![Dodaj nazwy głównej usługi w klastrze usługi HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3.png "Dodaj nazwy głównej usługi w klastrze usługi HDInsight")

  Domyślnie __odczytu__, __zapisu__, AND __EXECUTE__ wybrane są.

3. Kliknij przycisk **wybierz** w dolnej części strony.
4. Kliknij przycisk **Uruchom** na potrzeby przypisywania uprawnień.
5. Kliknij przycisk **Gotowe**.

**Aby przypisać uprawnienia na poziomie głównym klastra usługi HDInsight**

1. Na **dostępu do usługi Data Lake Store** bloku, kliknij przycisk **dostępu**. **Wybierz uprawnienia do pliku** bloku jest otwarty. Wyświetlane są wszystkie konta usługi Data Lake Store w ramach subskrypcji.
1. Z **wybierz uprawnienia do pliku** bloku, kliknij nazwę usługi Data Lake Store, aby wyświetlić jego zawartości.
2. Wybierz katalogu głównego magazynu klastra usługi HDInsight, zaznaczając pole wyboru po lewej stronie folderu. Zgodnie z zrzut ekranu starszym katalogu głównego magazynu klastra jest __/klastrów__ folder określony podczas zaznaczania usługi Data Lake Store jako domyślnego magazynu.
3. Ustaw uprawnienia do folderu.  Domyślnie do odczytu, zapisu i wykonywania wybrane są.
4. Kliknij przycisk **wybierz** w dolnej części strony.
5. Kliknij pozycję **Run** (Uruchom).
6. Kliknij przycisk **Gotowe**.

Jeśli korzystasz z usługi Data Lake Store jako dodatkowego magazynu, należy przypisać uprawnienia tylko do folderów, które chcesz uzyskać dostęp z klastrem usługi HDInsight. Na przykład na poniższym zrzucie ekranu, należy zapewnić dostęp tylko do **hdiaddonstorage** folderu w ramach konta usługi Data Lake Store.

![Przypisz uprawnienia główna usługi klastra usługi HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3-1.png "przypisać uprawnienia głównej usługi w klastrze usługi HDInsight")


## <a name="verify-cluster-set-up"></a>Sprawdź ustawienia klastra

Po zakończeniu instalacji klastra, w bloku klastra sprawdź wyniki, wykonując jedną lub obie następujące czynności:

* Aby zweryfikować konta usługi Data Lake Store określonego skojarzony magazyn dla klastra, kliknij przycisk **kont magazynu** w okienku po lewej stronie.

    ![Dodaj nazwy głównej usługi w klastrze usługi HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6-1.png "Dodaj nazwy głównej usługi w klastrze usługi HDInsight")

* Aby sprawdzić, czy nazwy głównej usługi jest prawidłowo skojarzony z klastrem usługi HDInsight, kliknij przycisk **dostępu do usługi Data Lake Store** w okienku po lewej stronie.

    ![Dodaj nazwy głównej usługi w klastrze usługi HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6.png "Dodaj nazwy głównej usługi w klastrze usługi HDInsight")


## <a name="examples"></a>Przykłady

Po skonfigurowaniu klastra z usługą Data Lake Store jako magazynu dotyczą te przykłady sposobu korzystania z klastra usługi HDInsight do analizy danych, który jest przechowywany w usłudze Data Lake Store.

### <a name="run-a-hive-query-against-data-in-a-data-lake-store-as-primary-storage"></a>Uruchomienie zapytania programu Hive względem danych w usłudze Data Lake Store (jako podstawowy magazyn)

Aby uruchomić zapytanie Hive, użyj interfejsu widoki Hive w portalu Ambari. Aby uzyskać instrukcje dotyczące sposobu używania widoki Hive narzędzia Ambari, zobacz [użyć widoku Hive z usługą Hadoop w usłudze HDInsight](../hdinsight/hadoop/apache-hadoop-use-hive-ambari-view.md).

Podczas pracy z danymi w usłudze Data Lake Store, istnieje kilka ciągów, aby zmienić.

Jeśli używasz, na przykład klaster utworzony za pomocą usługi Data Lake Store jako podstawowy magazyn jest ścieżka do danych: *adl: / / < data_lake_store_account_name > /azuredatalakestore.net/path/to/file*. Zapytania programu Hive, aby utworzyć tabelę z przykładowych danych, które są przechowywane na koncie usługi Data Lake Store wygląda następująca instrukcja:

    CREATE EXTERNAL TABLE websitelog (str string) LOCATION 'adl://hdiadlsstorage.azuredatalakestore.net/clusters/myhdiadlcluster/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/'

Opisy:
* `adl://hdiadlstorage.azuredatalakestore.net/` jest elementem głównym na koncie usługi Data Lake Store.
* `/clusters/myhdiadlcluster` jest elementem głównym danych klastra, które można określić podczas tworzenia klastra.
* `/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/` jest to lokalizacja przykładowy plik, który został użyty w zapytaniu.

### <a name="run-a-hive-query-against-data-in-a-data-lake-store-as-additional-storage"></a>Uruchamianie zapytań programu Hive względem danych w usłudze Data Lake Store (jako dodatkowego magazynu)

Jeśli utworzony klaster używa magazynu obiektów Blob jako domyślny magazyn, przykładowych danych nie znajduje się na koncie Azure Data Lake Store, który jest używany jako dodatkowy magazyn. W takim przypadku najpierw przenieść dane z magazynu obiektów Blob do usługi Data Lake Store, a następnie uruchom zapytania, jak pokazano w poprzednim przykładzie.

Aby uzyskać informacje dotyczące kopiowania danych z magazynu obiektów Blob do usługi Data Lake Store zobacz następujące artykuły:

* [Kopiowanie danych między obiektów blob magazynu Azure i usługi Data Lake Store za pomocą narzędzia Distcp](data-lake-store-copy-data-wasb-distcp.md)
* [Użyj AdlCopy, aby skopiować dane z obiektów blob magazynu Azure do usługi Data Lake Store](data-lake-store-copy-data-azure-storage-blob.md)

### <a name="use-data-lake-store-with-a-spark-cluster"></a>Użyj Data Lake Store z klastra Spark
Klaster Spark służy do uruchomienia zadań Spark na dane, które są przechowywane w usłudze Data Lake Store. Aby uzyskać więcej informacji, zobacz [klastra Spark w usłudze HDInsight używany do analizowania danych w usłudze Data Lake Store](../hdinsight/spark/apache-spark-use-with-data-lake-store.md).


### <a name="use-data-lake-store-in-a-storm-topology"></a>Użyj Data Lake Store w topologii Storm
Data Lake Store służy do zapisywania danych z topologii Storm. Aby uzyskać instrukcje dotyczące sposobu uzyskania tego scenariusza, zobacz [użycia usługi Azure Data Lake Store z systemu Apache Storm z usługą HDInsight](../hdinsight/storm/apache-storm-write-data-lake-store.md).

## <a name="see-also"></a>Zobacz także
* [Użyj Data Lake Store z klastrami Azure HDInsight](../hdinsight/hdinsight-hadoop-use-data-lake-store.md)
* [Środowiska PowerShell: Tworzenie klastra usługi HDInsight do użycia usługi Data Lake Store](data-lake-store-hdinsight-hadoop-use-powershell.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx
