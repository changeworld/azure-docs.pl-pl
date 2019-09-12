---
title: Porównanie opcji magazynu do użycia z klastrami usługi Azure HDInsight
description: Zawiera omówienie typów magazynu i sposobu ich współdziałania z usługą Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 06/17/2019
ms.openlocfilehash: 5cb3b5c2d36707875c87bd589e3d96c0a2f4f939
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70885194"
---
# <a name="compare-storage-options-for-use-with-azure-hdinsight-clusters"></a>Porównanie opcji magazynu do użycia z klastrami usługi Azure HDInsight

Podczas tworzenia klastrów usługi HDInsight można wybrać kilka różnych usług magazynu platformy Azure:

* Azure Storage
* Azure Data Lake Storage Gen2
* Azure Data Lake Storage Gen1

Ten artykuł zawiera omówienie tych typów magazynu i ich unikatowych funkcji.

Poniższa tabela zawiera podsumowanie usług Azure Storage, które są obsługiwane przez różne wersje usługi HDInsight:

| Usługa magazynu | Typ konta | Typ przestrzeni nazw | Obsługiwane usługi | Obsługiwane warstwy wydajności | Obsługiwane warstwy dostępu | Wersja usługi HDInsight | Typ klastra |
|---|---|---|---|---|---|---|---|
|Azure Data Lake Storage Gen2| Ogólnego przeznaczenia w wersji 2 | Hierarchiczny (system plików) | Blob | Standardowa (Standard) | Gorąca, chłodna, archiwalna | 3.6 + | Wszyscy |
|Azure Storage| Ogólnego przeznaczenia w wersji 2 | Object | Blob | Standardowa (Standard) | Gorąca, chłodna, archiwalna | 3.6 + | Wszyscy |
|Azure Storage| Ogólnego przeznaczenia w wersji 1 | Object | Blob | Standardowa (Standard) | ND | Wszyscy | Wszyscy |
|Azure Storage| Blob Storage * * | Object | Blokowy obiekt blob | Standardowa (Standard) | Gorąca, chłodna, archiwalna | Wszyscy | Wszyscy |
|Azure Data Lake Storage Gen1| ND | Hierarchiczny (system plików) | ND | ND | ND | tylko 3,6 | Wszystkie z wyjątkiem HBase |

\* * W przypadku klastrów usługi HDInsight tylko konta magazynu pomocniczego mogą być typu BlobStorage.

Aby uzyskać więcej informacji na temat typów kont usługi Azure Storage, zobacz temat [konto usługi Azure Storage — Omówienie](../storage/common/storage-account-overview.md)

Aby uzyskać więcej informacji na temat warstw dostępu do usługi Azure [Storage, zobacz Azure Blob Storage: Warstwy magazynu Premium (wersja zapoznawcza), gorąca, chłodna i archiwalna](../storage/blobs/storage-blob-storage-tiers.md)

Klaster można utworzyć przy użyciu różnych kombinacji usług dla podstawowego i opcjonalnego magazynu pomocniczego. Poniższa tabela zawiera podsumowanie konfiguracji magazynu klastra, które są obecnie obsługiwane w usłudze HDInsight:

| Wersja usługi HDInsight | Magazyn podstawowy | Magazyn pomocniczy | Obsługiwane |
|---|---|---|---|
| 3,6 & 4,0 | Ogólnego przeznaczenia V1, Ogólnego przeznaczenia v2 | Ogólnego przeznaczenia V1, Ogólnego przeznaczenia v2, BlobStorage (blokowe obiekty blob) | Tak |
| 3,6 & 4,0 | Ogólnego przeznaczenia V1, Ogólnego przeznaczenia v2 | Usługa Data Lake Storage 2. generacji | Nie |
| 3,6 & 4,0 | Ogólnego przeznaczenia V1, Ogólnego przeznaczenia v2 | Usługa Data Lake Storage 1. generacji | Tak |
| 3,6 & 4,0 | Data Lake Storage Gen2 * | Usługa Data Lake Storage 2. generacji | Tak |
| 3,6 & 4,0 | Data Lake Storage Gen2 * | Ogólnego przeznaczenia V1, Ogólnego przeznaczenia v2, BlobStorage (blokowe obiekty blob) | Tak |
| 3,6 & 4,0 | Usługa Data Lake Storage 2. generacji | Usługa Data Lake Storage 1. generacji | Nie |
| 3.6 | Usługa Data Lake Storage 1. generacji | Usługa Data Lake Storage 1. generacji | Tak |
| 3.6 | Usługa Data Lake Storage 1. generacji | Ogólnego przeznaczenia V1, Ogólnego przeznaczenia v2, BlobStorage (blokowe obiekty blob) | Tak |
| 3.6 | Usługa Data Lake Storage 1. generacji | Usługa Data Lake Storage 2. generacji | Nie |
| 4.0 | Usługa Data Lake Storage 1. generacji | Any | Nie |

\* = Może to być jedno lub wiele kont Data Lake Storage Gen2, o ile są one skonfigurowane do używania tej samej tożsamości zarządzanej na potrzeby dostępu do klastra.

## <a name="use-azure-data-lake-storage-gen2-with-apache-hadoop-in-azure-hdinsight"></a>Używanie Azure Data Lake Storage Gen2 z Apache Hadoop w usłudze Azure HDInsight

Azure Data Lake Storage Gen2 pobiera podstawowe funkcje z Azure Data Lake Storage Gen1 i integruje je z usługą Azure Blob Storage. Te funkcje obejmują system plików, który jest zgodny z usługą Hadoop, Azure Active Directory (Azure AD) i list kontroli dostępu (ACL) oparty na standardzie POSIX. Ta kombinacja pozwala korzystać z wydajności Azure Data Lake Storage Gen1, a także przy użyciu zarządzania warstwami i cyklem życia danych usługi BLOB Storage.

Aby uzyskać więcej informacji na temat Azure Data Lake Storage Gen2, zobacz [wprowadzenie do Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md).

### <a name="core-functionality-of-azure-data-lake-storage-gen2"></a>Podstawowe funkcje programu Azure Data Lake Storage Gen2

* **Dostęp zgodny z usługą Hadoop:** W Azure Data Lake Storage Gen2 można zarządzać danymi i uzyskiwać do nich dostęp tak samo jak w przypadku rozproszony system plików Hadoop (HDFS). Sterownik systemu plików obiektów blob platformy Azure (ABFS) jest dostępny w ramach wszystkich środowisk Apache Hadoop, w tym usługi Azure HDInsight i Azure Databricks. Użyj ABFS, aby uzyskać dostęp do danych przechowywanych w Data Lake Storage Gen2.

* **Nadzbiór uprawnień systemu POSIX:** Model zabezpieczeń dla Data Lake Gen2 obsługuje uprawnienia list ACL i POSIX oraz niezbędny stopień szczegółowości Data Lake Storage Gen2. Ustawienia można skonfigurować za poorednictwem narzędzi administracyjnych lub platform, takich jak Apache Hive i Apache Spark.

* **Efektywność kosztów:** Data Lake Storage Gen2 oferuje niską wydajność magazynu i transakcji. Funkcje, takie jak cykl życiowy usługi Azure Blob Storage, ułatwiają obniżenie kosztów przez dostosowanie stawek rozliczeń w miarę ich cyklu życia.

* **Zgodność z narzędziami, strukturami i aplikacjami magazynu obiektów blob:** Data Lake Storage Gen2 nadal pracują z szeroką gamę narzędzi, struktur i aplikacji dla usługi BLOB Storage.

* **Zoptymalizowany sterownik:** Sterownik ABFS jest zoptymalizowany pod kątem analizy danych Big Data. Odpowiednie interfejsy API REST są nadawane za pomocą punktu końcowego rozproszonego systemu plików (DFS) dfs.core.windows.net.

### <a name="whats-new-for-azure-data-lake-storage-gen-2"></a>Co nowego w Azure Data Lake Storage generacji 2

#### <a name="managed-identities-for-secure-file-access"></a>Zarządzane tożsamości na potrzeby bezpiecznego dostępu do plików

Usługa Azure HDInsight używa zarządzanych tożsamości do zabezpieczania dostępu do klastrów do plików w Azure Data Lake Storage Gen2. Tożsamości zarządzane są funkcją Azure Active Directory, która zapewnia usługi platformy Azure z zestawem automatycznie zarządzanych poświadczeń. Przy użyciu tych poświadczeń można uwierzytelniać się w dowolnej usłudze, która obsługuje uwierzytelnianie Active Directory. Korzystanie z tożsamości zarządzanych nie wymaga przechowywania poświadczeń w plikach kodu lub konfiguracji.

Aby uzyskać więcej informacji, zobacz [zarządzane tożsamości dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/overview.md).

#### <a name="azure-blob-file-system-driver"></a>Sterownik systemu plików obiektów blob platformy Azure

Apache Hadoop aplikacje natywnie oczekują odczytu i zapisu danych z magazynu na dysku lokalnym. Sterownik systemu plików Hadoop, taki jak ABFS, umożliwia aplikacjom Hadoop współdziałanie z magazynem w chmurze przez emulowanie zwykłych operacji systemu plików usługi Hadoop. Sterownik konwertuje te polecenia otrzymane z aplikacji na operacje, które są zrozumiałe dla rzeczywistej platformy magazynu w chmurze.

Wcześniej sterownik systemu plików Hadoop przekonwertował wszystkie operacje systemu plików na wywołania interfejsu API REST usługi Azure Storage po stronie klienta, a następnie wywołał interfejs API REST. Ta konwersja po stronie klienta, jednak wywołała wiele wywołań interfejsu API REST dla operacji systemu pojedynczego pliku, takich jak zmiana nazwy pliku. ABFS przeniósł część logiki systemu plików Hadoop po stronie klienta po stronie serwera. Interfejs API Azure Data Lake Storage Gen2 jest teraz uruchamiany równolegle z interfejsem API usługi BLOB. Ta migracja poprawia wydajność, ponieważ teraz Typowe operacje systemu plików usługi Hadoop można wykonać przy użyciu jednego wywołania interfejsu API REST.

Aby uzyskać więcej informacji, [Zobacz sterownik systemu plików obiektów blob platformy Azure (ABFS): Dedykowany sterownik magazynu platformy Azure dla](../storage/blobs/data-lake-storage-abfs-driver.md)usługi Hadoop.

#### <a name="uri-scheme-for-azure-data-lake-storage-gen-2"></a>Schemat identyfikatora URI dla Azure Data Lake Storage Gen 2 

Azure Data Lake Storage Gen2 używa nowego schematu identyfikatora URI do uzyskiwania dostępu do plików w usłudze Azure Storage z usługi HDInsight:

`abfs[s]://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/<PATH>`

Schemat URI zapewnia dostęp szyfrowany przy użyciu protokołu SSL`abfss://` (prefiks) i nieszyfrowany dostęp`abfs://` (prefiks). Użyj `abfss` wszędzie tam, gdzie to możliwe, nawet w przypadku uzyskiwania dostępu do danych znajdujących się w tym samym regionie na platformie Azure.

`<FILE_SYSTEM_NAME>`Identyfikuje ścieżkę do systemu plików Data Lake Storage Gen2.

`<ACCOUNT_NAME>`Określa nazwę konta usługi Azure Storage. Wymagana jest w pełni kwalifikowana nazwa domeny (FQDN).

`<PATH>`jest nazwą ścieżki systemu plików HDFS pliku lub katalogu.

Jeśli wartości dla `<FILE_SYSTEM_NAME>` i `<ACCOUNT_NAME>` nie są określone, używany jest domyślny system plików. Dla plików w domyślnym systemie plików użyj ścieżki względnej lub ścieżki bezwzględnej. Na przykład `hadoop-mapreduce-examples.jar` plik, który jest dostarczany z klastrami usługi HDInsight, może być określony przy użyciu jednej z następujących ścieżek:

```
abfss://myfilesystempath@myaccount.dfs.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
abfss:///example/jars/hadoop-mapreduce-examples.jar /example/jars/hadoop-mapreduce-examples.jar
```

> [!Note]
> Nazwa pliku jest `hadoop-examples.jar` w klastrach usługi HDInsight w wersji 2,1 i 1,6. Podczas pracy z plikami spoza usługi HDInsight okaże się, że większość narzędzi nie rozpoznaje formatu ABFS, ale zamiast tego oczekuje podstawowego formatu ścieżki, takiego jak `example/jars/hadoop-mapreduce-examples.jar`.

Aby uzyskać więcej informacji, zobacz [Korzystanie z identyfikatora URI Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction-abfs-uri.md).

## <a name="azure-storage"></a>Azure Storage

Usługa Azure Storage to niezawodne rozwiązanie do magazynowania ogólnego przeznaczenia, które bezproblemowo integruje się z usługą HDInsight. Usługa HDInsight może używać kontenera obiektów blob w usłudze Azure Storage jako domyślnego systemu plików dla klastra. Za pomocą interfejsu HDFS pełny zestaw składników w usłudze HDInsight może działać bezpośrednio na danych ze strukturą lub bez struktury przechowywanych jako obiekty blob.

Zalecamy używanie oddzielnych kontenerów magazynu dla domyślnego magazynu klastra i danych firmowych w celu izolowania dzienników usługi HDInsight i plików tymczasowych z własnych danych firmowych. Zalecamy również usunięcie domyślnego kontenera obiektów blob, który zawiera Dzienniki aplikacji i systemu po każdym użyciu, aby zmniejszyć koszty magazynu. Koniecznie pobierz dzienniki przed usunięciem kontenera.

Jeśli wybierzesz opcję zabezpieczenia konta magazynu za pomocą ograniczeń **zapory i sieci wirtualnych** w **wybranych sieciach**, należy włączyć wyjątek **Zezwalaj na zaufane usługi firmy Microsoft...** , aby Usługa HDInsight mogła uzyskać dostęp do magazynu koncie.

### <a name="hdinsight-storage-architecture"></a>Architektura magazynu usługi HDInsight

Na poniższym diagramie przedstawiono abstrakcyjny widok architektury usługi HDInsight w usłudze Azure Storage:

![Diagram przedstawiający sposób, w jaki klastry usługi Hadoop używają interfejsu API systemu plików HDFS do uzyskiwania dostępu do danych strukturalnych i bez struktury oraz przechowywania ich w usłudze BLOB Storage](./media/hdinsight-hadoop-compare-storage-options/storage-architecture.png "Architektura HDInsight Storage")

Usługa HDInsight zapewnia dostęp do rozproszonego systemu plików, który jest lokalnie dołączony do węzłów obliczeniowych. Dostęp do tego systemu plików można uzyskać przy użyciu w pełni kwalifikowanego identyfikatora URI, na przykład:

    hdfs://<namenodehost>/<path>

Usługa HDInsight umożliwia również dostęp do danych w usłudze Azure Storage. Składnia jest następująca:

    wasb://<containername>@<accountname>.blob.core.windows.net/<path>

W przypadku korzystania z konta usługi Azure Storage z klastrami HDInsight należy wziąć pod uwagę następujące zasady:

* **Kontenery na kontach magazynu, które są połączone z klastrem:** Ponieważ nazwa konta i klucz są skojarzone z klastrem podczas tworzenia, masz pełny dostęp do obiektów BLOB w tych kontenerach.

* **Kontenery publiczne lub publiczne obiekty blob na kontach magazynu, które *nie* są połączone z klastrem:** Masz uprawnienia tylko do odczytu obiektów BLOB w kontenerach.
  
  > [!NOTE]  
  > Kontenery publiczne umożliwiają uzyskanie listy wszystkich obiektów blob, które są dostępne w tym kontenerze i uzyskiwania metadanych kontenera. Publiczne obiekty blob umożliwiają dostęp do obiektów blob jedynie osobom znającym dokładny adres URL. Aby uzyskać więcej informacji, zobacz [Zarządzanie dostępem anonimowym w trybie odczytu do kontenerów i obiektów blob](../storage/blobs/storage-manage-access-to-resources.md).

