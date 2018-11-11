---
title: Przykładowe dane w tabelach Azure HDInsight Hive | Dokumentacja firmy Microsoft
description: Dół próbkuje dane w tabelach Hive HDInsight Azure (Hadopop)
services: machine-learning,hdinsight
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: f31e8d01-0fd4-4a10-b1a7-35de3c327521
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: deguhath
ms.openlocfilehash: 8a1fd001980efbff27f10cfb4be1502cd2f9f402
ms.sourcegitcommit: 96527c150e33a1d630836e72561a5f7d529521b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/09/2018
ms.locfileid: "51345933"
---
# <a name="sample-data-in-azure-hdinsight-hive-tables"></a>Przykładowe dane w tabelach usługi Azure HDInsight Hive
W tym artykule opisano, jak obniżenie częstotliwości próbkowania danych przechowywanych w usłudze Azure HDInsight Hive tabel przy użyciu zapytań programu Hive, aby zmniejszyć jego rozmiar łatwiejszych do analizy. Trzy metody pobierania próbek co jest często używany ona obejmować:

* Jednolite losowego pobierania próbek
* Losowego próbkowanie według grup
* Stratyfikowana próbkowania

**Dlaczego przykładowe dane?**
Jeśli zestaw danych, która ma zostać analizowanie jest duża, zazwyczaj przyczyną jest dobrym pomysłem jest obniżenie częstotliwości próbkowania danych, aby zmniejszyć jego rozmiar mniejszy, ale reprezentatywny i łatwiejsze w zarządzaniu. Próbkowanie szczegółów ułatwia zrozumienie danych, poznawanie i technicznego opracowywania funkcji. Jego rolę w procesie nauki o danych zespołu jest umożliwienie szybkiego tworzenia prototypów funkcji do przetwarzania danych i modeli uczenia maszynowego.

To zadanie próbkowania jest krok [Team Data Science naukowych](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

## <a name="how-to-submit-hive-queries"></a>Jak przesłać zapytania programu Hive
W konsoli wiersza polecenia usługi Hadoop na węzłem głównym klastra Hadoop można przesłać zapytania hive. Aby to zrobić, zaloguj się do węzła głównego klastra usługi Hadoop, otwórz konsolę wiersza polecenia usługi Hadoop i przesłać zapytania Hive, w tym miejscu. Aby uzyskać instrukcje dotyczące przesyłania zapytań programu Hive w konsoli wiersza polecenia usługi Hadoop, zobacz [jak przesłać zapytania Hive](move-hive-tables.md#submit).

## <a name="uniform"></a> Jednolite losowego pobierania próbek
Jednolite losowego próbkowanie oznacza, że każdy wiersz w zestawie danych ma równe szanse na próbkowane. Może być implementowany przez dodanie rand() dodatkowe pola do zestawu danych w "Wybierz" zapytanie wewnętrzne i zewnętrzne zapytania "Wybierz" tego warunku dla tego pola, losowych.

Oto przykładowe zapytanie:

    SET sampleRate=<sample rate, 0-1>;
    select
        field1, field2, …, fieldN
    from
        (
        select
            field1, field2, …, fieldN, rand() as samplekey
        from <hive table name>
        )a
    where samplekey<='${hiveconf:sampleRate}'

W tym miejscu `<sample rate, 0-1>` określa część rekordów, które użytkownicy mają do próbkowania.

## <a name="group"></a> Losowego próbkowanie według grup
Podczas próbkowania danych podzielonych na kategorie, warto uwzględnić lub wykluczyć wszystkie wystąpienia dla niektórych wartości podzielonych na kategorie zmiennej. Tego rodzaju próbkowania nosi nazwę "próbkowania przez grupę". Na przykład, jeśli zostały podzielone na kategorie zmiennej "*stanu*", która zawiera wartości, takich jak NY, MA, urząd certyfikacji, JORK i PA, rekordy z każdego stanu się ze sobą, czy są próbkowane, czy nie.

Poniżej przedstawiono przykładowe zapytanie tego przykłady przez grupę:

    SET sampleRate=<sample rate, 0-1>;
    select
        b.field1, b.field2, …, b.catfield, …, b.fieldN
    from
        (
        select
            field1, field2, …, catfield, …, fieldN
        from <table name>
        )b
    join
        (
        select
            catfield
        from
            (
            select
                catfield, rand() as samplekey
            from <table name>
            group by catfield
            )a
        where samplekey<='${hiveconf:sampleRate}'
        )c
    on b.catfield=c.catfield

## <a name="stratified"></a>Stratyfikowana próbkowania
Losowego próbkowanie jest uporządkować w odniesieniu do zmiennej podzielonych na kategorie, gdy przykłady uzyskane wartości podzielonych na kategorie, znajdujących się w tej samej proporcji znajdowały się w populacji nadrzędnej. Jak wyżej, przy użyciu tego samego przykładu załóżmy, że dane zawierają następujących obserwacjach według stanów: JORK ma 100 uwagi, NY ma 60 uwagi, a WA ma 300 uwagi. Jeśli określisz stopień stratyfikowana próbkowanie, aby być 0,5, następnie otrzymaną próbkę powinny mieć około 50, 30 i 150 obserwacje JORK, NY i WA odpowiednio.

Oto przykładowe zapytanie:

    SET sampleRate=<sample rate, 0-1>;
    select
        field1, field2, field3, ..., fieldN, state
    from
        (
        select
            field1, field2, field3, ..., fieldN, state,
            count(*) over (partition by state) as state_cnt,
              rank() over (partition by state order by rand()) as state_rank
          from <table name>
        ) a
    where state_rank <= state_cnt*'${hiveconf:sampleRate}'


Aby uzyskać informacje na bardziej zaawansowane metody pobierania próbek, które są dostępne w gałęzi, zobacz [próbkowania LanguageManual](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Sampling).

