---
title: Porównywanie opcji magazynowania używanych z klastrami usługi Azure HDInsight
description: Zawiera omówienie typów magazynu i sposobu pracy z usługą Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 98d71434ac9e3f712be0cbd8c505b7d5a537e7cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79095550"
---
# <a name="compare-storage-options-for-use-with-azure-hdinsight-clusters"></a>Porównywanie opcji magazynowania używanych z klastrami usługi Azure HDInsight

Podczas tworzenia klastrów usługi HDInsight można wybrać jedną z kilku różnych usług magazynu platformy Azure:

* Azure Storage
* Usługa Azure Data Lake Storage 2. generacji
* Usługa Azure Data Lake Storage 1. generacji

Ten artykuł zawiera omówienie tych typów magazynu i ich unikatowych funkcji.

W poniższej tabeli podsumowano usługi usługi Azure Storage obsługiwane w różnych wersjach usługi HDInsight:

| Usługa magazynowania | Typ konta | Typ obszaru nazw | Obsługiwane usługi | Obsługiwane warstwy wydajności | Obsługiwane warstwy dostępu | Wersja HDInsight | Typ klastra |
|---|---|---|---|---|---|---|---|
|Usługa Azure Data Lake Storage 2. generacji| Uniwersalny V2 | Hierarchiczny (system plików) | Obiekt blob | Standardowa | Gorący, Chłodny, Archiwum | 3,6+ | Wszystkie z wyjątkiem Spark 2.1 i 2.2|
|Azure Storage| Uniwersalny V2 | Obiekt | Obiekt blob | Standardowa | Gorący, Chłodny, Archiwum | 3,6+ | Wszystkie |
|Azure Storage| Uniwersalny V1 | Obiekt | Obiekt blob | Standardowa | Nie dotyczy | Wszystkie | Wszystkie |
|Azure Storage| Przechowywanie obiektów blob** | Obiekt | Blok blob | Standardowa | Gorący, Chłodny, Archiwum | Wszystkie | Wszystkie |
|Usługa Azure Data Lake Storage 1. generacji| Nie dotyczy | Hierarchiczny (system plików) | Nie dotyczy | Nie dotyczy | Nie dotyczy | 3.6 Tylko | Wszystkie z wyjątkiem HBase |

**W przypadku klastrów HDInsight tylko konta magazynu pomocniczego mogą być typu BlobStorage, a obiekt Blob strony nie jest obsługiwaną opcją magazynu.

Aby uzyskać więcej informacji na temat typów kont usługi Azure Storage, zobacz [omówienie konta magazynu platformy Azure](../storage/common/storage-account-overview.md)

Aby uzyskać więcej informacji na temat warstw dostępu usługi Azure Storage, zobacz [usługi Azure Blob storage: Premium (wersja zapoznawcza), Warstwy magazynu gorąca, chłodna i archiwum](../storage/blobs/storage-blob-storage-tiers.md)

Można utworzyć klaster przy użyciu różnych kombinacji usług dla magazynu pomocniczego i podstawowego. W poniższej tabeli podsumowano konfiguracje magazynu klastra, które są obecnie obsługiwane w programie HDInsight:

| Wersja HDInsight | Magazyn podstawowy | Pamięć dodatkowa | Obsługiwane |
|---|---|---|---|
| 3,6 & 4,0 | Ogólnego przeznaczenia V1 , ogólnego przeznaczenia V2 | Ogólnego przeznaczenia V1 , ogólnego przeznaczenia V2, BlobStorage (blokowe obiekty blob) | Tak |
| 3,6 & 4,0 | Ogólnego przeznaczenia V1 , ogólnego przeznaczenia V2 | Usługa Data Lake Storage 2. generacji | Nie |
| 3,6 & 4,0 | Pamięć masowa Data Lake Gen2* | Usługa Data Lake Storage 2. generacji | Tak |
| 3,6 & 4,0 | Pamięć masowa Data Lake Gen2* | Ogólnego przeznaczenia V1 , ogólnego przeznaczenia V2, BlobStorage (blokowe obiekty blob) | Tak |
| 3,6 & 4,0 | Usługa Data Lake Storage 2. generacji | Usługa Data Lake Storage 1. generacji | Nie |
| 3.6 | Usługa Data Lake Storage 1. generacji | Usługa Data Lake Storage 1. generacji | Tak |
| 3.6 | Usługa Data Lake Storage 1. generacji | Ogólnego przeznaczenia V1 , ogólnego przeznaczenia V2, BlobStorage (blokowe obiekty blob) | Tak |
| 3.6 | Usługa Data Lake Storage 1. generacji | Usługa Data Lake Storage 2. generacji | Nie |
| 4.0 | Usługa Data Lake Storage 1. generacji | Dowolne | Nie |
| 4.0 | Ogólnego przeznaczenia V1 , ogólnego przeznaczenia V2 | Usługa Data Lake Storage 1. generacji | Nie |

