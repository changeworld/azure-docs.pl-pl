---
title: Reduktor działa wolno w usłudze Azure HDInsight
description: Reduktor jest powolny w usłudze Azure HDInsight od możliwego pochylania danych
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: 8a9c7ed9f6b5b8ec89bfca6dd59034b11f05f9a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895163"
---
# <a name="scenario-reducer-is-slow-in-azure-hdinsight"></a>Scenariusz: Reduktor działa wolno w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów i możliwe rozwiązania problemów podczas korzystania ze składników zapytania interaktywnego w klastrach usługi Azure HDInsight.

## <a name="issue"></a>Problem

Podczas uruchamiania kwerendy, takich jak `insert into table1 partition(a,b) select a,b,c from table2` plan kwerend uruchamia kilka reduktorów, ale dane z każdej partycji przechodzi do jednego reduktora. Powoduje to, że kwerenda jest tak powolny, jak czas wykonany przez reduktora największej partycji.

## <a name="cause"></a>Przyczyna

Otwórz [beeline](../hadoop/apache-hadoop-use-hive-beeline.md) i sprawdź `hive.optimize.sort.dynamic.partition`wartość zestawu .

Wartość tej zmiennej ma być ustawiona na true/false na podstawie charakteru danych.

Jeśli partycje w tabeli wejściowej są mniejsze (powiedzmy mniej niż 10), a więc `true`jest liczba partycji wyjściowych, a zmienna jest ustawiona na , powoduje to, że dane mają być globalnie sortowane i zapisywane przy użyciu jednego reduktora na partycję. Nawet jeśli liczba dostępnych reduktorów jest większa, kilka reduktorów może być opóźnionych ze względu na pochylenie danych i nie można osiągnąć maksymalnego równoległości. Po zmianie `false`na , więcej niż jeden reduktor może obsługiwać jedną partycję i wiele mniejszych plików zostaną zapisane, w wyniku szybszego wstawiania. Może to jednak wpłynąć na dalsze zapytania ze względu na obecność mniejszych plików.

Wartość ma `true` sens, gdy liczba partycji jest większa, a dane nie są wypaczone. W takich przypadkach wynik fazy mapy zostaną zapisane w taki sposób, że każda partycja będzie obsługiwana przez jeden reduktor, co spowoduje lepszą późniejszą wydajność kwerendy.

## <a name="resolution"></a>Rozwiązanie

1. Spróbuj ponownie podzielić dane na wiele partycji.

1. Jeśli #1 nie jest możliwe, ustaw wartość konfiguru na false w sesji beeline i spróbuj ponownie wykonać kwerendę. `set hive.optimize.sort.dynamic.partition=false`. Ustawienie wartości false na poziomie klastra nie jest zalecane. Wartość jest `true` optymalna i ustawić parametr w razie potrzeby na podstawie charakteru danych i kwerendy.

## <a name="next-steps"></a>Następne kroki

Jeśli nie widzisz problemu lub nie możesz rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy technicznej:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej platformy Azure Community.](https://azure.microsoft.com/support/community/)

* Połącz [@AzureSupport](https://twitter.com/azuresupport) się z — oficjalnym kontem platformy Microsoft Azure w celu poprawy jakości obsługi klienta, łącząc społeczność platformy Azure z odpowiednimi zasobami: odpowiedziami, pomocą techniczną i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy z [witryny Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Wybierz **pozycję Obsługa z** paska menu lub otwórz centrum pomocy + pomocy **technicznej.** Aby uzyskać bardziej szczegółowe informacje, zapoznaj się z [instrukcjami tworzenia żądania pomocy technicznej platformy Azure.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Dostęp do obsługi zarządzania subskrypcjami i rozliczeń jest dołączony do subskrypcji platformy Microsoft Azure, a pomoc techniczna jest świadczona za pośrednictwem jednego z [planów pomocy technicznej platformy Azure.](https://azure.microsoft.com/support/plans/)
