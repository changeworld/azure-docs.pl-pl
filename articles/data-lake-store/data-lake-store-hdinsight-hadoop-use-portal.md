---
title: Tworzenie klastrów usługi Azure HDInsight przy użyciu usługi Azure Data Lake Storage Gen1 za pomocą witryny Azure portal | Dokumentacja firmy Microsoft
description: Użyj witryny Azure portal do tworzenia i używania klastrów HDInsight za pomocą usługi Azure Data Lake Storage Gen1
services: data-lake-store,hdinsight
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: a8c45a83-a8e3-4227-8b02-1bc1e1de6767
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 6f9064c6027499fff3a8551ee60722cd66c54dc2
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "58883432"
---
# <a name="create-hdinsight-clusters-with-azure-data-lake-storage-gen1-by-using-the-azure-portal"></a>Tworzenie klastrów HDInsight za pomocą usługi Azure Data Lake Storage Gen1 przy użyciu witryny Azure portal
> [!div class="op_single_selector"]
> * [Korzystanie z witryny Azure Portal](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Na użytek programu PowerShell (magazyn domyślny)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Na użytek programu PowerShell (magazyn dodatkowy)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Użyj usługi Resource Manager](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Dowiedz się, jak używać witryny Azure portal do utworzenia klastra HDInsight z kontem usługi Azure Data Lake Storage Gen1 jako magazynem domyślnym lub dodatkowego miejsca do magazynowania. Mimo że dodatkowego miejsca do magazynowania jest opcjonalny w przypadku klastra HDInsight, zalecane jest przechowywanie danych biznesowych w kolejnych kont magazynu.

## <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem tego samouczka, upewnij się, że zostały spełnione następujące wymagania:

* **Subskrypcja platformy Azure**. Przejdź do [uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Konta Data Lake Storage Gen1**. Postępuj zgodnie z instrukcjami z [Rozpoczynanie pracy z usługą Azure Data Lake Storage Gen1 przy użyciu witryny Azure portal](data-lake-store-get-started-portal.md). Należy także utworzyć folderem głównym na koncie.  W tym samouczku folderem głównym o nazwie __/klastrów__ jest używany.
* **Jednostki usługi Azure Active Directory**. Ten samouczek zawiera instrukcje dotyczące sposobu tworzenia usługę podmiotu zabezpieczeń w usłudze Azure Active Directory (Azure AD). Jednak aby utworzyć nazwę główną usługi, musi być administratorem usługi Azure AD. Jeśli jesteś administratorem, możesz pominąć to wymaganie wstępne i kontynuować z tego samouczka.

    >[!NOTE]
    >Usługi można utworzyć jednostki, tylko wtedy, gdy jesteś administratorem usługi Azure AD. Administrator usługi Azure AD należy utworzyć jednostkę usługi można było utworzyć klaster usługi HDInsight za pomocą programu Data Lake Storage Gen1. Ponadto jednostka usługi musi zostać utworzona z certyfikatu, zgodnie z opisem w [utworzyć nazwę główną usługi za pomocą certyfikatu](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate).
    >

## <a name="create-an-hdinsight-cluster"></a>Tworzenie klastra HDInsight

W tej sekcji tworzysz klaster HDInsight przy użyciu konta Data Lake Storage Gen1 jako domyślny lub dodatkowego magazynu. Ten artykuł koncentruje się tylko część konfigurowania konta Data Lake Storage Gen1.  Aby klaster ogólne informacje o tworzeniu i procedury, zobacz [Tworzenie klastrów usługi Hadoop w HDInsight](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md).

### <a name="create-a-cluster-with-data-lake-storage-gen1-as-default-storage"></a>Tworzenie klastra z usługą Data Lake Storage Gen1 jako magazynem domyślnym

**Do utworzenia klastra HDInsight z kontem usługi Data Lake Storage Gen1 jako domyślne konto magazynu**

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Postępuj zgodnie z [Tworzenie klastrów](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md#create-clusters) ogólne informacje na temat tworzenia klastrów HDInsight.
3. Na **magazynu** bloku, w obszarze **podstawowy typ magazynu**, wybierz opcję **usługi Azure Data Lake Storage Gen1**, a następnie wprowadź następujące informacje:

    ![Dodaj jednostkę usługi z klastrem HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.adls.storage.png "Dodaj jednostkę usługi z klastrem HDInsight")

    - **Wybierz opcję Data Lake Store konta**: Wybierz istniejące konto Data Lake Storage Gen1. Istniejące konta Data Lake Storage Gen1 jest wymagany.  Zobacz [Wymagania wstępne](#prerequisites).
    - **Ścieżka katalogu głównego**: Wprowadź ścieżkę, w którym mają być przechowywane pliki dotyczące klastra. Na zrzucie ekranu jest __/klastrów/myhdiadlcluster/__, w którym __/klastrów__ folder musi istnieć i Portal tworzy *myhdicluster* folderu.  *Myhdicluster* jest nazwą klastra.
    - **Data Lake Store dostępu**: Konfigurowanie dostępu między konta Data Lake Storage Gen1 oraz klastra HDInsight. Aby uzyskać instrukcje, zobacz skonfigurować Data Lake Storage Gen1 dostępu.
    - **Dodatkowe konta magazynu**: Dodawanie konta usługi Azure storage jako dodatkowe konta magazynu dla klastra. Aby dodać dodatkowe konta Data Lake Storage Gen1 odbywa się przez nadanie uprawnień do klastra na danych w kolejnych kont Data Lake Storage Gen1 podczas konfigurowania konta Data Lake Storage Gen1 jako typ podstawowy magazyn. Zobacz Konfigurowanie Data Lake Storage Gen1 dostęp.

4. Na **dostępu Data Lake Store**, kliknij przycisk **wybierz**, a następnie kontynuuj tworzenia klastra zgodnie z opisem w [Tworzenie klastrów usługi Hadoop w HDInsight](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md).


### <a name="create-a-cluster-with-data-lake-storage-gen1-as-additional-storage"></a>Tworzenie klastra przy użyciu Data Lake Storage Gen1 jako magazyn dodatkowy

Zgodnie z poniższymi instrukcjami Tworzenie klastra HDInsight z konta usługi Azure storage jako magazynu domyślnego i konta Data Lake Storage Gen1 jako dodatkowego magazynu.

**Do utworzenia klastra HDInsight z kontem usługi Data Lake Storage Gen1 jako dodatkowe konto magazynu**

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Postępuj zgodnie z [Tworzenie klastrów](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md#create-clusters) ogólne informacje na temat tworzenia klastrów HDInsight.
3. Na **magazynu** bloku, w obszarze **podstawowy typ magazynu**, wybierz opcję **usługi Azure Storage**, a następnie wprowadź następujące informacje:

    ![Dodaj jednostkę usługi z klastrem HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.png "Dodaj jednostkę usługi z klastrem HDInsight")

    - **Metoda wyboru**: użyj jednej z następujących opcji:

        * Aby określić konto magazynu, który jest częścią subskrypcji platformy Azure, zaznacz **Moje subskrypcje**, a następnie wybierz konto magazynu.
        * Aby określić konto magazynu, który znajduje się poza subskrypcją platformy Azure, zaznacz **klucz dostępu**, a następnie podaj informacje dla konta magazynu zewnętrznego.

    - **Używanie domyślnego kontenera**: Użyj wartości domyślnej lub określić własną nazwę.

    - Dodatkowe konta magazynu: dodawanie kont usługi Azure storage jako magazynu dodatkowego.
    - Data Lake Store dostępu: skonfigurować dostęp między konta Data Lake Storage Gen1 oraz klastra HDInsight. Aby uzyskać instrukcje zobacz [dostępu skonfigurować Data Lake Storage Gen1](#configure-data-lake-storage-gen1-access).

## <a name="configure-data-lake-storage-gen1-access"></a>Konfigurowanie dostępu Data Lake Storage Gen1 

W tej sekcji skonfigurujesz dostęp Data Lake Storage Gen1 z klastrami HDInsight przy użyciu nazwy głównej usługi Azure Active Directory. 

### <a name="specify-a-service-principal"></a>Określ nazwę główną usługi

W witrynie Azure portal możesz użyć istniejącej jednostki usługi lub Utwórz nową.

**Aby utworzyć nazwę główną usługi w witrynie Azure portal**

1. Kliknij przycisk **dostępu Data Lake Store** z bloku pamięci masowej.
2. Na **dostępu Data Lake Storage Gen1** bloku kliknij **Utwórz nową**.
3. Kliknij przycisk **nazwy głównej usługi**, a następnie postępuj zgodnie z instrukcjami, aby utworzyć nazwę główną usługi.
4. Pobierz certyfikat, jeśli zdecydujesz się używać go w przyszłości. Pobieranie certyfikatu jest przydatne, jeśli chcesz używać tej samej nazwy głównej usługi podczas tworzenia dodatkowych klastrów HDInsight.

    ![Dodaj jednostkę usługi z klastrem HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.2.png "Dodaj jednostkę usługi z klastrem HDInsight")

4. Kliknij przycisk **dostępu** skonfigurować dostęp do folderów.  Zobacz [skonfigurować uprawnienia do pliku](#configure-file-permissions).


**Aby użyć istniejącej nazwy głównej usługi w witrynie Azure portal**

1. Kliknij przycisk **dostępu Data Lake Store**.
1. Na **dostępu Data Lake Storage Gen1** bloku kliknij **Użyj istniejącej**.
2. Kliknij przycisk **nazwy głównej usługi**, a następnie wybierz nazwę główną usługi. 
3. Przekaż certyfikat (pfx) jest skojarzona z jednostki usługi wybrane, a następnie wprowadź hasło certyfikatu.

    ![Dodaj jednostkę usługi z klastrem HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.5.png "Dodaj jednostkę usługi z klastrem HDInsight")

4. Kliknij przycisk **dostępu** skonfigurować dostęp do folderów.  Zobacz [skonfigurować uprawnienia do pliku](#configure-file-permissions).


### <a name="configure-file-permissions"></a>Skonfiguruj uprawnienia do pliku

Konfiguruje różnią się w zależności od tego, czy konto jest używane jako magazyn domyślny lub dodatkowe konto magazynu:

- Używane jako magazyn domyślny

    - uprawnienia na poziomie głównym konta Data Lake Storage Gen1
    - uprawnienia na poziomie głównym magazynu klastra HDInsight. Na przykład __/klastrów__ folderu używanych we wcześniejszej części tego samouczka.
- Użyj jako magazynu dodatkowego

    - Uprawnienia w foldery, których potrzebujesz pliku dostępu.

**Na potrzeby przypisywania uprawnień na poziomie katalogu głównego konta Data Lake Storage Gen1**

1. Na **dostępu Data Lake Storage Gen1** bloku kliknij **dostępu**. **Wybieranie uprawnień do plików** zostanie otwarty blok. Wyświetla listę wszystkich kont Data Lake Storage Gen1 w ramach subskrypcji.
2. Umieść kursor (nie należy klikać przycisku) wskaźnik myszy nad nazwą konta Data Lake Storage Gen1, aby uwidocznić pole wyboru, a następnie zaznacz pole wyboru.

    ![Dodaj jednostkę usługi z klastrem HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3.png "Dodaj jednostkę usługi z klastrem HDInsight")

   Domyślnie __odczytu__, __zapisu__, i __EXECUTE__ są zaznaczone.

3. Kliknij przycisk **wybierz** w dolnej części strony.
4. Kliknij przycisk **Uruchom** na potrzeby przypisywania uprawnień.
5. Kliknij przycisk **Gotowe**.

**Na potrzeby przypisywania uprawnień na poziomie głównym w klastrze HDInsight**

1. Na **dostępu Data Lake Storage Gen1** bloku kliknij **dostępu**. **Wybieranie uprawnień do plików** zostanie otwarty blok. Wyświetla listę wszystkich kont Data Lake Storage Gen1 w ramach subskrypcji.
1. Z **Wybieranie uprawnień do plików** bloku, kliknij nazwę konta Data Lake Storage Gen1, aby wyświetlić jego zawartość.
2. Wybieranie katalogu głównego magazynu dla klastra HDInsight zaznaczenie tego pola wyboru po lewej stronie folderu. Zgodnie z zrzucie ekranu starszym katalogu głównego magazynu klastra jest __/klastrów__ podanym podczas wybierania Data Lake Storage Gen1 jako magazynem domyślnym folderze.
3. Ustaw uprawnienia do folderu.  Domyślnie, odczytu, zapisu i wykonania są zaznaczone.
4. Kliknij przycisk **wybierz** w dolnej części strony.
5. Kliknij pozycję **Run** (Uruchom).
6. Kliknij przycisk **Gotowe**.

Jeśli Data Lake Storage Gen1 używają jako magazynu dodatkowego, należy przypisać uprawnienia tylko do folderów, które chcesz uzyskać dostęp z klastra HDInsight. Na przykład na poniższym zrzucie ekranu, możesz zapewnić dostęp tylko do **mojnowyfolder** folderze na koncie usługi Data Lake Storage Gen1.

![Przypisz uprawnienia jednostki usługi z klastrem HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3-1.png "przypisać uprawnienia jednostki usługi z klastrem HDInsight")


## <a name="verify-cluster-set-up"></a>Sprawdź Konfigurowanie klastra

Po zakończeniu instalacji klastra, w bloku klastra sprawdź wyniki, wykonując jedną lub obie następujące czynności:

* Aby sprawdzić, czy skojarzony magazyn dla klastra jest konta Data Lake Storage Gen1, który określiłeś, kliknij przycisk **kont magazynu** w okienku po lewej stronie.

    ![Dodaj jednostkę usługi z klastrem HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6-1.png "Dodaj jednostkę usługi z klastrem HDInsight")

* Aby sprawdzić, czy nazwy głównej usługi jest on prawidłowo skojarzony z klastrem usługi HDInsight, kliknij przycisk **dostępu Data Lake Storage Gen1** w okienku po lewej stronie.

    ![Dodaj jednostkę usługi z klastrem HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6.png "Dodaj jednostkę usługi z klastrem HDInsight")


## <a name="examples"></a>Przykłady

Po skonfigurowaniu klastra za pomocą programu Data Lake Storage Gen1 jako magazyn odnoszą się do tych przykładów dotyczących sposobów używania klastra HDInsight do analizy danych, która jest przechowywana w Data Lake Storage Gen1.

### <a name="run-a-hive-query-against-data-in-a-data-lake-storage-gen1-account-as-primary-storage"></a>Uruchomienie zapytania programu Hive względem danych w ramach konta Data Lake Storage Gen1 (jako podstawowy magazyn)

Aby uruchomić zapytanie programu Hive, należy użyć interfejsu widoki Hive, korzystając z portalu Ambari. Aby uzyskać instrukcje dotyczące sposobu używania widoki Ambari, Hive, zobacz [widoku Hive za pomocą usługi Hadoop w HDInsight](../hdinsight/hadoop/apache-hadoop-use-hive-ambari-view.md).

Podczas pracy z danymi w ramach konta Data Lake Storage Gen1, istnieje kilka ciągów, aby zmienić.

Jeśli używasz, na przykład klastra, który został utworzony za pomocą Data Lake Storage Gen1 jako podstawowy magazyn jest ścieżka do danych: *adl: / / < data_lake_storage_gen1_account_name > /azuredatalakestore.net/path/to/file*. Zapytanie programu Hive, aby utworzyć tabelę z przykładowych danych, który jest przechowywany na koncie usługi Data Lake Storage Gen1 wygląda następującą instrukcję:

    CREATE EXTERNAL TABLE websitelog (str string) LOCATION 'adl://hdiadlsg1storage.azuredatalakestore.net/clusters/myhdiadlcluster/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/'

Opisy:
* `adl://hdiadlsg1storage.azuredatalakestore.net/` jest głównym konta Data Lake Storage Gen1.
* `/clusters/myhdiadlcluster` jest głównym dane klastra, które określono podczas tworzenia klastra.
* `/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/` jest to lokalizacja przykładowy plik, który jest używany w zapytaniu.

### <a name="run-a-hive-query-against-data-in-a-data-lake-storage-gen1-account-as-additional-storage"></a>Uruchomienie zapytania programu Hive względem danych w ramach konta Data Lake Storage Gen1 (jako magazyn dodatkowy)

Jeśli utworzony klaster używa magazynu obiektów Blob jako magazynem domyślnym, przykładowe dane nie znajduje się w ramach konta Data Lake Storage Gen1, która jest używana jako magazyn dodatkowy. W takim przypadku należy najpierw przenieść dane z magazynu obiektów Blob do konta Data Lake Storage Gen1, a następnie uruchom zapytania, jak pokazano w powyższym przykładzie.

Aby uzyskać informacje dotyczące kopiowania danych z magazynu obiektów Blob do konta Data Lake Storage Gen1 zobacz następujące artykuły:

* [Kopiowanie danych między obiektów blob usługi Azure Storage i Data Lake Storage Gen1 za pomocą narzędzia Distcp](data-lake-store-copy-data-wasb-distcp.md)
* [Kopiowanie danych z obiektów blob usługi Azure Storage do Data Lake Storage Gen1 za pomocą narzędzia AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)

### <a name="use-data-lake-storage-gen1-with-a-spark-cluster"></a>Za pomocą usług Data Lake Storage Gen1 klastra Spark
Klaster Spark służy do uruchamiania zadań platformy Spark w danych jest przechowywanych na koncie usługi Data Lake Storage Gen1. Aby uzyskać więcej informacji, zobacz [klastra HDInsight korzystanie z platformy Spark do analizy danych Data Lake Storage Gen1](../hdinsight/spark/apache-spark-use-with-data-lake-store.md).


### <a name="use-data-lake-storage-gen1-in-a-storm-topology"></a>Używanie programu Data Lake Storage Gen1 w topologii systemu Storm
Konta Data Lake Storage Gen1 służy do zapisywania danych pochodzących z topologii Storm. Aby uzyskać instrukcje dotyczące sposobu realizacji tego scenariusza, zobacz [użycia usługi Azure Data Lake magazynu Gen1 z platformą Apache Storm przy użyciu HDInsight](../hdinsight/storm/apache-storm-write-data-lake-store.md).

## <a name="see-also"></a>Zobacz także
* [Za pomocą usług Data Lake Storage Gen1 klastrów Azure HDInsight](../hdinsight/hdinsight-hadoop-use-data-lake-store.md)
* [PowerShell: Tworzenie klastra usługi HDInsight, aby użyć Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-powershell.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx
