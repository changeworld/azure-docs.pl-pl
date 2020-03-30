---
title: Poznaj formaty danych platformy Apache Spark dla deweloperów U-SQL usługi Azure Data Lake Analytics.
description: W tym artykule opisano pojęcia platformy Apache Spark, aby pomóc deweloperom U_SQL zrozumieć różnice między formatami danych U-SQL i Spark.
author: guyhay
ms.author: guyhay
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: conceptual
ms.custom: understand-apache-spark-data-formats
ms.date: 01/31/2019
ms.openlocfilehash: 36f39503ca32f1ee4b422ae7b1cf9abf48716f07
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73648443"
---
# <a name="understand-differences-between-u-sql-and-spark-data-formats"></a>Opis różnic między formatami danych U-SQL i Spark

Jeśli chcesz użyć [usługi Azure Databricks](../azure-databricks/what-is-azure-databricks.md) lub [Azure HDInsight Spark,](../hdinsight/spark/apache-spark-overview.md)zalecamy migrację danych z [usługi Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md) do [usługi Azure Data Lake Storage Gen2.](../storage/blobs/data-lake-storage-introduction.md)

Oprócz przenoszenia plików, należy również udostępnić dane, przechowywane w tabelach U-SQL, dostępne dla platformy Spark.

## <a name="move-data-stored-in-azure-data-lake-storage-gen1-files"></a>Przenoszenie danych przechowywanych w plikach usługi Azure Data Lake Storage Gen1

Dane przechowywane w plikach mogą być przenoszone na różne sposoby:

- Napisz potok [usługi Azure Data Factory,](../data-factory/introduction.md) aby skopiować dane z konta [usługi Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md) na konto [usługi Azure Data Lake Storage Gen2.](../storage/blobs/data-lake-storage-introduction.md)
- Napisz zadanie platformy Spark, które odczytuje dane z konta [usługi Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md) i zapisuje je na koncie usługi Azure Data Lake Storage [Gen2.](../storage/blobs/data-lake-storage-introduction.md) Na podstawie przypadku użycia, można zapisać go w innym formacie, takich jak Parkiet, jeśli nie trzeba zachować oryginalny format pliku.

Zalecamy zapoznanie się z artykułem [Uaktualnianie rozwiązań do analizy dużych zbiorów danych z usługi Azure Data Lake Storage Gen1 do usługi Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-upgrade.md)

## <a name="move-data-stored-in-u-sql-tables"></a>Przenoszenie danych przechowywanych w tabelach U-SQL

Tabele U-SQL nie są rozumiane przez program Spark. Jeśli masz dane przechowywane w tabelach U-SQL, uruchomisz zadanie U-SQL, które wyodrębnia dane tabeli i zapisuje go w formacie, który rozumie platforma Spark. Najbardziej odpowiednim formatem jest utworzenie zestawu plików Parkietu zgodnie z układem folderu magazynu hive.

Dane wyjściowe można osiągnąć w U-SQL za pomocą wbudowanego wyjścia Parkiet i przy użyciu dynamicznego partycjonowania danych wyjściowych z zestawami plików do tworzenia folderów partycji. [Przetwarzaj więcej plików niż kiedykolwiek i użyj parkietu,](https://blogs.msdn.microsoft.com/azuredatalake/2018/06/11/process-more-files-than-ever-and-use-parquet-with-azure-data-lake-analytics) co stanowi przykład tworzenia takich danych eksploatacyjnych spark.

Po tej transformacji należy skopiować dane zgodnie z opisem w rozdziale [Przenieś dane przechowywane w plikach usługi Azure Data Lake Storage Gen1](#move-data-stored-in-azure-data-lake-storage-gen1-files).

## <a name="caveats"></a>Zastrzeżenia

- Semantyka danych Podczas kopiowania plików kopia będzie występować na poziomie bajtu. Dlatego te same dane powinny być wyświetlane na koncie [usługi Azure Data Lake Storage Gen2.](../storage/blobs/data-lake-storage-introduction.md) Należy jednak zauważyć, Spark może interpretować niektóre znaki inaczej. Na przykład może użyć innej wartości domyślnej dla ogranicznika wiersza w pliku CSV.
    Ponadto w przypadku kopiowania wpisanych danych (z tabel), a następnie Parkiet i Iskra może mieć inną precyzję i skalę dla niektórych wpisanych wartości (na przykład float) i może traktować wartości null inaczej. Na przykład U-SQL ma semantykę C# dla wartości null, podczas gdy Spark ma logikę trzech wartości dla wartości null.

- Organizacja danych (partycjonowanie) tabele U-SQL zapewniają partycjonowanie dwupoziomowe. Poziom zewnętrzny`PARTITIONED BY`( ) jest według wartości i mapuje głównie do schematu partycjonowania Hive/Spark przy użyciu hierarchii folderów. Należy upewnić się, że wartości null są mapowane do odpowiedniego folderu. Poziom wewnętrzny`DISTRIBUTED BY`( ) w U-SQL oferuje 4 schematy dystrybucji: działanie okrężne, zakres, skrót i skrót bezpośredni.
    Tabele hive/Spark obsługują tylko partycjonowanie wartości lub partycjonowanie skrótów przy użyciu innej funkcji mieszania niż U-SQL. Podczas tworzenia danych tabeli U-SQL, prawdopodobnie będzie można mapować tylko do partycjonowania wartości dla platformy Spark i może być konieczne dalsze dostrajanie układu danych w zależności od ostatecznych zapytań platformy Spark.

## <a name="next-steps"></a>Następne kroki

- [Poznaj pojęcia dotyczące kodu platformy Spark dla deweloperów języka U-SQL](understand-spark-code-concepts.md)
- [Uaktualnianie rozwiązań do analizy dużych zbiorów danych z usługi Azure Data Lake Storage Gen1 do usługi Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-upgrade.md)
- [Platforma .NET dla platformy Apache Spark](https://docs.microsoft.com/dotnet/spark/what-is-apache-spark-dotnet)
- [Przekształcanie danych przy użyciu aktywności platformy Spark w usłudze Azure Data Factory](../data-factory/transform-data-using-spark.md)
- [Przekształcanie danych przy użyciu działania gałęzi usługi Hadoop w usłudze Azure Data Factory](../data-factory/transform-data-using-hadoop-hive.md)
- [Co to jest platforma Apache Spark w usłudze Azure HDInsight](../hdinsight/spark/apache-spark-overview.md)
