---
title: Sterownik Filesystem obiektów Blob Azure dla usługi Azure Data Lake magazynu Gen2 podglądu
description: Sterownik systemu plików Hadoop ABFS
services: storage
keywords: ''
author: jamesbak
manager: jahogg
ms.topic: article
ms.author: jamesbak
ms.date: 06/27/2018
ms.service: storage
ms.component: data-lake-storage-gen2
ms.openlocfilehash: a726779e731be2534e457ba595d93fe51c023601
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37036018"
---
# <a name="the-azure-blob-filesystem-driver-abfs-a-dedicated-azure-storage-driver-for-hadoop"></a>Sterownik Filesystem obiektów Blob Azure (ABFS): dedykowany sterownika usługi Azure Storage dla platformy Hadoop

Jedną z metod podstawowego dostępu do danych w wersji zapoznawczej usługi Azure Data Lake magazynu Gen2 odbywa się za pośrednictwem [systemu plików Hadoop](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/filesystem/index.html). Azure Data Lake magazynu Gen2 funkcji sterownikiem skojarzony sterownik systemu plików obiektów Blob Azure lub `ABFS`. ABFS jest częścią Apache Hadoop i znajduje się w wielu komercyjnych dystrybucje platformy Hadoop. Za pomocą tego sterownika, wiele aplikacji i platform można dostęp do danych w Data Lake magazynu Gen2 bez żadnego kodu jawnie odwołuje się do usługi Data Lake magazynu Gen2.

## <a name="prior-capability-the-windows-azure-storage-blob-driver"></a>Możliwość uprzedniego: sterownik obiektu Blob magazynu Azure z systemem Windows

Sterownik obiektu Blob magazynu Azure dla systemu Windows lub [sterownika WASB](https://hadoop.apache.org/docs/current/hadoop-azure/index.html) podane oryginalnego obsługę obiektach blob magazynu Azure. Ten sterownik wykonać złożone zadania mapowania systemu plików interfejs w stylu udostępnianych przez usługi Azure Blob Storage przechowywania semantykę (co jest wymagane przez interfejs systemu plików Hadoop) do tego obiektu. Ten sterownik nadal obsługuje ten model, zapewniając wysoką wydajność dostępu do danych przechowywanych w obiektach blob, ale zawiera znaczną ilość kodu wykonywanie tego mapowania, dzięki czemu trudne w utrzymaniu. Ponadto niektóre operacje, takie jak [FileSystem.rename()](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/filesystem/filesystem.html#boolean_renamePath_src_Path_d) i [FileSystem.delete()](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/filesystem/filesystem.html#boolean_deletePath_p_boolean_recursive) po zastosowaniu do katalogów wymagają sterowników do wykonania ogromną liczbę operacji (ze względu na brak magazynów obiektu wsparcia dla katalogów) która często prowadzi do pogorszenie wydajności.

W związku z tym aby wyeliminować braki projektowania związane z WASB, Nowa usługa Azure Data Lake Storage zostało wykonane ze wsparciem nowego sterownika ABFS.

## <a name="the-azure-blob-file-system-driver"></a>Sterownik systemu plików obiektów Blob Azure

[Interfejsu Azure Data Lake magazynu REST](https://docs.microsoft.com/en-us/rest/api/datalakestorage/) jest przeznaczona do obsługi semantyki systemu plików w magazynie obiektów Blob Azure. Biorąc pod uwagę, że w systemie plików usługi Hadoop jest również przystosowany do obsługi tej samej semantyki nie jest wymagane dla złożonych mapowania sterownika. W związku z tym sterownik System plików obiektów Blob Azure (lub ABFS) jest podkładki zwykłe klienta interfejsu API REST.

Istnieją jednak niektóre funkcje, które sterownik nadal należy wykonać:

### <a name="uri-scheme-to-reference-data"></a>Schemat URI do danych referencyjnych

Zgodnie z innych implementacji systemu plików w ramach platformy Hadoop, własny schemat identyfikatora URI definiuje sterownik ABFS, dzięki czemu zasobów (pliki i katalogi) mogą być kierowane osobno. Schemat identyfikatora URI jest udokumentowany w [Użyj identyfikatora URI Azure Data Lake magazynu Gen2](./introduction-abfs-uri.md). Struktura identyfikatora URI jest: `abfs[s]://file_system@account_name.dfs.core.windows.net/<path>/<path>/<file_name>`

Przy użyciu powyższego formatu identyfikatora URI, standardowych narzędzi Hadoop i platform można odwołanie do tych zasobów:

```bash
hdfs dfs -mkdir -p abfs://fileanalysis@myanalytics.dfs.core.windows.net/tutorials/flightdelays/data 
hdfs dfs -put flight_delays.csv abfs://fileanalysis@myanalytics.dfs.core.windows.net/tutorials/flightdelays/data/ 
```

Wewnętrznie sterownik ABFS tłumaczy zasoby określona w identyfikatorze URI do plików i katalogów i wykonywania wywołań do Azure Data Lake magazynu interfejsu API REST z odwołania.

### <a name="authentication"></a>Authentication

Sterownik ABFS obecnie obsługuje udostępniony klucz uwierzytelniania, dzięki czemu aplikacji Hadoop bezpieczny dostęp do zasobów zawartych w Gen2 magazynu Lake danych. Klucz jest szyfrowane i przechowywane w konfiguracji usługi Hadoop.

### <a name="configuration"></a>Konfigurowanie

Cała konfiguracja sterownika ABFS są przechowywane w <code>core-site.xml</code> pliku konfiguracji. Na dystrybucje Hadoop, oferujący funkcje [Ambari](http://ambari.apache.org/), konfiguracja może być także zarządzane przy użyciu portalu sieci web lub interfejsu Ambari API REST.

Szczegółowe informacje o wszystkich wpisów obsługiwanej konfiguracji są określone w [Hadoop oficjalna dokumentacja](http://hadoop.apache.org/docs/current/hadoop-azure/index.html).

### <a name="hadoop-documentation"></a>Dokumentacja dotycząca platformy Hadoop

Sterownik ABFS jest w pełni udokumentowane w [Hadoop oficjalna dokumentacja](http://hadoop.apache.org/docs/current/hadoop-azure/index.html)

## <a name="next-steps"></a>Kolejne kroki

- [Konfiguracja klastrów usługi HDInsight](./quickstart-create-connect-hdi-cluster.md)
- [Tworzenie klastra Databricks platformy Azure](./quickstart-create-databricks-account.md)
- [Użyj usługi Azure Data Lake Storage Gen2 identyfikatora URI](./introduction-abfs-uri.md)