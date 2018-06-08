---
title: Przykładowe dane w tabelach platformy Azure HDInsight Hive | Dokumentacja firmy Microsoft
description: Dół próbkowania dane w tabelach Hive w usłudze Azure HDInsight (Hadopop)
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
ms.openlocfilehash: 558f7d684453c8b5040f586820bd2a8a9ac0f9c8
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34838436"
---
# <a name="sample-data-in-azure-hdinsight-hive-tables"></a>Przykładowe dane w tabelach usługi Azure HDInsight Hive
W tym artykule opisano sposób dół przykładowe dane przechowywane w tabelach platformy Azure HDInsight Hive za pomocą zapytań programu Hive, aby zmniejszyć jego rozmiar łatwiejsze do analizy. Obejmuje ono trzy metody popularly używane próbkowania:

* Jednolite losowego pobierania próbek
* Losowe próbkowania według grup
* Stratyfikowana pobierania próbek

Następujące **menu** linki do tematów opisujących sposób przykładowe dane z różnych środowiskach magazynu.

[!INCLUDE [cap-sample-data-selector](../../../includes/cap-sample-data-selector.md)]

**Dlaczego przykładowe dane?**
Jeśli zestaw danych, które mają być analizowanie jest duży, zazwyczaj jest dobrym rozwiązaniem w dół przykładowych danych, aby zmniejszyć jego rozmiar mniejsze, ale reprezentatywny i łatwiejsze w zarządzaniu. Próbkowania w dół ułatwia zrozumienie danych, badanie i inżynieria funkcji. Swoją rolę w procesie nauki danych zespołu jest umożliwienie szybkiego prototypy funkcji przetwarzania danych i modeli uczenia maszynowego.

To zadanie próbkowania jest krokiem w [zespołu danych nauki procesu (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

## <a name="how-to-submit-hive-queries"></a>Temat dotyczący przesyłania zapytań programu Hive
Można przesłać zapytań programu hive z wiersza polecenia konsoli Hadoop na węzła głównego klastra usługi Hadoop. Aby to zrobić, zaloguj się do węzła głównego klastra usługi Hadoop, otwórz konsolę wiersza polecenia platformy Hadoop i wysyłanie zapytań programu Hive z tego miejsca. Aby uzyskać instrukcje dotyczące przesyłania zapytań programu Hive w konsoli usługi Hadoop wiersza polecenia, zobacz [sposobu przesyłania zapytań Hive](move-hive-tables.md#submit).

## <a name="uniform"></a> Jednolite losowego pobierania próbek
Jednolite próbkowania losowe oznacza, że każdego wiersza w zestawie danych ma taki sam sposób przez cały czas jest próbkowany. Można ją wdrożyć na dodaniu rand() dodatkowe pola zestawu danych wewnętrzny zapytania "select" i w zapytaniu "Wybierz" zewnętrzne tego warunku losowe pola.

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

## <a name="group"></a> Losowe próbkowania według grup
Podczas pobierania próbek danych podzielone na kategorie, warto uwzględnić lub wykluczyć wszystkich wystąpień dla niektórych wartości zmiennej podzielone na kategorie. Tego rodzaju próbkowania jest nazywany "próbkowania przez grupę". Na przykład, jeśli masz podzielone na kategorie zmiennej "*stanu*", która zawiera wartości, takie jak NY, MA urzędu certyfikacji, NJ i PA, rekordy z każdym ze stanów w się ze sobą, czy są pobierane, czy nie.

Poniżej przedstawiono przykładowe zapytanie tej próbki przez grupę:

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

## <a name="stratified"></a>Stratyfikowana pobierania próbek
Losowego pobierania próbek jest uporządkować względem zmienną podzielone na kategorie, gdy próbki otrzymane wartości podzielone na kategorie, które znajdują się w tej samej stosunek co w populacji nadrzędnej. Przy użyciu w tym samym przykładzie, jako powyżej, załóżmy, że dane mają następujące uwagi przez Państwa: NJ ma 100 uwagi, NY ma 60 uwag, a WA ma 300 uwag. Jeśli określisz częstotliwość próbkowania stratyfikowana jako 0,5, następnie otrzymaną próbkę ma około 50, 30 i 150 obserwacji NJ, NY i WA odpowiednio.

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


Aby uzyskać informacje na bardziej zaawansowane metody pobierania próbek, które są dostępne w gałęzi, zobacz [LanguageManual próbkowania](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Sampling).