* **Prywatne kontenery na kontach magazynu, które *nie* są połączone z klastrem:** Nie można uzyskać dostępu do obiektów BLOB w kontenerach, chyba że zostanie zdefiniowane konto magazynu podczas przesyłania zadań WebHCat. 

Konta magazynu definiowane w procesie tworzenia oraz ich klucze są przechowywane w pliku %HADOOP_HOME%/conf/core-site.xml w węzłach klastra. Domyślnie Usługa HDInsight używa kont magazynu zdefiniowanych w pliku pliku Core-site. XML. To ustawienie można zmodyfikować za pomocą usługi [Apache Ambari](./hdinsight-hadoop-manage-ambari.md).

Wiele zadań WebHCat, w tym Apache Hive, MapReduce, Apache Hadoop streaming i Apache świni, może zawierać opis kont magazynu i metadanych z nimi. (Obecnie jest to prawdziwe w przypadku trzody chlewnej z kontami magazynu, ale nie dla metadanych). Aby uzyskać więcej informacji, zobacz [Korzystanie z klastra usługi HDInsight z alternatywnymi kontami magazynu i magazynami](https://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx).

Obiekty blob mogą być używane z danymi ze strukturą i bez niej. Kontenery obiektów BLOB przechowują dane jako pary klucz/wartość i nie mają hierarchii katalogów. Jednak nazwa klucza może zawierać znak ukośnika (/), aby pojawił się tak, jakby plik był przechowywany w strukturze katalogów. Na przykład klucz obiektu BLOB może być `input/log1.txt`. Nie istnieje `input` rzeczywisty katalog, ale z powodu znaku ukośnika w nazwie klucza, klucz wygląda jak ścieżka do pliku.

### <a id="benefits"></a>Korzyści z usługi Azure Storage
W przypadku klastrów obliczeniowych i zasobów magazynu, które nie znajdują się na tym samym kosztem wydajności. Te koszty są korygowane przez sposób tworzenia klastrów obliczeniowych w pobliżu zasobów konta magazynu w regionie świadczenia usługi Azure. W tym regionie węzły obliczeniowe mogą efektywnie uzyskiwać dostęp do danych za pośrednictwem szybkiej sieci w usłudze Azure Storage.

W przypadku przechowywania danych w usłudze Azure Storage zamiast systemu plików HDFS uzyskasz kilka korzyści:

* **Ponowne użycie i udostępnianie danych:** Dane w systemie plików HDFS znajdują się w klastrze obliczeniowym. Tylko te aplikacje, które mają dostęp do klastra obliczeniowego mogą używać danych za pomocą interfejsów API systemu plików HDFS. Z kolei dostęp do danych w usłudze Azure Storage można uzyskać za pomocą interfejsów API systemu plików HDFS lub interfejsów API REST usługi BLOB Storage. W związku z tym, większy zestaw aplikacji (w tym inne klastry usługi HDInsight) i narzędzia mogą służyć do tworzenia i korzystania z danych.

* **Archiwizowanie danych:** Gdy dane są przechowywane w usłudze Azure Storage, klastry usługi HDInsight używane do obliczeń można bezpiecznie usunąć bez utraty danych użytkownika.

* **Koszt magazynowania danych:** Przechowywanie danych w systemie plików DFS przez długi czas jest droższe niż przechowywanie danych w usłudze Azure Storage, ponieważ koszt klastra obliczeniowego jest wyższy niż koszt usługi Azure Storage. Ponadto, ponieważ nie trzeba ponownie ładować danych dla każdej generacji klastra obliczeniowego, są również zapisywane koszty ładowania danych.

* **Elastyczne skalowanie w poziomie:** Chociaż system plików HDFS udostępnia skalowalny w poziomie, skalowanie jest ustalane na podstawie liczby węzłów tworzonych dla klastra. Zmiana skali może być bardziej skomplikowana niż poleganie na możliwościach skalowania elastycznego, które są automatycznie dostępne w usłudze Azure Storage.

* **Replikacja geograficzna:** Usługa Azure Storage może być replikowana geograficznie. Chociaż replikacja geograficzna umożliwia odzyskiwanie geograficzne i nadmiarowość danych, przejście w tryb failover do lokalizacji z replikacją geograficzną poważnie wpływa na wydajność i może pociągnąć za sobą dodatkowe koszty. Dlatego należy ostrożnie wybierać replikację geograficzną i tylko wtedy, gdy wartość danych uzasadnia dodatkowy koszt.

Niektóre zadania i pakiety MapReduce mogą tworzyć wyniki pośrednie, które nie powinny być przechowywane w usłudze Azure Storage. W takim przypadku można wybrać przechowywanie danych w lokalnym systemie plików HDFS. Usługa HDInsight używa systemu plików DFS dla kilku wyników pośrednich w zadaniach Hive i innych procesach.

> [!NOTE]  
> Większość poleceń systemu plików HDFS (na `ls`przykład `copyFromLocal`, i `mkdir`) działają zgodnie z oczekiwaniami w usłudze Azure Storage. Tylko polecenia specyficzne dla natywnej implementacji systemu plików HDFS (zwanej systemem DFS), takie jak `fschk` i `dfsadmin`, pokazują inne zachowanie w usłudze Azure Storage.

## <a name="overview-of-azure-data-lake-storage-gen1"></a>Omówienie Azure Data Lake Storage Gen1

Azure Data Lake Storage Gen1 to krótkoterminowe repozytorium w całym przedsiębiorstwie na potrzeby obciążeń analitycznych danych Big Data. Za pomocą Azure Data Lake można przechwytywać dane dowolnego rozmiaru, typu i szybkości pozyskiwania w jednym miejscu na potrzeby analiz operacyjnych i poznawczych.

Data Lake Storage Gen1 dostępu z usługi Hadoop (dostępnej w klastrze usługi HDInsight) przy użyciu interfejsów API REST zgodnych z WebHDFS. Data Lake Storage Gen1 zaprojektowano w celu włączenia analizy przechowywanych danych i dostrajania wydajności w scenariuszach analizy danych. Poza tym obejmuje funkcje, które są niezbędne dla rzeczywistych przypadków użycia w przedsiębiorstwie. Te możliwości obejmują zabezpieczenia, możliwości zarządzania, skalowalności, niezawodności i dostępności.

Aby uzyskać więcej informacji na temat Azure Data Lake Storage Gen1, Zobacz szczegółowe [omówienie Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md).

Kluczowe możliwości Data Lake Storage Gen1 są następujące:

### <a name="compatibility-with-hadoop"></a>Zgodność z usługą Hadoop

Data Lake Storage Gen1 to Apache Hadoop system plików, który jest zgodny z systemem HDFS i współpracuje z ekosystemem usługi Hadoop.  Istniejące aplikacje lub usługi HDInsight, które korzystają z interfejsu API WebHDFS, można łatwo zintegrować z Data Lake Storage Gen1. Data Lake Storage Gen1 udostępnia również Interfejs REST zgodny z WebHDFS dla aplikacji.

Dane przechowywane w Data Lake Storage Gen1 mogą być łatwo przeanalizowane przy użyciu platform analitycznych Hadoop, takich jak MapReduce lub Hive. Klastry usługi Azure HDInsight można zainicjować i skonfigurować do bezpośredniego dostępu do danych przechowywanych w Data Lake Storage Gen1.

### <a name="unlimited-storage-petabyte-files"></a>Nieograniczony magazyn, petabajtowe pliki

Data Lake Storage Gen1 zapewnia nieograniczony magazyn i jest odpowiedni do przechowywania różnych danych do analizy. Nie nakłada to limitów rozmiarów kont, rozmiarów plików ani ilości danych, które mogą być przechowywane w usłudze Data Lake. Poszczególne pliki mogą mieć zakres od kilobajtów do petabajtów, co sprawia, że Data Lake Storage Gen1 doskonały wybór do przechowywania dowolnego typu danych. Dane są przechowywane trwale przez utworzenie wielu kopii i nie ma żadnych ograniczeń dotyczących czasu przechowywania danych w usłudze Data Lake.

### <a name="performance-tuning-for-big-data-analytics"></a>Dostrajanie wydajności do analizy danych Big Data

Data Lake Storage Gen1 jest zbudowana do uruchamiania systemów analitycznych o dużej skali, które wymagają ogromnej przepływności do wykonywania zapytań i analizowania dużej ilości danych. Data Lake rozprzestrzenia części pliku na kilka pojedynczych serwerów magazynu. Podczas analizowania danych ta konfiguracja zwiększa przepływność odczytu, gdy plik jest odczytywany równolegle.

### <a name="readiness-for-enterprise-highly-available-and-secure"></a>Gotowość dla przedsiębiorstwa: Wysoka dostępność i bezpieczeństwo

Data Lake Storage Gen1 zapewnia standardową dostępność i niezawodność. Zasoby danych są przechowywane trwale: chroni nadmiarowe kopie przed nieoczekiwanymi awariami. Przedsiębiorstwa mogą używać Data Lake Storage Gen1 w swoich rozwiązaniach jako ważnej części istniejącej platformy danych.

Data Lake Storage Gen1 również zapewnia zabezpieczenia klasy korporacyjnej dla przechowywanych danych. Aby uzyskać więcej informacji, zobacz [Zabezpieczanie danych w Azure Data Lake Storage Gen1](#DataLakeStoreSecurity).

### <a name="flexible-data-structures"></a>Elastyczne struktury danych

Data Lake Storage Gen1 mogą przechowywać dowolne dane w formacie natywnym, tak jak to, bez konieczności uprzedniego przekształcenia. Data Lake Storage Gen1 nie wymaga zdefiniowania schematu przed załadowaniem danych. Pojedyncze środowisko analityczne interpretuje dane i definiuje schemat w czasie analizy. Ponieważ może przechowywać pliki o dowolnym rozmiarze i formatach, Data Lake Storage Gen1 mogą obsługiwać dane ze strukturą, częściową strukturą i bez struktury.

Kontenery Data Lake Storage Gen1 dla danych to zasadniczo foldery i pliki. Dane przechowywane są wykonywane przy użyciu zestawów SDK, Azure Portal i Azure PowerShell. Tak długo, jak dane są umieszczane w magazynie przy użyciu tych interfejsów i odpowiednich kontenerów, można przechowywać dane dowolnego typu. Data Lake Storage Gen1 nie wykonuje żadnej specjalnej obsługi danych na podstawie typu przechowywanych danych.

## <a name="DataLakeStoreSecurity"></a>Bezpieczeństwo danych w Data Lake Storage Gen1
Data Lake Storage Gen1 używa Azure Active Directory do uwierzytelniania i używa list kontroli dostępu (ACL) do zarządzania dostępem do danych.

| **Funkcja** | **Opis** |
| --- | --- |
| Authentication |Data Lake Storage Gen1 integruje się z usługą Azure Active Directory (Azure AD) na potrzeby zarządzania tożsamościami i dostępem dla wszystkich danych przechowywanych w Data Lake Storage Gen1. Ze względu na integrację Data Lake Storage Gen1 korzyści ze wszystkich funkcji usługi Azure AD. Te funkcje obejmują uwierzytelnianie wieloskładnikowe, dostęp warunkowy, funkcję kontroli dostępu opartej na rolach, monitorowanie użycia aplikacji, monitorowanie zabezpieczeń i alerty itd. Data Lake Storage Gen1 obsługuje uwierzytelnianie przy użyciu protokołu OAuth 2,0 w ramach interfejsu REST. Zobacz [uwierzytelnianie w ramach Azure Data Lake Storage Gen1 przy użyciu Azure Active Directory](../data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md)|
| Kontrola dostępu |Data Lake Storage Gen1 zapewnia kontrolę dostępu przez obsługę uprawnień w stylu POSIX, które są udostępniane przez protokół WebHDFS. Listy kontroli dostępu można włączyć dla folderu głównego, podfolderów i poszczególnych plików. Aby uzyskać więcej informacji na temat działania list ACL w kontekście Data Lake Storage Gen1, zobacz [Kontrola dostępu w Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md). |
| Szyfrowanie |Data Lake Storage Gen1 również zapewnia szyfrowanie danych przechowywanych na koncie. Podczas tworzenia konta Data Lake Storage Gen1 należy określić ustawienia szyfrowania. Możesz zdecydować się na zaszyfrowanie danych lub rezygnację z szyfrowania. Aby uzyskać więcej informacji, zobacz [szyfrowanie w Data Lake Storage Gen1](../data-lake-store/data-lake-store-encryption.md). Aby uzyskać instrukcje dotyczące sposobu zapewnienia konfiguracji związanej z szyfrowaniem, zobacz Wprowadzenie do [Azure Data Lake Storage Gen1 przy użyciu Azure Portal](../data-lake-store/data-lake-store-get-started-portal.md). |

Aby dowiedzieć się więcej na temat zabezpieczania danych w Data Lake Storage Gen1, zobacz [Zabezpieczanie danych przechowywanych w Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-secure-data.md).

## <a name="applications-that-are-compatible-with-data-lake-storage-gen1"></a>Aplikacje zgodne z Data Lake Storage Gen1
Data Lake Storage Gen1 jest zgodny z większością składników Open Source w ekosystemie usługi Hadoop. Bardzo dobrze integruje się również z innymi usługami Azure.  Skorzystaj z poniższych linków, aby dowiedzieć się więcej o tym, jak Data Lake Storage Gen1 mogą być używane zarówno z składnikami Open Source, jak i innymi usługami platformy Azure.

* Zobacz [aplikacje danych Big Data typu "open source", które współpracują z Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-compatible-oss-other-applications.md) , aby uzyskać listę aplikacji typu "open source", które współdziałają z Data Lake Storage Gen1.
* Zobacz [integrowanie Azure Data Lake Storage Gen1 z innymi usługami platformy Azure](../data-lake-store/data-lake-store-integrate-with-other-services.md) , aby dowiedzieć się, jak używać Data Lake Storage Gen1 z innymi usługami platformy Azure w celu zapewnienia szerszego zakresu scenariuszy.
* Aby dowiedzieć się, jak używać Data Lake Storage Gen1 w scenariuszach, takich jak pozyskiwanie danych, przetwarzanie danych, pobieranie danych i Wizualizacja danych, zobacz temat [używanie Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-data-scenarios.md) .

## <a name="data-lake-storage-gen1-file-system-adl"></a>System plików Data Lake Storage Gen1 (adl://)
W środowiskach Hadoop (dostępnych w klastrze usługi HDInsight) można uzyskać dostęp do Data Lake Storage Gen1 za pomocą nowego systemu plików, AzureDataLakeFilesystem (adl://). Wydajność aplikacji i usług korzystających z adl://można zoptymalizować w sposób, który nie jest obecnie dostępny w WebHDFS. W związku z tym, gdy używasz Data Lake Storage Gen1, uzyskasz elastyczność, aby uzyskać najlepszą wydajność przy użyciu zalecanych adl://lub zachować istniejący kod, kontynuując bezpośrednio używanie interfejsu API WebHDFS. Usługa Azure HDInsight w pełni wykorzystuje AzureDataLakeFilesystem, aby zapewnić najlepszą wydajność na Data Lake Storage Gen1.

Uzyskaj dostęp do danych w Data Lake Storage Gen1, wykonując następujące czynności:

`adl://<data_lake_storage_gen1_name>.azuredatalakestore.net`

Aby uzyskać więcej informacji na temat uzyskiwania dostępu do danych w Data Lake Storage Gen1, zobacz [akcje dostępne dla przechowywanych danych](../data-lake-store/data-lake-store-get-started-portal.md#properties).



## <a name="next-steps"></a>Następne kroki

* [Wprowadzenie do Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
* [Wprowadzenie do usługi Azure Storage](../storage/common/storage-introduction.md)
