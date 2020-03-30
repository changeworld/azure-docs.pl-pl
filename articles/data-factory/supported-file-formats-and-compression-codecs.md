---
title: Obsługiwane formaty plików w usłudze Azure Data Factory
description: W tym temacie opisano formaty plików i kody kompresji, które są obsługiwane przez łączniki oparte na plikach w usłudze Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: jingwang
ms.openlocfilehash: 6855eea5939419c9a0a867de4e0621b4d4ae02b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75439571"
---
# <a name="supported-file-formats-and-compression-codecs-in-azure-data-factory"></a>Obsługiwane formaty plików i kodeki kompresji w usłudze Azure Data Factory

*Ten artykuł dotyczy następujących łączników: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), System [plików](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md)i [SFTP](connector-sftp.md).*

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Za pomocą [działania Kopiuj](copy-activity-overview.md) można kopiować pliki w stanie takim, w jakim znajdują się dwa magazyny danych opartych na plikach, w którym to przypadku dane są kopiowane wydajnie bez serializacji lub deserializacji. 

Ponadto można również analizować lub generować pliki o danym formacie. Na przykład można wykonać następujące czynności:

* Kopiowanie danych z lokalnej bazy danych programu SQL Server i zapisywanie w usłudze Azure Data Lake Storage Gen2 w formacie Parquet.
* Kopiowanie plików w formacie CSV (CSV) z lokalnego systemu plików i zapisywanie w magazynie obiektów Blob platformy Azure w formacie Avro.
* Skopiuj spakowane pliki z lokalnego systemu plików, zdekompresuj je w locie i zapisuj wyodrębnione pliki w usłudze Azure Data Lake Storage Gen2.
* Skopiuj dane w formacie CSV (Gzip skompresowanego tekstu) z magazynu obiektów blob platformy Azure i zapisz je w bazie danych SQL Azure.
* Wiele innych działań, które wymagają serializacji/deserializacji lub kompresji/dekompresji.

## <a name="next-steps"></a>Następne kroki

Zobacz inne artykuły dotyczące działania kopiowania:

- [Omówienie działania kopiowania](copy-activity-overview.md)
- [Kopiowanie wydajności działania](copy-activity-performance.md)
