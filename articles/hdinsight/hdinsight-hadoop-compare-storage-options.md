---
title: Porównanie opcji magazynu do użycia z klastrami usługi Azure HDInsight
description: Zawiera omówienie typów magazynów i ich współdziałania z usługą Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/08/2019
ms.openlocfilehash: ac1a0e4eadc0b84fdd2a170c2e0f6e0a2f2af3a4
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59361778"
---
# <a name="compare-storage-options-for-use-with-azure-hdinsight-clusters"></a>Porównanie opcji magazynu do użycia z klastrami usługi Azure HDInsight

Możesz wybrać między kilka usług innego magazynu platformy Azure podczas tworzenia klastrów HDInsight:

* Azure Storage
* Usługa Azure Data Lake Storage 2. generacji
* Usługa Azure Data Lake Storage 1. generacji

Ten artykuł zawiera omówienie tych typów magazynów i ich unikatowych funkcji.

Poniższa tabela zawiera podsumowanie usług Azure Storage, które są obsługiwane z użyciem różnych wersji HDInsight:

| Usługa magazynu | Typ konta | Typ Namespace | Obsługiwane usługi | Warstwy wydajności obsługiwane | Warstwy dostępu obsługiwane | Wersja usługi HDInsight | Typ klastra |
|---|---|---|---|---|---|---|---|
|Usługa Azure Data Lake Storage 2. generacji| Ogólnego przeznaczenia w wersji 2 | Hierarchiczne (system plików) | Obiekt blob | Standardowa (Standard) | Gorąca, chłodna, archiwum | 3.6 + | Wszyscy |
|Azure Storage| Ogólnego przeznaczenia w wersji 2 | Obiekt | Obiekt blob | Standardowa (Standard) | Gorąca, chłodna, archiwum | 3.6 + | Wszyscy |
|Azure Storage| Ogólnego przeznaczenia w wersji 1 | Obiekt | Obiekt blob | Standardowa (Standard) | ND | Wszyscy | Wszyscy |
|Azure Storage| Blob Storage | Obiekt | Obiekt blob | Standardowa (Standard) | Gorąca, chłodna, archiwum | Wszyscy | Wszyscy |
|Usługa Azure Data Lake Storage 1. generacji| ND | Hierarchiczne (system plików) | ND | ND | ND | Tylko 3.6 | Wszystkie regiony z wyjątkiem HBase |

Aby uzyskać więcej informacji na temat warstw dostępu do magazynu Azure, zobacz [usługi Azure Blob storage: — Wersja Premium (wersja zapoznawcza), warstw magazynowania gorąca, chłodna i archiwum](../storage/blobs/storage-blob-storage-tiers.md)

Można utworzyć klastra przy użyciu różnych kombinacji usług podstawowych i opcjonalnie magazynu pomocniczego. Poniższa tabela zawiera podsumowanie konfiguracji magazynu klastra, które są obecnie obsługiwane w HDInsight:

| Wersja usługi HDInsight | Magazyn podstawowy | Magazyn pomocniczy | Obsługiwane |
|---|---|---|---|
| 3.6 & 4.0 | Standardowa obiektów Blob | Standardowa obiektów Blob | Yes |
| 3.6 & 4.0 | Standardowa obiektów Blob | Usługa Data Lake Storage 2. generacji | Nie |
| 3.6 & 4.0 | Standardowa obiektów Blob | Usługa Data Lake Storage 1. generacji | Yes |
| 3.6 & 4.0 | Data Lake Storage Gen2* | Usługa Data Lake Storage 2. generacji | Yes |
| 3.6 & 4.0 | Data Lake Storage Gen2* | Standardowa obiektów Blob | Yes |
| 3.6 & 4.0 | Usługa Data Lake Storage 2. generacji | Usługa Data Lake Storage 1. generacji | Nie |
| 3.6 | Usługa Data Lake Storage 1. generacji | Usługa Data Lake Storage 1. generacji | Yes |
| 3.6 | Usługa Data Lake Storage 1. generacji | Standardowa obiektów Blob | Yes |
| 3.6 | Usługa Data Lake Storage 1. generacji | Usługa Data Lake Storage 2. generacji | Nie |
| 4.0 | Usługa Data Lake Storage 1. generacji | Dowolne | Nie |

