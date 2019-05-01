---
title: Migrowanie lokalnych klastrów Apache Hadoop do usługi Azure HDInsight — najlepsze rozwiązania magazynu
description: Poznaj najlepsze rozwiązania magazynu migracji lokalnych klastrów Hadoop do usługi Azure HDInsight.
author: hrasheed-msft
ms.reviewer: ashishth
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/25/2018
ms.author: hrasheed
ms.openlocfilehash: c62a5384edf66fd9309bc7afcb50ada48e3fca7d
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64691520"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---storage-best-practices"></a>Migrowanie lokalnych klastrów Apache Hadoop do usługi Azure HDInsight — najlepsze rozwiązania magazynu

Ten artykuł zawiera zalecenia dotyczące przechowywania danych w systemach usługi Azure HDInsight. Jest częścią serii, która zapewnia najlepsze rozwiązania w celu ułatwienia migrowania lokalnych systemów Apache Hadoop do usługi Azure HDInsight.

## <a name="choose-right-storage-system-for-hdinsight-clusters"></a>Wybierz system przechowywania dla klastrów HDInsight

Struktura katalogów lokalnych System plików Apache Hadoop (HDFS) mogą ponownie tworzone w usłudze Azure Storage lub magazynu usługi Azure Data Lake. Następnie można bezpiecznie usuwać klastry HDInsight, które są używane do obliczeń bez utraty danych użytkownika. Obie te usługi może służyć jako domyślny system plików i system plików dodatkowych dla klastra usługi HDInsight. Klaster HDInsight i konto magazynu musi być hostowany w tym samym regionie.

### <a name="azure-storage"></a>Azure Storage

Klastry HDInsight służy jako domyślny system plików lub systemu plików dodatkowych kontenera obiektów blob w usłudze Azure Storage. Konto magazynu w warstwie standardowa jest obsługiwane do użytku z klastrami HDInsight. Premier warstwa nie jest obsługiwana. Domyślny kontener obiektów blob przechowuje informacje dotyczące klastra, takie jak dzienniki i historia zadań. Udostępnianie pojedynczego kontenera obiektów blob jako domyślnego systemu plików dla wielu klastrów nie jest obsługiwane.

Konta magazynu, które są zdefiniowane w proces tworzenia i odpowiednie klucze są przechowywane w `%HADOOP_HOME%/conf/core-site.xml` w węzłach klastra. Ponadto są dostępne w sekcji "Niestandardowa witryna core" w konfiguracji systemu plików HDFS w Interfejsie użytkownika Ambari. Klucz konta magazynu jest domyślne szyfrowanie przekazywanego materiału, a skrypt odszyfrowywania niestandardowy jest używany do odszyfrowywania kluczy przed jest przekazywane do usługi Hadoop demonów. Zadania w tym Hive, MapReduce, przesyłanie strumieniowe usługi Hadoop, a także Pig, zawierają opis kont magazynu i metadane z nimi.

Usługa Azure storage mogą być replikowane geograficznie. Replikacja geograficzna umożliwia odzyskiwanie geograficzne i nadmiarowość danych, tryb failover do lokalizacji zreplikowanych geograficznie poważnie wpływa na wydajność i może pociągnąć za sobą dodatkowe koszty. Zalecane jest, aby wybrać replikację geograficzną, należy uważnie i tylko jeśli wartość danych jest ponoszenie dodatkowych kosztów.

Jedną z następujących formatów można uzyskać dostęp do danych, która jest przechowywana w usłudze Azure Storage:

|Format dostępu do danych |Opis |
|---|---|
|`wasb:///`|Domyślny magazyn przy użyciu nieszyfrowanego komunikacji.|
|`wasbs:///`|Dostęp do magazynu domyślnego za pomocą komunikacji szyfrowanej.|
|`wasb://<container-name>@<account-name>.blob.core.windows.net/`|Używany podczas komunikacji z kontem magazynu innego niż domyślny. |


[Usługa Azure Storage cele dotyczące skalowalności i wydajności](../../storage/common/storage-scalability-targets.md) Wyświetla bieżące limity konta magazynu platformy Azure. Potrzebom aplikacji przekroczy cele skalowalności z jednego konta magazynu, aplikacji mogą być wbudowane w wielu kont magazynu, a następnie partycji obiekty danych na tych kontach magazynu.

[Analizy usługi Azure Storage](../../storage/storage-analytics.md) generuje dane pomiarowe dla wszystkich usług magazynu i witryny Azure portal może być skonfigurowany zbieranie metryk, aby być wizualizowane za pomocą wykresów. Alerty mogą być tworzone na potrzeby powiadomień podczas zostały osiągnięte progi dla metryk zasobów usługi storage.

Usługa Azure Storage oferuje [usuwania nietrwałego dla obiektów blob](../../storage/blobs/storage-blob-soft-delete.md) odzyskiwaniu danych, gdy jest przypadkowo zmienione lub usunięte przez aplikację lub innego użytkownika do konta magazynu.

Możesz utworzyć [migawek obiektów blob](https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob). Migawki to wersja tylko do odczytu obiektów blob, która jest wykonywana w punkcie w czasie i zapewnia sposób, aby utworzyć kopię zapasową obiektu blob. Po utworzeniu migawki go można odczytać, kopiowana, lub usunięte, ale nie zostały zmodyfikowane.

> [!Note]
> Dla starszych wersji lokalnej usługi Hadoop dystrybucji, które nie mają certyfikatu "wasbs" muszą one zostać zaimportowane do magazynu zaufania Java.

Następujące metody może służyć do importowania certyfikatów do magazynu zaufanych Java:

Pobierz certyfikat ssl usługi Azure Blob do pliku

```bash
echo -n | openssl s_client -connect <storage-account>.blob.core.windows.net:443 | sed -ne '/-BEGIN CERTIFICATE-/,/-END CERTIFICATE-/p' > Azure_Storage.cer
```

Zaimportuj plik powyżej do magazynu zaufanych Java we wszystkich węzłach

```bash
keytool -import -trustcacerts -keystore /path/to/jre/lib/security/cacerts -storepass changeit -noprompt -alias blobtrust -file Azure_Storage.cer
```

Sprawdź, czy dodano certyfikat w magazynie zaufania

```bash
keytool -list -v -keystore /path/to/jre/lib/security/cacerts
```

Aby uzyskać więcej informacji zobacz następujące artykuły:

- [Użyj usługi Azure storage z klastrami usługi Azure HDInsight](../hdinsight-hadoop-use-blob-storage.md)
- [Azure Storage Scalability and Performance Targets (Cele dotyczące skalowalności i wydajności usługi Azure Storage)](../../storage/common/storage-scalability-targets.md)
- [Microsoft Azure Storage Performance and Scalability Checklist (Lista kontrolna dotycząca wydajności i skalowalności usługi Microsoft Azure Storage)](../../storage/common/storage-performance-checklist.md)
- [Monitor, diagnose, and troubleshoot Microsoft Azure Storage (Monitorowanie, diagnozowanie i rozwiązywanie problemów z usługą Microsoft Azure Storage)](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md)
- [Monitorowanie konta magazynu w witrynie Azure portal](../../storage/common/storage-monitor-storage-account.md)

### <a name="azure-data-lake-storage-gen1"></a>Usługa Azure Data Lake Storage 1. generacji

Usługi Azure Data Lake Storage implementuje systemu plików HDFS i POSIX styl modelu kontroli dostępu. Zapewnia najwyższej klasy Integracja z usługą AAD dla kontroli dostępu szczegółowe w dobrym stanie. Nie ma ograniczeń do rozmiaru danych, które mogą być przechowywane i możliwość uruchamiania analiz równoległych.

Aby uzyskać więcej informacji zobacz następujące artykuły:

- [Tworzenie klastrów HDInsight za pomocą usługi Data Lake Storage przy użyciu witryny Azure portal](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)
- [Użyj usługi Data Lake Storage z klastrami usługi Azure HDInsight](../hdinsight-hadoop-use-data-lake-store.md)

