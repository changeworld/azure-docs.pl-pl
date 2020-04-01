---
title: 'Magazyn: migrowanie lokalnego usługi Apache Hadoop do usługi Azure HDInsight'
description: Poznaj najlepsze rozwiązania dotyczące migracji lokalnych klastrów Hadoop do usługi Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: ashishth
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/10/2019
ms.openlocfilehash: f19d4adad675cdf95f59aca0f752f46211b75e8f
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80436902"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight"></a>Migrowanie lokalnych klastrów Apache Hadoop do usługi Azure HDInsight

Ten artykuł zawiera zalecenia dotyczące przechowywania danych w systemach Azure HDInsight. Jest to część serii, która zapewnia najlepsze rozwiązania ułatwiające migrację lokalnych systemów Apache Hadoop do usługi Azure HDInsight.

## <a name="choose-right-storage-system-for-hdinsight-clusters"></a>Wybierz odpowiedni system pamięci masowej dla klastrów HDInsight

Lokalna struktura katalogów systemu plików Apache Hadoop (HDFS) może zostać ponownie utworzona w usłudze Azure Storage lub usłudze Azure Data Lake Storage. Następnie można bezpiecznie usunąć klastry HDInsight, które są używane do obliczeń bez utraty danych użytkownika. Obie usługi mogą być używane zarówno jako domyślny system plików, jak i dodatkowy system plików dla klastra HDInsight. Klaster HDInsight i konto magazynu muszą być hostowane w tym samym regionie.

### <a name="azure-storage"></a>Azure Storage

Klastry HDInsight można użyć kontenera obiektów blob w usłudze Azure Storage jako domyślny system plików lub dodatkowy system plików.Konto magazynu warstwy Standardowa jest obsługiwane do użytku z klastrami HDInsight. Warstwa Premier nie jest obsługiwana. Domyślny kontener obiektów blob przechowuje informacje dotyczące klastra, takie jak dzienniki i historia zadań.Udostępnianie jednego kontenera obiektów blob jako domyślnego systemu plików dla wielu klastrów nie jest obsługiwane.

Konta magazynu, które są zdefiniowane w procesie tworzenia `%HADOOP_HOME%/conf/core-site.xml` i ich odpowiednie klucze są przechowywane w węzłach klastra. Można je również uzyskać w sekcji "Niestandardowa podstawowa lokacja" w konfiguracji HDFS w interfejsie użytkownika Ambari. Klucz konta magazynu jest domyślnie szyfrowany, a niestandardowy skrypt odszyfrowywania jest używany do odszyfrowywania kluczy przed przekazaniem ich demonom Hadoop. Zadania, w tym Hive, MapReduce, Hadoop streaming i Pig, zawierają opis kont magazynu i metadanych z nimi.

Usługa Azure Storage może być replikowana geograficznie. Mimo że replikacja geograficzna zapewnia odzyskiwanie geograficzne i nadmiarowość danych, funkcja failover lokalizacji replikowanej geograficznie poważnie wpływa na wydajność i może ponieść dodatkowe koszty. Zaleca się, aby wybrać replikację geograficzną mądrze i tylko wtedy, gdy wartość danych jest warta dodatkowych kosztów.

Jeden z następujących formatów może służyć do uzyskiwania dostępu do danych przechowywanych w usłudze Azure Storage:

|Format dostępu do danych |Opis |
|---|---|
|`wasb:///`|Uzyskaj dostęp do magazynu domyślnego przy użyciu komunikacji niezaszyfrowanej.|
|`wasbs:///`|Uzyskaj dostęp do magazynu domyślnego przy użyciu szyfrowanej komunikacji.|
|`wasb://<container-name>@<account-name>.blob.core.windows.net/`|Używane podczas komunikowania się z domyślnym kontem magazynu. |

[Cele skalowalności dla standardowych kont magazynu](../../storage/common/scalability-targets-standard-account.md) zawierają listę bieżących limitów na kontach usługi Azure Storage. Jeśli potrzeby aplikacji przekraczają cele skalowalności pojedynczego konta magazynu, aplikacja może być zbudowana w celu użycia wielu kont magazynu, a następnie partycjonowania obiektów danych na tych kontach magazynu.

[Usługa Azure Storage Analytics](../../storage/storage-analytics.md) udostępnia metryki dla wszystkich usług magazynu, a witrynę Azure Portal można skonfigurować w celu zbierania metryk do wizualizacji za pomocą wykresów. Alerty można utworzyć, aby powiadamiać o osiągnięciu progów dla metryk zasobów magazynu.

Usługa Azure Storage oferuje [usuwanie nietrwałe dla obiektów obiektów blob,](../../storage/blobs/storage-blob-soft-delete.md) aby pomóc odzyskać dane, gdy jest przypadkowo zmodyfikowany lub usunięty przez użytkownika aplikacji lub innego konta magazynu.

Można tworzyć [migawki obiektów blob](https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob). Migawka jest tylko do odczytu wersja obiektu blob, który jest wykonany w momencie w czasie i zapewnia sposób na tworzenie kopii zapasowych obiektu blob. Po utworzeniu migawki można ją odczytać, skopiować lub usunąć, ale nie zmodyfikować.

> [!Note]
> W przypadku starszych wersji lokalnych dystrybucji hadoop, które nie mają certyfikatu "wasbs", muszą zostać zaimportowane do magazynu zaufania Java.

Do importowania certyfikatów do magazynu zaufania języka Java można użyć następujących metod:

Pobieranie certyfikatu TLS/SSL usługi Azure Blob do pliku

```bash
echo -n | openssl s_client -connect <storage-account>.blob.core.windows.net:443 | sed -ne '/-BEGIN CERTIFICATE-/,/-END CERTIFICATE-/p' > Azure_Storage.cer
```

Zaimportuj powyższy plik do magazynu zaufania Java we wszystkich węzłach

```bash
keytool -import -trustcacerts -keystore /path/to/jre/lib/security/cacerts -storepass changeit -noprompt -alias blobtrust -file Azure_Storage.cer
```

Sprawdź, czy dodany certyfikat znajduje się w magazynie zaufania

```bash
keytool -list -v -keystore /path/to/jre/lib/security/cacerts
```

Aby uzyskać więcej informacji zobacz następujące artykuły:

- [Korzystanie z usługi Azure Storage z klastrami usługi Azure HDInsight](../hdinsight-hadoop-use-blob-storage.md)
- [Cele skalowalności dla standardowych kont magazynu](../../storage/common/scalability-targets-standard-account.md)
- [Cele dotyczące skalowalności i wydajności pamięci masowej obiektów Blob](../../storage/blobs/scalability-targets.md)
- [Microsoft Azure Storage Performance and Scalability Checklist (Lista kontrolna dotycząca wydajności i skalowalności usługi Microsoft Azure Storage)](../../storage/common/storage-performance-checklist.md)
- [Monitorowanie, diagnozowanie i rozwiązywanie problemów z usługą Microsoft Azure Storage](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md)
- [Monitorowanie konta magazynu w witrynie Azure Portal](../../storage/common/storage-monitor-storage-account.md)

### <a name="azure-data-lake-storage-gen1"></a>Usługa Azure Data Lake Storage 1. generacji

Usługa Azure Data Lake Storage implementuje model kontroli dostępu w stylu HDFS i POSIX. Zapewnia pierwszorzędną integrację z AAD dla precyzyjnej kontroli dostępu. Nie ma żadnych ograniczeń co do rozmiaru danych, które może przechowywać, ani jego zdolności do uruchamiania analizy masowo równoległej.

Aby uzyskać więcej informacji zobacz następujące artykuły:

- [Tworzenie klastrów usługi HDInsight za pomocą usługi Data Lake Storage przy użyciu witryny Azure portal](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)
- [Korzystanie z magazynu usługi Data Lake w klastrach usługi Azure HDInsight](../hdinsight-hadoop-use-data-lake-store.md)

### <a name="azure-data-lake-storage-gen2"></a>Usługa Azure Data Lake Storage 2. generacji

Usługa Azure Data Lake Storage Gen2 to najnowsza oferta magazynu. Ujednolica podstawowe możliwości z pierwszej generacji usługi Azure Data Lake Storage z punktem końcowym systemu plików zgodnym z usługą Hadoop bezpośrednio zintegrowanym z usługą Azure Blob Storage. To ulepszenie łączy skalę i koszty kosztów magazynu obiektów z niezawodnością i wydajnością zwykle skojarzoną tylko z lokalnymi systemami plików.

Usługa ADLS Gen 2 jest oparta na [magazynie obiektów Blob platformy Azure](../../storage/blobs/storage-blobs-introduction.md) i umożliwia interfejs z danymi przy użyciu paradygmatów magazynu plików i obiektów. Funkcje [usługi Azure Data Lake Storage Gen1](../../data-lake-store/index.yml), takie jak semantyka systemu plików, zabezpieczenia na poziomie plików i skala, są połączone z tanim, warstwowym magazynem, możliwościami wysokiej dostępności/odzyskiwania po awarii oraz dużym ekosystemem SDK/tooling z magazynu obiektów [Blob platformy Azure.](../../storage/blobs/storage-blobs-introduction.md) W usłudze Data Lake Storage Gen2 wszystkie zalety magazynu obiektów pozostają przy jednoczesnym dodaniu zalet interfejsu systemu plików zoptymalizowanego pod kątem obciążeń analitycznych.

Podstawową cechą Usługi Data Lake Storage Gen2 jest dodanie [hierarchicznego obszaru](../../storage/data-lake-storage/namespace.md) nazw do usługi magazynu obiektów Blob, która organizuje obiekty/pliki w hierarchię katalogów w celu uzyskania dostępu do danych.Struktura hierarchiczna umożliwia operacje, takie jak zmiana nazwy lub usunięcie katalogu, aby były pojedynczymi operacjami metadanych niepodzielnych w katalogu, a nie wyliczaniem i przetwarzaniem wszystkich obiektów, które współużytkują prefiks nazwy katalogu.

W przeszłości analizy oparte na chmurze musiały naruszyć bezpieczeństwo w obszarach wydajności, zarządzania i bezpieczeństwa. Najważniejsze funkcje usługi Azure Data Lake Storage (ADLS) Gen2 są następujące:

- **Dostęp zgodny z hadoop:** Usługa Azure Data Lake Storage Gen2 umożliwia zarządzanie i dostęp do danych tak samo, jak w przypadku [rozproszonego systemu plików Hadoop (HDFS).](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html) Nowy  [sterownik ABFS](../../storage/data-lake-storage/abfs-driver.md)jest dostępny we wszystkich środowiskach Apache Hadoop, które są zawarte w [usłudze Azure HDInsight.](../index.yml) Ten sterownik umożliwia dostęp do danych przechowywanych w programie Data Lake Storage Gen2.

- **Nadzstaw uprawnień POSIX:** Model zabezpieczeń dla usługi Data Lake Gen2 w pełni obsługuje uprawnienia ACL i POSIX wraz z pewnymi dodatkowymi szczegółowościami specyficznymi dla usługi Data Lake Storage Gen2. Ustawienia mogą być konfigurowane za pomocą narzędzi administracyjnych lub za pośrednictwem struktur, takich jak Hive i Spark.

- **Opłacalność:** Data Lake Storage Gen2 oferuje tanią pojemność pamięci masowej i transakcje. W miarę przechodzenia danych przez cały cykl życia stawki opłat zmieniają się, aby zminimalizować koszty za pomocą wbudowanych funkcji, takich jak [cykl życia magazynu obiektów blob platformy Azure.](../../storage/common/storage-lifecycle-management-concepts.md)

- **Współpracuje z narzędziami magazynu obiektów Blob, strukturami i aplikacjami:** Usługa Data Lake Storage Gen2 kontynuuje pracę z szeroką gamą narzędzi, struktur i aplikacji, które istnieją obecnie dla magazynu obiektów Blob.

- **Zoptymalizowany sterownik:** Sterownik systemu plików obiektów Blob azure (ABFS) jest [zoptymalizowany specjalnie](../../storage/data-lake-storage/abfs-driver.md) pod kątem analizy dużych zbiorów danych. Odpowiednie interfejsy API REST są rzuty powierzchniowe przez punkt końcowy dfs, dfs.core.windows.net.

Jeden z następujących formatów może służyć do uzyskiwania dostępu do danych przechowywanych w ADLS Gen2:
- `abfs:///`: Dostęp do domyślnego magazynu usługi Data Lake dla klastra.
- `abfs://file_system@account_name.dfs.core.windows.net`: Używany podczas komunikowania się z nieobezwłasnym magazynem usługi Data Lake.

Aby uzyskać więcej informacji zobacz następujące artykuły:

- [Wprowadzenie do usługi Azure Data Lake Storage Gen2](../../storage/data-lake-storage/introduction.md)
- [Sterownik systemu plików obiektów Blob platformy Azure (ABFS.md)](../../storage/data-lake-storage/abfs-driver.md)
- [Korzystanie z usługi Azure Data Lake Storage Gen2 z klastrami usługi Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen2.md)

## <a name="secure-azure-storage-keys-within-on-premises-hadoop-cluster-configuration"></a>Bezpieczne klucze usługi Azure Storage w lokalnej konfiguracji klastra Hadoop

Klucze usługi Azure Storage, które są dodawane do plików konfiguracyjnych Usługi Hadoop, ustanawiają łączność między lokalnym plikiem HDFS i magazynem obiektów Blob platformy Azure. Te klucze mogą być chronione przez szyfrowanie ich za pomocą struktury dostawcy poświadczeń Hadoop. Po zaszyfrowaniu można je bezpiecznie przechowywać i uzyskać do nich dostęp.

**Aby aprowizować poświadczenia:**

```bash
hadoop credential create fs.azure.account.key.account.blob.core.windows.net -value <storage key> -provider jceks://hdfs@headnode.xx.internal.cloudapp.net/path/to/jceks/file
```

**Aby dodać powyższą ścieżkę dostawcy do core-site.xml lub do konfiguracji Ambari w niestandardowej lokacji rdzenia:**

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
> Właściwość ścieżki dostawcy można również dodać do wiersza polecenia distcp zamiast przechowywania klucza na poziomie klastra w core-site.xml w następujący sposób:

```bash
hadoop distcp -D hadoop.security.credential.provider.path=jceks://hdfs@headnode.xx.internal.cloudapp.net/path/to/jceks /user/user1/ wasb:<//yourcontainer@youraccount.blob.core.windows.net/>user1
```

## <a name="restrict-azure-storage-data-access-using-sas"></a>Ograniczanie dostępu do danych usługi Azure Storage przy użyciu sygnatury dostępu współdzielonego

Usługa HDInsight domyślnie ma pełny dostęp do danych na kontach usługi Azure Storage skojarzonych z klastrem. Sygnatury dostępu współdzielonego (SAS) w kontenerze obiektów blob mogą być używane do ograniczania dostępu do danych, takich jak zapewnienie użytkownikom dostępu tylko do odczytu do danych.

### <a name="using-the-sas-token-created-with-python"></a>Korzystanie z tokenu sygnatury dostępu Współdzielonego utworzonego za pomocą pythona

1. Otwórz plik [SASToken.py](https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature/blob/master/Python/SASToken.py) i zmień następujące wartości:

    |Właściwość tokenu|Opis|
    |---|---|
    |policy_name|Nazwa używana do tworzenia przechowywanych zasad.|
    |storage_account_name|Nazwa konta magazynu.|
    |storage_account_key|Klucz dla konta magazynu.|
    |storage_container_name|Kontener na koncie magazynu, do którego chcesz ograniczyć dostęp.|
    |example_file_path|Ścieżka do pliku, który jest przekazyzony do kontenera.|

2. Plik SASToken.py jest dostarczany z `ContainerPermissions.READ + ContainerPermissions.LIST` uprawnieniami i może być dostosowywany na podstawie przypadku użycia.

3. Wykonaj skrypt w następujący sposób:`python SASToken.py`

4. Wyświetla token sygnatury dostępu Współdzielonego podobny do następującego tekstu po zakończeniu skryptu:`sr=c&si=policyname&sig=dOAi8CXuz5Fm15EjRUu5dHlOzYNtcK3Afp1xqxniEps%3D&sv=2014-02-14`

5. Aby ograniczyć dostęp do kontenera z podpisem dostępu udostępnionego, dodaj wpis niestandardowy do konfiguracji lokacji podstawowej dla klastra w obszarze Ambari HDFS Configs Advanced Custom core-site Add.

6. Użyj następujących wartości dla pól **Klucz** i **Wartość:**

    **Klucz:** `fs.azure.sas.YOURCONTAINER.YOURACCOUNT.blob.core.windows.net` **Wartość:** Klucz sygnatury dostępu Współdzielonego zwrócony przez aplikację Pythona z kroku 4 powyżej.

7. Kliknij przycisk **Dodaj,** aby zapisać ten klucz i wartość, a następnie kliknij przycisk **Zapisz,** aby zapisać zmiany konfiguracji. Po wyświetleniu monitu dodaj opis zmiany (na przykład"dodawanie dostępu do magazynu sygnatury dostępu współdzielonego), a następnie kliknij przycisk **Zapisz**.

8. W interfejsie użytkownika sieci Web Ambari wybierz hdfs z listy po lewej stronie, a następnie wybierz **pozycję Uruchom ponownie wszystkie, których dotyczy problem,** z listy rozwijanej Akcje usługi po prawej stronie. Po wyświetleniu monitu wybierz pozycję **Potwierdź ponownie wszystko**.

9. Powtórz ten proces dla MapReduce2 i YARN.

Istnieją trzy ważne rzeczy, o których należy pamiętać o używaniu tokenów sygnatury dostępu Współdzielonego na platformie Azure:

1. Gdy tokeny sygnatury dostępu Współdzielonego są tworzone z uprawnieniami "READ + LIST", użytkownicy, którzy uzyskują dostęp do kontenera obiektów Blob z tym tokenem sygnatury dostępu Współdzielonego, nie będą mogli "zapisywać i usuwać" danych. Użytkownicy, którzy uzyskują dostęp do kontenera obiektów Blob za pomocą `"This request is not authorized to perform this operation"`tego tokenu Sygnatury dostępu współdzielonego i próbują wykonać operację zapisu lub usuwania, otrzymają wiadomość w stylu .

2. Gdy tokeny sygnatury dostępu `DELETE` Współdzielonego `hadoop fs -put` są generowane `\_COPYING\_` z `READ + LIST + WRITE` uprawnieniami (tylko w celu ograniczenia), polecenia, takie jak najpierw zapis do pliku, a następnie spróbuj zmienić nazwę pliku. Ta operacja HDFS `copy+delete` jest mapowana na dla WASB. Ponieważ `DELETE` pozwolenie nie zostało dostarczone, "put" nie powiedzie się. Operacja `\_COPYING\_` jest hadoop funkcji przeznaczone do zapewnienia kontroli współbieżności. Obecnie nie ma sposobu, aby ograniczyć tylko "DELETE" operacji bez wpływu na operacje "WRITE", jak również.

3. Niestety dostawca poświadczeń hadoop i dostawca klucza odszyfrowywania (ShellDecryptionKeyProvider) obecnie nie działają z tokenami sygnatury dostępu Współdzielonego i obecnie nie można go chronić przed widocznością.

Aby uzyskać więcej informacji, zobacz [Ograniczanie dostępu do danych w programie HDInsight za pomocą podpisów dostępu udostępnionego usługi Azure Storage.](../hdinsight-storage-sharedaccesssignature-permissions.md)

## <a name="use-data-encryption-and-replication"></a>Korzystanie z szyfrowania i replikacji danych

Wszystkie dane zapisywane w usłudze Azure Storage są automatycznie szyfrowane przy użyciu [szyfrowania usługi storage service (SSE).](../../storage/common/storage-service-encryption.md) Dane na koncie usługi Azure Storage są zawsze replikowane w celu zapewnienia wysokiej dostępności.Podczas tworzenia konta magazynu można wybrać jedną z następujących opcji replikacji:

- [Magazyn lokalnie nadmiarowy (LRS)](../../storage/common/storage-redundancy-lrs.md)
- [Magazyn strefowy (ZRS)](../../storage/common/storage-redundancy-zrs.md)
- [Magazyn geograficznie nadmiarowy (GRS)](../../storage/common/storage-redundancy-grs.md)
- [Magazyn geograficznie nadmiarowy dostępny do odczytu (RA-GRS)](../../storage/common/storage-redundancy.md)

Usługa Azure Data Lake Storage zapewnia lokalnie nadmiarowego magazynu (LRS), ale należy również skopiować dane krytyczne do innego konta usługi Data Lake storage w innym regionie z częstotliwością dostosowaną do potrzeb planu odzyskiwania po awarii.Istnieją różne metody kopiowania danych, w tym [ADLCopy](../../data-lake-store/data-lake-store-copy-data-azure-storage-blob.md), [DistCp](https://hadoop.apache.org/docs/current/hadoop-distcp/DistCp.html), [Azure PowerShell](../../data-lake-store/data-lake-store-get-started-powershell.md)lub [Azure Data Factory](../../data-factory/connector-azure-data-lake-store.md).Zaleca się również wymuszanie zasad dostępu dla konta Usługi Data Lake Storage, aby zapobiec przypadkowemu usunięciu.

Aby uzyskać więcej informacji zobacz następujące artykuły:

- [Replikacja usługi Azure Storage](../../storage/common/storage-redundancy.md)
- [Wskazówki dotyczące awarii usługi Azure Data Lake Storage (ADLS)](../../data-lake-store/data-lake-store-disaster-recovery-guidance.md)

## <a name="attach-additional-azure-storage-accounts-to-cluster"></a>Dołączanie dodatkowych kont usługi Azure Storage do klastra

Podczas procesu tworzenia usługi HDInsight konto usługi Azure Storage lub konto usługi Azure Data Lake Storage jest wybierane jako domyślny system plików. Oprócz tego domyślnego konta magazynu można dodać dodatkowe konta magazynu z tej samej subskrypcji platformy Azure lub różnych subskrypcji platformy Azure podczas procesu tworzenia klastra lub po utworzeniu klastra.

Dodatkowe konto magazynu można dodać w jeden na następujący sposób:
- Ambari HDFS Config Advanced Custom core-site Dodaj nazwę konta magazynu i klucz Ponowne uruchomienie usług
- Przy użyciu [akcji Skrypt,](../hdinsight-hadoop-add-storage.md) przekazując nazwę konta magazynu i klucz

> [!Note]
> W prawidłowych przypadkach użycia limity dotyczące magazynu platformy Azure można zwiększyć za pomocą żądania złożonego do [pomocy technicznej platformy Azure.](https://azure.microsoft.com/support/faq/)

Aby uzyskać więcej informacji, zobacz [Dodawanie kolejnych kont magazynu do usługi HDInsight](../hdinsight-hadoop-add-storage.md).

## <a name="next-steps"></a>Następne kroki

Przeczytaj następny artykuł z tej serii: [Najlepsze wskazówki dotyczące migracji danych lokalnych do migracji usługi Azure HDInsight Hadoop](apache-hadoop-on-premises-migration-best-practices-data-migration.md).