* = Może to być jeden lub wiele kont Data Lake Storage Gen2, tak długo, jak są one wszystkie Instalatora, aby uzyskać dostęp do klastra za pomocą tej samej tożsamości zarządzanej.

## <a name="use-azure-data-lake-storage-gen2-with-apache-hadoop-in-azure-hdinsight"></a>Za pomocą usług Azure Data Lake Storage Gen2 Apache Hadoop w usłudze Azure HDInsight

Usługa Azure Data Lake Storage Gen2 przyjmuje core funkcjami urządzeń z usługi Azure Data Lake Storage Gen1 i integruje usługi Azure Blob storage. Te funkcje obejmują system plików, który jest zgodny z platformą Hadoop, Azure Active Directory (Azure AD) i kontrola dostępu oparta na modelu POSIX list (kontroli dostępu ACL). To połączenie umożliwia korzystanie z zalet wydajności usługi Azure Data Lake Storage Gen1 przy równoczesnym korzystaniu Zarządzanie cyklem życia danych i obsługa warstw, Blob Storage.

Aby uzyskać więcej informacji na temat usługi Azure Data Lake Storage Gen2, zobacz [wprowadzenie do usługi Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md).

### <a name="core-functionality-of-azure-data-lake-storage-gen2"></a>Podstawowe funkcje usługi Azure Data Lake Storage Gen2

* **Dostęp, która jest zgodna z platformą Hadoop:** W usłudze Azure Data Lake magazynu Gen2 można zarządzać i uzyskiwać dostęp do danych, tak samo, jak za pomocą pliku System (HDFS, Hadoop Distributed). Sterownik usługi Azure Blob pliku System (ABFS) jest dostępny w ramach wszystkich środowisk technologii Apache Hadoop, w tym usługi Azure HDInsight i Azure Databricks. Użyj ABFS dostępu do danych przechowywanych w Data Lake Storage Gen2.

* **Nadzbiór uprawnień POSIX:** Model zabezpieczeń Data Lake Gen2 obsługuje listy ACL i POSIX uprawnienia oraz niektórych dodatkowy poziom szczegółowości specyficzne dla Data Lake Storage Gen2. Ustawienia można skonfigurować za pomocą narzędzia administracyjne lub środowisk, takich jak Apache Hive i Apache Spark.

* **Ekonomiczność:** Data Lake Storage Gen2 oferuje ekonomiczne pojemności i transakcji. Funkcje takie jak cykl życia magazynu obiektów Blob platformy Azure ułatwiają niższe koszty, dostosowując stawki rozliczeniowe, ponieważ dane są przenoszone przy użyciu jej cyklu życia.

* **Zgodność z narzędzia magazynu obiektów Blob, struktur i aplikacji:** Data Lake Storage Gen2 nadal działają z szeroką gamą narzędzi, platform i aplikacji dla usługi Blob storage.

* **Zoptymalizowane sterowników:** Sterownik ABFS jest dostosowana specjalnie do analizy danych big data. Odpowiednie interfejsy API REST są udostępniane za pośrednictwem punktu końcowego pliku rozproszonego systemu (plików DFS), dfs.core.windows.net.

### <a name="whats-new-for-azure-data-lake-storage-gen-2"></a>What's new for Azure Data Lake Storage Gen 2

#### <a name="managed-identities-for-secure-file-access"></a>Zarządzana tożsamość przy dostępie do bezpiecznych plików

Usługa Azure HDInsight korzysta z tożsamości zarządzanego zabezpieczyć klastrowi dostęp do plików w usłudze Azure Data Lake magazynu Gen2. Zarządzanych tożsamości to funkcja usługi Azure Active Directory, która zapewnia zestaw poświadczeń, automatycznie zarządzanych usług platformy Azure. Te poświadczenia można używane do uwierzytelniania na dowolne usługi obsługujące uwierzytelnianie usługi Active Directory. Przy użyciu tożsamości zarządzanej nie wymaga przechowywania poświadczeń w plikach kodu lub konfiguracji.

Aby uzyskać więcej informacji, zobacz [zarządzanych tożsamości dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/overview.md).

#### <a name="azure-blob-file-system-driver"></a>Sterownik systemu plików obiektów Blob platformy Azure

Aplikacje platformy Apache Hadoop natywnie oczekują na odczytywanie i zapisywanie danych z magazynu lokalnego dysku. Sterownik systemu plików usługi Hadoop, takich jak ABFS umożliwia aplikacjom usługi Hadoop do pracy z magazynem w chmurze poprzez emulację regularne operacje systemu plików usługi Hadoop. Sterownik konwertuje tych poleceń, odebrany od aplikacji do operacji, które rozumie platforma usług w chmurze rzeczywisty magazyn.

Poprzednio sterownik systemu plików Hadoop przekonwertować wszystkie operacje systemu plików do wywołań interfejsu API REST magazynu Azure po stronie klienta i następnie wywoływana z interfejsu API REST. Ta po stronie klienta konwersja, jednak spowodowało wiele interfejsu API REST wymaga pojedynczego pliku działania systemu, takie jak zmiana nazwy pliku. ABFS został przeniesiony część logiki systemu plików Hadoop po stronie klienta po stronie serwera. Interfejsu API usługi Azure Data Lake Storage Gen2 uruchamiane równolegle z interfejsem API obiektu Blob. Ta migracja zwiększa wydajność, ponieważ teraz można wykonywać typowe operacje systemu plików usługi Hadoop za pomocą jednego wywołania interfejsu API REST.

Aby uzyskać więcej informacji, zobacz [sterownik systemu plików obiektów Blob platformy Azure (ABFS): Dedykowany sterownika usługi Azure Storage dla platformy Hadoop](../storage/blobs/data-lake-storage-abfs-driver.md).

#### <a name="uri-scheme-for-azure-data-lake-storage-gen-2"></a>Schemat identyfikatora URI do usługi Azure Data Lake Storage Gen 2 

Azure Data Lake magazynu Gen2 używa nowy schemat identyfikatora URI dostępu do plików w usłudze Azure Storage z HDInsight:

`abfs[s]://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/<PATH>`

Schemat identyfikatora URI zapewnia dostęp z protokołem szyfrowania SSL (`abfss://` prefiks) i niezaszyfrowanego dostępu (`abfs://` prefiks). Użyj `abfss` wszędzie tam, gdzie to możliwe, nawet w przypadku uzyskiwania dostępu do danych, który znajduje się w tym samym regionie platformy Azure.

`<FILE_SYSTEM_NAME>` Określa ścieżkę systemu plików Data Lake Storage Gen2.

`<ACCOUNT_NAME>` Określa nazwę konta usługi Azure Storage. Wymagana jest w pełni kwalifikowana nazwa domeny (FQDN).

`<PATH>` jest nazwą ścieżki pliku lub katalogu HDFS.

Jeśli wartości `<FILE_SYSTEM_NAME>` i `<ACCOUNT_NAME>` nie są określone, używany jest domyślny system plików. Dla plików na domyślny system plików Użyj ścieżki względnej lub bezwzględnej. Na przykład `hadoop-mapreduce-examples.jar` pliku, który jest dostarczany z klastrami HDInsight mogą się odwoływać przy użyciu jednej z następujących ścieżek:

```
abfss://myfilesystempath@myaccount.dfs.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
abfss:///example/jars/hadoop-mapreduce-examples.jar /example/jars/hadoop-mapreduce-examples.jar
```

> [!Note]
> Nazwa pliku jest `hadoop-examples.jar` w klastrach w wersji 2.1 i 1.6 HDInsight. Podczas pracy z plikami poza HDInsight znajdziesz, że większość narzędzi nie rozpoznaje ABFS formatowanie, ale zamiast tego oczekuje podstawowego formatu ścieżki, takich jak `example/jars/hadoop-mapreduce-examples.jar`.

Aby uzyskać więcej informacji, zobacz [Użyj identyfikator URI usługi Azure Data Lake magazynu Gen2](../storage/blobs/data-lake-storage-introduction-abfs-uri.md).

## <a name="azure-storage"></a>Azure Storage

Usługa Azure Storage to rozwiązanie niezawodny magazyn ogólnego przeznaczenia, które bezproblemowo integruje się z HDInsight. Usługa HDInsight może używać kontenera obiektów blob w usłudze Azure Storage jako domyślnego systemu plików dla klastra. Korzystając interfejs systemu plików HDFS pełny zestaw składników w HDInsight może operować bezpośrednio na danych ze strukturą lub bez przechowywane jako obiekty BLOB.

Zaleca się korzystanie z kontenerów oddzielny magazyn dla domyślnego magazynu klastra i danych biznesowych do izolowania HDInsight dzienników i pliki tymczasowe z danych biznesowych. Zalecamy również usunięcie domyślnego kontenera obiektów blob, który zawiera Dzienniki aplikacji i systemu, po każdym użyciu, aby obniżyć koszty magazynowania. Koniecznie pobierz dzienniki przed usunięciem kontenera.

Jeśli chcesz zabezpieczyć swoje konto magazynu z **zapory i sieci wirtualne** ograniczenia dotyczące **wybrane sieci**, pamiętaj włączyć wyjątek **Zezwalaj na zaufane firmy Microsoft usługi...**  tak, aby HDInsight można uzyskać dostępu do konta magazynu.

### <a name="hdinsight-storage-architecture"></a>Architektura magazynu usługi HDInsight

Na poniższym diagramie przedstawiono abstrakcyjny widok architektury magazynu usługi Azure HDInsight:

![Diagram przedstawiający sposób klastry Hadoop używają interfejsu API systemu plików HDFS dostęp do przechowywania danych strukturalnych i bez struktury w magazynie obiektów Blob](./media/hdinsight-hadoop-compare-storage-options/HDI.WASB.Arch.png "architektury magazynu HDInsight")

Usługa HDInsight zapewnia dostęp do rozproszonego systemu plików, który jest lokalnie dołączony do węzłów obliczeniowych. Dostęp do tego systemu plików można uzyskać przy użyciu w pełni kwalifikowanego identyfikatora URI, na przykład:

    hdfs://<namenodehost>/<path>

Za pomocą HDInsight uzyskujesz również dostęp do danych w usłudze Azure Storage. Składnia jest następująca:

    wasb[s]://<containername>@<accountname>.blob.core.windows.net/<path>

Korzystając z konta usługi Azure Storage z klastrami HDInsight, należy wziąć pod uwagę następujące zasady:

* **Kontenery na kontach magazynu, które są podłączone do klastra:** Ponieważ nazwa konta i klucz są kojarzone z klastrem podczas tworzenia, masz pełny dostęp do obiektów blob w tych kontenerach.

* **Publiczne kontenery lub publiczne obiekty BLOB na kontach magazynu, które są *nie* podłączone do klastra:** Masz uprawnienia tylko do odczytu do obiektów blob w kontenerach.
  
  > [!NOTE]  
  > Kontenery publiczne pozwalają uzyskać listę wszystkich obiektów blob, które są dostępne w danym kontenerze oraz pobranie metadanych kontenera. Publiczne obiekty blob umożliwiają dostęp do obiektów blob jedynie osobom znającym dokładny adres URL. Aby uzyskać więcej informacji, zobacz [Zarządzanie dostępem anonimowym w trybie odczytu do kontenerów i obiektów blob](../storage/blobs/storage-manage-access-to-resources.md).

* **Prywatne kontenery na kontach magazynu, które są *nie* podłączone do klastra:** Nie masz dostępu do obiektów blob w kontenerach, chyba że zdefiniujesz konto magazynu podczas przesyłania zadań WebHCat. 

Konta magazynu definiowane w procesie tworzenia oraz ich klucze są przechowywane w pliku %HADOOP_HOME%/conf/core-site.xml w węzłach klastra. Domyślnie HDInsight używa kont magazynu zdefiniowanych w pliku core-site.xml. To ustawienie można zmodyfikować za pomocą [Apache Ambari](./hdinsight-hadoop-manage-ambari.md).

Wiele zadań WebHCat, w tym Apache Hive, MapReduce, przesyłanie strumieniowe usługi Apache Hadoop i Apache Pig, może przenosić Opis kont magazynu i metadane z nimi. (Dotyczy to obecnie Pig z kontami magazynu, ale nie dla metadanych.) Aby uzyskać więcej informacji, zobacz [przy użyciu klastra usługi HDInsight z kontami magazynu alternatywnej i magazyny metadanych](https://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx).

Obiekty blob mogą być używane z danymi ze strukturą i bez niej. Kontenery obiektów blob przechowują dane jako par klucz/wartość i mieć bez hierarchii katalogów. Jednak nazwa klucza może zawierać ukośnika (/), aby wyświetlić ją tak, jakby plik był przechowywany w ramach struktury katalogów. Na przykład klucz obiektu blob może być `input/log1.txt`. Nie rzeczywiste `input` katalog istnieje, ale z powodu znaku ukośnika w nazwie klucza, klucz wygląda jak ścieżki do pliku.

### <a id="benefits"></a>Korzyści z usługi Azure Storage
Klastry obliczeniowe i zasoby magazynu, które nie są wspólnie przechowywane mają niejawnego koszty wydajności. Te koszty są zmniejszany przez sposób tworzenia klastrów obliczeniowych w pobliżu zasobów konta magazynu w obrębie regionu świadczenia usługi Azure. W tym regionie węzłów obliczeniowych może efektywnie dostępu do danych za pośrednictwem szybkich sieci w usłudze Azure Storage.

Dane są przechowywane w usłudze Azure Storage zamiast systemu plików HDFS, pojawi się kilka korzyści:

* **Ponowne użycie danych i udostępniania:** Dane w systemie plików HDFS znajdują się wewnątrz klastra obliczeniowego. Tylko te aplikacje, które mają dostęp do klastra obliczeniowego mogą używać danych za pomocą interfejsów API systemu plików HDFS. Dane w usłudze Azure Storage, z kolei jest możliwy za pośrednictwem interfejsów API systemu plików HDFS lub interfejsów API REST usługi Blob storage. Ze względu na to rozmieszczenie większy zestaw narzędzi i aplikacji (w tym inne klastry HDInsight) może służyć do tworzenia i wykorzystywania danych.

* **Archiwizacja danych:** Gdy dane są przechowywane w usłudze Azure Storage, klastry HDInsight używane do obliczeń można bezpiecznie usunąć bez utraty danych użytkownika.

* **Koszt magazynowania danych:** Przechowywanie danych w systemie plików DFS w perspektywie długoterminowej jest droższe niż przechowywanie danych w usłudze Azure Storage, ponieważ koszt klastra obliczeniowego jest wyższy niż koszt usługi Azure Storage. Ponadto ponieważ dane nie trzeba ponownie załadować każdej generacji klastra obliczeniowego, są zapisywane również koszty ładowania danych.

* **Elastyczne skalowanie w poziomie:** Mimo że system plików HDFS zapewnia system plików skalowanych w poziomie, skala jest wyznaczana przez liczbę węzłów tworzonych dla klastra. Zmiana skali może być bardziej skomplikowane niż poleganie na z elastycznymi możliwościami skalowania, które można uzyskać automatycznie w usłudze Azure Storage.

* **Replikacja geograficzna:** Azure Storage mogą być replikowane geograficznie. Replikacja geograficzna umożliwia odzyskiwanie geograficzne i nadmiarowość danych, tryb failover do lokalizacji zreplikowanych geograficznie poważnie wpływa na wydajność i może pociągnąć za sobą dodatkowe koszty. Więc wybrać replikację geograficzną, ostrożnie i tylko wtedy, jeśli wartość danych uzasadnia dodatkowych kosztów.

Niektóre zadania i pakiety MapReduce mogą tworzyć wyniki pośrednie, które nie mają być przechowywane w usłudze Azure Storage. W takim przypadku można przechowywać dane w lokalnym systemie plików HDFS. HDInsight używa systemu plików DFS dla wielu wyników pośrednich w zadaniach Hive i innych procesów.

> [!NOTE]  
> Większość poleceń systemu plików HDFS (na przykład `ls`, `copyFromLocal`, i `mkdir`) działają zgodnie z oczekiwaniami w usłudze Azure Storage. Tylko polecenia specyficzne dla natywnych implementacji systemu plików HDFS (określanych jako systemu plików DFS), takie jak `fschk` i `dfsadmin`, Pokaż różne zachowanie w usłudze Azure Storage.

## <a name="overview-of-azure-data-lake-storage-gen1"></a>Omówienie usługi Azure Data Lake Storage Gen1

Azure Data Lake Storage Gen1 jest całego przedsiębiorstwa ogromne repozytorium do przechowywania obciążeń wynikających z analizy danych big data. Za pomocą usługi Azure Data Lake, możesz przechwytywać dane o dowolnym rozmiarze, typie i szybkości pozyskiwania w jednym miejscu na potrzeby analiz operacyjnych i poznawczych.

Dostęp Data Lake Storage Gen1 z usługi Hadoop (dostępnej z klastrem usługi HDInsight) przy użyciu interfejsów API REST zgodnych z WebHDFS. Data Lake Storage Gen1 zaprojektowano w celu umożliwienia analizy przechowywanych danych i jest ona dostrojona dla wydajności scenariusze analizy danych. Natychmiast po obejmuje funkcje, które są istotne dla przypadków użycia w przedsiębiorstwie w rzeczywistych warunkach. Te funkcje obejmują zabezpieczeń, możliwości zarządzania, skalowalności, niezawodności i dostępności.

Aby uzyskać więcej informacji na temat usługi Azure Data Lake Storage Gen1, znajduje się w sekcji [przegląd z usługi Azure Data Lake magazynu Gen1](../data-lake-store/data-lake-store-overview.md).

Oto kluczowe możliwości Data Lake Storage Gen1.

### <a name="compatibility-with-hadoop"></a>Zgodność z usługą Hadoop

Data Lake Storage Gen1 jest system plików Apache Hadoop, który jest zgodny z systemem plików HDFS i współpracuje z ekosystemem Hadoop.  Istniejące HDInsight aplikacji lub usług, które używają interfejsu API WebHDFS można łatwo zintegrować z Data Lake Storage Gen1. Data Lake Storage Gen1 udostępnia również interfejs REST zgodny z WebHDFS dla aplikacji.

Dane przechowywane w Data Lake Storage Gen1 można łatwo analizować za pomocą platform analitycznych Hadoop, takich jak MapReduce lub Hive. Klastry usługi Azure HDInsight można ustanowić i skonfigurować bezpośrednio uzyskać dostęp do danych przechowywanych w Data Lake Storage Gen1.

### <a name="unlimited-storage-petabyte-files"></a>Nieograniczony magazyn, petabajtowe pliki

Data Lake Storage Gen1 zapewnia nieograniczony magazyn i nadaje się do przechowywania różnorodnych danych na potrzeby analiz. Go nie wymusza limitów dotyczących rozmiarów kont, rozmiarów plików lub ilości danych, które mogą być przechowywane w usłudze data lake. Pojedyncze pliki mogą mieć różny rozmiar w kilobajtach do petabajtów, dzięki czemu Data Lake Storage Gen1 doskonałym wyborem do przechowywania danych dowolnego typu. Dane są przechowywane trwale dzięki tworzeniu wielu kopii i nie ma ograniczeń na jak długo dane mogą być przechowywane w usłudze data lake.

### <a name="performance-tuning-for-big-data-analytics"></a>Dostrajania dla danych big data, analiza wydajności

Data Lake Storage Gen1 został opracowany pod kątem uruchamiania dużych systemów analitycznych, wymagających ogromnej przepustowości do zadawania zapytań i analizowania dużych ilości danych. Usługa data lake się części pliku na kilku serwerach magazynu. Gdy jesteś analizowanie danych, ta konfiguracja zwiększa to przepływność odczytu podczas odczytywania pliku równolegle.

### <a name="readiness-for-enterprise-highly-available-and-secure"></a>Gotowość do przedsiębiorstwa: Wysoko dostępnych i bezpiecznych

Data Lake Storage Gen1 zapewnia będące standardami branżowymi, dostępność i niezawodność. Dane są przechowywane trwale: nadmiarowych kopii je przed nieprzewidzianymi awariami nieoczekiwany. Przedsiębiorstwa, można użyć Data Lake Storage Gen1 w swoich rozwiązaniach jako istotny element istniejącej platformy danych.

Data Lake Storage Gen1 zapewnia również zabezpieczenia korporacyjnej dla przechowywanych danych. Aby uzyskać więcej informacji, zobacz [Zabezpieczanie danych w usłudze Azure Data Lake magazynu Gen1](#DataLakeStoreSecurity).

### <a name="flexible-data-structures"></a>Struktury danych elastyczne

Data Lake Storage Gen1 może przechowywać wszystkie dane w formacie natywnym, w jakim są, bez konieczności uprzedniego przekształcania. Data Lake Storage Gen1 nie wymaga schematu należy zdefiniować przed załadowaniem danych. Poszczególne analityczne framework interpretuje dane i definiuje schemat w czasie analizy. Ponieważ można je przechowywać plików dowolnych rozmiarów i formatów, Data Lake Storage Gen1 może obsługiwać dane ze strukturą, częściową strukturą i bez struktury.

Data Lake Storage Gen1 kontenery dla danych są zasadniczo foldery i pliki. Działanie dla przechowywanych danych przy użyciu zestawów SDK, witryny Azure portal i programu Azure PowerShell. Tak długo, jak dane są umieszczane w magazynie przy użyciu tych interfejsów i odpowiednich kontenerów, można przechowywać dane dowolnego typu. Data Lake Storage Gen1 nie wykonuje żadnej specjalnej obsługi danych na podstawie typu danych, które przechowuje.

## <a name="DataLakeStoreSecurity"></a>Bezpieczeństwo danych w Data Lake Storage Gen1
Usługa Data Lake Gen1 magazynu używa usługi Azure Active Directory do uwierzytelniania i używa dostęp kontrolować list kontroli dostępu (ACL) do zarządzania dostępem do danych.

| **Funkcja** | **Opis** |
| --- | --- |
| Authentication |Data Lake Storage Gen1 integruje się z usługą Azure Active Directory (Azure AD) do zarządzania tożsamościami i dostępem dla wszystkich danych przechowywanych w Data Lake Storage Gen1. Dzięki integracji Data Lake Storage Gen1 korzyści płynące z wszystkich funkcji usługi Azure AD. Te funkcje obejmują uwierzytelnianie wieloskładnikowe, dostęp warunkowy, kontrola dostępu oparta na rolach, monitorowania użycia aplikacji, monitorowanie zabezpieczeń i alertów i tak dalej. Data Lake Storage Gen1 obsługuje protokół OAuth 2.0 na potrzeby uwierzytelniania w ramach interfejsu REST. Zobacz [uwierzytelniania w ramach usługi Azure Data Lake Storage Gen1 przy użyciu usługi Azure Active Directory](../data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md)|
| Kontrola dostępu |Data Lake Storage Gen1 zapewnia kontrolę dostępu dzięki obsłudze uprawnień POSIX, które są udostępniane przez protokół WebHDFS. Listy kontroli dostępu można włączyć dla folderu głównego, podfolderów i poszczególnych plików. Aby uzyskać więcej informacji na temat sposobu działania list ACL w kontekście Data Lake Storage Gen1, zobacz [kontrola dostępu w usługach Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md). |
| Szyfrowanie |Data Lake Storage Gen1 umożliwia także szyfrowanie danych przechowywanych na koncie. Ustawienia szyfrowania należy określić podczas tworzenia konta Data Lake Storage Gen1. Istnieje możliwość danych lub wybrać bez szyfrowania. Aby uzyskać więcej informacji, zobacz [szyfrowania w Data Lake Storage Gen1](../data-lake-store/data-lake-store-encryption.md). Aby uzyskać instrukcje na temat sposobu zapewnienia konfiguracji związanych z szyfrowaniem, zobacz [Rozpoczynanie pracy z usługą Azure Data Lake Storage Gen1 przy użyciu witryny Azure portal](../data-lake-store/data-lake-store-get-started-portal.md). |

Aby dowiedzieć się więcej na temat zabezpieczania danych w Data Lake Storage Gen1, zobacz [Zabezpieczanie danych przechowywanych w usłudze Azure Data Lake magazynu Gen1](../data-lake-store/data-lake-store-secure-data.md).

## <a name="applications-that-are-compatible-with-data-lake-storage-gen1"></a>Aplikacje, które są zgodne z Data Lake Storage Gen1
Data Lake Storage Gen1 jest zgodny z większości składników typu open source należący do ekosystemu platformy Hadoop. Bardzo dobrze integruje się również z innymi usługami Azure.  Skorzystaj z linków poniżej, aby dowiedzieć się więcej o Data Lake Storage Gen1 możliwości korzystania ze składników typu open source, a także innych usług platformy Azure.

* Zobacz [aplikacji do obsługi danych big data typu Open source, współpracujących z usługi Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-compatible-oss-other-applications.md) listę aplikacji typu open source, które jest współpraca z Data Lake Storage Gen1.
* Zobacz [integracji Azure dla Data Lake Storage Gen1 z innymi usługami Azure](../data-lake-store/data-lake-store-integrate-with-other-services.md) zrozumienie, jak użyć Data Lake Storage Gen1 z innymi usługami platformy Azure w celu włączenia szerszego zakresu scenariuszy.
* Zobacz [przy użyciu platformy Azure dla danych Lake Storage Gen1 dla wymagających danych big data](../data-lake-store/data-lake-store-data-scenarios.md) dowiesz się, jak używać Data Lake Storage Gen1 w scenariuszach, takich jak dane wprowadzane, przetwarzania danych, pobieranie danych i wizualizacja danych.

## <a name="data-lake-storage-gen1-file-system-adl"></a>System plików Gen1 magazynu jeziora danych (adl: / /)
W środowiskach Hadoop (dostępnej z klastrem usługi HDInsight), uzyskujesz dostęp Data Lake Storage Gen1 za pomocą nowego systemu plików AzureDataLakeFilesystem (adl: / /). Wydajność aplikacji i usług korzystających z systemu plików adl: / / można zoptymalizować w sposób, który nie są obecnie dostępne w systemie plików WebHDFS. W rezultacie, używając Data Lake Storage Gen1 masz swobodę albo rezultatów najlepszą wydajność przy użyciu zalecane adl: / / lub nadal obsługiwać istniejący kod przy użyciu interfejsu API WebHDFS bezpośrednio. Usługa Azure HDInsight wykorzystuje pełną system AzureDataLakeFilesystem, aby zapewnić najlepszą wydajność na Data Lake Storage Gen1.

Dostęp do danych w Data Lake Storage Gen1 przy użyciu następujących czynności:

`adl://<data_lake_storage_gen1_name>.azuredatalakestore.net`

Aby uzyskać więcej informacji na temat dostępu do danych w Data Lake Storage Gen1, zobacz [Akcje dostępne dla przechowywanych danych](../data-lake-store/data-lake-store-get-started-portal.md#properties).



## <a name="next-steps"></a>Kolejne kroki

* [Wprowadzenie do usługi Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
* [Wprowadzenie do usługi Azure Storage](../storage/common/storage-introduction.md)
