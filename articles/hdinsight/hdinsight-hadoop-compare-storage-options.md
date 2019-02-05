---
title: Porównanie opcji magazynu do użycia z klastrami usługi Azure HDInsight
description: Zawiera omówienie typów magazynów i ich współdziałania z usługą Azure HDInsight.
services: hdinsight,storage
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/04/2019
ms.openlocfilehash: 7f113587dfabd66461a9bcfbde18a0178c6608f0
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2019
ms.locfileid: "55733549"
---
# <a name="compare-storage-options-for-use-with-azure-hdinsight-clusters"></a>Porównanie opcji magazynu do użycia z klastrami usługi Azure HDInsight

Użytkownicy usługi Azure HDInsight można wybrać jedną z kilku opcji innego magazynu podczas tworzenia klastrów HDInsight:

* Usługa Azure Data Lake Storage 2. generacji
* Azure Storage
* Usługa Azure Data Lake Storage 1. generacji

Ten artykuł zawiera omówienie tych różnych typach pamięci masowej i ich unikatowych funkcji.

## <a name="use-azure-data-lake-storage-gen2-with-apache-hadoop-in-azure-hdinsight"></a>Za pomocą usług Azure Data Lake Storage Gen2 Apache Hadoop w usłudze Azure HDInsight

Aby uzyskać więcej informacji na temat usługi Azure Data Lake Storage Gen2, zobacz [wprowadzenie do usługi Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md).

Usługa Azure Data Lake Storage Gen2 przyjmuje podstawowych funkcji z usługi Azure Data Lake Storage Gen1 takich jak system plików zgodnych Hadoop, Azure Active Directory i dostępu na podstawie modelu POSIX list (kontroli dostępu ACL) kontroli i integruje usługi Azure Blob Storage. To połączenie umożliwia korzystanie z zalet wydajności usługi Azure Data Lake Storage Gen1 przy równoczesnym korzystaniu warstw magazynu obiektów Blob i zarządzanie cyklem życia danych.

### <a name="core-functionality-of-azure-data-lake-storage-gen2"></a>Podstawowe funkcje usługi Azure Data Lake Storage Gen2

* Dostęp zgodny z usługi Hadoop: Azure Data Lake magazynu Gen2 pozwala na zarządzanie i dostęp do danych, tak samo, jak za pomocą pliku System (HDFS, Hadoop Distributed). Sterownik systemu plików obiektów Blob platformy Azure (ABFS) jest dostępny w ramach wszystkich środowisk technologii Apache Hadoop, w tym usługi Azure HDInsight i Azure Databricks dostępu do danych przechowywanych w Data Lake Storage Gen2.

* Nadzbiór uprawnień POSIX: Model zabezpieczeń Data Lake Gen2 obsługuje listy ACL i POSIX uprawnienia oraz niektórych dodatkowy poziom szczegółowości specyficzne dla Data Lake Storage Gen2. Ustawienia można skonfigurować za pomocą narzędzia administracyjne lub środowisk, takich jak Apache Hive i Apache Spark.

* Ekonomiczne rozwiązanie: Data Lake Storage Gen2 oferuje ekonomiczne pojemności i transakcji. Funkcje, takie jak cykl życia magazynu obiektów Blob platformy Azure pomagają obniżyć koszty, dostosowując stawki rozliczeniowe, ponieważ dane są przenoszone przy użyciu jej cyklu projektowania.

* Działa z narzędzia magazynu obiektów Blob, struktur i aplikacji: Data Lake Storage Gen2 nadal działają z szeroką gamą narzędzi, struktur i aplikacje, które istnieją już dzisiaj dla magazynu obiektów Blob.

* Zoptymalizowane sterowników: Sterownik ABFS jest dostosowana specjalnie do analizy danych big data. Odpowiednie interfejsy API REST są udostępniane za pośrednictwem punktu końcowego systemu plików dfs, dfs.core.windows.net.

### <a name="whats-new-about-azure-data-lake-storage-gen-2"></a>Nowe funkcje usługi Azure Data Lake Storage Gen 2

#### <a name="managed-identities-for-secure-file-access"></a>Zarządzana tożsamość przy dostępie do bezpiecznych plików

Usługa Azure HDInsight korzysta z tożsamości zarządzanego zabezpieczyć klastrowi dostęp do plików w usłudze Azure Data Lake magazynu Gen2. Zarządzanych tożsamości to funkcja usługi Azure Active Directory, która zapewnia zestaw poświadczeń, automatycznie zarządzanych usług platformy Azure. Te poświadczenia mogą służyć do uwierzytelniania do dowolnej usługi, która obsługuje uwierzytelnianie w usłudze AD. Przy użyciu tożsamości zarządzanej nie wymaga przechowywania poświadczeń w plikach kodu lub konfiguracji.

Aby uzyskać więcej informacji, zobacz [co to jest zarządzanych tożsamości dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/overview.md).

#### <a name="azure-blob-filesystem-abfs-driver"></a>Sterownik systemu plików (ABFS) obiektów Blob platformy Azure

Aplikacje platformy Apache Hadoop natywnie oczekują na odczytywanie i zapisywanie danych z magazynu lokalnego dysku. Sterownik systemu plików usługi Hadoop, takich jak ABFS umożliwia aplikacjom usługi Hadoop do pracy z magazynem w chmurze poprzez emulację regularne operacje systemu plików usługi Hadoop. Sterownik konwertuje tych poleceń, odebrany od aplikacji do operacji, które rozumie platforma usług w chmurze rzeczywisty magazyn.

Poprzednio sterownik systemu plików Hadoop przekonwertować wszystkie operacje systemu plików do wywołań interfejsu API REST magazynu Azure po stronie klienta i następnie wywołania interfejsu API REST. Wywołuje to po stronie klienta konwersji, jednak spowodowało wiele interfejsu API REST dla operacji systemu plików w jednym, takie jak zmiana nazwy pliku. ABFS został przeniesiony część logiki system plików Hadoop po stronie klienta po stronie serwera i interfejsu API usługi Azure Data Lake Storage Gen2 — teraz jest uruchamiany równolegle z interfejsem API obiektu Blob. Ta migracja zwiększa wydajność, ponieważ teraz można wykonywać typowe operacje systemu plików usługi Hadoop za pomocą jednego wywołania interfejsu API REST.

Aby uzyskać więcej informacji, zobacz [sterownik systemu plików obiektów Blob platformy Azure (ABFS): Dedykowany sterownika usługi Azure Storage dla platformy Hadoop](../storage/blobs/data-lake-storage-abfs-driver.md).

#### <a name="azure-data-lake-storage-gen-2-uri-scheme"></a>Azure Data Lake magazynu Gen 2 URI schematu

Azure Data Lake magazynu Gen2 używa nowy schemat identyfikatora URI do uzyskiwania dostępu do plików w usłudze Azure storage z HDInsight:

`abfs[s]://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/<PATH>`

Schemat identyfikatora URI zapewnia dostęp do szyfrowania SSL (`abfss://` prefiks) i niezaszyfrowanego dostępu (`abfs://` prefiks). Firma Microsoft zaleca używanie `abfss` wszędzie tam, gdzie to możliwe, nawet w przypadku uzyskiwania dostępu do danych, który znajduje się w tym samym regionie platformy Azure.

`<FILE_SYSTEM_NAME>` Określa ścieżkę systemu plików Data Lake Storage Gen2.

`<ACCOUNT_NAME>` Określa nazwę konta usługi Azure Storage. Wymagana jest w pełni kwalifikowana nazwa domeny (FQDN).

`<PATH>` Jest nazwą ścieżki pliku lub katalogu HDFS.

Jeśli wartości `<FILE_SYSTEM_NAME>` i `<ACCOUNT_NAME>` nie są określone, używany jest domyślny system plików. W przypadku plików w domyślnym systemie plików można używać ścieżki względnej lub bezwzględnej. Na przykład `hadoop-mapreduce-examples.jar` pliku, który jest dostarczany z klastrami HDInsight mogą się odwoływać przy użyciu jednej z następujących ścieżek:

```
abfss://myfilesystempath@myaccount.dfs.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
abfss:///example/jars/hadoop-mapreduce-examples.jar /example/jars/hadoop-mapreduce-examples.jar
```

> [!Note]
> Nazwa pliku jest `hadoop-examples.jar` w klastrach w wersji 2.1 i 1.6 HDInsight. Podczas pracy z plikami poza HDInsight, większość narzędzi nie rozpoznają ABFS sformatowanie i zamiast tego oczekuje podstawowego formatu ścieżki, takich jak `example/jars/hadoop-mapreduce-examples.jar`.

Aby uzyskać więcej informacji, zobacz [Użyj identyfikator URI usługi Azure Data Lake magazynu Gen2](../storage/blobs/data-lake-storage-introduction-abfs-uri.md).

## <a name="use-azure-storage"></a>Korzystanie z usługi Azure Storage

Usługa Azure Storage to niezawodne rozwiązanie ogólnego przeznaczenia, które bezproblemowo integruje się z usługą HDInsight. Usługa HDInsight może używać kontenera obiektów blob w usłudze Azure Storage jako domyślnego systemu plików dla klastra. Korzystając z interfejsu rozproszonego systemu plików Hadoop (HDFS), pełny zestaw składników usługi HDInsight może operować bezpośrednio na danych ze strukturą lub bez niej przechowywanych jako obiekty blob.

> [!WARNING]  
> Podczas tworzenia konta usługi Azure Storage jest dostępnych kilka opcji. W poniższej tabeli zawarto informacje na temat opcji obsługiwanych z usługą HDInsight:

| Typ konta magazynu | Obsługiwane usługi | Warstwy wydajności obsługiwane | Warstwy dostępu obsługiwane |
|----------------------|--------------------|-----------------------------|------------------------|
| Ogólnego przeznaczenia w wersji 2   | Obiekt blob               | Standardowa (Standard)                    | Gorąca, chłodna, archiwum *    |
| Ogólnego przeznaczenia w wersji 1   | Obiekt blob               | Standardowa (Standard)                    | ND                    |
| Blob Storage         | Obiekt blob               | Standardowa (Standard)                    | Gorąca, chłodna, archiwum *    |

Nie zaleca się używanie domyślnego kontenera obiektów blob do przechowywania danych firmowych. Dobrym rozwiązaniem jest usunięcie domyślnego kontenera obiektów blob po każdym użyciu, aby obniżyć koszty magazynowania. Domyślny kontener zawiera aplikacji i systemu dzienniki. Koniecznie pobierz dzienniki przed usunięciem kontenera.

Przy użyciu jednego kontenera obiektów blob jako domyślny system plików dla wielu klastrów nie jest obsługiwane.
 
 > [!NOTE]  
 > Warstwa dostępu archiwum jest w trybie offline warstwę, która ma kilka opóźnieniem godzinę i nie jest zalecane do użycia z usługą HDInsight. Aby uzyskać więcej informacji, zobacz [warstwę dostępu archiwum](../storage/blobs/storage-blob-storage-tiers.md#archive-access-tier).

### <a name="hdinsight-storage-architecture"></a>Architektura magazynu usługi HDInsight
Na poniższym diagramie przedstawiono schemat architektury magazynu usługi HDInsight dotyczący korzystania z usługi Azure Storage:

![Klastry Hadoop używają interfejsu API systemu plików HDFS w celu dostępu do danych strukturalnych i bez struktury oraz przechowywania ich w usłudze Blob Storage.](./media/hdinsight-hadoop-compare-storage-options/HDI.WASB.Arch.png "Architektura usługi DHInsight Storage")

Usługa HDInsight zapewnia dostęp do rozproszonego systemu plików, który jest lokalnie dołączony do węzłów obliczeniowych. Dostęp do tego systemu plików można uzyskać przy użyciu w pełni kwalifikowanego identyfikatora URI, na przykład:

    hdfs://<namenodehost>/<path>

Ponadto usługa HDInsight zapewnia możliwość dostępu do danych przechowywanych w usłudze Azure Storage. Składnia jest następująca:

    wasb[s]://<containername>@<accountname>.blob.core.windows.net/<path>

Poniżej przedstawiono kilka zagadnień dotyczących korzystania z konta usługi Azure Storage w połączeniu z klastrami usługi HDInsight.

* **Kontenery na kontach magazynu, które są podłączone do klastra:** Ponieważ nazwa konta i klucz są kojarzone z klastrem podczas tworzenia, masz pełny dostęp do obiektów blob w tych kontenerach.

* **Publiczne kontenery lub publiczne obiekty BLOB na kontach magazynu, które nie są podłączone do klastra:** Masz uprawnienia tylko do odczytu do obiektów blob w kontenerach.
  
  > [!NOTE]  
  > Kontenery publiczne pozwalają na pobranie listy wszystkich obiektów blob, które są dostępne w danym kontenerze, oraz pobranie metadanych kontenera. Publiczne obiekty blob umożliwiają dostęp do obiektów blob jedynie osobom znającym dokładny adres URL. Aby uzyskać więcej informacji, zobacz [zarządzanie dostępem do kontenerów i obiektów blob](../storage/blobs/storage-manage-access-to-resources.md).

* **Prywatne kontenery na kontach magazynu, które nie są podłączone do klastra:** Nie masz dostępu do obiektów blob w kontenerach, chyba że zdefiniujesz konto magazynu podczas przesyłania zadań WebHCat. Wyjaśnienie jest zawarte w dalszej części tego artykułu.

Konta magazynu definiowane w procesie tworzenia oraz ich klucze są przechowywane w pliku %HADOOP_HOME%/conf/core-site.xml w węzłach klastra. Domyślne działanie usługi HDInsight polega na korzystaniu z kont magazynu zdefiniowanych w pliku core-site.xml. Możesz zmodyfikować to ustawienie przy użyciu [Apache Ambari](./hdinsight-hadoop-manage-ambari.md).

Wiele zadań WebHCat, w tym Apache Hive, MapReduce, przesyłanie strumieniowe usługi Apache Hadoop i Apache Pig, może przenosić Opis kont magazynu i metadane z nimi. (W przypadku technologii Pig obecnie działa to z kontami magazynu, ale nie dla metadanych). Aby uzyskać więcej informacji, zobacz [Using an HDInsight Cluster with Alternate Storage Accounts and Metastores](https://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx) (Używanie klastra usługi HDInsight z alternatywnymi kontami magazynu i magazynami metadanych).

Obiekty blob mogą być używane z danymi ze strukturą i bez niej. Kontenery obiektów blob przechowują dane jako pary klucz/wartość, bez hierarchii katalogów. Jednak wewnątrz nazwy klucza można użyć znaku ukośnika (/), co sprawi, że będzie wyglądała, jakby plik był przechowywany w ramach struktury katalogów. Na przykład klucz obiektu blob może być `input/log1.txt`. Nie rzeczywiste `input` katalog istnieje, ale z powodu obecności znaku ukośnika w nazwie klucza ma wygląd ścieżki do pliku.

### <a id="benefits"></a>Korzyści z usługi Azure Storage
Wynikowy koszt wydajności związany z brakiem łączenia klastrów obliczeniowych i zasobów magazynu jest zmniejszany przez sposób tworzenia klastrów obliczeniowych w pobliżu zasobów konta magazynu w obrębie regionu świadczenia usługi Azure, gdzie szybkie sieci zapewniają wydajny dostęp węzłów obliczeniowych do danych wewnątrz usługi Azure Storage.

Przechowywanie danych w usłudze Azure Storage zamiast w systemie plików HDFS ma wiele zalet:

* **Ponowne użycie danych i udostępniania:** Dane w systemie plików HDFS znajdują się wewnątrz klastra obliczeniowego. Tylko te aplikacje, które mają dostęp do klastra obliczeniowego mogą używać danych za pomocą interfejsów API systemu plików HDFS. Dane w usłudze Azure storage jest możliwy za pośrednictwem interfejsów API systemu plików HDFS lub za pośrednictwem interfejsów API REST usługi Blob Storage. W związku z tym większy zestaw narzędzi i aplikacji (w tym inne klastry HDInsight) może służyć do tworzenia i wykorzystywania danych.
* **Archiwizacja danych:** Przechowywanie danych w usłudze Azure storage pozwala klastry HDInsight używane do obliczeń, można bezpiecznie usunąć bez utraty danych użytkownika.
* **Koszt magazynowania danych:** Przechowywanie danych w systemie plików DFS w perspektywie długoterminowej jest droższe niż przechowywanie danych w usłudze Azure storage, ponieważ koszt klastra obliczeniowego jest wyższy niż koszt usługi Azure storage. Ponadto ponieważ dane nie trzeba ponownie załadować każdej generacji klastra obliczeniowego, są zapisywane również koszty ładowania danych.
* **Elastyczne skalowanie w poziomie:** Mimo że system plików HDFS zapewnia system plików skalowanych w poziomie, skala jest wyznaczana przez liczbę węzłów tworzonych dla klastra. Zmiana skali może stać się procesem bardziej skomplikowanym w porównaniu z elastycznymi możliwościami skalowania, które można uzyskać automatycznie w usłudze Azure Storage.
* **Replikacja geograficzna:** Usługi Azure storage można replikować geograficznie. Chociaż zapewnia to odzyskiwanie geograficzne i nadmiarowość danych, praca w trybie failover w przypadku lokalizacji zreplikowanych geograficznie poważnie wpływa na wydajność i może pociągać za sobą dodatkowe koszty. Dlatego zalecamy, aby wybierać replikację geograficzną w rozsądny sposób i tylko wtedy, gdy wartość danych uzasadnia ponoszenie dodatkowych kosztów.

Niektóre zadania i pakiety MapReduce mogą tworzyć wyniki pośrednie, których nie potrzeba przechowywać w usłudze Azure Storage. W takim przypadku można zdecydować się na przechowywanie danych w lokalnym systemie plików HDFS. W rzeczywistości HDInsight używa systemu plików DFS dla wielu wyników pośrednich w zadaniach Hive i innych procesach.

> [!NOTE]  
> Większość poleceń systemu plików HDFS (na przykład `ls`, `copyFromLocal` i `mkdir`) nadal działa zgodnie z oczekiwaniami. Tylko polecenia specyficzne dla natywnych implementacji systemu plików HDFS (określanych jako systemu plików DFS), takie jak `fschk` i `dfsadmin`, Pokaż różne zachowanie w usłudze Azure storage.

## <a name="use-azure-data-lake-storage-gen1"></a>Użyj usługi Azure Data Lake Storage Gen1

### <a name="overview"></a>Przegląd

Aby uzyskać więcej informacji na temat usługi Azure Data Lake Storage Gen1, zobacz [przegląd z usługi Azure Data Lake magazynu Gen1](../data-lake-store/data-lake-store-overview.md).

Azure Data Lake Storage Gen1 jest repozytorium ogromnej skali całego przedsiębiorstwa na potrzeby obciążeń analizy danych big data. Usługa Azure Data Lake umożliwia przechwytywanie danych dowolnego typu, o dowolnym rozmiarze i szybkości wprowadzania oraz przechowywanie ich w jednym miejscu na potrzeby analiz operacyjnych i poznawczych.

Data Lake Storage Gen1 jest możliwy z usługi Hadoop (dostępnej z klastrem usługi HDInsight) przy użyciu interfejsów API REST zgodnych z WebHDFS. Została zaprojektowana w celu umożliwienia analizy przechowywanych danych i jest ona dostrojona dla wydajności dla scenariuszy analizy danych. Natychmiast po wdrożeniu zapewnia wszystkie funkcje klasy korporacyjnej — zabezpieczeń, zarządzania, skalowalności, niezawodności i dostępności — niezbędne do obsługi rzeczywistych przypadków użycia w przedsiębiorstwie.

![Usługi Azure Data Lake Storage](./media/hdinsight-hadoop-compare-storage-options/data-lake-store-concept.png "architektury magazynu HDInsight")

Niektóre z kluczowych możliwości Data Lake Storage Gen1 następujące.

#### <a name="built-for-hadoop"></a>Stworzona dla platformy Hadoop

Data Lake Storage Gen1 jest system plików Apache Hadoop zgodny z pliku System (HDFS, Hadoop Distributed), który działa w ekosystemie usługi Hadoop.  Istniejące HDInsight aplikacji lub usług, które używają interfejsu API WebHDFS można łatwo zintegrować z Data Lake Storage Gen1. Data Lake Storage Gen1 również uwidacznia interfejs REST zgodny z WebHDFS dla aplikacji

Dane przechowywane w Data Lake Storage Gen1 można łatwo analizować za pomocą platform analitycznych Hadoop, takich jak MapReduce lub Hive. Klastry HDInsight Azure firmy Microsoft, można ustanowić i skonfigurować bezpośrednio uzyskać dostęp do danych przechowywanych w Data Lake Storage Gen1.

#### <a name="unlimited-storage-petabyte-files"></a>Nieograniczony magazyn, petabajtowe pliki

Data Lake Storage Gen1 zapewnia nieograniczony magazyn i nadaje się do przechowywania różnorodnych danych na potrzeby analiz. Nie nakłada żadnych limitów dotyczących rozmiarów kont, rozmiarów plików lub ilości danych, które mogą być przechowywane w usłudze Data Lake. Pojedyncze pliki mogą mieć rozmiar od kilobajta do petabajtów, co czyni usługę doskonałym wyborem do przechowywania danych dowolnego typu. Dane są przechowywane trwale dzięki tworzeniu wielu kopii i nie ma żadnego limitu czasu przechowywania danych w usłudze Data Lake.

#### <a name="performance-tuned-for-big-data-analytics"></a>Wydajność dostosowana na potrzeby analizy danych big data

Data Lake Storage Gen1 zaprojektowano pod kątem działania dużych systemów analitycznych, wymagających ogromnej przepustowości do zadawania zapytań i analizowania dużych ilości danych. Usługa Data Lake rozmieszcza części pliku na wielu serwerach magazynu. Zwiększa to przepływność odczytu podczas odczytywania pliku równolegle w celu wykonywania analizy danych.

#### <a name="enterprise-ready-highly-available-and-secure"></a>Enterprise-ready: Wysoko dostępnych i bezpiecznych

Data Lake Storage Gen1 zapewnia będące standardami branżowymi, dostępność i niezawodność. Dane są przechowywane trwale dzięki wykonywaniu nadmiarowych kopii, aby zapewnić ochronę danych na wypadek nieoczekiwanych awarii. Przedsiębiorstwa, można użyć Data Lake Storage Gen1 w swoich rozwiązaniach jako istotny element istniejącej platformy danych.

Data Lake Storage Gen1 zapewnia również zabezpieczenia korporacyjnej dla przechowywanych danych. Aby uzyskać więcej informacji, zobacz [Zabezpieczanie danych w usłudze Azure Data Lake magazynu Gen1](#DataLakeStoreSecurity).

#### <a name="all-data"></a>Wszystkie dane

Data Lake Storage Gen1 może przechowywać wszystkie dane w ich natywnym formacie, w jakim są, bez wymagania jakiegokolwiek uprzedniego przekształcania. Data Lake Storage Gen1 nie wymaga schematu należy zdefiniować przed załadowaniem danych równoczesnym pozostawieniu ich do poszczególnych framework analitycznych do interpretowania danych i zdefiniowanie schematu w czasie analizy. Możliwość przechowywania plików dowolnych rozmiarów i formatów umożliwia Data Lake Storage Gen1 do obsługi danych ze strukturą, częściową strukturą i bez struktury.

Data Lake Storage Gen1 kontenery dla danych są zasadniczo foldery i pliki. Używanym dla przechowywanych danych przy użyciu zestawów SDK, witryny Azure portal i programu Azure Powershell. Dopóki dane są umieszczane w magazynie usługi za pomocą tych interfejsów i przy użyciu odpowiednich kontenerów, można w niej przechowywać dane dowolnego typu. Data Lake Storage Gen1 nie wykonuje żadnej specjalnej obsługi danych na podstawie typu danych, które przechowuje.

### <a name="DataLakeStoreSecurity"></a>Zabezpieczanie danych w Data Lake Storage Gen1
Usługa Data Lake Gen1 magazynu używa usługi Azure Active Directory do uwierzytelniania i dostępu do kontroli list kontroli dostępu (ACL) do zarządzania dostępem do danych.

| Cecha | Opis |
| --- | --- |
| Authentication |Data Lake Storage Gen1 integruje się z usługi Azure Active Directory (AAD) na potrzeby zarządzania tożsamościami i dostępem dla wszystkich danych przechowywanych w Data Lake Storage Gen1. Dzięki tej integracji usługa Data Lake Storage Gen1 korzyści ze wszystkich funkcji usługi AAD, w tym uwierzytelnianie wieloskładnikowe, dostęp warunkowy, kontroli dostępu opartej na rolach, monitorowania użycia aplikacji, zabezpieczenia, monitorowania i alertów, itd. Data Lake Storage Gen1 obsługuje protokół OAuth 2.0 na potrzeby uwierzytelniania przy użyciu interfejsu REST. Zobacz [Data Lake Storage Gen1 uwierzytelniania](../data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md)|
| Kontrola dostępu |Data Lake Storage Gen1 zapewnia kontrolę dostępu dzięki obsłudze uprawnień POSIX udostępnianych przez protokół WebHDFS. Listy kontroli dostępu można włączyć dla folderu głównego, podfolderów i poszczególnych plików. Aby uzyskać więcej informacji na temat sposobu działania list ACL w kontekście Data Lake Storage Gen1, zobacz [kontrola dostępu w usługach Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md). |
| Szyfrowanie |Data Lake Storage Gen1 umożliwia także szyfrowanie danych przechowywanych na koncie. Ustawienia szyfrowania należy określić podczas tworzenia konta Data Lake Storage Gen1. Możesz wybrać szyfrowanie danych lub jego brak. Aby uzyskać więcej informacji, zobacz [szyfrowania w Data Lake Storage Gen1](../data-lake-store/data-lake-store-encryption.md). Aby uzyskać instrukcje na temat sposobu zapewnienia konfiguracji związanych z szyfrowaniem, zobacz [Rozpoczynanie pracy z usługą Azure Data Lake Storage Gen1 przy użyciu witryny Azure portal](../data-lake-store/data-lake-store-get-started-portal.md). |

Chcesz dowiedzieć się więcej na temat zabezpieczania danych w Data Lake Storage Gen1? Skorzystaj z poniższych linków.

* Aby uzyskać instrukcje, jak zabezpieczyć dane w Data Lake Storage Gen1, zobacz [Zabezpieczanie danych w usłudze Azure Data Lake magazynu Gen1](../data-lake-store/data-lake-store-secure-data.md).

### <a name="applications-compatible-with-data-lake-storage-gen1"></a>Aplikacje zgodne z usługą Data Lake Storage Gen1
Data Lake Storage Gen1 jest zgodny z większości składników typu open source należący do ekosystemu platformy Hadoop. Bardzo dobrze integruje się również z innymi usługami Azure.  Skorzystaj z linków poniżej, aby dowiedzieć się więcej o Data Lake Storage Gen1 możliwości korzystania ze składników typu open source, a także innych usług platformy Azure.

* Zobacz [aplikacje i usługi zgodne z usługą Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-compatible-oss-other-applications.md) listę współpracujący z Data Lake Storage Gen1 aplikacje typu open source.
* Zobacz [integracji z innymi usługami Azure](../data-lake-store/data-lake-store-integrate-with-other-services.md) Aby zrozumieć, jak Data Lake Storage Gen1 może służyć z innymi usługami platformy Azure do realizacji szerszego zakresu scenariuszy.
* Zobacz [scenariusze użycia Data Lake Storage Gen1](../data-lake-store/data-lake-store-data-scenarios.md) dowiesz się, jak używać Data Lake Storage Gen1 w scenariuszach, takich jak dane wprowadzane, przetwarzania danych, pobieranie danych i wizualizacja danych.

### <a name="what-is-data-lake-storage-gen1-file-system-adl"></a>Co to jest system plików Data Lake Storage Gen1 (adl: / /)?
Data Lake Storage Gen1 jest możliwy za pośrednictwem nowego systemu plików AzureDataLakeFilesystem (adl: / /), w środowiskach Hadoop (dostępnych z klastrem HDInsight). Aplikacje i usługi używające systemu plików adl:// mogą skorzystać z dalszej optymalizacji wydajności, która nie jest obecnie dostępna w systemie plików WebHDFS. W rezultacie, Data Lake Storage Gen1 zapewnia elastyczność można uzyskać najlepszą wydajność dzięki to zalecana opcja używania systemu plików adl: / / lub nadal obsługiwać istniejący kod przy użyciu interfejsu API WebHDFS bezpośrednio. Usługa Azure HDInsight w pełni wykorzystuje system AzureDataLakeFilesystem, aby zapewnić najlepszą wydajność na Data Lake Storage Gen1.

Dostępne dane przy użyciu Data Lake Storage Gen1 `adl://<data_lake_storage_gen1_name>.azuredatalakestore.net`. Aby uzyskać więcej informacji na temat dostępu do danych w Data Lake Storage Gen1, zobacz [wyświetlanie właściwości przechowywanych danych](../data-lake-store/data-lake-store-get-started-portal.md#properties)



## <a name="next-steps"></a>Kolejne kroki

* [Wprowadzenie do usługi Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md).
* [Wprowadzenie do usługi Azure Storage](../storage/common/storage-introduction.md)