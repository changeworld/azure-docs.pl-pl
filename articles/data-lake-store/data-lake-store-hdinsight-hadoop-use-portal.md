---
title: Tworzenie klastrów usługi Azure HDInsight za pomocą usługi Data Lake Storage Gen1 — portal
description: Tworzenie i używanie klastrów usługi HDInsight za pomocą usługi Azure Data Lake Storage Gen1 za pomocą witryny Azure Portal
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 1d1368ef8ffb474c6bec1240f567f043961597fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265573"
---
# <a name="create-hdinsight-clusters-with-azure-data-lake-storage-gen1-by-using-the-azure-portal"></a>Tworzenie klastrów usługi HDInsight przy użyciu usługi Azure Data Lake Storage Gen1 przy użyciu witryny Azure portal

> [!div class="op_single_selector"]
> * [Korzystanie z witryny Azure Portal](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Używanie programu PowerShell (do magazynu domyślnego)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Użyj programu PowerShell (do dodatkowego magazynu)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Korzystanie z Menedżera zasobów](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Dowiedz się, jak użyć witryny Azure portal do utworzenia klastra USŁUGI HDInsight z kontem usługi Azure Data Lake Storage Gen1 jako magazynu domyślnego lub dodatkowego magazynu. Mimo że dodatkowa pamięć masowa jest opcjonalna dla klastra HDInsight, zaleca się przechowywanie danych biznesowych na dodatkowych kontach magazynu.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że spełniasz następujące wymagania:

* **Subskrypcja platformy Azure**. Przejdź do [bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Konto Data Lake Storage Gen1**. Postępuj zgodnie z instrukcjami z [Wprowadzenie do usługi Azure Data Lake Storage Gen1 przy użyciu witryny Azure portal.](data-lake-store-get-started-portal.md) Należy również utworzyć folder główny na koncie.  W tym artykule używany jest folder główny o nazwie __/clusters.__
* **Podmiot usługi Azure Active Directory**. Ten przewodnik zawiera instrukcje dotyczące tworzenia jednostki usługi w usłudze Azure Active Directory (Azure AD). Jednak aby utworzyć jednostkę usługi, musisz być administratorem usługi Azure AD. Jeśli jesteś administratorem, możesz pominąć to wymaganie wstępne i kontynuować.

>[!NOTE]
>Jednostkę usługi można utworzyć tylko wtedy, gdy jesteś administratorem usługi Azure AD. Administrator usługi Azure AD musi utworzyć jednostkę usługi, zanim będzie można utworzyć klaster HDInsight z usługą Data Lake Storage Gen1. Ponadto podmiot usługi musi zostać utworzony przy za pomocą certyfikatu, zgodnie z [opisem w pozycję Utwórz jednostkę usługi z certyfikatem.](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate)
>

## <a name="create-an-hdinsight-cluster"></a>Tworzenie klastra HDInsight

W tej sekcji utworzysz klaster HDInsight z kontami Gen1 magazynu usługi Data Lake jako domyślny lub dodatkowy magazyn. W tym artykule skupiono się tylko na konfigurowaniu kont usługi Data Lake Storage Gen1. Aby uzyskać ogólne informacje i procedury tworzenia klastra, zobacz [Tworzenie klastrów Hadoop w umiaśnie HDInsight](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md).

### <a name="create-a-cluster-with-data-lake-storage-gen1-as-default-storage"></a>Tworzenie klastra z pamięcią Gen1 magazynu usługi Data Lake jako magazynu domyślnego

Aby utworzyć klaster USŁUGI HDInsight z kontem Data Lake Storage Gen1 jako domyślnym kontem magazynu:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
2. Postępuj zgodnie [z temacie Utwórz klastry,](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md#create-clusters) aby uzyskać ogólne informacje na temat tworzenia klastrów usługi HDInsight.
3. W bloku **Magazyn** w obszarze **Typ magazynu podstawowego**wybierz pozycję **Azure Data Lake Storage Gen1**, a następnie wprowadź następujące informacje:

    ![Dodawanie jednostki usługi do klastra usługi HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.adls.storage.png "Dodawanie jednostki usługi do klastra usługi HDInsight")

    * **Wybierz konto w sklepie Data Lake Store**: Wybierz istniejące konto Data Lake Storage Gen1. Wymagane jest istniejące konto Data Lake Storage Gen1.  Zobacz [Wymagania wstępne](#prerequisites).
    * **Ścieżka główna:** Wprowadź ścieżkę, w której mają być przechowywane pliki specyficzne dla klastra. Na zrzucie ekranu znajduje się __folder /clusters/myhdiadlcluster/__, w którym folder __/clusters__ musi istnieć, a portal tworzy folder *myhdicluster.*  *Myhdicluster* to nazwa klastra.
    * **Dostęp do magazynu Data Lake**Store: Konfigurowanie dostępu między kontem Data Lake Storage Gen1 a klastrem HDInsight. Aby uzyskać instrukcje, zobacz [Konfigurowanie dostępu do magazynu danych Lake Gen1](#configure-data-lake-storage-gen1-access).
    * **Dodatkowe konta magazynu:** Dodaj konta magazynu platformy Azure jako dodatkowe konta magazynu dla klastra. Aby dodać dodatkowe konta Data Lake Storage Gen1 odbywa się przez nadanie uprawnień klastra na dane w więcej kont Data Lake Storage Gen1 podczas konfigurowania konta Data Lake Storage Gen1 jako typ magazynu podstawowego. Zobacz [Konfigurowanie dostępu do magazynu danych Lake Gen1](#configure-data-lake-storage-gen1-access).

4. W **obszarze Dostęp do magazynu Data Lake**kliknij pozycję **Wybierz**, a następnie kontynuuj tworzenie klastra zgodnie z opisem w obszarze [Tworzenie klastrów Hadoop w programie HDInsight](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md).

### <a name="create-a-cluster-with-data-lake-storage-gen1-as-additional-storage"></a>Tworzenie klastra z pamięcią Masową dla usługi Data Lake Gen1 jako dodatkowego magazynu

Poniższe instrukcje utworzyć klaster HDInsight z kontem magazynu platformy Azure jako magazynu domyślnego i konta Usługi Data Lake Storage Gen1 jako dodatkowego magazynu.

Aby utworzyć klaster USŁUGI HDInsight z kontem Data Lake Storage Gen1 jako dodatkowym kontem magazynu:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
2. Postępuj zgodnie [z temacie Utwórz klastry,](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md#create-clusters) aby uzyskać ogólne informacje na temat tworzenia klastrów usługi HDInsight.
3. W bloku **Magazyn** w obszarze **Typ magazynu podstawowego**wybierz pozycję **Usługa Azure Storage**, a następnie wprowadź następujące informacje:

    ![Dodawanie jednostki usługi do klastra usługi HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.png "Dodawanie jednostki usługi do klastra usługi HDInsight")

    * **Metoda wyboru** — aby określić konto magazynu, które jest częścią subskrypcji platformy Azure, wybierz **pozycję Moje subskrypcje**, a następnie wybierz konto magazynu. Aby określić konto magazynu, które znajduje się poza subskrypcją platformy Azure, wybierz **klucz programu Access**, a następnie podaj informacje dotyczące zewnętrznego konta magazynu.

    * **Kontener domyślny** — użyj wartości domyślnej lub określ własną nazwę.
    * **Dodatkowe konta magazynu** — dodaj więcej kont magazynu platformy Azure jako dodatkowego magazynu.
    * **Dostęp do magazynu Usługi Data Lake** — konfigurowanie dostępu między kontem Data Lake Storage Gen1 a klastrem HDInsight. Aby uzyskać [instrukcje, zobacz Konfigurowanie dostępu do magazynu danych Lake Gen1](#configure-data-lake-storage-gen1-access).

## <a name="configure-data-lake-storage-gen1-access"></a>Konfigurowanie dostępu do pamięci masowej w ułowiowej w u źródła danych

W tej sekcji można skonfigurować dostęp do magazynu usługi Data Lake Gen1 z klastrów HDInsight przy użyciu jednostki usługi Active Directory platformy Azure.

### <a name="specify-a-service-principal"></a>Określanie jednostki usługi

Z witryny Azure portal można użyć istniejącego podmiotu usługi lub utworzyć nowy.

Aby utworzyć jednostkę usługi z witryny Azure portal:

1. Wybierz **dostęp do magazynu Usługi Data Lake** z bloku Magazyn.
1. W bloku **dostępu Data Lake Storage Gen1** wybierz pozycję **Utwórz nowy**.
1. Wybierz **pozycję Jednostki usługi**, a następnie postępuj zgodnie z instrukcjami, aby utworzyć jednostkę usługi.
1. Pobierz certyfikat, jeśli zdecydujesz się go użyć ponownie w przyszłości. Pobieranie certyfikatu jest przydatne, jeśli chcesz użyć tej samej jednostki usługi podczas tworzenia dodatkowych klastrów HDInsight.

    ![Dodawanie jednostki usługi do klastra usługi HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.2.png "Dodawanie jednostki usługi do klastra usługi HDInsight")

1. Wybierz **pozycję Dostęp,** aby skonfigurować dostęp do folderu.  Zobacz [Konfigurowanie uprawnień do plików](#configure-file-permissions).

Aby użyć istniejącego podmiotu usługi z witryny Azure portal:

1. Wybierz **dostęp do magazynu Data Lake**.
1. W bloku **dostępu Data Lake Storage Gen1** wybierz pozycję Użyj **istniejącego**.
1. Wybierz **pozycję Jednostka usługi**, a następnie wybierz jednostkę usługi.
1. Przekaż certyfikat (plik pfx), który jest skojarzony z wybranym podmiotem usługi, a następnie wprowadź hasło certyfikatu.

    ![Dodawanie jednostki usługi do klastra usługi HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.5.png "Dodawanie jednostki usługi do klastra usługi HDInsight")

1. Wybierz **pozycję Dostęp,** aby skonfigurować dostęp do folderu.  Zobacz [Konfigurowanie uprawnień do plików](#configure-file-permissions).

### <a name="configure-file-permissions"></a><a name="configure-file-permissions"></a>Konfigurowanie uprawnień do plików

Konfiguracja różni się w zależności od tego, czy konto jest używane jako domyślne konto magazynu, czy dodatkowe konto magazynu:

* Używany jako domyślny magazyn

  * uprawnienie na poziomie głównym konta Data Lake Storage Gen1
  * na poziomie głównym magazynu klastra HDInsight. Na przykład __folder /clusters__ używany wcześniej w samouczku.

* Użyj jako dodatkowego miejsca do przechowywania

  * Uprawnienie w folderach, w których potrzebny jest dostęp do plików.

Aby przypisać uprawnienia na poziomie głównym konta Usługi Data Lake Storage Gen1:

1. W bloku **dostępu Data Lake Storage Gen1** wybierz pozycję **Dostęp**. Zostanie otwarty blok **Uprawnienia do wyboru.** Zawiera listę wszystkich kont Data Lake Storage Gen1 w ramach subskrypcji.
1. Najedź kursorem myszy na nazwę konta Data Lake Storage Gen1, aby pole wyboru było widoczne, a następnie zaznacz pole wyboru.

    ![Dodawanie jednostki usługi do klastra usługi HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3.png "Dodawanie jednostki usługi do klastra usługi HDInsight")

   Domyślnie wybrano opcję __ODCZYT,__ __ZAPIS__I __WYKONANIE.__

1. Kliknij **pozycję Wybierz** u dołu strony.
1. Wybierz **pozycję Uruchom,** aby przypisać uprawnienia.
1. Wybierz pozycję **Done** (Gotowe).

Aby przypisać uprawnienia na poziomie głównym klastra USŁUGI HDInsight:

1. W bloku **dostępu Data Lake Storage Gen1** wybierz pozycję **Dostęp**. Zostanie otwarty blok **Uprawnienia do wyboru.** Zawiera listę wszystkich kont Data Lake Storage Gen1 w ramach subskrypcji.
1. W bloku **Wybierz uprawnienia do plików** wybierz nazwę konta Data Lake Storage Gen1, aby wyświetlić jego zawartość.
1. Zaznacz katalog główny magazynu klastra HDInsight, zaznaczając pole wyboru po lewej stronie folderu. Zgodnie z wcześniejszym zrzutem ekranu katalog główny magazynu klastra to folder __/clusters,__ który został określony podczas wybierania usługi Data Lake Storage Gen1 jako magazynu domyślnego.
1. Ustaw uprawnienia do folderu.  Domyślnie są zaznaczone wszystkie opcje odczytu, zapisu i wykonania.
1. Kliknij **pozycję Wybierz** u dołu strony.
1. Wybierz pozycję **Uruchom**.
1. Wybierz pozycję **Done** (Gotowe).

Jeśli używasz usługi Data Lake Storage Gen1 jako dodatkowego magazynu, należy przypisać uprawnienia tylko dla folderów, do których chcesz uzyskać dostęp z klastra HDInsight. Na przykład na poniższym zrzucie ekranu udostępniasz dostęp tylko do folderu **mynewfolder** na koncie Data Lake Storage Gen1.

![Przypisywanie uprawnień jednostki usługi do klastra USŁUGI HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3-1.png "Przypisywanie uprawnień jednostki usługi do klastra USŁUGI HDInsight")

## <a name="verify-cluster-setup"></a><a name="verify-cluster-set-up"></a>Weryfikowanie konfiguracji klastra

Po zakończeniu instalacji klastra w bloku klastra sprawdź wyniki, wykonując jedną lub obie z następujących czynności:

* Aby sprawdzić, czy skojarzony magazyn dla klastra jest określonym kontem Data Lake Storage Gen1, wybierz **konta magazynu** w lewym okienku.

    ![Dodawanie jednostki usługi do klastra usługi HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6-1.png "Dodawanie jednostki usługi do klastra usługi HDInsight")

* Aby sprawdzić, czy podmiot usługi jest poprawnie skojarzony z klastrem HDInsight, wybierz **dostęp do magazynu usługi Data Lake Gen1** w lewym okienku.

    ![Dodawanie jednostki usługi do klastra usługi HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6.png "Dodawanie jednostki usługi do klastra usługi HDInsight")

## <a name="examples"></a>Przykłady

Po skonfigurowaniu klastra z magazynem Data Lake Storage Gen1 jako magazynem zobacz te przykłady użycia klastra HDInsight do analizowania danych przechowywanych w ułanmie magazynu usługi Data Lake Gen1.

### <a name="run-a-hive-query-against-data-in-a-data-lake-storage-gen1-account-as-primary-storage"></a>Uruchamianie kwerendy gałęzi względem danych na koncie Data Lake Storage Gen1 (jako magazynu podstawowego)

Aby uruchomić kwerendę hive, użyj interfejsu widoki gałęzi w portalu Ambari. Aby uzyskać instrukcje dotyczące używania widoków ula Ambari, zobacz [Używanie widoku gałęzi z hadoopem w programie HDInsight](../hdinsight/hadoop/apache-hadoop-use-hive-ambari-view.md).

Podczas pracy z danymi na koncie Data Lake Storage Gen1, istnieje kilka ciągów do zmiany.

Jeśli używasz na przykład klastra utworzonego za pomocą magazynu usługi Data Lake Storage Gen1 jako magazynu podstawowego, ścieżka do danych to: *adl://<data_lake_storage_gen1_account_name>/azuredatalakestore.net/path/to/file*. Kwerenda hive, aby utworzyć tabelę na podstawie przykładowych danych przechowywanych na koncie Data Lake Storage Gen1 wygląda następująco:

    CREATE EXTERNAL TABLE websitelog (str string) LOCATION 'adl://hdiadlsg1storage.azuredatalakestore.net/clusters/myhdiadlcluster/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/'

Opisy:

* `adl://hdiadlsg1storage.azuredatalakestore.net/`jest katalogiem głównym konta Data Lake Storage Gen1.
* `/clusters/myhdiadlcluster`jest katalogiem głównym danych klastra określonych podczas tworzenia klastra.
* `/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/`jest lokalizacją przykładowego pliku użytego w kwerendzie.

### <a name="run-a-hive-query-against-data-in-a-data-lake-storage-gen1-account-as-additional-storage"></a>Uruchamianie kwerendy hive względem danych na koncie Data Lake Storage Gen1 (jako dodatkowego magazynu)

Jeśli utworzony klaster używa magazynu obiektów Blob jako magazynu domyślnego, przykładowe dane nie są zawarte na koncie Data Lake Storage Gen1, które jest używane jako dodatkowy magazyn. W takim przypadku najpierw przenieś dane z magazynu obiektów Blob do konta Data Lake Storage Gen1, a następnie uruchom kwerendy, jak pokazano w poprzednim przykładzie.

Aby uzyskać informacje na temat kopiowania danych z magazynu obiektów Blob do konta Usługi Data Lake Storage Gen1, zobacz następujące artykuły:

* [Kopiowanie danych między obiektami blob usługi Azure Storage a usługą Data Lake Storage Gen1 za pomocą protokołu Distcp](data-lake-store-copy-data-wasb-distcp.md)
* [Kopiowanie danych z obiektów blob usługi Azure Storage do usługi Data Lake Storage Gen1 za pomocą biblioteki AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)

### <a name="use-data-lake-storage-gen1-with-a-spark-cluster"></a>Używanie genu 1 magazynu usługi Data Lake z klastrem Spark

Za pomocą klastra platformy Spark można uruchamiać zadania platformy Spark na danych przechowywanych na koncie Data Lake Storage Gen1. Aby uzyskać więcej informacji, zobacz Analizowanie danych w pamięci [magazynu usługi Data Lake Gen1 za pomocą klastra platformy SPARK usługi HDInsight.](../hdinsight/spark/apache-spark-use-with-data-lake-store.md)

### <a name="use-data-lake-storage-gen1-in-a-storm-topology"></a>Korzystanie z usługi Data Lake Storage Gen1 w topologii burzy

Za pomocą konta Data Lake Storage Gen1 można zapisywać dane z topologii burzy. Aby uzyskać instrukcje dotyczące sposobu osiągnięcia tego scenariusza, zobacz [Korzystanie z usługi Azure Data Lake Storage Gen1 z usługą Apache Storm z programem HDInsight.](../hdinsight/storm/apache-storm-write-data-lake-store.md)

## <a name="see-also"></a>Zobacz też

* [Używanie usługi Data Lake Storage Gen1 w klastrach usługi Azure HDInsight](../hdinsight/hdinsight-hadoop-use-data-lake-store.md)
* [Program PowerShell: tworzenie klastra USŁUGI HDInsight w celu użycia pamięci masowej usługi Data Lake Gen1](data-lake-store-hdinsight-hadoop-use-powershell.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx
