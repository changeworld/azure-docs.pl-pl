---
title: Co to jest Azure Data Lake Storage Gen1? | Microsoft Docs
description: Omówienie Data Lake Storage Gen1, (wcześniej znane jako usługi Azure Data Lake Store) i wartość jest lepsza od innych magazynów danych
services: data-lake-store
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: twooley
ms.openlocfilehash: 99384374226fd89cfd672c6b4f851a1743db0764
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67118801"
---
# <a name="what-is-azure-data-lake-storage-gen1"></a>Co to jest Azure Data Lake Storage Gen1?

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Azure Data Lake Storage Gen1 jest repozytorium ogromnej skali całego przedsiębiorstwa na potrzeby obciążeń analizy danych big data. Usługa Azure Data Lake umożliwia przechwytywanie danych dowolnego typu, o dowolnym rozmiarze i szybkości wprowadzania oraz przechowywanie ich w jednym miejscu na potrzeby analiz operacyjnych i poznawczych.

Data Lake Storage Gen1 jest możliwy z usługi Hadoop (dostępnej z klastrem HDInsight) przy użyciu interfejsów API REST zgodnych z WebHDFS. On zostało zaprojektowane w celu umożliwienia analizy przechowywanych danych i jest ona dostrojona dla wydajności dla scenariuszy analizy danych. Data Lake Storage Gen1 zapewnia wszystkie funkcje klasy korporacyjnej: zabezpieczeń, możliwości zarządzania, skalowalności, niezawodności i dostępności.

![Azure Data Lake](./media/data-lake-store-overview/data-lake-store-concept.png)

## <a name="key-capabilities"></a>Najważniejsze możliwości

Niektóre z kluczowych możliwości Data Lake Storage Gen1 następujące.

### <a name="built-for-hadoop"></a>Stworzona dla platformy Hadoop

Data Lake Storage Gen1 jest system plików Apache Hadoop, jest zgodna z pliku System (HDFS, Hadoop Distributed), która działa w ekosystemie usługi Hadoop. Istniejące HDInsight aplikacji lub usług, które używają interfejsu API WebHDFS można łatwo zintegrować z Data Lake Storage Gen1. Data Lake Storage Gen1 udostępnia również interfejs REST zgodny z WebHDFS dla aplikacji.

Można łatwo analizować dane przechowywane w Data Lake Storage Gen1 za pomocą platform analitycznych Hadoop, takich jak MapReduce lub Hive. Można inicjować obsługę klastrów usługi Azure HDInsight i ich konfigurowania pod kątem uzyskania bezpośredniego dostępu do danych przechowywanych w Data Lake Storage Gen1.

### <a name="unlimited-storage-petabyte-files"></a>Nieograniczony magazyn, petabajtowe pliki

Data Lake Storage Gen1 zapewnia nieograniczony magazyn i mogą przechowywać różne danych na potrzeby analiz. Go nie nakłada żadnych limitów dotyczących rozmiarów kont, rozmiarów plików lub ilości danych, które mogą być przechowywane w usłudze data lake. Pojedyncze pliki należą do zakresu od kilobajta do petabajtów rozmiar. Dane są przechowywane trwale dzięki tworzeniu wielu kopii. Nie ma żadnego limitu na czas, dla którego dane mogą być przechowywane w usłudze data lake.

### <a name="performance-tuned-for-big-data-analytics"></a>Wydajność dostosowana na potrzeby analizy danych big data

Data Lake Storage Gen1 zaprojektowano pod kątem uruchamiania dużych systemów analitycznych, wymagających ogromnej przepustowości do zadawania zapytań i analizowania dużych ilości danych. Usługa Data Lake rozmieszcza części pliku na wielu serwerach magazynu. Zwiększa to przepływność odczytu podczas odczytywania pliku równolegle w celu wykonywania analizy danych.

### <a name="enterprise-ready-highly-available-and-secure"></a>Z myślą o przedsiębiorstwach: Wysoko dostępnych i bezpiecznych

Data Lake Storage Gen1 zapewnia będące standardami branżowymi, dostępność i niezawodność. Dane są przechowywane trwale dzięki wykonywaniu nadmiarowych kopii, aby zapewnić ochronę danych na wypadek nieoczekiwanych awarii.

Data Lake Storage Gen1 zapewnia również zabezpieczenia korporacyjnej dla przechowywanych danych. Aby uzyskać więcej informacji, zobacz [Zabezpieczanie danych w usłudze Azure Data Lake magazynu Gen1](#DataLakeStoreSecurity).

### <a name="all-data"></a>Wszystkie dane

Data Lake Storage Gen1 może przechowywać wszystkie dane w formacie natywnym, bez wymagania jakiegokolwiek uprzedniego przekształcania. Data Lake Storage Gen1 nie wymaga schematu należy zdefiniować przed załadowaniem danych równoczesnym pozostawieniu ich do poszczególnych framework analitycznych do interpretowania danych i zdefiniowanie schematu w czasie analizy. Możliwość przechowywania plików dowolnych rozmiarów i formatów sprawiają, że Data Lake Storage Gen1 do obsługi danych ze strukturą, częściową strukturą i bez struktury.

Data Lake Storage Gen1 kontenery dla danych są zasadniczo foldery i pliki. Używanym dla przechowywanych danych przy użyciu zestawów SDK, witryny Azure portal i programu Azure Powershell. Jeśli dane są umieszczane w magazynie, za pomocą tych interfejsów i przy użyciu odpowiednich kontenerów, można przechowywać dane dowolnego typu. Data Lake Storage Gen1 nie wykonuje żadnej specjalnej obsługi danych na podstawie typu danych, które przechowuje.

## <a name="DataLakeStoreSecurity"></a>Zabezpieczanie danych

Usługa Data Lake Gen1 magazynu używa usługi Azure Active Directory (Azure AD) do uwierzytelniania i dostępu do kontroli list kontroli dostępu (ACL) do zarządzania dostępem do danych.

| Cecha | Opis |
| --- | --- |
| Authentication |Data Lake Storage Gen1 integruje się z usługą Azure AD do zarządzania tożsamościami i dostępem dla wszystkich danych przechowywanych w Data Lake Storage Gen1. Dzięki integracji Data Lake Storage Gen1 korzyści z wszystkie usługi Azure AD są wyposażone w takie jak uwierzytelnianie wieloskładnikowe, dostęp warunkowy, kontrola dostępu oparta na rolach, monitorowania użycia aplikacji, monitorowanie zabezpieczeń i alertów i tak dalej. Data Lake Storage Gen1 obsługuje protokół OAuth 2.0 na potrzeby uwierzytelniania w ramach interfejsu REST. Zobacz [uwierzytelniania Data Lake Storage Gen1](data-lakes-store-authentication-using-azure-active-directory.md).|
| Kontrola dostępu |Data Lake Storage Gen1 zapewnia kontrolę dostępu dzięki obsłudze uprawnień POSIX udostępnianych przez protokół WebHDFS. Można włączyć listy ACL dla folderu głównego, podfolderów i poszczególnych plików. Aby uzyskać więcej informacji na temat sposobu działania list kontroli dostępu w kontekście Data Lake Storage Gen1 zobacz [kontrola dostępu w usługach Data Lake Storage Gen1](data-lake-store-access-control.md). |
| Szyfrowanie |Data Lake Storage Gen1 umożliwia także szyfrowanie danych przechowywanych na koncie. Ustawienia szyfrowania należy określić podczas tworzenia konta Data Lake Storage Gen1. Istnieje możliwość danych lub wybrać bez szyfrowania. Aby uzyskać więcej informacji, zobacz [szyfrowania w Data Lake Storage Gen1](data-lake-store-encryption.md). Aby uzyskać instrukcje na temat sposobu zapewnienia konfiguracji związanych z szyfrowaniem, zobacz [Rozpoczynanie pracy z usługą Data Lake Storage Gen1 przy użyciu witryny Azure portal](data-lake-store-get-started-portal.md). |

Aby uzyskać instrukcje, jak zabezpieczyć dane w Data Lake Storage Gen1, zobacz [Zabezpieczanie danych w usłudze Azure Data Lake magazynu Gen1](data-lake-store-secure-data.md).

## <a name="application-compatibility"></a>Zgodność aplikacji

Data Lake Storage Gen1 jest zgodny z większości składników typu open source należący do ekosystemu platformy Hadoop. Integruje się także współpracuje z innymi usługami platformy Azure. Aby dowiedzieć się więcej na temat wykorzystania programu Data Lake Storage Gen1 dzięki składnikom typu open source i innych usług platformy Azure, użyj następujących linków:

- Zobacz [aplikacje i usługi zgodne z usługą Azure Data Lake Storage Gen1](data-lake-store-compatible-oss-other-applications.md) listę współpracujący z Data Lake Storage Gen1 aplikacje typu open source.
- Zobacz [integracji z innymi usługami Azure](data-lake-store-integrate-with-other-services.md) zrozumienie, jak użyć Data Lake Storage Gen1 z innymi usługami platformy Azure w celu włączenia szerszego zakresu scenariuszy.
- Zobacz [scenariusze użycia Data Lake Storage Gen1](data-lake-store-data-scenarios.md) dowiesz się, jak używać Data Lake Storage Gen1 w scenariuszach, takich jak dane wprowadzane, przetwarzania danych, pobieranie danych i wizualizacja danych.

## <a name="data-lake-storage-gen1-file-system"></a>System plików Gen1 magazynu jeziora danych

Data Lake Storage Gen1 jest możliwy za pośrednictwem systemu plików AzureDataLakeFilesystem (adl: / /) w środowiskach Hadoop (dostępnych z klastrem HDInsight). Aplikacje i usługi używające systemu plików adl: / / skorzystać z dalszej optymalizacji wydajności, które nie są obecnie dostępne w systemie plików WebHDFS. Co w efekcie Data Lake Storage Gen1 zapewnia elastyczność albo wprowadzone za pomocą o najlepszej wydajności zalecane możliwość korzystania z systemu plików adl: / / lub nadal obsługiwać istniejący kod przy użyciu interfejsu API WebHDFS bezpośrednio. Usługa Azure HDInsight w pełni wykorzystuje system AzureDataLakeFilesystem, aby zapewnić najlepszą wydajność na Data Lake Storage Gen1.

Dostępne dane przy użyciu Data Lake Storage Gen1 `adl://<data_lake_storage_gen1_name>.azuredatalakestore.net`. Aby uzyskać więcej informacji o tym, jak uzyskać dostęp do danych Data Lake Storage Gen1, zobacz [wyświetlanie właściwości przechowywanych danych](data-lake-store-get-started-portal.md#properties).

## <a name="next-steps"></a>Kolejne kroki

- [Rozpoczynanie pracy z usługą Data Lake Storage Gen1 przy użyciu witryny Azure portal](data-lake-store-get-started-portal.md)
- [Rozpoczynanie pracy z usługą Data Lake Storage Gen1 przy użyciu zestawu .NET SDK](data-lake-store-get-started-net-sdk.md)
- [Usługa Azure HDInsight za pomocą programu Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)