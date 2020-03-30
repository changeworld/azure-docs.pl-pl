---
title: Eksploruj dane w tabelach gałęzi za pomocą zapytań hive — proces nauki o danych zespołowych
description: Użyj przykładowych skryptów hive, które są używane do eksplorowania danych w tabelach hive w klastrze PROGRAMU HDInsight Hadoop.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76722173"
---
# <a name="explore-data-in-hive-tables-with-hive-queries"></a>Eksplorowanie danych tabel programu Hive za pomocą zapytań Hive

Ten artykuł zawiera przykładowe skrypty hive, które są używane do eksplorowania danych w tabelach hive w klastrze HDInsight Hadoop.

To zadanie jest krokiem w [procesie nauki o danych zespołu.](overview.md)

## <a name="prerequisites"></a>Wymagania wstępne
W tym artykule założono, że masz:

* Utworzono konto magazynu platformy Azure. Jeśli potrzebujesz instrukcji, zobacz [Tworzenie konta usługi Azure Storage](../../storage/common/storage-account-create.md)
* Aprowizowana dostosowana klaster Hadoop z usługą HDInsight. Jeśli potrzebujesz instrukcji, zobacz [Dostosowywanie klastrów usługi Azure HDInsight Hadoop dla analizy zaawansowanej](customize-hadoop-cluster.md).
* Dane zostały przekazane do tabel hive w klastrach usługi Azure HDInsight Hadoop. Jeśli tak nie jest, postępuj zgodnie z instrukcjami w [Tworzenie i załadować dane do tabel hive,](move-hive-tables.md) aby przekazać dane do tabel hive pierwszy.
* Włączony zdalny dostęp do klastra. Jeśli potrzebujesz instrukcji, zobacz [Dostęp do węzła głównego klastra Hadoop](customize-hadoop-cluster.md).
* Jeśli potrzebujesz instrukcji dotyczących przesyłania zapytań dotyczących gałęzi, zobacz [Jak przesyłać zapytania dotyczące gałęzi](move-hive-tables.md#submit)

## <a name="example-hive-query-scripts-for-data-exploration"></a>Przykładowe skrypty zapytań hive do eksploracji danych
1. Pobierz liczbę obserwacji na partycję`SELECT <partitionfieldname>, count(*) from <databasename>.<tablename> group by <partitionfieldname>;`
2. Uzyskaj liczbę obserwacji dziennie`SELECT to_date(<date_columnname>), count(*) from <databasename>.<tablename> group by to_date(<date_columnname>);`
3. Pobierz poziomy w kolumnie kategorycznej  
    `SELECT  distinct <column_name> from <databasename>.<tablename>`
4. Uzyskaj liczbę poziomów w połączeniu z dwiema kolumnami kategorycznymi`SELECT <column_a>, <column_b>, count(*) from <databasename>.<tablename> group by <column_a>, <column_b>`
5. Pobierz rozkład kolumn numerycznych  
    `SELECT <column_name>, count(*) from <databasename>.<tablename> group by <column_name>`
6. Wyodrębnianie rekordów z łączenia dwóch tabel
   
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

## <a name="additional-query-scripts-for-taxi-trip-data-scenarios"></a>Dodatkowe skrypty zapytań dla scenariuszy danych podróży taksówką
Przykłady zapytań, które są specyficzne dla [nyc taxi trip dane](https://chriswhong.com/open-data/foil_nyc_taxi/) scenariusze są również w [repozytorium GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts). Te zapytania mają już określony schemat danych i są gotowe do przesłania do uruchomienia.

