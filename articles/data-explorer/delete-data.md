---
title: Usuwanie danych z Eksploratora danych platformy Azure
description: W tym artykule opisano zbiorczego usuwania scenariuszy w Eksploratorze danych platformy Azure, w tym przeczyszczania i usuwa przechowywania na podstawie.
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
services: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 485ee48bf197ad996bba8a516b80882f44ba623a
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/29/2018
ms.locfileid: "50212917"
---
# <a name="delete-data-from-azure-data-explorer"></a>Usuwanie danych z Eksploratora danych platformy Azure

Eksplorator danych usługi Azure obsługuje kilka zbiorczego usuwania metod, które zostaną przedstawione w tym artykule. Program nie obsługuje usuwania każdego rekordu w czasie rzeczywistym, ponieważ jest zoptymalizowany do szybkiego dostępu do odczytu.

* Jeśli co najmniej jedna tabela nie jest już potrzebny, usuń je przy użyciu tabeli docelowej lub porzucić tabel polecenia.

    ```Kusto
    .drop table <TableName>

    .drop tables (<TableName1>, <TableName2>,...)
    ```

* Jeśli stare dane nie jest potrzebna, usuń go, zmieniając okresu przechowywania na poziomie bazy danych lub tabeli.

    Należy wziąć pod uwagę bazy danych lub tabeli, który jest ustawiony przez 90 dni przechowywania. Business musi ulec zmianie, więc teraz tylko 60 dni dla danych jest wymagana. W takim przypadku usuń starszych danych w jednym z następujących sposobów.

    ```Kusto
    .alter-merge database <DatabaseName> policy retention softdelete = 60d

    .alter-merge table <TableName> policy retention softdelete = 60d
    ```

    Aby uzyskać więcej informacji, zobacz [zasady przechowywania](https://docs.microsoft.com/azure/kusto/concepts/retentionpolicy).

Jeśli potrzebujesz pomocy z problemami usuwania danych, otwórz żądanie pomocy technicznej w [witryny Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).
