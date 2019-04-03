---
title: Omówienie usługi Azure Data Lake Storage Gen1 | Dokumentacja firmy Microsoft
description: Zrozumienie, jakie Gen1 magazynu Data Lake jest (wcześniej znane jako usługi Azure Data Lake Store), a wartość jest lepsza od innych magazynów danych
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: twooley
ms.openlocfilehash: 438eab091fac103b66f0789beca0098b87ee44cd
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58885659"
---
# <a name="overview-of-azure-data-lake-storage-gen1"></a>Omówienie usługi Azure Data Lake Storage Gen1

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Azure Data Lake Storage Gen1 jest repozytorium ogromnej skali całego przedsiębiorstwa na potrzeby obciążeń analizy danych big data. Usługa Azure Data Lake umożliwia przechwytywanie danych dowolnego typu, o dowolnym rozmiarze i szybkości wprowadzania oraz przechowywanie ich w jednym miejscu na potrzeby analiz operacyjnych i poznawczych.

> [!TIP]
> Użyj [ścieżka szkoleniowa usługi Data Lake Storage Gen1](https://azure.microsoft.com/documentation/learning-paths/data-lake-store-self-guided-training/) aby rozpocząć eksplorowanie usługi Data Lake Storage Gen1.
> 
> 

Data Lake Storage Gen1 jest możliwy z usługi Hadoop (dostępnej z klastrem HDInsight) przy użyciu interfejsów API REST zgodnych z WebHDFS. Usługa została specjalnie zaprojektowana w celu umożliwienia analizy przechowywanych danych i dostosowana pod kątem wydajności dla scenariuszy analizy danych. Natychmiast po wdrożeniu zapewnia wszystkie funkcje klasy korporacyjnej — zabezpieczeń, zarządzania, skalowalności, niezawodności i dostępności — niezbędne do obsługi rzeczywistych przypadków użycia w przedsiębiorstwie.

![Azure Data Lake](./media/data-lake-store-overview/data-lake-store-concept.png)

Niektóre z kluczowych możliwości Data Lake Storage Gen1 następujące.

### <a name="built-for-hadoop"></a>Stworzona dla platformy Hadoop
Data Lake Storage Gen1 jest system plików Apache Hadoop zgodny z pliku System (HDFS, Hadoop Distributed), który działa w ekosystemie usługi Hadoop.  Istniejące HDInsight aplikacji lub usług, które używają interfejsu API WebHDFS można łatwo zintegrować z Data Lake Storage Gen1. Data Lake Storage Gen1 również uwidacznia interfejs REST zgodny z WebHDFS dla aplikacji

Dane przechowywane w Data Lake Storage Gen1 można łatwo analizować za pomocą platform analitycznych Hadoop, takich jak MapReduce lub Hive. Klastry HDInsight Azure firmy Microsoft, można ustanowić i skonfigurować bezpośrednio uzyskać dostęp do danych przechowywanych w Data Lake Storage Gen1.

### <a name="unlimited-storage-petabyte-files"></a>Nieograniczony magazyn, petabajtowe pliki
Data Lake Storage Gen1 zapewnia nieograniczony magazyn i nadaje się do przechowywania różnorodnych danych na potrzeby analiz. Nie nakłada żadnych limitów dotyczących rozmiarów kont, rozmiarów plików lub ilości danych, które mogą być przechowywane w usłudze Data Lake. Pojedyncze pliki mogą mieć rozmiar od kilobajta do petabajtów, co czyni usługę doskonałym wyborem do przechowywania danych dowolnego typu. Dane są przechowywane trwale dzięki tworzeniu wielu kopii i nie ma żadnego limitu czasu przechowywania danych w usłudze Data Lake.

### <a name="performance-tuned-for-big-data-analytics"></a>Wydajność dostosowana na potrzeby analizy danych big data
Data Lake Storage Gen1 zaprojektowano pod kątem działania dużych systemów analitycznych, wymagających ogromnej przepustowości do zadawania zapytań i analizowania dużych ilości danych. Usługa Data Lake rozmieszcza części pliku na wielu serwerach magazynu. Zwiększa to przepływność odczytu podczas odczytywania pliku równolegle w celu wykonywania analizy danych.

### <a name="enterprise-ready-highly-available-and-secure"></a>Enterprise-ready: Wysoce dostępnych i bezpiecznych
Data Lake Storage Gen1 zapewnia będące standardami branżowymi, dostępność i niezawodność. Dane są przechowywane trwale dzięki wykonywaniu nadmiarowych kopii, aby zapewnić ochronę danych na wypadek nieoczekiwanych awarii. Przedsiębiorstwa, można użyć Data Lake Storage Gen1 w swoich rozwiązaniach jako istotny element istniejącej platformy danych.

Data Lake Storage Gen1 zapewnia również zabezpieczenia korporacyjnej dla przechowywanych danych. Aby uzyskać więcej informacji, zobacz [Zabezpieczanie danych w usłudze Azure Data Lake magazynu Gen1](#DataLakeStoreSecurity).

### <a name="all-data"></a>Wszystkie dane
Data Lake Storage Gen1 może przechowywać wszystkie dane w ich natywnym formacie, w jakim są, bez wymagania jakiegokolwiek uprzedniego przekształcania. Data Lake Storage Gen1 nie wymaga schematu należy zdefiniować przed załadowaniem danych równoczesnym pozostawieniu ich do poszczególnych framework analitycznych do interpretowania danych i zdefiniowanie schematu w czasie analizy. Możliwość przechowywania plików dowolnych rozmiarów i formatów umożliwia Data Lake Storage Gen1 do obsługi danych ze strukturą, częściową strukturą i bez struktury.

Data Lake Storage Gen1 kontenery dla danych są zasadniczo foldery i pliki. Przechowywane dane można obsługiwać przy użyciu zestawów SDK, witryny Azure Portal i programu Azure Powershell. Dopóki dane są umieszczane w magazynie usługi za pomocą tych interfejsów i przy użyciu odpowiednich kontenerów, można w niej przechowywać dane dowolnego typu. Data Lake Storage Gen1 nie wykonuje żadnej specjalnej obsługi danych na podstawie typu danych, które przechowuje.

## <a name="DataLakeStoreSecurity"></a>Zabezpieczanie danych w Data Lake Storage Gen1
Usługa Data Lake Gen1 magazynu używa usługi Azure Active Directory do uwierzytelniania i dostępu do kontroli list kontroli dostępu (ACL) do zarządzania dostępem do danych.

| Cecha | Opis |
| --- | --- |
| Authentication |Data Lake Storage Gen1 integruje się z usługi Azure Active Directory (AAD) na potrzeby zarządzania tożsamościami i dostępem dla wszystkich danych przechowywanych w Data Lake Storage Gen1. Dzięki tej integracji usługa Data Lake Storage Gen1 korzyści ze wszystkich funkcji usługi AAD, w tym uwierzytelnianie wieloskładnikowe, dostęp warunkowy, kontroli dostępu opartej na rolach, monitorowania użycia aplikacji, zabezpieczenia, monitorowania i alertów, itd. Data Lake Storage Gen1 obsługuje protokół OAuth 2.0 na potrzeby uwierzytelniania przy użyciu interfejsu REST. Zobacz [Data Lake Storage Gen1 uwierzytelniania](data-lakes-store-authentication-using-azure-active-directory.md)|
| Kontrola dostępu |Data Lake Storage Gen1 zapewnia kontrolę dostępu dzięki obsłudze uprawnień POSIX udostępnianych przez protokół WebHDFS. Listy kontroli dostępu można włączyć dla folderu głównego, podfolderów i poszczególnych plików. Aby uzyskać więcej informacji na temat sposobu działania list ACL w kontekście Data Lake Storage Gen1, zobacz [kontrola dostępu w usługach Data Lake Storage Gen1](data-lake-store-access-control.md). |
| Szyfrowanie |Data Lake Storage Gen1 umożliwia także szyfrowanie danych przechowywanych na koncie. Ustawienia szyfrowania należy określić podczas tworzenia konta Data Lake Storage Gen1. Możesz wybrać szyfrowanie danych lub jego brak. Aby uzyskać więcej informacji, zobacz [szyfrowania w Data Lake Storage Gen1](data-lake-store-encryption.md). Aby uzyskać instrukcje na temat sposobu zapewnienia konfiguracji związanych z szyfrowaniem, zobacz [Rozpoczynanie pracy z usługą Azure Data Lake Storage Gen1 przy użyciu witryny Azure Portal](data-lake-store-get-started-portal.md). |

Chcesz dowiedzieć się więcej na temat zabezpieczania danych w Data Lake Storage Gen1? Skorzystaj z poniższych linków.

* Aby uzyskać instrukcje, jak zabezpieczyć dane w Data Lake Storage Gen1, zobacz [Zabezpieczanie danych w usłudze Azure Data Lake magazynu Gen1](data-lake-store-secure-data.md).
* Wolisz filmy wideo? [Obejrzyj ten film wideo](https://mix.office.com/watch/1q2mgzh9nn5lx) o tym, jak zabezpieczyć dane przechowywane w Data Lake Storage Gen1.

## <a name="applications-compatible-with-data-lake-storage-gen1"></a>Aplikacje zgodne z usługą Data Lake Storage Gen1
Data Lake Storage Gen1 jest zgodny z najbardziej otwarta składniki źródła w ekosystemie usługi Hadoop. Bardzo dobrze integruje się również z innymi usługami Azure. To sprawia, że Data Lake Storage Gen1 jest doskonałą opcją dla potrzeb dotyczących magazynu danych. Skorzystaj z linków poniżej, aby dowiedzieć się więcej o Data Lake Storage Gen1 możliwości korzystania ze składników typu open source, a także innych usług platformy Azure.

* Zobacz [aplikacje i usługi zgodne z usługą Azure Data Lake Storage Gen1](data-lake-store-compatible-oss-other-applications.md) listę współpracujący z Data Lake Storage Gen1 aplikacje typu open source.
* Zobacz [integracji z innymi usługami Azure](data-lake-store-integrate-with-other-services.md) Aby zrozumieć, jak Data Lake Storage Gen1 może służyć z innymi usługami platformy Azure do realizacji szerszego zakresu scenariuszy.
* Zobacz [scenariusze użycia Data Lake Storage Gen1](data-lake-store-data-scenarios.md) dowiesz się, jak używać Data Lake Storage Gen1 w scenariuszach, takich jak dane wprowadzane, przetwarzania danych, pobieranie danych i wizualizacja danych.

## <a name="what-is-data-lake-storage-gen1-file-system-adl"></a>Co to jest system plików Data Lake Storage Gen1 (adl: / /)?
Data Lake Storage Gen1 jest możliwy za pośrednictwem nowego systemu plików AzureDataLakeFilesystem (adl: / /), w środowiskach Hadoop (dostępnych z klastrem HDInsight). Aplikacje i usługi używające systemu plików adl:// mogą skorzystać z dalszej optymalizacji wydajności, która nie jest obecnie dostępna w systemie plików WebHDFS. W rezultacie, Data Lake Storage Gen1 zapewnia elastyczność można uzyskać najlepszą wydajność dzięki to zalecana opcja używania systemu plików adl: / / lub nadal obsługiwać istniejący kod przy użyciu interfejsu API WebHDFS bezpośrednio. Usługa Azure HDInsight w pełni wykorzystuje system AzureDataLakeFilesystem, aby zapewnić najlepszą wydajność na Data Lake Storage Gen1.

Dostępne dane przy użyciu Data Lake Storage Gen1 `adl://<data_lake_storage_gen1_name>.azuredatalakestore.net`. Aby uzyskać więcej informacji na temat dostępu do danych w Data Lake Storage Gen1, zobacz [wyświetlanie właściwości przechowywanych danych](data-lake-store-get-started-portal.md#properties)

## <a name="next-steps"></a>Kolejne kroki

* [Rozpoczynanie pracy z usługą Data Lake Storage Gen1 przy użyciu witryny Azure Portal](data-lake-store-get-started-portal.md)
* [Rozpoczynanie pracy z usługą Azure Data Lake Storage Gen1 przy użyciu zestawu .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Usługa Azure HDInsight za pomocą programu Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)