### <a name="azure-data-lake-storage-gen2"></a>Usługa Azure Data Lake Storage 2. generacji

Azure Data Lake magazynu Gen2 stanowi najnowszą magazynowania oferty. Łączy on podstawowe funkcje z pierwsza generacja usługi Azure Data Lake Storage z punktem końcowym systemu na zgodny plik usługi Hadoop bezpośrednio zintegrowana usługa Azure Blob Storage. To ulepszenie łączy korzyści skali i koszt magazyn obiektów o niezawodności i wydajności, które zazwyczaj są skojarzone tylko z systemami plików lokalnych.

Azure Data Lake Store generacji 2 jest wbudowana w górnej części [usługi Azure Blob storage](../../storage/blobs/storage-blobs-introduction.md) i pozwala na łączenie się z danymi przy użyciu obu paradygmatów magazynu plików, jak systemu i obiekt. Funkcje z [usługi Azure Data Lake Storage Gen1](../../data-lake-store/index.md), takich jak semantyki systemu plików, pliku poziom zabezpieczeń i skalowania są połączone z niskie koszty i warstwowego, możliwości odzyskiwania wysoka dostępność/po awarii i duży zestaw SDK/narzędzia ekosystem z [usługi Azure Blob storage](../../storage/blobs/storage-blobs-introduction.md). W Data Lake Storage Gen2 jakość magazynu obiektów pozostają podczas dodawania zalety interfejsu systemu plików zoptymalizowana pod kątem analizy obciążeń.

Podstawową cechą Data Lake Storage Gen2, to dodanie [hierarchicznej przestrzeni nazw](../../storage/data-lake-storage/namespace.md) do usługi Blob storage, który organizuje pliki/obiekty w hierarchii katalogów wydajne dostępu do danych. Hierarchiczna struktura umożliwia wykonywanie operacji, takich jak zmiana nazwy lub usuwanie katalogu jako pojedynczej Atomowej metadanych operacji w katalogu, a nie wyliczanie i przetwarzanie wszystkich obiektów, które mają prefiks nazwy katalogu.

W przeszłości funkcje analizy chmurowej — było naruszenia bezpieczeństwa w zakresie wydajności, zarządzania i zabezpieczeń. Funkcje klucza Gen2 usługi Azure Data Lake Storage (ADLS) są następujące:

- **Dostęp zgodny z usługi Hadoop**: Azure Data Lake magazynu Gen2 pozwala zarządzać i uzyskiwać dostęp do danych, tak samo, jak za pomocą [pliku System (HDFS, Hadoop Distributed)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html). Nowy [sterownika ABFS](../../storage/data-lake-storage/abfs-driver.md) jest dostępna w ramach wszystkich środowisk technologii Apache Hadoop, które są objęte [Azure HDInsight](../index.yml). Ten sterownik umożliwia dostęp do danych przechowywanych w Data Lake Storage Gen2.

- **Nadzbiór uprawnień POSIX**: Model zabezpieczeń Gen2 Data Lake — w pełni obsługuje listy ACL i POSIX uprawnienia oraz niektórych dodatkowy poziom szczegółowości specyficzne dla Data Lake Storage Gen2. Ustawienia można skonfigurować za pomocą narzędzia administracyjne lub środowisk, takich jak Hive, jak i platformy Spark.

- **Niskie koszty**: Data Lake Storage Gen2 funkcji niedrogiej pojemności i transakcji. Jako przejścia danych za pośrednictwem jego pełnego cyklu życia, stawki rozliczeniowe zmienić, aby zminimalizować koszty, za pomocą wbudowanych funkcji, takich jak [cykl życia magazynu obiektów Blob platformy Azure](../../storage/common/storage-lifecycle-management-concepts.md).

- **Współpracuje z narzędzia magazynu obiektów Blob, struktur i aplikacje**: Data Lake Storage Gen2 nadal działają z szeroką gamą narzędzi, struktur i aplikacje, które istnieją już dzisiaj dla magazynu obiektów Blob.

- **Sterownik zoptymalizowane**: Sterownik systemu plików obiektów Blob platformy Azure (ABFS) jest [specjalnie zoptymalizowane pod kątem](../../storage/data-lake-storage/abfs-driver.md) do analizy danych big data. Odpowiednie interfejsy API REST są udostępniane za pośrednictwem punktu końcowego systemu plików dfs, dfs.core.windows.net.

Jedną z następujących formatów można uzyskać dostęp do danych, która jest przechowywana w usłudze ADLS Gen2:
- `abfs:///`: Dostęp do domyślnej usługi Data Lake Storage dla klastra.
- `abfs[s]://file_system@account_name.dfs.core.windows.net`: Używany podczas komunikacji z usługi Data Lake Storage innych niż domyślne.

Aby uzyskać więcej informacji zobacz następujące artykuły:

- [Wprowadzenie do usługi Azure Data Lake Storage Gen2](../../storage/data-lake-storage/introduction.md)
- [Sterownik systemu plików obiektów Blob platformy Azure (ABFS.md)](../../storage/data-lake-storage/abfs-driver.md)
- [Korzystanie z usługi Azure Data Lake Storage Gen2 w połączeniu z klastrami usługi Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen2.md)

## <a name="secure-azure-storage-keys-within-on-premises-hadoop-cluster-configuration"></a>Kluczy zabezpieczeń usługi Azure Storage w konfiguracji klastra usługi Hadoop w środowisku lokalnym

Klucze usługi Azure storage, które są dodawane do plików konfiguracji usługi Hadoop, nawiąż łączność programów na lokalnego systemu plików HDFS i usługi Azure Blob storage. Te klucze mogą być chronione przez szyfrowanie ich przy użyciu struktury dostawcy poświadczeń w usłudze Hadoop. Gdy zaszyfrowane, mogą być przechowywane i bezpieczny dostęp do.

**Aby aprowizować poświadczenia:**

```bash
hadoop credential create fs.azure.account.key.account.blob.core.windows.net -value <storage key> -provider jceks://hdfs@headnode.xx.internal.cloudapp.net/path/to/jceks/file
```

**Aby dodać powyżej Ścieżka dostawcy core-site.xml lub konfigurację systemu Ambari w niestandardowej witrynie core:**

```xml
<property>
    <name>hadoop.security.credential.provider.path</name>
        <value>
        jceks://hdfs@headnode.xx.internal.cloudapp.net/path/to/jceks
        </value>
    <description>Path to interrogate for protected credentials.</description>
</property>
```

> [!Note]
> Ścieżka właściwości dostawcy mogą być również dodawane do wiersza polecenia distcp zamiast przechowywania klucza na poziomie klastra na core-site.xml w następujący sposób:

```bash
hadoop distcp -D hadoop.security.credential.provider.path=jceks://hdfs@headnode.xx.internal.cloudapp.net/path/to/jceks /user/user1/ wasb:<//yourcontainer@youraccount.blob.core.windows.net/>user1
```

## <a name="restrict-azure-storage-data-access-using-sas"></a>Ograniczanie dostępu do danych usługi Azure storage przy użyciu sygnatury dostępu Współdzielonego

HDInsight domyślnie ma pełny dostęp do danych w ramach kont usługi Azure Storage skojarzonym z klastrem. Dostęp do sygnatur Współdzielonego dla kontenera obiektów blob może służyć do ograniczania dostępu do danych, takich jak zapewnić użytkownikom dostęp tylko do odczytu do danych.

### <a name="using-the-sas-token-created-with-python"></a>Przy użyciu tokenu sygnatury dostępu Współdzielonego utworzony za pomocą języka python

1. Otwórz [SASToken.py](https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature/blob/master/Python/SASToken.py) pliku, a następnie Zmień następujące wartości:

    |Token — właściwość|Opis|
    |---|---|
    |nazwa_zasady|Nazwa używać przechowywanych zasad w celu utworzenia.|
    |storage_account_name|Nazwa konta magazynu.|
    |storage_account_key|Klucz konta magazynu.|
    |storage_container_name|Kontener na koncie magazynu, którą chcesz ograniczyć dostęp do.|
    |example_file_path|Ścieżka do pliku, który zostanie przekazany do kontenera.|

2. Dołączono plik SASToken.py `ContainerPermissions.READ + ContainerPermissions.LIST` uprawnień i można go dostosować w oparciu o przypadek użycia.

3. Uruchom następujący skrypt: `python SASToken.py`

4. Po ukończeniu działania skryptu, wyświetla tokenu sygnatury dostępu Współdzielonego, podobny do następującego tekstu: `sr=c&si=policyname&sig=dOAi8CXuz5Fm15EjRUu5dHlOzYNtcK3Afp1xqxniEps%3D&sv=2014-02-14`

5. Aby ograniczyć dostęp do kontenera przy użyciu sygnatura dostępu współdzielonego, należy dodać wpis niestandardowego do konfiguracji lokacji podstawowej dla klastra, w obszarze Ambari systemu plików HDFS konfiguracje zaawansowane lokacji podstawowej Dodawanie właściwości niestandardowych.

6. Użyj następujących wartości dla **klucz** i **wartość** pola:

    **Klucz**: `fs.azure.sas.YOURCONTAINER.YOURACCOUNT.blob.core.windows.net` **Wartość**: KLUCZ sygnatury dostępu Współdzielonego zwracane przez FROM aplikacji języka Python w kroku 4 powyżej.

7. Kliknij przycisk **Dodaj** przycisk, aby zapisać ten klucz i wartość, a następnie kliknij przycisk **Zapisz** przycisk, aby zapisać zmiany konfiguracji. Po wyświetleniu monitu, Dodaj opis zmiany ("Dodawanie dostępu do magazynu sygnatur dostępu Współdzielonego" na przykład), a następnie kliknij przycisk **Zapisz**.

8. W Ambari web UI, wybierz systemu plików HDFS z listy po lewej stronie, a następnie wybierz **ponowne uruchomienie wszystkich wpływ** z akcji z listy rozwijanej po prawej stronie. Po wyświetleniu monitu wybierz **upewnij się, uruchom ponownie wszystkie**.

9. Powtórz ten proces dla MapReduce2 i YARN.

Istnieją trzy ważne warto zapamiętać dotyczących używania tokenów sygnatur dostępu Współdzielonego na platformie Azure:

1. Podczas tworzenia tokenów sygnatur dostępu Współdzielonego przy użyciu uprawnień "Odczyt + LIST" użytkowników, którzy korzystają z kontenera obiektów Blob za pomocą tokenu sygnatury dostępu Współdzielonego będzie "zapisu i usuwania" danych. Użytkownicy, którzy dostęp do kontenera obiektów Blob za pomocą tokenu sygnatury dostępu Współdzielonego i spróbuj zapis lub usunięcie operacji, zostanie wyświetlony komunikat z takich jak `"This request is not authorized to perform this operation"`.

2. Wygenerowano tokeny sygnatur dostępu Współdzielonego za pomocą `READ + LIST + WRITE` uprawnienia (ograniczyć `DELETE` tylko), polecenia takie jak `hadoop fs -put` najpierw zapisać `\_COPYING\_` pliku, a następnie spróbuj zmienić nazwę pliku. Tej operacji systemu plików HDFS mapuje `copy+delete` dla WASB. Ponieważ `DELETE` uprawnienie nie został podany, "put" może zakończyć się niepowodzeniem. `\_COPYING\_` Operacji jest funkcją usługi Hadoop, mają na celu dostarczenie niektóre mechanizm kontroli współbieżności. Obecnie nie istnieje żaden sposób ograniczyć tylko operację "DELETE" bez wpływu na "WRITE" także operacje.

3. Niestety Dostawca poświadczeń usługi hadoop i odszyfrowywania klucza dostawcy (ShellDecryptionKeyProvider) obecnie nie współpracujesz z tokenów sygnatur dostępu Współdzielonego i dlatego obecnie nie można chronić z widoczności.

