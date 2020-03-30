---
title: Korzystanie z identyfikatora URI usługi Azure Data Lake Storage Gen2
description: Korzystanie z identyfikatora URI usługi Azure Data Lake Storage Gen2
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.date: 12/06/2018
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.reviewer: jamesbak
ms.openlocfilehash: 04df30c2a97e865d23999df26768b38cb38be607
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68855561"
---
# <a name="use-the-azure-data-lake-storage-gen2-uri"></a>Korzystanie z identyfikatora URI usługi Azure Data Lake Storage Gen2

Sterownik [systemu plików Hadoop,](https://www.aosabook.org/en/hdfs.html) który jest zgodny z usługą Azure `abfs` Data Lake Storage Gen2 jest znany przez jego identyfikator schematu (Azure Blob File System). Zgodnie z innymi sterownikami Systemu Plików Hadoop, sterownik ABFS wykorzystuje format URI do adresowania plików i katalogów w ramach konta obsługującego usługę Data Lake Storage Gen2.

## <a name="uri-syntax"></a>Składnia identyfikatora URI

Składnia identyfikatora URI dla usługi Data Lake Storage Gen2 zależy od tego, czy twoje konto magazynu jest skonfigurowane tak, aby usługa Data Lake Storage Gen2 była domyślnym systemem plików.

Jeśli konto obsługujące usługę Data Lake Storage Gen2, które chcesz rozwiązać, **nie jest** ustawione jako domyślny system plików podczas tworzenia konta, wówczas skrócona składnia identyfikatora URI jest następująca:

<pre>abfs[s]<sup>1</sup>://&lt;file_system&gt;<sup>2</sup>@&lt;account_name&gt;<sup>3</sup>.dfs.core.windows.net/&lt;path&gt;<sup>4</sup>/&lt;file_name&gt;<sup>5</sup></pre>

1. **Identyfikator schematu** `abfs` : Protokół jest używany jako identyfikator schematu. Można połączyć się z połączeniem ssl (secure socket layer) lub bez niego. Służy `abfss` do łączenia się z bezpiecznym połączeniem warstwy gniazda.

2. **System plików**: Lokalizacja nadrzędna, w którym znajdują się pliki i foldery. Jest to taka sama jak kontenery w usłudze Azure Storage Obiektów Blobs.

3. **Nazwa konta**: nazwa nadana kontu magazynu podczas tworzenia.

4. **Ścieżki: Przecięcie**do`/`przodu rozdzielone ( ) reprezentacja struktury katalogów.

5. **Nazwa pliku**: Nazwa pojedynczego pliku. Ten parametr jest opcjonalny, jeśli adresujesz katalog.

Jeśli jednak konto, które chcesz zaajmi adres, jest ustawione jako domyślny system plików podczas tworzenia konta, wówczas skrócona składnia identyfikatora URI jest następująca:

<pre>/&lt;path&gt;<sup>1</sup>/&lt;file_name&gt;<sup>2</sup></pre>

1. **Ścieżka:** Przecięcie do`/`przodu rozdzielone ( ) reprezentacja struktury katalogów.

2. **Nazwa pliku**: Nazwa pojedynczego pliku.


## <a name="next-steps"></a>Następne kroki

- [Korzystanie z usługi Azure Data Lake Storage Gen2 z klastrami usługi Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
