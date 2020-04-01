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
ms.openlocfilehash: fa0f67e0d72ee5710a42b6de744ddae98e20220a
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437125"
---
# <a name="use-the-azure-data-lake-storage-gen2-uri"></a>Korzystanie z identyfikatora URI usługi Azure Data Lake Storage Gen2

Sterownik [systemu plików Hadoop,](https://www.aosabook.org/en/hdfs.html) który jest zgodny z usługą Azure `abfs` Data Lake Storage Gen2 jest znany przez jego identyfikator schematu (Azure Blob File System). Zgodnie z innymi sterownikami Systemu Plików Hadoop, sterownik ABFS wykorzystuje format URI do adresowania plików i katalogów w ramach konta obsługującego usługę Data Lake Storage Gen2.

## <a name="uri-syntax"></a>Składnia identyfikatora URI

Składnia identyfikatora URI dla usługi Data Lake Storage Gen2 zależy od tego, czy twoje konto magazynu jest skonfigurowane tak, aby usługa Data Lake Storage Gen2 była domyślnym systemem plików.

Jeśli konto obsługujące usługę Data Lake Storage Gen2, które chcesz rozwiązać, **nie jest** ustawione jako domyślny system plików podczas tworzenia konta, wówczas skrócona składnia identyfikatora URI jest następująca:

<pre>abfs[s]<sup>1</sup>://&lt;file_system&gt;<sup>2</sup>@&lt;account_name&gt;<sup>3</sup>.dfs.core.windows.net/&lt;path&gt;<sup>4</sup>/&lt;file_name&gt;<sup>5</sup></pre>

1. **Identyfikator schematu** `abfs` : Protokół jest używany jako identyfikator schematu. Użytkownik ma możliwość nawiązania połączenia z tls (Transport Layer Security), znanego wcześniej jako Secure Sockets Layer (SSL). Służy `abfss` do łączenia się z połączeniem TLS.

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
