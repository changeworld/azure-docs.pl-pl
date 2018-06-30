---
title: Korzystając z usługi Azure Data Lake Storage Gen2 podglądu identyfikatora URI
description: Korzystając z usługi Azure Data Lake Storage Gen2 podglądu identyfikatora URI
services: storage
keywords: ''
author: jamesbak
ms.topic: article
ms.author: jamesbak
manager: jahogg
ms.date: 06/27/2018
ms.service: storage
ms.component: data-lake-storage-gen2
ms.openlocfilehash: a6130d8440b16e5a72c939fc07f6bf32c0946418
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37114296"
---
# <a name="use-the-azure-data-lake-storage-gen2-uri"></a>Użyj usługi Azure Data Lake Storage Gen2 identyfikatora URI

[Systemu plików Hadoop](http://www.aosabook.org/en/hdfs.html) sterownik, który jest zgodny z wersji zapoznawczej Gen2 magazynu Azure Data Lake jest rozpoznawany na podstawie jego identyfikatora schematu `abfs` (Azure Blob File System). Zgodnie z innych sterowników systemu plików Hadoop, sterownik ABFS wykorzystuje format identyfikatora URI adresu plików i katalogów w ramach konta Data Lake magazynu Gen2 stanie.

## <a name="uri-syntax"></a>Składnia identyfikatora URI

Składnia identyfikatora URI Data Lake magazynu Gen2 jest zależne od tego, czy konta magazynu ustawiono ma Data Lake magazynu Gen2 jako domyślny system plików.

Jeśli konto stanie Data Lake magazynu Gen2 chcesz adres **nie jest** ustawiony jako domyślny system plików podczas tworzenia konta, a następnie jest skrócona składni identyfikatora URI:

<pre>abfs[s]<sup>1</sup>://&lt;file_system&gt;<sup>2</sup>@&lt;account_name&gt;<sup>3</sup>.dfs.core.widows.net/&lt;path&gt;<sup>4</sup>/&lt;file_name&gt;<sup>5</sup></pre>

1. **Identyfikator schematu**: `abfs` protokół jest używany jako identyfikator schematu. Istnieje możliwość nawiązania połączenia z lub bez połączenie secure sockets layer (SSL). Użyj `abfss` nawiązać połączenie secure sockets layer.

2. **System plików**: lokalizacji nadrzędnej, który zawiera pliki i foldery. Jest to ten sam, jak kontenery w usłudze obiektów blob magazynu Azure.

3. **Nazwa konta**: Nazwa konta magazynu podczas tworzenia.

4. **Ścieżki**: rozdzielonych ukośnikiem (`/`) reprezentację struktury katalogów.

5. **Nazwa pliku**: nazwa pojedynczego pliku. Ten parametr jest opcjonalny, jeśli katalog rozwiązania.

Jednak jeśli konto, które chcesz adres jest ustawiony jako domyślny system plików podczas tworzenia konta, następnie skrócona składni identyfikatora URI jest:

<pre>/&lt;path&gt;<sup>1</sup>/&lt;file_name&gt;<sup>2</sup></pre>

1. **Ścieżka**: rozdzielonych ukośnikiem (`/`) reprezentację struktury katalogów.

2. **Nazwa pliku**: nazwa pojedynczego pliku.


## <a name="next-steps"></a>Kolejne kroki

- [Użyj usługi Azure Data Lake magazynu Gen2 z klastrami Azure HDInsight](use-hdi-cluster.md)