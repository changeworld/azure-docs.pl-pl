---
title: Użyj usługi Azure Data Lake Storage Gen2 Podgląd identyfikatora URI
description: Użyj usługi Azure Data Lake Storage Gen2 Podgląd identyfikatora URI
services: storage
author: jamesbak
ms.topic: conceptual
ms.author: jamesbak
ms.date: 12/06/2018
ms.service: storage
ms.component: data-lake-storage-gen2
ms.openlocfilehash: e596123cb218a542166d80b53916a73034f71760
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2018
ms.locfileid: "52975256"
---
# <a name="use-the-azure-data-lake-storage-gen2-uri"></a>Użyj Gen2 usługi Azure Data Lake Storage — identyfikator URI

[System plików Hadoop](http://www.aosabook.org/en/hdfs.html) sterownika, który jest zgodny z usługi Azure Data Lake Gen2 — wersja zapoznawcza jest znany identyfikator jego schematu `abfs` (systemu plików obiektów Blob platformy Azure). Zgodnie z innych sterowników systemu plików usługi Hadoop, sterownik ABFS stosuje format identyfikatora URI, adresowanie plików i katalogów w ramach konta Data Lake Storage Gen2 stanie.

## <a name="uri-syntax"></a>Składnia identyfikatora URI

Składnia identyfikatora URI Data Lake Storage Gen2 jest zależy od tego, czy konto magazynu jest skonfigurowany do mają Data Lake Storage Gen2 jako domyślny system plików.

Jeśli konto stanie Data Lake Storage Gen2 chcesz adres **nie** Ustaw jako domyślny system plików podczas tworzenia konta, a następnie jest skróconej składni identyfikatora URI:

<pre>abfs[s]<sup>1</sup>://&lt;file_system&gt;<sup>2</sup>@&lt;account_name&gt;<sup>3</sup>.dfs.core.windows.net/&lt;path&gt;<sup>4</sup>/&lt;file_name&gt;<sup>5</sup></pre>

1. **Identyfikator schematu**: `abfs` protokół jest używany jako identyfikator schematu. Masz możliwość nawiązania połączenia z lub bez połączenia z secure sockets layer (SSL). Użyj `abfss` nawiązywanie połączeń z secure sockets layer połączenia.

2. **System plików**: lokalizacji nadrzędnej, który zawiera pliki i foldery. To jest taka sama, jak kontenery w usłudze Azure Storage blob.

3. **Nazwa konta**: nazwa nadana podczas tworzenia konta magazynu.

4. **Ścieżki**: rozdzielonych ukośnikiem (`/`) reprezentujący strukturę katalogów.

5. **Nazwa pliku**: Nazwa poszczególnych plików. Ten parametr jest opcjonalny, jeśli są adresowania katalogu.

Jednak jeśli konto, którego chcesz adres jest ustawiony jako domyślny system plików podczas tworzenia konta, następnie skróconej składni identyfikatora URI jest:

<pre>/&lt;path&gt;<sup>1</sup>/&lt;file_name&gt;<sup>2</sup></pre>

1. **Ścieżka**: rozdzielonych ukośnikiem (`/`) reprezentujący strukturę katalogów.

2. **Nazwa pliku**: Nazwa poszczególnych plików.


## <a name="next-steps"></a>Kolejne kroki

- [Za pomocą usług Azure Data Lake Storage Gen2 klastrów Azure HDInsight](data-lake-storage-use-hdi-cluster.md)