Aby uzyskać więcej informacji, zobacz [użycia usługi Azure Storage sygnatur dostępu współdzielonego do ograniczania dostępu do danych w HDInsight](../hdinsight-storage-sharedaccesssignature-permissions.md).

## <a name="use-data-encryption-and-replication"></a>Użyj szyfrowania danych i replikacji

Wszystkie dane zapisane w usłudze Azure Storage są automatycznie szyfrowane przy użyciu [szyfrowanie usługi Storage (SSE)](../../storage/common/storage-service-encryption.md). Dane na koncie Azure storage są zawsze replikowane w celu zapewnienia wysokiej dostępności. Podczas tworzenia konta magazynu można wybrać jedną z następujących opcji replikacji:

- [Magazyn lokalnie nadmiarowy (LRS)](../../storage/common/storage-redundancy-lrs.md)
- [Magazyn strefowo nadmiarowy (ZRS)](../../storage/common/storage-redundancy-zrs.md)
- [Magazyn geograficznie nadmiarowy (GRS)](../../storage/common/storage-redundancy-grs.md)
- [Magazyn geograficznie nadmiarowy dostępny do odczytu (RA-GRS)](../../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage)

Usługi Azure Data Lake Storage zapewnia magazyn lokalnie nadmiarowy (LRS), ale należy także skopiować kluczowych danych do innego konta usługi Data Lake Storage w innym regionie z częstotliwością dostosowaną do potrzeb planu odzyskiwania po awarii. Istnieją różne metody kopiowania danych, takie jak [ADLCopy](../../data-lake-store/data-lake-store-copy-data-azure-storage-blob.md), DistCp, [programu Azure PowerShell](../../data-lake-store/data-lake-store-get-started-powershell.md), lub [usługi Azure Data Factory](../../data-factory/connector-azure-data-lake-store.md). Zalecane jest również wymusić zasady dostępu dla konta usługi Data Lake Storage, aby zapobiec przypadkowemu usunięciu.

Aby uzyskać więcej informacji zobacz następujące artykuły:

- [Replikacja usługi Azure storage](../../storage/common/storage-redundancy.md)
- [Wskazówki dotyczące po awarii dla usługi Azure Data Lake Storage (ADLS)](../../data-lake-store/data-lake-store-disaster-recovery-guidance.md)

## <a name="attach-additional-azure-storage-accounts-to-cluster"></a>Dołącz dodatkowe Azure konta magazynu do klastra

W trakcie procesu tworzenia HDInsight konto Azure Storage lub konta magazynu usługi Azure Data Lake zostanie wybrany jako domyślny system plików. Oprócz domyślne konto magazynu można dodać dodatkowe konta magazynu z tej samej subskrypcji platformy Azure lub różnych subskrypcji platformy Azure w trakcie procesu tworzenia klastra lub po utworzeniu klastra.

Można dodać dodatkowe konto magazynu w jednym w następujący sposób:
- Lokacji podstawowej Ambari systemu plików HDFS Config zaawansowane niestandardowe Dodaj nazwę konta magazynu i klucza, ponowne uruchomienie usług
- Za pomocą [skryptu akcji](../hdinsight-hadoop-add-storage.md) przez przekazanie nazwy konta magazynu i klucza

> [!Note]
> W prawidłowy przypadki użycia, można zwiększyć limity usługi Azure storage za pośrednictwem żądania dotyczącego [pomocy technicznej systemu Azure](https://azure.microsoft.com/support/faq/).

Aby uzyskać więcej informacji zobacz następujące artykuły:
- [Dodawanie kolejnych kont magazynu do HDInsight](../hdinsight-hadoop-add-storage.md)

## <a name="next-steps"></a>Kolejne kroki

Przeczytaj następny artykuł w tej serii:

- [Dane migracji najlepsze rozwiązania dotyczące lokalnych do migracji usługi Azure HDInsight Hadoop](apache-hadoop-on-premises-migration-best-practices-data-migration.md)
