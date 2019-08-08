---
title: Użyj identyfikatora URI Azure Data Lake Storage Gen2
description: Użyj identyfikatora URI Azure Data Lake Storage Gen2
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.date: 12/06/2018
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.reviewer: jamesbak
ms.openlocfilehash: 04df30c2a97e865d23999df26768b38cb38be607
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2019
ms.locfileid: "68855561"
---
# <a name="use-the-azure-data-lake-storage-gen2-uri"></a>Użyj identyfikatora URI Azure Data Lake Storage Gen2

Sterownik systemu [plików Hadoop](https://www.aosabook.org/en/hdfs.html) zgodny z Azure Data Lake Storage Gen2 jest znany przez jego identyfikator `abfs` schematu (system plików obiektów blob platformy Azure). W przypadku innych sterowników systemu plików Hadoop sterownik ABFS korzysta z formatu identyfikatora URI do adresowania plików i katalogów w ramach konta z możliwością Data Lake Storage Gen2.

## <a name="uri-syntax"></a>Składnia identyfikatora URI

Składnia identyfikatora URI dla Data Lake Storage Gen2 jest zależna od tego, czy konto magazynu zostało skonfigurowane w taki sposób, że Data Lake Storage Gen2 jako domyślny system plików.

Jeśli konto obsługujące Data Lake Storage Gen2, które ma zostać rozadresowane, **nie jest** ustawione jako domyślny system plików podczas tworzenia konta, składnia identyfikatora URI w postaci skróconej jest następująca:

<pre>abfs[s]<sup>1</sup>://&lt;file_system&gt;<sup>2</sup>@&lt;account_name&gt;<sup>3</sup>.dfs.core.windows.net/&lt;path&gt;<sup>4</sup>/&lt;file_name&gt;<sup>5</sup></pre>

1. **Identyfikator schematu**: `abfs` Protokół jest używany jako identyfikator schematu. Istnieje możliwość nawiązywania połączenia z usługą Secure Socket Layer (SSL) lub bez niej. Służy `abfss` do nawiązywania połączenia z bezpiecznym połączeniem warstwy gniazda.

2. **System plików**: Lokalizacja nadrzędna, w której znajdują się pliki i foldery. Są one takie same jak kontenery w usłudze Azure Storage BLOB Service.

3. **Nazwa konta**: Nazwa nadana kontu magazynu podczas tworzenia.

4. **Ścieżki**: Przedstawiona kreska ukośna (`/`) reprezentacja struktury katalogów.

5. **Nazwa pliku**: Nazwa pojedynczego pliku. Ten parametr jest opcjonalny w przypadku adresowania katalogu.

Jeśli jednak konto, które ma zostać rozadresowane, jest ustawione jako domyślny system plików podczas tworzenia konta, składnia identyfikatora URI w postaci skróconej jest następująca:

<pre>/&lt;path&gt;<sup>1</sup>/&lt;file_name&gt;<sup>2</sup></pre>

1. **Ścieżka**: Przedstawiona kreska ukośna (`/`) reprezentacja struktury katalogów.

2. **Nazwa pliku**: Nazwa pojedynczego pliku.


## <a name="next-steps"></a>Kolejne kroki

- [Korzystanie z usługi Azure Data Lake Storage Gen2 w połączeniu z klastrami usługi Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
