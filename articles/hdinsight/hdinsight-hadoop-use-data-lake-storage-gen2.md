---
title: Korzystanie z usługi Azure Data Lake Storage Gen2 w połączeniu z klastrami usługi Azure HDInsight
description: Dowiedz się, jak korzystać z usługi Azure Data Lake Storage Gen2 z klastrami usługi Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/20/2020
ms.openlocfilehash: d711cc7e58fb055eda62cfc364a5552a7d10f7bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272294"
---
# <a name="use-azure-data-lake-storage-gen2-with-azure-hdinsight-clusters"></a>Korzystanie z usługi Azure Data Lake Storage Gen2 w połączeniu z klastrami usługi Azure HDInsight

Usługa Azure Data Lake Storage Gen2 to usługa magazynu w chmurze dedykowana do analizy dużych zbiorów danych, oparta na magazynie obiektów Blob platformy Azure. Usługa Data Lake Storage Gen2 łączy w sobie możliwości usługi Azure Blob storage i usługi Azure Data Lake Storage Gen1. Wynikowa usługa oferuje funkcje z usługi Azure Data Lake Storage Gen1, takie jak semantyka systemu plików, zabezpieczenia na poziomie katalogów i plików oraz skalowalność, a także tanie, warstwowe magazynowanie, wysoka dostępność i funkcje odzyskiwania po awarii z magazynu obiektów Blob platformy Azure.

## <a name="data-lake-storage-gen2-availability"></a>Dostępność pamięci masowej w u źródła danych Gen2

Data Lake Storage Gen2 jest dostępna jako opcja magazynu dla prawie wszystkich typów klastra usługi Azure HDInsight jako domyślne i dodatkowe konto magazynu. HBase może jednak mieć tylko jedno konto Data Lake Storage Gen2.

Aby uzyskać pełne porównanie opcji tworzenia klastra przy użyciu usługi Data Lake Storage Gen2, zobacz [Porównywanie opcji magazynu do użycia z klastrami usługi Azure HDInsight](hdinsight-hadoop-compare-storage-options.md).

> [!Note]  
> Po wybraniu data lake storage gen2 jako **typ magazynu podstawowego,** nie można wybrać konta Data Lake Storage Gen1 jako dodatkowego magazynu.

## <a name="create-a-cluster-with-data-lake-storage-gen2-through-the-azure-portal"></a>Tworzenie klastra z usługą Data Lake Storage Gen2 za pośrednictwem witryny Azure portal

Aby utworzyć klaster HDInsight, który używa usługi Data Lake Storage Gen2 do przechowywania, wykonaj następujące kroki, aby skonfigurować konto Data Lake Storage Gen2.

### <a name="create-a-user-assigned-managed-identity"></a>Tworzenie tożsamości zarządzanej przypisanej przez użytkownika

Utwórz tożsamość zarządzaną przypisaną przez użytkownika, jeśli jeszcze jej nie masz.

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
1. W lewym górnym rogu kliknij pozycję **Utwórz zasób**.
1. W polu wyszukiwania wpisz **przypisaną przez użytkownika** i kliknij pozycję **Tożsamość zarządzana przypisana użytkownikowi**.
1. Kliknij przycisk **Utwórz**.
1. Wprowadź nazwę tożsamości zarządzanej, wybierz poprawną subskrypcję, grupę zasobów i lokalizację.
1. Kliknij przycisk **Utwórz**.

Aby uzyskać więcej informacji na temat działania tożsamości zarządzanych w usłudze Azure HDInsight, zobacz [Tożsamości zarządzane w usłudze Azure HDInsight.](hdinsight-managed-identities.md)

![Tworzenie tożsamości zarządzanej przypisanej przez użytkownika](./media/hdinsight-hadoop-use-data-lake-storage-gen2/create-user-assigned-managed-identity-portal.png)

### <a name="create-a-data-lake-storage-gen2-account"></a>Tworzenie konta usługi Data Lake Storage Gen2