*=Może to być jedno lub wiele kont Data Lake Storage Gen2, o ile wszystkie są skonfigurowane do używania tej samej tożsamości zarządzanej dla dostępu do klastra.

> [!Note] 
> Magazyn podstawowy magazynu usługi Data Lake Storage Gen2 nie jest obsługiwany dla klastrów platformy Spark 2.1 lub 2.2. 

## <a name="use-azure-data-lake-storage-gen2-with-apache-hadoop-in-azure-hdinsight"></a>Korzystanie z usługi Azure Data Lake Storage Gen2 z apache Hadoop w usłudze Azure HDInsight

Usługa Azure Data Lake Storage Gen2 pobiera podstawowe funkcje z usługi Azure Data Lake Storage Gen1 i integruje je z magazynem obiektów blob platformy Azure. Funkcje te obejmują system plików, który jest zgodny z Hadoop, Azure Active Directory (Azure AD) i posix oparte na listach kontroli dostępu (Listy KONTROLI DOSTĘPU). Ta kombinacja umożliwia korzystanie z wydajności usługi Azure Data Lake Storage Gen1 przy jednoczesnym użyciu zarządzania warstwami i cyklu życia danych magazynu obiektów Blob.

Aby uzyskać więcej informacji na temat usługi Azure Data Lake Storage Gen2, zobacz [Wprowadzenie do usługi Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md).

### <a name="core-functionality-of-azure-data-lake-storage-gen2"></a>Podstawowa funkcjonalność usługi Azure Data Lake Storage Gen2

* **Dostęp zgodny z Hadoop:** W usłudze Azure Data Lake Storage Gen2 można zarządzać i uzyskiwać dostęp do danych tak samo, jak w przypadku rozproszonego systemu plików Hadoop (HDFS). Sterownik systemu plików obiektów Blob (Azure Blob File System) (ABFS) jest dostępny we wszystkich środowiskach Apache Hadoop, w tym usługi Azure HDInsight i Azure Databricks. Użyj ABFS, aby uzyskać dostęp do danych przechowywanych w data lake storage gen2.

* **Nadzbiór uprawnień POSIX:** Model zabezpieczeń dla usługi Data Lake Gen2 obsługuje uprawnienia ACL i POSIX wraz z kilkoma dodatkowymi szczegółowościami specyficznymi dla usługi Data Lake Storage Gen2. Ustawienia można konfigurować za pomocą narzędzi administracyjnych lub struktur, takich jak Apache Hive i Apache Spark.

* **Opłacalność:** Data Lake Storage Gen2 oferuje tanią pojemność pamięci masowej i transakcje. Funkcje, takie jak cykl życia magazynu obiektów Blob platformy Azure pomagają obniżyć koszty, dostosowując stawki rozliczeniowe w miarę przechodzenia danych przez cały cykl życia.

* **Zgodność z narzędziami magazynu obiektów Blob, strukturami i aplikacjami:** Data Lake Storage Gen2 kontynuuje pracę z szeroką gamą narzędzi, struktur i aplikacji dla magazynu obiektów Blob.

* **Zoptymalizowany sterownik:** Sterownik ABFS jest zoptymalizowany specjalnie pod kątem analizy dużych zbiorów danych. Odpowiednie interfejsy API REST są rzutowane za pośrednictwem punktu końcowego rozproszonego systemu plików (DFS), dfs.core.windows.net.

### <a name="whats-new-for-azure-data-lake-storage-gen-2"></a>Co nowego w usłudze Azure Data Lake Storage Gen 2

#### <a name="managed-identities-for-secure-file-access"></a>Tożsamości zarządzane dla bezpiecznego dostępu do plików

Usługa Azure HDInsight używa tożsamości zarządzanych do bezpiecznego dostępu klastra do plików w usłudze Azure Data Lake Storage Gen2. Tożsamości zarządzane są funkcją usługi Azure Active Directory, która zapewnia usługom platformy Azure zestaw automatycznie zarządzanych poświadczeń. Te poświadczenia mogą służyć do uwierzytelniania w dowolnej usłudze obsługującej uwierzytelnianie usługi Active Directory. Przy użyciu tożsamości zarządzanych nie wymaga przechowywania poświadczeń w plikach kodu lub konfiguracji.

Aby uzyskać więcej informacji, zobacz [Zarządzane tożsamości zasobów platformy Azure](../active-directory/managed-identities-azure-resources/overview.md).

#### <a name="azure-blob-file-system-driver"></a>Sterownik systemu plików obiektów Blob platformy Azure

Apache Hadoop aplikacje natywnie oczekiwać odczytu i zapisu danych z magazynu dysków lokalnych. Sterownik systemu plików Hadoop, taki jak ABFS, umożliwia aplikacjom Hadoop pracę z pamięcią masową w chmurze poprzez emulację regularnych operacji systemu plików Hadoop. Sterownik konwertuje te polecenia odebrane z aplikacji na operacje, które rozumie rzeczywista platforma magazynu w chmurze.

Wcześniej sterownik systemu plików Hadoop konwertował wszystkie operacje systemu plików na wywołania interfejsu API rest usługi Azure Storage po stronie klienta, a następnie wywołał interfejs API REST. Ta konwersja po stronie klienta spowodowała jednak wiele wywołań interfejsu API REST dla operacji pojedynczego systemu plików, takich jak zmiana nazwy pliku. ABFS przeniósł niektóre logiki systemu plików Hadoop od strony klienta do strony serwera. Interfejs API usługi Azure Data Lake Storage Gen2 działa teraz równolegle z interfejsem API obiektów Blob. Ta migracja zwiększa wydajność, ponieważ teraz typowe operacje systemu plików Hadoop mogą być wykonywane za pomocą jednego wywołania interfejsu API REST.

Aby uzyskać więcej informacji, zobacz [sterownik systemu plików obiektów Blob azure (ABFS): dedykowany sterownik usługi Azure Storage dla usługi Hadoop](../storage/blobs/data-lake-storage-abfs-driver.md).

#### <a name="uri-scheme-for-azure-data-lake-storage-gen-2"></a>Schemat identyfikatorów URI dla usługi Azure Data Lake Storage Gen 2 

Usługa Azure Data Lake Storage Gen2 używa nowego schematu identyfikatorów URI do uzyskiwania dostępu do plików w usłudze Azure Storage z usługi HDInsight:

`abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/<PATH>`

Schemat identyfikatorów URI zapewnia dostęp zaszyfrowany za szyfrowany ssl.

`<FILE_SYSTEM_NAME>`identyfikuje ścieżkę systemu plików Data Lake Storage Gen2.

`<ACCOUNT_NAME>`identyfikuje nazwę konta usługi Azure Storage. Wymagana jest w pełni kwalifikowana nazwa domeny (FQDN).

`<PATH>`jest nazwą ścieżki pliku lub katalogu HDFS.

Jeśli wartości `<FILE_SYSTEM_NAME>` `<ACCOUNT_NAME>` dla i nie są określone, używany jest domyślny system plików. W przypadku plików w domyślnym systemie plików użyj ścieżki względnej lub ścieżki bezwzględnej. Na przykład `hadoop-mapreduce-examples.jar` plik dostarczany z klastrami HDInsight można odwoływać się przy użyciu jednej z następujących ścieżek:

```
abfs://myfilesystempath@myaccount.dfs.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
abfs:///example/jars/hadoop-mapreduce-examples.jar /example/jars/hadoop-mapreduce-examples.jar
```

> [!Note]
> Nazwa pliku `hadoop-examples.jar` znajduje się w klastrach HDInsight w wersjach 2.1 i 1.6. Podczas pracy z plikami spoza hdinsight, przekonasz się, że większość narzędzi nie rozpoznaje formatu ABFS, `example/jars/hadoop-mapreduce-examples.jar`ale zamiast tego oczekuje podstawowego formatu ścieżki, takiego jak .

Aby uzyskać więcej informacji, zobacz [Korzystanie z identyfikatora URI usługi Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction-abfs-uri.md).

## <a name="azure-storage"></a>Azure Storage

Usługa Azure Storage to niezawodne rozwiązanie magazynu ogólnego przeznaczenia, które bezproblemowo integruje się z usługą HDInsight. Usługa HDInsight może używać kontenera obiektów blob w usłudze Azure Storage jako domyślnego systemu plików dla klastra. Za pośrednictwem interfejsu HDFS pełny zestaw składników w programie HDInsight może działać bezpośrednio na ustrukturyzowanych lub nieustrukturyzowanych danych przechowywanych jako obiekty blob.

Zaleca się użycie oddzielnych kontenerów magazynu dla domyślnego magazynu klastra i danych biznesowych, aby odizolować dzienniki HDInsight i pliki tymczasowe od własnych danych biznesowych. Zaleca się również usunięcie domyślnego kontenera obiektów blob, który zawiera dzienniki aplikacji i systemu, po każdym użyciu w celu zmniejszenia kosztów magazynowania. Koniecznie pobierz dzienniki przed usunięciem kontenera.

Jeśli zdecydujesz się zabezpieczyć swoje konto magazynu za pomocą ograniczeń **zapory i sieci wirtualnych** w **wybranych sieciach,** należy włączyć wyjątek **Zezwalaj na zaufane usługi firmy Microsoft...** aby usługa HDInsight mogła uzyskać dostęp do twojego konta magazynu.

### <a name="hdinsight-storage-architecture"></a>Architektura magazynu usługi HDInsight

Poniższy diagram zawiera abstrakcyjny widok architektury HDInsight usługi Azure Storage:

![Architektura pamięci masowej HDInsight](./media/hdinsight-hadoop-compare-storage-options/storage-architecture.png "Architektura pamięci masowej HDInsight")

Usługa HDInsight zapewnia dostęp do rozproszonego systemu plików, który jest lokalnie dołączony do węzłów obliczeniowych. Dostęp do tego systemu plików można uzyskać przy użyciu w pełni kwalifikowanego identyfikatora URI, na przykład:

    hdfs://<namenodehost>/<path>

Za pośrednictwem usługi HDInsight można również uzyskać dostęp do danych w usłudze Azure Storage. Składnia jest następująca:

    wasb://<containername>@<accountname>.blob.core.windows.net/<path>

Należy wziąć pod uwagę następujące zasady podczas korzystania z konta usługi Azure Storage z klastrami usługi HDInsight:

* **Kontenery w ramach kont magazynu, które są podłączone do klastra:** ponieważ nazwa konta i klucz są kojarzone z klastrem podczas tworzenia, masz pełny dostęp do obiektów blob w tych kontenerach.

* **Kontenery publiczne lub publiczne obiekty blob na kontach magazynu, które *nie* są połączone z klastrem:** Masz uprawnienia tylko do odczytu do obiektów blob w kontenerach.
  
  > [!NOTE]  
  > Kontenery publiczne umożliwiają uzyskanie listy wszystkich obiektów blob, które są dostępne w tym kontenerze i uzyskać metadane kontenera. Publiczne obiekty blob umożliwiają dostęp do obiektów blob jedynie osobom znającym dokładny adres URL. Aby uzyskać więcej informacji, zobacz [Zarządzanie anonimowym dostępem do odczytu kontenerów i obiektów blob.](../storage/blobs/storage-manage-access-to-resources.md)

* **Kontenery prywatne na kontach magazynu, które *nie* są połączone z klastrem:** Nie można uzyskać dostępu do obiektów blob w kontenerach, chyba że zdefiniujesz konto magazynu podczas przesyłania zadań WebHCat. 

Konta magazynu definiowane w procesie tworzenia oraz ich klucze są przechowywane w pliku %HADOOP_HOME%/conf/core-site.xml w węzłach klastra. Domyślnie program HDInsight używa kont magazynu zdefiniowanych w pliku core-site.xml. To ustawienie można zmodyfikować za pomocą [programu Apache Ambari](./hdinsight-hadoop-manage-ambari.md).

Wiele zadań WebHCat, w tym Apache Hive, MapReduce, Apache Hadoop streaming i Apache Pig, może nosić ze sobą opis kont magazynu i metadanych. (Jest to obecnie prawdziwe w przypadku świni z kontami magazynu, ale nie dla metadanych.) Aby uzyskać więcej informacji, zobacz [Korzystanie z klastra HDInsight z alternatywnymi kontami magazynu i sklepami.](https://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx)

Obiekty blob mogą być używane z danymi ze strukturą i bez niej. Kontenery obiektów blob przechowują dane jako pary kluczy/wartości i nie mają hierarchii katalogów. Jednak nazwa klucza może zawierać znak ukośnika ( / ), aby wyglądał tak, jakby plik był przechowywany w strukturze katalogów. Na przykład klucz obiektu blob `input/log1.txt`może być . Nie `input` istnieje rzeczywisty katalog, ale ze względu na znak ukośnika w nazwie klucza wygląda jak ścieżka pliku.

### <a name="benefits-of-azure-storage"></a><a id="benefits"></a>Korzyści z usługi Azure Storage
Klastry obliczeniowe i zasoby magazynu, które nie są współlokowane, mają implikowane koszty wydajności. Koszty te są ograniczane przez sposób klastrów obliczeniowych są tworzone w pobliżu zasobów konta magazynu w regionie platformy Azure. W tym regionie węzły obliczeniowe mogą skutecznie uzyskiwać dostęp do danych za pośrednictwem sieci o dużej szybkości w usłudze Azure Storage.

Podczas przechowywania danych w usłudze Azure Storage zamiast hdfs, można uzyskać kilka korzyści:

* **Udostępnianie i ponowne użycie danych:** dane w systemie plików HDFS znajdują się wewnątrz klastra obliczeniowego. Tylko te aplikacje, które mają dostęp do klastra obliczeniowego mogą używać danych za pomocą interfejsów API systemu plików HDFS. Dane w usłudze Azure Storage, z drugiej strony, można uzyskać za pośrednictwem interfejsów API HDFS lub interfejsów API REST magazynu obiektów blob. Z powodu tego układu większy zestaw aplikacji (w tym innych klastrów HDInsight) i narzędzia mogą być używane do tworzenia i korzystania z danych.

* **Archiwizacja danych:** Gdy dane są przechowywane w usłudze Azure Storage, klastry HDInsight używane do obliczeń można bezpiecznie usunąć bez utraty danych użytkownika.

* **Koszt przechowywania danych:** Przechowywanie danych w systemie plików DFS w dłuższej perspektywie jest bardziej kosztowne niż przechowywanie danych w usłudze Azure Storage, ponieważ koszt klastra obliczeniowego jest wyższy niż koszt usługi Azure Storage. Ponadto ponieważ dane nie muszą być ponownie ładowane dla każdej generacji klastra obliczeniowego, oszczędzasz również koszty ładowania danych.

* **Elastyczne skalowanie w poziomie:** chociaż system plików HDFS zapewnia skalowanie w poziomie, skala jest wyznaczana przez liczbę węzłów tworzonych dla klastra. Zmiana skali może być bardziej skomplikowana niż poleganie na elastycznych możliwościach skalowania, które są automatycznie uchwalane w usłudze Azure Storage.

* **Replikacja geograficzna:** Usługa Azure Storage może być replikowana geograficznie. Mimo że replikacja geograficzna zapewnia odzyskiwanie geograficzne i nadmiarowość danych, funkcja failover lokalizacji replikowanej geograficznie poważnie wpływa na wydajność i może ponieść dodatkowe koszty. Dlatego wybierz replikację geograficzną ostrożnie i tylko wtedy, gdy wartość danych uzasadnia dodatkowy koszt.

Niektóre zadania i pakiety MapReduce mogą tworzyć wyniki pośrednie, których nie chcesz przechowywać w usłudze Azure Storage. W takim przypadku można zapisać dane w lokalnym pliku HDFS. HdInsight używa systemu plików DFS dla kilku z tych wyników pośrednich w zadaniach hive i innych procesach.

> [!NOTE]  
> Większość poleceń HDFS (na `copyFromLocal`przykład `mkdir` `ls`, i ) działa zgodnie z oczekiwaniami w usłudze Azure Storage. Tylko polecenia, które są specyficzne dla implementacji natywnego systemu PLIKÓW HDFS (który jest określany jako DFS), takich jak `fschk` i `dfsadmin`, pokaż różne zachowanie w usłudze Azure Storage.

## <a name="overview-of-azure-data-lake-storage-gen1"></a>Omówienie usługi Azure Data Lake Storage Gen1

Usługa Azure Data Lake Storage Gen1 to repozytorium hiperskalowe dla obciążeń analitycznych dużych zbiorów danych w całej organizacji. Korzystając z usługi Azure Data Lake, można przechwytywać dane o dowolnym rozmiarze, typie i szybkości pozyskiwania w jednym miejscu w celu analizy operacyjnej i eksploracyjnej.

Uzyskaj dostęp do magazynu danych Lake Gen1 z usługi Hadoop (dostępnego z klastrem HDInsight) przy użyciu interfejsów API REST zgodnych ze standardem WebHDFS. Data Lake Storage Gen1 został zaprojektowany, aby umożliwić analizę przechowywanych danych i jest dostrojony pod kątem wydajności w scenariuszach analizy danych. Po wyjęciu z pudełka zawiera możliwości, które są niezbędne dla rzeczywistych przypadków użycia w przedsiębiorstwie. Te funkcje obejmują zabezpieczenia, łatwość zarządzania, skalowalność, niezawodność i dostępność.

Aby uzyskać więcej informacji na temat usługi Azure Data Lake Storage Gen1, zobacz szczegółowe [omówienie usługi Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md).

Najważniejsze możliwości usługi Data Lake Storage Gen1 są następujące.

### <a name="compatibility-with-hadoop"></a>Kompatybilność z Hadoop

Data Lake Storage Gen1 to system plików Apache Hadoop, który jest kompatybilny z HDFS i współpracuje z ekosystemem Hadoop.  Istniejące aplikacje lub usługi HDInsight korzystające z interfejsu API usługi WebHDFS można łatwo zintegrować z usługą Data Lake Storage Gen1. Data Lake Storage Gen1 udostępnia również interfejs REST zgodny z usługą WebHDFS dla aplikacji.

Dane przechowywane w uszczerzenia danych Lake Gen1 można łatwo analizować przy użyciu struktur analitycznych Hadoop, takich jak MapReduce lub Hive. Klastry usługi Azure HDInsight można aprowizować i skonfigurować w celu bezpośredniego dostępu do danych przechowywanych w usłudze Data Lake Storage Gen1.

### <a name="unlimited-storage-petabyte-files"></a>Nieograniczony magazyn, petabajtowe pliki

Data Lake Storage Gen1 zapewnia nieograniczoną ilość miejsca do magazynowania i nadaje się do przechowywania różnych danych do analizy. Nie nakłada limitów na rozmiary kont, rozmiary plików ani ilość danych, które mogą być przechowywane w uliczce danych. Poszczególne pliki mogą mieć rozmiar od kilobajtów do petabajtów, dzięki czemu pamięć masowa Data Lake Storage Gen1 jest doskonałym wyborem do przechowywania dowolnego typu danych. Dane są przechowywane trwale przez tworzenie wielu kopii i nie ma żadnych ograniczeń co do czasu, przez jaki dane mogą być przechowywane w ułowieniu danych.

### <a name="performance-tuning-for-big-data-analytics"></a>Dostrajanie wydajności w przypadku analizy dużych zbiorów danych

Data Lake Storage Gen1 jest zbudowany do uruchamiania dużych systemów analitycznych, które wymagają ogromnej przepływności do wykonywania zapytań i analizowania dużych ilości danych. Jezioro danych rozprzestrzenia części pliku na kilku pojedynczych serwerach pamięci masowej. Podczas analizowania danych, ta konfiguracja poprawia przepływność odczytu, gdy plik jest odczytywany równolegle.

### <a name="readiness-for-enterprise-highly-available-and-secure"></a>Gotowość dla przedsiębiorstw: Wysoka dostępna i bezpieczna

Data Lake Storage Gen1 zapewnia dostępność i niezawodność w standardzie branżowym. Zasoby danych są przechowywane trwale: nadmiarowe kopie chronią przed nieoczekiwanymi awariami. Przedsiębiorstwa mogą używać usługi Data Lake Storage Gen1 w swoich rozwiązaniach jako ważnej części istniejącej platformy danych.

Data Lake Storage Gen1 zapewnia również bezpieczeństwo przechowywanech danych klasy korporacyjnej. Aby uzyskać więcej informacji, zobacz [zabezpieczanie danych w usłudze Azure Data Lake Storage Gen1](#DataLakeStoreSecurity).

### <a name="flexible-data-structures"></a>Elastyczne struktury danych

Data Lake Storage Gen1 może przechowywać dowolne dane w formacie macierzystym, tak jak jest, bez konieczności wcześniejszych przekształceń. Data Lake Storage Gen1 nie wymaga zdefiniowania schematu przed załadowaniem danych. Indywidualna struktura analityczna interpretuje dane i definiuje schemat w czasie analizy. Ponieważ może przechowywać pliki o dowolnych rozmiarach i formatach, Usługa Data Lake Storage Gen1 może obsługiwać dane strukturalne, częściowo zorganizowane i nieustrukturyzowane.

Kontenery Data Lake Storage Gen1 dla danych są zasadniczo folderami i plikami. Działasz na przechowywanych danych przy użyciu zestawów SDK, witryny Azure portal i programu Azure PowerShell. Tak długo, jak można umieścić dane w magazynie przy użyciu tych interfejsów i odpowiednich kontenerów, można przechowywać dowolny typ danych. Usługa Data Lake Storage Gen1 nie wykonuje żadnej specjalnej obsługi danych na podstawie typu danych, które przechowuje.

## <a name="data-security-in-data-lake-storage-gen1"></a><a name="DataLakeStoreSecurity"></a>Bezpieczeństwo danych w pamięci masowej w uł.o11
Usługa Data Lake Storage Gen1 używa usługi Azure Active Directory do uwierzytelniania i używa list kontroli dostępu (ACL) do zarządzania dostępem do danych.

| **Funkcja** | **Opis** |
| --- | --- |
| Uwierzytelnianie |Usługa Data Lake Storage Gen1 integruje się z usługą Azure Active Directory (Azure AD) w celu zarządzania tożsamościami i dostępem dla wszystkich danych przechowywanych w usłudze Data Lake Storage Gen1. Ze względu na integrację usługa Data Lake Storage Gen1 korzysta ze wszystkich funkcji usługi Azure AD. Funkcje te obejmują uwierzytelnianie wieloskładnikowe, dostęp warunkowy, kontrolę dostępu opartą na rolach, monitorowanie użycia aplikacji, monitorowanie zabezpieczeń i alerty itd. Data Lake Storage Gen1 obsługuje protokół OAuth 2.0 do uwierzytelniania w interfejsie REST. Zobacz [Uwierzytelnianie w usłudze Azure Data Lake Storage Gen1 przy użyciu usługi Azure Active Directory](../data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md)|
| Kontrola dostępu |Data Lake Storage Gen1 zapewnia kontrolę dostępu, obsługując uprawnienia w stylu POSIX, które są udostępniane przez protokół WebHDFS. Listy kontroli dostępu można włączyć dla folderu głównego, podfolderów i poszczególnych plików. Aby uzyskać więcej informacji na temat działania list ACL w kontekście pamięci masowej Data Lake Storage Gen1, zobacz [Kontrola dostępu w programie Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md). |
| Szyfrowanie |Data Lake Storage Gen1 zapewnia również szyfrowanie danych przechowywanych na koncie. Ustawienia szyfrowania można określić podczas tworzenia konta Usługi Data Lake Storage Gen1. Możesz zdecydować się na szyfrowanie danych lub zdecydować się na brak szyfrowania. Aby uzyskać więcej informacji, zobacz [Szyfrowanie w pamięci masowej usługi Data Lake Gen1](../data-lake-store/data-lake-store-encryption.md). Aby uzyskać instrukcje dotyczące dostarczania konfiguracji związanej z szyfrowaniem, zobacz Wprowadzenie do [usługi Azure Data Lake Storage Gen1 przy użyciu witryny Azure portal.](../data-lake-store/data-lake-store-get-started-portal.md) |

Aby dowiedzieć się więcej o zabezpieczaniu danych w usłudze Data Lake Storage Gen1, zobacz [Zabezpieczanie danych przechowywanych w usłudze Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-secure-data.md).

## <a name="applications-that-are-compatible-with-data-lake-storage-gen1"></a>Aplikacje zgodne z programem Data Lake Storage Gen1
Data Lake Storage Gen1 jest kompatybilny z większością komponentów open source w ekosystemie Hadoop. Bardzo dobrze integruje się również z innymi usługami Azure.  Skorzystaj z poniższych łączy, aby dowiedzieć się więcej o tym, jak usługa Data Lake Storage Gen1 może być używana zarówno ze składnikami typu open source, jak i z innymi usługami platformy Azure.

* Zobacz [aplikacje big data typu open source, które współpracują z usługą Azure Data Lake Storage Gen1,](../data-lake-store/data-lake-store-compatible-oss-other-applications.md) aby uzyskać listę aplikacji typu open source, które współpracują z usługą Data Lake Storage Gen1.
* Zobacz [integrowanie usługi Azure Data Lake Storage Gen1 z innymi usługami platformy Azure,](../data-lake-store/data-lake-store-integrate-with-other-services.md) aby dowiedzieć się, jak korzystać z usługi Data Lake Storage Gen1 z innymi usługami platformy Azure, aby włączyć szerszy zakres scenariuszy.
* Zobacz [Korzystanie z usługi Azure Data Lake Storage Gen1, aby uzyskać wymagania dotyczące dużych zbiorów danych,](../data-lake-store/data-lake-store-data-scenarios.md) aby dowiedzieć się, jak korzystać z usługi Data Lake Storage Gen1 w scenariuszach, takich jak pozyskiwania danych, przetwarzania danych, pobierania danych i wizualizacji danych.

## <a name="data-lake-storage-gen1-file-system-adl"></a>System plików Data Lake Storage Gen1 (adl://)
W środowiskach Hadoop (dostępnych w klastrze HDInsight) można uzyskać dostęp do magazynu usługi Data Lake Gen1 za pośrednictwem nowego systemu plików AzureDataLakeFilesystem (adl://). Wydajność aplikacji i usług korzystających z adl:// można zoptymalizować w sposób, który nie jest obecnie dostępny w uprzeglądarce WebHDFS. W rezultacie podczas korzystania z usługi Data Lake Storage Gen1, można uzyskać elastyczność albo korzystać z najlepszej wydajności przy użyciu zalecanej adl:// lub utrzymania istniejącego kodu, kontynuując korzystanie z interfejsu API WebHDFS bezpośrednio. Usługa Azure HDInsight w pełni wykorzystuje system AzureDataLakeFilesystem, aby zapewnić najlepszą wydajność w usłudze Data Lake Storage Gen1.

Dostęp do danych w aplikacji Data Lake Storage Gen1 przy użyciu następujących elementów:

`adl://<data_lake_storage_gen1_name>.azuredatalakestore.net`

Aby uzyskać więcej informacji na temat uzyskiwania dostępu do danych w programie Data Lake Storage Gen1, zobacz [Akcje dostępne na przechowywanych danych](../data-lake-store/data-lake-store-get-started-portal.md#properties).



## <a name="next-steps"></a>Następne kroki

* [Wprowadzenie do usługi Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
* [Wprowadzenie do usługi Azure Storage](../storage/common/storage-introduction.md)
