---
title: Sterownik systemu plików obiektów Blob platformy Azure dla usługi Azure Data Lake Gen2 — wersja zapoznawcza
description: Sterownik systemu plików Hadoop ABFS
services: storage
keywords: ''
author: jamesbak
ms.topic: article
ms.author: jamesbak
ms.date: 06/27/2018
ms.service: storage
ms.component: data-lake-storage-gen2
ms.openlocfilehash: f618b925839d6f501635748734327293a2073b64
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2018
ms.locfileid: "49384859"
---
# <a name="the-azure-blob-filesystem-driver-abfs-a-dedicated-azure-storage-driver-for-hadoop"></a>Sterownik systemu plików obiektów Blob platformy Azure (ABFS): dedykowanych sterownika usługi Azure Storage dla platformy Hadoop

Jedną z metod dostępu do danych w usłudze Azure Data Lake Gen2 — wersja zapoznawcza jest za pośrednictwem [system plików Hadoop](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/filesystem/index.html). Azure Data Lake magazynu Gen2 funkcji skojarzone sterownika, sterownik systemu plików obiektów Blob platformy Azure lub `ABFS`. ABFS jest częścią technologii Apache Hadoop i znajduje się w wielu komercyjnych dystrybucje usługi Hadoop. Korzystając z tego sterownika, wiele aplikacji i struktury mogą dostęp do danych w Data Lake Storage Gen2 bez konieczności wprowadzania kodu jawnie odwołuje się do usługi Data Lake Storage Gen2.

## <a name="prior-capability-the-windows-azure-storage-blob-driver"></a>Możliwość uprzedniego: sterownik Windows Azure Storage Blob

Sterownik systemu Windows Azure Storage Blob lub [sterownika WASB](https://hadoop.apache.org/docs/current/hadoop-azure/index.html) podane oryginalnego wsparcie dla obiektów blob usługi Azure Storage. Ten sterownik wykonać złożone zadania system plików mapowania semantyki (zgodnie z potrzebami przy użyciu interfejsu systemu plików usługi Hadoop) do tego obiektu przechowywania interfejs w stylu udostępnianych przez usługi Azure Blob Storage. Ten sterownik w dalszym ciągu obsługuje ten model, zapewniając wysoką wydajność dostępu do danych przechowywanych w obiektach blob, ale zawiera znaczną ilość kodu, wykonywanie tego mapowania, dzięki czemu trudne w utrzymaniu. Ponadto niektóre operacje, takie jak [FileSystem.rename()](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/filesystem/filesystem.html#boolean_renamePath_src_Path_d) i [FileSystem.delete()](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/filesystem/filesystem.html#boolean_deletePath_p_boolean_recursive) po zastosowaniu do katalogów wymagają sterowników do wykonania ogromną liczbę operacji (z powodu braku magazyny obiektów obsługę katalogów) co często prowadzi do pogorszenie wydajności. Nowa usługa Azure Data Lake Storage został zaprojektowany w celu pokonania nieprzerwaną pracę braki WASB.

## <a name="the-azure-blob-file-system-driver"></a>Sterownik systemu plików obiektów Blob platformy Azure

[Interfejsu usługi Azure Data Lake Storage REST](https://docs.microsoft.com/rest/api/storageservices/data-lake-storage-gen2) jest przeznaczona do obsługi semantyki systemu plików za pośrednictwem usługi Azure Blob Storage. Biorąc pod uwagę, że system plików usługi Hadoop jest też przeznaczona do obsługi tej samej semantyki nie jest wymagane dla złożonych mapowania w sterowniku. W związku z tym sterownik systemu plików obiektów Blob platformy Azure (lub ABFS) jest typu shim sam klient interfejsu API REST.

Istnieją jednak pewne funkcje, które sterownik musi nadal wykonać:

### <a name="uri-scheme-to-reference-data"></a>Schemat identyfikatora URI z danymi referencyjnymi

Zgodnie z innych implementacji systemu plików w ramach platformy Hadoop, własny schemat identyfikatora URI definiuje sterownik ABFS, dzięki czemu zasoby (pliki i katalogi) może zająć wyraźnie. Schemat identyfikatora URI jest udokumentowany w [Użyj identyfikator URI usługi Azure Data Lake magazynu Gen2](./introduction-abfs-uri.md). Struktura identyfikatora URI jest: `abfs[s]://file_system@account_name.dfs.core.windows.net/<path>/<path>/<file_name>`

Korzystając z powyższych format identyfikatora URI, standardowych narzędzi usługi Hadoop i platformy można odwoływać się do tych zasobów:

```bash
hdfs dfs -mkdir -p abfs://fileanalysis@myanalytics.dfs.core.windows.net/tutorials/flightdelays/data 
hdfs dfs -put flight_delays.csv abfs://fileanalysis@myanalytics.dfs.core.windows.net/tutorials/flightdelays/data/ 
```

Wewnętrznie sterownik ABFS tłumaczy zasoby określona w identyfikatorze URI do plików i katalogów i wykonywania wywołań do usługi Azure Data Lake magazynu interfejsu API REST przy użyciu tych odwołań.

### <a name="authentication"></a>Authentication

Sterownik ABFS obsługuje dwie formy uwierzytelniania, dzięki czemu aplikacji platformy Hadoop bezpieczny dostęp do zasobów znajdujących się w ramach konta Data Lake Storage Gen2 stanie. Pełne informacje na temat schematów uwierzytelniania dostępne znajdują się w [Przewodnik po zabezpieczeniach usługi Azure Storage](../common/storage-security-guide.md). Oto one:

- **Klucz współużytkowany:** to pozwala użytkownikom dostęp do wszystkich zasobów w ramach konta. Klucz jest zaszyfrowany i przechowywane w konfiguracji usługi Hadoop.

- **Usługa Azure Active Directory elementu nośnego OAuth Token:** tokenów elementu nośnego usługi Azure AD są uzyskane i odświeżane przez sterownik przy użyciu tożsamości użytkownika albo skonfigurowanej nazwy głównej usługi. Przy użyciu tego modelu uwierzytelniania, wszelki dostęp jest autoryzowany dla każdego wywołania przy użyciu tożsamości skojarzone z tokenem dostarczony i oceniać je względem przypisanych POSIX kontroli dostępu listy (ACL).

### <a name="configuration"></a>Konfigurowanie

Cała konfiguracja sterownika ABFS są przechowywane w <code>core-site.xml</code> pliku konfiguracji. W dystrybucjach Hadoop, oferujący funkcje [Ambari](http://ambari.apache.org/), konfiguracja może być także zarządzane przy użyciu portalu sieci web lub interfejsu API REST Ambari.

Szczegółowe informacje o wszystkich wpisów obsługiwanej konfiguracji są określone w [dokumentacji oficjalnego Hadoop](http://hadoop.apache.org/docs/current/hadoop-azure/index.html).

### <a name="hadoop-documentation"></a>Dokumentacja usługi Hadoop

Sterownik ABFS jest w pełni udokumentowane w [dokumentacji oficjalnego usługi Hadoop](http://hadoop.apache.org/docs/current/hadoop-azure/index.html)

## <a name="next-steps"></a>Kolejne kroki

- [Konfigurowanie klastrów HDInsight](./quickstart-create-connect-hdi-cluster.md)
- [Tworzenie klastra usługi Azure Databricks](./quickstart-create-databricks-account.md)
- [Użyj Gen2 usługi Azure Data Lake Storage — identyfikator URI](./introduction-abfs-uri.md)
