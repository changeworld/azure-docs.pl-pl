---
title: Poznaj programów Apache Spark for Azure Data Lake Analytics U-SQL.
description: W tym artykule opisano pojęcia platformy Apache Spark, aby pomóc w różnicach między deweloperami U-SQL.
author: guyhay
ms.author: guyhay
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: conceptual
ms.custom: understand-apache-spark-for-usql-developers
ms.date: 10/15/2019
ms.openlocfilehash: 594e1055c4c063e4e151fefa3e183e6e799c90b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73648430"
---
# <a name="understand-apache-spark-for-u-sql-developers"></a>Informacje o platformie Apache Spark dla deweloperów języka U-SQL

Firma Microsoft obsługuje kilka usług analytics, takich jak [Azure Databricks](../azure-databricks/what-is-azure-databricks.md) i [Azure HDInsight,](../hdinsight/hdinsight-overview.md) a także usługa Azure Data Lake Analytics. Słyszymy od deweloperów, że mają wyraźne preferencje dla rozwiązań typu open source podczas tworzenia potoków analizy. Aby pomóc deweloperom U-SQL zrozumieć Apache Spark i jak można przekształcić skrypty U-SQL do Apache Spark, utworzyliśmy te wskazówki.

Zawiera szereg kroków, które można podjąć i kilka alternatyw.

## <a name="steps-to-transform-u-sql-to-apache-spark"></a>Kroki przekształcania U-SQL w apache spark

1. Przekształć potoki aranżacji zadań.

   Jeśli używasz [usługi Azure Data Factory](../data-factory/introduction.md) do organizowania skryptów usługi Azure Data Lake Analytics, musisz dostosować je do organizowania nowych programów Platformy Spark.
2. Zrozumienie różnic między zarządzaniem danymi u-SQL i spark

   Jeśli chcesz przenieść dane z [usługi Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md) do usługi Azure Data Lake Storage [Gen2](../storage/blobs/data-lake-storage-introduction.md), musisz skopiować zarówno dane pliku, jak i dane obsługiwane przez wykaz. Należy zauważyć, że usługa Azure Data Lake Analytics obsługuje tylko usługę Azure Data Lake Storage Gen1. Zobacz [Opis formatów danych platformy Spark](understand-spark-data-formats.md)
3. Przekształcanie skryptów U-SQL w program Spark

   Przed przekształceniem skryptów U-SQL należy wybrać usługę analityczną. Niektóre z dostępnych usług obliczeniowych to:
      - [Przepływ danych fabryki danych platformy Azure](../data-factory/concepts-data-flow-overview.md) Przepływy danych mapowania są wizualnie zaprojektowane przekształcenia danych, które umożliwiają inżynierom danych opracowanie logiki transformacji danych graficznych bez pisania kodu. Chociaż nie nadaje się do wykonywania złożony kod użytkownika, mogą one łatwo reprezentować tradycyjne transformacje przepływu danych podobne do SQL
      - [Gałąź usługi Azure HDInsight](../hdinsight/hadoop/apache-hadoop-using-apache-hive-as-an-etl-tool.md) Apache Hive on HDInsight nadaje się do operacji wyodrębniania, przekształcania i ładowania (ETL). Oznacza to, że masz zamiar przetłumaczyć skrypty U-SQL do Apache Hive.
      - Apache Spark aparaty, takie jak [Azure HDInsight Spark](../hdinsight/spark/apache-spark-overview.md) lub [Azure Databricks](../azure-databricks/what-is-azure-databricks.md) Oznacza to, że zamierzasz przetłumaczyć skrypty U-SQL do platformy Spark. Aby uzyskać więcej informacji, zobacz [Opis formatów danych platformy Spark](understand-spark-data-formats.md)

> [!CAUTION]
> Zarówno [usługi Azure Databricks,](../azure-databricks/what-is-azure-databricks.md) jak i [Azure HDInsight Spark](../hdinsight/spark/apache-spark-overview.md) są usługami klastrowania, a nie zadaniami bezserwerowymi, takimi jak usługa Azure Data Lake Analytics. Należy wziąć pod uwagę sposób aprowizowania klastrów, aby uzyskać odpowiedni stosunek kosztów do wydajności i jak zarządzać ich okresem istnienia, aby zminimalizować koszty. Te usługi mają różne właściwości wydajności z kodem użytkownika napisanym w .NET, więc trzeba będzie napisać otoki lub przepisać kod w obsługiwanym języku. Aby uzyskać więcej informacji, zobacz [Opis formatów danych platformy Spark](understand-spark-data-formats.md), Zrozumieć pojęcia dotyczące kodu Platformy Spark [apache dla deweloperów języka U-SQL](understand-spark-code-concepts.md), [.Net for Apache Spark](https://dotnet.microsoft.com/apps/data/spark)

## <a name="next-steps"></a>Następne kroki

- [Zrozumienie formatów danych platformy Spark dla deweloperów języka U-SQL](understand-spark-data-formats.md)
- [Poznaj pojęcia dotyczące kodu platformy Spark dla deweloperów języka U-SQL](understand-spark-code-concepts.md)
- [Uaktualnianie rozwiązań do analizy dużych zbiorów danych z usługi Azure Data Lake Storage Gen1 do usługi Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-upgrade.md)
- [Platforma .NET dla platformy Apache Spark](https://docs.microsoft.com/dotnet/spark/what-is-apache-spark-dotnet)
- [Przekształcanie danych przy użyciu działania gałęzi usługi Hadoop w usłudze Azure Data Factory](../data-factory/transform-data-using-hadoop-hive.md)
- [Przekształcanie danych przy użyciu aktywności platformy Spark w usłudze Azure Data Factory](../data-factory/transform-data-using-spark.md)
- [Co to jest platforma Apache Spark w usłudze Azure HDInsight](../hdinsight/spark/apache-spark-overview.md)
