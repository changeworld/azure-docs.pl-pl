---
title: Eksploruj dane w tabelach gałąź z zapytań programu Hive | Dokumentacja firmy Microsoft
description: Eksploruj dane w tabelach Hive za pomocą zapytań Hive.
services: machine-learning
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: 0d46cea5-2b4c-4384-9bfa-fa20f6f75148
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: deguhath
ms.openlocfilehash: b0156cc32348a086a9aeb720acfbbfe31efb285c
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34837110"
---
# <a name="explore-data-in-hive-tables-with-hive-queries"></a>Eksplorowanie danych tabel programu Hive za pomocą zapytań Hive
Ten dokument zawiera przykładowe skrypty Hive, które są używane, aby eksplorować dane w tabelach gałęzi w klastrze usługi HDInsight Hadoop.

Następujące **menu** linki do tematów opisujących sposób użycia narzędzia, aby eksplorować dane w różnych środowiskach magazynu.

[!INCLUDE [cap-explore-data-selector](../../../includes/cap-explore-data-selector.md)]

## <a name="prerequisites"></a>Wymagania wstępne
W tym artykule przyjęto założenie, że masz:

* Utworzone konto magazynu platformy Azure. Aby uzyskać instrukcje, zobacz [Tworzenie konta usługi Azure Storage](../../storage/common/storage-create-storage-account.md#create-a-storage-account)
* Zainicjowano obsługę administracyjną dostosowane klastra usługi Hadoop w usłudze HDInsight. Aby uzyskać instrukcje, zobacz [dostosować Azure klastrów usługi HDInsight Hadoop zaawansowane analizy](customize-hadoop-cluster.md).
* Dane przekazane do tabele programu Hive w klastrach usługi Azure HDInsight Hadoop. Jeśli nie, postępuj zgodnie z instrukcjami [tworzenie i ładowanie danych do tabel Hive](move-hive-tables.md) najpierw przekazywać dane do tabele programu Hive.
* Włączyć dostęp zdalny do klastra. Aby uzyskać instrukcje, zobacz [dostęp węzła Head klastra usługi Hadoop](customize-hadoop-cluster.md).
* Aby uzyskać instrukcje dotyczące sposobu przesyłania zapytań programu Hive, zobacz [sposobu przesyłania zapytań Hive](move-hive-tables.md#submit)

## <a name="example-hive-query-scripts-for-data-exploration"></a>Przykładowe skrypty służące zapytania Hive do Eksploracja danych
1. Zliczanie uwagi dla każdej partycji  `SELECT <partitionfieldname>, count(*) from <databasename>.<tablename> group by <partitionfieldname>;`
2. Zliczanie uwagi na dzień  `SELECT to_date(<date_columnname>), count(*) from <databasename>.<tablename> group by to_date(<date_columnname>);`
3. Pobierz poziomy w kolumnie podzielone na kategorie  
    `SELECT  distinct <column_name> from <databasename>.<tablename>`
4. Pobierz liczbę poziomów w połączeniu z dwóch kolumn podzielone na kategorie  `SELECT <column_a>, <column_b>, count(*) from <databasename>.<tablename> group by <column_a>, <column_b>`
5. Pobierz dystrybucji dla kolumn wartości liczbowych  
    `SELECT <column_name>, count(*) from <databasename>.<tablename> group by <column_name>`
6. Wyodrębnij rekordy z Sprzęganie dwóch tabel
   
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

## <a name="additional-query-scripts-for-taxi-trip-data-scenarios"></a>Skrypty zapytania dodatkowe scenariusze danych podróży taksówki
Przykłady zapytań, które są specyficzne dla [danych podróży taksówki NYC](http://chriswhong.com/open-data/foil_nyc_taxi/) scenariuszy znajdują się również w [repozytorium GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts). Te zapytania już mieć określony schemat danych i jest gotowe do przesłania do uruchomienia.

