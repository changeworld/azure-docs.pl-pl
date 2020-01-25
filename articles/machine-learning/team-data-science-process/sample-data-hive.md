---
title: Przykładowe dane w tabelach Azure HDInsight Hive - zespołu danych dla celów naukowych
description: Obniżenie częstotliwości próbkowania danych przechowywanych w usłudze Azure HDInsight Hive tabel przy użyciu zapytań programu Hive w celu zmniejszenia ilości danych do rozmiaru łatwiejszych do analizy.
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
ms.openlocfilehash: df85edc3de00e2b0342bc3102fe9e85564a9835b
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76719997"
---
# <a name="sample-data-in-azure-hdinsight-hive-tables"></a>Przykładowe dane w tabelach usługi Azure HDInsight Hive
W tym artykule opisano, jak obniżenie częstotliwości próbkowania danych przechowywanych w usłudze Azure HDInsight Hive tabel przy użyciu zapytań programu Hive, aby zmniejszyć jego rozmiar łatwiejszych do analizy. Obejmuje trzy popularne metody próbkowania:

* Jednolite losowego pobierania próbek
* Losowego próbkowanie według grup
* Stratyfikowana próbkowania

**Dlaczego przykładowe dane?**
Jeśli zestaw danych, która ma zostać analizowanie jest duża, zazwyczaj przyczyną jest dobrym pomysłem jest obniżenie częstotliwości próbkowania danych, aby zmniejszyć jego rozmiar mniejszy, ale reprezentatywny i łatwiejsze w zarządzaniu. Próbkowanie szczegółów ułatwia zrozumienie danych, poznawanie i technicznego opracowywania funkcji. Jego rolę w procesie nauki o danych zespołu jest umożliwienie szybkiego tworzenia prototypów funkcji do przetwarzania danych i modeli uczenia maszynowego.

To zadanie próbkowania jest krok [Team Data Science naukowych](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

## <a name="how-to-submit-hive-queries"></a>Jak przesłać zapytania programu Hive
W konsoli wiersza polecenia usługi Hadoop na węzłem głównym klastra Hadoop można przesłać zapytania hive.  Zaloguj się do węzła głównego klastra Hadoop, Otwórz konsolę wiersza polecenia usługi Hadoop i prześlij do niej zapytania programu Hive. Aby uzyskać instrukcje dotyczące przesyłania zapytań programu Hive w konsoli wiersza polecenia usługi Hadoop, zobacz [jak przesłać zapytania Hive](move-hive-tables.md#submit).

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