Utwórz konto usługi Azure Data Lake Storage Gen2.

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
1. W lewym górnym rogu kliknij pozycję **Utwórz zasób**.
1. W polu wyszukiwania wpisz **miejsce do magazynowania** i kliknij pozycję **Konto magazynu**.
1. Kliknij przycisk **Utwórz**.
1. Na ekranie **Tworzenie konta magazynu:**
    1. Wybierz poprawną subskrypcję i grupę zasobów.
    1. Wprowadź nazwę konta Data Lake Storage Gen2. Aby uzyskać więcej informacji na temat konwencji nazewnictwa kont magazynu, zobacz [Konwencje nazewnictwa zasobów platformy Azure](/azure/azure-resource-manager/management/resource-name-rules#microsoftstorage).
    1. Kliknij kartę **Zaawansowane.**
    1. Kliknij **pozycję Włączone** obok **hierarchicznego obszaru nazw** w obszarze Magazyn danych **Gen2**.
    1. Kliknij pozycję **Przegląd + utwórz**.
    1. Kliknij **przycisk Utwórz**

Aby uzyskać więcej informacji na temat innych opcji podczas tworzenia konta magazynu, zobacz [Szybki start: Tworzenie konta magazynu usługi Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-quickstart-create-account.md).

![Zrzut ekranu przedstawiający tworzenie konta magazynu w witrynie Azure portal](./media/hdinsight-hadoop-use-data-lake-storage-gen2/azure-data-lake-storage-account-create-advanced.png)

### <a name="set-up-permissions-for-the-managed-identity-on-the-data-lake-storage-gen2-account"></a>Konfigurowanie uprawnień dla tożsamości zarządzanej na koncie Data Lake Storage Gen2

Przypisz tożsamość zarządzana do roli **Właściciel danych obiektu blob magazynu** na koncie magazynu.

1. W [witrynie Azure portal](https://portal.azure.com)przejdź do konta magazynu.
1. Wybierz konto magazynu, a następnie wybierz **pozycję Kontrola dostępu (IAM),** aby wyświetlić ustawienia kontroli dostępu dla konta. Wybierz kartę **Przypisania ról,** aby wyświetlić listę przypisań ról.

    ![Zrzut ekranu przedstawiający ustawienia kontroli dostępu do magazynu](./media/hdinsight-hadoop-use-data-lake-storage-gen2/portal-access-control.png)

1. Wybierz przycisk **+ Dodaj przypisanie roli,** aby dodać nową rolę.
1. W oknie **Dodawanie przypisania roli** wybierz rolę **Właściciel danych obiektu Blob magazynu.** Następnie wybierz subskrypcję, która ma zarządzane konto tożsamości i magazynu. Następnie wyszukaj, aby zlokalizować przypisaną przez użytkownika tożsamość zarządzaną, która została wcześniej utworzona. Na koniec wybierz tożsamość zarządzaną, a zostanie wyświetlona w obszarze **Wybrane elementy członkowskie**.

    ![Zrzut ekranu przedstawiający sposób przypisywania roli RBAC](./media/hdinsight-hadoop-use-data-lake-storage-gen2/add-rbac-role3-window.png)

1. Wybierz **pozycję Zapisz**. Wybrana tożsamość przypisana przez użytkownika jest teraz wyświetlana w wybranej roli.
1. Po zakończeniu tej początkowej konfiguracji można utworzyć klaster za pośrednictwem portalu. Klaster musi znajdować się w tym samym regionie platformy Azure co konto magazynu. Na karcie **Magazyn** w menu tworzenia klastra wybierz następujące opcje:

    * W przypadku **głównego typu magazynu**wybierz pozycję Azure Data Lake Storage **Gen2**.
    * W obszarze **Konto magazynu podstawowego**wyszukaj i wybierz nowo utworzone konto magazynu Data Lake Storage Gen2.

    * W obszarze **Tożsamość**wybierz nowo utworzoną tożsamość zarządzaną przypisaną przez użytkownika.

        ![Ustawienia magazynu do korzystania z usługi Data Lake Storage Gen2 za pomocą usługi Azure HDInsight](./media/hdinsight-hadoop-use-data-lake-storage-gen2/azure-portal-cluster-storage-gentwo.png)

    > [!NOTE]
    > * Aby dodać dodatkowe konto Data Lake Storage Gen2, na poziomie konta magazynu, wystarczy przypisać tożsamość zarządzaną utworzoną wcześniej do nowego konta magazynu Usługi Data Lake Storage Gen2, które chcesz dodać. Należy pamiętać, że dodanie dodatkowego konta Data Lake Storage Gen2 za pośrednictwem bloku "Dodatkowe konta magazynu" w programie HDInsight nie jest obsługiwane.
    > * Można włączyć RA-GRS lub RA-ZRS na koncie magazynu platformy Azure, którego używa usługa HDInsight. Jednak tworzenie klastra względem RA-GRS lub RA-ZRS pomocniczego punktu końcowego nie jest obsługiwane.


## <a name="create-a-cluster-with-data-lake-storage-gen2-through-the-azure-cli"></a>Tworzenie klastra z usługą Data Lake Storage Gen2 za pośrednictwem interfejsu wiersza polecenia platformy Azure

Możesz [pobrać przykładowy plik szablonu](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/hdinsight-adls-gen2-template.json) i [pobrać przykładowy plik parametrów](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/parameters.json). Przed użyciem szablonu i fragmentu kodu interfejsu wiersza polecenia platformy Azure poniżej zastąp następujące symbole zastępcze ich poprawnymi wartościami:

| Symbol zastępczy | Opis |
|---|---|
| `<SUBSCRIPTION_ID>` | Identyfikator subskrypcji platformy Azure |
| `<RESOURCEGROUPNAME>` | Grupa zasobów, w której ma zostać utworzone nowe konto klastra i magazynu. |
| `<MANAGEDIDENTITYNAME>` | Nazwa tożsamości zarządzanej, która będzie mieć uprawnienia do konta usługi Azure Data Lake Storage Gen2. |
| `<STORAGEACCOUNTNAME>` | Nowe konto usługi Azure Data Lake Storage Gen2, które zostanie utworzone. |
| `<CLUSTERNAME>` | Nazwa klastra HDInsight. |
| `<PASSWORD>` | Wybrane hasło do zalogowania się do klastra przy użyciu SSH oraz pulpitu nawigacyjnego Ambari. |

Poniższy fragment kodu wykonuje następujące kroki początkowe:

1. Loguje się do konta platformy Azure.
1. Ustawia aktywną subskrypcję, w której zostaną wykonane operacje tworzenia.
1. Tworzy nową grupę zasobów dla nowych działań wdrażania.
1. Tworzy tożsamość zarządzaną przypisaną przez użytkownika.
1. Dodaje rozszerzenie do interfejsu wiersza polecenia platformy Azure, aby używać funkcji dla usługi Data Lake Storage Gen2.
1. Tworzy nowe konto Data Lake Storage `--hierarchical-namespace true` Gen2 przy użyciu flagi.

```azurecli
az login
az account set --subscription <SUBSCRIPTION_ID>

# Create resource group
az group create --name <RESOURCEGROUPNAME> --location eastus

# Create managed identity
az identity create -g <RESOURCEGROUPNAME> -n <MANAGEDIDENTITYNAME>

az extension add --name storage-preview

az storage account create --name <STORAGEACCOUNTNAME> \
    --resource-group <RESOURCEGROUPNAME> \
    --location eastus --sku Standard_LRS \
    --kind StorageV2 --hierarchical-namespace true
```

Następnie zaloguj się do portalu. Dodaj nową tożsamość zarządzaną przypisaną przez użytkownika do roli **współautora danych obiektów blob magazynu** na koncie magazynu, zgodnie z opisem w kroku 3 w obszarze [Korzystanie z witryny Azure portal](hdinsight-hadoop-use-data-lake-storage-gen2.md).

Po przypisaniu roli tożsamości zarządzanej przypisanej przez użytkownika należy wdrożyć szablon przy użyciu następującego fragmentu kodu.

```azurecli
az group deployment create --name HDInsightADLSGen2Deployment \
    --resource-group <RESOURCEGROUPNAME> \
    --template-file hdinsight-adls-gen2-template.json \
    --parameters parameters.json
```

## <a name="create-a-cluster-with-data-lake-storage-gen2-through-azure-powershell"></a>Tworzenie klastra z usługą Data Lake Storage Gen2 za pośrednictwem programu Azure PowerShell

Za pomocą programu PowerShell do tworzenia klastra HDInsight z usługi Azure Data Lake Storage Gen2 nie jest obecnie obsługiwany.

## <a name="access-control-for-data-lake-storage-gen2-in-hdinsight"></a>Kontrola dostępu dla pamięci masowej Data Lake Gen2 w programie HDInsight

### <a name="what-kinds-of-permissions-does-data-lake-storage-gen2-support"></a>Jakie uprawnienia obsługuje usługa Data Lake Storage Gen2?

Data Lake Storage Gen2 używa modelu kontroli dostępu, który obsługuje zarówno oparte na rolach kontroli dostępu (RBAC) i POSIX-like listy kontroli dostępu (Listy KONTROLI DOSTĘPU). Data Lake Storage Gen1 obsługuje listy kontroli dostępu tylko do kontrolowania dostępu do danych.

RBAC używa przypisań ról, aby skutecznie stosować zestawy uprawnień do użytkowników, grup i podmiotów korzystających z usług dla zasobów platformy Azure. Zazwyczaj te zasoby platformy Azure są ograniczone do zasobów najwyższego poziomu (na przykład kont usługi Azure Storage). W przypadku usługi Azure Storage, a także usługi Data Lake Storage Gen2 ten mechanizm został rozszerzony na zasób systemu plików.

 Aby uzyskać więcej informacji na temat uprawnień do plików za pomocą funkcji RBAC, zobacz [Kontrola dostępu oparta na rolach platformy Azure (RBAC)](../storage/blobs/data-lake-storage-access-control.md#azure-role-based-access-control-rbac).

Aby uzyskać więcej informacji o uprawnieniach do plików z listami ACL, zobacz [Listy kontroli dostępu w plikach i katalogach](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories).

### <a name="how-do-i-control-access-to-my-data-in-data-lake-storage-gen2"></a>Jak kontrolować dostęp do moich danych w programie Data Lake Storage Gen2?

Możliwość dostępu do plików w programie Data Lake Storage Gen2 jest kontrolowana za pomocą zarządzanych tożsamości. Tożsamość zarządzana to tożsamość zarejestrowana w usłudze Azure Active Directory (Azure AD), której poświadczenia są zarządzane przez platformę Azure. W tożsamościach zarządzanych nie trzeba rejestrować podmiotów usługi w usłudze Azure AD ani obsługiwać poświadczeń, takich jak certyfikaty.

Usługi platformy Azure mają dwa typy tożsamości zarządzanych: przypisane do systemu i przypisane przez użytkownika. Usługa HDInsight używa tożsamości zarządzanych przypisanych przez użytkownika do uzyskiwania dostępu do pamięci masowej usługi Data Lake Gen2. Tożsamość zarządzana przypisana przez użytkownika jest tworzona jako autonomiczny zasób platformy Azure. W ramach procesu tworzenia platforma Azure tworzy tożsamość w dzierżawie usługi Azure AD, której ufa używana subskrypcja. Utworzoną tożsamość można przypisać do co najmniej jednego wystąpienia usługi platformy Azure.

Cykl życiowy tożsamości przypisanej przez użytkownika jest zarządzany oddzielnie od cyklu życiowego wystąpień usługi platformy Azure, do których została przypisana. Aby uzyskać więcej informacji na temat tożsamości zarządzanych, zobacz [Jak działają tożsamości zarządzane dla zasobów platformy Azure?](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work).

### <a name="how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-by-using-hive-or-other-services"></a>Jak ustawić uprawnienia użytkowników usługi Azure AD do wykonywania zapytań o dane w usłudze Data Lake Storage Gen2 przy użyciu gałęzi lub innych usług?

Aby ustawić uprawnienia użytkowników do wykonywania zapytań o dane, użyj grup zabezpieczeń usługi Azure AD jako przypisanego podmiotu w listach ACL. Nie przypisuj bezpośrednio uprawnień dostępu do plików poszczególnym użytkownikom ani jednostkom usługi. Korzystając z grup zabezpieczeń usługi Azure AD do kontrolowania przepływu uprawnień, można dodawać i usuwać użytkowników lub podmioty usługi bez ponownego stosowania list ACL do całej struktury katalogów. Wystarczy tylko dodać lub usunąć użytkowników z odpowiedniej grupy zabezpieczeń usługi Azure AD. Listy ACL nie są dziedziczone, więc ponowne nakładanie list ACL wymaga aktualizacji listy ACL w każdym pliku i podkatalogu.

## <a name="access-files-from-the-cluster"></a>Dostęp do plików z klastra

Istnieje kilka sposobów uzyskiwania dostępu do plików w programie Data Lake Storage Gen2 z klastra HDInsight.

* **Przy użyciu w pełni kwalifikowanej nazwy**. W przypadku tej metody należy podać pełną ścieżkę do pliku, do którego chcesz uzyskać dostęp.

    ```
    abfs://<containername>@<accountname>.dfs.core.windows.net/<file.path>/
    ```

* **Przy użyciu skróconego formatu ścieżki**. Za pomocą tego podejścia można zastąpić ścieżkę do katalogu głównego klastra:

    ```
    abfs:///<file.path>/
    ```

* **Przy użyciu ścieżki względnej**. W przypadku tej metody należy podać tylko względną ścieżkę do pliku, do którego chcesz uzyskać dostęp.

    ```
    /<file.path>/
    ```

### <a name="data-access-examples"></a>Przykłady dostępu do danych

Przykłady są oparte na [połączeniu ssh](./hdinsight-hadoop-linux-use-ssh-unix.md) z węzłem głównym klastra. W przykładach użyto wszystkich trzech schematów URI. Zastąp `CONTAINERNAME` i `STORAGEACCOUNT` odpowiednie wartości

#### <a name="a-few-hdfs-commands"></a>Kilka poleceń hdfs

1. Tworzenie pliku w magazynie lokalnym.

    ```bash
    touch testFile.txt
    ```

1. Tworzenie katalogów w magazynie klastra.

    ```bash
    hdfs dfs -mkdir abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -mkdir abfs:///sampledata2/
    hdfs dfs -mkdir /sampledata3/
    ```

1. Kopiowanie danych z magazynu lokalnego do magazynu klastra.

    ```bash
    hdfs dfs -copyFromLocal testFile.txt  abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -copyFromLocal testFile.txt  abfs:///sampledata2/
    hdfs dfs -copyFromLocal testFile.txt  /sampledata3/
    ```

1. Lista zawartości katalogu w magazynie klastra.

    ```bash
    hdfs dfs -ls abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -ls abfs:///sampledata2/
    hdfs dfs -ls /sampledata3/
    ```

#### <a name="creating-a-hive-table"></a>Tworzenie tabeli gałęzi

Trzy lokalizacje plików są wyświetlane w celach ilustracyjnych. W przypadku rzeczywistego wykonania należy `LOCATION` użyć tylko jednego z wpisów.

```hql
DROP TABLE myTable;
CREATE EXTERNAL TABLE myTable (
    t1 string,
    t2 string,
    t3 string,
    t4 string,
    t5 string,
    t6 string,
    t7 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
STORED AS TEXTFILE
LOCATION 'abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/example/data/';
LOCATION 'abfs:///example/data/';
LOCATION '/example/data/';
```

## <a name="next-steps"></a>Następne kroki

* [Integracja usługi Azure HDInsight z podglądem usługi Data Lake Storage Gen2 — listy ACL i aktualizacja zabezpieczeń](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/)
* [Wprowadzenie do usługi Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
* [Samouczek: wyodrębnianie, przekształcanie i ładowanie danych przy użyciu interaktywnej kwerendy w usłudze Azure HDInsight](./interactive-query/interactive-query-tutorial-analyze-flight-data.md)
