---
title: Eksplorowanie danych tabel programu Hive za pomocą zapytań Hive - zespołu danych dla celów naukowych
description: Użyj przykładowe skrypty Hive, które są używane do eksplorowania danych tabel programu Hive w klastrze usługi HDInsight Hadoop.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/09/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: c9761ade493641b6445cf151424f9598ab25e384
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60303649"
---
# <a name="explore-data-in-hive-tables-with-hive-queries"></a>Eksplorowanie danych tabel programu Hive za pomocą zapytań Hive

Ten artykuł zawiera przykładowe skrypty Hive, które są używane do eksplorowania danych tabel programu Hive w klastrze usługi HDInsight Hadoop.

To zadanie jest to krok w [zespołu danych dla celów naukowych](overview.md).

## <a name="prerequisites"></a>Wymagania wstępne
W tym artykule założono, że masz:

* Utworzone konto magazynu platformy Azure. Aby uzyskać instrukcje, zobacz [Tworzenie konta usługi Azure Storage](../../storage/common/storage-quickstart-create-account.md)
* Zainicjowano obsługę administracyjną dostosowane klaster Hadoop w usłudze HDInsight. Aby uzyskać instrukcje, zobacz [dostosować Azure HDInsight klastry Hadoop dla usługi Advanced Analytics](customize-hadoop-cluster.md).
* Danych został przekazany do tabel programu Hive w klastrach usługi Azure HDInsight Hadoop. Jeśli nie, postępuj zgodnie z instrukcjami [tworzenie i ładowanie danych do tabel programu Hive](move-hive-tables.md) najpierw przekazywania danych do tabel programu Hive.
* Włączony zdalny dostęp do klastra. Aby uzyskać instrukcje, zobacz [dostęp węzeł główny klastra Hadoop](customize-hadoop-cluster.md).
* Aby uzyskać instrukcje dotyczące sposobu przesłać zapytania Hive, zobacz [jak przesłać zapytania Hive](move-hive-tables.md#submit)

## <a name="example-hive-query-scripts-for-data-exploration"></a>Przykładowe skrypty zapytania Hive do eksploracji danych
1. Zliczanie uwagi na partycję  `SELECT <partitionfieldname>, count(*) from <databasename>.<tablename> group by <partitionfieldname>;`
2. Zliczanie uwagi na dzień  `SELECT to_date(<date_columnname>), count(*) from <databasename>.<tablename> group by to_date(<date_columnname>);`
3. Pobierz poziomy w kolumnie podzielonych na kategorie  
    `SELECT  distinct <column_name> from <databasename>.<tablename>`
4. Pobierz liczbę poziomów w połączeniu z dwóch kolumn podzielonych na kategorie  `SELECT <column_a>, <column_b>, count(*) from <databasename>.<tablename> group by <column_a>, <column_b>`
5. Rozpoczynanie dystrybucji dla kolumny liczbowe  
    `SELECT <column_name>, count(*) from <databasename>.<tablename> group by <column_name>`
6. Prowadzenie rekordów Sprzęganie dwóch tabel
   
        SELECT
            a.<common_columnname1> as <new_name1>,
            a.<common_columnname2> as <new_name2>,
            a.<a_column_name1> as <new_name3>,
            a.<a_column_name2> as <new_name4>,
            b.<b_column_name1> as <new_name5>,
            b.<b_column_name2> as <new_name6>
        FROM
            (
            SELECT <common_columnname1>,
                <common_columnname2>,
                <a_column_name1>,
                <a_column_name2>,
            FROM <databasename>.<tablename1>
            ) a
            join
            (
            SELECT <common_columnname1>,
                <common_columnname2>,
                <b_column_name1>,
                <b_column_name2>,
            FROM <databasename>.<tablename2>
            ) b
            ON a.<common_columnname1>=b.<common_columnname1> and a.<common_columnname2>=b.<common_columnname2>

## <a name="additional-query-scripts-for-taxi-trip-data-scenarios"></a>Skrypty dodatkowe scenariusze danych podróży taksówek
Przykłady kwerend, które są specyficzne dla [danych podróży taksówek NYC](https://chriswhong.com/open-data/foil_nyc_taxi/) scenariusze są również dostępne w [repozytorium GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts). Te zapytania już mają określony schemat danych i gotowe do wysłania do uruchomienia.

