---
author: vhorne
ms.service: dns
ms.topic: include
ms.date: 11/25/2018
ms.author: victorh
ms.openlocfilehash: 9cc650cea17acb8d89933c819c4ca60e2c459d1c
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67183862"
---
Rekordy framework (SPF) zasad nadawcy są używane do określenia serwerów poczty e-mail, które może wysyłać wiadomości e-mail w imieniu nazwy domeny. Prawidłowej konfiguracji rekordy SPF ważne jest, aby zapobiec odbiorców oznaczanie wiadomości e-mail jako wiadomości-śmieci.

Specyfikacje RFC DNS pierwotnie została wprowadzona nowy typ rekordu SPF, aby obsługiwać ten scenariusz. Aby zapewnić obsługę starszych serwerów nazw, również dozwolone użycie typu rekordu TXT, aby określić rekordy SPF. Tę niejednoznaczność doprowadziło do wprowadzenia w błąd, który został rozwiązany przez [RFC 7208](http://tools.ietf.org/html/rfc7208#section-3.1). Stwierdza, że rekordy SPF, muszą zostać utworzone przy użyciu typu rekordu TXT. Stanowi również, czy typ rekordu SPF jest przestarzały.

**Rekordy SPF są obsługiwane przez usługę Azure DNS i musi zostać utworzony przy użyciu typu rekordu TXT.** Przestarzałe typu rekordu SPF nie jest obsługiwane. Po użytkownik [zaimportować plik strefy DNS](../articles/dns/dns-import-export.md), wszystkie rekordy SPF, korzystających z typu rekordu SPF są konwertowane na typ rekordu TXT.
