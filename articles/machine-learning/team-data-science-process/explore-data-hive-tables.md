---
title: Eksplorowanie danych tabel programu Hive za pomocą zapytań Hive - zespołu danych dla celów naukowych
description: Użyj przykładowe skrypty Hive, które są używane do eksplorowania danych tabel programu Hive w klastrze usługi HDInsight Hadoop.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 75dce2b5a83d13fe4a7d166595456e9a8d6324ba
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722173"
---
# <a name="explore-data-in-hive-tables-with-hive-queries"></a>Eksplorowanie danych tabel programu Hive za pomocą zapytań Hive

Ten artykuł zawiera przykładowe skrypty Hive, które są używane do eksplorowania danych tabel programu Hive w klastrze usługi HDInsight Hadoop.

To zadanie jest krokiem w [procesie nauki danych zespołu](overview.md).

## <a name="prerequisites"></a>Wymagania wstępne
W tym artykule założono, że masz:

* Utworzone konto magazynu platformy Azure. Jeśli potrzebujesz instrukcji, zobacz [Tworzenie konta usługi Azure Storage](../../storage/common/storage-account-create.md)
* Zainicjowano obsługę administracyjną dostosowane klaster Hadoop w usłudze HDInsight. Jeśli potrzebujesz instrukcji, zobacz [Dostosowywanie klastrów Azure HDInsight Hadoop na potrzeby zaawansowanej analizy](customize-hadoop-cluster.md).
* Danych został przekazany do tabel programu Hive w klastrach usługi Azure HDInsight Hadoop. Jeśli tak nie jest, postępuj zgodnie z instrukcjami w temacie [Tworzenie i ładowanie danych do tabel programu Hive](move-hive-tables.md) , aby najpierw przekazać dane do tabel programu Hive.
* Włączony zdalny dostęp do klastra. Jeśli potrzebujesz instrukcji, zobacz [dostęp do węzła głównego klastra usługi Hadoop](customize-hadoop-cluster.md).
* Jeśli potrzebujesz instrukcji dotyczących przesyłania zapytań Hive, zobacz [Jak przesłać zapytania Hive](move-hive-tables.md#submit)

## <a name="example-hive-query-scripts-for-data-exploration"></a>Przykładowe skrypty zapytania Hive do eksploracji danych
1. Pobierz liczbę obserwacji na partycję `SELECT <partitionfieldname>, count(*) from <databasename>.<tablename> group by <partitionfieldname>;`
2. Pobierz liczbę obserwacji dziennie `SELECT to_date(<date_columnname>), count(*) from <databasename>.<tablename> group by to_date(<date_columnname>);`
3. Pobierz poziomy w kolumnie podzielonych na kategorie  
    `SELECT  distinct <column_name> from <databasename>.<tablename>`
4. Pobierz liczbę poziomów w połączeniu dwóch kategorii kolumn `SELECT <column_a>, <column_b>, count(*) from <databasename>.<tablename> group by <column_a>, <column_b>`
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
Przykłady zapytań, które są specyficzne dla scenariuszy [danych dotyczących rejsów z NYCem](https://chriswhong.com/open-data/foil_nyc_taxi/) , są również dostępne w [repozytorium GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts). Te zapytania już mają określony schemat danych i gotowe do wysłania do uruchomienia.

