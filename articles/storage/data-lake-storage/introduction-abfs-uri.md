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
ms.openlocfilehash: 75edf6dc7382a8a2ece7c25edd09aeacfe1c5189
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37060063"
---
# <a name="use-the-azure-data-lake-storage-gen2-uri"></a>Użyj usługi Azure Data Lake Storage Gen2 identyfikatora URI

[Systemu plików Hadoop](http://www.aosabook.org/en/hdfs.html) sterownik, który jest zgodny z wersji zapoznawczej Gen2 magazynu Azure Data Lake jest rozpoznawany na podstawie jego identyfikatora schematu `abfs` (Azure Blob File System). Zgodnie z innych sterowników systemu plików Hadoop, sterownik ABFS wykorzystuje format identyfikatora URI adresu plików i katalogów w ramach konta Data Lake magazynu Gen2 stanie.

## <a name="uri-syntax"></a>Składnia identyfikatora URI

Składnia identyfikatora URI Data Lake magazynu Gen2 jest zależne od tego, czy konta magazynu ustawiono ma Data Lake magazynu Gen2 jako domyślny system plików.

Jeśli Data Lake magazynu Gen2 stanie konta, na którym chcesz adres jest ustawiony jako domyślny system plików podczas tworzenia konta, skrócona składni identyfikatora URI jest:

<pre>/&lt;path&gt;<sup>1</sup>/&lt;file_name&gt;<sup>2</sup></pre>

1. **Ścieżka**: rozdzielonych ukośnikiem (`/`) reprezentację struktury katalogów.

2. **Nazwa pliku**: nazwa pojedynczego pliku.

Jeśli konto stanie Data Lake magazynu Gen2 chcesz adres *nie* domyślny system plików składnia identyfikatora URI jest:

<pre>abfs[s]<sup>1</sup>://&lt;file_system&gt;<sup>2</sup>@&lt;account_name&gt;<sup>3</sup>.dfs.core.widows.net/&lt;path&gt;<sup>4</sup>/&lt;file_name&gt;<sup>5</sup></pre>

1. **Identyfikator schematu**: `abfs` protokół jest używany jako identyfikator schematu. Istnieje możliwość nawiązania połączenia z lub bez połączenie secure sockets layer (SSL). Użyj `abfss` nawiązać połączenie secure sockets layer.

2. **System plików**: lokalizacji nadrzędnej, który zawiera pliki i foldery. Jest to ten sam, jak kontenery w usłudze obiektów blob magazynu Azure.

3. **Nazwa konta**: Nazwa konta magazynu podczas tworzenia.

4. **Ścieżki**: rozdzielonych ukośnikiem (`/`) reprezentację struktury katalogów.

5. **Nazwa pliku**: nazwa pojedynczego pliku. Ten parametr jest opcjonalny, jeśli katalog rozwiązania.

## <a name="next-steps"></a>Kolejne kroki

- [Użyj usługi Azure Data Lake magazynu Gen2 z klastrami Azure HDInsight](use-hdi-cluster.md)