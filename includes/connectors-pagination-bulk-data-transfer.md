---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: logic-apps
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.date: 05/09/2018
ms.author: estfan
ms.custom: include file
ms.openlocfilehash: 524bc1d3a19ad8ecfc8d0d210e735d6a9ae0058b
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34678271"
---
### <a name="set-up-pagination"></a>Konfigurowanie podział na strony

Niektóre łączników i ich działania, które pobierają wiele elementów wyniki może przekroczyć domyślny rozmiar strony łącznika. W takim przypadku akcji zwraca tylko pierwszej strony wyników. Na przykład domyślny rozmiar strony dla **SQL Server — Pobierz wiersze** akcji jest 2048, ale może się różnić w zależności od innych ustawień. Aby pobrać wszystkie rekordy, włączyć **podział na strony** ustawienie dla tej akcji. To ustawienie nie ma aplikacji logiki poproś o łącznik pozostałych rekordów, ale zwrócone wszystkie wyniki jako pojedynczej wiadomości, po zakończeniu działania. 

Poniżej przedstawiono kilka łączników, których można włączyć podział na strony dla określonych akcji: 

* <a href="https://docs.microsoft.com/connectors/db2/" target="_blank">DB2</a>
* <a href="https://docs.microsoft.com/connectors/dynamicscrmonline/" target="_blank">Dynamics 365 CRM Online</a>
* <a href="https://docs.microsoft.com/connectors/excel/" target="_blank">Excel</a>
* <a href="https://docs.microsoft.com/connectors/oracle/" target="_blank">Baza danych Oracle</a>
* <a href="https://docs.microsoft.com/connectors/sharepointonline/" target="_blank">SharePoint</a>
* <a href="https://docs.microsoft.com/connectors/sql/" target="_blank">SQL Server</a> 

Oto przykład **Pobierz wiersze** akcji:

1. Aby dowiedzieć się, czy akcja obsługuje podział na strony, otwórz akcji **ustawienia**. 

   ![Akcję Otwórz "Ustawienia"](./media/connectors-pagination-bulk-data-transfer/sql-action-settings.png)

2. Jeśli akcja obsługuje podział na strony, należy zmienić **podział na strony** z **poza** do **na**. Aby upewnić się, że akcja zwraca minimalny zestaw wyników, należy określić wartość dla **Limit**.

   ![Określ, że zwracana akcji minimalną liczbę wyników](./media/connectors-pagination-bulk-data-transfer/sql-action-settings-pagination.png)

3. Gdy wszystko jest gotowe, wybierz pozycję **gotowe**.