---
title: Przykładowe dane w tabelach hive usługi Azure HDInsight — proces nauki o danych zespołu
description: Dane w dół próbki przechowywane w tabelach hive usługi Azure HDInsight przy użyciu zapytań hive w celu zmniejszenia ilości danych do rozmiaru łatwiejszego w zarządzaniu do analizy.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76719997"
---
# <a name="sample-data-in-azure-hdinsight-hive-tables"></a>Przykładowe dane w tabelach usługi Azure HDInsight Hive
W tym artykule opisano sposób pobierania próbek danych przechowywanych w tabelach hive usługi Azure HDInsight przy użyciu zapytań hive, aby zmniejszyć je do rozmiaru łatwiejszego w zarządzaniu do analizy. Obejmuje trzy powszechnie stosowane metody pobierania próbek:

* Jednolite losowe pobieranie próbek
* Losowe pobieranie próbek według grup
* Próbkowanie stratified

**Dlaczego warto przykładać swoje dane?**
Jeśli zestaw danych, który planujesz analizować, jest duży, zwykle warto pobrać próbkę w dół danych, aby zmniejszyć je do mniejszego, ale reprezentatywnego i łatwiejszego w zarządzaniu rozmiaru. Pobieranie próbek w dół ułatwia zrozumienie danych, eksplorację i inżynierię funkcji. Jego rolą w procesie nauki o danych zespołu jest umożliwienie szybkiego prototypowania funkcji przetwarzania danych i modeli uczenia maszynowego.

To zadanie próbkowania jest krokiem w [procesie nauki o danych zespołu (TDSP).](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)

## <a name="how-to-submit-hive-queries"></a>Jak przesyłać zapytania dotyczące gałęzi
Zapytania gałęzi można przesyłać z konsoli Wiersza polecenia Hadoop w węźle głównym klastra Hadoop.  Zaloguj się do węzła głównego klastra Hadoop, otwórz konsolę Wiersz polecenia Hadoop i prześlij stamtąd zapytania hive. Aby uzyskać instrukcje dotyczące przesyłania zapytań hive w konsoli wiersza polecenia Hadoop, zobacz [Jak przesyłać zapytania gałęzi](move-hive-tables.md#submit).

## <a name="uniform-random-sampling"></a><a name="uniform"></a>Jednolite losowe pobieranie próbek
Jednolite losowe pobieranie próbek oznacza, że każdy wiersz w zestawie danych ma równe szanse na pobranie próbek. Można go zaimplementować, dodając dodatkowe pole rand() do zestawu danych w wewnętrznej kwerendzie "select" i w zewnętrznej kwerendzie "select" tego warunku w tym losowym polu.

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

W `<sample rate, 0-1>` tym miejscu określa proporcję rekordów, które użytkownicy mają próbki.

## <a name="random-sampling-by-groups"></a><a name="group"></a>Losowe pobieranie próbek według grup
Podczas pobierania próbek danych kategorii, można dołączyć lub wykluczyć wszystkie wystąpienia dla niektórych wartości zmiennej kategorycznej. Ten rodzaj pobierania próbek jest nazywany "pobieraniem próbek według grup". Na przykład jeśli masz zmienną kategoryczną *"Stan",* która ma wartości, takie jak NY, MA, CA, NJ i PA, chcesz rekordy z każdego stanu, które mają być razem, czy są próbkowane, czy nie.

Oto przykładowa kwerenda, która przykłada według grup:

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

## <a name="stratified-sampling"></a><a name="stratified"></a>Próbkowanie stratified
Losowe pobieranie próbek jest stratyfikowane w odniesieniu do zmiennej kategorycznej, gdy uzyskane próbki mają wartości kategoryczne, które są obecne w tym samym stosunku, jak w populacji macierzystej. Korzystając z tego samego przykładu, jak powyżej, załóżmy, że dane mają następujące obserwacje według państw: NJ ma 100 obserwacji, NY ma 60 obserwacji, a WA ma 300 obserwacji. Jeśli określisz szybkość próbkowania stratowanego na 0,5, otrzymana próbka powinna mieć odpowiednio około 50, 30 i 150 obserwacji NJ, NY i WA.

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


Aby uzyskać informacje na temat bardziej zaawansowanych metod próbkowania, które są dostępne w uli, zobacz [Próbkowanie czne languagemanual](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Sampling).

